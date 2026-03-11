## 3. Keep it simple (KISS)
Choose the simplest implementation that fully satisfies the requirement. Avoid premature optimization. Before adding any abstraction, pattern, or indirection, confirm that removing it would make the code meaningfully harder to change or understand.

<why>
  Simpler code has fewer bugs, is cheaper to maintain, and is easier for others to modify safely. 
</why>

<red-flags>
- You would need a diagram to explain your change to a teammate — the code itself should have been explanation enough.
- You are introducing a new file solely to house an interface or base class that has exactly one implementor.
- You are writing a helper function to work around the complexity of a helper function you wrote earlier in the same PR.
</red-flags>

<examples>
- Using a dict lookup instead of a class hierarchy
- Using a readable for-loop instead of a chained comprehension that requires a comment to decode.
- Using stdlib lru_cache instead of deploying Redis for a function with 6 possible inputs.
- Using a flat script instead of a microservice for a task that runs once a day and touches one table.
</examples>
