## <a name="troubleshoot"></a>Solución de problemas

### <a name="cookies-and-site-data"></a>Cookies y datos de sitios

Las cookies y los datos de sitios pueden persistir durante las actualizaciones de la aplicación e interferir con las pruebas y la solución de problemas. Borre los elementos siguientes al realizar cambios en el código de la aplicación, cambios en la cuenta de usuario con el proveedor o cuando el proveedor modifique la configuración de la aplicación:

* Cookies de inicio de sesión de usuario
* Cookies de aplicación
* Datos de sitios almacenados y en caché

El enfoque siguiente sirve para evitar que las cookies persistentes y los datos de sitios interfieran con las pruebas y la solución de problemas:

* Configuración de un explorador
  * Use un explorador para las pruebas y configúrelo para que elimine todas las cookies y los datos de sitios cada vez que se cierre.
  * Asegúrese de que el explorador se cierra manualmente o mediante el IDE siempre que se produzca cualquier cambio en la aplicación, el usuario de prueba o la configuración del proveedor.
* Use un comando personalizado para abrir un explorador en el modo incógnito o privado en Visual Studio:
  * Abra el cuadro de diálogo **Examinar con** mediante el botón **Ejecutar** de Visual Studio.
  * Seleccione el botón **Agregar**.
  * Proporcione la ruta de acceso al explorador en el campo **Programa**. Las siguientes rutas de acceso del archivo ejecutable son ubicaciones de instalación típicas para Windows 10. Si el explorador está instalado en una ubicación diferente o no usa Windows 10, proporcione la ruta de acceso al archivo ejecutable del explorador.
    * Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`
  * En el campo **Argumentos**, proporcione la opción de línea de comandos que utiliza el explorador para abrirse en el modo incógnito o privado. Algunos exploradores requieren la dirección URL de la aplicación.
    * Microsoft Edge: Use `-inprivate`.
    * Google Chrome: Use `--incognito --new-window {URL}`, donde el marcador de posición `{URL}` es la dirección URL que se va a abrir (por ejemplo, `https://localhost:5001`).
    * Mozilla Firefox: Use `-private -url {URL}`, donde el marcador de posición `{URL}` es la dirección URL que se va a abrir (por ejemplo, `https://localhost:5001`).
  * Proporcione un nombre en el campo **Nombre descriptivo**. Por ejemplo: `Firefox Auth Testing`.
  * Seleccione el botón **Aceptar**.
  * Para evitar tener que seleccionar el perfil de explorador para cada iteración de pruebas con una aplicación, establezca el perfil como predeterminado con el botón **Establecer como predeterminado**.
  * Asegúrese de que el explorador se cierra mediante el IDE siempre que se produzca cualquier cambio en la aplicación, el usuario de prueba o la configuración del proveedor.

### <a name="run-the-server-app"></a>Ejecución de la aplicación Server

Al probar y solucionar problemas de una aplicación Blazor hospedada, asegúrese de que está ejecutando la aplicación desde el proyecto **`Server`** . Por ejemplo, en Visual Studio, confirme que el proyecto Server está resaltado en el **Explorador de soluciones** antes de iniciar la aplicación con cualquiera de los métodos siguientes:

* Haga clic en el botón **Ejecutar**.
* En el menú, seleccione **Depurar** > **Iniciar depuración**.
* Presione <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Inspección del contenido de un JSON Web Token (JWT)

Para descodificar un JSON Web Token (JWT), use la herramienta [jwt.ms](https://jwt.ms/) de Microsoft. Los valores de la interfaz de usuario nunca salen del explorador.
