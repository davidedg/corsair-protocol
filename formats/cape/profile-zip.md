# `PROFILE.ZIP`: compressed profile model

This page describes files stored on the **K95 RGB Platinum (`1b1c:1b2d`), firmware 3.29, bootloader 3.03**, with the profile structure produced by **iCUE 5.51.40.0**. The versions and field layouts below describe that combination; compatibility with other software versions, devices or firmware is not established.

`PROFILE.ZIP` contains the editable model of one slot: actions, lighting layers, indicator colours and Win Lock settings. The firmware uses the corresponding [binary profile files](README.md#the-files-of-a-slot) to execute the profile. It does not need this backup to run the slot. A slot without the backup can work on the keyboard while appearing empty in the host application.

The model contains information that the binary files cannot reconstruct, including action and layer names, UUIDs, original colour-curve points, unassigned actions and disabled lighting layers. A compatible save must keep the backup and the executable files consistent.

## Compressed container

Despite the filename, this is **not a ZIP archive**. It has the following layout:

| Offset | Size | Content |
|---|---|---|
| 0 | 4 | uncompressed XML length, unsigned 32-bit **big-endian** |
| 4 | remaining bytes | one zlib stream containing UTF-8 XML |

This is the Qt `qCompress` container format. The stored streams start with `78 da`, and their compressed bytes are reproducible with classic zlib at compression level 9. Different zlib implementations can produce different compressed bytes for the same XML; the decompressed document is the useful comparison across implementations.

A reader should check the declared length against the decompressed byte count, the zlib checksum, and the XML structure. Limits on compressed size, decompressed size, nesting and collection lengths are reader policy, rather than established limits of the keyboard's backup format. The file occupies flash sectors like any other [slot file](README.md#space).

## XML envelope and values

The document starts as follows; the omitted property bodies are described below. This is an outline, not a complete profile:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<cereal>
    <profile>
        <cereal_class_version>203</cereal_class_version>
        <isAdvancedMode>true</isAdvancedMode>
        <!-- LightingsProperty -->
        <!-- BrightnessIndicatorProperty -->
        <!-- WinlockIndicatorProperty -->
        <!-- ProfileIndicatorProperty -->
        <!-- WinlockProperty -->
        <!-- SideLightingProperty -->
        <!-- ActionsProperty -->
    </profile>
</cereal>
```

The eight properties after `cereal_class_version` occur in the order shown, counting `isAdvancedMode`. The slot's name, GUID and revision cookie are absent from this root. [PROFILE.I](profile-i.md) holds all three; the [slot table](../../host-to-device/0e/17/04/0e1704.md) holds the GUID and cookie. Action, animation and layer identities inside the model are separate from the slot GUID. The profile icon colour used by the host interface is absent from the slot files.

The XML uses these conventions:

- Booleans are `true` or `false`; integers are decimal. Many action options are symbolic strings such as `OnPress`, while predefined lighting parameters such as `speed` are numeric.
- Collections have `size="dynamic"` and children `value0`, `value1`, and so on. Empty collections can be self-closing. Pairs have `first` and `second` children.
- Each type's `cereal_class_version` is emitted at its first occurrence in the document. Later instances can omit it, including within a `base` node. An empty `<base/>` does not mean that the outer object has no fields or no version.
- A `base` node contains inherited fields before the fields of the enclosing type. The lighting type `ColorWaveLighting` uses `value0` in place of this immediate `base` node.
- UUID values use lowercase hexadecimal with braces: `{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}`. The all-zero UUID is present in optional associations such as `actionLighting` and inactive action presets.
- Empty strings occur as `<name></name>`; empty structures and collections also occur as self-closing elements. A scalar with whitespace at its edges can have `xml:space="preserve"`. Text values include literal tabs and CR LF as well as escaped XML characters; ordinary XML line-ending normalisation can change CR LF when reading them.
- Stored documents use tab indentation. Floating-point transition times can have up to 17 significant digits. Indentation is separate from the meaning of the model.

### Object wrappers and type IDs

Properties, layers, effects and actions use `polymorphic_id` and `ptr_wrapper`. The first named type in a document has ID `2147483649` (`80000001` in hexadecimal); successive new named types increment the low part. A first occurrence includes `polymorphic_name`, for example `AdvancedLightingLayer` or `KeyRemapAction`. Later objects of the same type use the low ID alone and omit the name.

Objects whose type is fixed by the containing property also use `polymorphic_id` = `1073741824` (`40000000` in hexadecimal), without a name. The identifier is metadata for the XML object, not a firmware action or lighting type number.

Two wrapper forms occur:

| Wrapper contents | Use in the stored model |
|---|---|
| `valid` = 1, then `data` | root properties |
| numeric `id`, then `data` | lighting layers, animations, actions, macro events and action presets |

The numeric wrapper IDs start at `2147483649` and count objects independently of the named-type IDs. Their high bit marks a definition. They are distinct from the UUID-valued `id` inside an object's data. Type IDs and object IDs must be interpreted in their own document; they are not stable identifiers across profiles.

For example, the first property of a profile with no lighting layers is:

```xml
<LightingsProperty>
    <polymorphic_id>2147483649</polymorphic_id>
    <polymorphic_name>AdvancedLightingsProperty</polymorphic_name>
    <ptr_wrapper>
        <valid>1</valid>
        <data>
            <cereal_class_version>200</cereal_class_version>
            <base>
                <cereal_class_version>200</cereal_class_version>
                <base>
                    <cereal_class_version>200</cereal_class_version>
                </base>
                <layers size="dynamic"/>
            </base>
        </data>
    </ptr_wrapper>
</LightingsProperty>
```

### Colours and optional colours

Indicator and transition colours are strings with components normalised to 0 through 1:

| Value | Meaning |
|---|---|
| `rgb(1 0 0)` | opaque red |
| `rgb(0 0.501961 1)` | RGB bytes 0, 128, 255 |
| `rgba(0 0 1 0.501961)` | blue with alpha 128 |

The stored RGB component strings use up to six significant digits. An omitted alpha means full opacity. Only `rgb(...)` and `rgba(...)` are described here.

An optional colour has `<nullopt>true</nullopt>` when absent. When present, it has `<nullopt>false</nullopt>` followed by `<data>rgb(...)</data>` or its RGBA equivalent. Absence is distinct from a black or transparent colour.

Effect fields named `colors` have a different representation: a pair of decimal unsigned 32-bit values, each an **ARGB** packed colour. For example, `4294901760` is hexadecimal `ffff0000` (opaque red), and `4278190335` is hexadecimal `ff0000ff` (opaque blue). These values differ from the byte order R G B A of a firmware lighting descriptor.

## Actions

The root `ActionsProperty` has an outer data version of **300**, containing `base` followed by `hwActionsSupportedTypes` (0 in the stored profiles). That `base`, version **303**, contains an empty property `base`, then `actions`, then `presetsInfo`.

Each `actions/valueN` is a pair:

| Child | Content |
|---|---|
| `first` | wrapped action object |
| `second` | source binding, version 400: `key`, `layer`, `event`, `distance` |

Bindings use symbolic source keys such as `G1`, `F1` or `Key_Profile`. The stored bindings use `layer` = `StandardLayer`, `event` = `Click`, and `distance` = 0. An action with an empty `key` remains in the XML but has no DAT entry or macro file. XML key names must be mapped to [firmware key indices](key-index.md), rather than interpreted as numeric indices.

`presetsInfo` is a wrapped object, version **400**, with `activePresetId` and `isActive`. Inactive presets have the zero UUID and `false`.

### Common action fields

Each concrete action starts with `base`, version **202**, whose fields are in this order:

```text
name, id, repeatOptions, executionHints,
actionLighting, actionSoundPath, attachedActions
```

The name and UUID identify the editable action and are not present in the firmware's macro events. The stored hardware actions have a zero `actionLighting` UUID, an empty `actionSoundPath`, and an empty `attachedActions` collection.

`repeatOptions`, version **300**, has:

```text
repeatCount, repeatMode, delay, delayMode, randomDelayFrom, randomDelayTo
```

The stored actions use `repeatCount` = 1, `repeatMode` = `NoRepeat`, `delayMode` = `Constant`, and zero delay fields. The presence of additional repeat metadata does not establish hardware support for other repeat modes.

`executionHints`, version **201**, has:

```text
terminateOnSecondExec, restartOnSecondExec, execHint, retainOriginalKeyOutput
```

`terminateOnSecondExec` and `retainOriginalKeyOutput` are false in the described actions. `restartOnSecondExec` is true for the recorded macros and false for the other action types. The following `execHint` values have corresponding [DAT settings](profile-dat.md#start-condition-and-run-type):

| Action and hint | DAT start / run / repeat |
|---|---|
| ordinary key combination, `OnBoth` | `00 / 01 / 01` |
| Macro, `OnPress` | `00 / 01 / 01` |
| Macro, `OnRelease` | `11 / 01 / 01` |
| Macro, `WhilePressed` | `00 / 03 / 01` |
| Macro, `Toggle` | `00 / 04 / 01` |

An ordinary key combination with `OnPress` can describe the same subtype-01 macro events as `OnBoth`. On import, iCUE normalises such an action to `OnBoth` without rewriting the slot. Writers should use `OnBoth` for a newly edited ordinary key combination.

### Concrete actions and firmware files

Fields below follow the common `base`, in the listed order:

| XML type | Version | Fields |
|---|---|---|
| `KeyRemapAction` | 400 | `keyName`, `keyStroke`, `holdingKeyEnabled`, `holdingKeyType`, `holdingKeyOnPressInterval`, `programPath`, `sniperSwitchMode`, `remapGroup` |
| `MacroAction` | 201 | `terminationMethod`, `highPriority`, `events` |
| `TextAction` | 201 | `text`, `delayBetweenCharacters` |
| `MediaAction` | 200 | `action` |

- A simple `KeyRemapAction` uses `keyName` as its destination and an empty `keyStroke`. `remapGroup` distinguishes Keyboard (0), Mouse (6), key combinations (7) and Language keys (9). A plain remap becomes a [MAP entry](profile-map.md); the backup retains the destination's symbolic name even when the firmware mapping is limited.
- A key combination uses the `keyStroke` collection of symbolic destination keys and becomes a DAT entry plus a subtype-01 [macro file](macro.md#key-combination-subtype-01). Keyboard remaps with `holdingKeyEnabled` also use DAT and subtype 01: `holdingKeyType` = `OnPress` adds the requested delay, in milliseconds, between press and release; `Toggle` uses delay zero and DAT run type `84`, repeat zero. The XML can retain a requested interval larger than the firmware's maximum delay; the [binary delay is clamped](macro.md#imitate-holding-key).
- The described `MacroAction` objects have `terminationMethod` = `Interrupt` and `highPriority` = `false`. Their `events` collection becomes a DAT entry and a subtype-00 macro file.
- `TextAction` retains the original Unicode text and `delayBetweenCharacters` in milliseconds. The subtype-03 macro file contains keyboard events for the text, including modifiers, rather than this Unicode string. Its interpretation therefore also depends on the host keyboard layout.
- `MediaAction/action` is symbolic, with stored values including `Stop`, `PrevTrack`, `NextTrack`, `VolumeUp` and `Mute`. These standard media actions become MAP remaps, without a macro file.

The other remap fields remain in the XML even when inactive: examples have empty `programPath` and `sniperSwitchMode` = `WhilePressed`. Their presence does not imply an executable firmware operation on this keyboard.

The two macro event types present in the described backups are:

| XML type | Version | Fields after `base` |
|---|---|---|
| `KeyboardMacroActionEvent` | 200 | `keys`, `sub` |
| `DelayMacroActionEvent` | 200 | `useRandom`, then `delay` if false, or `delayMin`, `delayMax` if true |

Their event `base` is version **200** and has no fields. Keyboard events have a collection of symbolic keys and `sub` = `KeyPress` or `KeyRelease`. Delay values and random-delay bounds are in milliseconds. A stored random delay from 100 to 900 ms has `useRandom=true`, `delayMin=100` and `delayMax=900`, with no `delay` field; its [firmware event](macro.md#events) encodes those two bounds. This page does not define other event types.

DAT entries and macro filenames follow the source-key order described in [PROFILE.DAT](profile-dat.md#entry-16-bytes), including the profile-button exception. This is independent of names and object IDs in the XML.

## Indicators and Win Lock

The following root properties use a `base` of version **200** with no fields. Their own fields follow that base:

| Property | Version | Fields in order |
|---|---|---|
| `BrightnessIndicatorProperty` | 200 | `indicatorColor` |
| `WinlockIndicatorProperty` | 400 | `enabledIndicatorColor`, `disabledIndicatorColor` (both optional) |
| `ProfileIndicatorProperty` | 400 | `m1Color`, `m2Color`, `m3Color`, `profileIndicatorColor` (last field optional) |
| `WinlockProperty` | 300 | `disableAltTab`, `disableAltF4`, `disableWinKey`, `disableShiftTab`, `disableEncryption`, `disableTouchpad`, `swapFKeyFunctions` |
| `SideLightingProperty` | 200 | `enabled` |

The four RGB triples in [PROFILE.I](profile-i.md) correspond to `profileIndicatorColor`, `indicatorColor`, `enabledIndicatorColor` and `disabledIndicatorColor`, respectively. Absent optional colours yield the default profile red, Win Lock on cyan and Win Lock off red. There is one brightness indicator colour, dimmed by the firmware at lower brightness levels. The `m1Color`/`m2Color`/`m3Color` metadata does not provide three extra indicator triples in the firmware file.

The four supported Win Lock booleans map to bits 0 through 3 of [DAT byte 1](profile-dat.md#win-lock-options-byte-1), in the order `disableWinKey`, `disableAltTab`, `disableAltF4`, `disableShiftTab`. The stored model also has `disableEncryption` = `false`, `disableTouchpad` = `false`, `swapFKeyFunctions` = `true`, and `SideLightingProperty/enabled` = `true`. No additional executable K95 RGB Platinum setting is established for those fields.

## Lighting model

`LightingsProperty` wraps an `AdvancedLightingsProperty`, version **200**. Its `base`, version **200**, contains an empty property `base` of version **200**, then the `layers` collection.

Each layer is a wrapped `AdvancedLightingLayer`, version **200**, with `base` then `lighting`. The layer `base`, version **400**, contains:

```text
id, name, keys, enabled, executionHints, option
```

`id` is the layer UUID, `name` its editable name, `keys` a collection of symbolic LED/key names, and `lighting` a separate wrapped effect with its own animation UUID.

### Selection, order and disabled layers

`option` distinguishes `ConcreteLeds` (the explicit `keys` selection) from `AllLeds` (all LEDs). `AllLeds` can have an empty `keys` collection; it must not be read as an empty explicit selection. Keys and light-bar zones use names such as `Escape` and `Led_TopZone1`, mapped to the [lighting cells](lighting-cells.md). The six physical G keys use lighting names `G3`, `G6`, `G9`, `G12`, `G15`, `G18`, in physical G1 through G6 order. These differ from the action-binding names `G1` through `G6`: lighting `G3` selects physical G1 (cell 98), not physical G3. The three indicator buttons are represented by their own properties, rather than cells in the lighting files.

The XML lists layers **top first**. Firmware groups are numbered **bottom first**, after excluding disabled layers. Thus for XML layers A, B, C, all enabled, the groups are C = `lght_00`, B = `lght_01`, A = `lght_02`. If B is disabled, C remains `lght_00` and A becomes `lght_01`; A and C retain the same descriptor, key and colour bytes.

`enabled=false` preserves the layer object, name, order, selection and effect data in the backup. The layer UUID can change when saving, including when disabling or re-enabling a layer; it is not a stable key for comparing those backups. A disabled layer contributes no firmware lighting files and does not increase `lghtcnt.cnt`. An all-disabled model has `lghtcnt.cnt` = 0 and no `.d`, `.k` or `.r` files, while retaining its layers in the XML. Re-enabling Rain or static layers with unchanged parameters restores their lighting files byte for byte. Disabled layers are distinct from empty selections, black colours and zero opacity. How disabled layers count towards the host interface's layer limits is not established.

### Layer execution hints

The layer's `executionHints`, version **200**, contains:

```text
startOnKeyPress, startWithProfile, stopOnKeyPress, stopOnKeyRelease,
stopAfterTimes, playOption
```

The first four booleans match bytes 17 through 20 of static and custom [lighting descriptors](custom-effects.md#layout-of-lght_nnd). A positive `stopAfterTimes` is the repeat limit; stored inactive limits include -1 and 0, represented as zero in the binary descriptor. Stored layers use `playOption` = `Standard`; other play options are outside this description.

### Animation bases and transitions

Every effect includes animation data of version **300**, with:

```text
name, id, duration, brightness
```

Here `duration` is in **milliseconds** and `brightness` is 10 in the stored layers. This duration is separate from the small numeric duration enum of `TypeLighting` below.

Static and custom effects have an intermediate `base` of version **201**, containing the animation `base` followed by `transitions`. Each transition, version **200**, has `time` followed by `color`. Times are fractions of the animation duration, from 0 to 1; colours are the RGB/RGBA strings described above. The original points are retained in the backup, whereas a custom firmware descriptor holds 128 colour samples. Different point sets can therefore yield the same executable samples.

### Effect types and fields

The following fields come after the effect's base. Static and custom effects use the transition base described above; predefined effects use the animation base directly. `ColorWaveLighting` names that base `value0`.

| XML type | Version | Fields after base | Firmware EffectType |
|---|---|---|---|
| `StaticLighting` | 201 | `opacity` | 7 |
| `SolidLighting` | 201 | none | 10 |
| `GradientLighting` | 300 | none | 11 |
| `WaveLighting` | 201 | `velocity`, `tailLength`, `isDoublesided`, `angle` | 13 |
| `RippleLighting` | 201 | `velocity`, `tailLength`, `autoDuration`, `waveSpread`, `colors` | 12 |
| `ColorPulseLighting` | 300 | `colors`, `random`, `speed` | 1 |
| `ColorShiftLighting` | 300 | `colors`, `random`, `speed` | 0 |
| `ColorWaveLighting` | 300 | `speed`, `random`, `colors`, `direction` | 4 |
| `RainLighting` | 301 | `colors`, `random`, `opacity`, `speed`, `direction` | 6 |
| `RainbowWaveLighting` | 301 | `speed`, `direction` | 3 |
| `SpiralRainbowLighting` | 301 | `clockwise`, `speed` | 2 |
| `TypeLighting` | 401 | `colors`, `random`, `opacity`, `speed`, `duration`, `typeLightingBehavior` | 8 (key), 9 (ripple) |
| `VisorLighting` | 301 | `colors`, `random`, `speed`, `direction` | 5 |

The XML version is independent of the firmware EffectType. The stored layouts and values relate to the binary files as follows:

- **Static:** one transition supplies the colour; `opacity` is an integer from 0 to 255 supplying the alpha of every used `.r` entry. The `.d` is the fixed 37-byte static descriptor, with cells in `.k` and colours in the 1024-byte `.r`. On the UK/ISO layout, `AllLeds` produces 135 cells and 138 repeated colour entries, including the extra entries described in [lighting files](lighting.md#lght_nnr-the-colours-of-a-static-layer).
- **Solid, Gradient, Wave, Ripple:** a 553-byte `.d` and a `.k`, without `.r`. The millisecond animation duration becomes tenths of a second in `.d` (1000 ms becomes 10). XML `velocity` and `tailLength` are in tenths of the corresponding descriptor units: 20 and 40 become 2 and 4; fractional descriptor values are rounded to whole bytes. `angle` is in degrees and `isDoublesided` supplies the two-sided flag. Stored Ripple layers have `autoDuration=false` and `waveSpread=OnlyBetweenFronts`; their `colors` pair is zero and their colour curve is in `transitions`. The binary layout, origin behaviour and sample representation are in [custom effects](custom-effects.md).
- **Predefined:** one enabled effect supplies a 13-byte `.d`, without `.k` or `.r`; it applies to the whole keyboard. For effects with a colour choice, `random=true` supplies random colour mode, otherwise the two ARGB colours supply alternating mode. Speed values are 1 (slow), 2 (medium), 3 (fast); stored TypeLighting key-mode durations include 2 (medium) and 3 (long). Direction values and their firmware representation are in [enumerations](enums.md). `clockwise` selects the spiral direction; `typeLightingBehavior` selects key mode (0) or ripple mode (1). Fields that are present in XML need not be active controls: Rain retains `direction`, although its descriptor's direction byte is zero and its editable parameters do not include a direction. The [predefined-effect table](predefined-effects.md) identifies which parameters reach each descriptor; inactive parameters have zero bytes there.

The exact conversion of XML transition points to binary colour samples and the keyboard's geometry are outside this XML description. The samples are already present in the descriptor supplied to the firmware. The supported layer combinations and binary formats remain those of [lighting files](lighting.md), [custom effects](custom-effects.md) and [predefined effects](predefined-effects.md).

## Revision cookies and reconstruction

The host reads the slot table and `PROFILE.I` before deciding whether it needs a backup. With no matching local copy, or with the same slot GUID and an older local revision cookie, it reads the necessary `PROFILE.ZIP` and reconstructs the model without writing flash or reading the raw action and lighting files. Other matching slots can remain in the local cache.

Consequently, a changed backup must be accompanied by an updated revision cookie in both the slot table and `PROFILE.I`. The table stores the cookie as a 24-bit value; INFO stores it as a 32-bit little-endian value. One save can advance the cookie by more than one.

The cookie is a revision marker, not a checksum of the backup or a complete specification of host cache behaviour. Lighting-layer UUIDs can change between saved backups while layer names, selection, order and parameters remain the same; animation and action UUIDs remain unchanged in those files. Compare the represented data as well as identities when checking consistency.

## Consistency of the slot files

A reader or writer can check the model against the executable files along these boundaries:

| Backup content | Corresponding firmware data |
|---|---|
| simple key and standard media remaps | `PROFILE.MAP` |
| assigned macros, text, key combinations and held-key remaps | `PROFILE.DAT` entries and the referenced `M###` files |
| four supported Win Lock flags | `PROFILE.DAT` byte 1 |
| four resolved indicator colours | `PROFILE.I` bytes 253 through 264 |
| enabled lighting layers, reversed to bottom-first order | contiguous `lght_NN.*` groups and `lghtcnt.cnt` |

Names, UUIDs, unassigned actions, disabled layers and original transition points remain model metadata and cannot be validated by expecting a separate raw file for each XML object. Conversely, matching the backup alone does not establish that the binary files execute that model: both representations, plus the slot identity and revision, belong to one coherent save.
