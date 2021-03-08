---
title: "[Jetpack Compose] LazyColumn: Escape from Recyclerview"
date: 2021-03-08 15:50:28 -0400
categories: android compose
---

Last month, Jetpack Compose Beta was released!  
[see Beta Overview][beta-overview]  
  
Even if it's beta, looks cool for some super advantages.  
In this posting, I will focus on ```LazyColumn``` which will replace ```RecyclerView```.  
<br>
Now I'm testing Compose Beta on Android Studio Preview(Canary version) on M1 Macbook Pro.  
it's slow as HELL!!! (normal Android Studio is not much slow)  
If you have a good Mac already, it's not time to buy M1 Mac yet.  
<br>
Anyway before dive into code, [Add Jetpack Compose to an existing project][add-jetpack-compose]  
<br>
<br>
<br>

What is an Column?
---------------------
```Column``` is a layout composable that places its children in a vertical sequence.  
It places its children like ```LinearLayout``` in Android Views.  
<br>

```kotlin
@Composable
fun ColumnTest() {
    Column {
        Text("Hello Compose")
        Image(painterResource(R.drawable.background), null)
        Spacer(Modifier.preferredHeight(16.dp))
        Text("Work from Home")
    }
}
```
To make it scrollable, use modifiers like this ```Column(Modifier.verticalScroll(rememberScrollState()))```  
<br>
<br>
<br>

LazyColumn🐌
------------
```kotlin
class ComposeActivity : ComponentActivity() {

    val itemList = (0..50).toList()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            NameList(itemList)
        }
    }

    @Composable
    fun NameList(name: List<Int>) {
        LazyColumn {
            items(name) {
                Text("Item is $it", color = Color.Blue)
            }
        }
    }
}
```
That's it. You don't need to make annoying Adapter at all!!  
<br>
```LazyColumn``` doesn't recycle its children like ```RecyclerView```. It emits new Composables as you scroll through it and is still performant as emitting Composables is relatively cheap compared to instantiating Android Views.  
<br>
It's a basic usage, you can also set items like below . 
<br>
```kotlin
val itemsList = (0..5).toList()
val itemsIndexedList = listOf("A", "B", "C")

LazyColumn {
    items(itemsList) {
        Text("Item is $it")
    }

    item {
        Text("Single item")
    }

    itemsIndexed(itemsIndexedList) { index, item ->
        Text("Item at index $index is $item")
    }
}
```
<br>

Parameters of LazyColumn:
```kotlin
fun LazyColumn(
    modifier: Modifier = Modifier,
    state: LazyListState = rememberLazyListState(),
    contentPadding: PaddingValues = PaddingValues(0.dp),
    reverseLayout: Boolean = false,
    verticalArrangement: Arrangement.Vertical =
        if (!reverseLayout) Arrangement.Top else Arrangement.Bottom,
    horizontalAlignment: Alignment.Horizontal = Alignment.Start,
    flingBehavior: FlingBehavior = ScrollableDefaults.flingBehavior(),
    content: LazyListScope.() -> Unit
)
```
Description:
>modifier - the modifier to apply to this layout.  
><br>
>state - the state object to be used to control or observe the list's state.  
><br>
>contentPadding - a padding around the whole content. This will add padding for the. content after it has been clipped, which is not possible via modifier param. You can use it to add a padding before the first item or after the last one. If you want to add a spacing between each item use verticalArrangement.  
><br>
>reverseLayout - reverse the direction of scrolling and layout, when true items will be composed from the bottom to the top and LazyListState.firstVisibleItemIndex == 0 will mean we scrolled to the bottom.  
><br>
>verticalArrangement - The vertical arrangement of the layout's children. This allows to add a spacing between items and specify the arrangement of the items when we have not enough of them to fill the whole   minimum size.
><br>
>horizontalAlignment - the horizontal alignment applied to the items.  
><br>
>flingBehavior - logic describing fling behavior.  
><br>
>content - a block which describes the content. Inside this block you can use methods like LazyListScope.item to add a single item or LazyListScope.items to add a list of items.  
<br>
<br>
<br>

What is different *ScrollableColumn* and *LazyColumn*?
---------------------------------------------------------
>```ScrollableColumn``` is a wrapper of ```Column``` using a ```verticalScroll``` modifier. It renders all its children at the same time. Its equivalent in Android Views is ```ScrollView```  
><br>
>```LazyColumn``` renders only the visible items on screen, allowing performance gains when rendering a big list. It's equivalent in Android Views is ```RecyclerView```  
><br>
>As the list holds thousands of items, which will impact the app's fluidity when rendered, use ```LazyColumn``` to only render the visible elements on the screen rather than all of them.  
<br>
<br>
<br>

********************

If there's a mistake, always welcome your opinion!  


[beta-overview]: https://youtu.be/Ef1xKWjA9E8
[add-jetpack-compose]: https://developer.android.com/jetpack/compose/setup#add-compose
