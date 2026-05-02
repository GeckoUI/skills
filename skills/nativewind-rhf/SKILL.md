---
name: nativewind-rhf
description: Use this skill when the user asks about "@geckoui/nativewind-rhf", "React Hook Form for React Native", "RHFInput", "RHFSelect", "RHFCheckbox", "RHFRadio", "RHFSwitch", "RHFTextarea", "RHFDateInput", "RHFDateRangeInput", "RHFOTPInput", "RHFCounterInput", "RHFNumberInput", "RHFCurrencyInput", "RHFController", "RHFError", "RHFInputGroup", "form validation in React Native", or needs to wire React Hook Form to @geckoui/nativewind components.
---

# @geckoui/nativewind-rhf

React Hook Form wrappers for `@geckoui/nativewind` base components. Each wrapper binds its base component to an RHF `Controller`, forwards remaining props through, and exposes `--error` BEM class hooks so consumers can style invalid states from `global.css`.

For the underlying base components (their full prop surface, sizes, variants, etc.) see the **`nativewind` skill** (install separately if not already). This skill only documents the RHF-specific delta on top.

## Setup

```bash
pnpm add @geckoui/nativewind-rhf @geckoui/nativewind react-hook-form
```

In your `global.css`, import the base stylesheet **before** the RHF stylesheet — import order matters because the RHF error classes layer on top of the base component styles:

```css
@import "tailwindcss";
@import "nativewind/preflight.css";

@import "@geckoui/nativewind/styles.css"; /* base components — must come first */
@import "@geckoui/nativewind-rhf/styles.css"; /* RHF error overrides — later wins */
```

Wrap your form in RHF's `FormProvider` (or pass `control` explicitly to every wrapper):

```tsx
import { Button } from "@geckoui/nativewind";
import { RHFError, RHFInput } from "@geckoui/nativewind-rhf";
import { FormProvider, useForm } from "react-hook-form";

function SignupForm() {
  const methods = useForm({ defaultValues: { email: "" } });

  return (
    <FormProvider {...methods}>
      <RHFInput name="email" placeholder="Email" rules={{ required: "Email is required" }} />
      <RHFError name="email" />
      <Button onPress={methods.handleSubmit(onSubmit)}>Submit</Button>
    </FormProvider>
  );
}
```

## Foundation

### `RHFBaseProps`

Every wrapper extends `RHFBaseProps`. These three props are explained once here and not repeated in each section.

| Prop      | Type                       | Default                 | Description                                                                                                            |
| --------- | -------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `name`    | `string`                   | required                | Field name injected into RHF's `Controller`.                                                                           |
| `rules`   | `ControllerProps['rules']` | -                       | RHF validation rules (`required`, `pattern`, `validate`, `min`, `max`, etc.).                                          |
| `control` | `Control<any>`             | from `useFormContext()` | RHF control object. Pass explicitly only when you have nested `FormProvider`s and need to bind to a specific instance. |

### `RHFController`

Thin wrapper around RHF's `Controller` that auto-injects `control` from `useFormContext()`. Throws if neither a `FormProvider` nor a `control` prop is present. Use this directly when you need a non-standard binding that isn't covered by an existing wrapper.

```tsx
import { RHFController } from "@geckoui/nativewind-rhf";

<RHFController
  name="customField"
  rules={{ required: "Required" }}
  render={({ field, fieldState }) => (
    <MyCustomInput value={field.value} onChange={field.onChange} hasError={!!fieldState.error} />
  )}
/>;
```

### `RHFError`

Renders the validation error message for a field. By default uses the base `InputError` from `@geckoui/nativewind`; pass `render` to fully control the output. Renders nothing when `fieldState.error?.message` is falsy.

```tsx
<RHFError name="email" />
<RHFError name="email" className="mt-1" />

{/* Custom render — function form receives RHF's fieldState */}
<RHFError
  name="email"
  render={({ error }) => <Text className="text-error">{error?.message}</Text>}
/>

{/* Custom render — static node form, rendered as-is when an error is present */}
<RHFError name="email" render={<Text>Please fix this</Text>} />
```

| Prop        | Type                                                                       | Default | Description                                                                                                                   |
| ----------- | -------------------------------------------------------------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `className` | `string`                                                                   | -       | Class applied to the default `InputError`. Ignored when `render` is set.                                                      |
| `render`    | `ReactNode \| ((props: ControllerFieldState) => JSX.Element \| ReactNode)` | -       | Custom error renderer. Function receives RHF's `fieldState`. A static `ReactNode` is rendered as-is when an error is present. |

### `RHFInputGroup`

Convenience layout that renders a `Label`, the wrapped RHF input, and a matching `RHFError` underneath. It auto-detects the inner RHF child (any element whose `displayName` contains "RHF") to read `name` and `control`, so you don't have to wire the error twice.

```tsx
<RHFInputGroup label="Email" required tooltip="We'll never share it">
  <RHFInput name="email" placeholder="you@example.com" rules={{ required: "Required" }} />
</RHFInputGroup>
```

| Prop               | Type                   | Default  | Description                                                                  |
| ------------------ | ---------------------- | -------- | ---------------------------------------------------------------------------- |
| `label`            | `string`               | -        | Label text rendered above the child input. If omitted, no label is rendered. |
| `children`         | `ReactNode`            | required | Typically a single RHF wrapper.                                              |
| `className`        | `string`               | -        | Class applied to the outer wrapper `View`.                                   |
| `labelClassName`   | `string`               | -        | Class applied to the inner `Label`.                                          |
| `errorClassName`   | `string`               | -        | Class applied to the `RHFError` rendered below the input.                    |
| `style`            | `StyleProp<ViewStyle>` | -        | Style applied to the outer wrapper `View`.                                   |
| `required`         | `boolean`              | -        | Forwarded to the `Label` — appends a required marker.                        |
| `tooltip`          | (from `LabelProps`)    | -        | Forwarded to the `Label`.                                                    |
| `tooltipIcon`      | (from `LabelProps`)    | -        | Forwarded to the `Label`.                                                    |
| `tooltipClassName` | `string`               | -        | Forwarded to the `Label`.                                                    |

Extends `Omit<LabelProps, 'className' | 'style'>` — any other label-passthrough props (e.g. `htmlFor`-like extras) flow to the inner `Label`.

## Wrappers

All wrappers below extend `RHFBaseProps` (see above). Each section lists only the **delta** vs the base component — for everything else (`placeholder`, `disabled`, `size`, etc.) consult the **`nativewind` skill** (install separately if not already).

### `RHFCheckbox`

Wraps `Checkbox`. Supports three binding modes via `value` / `single`:

- **No `value`** — toggles between `true` and `false` on the form field.
- **`value` set, `single: false`** (default when `value` is set) — multi-select; field stores an array, `value` is added/removed.
- **`value` set, `single: true`** — single-select; field stores `value` when checked, `uncheckedValue` (or `null`) when unchecked.

```tsx
{/* boolean checkbox */}
<RHFCheckbox name="agree" label="I agree to the terms" rules={{ required: "Required" }} />

{/* multi-select array */}
<RHFCheckbox name="colors" value="red" label="Red" />
<RHFCheckbox name="colors" value="blue" label="Blue" />

{/* single-select with explicit on/off values */}
<RHFCheckbox name="plan" value="pro" uncheckedValue={null} single label="Pro plan" />

{/* indeterminate / partial */}
<RHFCheckbox name="all" partial={({ field }) => Array.isArray(field.value) && field.value.length > 0 && field.value.length < total} />
```

| Prop             | Type                                            | Default | Description                                                                                        |
| ---------------- | ----------------------------------------------- | ------- | -------------------------------------------------------------------------------------------------- |
| `value`          | `unknown`                                       | -       | Value written to form data when checked. Omit for boolean toggle.                                  |
| `single`         | `boolean`                                       | `false` | When true, behaves like a radio (stores `value` or `uncheckedValue`); when false, stores an array. |
| `uncheckedValue` | `unknown`                                       | `null`  | Value written when unchecked. Only used in `single` mode.                                          |
| `partial`        | `boolean \| ((args: RHFRenderArgs) => boolean)` | -       | Renders the indeterminate dash. Function form receives RHF render args.                            |
| `label`          | `string`                                        | -       | Display text rendered next to the checkbox. Wraps both in a `Pressable` row.                       |
| `labelClassName` | `string`                                        | -       | Class applied to the label `Text`.                                                                 |
| `onChange`       | `(value: unknown) => void`                      | -       | Called with the new form value (after RHF update).                                                 |

Extends `Omit<CheckboxProps, 'name' \| 'value' \| 'onChange' \| 'checked'>`.

### `RHFCounterInput`

Wraps `CounterInput`. Binds a numeric value to the form (defaults to `0` when the field is uninitialized).

```tsx
<RHFCounterInput
  name="quantity"
  min={1}
  max={10}
  rules={{ required: "Required", min: { value: 1, message: "At least 1" } }}
/>
```

| Prop       | Type                      | Default | Description                                          |
| ---------- | ------------------------- | ------- | ---------------------------------------------------- |
| `onChange` | `(value: number) => void` | -       | Called with the current numeric value alongside RHF. |

Extends `Omit<CounterInputProps, 'value' \| 'onChange'>`. Adds `--error` on the wrapper, `__button--error`, and `__input--error` classes when invalid.

### `RHFCurrencyInput`

`RHFNumberInput` enhanced with a currency symbol prefix and code suffix, plus thousands-separator formatting on the displayed value (the form state stays raw — only the visual `transform.input` is overridden).

```tsx
<RHFCurrencyInput
  name="price"
  currency={{ symbol: "$", code: "USD" }}
  maxFractionDigits={2}
  rules={{ required: "Price is required" }}
/>
```

| Prop       | Type                                 | Default | Description                                                   |
| ---------- | ------------------------------------ | ------- | ------------------------------------------------------------- |
| `currency` | `{ symbol?: string; code?: string }` | -       | `symbol` becomes the input prefix, `code` becomes the suffix. |

Extends `Omit<RHFNumberInputProps, 'strict'>` — `strict` is always forced to `true`. Inherits `positiveOnly`, `maxFractionDigits`, `maxWholeDigitPlaces`, `transform`, and all `RHFInputProps`.

### `RHFDateInput`

Wraps `DateInput`. Stores `string | null` on the form field.

```tsx
<RHFDateInput name="birthDate" format="MM/DD/YYYY" rules={{ required: "Date of birth required" }} />
```

| Prop       | Type                             | Default | Description                                             |
| ---------- | -------------------------------- | ------- | ------------------------------------------------------- |
| `onChange` | `(date: string \| null) => void` | -       | Called with the selected date (or `null` when cleared). |

Extends `Omit<DateInputProps, 'value' \| 'onChange'>`.

### `RHFDateRangeInput`

Wraps `DateRangeInput`. Stores `DateRange | null` on the form field.

```tsx
<RHFDateRangeInput
  name="vacation"
  numberOfMonths={2}
  rules={{ validate: (v) => (v?.from && v?.to ? true : "Pick a start and end") }}
/>
```

| Prop       | Type                                 | Default | Description                                              |
| ---------- | ------------------------------------ | ------- | -------------------------------------------------------- |
| `onChange` | `(range: DateRange \| null) => void` | -       | Called with the selected range (or `null` when cleared). |

Extends `Omit<DateRangeInputProps, 'value' \| 'onChange'>`.

### `RHFInput`

Wraps `Input`. The form field stores a `string`; `transform.input` and `transform.output` translate between the form value and what the user types/sees (per [RHF's transform & parse pattern](https://react-hook-form.com/advanced-usage#TransformandParse)).

```tsx
<RHFInput
  name="email"
  placeholder="you@example.com"
  keyboardType="email-address"
  rules={{
    required: "Email is required",
    pattern: { value: /^\S+@\S+$/i, message: "Invalid email" }
  }}
/>;

{
  /* Trim whitespace on the way in, uppercase on display */
}
<RHFInput
  name="code"
  transform={{
    input: (v) => v.toUpperCase(),
    output: (v) => v.trim()
  }}
/>;

{
  /* Dynamic prefix that reacts to field state */
}
<RHFInput
  name="username"
  prefix={({ fieldState }) => (fieldState.error ? <ErrorIcon /> : <AtIcon />)}
/>;
```

| Prop        | Type                                                                | Default | Description                                                                |
| ----------- | ------------------------------------------------------------------- | ------- | -------------------------------------------------------------------------- |
| `transform` | `{ input?: (v: string) => string; output?: (v: string) => string }` | -       | `input`: form state → displayed value; `output`: typed value → form state. |
| `prefix`    | `ReactNode \| string \| ((args: RHFRenderArgs) => ReactNode)`       | -       | Rendered before the input. Function form receives RHF render args.         |
| `suffix`    | `ReactNode \| string \| ((args: RHFRenderArgs) => ReactNode)`       | -       | Rendered after the input. Same shape as `prefix`.                          |
| `onChange`  | `(value: string) => void`                                           | -       | Called with the post-`transform.output` value.                             |
| `onBlur`    | `(value: string) => void`                                           | -       | Called with the current displayed value when the input loses focus.        |

Extends `Omit<InputProps, 'name' \| 'value' \| 'onChange' \| 'onChangeText' \| 'onBlur' \| 'prefix' \| 'suffix'>`.

### `RHFNumberInput`

`RHFInput` constrained to numeric input. Its default `transform.output` strips non-digits, caps fractional / whole-digit length, and (optionally) rejects negatives or normalizes leading zeros. Sets `keyboardType="decimal-pad"` by default.

```tsx
<RHFNumberInput
  name="age"
  positiveOnly
  maxFractionDigits={0}
  rules={{
    required: "Required",
    validate: (v) => Number(v) >= 18 || "Must be 18+"
  }}
/>
```

| Prop                  | Type      | Default | Description                                                                                   |
| --------------------- | --------- | ------- | --------------------------------------------------------------------------------------------- |
| `strict`              | `boolean` | `true`  | Sanitize / reject leading zeros on the integer portion. Disable for things like room numbers. |
| `positiveOnly`        | `boolean` | `false` | Reject negative numbers.                                                                      |
| `maxFractionDigits`   | `number`  | -       | Max decimal places. `0` forces integer-only.                                                  |
| `maxWholeDigitPlaces` | `number`  | -       | Max digits before the decimal point.                                                          |

Extends `RHFInputProps`. If you supply your own `transform.output`, it replaces the built-in sanitizer.

### `RHFOTPInput`

Wraps `OTPInput`. Stores the OTP as a `string` on the form field.

```tsx
<RHFOTPInput
  name="otp"
  length={6}
  onOTPComplete={(code) => verify(code)}
  rules={{
    required: "Enter the code",
    minLength: { value: 6, message: "Must be 6 digits" }
  }}
/>
```

| Prop       | Type                      | Default | Description                                             |
| ---------- | ------------------------- | ------- | ------------------------------------------------------- |
| `onChange` | `(value: string) => void` | -       | Called with the current OTP value alongside RHF.        |
| `onBlur`   | `(value: string) => void` | -       | Called with the current value when the OTP loses focus. |

Extends `Omit<OTPInputProps, 'value' \| 'onChange' \| 'onBlur'>`. The base's `error` prop is auto-driven from `fieldState.error` (suppressed when `disabled`).

### `RHFRadio`

Wraps `Radio`. Each radio binds the same `name` to a different `value` — the form field stores whichever `value` was last selected. **`value` is required** (the wrapper throws if `value` is `undefined` or `null`). Object/array values are compared structurally.

```tsx
<RHFRadio name="plan" value="free" label="Free" />
<RHFRadio name="plan" value="pro" label="Pro" />
<RHFError name="plan" />
```

| Prop             | Type                       | Default             | Description                                                               |
| ---------------- | -------------------------- | ------------------- | ------------------------------------------------------------------------- |
| `value`          | `unknown`                  | required (non-null) | Value written to form data when this radio is selected.                   |
| `label`          | `string`                   | -                   | Display text rendered next to the radio. Wraps both in a `Pressable` row. |
| `labelClassName` | `string`                   | -                   | Class applied to the label `Text`.                                        |
| `onChange`       | `(value: unknown) => void` | -                   | Called with the new form value (after RHF update).                        |

Extends `Omit<RadioProps, 'name' \| 'value' \| 'onChange' \| 'checked'>`.

### `RHFSelect`

Wraps `Select`. Mirrors the base component's overload — pass `multiple` for array values, omit it for a single value. Note `wrapperClassName` and `className` are merged onto the same wrapper element.

```tsx
{
  /* single */
}
<RHFSelect name="country" placeholder="Select country" rules={{ required: "Pick one" }}>
  <SelectOption value="us" label="United States" />
  <SelectOption value="uk" label="United Kingdom" />
</RHFSelect>;

{
  /* multi */
}
<RHFSelect name="tags" multiple filterable placeholder="Pick tags">
  <SelectOption value="bug" label="Bug" />
  <SelectOption value="feature" label="Feature" />
</RHFSelect>;
```

| Prop       | Type                                           | Default | Description                                      |
| ---------- | ---------------------------------------------- | ------- | ------------------------------------------------ |
| `onChange` | `(value: T) => void` or `(value: T[]) => void` | -       | Called with the selected value(s) alongside RHF. |

Extends `Omit<SingleSelectProps<T>, 'value' \| 'onChange'>` or `Omit<MultiSelectProps<T>, 'value' \| 'onChange'>`. All other `Select` props (`filterable`, `clearable`, `placement`, `wrapperClassName`, `buttonClassName`, etc.) pass through.

### `RHFSwitch`

Wraps `Switch`. Defaults to a `boolean` toggle, but you can map on/off to arbitrary values using `value` / `uncheckedValue`.

```tsx
{
  /* boolean */
}
<RHFSwitch name="notifications" />;

{
  /* string-valued switch */
}
<RHFSwitch name="visibility" value="public" uncheckedValue="private" />;
```

| Prop             | Type                       | Default | Description                                                 |
| ---------------- | -------------------------- | ------- | ----------------------------------------------------------- |
| `value`          | `unknown`                  | -       | Value written when on. Omit for boolean toggle.             |
| `uncheckedValue` | `unknown`                  | -       | Value written when off. Only used when `value` is provided. |
| `onChange`       | `(value: unknown) => void` | -       | Called with the new form value (after RHF update).          |

Extends `Omit<SwitchProps, 'name' \| 'value' \| 'onChange'>`.

### `RHFTextarea`

Wraps `Textarea`. Same `transform` pattern as `RHFInput`. The error class is suppressed while `disabled`.

```tsx
<RHFTextarea
  name="bio"
  placeholder="Tell us about yourself"
  rules={{
    required: "Required",
    maxLength: { value: 500, message: "Max 500 chars" }
  }}
/>
```

| Prop        | Type                                                                | Default | Description                                                                |
| ----------- | ------------------------------------------------------------------- | ------- | -------------------------------------------------------------------------- |
| `transform` | `{ input?: (v: string) => string; output?: (v: string) => string }` | -       | `input`: form state → displayed value; `output`: typed value → form state. |
| `onChange`  | `(value: string) => void`                                           | -       | Called with the post-`transform.output` value.                             |
| `onBlur`    | `(value: string) => void`                                           | -       | Called with the raw form value when the textarea loses focus.              |

Extends `Omit<TextareaProps, 'value' \| 'onChange' \| 'onChangeText' \| 'onBlur'>`.

## Validation example

`rules` accepts the full RHF rules object. Common shapes:

```tsx
<RHFInput
  name="email"
  rules={{
    required: "Email is required",
    pattern: { value: /^\S+@\S+$/i, message: "Invalid email" },
    maxLength: { value: 254, message: "Too long" },
    validate: (value) => !value.endsWith("@example.com") || "No example.com",
  }}
/>

<RHFNumberInput
  name="age"
  rules={{
    required: "Required",
    min: { value: 18, message: "Must be 18+" },
    max: { value: 120, message: "Must be ≤120" },
  }}
/>

<RHFCheckbox
  name="terms"
  label="I agree"
  rules={{ validate: (v) => v === true || "You must agree to continue" }}
/>
```

## Error styling

Every wrapper applies a `GeckoUIRHF<Component>--error` BEM class on the relevant element when `fieldState.error` is truthy. Customize the look from `global.css`:

```css
@layer components {
  .GeckoUIRHFInput--error {
    @apply border-2 border-red-600 bg-red-50;
  }

  .GeckoUIRHFInputGroup {
    @apply gap-2;
  }
}
```

For the design tokens these classes use (`border-error`, etc.), see the `nativewind` skill's `references/theming.md`.
