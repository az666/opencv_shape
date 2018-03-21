
## 计算机视觉分拣物体 ##
> 此项目，用于检测视频流中的圆形物体，项目计划用于学校创新比赛，自己做的计算机视觉分拣物品--主要针对一些简单的物品，形状规则，颜色明显的物品分拣。


----------

> 之后会更新一篇识别矩形或者非圆形的代码，正在筹划中，我想，只要一些简单的历程能够实现，基本就可以模拟整个场景的工作了。
##流程##

> 首先计算机摄像头处理传送带上的物体，快速扫描后，识别出物品，比如只有圆形和方形，计算机做出判断，然后将指令发送到单片机，单片机接受指令，做出相应动作，将物品分离--可以是机械臂，（简易一点的就是靠舵机直接驱动分离物品。）
![识别圆形物体](http://img.blog.csdn.net/20180321093527245?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3dpdGNoX2xvdmVfY2FzZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


----------

> 初级的部分代码如下：（检测圆形）


```
mport cv2
import  numpy as np
def detect_circle_demo ():
    video_capture = cv2.VideoCapture(0)
    while True:
        if not video_capture.isOpened():
            print('Unable to load camera.')
            break
        ret, img = video_capture.read()
        #img = cv2.pyrMeanShiftFiltering(img, 10, 25)
        gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)  # 灰度图像
        circles1 = cv2.HoughCircles(gray, cv2.HOUGH_GRADIENT , 1, 100, param1=100, param2=100, minRadius=50,maxRadius=200)
       # cv.HoughCircles(cimage, cv.HOUGH_GRADIENT, 1, 20, param1=50, param2=30, minRadius=0, maxRadius=0)
        try:  # 如果上一步没有检测到。执行try内容，就会报错。可以修改尝试看下。
            circles = circles1[0, :, :]  # 提取为二维
        except TypeError:
            print('未发现圆形物体！！')
        else:
            circles = np.uint16(np.around(circles))  # 四舍五入
            for i in circles[:]:
                cv2.circle(img, (i[0], i[1]), i[2], color=[0, 0, 0], thickness=2)  # 画圆
                cv2.circle(img, (i[0], i[1]), 2, color=[0, 255, 0], thickness=2)  # 画圆心
                cv2.putText(img, "center", (i[0] - 20, i[1] - 20), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 2)
                print(i[0],i[1])
                # 输出坐标
        # 显示视频
        cv2.imshow('Video', img)
        cv2.waitKey(10)
detect_circle_demo()


"""
dp，用来检测圆心的累加器图像的分辨率于输入图像之比的倒数，且此参数允许创建一个比输入图像分辨率低的累加器。上述文字不好理解的话，来看例子吧。例如，如果dp= 1时，累加器和输入图像具有相同的分辨率。如果dp=2，累加器便有输入图像一半那么大的宽度和高度。
minDist，为霍夫变换检测到的圆的圆心之间的最小距离，即让我们的算法能明显区分的两个不同圆之间的最小距离。这个参数如果太小的话，多个相邻的圆可能被错误地检测成了一个重合的圆。反之，这个参数设置太大的话，某些圆就不能被检测出来了。
param1，有默认值100。它是method设置的检测方法的对应的参数。对当前唯一的方法霍夫梯度法，它表示传递给canny边缘检测算子的高阈值，而低阈值为高阈值的一半。
param2，也有默认值100。它是method设置的检测方法的对应的参数。对当前唯一的方法霍夫梯度法，它表示在检测阶段圆心的累加器阈值。它越小的话，就可以检测到更多根本不存在的圆，而它越大的话，能通过检测的圆就更加接近完美的圆形了。
minRadius，默认值0，表示圆半径的最小值。
maxRadius，也有默认值0，表示圆半径的最大值
"""
```
