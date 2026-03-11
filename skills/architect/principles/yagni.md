## 5. Build only what's needed (YAGNI)
Build only what's needed, do not write code for hypothetical future requirements. YAGNI applies to features and abstractions, never to code quality: error handling, input validation, logging, and tests are always needed.

<why>
  Nearly half of all features built in typical projects are never used. Every speculative feature adds code to read, test, and maintain — and the predicted need is usually wrong.
</why>

<red-flags>
- You are adding a parameter, config option, or extension point that no current caller uses.
- You catch yourself saying "while I'm in here, I might as well also support X."
- Your PR justification will need to mention a future need as the motivation rather than a current one.
</red-flags>

<examples>
- Using a direct email call instead of a pluggable multi-channel notification system when only email exists.
- Using if/elif dispatch instead of a dynamic plugin loader for three stable export formats.
- Using a hardcoded admin role check instead of a configurable RBAC engine when there are only two roles.
- Using a single-file script instead of a CLI framework with subcommands for a tool that does one thing.
</examples>
