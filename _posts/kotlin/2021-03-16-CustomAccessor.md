---
title: "[Custom Accessor] Make Getter more simple with Kotlin"
date: 2021-03-16 15:50:28 -0400
categories: kotlin
classes: wide
---

<br>
<br>
Let's say you already know about *getter* and *setter* of Java.  
The Accessors are like this:  
<br>

```java
class Pizza {
    private int radius;
    
    public void setRadius(int radius) {
        this.radius = radius;
    }
    
    public int getRadius() {
        return radius;
    }
}
```
<br>
In Kotlin, it is really simple!  
<br>

```kotlin
class Pizza(var radius: Int)
```
That's it! It is same with above Java code. 
property ```radius``` makes itself *getter* and *setter* internally.(If you want to make only *getter*, ```val radius: Int```)  
<br>
<br>
<br>
Let's define 'large size' if its radius longer than 20.  
<br>
To check if a pizza is large size or not, you could add a property like ```val isLargeSize: Boolean = true``` or ```val isLargeSize: Boolean = false```  
<br>
But *Custom Accessor* makes it even more simple.  

```kotlin
class Pizza(var radius: Int) {
    val isLargeSize get() = radius>20
}
```
It doesn't need field that save its value. Getter calculate ```isLargeSize``` everytime client access to the property.  
<br>
<br>
<br>
Moreover you can use this for some util function.  

```kotlin
val Int.isEven get() = this%2==0

val String.hasUpperCase: Boolean get() {
    this.forEach { 
        if(it.isUpperCase()) return true
    }
    return false
}


//usage
println("Is 6 even number? ${6.isEven}")    //true
println("macbook has uppercase? ${"macbook".hasUpperCase}"  //false
```

<br>
<br>
<br>

********************

If there's a mistake, always welcome your opinion!  
<br>
<br>
</br>

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
