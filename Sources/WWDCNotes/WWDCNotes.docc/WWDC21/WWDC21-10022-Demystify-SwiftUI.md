# Demystify SwiftUI

Peek behind the curtain into the core tenets of SwiftUI philosophy: Identity, Lifetime, and Dependencies. Find out about common patterns, learn the principles that drive the framework, and discover how you can use them to guarantee correctness and performance for your app.

@Metadata {
   @TitleHeading("WWDC21")
   @PageKind(sampleCode)
   @CallToAction(url: "https://developer.apple.com/wwdc21/10022", purpose: link, label: "Watch Video (40 min)")

   @Contributors {
      @GitHubUser(zntfdr)
   }
}



Three key aspects on how SwiftUI sees your views:

- Identity - how SwiftUI recognizes view to be the same or distinct, across multiple updates of your app

- Lifetime - how SwiftUI tracks the existence of views and data over time

- Dependencies - how SwiftUI understands when your views need to be updated and why

Together, these three concepts inform how SwiftUI decides what needs to change, how, and when, resulting in the dynamic user interface you see onscreen.

## View identity

Views that share the same identity represent different states of the same conceptual UI element.

Let's take two views from two different screen states:

- are those views the same view in a different state?
- or are those two completely distinct views?

The answer changes how our interface transitions from one state to another:

- if they're the same view, SwiftUI will apply the new state to that view, for example by moving that view from one location to the other
- if they're distinct, that means a change of state will make them transition independently, such as fading in and out

Connecting views across different states is important, because that's how SwiftUI understands how to transition between them.

SwiftUI types of identity:

1. Explicit identity - defined by using custom or data-driven identifiers
2. Structural identity - defined by the view type and position in the view hierarchy

### Explicit identity

- `ForEach`'s (and `List`'s)  `id` parameter is a form of explicit identity:

```swift
ForEach(..., id: \.someProperty) { ... }
```

- the `id(_:)` modifier also provides a form of explicit identity:

```swift
Header().id(headerIdentifier)
```

We don't have to explicitly identify every view, just the ones we need to refer to elsewhere in the code, for example in a `ScrollViewReader`.

### Structural identity

Every view has an identity, even if it's not explicit:  
SwiftUI uses the structure of your view hierarchy to generate implicit identities for your views so you don't have to.

Example:

```swift
var body: some View {
  if condition {
      viewA
    } else {
      viewB
    }
  }
}
```

- `viewA` is the "true" view
- `viewB` is the "false" view

This works only if SwiftUI can statically guarantee that these views stay where they are and never swap places:  
SwiftUI accomplishes this by looking at the type structure of your view hierarchy.

`some View` is a placeholder that represents our view static composite type:  
for the example above, the real type is `_ConditionalContent<ViewAType, ViewBType>`, which is what SwiftUI uses to identify the "true" and "false" view.

From SwiftUI point of view, the `if-else` statement represents two different views with distinct identities.

To make those views the same identity, we'd need to apply the condition in other ways, for example via an inert view modifier:

```swift
var body: some View {
  sharedView
    .background(condition ? Color.green : Color.green)
}
```

By default, try to preserve identity and provide more fluid transitions:  
this also helps preserve your view's lifetime and state.

`AnyView` makes it hard for SwiftUI to optimize and identify each view, avoid it as much as possible, use `@ViewBuilder` if needed.

## How view identity is related to their lifetime and state

During its lifetime, a view can change its state, view identity connects these different state values as a single entity.

E.g.:

```swift 
var body: some View {
  ViewA(value: 1)
}
```

later on we change the value to 2

```swift
var body: some View {
  ViewA(value: 2)
}
```

When we change that value from `1` to `2`, SwiftUI will keep around a copy of the old view value to perform a comparison and know if the view has changed. After that, the old value is destroyed.

The view value is different from the view identity:

- View values are ephemeral and you should not rely on their lifetime

- What you can control is their identity

Over time, driven by updates, new values for the view are created (like when we moved from `1` to `2` above), but from SwiftUI's perspective, these represent the same view.


- lifetime of a view: duration of the identity associated with that view

The state of a view is associated with its lifetime: `@State` and `@StateObject` are the persistent storage associated with your view's identity.

At the beginning of a view's identity, when it's created for the first time, SwiftUI is going to allocate storage in memory for State and StateObject using their initial values.

## How SwiftUI updates your view

All view properties are dependencies of a view

```swift
struct MyView: View {
  @Binding var isOn = false // this is a dependency
  var name: String          // this is a dependency

  var body: some View {
    ...
  }
}
```

- A dependency is just an input to the view.
- When a dependency changes, the view is **required** to produce a new body

Each view has its own dependencies, multiple views can depend on the same dependency source:

- SwiftUI maintains a dependency graph of all these dependencies.
- when any dependency changes, SwiftUI will call each view's body, producing a new body value, for all views depending on that dependency
