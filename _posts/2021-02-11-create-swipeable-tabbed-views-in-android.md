---
layout: post
---
How to create a tabbed layout view in Android with *Different* Fragments

In this tutorial we will be using viewpager2, tablayout and fragments.
The viewpager will hold fragments that are all different and thus allow one to create different views 
within each tab while providing a beautiful interface.

In order to create a tabbed layout view first make sure you have android material design as a
dependent in your gradle build file:
```gradle
implementation 'com.google.android.material:material:1.2.1'
```
At the time of this writing 1.2.1 is the latest version but do make sure you have the latest version.
This is necessary since we will be using material design's tabs.

Next create a new file in layout titled fragment_main_pager.
Go ahead and set it as a LinearLayout, then add in a TabLayout and then a ViewPager2.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.google.android.material.tabs.TabLayout
        android:id="@+id/tab_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabMode="scrollable" />

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/fragment_main_pager"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1" />

</LinearLayout>
```
The important part of this layout is **line 12**. 
This is used if we have a large number of tabs so the user can scroll through them
rather than try fitting every single one on the screen. However if you know the number of tabs
will be small then you can omit that line. 


Next let's create a fragment called MainPagerFragment.

```kotlin
class MainPagerFragment : Fragment() {
    private lateinit var musicCollectionAdapter: MusicCollectionAdapter
    private lateinit var viewPager: ViewPager2

    override fun onCreateView(
            inflater: LayoutInflater,
            container: ViewGroup?,
            savedInstanceState: Bundle?,
    ): View? {
        return inflater.inflate(R.layout.fragment_main_pager, container, false)
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        mainCollectionAdapter = MainCollectionAdapter(this)
        viewPager = view.findViewById(R.id.fragment_main_pager)
        viewPager.adapter = mainCollectionAdapter

        val tabLayout = view.findViewById(R.id.tab_layout) as TabLayout
        TabLayoutMediator(tabLayout, viewPager) { tab, position ->
            when(position) {
                0 -> tab.text = "FRAGMENT1" //titles for each tab
                1 -> tab.text = "FRAGMENT2"
                2 -> tab.text = "FRAGMENT3"
                3 -> tab.text = "FRAGMENT4"
                else -> tab.text = "FRAGMENT5"
            }
        }.attach()
        viewPager.currentItem = musicCollectionAdapter.getItemId(2).toInt()

    }
}
```
I would like to bring attention to lines 19-27. Here we use **TabLayoutMediator**
which is used to connect our TabLayout and the ViewPager2 together and attach it to the view.
Further, line 28 sets our view to the second item in the list of views. So when you open the app
it will open up to the third tab which is FRAGMENT3. Feel free to omit this line if you rather have the viewpager
open up to the first view instead.

The very last part to make this work is we need to create the adapter for our ViewPager2.

This is where we incorporate the use of creating 5 different fragments that are each in their
own tab. Make note of the fact that you must replace the 5 lines of fragments below with your own fragments.

```kotlin
class MainCollectionAdapter(fragment: Fragment) : FragmentStateAdapter(fragment) {

    //set this to the number of fragments you have
    override fun getItemCount(): Int = 5 

    override fun createFragment(position: Int): Fragment {

        return when (position) {
            0 -> Fragment1() //replace these lines with your own fragments
            1 -> Fragment2() //make sure they match up with titles of the tabs
            2 -> Fragment3() //you set in MainPagerFragment
            3 -> Fragment4()
            else -> Fragment5()
        }
    }
}
```
There you have it! A ViewPager that has tabs and creates 5 different fragments
that reside in their respective tab.
Please reach out if you have any questions as I would be happy to answer them!
