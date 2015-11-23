# RecyclerView FastScroll – Part 2

[请留言](https://blog.stylingandroid.com/recyclerview-fastscroll-part-2/#respond) 
在[之前的文章](https://blog.stylingandroid.com/recyclerview-fastscroll-part-1)中，我们学会了 FastScroller 控制框架。在这一结束篇中，我们将添加触摸和滚动行为。 
 
![Recycler](../images/issue-145/31.gif) 

首先我们需要的是一种内部方法，当由于 FastScroller 的触摸事件或者 用户滚动了 *RecyclerView*，滚动的位置变化时，为了设置 bubble 和 handle 的位置，该方法会被调用： 

FastScroller.java 

```
   public class FastScroller extends LinearLayout {
    .
    .
    .
    private void setPosition(float y) {
        float position = y / height;
        int bubbleHeight = bubble.getHeight();
        bubble.setY(getValueInRange(0, height - bubbleHeight, (int) ((height - bubbleHeight) * position)));
        int handleHeight = handle.getHeight();
        handle.setY(getValueInRange(0, height - handleHeight, (int) ((height - handleHeight) * position)));
    }

    private int getValueInRange(int min, int max, int value) {
        int minimum = Math.max(min, value);
        return Math.min(minimum, max);
    }
    .
    .
    .
}
```

这里需要一些数学知识，因为 handle 和 bubble 是不同高度的，且我们需要单独的处理每一个。当滚动的时候，我们想让每一个都有自己的上边缘。 

列表中的项目时可见的。 

`getValueInRange()` 是一个实用程序方法，确保 bubble 和 handle 在它们的追踪中。 

我们的 *FastScroller* 控制与 *RecyclerView* 相关联，所以下一个任务是提供一种机制，使用一个简单的设值函数来实现关联：  
FastScroller.java 

```
    public class FastScroller extends LinearLayout {

    	private final ScrollListener scrollListener = new ScrollListener();
 
    	public void setRecyclerView(RecyclerView recyclerView) {
        	this.recyclerView = recyclerView;
        	recyclerView.setOnScrollListener(scrollListener);
    	}
 
    	private class ScrollListener extends OnScrollListener {
        	@Override
        	public void onScrolled(RecyclerView rv, int dx, int dy) {
            	View firstVisibleView = recyclerView.getChildAt(0);
            	int firstVisiblePosition = recyclerView.getChildPosition(firstVisibleView);
            	int visibleRange = recyclerView.getChildCount();
            	int lastVisiblePosition = firstVisiblePosition + visibleRange;
            	int itemCount = recyclerView.getAdapter().getItemCount();
            	int position;
            	if (firstVisiblePosition == 0) {
                	position = 0;
            	} else if (lastVisiblePosition == itemCount - 1) {
                	position = itemCount - 1;
            	} else {
                	position = firstVisiblePosition;
            	}
            	float proportion = (float) position / (float) itemCount;
            	setPosition(height * proportion);
        	}
    	}
    }
```

当调用设值函数时，设置一个 `OnScrollListener` 实例，当用户直接 scroll *RecyclerView* 时，该实例会被调用，由此我们可以调整 handle 和 buddle 的位置。为了在列表顶部和底部提供正确的位置，需要一些逻辑知识。 

接下来我们需要看 *FastScroller* 控制中处理触摸事件。我们希望实现的行为是：当用户在控制中轻触时，handle 会出现。用户可以上下拖动来改变当前位置。当用户释放时，在 handle 隐藏之前会有一个短暂的延迟。这是通过覆盖 `onTouchEvent()` 实现的：   
FastScroller.java 

```
    public class FastScroller extends LinearLayout {
    .
    .
    .
    private static final int HANDLE_HIDE_DELAY = 1000;
    private static final int TRACK_SNAP_RANGE = 5;

    private final HandleHider handleHider = new HandleHider();

    @Override
    public boolean onTouchEvent(@NonNull MotionEvent event) {
        if (event.getAction() == MotionEvent.ACTION_DOWN || event.getAction() == MotionEvent.ACTION_MOVE) {
            setPosition(event.getY());
            if (currentAnimator != null) {
                currentAnimator.cancel();
            }
            getHandler().removeCallbacks(handleHider);
            if (handle.getVisibility() == INVISIBLE) {
                showHandle();
            }
            setRecyclerViewPosition(event.getY());
            return true;
        } else if (event.getAction() == MotionEvent.ACTION_UP) {
            getHandler().postDelayed(handleHider, HANDLE_HIDE_DELAY);
            return true;
        }
        return super.onTouchEvent(event);
    }

    private class HandleHider implements Runnable {
        @Override
        public void run() {
            hideHandle();
        }
    }

    private void setRecyclerViewPosition(float y) {
        if (recyclerView != null) {
            int itemCount = recyclerView.getAdapter().getItemCount();
            float proportion;
            if (bubble.getY() == 0) {
                proportion = 0f;
            } else if (bubble.getY() + bubble.getHeight() >= height - TRACK_SNAP_RANGE) {
                proportion = 1f;
            } else {
                proportion = y / (float) height;
            }
            int targetPos = getValueInRange(0, itemCount - 1, (int) (proportion * (float) itemCount));
            recyclerView.scrollToPosition(targetPos);
        }
    }
    .
    .
    .
}
```

当接收一个向下或移动的动作时，我们设置当前的位置来与当前的 Y 位置匹配，取消可能运行的动画，取消延迟的处理程序回调(关于这点在第二部分中有更多描述)。如果 handle 不可见，那我们调用之前创建的方法使它显现。最后在重调 true 之前，我们设置 RecyclerView 的当前位置来使用触发事件。 

当接收一个向上的动作时，在一个短暂的延迟后，我们使用一个 *Handler* 发布一个延迟动作来隐藏 handle。 

当我们设置 *RecyclerView* 的位置时，如果我们在底部一定距离内来与底部对齐，或若第一项可见时与顶部对齐，那么我们要明白其逻辑关系，否则，如果在中间的某个位置，那就需要计算出正确的比例值。

注意在这里我们只使用 `scrollToPosition()` ，而不用 `smoothScrollToPosition()`，所以在[之前的系列](https://blog.stylingandroid.com/scrolling-recyclerview-part-1/)中不会出现问题。 

这是我们控制完成的。剩下的就是连接。首先我们将它添加到包含 RecyclerView 的布局：  
res/layout/activity_main.xml 

```
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    	xmlns:tools="http://schemas.android.com/tools"
    	android:layout_width="match_parent"
    	android:layout_height="match_parent">
 
    	<android.support.v7.widget.RecyclerView
        	android:id="@+id/recyclerview"
        	android:layout_width="match_parent"
        	android:layout_height="match_parent"
        	android:paddingLeft="@dimen/activity_horizontal_margin"
        	android:paddingRight="@dimen/activity_horizontal_margin"
        	android:paddingTop="@dimen/activity_vertical_margin"
        	android:paddingBottom="@dimen/activity_vertical_margin"
        	android:scrollbars="none"
        	tools:context=".MainActivity" />
 
    	<com.stylingandroid.smoothscrolling.FastScroller
        	android:id="@+id/fast_scroller"
        	android:layout_width="wrap_content"
        	android:layout_height="match_parent"
        	android:layout_alignParentEnd="true" />
    </RelativeLayout> 
```

最后我们需要在 *RecyclerView* 和 *FastScroller* 之间创建联系：  
MainActivity.java 

```
    public class MainActivity extends Activity {
    .
    .
    .
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        recyclerView = (RecyclerView) findViewById(R.id.recyclerview);
        recyclerView.setAdapter(LargeAdapter.newInstance(this));
        int duration = getResources().getInteger(R.integer.scroll_duration);
        recyclerView.setLayoutManager(new ScrollingLinearLayoutManager(this, LinearLayoutManager.VERTICAL, false, duration));
        FastScroller fastScroller = (FastScroller) findViewById(R.id.fastscroller);
        fastScroller.setRecyclerView(recyclerView);
    }
    .
    .
    .
}
```

就这样。现在我们可以看到 fas 滚动行为： 

最后一点：在联系人应用程序中，FastScroller 处理包含一个字母表示的列表中的当前位置。这是使用了一个比我们的示例稍微复杂的适配器，但是添加这个不应该是繁琐的工作。也许这是我们在以后的部分中要讨论的。 

可用的源代码 [here](https://bitbucket.org/StylingAndroid/scrollingrecyclerview/src/470c1da307401c1a2af4564780c07486e23a6d76/?at=FastScroll)。 

[Mark Allison](https://blog.stylingandroid.com/).版权所有。这篇文章最开始出现在[ Styling Android ](http://blog.stylingandroid.com/)。 

这个页面的部分内容是基于 Google 创建和共享的工作修改的。

