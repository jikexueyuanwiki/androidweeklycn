# 自定义颜色范围

《Styling Android》的忠实读者应该知道我是一个Spans的骨灰级粉丝，知道我认为掌握好Span对于发挥TextView的最大功效来说至关重要。人们说Span有时只做一些简单的工作，比如仅仅改变文本的颜色，这样看起来有一点尴尬。这本文中，我们将展望如何利用我们自己的Span，并且看一下使用自定义Span可进行如何的简化。

在我们开始前，我要先简短地介绍一下。编写本文的灵感来自于一些代码，这些代码使用一个我最不喜欢的类别即android.text.Html中执行的文本样式。在这个类别在一个文本字符串中解析html标记并生成相关的Span后，其本身就不是问题了。然而，我碰到的问题时是这个类别支持难以维护代码。使用Html的代码常常需要进行重写才可以直接利用使用Span的功能以满足做出更改的需要。

一个示例可能会帮助阐明这个问题。想一想TextView文本的一部分需要在剩下的字符串中采用不同颜色的情况。这个任务可以使用Html类别来完成：

```
Spanned text = Html.fromHtml("One word should be <font color='16711680'>red</font>");
textView.setText(text);
```

我首先遇到的麻烦是它看起来很不臃肿。Java代码中字符串的嵌入式HTML标记是一个看起来简单的代码，这是因为有很多可行的工具比其更有效。虽然字符串可以移入一个字符串资源中，但如果在运行时需要对实际颜色值进行确定，这种情况则通常不会发生。 我们最后必须执行甚至更为臃肿的字符串连接，或我们需要使用格式化的字符串资源来生成适当的HTML字符串，之后我们使用Html类别对其进行解析。还有一个更大的麻烦就是Html的fromHtml()方法可使我们在标记内指定颜色资源，包括颜色状态列表。我们只能在‘android’包中找到它，所以我们无法介入系统资源，并不能使用我们自己的资源。  如果我们在上层TextView更改的情况下需要改变文本颜色，这就会给我们带来问题。

我已经对一个Html为什么不好的用例进行了阐述，那么`应`如何解决这个问题呢？ 使用Span来更改文本的通常的方法是使用TextAppearanceSpan，但是这常常需要我们指定其他的因素，比如文本外观或字体等。 在上述列子中，我们仅关注了改变一部分字符串字体颜色的问题，所以大可不必使用TextAppearanceSpan。其他的选择还有 ForegroundColorSpan，但这个方法同样存在着不支持颜色状态列表的问题。

但是我们可以轻松地创建我们自己的自定义Span，它可以准确地完成我们需要做的工作（而且我们可以在此之后在整个应用程序中再次使用这个自定义Span）。

让我们首先建立一个简单的自定义Span,其将仅仅改变文本的颜色。我们在这里可以改用ForegroundColorSpan（而且我肯定会使用生产代码），但是我选择创建一个自定义的代码，仅此用来帮助解释如何编写我们自己的Span：

```
class StaticColourSpan extends CharacterStyle {
    private final int colour;
 
    public StaticColourSpan(int colour) {
        super();
        this.colour = colour;
    }
 
    @Override
    public void updateDrawState(TextPaint tp) {
        tp.setColor(colour);
    }
}
```

很简单吧？ 构造函数使用了一个颜色值，因为我们扩展了CharacterStyle，所以我们需要使用updateDrawState()。这种方法将在文本的onDraw()前调用，并允许我们调整Paint绘画对象，以此给文本上色。所以，我们所有需要做的是设置绘画对象的颜色，之后就全部搞定了。

现在一些人可能已经意识到这种方法并不能解决TextView状态变化下文本更改颜色用例的问题，但我们可以创建另一种可以精确完成这个工作的Span。

```
class ColourStateListSpan extends CharacterStyle {
    private final ColorStateList colorStateList;
 
    public ColourStateListSpan(ColorStateList colorStateList) {
        super();
        this.colorStateList = colorStateList;
    }
 
    @Override
    public void updateDrawState(TextPaint tp) {
        tp.setColor(colorStateList.getColorForState(tp.drawableState, 0));
    }
}
```

这是非常相似的，差异是构造函数采用了ColorStateList而不是原始的颜色值，而且在updateDrawSate()，我们在ColorStateList（颜色状态列表）中查找了合适的颜色，这些颜色依赖我们从TextPaint对象获得的状态。updateDrawState() 将在TextView 刷新后调出，所以我们在此时可获知该控制状态。

下一个考虑的事情就是我们真的不希望加载ColorStateList对象将其调出。 但是我们可以轻松地创建一个工厂方法，这个方法将采取资源标识符并依靠资源类型加载适合的Span。

```
public abstract class TextColourSpan extends CharacterStyle {
    public static TextColourSpan newInstance(Context context, int resourceId) {
        Resources resources = context.getResources();
        ColorStateList colorStateList = resources.getColorStateList(resourceId);
        if (colorStateList != null) {
            return new ColourStateListSpan(colorStateList);
        }
        int colour = resources.getColor(resourceId);
        if (colour >= 0) {
            return new StaticColourSpan(colour);
        }
        return null;
    }
}
```

如果我们改变StaticColourSpan和 ColourStateListSpan类别，从而扩展基础类别，而不是直接扩展CharacterStyle，那么我们有了一个多态的newInstance()方法，这个方法将依靠加载资源的类型而返回合适的对象。

最后一件值得考虑的事就是如何确定Span应用在字符串的范围。字符串模式匹配的一个显而易见的选择是使用正则表达式，研究一个实用类别是如何为我们完成这个工作的。

```
public final class SpanUtils {
    private SpanUtils() {
    }
 
    public static CharSequence createSpannable(Context context, int stringId, Pattern pattern, CharacterStyle... styles) {
        String string = context.getString(stringId);
        return createSpannable(string, pattern, styles);
    }
 
    public static CharSequence createSpannable(CharSequence source, Pattern pattern, CharacterStyle... styles) {
        SpannableStringBuilder spannableStringBuilder = new SpannableStringBuilder(source);
        Matcher matcher = pattern.matcher(source);
        while (matcher.find()) {
            int start = matcher.start();
            int end = matcher.end();
            applyStylesToSpannable(spannableStringBuilder, start, end, styles);
        }
        return spannableStringBuilder;
    }
 
    private static SpannableStringBuilder applyStylesToSpannable(SpannableStringBuilder source, int start, int end, CharacterStyle... styles) {
        for (CharacterStyle style : styles) {
            source.setSpan(CharacterStyle.wrap(style), start, end, Spanned.SPAN_INCLUSIVE_INCLUSIVE);
        }
        return source;
    }
}
```

我们现在可以使用字符串将其调出，在出现匹配时应用我们希望匹配的正则表达式和Span对象列表。 这种方法可轻松地随时进行应用，而且比Html实施机制更为简洁。然而，可以肯定的是其在理解和维护上更为轻松。而且这个方法用起来很有效

```
public class MainActivity extends ActionBarActivity {
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
 
        CharacterStyle redText = TextColourSpan.newInstance(this, R.color.bright_red);
        CharacterStyle changeText = TextColourSpan.newInstance(this, R.color.pressable_string);
        Pattern redPattern = Pattern.compile(getString(R.string.simple_string_pattern));
        Pattern changePattern = Pattern.compile(getString(R.string.pressable_string_pattern));
 
        final TextView text2 = (TextView) findViewById(R.id.text2);
        final TextView text3 = (TextView) findViewById(R.id.text3);
 
        formatUsingSpans(text2, R.string.simple_string, redPattern, redText);
 
        formatUsingSpans(text3, R.string.pressable_string, redPattern, redText);
        formatUsingSpans(text3, changePattern, changeText);
    }
 
    private void formatUsingSpans(TextView textView, int stringId, Pattern pattern, CharacterStyle... styles) {
        CharSequence text = SpanUtils.createSpannable(this, stringId, pattern, styles);
        textView.setText(text);
    }
}
```

附带的源文件还有一些进一步的例子，指出这种技术是如何应用到这几个简单类别的。

所以，总而言之：如果我承继了你使用Html走了捷径编写的代码，那么我将把你找出来并报仇雪恨。 然而，更有可能发生的事你将必须维护这个代码，而且最后自怨自艾。 不要使用Html类别来创建技术债务，应该多用点心使用Spans来正确地完成任务。

本文中的源代码在[此](https://bitbucket.org/StylingAndroid/customcolourspans/src/6dda94855654519803e478267b6ab84b8dcd47ea/?at=master)可见。