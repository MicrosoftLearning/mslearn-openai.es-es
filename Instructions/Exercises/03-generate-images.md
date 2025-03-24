---
lab:
  title: Generación de imágenes con IA
  description: Aprende a usar un modelo DALL-E de OpenAI para generar imágenes.
  status: new
---

# Generación de imágenes con IA

En este ejercicio, usarás el modelo de IA generativa DALL-E de OpenAI para generar imágenes. Vas a desarrollar la aplicación mediante Fundición de IA de Azure y Azure OpenAI Service.

Este ejercicio dura aproximadamente **30** minutos.

## Creación de un proyecto de Azure AI Foundry

Comencemos creando un proyecto de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen:

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](../media/ai-foundry-home.png)

1. En la página principal, selecciona **+Crear proyecto**.
1. En el Asistente para **crear un proyecto**, escribe un nombre de proyecto adecuado (como `my-ai-project`) y revisa los recursos de Azure que se crearán automáticamente para admitir el proyecto.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *un nombre único; por ejemplo, `my-ai-hub`*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea un nuevo grupo de recursos con un nombre único (como `my-ai-resources`) o selecciona uno existente*
    - **Ubicación**: selecciona **Ayudarme a elegir**, a continuación, selecciona **dalle** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crea un nuevo recurso de AI Services con un nombre adecuado (como `my-ai-services`) o usa uno existente.*
    - **Conectar Búsqueda de Azure AI**: omite la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Siguiente** y revisa tu configuración. Luego, selecciona **Crear** y espera a que se complete el proceso.
1. Cuando se cree el proyecto, cierra las sugerencias que se muestran y revisa la página del proyecto en el Portal de la Fundición de IA de Azure, que debe tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el Portal de la Fundición de IA de Azure.](../media/ai-foundry-project.png)

## Implementación de un modelo DALL-E

Ahora estás listo para implementar un modelo DALL-E para admitir la generación de imágenes.

1. En la barra de herramientas de la parte superior derecha de la página del proyecto de Fundición de IA de Azure, usa el icono **Características de versión preliminar** para habilitar la característica **Implementar modelos en el servicio de inferencia de modelos de Azure AI**.
1. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.
1. En la página **Modelos y puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+ Implementar modelo**, selecciona **Implementar modelo base**.
1. Busca el modelo **dall-e-3** en la lista, selecciónalo y confirma.
1. Acepta el contrato de licencia si se te solicita y, a continuación, implementa el modelo con la siguiente configuración seleccionando **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *nombre único para la implementación del modelo, por ejemplo `dall-e-3` (recuerda el nombre que asignes, lo necesitarás más adelante*).
    - **Tipo de implementación**: estándar
    - **Detalles de implementación**: *usa la configuración predeterminada*
1. Espera a que se **complete** el estado de aprovisionamiento de la implementación.

## Prueba del modelo en el área de juegos

Antes de crear una aplicación cliente, vamos a probar el modelo DALL-E en el área de juegos.

1. En la página del modelo DALL-E que implementaste, selecciona **Abrir en el área de juegos** (o en la página **Áreas de juegos**, abre el **Área de juegos de imágenes**).
1. Asegúrate de que la implementación de modelo DALL-E está seleccionada. A continuación, en el cuadro de **Solicitud**, escribe una solicitud como `Create an image of an robot eating spaghetti`.
1. Revisa la imagen resultante en el área de juegos:

    ![Captura de pantalla del área de juegos de imágenes con una imagen generada.](../media/images-playground.png)

1. Escribe una solicitud de seguimiento, como `Show the robot in a restaurant` y revisa la imagen resultante.
1. Sigue probando con nuevas solicitudes para refinar la imagen hasta que estés satisfecho con ella.

## Creación de una aplicación cliente

El modelo parece funcionar en el área de juegos. Ahora puedes usar el SDK de Azure OpenAI para usarlo en una aplicación cliente.

> **Sugerencia**: puedes elegir desarrollar la solución mediante C# de Python o Microsoft. Sigue las instrucciones de la sección adecuada para el idioma elegido.

### Preparación de la configuración de aplicación

1. En el Portal de la Fundición de IA de Azure, mira la página **Información general** del proyecto.
1. En el área **Detalles del proyecto**, anota la **Cadena de conexión del proyecto**. Usarás esta cadena de conexión para conectarte al proyecto en una aplicación cliente.
1. Abre una nueva pestaña del explorador (mantén el Portal de la Fundición de IA de Azure abierto en la pestaña existente). En la nueva pestaña, explora [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure, si se te solicita.
1. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal, para lo que deberás seleccionar un entorno de ***PowerShell***. Cloud Shell proporciona una interfaz de la línea de comandos en un panel situado en la parte inferior de Azure Portal.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *Bash*, cámbiala a ***PowerShell***.

1. En la barra de herramientas de Cloud Shell, en el menú **Configuración**, selecciona **Ir a la versión clásica** (esto es necesario para usar el editor de código).

    > **Sugerencia**: al pegar comandos en CloudShell, la salida puede ocupar una gran cantidad del búfer de pantalla. Puedes despejar la pantalla al escribir el comando `cls` para que te resulte más fácil centrarte en cada tarea.

1. En el panel de PowerShell, escribe los siguientes comandos para clonar el repo de GitHub para este ejercicio:

    ```
    rm -r mslearn-openai -f
    git clone https://github.com/microsoftlearning/mslearn-openai mslearn-openai
    ```

> **Nota**: sigue los pasos del lenguaje de programación elegido.

1. Una vez clonado el repo, ve a la carpeta que contiene los archivos de código de aplicación:  

    **Python**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/Python
    ```

    **C#**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/CSharp
    ```

1. En el panel de la línea de comandos de Cloud Shell, escribe el siguiente comando para instalar las bibliotecas que vas a usar:

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects openai requests
    ```

    *Puedes omitir los errores sobre la versión de pip y la ruta de acceso local*

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI
    ```

1. Escribe el siguiente comando para editar el archivo de configuración que se ha proporcionado:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    El archivo se abre en un editor de código.

1. En el archivo de código, reemplaza el marcador de posición **your_project_endpoint** por la cadena de conexión del proyecto (que copiaste de la página **Información general** del proyecto en el Portal de la Fundición de IA de Azure) y el marcador de posición **your_model_deployment** por el nombre que asignaste a la implementación del modelo dall-e-3.
1. Después de reemplazar los marcadores de posición, usa el comando **CTRL+S** para guardar los cambios y, a continuación, usa el comando **CTRL+Q** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Escritura del código para conectarte al proyecto y chatear con el modelo

> **Sugerencia**: al agregar código, asegúrate de mantener la sangría correcta.

1. Escribe el siguiente comando para editar el archivo de código que se ha proporcionado:

    **Python**

    ```
   code dalle-client.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. En el archivo de código, observa las instrucciones existentes que se han agregado en la parte superior del archivo para importar los espacios de nombres de SDK necesarios. Después, en el comentario **Agregar referencias**, agrega el código siguiente para hacer referencia a los espacios de nombres de las bibliotecas que has instalado anteriormente:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
   import requests
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.OpenAI;
   using OpenAI.Images;
    ```

1. En la función **principal**, en el comentario **Obtener ajustes de configuración**, observa que el código carga los valores de la cadena de conexión del proyecto y del nombre de implementación del modelo que has definido en el archivo de configuración.
1. En el comentario **Inicializar el cliente del proyecto**, agrega el siguiente código para conectarte a tu proyecto de Azure AI Foundry usando las credenciales de Azure con las que has iniciado sesión:

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. En el comentario **Obtener un cliente de OpenAI**, agrega el siguiente código para crear un objeto de cliente para chatear con un modelo:

    **Python**

    ```
   openai_client = project_client.inference.get_azure_openai_client(api_version="2024-06-01")

    ```

    **C#**

    ```
   ConnectionResponse connection = projectClient.GetConnectionsClient().GetDefaultConnection(ConnectionType.AzureOpenAI, withCredential: true);

   var connectionProperties = connection.Properties as ConnectionPropertiesApiKeyAuth;

   AzureOpenAIClient openAIClient = new(
        new Uri(connectionProperties.Target),
        new AzureKeyCredential(connectionProperties.Credentials.Key));

   ImageClient openAIimageClient = openAIClient.GetImageClient(model_deployment);

    ```

1. Ten en cuenta que el código incluye un bucle para permitir que un usuario escriba una indicación hasta que escriba "salir". A continuación, en la sección bucle, en el comentario **Generar una imagen**, agrega el código siguiente para enviar la indicación y recuperar la dirección URL para la imagen generada del modelo:

    **Python**

    ```python
   result = openai_client.images.generate(
        model=model_deployment,
        prompt=input_text,
        n=1
    )

    json_response = json.loads(result.model_dump_json())
    image_url = json_response["data"][0]["url"] 
    ```

    **C#**

    ```
   var imageGeneration = await openAIimageClient.GenerateImageAsync(
            input_text,
            new ImageGenerationOptions()
            {
                Size = GeneratedImageSize.W1024xH1024
            }
   );
   imageUrl= imageGeneration.Value.ImageUri;
    ```

1. Ten en cuenta que el código del resto de la función **principal** pasa la dirección URL de la imagen y un nombre de archivo a una función proporcionada, que descarga la imagen generada y la guarda como un archivo .png.

1. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código y, a continuación, usa el comando **CTRL+Q** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Ejecución de la aplicación cliente

1. En el panel de línea de comandos de Cloud Shell, escribe el siguiente comando para ejecutar la aplicación:

    **Python**

    ```
   python dalle-client.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Cuando se te solicite, escribe una solicitud para una imagen, como `Create an image of a robot eating pizza`. Tras unos instantes, la aplicación debe confirmar que la imagen se ha guardado.
1. Prueba algunas indicaciones más. Cuando hayas terminado, presiona `quit` para salir del programa.

    > **Nota**: en esta aplicación sencilla, no hemos implementado una lógica para conservar el historial de conversaciones, por lo que el modelo tratará cada indicación como una nueva solicitud sin contexto de la indicación anterior.

1. Para descargar y ver las imágenes generadas por la aplicación, en la barra de herramientas del panel de Cloud Shell, usa el botón **Cargar o descargar archivos** para descargar un archivo y, a continuación, ábrelo. Para descargar un archivo, completa su ruta de acceso de archivo en la interfaz de descarga; por ejemplo:

    **Python**

    /inicio/*usuario*`/mslearn-openai/Labfiles/03-image-generation/Python/images/image_1.png`

    **C#**

    /home/*user*`/mslearn-openai/Labfiles/03-image-generation/CSharp/images/image_1.png`

## Resumen

En este ejercicio, has usado Fundición de IA de Azure y el SDK de Azure OpenAI para crear una aplicación cliente que usa un modelo DALL-E para generar imágenes.

## Limpieza

Si has terminado de explorar DALL-E, debes eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Vuelve a la pestaña del explorador que contiene Azure Portal (o vuelve a abrir [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` en una nueva pestaña del explorador) y mira el contenido del grupo de recursos donde implementó los recursos usados en este ejercicio.
1. Selecciona **Eliminar grupo de recursos** en la barra de herramientas.
1. Escribe el nombre del grupo de recursos y confirma que deseas eliminarlo.
