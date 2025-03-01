---
title: FrameworkElement Extensions
author: Sergio0694
description: Provides attached dependency properties and extensions for the FrameworkElement type.
keywords: windows 10, uwp, windows community toolkit, uwp community toolkit, uwp toolkit, FrameworkElement, extensions
dev_langs:
  - csharp
  - vb
---

# FrameworkElement Extensions

[`FrameworkElementExtensions`](/dotnet/api/microsoft.toolkit.uwp.ui.frameworkelementextensions) provides a collection of attached dependency properties, helpers and extension methods to work with [`FrameworkElement`](/uwp/api/windows.ui.xaml.frameworkelement) objects. In particular, it also includes a series of extension methods to explore the logical tree from a given UI element and find child or parent objects.

> **Platform APIs:** [`FrameworkElementExtensions`](/dotnet/api/microsoft.toolkit.uwp.ui.frameworkelementextensions), [`DependencyObjectExtensions`](/dotnet/api/microsoft.toolkit.uwp.ui.DependencyObjectExtensions)

## Logical tree extensions

The `FindChild` and `FindParent` methods (and their overloads) provide an easy way to explore the logical tree starting from a given `FrameworkElement` instance and find other controls connected to it.

These APIs differ from the *visual tree* extensions (in the [`DependencyObjectExtensions`](/dotnet/api/microsoft.toolkit.uwp.ui.DependencyObjectExtensions) class) where extra containers and styles can wrap other elements. The logical tree instead defines how controls are directly connected through construction. These methods can also be used on controls that aren't yet connected or rendered in the visual tree.

Here are some examples of how these extensions can be used:

```csharp
// Include the namespace to access the extensions
using Microsoft.Toolkit.Uwp.UI;

// Find a logical child control using its name
var control = uiElement.FindChild("MyTextBox");

// Find the first logical child control of a specified type
control = uiElement.FindChild<ListView>();

// Find all logical child controls of the specified type.
// The FindChildren extension will iterate through all the existing
// child nodes of the starting control, so here we also use the
// OfType<T>() LINQ extension (from System.Linq) to filter to a type.
foreach (var child in uiElement.FindChildren().OfType<ListViewItem>())
{
    // ...
}

// Find the first logical parent using its name
control = uiElement.FindParent("MyGrid");

// Find the first logical parent control of a specified type
control = uiElement.FindParent<Grid>();

// Retrieves the Content for the specified control from whatever its "Content" property may be
var content = uiElement.GetContentControl();
```

```vb
' Include the namespace to access the extensions
Imports Microsoft.Toolkit.Uwp.UI

' Find a logical child control using its name
Dim control = uiElement.FindChild("MyTextBox")

' Find the first logical child control of a specified type
control = uiElement.FindChild(Of ListView)()

' Find all the child nodes of a specified type. Like in the C# example,
' here we are also using a LINQ extension to filter the returned items.
For Each child In uiElement.FindChildren().OfType(Of ListViewItem)()
    ' ...
Next

' Find the first logical parent using its name
control = uiElement.FindParent("MyGrid")

' Find the first logical parent control of a specified type
control = uiElement.FindParent(Of Grid)()

' Retrieves the Content for the specified control from whatever its "Content" property may be
Dim content = uiElement.GetContentControl()
```

## EnableActualSizeBinding

The `EnableActualSizeBinding` property allows you to enable/disable the binding for the `ActualHeight` and `ActualWidth` extensions. The `ActualHeight` and `ActualWidth` properties then make it possible to bind to the `ActualHeight` and `ActualWidth` properties of a given object.

Here is an example of how the `ActualWidth` attached property can be used in a binding:

```xaml
<Rectangle
    x:Name="TargetObject"
    ui:FrameworkElementExtensions.EnableActualSizeBinding="true"/>
...
<TextBlock Text="{Binding ElementName=TargetObject, Path=(ui:FrameworkElementExtensions.ActualHeight)}" />
```

## AncestorType

The `AncestorType` attached property will walk the visual tree from the attached element for another element of the specified type.  That value will be stored in the attached element's `Ancestor` property.  This can then be used for binding to properties on the parent element.  This is similar to the [`FindAncestor`](/dotnet/api/system.windows.data.relativesourcemode) mode to [`RelativeSource`](/dotnet/desktop/wpf/advanced/relativesource-markupextension) data binding in WPF.

Here is an example of how this can be used:

```xaml
<Button
    ui:FrameworkElementExtensions.AncestorType="Grid"
    Visibility="{Binding (ui:FrameworkElementExtensions.Ancestor).Visibility,RelativeSource={RelativeSource Self}}"/>
```

## Cursor

The `Cursor` attached property enables you to easily change the mouse cursor over specific Framework elements. Values of this property are values from the [`CoreCursorType`](/uwp/api/windows.ui.core.corecursortype) type.

Here is how you can easily set a custom cursor type for a target `FrameworkElement` instance:

```xaml
<Page
    x:Class="Microsoft.Toolkit.Uwp.SampleApp.SamplePages.MouseCursorPage"
    xmlns="https://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="https://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:ui="using:Microsoft.Toolkit.Uwp.UI">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Border
            ui:FrameworkElementExtensions.Cursor="Hand"
            Width="220" Height="120" Background="DeepSkyBlue"
            HorizontalAlignment="Center" VerticalAlignment="Center"/>
    </Grid>
</Page>
```

> [!NOTE]
> Even though Microsoft recommends in [UWP Design guidelines](/windows/apps/design/input/mouse-interactions#cursors) hover effects instead of custom cursors over interactive elements, custom cursors can be useful in some specific scenarios.

> [!WARNING]
> Because the UWP framework does not support metadata on attached properties, specifically the [`FrameworkPropertyMetadata.Inherits`](/dotnet/api/system.windows.frameworkpropertymetadata.-ctor#System_Windows_FrameworkPropertyMetadata__ctor_System_Object_System_Windows_FrameworkPropertyMetadataOptions_System_Windows_PropertyChangedCallback_System_Windows_CoerceValueCallback_) flag, the `Cursor` property might not work properly in some very specific XAML layout scenarios when combining nested `FrameworkElement`-s with different `CoreCursorType` values set on them.

## CanDragElement

The `CanDragElement` attached property enables repositions the element
in response to mouse drag gestures on the element.

Here is how you can easily set a this for a target `FrameworkElement` instance:
```xaml
<Page
    x:Class="Microsoft.Toolkit.Uwp.SampleApp.SamplePages.CanDragElementPage"
    xmlns="https://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="https://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:ui="using:Microsoft.Toolkit.Uwp.UI"
    >
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Border
            ui:FrameworkElementExtensions.CanDragElement="True"
            ui:FrameworkElementExtensions.ConstrainDragToParentBounds="True"
            Width="200"
            Height="200"
            Background="DeepSkyBlue"
            HorizontalAlignment="Center"
            VerticalAlignment="Center"
            />
    </Grid>
</Page>
```

## ConstrainDragToParentBounds

The `ConstrainDragToParentBounds` attached property allows you to constrain the dragged element
to stay within the bounds of its parent container.  
Not necessary. The default value is false.

## DragX

The `DragX` attached property allows you get/set the X position of the dragged element,
relative to the left of the root element.

## DragY

The `DragY` attached property allows you get/set the Y position of the dragged element,
relative to the top of the root element.

## Examples

You can find more examples in the [unit tests](https://github.com/windows-toolkit/WindowsCommunityToolkit/tree/rel/7.1.0/UnitTests).
