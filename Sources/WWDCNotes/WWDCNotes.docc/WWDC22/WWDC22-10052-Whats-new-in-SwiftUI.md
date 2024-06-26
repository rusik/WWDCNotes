# What's new in SwiftUI

It's a SwiftUI party — and you're invited! Join us as we share the latest updates and a glimpse into the future of UI framework design. Discover deep levels of customization, advanced techniques for layout, elegant strategies for sharing, and rock-solid structural approaches for designing an app top-to-bottom in SwiftUI. We’ll also have some celebratory fun as we play with the latest graphical effects and explore APIs.

@Metadata {
   @TitleHeading("WWDC22")
   @PageKind(sampleCode)
   @CallToAction(url: "https://developer.apple.com/wwdc22/10052", purpose: link, label: "Watch Video (33 min)")

   @Contributors {
      @GitHubUser(Jeehut)
      @GitHubUser(zntfdr)
   }
}



## Swift Charts

- `Chart` view that takes data, `BarMark`, `LineMark` and other views inside
- `.foregroundStyle(by: ...)` helps declaratively specify details
- `.symbol(by: ...)` to declaratively specify details in chart
- `.annotation` supported, e.g. for marks
- supports dark mode automatically (like any SwiftUI view)
- Smart defaults for legends etc.

## Navigation and windows

### Stacks

- `NavigationStack` a new container view for push/pop style navigation
- wraps a root content view
- works with `NavigationLink` and `.navigationTitle`
- New data-driven APIs: `.navigationDestination(for: ...)`
- `NavigationLink` can now take a value that represents a destination
- now also supported on macOS

### Split Views

- `NavigationSplitView` for multi-column navigation
- can declare 2- or 3-column layouts
- accepts a `detail` view
- Split view adapts on resize automatically
- works with stack together for complex navigation structures

### Scene

### Window

New [`Window`][window] type to declare a single, unique window for your app

- appears automatically in `Window` menu bar
- add a [`.keyboardShortcut(_:)`][keyboardshortcut(_:)] modifier to quickly open that window via keyboard shortcut
- new [`openWindow`][openwindow] environment value for programmatic trigger

```swift
@main
struct PartyPlanner: App {
  var body: some Scene {
    WindowGroup("Party Planner") {
      PartyPlannerHome()
    }

    // 👇🏻 🆕
    Window("Party Budget", id: "budget") {
      Text("Budget View")
    }
    .keyboardShortcut("0")
  }
}
```

### Window customizations

New windows customizations via modifiers:

- [`defaultPosition(_:)`][defaultposition(_:)] for default positions (on window open)
- [`defaultSize(_:)`][defaultsize(_:)] for setting the window default size
- [`windowResizability(_:)`][windowResizability(_:)] for specifying window resizability preference
- [`presentationDetents(_:)`][presentationdetents(_:)] allows controls for resizable sheets, e.g. with two different sizes

### [`MenuBarExtra`][MenuBarExtra]

Menu bar extras now entirely in SwiftUI via [`MenuBarExtra`][MenuBarExtra]

- always shown in Menu Bar while app is running
- also possible to just provide a `MenuBarExtra` in an app as root scene

```swift
@main
struct PartyPlanner: App {
  var body: some Scene {
    Window("Party Budget", id: "budget") {
      Text("Budget View")
    }

    MenuBarExtra("Bulletin Board", systemImage: "quote.bubble") {
      BulletinBoard()
    }
    .menuBarExtraStyle(.window)
  }
}
```

## Advanced controls

### Forms

- new settings app design in macOS uses new navigation SwiftUI views
- New `.formStyle(.grouped)` for forms to improve forms on macOS
- `LabeledContent` used to build new controls or just read-only information
    - allows selection for simple data

- SwiftUI is now smarter in formatting text in Forms
- New popup menu pickers in iOS (macOS inspired dropdowns)

### Controls

- `TextField` can now have an expanding `axis` specified (e.g. `.vertical`)
  - `.lineLimit(5...10)` range helps expanding with min/max lines

- New [`MultiDatePicker`][MultiDatePicker] 
  - supports non-contiguous date selection

```swift
struct ContentView: View {
  @State private var activityDates: Set<DateComponents> = [
    DateComponents(calendar: .current, year: 2022, month: 6, day: 6),
    DateComponents(calendar: .current, year: 2022, month: 6, day: 9),
    DateComponents(calendar: .current, year: 2022, month: 6, day: 10)
  ]

  var body: some View {
    MultiDatePicker("Activities Dates", selection: $activityDates)
  }
}
```

- Use `DisclosureGroup` to combine multiple toggles into one:

```swift
DisclosureGroup {
  HStack {
    Toggle("Balloons 🎈", isOn: $includeBalloons)
    Spacer()
    decorationThemes[.balloon].map { $0.swatch }
  }
  .tag(Decoration.balloon)

  HStack {
    Toggle("Confetti 🎊", isOn: $includeConfetti)
    Spacer()
    decorationThemes[.confetti].map { $0.swatch }
  }
  .tag(Decoration.confetti)

  HStack {
    Toggle("Inflatables 🪅", isOn: $includeInflatables)
    Spacer()
    decorationThemes[.inflatables].map { $0.swatch }
  }
  .tag(Decoration.inflatables)

  HStack {
    Toggle("Party Horns 🥳", isOn: $includeBlowers)
    Spacer()
    decorationThemes[.noisemakers].map { $0.swatch }
  }
  .tag(Decoration.noisemakers)
} label: {
  Toggle("All Decorations", isOn: [
    $includeBalloons, $includeConfetti,
    $includeInflatables, $includeBlowers
  ])
  .tag(Decoration.all)
}
```

- Use `Picker` as an alternative:

```swift
Picker("Decoration theme", selection: themes) {
  Text("Blue").tag(Theme.blue)
  Text("Black").tag(Theme.black)
  Text("Gold").tag(Theme.gold)
  Text("White").tag(Theme.white)
}
```

- Button styles will now apply to any control that supports a button-like appearance, including toggles, menus, and pickers
- `Stepper` with `format` parameter, now also available on watchOS
- `.accessibilityQuickAction` with button for easy accessibility

### Tables

- `Table` now supported on iOS and iPadOS
- Will render also on iPhone with a primary column
- new `.contextMenu` modifier with multiple selections possible
- allows to define advanced context menus
- new `.toolbar` in iPadOS, customizable by specifying explicit `id`
- placement: `.secondaryAction` to be used for toolbar items

### Search

Search fields now support (via new [search modifiers][sm]):

- tokenized inputs and suggestions
- search scopes (filters) 

```swift
NavigationStack {
  ...
    .searchable(text: $query, tokens: $tokens, scope: $scope) { token in
      Label(token.query, systemImage: token.systemImage)
    } scopes: {
      Text("In Person").tag(AttendanceScope.inPerson)
      Text("Online").tag(AttendanceScope.online)
    } suggestions: {
      suggestions
  }
}
```

## Sharing

### Photos Picker

- `PhotosPicker` view anywhere in app, presents picker UI
- takes a binding to a selected `PhotosPickerItem`
- filtering type of content and more customizable

### Sharing

- watchOS supports `ShareLink` view, provide content and preview
- adapt to the `.contextMenu` they’re applied to

### Transferable

- Makes drag & drop easy
- `.dropDestination` with `payloadType`, comes with closure for received data
- many standard types conform to `Transferable`
- Implement in custom types by conforming to `representation: some TransferRepresentation`

## Graphics and layout

### Shape Styles

- new [`gradient: AnyGradient`][gradient] property on `Color`, to apply a subtle gradient derived from the color

```swift
...
  .backgroundStyle(.blue.gradient)
```

- new [`shadow(_:)`][shadow] modifier for [`ShapeStyle`][ss]

```swift
...
  .foregroundStyle(.white.shadow(.drop(radius: 1, y: 1.5)))
```

### Text

`Text` can now be beautifully animated between weights, styles, and layouts, just use `withAnimation`

### Layout

#### Grid

New [`Grid`][grid] container.

- arranges views in a two-dimensional grid
- will measure its subviews up front to enable cells that span multiple columns and enable automatic alignments across rows and columns

- Use [`Grid`][grid], [`GridRow`][GridRow] and associated modifiers to specify your layout

```swift
struct IconGrid: View {
  var body: some View {
    Grid(horizontalSpacing: 16, verticalSpacing: 16) {
      ForEach(0..<3) { i in
        GridRow {
          ForEach(0..<5) { j in
            icons[i * 5 + j]
          }
        }
      }
    }
    .background(.black.opacity(0.8))
  }
}
```

- built with composition in mind

#### Layout

For true custom SwiftUI layout à la `UICollectionViewLayout`.

- new [`Layout`][Layout] protocol for even more customization

Guest table party example:

```swift
// MARK: Custom Table Layout

private let tableSize = CGSize(width: 130, height: 90)
private let guestSize = CGSize(width: 40, height: 40)

/// Which of 6 tables this view represents
private struct TableViewLayoutKey: LayoutValueKey {
  static let defaultValue: Int? = nil
}

extension View {
  fileprivate func tableViewLayoutKey(_ value: Int) -> some View  {
    return layoutValue(key: TableViewLayoutKey.self, value: value)
  }
}

/// Which of 36 guests this view represents
private struct GuestViewLayoutKey: LayoutValueKey {
  static let defaultValue: Int? = 0
}

extension View {

  /// Guests 1 - 36
  fileprivate func guestViewLayoutKey(_ value: Int) -> some View  {
    return layoutValue(key: GuestViewLayoutKey.self, value: value)
  }
}

let initials = [
"Ju", "As", "Ma", "As", "Ly", "Ga", "Ni", "Ar", "Ca", 
"Do", "Je", "Ca", "Em", "Ma", "Ze", "Jo", "Da", "Sh", 
"Sa", "Pl", "Pa", "Sc", "Ma", "Je", "Li", "Ma", "Ta", 
"Je", "Cu", "Lu", "Ra", "Na", "Sa", "Pa", "Le", "Pi",
]

struct SeatingChartView: View {

  /// If true, the guests will be positioned in "pods" of tables. No table will touch another table. Otherwise
  /// the guests will side in two longs rows.
  @State private var usePods = true

  var body: some View {
    ZStack(alignment: .bottomTrailing) {
      GeometryReader { proxy in
        SeatingLayout(usePods: usePods).callAsFunction {
          TableView(tableNumber: 1)
          TableView(tableNumber: 2)
          TableView(tableNumber: 3)
          TableView(tableNumber: 4)
          TableView(tableNumber: 5)
          TableView(tableNumber: 6)
          ForEach(1..<37) { i in
            SeatedGuestOption2(guestNumber: i - 1)
          }
        }
        .animation(.default, value: proxy.size)
      }
      .background(.black.opacity(0.13))
      Picker("Arrangement", selection: $usePods.animation()) {
        Text("Pods").tag(true)
        Text("Rows  ").tag(false)
      }
      .fixedSize()
      .pickerStyle(.segmented)
      .padding()
    }
  }
}

struct TableView: View {
  let tableNumber: Int

  var body: some View {
    ZStack(alignment: .bottomTrailing) {
      HStack {
        Image(systemName: "table.furniture")
          .background(.quaternary.shadow(.inner(radius: 1, y: 1.5)),
                in: Circle().inset(by: -8))
          .padding(5)
        Text("Table \(tableNumber)")
      }
      .foregroundStyle(.secondary)
      .padding(8)
      .frame(width: tableSize.width, height: tableSize.height)
      #if os(macOS) || os(iOS)
      .background(.regularMaterial.shadow(.drop(radius: 1, y: 1.5)),
            in: RoundedRectangle(cornerRadius: 12, style: .continuous))
      #endif
    }

    .tableViewLayoutKey(tableNumber)
  }
}

private let colors: [Color] = [
  .red, .orange, .yellow, .green, .mint, .teal, .cyan, .blue,
  .indigo, .purple, .pink, .gray, .black, .white, .brown,
  .red, .orange, .yellow, .green, .mint, .teal, .cyan, .blue,
  .indigo, .purple, .pink, .gray, .black, .white, .brown, .red,
  .orange, .yellow, .green, .mint, .teal, .cyan
]

struct SeatedGuest: View {

  let guestNumber: Int

  var body: some View {
    Image(systemName: "person")
      .resizable()
      .aspectRatio(contentMode: .fit)
      .padding(9)
      .background(in: Circle())
      .backgroundStyle(
        colors[guestNumber].gradient
      )
      .foregroundStyle(guestNumber == 13 ? .black : .white)
      .frame(width: 40, height: 40)
      .guestViewLayoutKey(guestNumber + 1)
  }
}

struct SeatedGuestOption2: View {
  let guestNumber: Int

  var body: some View {
    Circle()
      .stroke(colors[guestNumber], style: StrokeStyle(lineWidth: 3))
      .background(.white.gradient, in: Circle())
      .frame(width: guestSize.width, height: guestSize.height)
      .guestViewLayoutKey(guestNumber + 1)
      .overlay {
        Text(initials[guestNumber])
          .foregroundColor(.secondary)
          .font(.callout)

      }
  }
}

struct SeatingChartView_Previews: PreviewProvider {
  static var previews: some View {
    SeatingChartView()
      .frame(width: 600, height: 600)
  }
}

struct SeatingLayout: Layout {

  /// If true, the guests will be positioned in "pods" of tables. No table will touch another table. Otherwise
  /// the guests will side in two longs rows.
  let usePods: Bool

  struct Cache {
    ///  The width proposed to the view. We assume a certain height, otherwise, overlapping views
    var width: CGFloat?
  }

  func sizeThatFits(
    proposal: ProposedViewSize,
    subviews: LayoutSubviews,
    cache: inout Cache
  ) -> CGSize {
    cache.width = proposal.width
    return proposal.replacingUnspecifiedDimensions()
  }

  func makeCache(subviews: Subviews) -> Cache { Cache() }

  func placeSubviews(in bounds: CGRect,
             proposal: ProposedViewSize,
             subviews: Subviews,
             cache: inout Cache) {
    guard let width = cache.width else { return }

    /// Helper function: Place 6 guests around all edges of a table.
    func seat(_ guests: [LayoutSubview], around table: CGRect) {
      guests[0].place(
        at: .init(
          x: table.origin.x + 3 - guestSize.width,
          y: table.origin.y + (table.height / 2.0) - (guestSize.height / 2.0)),
        proposal: .infinity)
      guests[1].place(
        at: .init(
          x: table.origin.x + (table.width / 4.0) - guestSize.width / 2.0,
          y: table.origin.y + 5 - guestSize.height),
        proposal: .infinity)
      guests[2].place(
        at: .init(
          x: table.origin.x + table.width * 0.75 - guestSize.width / 2.0,
          y: table.origin.y + 5 - guestSize.height),
        proposal: .infinity)
      guests[3].place(
        at: .init(
          x: table.maxX - 5,
          y: table.origin.y + (table.height / 2.0) - (guestSize.height / 2.0)),
        proposal: .infinity)
      guests[4].place(
        at: .init(
          x: table.origin.x + table.width * 0.75 - guestSize.width / 2.0,
          y: table.maxY - 5),
        proposal: .infinity)
      guests[5].place(
        at: .init(
          x: table.origin.x + (table.width / 4.0) - guestSize.width / 2.0,
          y: table.maxY - 5),
        proposal: .infinity)
    }

    /// Helper function: Place 6 guests, dining hall style (not along the shorter sides of a table)
    func seat(_ guests: [LayoutSubview], along table: CGRect) {
      guests[0].place(
        at: .init(
          x: table.minX + tableSize.width / 3 - guestSize.width - 4,
          y: table.origin.y + 5 - guestSize.height),
        proposal: .infinity)
      guests[1].place(
        at: .init(
          x: table.minX + tableSize.width * 2/3 - guestSize.width - 4,
          y: table.origin.y + 5 - guestSize.height),
        proposal: .infinity)
      guests[2].place(
        at: .init(
          x: table.minX + tableSize.width - guestSize.width - 4,
          y: table.origin.y + 5 - guestSize.height),
        proposal: .infinity)
      guests[3].place(
        at: .init(
          x: table.minX + tableSize.width / 3 - guestSize.width - 4,
          y: table.maxY - 5),
        proposal: .infinity)
      guests[4].place(
        at: .init(
          x: table.minX + tableSize.width * 2/3 - guestSize.width - 4,
          y: table.maxY - 5),
        proposal: .infinity)
      guests[5].place(
        at: .init(
          x: table.minX + tableSize.width - guestSize.width - 4,
          y: table.maxY - 5),
        proposal: .infinity)
    }

    // Get tables
    let table1 = subviews.first(where: { $0[TableViewLayoutKey.self] == 1 })!
    let table2 = subviews.first(where: { $0[TableViewLayoutKey.self] == 2 })!
    let table3 = subviews.first(where: { $0[TableViewLayoutKey.self] == 3 })!
    let table4 = subviews.first(where: { $0[TableViewLayoutKey.self] == 4 })!
    let table5 = subviews.first(where: { $0[TableViewLayoutKey.self] == 5 })!
    let table6 = subviews.first(where: { $0[TableViewLayoutKey.self] == 6 })!

    // Get guests
    let table1Guests = subviews
      .filter {
        guard let guestNumber = $0[GuestViewLayoutKey.self] else { return false }
        return guestNumber >= 1 && guestNumber <= 6
      }
    let table2Guests = subviews
      .filter {
        guard let guestNumber = $0[GuestViewLayoutKey.self] else { return false }
        return guestNumber >= 7 && guestNumber <= 12
      }
    let table3Guests = subviews
      .filter {
        guard let guestNumber = $0[GuestViewLayoutKey.self] else { return false }
        return guestNumber >= 13 && guestNumber <= 18
      }
    let table4Guests = subviews
      .filter {
        guard let guestNumber = $0[GuestViewLayoutKey.self] else { return false }
        return guestNumber >= 19 && guestNumber <= 24
      }
    let table5Guests = subviews
      .filter {
        guard let guestNumber = $0[GuestViewLayoutKey.self] else { return false }
        return guestNumber >= 25 && guestNumber <= 30
      }
    let table6Guests = subviews
      .filter {
        guard let guestNumber = $0[GuestViewLayoutKey.self] else { return false }
        return guestNumber >= 31 && guestNumber <= 36
      }

    if usePods {
      let table1Origin = CGPoint(x: 60, y: 120)
      let table2Origin = CGPoint(x: 200, y: 280)
      let table3Origin = CGPoint(x: 50, y: 450)
      let table4Origin = CGPoint(x: 300, y: 120)
      let table5Origin = CGPoint(x: 440, y: 280)
      let table6Origin = CGPoint(x: 290, y: 450)
      table1.place(at: table1Origin, proposal: .infinity)
      table2.place(at: table2Origin, proposal: .infinity)
      table3.place(at: table3Origin, proposal: .infinity)
      table4.place(at: table4Origin, proposal: .infinity)
      table5.place(at: table5Origin, proposal: .infinity)
      table6.place(at: table6Origin, proposal: .infinity)
      seat(table1Guests, around: CGRect(origin: table1Origin, size: tableSize))
      seat(table2Guests, around: CGRect(origin: table2Origin , size: tableSize))
      seat(table3Guests, around: CGRect(origin: table3Origin, size: tableSize))
      seat(table4Guests, around: CGRect(origin: table4Origin, size: tableSize))
      seat(table5Guests, around: CGRect(origin: table5Origin , size: tableSize))
      seat(table6Guests, around: CGRect(origin: table6Origin, size: tableSize))
    } else {
      let table1Origin = CGPoint(x: width / 2.0 - 6 - tableSize.width * 1.5, y: 130)
      let table2Origin = CGPoint(x: table1Origin.x + tableSize.width + 6, y: 130)
      let table3Origin = CGPoint(x: table2Origin.x + tableSize.width + 6, y: 130)
      let table4Origin = CGPoint(x: width / 2.0 - 6 - tableSize.width * 1.5, y: 360)
      let table5Origin = CGPoint(x: table1Origin.x + tableSize.width + 6, y: 360)
      let table6Origin = CGPoint(x: table2Origin.x + tableSize.width + 6, y: 360)
      table1.place(at: table1Origin, proposal: .infinity)
      table2.place(at: table2Origin, proposal: .infinity)
      table3.place(at: table3Origin, proposal: .infinity)
      table4.place(at: table4Origin, proposal: .infinity)
      table5.place(at: table5Origin, proposal: .infinity)
      table6.place(at: table6Origin, proposal: .infinity)
      seat(table1Guests, along: CGRect(origin: table1Origin, size: tableSize))
      seat(table2Guests, along: CGRect(origin: table2Origin , size: tableSize))
      seat(table3Guests, along: CGRect(origin: table3Origin, size: tableSize))
      seat(table4Guests, along: CGRect(origin: table4Origin, size: tableSize))
      seat(table5Guests, along: CGRect(origin: table5Origin , size: tableSize))
      seat(table6Guests, along: CGRect(origin: table6Origin, size: tableSize))
    }
  }
}
```

- new [`AnyLayout`][anylayout] type to switch between layouts easily

Example switching between two layouts:

```swift
import SwiftUI
import GameplayKit
import Combine

@main
struct InvitationApp: App {
  var body: some Scene {
    WindowGroup {
      PolygonDesignerView()
        .environmentObject(PolygonModel())
      #if os(iOS)
        .statusBar(hidden: true)
      #endif
        .edgesIgnoringSafeArea(.all)
    }
  }
}

// MARK: Views

/// A view that arranges polygons in a grid, or a custom, scattered layout.
private struct DynamicPolygonView: View {
  @EnvironmentObject var model: PolygonModel
  @Binding var cycleLayouts: Bool

  private var sideLength: Int {
    Int(CGFloat(model.polygonGeometries.count).squareRoot())
  }

  /// Timer whose ticking dictates how often to regenerate and animate-to a new scattered layout.
  /// - Note: The layout will only transition if `cycleLayouts` is `true`.
  private let layoutChangingTimer = Timer
    .publish(every: 1.2, on: .current, in: .default).autoconnect()

  /// Animation used to transition layouts
  private let animation = Animation.easeInOut(duration: 1.3)

  /// Timer that ticks at 128 beats per minute, matching the beat of the song in the WWDC session.
  let musicBeatTimer = Timer
    .publish(every: 0.234378662, tolerance: 0,  on: .main, in: .default)

  @State private var musicBeatTimerCancellable: (any Cancellable)? = nil

  /// Whether or not the font should be rendered heavy.
  @State private var heavy: Bool = false

  @State private var scatteredLayout = newScatteredLayout(
    Date(timeIntervalSince1970: 0)
  )

  /// By providing a seed value, the `ScatteredLayout` struct will know when to bust its cache and
  /// generate new layout data.
  private static func newScatteredLayout(_ seed: Date) -> ScatteredLayout {
    ScatteredLayout(count: PolygonModel.total,
            seed: seed.timeIntervalSinceReferenceDate,
            textAvoidanceRect: CGRect(
              x: 152,
              y: 245,
              width: 220,
              height: 40)
    )
  }

  var body: some View {
    let layout = model.usesGridLayout
    ? AnyLayout(Grid(alignment: .center,
             horizontalSpacing: 0,
             verticalSpacing: 0))
    : AnyLayout(scatteredLayout)

    ZStack(alignment: .center) {
      Label(title:  {
        Text("You're Invited")
      }, icon: { Image(systemName: "party.popper.fill")})
      .font(.system(size:100).weight(heavy ? .black : .thin))
      .onTapGesture {
        musicBeatTimerCancellable = musicBeatTimer.connect()
      }
      .zIndex(-1)

      layout {
        ForEach((0..<sideLength), id: \.self) { row in
          GridRow { // GridRow is a no-op in non-Grid layouts
            ForEach((0..<sideLength), id: \.self) { column in
              let polygon = model
                .polygonGeometries[sideLength * row + column]
              PolygonView(polygonGeometry: polygon)
                .polygonViewLayoutKey(polygon)
            }
          }
        }
      }
    }
    .drawingGroup()
    .frame(maxWidth: .infinity, maxHeight: .infinity)
    .onReceive(musicBeatTimer) { date in
      if heavy {
        // Transitioning to a thin font happens slowly
        withAnimation(.easeOut(duration: 0.468757324 - 0.1)) {
          heavy.toggle()
        }
      } else {
        // Transitioning to thick happens quickly, to give the
        // appearance of a "strong" downbeat
        withAnimation(.easeIn(duration: 0.1)) {
          heavy.toggle()
        }
      }
    }
    .onReceive(layoutChangingTimer) { date in
      guard cycleLayouts else { return }
      withAnimation(animation) {
        scatteredLayout = DynamicPolygonView.newScatteredLayout(date)
      }

    }
  }
}

private struct PolygonDesignerView: View {
  @EnvironmentObject var model: PolygonModel
  @State var cycleLayouts = false
  @State var hideDesignerView = true

  var body: some View {
    ZStack(alignment: .bottom) {
      DynamicPolygonView(cycleLayouts: $cycleLayouts)
        .onTapGesture(count: 2) {
          withAnimation {
            hideDesignerView.toggle()
          }
        }
      ControlView(cycleLayouts: $cycleLayouts)
        .padding()
        .background(.thickMaterial)
        .offset(CGSize(width: 0, height: hideDesignerView ? 300 : 0))
    }
  }
}

/// Tunes the parameters of a `PolygonModel`
private struct ControlView: View {

  /// The instance `self` tunes the parameters of.
  @EnvironmentObject var model: PolygonModel

  /// Can be used by a parent view to cycle through instances of layouts.
  @Binding var cycleLayouts: Bool

  var body: some View {
    VStack {
      Button("Reset", action: model.reset)
      let layout = HStack()
      layout {
        Toggle("Tiled", isOn: Binding(get: {
          model.tiled
        }, set: { tile in
          // After toggled, wait 5 seconds, then transition back to a
          // scattered layout
          DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
            withAnimation(.linear(duration: 1.4)) {
              model.usesGridLayout = false
              model.drawAsRandomPolygons = true
            }
          }

          withAnimation(.linear(duration: 1.8)) {
            model.usesGridLayout = tile
            model.drawAsRandomPolygons = !tile
          }
        }))

        Toggle("Cycle Layouts", isOn: $cycleLayouts)
      }
    }
    .padding(2)
  }
}

// MARK: PolygonView

/// Wraps a ``Polygon`` shape applying a fill.
private struct PolygonView: View {
  var polygonGeometry: PolygonGeometry

  var body: some View {
    Polygon(polygonGeometry: polygonGeometry)
      .fill(polygonGeometry.color)
  }
}

/// A Polygon shape that supports any number of sides as defined by `polygonGeometry`
private struct Polygon: Shape {
  var polygonGeometry: PolygonGeometry

  typealias AnimatableData = AnimatableVector

  var animatableData: AnimatableVector {
    get { polygonGeometry.vectorPath }
    set { polygonGeometry.points = newValue.points }
  }

  func path(in rect: CGRect) -> Path {
    // Scale up the shape's path to fill as much space as it is given
    let path = polygonGeometry.path
    let boundingRect = path.boundingRect

    let xScale = rect.width / boundingRect.width
    let yScale = rect.height / boundingRect.height

    let translate = CGAffineTransform(
      translationX: -boundingRect.origin.x * xScale,
      y: -boundingRect.origin.y * yScale
    )
    let scale = CGAffineTransform(scaleX: xScale, y: yScale)
    return path.applying(scale.concatenating(translate))
  }

  func sizeThatFits(_ proposal: ProposedViewSize) -> CGSize {
    if proposal == .infinity {
      // If proposed infinite space, use the preferred, absolute size.
      return CGSize(width: polygonGeometry.sideLength,
              height: polygonGeometry.sideLength)
    } else {
      // If we don't have infinite space, assume we've been given all the
      // space the parent view can afford, and take all of it.
      return proposal.replacingUnspecifiedDimensions()
    }
  }
}

// MARK: ScatteredLayout

private struct PolygonViewLayoutKey: LayoutValueKey {
  static let defaultValue: PolygonGeometry? = nil
}

extension View {
  fileprivate func polygonViewLayoutKey(_ value: PolygonGeometry)
  -> some View {
    return layoutValue(key: PolygonViewLayoutKey.self, value: value)
  }
}

/// ScatteredLayout assumes a certain standard size and lays out its views
/// (tagged with `PolygonViewLayoutKey` data) such that they don't collide
/// within that size. As the size grows, the shapes stay the same size,
/// but get farther or closer.
private struct ScatteredLayout: Layout {

  /// Cache data for a `ScatteredLayout`.
  struct Cache {

    /// Maps a `PolygonGeometry.id` to its position in a `standardSize`
    /// coordinate space.
    var rects: [UUID: CGRect]

    /// Used as a cache buster.
    var seed: TimeInterval?
  }

  /// The smallest size a view using this layout can be.
  private let minimumBaseSize: CGSize

  /// The base coordinate system this view assumes when laying out.
  private let standardSize: CGSize = CGSize(width: 500, height: 500)

  /// Clients can pass a value here and polygons won't be placed in that rect.
  var textAvoidanceRect: CGRect = .zero

  /// If different, we've been requested to bust the cache, and create a new
  /// one.
  /// - Note the cache can persist across different instances of a
  ///  `ScatteredLayout`
  private let seed: TimeInterval

  func sizeThatFits(
    proposal: ProposedViewSize,
    subviews: LayoutSubviews,
    cache: inout Cache
  ) -> CGSize {
    let proposedSize = proposal
      .replacingUnspecifiedDimensions(by: minimumBaseSize)
    return CGSize(
      width: proposedSize.width
        .clamped(
          to: minimumBaseSize.width..<CGFloat.greatestFiniteMagnitude
        ),
      height: proposedSize.height
        .clamped(
          to: minimumBaseSize.height..<CGFloat.greatestFiniteMagnitude
        )
    )
  }

  init(count: Int, seed: TimeInterval, textAvoidanceRect: CGRect = .zero) {
    self.seed = seed
    minimumBaseSize = CGSize(width: CGFloat(count), height: CGFloat(count))
    self.textAvoidanceRect = textAvoidanceRect
  }

  func makeCache(subviews: Subviews) -> Cache {
    var cache: Cache =  Cache(rects: [:], seed: self.seed)
    var placedPolygons: [CGRect] = []

    for subview in subviews {

      guard let polygon = subview[PolygonViewLayoutKey.self] else {
        // This is the title text view, skip it.
        continue
      }

      var subviewsPreferredSize = subview.sizeThatFits(.infinity)
      var counter = 20

      while counter > 0 {
        counter -= 1
        let randomX = CGFloat.random(in: 0..<standardSize.width)
        let randomY: CGFloat
        if randomX > textAvoidanceRect.minX
          && randomX < textAvoidanceRect.maxX {
          // Pick from either above or below the avoidance rect
          if Bool.random() {
            randomY = CGFloat.random(
              in: 0..<textAvoidanceRect.minY
            )
          } else {
            randomY = CGFloat.random(
              in: textAvoidanceRect.maxY..<standardSize.height
            )
          }
        } else {
          randomY = CGFloat.random(in: 0..<standardSize.height)
        }

        let origin = CGPoint(x: randomX, y: randomY)
        let rect = CGRect(origin: origin, size: subviewsPreferredSize)

        if placedPolygons.allSatisfy({ placed in
          !placed.intersects(rect)
        }) && !rect.intersects(textAvoidanceRect) {
          // The shape found a non-overlapping place to be. Lock in
          // it's position
          placedPolygons.append(rect)
          cache.rects[polygon.id] =
          CGRect(origin: origin,
               size: subviewsPreferredSize)
          break
        } else  {
          if (counter == 0) {
            if rect.intersects(textAvoidanceRect) {
              subviewsPreferredSize = .zero
            }
            placedPolygons.append(rect)
            cache.rects[polygon.id] =
            CGRect(origin: origin,
                 size: subviewsPreferredSize)
          }
        }
      }
    }
    return cache
  }

  func placeSubviews(in bounds: CGRect,
             proposal: ProposedViewSize,
             subviews: Subviews,
             cache: inout Cache) {
    // We have the frame value cached (via makeCache())
    // for every view to be placed in a `standardSize` coordinate system.
    // Now we need to map that `standardSize` to the size was proposed.
    let proposedSize = proposal
      .replacingUnspecifiedDimensions(by: minimumBaseSize)
    let xProposedToBaseRatio = proposedSize.width / standardSize.width
    let yProposedToBaseRatio = proposedSize.height / standardSize.height

    for subview in subviews {
      guard let uuid = subview[PolygonViewLayoutKey.self]?.id, let rect =
          cache.rects[uuid] else {
        let desiredSize = subview.sizeThatFits(.zero)
        let centered = desiredSize.centered(in: bounds)
        subview.place(
          at: centered.origin,
          proposal: ProposedViewSize(
            width: desiredSize.width,
            height: desiredSize.height
          )
        )
        continue
      }

      let mappedPoint = CGPoint(x: rect.origin.x * xProposedToBaseRatio,
                    y: rect.origin.y * yProposedToBaseRatio)

      subview.place(at: mappedPoint,
              proposal: ProposedViewSize(width: rect.size.width,
                           height:rect.size.height)
      )
    }
  }

  func updateCache(_ cache: inout Cache, subviews: Subviews) {

    // Bust the cache if we've been given a new seed value
    // or if our subviews have been swapped out from underneath us.
    if self.seed != cache.seed
      || !cache.rects.contains(where: { (key: UUID, value: CGRect) in
        subviews.first?[PolygonViewLayoutKey.self]?.id == key
      })  {
      cache = makeCache(subviews: subviews)
      return
    }
  }

}

/// This struct facilitates animation of point-based `Path`s so long as said
/// source and destination `Path` have an equal number of vertices.
private struct AnimatableVector: VectorArithmetic {

  static var zero: AnimatableVector = AnimatableVector(points: [])

  private(set) var points: [CGPoint]

  var magnitudeSquared: Double {
    let squared = points.map { point in
      CGPoint(x: point.x * point.x, y: point.y * point.y)
    }
    let sumOfSquares = squared.map { point in // dot product?
      sqrt(point.x + point.y)
    }
    let sum = sumOfSquares.reduce(0, +)
    return Double(sum)
  }

  /// Facilitates a valid `.zero` value, no matter the dimension of the vector
  subscript(safe index: Int) -> CGPoint {
    return (self.points.count <= index) ? .zero : points[index]
  }

  static func - (lhs: AnimatableVector, rhs: AnimatableVector)
  -> AnimatableVector {
    let negated = rhs.points.map { CGPoint(x: -$0.x, y: -$0.y) }
    return lhs + AnimatableVector(points: negated)
  }

  static func + (lhs: AnimatableVector, rhs: AnimatableVector)
  -> AnimatableVector {
    var output: [CGPoint] = []
    for i in 0..<lhs.points.count {
      output.append(CGPoint(x: lhs[safe: i].x + rhs[safe: i].x,
                  y:lhs[safe: i].y + rhs[safe: i].y ))
    }
    return AnimatableVector(points: output)
  }

  mutating func scale(by rhs: Double) {
    points = points.map { CGPoint(x: $0.x * CGFloat(rhs),
                    y: $0.y * CGFloat(rhs)) }
  }
}

// MARK: Random Polygon Generation & Geometry

private let mean: Float = 10
private let deviation: Float = 3
private let gaussian = GKGaussianDistribution(
  randomSource: GKARC4RandomSource(),
  mean: mean,
  deviation: deviation)

/// Factory type for creating points describing a random Polygon
private struct PolygonGeometry: Identifiable, Equatable, Hashable {

  /// The horizontal and vertical side lengths of the polygon's bounding box.
  let sideLength: CGFloat

  /// A constant count of the total points that comprise this
  /// `PolygonGeometry`'s path. Clients can set `points` to a new value, but
  /// the new value should have the same `count` for smooth `Path` animations
  let numberOfVertices: Int

  /// Supports animation of point-based `Path`s by providing an array of
  /// points that can be interpolated.
  var vectorPath: AnimatableVector {
    AnimatableVector(points: points)
  }

  /// If `false`, this instance will present itself as a rectangular shape
  /// (not necessarily with 4 vertices) that fills available space.
  private(set) var drawsAsPolygon: Bool = true

  /// Points describing the `Path` used to render `self`.
  var points: [CGPoint] {
    willSet {
      assert(points.count == polygonPathPoints.count)
    }
  }

  /// Delineate the path of the random polygon.
  private let polygonPathPoints: [CGPoint]

  let color: Color = [
    Color(red: 0.73, green: 0.20, blue: 0.20),
    Color(red: 0.95, green: 0.66, blue: 0.24),
    Color(red: 0.14, green: 0.29, blue: 0.49),
    Color(red: 0.46, green: 0.76, blue: 0.67),
    Color(red: 0.30, green: 0.33, blue: 0.22),
    Color(red: 0.49, green: 0.55, blue: 0.64),
    Color(red: 0.92, green: 0.53, blue: 0.30),
    Color(red: 0.20, green: 0.45, blue: 0.55),
    Color(red: 0.41, green: 0.45, blue: 0.45),
    Color(red: 0.87, green: 0.67, blue: 0.61)
  ].randomElement()!

  private var spikiness: CGFloat = 0.2
  private var irregularity: CGFloat = 0.2

  let id = UUID()

  /// Owning `Shape` instances should use this to draw.
  var path: Path { Path(from: points) }

  init(pointsVector: [CGPoint], sideLength: CGFloat) {
    self.numberOfVertices = pointsVector.count
    self.points = pointsVector
    self.polygonPathPoints = points
    self.sideLength = sideLength
  }

  func drawn(asRandomizedPolygon: Bool) -> Self {
    var copy = self
    copy.drawsAsPolygon = asRandomizedPolygon
    copy.points = asRandomizedPolygon
    ? copy.polygonPathPoints
    : CGRect(x: 0, y: 0, width: 1, height: 1)
      .pointSequence(of: copy.numberOfVertices)
    return copy
  }

  func hash(into hasher: inout Hasher) {
    hasher.combine(id)
  }
}

/// A namespace around functionality to generate a  path drawn in a 1x1 square
/// with configurable "irregularity" and "spikiness".
/// The closer both are to zero, the closer the generated polygon is to a
/// [regular polygon](https://mathworld.wolfram.com/RegularPolygon.html)
private enum UnitPolygonGeometryFactory {

  /// The maximum possible radius. A value of 0.5 restricts the algorithm
  /// to the unit square.
  private static let maxRadius: CGFloat = 0.5

  /// A — by no means definitive — algorithm for creating an arbitrary
  /// polygon of `vertexCount` vertices
  /// - Parameters:
  ///   - vertexCount: How many vertices (and edges) the polygon will have
  ///   - irregularity: A subjective term for how "irregular" the polygon is.
  ///   A fully regular polygon has all equal sides, assuming 0 `spikinesss`.
  ///   - spikiness: A subjective term for how "spiky" the polygon is.
  ///   A polygon with high spikiness will have more vertices closer and
  ///   farther from where the vertex would be on a regular polygon.
  /// - Returns: An array of points representing the point-based path of
  /// the polygon
  static func random(vertexCount: Int,
             irregularity: CGFloat = 0.2,
             spikiness: CGFloat = 0.2)
  -> [CGPoint] {

    let floatVertices = CGFloat(vertexCount)

    // Irregularity is how much we're willing to allow the angular steps to
    // vary from "perfect". For example, in a regular (all sides equal)
    // six-sided polygon, each angular step is 2𝜋 / 6. Irregularity
    // defines the range that value can take, centered around a mean of
    // 2𝜋 / 6. We accept an irregularity between 0 and 1, and then
    // scale it for how much that represents out of a circle's radians.
    let scaledIrregularity = irregularity * 2.0 * CGFloat.pi / floatVertices

    // Spikiness describes how often we want to see values that are very
    // far from where a vertex of a regular polygon would be. For example,
    // a high positive spikiness might push a vertex radially very far from
    // the center, leading to a big "spike". Meanwhile, a spikiness of 0
    // will yield more circular polygons.
    let denormalizedSpikiness = spikiness * maxRadius

    let gaussian = GKGaussianDistribution(
      randomSource: GKARC4RandomSource(),
      mean: Float(maxRadius * 1024),
      deviation: Float(denormalizedSpikiness * 1024))

    // Generate the angular steps
    var raidanAngleSteps: [CGFloat] = []

    // Both of these measured in radians
    let minimumSliceWidth =
    (2.0 * CGFloat.pi / floatVertices) - scaledIrregularity
    let maximumSliceWidth =
    (2.0 * CGFloat.pi / floatVertices) + scaledIrregularity

    var sum: CGFloat = 0

    for _ in (0..<vertexCount) {
      let radians = CGFloat
        .random(in: minimumSliceWidth...maximumSliceWidth)
      raidanAngleSteps.append(radians)
      sum += radians
    }

    // Re-divide these steps so the point 0 and n+1 are the same.
    // I.e. if the random angle generation from the above loop yielded
    // more or less than 2𝜋 radians, reapportion those divisions to sum to
    // 2𝜋.
    let k = sum / (2 * CGFloat.pi)
    (0..<vertexCount).forEach { i in
      raidanAngleSteps[i] /= k
    }

    let maximumPossibleGaussianSample = CGFloat(
      gaussian.mean + Float(denormalizedSpikiness * 1024)*3
    )

    // Finally, make all of the normalized points within a 1x1 square
    // Unlike the unit circle of traditional geometry, because (0, 0) is in
    // the top left, (0.5, 0.5) is in the middle. Thus, positively
    // incrementing the angle moves us clockwise around the circle
    var points: [CGPoint] = []
    let center = CGPoint(x: maxRadius, y: maxRadius)
    var cumulativeAngle: CGFloat = 0.0
    for i in (0..<Int(vertexCount)) {

      // * 2 to keep the sample <= 0.5 (`maxRadius)
      let radiusForPoint = CGFloat(gaussian.nextInt())
      / (maximumPossibleGaussianSample * 2)

      let x = center.x + radiusForPoint * cos(cumulativeAngle)
      let y = center.y + radiusForPoint * sin(cumulativeAngle)
      points.append(CGPoint(x: x, y: y))

      cumulativeAngle += raidanAngleSteps[i]
    }
    return points
  }
}

// MARK: Observable Polygon Model

/// A `PolygonModel` describes a collection of randomized ``Polygons`` that
/// can be laid out by `AnyLayout` type.
private class PolygonModel: ObservableObject {

  static let total = (maxSides - minSides + 1) * polygonsPerSideCount

  /// The minimum sides the randomly generated sides will have
  private static let minSides = 4

  /// The maximum sides the randomly generated sides will have
  private static let maxSides = 7

  /// The number of randomly generated polygons to make _per side length_.
  private static let polygonsPerSideCount = 32

  /// All `PolygonGeometry`s that are laid out with `scatteredLayout`
  @Published var polygonGeometries: [PolygonGeometry] = makeGeometries()

  /// If `true`, `self` is expressing a grid layout with rectangular tiles.
  var tiled: Bool { usesGridLayout && !drawAsRandomPolygons }

  /// If `true`, ignore `scatteredLayout` and instead use a `Grid` layout
  @Published var usesGridLayout: Bool = false

  /// If `true`, `polygonGeometries` draw themselves as randomized polygons.
  /// If false, a rectangle that fills all available space.
  @Published var drawAsRandomPolygons: Bool = true {
    didSet {
      polygonGeometries = polygonGeometries.map {
        $0.drawn(asRandomizedPolygon: drawAsRandomPolygons)
      }
    }
  }

  /// Tunable by clients to experiment with different values.
  let spikiness: CGFloat = 0.2
  /// Tunable by clients to experiment with different values.
  let irregularity: CGFloat = 0.2

  /// Creates many ``PolygonGeometry`` instances with the given parameters.
  /// - Parameters:
  ///   - irregularity: A subjective term for how "irregular" the polygon is.
  ///   A fully regular polygon has all equal sides, assuming 0 `spikinesss`.
  ///   - spikiness: A subjective term for how "spiky" the polygon is.
  ///   A polygon with high spikiness will have more vertices closer and
  ///   farther from where the vertex would be on a regular polygon.
  /// - Returns: An array of `n` polygons where `n` is defined by the
  ///  `PolygonModel` class.
  private static func makeGeometries(
    irregularity: CGFloat = 0.3,
    spikiness: CGFloat = 0.3) -> [PolygonGeometry] {
      var scales: Array<CGFloat> = polygonSizeRatios
        .reduce(into: []) { partialResult, sizeRatio in
          let (size, percentage) = sizeRatio
          let scalesToMake = Int(ceil(percentage * CGFloat(total)))
          partialResult.append(contentsOf: (0..<scalesToMake)
            .map { _ in CGFloat.random(in: size.sizeRange) })
        }.shuffled()

      return (minSides...maxSides).flatMap { vertexCount in
        return (0..<polygonsPerSideCount).map { _ in
          let unitPolygon = UnitPolygonGeometryFactory
            .random(vertexCount: vertexCount,
                irregularity: irregularity,
                spikiness: spikiness)
          let polygonGeometry = PolygonGeometry(
            pointsVector: unitPolygon,
            sideLength: scales.removeFirst())
          return polygonGeometry
        }
      }.shuffled()
    }

  /// Complete remove and regenerate all model data.
  func reset() {
    polygonGeometries.removeAll(keepingCapacity: true)
    polygonGeometries = PolygonModel.makeGeometries(
      irregularity: irregularity,
      spikiness: spikiness
    )
  }
}

private extension PolygonModel {

  /// Use a sampling of various sized polygons
  enum PieceSize: Hashable {
    case tiny
    case small
    case medium
    case large

    /// The range for the side length of the bounding rect of a polygon
    var sizeRange: ClosedRange<CGFloat> {
      switch self {
      case .tiny:
        return 16.0...25.0
      case .small:
        return 25.0...40.0
      case .medium:
        return 40.0...50.0
      case .large:
        return 50.0...65.0
      }
    }
  }

  /// This dictionary denotes the ratio of sizes to use.
  /// - warning: Should sum to 100.
  private static let polygonSizeRatios: [PieceSize: CGFloat] =
  [
    .large: 0.15,
    .medium: 0.25,
    .small: 0.25,
    .tiny: 0.35
  ]
}

// MARK: - Utility Extensions

extension FloatingPoint {

  /// - returns an instance of `Self` clamped to the ``ClosedRange``.
  func clamped(to limits: ClosedRange<Self>) -> Self {
    return min(max(self, limits.lowerBound), limits.upperBound)
  }

  /// - returns an instance of `Self` clamped to the ``Range``.
  /// - note the value returned will be less than the provided upper bound, as
  ///  is dictated by ``Range``.
  func clamped(to limits: Range<Self>) -> Self {
    return min(max(self, limits.lowerBound), limits.upperBound.nextDown)
  }
}

extension CGRect {

  /// Creates a rectangular sequence of `vertexCount `points denoting a
  /// rectangular path.
  /// - note This is helpful for animating a `Path` composed of `vertexCount`
  /// points into a ``Rectangle``.
  func pointSequence(of vertexCount: Int) -> [CGPoint] {
    // Start at a random corner. When many Polygons are using this
    // animation at once, if they all start at the same corner, an
    // unnatural uniformity of motion emerges.
    var startingPercent = [0, 0.25, 0.5, 0.75].randomElement()!
    var points: [CGPoint] = []

    let extraPoints = vertexCount - 4
    let (groups, remainder) = extraPoints
      .quotientAndRemainder(dividingBy: 3)

    for edge in 0...3 {
      points.append(pointAlongPerimeter(at: startingPercent))
      for i in (0..<(edge == 3 ? remainder : groups)) {
        points.append(pointAlongPerimeter(
          at: startingPercent + 0.25
          / CGFloat(groups + 1) * CGFloat(i)))
      }
      startingPercent += 0.25
      startingPercent.formTruncatingRemainder(dividingBy: 1)
    }
    assert(points.count == vertexCount)
    return points
  }

  /// Returns the ``CGPoint`` that is `percent` along the path of `self`,
  /// with 0% mapping to the top-left corner, progressing clockwise.
  /// E.g. 50% would map to the bottom right corner if and only if `self` is
  ///  a square.
  /// - Parameters:
  ///   - percent: A percentage between `0.0` and `1.0`
  private func pointAlongPerimeter(at percent: CGFloat) -> CGPoint {
    let perimeter = size.width * 2 + size.height * 2

    // Mark the four corners as percentages around the rect. For example,
    /// these values for a square would be 25%, 50%, 75%, 100%
    let topRight = size.width / perimeter
    let bottomRight = topRight + (size.height / perimeter)
    let bottomLeft = bottomRight + (size.width / perimeter)
    let topLeft = 1.0

    switch percent {
    case 0..<topRight:
      return CGPoint(
        x: percent / topRight * size.width,
        y: minY)
    case topRight..<bottomRight:
      return CGPoint(
        x: maxX,
        y: (percent - topRight)
        / (bottomRight - topRight) * size.height)
    case bottomRight..<bottomLeft:
      return CGPoint(
        x: maxX - ((percent - bottomRight) / (bottomLeft - bottomRight)
               * size.width),
        y: maxY)
    case bottomLeft...topLeft:
      return CGPoint(
        x: minX,
        y: maxY - (percent - bottomLeft) / (topLeft - bottomLeft)
        * size.height
      )
    default:
      preconditionFailure("Invalid percentage requested")
    }
  }
}

/// Returns a new `CGRect` with the same size as `self`, but centered in `other`
/// vertically, and horizontally.
extension CGSize {
  func centered(in other: CGRect) -> CGRect {
    CGRect(x: other.midX - width / 2.0,
         y: other.midY - height / 2.0,
         width: width,
         height: height)
  }
}

extension Path {
  /// Convenience for initializing a `Path` from an array of `CGPoint`s given
  /// the first point element is the `Path`'s first point.
  init(from points: [CGPoint]) {
    self.init()
    self.addLines(points)
    self.closeSubpath()
  }
}
```

[window]: https://developer.apple.com/documentation/swiftui/window
[keyboardshortcut(_:)]: https://developer.apple.com/documentation/swiftui/scene/keyboardshortcut(_:)
[openwindow]: https://developer.apple.com/documentation/swiftui/environmentvalues/openwindow
[defaultposition(_:)]: https://developer.apple.com/documentation/swiftui/window/defaultposition(_:)
[defaultsize(_:)]: https://developer.apple.com/documentation/swiftui/windowgroup/defaultsize(_:)
[windowResizability(_:)]: https://developer.apple.com/documentation/swiftui/windowgroup/windowresizability(_:)
[presentationdetents(_:)]: https://developer.apple.com/documentation/swiftui/presentedwindowcontent/presentationdetents(_:)
[MenuBarExtra]: https://developer.apple.com/documentation/swiftui/menubarextra
[MultiDatePicker]: https://developer.apple.com/documentation/swiftui/multidatepicker
[sm]: https://developer.apple.com/documentation/swiftui/view-search
[gradient]: https://developer.apple.com/documentation/swiftui/color/gradient
[shadow]: https://developer.apple.com/documentation/swiftui/shapestyle/shadow(_:)-swift.method
[ss]: https://developer.apple.com/documentation/swiftui/shapestyle
[grid]: https://developer.apple.com/documentation/swiftui/grid
[GridRow]: https://developer.apple.com/documentation/swiftui/gridrow
[Layout]: https://developer.apple.com/documentation/swiftui/layout
[anylayout]: https://developer.apple.com/documentation/swiftui/anylayout
