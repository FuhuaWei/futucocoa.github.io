---
layout: post
title:  "[译]Mac触摸栏人机界面指南"
date:   2016-11-17 10:10:11 +0800
tags: [mac]
author: bob,fallor,janisa,kamal,owen

---

本文翻译自苹果官方的[Mac Touch Bar Human Interface Guidelines](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/AbouttheTouchBar.html#//apple_ref/doc/uid/20000957-CH104-SW1)，辛苦团队成员`bob`, `fallor`, `janisa`, `kamal`, `owen`等同事。文章已经翻译完很久，今天才有时间整理外发。

##  1, Touch Bar概述


触摸栏位于最新款的MacBookPro键盘上方，是一个高清触摸屏，提供与主屏幕内容交互的动态界面控件。这些控件可根据当前上下文快速访问系统级和应用程序特定的功能。例如，当用户在文档中输入文本时，触摸栏可用于修改字体和调整字号。当用户查看地图时，触摸栏可以快速访问附近的加油站，住宿和餐馆等。触摸栏最右侧的Touch ID传感器跟iPhone上的Home键类似，支持指纹登录、购买App和Apple Pay支付。

![touch bar hero](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_hero_2x.png)

默认情况下，位于Touch Bar右侧的可扩展控件条（Control Strip）中包含了系统级操作的控件，如唤起Siri、调整主屏幕的亮度及音量等。而在此之前，用户是通过物理按键进行大多数的此类操作。你可以在位于控件条左侧的应用程序区域中，写入特定的应用控件。Esc（退出键）或其他系统按键会根据当前情况出现在应用区域的左侧。

![touch bar](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touch_bar_2x.png)

Touch Bar是可配置的。用户可以从控件条中移除功能，甚至将其完全隐藏。在隐藏状态下，仅显示应用控件。用户也可以隐藏应用程序区域，只显示扩展的控制条。有些应用也允许用户在应用区域中添加或删除操作。

若要在应用中以代码实现Touch Bar功能，请参阅 NSTouchBar的参考文档 。若想了解如何使用Xcode中的Interface Builder将Touch Bar控件添加到应用程序，请参阅 Xcode Help。

### Touch Bar的设计

在设计Touch Bar应用界面时，请遵循以下规范：

**设计情景化体验。**Touch Bar内容需与主屏幕当前内容相关。在应用程序中区分不同的场景，并根据应用程序的实际使用情况，思考如何曝光不同层级的功能。

**将Touch Bar看作键盘和触控板的延伸，而非显示器。**尽管在技术层面上Touch Bar就是屏幕，但它是被视作输入设备使用的，而非辅助显示器。用户可能会通过Touch Bar来定位或使用某个功能，但他们的焦点应该处于主屏幕之上。任何过分吸引用户注意力或者会影响主屏幕上首页任务的信息，如警告窗口、信息、滚动内容、静态内容等，都不应该在Touch Bar上展示。

**视觉效果尽量与实体键盘一致。**Touch Bar中的控件在大小和颜色方面应尽可能与实体键盘外观保持一致。

**不要单独地在Touch Bar中显示某项功能。**并非所有设备都有Touch Bar，用户也有可能选择禁用一个应用程序配置在Touch bar上的控件。应该始终提供能在键盘或触控板上执行任务的方式。

**控件应能立即生效。**提供更快捷的操作，否则用户需要用更多步骤来完成诸如点击控件或从菜单选取项目这样的任务。具体可查看Controls.

**立即响应用户操作。**即便应用在工作中或主屏幕正更新内容，Touch Bar中的任何已启用的控件也应能立即响应用户的操作。

**尽可能让在Touch Bar中启动的任务，在Touch Bar中完成。**用户不应该切换到键盘或触控板来完成任务，除非这项任务所要求的界面控件的复杂度超出了Touch Bar的支持范围。

**避免使用Touch Bar执行常见的快捷键任务。**一般来说，Touch Bar不提供包含查找、全选、取消选择、复制、剪切、粘贴、撤消、重做、新建、保存、关闭、打印和退出等操作，也不应该重复提供已有的键位导航，如向上翻页和向下翻页。

**一致并准确地反映状态。**如果控件同时处于Touch Bar和主屏幕之上，两处应呈现相同的状态。例如，如果一个按钮在主屏幕上是禁用状态，那么它在Touch Bar中也应为禁用状态。

**避免将Touch Bar上的交互行为镜像显示到主屏幕上。**例如，如果用户在Touch Bar中点击了按钮并显示了其选项列表，这些选项不应在主屏幕上显示。

## 2, 交互设计


### 无障碍访问

macOS为视障、听障和其他障碍用户提供了大量的无障碍访问功能。Touch Bar上的控件与标准的界面元素类似，能够被用户轻松访问。

**为控件提供替代文本**。Touch Bar上的控件的替代文本并不可见，但是能通过VoiceOver语音描述出来，使得视障用户能够更方便的操作和浏览。

**为所有用户可配置控件添加自定义文本**。VoiceOver会通过这些替代文本，来语音描述用户自定义屏幕上的控件。

### 自定义

大多数应用允许用户添加、删除和重新排列Touch Bar上的控件，从而满足其各自的工作方式。

**通常情况下，允许用户自定义**。用户使用应用的方式是无法预料的，因此重要和常用的功能需提供默认设置，但是在用户需要时，应允许用户进行一些调整来满足其独特需求。

### 全屏和聚焦内容型应用

运行在全屏模式下的应用提供了一个无干扰的工作环境。应用将工具栏和其他控件通常隐藏起来，只有当用户需要时才显示，例如将光标移动到屏幕顶部时。有的应用也会在主屏幕上隐藏控件以增强用户对内容的注意力。例如，当用户在QuickTime中播放电影或在Pohtos中播放幻灯片时，控件会被隐藏。通过在在Touch Bar中显示控件，用户可以直接访问常用功能，而无需移动光标或者查看覆盖在内容上的控件。

**提供相关和常用的控件**。当控件在主屏幕上隐藏时，Touch Bar上可能包含了唯一可见的控件。这些控件应该与主屏幕上用户浏览的内容相关，并且有用。

### 手势

用户可以通过以下手势和Touch Bar进行交互：

**点击**。启动一个控件，例如点击按钮。选择一个项目，例如选择表情符号，颜色或者分段控制段。

**长按**。在按钮等控件上发起辅助操作。例如，在邮件出于活动状态时，点击邮件上的“标记”按钮来为邮件添加标记，长按“标记”按钮会弹出窗口让你选择标记颜色。

**水平滑动**。滑动一个元素，例如从一侧向另一侧移动滚动条。通过滑动浏览内容，例如滑动浏览一个日期列表或者一组照片。

**多点触控**。虽然Touch Bar的确能够响应捏合操作等多点触控操作，但多点触控是很累赘的操作，应该谨慎使用。


## 3, 视觉设计


### 动画

**避免动画。**Touch Bar被认为是键盘的功能扩展，人们并不想在键盘上看到动画。此外，人们会被过度或者不必要的的动画效果打断，进而影响到工作。

### 颜色

macOS定义了一系列系统颜色，用来动态匹配标准接口控件（如按钮和标签）的配色方案。以下是Touch Bar的理想系统颜色：
- 控件颜色 (controlColor)
- 标签颜色 (labelColor)
- 二级标签颜色 (secondaryLabelColor)
- 三级标签颜色 (tertiaryLabelColor)
- 四级标签颜色 (quaternaryLabelColor)

这些系统颜色会自动响应环境光线和键盘背光等级等因素引起的系统参考白点的变化，并根据变化自动调整。

了解更多如何在应用程序中使用系统颜色，请参考本节有关系统颜色的文档。

**优先使用标准控件和系统图标。**标准控件和系统图标早已在Touch Bar上有了较为完善的色彩设计。具体请参照图标章节。
**少而精地使用色彩。**一般来说，Touch Bar应该和传统的物理键盘在外观上是一致的。单色模式应该是最好的选择。如果必须在控件中使用颜色，就要选择高雅些的颜色，并且在表示短暂、临时的状态时使用。不能过度使用颜色，也不能使用在不合适的地方。

![touch bar monochrome](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_monochrome_2x.png)

**用颜色凸显重要信息。**颜色能让人注意到重要的控件。保留蓝色用于默认控件，红色则保留用于警告型控件。
**在使用颜色时，选择一个与你的应用协调且有限的调色板。**巧妙颜色使用是宣传品牌的一种方式。

![touch bar branding 2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_branding_2x.png)

**提供色域广泛的设计稿。**Touch Bar可以支持P3色域，与sRGB相比，这种色域产生的颜色更丰富也更饱和。在16bits像素通道上使用Display P3呈现外形色彩并以.png的格式导出插图。

### 布局

Touch Bar的触摸区域（除去Touch ID的传感区后）的宽是2170px，高是60px。因为Touch Bar是高分辨率Retina屏幕，所以等同于宽1085pt高30pt。

![touch bar layout total 2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_layout_total_2x.png)

#### Touch Bar的区域

在标准配置中，Touch Bar包含三个主要区域。每个区域的间隔是32px，这是系统强制设定的。

|区域 |描述 |宽度 |
| :------ | :--- | :------- |
|控制条 |该区域包含系统级控制，例如用于启动Siri或调整音量级别。 |宽度可调，取决于用户的配置。它可以扩展到Touch Bar（在系统设置中被称为扩展控制条）的全宽，也可以折叠到Touch Bar的右侧，变窄、或完全隐藏。 默认情况下，控制条显示为折叠状态，并包含四个控件。|
|App范围 |应用程序的控件会显示在这个区域。|宽度可调，取决于用户配置。当控制条可见时，此区域的最小宽度为1370px。 用户可以选择完全隐藏此区域。|
|系统按钮 |基于上下文，系统显示一个按钮，例如退出(Esc)，取消(Cancel)或完成(Done)。|128px.|

![touch bar layout regions 2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_layout_regions_2x.png)

**设计时假设默认控制条是可见的。**虽然用户可以重新配置控制条，减小其尺寸，甚至完全禁用它，但是在设计应用程序时不能使用由于控制条的减小或者完全隐藏而变得空闲的空间。

#### 设置应用程序控件位置

该系统提供了几个选项用于分隔Touch Bar上的应用程序控件：

|间距类型|控件之间的宽度|
| :---- | :--- |
|默认|16px|
|固定位置（小）|32px|
|固定位置（大）|64px|
|非固定位置|根据可用空间的变化自动匹配|

**有条理且直观的位置控制。**应用程序区域的左侧适用于在不同模态之间持续的控件。例如，当Notes处于活动状态时，无论是在导航笔记，编辑笔记还是浏览附件，都会在触摸栏的最左侧显示用于添加注释的“撰写”按钮。否则，主控件最好居于Touch Bar的中间，其次是在左侧。

**构建灵活布局。**应用程序区域的宽度随着控制条的配置改变而改变，因此请考虑设置一定控件（例如slider控件和scrubber控件），当有更多空间可用时以便进行拉伸。

**尽量使用恒定间距。**尽可能使触摸栏中的控件等距，除非为了阅读清晰调整宽度，或对相关控件进行聚类需要调整宽度。

**用灵活空间和分组来帮助对齐。**控件之间灵活的空间会把左侧的控件向触摸杆的左侧推，右侧的控件朝向触摸杆的右侧推。分组允许您一次定位多个控件。您可以通过设置控件或组为触摸栏中的主要项目来使控件或组居中。

**不要自动重置控件位置。**控件自适应的位置的改变会让人们觉得奇怪和困惑。让用户通过自定义手动重新确定控件位置是可行的，但是应用程序应避免没有必要的重新定位。

**避免在从右到左阅读的语言环境中手动反转控件。**反转控制可能会导致Touch Bar自定义功能出现问题，并且系统具备了自动反转一定控件的功能，例如分段控件和滑动条。

#### 常见布局

由于可用的配置选项众多、控件尺寸范围广泛，触摸栏中的布局样式可能因应用程序而异。因此，请为你的控件使用常见的布局样式。

**流体布局。**此布局包括尺寸一致的控件，如按钮。

![](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_layout_example01_2x.png)

**有一个主要控件的布局。**这种情况是指在Touch Bar的中心包含单个大型控件，例如候选列表（在文本输入期间提供自动完成建议）。其他控件（如按钮和分段控件）位于左侧。

![touch bar layout example 04](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_layout_example04_2x.png)

**有两个主要控件的布局。**Touch Bar的中心包含两个尺寸一致的控件。其他控件位于左侧。

![](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_layout_example03_2x.png)

**有三个主要控件的布局。**Touch Bar的中心包含三个尺寸一致的控件。其他控件位于左侧。

![](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_layout_example02_2x.png)

### 字体

Touch Bar使用的是San Francisco字体的一种变体，即macOS中的系统字体。此字体已针对易读性，清晰度和一致性进行了优化。它也匹配物理键盘的字体。标准Touch Bar控件（如按钮和分段控件）会自动使用此字体。要了解如何在应用程序中编程应用系统字体，请参阅字体格式设置的参考文档。


## 4, 图标


### 图片尺寸和分辨率
为所有出现在Touch Bar上的插图提供比例系数达到 @2x的高分辨率图片。在一个 @2x 的图片中，1pt 等于 2px。例如，一个像素为36X36px的图标对应为18X18pt。在图片文件名后添加字段@2x，再把图片插入到 Xcode 工程的资源目录中。

### 自定义图标
如果您的应用中包含无法由系统图标表示的任务或者状态时，则可以创建自定义图标。

**设计辨识度高的图标**。 图标应该跟主屏幕上的应用明确相关，为了与 Touch Bar 的风格统一，图标可能需要做些修改。

**图标应简单**。 如果包含太多细节会让图标显得臃肿不易读。如果你有一个高保真的图片，提取并保留图片关键元素，抽象成基本图案。好的图标内部细节很少，通过轮廓传达意思。使用边缘柔化来保证图标的轮廓清晰。不要使用阴影，也不要尝试使用高光和阴影让图标立体。

**图标应统一**。 无论是只使用自定义图标，还是混合使用系统图标和自定义图标，都应让所有图标在尺寸、细节层次、透视和描边方面达到视觉上的统一。

**参考系统图标**。 设计自定义图标时，请参阅系统图标。尽量做到视觉统一。

**准备用作模版图片的图标**。 模版图片是一张黑色图片，具有透明背景和一个 alpha 通道。当在Touch Bar上显示模板图像时，系统会转换模板图片，并自动地应用合适的颜色。模板图像还能响应诸如周围光线和键盘背光水平等因素引起的系统参考白点改变事件，并且自动地对用户交互作出反应。

**测试图标**。 要在有上下文的情况下预览图标，这样才能准确地调整合适的外观。此外，要确保模板图片在被系统转换时能够按预期效果显示。

#### 图标尺寸

虽然可以生成延伸至 Touch Bar整个高度的图标，但图标的高度通常不应超过44像素（圆形图标为36像素）。

* 图标标准尺寸： 36px by 36px (18pt by 18pt @2x)
* 图标最大尺寸：44px by 44px (22pt by 22pt @2x)

![](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_custom_icon_example_2x.png)

**保持图标居中**。 裁剪图片以匹配图标宽度，然后根据需要添加填充，以便在控件上显示时使图标保持居中。

**对角图标功能优先使用45度角**。 在系统图标中，对角元素通常以45度角放置，例如，进入全屏和退出全屏图标上的箭头; 在Go Back，Go Down，Go Forward和Go Up图标上的"V"形图案; 音频输入静音图标上的斜线; 撰写图标上的铅笔; 和“在浏览器中打开”图标中的罗盘指针。 请参阅系统提供的图标模板。

#### 颜色和填充

Touch Bar中的图标的显示应与物理键盘按键上的文字的显示一致。如果使用模板图片和系统颜色，图标应自动实现此效果。

**不要使用颜色来传达开和关状态**。 系统会自动更改处于关闭状态的标准控件的背景外观。

**给大多数图标100％不透明填充**。 如果需要更改可读性，请组合使用70％不透明度填充和100％不透明度填充。仅在提高可读性和平衡的时候使用中间色调。

#### 描边

为了匹配物理键盘的样式，请设置大多数图标@2px的描边。 如果你的设计需要更多的视觉重量，尝试@3px的描边。

#### 角

为了匹配系统图标的样式，请在图标上应用@2px描边的方角，或者半径为1px、描边为@3px的圆角。半径为4px的圆角用于实体形状图案。

#### 模板

使用图标模板链接中的Photoshop和Sketch模板来设计比例合适的图标，以在Touch Bar中使用。 

#### 系统图标

系统提供了许多图标，代表常见任务和内容类型，可在应用控件中使用。

**优先使用系统图标，因为他们很常见**。 系统图标是模板图像，它们会自动调整为合适的颜色，对诸如环境光线和键盘背光等因素引起的系统参考白点的变化做出反应，并且它们会自动地对用户交互作出反应。

**不要重新定义系统图标的预期功能**。  请按预期功能使用系统图标来确保体验一致，。例如，不要在“下载”按钮上显示“文件夹移动”图标，而是显示“下载”图标。

**仅使用为Touch Bar设计的系统图标**。 其他类型的图标（如工具栏图标）不适用于Touch Bar。

> 注意
> 某些系统图标（例如后退和前进）在从右到左的语言环境中会自动反转方向。

### 参考链接

- [图标模板](https://developer.apple.com/design/downloads/touch-bar.zip)

表格 46-1 Touch Bar 的系统图标

|图标 |图标名称 |AppKit符号|功能|
|:-:|:----|:----|:----|
|![TB_adddetail_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_adddetail_2x.png)|添加详细信息 |AddDetail|显示项目的更多细节。 
|![TBAddd 2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_add_2x.png)|添加 |Add|创建一个新的项目。 
|![TBAlarm 2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_alarm_2x.png)|报警 |Alarm|设置或显示报警。 
|![TBAudioinputmute 2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_audioinputmute_2x.png)|音频输入静音 |AudioInputMute|静音音频输入或表示音频输入静音。 
|![TBAudioinput 2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_audioinput_2x.png)|音频输入 |AudioInput|表示音频输入。 
|![TB_audiooutputmute_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_audiooutputmute_2x.png)|音频输出静音 |AudioOutputMute|静音音频输出，或表示音频输出静音。 
|![TB_audiooutputvolumehigh_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_audiooutputvolumehigh_2x.png)|音频输出音量高 |AudioOutputVolumeHigh|设置音频输出音量为高或表示该音频输出音量被设定为高。 
|![TB_audiooutputvolumelow_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_audiooutputvolumelow_2x.png)|音频输出音量低 |AudioOutputVolumeLow|设置音频输出音量为低或表示该音频输出音量设置为低。 
|![TB_audiooutputvolumemedium_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_audiooutputvolumemedium_2x.png)|音频输出音量中等 |AudioOutputVolumeMedium|设置音频输出音量为中或表示该音频输出音量被设定为中等。 
|![TB_audiooutputvolumeoff_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_audiooutputvolumeoff_2x.png)|音频输出音量关闭 |AudioOutputVolumeOff|关闭音频输出或者表示音频输出被关闭。 
|![TB_bookmarks_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_bookmarks_2x.png)|书签 |Bookmarks|显示应用程序特定的书签。 
|![TB_colorpickerfill_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_colorpickerfill_2x.png)|颜色选择填充 |ColorPickerFill|显示颜色选择器，用户可以选择一种填充颜色。 
|![TB_colorpickerfont_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_colorpickerfont_2x.png)|字体颜色选择器|ColorPickerFont|显示颜色选择器，使用户可以选择文本颜色。 
|![TB_colorpickerstroke_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_colorpickerstroke_2x.png)|描边颜色选择器 |ColorPickerStroke|显示颜色选择器，使用户可以选择描边颜色。 
|![TB_communicationaudio_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_communicationaudio_2x.png)|通信音频 |CommunicationAudio|启动或表示音频通信。 
|![TB_communicationvideo_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_communicationvideo_2x.png)|视频通信 |CommunicationVideo|发起或表示视频通信。 
|![TB_compose_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_compose_2x.png)|撰写 |Compose|在编辑模式下打开一个新的文档或视图。 
|![TB_delete_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_delete_2x.png)|删除 |Delete|删除当前或所选项目。 
|![TB_download_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_download_2x.png)|下载 |Download|下载项目。 
|![TB_enterfullscreen_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_enterfullscreen_2x.png)|进入全屏模式 |EnterFullScreen|进入全屏模式。 
|![TB_exitfullscreen_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_exitfullscreen_2x.png)|退出全屏 |ExitFullScreen|退出全屏模式。 
|![TB_fastforward_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_fastforward_2x.png)|快进 |FastForward|媒体播放或幻灯片快进。 
|![TB_foldercopyto_2x.pngg](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_foldercopyto_2x.png)|文件夹拷贝到 |FolderCopyTo|复制项目到目标位置。 
|![TB_foldermoveto_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_foldermoveto_2x.png)|文件夹移动到 |FolderMoveTo|移动项目到新的目标位置。 
|![TB_folder_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_folder_2x.png)|文件夹 |Folder|打开或显示一个文件夹。 
|![TB_getinfo_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_getinfo_2x.png)|获取信息 |GetInfo|显示有关项目的其他信息。 
|![TB_goback_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_goback_2x.png)|返回 |GoBack|返回到上一个屏幕或位置。 
|![TB_godown_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_godown_2x.png)|向下 |GoDown|移动到下一个垂直项目。 
|![TB_goforward_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_goforward_2x.png)|前进 |GoForward|移动到下一个屏幕或位置。 
|![TB_goup_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_goup_2x.png)|向上 |GoUp|移动到前一个垂直项目。 
|![TB_history_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_history_2x.png)|历史 |History|显示的历史信息，如最近的下载。 
|![TB_iconview_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_iconview_2x.png)|图标视图 |IconView|显示图标视图中的项目。 
|![TB_listview_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_listview_2x.png)|列表显示 |ListView|在列表中显示视图中的项目。 
|![TB_mail_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_mail_2x.png)|邮件 |Mail|创建一个电子邮件。 
|![TB_newfolder_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_newfolder_2x.png)|新文件夹 |NewFolder|创建一个新的文件夹。 
|![TB_newmessage_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_newmessage_2x.png)|新消息 |NewMessage|创建一个新的消息或者表示使用消息传递。 
|![TB_openinbrowser_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_openinbrowser_2x.png)|在浏览器中打开 |OpenInBrowser|在用户的浏览器中打开一个项目。 
|![TB_pause_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_pause_2x.png)|暂停 |Pause|暂停播放媒体或幻灯片。暂停时，请务必保存当前的位置，以便以后可以恢复播放。 
|![TB_playpause_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_playpause_2x.png)|播放或暂停 |PlayPause|媒体或幻灯片播放和暂停播放之间的切换。 
|![TB_play_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_play_2x.png)|播放 |Play|开始或继续播放媒体或幻灯片。 
|![TB_quicklook_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_quicklook_2x.png)|快速查看 |QuickLook|在快速查看中打开一个项目。 
|![TB_recordstart_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_recordstart_2x.png)|开始录制 |RecordStart|开始录制。 
|![TB_recordstop_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_recordstop_2x.png)|停止录制 |RecordStop|停止录制或停止播放媒体或幻灯片。 
|![TB_refresh_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_refresh_2x.png)|刷新 |Refresh|刷新屏幕上的数据。 
|![TB_rewind_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_rewind_2x.png)|倒退 |Rewind|媒体播放或幻灯片向后倒退。 
|![TB_rotateleft_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_rotateleft_2x.png)|向左旋转 |RotateLeft|向左旋转项目。 
|![TB_rotateright_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_rotateright_2x.png)|向右旋转 |RotateRight|向右旋转项目。 
|![TB_search_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_search_2x.png)|搜索 |Search|显示一个搜索栏或启动搜索。 
|![TB_share_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_share_2x.png)|分享 |Share|与他人或向社交媒体分享内容。 
|![TB_sidebar_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_sidebar_2x.png)|侧边栏 |Sidebar|在当前视图中显示边栏。 
|![TB_skipahead15seconds_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skipahead15seconds_2x.png)|快进15秒 |SkipAhead15Seconds|在媒体播放时快进到15秒后的位置。 
|![TB_skipahead30seconds_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skipahead30seconds_2x.png)|快进30秒 |SkipAhead30Seconds|在媒体播放时快进到30秒后的位置。 
|![TB_skipahead_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skipahead_2x.png)|快进 |SkipAhead|媒体播放时快进到下一章或下一个位置。 
|![TB_skipback15seconds_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skipback15seconds_2x.png)|快退15秒 |SkipBack15Seconds|媒体播放时快退到15秒前的时刻。 
|![TB_skipback30seconds_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skipback30seconds_2x.png)|快退30秒 |SkipBack30Seconds|媒体播放时快退到30秒前的位置。 
|![TB_skipback_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skipback_2x.png)|快退 |SkipBack|媒体播放时快退到上一章节或上一个位置。 
|![TB_skiptoend_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skiptoend_2x.png)|跳转到最结束 |SkipToEnd|媒体播放时跳到结束位置。 
|![TB_skiptostart_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_skiptostart_2x.png)|跳转到启动 |SkipToStart|媒体播放时跳到启动位置。 
|![TB_slideshow_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_slideshow_2x.png)|幻灯片 |Slideshow|开始放映幻灯片。 
|![TB_tagicon_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_tagicon_2x.png)|标签图标 |TagIcon|在一个项目中应用标签。 
|![TB_textbold_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textbold_2x.png)|文本粗体 |TextBold|使文本加粗。 
|![TB_textbox_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textbox_2x.png)|文本框 |TextBox|插入一个文本框。 
|![TB_textcenteralign_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textcenteralign_2x.png)|文字居中对齐 |TextCenterAlign|文本中心对齐。 
|![TB_textitalic_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textitalic_2x.png)|文字斜体 |TextItalic|文字斜体。 
|![TB_textjustifiedalign_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textjustifiedalign_2x.png)|文本两边对齐 |TextJustifiedAlign|调整文本，使文本左侧和右侧都对齐。 
|![TB_textleftalign_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textleftalign_2x.png)|文本左对齐 |TextLeftAlign|左对齐文本。 
|![TB_textlist_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textlist_2x.png)|文本列表 |TextList|插入列表或将文本转换为列表形式。 
|![TB_textrightalign_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textrightalign_2x.png)|文本右对齐 |TextRightAlign|右对齐文本。 
|![TB_textstrikethrough_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textstrikethrough_2x.png)|文字删除线 |TextStrikethrough|在文本中使用删除线。 
|![TB_textunderline_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_textunderline_2x.png)|文字下划线 |TextUnderline|给文本添加下划线。 
|![TB_useradd_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_useradd_2x.png)|添加用户 |UserAdd|创建一个新用户。 
|![TB_usergroup_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_usergroup_2x.png)|用户组 |UserGroup|显示或代表一组用户。 
|![TB_user_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_user_2x.png)|用户 |User|显示或代表用户信息。 
|![TB_volumedown_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_volumedown_2x.png)|降低音量 |VolumeDown|降低音量。 
|![TB_volumeup_2x.png](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/TB_volumeup_2x.png)|提高音量 |VolumeUp|提高音量。 

## Controller 控件


系统提供了一系列标准控件，用于Touch Bar的应用程序区域。尽可能使用这些系统提供的控件以达到视觉上的统一。

### 按钮

点击按钮会触发应用程序特定的操作，按钮可以包括一个图标和标题。

![touchbar_button_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_button_2x.png)

**优先选择图标而不是文字。**设计的图标要尽量清晰，且能够独立表达意思，而不需要附带文字进行说明。

**使用短标题。**过长的标题会使 Touch Bar 显得很拥挤。

**边框颜色的变化要雅观。**系统提供的边框颜色和物理键盘按钮的相似。如果您的应用程序需要自定义边框颜色，建议使用较深的颜色。

### 切换控件

点击切换控件，其状态会在开启和关闭之间转换。

![touchbar_toggle_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_toggle_2x.png)

**背景指示按钮状态。**切换按钮处于关闭状态时，系统会自动改变按钮背景的外观，所以没有必要再使用颜色、文本或不同的图标来指示按钮不同的状态。

**用切换控件替代位复选框和单选按钮。**如果需要用户在两种状态之间进行选择，使用切换按钮。

### 候选列表

在用户编辑文本时，候选列表会提供自动补全的建议。用户可以在列表中选择一个，并将其插入到主屏幕上的活动文本区或者文本视图中。用户可以展开或者收缩候选列表。展开时，候选列表会取代原本位于其展开区域内的其他控件的位置。

![touchbar_candidatelist_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_candidatelist_2x.png)

### 字符选择器

点击时，字符选择器会打开一个包含特殊字符列表的弹出窗，如表情符号列表。用户可以选择列表中的一个进行插入。

![touchbar_characterpicker_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_characterpicker_2x.png)

![图片](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_characterpicker2_2x.png)

### 颜色选择器 

点击时，颜色选择器打开一个弹窗，其中包括用于颜色选择的控件。颜色选择器有三个可选功能：颜色选择器、描边颜色选择器和文本颜色选择器，具体使用哪个由用户决定。对于这三个功能，颜色选取器显示相同的弹窗是相同的。

![touchbar_colorpicker_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_colorpicker_2x.png)

![touchbar_colorpicker2_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_colorpicker2_2x.png)

**根据需要使用图标。**选择描边颜色时使用描边颜色选择器图标，选择文本颜色时使用文本颜色选择器图标。对于其他情境中的颜色选择，使用颜色选择器图标。

### 标签

标签是只读文本，通常用于描述控件的作用。

**一般情况下，避免使用标签。** 虽然Touch Bar可以显示标签，但由于标签没有交互功能，因此一般情况下也要避免用标签。相反，应该注重为控件设计更有意义的图标。在必须使用的情况下，应保证标签尽可能的短。

**为复杂的图标作补充说明时，优先使用标题而不是标签。**如果图标表达的含义不够清晰，应考虑在控件上添加一个简短的标题来进行补充说明。

![touchbar_buttontitles_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_buttontitles_2x.png)

### 弹出视窗

在折叠状态下，弹出控件在Touch Bar显示为一个单独按钮。

![touchbar_popover_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_popover_2x.png)

展开时，弹出视窗以一种模态覆盖的方式，用一些临时控件集取代当前应用区域内的控件集的位置。模态覆盖情况下，用户只能在当前所显示控件上做出选择，或者可以点击退出按钮返回到折叠状态并显示覆盖之前的控件集。

![touchbar_popover2_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_popover2_2x.png)

点击弹出框按钮来展开折叠下的弹出框，也可以用长按手势来展开，支持长按手势的弹出控件尾部会有一个箭头装饰。

![touchbar_popupbutton_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_popupbutton_2x.png)

通过点击和通过长按展开的弹出视窗可以是相同的，也可以是截然不同的。在“点击保持”手势的展开控件中，用户可以用另外一个手指选择一些项目，然后移开手指，展开控件会自动收起。

![touchbar_popupbutton2_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_popupbutton2_2x.png)

**谨慎使用弹出视窗。**单击应能够触发Touch Bar上的大多数控件。

**避免嵌套使用弹出视窗。**一般来说，尽量避免在Touch Bar中进行一级以上的导航。

**给简单的弹出视窗保留长按动作。**长按主要是为了展示简单的选项，如简单的分割好的的控件，用户可以做出选择。

**在弹出控件中标示出选择的项目。**弹出控件展开时显示的是选项列表，它的折叠状态应该所选选项的显示。

**提供一个明显的退出。**确保用户知道如何收起一个弹出视窗，并返回到上一组控件。

### 滑动条

滑动条允许用户左右滑动浏览内容，比如日期列表或者一组照片。滑动条可以是固定式的或者自由式的，也可以高度自定义化，但是需要保留和Touch Bar相称的外观。

#### 固定滑动条

固定式滑动条可以和已经加载或者存在的内容集合（例如打开的Safari标签）非常流畅连续的进行交互。当用户在滑动条上滑动手指时，手指下方的项目就会显示高亮状态。根据滑动条的设置，当用户的手指从一个项目移动到另外一个项目时，或者当用户抬起手指时，从而进行选择。如果内容的容量超出滑动条大小，用户的手指接近控件边缘时，滑动条自动添加滚轮以显示附加项目。在固式滑动条上，用户手指直接移动选项，而不是内容。

![touchbar_scrubber_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_scrubber_2x.png)

#### 自由式滑动条

自由式滑动条是在可以自由滚动的列表中显示内容，例如日历中日期列表，用户通过滑动直接滚动。根据自由式滑动条的配置，选择一个项目，用户可以将item拖动到一个特定的位置（比如滑动条的中间），或者滑动条静止用户点击该项目。

![touchbar_wheel_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_wheel_2x.png)

**用可以预测和逻辑有序的值**。当滚轮列表静止时，自由式滑动条上的许多值将会隐藏，最好的情况是，人们可以预测这些值是什么，例如使用字母表国家的列表，以便他们可以快速的浏览。

**避免显示太大值列表**。在touch bar上，长列表显得会冗长乏味。如果你有很大的值列表，请考虑将其显示在主屏幕上，而不是touch bar上，将键盘和触控板用于导航。

### 分段控件

分段控件是两个或者多个分段的线性集合，每个分段的作用如同按钮——通常会配置成切换按钮。在控件中，所有的分段都是等宽的。和按钮一样，分段也可以包含文本和图标。

![touchbar_segmentedcontrol_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_segmentedcontrol_2x.png)

**通过限制段数以提高可用性**。分段越宽，用户越容易点击。

**优先使用图标而不是标题**。尽可能设计出清晰明了的图标，尽量避免使用辅助性文本。

**保持分段的文本内容尺寸一致**。因为各个部件宽度相等，如果每个部件中内容填充不一的话，会显得不够美观。

**使用短标题**。太长的标题会让touch bar会显得很拥挤。

**边框颜色优先使用深色**。默认边框采取了和实体按键相似的外观设计。如果确实需要自定义的话，推荐使用深色的边框颜色。

### 共享服务选择器

分享服务选择器为用户提供了一种便捷的分享方式，用户可以分享文本、图像和应用程序、社交媒体账号中的其他内容，又或是其他服务。点击分享服务选择器会触发一个包含各种分享按钮的弹出视窗。

![touchbar_sharingservicepicker_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_sharingservicepicker_2x.png)

![touchbar_sharingservicepicker2_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_sharingservicepicker2_2x.png)

**仅在有可以分享的内容时才使能共享服务选择器**

	- 如果用户没有文本、图像或者其他可以共享的内容时，共享服务选择器应该被禁用。

### 滑块

滑块是一个水平轨道，其包含一个被称为拇指的控件。用户可以用手指滑动滑块，使其在最大值和最小值之间移动。例如媒体播放期间，移动滑块以改变屏幕的亮度级别和位置，随着滑块值的改变，最小值和拇指控件之间的轨道用颜色填充。

![touchbar_slider_2x](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/OSXHIGuidelines/Art/touchbar_slider_2x.png)

**根据应用自定义滑块的外观显示。**为滑块的轨道选择与应用程序相协调的颜色。

**在左右两边提供两个图标来表明最大值和最小值的含义。**例如，用于调整图像尺寸的滑块，可以在左侧显示一个小图图标，右侧显示一个大图图标。


