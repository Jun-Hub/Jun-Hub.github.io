---
title: "Activity Result API: RegisterForActivityResult"
date: 2021-02-16 23:50:28 -0400
categories: android
---

Almost every Android developers have tried passing data and getting response between two activities. 
Before `Result API` released, we passed data on `startActivityForResult` and got responses on `onActivityResult` which is easy to nested and 
complicated as project goes by. Unlike `onActivityResult`, `ActivityResultCallback` of `Result API` get responses at each `ActivityResultLauncher`.

<br>
<br>

Let's Dive into Code🌊
------------------------

First of all, don’t forget to add this to your `build.gradle`.

```gradle
implementation 'androidx.activity:activity-ktx:1.2.0'
implementation 'androidx.fragment:fragment-ktx:1.3.0'
```

<br>

`registerForActivityResult()` takes an `ActivityResultContract` and an `ActivityResultCallback` and returns an `ActivityResultLauncher` which 
you’ll use to launch the other activity. An `ActivityResultContract` defines the input type needed to produce a result along with the output type of the result.

```kotlin
class MainActivity : AppCompatActivity() {

    //'getContent' is an 'ActivityResultLauncher'
    val getContent = registerForActivityResult(ActivityResultContracts.GetContent()) { uri: Uri? ->
        // 'ActivityResultCallback': Handle the returned Uri    
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        selectButton.setOnClickListener {
            // Pass in the mime type you'd like to allow the user to select
            // as the input
            getContent.launch("image/*")
            // This launch Image folder to get image uri
        }
    }
}
```

<br>
<br>

Internal Code😯
-------------------
```java
public static class GetContent extends ActivityResultContract<String, Uri> {

    @CallSuper
    @NonNull
    @Override
    public Intent createIntent(@NonNull Context context, @NonNull String input) {
        return new Intent(Intent.ACTION_GET_CONTENT)
                .addCategory(Intent.CATEGORY_OPENABLE)
                .setType(input);
    }

    @Nullable
    @Override
    public final SynchronousResult<Uri> getSynchronousResult(@NonNull Context context,
            @NonNull String input) {
        return null;
    }

    @Nullable
    @Override
    public final Uri parseResult(int resultCode, @Nullable Intent intent) {
        if (intent == null || resultCode != Activity.RESULT_OK) return null;
        return intent.getData();
    }
}
```
This is specific code of `GetContent`. 
`createIntent()` takes a `Context` and the input and constructs the `Intent` that will be used with `startActivityForResult()`.
`parseResult()` produces the output from the given `resultCode` and the `Intent`.
It still use internally `resultCode`, `data` of `onActivityResult` we used before.

<br>
<br>

Default Contracts. Just use it!🥴
-----------------------------------
We’ve looked at `GetContent` as a Contract. There are some standard activity call contracts, as provided by android.
```
CreateDocument
GetContent
GetMultipleContents
OpenDocument
OpenDocumentTree
OpenMultipleDocuments
PickContact
RequestMultiplePermissions
RequestPermission
StartActivityForResult
StartIntentSenderForResult
TakePicture
TakePicturePreview
TakeVideo
```
[see detail description][android-docs]

<br>
<br>

Creating a Custom Contract📝
-------------------------------
```kotlin
class PickRingtone : ActivityResultContract<Int, Uri?>() {
    override fun createIntent(context: Context, ringtoneType: Int) =
        Intent(RingtoneManager.ACTION_RINGTONE_PICKER).apply {
            putExtra(RingtoneManager.EXTRA_RINGTONE_TYPE, ringtoneType)
        }

    override fun parseResult(resultCode: Int, result: Intent?) : Uri? {
        if (resultCode != Activity.RESULT_OK) {
            return null
        }
        return result?.getParcelableExtra(RingtoneManager.EXTRA_RINGTONE_PICKED_URI)
    }
}
```

```kotlin

class MainActivity : AppCompatActivity() {

    val getRington = registerForActivityResult(PickRingtone()) {
        //handle the returned uri
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        btn.setOnClickListener {
            getRington.launch(1)
        }
    }
}
```



[android-docs]: https://developer.android.com/reference/androidx/activity/result/contract/ActivityResultContracts

