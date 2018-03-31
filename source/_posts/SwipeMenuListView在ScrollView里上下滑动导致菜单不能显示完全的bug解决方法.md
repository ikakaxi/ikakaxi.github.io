title: SwipeMenuListView在ScrollView里上下滑动导致菜单不能显示完全的bug解决方法
author: superman
date: 2018-02-07 18:10:01
categories:
- android
tags:
- SwipeMenuListView
- ScrollView
---
这是因为上下滑动的时候,事件被ScrollView截获了,这时候应该禁止ScrollView截获上下滑动事件,解决方法如下
<!--more-->

```
public class NoRollSwipeMenuListView extends SwipeMenuListView {

    private GestureDetector mGestureDetector;

    public NoRollSwipeMenuListView(Context context) {
        super(context);
        mGestureDetector = new GestureDetector(context, onGestureListener);
    }

    public NoRollSwipeMenuListView(Context context, AttributeSet attrs) {
        super(context, attrs);
        mGestureDetector = new GestureDetector(context, onGestureListener);
    }

    public NoRollSwipeMenuListView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        mGestureDetector = new GestureDetector(context, onGestureListener);
    }

    public void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2, MeasureSpec.AT_MOST);
        super.onMeasure(widthMeasureSpec, expandSpec);
    }

    @Override
    public boolean onTouchEvent(MotionEvent ev) {
        boolean b = mGestureDetector.onTouchEvent(ev);
//        LogUtil.w("onTouchEvent", "mGestureDetector.onTouchEvent(ev)->" + b);
        return super.onTouchEvent(ev);
    }

    private GestureDetector.OnGestureListener onGestureListener = new GestureDetector.SimpleOnGestureListener() {

        //distanceX 左右滑动距离,左滑动正值,右滑动负值
        //distanceY 上下滑动距离,上滑动正值,下滑动负值
        @Override
        public boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY) {
            if (Math.abs(distanceY) >= Math.abs(distanceX)) {//上下滑动距离大于左右滑动距离,当作上下滑动
//                LogUtil.w("onScroll", "distanceX=" + distanceX + ":distanceY=" + distanceY);
//                LogUtil.w("onScroll", "true");
                //上下滑动不做任何操作,在这里父ScrollView已经交出onTouch权限,否则如果权限在父ScrollView的话这里接收不到事件
                //所以执行到这里是因为下面的setParentScrollAble(false);已经执行过了
                return true;
            }
            //当滑动NoRollSwipeMenuListView的时候，让父ScrollView交出onTouch权限，也就是让父ScrollView停住不能滚动
            setParentScrollAble(false);
//            LogUtil.w("onScroll", "false");
            return false;
        }
    };

    /**
     * 是否把滚动事件交给父ScrollView
     *
     * @param flag
     */
    private void setParentScrollAble(boolean flag) {
        //这里的parentScrollView就是NoRollSwipeMenuListView外面的那个ScrollView
//        LogUtil.w("setParentScrollAble", "flag->" + flag);
        getParent().requestDisallowInterceptTouchEvent(!flag);
    }
}
```