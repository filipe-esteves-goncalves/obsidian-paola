
#flashcards/Swift/views-animations


What are the distinct phases of the Core Animation rendering pipeline that execute before the GPU composites the layers? 
? 
The pipeline involves four main phases before reaching the out-of-process Render Server: **Layout** (calculating frames and Auto Layout constraints), **Display** (updating layer contents and executing custom `draw(_:)` calls), **Prepare** (decoding images from compressed formats on a background thread), and **Commit** (packaging the layer tree to send to the render server). Heavy layer trees delay the Commit phase and cause dropped frames.

What is offscreen rendering, and why does it negatively impact iOS animation performance?
?
Offscreen rendering occurs when the GPU cannot draw a layer directly to the screen in a single pass and must allocate a secondary offscreen buffer to composite effects—like masking, shadows, or corner radii combined with clipping. Switching contexts between the main screen buffer and offscreen buffers requires significant GPU overhead, which causes frame drops (jank) during fluid 60/120Hz animations.

How can you apply a shadow to a `UIView` or `CALayer` without triggering expensive offscreen rendering? 
? 
By defining the `shadowPath` property explicitly. When the system knows the exact geometry of the shadow via a `CGPath`, it skips the offscreen buffer calculation and draws the shadow directly. Without a `shadowPath`, Core Animation must first render the layer's entire contents offscreen to determine the shadow's shape.

What is the difference between the model layer and the presentation layer in Core Animation? 
?
The **model layer** represents the final target state and properties of the layer you set in code. The **presentation layer** represents the visual state currently displayed on screen at a specific millisecond during an animation. You query `layer.presentation()` to accurately perform hit testing or calculate dynamic intersections while a view is mid-flight.

Why does overriding `draw(_:)` (or `drawRect:`) consume significant memory even if you only draw a simple shape? 
? 
The moment you override `draw(_:)`, the system automatically allocates a backing store (a `CGContext` bitmap) with a memory footprint equal to `width × height × scale factor × 4 bytes`. For a full-screen view on a modern device, this wastes megabytes of RAM. For basic shapes, you should use `CAShapeLayer` or SwiftUI's `Path` to render directly on the GPU without CPU-bound bitmap allocations.

When diagnosing slow `UICollectionView` scrolling, what are three common main-thread bottlenecks that cause frame hitches? 
? 
Slow scrolling usually stems from: **1.** Synchronous image decoding occurring at draw time (fix by pre-decoding off the main thread), **2.** Auto Layout constraint recalculations triggered by ambiguous subviews during cell recycling, and **3.** Heavy synchronous disk or cache reads within `cellForItemAt`.

How do you handle animations that require exact synchronization with the display's refresh rate, such as ProMotion (120Hz)? 
? 
You use `CADisplayLink`, a timer bound to the display's VSync. To support ProMotion, you must configure the `preferredFrameRateRange` property, allowing the system to dynamically scale the refresh rate up to 120 FPS. You must also use `weak` references or an intermediate proxy to avoid retain cycles, as `CADisplayLink` retains its target.

What is the fundamental difference between implicit and explicit animations in iOS? 
? 
**Implicit animations** are automatically triggered by Core Animation when modifying animatable properties (like bounds or opacity) on standalone `CALayer` instances. However, UIKit disables implicit animations on layer properties backed by a `UIView` unless the changes are wrapped in a `UIView.animate` block. **Explicit animations** are created manually (e.g., `CABasicAnimation`) and added directly to the layer using `add(_:forKey:)`.

How does `UIViewPropertyAnimator` differ from standard `UIView.animate`, and when should you use it? 
? 
`UIViewPropertyAnimator` is an object-oriented, stateful animation engine. Unlike standard block animations, it allows you to pause, reverse, scrub linearly using a fraction complete, and dynamically alter timing curves or endpoints mid-flight. It is essential for building fluid, interruptible, and interactive gesture-driven UI transitions.

What are the performance implications of using `GeometryReader` in SwiftUI layouts? 
? 
`GeometryReader` intercepts the normal declarative layout negotiation and forces SwiftUI to continually recalculate frames during updates. Heavy nesting or unnecessary use of `GeometryReader` breaks layout stability, triggers expensive re-renders, and degrades animation performance. It should be isolated strictly to the leaf levels of the view hierarchy where necessary.

How does SwiftUI's child-parent layout negotiation work during a view update pass? 
? 
Layout happens in three continuous steps: **1. Proposal:** The parent offers a size to the child. **2. Sizing:** The child determines its own size based on the proposal and its internal rules (the parent cannot force a child's size). **3. Placement:** The parent takes the child's chosen size and places it within its coordinate space.

How does `matchedGeometryEffect` work under the hood, and how do you avoid layout jumps? 
? 
`matchedGeometryEffect` synchronizes the frame of two different views across a transition by sharing their layout data through a common namespace identifier. To prevent visual jumps or ghosting, ensure size and clipping modifiers (like `.frame` and `.padding`) are placed _before_ the `.matchedGeometryEffect` modifier so the system captures the correct final bounding box.

When applying `.shouldRasterize = true` to a layer, what mandatory property must also be set, and what is the primary pitfall? 
? 
You must set `layer.rasterizationScale = UIScreen.main.scale` to prevent the cached bitmap from looking pixelated on Retina displays. The pitfall: if the rasterized layer or its subviews animate or update frequently, the system is forced to discard and regenerate the offscreen bitmap every frame, causing massive CPU/GPU overhead instead of saving rendering time.

Why do touch events fail on a `UIView` while it is moving during a standard animation, and how do you fix it? 
? 
During standard block animations, hit testing uses the final coordinate values of the model layer immediately, ignoring the visible in-flight presentation layer. To allow interaction, you must pass the `.allowUserInteraction` option to `UIView.animate`. For precise custom hit-testing mid-flight, you must override `hitTest(_:with:)` and evaluate coordinates against `layer.presentation()`.

In SwiftUI, what is the difference between the `.animation()` view modifier and wrapping state changes in `withAnimation`? 
? 
`withAnimation` binds the animation explicitly to the state change transaction, meaning only views dependent on that specific state change animate. The implicit `.animation()` modifier applies to _any_ layout change inside that view tree, which often causes unintended cascading animations across unrelated views. Modern SwiftUI deprecates the generic modifier, requiring `.animation(_:value:)` to explicitly tie it to specific value changes.