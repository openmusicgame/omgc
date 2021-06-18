# 谱面定义

OMGC谱面可以使用JSON、XML或者YAML等多种格式储存，考虑到JSON的繁琐以及YAML缩进带来的语义不明确，下文所有的示例均以`JSON5`格式进行描述。

OMGC谱面兼容kubernetes的通用资源定义，也就是说甚至可以将YAML格式的谱面作为CRD导入k8s集群中！关于k8s资源定义的细节可以查看[此处](https://kubernetes.io/docs/reference/kubernetes-api/common-definitions/)。

## 0. 基本规则说明

### 0.1. 类型说明

综合参考了大多数编程语言的类型系统，最后选择了以`TypeScript`类型为主，辅以其它表达方式的类型。

> 没有说明的情况下，引用类型默认可为空(指空指针或者空引用，在不同语言里可能表示为null, NULL, nil, Nothing, nullptr等)，值类型（各种数字类型和布尔类型）默认不可空。

|类型|说明|
|--|--|
|*|任何可以JSON反序列化的类型|
|unknown|某一种具体类型，根据实际情况判断|
|void|没有类型或者不可能的类型|
|T!|不可为空的T类型|
|T?|可为空的T类型|
|A\|B|A类型或B类型|
|A&B|同时符合A类型和B类型要求的类型，如果A和B没有交集，则结果是void|
|(A p1, string ...args) => void|一个具有A类型的参数p1，string[]类型的可变长参数args，无返回值的函数|
|{ string A, number B }|一个对象，至少具有string类型的A和number类型的B两个字段|
|T[]或者Array\<T>|T类型的线性表，可以是数组或者List等集合类型|
|Dictionary\<T>|键为string，值为T类型的字典
|string|字符串|
|number|任意数字类型，默认为64位浮点数|
|boolean|布尔类型|
|int8, uint32, float64等|固定长度的数字类型|


### 0.2. 自定义标签说明

自定义标签需符合[kubernetes的标签语法](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/)，即包含一个可选的前缀和名称，中间用`/`进行分隔。

为了便于区别，通用标签不设前缀，和游戏相关的标签请使用游戏包名作为前缀，自己设的私有标签请自行添加前缀。


## 1. 基本结构

一个OMGC谱面文件包含三大部分：

* 类型定义（文件头）
* 资源元数据
* 谱面数据

三个部分均必须存在，不区分先后顺序。

```json5
{
    // 类型定义
    apiVersion: "omgc.io/v1alpha",
    kind: "Chart",
    // 资源元数据
    metadata: {
        name: "My Chart",
    },
    spec: {

    }
}

```
### 1.1. 类型定义

* string! `apiVersion`：为固定值`omgc.io/v1alpha`
* string! `kind`：对于谱面来说为固定值`Chart`

```json5
{
    apiVersion: "omgc.io/v1",
    kind: "Chart"
}

```
### 1.2. 资源元数据

存放在`metadata`字段中，仅 `name`字段是必需的，用于唯一地标记一个谱面资源，只能包含大小写字母数字下划线和横杠。可以使用`labels`字段添加额外标签信息。

```json5
{
    metadata: {
        name: "test_MyChart-v1"
    }
}

```
### 1.3. 谱面数据

存放在`spec`字段中。谱面数据分为以下部分：

* 谱面基本信息
* 谱面元数据
* 资源引用
* 特性声明
* 谱面本体


1. 谱面基本信息

    直接包含在`spec`字段中，用于储存展示在谱面列表中所需的基本信息，允许自行添加字段。可以使用`labels`字段添加标签。

    * string! `name`：谱面的展示名称，必填，不建议同名
    * unknown `id`：谱面在数据库储存所用的主键，各个实现系统可以自行生成值，若无需数据库储存可以不填
    * string! `uid`：谱面的全局唯一标识ID，必填，由工具自动生成。跨系统数据交互以这个值为准
    * string! `version`: 谱面版本，必填，遵循**semver**规范
    * string `author`：谱面作者
    * string `description`：谱面描述
    * string `license`：许可协议，例如`MIT`、`CC BY-NC-SA 4.0`
    * string[] `keywords`：关键字，用于搜索
    * string `repository`：项目地址，可以填Git地址或者谱面网站的详情地址
    * string `level`：等级，例如`8.5`、`☆☆☆`或`?`
    * string `rank`：难度，例如`Easy`、`HD`或`大师`

2. 谱面元数据

    * Dictionary\<*> `metadata`：一个字典，键为对应元数据的名称，值不同于k8s的metadata，可以为任何可以被反序列化的类型。

    谱面元数据可以储存任何对整个谱面的附加信息，例如标识源导入游戏，支持的皮肤，支持的游戏场景效果等等。
    常用的预定义元数据如下：

    |名称|类型|描述|取值|
    |--|--|--|--|
    |preserved.source|string|标识从什么游戏/网站导入此谱面|
    |preserved.creator|string|标识使用什么工具制作此谱面|
    |keyCount|number|有轨下落式的键/轨道数|
    |type|string|玩法，默认key|key：有轨<br/>slide：无轨<br/>taiko：太鼓<br/>catch：Malody的接盘子<br/>other：其它|
    |hasKeySound|boolean|谱面支持Key音（而不是普通按键音）|
    |isRandom|boolean|谱面支持随机|


3. 资源引用

    存放在`rel`字段中，标识所有外部资源的依赖，例如歌曲文件、封面图片、皮肤等。
    
    除下列预定义字段之外，允许自行添加字段。

    1. MusicInfo `music`：歌曲信息。可以使用`labels`字段添加标签。
        * string! `name`：歌曲完整原名，必填
        * unknown `id`：歌曲在数据库储存所用的主键，各个实现系统可以自行生成值，若无需数据库储存可以不填
        * string! `uid`：歌曲的全局唯一标识ID，必填，由工具自动生成。
        * string `file`：文件地址，可以是文件的路径，也可以是http等其他形式可以被工具解析的URL。
        
            如果是文件路径，统一使用`/`进行目录分隔，使用`..`代表上级目录。不能以根目录或盘符开头，或者其他固定位置的路径。
        * string `cover`：主要封面/曲绘图片地址，有多个曲绘的，请存放在扩展字段
        * Dictionary\<number> `bpms`： BPM值，必填，键为每个BPM值开始时刻的毫秒数，值为BPM的值。匀速歌曲填一个值，变速歌曲按每一段不同的速度列出
        * number `length`： 歌曲长度，必填，以毫秒为单位
        * Dictionary\<string> `artists`： 艺术家信息，多个人用英文逗号分隔，可以增加字段
            * `lyricist`：作词
            * `composer`：作曲
            * `arranger`：编曲
            * `singer`：演唱
            * `featuring`：feat.

    2. string `readme`：readme文件的地址，默认会查找同文件夹下名为`README`或者`README.md`的文件

    3. string `license`：lisence文件的地址，默认会查找同文件夹下名为`LICENSE`的文件。如果此处没有值，但`spec.license`值存在并且是已知许可类型，则会显示默认许可

    4. KeySoundRef[] `keySounds`：Key音使用的音频文件地址
        * string! `name`：名称，必填，用于引用
        * string `path`：文件地址


4. 特性声明

    存放在`attrs`字段中，用于描述谱面用到的特性细节，例如随机谱，变速，音符特效，判定线变换，场景切换，自带皮肤等等。这也是个扩展字段，特性由各自游戏本体支持，不同游戏所能支持的特性不同，因此同一谱面转换后效果存在差异，但所包含的信息不会丢失。

    特性声明具有`apiVersion`字段，由于需要对游戏能力进行抽象，特性值可能会在自定义特性和通用特性之间调整，区分版本号可以方便区分不同版本之间特性字段的差异
