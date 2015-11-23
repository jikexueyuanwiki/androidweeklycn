# 映射与包的神秘关系 

[*这篇文章是在欧金尼奥@workingkills Marletti的帮助下完成的。*]

警告——这是一个*很长*的帖子。

## 当边缘情况没有被覆盖 

假设你需要**传递一个映射的值作为额外的意图**。这可能不是一个常见的情况，不可否认，但它也可能会发生。它的确发生在 Eugenio。

**如果你正在使用一个哈希映射**，这是映射中最常见的类型，你没有创建一个包含“额外”信息的自定义类，**那么你是幸运的**。你可以写入：

```
    intent.putExtra("map", myHashMap);
```
在你接收的活动中，你会得到很好的映射，消除了额外的意图：

```
    HashMap map = (HashMap) getIntent().getSerializableExtra("map"); 
```

如果你需要**以额外的意图传递另外一种映射**——比如说，一个树形映射（或任何自定义实现),该怎么办呢？好吧，当你找到它：

```
    TreeMap map = (TreeMap) getIntent().getSerializableExtra("map"); 
```

然后，你可以得到：

```
    java.lang.ClassCastException: java.util.HashMap cannot be cast to java.util.TreeMap 
```

是的，一个很好的 *ClassCastException异常*，因为你的映射已经变成…一个哈希映射。 

>我们将看到，为什么我们稍后会使用getSerializableExtra()，现在我们足以说，那是因为所有的默认映射的实现是可序列化的并且没有范围过窄的putExtra()/get*Extra()可以接受他们。 

在我们继续之前，**让我们来了解一下这个过程中所有的参与者。** 

[*tl:dr; 如果你想要一个解决方案，请直接跳到最后的“解决方法”!*] 

## 包

你们中的很多人都知道（但也许有些人不知道），在 Android 框架下的**所有 IPC 通信**都是**基于** [**Binders**](http://elinux.org/Android_Binder) 的概念。并且希望你们很多人都知道，主要的机制是让**数据基于**[**包**](http://developer.android.com/reference/android/os/Parcel.html)**在进程之间进行编组**。 

包是 Android 为 IPC 使用的一个**优化的、非通用的接口机制**。与接口现象相反，你不应该**以任何一种持久性的形式使用包**，因为它没有提供用于处理不同版本的数据。**每当你看到一个包，说明你正在引擎盖下处理一个包。** 

*添加额外意图？包*

*在一个片段中设置参数？包* 

*等等*

**包知道如何处理一大堆**箱外类型，包括原生类型、字符串、数组、映射、稀疏阵列、打包和接口。**打包是一种你必须可以读写数据到任意一个包的机制**，除非你真的，真的组要使用接口。 

**打包相对于接口的优势主要是关于性能**，在大多数情况下，这应该是一个足够更倾向于前者的理由，并且接口有一定的开销。

## 让我们一步一步来分析吧 

所以，让我们试着去了解**什么让我们得到一个 `ClassCastException` **。从我们所使用的代码开始，我们可以看到，我们对 *Intent#putExtras()* 的调用解析需要一个字符串和接口。正如我们前面所说的，这是所预料到的，映射的实现是可序列化的，它们没有被打包。此外，没有一个 *putExtras()* 明确需要使用*映射*。 

### 步骤1：找到所述的第一个薄弱环节 

让我们来看看在 *Intent.putExtra(String,Serializable)* 会发生什么：

>[Intent.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core/java/android/content/Intent.java#L6271)   

```
    public Intent putExtra(String name, Serializable value) {
  	// ...
  	mExtras.putSerializable(name, value);
  	return this;
    }
```

在这里，*mExtras*显然是一个*包*。那么好吧，**意图代表将所有的额外都打到一个包，就如同我们所预期的**，并调用 *Bundle#putSerializable()*。让我们看看这个方法：
>[Bundle.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core/java/android/os/Bundle.java#L446)

```
    @Override
	public void putSerializable(String key, Serializable value) {
  	super.putSerializable(key, value);
    } 
```

事实证明，这恰恰仅代表了超级实现，那就是：
>[BaseBundle.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core/java/android/os/BaseBundle.java#L488)

```
    void putSerializable(String key, Serializable value) {
  	unparcel();
  	mMap.put(key, value);
    } 
```

好，到最后**我们尝到了一些甜头**。 

首先，让我们忽略 *unparcel()*。我们可以看到，*mMap* 是一个 *array<String,Object>*。这告诉我们，**我们正在失去任何一种曾经拥有过的类型的信息**，也就是说，在这一点上，不管我们将值放入包中使用的方法类型多么强大，**一切都会在包含对象值的一个大的映射上结束** 。

我们的蜘蛛意识开始发麻...... 

![映射与包的神秘关系](../images/issue-145/1-GokSVk3wQIhGoCQtVIwNTA.gif) 

###步骤2：编写映射 

当我们真正开始写*包*的内容时，才是真正有趣的时候。在此之前，如果我们检查额外的类型，我们仍可以得到正确的类型：

```
    Intent intent = new Intent(this, ReceiverActivity.class);
	intent.putExtra("map", treeMap);
	Serializable map = intent.getSerializableExtra("map");
    Log.i("MAP TYPE", map.getClass().getSimpleName()); 
```

这样的输出正如我们所预料，由 *TreeMap* 到 LogCat。所以这样的转变必须发生在包被写入到所述包中并被再次读取的时候。 

如果我们看一下**如何写一个包时**，我们可以看到 *BaseBundle#writeToParcelInner* 下的细节问题:

>[BaseBundle.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core/java/android/os/BaseBundle.java#L1292) 

```
    void writeToParcelInner(Parcel parcel, int flags) {
  		if (mParcelledData != null) {
    		// ...
  		} else {
    	// ...
    	int startPos = parcel.dataPosition();
    	parcel.writeArrayMapInternal(mMap);
    	int endPos = parcel.dataPosition();
    	// ...
      }
```

跳过所有对我们无关紧要的代码，我们可以看到，**大部分工作都是由 Parcel#writeArrayMapInternal()（记住是 mMap 是一个数组映射）执行的**：
>[Parcel.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core%2Fjava%2Fandroid%2Fos%2FParcel.java#L628)

```
    /* package */ void writeArrayMapInternal(
        	ArrayMap<String, Object> val) {
  	// ...
  	int startPos;
  	for (int i=0; i<N; i++) {
    	// ...
    	writeString(val.keyAt(i));
    	writeValue(val.valueAt(i));
    	// ...
  	}
    }
```

基本上做的就是**把在 BaseBundle 的映射里写入的每一个键-值对根据值的大小**形成连续的字符串（这里的值是字符串）。到目前为止后者似乎没有考虑到值的类型。 

让我们更深一层！ 

![映射与包的神秘关系](../images/issue-145/1-TOBlt2WOVLElnQTyG-R7YQ.gif) 

### 步骤3：编写映射值 

那么，你问，**Parcel#writeValue() 看起来怎么样**？在这里，在它的 *if-elseif-else* 体现：
>[Parcel.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core%2Fjava%2Fandroid%2Fos%2FParcel.java#L1250)

```
    public final void writeValue(Object v) {
  	if (v == null) {
    	writeInt(VAL_NULL);
  	} else if (v instanceof String) {
    	writeInt(VAL_STRING);
    	writeString((String) v);
  	} else if (v instanceof Integer) {
    	writeInt(VAL_INTEGER);
    	writeInt((Integer) v);
  	} else if (v instanceof Map) {
    	writeInt(VAL_MAP);
    	writeMap((Map) v);
  	} else if (/* you get the idea, this goes on and on */) {
    	// ...
  	} else {
    	Class<?> clazz = v.getClass();
    	if (clazz.isArray() &&
        	clazz.getComponentType() == Object.class) {
      	// Only pure Object[] are written here, Other arrays of non-primitive types are
      	// handled by serialization as this does not record the component type.
      	writeInt(VAL_OBJECTARRAY);
      	writeArray((Object[]) v);
    	} else if (v instanceof Serializable) {
      	// Must be last
      	writeInt(VAL_SERIALIZABLE);
      	writeSerializable((Serializable) v);
    	} else {
      	throw new RuntimeException("Parcel: unable to marshal value "+ v);
    	}
  	}
    } 
```

啊哈！明白了！即使我们把 *TreeMap* 作为一个借口放进包里，**writeValue() 方法实际上也会把它放进映射分支的一个实例V中**，这（原因很明显）在 else…if（V 是接口的实例）分支之前。

在这一点上，感觉变得越来越强烈。 

我现在想知道，对于映射他们是否使用了一些完全非法的捷径，不知怎的就将他们变成了哈希映射？ 

![映射与包的神秘关系](../images/issue-145/1-zA893bdM9QmLYPlRe7estg.gif) 

###步骤4：将映射写到包中 

那么，事实上，就其本身而言，除了后续我们将强化映射的类型，**writeMap() 自身做的事情并不多**：
>[Parcel.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core%2Fjava%2Fandroid%2Fos%2FParcel.java#L603)

```
    public final void writeMap(Map val) {
  	writeMapInternal((Map<String, Object>) val);
    } 
```

该方法的 JavaDoc 是很清楚的： 

>“映射的关键字必须是字符串对象。” 

[**类型擦除**](http://en.wikipedia.org/wiki/Generics_in_Java#Problems_with_type_erasure)**确保我们在这里不会有运行时间的错误**，即使我们可能在传递一个关键字不是字符串类型的映射（再一次，这完全是较高水平的非法行为…）。 

事实上，只要**我们看一下 writeMapIntent()**，我们就会想到：
>[Parcle.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core%2Fjava%2Fandroid%2Fos%2FParcel.java#L611)

```
    /* package */ void writeMapInternal(Map<String,Object> val) {
  	// ...
  	Set<Map.Entry<String,Object>> entries = val.entrySet();
  	writeInt(entries.size());
  	for (Map.Entry<String,Object> e : entries) {
    	writeValue(e.getKey());
    	writeValue(e.getValue());
  		}
    } 
```

再次，在这里类型擦除让**那些计算在运行时变得一文不值**。事实是我们将之前对**键和值**进行类型检查的 writeValue() 作为我们“解压缩”映射，把一切都放进包里。正如我们所看见的，**writeValue() 完全可以处理非字符串类型的键**。 

也许文档和代码在某些点上有点同步，但事实上，**在包中放置和检索一个 TreeMap<Integer,Object> 是相当容易的**。 

那么，理所当然，*树形映射*成为一个*哈希映射*是一种例外。

黑洞与启示

![映射与包的神秘关系](../images/issue-145/1-zA893bdM9QmLYPlRe7estg.gif) 

好吧，这里的**图片已经很清楚**了。当映射被写进一个包中时已经完全失去他们的类型了，所以**当他们进行回读的时候没有办法对信息进行恢复**。

### 步骤5：对映射进行回读 

作为最后一步，快速检查我们的理论，**让我们去检查一下 readValue()**，它是与 *writeValue()*相对应的：
>[Parcel.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core%2Fjava%2Fandroid%2Fos%2FParcel.java#L2135) 

```
    public final Object readValue(ClassLoader loader) {
  	int type = readInt();
  	switch (type) {
    	case VAL_NULL:
      		return null;
    	case VAL_STRING:
      		return readString();
    	case VAL_INTEGER:
      		return readInt();
    	case VAL_MAP:
      		return readHashMap(loader);
    	// ...
  		}
    } 
```

**当写入数据时包工作的方式**，每一项的内容如下： 

1. 它**定义了数据类型 int**（VAL_*常量之一） 

2. 对**数据本身进行转储**（可以包括其他元数据如非固定大小的数据类型，例如字符串长度） 

3. 对数据类型进行递归嵌套（非原始）

在这里，我们看到 **readValue() 读取数据的类型为 int**，这使得我们的 TreeMap 通过 writeValue() 被设置为 VAL_MAP，然后根据相应的选择情况来**调用 readHashMap() 来检索数据**本身：
>[Parcel.java](https://github.com/android/platform_frameworks_base/blob/lollipop-mr1-release/core%2Fjava%2Fandroid%2Fos%2FParcel.java#L1671)  

```
    public final HashMap readHashMap(ClassLoader loader)
	{
  		int N = readInt();
  		if (N < 0) {
     		return null;
  		}
  		HashMap m = new HashMap(N);
  		readMapInternal(m, N, loader);
  		return m;
    } 
```

>(the C#-style opening curly brace is actually in AOSP, it’s not my fault) 

你几乎可以想象，**readMapInternal() 简单的打包所有的映射条目，这些条目是从我们传递给映射的包中读取的**。 

是的。**这就是为什么你总是从一个包中得到一个哈希映射**。如何你创建一个自定义的映射，通过接口进行实现结果也是如此。但**绝对不是我们所希望的**！ 

**如果这是一个预期的效果或者只是一个疏忽**那就很难说了。这是无可否认的一个边缘情况，因为你有几个真正的理由来将一个映射传递到一个Intent中，并且你应该只是有很少的好理由去传递接口而不是包。但是缺乏文档让我觉得它实际上可能就只是一个疏忽而不是设计决策（从其他的设计决策中派生）。

## 解决方法（又名 tl;dr） 

好吧，我们要**深一层的了解我们的问题**，然而现在我们已经确定了被我们打乱了的关键路径。**我们需要确保我们的 TreeMap 不被抓到由 writeValue() 映射检查的实例 V 中**。  

当谈论到 Eugenio 时我的大脑中想到的第一个解决方案是很单一的但却很有效：**将映射包装到一个接口容器中**。Eugenio 迅速投入到了这个普通的包装中并确认它解决了这个问题。

>[MapWrapper.java](https://gist.github.com/Takhion/7967dd7c83591d7a2516)  

```
    public class MapWrapper<T extends Map & Serializable> implements Serializable {
 
  		private final T map;
  		public MapWrapper(T map) {
    		this.map = map;
  		}
  		public T getMap() {
    		return map;
  		}
  		public static <T extends Map & Serializable> Intent
         		putMapExtra(Intent intent, String name, T map) {

    		return intent.putExtra(name, new MapWrapper<>(map));
  		}
  		public static <T extends Map & Serializable> T
         		getMapExtra(Intent intent, String name)
         		throws ClassCastException {
    		Serializable s = intent.getSerializableExtra(name);
    		return s == null ? null : ((MapWrapper<T>)s).getMap();
  		}
    }

```

请注意，你在 gist 上找到的**完整代码**是使用 Android 的 @NonNull 注释强制执行的。如果你想单纯的在 Java 中使用这些代码，你可以用 JetBrain 的 @NonNull 取代它，或者你也可以选择脱离这些注释。

###另一个可能的解决方案

另一个解决方法是把它作为一个 Intent extra 之前，自己**先把映射提前序列化成一个字节数组**，然后用 getByteArrayExtra() 对它进行检索，但你必须手动处理序列化和反序列化。

如果你不怕麻烦想选择其他的解决方案来代替，Eugenio 已经为你提供了[单独 Gist 的代码](https://gist.github.com/Takhion/abf9ec3b01d3a6a2298e)。

###当你无法控制不断增多的 Intent 时 

最后，也许出于某种原因，**你无法控制 Bundle 创建的代码**——例如，因为它在某些第三方库中。

在这种情况下，请记住，**许多映射的实现需要有一个构造函数**，该构造函数是以映射作为输入的，比如新建一个 TreeMap(Map)。如果有需要的话，你可以使用构造函数**将你检索的哈希映射从 Bundle 中“变回”你之前使用的映射类型**。

请记住，在这种情况下，在映射上的**任何一个“extra”的属性都将会丢失**，并且只有键/值对会被保留下来。

##结论

**作为一个 Android 开发人员，意味着你几乎可以轻而易举的用你的方式去完成任何事情**，尤其是小的、微不足道的事情。

**我们从中可以学到些什么？**

当事情的发展不像我们所预期的那样， 

不要只盯着 JavaDoc 不动。 

因为那样只会浪费时间。  

或者是因为 JavaDoc 的作者不了解有关于你的具体情况。 

**答案可能就在 AOSP 代码中。** 


我们可以随意的访问AOSP代码。这在动态领域中几乎是独一无二的。我们可以而且应该知道这是为什么。

**尽管有时候它看起来像 WTF-land**,当你了解了你工作的内部运行平台，你就可以成为一名很好的开发人员了。

并且记住：没有打败你的事情只会让你变得更强，或者更疯狂。

![映射与包的神秘关系](../images/issue-145/0-40IPQ7jhknFGYKNR-.gif) 

