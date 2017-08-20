# 如何保证ImageView.setImageResource()传入.9图片时展示正常？

> ImageView占位图可以通过backgroud设置.9图片实现，但是在src设置成带透明alpha图片时会透视显示背景，所以用src实现占位图更合理，但是scaleType的裁剪方式同样会影响到作为src的.9图片,这将会导致.9图片不能按照预期展示占位图

## 现象

*  ic_launcher.9.png
![.9图片](http://imglf0.nosdn.127.net/img/Q3dzYVVKdDlxT21CTEpLdmhuT3VzVDl6Q0RsWVdKcGlGMWNnQThGbU90OS8wSFAzd1NUWDVBPT0.jpg?imageView&thumbnail=500x0&quality=96&stripmeta=0&type=jpg)


* deafault & centerCrop 同样裁剪中间显示

```<ImageView
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:src="@drawable/ic_launcher" />
```

```<ImageView
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:scaleType="centerCrop"
        android:src="@drawable/ic_launcher" />
```

![默认](http://imglf1.nosdn.127.net/img/Q3dzYVVKdDlxT21CTEpLdmhuT3VzVmloSGNFZldDOUcvMTcyeW5DUTlWS1IvREJyTGc1cWNnPT0.jpg?imageView&thumbnail=500x0&quality=96&stripmeta=0&type=jpg)

* fitXy显示如预期

```
    <ImageView
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:scaleType="fitXY"
        android:src="@drawable/ic_launcher" />

```
![fitxy](http://imglf2.nosdn.127.net/img/Q3dzYVVKdDlxT21CTEpLdmhuT3VzVFo5eTJUZTdmTDY4U2VWb3FUTVNCUUV4MXg0L216Ykx3PT0.jpg?imageView&thumbnail=500x0&quality=96&stripmeta=0&type=jpg)


## 问题分析 (read the fucking source code)

1. mScaleType怎么参与图片裁剪逻辑？
2. 图片裁剪在什么时机执行？
3. 如何干预裁剪方式？

第一个问题很简单ImageView.configureBounds(), 根据不同的case提供不同的换算策略，但是google用private告诉你，小子这不给你改！！！

```
    private void configureBounds() {
          
            ...
        
            if (ScaleType.MATRIX == mScaleType) {
                // Use the specified matrix as-is.
                if (mMatrix.isIdentity()) {
                    mDrawMatrix = null;
                } else {
                    mDrawMatrix = mMatrix;
                }
            } else if (fits) {
                // The bitmap fits exactly, no transform needed.
                mDrawMatrix = null;
            } else if (ScaleType.CENTER == mScaleType) {
                // Center bitmap in view, no scaling.
                mDrawMatrix = mMatrix;
                mDrawMatrix.setTranslate(Math.round((vwidth - dwidth) * 0.5f),
                                         Math.round((vheight - dheight) * 0.5f));
            } else if (ScaleType.CENTER_CROP == mScaleType) {   
            ...
            } else if (ScaleType.CENTER_INSIDE == mScaleType) {
            ...
            } else {
            ...
            }
        }
    }
```
 来看下第二个问题，configureBounds()都在哪调用？
  1. setImageMatrix(...) 
  2. updateDrawable(...) <-- setImageResource(...)
  3. setFrame(...)
  
分析不难发现在绘制前这1，2两个方法并不会触发configureBounds(),所以问题指向3,setFrame的调用在View的layout(...)方法中

```
public void layout(int l, int t, int r, int b) {

...
        int oldL = mLeft;
        int oldT = mTop;
        int oldB = mBottom;
        int oldR = mRight;

        boolean changed = isLayoutModeOptical(mParent) ?
                setOpticalFrame(l, t, r, b) : setFrame(l, t, r, b);

...
        mPrivateFlags &= ~PFLAG_FORCE_LAYOUT;
        mPrivateFlags3 |= PFLAG3_IS_LAID_OUT;
    }
```
 至此一和二两个问题解开迷雾，梳理下调用流程(-> 下一个方法 --> 方法内调用)
 
 view->measure->layout-->setFrame-->configureBounds()->draw
 
 不难分析只有在layout和draw之间动刀才可以干预裁剪方式，layout和setFrame都可以切入，根据最小作用域原则，更适合在setFrame处理.
 
 
 
## 解决办法

自定义ImageView重写setFrame()如下

```
public class NinePatchIamgeView extends ImageView {

    public NinePatchIamgeView(Context context) {
        super(context);
    }

    public NinePatchIamgeView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }

    public NinePatchIamgeView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }

    @Override
    protected boolean setFrame(int l, int t, int r, int b) {
        // 当前ScaleType
        ScaleType curScaleType = getScaleType();
        // .9图片裁剪前设置临时类型ScaleType.FIT_XY
        if (getDrawable() instanceof NinePatchDrawable) {
            setScaleType(ScaleType.FIT_XY);
        }
        boolean changed = super.setFrame(l, t, r, b);
        // 还原ScaleType
        setScaleType(curScaleType);
        return changed;
    }
}
```



