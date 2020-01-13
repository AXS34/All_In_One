# Houdini Lighting shading

可能还有其他快速的mantra灯光材质渲染概述，但是我找不到，至少在我需要的5分钟或更短的类别中找不到。这是我通常对任何新渲染器执行的kick-the-tyres过程，认为其他好奇的灯光师和材质师会发现它很有趣。


* 1. [Build a simple test scene](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#build-a-simple-test-scene)

* 2. [Setup a render node, render](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#setup-a-render-node-render)

* 3. [Material creation](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#material-creation)

* 4. [Things to note](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#things-to-note)

* 5. [Material attribute overrides](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#material-attribute-overrides)

     * 1. [Override at object level](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#override-at-object-level)
        
     * 2. [Override at point level](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#override-at-point-level)
        
* 6. [Assign materials to faces](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#assign-materials-to-faces)

* 7. [Point Colour (Cd) and Alpha](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#aovs-or-extra-image-planes)

* 8. [AOVs or Extra Image Planes](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#aovs-or-extra-image-planes)

    * 1. [Defining AOVs in a shader](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#defining-aovs-in-a-shader)

    * 2. [Add AOVs to Rop](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#add-aovs-to-rop)

* 9. [Houdini rendering setup from a maya perspective](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#houdini-rendering-setup-from-a-maya-perspective)

* 10. [Renderstate vop](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#renderstate-vop)

* 11. [Material wrangle via snippet](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#material-wrangle-via-snippet)

* 12. [Todo](https://github.com/FofightFong/All_In_One/blob/master/special_effects/tutorial_doc/cgwiki/Houdini_Lighting_Shading.md#todo)

## Build a simple test scene

摘要：茶壶放在地板上，有阳光，天空，相机

这些快速入门都假定你使用“Build”界面（Windows>Desktop>Build),并且熟悉选项卡菜单和基本的houdini导航。

![Lighting_setup_geo_sm.gif](http://www.tokeru.com/cgwiki/images/1/17/Lighting_setup_geo_sm.gif)

1.  创建grid

2.  创建一个platonic solid，放置在入场景中，将其模式设置为茶壶，半径为3。

3.  将其向上平移约1.39，以便使其位于grid上

4.  从工具架上创建distant light，旋转和平移使其看起来不错，将其着色为黄色。（提示：单击微型色轮，然后依次点击RGB，HSV和TMI，直到显示TMI。这是一种更好的调色滑块，第一个滑块控制暖冷色温

5.  从工具架上创建环境灯，将其设置为蓝色。

6.  选择一个较好的相机视角，按住Ctrl键并单击工具架上相机的按钮，这将为你复制具有视角属性设置的相机。 


##  Setup a render node, render

摘要：在PBR模式下创建ROP（Render OPerator，即渲染全局节点），在大多数情况下，这是当今首选的渲染器

![Lighting_setup_rop_sm.gif](http://www.tokeru.com/cgwiki/images/7/7f/Lighting_setup_rop_sm.gif)

1.  创建一个rop网络。虽然你可以在更高层“/out”网络中创建节点，但我发现其更容易跟踪子网络中的所有内容，并且跳转变得更加本地化。

2.  进入所创建的rop，创建一个mantra节点。默认设置将指向你创建的相机（/obj/cam1）。

3.  在渲染标签下，将渲染引擎设置为physically based rendering(简称PBR）

4.  在主视图中，切换到渲染视图选项卡。

5.  点击渲染

6.  惊叹于你的阳光茶壶。

##  Material creation

摘要：从调色板创建材材质，进行分配。

如果还没有，请回到/obj网络的顶部。

![Lighting_setup_shop_sm.gif](http://www.tokeru.com/cgwiki/images/7/70/Lighting_setup_shop_sm.gif)

1.  创建一个shop网络

2.  在右下角的选项卡中找到Material Palatte选项卡。

3.  在material palette的右侧，单击/shop/标题旁边的上下箭头以将其折叠。现在你应该看到关闭了。

4.  单击/obj/shopnet1旁边的三角形以将其打开（这是你刚创建的shopnet）

5.  在左侧，找到Generic下的Principled shader节点（由于houdini版本的迭代，这里有所不同，你也可以选择其它houdini内置的材质进行测试）。

6.  将其拖到/obj/shopnet1标题下。短暂的延迟后，你将看到一个名为principledsh..的着色器正方形

7.  返回到network选项卡，进入shop网络，其中包含你刚制作的材料。

8.  将Principled 材质节点直接拖动到茶壶上（可以在opengl视图或渲染视图中执行此操作）。这将分配材质。

分配材质的另一种方法是直接从对象节点进行分配：

1.  返回到/obj网络，找到grid节点，转到其material菜单，单击选择器图标，将显示一个选择器。

2.  选择/objshopnet1/mantrasurface‘。启用export relative path,点击accpt。

如果保持渲染视图运行，你应该可以在对象上看到更新后的材质。

##  Things to note

The material palette is similar to the maya visor, ie, a place to get and store presets. Like the Visor, its clunky and no-one really uses it. For some strange reason, SideFx decided this is the place to store the mantrasurface node, rather than the expected tab-complete menu in SHOPs, which itself is unusually empty. Fixed in a future version perhaps? (note to self; find a way to add nodes to the tab menu...)

I find using relative paths ( ../shopnet/mymaterial ) easier to read than explicit paths ( /obj/shopnet1/mymaterial ). Also means if you have to copy and paste setups, its less likely to clash with existing nodes in other shots.

The mantrasurface node is the latest attempt by SideFx at an all purpose, mia_material/vray/arnold style all-in-one physically correct shader. It does diffuse, 2 reflect (for base and clearcoat), refract, translucence, emission, opacity, with support for maps on most parameters. The one in H14 has had a bit of a tidy up, the tabs are cleaner, shader parameters better named than previous versions. Prior to the mantrasurface node was the surfacemode node (which you can still find in the palette underthe mantrasurface)

A great feature of the mantrasurface node is you can dive inside and see how it works. A horrible feature is that its pretty messy under there and a bit overwhelming at first! Don't try and understand it all in one hit, its better if you have a specific interest to just dive into that section and poke around, most of it is clearly labelled.

Other Houdini users have made their own versions of an all-in-one physically correct shader. The layered pbr shader on the orbolt store is very nice. A key difference is that its written in vex rather than vop nodes, so isn't designed to be pulled apart. There's also been attempts to recreate the disney bsdf shader, also worth a look. (provide links here) 

##  Material attribute overrides

摘要：取一个材质属性名称（例如baseColor），在一个对象或点级别创建一个具有相同名称的属性，为其赋予一个新值，它将覆盖该对象/点的材质默认值。很强大。

![Mat_attr_override.gif](http://www.tokeru.com/cgwiki/images/5/50/Mat_attr_override.gif)

###  Override at object level

[以下3,4,5步操作因为houdini版本的迭代没有了，类似的操作可以在geo内创建一个material节点，然后就可进行类似操作]()

1.  编辑材质的基础颜色，进行渲染。茶壶和地板都变成绿色。

2.  将鼠标悬停在baseColor标签上，请注意其名称为baseColor baseColorg baseColorb。baseColor是一个向量。

3.  选择茶壶，转到材质选项卡

4.  点击材质名称最右侧的下拉菜单，选择select and create local material parameters

5.  在弹出窗口，选择baseColor，点击Accept。

6.  将此颜色设为红色

7.  渲染视图现在显示红色的茶壶和绿色的地板，即使它们都使用相同的材质。

从同一下拉列表中你可以选择在对象上复制完整的材质界面，或将其重置为默认值，或删除局部替代。接下来，我们将在点层面进行覆盖

###  Override at point level

1.  进入grid对象，添加一个point vop节点，使其可以成为可渲染/显示的节点。

2.  进入point vop节点，创建一个bind export节点，将其类型设置为color，名称为baseColor。

3.  将P直接连接bind export节点。

##  Assign materials to faces

##  Point Colour (Cd) and Alpha

##  AOVs or Extra Image Planes

### Defining AOVs in a shader

### Add AOVs to Rop

##  Houdini rendering setup from a maya perspective

##  Renderstate vop

##  Material wrangle via snippet

##  Todo


