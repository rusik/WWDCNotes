# What’s new with text and text interactions

Text is an absolutely critical component of every app. Discover the latest features and enhancements for creating rich text experiences on Apple platforms. We’ll show you how to take advantage of common text elements and create entirely custom interactions for your app. Learn about updates to dictation, text loupe, and text selection, and explore improvements to text clipping, line wrapping, and hyphenation.

@Metadata {
   @TitleHeading("WWDC23")
   @PageKind(sampleCode)
   @CallToAction(url: "https://developer.apple.com/wwdc23/10058", purpose: link, label: "Watch Video (15 min)")

   @Contributors {
      @GitHubUser(RamitSharma991)
   }
}



Text plays a crucial role in every application. It's the primary way to consume information and communicate. Here are the tools to create a powerful text experience in your app:

## Changes to System selection UI

* a completely redesigned text cursor on all platforms.
* an inline, interactive switcher when changing input languages.
* more ergonomic selection handles when doing range selection.
* a completely new loupe to make it easier to place the cursor in large bodies of text.
* works with `UITextViews` or `UITextFields` automatically.
* clients of `UITextInteraction` also gets the new selection UI.
* for a highly customized UI displaying text added `UITextSelectionDisplayInteraction`
  * `UITextSelectionDisplayInteraction` is a new type of `UIInteraction` that can be installed on any UIView.
  * must adopt `UITextInput` protocol.
  * provides the cursor view and cursor accessories and the range highlight and selection handles.
  * All of these views are replaceable.

```swift
 // Add selection display interaction
 let selectionDisplayInteraction = UITextSelectionDisplayInteraction(textInput: document, delegate: self)
 view.addInteraction(selectionDisplayInteraction)

 // call this whenever selection changes in your document
 selectionDisplayInteraction.setNeedsSelectionUpdate()
```

* added a new API for displaying a loupe namely Text loupe.
  * new `UITextLoupeSession` object for displaying loupe.
  * can be used on any view.
  * `UITextSelectionDisplayInteraction` or `UITextInput` isn't required to use it.
  * recommended to use a gesture recognizer, such as `UIPanGestureRecognizer` to drive the loupe's updates.

## Text item actions and menus

* much more customizable using new APIs on `UITextViewDelegate`
* makes it possible to modify the primary action for text items.
* New methods on `UITextViewDelegate`

```swift
func textView(_ textView: UITextView, primaryActionFor textItem: UITextItem, defaultAction: UIAction) -> UIAction?

func textView(_ textView: UITextView, menuConfigurationFor textItem: UITextItem, defaultMenu: UIMenu) -> UITextItem.MenuConfiguration?
```

* Text items represent the content that support item interactions.
* includes text attachments, represented by `NSTextAttachment`, and links, represented by `NSLinkAttributeName`
* `UITextItem` now also supports tagging custom ranges of text for interaction.
* use the `UITextItemTagAttributeName` on the attributed string representing the range of text you wish to make interactive.
* now possible to change the default tap or menu behavior when a text item is interacted with.
* redirect known links to existing views within the app or add custom menus to parts of the text.
   
## Lists and bullets in TextKit 2
* Supports several different kinds of bullets.
* Automatic numbering of items.
* Localized for every language.

```swift
let textList = NSTextList(markerFormat: .circle, option: 0)
let mutableParagrapghStyle = NSMutableParagraphStyle()
mutableParagraphStyle.textLists = [textList]

textStorage.addAttribute(.paragraphStyle, value: mutableParagraphStyle, range: NSRange(location: 0, textStorage.length))
```

## Dictation on MacOS

* while scrolling, the indicator next to the cursor will stick to the edge of the scroll view, and offer a button to return to the current position in the document.
* If you're using a standard text control in AppKit like NSTextView, you'll get this behavior right out of the box.
* `NSTextInsertionIndicator` is a new API for macOS that also allows to remain consistent with the system's selection UI
  * System-provided insertion point
  * Customizable
  * Follows current accent color
  * Supports dictation effects

```swift
//code example to add NSTextInsertionIndicator
let insertIndicator = NSTextInsertionIndicator(frame: .zero)
documentView.addSubview(insertIndicator)

//Block to handle effect views added by system
insertionIndicator.effectsViewInserter = { view in
documentView.addSubview(view, positioned: .below, relativeTo: insertionIndicator)
}

func didLoseFocus() {
// When resigning first responder, make sure to hide cursor.
insertionIndicator.display = .hidden
}
```

  * updates to the frame will automatically be animated with a glow effect.
  * to disable the glow effect, remove the `showEffectsView` option from `automaticModeOptions`
  * after changing input modes, an effect view is displayed beneath the cursor in horizontal text to show the language selection UI.
  * to override the placement of this UI, specify `preferredTextAccessoryPlacement` in your implementation of `NSTextInputClient`
* adopt `NSTextInputClient` and implement the `selectionRect` and `documentVisibleRect`
* Notify the system about scrolling beginning and ending.


## Internationalization

* Dynamic line-height adjustments
  * automatically adjust the line height in `UILabel` or `UITextField`
  * depends on preferred languages in Language and region settings.
  * independent of actual language of content.
  * only affects text elements that use text styles.
  * use `UIFont.preferredFont(forTextStyle:)`
  * make sure `clipsToBounds = false`
  * Ascenders and descenders for languages like Thai and Hindi will often protrude beyond the line height bounds.
  * Respond to changes in vertical.
 
* Wrapping and hyphenation
  * Improved line breaking for Chinese, German, Japanese, and Korean.
  * Optimized for text style and language.
  * Adopt text styles.
