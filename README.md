# react-roving-tabindex-2

A simple and flexible React implementation of
[roving tabindex](https://developer.mozilla.org/en-US/docs/Web/Accessibility/Guides/Keyboard-navigable_JavaScript_widgets#technique_1_roving_tabindex).

Compared to <https://github.com/stevejay/react-roving-tabindex>,
it still works well if the items get reordered, removed or inserted in DOM.
It does so by relying on native browser behavior and API as much as possible,
being minimally invasive.

Things that it does not support (yet):

- Grid (2D) navigation.

  You can still apply it to widgets with grid layout,
  but users will only be able to go to "next" or "previous" element.

- Setting the initially active element, i.e. when the widget receives focus
  for the first time, the first or the last item will receive focus.

It has minimal state: only one `useState` to store the currently active element.

## Usage

```tsx
import {
  RovingTabindexProvider,
  useRovingTabindex,
} from 'react-roving-tabindex-2'

function MyList() {
  const ulRef = useRef(null)

  return (
    <ul ref={ulRef}>
      <RovingTabindexProvider
        wrapperElementRef={ulRef}
        classNameOfTargetElements='roving-tabindex' // Optional
        direction='vertical' // Optional
      >
        {['Button 1', 'Button 2', 'Button 3'].map(label => (
          <li>
            <MyButton label={label} />
          </li>
        ))}
      </RovingTabindexProvider>
    </ul>
  )
}

function MyButton(props: { label: string }) {
  const ref = useRef(null)
  const rovingTabindex = useRovingTabindex(ref)

  return (
    <button
      ref={ref}
      // `className` must be set on the target elements
      className={rovingTabindex.className + ' ' + 'my-class-name'}
      tabIndex={rovingTabindex.tabIndex}
      // Listen on arrow keys
      onKeyDown={rovingTabindex.onKeydown}
      // You can also call `setAsActiveElement()` at your discretion,
      // but having it `onFocus` is basically a requirement
      // in order for the widget to work as expected.
      onFocus={rovingTabindex.setAsActiveElement}
    >
      {props.label}
    </button>
  )
}
```

## Advice on utilizing roving tabindex and accessibility

This library does not provide you with "out of the box" accessibility.
Its only responsibility is to manage `tabindex` and focus.
You are responsible for applying the appropriate `aria-` attributes and such.

If you need this library, you are probably implementing
one of the following patterns:

- [Listbox](https://www.w3.org/WAI/ARIA/apg/patterns/listbox/)
- [Tabs](https://www.w3.org/WAI/ARIA/apg/patterns/tabs/)
- [Tree View](https://www.w3.org/WAI/ARIA/apg/patterns/treeview/)
- [Grid](https://www.w3.org/WAI/ARIA/apg/patterns/grid/)
- [Combobox](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/)

Follow the linked guides to make sure that your widget is accessible.

Implementing roving tabindex behavior incorrectly could do more harm than good.
Ensure that it's always clear to screen reader users
that they're skipping over some content when they press Tab.
For example, verify that the screen reader announces
the number of elements in the list (see
[`aria-setsize`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Reference/Attributes/aria-setsize)).

Actually try using your software with your eyes closed.
Having added roving tabindex, you might have fixed
a [keyboard trap](https://www.w3.org/WAI/WCAG21/Understanding/no-keyboard-trap.html),
but at a cost of making some list items "invisible" to screen reader users.

If you are not confident that you need to introduce roving tabindex,
consider improving other accessibility aspects of your software,
such as improving
[navigations landmarks](https://www.w3.org/WAI/ARIA/apg/patterns/landmarks/examples/navigation.html).
Some accessibility software (such as NVDA) provides a way
to skip between navigation landmarks, thus avoiding getting trapped
in long lists of focusable items.

## Development

1. `pnpm install`
2. `pnpm run build`
3. `pnpm publish`
