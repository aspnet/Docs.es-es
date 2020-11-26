* Para confiar en el certificado de desarrollo de HTTPS, ejecute el comando siguiente:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  El comando anterior no funciona en Linux. Vea la documentación de su distribución de Linux para confiar en un certificado.

  El comando anterior muestra el siguiente cuadro de diálogo:

  ![Cuadro de diálogo de advertencia de seguridad](~/getting-started/_static/cert.png)

* Si acepta confiar en el certificado de desarrollo, seleccione **Sí**.

  Para obtener más información, vea [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS ](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]