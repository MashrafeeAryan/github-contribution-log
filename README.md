# github-contribution-log
# Contribution [1]: Externalise hardcoded cga_colors palettes

**Contribution Number:** 1  
**Student:** Mashrafee  
**Issue:** [Externalise hardcoded cga_colors palettes #2051](https://github.com/dosbox-staging/dosbox-staging/issues/2051)  
**Status:** Phase I / In Progress

---

## Why I Chose This Issue

I’m an undergraduate Computer Science student looking to get my feet wet in open-source. I’ve got a decent grasp of C++, but I want to see how it’s actually used in a large, performance-critical codebase like an emulator. 

This issue felt like the perfect entry point because it’s labeled as a "good first issue." It’s a clean, well-defined refactoring task that will force me to explore how the project handles configurations and static resources, without throwing me completely into the deep end of emulation logic. Plus, it gives me a chance to get used to the project's workflow and coding standards.

---

## Understanding the Issue

### Problem Description

Right now, the CGA (Color Graphics Adapter) color palettes are hardcoded straight into the source files. Keeping massive static arrays inline makes the rendering code unnecessarily cluttered and rigid if anyone ever wants to tweak or update them later.

### Expected Behavior

The static CGA palettes should be moved out of the source code and into dedicated external resource files. While doing this, we should also extract the hex color parsing logic into its own reusable helper function so the code stays DRY (Don't Repeat Yourself).

The big catch here is that the **Tandy** and **IBM 5153** palettes are parametric—meaning they rely on specific dynamic calculations and parameters. Because of that, they **must stay hardcoded** as exceptions.

### Current Behavior

The `cga_colors` tables are sitting directly inside the implementation code, forcing the engine to compile them statically rather than pulling them dynamically from an external config or clean resource array.

### Affected Components

* **Video/Graphics Emulation:** The core rendering files where CGA palettes are initialized.
* **Utility Headers:** Wherever we decide to house the new hex string parsing helper functions.

---

## Reproduction Process

### Environment Setup

* Forked and cloned the `dosbox-staging` repository.
* Going through the [Contributing Guide](https://github.com/dosbox-staging/dosbox-staging/blob/main/docs/CONTRIBUTING.md) to set up dependencies and make sure everything builds cleanly on my machine.
* *(Note: Add any weird build errors or fixes you run into right here!)*

### Steps to Reproduce

1. Grep/search the codebase for references to `cga_colors`.
2. Look at how they are initialized to see where the data is tightly coupled with the logic.

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** Found the hardcoded arrays alongside the dynamic logic for the Tandy and IBM 5153 palettes.

---

## Solution Approach

### Analysis

The issue is classic tight coupling—data (the color hex codes) is mixed in with execution logic. We need to untangle them without breaking the special math required for the Tandy and IBM 5153 setups.

### Proposed Solution

Extract the static hex palettes into an external resource file. Then, implement a clean utility function that parses these hex strings into the internal formats the emulator expects during setup, leaving the dynamic palettes untouched.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** Separate static CGA palettes into external resources, keep Tandy/IBM 5153 inline, and write a helper for hex parsing.

**Match:** Check out how the codebase handles other video options or configuration setups to make sure my approach fits the existing style.

**Plan:**
1. Find the exact file where `cga_colors` lives.
2. Move the static hex values out into a resource structure.
3. Write/move a helper to handle the string-to-hex conversion.
4. Ensure Tandy and IBM 5153 logic is completely unaffected.
5. Update tests.

**Implement:** [Link to your branch/commits as you work]

**Review:** Double-check `CONTRIBUTING.md` to ensure my formatting, naming conventions, and syntax match the project's standards.

**Evaluate:** Boot up some classic CGA games to make sure the colors still look exactly like they do on the master branch.

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: Validate hex color parser helper with valid hex input (e.g., `#FFFFFF`, `0x000000`).
- [ ] Test case 2: Validate hex color parser handling of malformed input strings.

### Integration Tests

- [ ] Integration scenario 1: Verify that loading the externalized palettes generates the exact same color map arrays as the original code.

### Manual Testing

* Run a few CGA games locally and visually confirm that the colors aren't broken.

---

## Implementation Notes

### Week 1 Progress

* Dropped a comment on the issue thread to say hi to the maintainer and claim the issue.
* Read through the contributor documentation.
* Started digging into the video source directory to map out where the colors are defined.

### Code Changes

* **Files modified:** [List]
* **Key commits:** [Links to important commits]
* **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** This PR closes #2051 by externalizing the static `cga_colors` palettes into separate resources. Per the requirements, the parametric Tandy and IBM 5153 palettes remain hardcoded, and a reusable helper function was created to clean up hex string parsing.

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** Awaiting review

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

* [DOSBox Staging Contributing Guide](https://github.com/dosbox-staging/dosbox-staging/blob/main/docs/CONTRIBUTING.md)
* [GitHub Issue #2051 Thread](https://github.com/dosbox-staging/dosbox-staging/issues/2051)
