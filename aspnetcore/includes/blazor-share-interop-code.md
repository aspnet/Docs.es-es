## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="f7067-101">Uso compartido de código de interoperabilidad en una biblioteca de clases</span><span class="sxs-lookup"><span data-stu-id="f7067-101">Share interop code in a class library</span></span>

<span data-ttu-id="f7067-102">El código de interoperabilidad de JS se puede incluir en una biblioteca de clases, lo que permite compartir el código en un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="f7067-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="f7067-103">La biblioteca de clases controla la inserción de recursos de JavaScript en el ensamblado compilado.</span><span class="sxs-lookup"><span data-stu-id="f7067-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="f7067-104">Los archivos de JavaScript se colocan en la carpeta *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="f7067-104">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="f7067-105">Las herramientas se encargan de insertar los recursos cuando se compila la biblioteca.</span><span class="sxs-lookup"><span data-stu-id="f7067-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="f7067-106">Se hace referencia al paquete NuGet compilado en el archivo de proyecto de la aplicación de la misma manera que se hace referencia a cualquier paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="f7067-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="f7067-107">Una vez restaurado el paquete, el código de la aplicación puede llamar a JavaScript como si fuera C#.</span><span class="sxs-lookup"><span data-stu-id="f7067-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="f7067-108">Para obtener más información, vea <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="f7067-108">For more information, see <xref:blazor/class-libraries>.</span></span>