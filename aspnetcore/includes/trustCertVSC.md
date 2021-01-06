* <span data-ttu-id="6c847-101">Para confiar en el certificado de desarrollo de HTTPS, ejecute el comando siguiente:</span><span class="sxs-lookup"><span data-stu-id="6c847-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="6c847-102">El comando anterior no funciona en Linux.</span><span class="sxs-lookup"><span data-stu-id="6c847-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="6c847-103">Vea la documentación de su distribución de Linux para confiar en un certificado.</span><span class="sxs-lookup"><span data-stu-id="6c847-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="6c847-104">El comando anterior muestra el siguiente cuadro de diálogo:</span><span class="sxs-lookup"><span data-stu-id="6c847-104">The preceding command displays the following dialog:</span></span>

  ![Cuadro de diálogo de advertencia de seguridad](~/getting-started/_static/cert.png)

* <span data-ttu-id="6c847-106">Si acepta confiar en el certificado de desarrollo, seleccione **Sí**.</span><span class="sxs-lookup"><span data-stu-id="6c847-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="6c847-107">Para obtener más información, vea [Confiar en el certificado de desarrollo de ASP.NET Core HTTPS ](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="6c847-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]