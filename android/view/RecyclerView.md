#### SmoothScrollToPosition Center

~~~kotlin
private val centerScroll by lazy {
        object : LinearSmoothScroller(context) {
            override fun calculateDtToFit(viewStart: Int, viewEnd: Int, boxStart: Int, boxEnd: Int, snapPreference: Int): Int {
                return (boxStart + (boxEnd - boxStart) / 2) - (viewStart + (viewEnd - viewStart) / 2)
            }
        }
    }


fun ScrollToPositionCenter(position: Int) {
        centerScroll.targetPosition = position
        layoutManager?.startSmoothScroll(centerScroll)
    }
~~~



https://stackoverflow.com/questions/38416146/recyclerview-smoothscroll-to-position-in-the-center-android

