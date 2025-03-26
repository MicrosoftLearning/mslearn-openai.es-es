---
lab:
  title: Implementación de la generación aumentada de recuperación (RAG) con Azure OpenAI Service
  status: stale
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

1. Inicie sesión en **Azure Portal** en `https://portal.azure.com`.
2. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: *Selección de una suscripción de Azure aprobada para acceder al servicio Azure OpenAI*
    - **Grupo de recursos**: *elija o cree un grupo de recursos*
    - **Región**: *Elija de forma **aleatoria** cualquiera de las siguientes regiones*\*
        - Este de Australia
        - Este de Canadá
        - Este de EE. UU.
        - Este de EE. UU. 2
        - Centro de Francia
        - Japón Oriental
        - Centro-Norte de EE. UU
        - Centro de Suecia
        - Norte de Suiza
        - Sur de Reino Unido 2
    - **Nombre**: *nombre único que prefiera*
    - **Plan de tarifa**: estándar S0

    > \* Los recursos de Azure OpenAI están restringidos por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tenga que crear otro recurso en otra región.

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
    - **Rendimiento**: Estándar
    - **Redundancia**: Almacenamiento con redundancia local (LRS)
5. Una vez que los tres recursos se hayan implementado correctamente en la suscripción a Azure, revísalos en Azure Portal y recopila la siguiente información (que necesitarás más adelante en el ejercicio):
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - El punto de conexión del servicio de Búsqueda de Azure AI (el valor de dirección **URL** de la página de información general del recurso de Búsqueda de Azure AI en Azure Portal).
    - Una **clave de administrador principal** para el recurso de Búsqueda de Azure AI (disponible en la página **Claves** del recurso de Búsqueda de Azure AI en Azure Portal).

## Creación del código

Vas a fundamentar las indicaciones que usas con un modelo de IA generativa mediante tus propios datos. En este ejercicio, los datos constan de una colección de folletos de viaje de la empresa ficticia *Margies Travel*.

1. En una nueva pestaña del explorador, descargue un archivo de datos de folletos de `https://aka.ms/own-data-brochures`. Extraiga los folletos en una carpeta del equipo.
1. En Azure Portal, navega hasta la cuenta de almacenamiento y visualiza la página del **explorador de almacenamiento**.
1. Selecciona **Contenedores de blobs** y después agrega un nuevo contenedor con el nombre `margies-travel`.
1. Selecciona el contenedor **margies-travel** y luego carga los folletos .pdf que extrajiste anteriormente en la carpeta raíz del contenedor de blobs.

## Implementación de modelos de IA

Usarás dos modelos de IA en este ejercicio:

- Un modelo de inserción de texto para *vectorizar* el texto de los folletos de modo que se pueda indexar de forma eficaz para su uso en la fundamentación de solicitudes.
- Un modelo GPT que tu aplicación puede usar para generar respuestas a solicitudes fundamentadas en tus datos.

Para implementar estos modelos, usarás AI Foundry.

1. En Azure Portal, ve a tu recurso de Azure OpenAI. Después, usa el vínculo para abrir tu recurso en el **portal de Azure AI Foundry**.
1. En el portal de Azure AI Foundry, en la página **Implementaciones**, visualiza las implementaciones de modelos existentes. Luego crea una nueva implementación del modelo base del modelo **text-embedding-ada-002** con la siguiente configuración:
    - **Nombre de implementación**: text-embedding-ada-002
    - **Modelo**: text-embedding-ada-002
    - **Versión del modelo**: *la versión predeterminada*
    - **Tipo de implementación**: Estándar
    - **Límite de velocidad de tokens por minuto**: 5000\*
    - **Filtro de contenido**: valor predeterminado
    - **Habilitación de la cuota dinámica**: habilitado
1. Una vez implementado el modelo de inserción de texto, vuelve a la página **Implementaciones** y crea una nueva implementación del modelo **gpt-35-turbo-16k** con la siguiente configuración:
    - **Nombre de implementación**: gpt-35-turbo-16k
    - **Modelo**: gpt-35-turbo-16k *(si el modelo 16k no estuviera disponible, elija gpt-35-turbo)*
    - **Versión del modelo**: *la versión predeterminada*
    - **Tipo de implementación**: Estándar
    - **Límite de velocidad de tokens por minuto**: 5000\*
    - **Filtro de contenido**: valor predeterminado
    - **Habilitación de la cuota dinámica**: habilitado

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que deja capacidad para otras personas que usan la misma suscripción.

## Creación de un índice

Para facilitar el uso de tus propios datos en una solicitud, los indexarás mediante Búsqueda de Azure AI. Usarás el modelo de inserción de texto que implementaste anteriormente durante el proceso de indexación para *vectorizar* los datos de texto (lo que da como resultado que cada token de texto del índice se represente mediante vectores numéricos, por lo que es compatible con la forma en que un modelo de IA generativa representa texto)

1. En Azure Portal, ve al recurso de Búsqueda de Azure AI.
1. En la página **Información general**, seleccione **Importar y vectorizar datos**.
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
1. En la página siguiente, <u>no</u> selecciones las opciones para vectorizar imágenes ni extraer datos con aptitudes de IA.
1. En la página siguiente, habilita la clasificación semántica y programa el indizador para que se ejecute una vez.
1. En la página final, establece el **prefijo de nombre de objetos** en `margies-index` y luego crea el índice.

## Preparación para desarrollar una aplicación en Visual Studio Code

Ahora vamos a explorar el uso de sus propios datos en una aplicación que usa el SDK de Azure OpenAI Service. Desarrollará la aplicación con Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio **mslearn-openai**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.

    > **Nota**: Si Visual Studio Code muestra un mensaje emergente para solicitarle que confíe en el código que está abriendo, haga clic en la opción **Sí, confío en los autores** en el elemento emergente.

4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

## Configuración de la aplicación

Se han proporcionado aplicaciones para C# y Python, y ambas aplicaciones presentan la misma funcionalidad. En este ejercicio, completará algunas partes clave de la aplicación para habilitarla con el recurso de Azure OpenAI.

1. En Visual Studio Code, en el panel **Explorador**, vaya a la carpeta **Labfiles/06-use-own-data** y expanda la carpeta **CSharp** o **Python** según sus preferencias de lenguaje. Cada carpeta contiene los archivos específicos del lenguaje de una aplicación en la que va a integrar la funcionalidad de Azure OpenAI.
2. Haga clic con el botón derecho en la carpeta **CSharp** o **Python** que contenga los archivos de código y abra un terminal integrado. A continuación, instale el paquete del SDK de Azure OpenAI mediante la ejecución del comando adecuado para sus preferencias de lenguaje:

    **C#:**

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**:

    ```
    pip install openai==1.55.3
    ```

3. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de configuración para su lenguaje preferido.

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Actualiza los valores de configuración para incluir:
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - El **nombre de implementación** que has especificado para la implementación de modelo gpt-35-turbo (disponible en la página **Implementaciones** del portal de Azure AI Foundry).
    - Punto de conexión del servicio de búsqueda (el valor de dirección **URL** de la página de información general del recurso de búsqueda en Azure Portal).
    - Una **clave** para el recurso de búsqueda (disponible en la página **Claves** del recurso de búsqueda en Azure Portal: puedes usar cualquiera de las claves de administración).
    - Nombre del índice de búsqueda (que debe ser `margies-index`).
5. Guarde el archivo de configuración.

### Adición de código para usar el servicio Azure OpenAI

Ahora está listo para usar el SDK de Azure OpenAI para consumir el modelo implementado.

1. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de código de su idioma preferido y reemplace el comentario ***Configuración del origen de datos*** por código para agregar la biblioteca del SDK de Azure OpenAI:

    **C#**: ownData.cs

    ```csharp
    // Configure your data source
    AzureSearchChatExtensionConfiguration ownDataConfig = new()
    {
            SearchEndpoint = new Uri(azureSearchEndpoint),
            Authentication = new OnYourDataApiKeyAuthenticationOptions(azureSearchKey),
            IndexName = azureSearchIndex
    };
    ```

    **Python**: ownData.py

    ```python
    # Configure your data source
    extension_config = dict(dataSources = [  
            { 
                "type": "AzureCognitiveSearch", 
                "parameters": { 
                    "endpoint":azure_search_endpoint, 
                    "key": azure_search_key, 
                    "indexName": azure_search_index,
                }
            }]
        )
    ```

2. Revise el resto del código, teniendo en cuenta el uso de las *extensiones* en el cuerpo de la solicitud que se usa para proporcionar información sobre la configuración del origen de datos.

3. Guarde los cambios en el archivo del código.

## Ejecución de la aplicación

Ahora que ha configurado la aplicación, ejecútela para enviarle la solicitud al modelo y ver la respuesta. Observe que la única diferencia entre las distintas opciones es el contenido del mensaje. Todos los demás parámetros, como el recuento de tokens y la temperatura, son los mismos para todas las solicitudes.

1. En el panel de terminal interactivo, asegúrese de que el contexto de la carpeta es la carpeta del lenguaje que prefiera. Después, escriba el siguiente comando para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python ownData.py`

    > **Sugerencia**: Puede usar el icono **Maximizar el tamaño del panel** (**^**) en la barra de herramientas del terminal para ver más del texto de la consola.

2. Revise la respuesta al mensaje `Tell me about London`, que debe incluir una respuesta, así como algunos detalles de los datos usados para fundamentar el mensaje, que se obtuvieron del servicio de búsqueda.

    > **Sugerencia**: Si desea ver las citas del índice de búsqueda, establezca la variable ***mostrar citas*** cerca de la parte superior del archivo de código en **true**.

## Limpieza

Cuando hayas terminado de usar el recurso de Azure OpenAI, recuerda eliminar los recursos en **Azure Portal** en `https://portal.azure.com`. Asegúrese de incluir también la cuenta de almacenamiento y el recurso de búsqueda, ya que pueden suponer un costo relativamente grande.
