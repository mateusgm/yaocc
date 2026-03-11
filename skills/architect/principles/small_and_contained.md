## 1. Ship small, ship continuously (CI/CD Mindset)
Write code that is always in a deployable state. Keep changes small and self-contained so they can be built, integrated, tested, and deployed independently.

<why>
  This enables rapid feedback, fast recovery from failures, and eliminates the risk of catastrophic big-bang merges.
</why>

<red-flags>
- Your change cannot be described in a single sentence — it likely needs to be split into smaller, independently deployable pieces.
- You are commenting out or disabling existing tests to make your change pass.
- Your branch has been open for more than two days without merging to main.
</red-flags>

<examples>
- Using feature flags instead of long-lived branches to keep incomplete work deployable.
- Using additive database migrations instead of destructive renames or drops during rolling deploys.
- Using stdlib-only modules instead of importing the ORM, framework, or message broker into utility code.
- Using contract tests between services instead of requiring all services to deploy simultaneously.
</examples>
