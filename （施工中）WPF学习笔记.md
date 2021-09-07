---

layout:     post   				        # 使用的布局（不需要改）
title:      WPF学习笔记					# 标题 
subtitle:   随时弃坑						# 副标题
date:       2021-08-20 				    # 时间
author:     YXWang 					    # 作者
header-img: img/post-bg-keybord.jpg	 	# 这篇文章的标题背景图片
catalog: true 						    # 是否归档
tags:								    # 标签
    - WPF
    - 施工中
---

# WPF不深入理解笔记

课件主要来自 [WPF入门基础教程合集-bilibili](https://www.bilibili.com/video/BV1mJ411F7zG) 和 [WPF深入讲解合集-bilibili](https://www.bilibili.com/video/BV1HC4y1b76v) 



## 开篇入门

<img src=".\media\image-20210715214240910.webp" style="zoom: 33%;" />

1. 每个XAML文档只能有一个顶级元素

2. 有命名空间的性质

3. XAML格式**区分大小写**，但属性类型可以不区分大小写 

<img src=".\media\image-20210715215033185.webp" style="zoom: 50%;" />

创建元素可以在工具箱拖控件到设计窗口，也可以手动写；

双击控件可以快速设置其行为；

### WPF的编译过程

.xaml 和 .cs 文件经历如下过程：转换成 .baml 资源（即 .xaml 的二进制形式，解析速度更佳），嵌入到程序集中，应用程序工作时再从程序集读取这些控件信息

这里课件是用 [dnSpy](https://github.com/dnSpy/) 反编译读 .dll 文件得出该结论，还反编译了虎牙直播的图标资源作为示例，看起来不错（？）



## 布局

小技巧：VS 用 `Ctrl+K` （先按）和 `Ctrl+C` （后按）快捷键可以快速注释掉多行代码

### 常见的布局属性

| **常用属性**         | **功能**               |
| -------------------- | ---------------------- |
| HorizontalAlignment  | 设置元素水平位置       |
| VerticalAlignment    | 设置元素垂直位置       |
| Margin               | 指定元素与容器的边距   |
| Height               | 指定元素高度           |
| Width                | 指定元素宽度           |
| WinHeight / WinWidth | 指定元素最小高度和宽度 |
| MaxHeight / MaxWidth | 指定元素最大高度和宽度 |
| Padding              | 指定元素内部边距       |

### 常见的布局容器

| **布局容器** |                                                              |
| ------------ | ------------------------------------------------------------ |
| Grid         | 设置框架整体页面布局  可把空间切分成多行多列                 |
| StackPanel   | 可用Orientation属性设置元素排列方式，默认是垂直布局          |
| WrapPanel    | 可用Orientation属性设置元素排列方式，默认是水平布局  具备自动换行的特点 |
| DockPanel    | 设置元素的锚定位置                                           |
| UniformGrid  | 设置元素均匀分布地填充空间  可以显式指定行数列数             |

#### 例：Grid分隔2行2列表格：

```xaml
<Grid ShowGridLines="True">
    <Grid.RowDefinitions>
        <RowDefinition> </RowDefinition>
        <RowDefinition> </RowDefinition>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition> </ColumnDefinition>
        <ColumnDefinition> </ColumnDefinition>
    </Grid.ColumnDefinitions>
</Grid>
```

#### 例：Orientation="Horizontal"

```xaml
<StackPanel Orientation="Horizontal">
    <!-- 在未指定时, WrapPanel默认状态是Orientation="Horizontal", 
		 StackPanel默认Orientation="Horizontal"-->
    <Button Width="100" Height="30" Background="Red"></Button>
    <Button Width="100" Height="30" Background="Yellow"></Button>
    <Button Width="100" Height="30" Background="Blue"></Button>
    <Button Width="100" Height="30" Background="Green"></Button>
    <Button Width="100" Height="30" Background="Pink"></Button>
</StackPanel>
```

#### 例：DockPanel

```xaml
<DockPanel>
    <Button DockPanel.Dock="Bottom" Width="100" Height="30"></Button>
    <Button DockPanel.Dock="Left" Width="100" Height="30"></Button>
    <Button DockPanel.Dock="Right" Width="100" Height="30"></Button>
    <Button DockPanel.Dock="Top" Width="100" Height="30"></Button>
</DockPanel> 
```

#### 例：UniformGrid

```xaml
<UniformGrid Columns="3" Rows="4">
    <Button Width="100" Height="30"></Button>
    <Button Width="100" Height="30"></Button>
    <Button Width="100" Height="30"></Button>
    <Button Width="100" Height="30"></Button>
    <Button Width="100" Height="30"></Button>
</UniformGrid>
```



### 控件结构

#### WPF控件图示 

不需要死记硬背所有控件，但需要记住控件所在的**命名空间** 

<img src=".\media\image-20210717101311254.webp" style="zoom:80%;" />

####  用Content属性设置更复杂的按钮

转到定义可以看到，`Content `方法可以接收更复杂的 `object` 对象，而 `Text` 只能接收静态字符串 `string` 

```c#
public object Content { get; set; }
// 摘要:
// 获取或设置一个指定如何设置格式的复合字符串System.Windows.Controls.ContentControl.Content 属性，它显示为一个字符串。
//
// 返回结果:
// 指定如何设置格式的复合字符串 System.Windows.Controls.ContentControl.Content 属性，它显示为一个字符串。
    [Bindable(true)]
    [CustomCategoryAttribute("Content")]
```

```C#
public string Text { get; set; }
// 摘要:
// 获取 System.Windows.Documents.TextPointer 到中的内容的末尾 System.Windows.Controls.TextBlock。
//
// 返回结果:
// 一个 System.Windows.Documents.TextPointer 到中的内容的末尾 System.Windows.Controls.TextBlock。
```

1. 凡是继承于 `ContentControl` 的控件，定义内容都是用 `Content`；

2. 除 `TextBlock` 使用的是 `Text` 之外，大部分控件都是 `Content` 设置其显示内容。

3. 继承于 `Control` 的大部分控件都具备 `Padding` 属性，`TextBlock` 则单独实现了 `Padding` 属性。

4. `Margin` 是外边距，`Padding` 是内边距

5. `Content` 由于是 `object` 类型，所以对于常用的 `Button`, `CheckBox` 等类型控件，不仅可以接收字符串类型，也可以接受各种复杂的对象类型

   

### 样式

WPF中的各类控件元素都可以自由的设置样式，诸如：字体(FontFamily)、字体大小(FontSize)、背景颜色(Background)、字体颜色(Foreground)、边距(Margin)、水平位置(HorizontalAlignment)、垂直位置(VerticalAlignment)等，而样式是组织和重用的重要工具；

通过 `Style` 创建一系列封装所有这些细节的样式，就可以避免使用重复的标记填充XAML，此后只需要设置元素的 样式(Style) 属性就能设定其样式；

#### 样式的定义和使用

```xaml
<Window.Resources>
    <Style x:Key="BaseStyle" TargetType="Button">
        <Setter Property="Width" Value="100"></Setter>
        <Setter Property="Height" Value="40"></Setter>
    </Style>

    <Style x:Key="MyStyle1" TargetType="Button" BasedOn="{StaticResource BaseStyle}">
        <!-- 使 MyStyle1 样式继承 BaseStyle 样式 -->
        <Setter Property="Foreground" Value="Black"> </Setter>
        <Setter Property="Background" Value="Aqua"> </Setter>
        <Setter Property="Content" Value="Style"> </Setter>            
    </Style>
</Window.Resources>

<StackPanel Orientation="Horizontal">
    <Button Style="{StaticResource MyStyle1}"></Button>
    <Button Style="{StaticResource MyStyle1}"></Button>
</StackPanel>
```

效果：

<img src="media\image-20210717131559965.webp" alt="image-20210717131559965" style="zoom:50%;" />



### 触发器

当达到了触发的条件的时候，触发器就执行预期内的响应，可以是样式、数据变化、动画等。

触发器通过 `Style.Triggers` 集合连接到样式中，每个样式都可以有任意多个触发器，并且每个触发器都是 `System.Windows.TriggerBase` 的派生类实例

#### 触发器的类型

`Trigger` ：监测依赖属性的变化、触发器生效

`MultiTrigger` ：通过多个条件的设置、达到满足条件、触发器生效

`DataTrigger` ：通过数据的变化、触发器生效

`MultiDataTrigger ` ：多个数据条件的触发器

`EventTrigger` ：事件触发器,触发了某类事件时,触发器生效。

#### 例：条件触发-改变按钮样式

```xaml
<Window.Resources>
    <Style x:Key="BaseStyle" TargetType="Button">
        <Setter Property="Width" Value="100"></Setter>
        <Setter Property="Height" Value="40"></Setter>
        <Style.Triggers>
            <Trigger Property="IsMouseOver"  Value="True">
                <!-- 单触发 -->
                <Setter Property="Foreground" Value="Blue"></Setter>
                <Setter Property="FontSize" Value="20"></Setter>
            </Trigger>

            <MultiTrigger>
                <MultiTrigger.Conditions>
                    <!-- 多个触发条件 -->
                    <Condition Property="IsMouseOver" Value="True"></Condition>
                    <Condition Property="IsFocused" Value="True"></Condition>
                </MultiTrigger.Conditions>
                <MultiTrigger.Setters>
                    <!-- 生效 -->
                    <Setter Property="Background" Value="Pink"></Setter>
                </MultiTrigger.Setters>
            </MultiTrigger>
        </Style.Triggers>
    </Style>

    <Style x:Key="MyStyle1" TargetType="Button" BasedOn="{StaticResource BaseStyle}">
        <!-- 使 MyStyle1 样式继承 BaseStyle 样式 -->
        <Setter Property="Foreground" Value="Black"> </Setter>
        <Setter Property="Background" Value="Aqua"> </Setter>
        <Setter Property="Content" Value="Style"> </Setter>            
    </Style>

    <Style x:Key="MyStyle2" TargetType="TextBox">
        <Setter Property="Width" Value="100"></Setter>
        <Setter Property="Height" Value="40"></Setter>
        <Style.Triggers>
            <DataTrigger Binding="{Binding RelativeSource={RelativeSource Mode=Self}, Path=Text}" Value="123">
                <!-- 数据触发器，当输入框内容是123时，背景颜色变绿 -->
                <Setter Property="Background" Value="Green"></Setter>
            </DataTrigger>
        </Style.Triggers>
    </Style>
</Window.Resources>
    
<StackPanel Orientation="Horizontal">
    <Button Style="{StaticResource MyStyle1}"></Button>
    <Button Style="{StaticResource MyStyle1}"></Button>
    <Button Style="{StaticResource MyStyle1}"></Button>
    <TextBox Style="{StaticResource MyStyle2}"></TextBox>
</StackPanel>
```

效果：

<img src="media\image-20210717182729208.webp" alt="image-20210717231810708" style="zoom:50%;" />



### 例：设计 MicrosoftToDo 主界面

~~你已经学了那么多东西了，快来做个 MicrosoftToDo 吧~~

```xaml
<Window
    x:Class="MicrosoftToDO.MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:behavior="http://schemas.microsoft.com/xaml/behaviors"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:local="clr-namespace:MicrosoftToDO"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:viewmodels="clr-namespace:MicrosoftToDO.ViewModels"
    Width="1000"
    Height="650"
    mc:Ignorable="d">
    <Window.DataContext>
        <viewmodels:MainViewModel />
    </Window.DataContext>
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="210" />
            <!-- 左侧状态栏 -->
            <ColumnDefinition />
        </Grid.ColumnDefinitions>

        <DockPanel LastChildFill="True">

            <TextBlock
                Margin="10,10,0,20"
                DockPanel.Dock="Top"
                FontSize="12"
                Foreground="#FF737373"
                Text="Microsoft To Do" />

            <DockPanel
                Margin="0,10"
                DockPanel.Dock="Top"
                LastChildFill="False">
                <Image
                    Width="30"
                    Height="30"
                    Margin="10,0,0,0"
                    Source="logo.jpg" />
                <TextBlock
                    Margin="10,0,0,0"
                    VerticalAlignment="Center"
                    Text="henji" />

                <TextBlock
                    Margin="0,0,10,0"
                    VerticalAlignment="Center"
                    DockPanel.Dock="Right"
                    FontFamily="./Fonts/#iconfont"
                    FontSize="22"
                    Text="&#xe645;" />
            </DockPanel>

            <ListBox BorderThickness="0" ItemsSource="{Binding MenuItems}">

                <ListBox.ItemContainerStyle>
                    <Style TargetType="ListBoxItem">
                        <Setter Property="HorizontalContentAlignment" Value="Stretch" />
                        <Setter Property="Height" Value="45" />
                        <Setter Property="Margin" Value="0,2,0,0" />
                        <Setter Property="Template">
                            <Setter.Value>
                                <ControlTemplate TargetType="{x:Type ListBoxItem}">
                                    <Grid>
                                        <Border x:Name="bd1" SnapsToDevicePixels="{TemplateBinding SnapsToDevicePixels}" />
                                        <Border
                                            x:Name="bd2"
                                            Margin="5,8"
                                            SnapsToDevicePixels="{TemplateBinding SnapsToDevicePixels}" />
                                        <ContentPresenter
                                            HorizontalAlignment="Stretch"
                                            RecognizesAccessKey="True"
                                            SnapsToDevicePixels="{TemplateBinding SnapsToDevicePixels}" />
                                    </Grid>

                                    <ControlTemplate.Triggers>
                                        <Trigger Property="IsMouseOver" Value="True">
                                            <Setter TargetName="bd1" Property="Background" Value="#FFF5F4F4" />
                                        </Trigger>
                                        <Trigger Property="IsSelected" Value="True">
                                            <Setter Property="FontWeight" Value="Bold" />
                                            <Setter Property="Foreground" Value="{Binding BackColor}" />
                                            <Setter TargetName="bd1" Property="Opacity" Value="0.1" />
                                            <Setter TargetName="bd1" Property="Background" Value="{Binding BackColor}" />
                                            <Setter TargetName="bd2" Property="BorderThickness" Value="2,0,0,0" />
                                            <Setter TargetName="bd2" Property="BorderBrush" Value="{Binding BackColor}" />
                                        </Trigger>
                                    </ControlTemplate.Triggers>
                                </ControlTemplate>
                            </Setter.Value>
                        </Setter>
                    </Style>
                </ListBox.ItemContainerStyle>

                <!--  listbox的子项数据模板  -->
                <ListBox.ItemTemplate>
                    <DataTemplate>
                        <DockPanel
                            Margin="10,0"
                            Background="Transparent"
                            LastChildFill="False">
                            <TextBlock Style="{StaticResource iconTextBlockStyle}" Text="{Binding Icon}" />
                            <TextBlock
                                Margin="10,0,0,0"
                                VerticalAlignment="Center"
                                FontSize="14"
                                Text="{Binding Name}" />
                            <TextBlock
                                VerticalAlignment="Center"
                                DockPanel.Dock="Right"
                                FontWeight="Light"
                                Text="{Binding Count}" />
                        </DockPanel>
                    </DataTemplate>
                </ListBox.ItemTemplate>
            </ListBox>
        </DockPanel>

        <Border Grid.Column="1" Background="Green" />
    </Grid>
</Window>
```

### 控件模板

控件模板用于来定义控件的外观、样式，还可通过 `控件模板的触发器(ControlTemplate.Triggers)` 修改控件的行为、响应动画等。

- 在 WPF 中，每个控件都是无外观的，这意味着我们可以完全自定义其可视元素的外观，但是不能修改其内部的行为，因为控件的行为已经被固定在控件的具体类中。
- 在 Winform 中，控件的外观与行为都被固定在控件的具体类中，若想修改按钮的的边框弧度、或者修改控件本身一些细节，必须需要在修改外观的同时，把原来具备的所有行为重写一遍，我们大多数称之为自定义控件。
- ~~不过这和也没学过 Winform 的我有什么关系呢~~

#### 模板绑定

相当于是模板定义了一个新类，覆盖了原始的button类；

只有在模板中 `xxx =""{TemplateBinding xxx}"` 预留了自定义的功能，才允许下面的xaml修改生效；

```xaml
<!-- .xaml文件 -->
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="MainWindow" Height="240" Width="404">
    <Window.Resources>
        <Style x:Key="ButtonStyle1" TargetType="Button">
            <Setter Property="Template">
                <Setter.Value>
                    <ControlTemplate TargetType="Button">
                        <Border x:Name="border" 
                                CornerRadius="5"
                                BorderBrush="{TemplateBinding BorderBrush}" 
                                BorderThickness="{TemplateBinding BorderThickness}"
                                Background="{TemplateBinding Background}">

                            <ContentPresenter x:Name="contentPresenter" Focusable="False"
                                              HorizontalAlignment="{TemplateBinding HorizontalAlignment}"
                                              Margin="{TemplateBinding Margin}"
                                              RecognizesAccessKey="True"
                                              SnapsToDevicePixels="{TemplateBinding SnapsToDevicePixels}"
                                              VerticalAlignment="{TemplateBinding VerticalAlignment}">
                            </ContentPresenter>
                        </Border>
                        <ControlTemplate.Triggers>
                            <Trigger Property="IsMouseOver" Value="True">
                                
                            </Trigger>
                        </ControlTemplate.Triggers>

                    </ControlTemplate>
                </Setter.Value>
            </Setter>
        </Style>
    </Window.Resources>
    
    <Grid>
        <Button Width="100" Height="40" 
                Content="Hello" FontStyle="Italic"
                Style="{StaticResource ButtonStyle1}"
                HorizontalAlignment="Center"
                VerticalAlignment="Center"
                Background="BlueViolet"
                Click="Button_Click">
        </Button>
    </Grid>

</Window>
```

```c#
//.cs文件 

using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace WpfApp1
{
    /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }
        private void Button_Click(object sender, RoutedEventArgs e)
        {
            MessageBox.Show("123");
        }
    }
}

```

效果：

<img src="media\image-20210718133532055.webp" alt="image-20210718133532055" style="zoom:50%;" />

### 数据模板

```xaml
<Window x:Class="WpfApp1.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp1"
        mc:Ignorable="d"
        Title="MainWindow" Height="240" Width="400">

    <Grid>
        <ComboBox x:Name="com" Width="100" Height="20">
            <ComboBox.ItemTemplate>
                <!-- 很多容器都具备 ItemTemplate -->
                <DataTemplate>
                    <!-- 数据模板，为了使下拉菜单能正确显示 Student 类的姓名和性别 -->
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Text="{Binding Name}"></TextBlock>
                        <!-- 绑定 Student 对象的 Name 和 Sex 成员 -->
                        <TextBlock Text="-"></TextBlock>
                        <TextBlock Text="{Binding Sex}"></TextBlock>
                    </StackPanel>
                </DataTemplate>
            </ComboBox.ItemTemplate>
        </ComboBox>
    </Grid>

</Window>
```

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace WpfApp1
{
    /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
            List<Student> array = new List<Student>();
            array.Add(new Student { Name = "张三", Sex = "M" });
            array.Add(new Student { Name = "李四", Sex = "F" });
            array.Add(new Student { Name = "王五", Sex = "NB" });
            com.ItemsSource = array;
        }
    }

    public class Student
    {
        public string Name { get; set; }
        public string Sex { get; set; }

    }
}
```

效果：

<img src="media\image-20210718224223447.webp" alt="image-20210718231828640" style="zoom:50%;" />



### 例：DataGrid 实现学生管理数据库的完整项目

[WPF入门基础教程合集_p9_](https://www.bilibili.com/video/BV1mJ411F7zG?p=9)

~~这个暂时没整理，回头自己照着抄一遍；~~

仿写该项目的过程时发现要注意给 DataGrid 设置 AutoGenerateColumns 属性为 "False" ，不然你有几个 public 变量它就给你整出多余的几列；



### 理解绑定

把某个显示元素和界面其他元素绑定，或者与后台代码相关联；

#### 数据绑定

直接使用 ElementName 属性

```
<Grid>
    <StackPanel>
        <TextBox x:Name="textblock" Text="我也是一个广door人"></TextBox>
        <TextBox Text="{Binding ElementName=textblock, Path=Text}"></TextBox>
    </StackPanel>
</Grid>    
```

#### 设置数据的上下文

表示该窗口可以访问 MainViewModel 中的公开属性方法，此处该界面的文本绑定了 MainViewModel 中一个名为 MessageFromMain 的变量

```
<Windows.DataContext>
    <local: MainViewModel>
</Windows.DataContext>

<Grid>
    <StackPanel>
        <TextBox x:Name="textblock" Text="我也是一个广door人"></TextBox>
        <TextBox Text="{Binding MessageFromMain}"></TextBox>
    </StackPanel>
</Grid>  
```



 

