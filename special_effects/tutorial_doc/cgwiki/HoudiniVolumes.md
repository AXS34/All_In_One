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



<a href="Houdini_Lighting_Shading.md">
  <img src="https://github.com/BlenderCN/blenderTutorial/blob/master/mDrivEngine/blenderpng/logoleft.png" align="left">
</a>
<a href="HoudiniVex.md">
  <img src="https://github.com/BlenderCN/blenderTutorial/blob/master/mDrivEngine/blenderpng/logoright.png" align="right">
</a>

