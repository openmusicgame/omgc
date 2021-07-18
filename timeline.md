# 时间线

OMGC谱面主体由时间线(Timeline)构成，其中包含了音符源控制器(Note Source Controller)，判断区域控制器(Judge Area Controller)，媒体控制器(Audio & Video Controller)，谱面效果(Effect)，游戏反馈(Feedback)等多种可动画元素(Animatable)。 每个谱面均有一条默认时间线，名称为`main`，但也可以定义多条时间线来组成故事版(Storyboard)，以完成更加精细的控制。

时间线相比传统的简单音符容器，可以更方便地完成基于时间的控制，时停、倒退、延迟、变速、多BPM、多曲目、缓动(Ease)等等的设置，就和使用普通音视频编辑工具一样容易。

另外，在OMGC中空间（坐标系统）和时间一样也是一等公民，可以将效果、坐标和时间线结合，成为制作观赏谱的有力工具。

## 成员关系

* Animatable 可动画元素
    * Note 音符
        * Tap 点按
        * Hold 长按，包括直线无尾判滑条
        * Flick 滑键（粉键），包括定向滑键
        * Wipe 拖动键（黄键）
        * Hit 连打键
        * CombinedNote 组合音符，比如带尾判的长条
            * Slide 滑条（绿条、蛇），可以包含头判，尾判，节点和任意形状的本体长按，以及效果音符
        * EffectNote 效果音符
            * Score 加分键
            * Heal 回血键
            * Skill 技能键
            * Loot 掉落键，击中可以获得掉落物
            * Damage 伤害键，例如MuseDash的齿轮
    * JudgeArea 判定区域
        * JudgePoint 判定点
        * JudgeLine 判定线
        * JudgeRange 二维判定区域
    * Camera （场景视口）相机
    * OutputDevice 输出设备
        * Vibrator 振动器
        * LightSource 光源，包括普通光源，LED阵列等等
    * Effect 效果
        * Transform 2D/3D图形变换
            * Translate 平移
            * Rotate 旋转
            * Scale 缩放
            * Skew 倾斜/切变
        * TimeEffect 时间效果
            * SpeedChange 变速，包括加减速、时停、倒退
            * Seek 时间点跳跃
        * SceneEffect 场景效果
            * SceneSwitch 场景切换
            * SkinChange 皮肤切换
            * ModeChange 更改模式
            * Fever 触发Fever 
        * GameControl 对宿主游戏本体进行控制，例如各种彩蛋谱面
        * \<ObjectType>Animation 针对各种类型的属性提供的变换，例如变色，修改透明度，修改移动路径等等
    * UIElement 界面元素
        * TextBlock （富）文本块
        * Image 图像
    * AsyncAnimatable 异步可动画元素，随时间点触发，但可以后台运行，不需要等待其结束
        * Event 事件
        * Action 动作
            * Feedback 游戏反馈
            * Script 脚本
        * Timer 计时器
    * Timeline 时间线
        * MediaTimeline 媒体时间线
            * AudioController 音频控制器
            * VideoController 视频控制器
            * DeviceController 设备控制器
        * ParallelTimeline 并行时间线
            * Storyboard 故事版
            * EffectGroup 效果组
            * NoteGroup 音符组
            * MusicGroup 曲目组
        * NoteSourceController 音符源控制器
        * JudgeAreaController 判定区域控制器
        * HitAreaController 打击区域控制器

