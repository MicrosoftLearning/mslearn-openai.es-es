---
lab:
  title: Implementación de la generación aumentada de recuperación (RAG) con Azure OpenAI Service
---

# Implementación de la generación aumentada de recuperación (RAG) con Azure OpenAI Service

Azure OpenAI Service le permite usar sus propios datos con la inteligencia de LLM subyacentes. Puede limitar el modelo para que use solamente los datos para temas pertinentes o combinarlos con los resultados del modelo entrenado previamente.

En el escenario de este ejercicio, desempeñará el rol de un desarrollador de software que trabaja para la agencia de viajes de Margie. Explorará cómo usar la inteligencia artificial generativa para facilitar y mejorar la eficacia de las tareas de codificación. Las técnicas que se usan en el ejercicio se pueden aplicar a otros archivos de código, lenguajes de programación y casos de uso.

Este ejercicio dura aproximadamente **20** minutos.

## Aprovisionamiento de un recurso de Azure OpenAI

Si aún no tiene uno, aprovisione un recurso de Azure OpenAI en la suscripción de Azure.

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

3. Espere a que la implementación finalice. Luego, vaya al recurso de Azure OpenAI implementado en Azure Portal.

## Implementar un modelo

Azure OpenAI proporciona un portal basado en web denominado **Azure OpenAI Studio** que se puede usar para implementar, administrar y explorar modelos. Para iniciar la exploración de Azure OpenAI, use Azure OpenAI Studio para implementar un modelo.

1. En la página **Información general** del recurso Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador.
2. En Azure OpenAI Studio, en la página **Implementaciones**, visualiza las implementaciones de modelos existentes. Si aún no tienes una, crea una nueva implementación del modelo **gpt-35-turbo-16k** con la siguiente configuración:
    - **Modelo**: gpt-35-turbo-16k *(debe ser este modelo para usar las características de este ejercicio)*
    - **Versión de Modev**: actualización automática al valor predeterminado.
    - **Nombre de implementación**: *Un nombre único que prefiera. Usará este valor más adelante en este laboratorio.*
    - **Opciones avanzadas**
        - **Filtro de contenido**: valor predeterminado
        - **Tipo de implementación**: Estándar
        - **Límite de velocidad de tokens por minuto**: 5000\*
        - **Habilitación de la cuota dinámica**: habilitado

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que deja capacidad para otras personas que usan la misma suscripción.

## Observación del comportamiento normal del chat sin agregar sus propios datos

Antes de conectar Azure OpenAI a los datos, observe primero cómo responde el modelo base a las consultas sin datos de base.

1. En **Azure OpenAI Studio** en `https://oai.azure.com`, en la sección **Área de juegos**, seleccione la página **Chat**. La página del área de juegos de **Chat** consta de tres secciones principales:
    - **Instalación**: se usa para establecer el contexto de las respuestas del modelo.
    - **Sesión de chat**: se usa para enviar mensajes de chat y ver respuestas.
    - **Configuración**: se usa para configurar los valores de la implementación de modelo.
2. En el panel **Configuración**, asegúrese de que la implementación de modelo esté seleccionada.
3. En el área **Configuración**, seleccione la plantilla de mensaje del sistema predeterminada para establecer el contexto de la sesión de chat. El mensaje predeterminado del sistema es *Es un asistente de IA que ayuda a las personas a encontrar información*.
4. En la **sesión de chat**, envíe las siguientes consultas y revise las respuestas:

    ```prompt
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```prompt
    What are some facts about New York?
    ```

    Pruebe preguntas similares sobre el turismo y los lugares para alojarse en otras ubicaciones que se incluirán en nuestros datos de base, como Londres o San Francisco. Es probable que obtenga respuestas completas sobre áreas o vecindarios y algunos hechos generales sobre la ciudad.

## Conexión de los datos en el área de juegos de chat

Ahora agregará algunos datos para una compañía ficticia de agencia de viajes denominada *Margie's Travel*. A continuación, verá cómo responde el modelo de Azure OpenAI al usar los folletos de Margie's Travel como datos de base.

1. En una nueva pestaña del explorador, descargue un archivo de datos de folletos de `https://aka.ms/own-data-brochures`. Extraiga los folletos en una carpeta del equipo.
1. En Azure OpenAI Studio, en el área de juegos **Chat**, en la sección **Instalación**, seleccione **Agregar los datos**.
1. Seleccione **Agregar un origen de datos** y elija **Cargar archivos**.
1. Deberás crear una cuenta de almacenamiento y un recurso de Azure AI Search. En la lista desplegable del recurso de almacenamiento, seleccione **Crear un nuevo recurso de Azure Blob Storage** y cree una cuenta de almacenamiento con la siguiente configuración. Todo lo que no se especifique se deja con el valor predeterminado.

    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *seleccione el mismo grupo de recursos que el recurso de Azure OpenAI*.
    - **Nombre de la cuenta de almacenamiento**: *escriba un nombre único*
    - **Región**: *seleccione la misma región que el recurso de Azure OpenAI*.
    - **Redundancia**: almacenamiento con redundancia local (LRS)

1. Mientras se crea el recurso de la cuenta de almacenamiento, vuelva a Azure OpenAI Studio y seleccione **Crear un nuevo recurso de Azure AI Search** con la siguiente configuración. Todo lo que no se especifique se deja con el valor predeterminado.

    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *seleccione el mismo grupo de recursos que el recurso de Azure OpenAI*.
    - **Nombre del servicio**: *escriba un nombre único*
    - **Ubicación**: *seleccione la misma ubicación que el recurso de Azure OpenAI*.
    - **Plan de tarifa**: básico

1. Espere hasta que se haya implementado el recurso de búsqueda y, a continuación, vuelva a Azure AI Studio.
1. En **Agregar datos**, escriba los valores siguientes para el origen de datos y seleccione **Siguiente**.

    - **Seleccionar origen de datos**: cargar archivos
    - **Suscripción** : su suscripción a Azure.
    - **Seleccionar recurso de Azure Blob Storage**: *Use el botón **Actualizar** para volver a rellenar la lista y, a continuación, elija el recurso de almacenamiento que creó*.
        - Activación de CORS cuando se le solicite
    - **Seleccionar recurso de Azure AI Search**: *Use el botón **Actualizar** para volver a rellenar la lista y, a continuación, elija el recurso de búsqueda que ha creado*.
    - **Escribir el nombre del índice**: `margiestravel`
    - **Agregar búsqueda vectorial a este recurso de búsqueda**: no comprobado
    - **Reconozco que la conexión a una cuenta de Azure AI Search conllevará el uso de mi cuenta**: comprobado

1. En la página **Cargar archivos**, cargue los archivos PDF que descargó y, a continuación, seleccione **Siguiente**.
1. En la página **Administración de datos**, seleccione el tipo de búsqueda **Palabra clave** en la lista desplegable y, a continuación, seleccione **Siguiente**.
1. En la página **Revisar y finalizar**, seleccione **Guardar y cerrar**, lo que agregará los datos. Esta operación puede tardar unos minutos, durante los cuales debe dejar abierta la ventana. Una vez completado, verá el origen de datos, el recurso de búsqueda y el índice especificados en la sección **Instalación**.

    > **Sugerencia**: En ocasiones, la conexión entre el nuevo índice de búsqueda y Azure OpenAI Studio tarda demasiado tiempo. Si ha esperado unos minutos y aún no está conectado, compruebe los recursos de AI Search en Azure Portal. Si ve el índice completado, puede desconectar la conexión de datos en Azure OpenAI Studio y volver a agregarla especificando un origen de datos de Azure AI Search y seleccionando el nuevo índice.

## Chat con un modelo basado en los datos

Ahora que ha agregado los datos, realice las mismas preguntas que antes y vea cómo difiere la respuesta.

```prompt
I'd like to take a trip to New York. Where should I stay?
```

```prompt
What are some facts about New York?
```

Esta vez, observará una respuesta muy diferente, con detalles sobre determinados hoteles y una mención de Margie's Travel, así como referencias a donde procede la información proporcionada. Si abre la referencia en PDF que aparece en la respuesta, verá los mismos hoteles que el modelo proporcionó.

Pruebe a preguntar sobre otras ciudades incluidas en los datos de base, que son Dubái, Las Vegas, Londres y San Francisco.

> **Nota**: **Agregar los datos** todavía está en versión preliminar y es posible que no siempre se comporte según lo previsto para esta característica, como proporcionar la referencia incorrecta para una ciudad no incluida en los datos de base.

## Vamos a conectar la aplicación a tus propios datos

A continuación, vamos a explorar cómo conectar la aplicación para usar sus propios datos.

### Preparación para desarrollar una aplicación en Visual Studio Code

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
    pip install openai==1.13.3
    ```

3. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de configuración para su lenguaje preferido.

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Actualiza los valores de configuración para incluir:
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - El **nombre de implementación** que especificó para la implementación de modelo (disponible en la página **Implementaciones** de Azure OpenAI Studio).
    - Punto de conexión del servicio de búsqueda (el valor de dirección **URL** de la página de información general del recurso de búsqueda en Azure Portal).
    - Una **clave** para el recurso de búsqueda (disponible en la página **Claves** del recurso de búsqueda en Azure Portal: puedes usar cualquiera de las claves de administración).
    - Nombre del índice de búsqueda (que debe ser `margiestravel`).
1. Guarde el archivo de configuración.

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

## Limpiar

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar el recurso en **Azure Portal** en `https://portal.azure.com`. Asegúrese de incluir también la cuenta de almacenamiento y el recurso de búsqueda, ya que pueden suponer un costo relativamente grande.
