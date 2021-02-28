---
title: "Your First Custom View: Clubhouse ProgressBar!"
date: 2021-02-26 23:50:28 -0400
categories: android progressBar
---

No doubt about that **Clubhouse** is the most hottest social media recently. There is no special UI/UX which is never have seen anywhere else.
But I'm impressed by the ProgressBar. Even if it is not special something, it's quite pretty and simple!
<br>

It could be a good example of Custom View. Because its view is simple and doesn't have heavy animation.
<br>
<br>
<br>

So what the heck is *Clubhouse ProgressBar*?
--------------------------------------------------
<img src="https://user-images.githubusercontent.com/54348567/109168128-da4ffa00-77c1-11eb-8771-bf014bd6e075.GIF" width="300" height="620">
<br>
<br>
<br>

DotDrawable.class⚫️
-------------------------
```kotlin
class DotDrawable(private val radius: Float,
                  var paint: Paint) : Drawable() {

    override fun draw(canvas: Canvas) {
        canvas.drawCircle(bounds.exactCenterX(), bounds.exactCenterY(), radius, paint)
    }

    override fun setAlpha(alpha: Int) {}

    override fun setColorFilter(colorFilter: ColorFilter?) {}

    override fun getOpacity(): Int = PixelFormat.TRANSLUCENT
}
```
This is Dot Class which is created in ProgressBar we gonna make. It draw a Circle(dot) at center of ```bounds``` which means something like drawing paper.
We going to set ```bounds``` at ProgressBar Class.
<br>
Make sure that declare ```paint``` as a ```public var``` in Constructor! Not a ```private val``` or ```public val```. We will check below why it should be a ```public var```
<br>
<br>
<br>

attrs.xml
----------------------
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="ClubhouseProgressBar">
        <attr name="dotCount" format="integer"/>
        <attr name="dotRadius" format="dimension"/>
        <attr name="dotInterval" format="dimension"/>
        <attr name="activeColor" format="color"/>
        <attr name="inactiveColor" format="color"/>
        <attr name="animationDuration" format="integer" />
    </declare-styleable>
</resources>
```
Make this at ```res/values``` folder. This is Custom Attributes will be used at Layout. After all the work is done, you can specify attributes like below.
```xml
<your.package.name.ClubhouseProgressBar
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:dotCount="5"
    app:dotRadius="20dp"
    app:dotInterval="10dp"
    app:inactiveColor="@color/purple_200"
    app:activeColor="@color/purple_700"
    app:animationDuration="500" />
```
<br>
<br>
<br>

ClubhouseProgressBar.class
---------------------------------
```kotlin
init {
        context.theme.obtainStyledAttributes(attrs, R.styleable.ChProgressBar, 0, 0).let {
            try {
                dotCount = it.getInt(R.styleable.ChProgressBar_dotCount, 3)
                dotRadius = it.getDimension(R.styleable.ChProgressBar_dotRadius, 65f)
                dotInterval = it.getDimension(R.styleable.ChProgressBar_dotInterval, 25f)
                activePaint.color = it.getColor(R.styleable.ChProgressBar_activeColor, Color.BLACK)
                inactivePaint.color = it.getColor(
                    R.styleable.ChProgressBar_inactiveColor,
                    Color.WHITE
                )
                animDuration = it.getInt(R.styleable.ChProgressBar_animationDuration, 200).toLong()
            } finally {
                it.recycle()
            }
        }

        for (i in 0 until dotCount) {
            dotList.add(DotDrawable(dotRadius, inactivePaint))
        }
    }
```
First of all, get Attributes that user set from Layout. And add Dot object into ```dotList```(mutableList).  
<br>
<br>


```kotlin
override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
    super.onMeasure(widthMeasureSpec, heightMeasureSpec)


    adjustDotBounds()
}

private fun adjustDotBounds() {

    val diameter = (dotRadius * 2).toInt()
    val totalDotWidth = (diameter*dotCount + dotInterval*(dotCount-1)).toInt()
    val diameterWithInterval = (diameter + dotInterval).toInt()
    val paddingStart = (measuredWidth - totalDotWidth)/2f

    var left = paddingStart.toInt()
	val top = 0
    var right = left + diameter
	val bottom = measuredHeight

    dotList.forEach {
        it.setBounds(left, top, right, bottom)
        left += diameterWithInterval
        right += diameterWithInterval
    }
}
```
<img src="https://user-images.githubusercontent.com/54348567/109411039-7c4e2d00-79e2-11eb-976e-c120e77e5df8.jpg" width="900" height="400">

Now it's time to set bounds of each dot. *totalDotWidth* is the sum of every dot's diameter and every dotInterval. 
For example, if ```dotRadius``` is 30, ```dotCount``` is 5 and ```dotInterval``` is 10, the ```totalDotWidth``` is a 340.
```paddingStart``` is for no matter what width of this view user sets, locate at center like *gravity center*. We don't have to consider the height, because
Clubhouse ProgressBar is only horizontal line.
<br>
Finally, set bounds of each dot by giving each coordinate. Dot will be drew center of this bounds.
<br>
<br>
<br>

```kotlin
override fun onAttachedToWindow() {
    super.onAttachedToWindow()
    createAnimation()
}

 private fun createAnimation() {
    val paintEvaluator = TypeEvaluator<Paint> { fraction, _, _ ->

        when(fraction) {
            1f -> inactivePaint
            0f -> inactivePaint
            else -> activePaint
        }
    }

    dotList.forEach { dot ->
        val animator = ObjectAnimator.ofObject(dot, "paint", paintEvaluator, inactivePaint)
        animator.apply {
            duration = animDuration
            addUpdateListener { invalidate() }
        }
        animatorList.add(animator)
    }

    animatorSet.apply {
        playSequentially(animatorList)
        addListener(object : AnimatorListenerAdapter() {
            override fun onAnimationEnd(animation: Animator) {
                start()
            }
        })
        start()
    }
}
```
We need animation to change color of dots. So change ```paint``` by ObjectAnimator.
To ObjectAnimator access to ```paint``` property of Dot, we should have set as a ```public var``` as mention above.
```public var``` makes *getter* and *setter* automatically.
If you work with Java, you should add *setter* method like below.
```java
public void setPaint(Paint paint) {
    this.paint = paint;
}
```
<br>

In ```TypeEvaluator``` you can manipulate in fine detail animation. Observe ```fraction``` at Log when animation is running, you can catch how ```fraction``` works.
<br>
<br>

*****

💫Do you want to see more specific full code? Visit [my github repo][clubhouseProgressBar-repo]
<br>
<br>

If there's a mistake, always welcome your opinion!


[clubhouseProgressBar-repo]: https://github.com/Jun-Hub/ClubhouseProgressBar/tree/master/ChProgressBar/src/main/java/com/github/joon/chprogressbar

<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://joon-github-io.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>

