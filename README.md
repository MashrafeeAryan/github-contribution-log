# Contribution [#]: Externalise hardcoded cga_colors palettes

**Contribution Number:** 1  
**Student:** Mashrafee Aryan  
**Issue:** [https://github.com/dosbox-staging/dosbox-staging/issues/2051](https://github.com/dosbox-staging/dosbox-staging/issues/2051)  
**Status:** In Progress  

---

## Why I Chose This Issue

I chose this issue because I wanted to practice and improve my C++ coding skills. This project had the right amount of challenge and depth to help me learn and grow as a developer. I also saw that the project maintainers were very active and helpful, which gave me confidence that I could get good guidance if I got stuck.

Additionally, the problem itself made total sense to me. I clearly understood what needed to be fixed and what steps I had to take to complete the task. This made it the perfect issue for me to jump into and start contributing.

---

## Understanding the Issue

### Problem Description

The way the emulator handles CGA colors is outdated and not user-friendly. Previously, CGA color palettes were hardcoded and required passing raw lists of hex values separated by commas into config files. We replaced this system with external `.preset` configuration files parsed using the SimpleIni library, placed inside the resources folder, and updated the color keys to use standard, human-readable color names.

### Expected Behavior

The emulator should easily load predefined or user-created CGA color configuration preset files (`.preset`) from the resources directory using clear key-value pairs. If any color entry or file structure is missing or invalid, it should log a clear, specific warning message and gracefully fall back to default colors without crashing or failing silently.

### Current Behavior

Previously, loading CGA colors required deciphering long lists of hardcoded hex values in configuration strings. Furthermore, error handling during initialization would fail silently or throw secondary `MESSAGE NOT FOUND!` log errors because error text messages were registered too late in the startup sequence.

### Affected Components

- **Video Subsystem / INT10:** `src/gui/render.cpp`, `src/ints/int10*.cpp`, and palette initialization functions.
- **Resources & Config:** CGA palette preset files stored in the `resources/` folder.
- **Localization/Logging:** `register_int10_text_messages()` and log output formatting.

---

## Reproduction Process

### Environment Setup

- Cloned the `dosbox-staging` repository and built the codebase locally using Meson/Ninja tools.
- Ensured all local unit tests passed prior to modifying the video and INT10 subsystems.

### Steps to Reproduce

1. Launch DOSBox-Staging with custom or invalid inline `cga_colors` values in the configuration.
2. Observe confusing, non-descriptive error logs or missing error strings (`INT10H_CGA_PALETTE_INVALID_CONTENT` showing `MESSAGE NOT FOUND!`).
3. Attempt to configure custom color palettes via inline hex strings, noting the lack of user friendliness.

### Reproduction Evidence

```text
2026-07-15 17:18:12.131 | INT10H: Invalid 'cga_colors' value: 16 colors must be specified (found only 0)
2026-07-15 17:18:12.132 | LOCALE: Message 'INT10H_CGA_PALETTE_INVALID_CONTENT' not found
2026-07-15 17:18:12.132 | INT10H:  MESSAGE NOT FOUND!
```

**My findings:** Discovered that `register_int10_text_messages()` was being called *after* `INT10_SetupPalette()`, causing premature initialization error logs to fail string lookups.

---

## Solution Approach

### Analysis

1. Hardcoded inline CGA hex strings were difficult to read and maintain.
2. Preset files needed to adopt the `.preset` extension and use intuitive color names instead of obscure raw color names.
3. Message registration order in `INT10_Init()` was causing log failures when palette setup failed.
4. Unnecessary inline palette parsing code needed to be cleaned up and removed.

### Proposed Solution

1. Externalize CGA palettes into `.preset` files inside the resources folder using SimpleIni.
2. Standardize color key names across configuration files (`black`, `blue`, `green`, `cyan`, `red`, `magenta`, `brown`, `light_gray`, `dark_gray`, `bright_blue`, `bright_green`, `bright_cyan`, `bright_red`, `bright_magenta`, `yellow`, `white`).
3. Update `load_cga_palette_resources` to parse `.preset` extension files.
4. Move `register_int10_text_messages()` to the top of `INT10_Init()` so all localized error strings are registered before palette setup begins.
5. Remove obsolete inline palette parsing functions, inline documentation, and associated legacy tests.

### Implementation Plan

**Understand:** Transition from hardcoded/inline palette configuration to external `.preset` files with clear error logging.

**Match:** Follow existing SimpleIni resource loading patterns used across DOSBox-Staging config readers.

**Plan:**
1. Reorder execution in `INT10_Init()` so `register_int10_text_messages()` runs first.
2. Refactor `load_cga_palette_resources` to look for `.preset` files and parse the 16 updated color names.
3. Update default palette resource files (such as `colodore.preset`) with `[cga_colors]` section headers and descriptive key names.
4. Remove legacy inline palette support functions and tests.
5. Indent and format the `Note:` section at the end of the `cga_colors` description matching the `window_position` help text style.

---

## Testing Strategy

### Unit Tests & Error Handling Verification

Extensive manual and log verification was performed across all edge cases for `.preset` file parsing:

- [x] **Unknown Color Key:** Renamed `black` to `lack` in `colodore.preset`.  
  ```text
  2026-07-18 22:05:02.245 | INT10H: Palette file 'colodore' contains an unknown color key 'lack', falling back to default colors
  ```
- [x] **Invalid Color Value:** Set white hex value to `#asdf`.  
  ```text
  2026-07-18 22:12:32.619 | INT10H: Palette file 'colodore' has an invalid value '#asdf' for color 'white', falling back to default colors
  ```
- [x] **Extra Key:** Added a 17th key (`blah = #123456`).  
  ```text
  2026-07-18 22:14:31.809 | INT10H: Palette file 'colodore' contains an unknown color key 'blah', falling back to default colors
  ```
- [x] **Missing File:** Specified non-existent preset name `blah`.  
  ```text
  2026-07-18 22:29:49.672 | INT10H: CGA palette preset 'blah' not found, falling back to default colors
  ```
- [x] **Missing Section Header:** Omitted `[cga_colors]`.  
  ```text
  2026-07-18 22:33:17.066 | INT10H: Missing [cga_colors] section header in palette file: 'colodore', falling back to default colors
  ```
- [x] **Missing Key:** Removed the `blue` key from the preset.  
  ```text
  2026-07-18 22:35:50.658 | INT10H: Palette file 'colodore' is missing the required color 'blue', falling back to default colors
  ```

### Manual Testing

- Verified visual formatting of `cga_colors` help description in command line help to ensure proper indentation alignment with `window_position`.
- Confirmed that presets load cleanly during emulator boot without falling back to defaults when using valid `.preset` files.

---

## Implementation Notes

### Key Accomplishments & Progress

- Standardized color names across config resources to:
  * `black`
  * `blue`
  * `green`
  * `cyan`
  * `red`
  * `magenta`
  * `brown`
  * `light_gray`
  * `dark_gray`
  * `bright_blue`
  * `bright_green`
  * `bright_cyan`
  * `bright_red`
  * `bright_magenta`
  * `yellow`
  * `white`
- Updated file extension handling in `load_cga_palette_resources` to support `.preset`.
- Fixed initialization timing bug in `INT10_Init()`.
- Removed deprecated inline palette parsing code and obsolete test cases.
- Updated project documentation and locally verified all changes.

### Code Changes

- **Files modified:**
  - `src/ints/int10.cpp` (Moved message registration to top of `INT10_Init`).
  - `src/ints/int10_modes.cpp` / palette resource loaders (`load_cga_palette_resources`).
  - Preset resource files (e.g., `colodore.preset`).
  - Configuration & documentation files.

---

## Pull Request

**PR Link:** https://github.com/dosbox-staging/dosbox-staging/pull/4976

**PR Description Summary:**
> Updated CGA color loading to use external `.preset` resource files with human-readable color key names. Reordered `INT10_Init` to ensure text messages register prior to palette setup, providing clear, specific error logging on bad palette configs. Removed legacy inline palette parsing logic.

**Status:** In Progress / Awaiting Review

---

## Learnings & Reflections

### Technical Skills Gained

- Understanding initialization order and dependency chains in C++ software architecture.
- Working with SimpleIni for parsing structured configuration files in C++.
- Following standard Git conventions (imperative commit messages, line limits) and GitHub markdown formatting standards for open-source PRs.

### Challenges Overcome

- Diagnosing the root cause of `MESSAGE NOT FOUND!` errors in early initialization phases.
- Transitioning away from inline palette logic without breaking existing fallback behaviors.

### What I'd Do Differently Next Time

- Check subsystem text/locale registration order earlier when debugging missing log messages.

---

## Resources Used

- [DOSBox-Staging GitHub Repository](https://github.com/dosbox-staging/dosbox-staging)
- [DOSBox-Staging Issue #2051](https://github.com/dosbox-staging/dosbox-staging/issues/2051)
- SimpleIni Library Documentation
