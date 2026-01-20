# Graphics Interactive Package Plan

This document outlines the plan for the `graphics-interactive` package, providing interactive graphics rendering primarily for the web-based calculator (webcalc). This package handles canvas/WebGL rendering, user interaction, dynamic updates, and real-time manipulation of mathematical graphics.

## Package Structure

```
packages/graphics-interactive/
├── src/
│   ├── index.js              # Main exports and registration
│   ├── ratmath-module.js     # VariableManager module integration
│   ├── renderer.js           # Canvas/WebGL rendering engine
│   ├── interaction.js        # Mouse/touch event handling
│   ├── draggable.js          # Draggable points and objects
│   ├── animation.js          # Animation and transitions
│   ├── sliders.js            # Parameter slider controls
│   ├── zoom-pan.js           # Viewport navigation
│   ├── selection.js          # Object selection and highlighting
│   └── reactive.js           # Reactive updates (dependency tracking)
├── components/               # Web components (if using custom elements)
│   ├── graph-canvas.js       # Main graph canvas component
│   ├── slider-control.js     # Slider UI component
│   └── toolbar.js            # Graph toolbar component
├── help/
│   ├── interactive.txt       # Main package help
│   └── controls.txt          # User controls help
├── tests/
│   ├── renderer.test.js
│   ├── interaction.test.js
│   └── draggable.test.js
└── package.json
```

---

## Philosophy

Graphics-interactive focuses on **real-time user interaction**:
- Renders graphics-core objects to HTML Canvas or WebGL
- Provides drag-and-drop manipulation of geometric objects
- Supports parameter sliders for exploring function families
- Handles zoom, pan, and viewport navigation
- Maintains reactive updates when dependencies change
- Optimized for webcalc embedded graphing

---

## Category 1: Rendering Engine

### Canvas Rendering

| Function | Signature | Description |
|----------|-----------|-------------|
| `CreateCanvas` | `CreateCanvas(container, width, height)` | Create canvas element |
| `RenderFigure` | `RenderFigure(canvas, figure)` | Render graphics-core figure |
| `RenderObject` | `RenderObject(ctx, obj, viewport)` | Render single object |
| `ClearCanvas` | `ClearCanvas(canvas)` | Clear canvas |
| `ResizeCanvas` | `ResizeCanvas(canvas, width, height)` | Resize and re-render |

### Rendering Pipeline

```
1. Receive graphics-core Figure
2. Set up canvas context with viewport transform
3. Render layers back-to-front
4. Apply styles (stroke, fill, etc.)
5. Render labels and annotations
6. Add interactive handles if enabled
```

### Render Options

| Option | Description |
|--------|-------------|
| `antialiasing` | Enable anti-aliasing (default: true) |
| `pixelRatio` | Device pixel ratio for retina (default: auto) |
| `backgroundColor` | Canvas background color |
| `showGrid` | Auto-show grid (default: true) |
| `showAxes` | Auto-show axes (default: true) |

---

## Category 2: User Interaction

### Event Handling

| Function | Signature | Description |
|----------|-----------|-------------|
| `EnableInteraction` | `EnableInteraction(canvas)` | Enable mouse/touch events |
| `DisableInteraction` | `DisableInteraction(canvas)` | Disable events |
| `OnClick` | `OnClick(canvas, handler)` | Click event handler |
| `OnHover` | `OnHover(canvas, handler)` | Hover event handler |
| `OnDrag` | `OnDrag(canvas, handler)` | Drag event handler |
| `HitTest` | `HitTest(canvas, x, y)` | Find object at screen position |

### Coordinate Conversion

| Function | Signature | Description |
|----------|-----------|-------------|
| `ScreenToMath` | `ScreenToMath(canvas, sx, sy)` | Screen → math coordinates |
| `MathToScreen` | `MathToScreen(canvas, mx, my)` | Math → screen coordinates |
| `GetMouseMath` | `GetMouseMath(event, canvas)` | Get math coords from mouse event |

---

## Category 3: Draggable Objects

### Making Objects Draggable

| Function | Signature | Description |
|----------|-----------|-------------|
| `Draggable` | `Draggable(point, opts?)` | Make point draggable |
| `DraggableConstrained` | `DraggableConstrained(point, constraint)` | Constrained dragging |
| `DragHandle` | `DragHandle(obj, property)` | Add drag handle to modify property |
| `OnDragUpdate` | `OnDragUpdate(obj, callback)` | Callback when dragged |

### Constraint Types

```
# Constrain to line
DraggableConstrained(P, {type: "line", line: L})

# Constrain to circle
DraggableConstrained(P, {type: "circle", circle: C})

# Constrain to function curve
DraggableConstrained(P, {type: "curve", func: f})

# Constrain to grid
DraggableConstrained(P, {type: "grid", snap: 0.5})

# Constrain to region
DraggableConstrained(P, {type: "region", xmin: -5, xmax: 5, ymin: -5, ymax: 5})
```

### Drag Feedback

| Option | Description |
|--------|-------------|
| `showCoords` | Display coordinates while dragging |
| `ghostTrail` | Show trail of previous positions |
| `snapIndicator` | Visual feedback when snapping |
| `handleStyle` | Style for drag handles |

---

## Category 4: Parameter Sliders

### Slider Creation

| Function | Signature | Description |
|----------|-----------|-------------|
| `Slider` | `Slider(name, min, max, initial, opts?)` | Create parameter slider |
| `SliderInt` | `SliderInt(name, min, max, initial)` | Integer-only slider |
| `SliderDiscrete` | `SliderDiscrete(name, values)` | Discrete value slider |
| `SliderGroup` | `SliderGroup(sliders...)` | Group of sliders |

### Slider Options

```
Slider("a", -5, 5, 1, {
  step: 0.1,              # Step size
  label: "Parameter a",   # Display label
  showValue: 1,           # Show current value
  animate: 0,             # Auto-animate
  animateSpeed: 1,        # Animation speed
  onChange: callback      # Value change callback
})
```

### Slider Integration

| Function | Signature | Description |
|----------|-----------|-------------|
| `BindSlider` | `BindSlider(slider, variable)` | Bind slider to variable |
| `SliderValue` | `SliderValue(slider)` | Get current value |
| `SetSliderValue` | `SetSliderValue(slider, value)` | Set value programmatically |
| `AnimateSlider` | `AnimateSlider(slider, speed)` | Start animation |
| `StopAnimation` | `StopAnimation(slider)` | Stop animation |

---

## Category 5: Zoom and Pan

### Viewport Navigation

| Function | Signature | Description |
|----------|-----------|-------------|
| `EnableZoomPan` | `EnableZoomPan(canvas, opts?)` | Enable zoom/pan |
| `ZoomIn` | `ZoomIn(canvas, factor?)` | Zoom in |
| `ZoomOut` | `ZoomOut(canvas, factor?)` | Zoom out |
| `ZoomToFit` | `ZoomToFit(canvas, objects?)` | Fit viewport to objects |
| `ZoomToRect` | `ZoomToRect(canvas, rect)` | Zoom to rectangle |
| `Pan` | `Pan(canvas, dx, dy)` | Pan viewport |
| `ResetView` | `ResetView(canvas)` | Reset to initial viewport |

### Navigation Options

```
EnableZoomPan(canvas, {
  zoomWheel: 1,           # Scroll wheel zooms
  zoomPinch: 1,           # Pinch-to-zoom on touch
  panDrag: 1,             # Drag to pan (when not on object)
  panMiddle: 1,           # Middle-click drag to pan
  minZoom: 0.1,           # Minimum zoom level
  maxZoom: 100,           # Maximum zoom level
  smoothZoom: 1,          # Animated zoom transitions
  preserveAspect: 1       # Keep 1:1 aspect ratio
})
```

---

## Category 6: Selection & Highlighting

### Object Selection

| Function | Signature | Description |
|----------|-----------|-------------|
| `Selectable` | `Selectable(obj)` | Make object selectable |
| `Select` | `Select(canvas, obj)` | Select object |
| `Deselect` | `Deselect(canvas, obj?)` | Deselect object(s) |
| `GetSelected` | `GetSelected(canvas)` | Get selected objects |
| `OnSelect` | `OnSelect(canvas, callback)` | Selection callback |

### Highlighting

| Function | Signature | Description |
|----------|-----------|-------------|
| `Highlight` | `Highlight(obj, style?)` | Highlight object |
| `Unhighlight` | `Unhighlight(obj)` | Remove highlight |
| `HoverHighlight` | `HoverHighlight(canvas, style?)` | Auto-highlight on hover |

---

## Category 7: Animation

### Transitions

| Function | Signature | Description |
|----------|-----------|-------------|
| `Animate` | `Animate(obj, property, to, duration)` | Animate property |
| `AnimatePath` | `AnimatePath(obj, path, duration)` | Animate along path |
| `Transition` | `Transition(from, to, duration, easing?)` | Value transition |
| `Sequence` | `Sequence(anim1, anim2, ...)` | Sequential animations |
| `Parallel` | `Parallel(anim1, anim2, ...)` | Parallel animations |

### Easing Functions

```
"linear", "easeIn", "easeOut", "easeInOut",
"elastic", "bounce", "cubic", "quad"
```

### Continuous Animation

| Function | Signature | Description |
|----------|-----------|-------------|
| `AnimateLoop` | `AnimateLoop(callback, fps?)` | Animation loop |
| `StartAnimation` | `StartAnimation(canvas)` | Start render loop |
| `StopAnimation` | `StopAnimation(canvas)` | Stop render loop |
| `IsAnimating` | `IsAnimating(canvas)` | Check if animating |

---

## Category 8: Reactive Updates

### Dependency Tracking

When draggable points or slider values change, dependent objects automatically update:

```
# Define reactive graph
P := DraggablePoint(1, 1)
Q := DraggablePoint(3, 2)
L := Line(P, Q)           # L depends on P and Q
M := Midpoint(P, Q)       # M depends on P and Q

# When P is dragged, L and M automatically update
```

### Reactive Functions

| Function | Signature | Description |
|----------|-----------|-------------|
| `Reactive` | `Reactive(expr)` | Create reactive expression |
| `Watch` | `Watch(deps, callback)` | Watch for changes |
| `Invalidate` | `Invalidate(obj)` | Mark object as needing update |
| `Update` | `Update(canvas)` | Force update all |

---

## Web Component Integration

### Graph Canvas Component

```html
<ratmath-graph 
  width="600" 
  height="400"
  xmin="-10" xmax="10"
  ymin="-10" ymax="10"
  show-grid="true"
  show-axes="true">
  
  <ratmath-plot function="x^2 - 4" color="blue" />
  <ratmath-point x="2" y="0" draggable="true" label="A" />
  <ratmath-slider name="a" min="-5" max="5" value="1" />
  
</ratmath-graph>
```

### Component API

| Component | Description |
|-----------|-------------|
| `<ratmath-graph>` | Main graph container |
| `<ratmath-plot>` | Function plot |
| `<ratmath-point>` | Point (optionally draggable) |
| `<ratmath-line>` | Line through two points |
| `<ratmath-circle>` | Circle |
| `<ratmath-slider>` | Parameter slider |
| `<ratmath-toolbar>` | Graph toolbar (zoom controls, etc.) |

---

## Toolbar Features

| Button | Action |
|--------|--------|
| 🔍+ | Zoom in |
| 🔍- | Zoom out |
| ⌂ | Reset view |
| ↔ | Fit to content |
| 📷 | Export image |
| ⚙ | Settings |

---

## Implementation Priority

### Phase 1: Basic Rendering
- [ ] Canvas setup and context management
- [ ] Render graphics-core primitives
- [ ] Viewport coordinate transforms
- [ ] Basic styling

### Phase 2: Zoom & Pan
- [ ] Scroll wheel zoom
- [ ] Drag-to-pan
- [ ] Zoom to fit
- [ ] Touch gestures

### Phase 3: Interaction
- [ ] Hit testing
- [ ] Click and hover events
- [ ] Coordinate display

### Phase 4: Draggable Points
- [ ] Basic point dragging
- [ ] Constrained dragging
- [ ] Drag feedback

### Phase 5: Sliders
- [ ] Basic slider component
- [ ] Slider binding to variables
- [ ] Slider animation

### Phase 6: Reactivity
- [ ] Dependency tracking
- [ ] Automatic updates
- [ ] Performance optimization

### Phase 7: Animation
- [ ] Property transitions
- [ ] Easing functions
- [ ] Animation loops

### Phase 8: Web Components
- [ ] Custom element definitions
- [ ] Declarative API
- [ ] Webcalc integration

---

## Dependencies

- `@ratmath/graphics-core`: Abstract graphics objects
- `@ratmath/geometry`: Geometric computations (for constraints)
- `@ratmath/core`: Rational arithmetic

---

## Open Questions

1. **Canvas vs SVG vs WebGL**: Which rendering technology?
   - Proposed: Canvas for performance, with SVG fallback for export

2. **Framework Integration**: Support React/Vue/Svelte?
   - Proposed: Vanilla JS first, framework wrappers later

3. **Mobile Support**: Touch interaction priority?
   - Proposed: Full touch support from the start

4. **Performance**: How to handle many objects?
   - Proposed: Virtualization, dirty rectangles, WebGL for heavy loads

5. **Accessibility**: Keyboard navigation, screen readers?
   - Proposed: Add ARIA labels, keyboard shortcuts post-MVP
