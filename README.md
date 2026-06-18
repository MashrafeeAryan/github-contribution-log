# github-contribution-log

# Contribution [1]: Externalise hardcoded `cga_colors` palettes

**Contribution Number:** 1
**Student:** Mashrafee Aryan
**Issue:** [Externalise hardcoded `cga_colors` palettes #2051](https://github.com/dosbox-staging/dosbox-staging/issues/2051)
**Project:** [dosbox-staging](https://github.com/dosbox-staging/dosbox-staging)
**Fork/Branch:** [externalize-cga-palettes](https://github.com/mashrafeearyan07/dosbox-staging/tree/externalize-cga-palettes)
**Status:** Phase II Complete

---

## Why I Chose This Issue

I chose this issue because it is a good first open-source contribution that still touches a real part of a large C++ codebase. The task is not about changing emulator behavior. It is a refactoring task focused on improving maintainability by moving fixed CGA color palette data out of the source code and into resource files.

This felt like a good entry point because it lets me practice reading a large codebase, understanding existing project conventions, following contribution guidelines, and making small focused commits.

---

## Understanding the Issue

### Problem Description

The DOSBox Staging codebase currently stores several CGA color palettes directly inside C++ source code as hardcoded `constexpr cga_colors_t` arrays. These palettes are fixed data, but they live beside rendering and palette initialization logic.

The issue asks for the non-parametric CGA palettes to be externalized into bundled resource files. The two parametric palettes, `tandy` and `ibm5153`, should remain hardcoded because they depend on runtime parameters and calculations.

### Expected Behavior

The user-facing behavior should stay the same. Existing `cga_colors` settings such as `default`, `tandy-warm`, `agi-amiga-v1`, `scumm-amiga`, `colodore`, and `dga16` should still work.

The internal implementation should change so that fixed palettes are loaded from resource files instead of being stored directly as C++ arrays.

The `tandy` and `ibm5153` palettes should remain internal because they accept parameters such as:

```ini
cga_colors = tandy 100
cga_colors = ibm5153 60
```

### Current Behavior

The fixed CGA palettes are currently stored in `src/ints/int10_modes.cpp` as hardcoded `constexpr cga_colors_t` arrays. The `configure_cga_colors()` function then maps a user-provided `cga_colors` setting directly to one of those hardcoded arrays.

Examples of hardcoded palette arrays found:

```cpp
cga_colors_default
cga_colors_scumm_amiga
cga_colors_agi_amiga_v1
cga_colors_agi_amiga_v2
cga_colors_agi_amiga_v3
cga_colors_agi_amigaish
cga_colors_colodore_sat50
cga_colors_colodore_sat60
cga_colors_tandy_warm
cga_colors_dga16
```

### Affected Components

* `src/ints/int10_modes.cpp`

  * Contains the hardcoded CGA palette arrays.
  * Contains `configure_cga_colors()`, which selects the palette.
  * Contains parsing logic for custom CGA color definitions.

* `resources/cga_colors/`

  * New resource directory planned for fixed CGA palette files.

* Build/resource packaging files

  * May need updates depending on how DOSBox Staging includes bundled resources.

---

## Reproduction Process

### Environment Setup

1. Forked the `dosbox-staging` repository to my GitHub account.
2. Cloned my fork locally.
3. Added the upstream repository as a remote.
4. Created a focused working branch:

```bash
git checkout -b externalize-cga-palettes
```

5. Read through the DOSBox Staging contributing documentation to understand the expected workflow, coding style, and commit expectations.
6. Searched the codebase to find where CGA palettes are defined and selected.

### Steps to Reproduce

1. Open the DOSBox Staging repository locally.

2. Search for the `cga_colors` setting:

```bash
git grep -n "cga_colors"
```

3. Open the main implementation file where CGA palette logic appears:

```text
src/ints/int10_modes.cpp
```

4. Search for hardcoded CGA palette definitions:

```bash
git grep -n "constexpr cga_colors_t"
```

5. Confirm that several fixed palettes are stored directly in C++ as `constexpr cga_colors_t` arrays.

6. In `src/ints/int10_modes.cpp`, find `configure_cga_colors()`.

7. Confirm that `configure_cga_colors()` maps user configuration names directly to hardcoded palette arrays. For example:

```cpp
if (cga_colors_setting == "default") {
    return cga_colors_default;
}
```

8. Confirm that the `tandy` and `ibm5153` palettes are handled differently through dedicated functions:

```cpp
handle_cga_colors_prefs_tandy(...)
handle_cga_colors_prefs_ibm5153(...)
```

9. Read issue discussion/comments to confirm the intended direction:

   * Externalize non-parametric palettes into resources.
   * Keep `tandy` and `ibm5153` hardcoded because they are parametric.

10. Create the first resource directory and default palette resource as a small first step:

```text
resources/cga_colors/default
```

### Reproduction Evidence

* **Branch in my fork:** [externalize-cga-palettes](https://github.com/mashrafeearyan07/dosbox-staging/tree/externalize-cga-palettes)

* **Evidence found in code:**

  * The CGA palettes are defined as hardcoded `constexpr cga_colors_t` arrays in `src/ints/int10_modes.cpp`.
  * The `configure_cga_colors()` function directly returns those hardcoded arrays based on the value of the `cga_colors` setting.
  * The `tandy` and `ibm5153` options are handled through separate parametric helper functions, which supports the issue requirement that they remain hardcoded.

* **First reproduction/resource commit:**

  * Added `resources/cga_colors/default` as the first external CGA palette resource.
  * The default palette values were converted from the existing `Rgb666` C++ values into standard `#rrggbb` hex color values for a human-readable resource file.

---

## Solution Approach

### Analysis

This issue is about separating static palette data from palette selection and initialization logic.

Currently, the source file contains both:

1. Fixed palette data.
2. Logic for choosing and applying a palette.

The cleaner design is:

1. Store fixed palette data in resource files.
2. Keep palette selection logic in C++.
3. Load fixed palettes from resources on demand.
4. Keep parametric palettes in C++ because they are calculated from user-provided values.

### Proposed Solution

Move the non-parametric CGA palettes into `resources/cga_colors/` files.

Each resource file should represent one named palette. For example:

```text
resources/cga_colors/default
resources/cga_colors/tandy-warm
resources/cga_colors/agi-amiga-v1
resources/cga_colors/scumm-amiga
resources/cga_colors/colodore
resources/cga_colors/dga16
```

Each file can use a simple config-style format:

```ini
[cga_colors]
black = #000000
blue = #0000aa
green = #00aa00
cyan = #00aaaa
red = #aa0000
magenta = #aa00aa
brown = #aa5500
light-grey = #aaaaaa
dark-grey = #555555
light-blue = #5555ff
light-green = #55ff55
light-cyan = #55ffff
light-red = #ff5555
light-magenta = #ff55ff
yellow = #ffff55
white = #ffffff
```

The C++ code should then load the selected resource file, parse the color values, and produce the same internal `cga_colors_t` structure that the hardcoded arrays currently provide.

### Implementation Plan

1. **Confirm resource-loading pattern**

   * Search the codebase for how DOSBox Staging loads bundled resources such as shaders or translations.
   * Reuse the existing project resource-loading style instead of inventing a custom file path approach.

2. **Add initial resource file**

   * Add `resources/cga_colors/default`.
   * Convert the existing `cga_colors_default` values from `Rgb666` to standard `#rrggbb` hex format.
   * Commit this as a small standalone commit.

3. **Add CGA palette resource loader**

   * Create a helper function that loads a named CGA palette resource.
   * Parse the `[cga_colors]` section and collect the 16 required color names in the correct order.
   * Convert parsed `Rgb888` values into the internal `Rgb666` format.

4. **Update `configure_cga_colors()` incrementally**

   * First, make only `default` load from `resources/cga_colors/default`.
   * Keep the existing hardcoded `cga_colors_default` as a fallback while testing.
   * Verify that behavior remains unchanged.

5. **Externalize remaining fixed palettes**

   * Move the remaining non-parametric palettes into resource files:

     * `tandy-warm`
     * `agi-amiga-v1`
     * `agi-amiga-v2`
     * `agi-amiga-v3`
     * `agi-amigaish`
     * `scumm-amiga`
     * `colodore`
     * `colodore-sat`
     * `dga16`

6. **Keep parametric palettes hardcoded**

   * Do not externalize:

     * `tandy`
     * `ibm5153`
   * Keep their existing helper functions intact.

7. **Clean up parsing logic if appropriate**

   * If the resource loader needs hex parsing, reuse or extract the existing parsing logic instead of duplicating it.
   * Keep this as a separate commit if it becomes a meaningful refactor.

8. **Test the change**

   * Build DOSBox Staging.
   * Confirm `cga_colors = default` behaves the same as before.
   * Test at least one non-default fixed palette after externalization.
   * Confirm `tandy` and `ibm5153` still work with parameters.
   * Run formatting and any relevant project checks before opening a pull request later.

---

## Testing Strategy

### Unit or Parser Tests

* [ ] Validate parsing of valid `#rrggbb` color values.
* [ ] Validate handling of malformed color values.
* [ ] Validate that a resource file must contain all 16 CGA colors.
* [ ] Validate that missing or invalid resource values fall back safely or produce a useful warning.

### Integration Tests

* [ ] Verify that loading `resources/cga_colors/default` produces the same internal palette as the original hardcoded `cga_colors_default`.
* [ ] Verify that each externalized fixed palette maps to the same color values as before.
* [ ] Verify that `tandy` and `ibm5153` remain handled by their existing parametric code paths.

### Manual Testing

* [ ] Start DOSBox Staging with `cga_colors = default`.
* [ ] Start DOSBox Staging with at least one externalized non-default palette.
* [ ] Test `cga_colors = tandy 100`.
* [ ] Test `cga_colors = ibm5153 60`.
* [ ] Visually confirm that CGA colors do not regress compared to the original implementation.

---

## Implementation Notes

### Phase II Progress

* Forked and cloned the repository.
* Created the branch `externalize-cga-palettes`.
* Located the hardcoded CGA palette arrays in `src/ints/int10_modes.cpp`.
* Located the palette selection function `configure_cga_colors()`.
* Confirmed that `tandy` and `ibm5153` are special parametric palettes and should remain hardcoded.
* Added the first resource file: `resources/cga_colors/default`.
* Converted the default CGA palette from internal `Rgb666` values to human-readable `#rrggbb` values.

### Code Changes So Far

* **Files added:**

  * `resources/cga_colors/default`

* **Files investigated:**

  * `src/ints/int10_modes.cpp`
  * `resources/`
  * `resources/shaders/`
  * `resources/translations/`

### Approach Decisions

* I am using small focused commits instead of one large change.
* I started with only the `default` palette to prove the resource format before changing C++ loading logic.
* I am not changing `tandy` or `ibm5153` because they are parametric and should remain internal.
* I am not opening a pull request yet because Phase II only requires reproduction evidence and an implementation plan.

---

## Pull Request

**PR Link:** Not submitted yet.

**Current Status:** No pull request for Phase II. The current goal is only to document reproduction evidence and the implementation plan.

**Future PR Goal:** Externalize the fixed `cga_colors` palettes into resource files while keeping the parametric `tandy` and `ibm5153` palettes hardcoded.

---

## Learnings & Reflections

### Technical Skills Gained

* Learned how to trace a configuration option through a large C++ codebase.
* Learned how CGA palette data is represented internally using `Rgb666`.
* Learned why resource files are useful for fixed data that users may want to inspect or customize.
* Practiced making small focused commits for open-source contribution.

### Challenges Overcome

The hardest part was understanding the difference between fixed palettes and parametric palettes. At first, all CGA palettes looked like similar color data, but `tandy` and `ibm5153` are different because they are calculated from user-provided parameters.

Another challenge was separating the video mode tables from the actual CGA palette data. The CGA mode tables appear earlier in the file, but the hardcoded palette arrays are the actual target for this issue.

### What I'd Do Differently Next Time

I would start by mapping the flow earlier:

```text
config setting -> configure_cga_colors() -> selected cga_colors_t -> init_all_palettes()
```

That makes the issue much easier to understand.

---

## Resources Used

* [DOSBox Staging Contributing Guide](https://github.com/dosbox-staging/dosbox-staging/blob/main/docs/CONTRIBUTING.md)
* [GitHub Issue #2051 Thread](https://github.com/dosbox-staging/dosbox-staging/issues/2051)
* [Related Issue #2014: Add new palette options](https://github.com/dosbox-staging/dosbox-staging/issues/2014)
* [My contribution branch](https://github.com/mashrafeearyan07/dosbox-staging/tree/externalize-cga-palettes)
