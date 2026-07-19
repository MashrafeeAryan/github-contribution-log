# Contribution [#]: Externalise hardcoded cga_colors palettes

**Contribution Number:** 1 
**Student:** Mashrafee Aryan
**Issue:** https://github.com/dosbox-staging/dosbox-staging/issues/2051 
**Status:** In Progress

---

## Why I Chose This Issue

I chose this issue because I wanted to practice and improve my C++ coding skills. This project had the right amount of challenge and depth to help me learn and grow as a developer. I also saw that the project maintainers were very active and helpful, which gave me confidence that I could get good guidance if I got stuck.

Additionally, the problem itself made total sense to me. I clearly understood what needed to be fixed and what steps I had to take to complete the task. This made it the perfect issue for me to jump into and start contributing.

---

## Understanding the Issue

### Problem Description

The way the emulator loads cga-colors is outdated and not user friendly. We are going to replace it with more user friendly option using SimpleIni library and move the colors configuration files to resources folder.

### Expected Behavior

We should be able to load the cga-color configuration file or our own custom file effectively without any error in a very user friendly way.

### Current Behavior
We can still load the cga-colors but it is not user friendly. It is really hard to decipher 16 hex values seperated by commas affecting color configuration of the emulator.

### Affected Components
The render part and ints part
---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. Fork it
2. Build it
3. Run the .exe file

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

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

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
