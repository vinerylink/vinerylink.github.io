---
layout: post
title: "Android性能优化案例研究"
comments: true
permalink: android-performance
---

*译者前言：这是Google的Android开发工程师Romain Guy刊登在个人Blog上的一篇文章。Romain Guy 作为Android图形渲染和系统优化的专家，是Android 4.1中的“黄油项目”开发者之一。这篇译文将分为上下两个部分，上部分将通过一个实际的例子来展示如何利用现有的工具来定位Android应用程序的性能瓶颈，下部分将提供一些有效的方法来解决性能问题。希望能给读者和开发者带来启发和借鉴。*


# Falcon Pro

最近我在我的Nexus4上安装了[Falcon Pro](https://play.google.com/store/apps/details?id=com.jv.falcon.pro)(下 图)，一个新款的推特（Twitter）客户端。我觉得这款应用真的很赞，但我也注意到一些使用时的瑕疵：似乎在划屏滚动主界面的时间轴时，帧率并不能很 稳定。于是我利用我每天工作中所使用的工具和方法对此稍加研究，很快发现了Falcon Pro不能达到其应有性能的一些原因。


![Falcon Pro](/assets/ap_case_falcon_pro.png)

我这篇文章的主旨在于告诉你如何在一个应用中追踪和定位性能问题，甚至在没有它的源代码的情况下。你所要做的只是要获得最新的[Android4.2SDK](http://developer.android.com/sdk/index.html)（最新的ADT工具可以帮你轻而易举的完成此事）。我强烈推荐你“能够”去下载这款有待研究的应用。不幸的是，Falcon Pro是一款付费应用，我因此只能提供一些文件的链接以便你能对照我的分析。


# 说说关于性能优化

Android4.1通过“黄油项目”将焦点放在性能优化上，并且它也引入了一些性能分析的工具，比如systrace。Android4.2并没有提供像systrace那样显著的工具，但也为你的工具集增加了一些很有用的功能。你将会在接下来的篇幅中发现到它们。(黄油计划 Project Butter，是Google在 Android 4.1 Jellybean版本开始启动的Android性能提升计划，其寓意为“像黄油一样顺滑”，该项目主要针对Android长期以来的饱受诟病的运行流畅度问题，通过底层的优化，确保系统设备能达到60fps的帧刷新率，从而大大提高用户体验的流畅性——译者注)

性能分析通常是一项复杂的任务，它需要大量的经验，需要对工具，硬件，API等方面的深入理解。这些经验让我在这只要几分钟就可以做出分析（你可以在我12月1日的推特（Twitter）上看到它的实况转播。）而你可能得试上几次后才能对此得心应手。

# 证实我的疑问

记 忆中关于性能优化最重要的一件事就是通过量化来验证你的工作。即使对我而言，Falcon Pro在的Nexus4上有着很明显的丢帧现象，我仍然得用实际的数据来证明。因此我将这款应用安装到Nexus7上，因为Nexus7比Nexus4性 能更强大，同时Nexus7在性能分析上也有着比Neux4更有意思的优势，关于这一点，我将在稍后加以讨论。 

这款应用安装到Nexus7上也没有出现多大差别，我仍然能看到丢帧的现象甚至还略差。为了量化这个问题，我决定使用“Profile GPU rendering”（GPU渲染分析），一款Android4.1所引入的工具。你可以在“设置”应用的“开发者选项”中找到这个工具。

*如果开发者选项在你的Android4.2设备上不可见，你可以在“关于手机”或者“关于桌面选择”的界面底部，点击“版本号”七次。*

![Developing Mode](/assets/ap_case_developing_mode.png)

我这篇文章的主旨在于告诉你如何在一个应用中追踪和定位性能问题，甚至在没有它的源代码的情况下。你所要做的只是要获得最新的[Android4.2SDK](http://developer.android.com/sdk/index.html)（最新的ADT工具可以帮你轻而易举的完成此事）。我强烈推荐你“能够”去下载这款有待研究的应用。不幸的是，Falcon Pro是一款付费应用，我因此只能提供一些文件的链接以便你能对照我的分析。

![Developing Option](/assets/ap_case_developing_options.png)

当这个选项打开，系统将会记录画每个窗口绘画最后128帧所需要的时间。在使用这个工具前，你得先杀掉这个应用（Android未来的版本将会去掉这个要求）。

# 方法：

除非特别需要，在为这个分析做每一次测量时，需缓慢的滚动主界面的时间轴，让其滚动一段像素，使其能展现额外的条目。

在重新启动这个应用并滚动时间轴主界面时，我在终端上运行了下面这个命令：

```
$ adb shell dumpsys gfxinfo com.jv.falcon.pro
```

在 产生的日志中，你会发现一段标记为“Profile”的毫秒量级的数据。这段数据包含了一个有三列数据的表，应用的每个window（窗口）都有一个这样 的表。为了使用这个数据，你可以简单的将这个表拷到你最喜欢的电子制表软件中，从而生成一个数据堆叠的列图。以下这个图就是我的测量结果。

![Profile](/assets/ap_case_sysdump_profile.jpg)

每一列给出了每一帧花在渲染上的时间估计：

1. “Draw”是指Java层用在创建“display lists”（显示列表）上的时间。它表明运行例如View.onDraw(Canvas)需要多少时间。

2. “Process”是指Android 2D渲染引擎用在执行“display lists”上的时间。你的UI层级（hierarchy）中的View数量越多，需要执行的绘画命令就越多

3. “Execute”是指将一帧图像交给合成器（compositor）的时间。这部分占用的时间通常比较少

# 提醒：

要以60fps的帧率进行平滑的渲染，每一帧所占用的时间需要少于16ms。

# 关于“Execute”:

如 果Excute花费很多时间，这就意味着你跑在了系统绘图流水线的前面。Android在运行状态时最多可以用3块缓存，如果此时你的应用还需要一块缓 存，那应用就会被阻塞直到三块中的一块缓存被释放。这种情况的发生一般有两个原因。第一个原因是你的应用在Dalvik(java虚拟机)端画的太快，而 在它的Display list在GPU端执行太慢。第二个原因是你的应用花费太多时间在前几帧的渲染上，一旦流水线满了，它就跟不上，直到动画的完成。这些是我们想在下一个版 本的Android改进的地方。

以上这个图明显的证实了我的疑虑：这个应用在大部分时间运行良好，但某些时候会发生丢帧。

# 进一步研究

我们收集的数据显示这个应用有时绘图时间过长，但盖棺定论还为时过早。帧率也会被未调度的帧或者错过调度的帧的影响。例如，如果应用总是在16ms内完成一次绘图，但有时在帧与帧之间需要完成很长的任务，它就会因此错过一帧。

Systrace是一个很简单的工具去检查Falcon Pro是否存在这个问题。这个工具是系统级的，额外开销很低。它的时间统计是合理准确的，能给你一个整个系统运行的概况，包括你的应用。

开启Systrace，可以到开发者选项中选择“启动跟踪”，弹出一个对话框，会让你选择你想测量哪些方面的性能。我们只关注“Graphics”和“View”。

# 注意：

不要忘记关掉之前的GPU渲染分析选项。

![Systrace](/assets/ap_case_systrace.png)

使用systrace时，可以打开终端，在Android SDK的tools/systrace目录下，运行systrace.py:

```
$./systrace.py
```

这个工具默认会记录5秒钟内发生的事件。我简单的向上和向下滚动时间轴，得到了一个用HTML文档展现的[结果图](https://docs.google.com/file/d/0B3dxhm5xm1siQXZfV2xCMG1ETDg/edit)。

# 技巧：

浏览systrace的文档图，可以使用键盘上的WASD键去移动和缩放。W键是将鼠标所处位置进行放大。

systrace 的文档图显示了很多有意思的信息。例如，它可以显示一个进程是否被调度，是在哪个CPU上调度。如果你放大最后一行（叫做 10440：m.jv.falcon.pro）,你可以看到这个应用正在做什么。如果你点击一个“performTraversals”块，你可以看到这 个应用花在输出一帧图像上面多长时间。

大多数的performTraversals显示在16ms临界值以下，但有一些需要更多的时间，因此也证实了之前的猜测。（在935毫秒处放大可以看到这个块。）

更 有意思的是，你可以看到这个应用有时错过一帧是因为它没有管理调度一个draw的操作。在270ms处放大，找到占用25ms的 “deliverInputEvent”块。这个块表明这个应用用了25ms来处理一个触摸事件。考虑到这个应用是使用ListView，很有可能是这个 适配器（adapter）出了问题，等会我们再来探讨这个。Systrace很有用的地方不仅在于证实这个应用花在绘图的时间上太长，也在于帮我们找到另 一个潜在的性能瓶颈。它很有用但也有局限。它只能提供高层级的数据，我们必须转向其他工具来理解此时究竟在运行什么。

# 可视化重绘

绘 图性能问题有很多根本的原因，但共同的一点是重绘（overdraw）。重绘发生在每次应用让系统在某个画好的地方上面再画别的。想一个最简单的应用：一 个白色背景的窗口（window），上面是一个按钮。当系统要画这个按钮时，它要画在已经画好的白色背景的上面。这就是重绘。重绘是必然的，但太多的重绘 就是个问题。设备的数据传输带宽是有限的，当重绘使得你的应用需要更多的带宽时，性能就会下降。不同的设备能够承担的重绘的代价是不同的。

最佳的准则是重绘的最大次数不能超过两次。这就意味着你可以在屏幕画第一次，然后在这个屏幕上再画第二次，最后在其中某些像素上再画第三次。

重绘的存在通常表明有这些问题：太多的View，复杂的层级，更长的inflation时间等等。

Android提供了三个工具来帮助辨别和解决重绘问题：Hierachy Viewer，Tracer for OpenGL和Show GPU overdraw。前两个可以在ADT工具或者独立的monitor工具中找到，最后一个是在开发者选项的一部分。

Show GPU Overdraw会在屏幕上画不同的颜色来辨别重绘发生在哪儿，重绘了几次。现在就开启它并且别忘了先杀掉你的应用（将来版本的Android会去掉这个要求）。

![Redraw Visilization](/assets/ap_case_overdraw_visilization.png)

在我们查看Falcon Pro之前，让我们先看看当打开Show GPU overdraw，“设置”应用是什么样子。

![GPU Overdraw Setting](/assets/ap_case_overdraw_settings.png)

如果你记得每种颜色所表示的含义，你就能很容易的知道结果是什么：

1. 没有颜色就表示没有重绘。每个像素只画了一次。在这个例子里，你可以看到背景是完全无色的。

2. 蓝色：表示重绘了一次。每个像素只画了两次。大块的蓝色是可以接受的。（如果整个window是蓝色的，你就可以使用一个图层（layer）。）

3. 绿色：表示重绘了两次。每个像素画了三次。中等尺寸的绿色方块是可以接受的，但你最好尝试做出优化。

4. 红色：表示重绘了三次。这个像素被画了四次。很小尺寸的红色方块是可以接受的。

5. 黑色：表示重绘了四次及以上。这个像素被画了五次及以上。这个是错的，需要解决。

基于这些信息，你可以看到“设置”应用表现地很好，不需要额外的改进。只有在切换时有一点点红块，但不需要我们再做什么工作了。

# 透明像素：

再 仔细看看之前的截图。每一个图标都画成了蓝色。你可以看出位图（bitmap）中透明像素是解决了重绘的问题。透明像素必须由GPU处理，开销是昂贵的。 Android为了避免在图层（layer）和9-patches上绘画透明像素，做了优化，所以你只要考虑位图就行了。

# 重绘和GPU：

有 两种移动GPU架构。第一个使用延迟渲染，比如ImaginationTech的SGX系列。这种架构允许GPU在某些特定的场景下检查和处理重绘。（如 果你混合透明和不透明的像素，它有可能不起作用。） 第二钟架构使用及时渲染，它被NVIDIA的TegraGPU采用。这种架构不能为你优化重绘，这就是为什么我喜欢在Nexus7上测试（Nexus7使 用Tegra3）。这两种架构各有优劣。但这已经超出了本文的主题。仅仅只要知道两者都可以工作的很好就行了。现在就让我们看一下Falcon Pro…

![Falcon Overdraw](/assets/ap_case_overdraw_falcon_pro.png)

截图上有大量的红色！最感兴趣的却是列表的背景是绿色的。这就显示在应用程序开始描绘它的内容前已经发生了两次重绘。我们这里所看到问题很有可能是和使用了许多全屏图片背景相关。但要解决这个问题通常是很繁琐的。

英文原文：[Android Performance Case Study](http://www.curious-creature.org/docs/android-performance-case-study-1.html)  编译：ImportNew - 孙立
译文地址:  http://www.importnew.com/3784.html

*译者言：在Android性能优化案例研究前半部分，作者Romain Guy将Falcon Pro这款应用作为例子，通过Android现有的工具追踪和分析了其隐藏的性能问题（重绘）。后半部分作者将会带来如何解决此类问题的方法和思路。*

# 去掉冗余的图层

为 了去掉重绘我们必须首先理解它从哪里产生的。这就轮到Hierarchy Viewer和Tracer for OpenGL大显身手的时候了。Hierarchy Viewer是ADT工具（或者monitor）的一部分，可以被用作对视图层级进行快速解读。在处理布局问题时特别有用，对于性能问题也很适用。

# 重要：

Hierarchy Viewer默认只能在非加密设备使用，例如工程机，工程平板或者模拟器。为了能够在任何手机上使用Hierarchy Viewer，你得在你的应用中添加[ViewServer](https://github.com/romainguy/ViewServer)，这是一个开源库。

在 ADT（或者monitor）中打开Hierarchy Viewer的全景图，选择window标签。这个界面就会粗体高亮的显示当前设备运行的窗口，通常就是你想要研究的那个应用。选中它再点击工具栏的 Load按钮（它更像蓝色方块组成的树）。加载这棵树需要一段时间，所以请耐心等待。当这棵树加载完成你就可以看到如下图所示的画面。

![Hierachy Viewer](/assets/ap_case_hierachy_viewer.png)

现 在视图的层级已经加载到工具里，我们也可以将其转换为PhotoShop文档。只要点击工具栏的第二个按钮，工具提示说：“Capture the window layers [..]”。Adobe Photoshop本身不是必须的，因为生成的文档可以被其他工具兼容，例如Pixelmator, The GIMP等等。你们可以下载我所生成的[PSD文件](https://docs.google.com/file/d/0B3dxhm5xm1siSElRMXpoSkpFUFE/edit)。

PhotoShop文档可以显示每个视图的每个图层。一个图层可以标记为可见或者不可见，这是取决于[View.getVisibility()](http://developer.android.com/reference/android/view/View.html#getVisibility%28%29)返回的结果。每一个图层命名在一个视图的后面，如果视图的android:id存在则使用android:id，或者使用它的类名。我曾经开始添加对于组（group）的支持用于视图树的重建…我其实应该早点把这个功能做完。

![Hierachy Export](/assets/ap_case_hierachy_psd.jpg)

通过检查每个图层的列表，我们可以快速的辨别至少一种重绘的源头：多个全屏的背景。第一个就是第一个图层，叫做DecorView。这个view是由Android框架生成的，包含了皮肤主题指定的背景。这个默认的背景在应用中是不可见的，因此它可以被安全的去掉。

从DecorView向上滚动，你可以看到一个LinearLayout，它包含另一个全屏的背景。它和DecorView的背景是一回事，所以它也是不需要的。唯一可见且肯定存在的背景属于一个名叫id/tweet_list_container的view

# 去掉桌面背景：

定 义在你的主题皮肤里的背景通常是当系统启动你的应用时用来创建预览窗口的。千万不要设置它为空（null），除非你的应用是透明的。相反，设置它为你想要 的颜色或者图片，或者在onCreate()里调用getWindow().setBackgroundDrawable(null)来去掉它

# 进一步去掉重绘

用 Photoshop的文档图来理解应用是怎么创建的是很有用的。但是用来去掉小范围的重绘有点难度。现在我们就必须转向Tracer for OpenGL。同样在ADT（或者monitor）中打开它的视图，点击工具栏的箭头图标，输入你应用的包名和你主要的Activity的名字，然后选择 一个目的文件，点击Trace。

# 一句建议：

OpenGL traces抓取的数据量很大。为了让数据量较小，同时也利于更快速抓取。请去掉“all the Data Collection Options”选项。

![OpenGL Trace Option](/assets/ap_case_opengl_trace_options.jpg)

# Activity名字：

在应用启动时可以通过logcat获得包名和Activity名字。这就是为什么我可以知道在Tracer for OpenGL输入这些名字。

当启动并运行这个应用时，打开前两个选项：

. Collect Framebuffer contents on eglSwapBuffers()

. Collect Framebuffer contents on glDraw*()

第一个选项可以方便的快速找到你感兴趣的帧，第二个选项可以让我们看到每一帧是如何通过一步步绘图命令建立起来的。第二个选项就是解决重绘的关键。

![OpenGL Trace Framebuffer](/assets/ap_case_opengl_trace_framebuffer.jpg)

随着这两个选项的开启，我开始滚动屏幕。抓取每一帧需要很长时间（也许要30秒），所以我推荐你可以先简单的下载我抓取的trace[文件](https://docs.google.com/file/d/0B3dxhm5xm1siOEdTd05vRkg4RXM/edit?pli=1)。你可以通过Tracer for OpenGL工具栏的第一个按钮打开这个文件。

trace 文件一旦加载完成，你就可以看到每一帧发生给GPU的每一个GL命令。如果你下载了我的文件，你跳到第21帧。当一帧被选中后，你就可以看到Frame Summary选项卡中呈现的模样。此外，你还可以点击高亮为蓝色的drawing命令，这样你就可以在Details选项卡中获得当前帧的状态细节。

![GPU Trace File](/assets/ap_case_gpu_trace_file.jpg)

相继的点击前三个绘图命令，你就可以看到在PhotoShop里面已经得到鉴定的问题：全屏的背景被画了三次。

通过深入研究这个trace文件，我们可以找到更多优化的地方。当去画一个消息内容条目时，ImageView被用来画头像。这个ImageView先画了一个背景然后再画头像：

![Trace Draw Static](/assets/ap_case_trace_draw_static.jpg)

如果你看得再仔细点你就会注意到背景只是用来作为图片的边框。这就意味着在位于头像的黑色方块产生了重绘。那块9格图（9-patch）完全被头像覆盖了。

解决这个问题的有一个很简单的方法就是让这块9格图设为透明。Android的2D渲染引擎已经在9格图上做了优化。这个简单的方法就可以去掉重绘。

有趣的是，同样的问题也发生在内嵌的媒体内容上。头像很小所以它们的重绘不是个大问题。但内嵌的媒体内容却可以占据屏幕的大片区域，这个问题就严重了。可以用同样的方法去解决它。

![Trace Draw Static](/assets/ap_case_trace_draw_static_more.jpg)

![Trace Draw Static2](/assets/ap_case_trace_draw_static_more2.jpg)

# 未来的优化：

我希望Android的2D渲染流水线能够自动的检测和修正重绘。我们已经有了一些想法但还不能做出承诺。

# 扁平化View的层级

现在重绘已经基本考虑过了。让我们重新回到Hierarchy Viewer吧。通过研究这棵UI树，我们可以尽量去鉴别哪些View不是必须的。去掉View,特别是去掉ViewGroups，不仅可以提供帧率，也可以节省内存，加快启动时间等等。

看一眼Falcon Pro的View的层级树就可以发现一些ViewGroups是在同一个子节点上。ViewGroups通常不是必须的，也很容易去掉。下面这个截图显示至少有两个节点是可以去掉的。

![Hierachy View Redudant](/assets/ap_case_redudant_view.jpg)

也 有一些冗余的View可以去掉。比如每一个消息条目都包一个叫做id/listElementBottom的RelativeLayout。这个布局包含 了作者的名字，推特消息，已经发布了多长时间和一个图标。名字和消息用了两个不同的TextView，其实只需要一个TextView用不同的风格来显示 就行了。时间和图标用了一个TextView和一个ImageView，其实两者可以用一个TextView，然后用[可视化绑定到TextView上](http://developer.android.com/reference/android/widget/TextView.html)。

左边滑动的界面用了若干不同的LinearLayout+TextView+ImageView来显示标签和图标。他们都可以通过一个TextView来代替。

# 如何扁平化你的界面：

我在2009年的Google I/O大会上做了一篇题为[优化你的UI](http://www.google.com/events/io/2009/sessions/TurboChargeUiAndroidFast.html)的演讲，里面介绍了这其中的技术细节。

# 关于输入事件？

还记得我们在看systrace时找到一段处理很慢的触摸事件？现在可以看看这个问题。理解这个问题最佳的工具就是traceview。

traceview 是Dalvik性能解析工具，它可以测量一个应用在每个方法调用上花费的事件。在ADT或者monitor里打开DDMS，在设备选项卡里选择你应用所在 的进程，然后点击“start method profiling”按钮（三个箭头和一个红色的圈），你就可以使用traceview。

当启动了traceview后，我滚动应用的界面，然后点击那个按钮结束跟踪。你也可以下载我的[trace文件](https://docs.google.com/file/d/0B3dxhm5xm1sidl9kNVJseTR0Nms/edit)。结果如下图所示。

![Trace View](/assets/ap_case_trace_view.jpg)

点击条目21：ViewRootImpl.draw()，高亮它所花的时间。表的最后一列表明这个方法的和在它的子类里平均的调用时间。如果你仔细看看高亮的时间轴，你可以注意到帧与帧之间的差距。

用 一个简单的方法来查看差距里面到底发生了什么，可以放大他们开始的阶段，然后点击你找到的红色的块。你可以跟着调用链来找到你能认出的方法。在我的例子 里，我跟踪了一个大概占用了0.5毫秒的Pattern.compileImpl方法，一直到跟DBListAdapter.bindView。

很 显然这个应用将同一个正则表达式编译了好几次，每一次滚动屏幕都伴随着一个条目的绑定。TraceView显示bindView平均占用了38毫秒，而其 中56%的时间花在了解析HTML文本上。似乎可以将这个步骤放在后台运行而不去阻塞UI线程，而正则表达式不应该每次都需要重新编译。

# 现在轮到你了！

我保留了最后一个跟踪文件作为测验。这个应用有两个滑动的菜单，可以左右滑动时间轴。Show GPU overdraws高亮了滑动时大量的绘图。我用Tracer for OpenGL抓取了滑动时的若干帧。下载我的[trace文件](https://docs.google.com/file/d/0B3dxhm5xm1siYTNveG9aUlNuZ1E/edit)，然后看看你是否能找到重绘的原因（去看第34号帧）。

# 提示：

应 用应该调用View.setLayerType()来使用硬件图层（hardware layer）来简化绘图。大量的背景可以使用9格图来做优化。裁剪也很有效。最后，也许可以将一个颜色过滤器（colofilter）设置在画笔 （paint）上，然后传给setLayerType()，这样可以帮助去掉最后一个绘图命令。

我向你们展示了大量可以优化你们应用的工具。我其实还可以花费大量的时间来描述用这些工具处理这些问题的技术方法，但这样文章就会变成长篇大论。你们可以去参考[Android开发者的的官方文档](http://developer.android.com/index.html)和所有Google I/O上Android的演讲（ppt和视频都是免费可取得）。

英文原文：[Android Performance Case Study](http://www.curious-creature.org/docs/android-performance-case-study-1.html)  编译：ImportNew - 孙立

译文地址:  http://www.importnew.com/4065.html

{% include comments.html %}
