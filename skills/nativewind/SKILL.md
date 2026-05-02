---
name: nativewind
description: Use this skill when the user asks about "@geckoui/nativewind", "GeckoUI for React Native", "Button", "Input", "Select", "Calendar", "DateInput", "DateRangeInput", "OTPInput", "Switch", "Checkbox", "Radio", "Dialog", "Drawer", "Toast", "Tooltip", "Pagination", "CounterInput", "Menu", "Alert", "Label", "InputError", "LoadingButton", "Spinner", "Textarea", "GeckoUIPortal", "DynamicComponentRenderer", "GeckoUI theming for RN", or needs to build React Native UIs with @geckoui/nativewind components.
---

# @geckoui/nativewind

React Native component library for Expo / bare RN apps. Tailwind CSS v3 + NativeWind v4 + class-based dark mode + a single shared portal for overlays.

## Setup

```sh
npx expo install nativewind react-native-safe-area-context
npx expo install --dev tailwindcss postcss-import
npm install @geckoui/nativewind
```

`tailwindcss` must be `^3` (NativeWind v4 does not support Tailwind v4).

### `tailwind.config.js`

```js
module.exports = {
  content: ["./App.tsx", "./app/**/*.{js,jsx,ts,tsx}"],
  presets: [require("nativewind/preset"), require("@geckoui/nativewind/preset")]
};
```

The GeckoUI preset safelists every `GeckoUI*` class — no `node_modules` entry needed in `content`.

### `global.css`

```css
@import "@geckoui/nativewind/styles.css";

@tailwind base;
@tailwind components;
@tailwind utilities;
```

### `babel.config.js`

```js
module.exports = function (api) {
  api.cache(true);
  return {
    presets: [["babel-preset-expo", { jsxImportSource: "nativewind" }], "nativewind/babel"]
  };
};
```

### `metro.config.js`

```js
const { getDefaultConfig } = require("expo/metro-config");
const { withNativeWind } = require("nativewind/metro");

module.exports = withNativeWind(getDefaultConfig(__dirname), {
  input: "./global.css"
});
```

### App entry — providers + portal

```tsx
import { GeckoUIPortal } from "@geckoui/nativewind";
import { SafeAreaProvider } from "react-native-safe-area-context";

import "./global.css";

export default function App() {
  return (
    <SafeAreaProvider>
      {/* your screens */}
      <GeckoUIPortal />
    </SafeAreaProvider>
  );
}
```

`<GeckoUIPortal />` mounts all overlay hosts — render it once at the app root. Internally it renders `<GeckoUIOverlayHosts />` (calendar, menu panel, select, toast, tooltip) plus `<DialogHost />` and `<DrawerHost />`.

## Override priority

```
style prop  >  className prop  >  CSS class in global.css  >  CSS variable
```

```tsx
{/* 1. style (single instance, RN-native, bypasses NativeWind) */}
<Button style={{ borderRadius: 999 }}>Pill</Button>

{/* 2. className (single instance, NativeWind utilities) */}
<Button className="bg-emerald-600">Save</Button>

{/* 3. global.css (every instance) */}
@layer components {
  .GeckoUIButton--filled-primary { @apply bg-emerald-600; }
}

{/* 4. CSS variable (re-themes the whole library) */}
:root { --color-primary-600: #16a34a; }
```

## Components

### Alert

```tsx
<Alert variant="error" title="Something went wrong" />
<Alert variant="success" title="Saved" description="Your changes are live." />
<Alert variant="warning" title="Almost full" onRemove={() => {}} />
```

| Prop            | Type                                                       | Default     |
| --------------- | ---------------------------------------------------------- | ----------- |
| `variant`       | `"default" \| "error" \| "warning" \| "info" \| "success"` | `"default"` |
| `title`         | `ReactNode`                                                | required    |
| `description`   | `ReactNode`                                                | -           |
| `icon`          | `ReactNode`                                                | -           |
| `iconClassName` | `string`                                                   | -           |
| `onRemove`      | `() => void`                                               | -           |
| `className`     | `string`                                                   | -           |
| `style`         | `StyleProp<ViewStyle>` (RN-only)                           | -           |

Extends `ViewProps` (minus `style`).

### Button

```tsx
<Button onPress={submit}>Continue</Button>
<Button variant="outlined" onPress={cancel}>Cancel</Button>
<Button variant="ghost" size="sm">Learn More</Button>
<Button variant="icon">×</Button>
<Button disabled>Disabled</Button>
```

| Prop             | Type                                          | Default     |
| ---------------- | --------------------------------------------- | ----------- |
| `variant`        | `"filled" \| "outlined" \| "ghost" \| "icon"` | `"filled"`  |
| `color`          | `"primary"` (extensible)                      | `"primary"` |
| `size`           | `"xs" \| "sm" \| "md" \| "lg" \| "xl"`        | `"md"`      |
| `children`       | `ReactNode`                                   | -           |
| `disabled`       | `boolean`                                     | -           |
| `className`      | `string`                                      | -           |
| `labelClassName` | `string`                                      | -           |
| `style`          | `StyleProp<ViewStyle>` (RN-only)              | -           |
| `labelStyle`     | `StyleProp<TextStyle>` (RN-only)              | -           |

Extends `PressableProps` (minus `style` / `children`). Tap handler is `onPress`, not `onClick`. The `icon` variant is fixed 32x32 and ignores `size`.

### Calendar

```tsx
// Single date
<Calendar selectedDate={date} onSelectDate={setDate} />

// Date range
<Calendar mode="range" selectedRange={range} onSelectRange={setRange} />

// Disable specific dates + custom day cell
<Calendar
  selectedDate={date}
  onSelectDate={setDate}
  disableDate={(d) => isWeekend(d)}
  renderDayCell={({ day, isSelected }) => <Text>{day}</Text>}
/>

// Imperative — jump to month / clear (`month` is 1-indexed: 1=Jan, 12=Dec)
const ref = useRef<CalendarRef>(null);
ref.current?.moveTo(3, 2026); // jumps to March 2026
ref.current?.clearSelection();
```

`Calendar` is a discriminated union by `mode`. The single mode uses `selectedDate` / `onSelectDate`; range mode uses `selectedRange` / `onSelectRange`.

| Prop            | Type                                              | Default    |
| --------------- | ------------------------------------------------- | ---------- |
| `mode`          | `"single" \| "range"`                             | `"single"` |
| `selectedDate`  | `string \| null` (single mode, ISO `YYYY-MM-DD`)  | -          |
| `onSelectDate`  | `(date: string) => void` (single mode)            | -          |
| `selectedRange` | `{ from, to? }` (range mode)                      | -          |
| `onSelectRange` | `(range: DateRange \| null) => void` (range mode) | -          |
| `disableDate`   | `(date: string) => boolean`                       | -          |
| `renderDayCell` | `(props: DayCellRenderProps) => ReactNode`        | -          |
| `calendarRef`   | `Ref<CalendarRef>` — `{ moveTo, clearSelection }` | -          |
| `className`     | `string`                                          | -          |
| `style`         | `StyleProp<ViewStyle>` (RN-only)                  | -          |

`DayCellRenderProps`: `{ day, month, year, date, isDisabled, isSelected, isFocusedMonth }`.

`Calendar` renders `<CalendarHeader>` + one of `<CalendarDayPicker>`, `<CalendarMonthPicker>`, `<CalendarYearPicker>` and lets the user drill from year to month to day. The sub-components are also exported individually for custom layouts.

**`CalendarHeader` props:** `header?`, `onClickHeader?`, `onClickLeftArrow?`, `onClickRightArrow?`.
**`CalendarDayPicker` props:** `mode?`, `activeMonth`, `activeYear`, `selectedDate?` / `selectedRange?`, `onSelectDate?` / `onSelectRange?`, `disableDate?`, `renderDayCell?`, header callbacks.
**`CalendarMonthPicker` props:** `activeMonth`, `activeYear`, `onSelectMonth?`, header callbacks.
**`CalendarYearPicker` props:** `activeYear`, `onSelectYear?`, header callbacks.

### Checkbox

```tsx
<Checkbox checked={agree} onChange={setAgree} />
<Checkbox checked="indeterminate" />
<Checkbox checked disabled />
```

| Prop        | Type                             | Default |
| ----------- | -------------------------------- | ------- |
| `checked`   | `boolean \| "indeterminate"`     | `false` |
| `onChange`  | `(checked: boolean) => void`     | -       |
| `disabled`  | `boolean`                        | -       |
| `className` | `string`                         | -       |
| `style`     | `StyleProp<ViewStyle>` (RN-only) | -       |

Extends `PressableProps` (minus `style` / `children` / `onPress`). `'indeterminate'` shows a horizontal dash. Has 12px hit slop built in.

### ConfirmDialog

Imperative — see [Imperative APIs](#imperative-apis) below.

```tsx
ConfirmDialog.show({
  title: "Delete invoice?",
  content: "This action cannot be undone.",
  confirmButtonLabel: "Delete",
  cancelButtonLabel: "Keep",
  onConfirm: async () => {
    await deleteInvoice();
    // Dialog auto-dismisses after the handler resolves.
  }
});
```

| Option                   | Type                                                  | Default    |
| ------------------------ | ----------------------------------------------------- | ---------- |
| `title`                  | `string`                                              | -          |
| `content`                | `ReactNode \| FC<ConfirmDialogContentProps>`          | -          |
| `confirmButtonLabel`     | `string`                                              | `"Ok"`     |
| `cancelButtonLabel`      | `string`                                              | `"Cancel"` |
| `onConfirm`              | `({ dismiss }) => void \| Promise<void>`              | -          |
| `onCancel`               | `({ dismiss }) => void \| Promise<void>`              | -          |
| `dismissOnEsc`           | `boolean` (Android hardware-back / iPad keyboard Esc) | `true`     |
| `dismissOnOutsideClick`  | `boolean`                                             | `true`     |
| `className`              | `string` (dialog panel)                               | -          |
| `titleClassName`         | `string`                                              | -          |
| `contentClassName`       | `string`                                              | -          |
| `confirmButtonClassName` | `string`                                              | -          |
| `cancelButtonClassName`  | `string`                                              | -          |

If `onConfirm` returns a promise, the confirm button shows a spinner and the cancel button is disabled until it resolves (and vice versa for `onCancel`).

### CounterInput

```tsx
<CounterInput value={qty} onChange={setQty} min={0} max={99} />
<CounterInput value={qty} onChange={setQty} editable size="lg" />
```

| Prop              | Type                                | Default     |
| ----------------- | ----------------------------------- | ----------- |
| `value`           | `number`                            | required    |
| `onChange`        | `(value: number) => void`           | required    |
| `min`             | `number`                            | `-Infinity` |
| `max`             | `number`                            | `Infinity`  |
| `step`            | `number`                            | `1`         |
| `size`            | `"sm" \| "md" \| "lg"` (extensible) | `"md"`      |
| `disabled`        | `boolean`                           | -           |
| `readOnly`        | `boolean`                           | -           |
| `editable`        | `boolean` (allow typing)            | `false`     |
| `className`       | `string`                            | -           |
| `style`           | `StyleProp<ViewStyle>` (RN-only)    | -           |
| `inputClassName`  | `string` (value field)              | -           |
| `inputStyle`      | `StyleProp<TextStyle>` (RN-only)    | -           |
| `buttonClassName` | `string` (+ / − buttons)            | -           |
| `buttonStyle`     | `StyleProp<ViewStyle>` (RN-only)    | -           |

Numeric keyboard, automatic clamping to `[min, max]`. When `editable={false}` (default) the field shows the value but blocks typing.

### DateInput

```tsx
<DateInput value={date} onChange={setDate} format="DD/MM/YYYY" />
<DateInput value={date} onChange={setDate} disabled />
<DateInput
  value={date}
  onChange={setDate}
  disableDate={(d) => isWeekend(d)}
  placeholder="Pick a date"
/>
```

| Prop                   | Type                                           | Default         |
| ---------------------- | ---------------------------------------------- | --------------- |
| `value`                | `string \| null` (ISO `YYYY-MM-DD`)            | -               |
| `onChange`             | `(date: string \| null) => void`               | -               |
| `format`               | `"DD/MM/YYYY" \| "MM/DD/YYYY" \| "YYYY-MM-DD"` | `"DD/MM/YYYY"`  |
| `placeholder`          | `string`                                       | `"Select date"` |
| `disabled`             | `boolean`                                      | -               |
| `clearable`            | `boolean`                                      | `true`          |
| `hideClearIcon`        | `boolean`                                      | -               |
| `hideCalendarIcon`     | `boolean`                                      | -               |
| `disableDate`          | `(date: string) => boolean`                    | -               |
| `prefix`               | `ReactNode`                                    | -               |
| `suffix`               | `ReactNode`                                    | -               |
| `placeholderClassName` | `string`                                       | -               |
| `calendarClassName`    | `string` (Calendar inside the dialog)          | -               |
| `dialogClassName`      | `string` (dialog surface)                      | -               |
| `className`            | `string` (input row)                           | -               |
| `style`                | `StyleProp<ViewStyle>` (RN-only)               | -               |

Tapping the input opens `Dialog.show(...)` with a single-mode `<Calendar>` inside. Selection delays dialog dismiss by 100ms so the highlight settles before the close animation.

### DateRangeInput

```tsx
<DateRangeInput value={range} onChange={setRange} />
<DateRangeInput value={range} onChange={setRange} separator=" to " />
```

| Prop                   | Type                                           | Default               |
| ---------------------- | ---------------------------------------------- | --------------------- |
| `value`                | `DateRange \| null`                            | -                     |
| `onChange`             | `(range: DateRange \| null) => void`           | -                     |
| `format`               | `"DD/MM/YYYY" \| "MM/DD/YYYY" \| "YYYY-MM-DD"` | `"DD/MM/YYYY"`        |
| `separator`            | `string`                                       | `" — "`               |
| `placeholder`          | `string`                                       | `"Select date range"` |
| `placeholderTo`        | `string` (shown for "to" while picking)        | `"End date"`          |
| `disabled`             | `boolean`                                      | -                     |
| `clearable`            | `boolean`                                      | `true`                |
| `hideClearIcon`        | `boolean`                                      | -                     |
| `hideCalendarIcon`     | `boolean`                                      | -                     |
| `disableDate`          | `(date: string) => boolean`                    | -                     |
| `prefix`               | `ReactNode`                                    | -                     |
| `suffix`               | `ReactNode`                                    | -                     |
| `placeholderClassName` | `string`                                       | -                     |
| `calendarClassName`    | `string`                                       | -                     |
| `dialogClassName`      | `string`                                       | -                     |
| `className`            | `string`                                       | -                     |
| `style`                | `StyleProp<ViewStyle>` (RN-only)               | -                     |

`DateRange = { from: string \| null; to?: string \| null }`. Dialog auto-dismisses 100ms after the second date is picked.

### Dialog

Imperative — see [Imperative APIs](#imperative-apis).

```tsx
Dialog.show({
  content: ({ dismiss }) => (
    <View>
      <Text>Hello</Text>
      <Button onPress={dismiss}>Close</Button>
    </View>
  ),
  className: "max-w-sm"
});
```

| Option                  | Type                              | Default |
| ----------------------- | --------------------------------- | ------- |
| `content`               | `ReactNode \| FC<{ dismiss }>`    | -       |
| `dismissOnEsc`          | `boolean` (Android hardware back) | `true`  |
| `dismissOnOutsideClick` | `boolean`                         | `true`  |
| `className`             | `string` (dialog panel)           | -       |
| `style`                 | `StyleProp<ViewStyle>` (RN-only)  | -       |

**Built-in panel styles:** `bg-surface-primary`, `rounded-md`, `p-6`, `w-full`, `max-width: 400px`. Backdrop is `rgba(0,0,0,0.5)`.

Spring scale-in (0.95 → 1) + 180ms fade. Dismiss is 150ms.

### Drawer

Both declarative and imperative.

```tsx
{
  /* Declarative */
}
<Drawer open={isOpen} handleClose={() => setOpen(false)} placement="right">
  <View className="p-6">…</View>
</Drawer>;

{
  /* Imperative */
}
Drawer.show(<Filters />, { placement: "bottom" });
Drawer.dismiss();
```

| Prop                     | Type                                     | Default   |
| ------------------------ | ---------------------------------------- | --------- |
| `open`                   | `boolean`                                | required  |
| `handleClose`            | `() => void`                             | -         |
| `placement`              | `"top" \| "bottom" \| "left" \| "right"` | `"right"` |
| `hideBackdrop`           | `boolean`                                | `false`   |
| `dismissOnBackdropPress` | `boolean` (tap backdrop to close)        | `true`    |
| `dismissOnEscape`        | `boolean` (hardware back)                | `true`    |
| `children`               | `ReactNode`                              | -         |
| `backdropClassName`      | `string`                                 | -         |
| `backdropStyle`          | `StyleProp<ViewStyle>` (RN-only)         | -         |
| `className`              | `string` (panel)                         | -         |
| `style`                  | `StyleProp<ViewStyle>` (RN-only)         | -         |

**Built-in panel styles:** `bg-surface-primary`, `shadow-xl`. Left/right are `width: 80%` with `max-width: 400px` and full height; top/bottom are full width with `max-height: 60%`.

300ms slide animation, 250ms backdrop fade.

### DynamicComponentRenderer

Internal helper, also exported. Use it for any prop typed as `string | ReactNode | FC`.

```tsx
<DynamicComponentRenderer component={title} className="text-base" />
<DynamicComponentRenderer component={IconComponent} />
<DynamicComponentRenderer component={<Custom />} />
```

| Prop        | Type                                                 | Default |
| ----------- | ---------------------------------------------------- | ------- |
| `component` | `FC<T> \| ReactNode \| string \| number`             | -       |
| `className` | `string` (only used when `component` is a primitive) | -       |
| `style`     | `StyleProp<TextStyle>`                               | -       |

Strings/numbers are wrapped in `<Text>`; functions are invoked with the remaining props; nodes pass through. Returns `null` for `''`, `null`, or `undefined`.

### GeckoUIPortal

```tsx
<SafeAreaProvider>
  {/* app */}
  <GeckoUIPortal />
</SafeAreaProvider>
```

No props. Composes `<GeckoUIOverlayHosts />` + `<DialogHost />` + `<DrawerHost />`. Render exactly once at the app root, ABOVE the screens that call `Dialog.show()` / `Toast.success()` / etc.

### GeckoUIOverlayHosts

Mounts the overlay hosts that must live inside every `Modal` layer: `CalendarPickerHost`, `MenuPanelHost`, `SelectMenuHost`, `ToastHost`, `TooltipHost`. Already embedded automatically inside `<Drawer>` and `<DialogHost>`.

**Use this when you have a custom `Modal`** — drop it inside the modal so that `DateInput`, `Select`, `Menu`, `Toast`, and `Tooltip` all render above it:

```tsx
import { GeckoUIOverlayHosts } from "@geckoui/nativewind";
import { Modal } from "react-native";

<Modal visible={open} transparent statusBarTranslucent>
  {/* your modal content — DateInput, Select, Menu etc. work here */}
  <GeckoUIOverlayHosts />
</Modal>;
```

Each host uses a stack — mounting `GeckoUIOverlayHosts` inside a `Modal` pushes inner hosts to the top of the stack, so all overlays route to the correct layer. When the `Modal` closes the hosts unmount and the root-level hosts take back control automatically.

### Input

```tsx
<Input placeholder="Email" value={email} onChangeText={setEmail} />
<Input prefix={<Text>$</Text>} suffix={<Text>USD</Text>} />
<Input editable={false} value="readonly" />
```

| Prop             | Type                                      | Default |
| ---------------- | ----------------------------------------- | ------- |
| `prefix`         | `ReactNode` (left of TextInput)           | -       |
| `suffix`         | `ReactNode` (right of TextInput)          | -       |
| `editable`       | `boolean` (RN-native; `false` = disabled) | `true`  |
| `className`      | `string` (outer wrapper)                  | -       |
| `inputClassName` | `string` (TextInput)                      | -       |
| `style`          | `StyleProp<ViewStyle>` (RN-only)          | -       |
| `inputStyle`     | `StyleProp<TextStyle>` (RN-only)          | -       |

Extends `TextInputProps` (minus `style`). Forwards a `TextInput` ref.

**Disabled state** is driven by `editable={false}` — that flips on `GeckoUIInput--disabled`. Focus state flips on `GeckoUIInput--focused`.

**Note:** Do NOT use Tailwind `text-*` utilities (e.g. `text-sm`) on the TextInput — they include `line-height` which breaks RN layout. The default size is `text-base`; for custom sizes use `inputStyle={{ fontSize: 14 }}`.

### InputError

```tsx
<InputError>Email is required</InputError>
```

| Prop        | Type                             | Default |
| ----------- | -------------------------------- | ------- |
| `children`  | `ReactNode`                      | -       |
| `className` | `string`                         | -       |
| `style`     | `StyleProp<TextStyle>` (RN-only) | -       |

Extends `TextProps`. Default: `text-sm text-red-600`.

### Label

```tsx
<Label required>Email</Label>
<Label tooltip="We'll never share this">Email</Label>
<Label tooltip={<MyTip />} tooltipIcon={<MyIcon />}>Email</Label>
```

| Prop               | Type                             | Default |
| ------------------ | -------------------------------- | ------- |
| `children`         | `ReactNode`                      | -       |
| `required`         | `boolean` (red asterisk)         | -       |
| `tooltip`          | `string \| ReactNode`            | -       |
| `tooltipIcon`      | `string \| ReactNode`            | -       |
| `tooltipClassName` | `string`                         | -       |
| `textClassName`    | `string` (inner Text)            | -       |
| `textStyle`        | `StyleProp<TextStyle>` (RN-only) | -       |
| `className`        | `string` (outer View)            | -       |
| `style`            | `StyleProp<ViewStyle>` (RN-only) | -       |

Extends `TextProps` (minus `style` / `children`).

### LoadingButton

Wraps `Button`. While `loading`, the button is disabled and shows a `Spinner`.

```tsx
<LoadingButton loading={isSubmitting} onPress={submit}>Save</LoadingButton>
<LoadingButton loading loadingText="Saving…" spinnerPosition="end">Save</LoadingButton>
```

| Prop               | Type               | Default   |
| ------------------ | ------------------ | --------- |
| `loading`          | `boolean`          | -         |
| `loadingText`      | `string`           | -         |
| `spinnerPosition`  | `"start" \| "end"` | `"start"` |
| `spinnerClassName` | `string`           | -         |

Extends `ButtonProps`. Inherits `variant`, `color`, `size`, `disabled`, `className`, etc. Spinner color follows the variant (filled gets `text-text-on-primary`, outlined gets `text-text-primary`, ghost gets `text-primary-600`, icon gets `text-text-tertiary`).

### Menu

```tsx
{
  /* Default trigger */
}
<Menu label="Actions">
  <MenuItem onClick={edit}>Edit</MenuItem>
  <MenuItem onClick={archive}>Archive</MenuItem>
  <MenuItem disabled>Delete</MenuItem>
</Menu>;

{
  /* Custom trigger */
}
<Menu>
  <MenuTrigger>{({ toggleMenu }) => <Button onPress={toggleMenu}>Options</Button>}</MenuTrigger>
  <MenuItem onClick={edit}>Edit</MenuItem>
</Menu>;
```

`<Menu>` props:

| Prop              | Type                              | Default |
| ----------------- | --------------------------------- | ------- |
| `label`           | `string` (default trigger text)   | -       |
| `disabled`        | `boolean`                         | `false` |
| `children`        | `ReactNode`                       | -       |
| `className`       | `string` (outer View)             | -       |
| `style`           | `StyleProp<ViewStyle>` (RN-only)  | -       |
| `menuClassName`   | `string` (dropdown panel)         | -       |
| `buttonClassName` | `string` (default trigger button) | -       |

`<MenuItem>` props:

| Prop        | Type         | Default |
| ----------- | ------------ | ------- |
| `disabled`  | `boolean`    | -       |
| `onClick`   | `() => void` | -       |
| `children`  | `ReactNode`  | -       |
| `className` | `string`     | -       |

`<MenuTrigger>` is a render-prop child receiving `{ open, openMenu, closeMenu, toggleMenu, disabled }`.

`useMenu()` exposes the same context inside any descendant.

### OTPInput

```tsx
<OTPInput value={otp} onChange={setOtp} length={6} onOTPComplete={verify} />
<OTPInput value={otp} onChange={setOtp} numberOnly={false} />
```

| Prop             | Type                             | Default  |
| ---------------- | -------------------------------- | -------- |
| `value`          | `string`                         | required |
| `onChange`       | `(value: string) => void`        | required |
| `onOTPComplete`  | `(value: string) => void`        | -        |
| `onBlur`         | `TextInputProps['onBlur']`       | -        |
| `length`         | `number`                         | `6`      |
| `numberOnly`     | `boolean`                        | `true`   |
| `disabled`       | `boolean`                        | -        |
| `className`      | `string` (cell container)        | -        |
| `style`          | `StyleProp<ViewStyle>` (RN-only) | -        |
| `cellClassName`  | `string` (each cell View)        | -        |
| `cellStyle`      | `StyleProp<ViewStyle>` (RN-only) | -        |
| `inputClassName` | `string` (hidden TextInput)      | -        |
| `inputStyle`     | `StyleProp<ViewStyle>` (RN-only) | -        |

A single hidden `TextInput` collects keystrokes; the visible cells are display-only. SMS OTP auto-fill works on iOS (via `textContentType="oneTimeCode"`) and Android (via `autoComplete="sms-otp"`). `onOTPComplete` fires once when the last cell is filled.

### Pagination

```tsx
<Pagination currentPage={page} totalPages={42} onChange={setPage} />
```

| Prop          | Type                             | Default  |
| ------------- | -------------------------------- | -------- |
| `currentPage` | `number`                         | required |
| `totalPages`  | `number`                         | required |
| `onChange`    | `(page: number) => void`         | required |
| `className`   | `string`                         | -        |
| `style`       | `StyleProp<ViewStyle>` (RN-only) | -        |

Returns `null` when `totalPages <= 1`. Shows ellipsis when more than 7 pages.

### Radio

```tsx
<Radio checked={plan === 'free'} onChange={() => setPlan('free')} />
<Radio checked={plan === 'pro'} onChange={() => setPlan('pro')} />
```

| Prop        | Type                             | Default |
| ----------- | -------------------------------- | ------- |
| `checked`   | `boolean`                        | `false` |
| `onChange`  | `(checked: boolean) => void`     | -       |
| `disabled`  | `boolean`                        | -       |
| `className` | `string`                         | -       |
| `style`     | `StyleProp<ViewStyle>` (RN-only) | -       |

Extends `PressableProps` (minus `style` / `children` / `onPress`). `Radio` is a single circle; group selection logic is up to the consumer (typically via a `value` state).

### Select

```tsx
{
  /* Single */
}
<Select value={country} onChange={setCountry} placeholder="Country" filterable>
  <SelectOption value="us" label="United States" />
  <SelectOption value="uk" label="United Kingdom" />
</Select>;

{
  /* Multi */
}
<Select multiple value={tags} onChange={setTags} clearable>
  <SelectOption value="red" label="Red" />
  <SelectOption value="blue" label="Blue" />
</Select>;

{
  /* Custom trigger */
}
<Select value={country} onChange={setCountry}>
  <SelectTrigger>
    {({ selectedOptions, toggleMenu }) => (
      <Button onPress={toggleMenu}>{selectedOptions?.label ?? "Pick"}</Button>
    )}
  </SelectTrigger>
  <SelectOption value="us" label="US" />
</Select>;
```

`<Select>` props:

| Prop                   | Type                                  | Default                          |
| ---------------------- | ------------------------------------- | -------------------------------- |
| `value`                | `T` (single) / `T[]` (multi)          | required                         |
| `onChange`             | `(v: T) => void` / `(v: T[]) => void` | required                         |
| `multiple`             | `boolean`                             | `false`                          |
| `placeholder`          | `string`                              | `"Select option"`                |
| `disabled`             | `boolean`                             | -                                |
| `filterable`           | `boolean`                             | -                                |
| `closeMenuOnSelect`    | `boolean`                             | `true` (single), `false` (multi) |
| `clearable`            | `boolean`                             | `false`                          |
| `hideDefaultEmptyUI`   | `boolean`                             | -                                |
| `prefix`               | `ReactNode`                           | -                                |
| `suffix`               | `ReactNode`                           | -                                |
| `wrapperClassName`     | `string` (outer wrapper)              | -                                |
| `wrapperStyle`         | `StyleProp<ViewStyle>` (RN-only)      | -                                |
| `buttonClassName`      | `string` (default trigger button)     | -                                |
| `menuClassName`        | `string` (bottom-sheet menu surface)  | -                                |
| `menuStyle`            | `StyleProp<ViewStyle>` (RN-only)      | -                                |
| `placeholderClassName` | `string`                              | -                                |
| `children`             | `ReactNode`                           | -                                |

The menu opens as a bottom sheet (full-width sheet at the bottom of the screen, animated via `<SelectMenuHost />` inside `<GeckoUIPortal />`). When the keyboard is open the sheet auto-resizes.

`<SelectOption>` props:

| Prop            | Type                                                                                                               | Default     |
| --------------- | ------------------------------------------------------------------------------------------------------------------ | ----------- |
| `value`         | `T`                                                                                                                | required    |
| `label`         | `string`                                                                                                           | required    |
| `hideCheckIcon` | `boolean`                                                                                                          | -           |
| `disabled`      | `boolean`                                                                                                          | -           |
| `visibility`    | `"default" \| "always" \| "empty" \| "filtered-and-empty"`                                                         | `"default"` |
| `className`     | `string \| (props: { value, selected, focused }) => string`                                                        | -           |
| `children`      | `ReactNode \| (props: { value, selected, focused, selectCurrentOption, closeMenu, filteredKeyword }) => ReactNode` | -           |
| `onClick`       | `(args: CustomSelectOptionArgs) => void` (single tap; `preventDefault` to skip default selection)                  | -           |
| `onRemove`      | `(args: CustomSelectOptionArgs) => void` (multi remove)                                                            | -           |

`<SelectButton>` is the default trigger (rendered automatically; props: `className`, `prefix`, `suffix`).

`<SelectMenu>` props: `children`, `className`, `style` (rendered automatically inside `<Select>`).

`<SelectDropdownSearch>` props: `placeholder` (default `"Search options..."`), `className`, `wrapperClassName` (rendered automatically when `filterable`; place a custom one as a child of `<Select>` to override).

`<SelectEmpty>` props: `children` (default `"No options"`), `className` (renders only when no options match the keyword; place as child of `<Select>` to override default).

`<SelectTrigger>`: render-prop child for fully custom triggers. The render fn receives `{ keyword, selectedOptions, hasValue, options, filteredOptions, handleSearchChange, handleChange, toggleMenu, open, openMenu, closeMenu }`. `selectedOptions` is `{ label, value }` for single mode and `{ label, value }[]` for multi (controlled by the `multiple` prop on `<SelectTrigger>`).

`<SelectConsumer render={(ctx) => …} />`: consumer-style access to the full `SelectContextProps`. `useSelect<T>()` is the hook equivalent.

### Spinner

```tsx
<Spinner />
<Spinner size="large" className="text-red-500" />
```

| Prop        | Type                             | Default   |
| ----------- | -------------------------------- | --------- |
| `size`      | `"small" \| "large" \| number`   | `"small"` |
| `className` | `string`                         | -         |
| `style`     | `StyleProp<ViewStyle>` (RN-only) | -         |

Extends `ActivityIndicatorProps` (minus `style` / `size`). Spinner color is the `text-*` utility (set via NativeWind cssInterop). Default: `text-primary-600`.

### Switch

```tsx
<Switch checked={notif} onChange={setNotif} />
<Switch checked={notif} onChange={setNotif} size="sm" />
<Switch checked disabled />
```

| Prop             | Type                             | Default |
| ---------------- | -------------------------------- | ------- |
| `checked`        | `boolean`                        | `false` |
| `onChange`       | `(value: boolean) => void`       | -       |
| `size`           | `"sm" \| "md"` (extensible)      | `"md"`  |
| `disabled`       | `boolean`                        | -       |
| `className`      | `string` (track)                 | -       |
| `thumbClassName` | `string`                         | -       |
| `style`          | `StyleProp<ViewStyle>` (RN-only) | -       |
| `thumbStyle`     | `StyleProp<ViewStyle>` (RN-only) | -       |

Extends `PressableProps` (minus `style` / `children` / `onPress`). 12px hit slop. 150ms thumb animation.

### Textarea

```tsx
<Textarea placeholder="Message" value={msg} onChangeText={setMsg} />
<Textarea rows={6} />
```

| Prop        | Type                             | Default |
| ----------- | -------------------------------- | ------- |
| `rows`      | `number`                         | `2`     |
| `className` | `string`                         | -       |
| `style`     | `StyleProp<TextStyle>` (RN-only) | -       |

Extends `TextInputProps` (minus `style`). Renders a multiline `TextInput` with `textAlignVertical="top"`. Height = `rows * 22 + 16`.

### Toast

Imperative — see [Imperative APIs](#imperative-apis).

```tsx
import { Toast } from "@geckoui/nativewind";

Toast.success("Saved");
Toast.error("Failed", { description: "Try again" });
Toast.warning("Almost full");
Toast.info("Heads up");
Toast.show({ title: "Custom", variant: "info", duration: 5000 });
Toast.dismiss("id-1");
Toast.dismiss(); // dismiss all
```

| Option        | Type                                                       | Default     |
| ------------- | ---------------------------------------------------------- | ----------- |
| `id`          | `string` (replaces existing toast with same id)            | auto        |
| `title`       | `string`                                                   | -           |
| `description` | `string`                                                   | -           |
| `variant`     | `"default" \| "success" \| "error" \| "warning" \| "info"` | `"default"` |
| `duration`    | `number` ms (`0` or `Infinity` = sticky)                   | `3000`      |
| `action`      | `{ label: string; onPress: () => void }`                   | -           |
| `closable`    | `boolean` (show ✕)                                         | `true`      |
| `hideIcon`    | `boolean`                                                  | `false`     |
| `onDismiss`   | `() => void`                                               | -           |
| `className`   | `string`                                                   | -           |
| `style`       | `StyleProp<ViewStyle>` (RN-only)                           | -           |

`<ToastHost>` is mounted by `<GeckoUIPortal />`. To configure stack position or render custom toasts, mount `<ToastHost position="bottom" renderToast={…} />` directly instead of (or in addition to) `<GeckoUIPortal />`.

| `<ToastHost>` prop | Type                                                       | Default |
| ------------------ | ---------------------------------------------------------- | ------- |
| `position`         | `"top" \| "bottom"`                                        | `"top"` |
| `renderToast`      | `(toast: ToastInstance, dismiss: () => void) => ReactNode` | -       |

Default toasts have swipe-to-dismiss (vertical) and respect safe-area insets.

### Tooltip

```tsx
<Tooltip content="Helpful text" placement="top">
  <Text>Tap me</Text>
</Tooltip>;

{
  /* triggerAsChild — clones onto a Pressable child instead of wrapping */
}
<Tooltip content="Save changes" triggerAsChild>
  <Button onPress={save}>Save</Button>
</Tooltip>;

{
  /* Long-press only, auto-dismiss after 2s */
}
<Tooltip content="Long press info" longPress duration={2000}>
  <Text>Hold me</Text>
</Tooltip>;
```

| Prop               | Type                                  | Default  |
| ------------------ | ------------------------------------- | -------- |
| `content`          | `ReactNode` (string auto-wrapped)     | required |
| `children`         | `ReactNode` (trigger)                 | required |
| `placement`        | `"top" \| "bottom" \| "auto"`         | `"auto"` |
| `disabled`         | `boolean`                             | -        |
| `longPress`        | `boolean`                             | `false`  |
| `duration`         | `number` ms (0 = manual dismiss)      | `0`      |
| `triggerAsChild`   | `boolean`                             | `false`  |
| `contentClassName` | `string`                              | -        |
| `contentStyle`     | `StyleProp<ViewStyle>` (RN-only)      | -        |
| `className`        | `string` (trigger wrapper)            | -        |
| `style`            | `StyleProp<ViewStyle>` (RN-only)      | -        |
| `hitSlop`          | `PressableProps['hitSlop']` (RN-only) | -        |

Tap toggles open/close. Only one tooltip is open at a time. `triggerAsChild` injects ref + press handlers onto the child instead of wrapping in a `<Pressable>` — use this when the child is already pressable to avoid nested Pressables.

## Imperative APIs

`Dialog`, `ConfirmDialog`, `Drawer`, and `Toast` expose imperative methods. They require `<GeckoUIPortal />` (or the individual `*Host` component) to be mounted.

### `Dialog.show(options)` / `Dialog.dismiss()`

```tsx
import { Dialog } from "@geckoui/nativewind";

Dialog.show({
  content: ({ dismiss }) => <Sheet onDone={dismiss} />,
  className: "max-w-md"
});

Dialog.dismiss();
```

Only one dialog is open at a time — calling `show` while one is open replaces it. Animated dismiss (150ms scale-out) before unmount.

### `ConfirmDialog.show(options)` / `ConfirmDialog.dismiss()`

```tsx
import { ConfirmDialog } from "@geckoui/nativewind";

ConfirmDialog.show({
  title: "Sign out?",
  content: "You will be returned to the login screen.",
  confirmButtonLabel: "Sign out",
  onConfirm: ({ dismiss }) => {
    signOut();
    dismiss();
  }
});
```

Built on `Dialog.show`. If `onConfirm` / `onCancel` returns a Promise, the corresponding button shows a spinner until it resolves; the other button is disabled meanwhile. The dialog dismisses automatically after either handler returns.

### `Drawer.show(node, options)` / `Drawer.dismiss()`

```tsx
import { Drawer } from "@geckoui/nativewind";

Drawer.show(<Filters />, { placement: "bottom" });
Drawer.dismiss();
```

Imperative drawer reuses the declarative props — pass `placement`, `hideBackdrop`, `dismissOnBackdropPress`, `dismissOnEscape`, `className`, `backdropClassName`, etc. via the `options` arg.

### `Toast.success / .error / .warning / .info / .show / .dismiss`

```tsx
import { Toast } from "@geckoui/nativewind";

const id = Toast.show({
  title: "Uploading…",
  duration: Infinity,
  closable: false
});

// Replace by passing the same id
Toast.show({ id, title: "Uploaded", variant: "success", duration: 3000 });

// Dismiss specific toast
Toast.dismiss(id);

// Dismiss all
Toast.dismiss();
```

`Toast.success(title, options?)` / `error` / `warning` / `info` are shorthands for `Toast.show({ title, variant, ...options })`.

## Module augmentation

Extend the built-in variant/color/size maps:

```tsx
declare module "@geckoui/nativewind" {
  interface ButtonColorMap {
    secondary: unknown;
    danger: unknown;
  }

  interface CounterInputSizeMap {
    xl: unknown;
  }
}
```

Then add the matching CSS in your `global.css`:

```css
@layer components {
  .GeckoUIButton[data-variant="filled"].GeckoUIButton--filled-danger {
    @apply bg-red-600;
  }
  .GeckoUIButton__label--filled-danger {
    @apply text-text-on-primary;
  }
}
```

**Extensible interfaces:** `ButtonVariantMap`, `ButtonColorMap`, `ButtonSizeMap`, `AlertVariantMap`, `SwitchSizeMap`, `CounterInputSizeMap`, `DrawerPlacementMap`.

## Styling

Every component exposes a BEM root class (e.g. `GeckoUIButton`) plus modifier and element classes. Override globally from `global.css`:

```css
/* global.css */
@layer components {
  .GeckoUIButton--filled-primary {
    @apply border-emerald-600 bg-emerald-600;
  }
}
```

Override priority is `style` > `className` > CSS class in `global.css` > CSS variable (see [Override priority](#override-priority) above for the per-instance vs. global trade-off).

For the complete CSS variable reference (all tokens, light + dark), see `references/theming.md`.

## RN-specific notes

- **Tap handler is `onPress`**, not `onClick` (except inside `<MenuItem>` and `<SelectOption>` which use `onClick` for cross-codebase parity with the web `geckoui` package).
- **Disabled inputs** flip via `editable={false}` (Input) / `disabled` (everything else).
- **Tailwind `text-*` utilities** include `line-height` — never use them on `TextInput`. Use `inputStyle={{ fontSize: 14 }}` instead. (`Text`/`View` are unaffected.)
- **`hitSlop`** is the RN expansion-of-touchable-area prop, available on `Tooltip`, `Checkbox` (12px built-in), `Radio` (12px built-in), `Switch` (12px built-in).
- **Dark mode**: class-based (`darkMode: 'class'`). Toggle the `dark` className on a parent `<View>`.
- **Imperative components** require `<GeckoUIPortal />` mounted in the tree (typically inside `<SafeAreaProvider>`).
- **Forms**: For React Hook Form integration, see `@geckoui/nativewind-rhf` (separate package).

## References

- `references/theming.md` — Complete CSS variable reference (light + dark, all tokens).
