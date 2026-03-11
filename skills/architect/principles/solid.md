## 7. SOLID
Use Single Responsibility to keep classes focused. Use Open/Closed to extend behavior without modifying stable code. Use Liskov Substitution to ensure subtypes are safe drop-in replacements. Use Interface Segregation to avoid forcing clients to depend on methods they don't use. Use Dependency Inversion to decouple high-level policy from low-level detail. Apply each when it reduces coupling and increases cohesion — stop when it only adds files and indirection.

<red-flags>
- Adding a new feature requires modifying an existing class rather than extending or composing new behavior alongside it.
- Your constructor's parameter list is growing — the new dependency you're injecting is the sixth or seventh.
- You are importing a concrete third-party client directly into a module that contains business rules.
</red-flags>

<examples>
- Using focused classes (ReportGenerator + ReportMailer) instead of a God class that queries, formats, sends, and logs.
- Using a Protocol abstraction instead of importing Stripe directly inside business logic.
- Using substitutable subtypes instead of subclasses that raise NotImplementedError on inherited methods.
- Using narrow interfaces (Readable, Writable) instead of one fat interface that forces implementors to stub unused methods.
- Using dependency injection at the composition root instead of instantiating collaborators inside constructors.
</examples>
