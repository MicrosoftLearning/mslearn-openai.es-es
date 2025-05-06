---
lab:
  title: Implementación de la generación aumentada de recuperación (RAG) con Azure OpenAI Service
  status: new
---

# Implementación de la generación aumentada de recuperación (RAG) con Azure OpenAI Service

Azure OpenAI Service le permite usar sus propios datos con la inteligencia de LLM subyacentes. Puede limitar el modelo para que use solamente los datos para temas pertinentes o combinarlos con los resultados del modelo entrenado previamente.

En el escenario de este ejercicio, desempeñará el rol de un desarrollador de software que trabaja para la agencia de viajes de Margie. Explorarás cómo usar Búsqueda de Azure AI para indexar tus propios datos y usarlos con Azure OpenAI para aumentar las solicitudes.

Este ejercicio dura aproximadamente **30** minutos.

## Aprovisionamiento de los recursos de Azure

Para completar este ejercicio, necesitarás lo siguiente:

- Un recurso de Azure OpenAI.
- Un recurso de Azure AI Search.
- Un recurso de cuenta de Azure Storage.

1. Inicia sesión en **Azure Portal** en `https://portal.azure.com`.
2. Crea un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: *selecciona una suscripción a Azure aprobada para acceder al servicio Azure OpenAI*
    - **Grupo de recursos**: *elige o crea un grupo de recursos*
    - **Región**: *elige de forma **aleatoria** cualquiera de las siguientes regiones*\*
        - Este de EE. UU.
        - Este de EE. UU. 2
        - Centro-Norte de EE. UU
        - Centro-sur de EE. UU.
        - Centro de Suecia
        - Oeste de EE. UU.
        - Oeste de EE. UU. 3
    - **Nombre**: *nombre único que prefieras*
    - **Plan de tarifa**: estándar S0

    > \* Los recursos de Azure OpenAI están restringidos por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

3. Mientras se aprovisiona el recurso de Azure OpenAI, crea un recurso de **Búsqueda de Azure AI** con la siguiente configuración:
    - **Suscripción**: *la suscripción en la que aprovisionaste el recurso de Azure OpenAI*
    - **Grupo de recursos**: *El grupo de recursos en el que aprovisionó el recurso de Azure OpenAI*
    - **Nombre**: *nombre único que prefieras*
    - **Ubicación**: *la región en la que aprovisionaste el recurso de Azure OpenAI*
    - **Plan de tarifa**: básico
4. Mientras se aprovisiona el recurso de Búsqueda de Azure AI, crea un recurso de **cuenta de almacenamiento** con la siguiente configuración:
    - **Suscripción**: *la suscripción en la que aprovisionaste el recurso de Azure OpenAI*
    - **Grupo de recursos**: *El grupo de recursos en el que aprovisionó el recurso de Azure OpenAI*
    - **Nombre de la cuenta de almacenamiento**: *nombre único que prefieras*
    - **Región**: *la región en la que aprovisionaste el recurso de Azure OpenAI*
    - **Servicio principal**: Azure Blob Storage o Azure Data Lake Storage Gen 2
    - **Rendimiento**: Estándar
    - **Redundancia**: Almacenamiento con redundancia local (LRS)
5. Una vez que los tres recursos se hayan implementado correctamente en la suscripción a Azure, revísalos en Azure Portal y recopila la siguiente información (que necesitarás más adelante en el ejercicio):
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - El punto de conexión del servicio de Búsqueda de Azure AI (el valor de dirección **URL** de la página de información general del recurso de Búsqueda de Azure AI en Azure Portal).
    - Una **clave de administrador principal** para el recurso de Búsqueda de Azure AI (disponible en la página **Claves** del recurso de Búsqueda de Azure AI en Azure Portal).

## Creación del código

Vas a fundamentar las indicaciones que usas con un modelo de IA generativa mediante tus propios datos. En este ejercicio, los datos constan de una colección de folletos de viaje de la empresa ficticia *Margies Travel*.

1. En una nueva pestaña del explorador, descarga un archivo de datos de folletos de `https://aka.ms/own-data-brochures`. Extrae los folletos en una carpeta del equipo.
1. En Azure Portal, navega hasta la cuenta de almacenamiento y visualiza la página del **explorador de almacenamiento**.
1. Selecciona **Contenedores de blobs** y después agrega un nuevo contenedor con el nombre `margies-travel`.
1. Selecciona el contenedor **margies-travel** y luego carga los folletos .pdf que extrajiste anteriormente en la carpeta raíz del contenedor de blobs.

## Implementación de modelos de IA

Usarás dos modelos de IA en este ejercicio:

- Un modelo de inserción de texto para *vectorizar* el texto de los folletos de modo que se pueda indexar de forma eficaz para su uso en la fundamentación de solicitudes.
- Un modelo GPT que tu aplicación puede usar para generar respuestas a solicitudes fundamentadas en tus datos.

## Implementación de un modelo

Después, implementarás los modelos de Azure OpenAI desde Cloud Shell.

1. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal. Para ello, deberás seleccionar un entorno de ***Bash***. Cloud Shell proporciona una interfaz de la línea de comandos en un panel situado en la parte inferior de Azure Portal.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *PowerShell*, cámbiala a ***Bash***.

```dotnetcli
az cognitiveservices account deployment create \
   -g <your_resource_group> \
   -n <your_OpenAI_resource> \
   --deployment-name text-embedding-ada-002 \
   --model-name text-embedding-ada-002 \
   --model-version "2"  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

> **Nota**: la capacidad de SKU se mide en miles de tokens por minuto. Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que deja capacidad para otras personas que usan la misma suscripción.

Una vez implementado el modelo de incrustación de texto, crea una nueva implementación del modelo **gpt-4o** con la siguiente configuración:

```dotnetcli
az cognitiveservices account deployment create \
   -g <your_resource_group> \
   -n <your_OpenAI_resource> \
   --deployment-name gpt-4o \
   --model-name gpt-4o \
   --model-version "2024-05-13" \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

## Creación de un índice

Para facilitar el uso de tus propios datos en una solicitud, los indexarás mediante Búsqueda de Azure AI. Usarás el modelo de inserción de texto para *vectorizar* los datos de texto (lo que da como resultado que cada token de texto del índice se represente mediante vectores numéricos, por lo que es compatible con la forma en que un modelo de IA generativa representa texto).

1. En Azure Portal, ve al recurso de Búsqueda de Azure AI.
1. En la página **Información general**, selecciona **Importar y vectorizar datos**.
1. En la página **Configuración de la conexión de datos**, selecciona **Azure Blob Storage** y configura el origen de datos así:
    - **Suscripción**: la suscripción a Azure en la que aprovisionaste la cuenta de almacenamiento.
    - **Cuenta de Blob Storage**: la cuenta de almacenamiento que creaste antes.
    - **Contenedor de blobs**: margies-travel
    - **Carpeta de blobs**: *déjala en blanco*
    - **Habilitar el seguimiento de eliminación**: sin seleccionar
    - **Autenticar mediante identidad administrada**: no seleccionado
1. En la página **Vectorizar el texto**, selecciona la siguiente configuración:
    - **Variante**: Azure OpenAI
    - **Suscripción**: la suscripción a Azure en la que aprovisionaste Azure OpenAI Service.
    - **Azure OpenAI Service**: tu recurso de Azure OpenAI Service
    - **Implementación de modelo**: text-embedding-ada-002
    - **Tipo de autenticación**: clave de API
    - **Reconozco que la conexión a Azure OpenAI Service conllevará costes adicionales para mi cuenta**: seleccionado
1. En la página siguiente, **no** selecciones las opciones para vectorizar imágenes ni extraer datos con aptitudes de IA.
1. En la página siguiente, habilita la clasificación semántica y programa el indizador para que se ejecute una vez.
1. En la página final, establece el **prefijo de nombre de objetos** en `margies-index` y luego crea el índice.

## Preparación para desarrollar una aplicación en Visual Studio Code

Ahora vamos a explorar el uso de sus propios datos en una aplicación que usa el SDK de Azure OpenAI Service. Desarrollarás la aplicación con Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: si ya has clonado el repositorio **mslearn-openai**, ábrelo en Visual Studio Code. De lo contrario, sigue estos pasos para clonarlo en el entorno de desarrollo.

1. Inicia Visual Studio Code.
2. Abre la paleta (Mayús + Ctrl + P o **View** > **Command Palette...**) y ejecuta un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abre la carpeta en Visual Studio Code.

    > **Nota**: si Visual Studio Code muestra un mensaje emergente para solicitarte que confíes en el código que estás abriendo, haz clic en la opción **Yes, I trust the authors** en el elemento emergente.

4. Espera mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: si se te pide que agregues los recursos necesarios para compilar y depurar, selecciona **Ahora no**.

## Configuración de la aplicación

Se han proporcionado aplicaciones para C# y Python, y ambas aplicaciones presentan la misma funcionalidad. En este ejercicio, completarás algunas partes clave de la aplicación para habilitarla con el recurso de Azure OpenAI.

1. En Visual Studio Code, en el panel **Explorador**, ve a la carpeta **Labfiles/02-use-own-data** y expande la carpeta **CSharp** o **Python** según tus preferencias de lenguaje. Cada carpeta contiene los archivos específicos del idioma de una aplicación en la que se integrará la funcionalidad de Azure OpenAI.
2. Haz clic con el botón derecho en la carpeta **CSharp** o **Python** que contenga los archivos de código y abre un terminal integrado. A continuación, instala el paquete del SDK de Azure OpenAI mediante la ejecución del comando adecuado para tu preferencia de idioma:

    **C#:**

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
    dotnet add package Azure.Search.Documents --version 11.6.0
    ```

    **Python**:

    ```powershell
    pip install openai==1.65.2
    ```

3. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abre el archivo de configuración para tu idioma preferido.

    - **C#**: appsettings.json
    - **Python**: .env

4. Actualiza los valores de configuración para incluir:
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - El **nombre de implementación** que has especificado para la implementación de tu modelo gpt-4o (debe ser `gpt-4o`).
    - Punto de conexión del servicio de búsqueda (el valor de dirección **URL** de la página de información general del recurso de búsqueda en Azure Portal).
    - Una **clave** para el recurso de búsqueda (disponible en la página **Claves** del recurso de búsqueda en Azure Portal: puedes usar cualquiera de las claves de administración).
    - Nombre del índice de búsqueda (que debe ser `margies-index`).
5. Guarda el archivo de configuración.

### Adición de código para usar el servicio Azure OpenAI

Ya estás listo para usar el SDK de Azure OpenAI para consumir el modelo implementado.

1. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abre el archivo de código de tu idioma preferido y reemplaza el comentario ***Configure your data source*** por código para tu índice como origen de datos para la finalización del chat:

    **C#**: ownData.cs

    ```csharp
    // Configure your data source
    // Extension methods to use data sources with options are subject to SDK surface changes. Suppress the warning to acknowledge this and use the subject-to-change AddDataSource method.
    #pragma warning disable AOAI001
    
    ChatCompletionOptions chatCompletionsOptions = new ChatCompletionOptions()
    {
        MaxOutputTokenCount = 600,
        Temperature = 0.9f,
    };
    
    chatCompletionsOptions.AddDataSource(new AzureSearchChatDataSource()
    {
        Endpoint = new Uri(azureSearchEndpoint),
        IndexName = azureSearchIndex,
        Authentication = DataSourceAuthentication.FromApiKey(azureSearchKey),
    });
    ```

    **Python**: ownData.py

    ```python
    # Configure your data source
    text = input('\nEnter a question:\n')
    
    completion = client.chat.completions.create(
        model=deployment,
        messages=[
            {
                "role": "user",
                "content": text,
            },
        ],
        extra_body={
            "data_sources":[
                {
                    "type": "azure_search",
                    "parameters": {
                        "endpoint": os.environ["AZURE_SEARCH_ENDPOINT"],
                        "index_name": os.environ["AZURE_SEARCH_INDEX"],
                        "authentication": {
                            "type": "api_key",
                            "key": os.environ["AZURE_SEARCH_KEY"],
                        }
                    }
                }
            ],
        }
    )
    ```

1. Guarda los cambios en el archivo del código.

## Ejecución de la aplicación

Ahora que has configurado la aplicación, ejecútala para enviarle la solicitud al modelo y ver la respuesta. Observa que la única diferencia entre las distintas opciones es el contenido del mensaje. Todos los demás parámetros, como el recuento de tokens y la temperatura, son los mismos para todas las solicitudes.

1. En el panel de terminal interactivo, asegúrate de que el contexto de la carpeta es la carpeta del idioma que prefieras. Después, escribe el siguiente comando para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python ownData.py`

    > **Sugerencia**: puedes usar el icono **Maximizar el tamaño del panel** (**^**) en la barra de herramientas del terminal para ver más del texto de la consola.

2. Revisa la respuesta a la indicación `Tell me about London`, que debe incluir una respuesta, así como algunos detalles de los datos usados para fundamentar la indicación, que se obtuvieron del servicio de búsqueda.

    > **Sugerencia**: si deseas ver las citas del índice de búsqueda, establece la variable ***mostrar citas*** cerca de la parte superior del archivo de código en **true**.

## Limpieza

Cuando hayas terminado de usar el recurso de Azure OpenAI, recuerda eliminar los recursos en **Azure Portal** en `https://portal.azure.com`. Asegúrate de incluir también la cuenta de almacenamiento y el recurso de búsqueda, ya que pueden suponer un coste relativamente grande.
