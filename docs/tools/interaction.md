# Interaction tools

Tools for interacting with components in the active window. All UID parameters
come from `take_snapshot` (see [inspection.md](inspection.md)). Actions are
executed on the owning toolkit's UI thread: the Event Dispatch Thread for
Swing, the JavaFX Application Thread for JavaFX.

## `click`

Click a component. A plain left click on an `AbstractButton` (button,
checkbox, radio button, toggle button) or a JavaFX `ButtonBase` is a direct
`doClick()` / `fire()`. Any other click is delivered as mouse events
(moved, pressed, released, clicked) to the component under the point, so it
works while the window is covered or in the background and does not move the
user's mouse.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | Component UID |
| `button` | string | no | `LEFT` (default), `RIGHT`, `MIDDLE` |
| `clickType` | string | no | `SINGLE` (default) or `DOUBLE` |
| `x` | number | no | Offset from the component's left edge (default: center) |
| `y` | number | no | Offset from the component's top edge (default: center) |
| `modifiers` | string | no | Keys held during the click: `CTRL`, `SHIFT`, `ALT`, `META` (Cmd), `SHORTCUT` (Ctrl, or Cmd on macOS), combined with `+`, e.g. `CTRL+SHIFT` |
| `robot` | boolean | no | Swing: move the real OS mouse with `java.awt.Robot` instead; the window must be in front and uncovered |

**Notes:**
- Use `x`/`y` for content drawn inside one component: points of a chart,
  items on a canvas, handles. With `x`/`y` on a container, the click goes to
  the child under that point, as a real click would.
- A right click is flagged as the platform's popup trigger.

## `hover`

Move the mouse over a component to trigger hover effects and tooltips. Like
`click`, it is delivered as mouse-entered/moved events unless `robot` is set;
hovering another component first sends mouse-exited to the previous one.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | Component UID |
| `x` | number | no | Offset from the component's left edge (default: center) |
| `y` | number | no | Offset from the component's top edge (default: center) |
| `robot` | boolean | no | Swing: move the real OS mouse instead |

## `focus`

Give keyboard focus to a component so that subsequent `press_key` or
`type_text` calls target it deterministically.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | Component UID |

## `type_text`

Type text character-by-character into the focused component using key events
(as opposed to `fill`, which sets the value directly). Use this for UIs with
per-keystroke listeners, input masks, or autocompletion.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `text` | string | yes | Text to type |
| `uid` | string | no | Component UID to focus before typing |

## `fill`

Set the text/value of a text component (`JTextField`, `JTextArea`, …),
`JSpinner`, `JSlider`, or editable `JComboBox` (Swing) — or a
`TextInputControl` (`TextField`, `PasswordField`, `TextArea`), `Spinner`, or
editable `ComboBox` (JavaFX).

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | Component UID |
| `text` | string | yes | Text or value to enter |

## `select_option`

Select an option in a `JList`, `JComboBox`, or `JTabbedPane` (Swing) — or a
`ComboBox`, `ChoiceBox`, `ListView`, or `TabPane` (JavaFX) — by index or
visible text.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | Component UID |
| `index` | number | no | Zero-based option index |
| `text` | string | no | Visible text of the option |

**Notes:** provide either `index` or `text`.

## `select_tree_node`

Select a `JTree` (Swing) or `TreeView` (JavaFX) node by path.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | `JTree` (Swing) or `TreeView` (JavaFX) component UID |
| `path` | string | yes | Node path separated by ` > `, e.g. `Root > Folder > Leaf` |

## `select_table_cell`

Select a `JTable` (Swing) or `TableView` (JavaFX) cell.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | `JTable` (Swing) or `TableView` (JavaFX) component UID |
| `row` | number | yes | Zero-based row index |
| `col` | number | yes | Zero-based column index |

## `select_menu_item`

Click a menu item in the active window's menu bar (`JMenuBar` for Swing,
`MenuBar` for JavaFX).

| Parameter | Type | Required | Description |
|---|---|---|---|
| `path` | string | yes | Menu path separated by ` > `, e.g. `File > Save` |

## `select_context_menu_item`

Open the context menu of a component and click an item by path
(`JPopupMenu` for Swing, `ContextMenu` for JavaFX). The component's
registered popup menu is used when available; otherwise a right-click popup
trigger is dispatched.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | Component UID |
| `path` | string | yes | Menu item path separated by ` > `, e.g. `Copy` or `Refactor > Rename` |

## `press_key`

Press a key or key chord using AWT `Robot`.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `keys` | string | yes | Key or chord such as `ENTER`, `TAB`, `CTRL+S` |

## `drag`

Drag with the left button from a point in one component to a point in
another, or within one component. The press, the intermediate drag events,
and the release are delivered to the component under the start point, as AWT
does for a real drag; this covers sliders, split-pane dividers, chart zoom
rectangles, and canvas handles.

| Parameter | Type | Required | Description |
|---|---|---|---|
| `fromUid` | string | yes | Source component UID |
| `toUid` | string | no | Target component UID (default: `fromUid`) |
| `fromX` / `fromY` | number | no | Start point in the source component (default: center) |
| `toX` / `toY` | number | no | End point in the target component (default: center) |
| `robot` | boolean | no | Move the real OS mouse in steps; needed for native drag-and-drop (`TransferHandler`) between components. The window must be in front and uncovered |

**Notes:**
- Swing only; on a JavaFX uid this fails with an error saying so.
- Native drag-and-drop runs in the operating system's drag loop, which events
  sent to a component cannot drive. Without `robot`, a drag between two lists
  or trees is delivered but no data is transferred.

## `scroll`

Scroll a component inside a `JScrollPane` (Swing) or `ScrollPane` (JavaFX).

| Parameter | Type | Required | Description |
|---|---|---|---|
| `uid` | string | yes | Component UID inside a scroll pane |
| `direction` | string | no | `UP`, `DOWN` (default), `LEFT`, `RIGHT` |
| `amount` | number | no | Scroll units (default 3) |
