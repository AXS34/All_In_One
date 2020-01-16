# HoudiniVolumes

## Contents

* 1 [Volumes are primitives]()

  * 1.1 [VDB?]()

  * 1.2 [VDB interactive tutorial]()

  * 1.3[ VDB vs Houdini volumes]()

  * 1.4 [Scalar and vector volumes]()

  * 1.5 [SDF]()

  * 1.6 [Viewing volume data]()
  
* 2 [Volumes, SDFs, Trails]()

* 3 [Volume magnetic lines]()

* 4 [Swirly lines, or worms with volumes and curl noise]()

* 5 [Convert geometry to velocity volumes efficiently]()

* 6 [Volume displacement]()

* 7 [Volume deform]()

* 8 [VDB combine]()

* 9 [Cloud light]()

* 10 [Further learning]()

## Volumes are primitives

一开始我有点困惑，所以值得一提。volume是primitive，就像point是primitive或p网格不同，没有r，与多边形imitive sphere是primitive一样。这意味着，例如，与polygon mesh不同，没有深入到其子组件的操作。你无法打开任何geometry spreadsheet以查看各个体素的值。

还有其他工具可以帮助你查看体积中发生了什么（volume slice sop,volume visualisation sop,volume trails sop等），但最终，如果将400×400×400体素volume与primitive sphere进行merge，则如果检查merge，将显示你有两个primitives，一个volume和一个sphere。

houdini支持2种volume类型，其自己的volume格式和VDB。这些被视为诸如polys或nurbs之类的primitives，如果你在节点上按住中键不放，则会看到它说1VDB或1volume或类似内容。

### VDB？

对于volumes，最简单的描述方式是Alembic。这是一种开源的标准格式，因此你可以从houdini中导出VDB，将其加载到maya中，然后在vray中进行渲染。它有多家公司来推动它的发展（Dreamworks，Double Negative，SideFx最引人注目），通常是一件好事。

但是它比Alembic更重要，因为Alembic主要是一种文件格式，并且是一些用于操纵和查看Alembic文件的示例工具。VDB工具箱既是存储volume的一种方式，又是一套用于处理volume算法。

VDB最有趣的特性之一是它不会浪费空像素上的存储空间。与其他格式相比，磁盘上的VDB可以小得多。

VDB存储格式在Houdini中作为primitive（足够有趣的是VDB primitive）公开，而操纵工具则作为sops公开，大多数带有VDB前缀或后缀。

原始论文中提到的VDB名称来自...Volumetric,Dynamic grid that shares several characteristics with B+ trees.

进一步阅读[http://www.openvdb.org/about/](http://www.openvdb.org/about/)

### VDB interactive tutorial

也可以从openvdb网站上获得VDB功能的精彩介绍。这是一个单一的houdini文件，就像VDB上的一本互动书;它分成几章，有很多便笺，我很惊讶我最近才发现它。非常感谢odforce上的人指出这一点！

如果你想和老板一样使用VDB，请立即下载。

[http://www.openvdb.org/download/](http://www.openvdb.org/download/)

[https://nexus.aswf.io/content/repositories/releases/io/aswf/openvdb/houdini_examples.hip/1.0.0/houdini_examples.hip-1.0.0.zip](https://nexus.aswf.io/content/repositories/releases/io/aswf/openvdb/houdini_examples.hip/1.0.0/houdini_examples.hip-1.0.0.zip)

### VDB vs Houdini volumes

houdini自己的volume格式在VDB到来之前已经存在了一段时间，因此那里有大量的节点可以使用它们（你可以通过选项卡菜单中键入volume在sops中看到它）。VDB拥有自己的节点集合（以VDB为前缀或后缀）。

VDB可以比houdini本地volume小得多（通常可节省50%），而且VDB sops通常比houdini本地volume更快，并且具有更有趣的功能。

有一种.vdb文件格式（可用于导出到其他应用程序），但你无需导出.vdb文件即可节省空间，只需在houdini中使用vdb primitive就足够了。可以随时将houdini volume primitive转换为vdb primitive，只需要创建convertvdb节点，并将其模式设置为VDB。如果在节点上单击鼠标中键，将看到Volume primitive已被VDB替换。现在，你可以像其他任何houdini geo一样将其缓存到.bgeo,但比不进行转换的情况要小得多。

令人高兴的是，许多houdini volume工具也已更新，可以与VDB primitive一起使用。例如，Volume VOPS和Volume Wrangles都可以与VDB一起使用。这意味着在任何时候使用VDB都相当安全，因为你可以根据需要轻松地将VDB临时转换为本地volume，然后再次转换。

进一步阅读[http://www.sidefx.com/docs/houdini15.0/model/volumes](http://www.sidefx.com/docs/houdini15.0/model/volumes)

### Scalar and vector volumes

例如，用于表示云的volume仅需要为每个体素密度存储一个值。如果密度为0,则体素是完全透明的;如果密度为1,则体素是不透明的。例如，cloud sop使用引擎盖下的噪音功能将密度雕刻成令人愉悦的云形。

如果要存储在整个云中变化的颜色，则需要存储红色/绿色/蓝色分量。这意味着你需要一个向量volume（或向量字段，你会发现文档和论坛经常将volume称为字段）来存储此信息。

向量场的更常见用途是速度。houdini中的一个约定是将速度体积命名为vel。体积中的每个体素都存储一个方向。pyro solver将使用该速度场在周围传递密度，从而给人以运动感。pyro sims的其他属性是修改速度场本身，因此你可能会得到向上滚动的蘑菇云，或使密度回旋的噪声。

需要注意的是本地houdini向量volume和vdb向量volume之间的区别。在内部，Houdini并没有真正使用矢量volume格式，但是它会创建3个标量volume，并且知道将它们视为单个空对象。使用密度和vel的标准烟雾设置将具有4个volume primitives;density，vel.x,vel.y,vel.z。

VDB确实支持矢量volume，因此相同的烟雾设置将是2个vdb primitives，density和vel。

请注意，这几乎与使用标准几何图形和属性的方式相反

* 1. grid是单个几何体，但是grid上的每个点可能具有很多属性（N，Cd，id，foo，myattr等）

* 2. 一个volume只能包含一个属性，因此你可以创建多个volume，每个volume都存储一个属性（density,vel,fuel等）。

houdini中的一些工具知道这一点，并且知道如何将所有volume连接起来并将它们视为单个空对象。但是其他工具却没有，例如你需要显式地告诉一个pyro solver，以同时修改Cd字段的密度和速度（此示例更近一步。）

大多数情况下，你在处理houdini期望的默认字段，因此在实践中通常不会经常手动进行字段接线。

### SDF

![Volume_compare.jpg](http://www.tokeru.com/cgwiki/images/9/97/Volume_compare.jpg)

volume（或vdb）可以表示诸如雾或火之类的无定型形状，也可以表示固体表面。进行后者时，每个体素都会存储距离表面上最接近点的距离，并且存储在该表面的外部和内部。因为这些值可以是分数（例如，距离表面1.42个单位而不仅仅是一个单位），所以它们可以很好地贴合输入的几何形状。辨别体素是在表面的内部还是外部，他变为正或负。正面在外面，负面在i里面。如果将其可视化，它将看起来在形状轮廓上为0的gradient，以及在形状内部和外部延伸的平滑gradient。该volume称为Signed Distance Field或SDF。

![Viz_sdf.gif](http://www.tokeru.com/cgwiki/images/8/88/Viz_sdf.gif)

这在很多事情上都很方便。如果你有生成SDF的可靠方法（当然，我们在houdini中也是如此），那么这是生成模拟碰撞几何的好方法。粒子或RBD对象可以非常快速地对SDF进行采样，如果其符号为负，则它们位于形状内部。然后，他们可以测量SDF的gradient,并准确确定停止相交的距离和推动量。

如果你使用SDF并将其转换为多边形，则会得到均匀啮合的防水表面。你可以平滑地扩展或膨胀表面，可以合并SDF并获得非常干净的布尔值，它们在很多情况下都很方便。

![Poly_mesh_vdb.jpg](http://www.tokeru.com/cgwiki/images/c/cd/Poly_mesh_vdb.jpg)

houdini提供了两种从poly geo生成SDF的方法。原生houdini方式（在IsoOffset SOP节点中使用使用SDF volume模式）和VDB方式（VDB-from-polygons sop）。我的30秒测试表明，对于更详细的几何图形，VDB的速度要快得多。

houdini使用SDF场为0的每个体素的sprite来可视化SDF体积。看起来它使用SDF gradient来派生sprite的法线，以此对照明做出响应。


### Viewing volume data

![Sdf_visualise.jpg](http://www.tokeru.com/cgwiki/images/7/72/Sdf_visualise.jpg)

由于将volume或vdb视为其自身的primitive类型，因此无法像你最初期望的那样从geometry spreadsheet中检查体素数据。如果尝试，你将看到的是每个volume的原始编号。

相反，你应该在视口中使用可视化工具。volume slice可以让你查看映射到色带的二维体素数据的切片，volume trails节点可让你跟踪体积内的方向线，通过volume道出gradient。

[hip示例文件](http://www.tokeru.com/cgwiki/index.php?title=File:Vis_volume.hip)是使用volume的两种不同方式的示例,它以两种方式计算gradient，一种是通过vex的volumegradient调用，另一种是通过VDB analysis sop。VDB节点的显示方式与常规houdini节点略有不同，但功能非常强大且非常快。

### Volumes,SDF,Trails

![](http://www.tokeru.com/cgwiki/images/7/73/Vol_trails.gif)

看到了有关生成涡旋线的有趣的[vimeo教程](https://vimeo.com/134057856),以及一个[odforce帖子](http://forums.odforce.net/topic/23535-noise-how-to-convert-into-line/),询问如何从噪声生成示踪线，这是我将两者结合在一起的尝试。volume trail sop用于可视化volume中的场，但是如果你以正确的方式设置volume，则可以将其用于自己的目的。

这里的想法是从一个形状中创建一个SDF（Signed Distance Field）。这是每个体素存储到曲面上最近点的距离的体积。曲面上的体素的值为0曲面外的体素的值为正，曲面内的体素的值为负。这使它们非常容易检查碰撞，或做其他事情。（这里是一个不错的shadertoy演示，用于在2d中可视化SDF：https://www.shadertoy.com/view/ltBGzK）

然后，我取另一个体积，每个体素可以在其中存储一个矢量值，称为vel（用于在volume中存储速度的houdini标准）。然后，我使用SDF对vel场进行分片，以便仅保留表面0.1以内的体素，其余部分被裁剪掉。

然后，我通过curl noise运行该volume，因此vel场现在具有穿过它的粗糙块状漩涡。将其放入到volume trails sop产生彩线。

虽然这种排序沿着猪的表面，但线条倾向于在表面的上方和下方有些晃动。为了使他们直接贴在上面，我只需将曲线映射到稍胖的猪的副本上，以避免交叉问题）

可能存在一种直接从SDF生成curl noise的方法，以使最终产生的线条留在表面，ray是一种快速偷懒的方法：）

### Volume magnetic lines

找到了这篇[文章](http://pepefx.blogspot.com.au/2015/08/how-to-create-magnetic-vector-field-in.html),其中介绍了如何设置磁力线。试着在很累的时候跟着操作，然后一个月后再试一次，不看他的笔记我是否能得到类似的效果，并且不用他巧妙的vex。

在此设置中，可能有两件事很有趣：

* 从多边形创建vdb，特别是从一个多边形同时创建密度和vel的vdb。vdb-from-polygons sop具有一个multilister，类似于在rop创建aovs/imge planes,单击创建一个新的Surface Attributes，告诉它源属性是什么（例如，v表示速度）以及它将映射到哪种vdb（矢量）

* combine vdb。我设置一个正负vdb，然后使用add模式combine他们，在我看来我可以先merge多边形的形状，然后一步一步制作vdb，这样会更有效率。还有，方便学习练习不是吗？

我敢肯定最终结果和科学上是不符合的，但看起来很漂亮。老实说，我确定该设置将无法正常工作。仅创建一个负的和正的速度场似乎太简单了，以至于wolume trails无法完成它的工作，但这就是在起作用。线条和颜色通常是我对volume trails和point wrangles的滥用。

### Swirly lines,or worms with volumes and curl noise

![](http://www.tokeru.com/cgwiki/images/0/0e/Pig_worms.gif)

这是另一种尝试处理涡旋线的方法，但我认为效果更好。我看过Raph在vimeo上做的非常酷的实验，并对如何实现感到好奇。即使他离我只有两米远，我自然也很骄傲地问他。我一直研究了很久。

我在pyro上进行的实验让我熟悉了volume并创建了速度场，而volume trail sop始终提供了不错的结果。当我注意到sdf的输入时，正在volume vop中使用curl noise。开始使用他时，令我惊讶的是她确实达到了我所追求的效果，并且帮助文档证实了这一点;就像将石头放到溪流中一样，它会迫使curl noise在你给它的sdf周围流动

因此，工作流程如下：

1. 获取一个形状。（在这种情况下，值得信赖的猪）

2. 获取bound，并从中创建一个vel fog vdb

3. 同时创建一个猪的SDF。

4. 在volume vop中，使用curl noise，将SDF连接到curl noise的输入上以绕动它，调整滑块以得到满意的效果。

5.测试vdb是否为负值（即在猪的形状的内部），是则设为1,外部的值设置为0

6.将此值乘以curl noise以将所有猪外部的vel设置为0，现在，curl noise沿着表面和猪的volume内部流动。

7.在猪的表面scatter点，用volume trail sop生成酷的线。

8. 使用@width直接渲染曲线或使用polywire sop获得poly tubes。

具有@width的曲线当然更有效，但是他们和opengl都有一个bug，导致奇怪的尖峰和爆裂，因此我需要用polywire来进行我的flipbook（拍屏）的获取。可以从添加time到curl noise的输入来使线条动起来。

这同样适用于squab，通过SDF的值驱动颜色的增加，进而驱动ramp，表面附近的值是白色，然后是红色，然后是粉色，然后是蓝色。令人毛骨悚然。

![](http://www.tokeru.com/cgwiki/images/e/e4/Squab_viscera_small.jpg)

### Convert geometry to velocity volumes efficiently

vdb from polygons节点非常适合将meshes转换为sdf或fog，并且如果mesh上具有@v，也可以创建速度场。

例如，我有一头猪，给它point noamal，用@v=cross（@N，{0,1,0}）创建一个涡旋状的@v。然后使用vdb from polygons创建一个密度场，并另外通过@v创建一个@vel场。（我还附加了一个scatter和volume trail来可视化涡旋场。）

![](http://www.tokeru.com/cgwiki/images/3/35/Vel_field_watertight.gif)

但是，如果你没有质密的mesh，则会导致vdb convert sop的结果非常混乱，因此要么需要非常密集的采样设置，要么用挤出或其他来创建多面体几何体，这可能是不可能的。

在这里，我将猪换成具有法线和速度的曲线。vdb from polys节点无法创建密度场，因此整个过程都将中断：

![](http://www.tokeru.com/cgwiki/images/6/67/Vel_field_curve_bad.gif)

这里我们真正需要的是能够生成通用的密度fog，然后将@v从曲线到体积的@vel进行属性传递。有几个节点可以执行此操作（volume from attribute，velocity volume，volume from curve等），但是不幸的是，所有的这些似乎都不适用于vdb，只有常规的houdini体积。这意味着你要转换，执行此操作并转换回去，这很浪费。（现在可以了）

更好的方式是使用volume wrangle：

![](http://www.tokeru.com/cgwiki/images/e/e7/Vel_field_curve_good.gif)

这是gif中发生的事情：

* 1. 首先我们需要一个通用的密度框，因此我给猪使用了了bounds sop，然后将其边界框接入到vdb from polys。

* 2. vdb节点有一点混乱，因为它是通过@v创建vel的，而该框是没有的。快速重置为零，然后重置P，清除其头部。所以现在@vel实际上正在存储@P，但这只是一个占位符。

* 3. vdb节点之后添加一个volume wrangel，它所做的是两件事情：

 * 1. int pt = nearpoint(1,@P);对于每个体素，曲线上最接近的点，获取其@ptnum，将其存储为pt。
 
 * 2. v@vel=point(1,'v',pt);---在曲线上查找点pt，读取其@v，将其存储为当前的体素@vel。
 
* 4. 将其连接到volume trail中即可查看结果。

* 5. 注意，我使用v@vel而不是@vel;我意外的发现@vel不是自动识别为哪种类型的特殊wrangle属性之一。如果你忘记了，它会被当成float，这一度使我感到困惑。

这是[场景文件](http://www.tokeru.com/cgwiki/index.php?title=File:Volume_vel_from_geo_curve.hipnc)

感谢Luke Gravett向我展示这个小技巧。

### volume displacement

![](http://www.tokeru.com/cgwiki/images/6/64/Volume_displacement.gif)

再一次，把它建立起来确实很复杂，但实际上却很容易。一般来说，当使用较小的体素时，volumes看起来更好，但这当然是以磁盘空间，内存和渲染时间为代价的。我知道houdini和mantra支持volume置换，但是我认为无论看什么教程，它都看起来非常复杂。

Patreon的支持者Isaac Katz的提示促使我去做一些研究，这使我在odforce论坛上举了[Marc Picco的简单例子](http://forums.odforce.net/topic/12050-displace-volumes/?do=findComment&comment=77623)。你要做的就是像对常规多边形置换那样设置着色器，因此取P，添加一些噪声，填入一些置换输出。

上面的gif显示了这种方法的有效性。volume 猪的起始分辨率非常第（25×27×29,刚好可以捕捉基本的猪形）。通过在渲染时进行置换，可以得到像素级的细节，并且可以放大得足够近，甚至可以是子像素），并且渲染时间仍然非常合理。

这显示了我从materal palette添加到basic smoke着色器的额外节点。唯一令人困惑的地方是如何创建置换节点。它只是一个输出节点，其类型更改为displacement。

![](http://www.tokeru.com/cgwiki/images/d/d9/Volume_displacement_shop_network.jpg)

### Volume deform

![](http://www.tokeru.com/cgwiki/images/a/a8/Volume_deform.gif)

变形volumes似乎很简单，然后你尝试一下却意识到并非如此。令人沮丧的是，像Pixar的REVES这样酷的内部工具似乎可以毫不费力地处理它，但是我们其余人会做什么？

嗯，有正确的方法可以做到这一点（例如Orbolt上的[volume Lattice资产](https://www.orbolt.com/asset/StianHalvorsen::Volume_Lattice)，或[Juraj Tomori的示例](https://vimeo.com/139814928)），两者都可以直接将其转换为体积，并且这种方法有些怪异。在我丢失的一个vimeo帖子上阅读了有关如何执行此操作的提示以及同事的提示后，这还不错，并且在相当高的分辨率下速度相当快。

这有两个部分，一个是使用vdb可视化工具将volume转换为points，另一个是使用volume from attribute转换回volume。

vdb可视化工具可以生成从体积到点的精确转换。如果将其模式更改为仅对active体素进行操作，并导出带有值的点，那么你将获得此结果。每个体素的点，其密度存储为@vdb_float。

[From](http://www.tokeru.com/cgwiki/index.php?title=HoudiniVolumes)


<a href="Houdini_Lighting_Shading.md">
  <img src="https://github.com/BlenderCN/blenderTutorial/blob/master/mDrivEngine/blenderpng/logoleft.png" align="left">
</a>
<a href="HoudiniVex.md">
  <img src="https://github.com/BlenderCN/blenderTutorial/blob/master/mDrivEngine/blenderpng/logoright.png" align="right">
</a>

