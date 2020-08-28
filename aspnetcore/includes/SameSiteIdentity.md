ASP.NET Core [identidad](xref:security/authentication/identity) no se ve afectado en gran medida por las [cookies de SameSite](xref:security/samesite) , excepto en escenarios avanzados como `IFrames` o `OpenIdConnect` integración.

Al usar `Identity` , ***no*** agregue ningún proveedor de cookies o llame a ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` , se `Identity` encarga de ello.