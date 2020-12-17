## <a name="share-interop-code-in-a-class-library"></a>Uso compartido de código de interoperabilidad en una biblioteca de clases

El código de interoperabilidad de JS se puede incluir en una biblioteca de clases, lo que permite compartir el código en un paquete NuGet.

La biblioteca de clases controla la inserción de recursos de JavaScript en el ensamblado compilado. Los archivos de JavaScript se colocan en la carpeta `wwwroot`. Las herramientas se encargan de insertar los recursos cuando se compila la biblioteca.

Se hace referencia al paquete NuGet compilado en el archivo de proyecto de la aplicación de la misma manera que se hace referencia a cualquier paquete NuGet. Una vez restaurado el paquete, el código de la aplicación puede llamar a JavaScript como si fuera C#.

Para obtener más información, vea <xref:blazor/components/class-libraries>.
