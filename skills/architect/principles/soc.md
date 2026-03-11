
### 2. Separation of Concerns
Give each module, class, and function exactly one job, defined by *what domain problem it solves* — not by what technology it uses. 

<why>
  This makes it possible to change, test, and understand one part of the system without reading or risking the rest.
</why

<red-flags>
- Your new function needs to import from three or more unrelated packages (e.g., the ORM, a serializer, and an HTTP client).
- You are adding a try/except around someone else's module because your change introduced a failure mode that belongs in their layer.
- A reviewer will likely ask "why does this file need to know about X?"
</red-flags>

<examples>
- Using pure domain functions instead of embedding SQL inside business logic.
- Using domain events instead of direct calls from order processing to email, SMS, and analytics.
- Using staged pipelines (parse → validate → transform) instead of one function that does all three.
- Using a dedicated error-formatting layer instead of scattering HTTP status code logic across services.
</examples>
