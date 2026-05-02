# @geckoui/nativewind-rhf Theming Reference

`@geckoui/nativewind-rhf` adds no design tokens of its own — it inherits the colour scale, surface, text, border, status, and tooltip variables from `@geckoui/nativewind`. See that skill's `references/theming.md` for the full token reference.

What this file documents: the BEM class hooks each RHF wrapper applies on top of the base component, so consumers can target validation states from `global.css`.

## Class Stacking

Each RHF wrapper passes its `className` through to the underlying base component, so multiple `GeckoUI*` classes end up on the **same element**. Styling the base class affects the wrapper too.

| Element                                | Classes on the same element                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `View` (Input wrapper)                 | `.GeckoUIInput` + `.GeckoUIRHFInput`                                                         |
| `View` (Input wrapper, number)         | `.GeckoUIInput` + `.GeckoUIRHFInput` + `.GeckoUIRHFNumberInput`                              |
| `View` (Input wrapper, currency)       | `.GeckoUIInput` + `.GeckoUIRHFInput` + `.GeckoUIRHFNumberInput` + `.GeckoUIRHFCurrencyInput` |
| `TextInput` (textarea)                 | `.GeckoUITextarea` + `.GeckoUIRHFTextarea`                                                   |
| `View` (Select outer)                  | `.GeckoUISelect` + `.GeckoUIRHFSelect`                                                       |
| `Pressable` (Switch track)             | `.GeckoUISwitch` + `.GeckoUIRHFSwitch`                                                       |
| `Animated.View` (Switch thumb)         | `.GeckoUISwitch__thumb` + `.GeckoUIRHFSwitch__thumb`                                         |
| `Pressable` (OTP grid)                 | `.GeckoUIOTPInput` + `.GeckoUIRHFOTPInput`                                                   |
| `View` (Counter wrapper)               | `.GeckoUICounterInput` + `.GeckoUIRHFCounterInput`                                           |
| `Pressable` (DateInput container)      | `.GeckoUIDateInput` + `.GeckoUIRHFDateInput`                                                 |
| `Pressable` (DateRangeInput container) | `.GeckoUIDateRangeInput` + `.GeckoUIRHFDateRangeInput`                                       |
| `Pressable` (Checkbox)                 | `.GeckoUICheckbox` + `.GeckoUIRHFCheckbox`                                                   |
| `Pressable` (Radio)                    | `.GeckoUIRadio` + `.GeckoUIRHFRadio`                                                         |
| `Text` (InputError)                    | `.GeckoUIInputError` + `.GeckoUIRHFError`                                                    |

**Impact:** Styling `.GeckoUIInput` also affects `RHFInput`, `RHFNumberInput`, and `RHFCurrencyInput` since they share the same element.

## Component Class Reference

Every CSS class that an RHF wrapper applies, what RN element it lands on, and what it targets. `Modifiers` lists the `--xxx` BEM modifier suffixes (RN doesn't use `data-*` attributes — state is encoded as BEM modifiers). The `--error` modifier is added when RHF reports a validation error.

### RHFCheckbox

| Class                        | Element     | Targets                                            | Modifiers |
| ---------------------------- | ----------- | -------------------------------------------------- | --------- |
| `.GeckoUIRHFCheckbox`        | `Pressable` | The checkbox square (stacks on `.GeckoUICheckbox`) | `--error` |
| `.GeckoUIRHFCheckbox__row`   | `Pressable` | Row wrapper when `label` is set                    | —         |
| `.GeckoUIRHFCheckbox__label` | `Text`      | Label text next to checkbox                        | —         |

`--error` paints a red border on the checkbox square.

### RHFCounterInput

| Class                                    | Element     | Targets                                            | Modifiers |
| ---------------------------------------- | ----------- | -------------------------------------------------- | --------- |
| `.GeckoUIRHFCounterInput`                | `View`      | Outer container (stacks on `.GeckoUICounterInput`) | `--error` |
| `.GeckoUIRHFCounterInput__button--error` | `Pressable` | Increment/decrement button when invalid            | —         |
| `.GeckoUIRHFCounterInput__input--error`  | `TextInput` | Number input when invalid                          | —         |

`--error` selectors apply red borders to both the buttons and the input field.

### RHFCurrencyInput

| Class                                       | Element | Targets                                                                                         | Modifiers |
| ------------------------------------------- | ------- | ----------------------------------------------------------------------------------------------- | --------- |
| `.GeckoUIRHFCurrencyInput`                  | `View`  | Outer Input wrapper (stacks on `.GeckoUIInput` + `.GeckoUIRHFInput` + `.GeckoUIRHFNumberInput`) | —         |
| `.GeckoUIRHFCurrencyInput__currency-symbol` | `Text`  | Currency symbol prefix ($, €, £)                                                                | —         |
| `.GeckoUIRHFCurrencyInput__currency-code`   | `Text`  | Currency code suffix (USD, EUR)                                                                 | —         |

Inherits `--error` styling via the stacked `.GeckoUIRHFInput--error` class.

### RHFDateInput

| Class                  | Element     | Targets                                             | Modifiers |
| ---------------------- | ----------- | --------------------------------------------------- | --------- |
| `.GeckoUIRHFDateInput` | `Pressable` | DateInput container (stacks on `.GeckoUIDateInput`) | `--error` |

`--error` paints a red border on the input container.

### RHFDateRangeInput

| Class                       | Element     | Targets                                                       | Modifiers |
| --------------------------- | ----------- | ------------------------------------------------------------- | --------- |
| `.GeckoUIRHFDateRangeInput` | `Pressable` | DateRangeInput container (stacks on `.GeckoUIDateRangeInput`) | `--error` |

`--error` paints a red border on the input container.

### RHFError

| Class              | Element | Targets                                        | Modifiers |
| ------------------ | ------- | ---------------------------------------------- | --------- |
| `.GeckoUIRHFError` | `Text`  | Error message (stacks on `.GeckoUIInputError`) | —         |

### RHFInput

| Class              | Element | Targets                                   | Modifiers |
| ------------------ | ------- | ----------------------------------------- | --------- |
| `.GeckoUIRHFInput` | `View`  | Input wrapper (stacks on `.GeckoUIInput`) | `--error` |

`--error` paints a red border on the input wrapper.

### RHFInputGroup

| Class                   | Element | Targets                                  | Modifiers |
| ----------------------- | ------- | ---------------------------------------- | --------- |
| `.GeckoUIRHFInputGroup` | `View`  | Form field group (label + input + error) | —         |

Built-in: `gap-1.5` between label, input, and error.

### RHFNumberInput

| Class                    | Element | Targets                                                        | Modifiers |
| ------------------------ | ------- | -------------------------------------------------------------- | --------- |
| `.GeckoUIRHFNumberInput` | `View`  | Input wrapper (stacks on `.GeckoUIInput` + `.GeckoUIRHFInput`) | —         |

Inherits `--error` styling via the stacked `.GeckoUIRHFInput--error` class.

### RHFOTPInput

| Class                 | Element     | Targets                                           | Modifiers |
| --------------------- | ----------- | ------------------------------------------------- | --------- |
| `.GeckoUIRHFOTPInput` | `Pressable` | OTP grid container (stacks on `.GeckoUIOTPInput`) | `--error` |

`--error` paints a red border around the OTP grid (the visual error border on each cell is driven by the base `.GeckoUIOTPInput__cell--error` modifier, toggled via the `error` prop the wrapper forwards).

### RHFRadio

| Class                     | Element     | Targets                                  | Modifiers |
| ------------------------- | ----------- | ---------------------------------------- | --------- |
| `.GeckoUIRHFRadio`        | `Pressable` | Radio circle (stacks on `.GeckoUIRadio`) | `--error` |
| `.GeckoUIRHFRadio__row`   | `Pressable` | Row wrapper when `label` is set          | —         |
| `.GeckoUIRHFRadio__label` | `Text`      | Label text next to radio                 | —         |

`--error` paints a red border on the radio circle.

### RHFSelect

| Class                              | Element     | Targets                                                                     | Modifiers |
| ---------------------------------- | ----------- | --------------------------------------------------------------------------- | --------- |
| `.GeckoUIRHFSelect`                | `View`      | Select wrapper (stacks on `.GeckoUISelect`, applied via `wrapperClassName`) | `--error` |
| `.GeckoUIRHFSelect__button--error` | `Pressable` | SelectButton when invalid (applied via `buttonClassName`)                   | —         |

The two error classes target two different elements: the wrapper and the inner button. Combined, they deliver the red-border error treatment.

### RHFSwitch

| Class                      | Element         | Targets                                          | Modifiers |
| -------------------------- | --------------- | ------------------------------------------------ | --------- |
| `.GeckoUIRHFSwitch`        | `Pressable`     | Switch track (stacks on `.GeckoUISwitch`)        | `--error` |
| `.GeckoUIRHFSwitch__thumb` | `Animated.View` | Switch thumb (stacks on `.GeckoUISwitch__thumb`) | —         |

`--error` adds a red border to the switch track (the base switch has no border, so the error state introduces one).

### RHFTextarea

| Class                 | Element     | Targets                                 | Modifiers |
| --------------------- | ----------- | --------------------------------------- | --------- |
| `.GeckoUIRHFTextarea` | `TextInput` | Textarea (stacks on `.GeckoUITextarea`) | `--error` |

`--error` paints a red border on the textarea.
