---
title: "Focusing Hint EditText: A Picky requirement from UI Designer"
date: 2021-06-28 23:20:28 -0400
categories: android editText
classes: wide
---

<br>
<br>

Software Engineers deal with picky requirements. Sometimes, I would just say 'It's impossible because Android does not allow it.'.
The requirements looks really simple for UI/UX designers, but for us it's not! Anyway actually I make it.
<br>
<br>

In Android, When `EditText` is focused, the `hint` is disappear. But UI/UX designer wanted that hint remain when it doesn't have any text even if it's focused.
After an hour trying, I made it!!

<br>
<br>
<br>

Be Tricky Developer👻
-------------------------
MainActivity.kt
```kotlin
class MainActivity: AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding
    private var hasChanged = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.editText.run {
            requestFocus()
            //set TextColor to the hint color
            setTextColor(context.getColorCompatible(android.R.color.darker_gray))
            //set position of cursor at the head.
            setSelection(0)
			
            addTextChangedListener(object: TextWatcher {
                override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {}

                override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
                    if(!hasChanged) {
                        //this Focusing Hint valid only the first time.
                        hasChanged = true
                        //set TextColor to the normal color
                        setTextColor(context.getColorCompatible(android.R.color.black))
                        //remove the hint in the Text
                        text = text.toString().replace("YOUR_HINT", "").toEditable()
                        //set position of cursor at the second
                        setSelection(1)
                    }
                }

                override fun afterTextChanged(s: Editable?) {}

            })
			
        }
    }
}
```

<br>
<br>

activity_main.xml
```xml
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
	
	<!-- Actually `android:text` will be a tricky hint, because it's focused. -->
	<EditText
            android:id="@+id/editText"
            android:layout_width="200dp"
            android:layout_height="70dp"
            android:hint=""
            android:inputType="textMultiLine"
            android:text="YOUR_HINT"
            android:textSize="16dp"
            app:layout_cosstraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent" />
	
</androidx.constraintlayout.widget.ConstraintLayout>
```
<br>
<br>

*********************

<br>
<br>

If there are some mistakes, always welcome your opinion!


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
