## 8. DRY
Every piece of business knowledge has one authoritative source. Do not confuse knowledge duplication (dangerous) with code that looks similar (often harmless).

<red-flag>
- You are copy-pasting a block from another file and changing one or two values inside it.
- You are adding a boolean flag to a shared function so it can serve a second caller with slightly different behavior.
- A grep for a business rule (e.g., "30 days", "5 attempts") returns hits in more than one module.
</red-flag>

<examples>
- Using a single eligibility function called by both the API and the background job instead of duplicating the rule.
- Using separate domain functions for billing and marketing discounts instead of merging look-alikes with a flag.
- Using named constants in a shared config instead of magic numbers scattered across files.
- Using a schema definition as the single source for both validation and API documentation instead of maintaining two copies.
- Using a shared date-formatting utility instead of calling strftime with the same format string in 12 files.
</examples>
