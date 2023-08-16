# GAL中的标注弹窗功能——Renpy系列1
`注：面对没有python和renpy基础的萌新而设计的易理解，好修改的系统，代码简陋，请见谅哦~`

---



@[TOC](文章目录)

---

# 前言



这几天看算法看麻了，于是想起了手头上的一个小小小项目还急着优化，趁着这个机会发一篇博客~

这次的目标是实现一个标注弹窗的小功能，当我们把鼠标拖上黑体文字的时候可以显现出一些相关的标注

---



# 一、探寻实现思路——不要用hotspot！！！

根据我查找的大量资料，我发现官方文档中有两个思路指向成功实现

第一个是使用Hotspot框架，第二个是使用Tooltip框架。

[文档出处](https://www.renpy.cn/doc/screens.html#hotspot)
![hotspot](https://img-blog.csdnimg.cn/2ccbbc6e70134f05a775e8d759c453f9.png)
>样例代码：
```python
screen preferences():

    tag menu
    use navigation

    imagemap:
        auto "gui_set/gui_prefs_%s.png"

        hotspot (740, 232, 75, 73) action Preference("display", "fullscreen") alt _("Display Fullscreen")
        hotspot (832, 232, 75, 73) action Preference("display", "window") alt _("Display Window")
        hotspot (1074, 232, 75, 73) action Preference("transitions", "all") alt _("Transitions All")
        hotspot (1166, 232, 75, 73) action  Preference("transitions", "none") alt _("Transitions None")

        hotbar (736, 415, 161, 20) value Preference("music volume") alt _("Music Volume")
        hotbar (1070, 415, 161, 20) value Preference("sound volume") alt _("Sound Volume")
        hotbar (667, 535, 161, 20) value Preference("voice volume") alt _("Voice Volume")
        hotbar (1001, 535, 161, 20) value Preference("text speed") alt _("Text Speed")
```

原理很简单，就是我们建立一个热区为button，当我们选中这个button的时候可以进行action操作。

但是据笔者浅薄的经验，只会用action来赋值实现开关以及调channel，ShowMenu（），Hide（）这种简单的操作，无法实现我们的鼠标聚焦选中（后来询问了高人发现Hotspot确实无法和hover建立起联系），文档中说的hover是指button的背景，也就是这个“ImageMap”的聚焦，并非指鼠标选中热区的hover。

上国外论坛查了一天然后只写了个这. . .后来发现可以用作游戏的“Press Start”操作，感觉离用Renpy开发移动端又近了一步~

```python
default exp_1 = False
screen hot1():
    imagemap:
        ground "images/transparent.png"
        hotspot (200, 800, 1960, 1280) action ShowMenu("exp1")
```
[知乎某Renpy论坛佬写的实现单机开始的Press Start](https://zhuanlan.zhihu.com/p/55301554)

效果大概是这样：
![Renpy](https://img-blog.csdnimg.cn/91a3366b1e8e4793911f9688ee5b70ed.png)


# 二、使用Tooltip以及其变式
## 1.实现思路一
>话不多说，上代码！！！

```python
default exp_1 = False
screen exp1():
    if exp_1:
        frame:
            padding (20, 20)
            align (.375, .81)
            has vbox
            textbutton (_("科技")):
                action Return("w")
                tooltip "{color=fff}{b}解释1:{/b}\n这是有关科技的介绍{/color}"
        # 这是界面上最后显示的内容。
        $ tooltip = GetTooltip()
        if tooltip:
            nearrect:
                focus "tooltip"
                prefer_top True
                frame:
                    xalign 0.5
                    text tooltip
init python:
    config.overlay_screens.append("exp1")
```

在script文件中的代码片段是这样的：

```python
$exp_1 = True
"这个世界，是凝聚了{color=#0000ffff}{b}科技{/b}{/color}和经济，无比伟大而光明的"
$exp_1 = False
```
这样就轻而易举地实现了开关的功能~

效果如下

（未hover）：
![未hover前](https://img-blog.csdnimg.cn/4c3dcdc726664bc9b075551247f83d1a.png)
（hover）：
![hover](https://img-blog.csdnimg.cn/66a5a1d97b5c4a2cb45bfcbbfcdc1977.png)
但这样确实丑，虚化一下“科技”这个词的背景应该会好很多w


## 2.实现思路二
>把上面那个方法稍微改进下，同时缩短一下描述效果，视觉效果就会有质的提升！！！

```python
screen exp1():
    $ tooltip = GetTooltip()
    if tooltip:
        text "[tooltip]" pos (600,700)
    if exp_1:
        textbutton (_("科技")):
            xalign 0.38
            yalign 0.84
            action NullAction()
            tooltip "{color=fff}{b}解释1:{/b}\n这是有关科技的介绍{/color}"
init python:
    config.overlay_screens.append("exp1")
```
（未hover）：
![未hover](https://img-blog.csdnimg.cn/da4535b69c6941f7b707800b99a25871.png)
（hover）：
![hover](https://img-blog.csdnimg.cn/b31a51c1ba5744c6950c101edadb2a6c.png)

后续的美化工作就交给后面的自己吧（感觉空空显示其实也比美工弄个文本框要好很多w）


---

# 总结


如果还有选择引擎的机会，一定要用Unity！！！Renpy是大坑，退！退！退！

