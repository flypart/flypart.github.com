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

