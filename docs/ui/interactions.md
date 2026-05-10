---
title: "UI Interactions"
icon: "🖱️"
created: 2026-05-10
updated: 2026-05-10
---

# UI Interactions

## Pointer Events

By default, panels do not receive mouse input. You need to enable `pointer-events` in your stylesheet to make a panel interactable.

```scss
.button {
    pointer-events: all;
}
```

To disable pointer events on a child element (e.g. so clicks pass through to the parent):

```scss
.overlay {
    pointer-events: none;
}
```

## onclick

You can handle click events using the `onclick` attribute in Razor:

```csharp
<root>
    <div class="button" onclick=@OnButtonClick>Click Me</div>
</root>

@code
{
    void OnButtonClick()
    {
        Log.Info( "Button clicked!" );
    }
}
```

Or inline with a lambda:

```csharp
<div class="button" onclick=@(() => Log.Info( "Clicked!" ))>Click Me</div>
```

You can also handle clicks in C# by overriding `OnClick` on a Panel subclass:

```csharp
public class MyButton : Panel
{
    protected override void OnClick( MousePanelEvent e )
    {
        Log.Info( "Clicked!" );
        e.StopPropagation();
    }
}
```

`e.StopPropagation()` prevents the event from bubbling up to parent panels.

## Other Mouse Events

| Attribute / Override | Fires when… |
|---|---|
| `onmouseover` / `OnMouseOver` | Cursor enters the panel |
| `onmouseout` / `OnMouseOut` | Cursor leaves the panel |
| `onmousedown` / `OnMouseDown` | Mouse button is pressed |
| `onmouseup` / `OnMouseUp` | Mouse button is released |
| `onclick` / `OnClick` | Panel is clicked |

```csharp
<div class="card" onmouseover=@OnHover onmouseout=@OnUnhover>Hover Me</div>

@code
{
    void OnHover()   => Log.Info( "Hovered" );
    void OnUnhover() => Log.Info( "Unhovered" );
}
```
