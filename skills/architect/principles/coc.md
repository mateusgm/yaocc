## 11. Convention over Configuration
Follow established naming patterns, directory structures, and project layouts consistently. When you override a convention, document why.

<why>
  This removes decision fatigue, makes the codebase navigable by anyone familiar with the conventions, and reduces onboarding time. 
</why>

<red-flags>
- A new team member would need tribal knowledge, not just the directory structure, to find where your new code lives.
- You are inventing a new naming pattern for this one file because the existing convention "doesn't quite fit."
- Your function name starts with a verb that doesn't match the return-type convention of the codebase (e.g., `get_` but returns None on missing instead of raising like every other `get_`).
</red-flags>

<examples>
- Using a standard project layout (models/, services/, api/, tests/) instead of ad-hoc file placement.
- Using verb-prefixed function names (is_, get_, list_, create_, ensure_) instead of generic names.
- Using inline comments to document convention overrides instead of leaving silent deviations.
- Using consistent test file naming (test_<module>.py) instead of a flat tests/ folder with arbitrary names.
- Using framework-default settings for logging, encoding, and locale instead of custom config for each.
</examples>
