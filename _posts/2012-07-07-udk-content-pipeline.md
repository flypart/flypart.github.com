---
layout: post
title: "Udk Content Pipeline"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## FBX Content Pipeline ##

UDK使用FBX作为中间格式将3DSMax等软件中制作的资源导入到UDK编辑器中。使用说明见：[http://udn.epicgames.com/Three/FBXPipeline.html](http://udn.epicgames.com/Three/FBXPipeline.html)

FBX是由Autodesk公司开发的文件格式，3dsMax、Maya、MotionBuilder等软件都支持导出。除此之外它还有如下好处：

- Static Mesh、Skeletal Mesh、Animation、Morph Target都可用这种格式描述。

- 多个资源可以被包含在同一个fbx文件中。

- 一次操作就可以导入多个LOD和Morphs。

- 导入Material和Textuer时可自动将它们附在mesh上。

导入时候的界面是：

![](/image/udk/ImportFBX.jpg)

总体设置：


- Import Type：文件类型会自动从文件头信息里获取，含有Deformers则为Skeletal Mesh，否则为Static Mesh。如果文件实际是StaticMesh，而用户手动选择为SkeletalMesh，则什么也不会导入；如果文件实际是SkeletalMesh，而用户手动选择为StaticMesh，则会作为StaticMesh导入。


- Override Full Name：如果设为True、并且fbx文件中只含有一个Mesh，则该mesh会以此名字命名。否则会遵守命名规则，由用户在该对话框中输入的Name和fbx文件中节点的名字合成。


- Import Mesh LOD：如果设为True，将会从fbx文件中读取LOD，否则只读取基础Mesh。对于SkeletalMesh，LOD模型可以被绑定到相同的或者不同的骨骼。如果绑定到了不同的骨骼，那么它必须满足Unreal的要求（这个要求是什么？）。

SkeletalMesh导入设置：

- Import Morph Targets：是否创建Unreal Morph Objects。支持Maya BlendShadpes和3dsmax Morph modifiers。

- Import Animation：设为True则会提取出fbx文件中的动画，生成新的AnimSet

- Import Rigid Animation：设为True则会导入fbx文件中的unskinned、hierarchy-based animation。

- Resample Animations：设为True则会对所有动画进行重新采样，频率30FPS。

- Use T0As Ref Pose：设为True会用Animation Track的第一帧取代skeletal mesh的reference pose。

- Split Non  Matching Triangle：设为True则没有平滑组的三角形会被split，共享的顶点会复制一份。

Static Mesh导入设置：

- Combine Meshes：将fbx文件中的mesh合并为一整个mesh

- Explicit Normals：设为true则从fbx文件中读取法线

- Remove Degenerates：移除退化的三角形，一般都得开启该选项。

Material导入设置：

- Import Materials：设为True则会把fbx文件中的每个material都在Unreal中创建一份。

- Import Textures：把fbx文件中引用到的纹理都导入到Unreal。如果上一项设置为true，则必然会导入纹理。

- Invert Normal Maps：如果纹理导入了，那么开启该项会将所有法线图方向倒转。

- Create Groups Automaticall：任何导入的纹理或材质都会被放入指定包得Materials或者Textures组中。

FBX Scene Info窗口：可在此窗口中查看fbx文件的场景树信息。

![](/image/udk/SceneInfo.jpg)

- States面板：在其中显示统计信息。

- Meshes：网格的统计信息。

- Animation：显示动画信息。

## FBX Static Mesh Pipeline ##

使用fbx导入网格是件简单的事情，不仅Meshes可以被导入，同时附着在mesh上的纹理（仅包含diffuse和normal）也会被导入，此外还会生成自动生成材质并应用到Mesh上。fbx导入支持如下特性：

- 设定了材质和纹理的Mesh

- 自定义的碰撞体

- 多组uv

- 平滑组Smoothing Group

- 顶点颜色

- LODs

- 多个分离的Static Meshes（可以在导入时合并为一个）

你可以使用任何工具来创建网格，只要它们支持fbx导出。你要做的只是设置好uv、摆放好模型，然后一切都交由工具来完成。

网格最多只能有65535个顶点，超过此数目导入会失败。

模型的中心点：决定了旋转、缩放、平移围绕哪个点进行，永远位于0,0,0处。最好在创建网格的时候把中心点对准其某个角，这样在Editor中snapping的时候会比较容易。

![](/image/udk/pivot_origin.jpg)

**三角化**：模型必须被三角化。有如下几种方法：

- 建模的时候只使用三角形：最佳方法，直接控制。

- 在3D app中自动三角化：good，允许清理和在导出前修改。

- 让FBX导出插件三角化：OK，对简单网格可以。

- 让导入插件三角化：OK，对简单网格可以。

最好永远都手工三角化。自动三角化可能会产生意外的结果。

**UV纹理坐标**：支持导入多重纹理坐标。对static mesh，一般用一组坐标采样diffuse，另一组坐标采样光照图。具体参见展开光照图UV部分？。

**创建法线图**：最好创建高精度模型来生成法线图。具体参考各个软件的说明。

**材质**：前面提到DCC软件中创建的材质也会自动被导入到UDK中，用户不需要再导入纹理、创建材质、附上材质。在mesh含有多个材质的时候需要特别注意，具体参见 FBX Material Pipeline？

**碰撞体**：把简化模型作为碰撞体是非常重要的。UDK中可以在StaticMeshEditor中为mesh创建碰撞体。不过最好还是在3D建模软件中创建，通过fbx文件一起导入到udk中，特别当mesh并不是凸多面体的时候。

碰撞体网格的命名是有规则的。

- UBX_[RenderMeshName]：Box形状。不能拖动顶点的位置。

- USP_[RenderMeshName]：Sphere形状。不需要有太多segment，因为在游戏中它被表示为一个球。不要随意移动任何顶点。

- UCX_[RenderMeshName]：Convex形状。

RenderMeshName必须与对应的渲染网格的名字一致。例如名字为Tree_01的网格的碰撞体应被命名为UCX_Tree_01。

Sphere只参与rigid-body collision和Unreal's zero-extent traces（例如武器），不参与non-zero extent traces（例如人物移动）。此外Sphere和Box也只能用于uniformly缩放。因此一般情况下应创建UCX碰撞体。

制作好渲染和碰撞模型后，可以把它们导出到同一个fbx文件中。导入Editor之后，会自动把碰撞模型设置好。

注意：当用多个Convex描述一个物体时，这些Convex最好不要相交，并且在中间留有一些空隙。

![](/image/udk/lollipop.jpg)

**顶点颜色**：可以通过fbx导入，不用做特殊设置。

![](/image/udk/vertex_color.jpg)

**导出模型**：静态模型可以一次导出单个或者多个模型。导入时会把多个模型分拆成多个mesh。如果导入时制定了Combine Meshes，则会合并成一个。

3dsmax操作：选中模型->Export Selected->Save->设置一些Options

Maya操作：略。


**导入模型**：

在内容浏览器中点击Import按钮打开文件对话框，选择要导入的fbx文件。

在前面描述的FBX Import Dialog中进行设置。点击Import按钮后，Mesh、Material和Textuer都会被导入，显示在内容浏览器中。

![](/image/udk/import_result.jpg)

在Static Mesh Editor中可以查看碰撞体有没有被正确导入。

![](/image/udk/import_collision.jpg)

**Static Mesh LODs**

LOD会含有更少的顶点，或者更精简的材质。

导出设置：在3dsmax中选中所有的mesh，包括LOD的。点击Group命令。输入组的名称来创建一个组。打开Utilities面板中的Level of Detail工具，选中刚创建的组，点击Create New Set按钮创建一个LOD set，把选中组的mesh都添加进去，它们会自动按照复杂度排好序。选中组和碰撞体模型，进行导出。确保开启了export of animations。

导入设置：确保Import Mesh LODs被选中即可，在Static Model编辑器中可以查看结果。

单独导入LOD：在Static Mesh Editor中执行Import Mesh LOD，选择合适的LOD Level导入即可。

## FBX Skeletal Mesh Pipeline ##

通过FBX导入Skeletal Mesh，不仅可以导入mesh，还可导入animation和morph targets，以及材质和纹理。具体支持的特性有：

- 导入Skeletal Mesh，同时包括材质和纹理

- 导入Animations

- Morph Target

- 多组UV

- 平滑组

- 顶点颜色

- LODs

注意：当前通过一个文件只能导入一个Animation，但可以导入多个Morph Targets。

**Single Mesh vs Multi-part Mesh**

Skeletal Mesh可以由单个Mesh组成， 也可以由多个绑定到同一个Skeleton的多个Mesh组成。采用多个Mesh可以让每个部件有LOD，也可以让不同组件被分开导入，这不会有性能上的影响。在Unreal Editor中多个单个组件会组合起来。

![](/image/udk/multipart.jpg)

**Rigging**

意指绑定骨骼。确定每个顶点被那些骨骼影响。

**Skeleton**

根据使用的模型制作软件，有多种方法可以创建骨骼。在3DsMax中，可以使用标准的BonesTools等等，教程多的去了。

**Binding**

每种软件的骨骼绑定方法也不同。3DsMax中，通过SkinModifier进行绑定。具体流程是：选中要绑定的Mesh；添加一个Skin Modifie；展开Skin，点击Add按钮，弹出Select Bones对话框；选中影响这个Mesh的骨骼，然后这些骨骼会显示在modifer的Bones列表中；接下来可以调整骨骼的权重。

**Pivot Point**

一个Skeletal Mesh的中心点永远在骨骼的根骨头处。

**三角化、NormalMap、Material、VertexColor**

同静态模型。

**导出**

选中所有的Mesh和骨骼，导出FBX即可。

**导入**


































