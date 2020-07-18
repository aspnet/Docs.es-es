<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Deshabilitar la comprobación de la cuenta predeterminada

Con las plantillas predeterminadas, se redirige al usuario a, `Account.RegisterConfirmation` donde puede seleccionar un vínculo para que la cuenta se confirme. El valor predeterminado `Account.RegisterConfirmation` ***solo*** se usa para las pruebas; la comprobación automática de cuentas debe estar deshabilitada en una aplicación de producción.

Para requerir una cuenta confirmada y evitar el inicio de sesión inmediato en el registro, establezca `DisplayConfirmAccountLink = false` en */areas/Identity/pages/Account/RegisterConfirmation.cshtml.CS*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]