# @geckoui/nativewind Theming Reference

`@geckoui/nativewind` is built on Tailwind CSS v3 + NativeWind v4. Every color is a CSS custom property defined in `styles.css`. To retheme, override these variables in your own `global.css` (after the `@import '@geckoui/nativewind/styles.css'`).

Class-based dark mode is enabled — toggle the `dark` className on a parent `<View>` to flip the palette. NativeWind looks up CSS variables on the nearest `:root` / `.dark` ancestor.

## How to override

```css
/* global.css */
@import "@geckoui/nativewind/styles.css";

@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  --color-primary-600: #16a34a;
  --color-primary-700: #15803d;
}

.dark {
  --color-primary-600: #4ade80;
  --color-primary-700: #22c55e;
}
```

## Complete variable reference

### Primary scale (brand)

Drives buttons, focus borders, switch on-state, calendar selection, etc.

```css
:root {
  --color-primary-50: #edefff;
  --color-primary-100: #d5d9ff;
  --color-primary-200: #b0b5ff;
  --color-primary-300: #8a8fff;
  --color-primary-400: #7171ed; /* focused borders */
  --color-primary-500: #6363dc;
  --color-primary-600: #5b5bd6; /* default brand */
  --color-primary-700: #4747c2;
  --color-primary-800: #3636a0;
  --color-primary-900: #272778;
  --color-primary-950: #181858;
}

.dark {
  --color-primary-600: #7979e8; /* lifted brand for dark surfaces */
  --color-primary-700: #6868da;
}
```

### Surface (backgrounds)

```css
:root {
  --color-surface-primary: #ffffff; /* card / dialog / input bg */
  --color-surface-secondary: #fafafa; /* select chip bg */
  --color-surface-hover: #f5f5f5; /* pressed-state bg */
  --color-surface-disabled: #f0f0f0; /* disabled input bg */
}

.dark {
  --color-surface-primary: #1a1a1a;
  --color-surface-secondary: #242424;
  --color-surface-hover: #2e2e2e;
  --color-surface-disabled: #2e2e2e;
}
```

### Text

```css
:root {
  --color-text-primary: #1a1a1a; /* body text */
  --color-text-secondary: #737373; /* labels, placeholders */
  --color-text-tertiary: #a3a3a3; /* hints, icons */
  --color-text-disabled: #d4d4d4;
  --color-text-on-primary: #ffffff; /* text on filled buttons */
}

.dark {
  --color-text-primary: #f8f8f8;
  --color-text-secondary: #a3a3a3;
  --color-text-tertiary: #737373;
  --color-text-disabled: #404040;
}
```

### Border

```css
:root {
  --color-border-primary: #e0e0e0; /* card / alert border */
  --color-border-secondary: #d4d4d4; /* input border */
  --color-border-focus: #8a8fff; /* focus border (light only) */
  --color-border-disabled: #ebebeb;
}

.dark {
  --color-border-primary: #3d3d3d;
  --color-border-secondary: #3d3d3d;
  --color-border-disabled: #2a2a2a;
}
```

### Status

```css
:root {
  --color-success: #22c55e;
  --color-error: #ef4444;
  --color-warning: #f97316;
  --color-info: #3b82f6;
}
```

Status tokens are identical in light + dark.

### Tooltip

The tooltip flips colors in dark mode automatically.

```css
:root {
  --color-tooltip-bg: #1a1a1a;
  --color-tooltip-fg: #f8f8f8;
}

.dark {
  --color-tooltip-bg: #f8f8f8;
  --color-tooltip-fg: #1a1a1a;
}
```

## Tailwind utility shortcuts

The `@geckoui/nativewind/preset` exposes every token as a Tailwind utility class so you can use them directly in your own components:

| Tailwind class            | CSS variable               |
| ------------------------- | -------------------------- |
| `bg-primary-600`          | `--color-primary-600`      |
| `text-text-primary`       | `--color-text-primary`     |
| `text-text-on-primary`    | `--color-text-on-primary`  |
| `border-border-secondary` | `--color-border-secondary` |
| `bg-surface-hover`        | `--color-surface-hover`    |
| `bg-success`              | `--color-success`          |
| `bg-error`                | `--color-error`            |
| `text-tooltip-fg`         | `--color-tooltip-fg`       |

(All scales — 50 through 950 for `primary`, all surface/text/border roles — are exposed.)

## Class Stacking

Some components compose a base component, so multiple `GeckoUI*` classes end up on the **same element**. Styling the base class affects the wrapper too.

| Element              | Classes on the same element                                 |
| -------------------- | ----------------------------------------------------------- |
| `Pressable` (button) | `.GeckoUIButton` + `.GeckoUILoadingButton__row` (inner row) |

`LoadingButton` renders a `<Button>` underneath, so its outer Pressable has only `.GeckoUIButton`. Its `__row` and `__spinner--*` classes apply to the inner `View`/`Spinner`.

## Component Class Reference

Every CSS class, what RN element it renders on, and what it targets. Use these for component-level overrides. `Modifiers` lists the `--xxx` BEM modifier suffixes that may be appended to the same class (RN doesn't use `data-*` attributes — state is encoded as BEM modifiers).

### Alert

| Class                             | Element     | Targets                           | Modifiers                                                                                           |
| --------------------------------- | ----------- | --------------------------------- | --------------------------------------------------------------------------------------------------- |
| `.GeckoUIAlert`                   | `View`      | Alert container                   | `--default`, `--error`, `--warning`, `--info`, `--success`                                          |
| `.GeckoUIAlert__body`             | `View`      | Header row (icon + title + close) | —                                                                                                   |
| `.GeckoUIAlert__icon`             | `View`      | Variant icon circle               | `--default`, `--error`, `--warning`, `--info`, `--success`                                          |
| `.GeckoUIAlert__icon-glyph`       | `Text`      | Icon glyph character              | —                                                                                                   |
| `.GeckoUIAlert__title-wrap`       | `View`      | Title flex wrapper                | —                                                                                                   |
| `.GeckoUIAlert__title`            | `Text`      | Title text                        | `--default`, `--error`, `--warning`, `--info`, `--success` (color via `.GeckoUIAlert--*` selectors) |
| `.GeckoUIAlert__remove`           | `Pressable` | Close/dismiss button              | —                                                                                                   |
| `.GeckoUIAlert__remove-glyph`     | `Text`      | Close icon character              | —                                                                                                   |
| `.GeckoUIAlert__description`      | `View`      | Description container             | —                                                                                                   |
| `.GeckoUIAlert__description-text` | `Text`      | Description text                  | —                                                                                                   |

Built-in: `rounded-lg`, `border`, `px-4`, `py-3`, `gap-0.5`.

### Button

| Class                   | Element     | Targets                       | Modifiers                                                                                                                           |
| ----------------------- | ----------- | ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `.GeckoUIButton`        | `Pressable` | The button itself             | `--xs`, `--sm`, `--md`, `--lg`, `--xl`, `--filled-primary`, `--outlined-primary`, `--ghost-primary`, `--icon-primary`               |
| `.GeckoUIButton__label` | `Text`      | Inner label inside the button | `--xs`, `--sm`, `--md`, `--lg`, `--xl`, `--filled-primary`, `--outlined-primary`, `--ghost-primary`, `--icon-primary`, `--disabled` |

Built-in: `flex-row`, `items-center`, `justify-center`, `gap-1.5`, `rounded-md`, `overflow-hidden`. Sizes apply `h-*`/`px-*`. The `--icon-primary` variant forces a fixed `w-8 h-8`, ignoring size.

### Calendar

| Class              | Element | Targets          | Modifiers |
| ------------------ | ------- | ---------------- | --------- |
| `.GeckoUICalendar` | `View`  | Calendar wrapper | —         |

#### Header

| Class                                    | Element     | Targets                      | Modifiers     |
| ---------------------------------------- | ----------- | ---------------------------- | ------------- |
| `.GeckoUICalendar__header`               | `View`      | Header row (arrows + title)  | —             |
| `.GeckoUICalendar__header__title`        | `Pressable` | Month/year title (clickable) | `--clickable` |
| `.GeckoUICalendar__header__title-text`   | `Text`      | Title text                   | —             |
| `.GeckoUICalendar__header__arrow-button` | `Pressable` | Navigation arrow             | —             |
| `.GeckoUICalendar__header__arrow-icon`   | `Text`      | Arrow glyph (‹ ›)            | —             |

#### DayPicker

| Class                                          | Element     | Targets                            | Modifiers                                                    |
| ---------------------------------------------- | ----------- | ---------------------------------- | ------------------------------------------------------------ |
| `.GeckoUICalendar__day-picker-weekdays`        | `View`      | Weekday labels row (S M T W T F S) | —                                                            |
| `.GeckoUICalendar__day-picker-weekday`         | `Text`      | Single weekday label               | —                                                            |
| `.GeckoUICalendar__day-picker`                 | `View`      | Day grid container                 | —                                                            |
| `.GeckoUICalendar__day-picker-row`             | `View`      | Single week row                    | —                                                            |
| `.GeckoUICalendar__day-picker__range-underlay` | `View`      | Range highlight strip              | —                                                            |
| `.GeckoUICalendar__day-picker__button`         | `Pressable` | Individual day cell                | `--selected`, `--partial-range`, `--prev-next`, `--disabled` |
| `.GeckoUICalendar__day-picker__button-text`    | `Text`      | Day number text                    | `--today`, `--selected`, `--partial-range`                   |

#### MonthPicker

| Class                                         | Element     | Targets          | Modifiers    |
| --------------------------------------------- | ----------- | ---------------- | ------------ |
| `.GeckoUICalendar__month-picker`              | `View`      | Month grid       | —            |
| `.GeckoUICalendar__month-picker-row`          | `View`      | Single month row | —            |
| `.GeckoUICalendar__month-picker__button`      | `Pressable` | Month cell       | `--selected` |
| `.GeckoUICalendar__month-picker__button-text` | `Text`      | Month label text | —            |

#### YearPicker

| Class                                        | Element     | Targets         | Modifiers                   |
| -------------------------------------------- | ----------- | --------------- | --------------------------- |
| `.GeckoUICalendar__year-picker`              | `View`      | Year grid       | —                           |
| `.GeckoUICalendar__year-picker-row`          | `View`      | Single year row | —                           |
| `.GeckoUICalendar__year-picker__button`      | `Pressable` | Year cell       | `--selected`, `--prev-next` |
| `.GeckoUICalendar__year-picker__button-text` | `Text`      | Year label text | —                           |

Built-in: `border`, `rounded-md`, `px-3`, `py-4`.

### Checkbox

| Class                     | Element     | Targets                            | Modifiers                 |
| ------------------------- | ----------- | ---------------------------------- | ------------------------- |
| `.GeckoUICheckbox`        | `Pressable` | The checkbox square (full element) | `--checked`, `--disabled` |
| `.GeckoUICheckbox__check` | `Text`      | Check glyph (✓)                    | —                         |
| `.GeckoUICheckbox__dash`  | `View`      | Indeterminate dash bar             | —                         |

Built-in: `h-5`, `w-5`, `rounded`, `border`, `items-center`, `justify-center`.

### ConfirmDialog

| Class                                   | Element         | Targets                                           | Modifiers |
| --------------------------------------- | --------------- | ------------------------------------------------- | --------- |
| `.GeckoUIConfirmDialog__dialog`         | `Animated.View` | Dialog wrapper (extends `.GeckoUIDialog__dialog`) | —         |
| `.GeckoUIConfirmDialog__title`          | `Text`          | Title (text-base, font-semibold)                  | —         |
| `.GeckoUIConfirmDialog__content`        | `View`          | Content wrapper                                   | —         |
| `.GeckoUIConfirmDialog__content-text`   | `Text`          | Content body text                                 | —         |
| `.GeckoUIConfirmDialog__actions`        | `View`          | Button row (flex-row, justify-end, gap-2)         | —         |
| `.GeckoUIConfirmDialog__cancel-button`  | `LoadingButton` | Cancel action (no built-in style)                 | —         |
| `.GeckoUIConfirmDialog__confirm-button` | `LoadingButton` | Confirm action (no built-in style)                | —         |

### CounterInput

| Class                          | Element     | Targets                    | Modifiers                                                                       |
| ------------------------------ | ----------- | -------------------------- | ------------------------------------------------------------------------------- |
| `.GeckoUICounterInput`         | `View`      | Outer flex container       | `--disabled`                                                                    |
| `.GeckoUICounterInput__button` | `Pressable` | Increment/decrement button | `--decrement`, `--increment`, `--sm`, `--md`, `--lg`, `--focused`, `--inactive` |
| `.GeckoUICounterInput__icon`   | `Text`      | Plus/minus glyph (+ −)     | `--sm`, `--md`, `--lg`                                                          |
| `.GeckoUICounterInput__input`  | `TextInput` | The number display input   | `--sm`, `--md`, `--lg`, `--focused`, `--disabled`                               |

Built-in: `flex-row`, `items-center`. Buttons share top/bottom border with input; outer corners rounded.

### DateInput

| Class                              | Element         | Targets                        | Modifiers    |
| ---------------------------------- | --------------- | ------------------------------ | ------------ |
| `.GeckoUIDateInput`                | `Pressable`     | Input container (border, flex) | `--disabled` |
| `.GeckoUIDateInput__content`       | `View`          | Value/placeholder area         | —            |
| `.GeckoUIDateInput__value`         | `Text`          | Selected date display          | —            |
| `.GeckoUIDateInput__placeholder`   | `Text`          | Placeholder text               | —            |
| `.GeckoUIDateInput__icons`         | `View`          | Icons container (clear, arrow) | —            |
| `.GeckoUIDateInput__clear-button`  | `Pressable`     | Clear button                   | —            |
| `.GeckoUIDateInput__clear-icon`    | `Text`          | Clear icon (✕)                 | —            |
| `.GeckoUIDateInput__calendar-icon` | `Text`          | Arrow/calendar glyph (▾)       | —            |
| `.GeckoUIDateInput__dialog`        | `Animated.View` | Calendar popup dialog          | —            |
| `.GeckoUIDateInput__calendar`      | `View`          | Calendar inside the popup      | —            |

Built-in: `h-12`, `flex-row`, `items-center`, `gap-2`, `w-full`, `rounded-md`, `border`, `px-3`.

### DateRangeInput

| Class                                   | Element         | Targets                   | Modifiers    |
| --------------------------------------- | --------------- | ------------------------- | ------------ |
| `.GeckoUIDateRangeInput`                | `Pressable`     | Input container           | `--disabled` |
| `.GeckoUIDateRangeInput__content`       | `View`          | Value/placeholder area    | —            |
| `.GeckoUIDateRangeInput__value`         | `Text`          | Selected range display    | —            |
| `.GeckoUIDateRangeInput__placeholder`   | `Text`          | Placeholder text          | —            |
| `.GeckoUIDateRangeInput__icons`         | `View`          | Icons container           | —            |
| `.GeckoUIDateRangeInput__clear-button`  | `Pressable`     | Clear button              | —            |
| `.GeckoUIDateRangeInput__clear-icon`    | `Text`          | Clear icon (✕)            | —            |
| `.GeckoUIDateRangeInput__calendar-icon` | `Text`          | Arrow glyph (▾)           | —            |
| `.GeckoUIDateRangeInput__dialog`        | `Animated.View` | Calendar popup dialog     | —            |
| `.GeckoUIDateRangeInput__calendar`      | `View`          | Calendar inside the popup | —            |

Built-in: same as DateInput.

### Dialog

| Class                    | Element         | Targets         | Modifiers |
| ------------------------ | --------------- | --------------- | --------- |
| `.GeckoUIDialog__dialog` | `Animated.View` | The modal panel | —         |

Built-in: `bg-surface-primary`, `w-full`, `rounded-md`, `p-6`, `max-width: 400px`. The backdrop is a `Pressable` inside an `Animated.View` overlay (no class hook — `Dialog` does not expose `backdropClassName`).

### Drawer

| Class                    | Element         | Targets           | Modifiers                                |
| ------------------------ | --------------- | ----------------- | ---------------------------------------- |
| `.GeckoUIDrawer__drawer` | `Animated.View` | The sliding panel | `--right`, `--left`, `--top`, `--bottom` |

Built-in: `bg-surface-primary`, `shadow-xl`. Right/left placements get `h-full`, `w-80%`, `max-w-[400px]`. Top/bottom placements get `w-full`, `max-h-[60%]`.

### Input

| Class                  | Element     | Targets                                                             | Modifiers                 |
| ---------------------- | ----------- | ------------------------------------------------------------------- | ------------------------- |
| `.GeckoUIInput`        | `View`      | Outer wrapper (border, flex container with prefix + input + suffix) | `--focused`, `--disabled` |
| `.GeckoUIInput__input` | `TextInput` | The actual text input                                               | —                         |

Built-in: `h-12`, `flex-row`, `items-center`, `gap-2`, `w-full`, `rounded-md`, `border`, `px-3`.

### InputError

| Class                | Element | Targets            | Modifiers |
| -------------------- | ------- | ------------------ | --------- |
| `.GeckoUIInputError` | `Text`  | Error message text | —         |

Built-in: `text-sm`, `text-red-600`.

### Label

| Class                               | Element | Targets               | Modifiers |
| ----------------------------------- | ------- | --------------------- | --------- |
| `.GeckoUILabel`                     | `View`  | Outer label row       | —         |
| `.GeckoUILabel__text`               | `Text`  | Label text            | —         |
| `.GeckoUILabel__required-indicator` | `Text`  | Red asterisk (\*)     | —         |
| `.GeckoUILabel__tooltip-icon`       | `View`  | Help/info icon circle | —         |
| `.GeckoUILabel__tooltip-icon-glyph` | `Text`  | Icon glyph (!)        | —         |

Built-in: `flex-row`, `items-center`. Tooltip icon is a 14px circle with `bg-info`.

### LoadingButton

| Class                            | Element   | Targets                                                 | Modifiers                                                                     |
| -------------------------------- | --------- | ------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `.GeckoUILoadingButton__row`     | `View`    | Inner row (spinner + label)                             | —                                                                             |
| `.GeckoUILoadingButton__spinner` | `Spinner` | Spinner icon (only modifier classes are present in CSS) | `--filled-primary`, `--outlined-primary`, `--ghost-primary`, `--icon-primary` |

Note: `LoadingButton` renders a `<Button>` underneath, so its outer Pressable carries only `.GeckoUIButton`. The `--*` spinner color classes recolor the spinner via `text-*` (NativeWind picks up `currentColor`).

### Menu

| Class                       | Element     | Targets                              | Modifiers    |
| --------------------------- | ----------- | ------------------------------------ | ------------ |
| `.GeckoUIMenu`              | `View`      | Outer wrapper                        | —            |
| `.GeckoUIMenu__button`      | `Pressable` | Default trigger button               | —            |
| `.GeckoUIMenu__button-text` | `Text`      | Trigger button label                 | —            |
| `.GeckoUIMenu__items`       | `View`      | Floating dropdown panel (in `Modal`) | —            |
| `.GeckoUIMenu__item`        | `Pressable` | Individual menu action item          | `--disabled` |
| `.GeckoUIMenu__item-text`   | `Text`      | Item label text                      | `--disabled` |

Built-in panel: `bg-surface-primary`, `rounded-md`, `border`, `p-1`, `shadow-xl`. Built-in item: `px-3`, `py-3`, `rounded`, `active:bg-surface-hover`. The `__item--disabled` class is appended on disabled items but ships with no default style — only the inner `__item-text--disabled` has a built-in `text-text-disabled` rule.

### OTPInput

| Class                         | Element     | Targets                     | Modifiers                                               |
| ----------------------------- | ----------- | --------------------------- | ------------------------------------------------------- |
| `.GeckoUIOTPInput`            | `Pressable` | Grid container (cells row)  | `--disabled`                                            |
| `.GeckoUIOTPInput__cell`      | `View`      | Individual digit cell       | `--focused`, `--disabled`, `--error`, `--error-focused` |
| `.GeckoUIOTPInput__cell-text` | `Text`      | Digit/placeholder character | `--placeholder`                                         |
| `.GeckoUIOTPInput__input`     | `TextInput` | Hidden input capturing keys | —                                                       |

Built-in cell: `aspect-square`, `rounded-lg`, `border`, `max-width: 56px`.

### Pagination

| Class                                  | Element     | Targets                | Modifiers                |
| -------------------------------------- | ----------- | ---------------------- | ------------------------ |
| `.GeckoUIPagination`                   | `View`      | Pagination wrapper     | —                        |
| `.GeckoUIPagination__arrow`            | `Pressable` | Prev/next arrow button | `--disabled`             |
| `.GeckoUIPagination__arrow-icon`       | `Text`      | Arrow glyph (‹ ›)      | —                        |
| `.GeckoUIPagination__page-button`      | `Pressable` | Individual page number | `--active`               |
| `.GeckoUIPagination__page-button-text` | `Text`      | Page number text       | `--active`, `--ellipsis` |

Built-in: `flex-row`, `items-center`, `justify-center`, `gap-0.5`. Arrows/buttons are 44px tall.

### Radio

| Class                | Element     | Targets                         | Modifiers                 |
| -------------------- | ----------- | ------------------------------- | ------------------------- |
| `.GeckoUIRadio`      | `Pressable` | The radio circle (full element) | `--checked`, `--disabled` |
| `.GeckoUIRadio__dot` | `View`      | Inner filled dot when checked   | —                         |

Built-in: `h-5`, `w-5`, `rounded-full`, `border`, `items-center`, `justify-center`.

### Select

| Class                                                          | Element                      | Targets                                         | Modifiers                  |
| -------------------------------------------------------------- | ---------------------------- | ----------------------------------------------- | -------------------------- |
| `.GeckoUISelect`                                               | `View`                       | Outer wrapper                                   | —                          |
| `.GeckoUISelectButton`                                         | `Pressable`                  | The trigger/button area                         | `--disabled`               |
| `.GeckoUISelectButton__content`                                | `View`                       | Content area inside button                      | —                          |
| `.GeckoUISelectButton__value`                                  | `Text`                       | Selected value display                          | —                          |
| `.GeckoUISelectButton__placeholder`                            | `Text`                       | Placeholder text                                | —                          |
| `.GeckoUISelectButton__icons`                                  | `View`                       | Icons container (clear, arrow)                  | —                          |
| `.GeckoUISelectButton__clear-button`                           | `Pressable`                  | Clear selection button                          | —                          |
| `.GeckoUISelectButton__clear-icon`                             | `Text`                       | Clear icon (✕)                                  | —                          |
| `.GeckoUISelectButton__arrow`                                  | `Text`                       | Dropdown arrow glyph (▾)                        | —                          |
| `.GeckoUISelectButton__multiselected-chip`                     | `View`                       | Multi-select tag/chip                           | `--disabled`               |
| `.GeckoUISelectButton__multiselected-chip__label`              | `Text`                       | Chip label text                                 | —                          |
| `.GeckoUISelectButton__multiselected-chip__clear-button`       | `Pressable`                  | Remove chip button                              | —                          |
| `.GeckoUISelectButton__multiselected-chip__clear-button__icon` | `Text`                       | Remove chip icon (✕)                            | —                          |
| `.GeckoUISelectMenu__overlay`                                  | `View`                       | Full-screen overlay (z-1000)                    | —                          |
| `.GeckoUISelectMenu__backdrop`                                 | `Pressable`                  | Tap-out backdrop                                | —                          |
| `.GeckoUISelectMenu__container`                                | `View`                       | Bottom-sheet container                          | —                          |
| `.GeckoUISelectMenu`                                           | `View`                       | Bottom-sheet panel                              | —                          |
| `.GeckoUISelectMenu__list`                                     | `FlatList`                   | Scrollable options list                         | —                          |
| `.GeckoUISelectMenu__items`                                    | (FlatList content container) | List padding                                    | —                          |
| `.GeckoUISelectOption`                                         | `Pressable`                  | Individual option row                           | `--selected`, `--disabled` |
| `.GeckoUISelectOption__label`                                  | `Text`                       | Option label                                    | —                          |
| `.GeckoUISelectOption__check-icon`                             | `Text`                       | Check icon (✓) when selected                    | —                          |
| `.GeckoUISelectEmpty`                                          | `View`                       | Empty state message wrapper                     | —                          |
| `.GeckoUISelectEmpty__text`                                    | `Text`                       | Empty state text                                | —                          |
| `.GeckoUISelectDropdownSearch`                                 | `Input`                      | Dropdown search input (extends `.GeckoUIInput`) | —                          |
| `.GeckoUISelectDropdownSearch__wrapper`                        | `View`                       | Search input wrapper (px/mb)                    | —                          |
| `.GeckoUISelectDropdownSearch__icon`                           | `Text`                       | Search icon glyph (⌕)                           | —                          |

Built-in button: `min-h-12`, `flex-row`, `items-center`, `gap-2`, `w-full`, `rounded-md`, `border`, `px-3`. Bottom-sheet menu: `bg-surface-primary`, `rounded-t-xl`, `pt-3`.

### Spinner

| Class                 | Element             | Targets          | Modifiers |
| --------------------- | ------------------- | ---------------- | --------- |
| `.GeckoUISpinnerIcon` | `ActivityIndicator` | Animated spinner | —         |

Built-in: `text-primary-600` (mapped to the `ActivityIndicator` `color` prop via `cssInterop`).

### Switch

| Class                   | Element         | Targets                       | Modifiers                            |
| ----------------------- | --------------- | ----------------------------- | ------------------------------------ |
| `.GeckoUISwitch`        | `Pressable`     | Visual track (the pill shape) | `--sm`, `--md`, `--on`, `--disabled` |
| `.GeckoUISwitch__thumb` | `Animated.View` | Sliding thumb circle          | `--sm`, `--md`                       |

Built-in: `flex-row`, `items-center`, `justify-start`, `rounded-full`, `bg-surface-hover`, `px-0.5`. The on-state class `--on` flips bg to `bg-primary-600`.

### Textarea

| Class              | Element     | Targets                     | Modifiers |
| ------------------ | ----------- | --------------------------- | --------- |
| `.GeckoUITextarea` | `TextInput` | The textarea element itself | —         |

Built-in: `w-full`, `rounded-md`, `border`, `px-3`, `py-2`, `font-size: 16px`.

### Toast

| Class                         | Element     | Targets                             | Modifiers                                                  |
| ----------------------------- | ----------- | ----------------------------------- | ---------------------------------------------------------- |
| `.GeckoUIToast`               | `View`      | Toast card (inside `Animated.View`) | —                                                          |
| `.GeckoUIToast__icon`         | `View`      | Variant icon circle                 | `--default`, `--success`, `--error`, `--warning`, `--info` |
| `.GeckoUIToast__icon-glyph`   | `Text`      | Icon glyph character                | —                                                          |
| `.GeckoUIToast__body`         | `View`      | Title + description container       | —                                                          |
| `.GeckoUIToast__title`        | `Text`      | Title text                          | —                                                          |
| `.GeckoUIToast__description`  | `Text`      | Description text                    | —                                                          |
| `.GeckoUIToast__action`       | `Pressable` | Action button                       | —                                                          |
| `.GeckoUIToast__action-label` | `Text`      | Action label text                   | —                                                          |
| `.GeckoUIToast__dismiss`      | `Pressable` | Close button                        | —                                                          |
| `.GeckoUIToast__dismiss-icon` | `Text`      | Close icon (✕)                      | —                                                          |

Built-in card: `flex-row`, `items-center`, `gap-3`, `mx-3`, `mb-2`, `rounded-lg`, `border`, `px-4`, `py-3`.

### Tooltip

| Class                      | Element     | Targets                | Modifiers        |
| -------------------------- | ----------- | ---------------------- | ---------------- |
| `.GeckoUITooltip__trigger` | `Pressable` | Trigger wrapper        | —                |
| `.GeckoUITooltip`          | `View`      | Tooltip content bubble | —                |
| `.GeckoUITooltip__text`    | `Text`      | Tooltip text           | —                |
| `.GeckoUITooltip__arrow`   | `View`      | Arrow element          | `--up`, `--down` |

Built-in bubble: `rounded-md`, `px-3`, `py-2`, `bg-tooltip-bg`. Arrow uses CSS borders to draw triangle pointing up or down.
