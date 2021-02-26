---
title: "Your First Custom View: Clubhouse ProgressBar!"
date: 2021-02-26 23:50:28 -0400
categories: android
---

No doubt about that **Clubhouse** is the most hottest social media recently. There is no special UI/UX which is never have seen anywhere else.
But I'm impressed by the ProgressBar. Even if it is not special something, it's quite pretty and simple!
<br>

It could be a good example of Custom View. Because its view is simple and doesn't have heavy animation.
</br>
</br>

So what the heck is *Clubhose ProgressBar*?
--------------------------------------------------
<img src="https://user-images.githubusercontent.com/54348567/109168128-da4ffa00-77c1-11eb-8771-bf014bd6e075.GIF" width="300" height="620">
</br>
</br>

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
This is Dot Class which is created in CustomView we gonna make. It draw a Circle(dot) at center of bounds which means something like drawing paper.
We going to set bounds at CustomView Class.
</br>
And make sure that declare *paint* as a ```public var``` in Constructor! not a ```private val``` or ```public val```. We will check below why it should be a ```public var```
</br>
</br>

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
Make this at ```res/values``` folder. This is Custom Attributes used at layout file. So after this CustomView made, you can specify attributes like below.
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
</br>
</br>

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
First of all, get Attributes that user set from layout.xml. And add Dot object into dotList(mutableList).  
</br>


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
    var right = left + diameter

    dotList.forEach {
        // adjust as if 'setGravity' center
        it.setBounds(left, 0, right, measuredHeight)
        left += diameterWithInterval
        right += diameterWithInterval
    }
}
```
Now it's time to set bounds of each dot. *totalDotWidth* is every dot diameter plus every dotInterval. 
For example, if ```dotRadius``` is 30 ```dotCount``` is 5 and ```dotInterval``` is 10, the ```totalDotWidth``` is a 340.
```paddingStart``` is for no matter what user set width of this view, locate at center like *gravity center*. We don't have to consider the height, because
regardless of height user set, it will be located at center of total view height.
</br>
Finally, set bounds of each dot as give each coordinate(left, top, right, bottom). Dot will be drew center of this bounds.
</br>
</br>

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
We just need animation about change color of dots. So change ```paint``` by ObjectAnimator.
For ObjectAnimator access to ```paint``` property of Dot, we should have set as a ```public var``` like above.
```public var``` make ```get``` and ```set``` automatically.
If you work with Java, you should add ```set``` method like below.
```java
public void setPaint(Paint paint) {
    this.paint = paint;
}
```
<br>

In ```TypeEvaluator``` you can manipulate in fine detail. Observe ```fraction``` in Log when animation is running, you can catch how ```fraction``` works.
</br>
*****

💫If you want to see more specific full code, visit [my github repo][clubhouseProgressBar-repo]


[clubhouseProgressBar-repo]: https://github.com/Jun-Hub/ClubhouseProgressBar/tree/master/ChProgressBar/src/main/java/com/github/joon/chprogressbar




