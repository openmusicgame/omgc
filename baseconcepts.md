# 基本概念

## 格式设计理念
OMGC谱面的设计理念与大部分音游不同，核心元素不是音符，也不是判定线，而是时间与空间。

类似常规的音视频与动画编辑软件，时间的核心元素是时间线(Timeline)，一种声明式的表达在经典时间轴上发生事件的方式。

同样，类似常规的绘画与图形编辑软件，空间的核心元素是坐标。OMGC的坐标和普通的2D/3D软件不同，不拘于数学上的直角坐标或者极坐标等，可以通过任意个维度和对应的单位描述物体的位置。例如，对于传统有轨下落式音游，只需要`{轨道编号，y轴位移}`两个坐标即可定位；而对于`Arcaea`的音弧，则需要增加第三个维度描述在竖直方向上的位移。

时间线作为容器，可以包含普通对象和其它子时间线元素，从而对控制的对象进行分组。最常用的时间线容器是音符源控制器(NoteSourceController, NSC)，它可以包含若干个音符，通过一个基准坐标生成一个新的坐标系，从而根据时间线和其它参数控制一组音符的生命周期和交互。比如在`Phigros`中，每一根判定线都可以看做1个NSC，从属于该判定线的音符作为NSC控制的对象，通过判定线定位点产生的相对坐标系描述这一组音符的位置。当然，也可将判定线上下2面的音符拆分为2个NSC，做更加精细的运动控制。