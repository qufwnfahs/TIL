# CoordinatorLayout 알아보기

### 1. 개요

[https://developer.android.com/jetpack/androidx/releases/coordinatorlayout?hl=ko](https://developer.android.com/jetpack/androidx/releases/coordinatorlayout?hl=ko)

</br>

`AppbarLayout` 및 `FloatingActionButton`과 같은 최상위 어플리케이션 위젯을 배치한다.

### 2. dependency - build.gradle (app)

```kotlin
dependencies {
		implementation "androidx.coordinatorlayout:coordinatorlayout:1.1.0"
}
```

### 3. 기본 구조

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout 
		xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/appbar"
        android:layout_height="300dp"
        android:layout_width="match_parent">

        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:toolbarId="@+id/toolbar"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            app:contentScrim="?attr/colorPrimary">

            <androidx.appcompat.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_height="?attr/actionBarSize"
                android:layout_width="match_parent">

            </androidx.appcompat.widget.Toolbar>

        </com.google.android.material.appbar.CollapsingToolbarLayout>

    </com.google.android.material.appbar.AppBarLayout>

    <androidx.core.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="com.google.android.material.appbar.AppBarLayout$ScrollingViewBehavior">

    </androidx.core.widget.NestedScrollView>

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

### 4. AppbarLayout

[https://developer.android.com/reference/com/google/android/material/appbar/AppBarLayout](https://developer.android.com/reference/com/google/android/material/appbar/AppBarLayout)

</br>

**AppbarLayout**은 `Scrolling Gesture`의 많은 특징을 구현한  `Vertical LinearLayout` 이다.

자식들은 `setScrollFlags(int)` 함수를 이용하여 원하는 `scrolling behavior`를 제공받을 수 있다.

(xml로는 `app:layout_scrollFlags`)

</br>

**AppbarLayout**은 **CoordinatorLayout**에 **Direct Child**로 사용되는 것에 크게 의존한다.

또한, 언제 스크롤해야 하는지 알기 위해 별도의 스크롤링 형제가 필요하다. (ex : `NestedScrollView`)

### 4.1 scrollFlags

> scroll | enterAlways

- 스크롤의 위치와 상관없이 아래로 이동 시엔 **AppbarLayout**이 사라졌다가 위쪽으로 이동하게 되면 **AppbarLayout**이 먼저 보여진 후 스크롤이 된다.

- 스크롤 이동 시 바로 메뉴 같은게 보여지고 싶을 때 사용하면 유용하다.

> scroll | enterAlways | enterAlwaysCollapsed

- 스크롤의 위치와 상관없이 아래로 이동 시엔 **AppbarLayout**이 사라졌다가 위쪽으로 이동하게 되면 `Toolbar의 Height` 만큼만 내려오고 스크롤이 최상단에 도착 시 나머지 **AppbarLayout**의 전체가 내려오게 된다.

> scroll | exitUntilCollapsed

- 스크롤을 아래, 위로 이동 시 `Toolbar 의 Height` 만큼만 보여지고 스크롤이 최상단에 도착 시 나머지 **AppbarLayout**의 전체가 내려오게 된다.

- **AppbarLayout**이 완전히 사라지지 않는다.

> scroll | snap

- `snap` 은 마치 자석에 달라붙는 것 같은 느낌으로 `AppbarLayout Height의 절반`을 기준으로 아래 위로 달라 붙는 **flag**이다.

</br>

### 5. CollapsingToolbarLayout

[https://developer.android.com/reference/com/google/android/material/appbar/CollapsingToolbarLayout](https://developer.android.com/reference/com/google/android/material/appbar/CollapsingToolbarLayout)

`CollapsingToolbarLayout`은 `Collapsing App Bar`를 구현한 `Toolbar`를 위한 `wrapper`이다.

**AppbarLayout**의 **직속 자식**으로 사용되기로 고안되었다.

### 5.1 Collapsing title

- **AppbarLayout**의 **확대 / 축소**에 따라 크기가 달라지는 Title 이다.

- `setTitle(CharSequence)` 를 통해 보일 **Title** 을 설정할 수 있다.

- **Title Appearence** 는 `collapsedTextAppearence` 와 `expandedTextAppearence` 속성을 통해 수정될 수 있다.

### 5.2 Content scrim

- **scroll position**이 특정 임계값을 건드리면 보여지거나 숨어지는 `full-bleed scrim`

- `setContentScrim(Drawable)` 로 변경

### 5.3 Status bar scrim

- **scroll position**이 특정 임계값을 건드리면 보여지거나 숨어지는 `scrim`

- `setStatusBarScrim(Drawable)` 로 변경

- `fitsSystemWindows = true` 필요

### 5.4 Parallax scrolling children

- 하위 뷰는 시차 방식으로 이 레이아웃 내에서 스크롤되도록 선택할 수 있다.

- `app:layout_collapseMode="parallax"`

- `setParallaxMultiplier(float)` : `0.0f` **to** `1.0f`

### 5.5 Pinned position children

- 스크롤에 상관없이 View를 고정시킬 수 있다.

- **Do not manually add views to the Toolbar at run time.**

</br>

### 참고

[https://android.jlelse.eu/parallax-scrolling-header-tabs-android-tutorial-2cc6e40aa257](https://android.jlelse.eu/parallax-scrolling-header-tabs-android-tutorial-2cc6e40aa257)
