---
lab:
  title: Uso de SDK de Azure OpenAI en la aplicación
  status: stale
---

# Uso de las API de Azure OpenAI en la aplicación

Con Azure OpenAI Service, los desarrolladores pueden crear bots de chat, modelos de lenguaje y otras aplicaciones que destaquen en el reconocimiento del lenguaje humano natural. Azure OpenAI proporciona acceso a modelos de inteligencia artificial previamente entrenados, así como a un conjunto de API y herramientas para personalizar y ajustar esos modelos con el fin de satisfacer los requisitos específicos de una aplicación. En este ejercicio, aprenderás a implementar un modelo en Azure OpenAI y a usarlo en tu propia aplicación.

En el escenario de este ejercicio, realizarás el rol de un desarrollador de software encargado de implementar una aplicación que pueda usar IA generativa que proporcione recomendaciones sobre senderismo. Las técnicas que se usan en el ejercicio se pueden aplicar a cualquier aplicación que quiera usar las API de Azure OpenAI.

Este ejercicio dura aproximadamente **30** minutos.

## Aprovisionamiento de un recurso de Azure OpenAI

Si aún no tienes uno, aprovisiona un recurso de Azure OpenAI en la suscripción a Azure.

1. Inicia sesión en **Azure Portal** en `https://portal.azure.com`.
2. Crea un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: *selecciona una suscripción a Azure aprobada para acceder al servicio Azure OpenAI*
    - **Grupo de recursos**: *elige o crea un grupo de recursos*
    - **Región**: *elige de forma **aleatoria** cualquiera de las siguientes regiones*\*
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
    - **Nombre**: *nombre único que prefieras*
    - **Plan de tarifa**: estándar S0

    > \* Los recursos de Azure OpenAI están restringidos por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

3. Espera a que la implementación finalice. Luego, ve al recurso de Azure OpenAI implementado en Azure Portal.

## Implementar un modelo

Azure proporciona un portal basado en web denominado **Portal de Azure AI Foundry** que puedes usar para implementar, administrar y explorar modelos. Para iniciar la exploración de Azure OpenAI, usa Portal de Azure AI Foundry para implementar un modelo.

> **Nota**: a medida que usas Portal de Azure AI Foundry, es posible que se muestren cuadros de mensaje que sugieren tareas que se van a realizar. Puedes cerrarlos y seguir los pasos descritos en este ejercicio.

1. En Azure Portal, en la página **Información general** del recurso de Azure OpenAI, desplázate hacia abajo hasta la sección **Comenzar** y selecciona el botón para ir a **Portal de Azure AI Foundry** (anteriormente AI Studio).
1. En Portal de Azure AI Foundry, en el panel de la izquierda, selecciona la página **Implementaciones** y consulta las implementaciones de modelos existentes. Si aún no tienes una, crea una nueva implementación del modelo **gpt-4o** con la siguiente configuración:
    - **Nombre de implementación**: *nombre único que prefieras*
    - **Modelo**: gpt-4o
    - **Versión del modelo**: *usa la versión predeterminada*
    - **Tipo de implementación**: Estándar
    - **Límite de velocidad de tokens por minuto**: 5000\*
    - **Filtro de contenido**: valor predeterminado
    - **Habilitación de la cuota dinámica**: deshabilitada

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que dejas capacidad para otras personas que usan la misma suscripción.

## Preparación para desarrollar una aplicación en Visual Studio Code

Desarrollarás la aplicación de Azure OpenAI con Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: si ya has clonado el repositorio **mslearn-openai**, ábrelo en Visual Studio Code. De lo contrario, sigue estos pasos para clonarlo en el entorno de desarrollo.

1. Inicia Visual Studio Code.
2. Abre la paleta de comandos (Mayús + Ctrl + P) o **View** > **Command Palette...**) y ejecuta un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abre la carpeta en Visual Studio Code.

    > **Nota**: si Visual Studio Code muestra un mensaje emergente para solicitarte que confíes en el código que estás abriendo, haz clic en la opción **Yes, I trust the authors** en el elemento emergente.

4. Espera mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: si se te pide que agregues los recursos necesarios para compilar y depurar, selecciona **Ahora no**.

## Configuración de la aplicación

Se han proporcionado aplicaciones para C# y Python. Las dos aplicaciones tienen la misma funcionalidad. En este ejercicio, completarás algunas partes clave de la aplicación para habilitarla con el recurso Azure OpenAI.

1. En Visual Studio Code, en el panel **Explorador**, ve a la carpeta **Labfiles/02-azure-openai-api** y expande las carpetas **CSharp** o **Python**, según tu preferencia de idioma. Cada carpeta contiene los archivos específicos del idioma de una aplicación en la que se integrará la funcionalidad de Azure OpenAI.
2. Haz clic con el botón derecho en la carpeta **CSharp** o **Python** que contenga los archivos de código y abre un terminal integrado. A continuación, instala el paquete del SDK de Azure OpenAI mediante la ejecución del comando adecuado para tu preferencia de idioma:

    **C#:**

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
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
    - El **nombre de implementación** que especificaste para la implementación del modelo (disponible en la página **Implementaciones** de Inteligencia artificial de Portal de Azure AI Foundry).
5. Guarda el archivo de configuración.

## Adición de código para usar el servicio Azure OpenAI

Ya estás listo para usar el SDK de Azure OpenAI para consumir el modelo implementado.

1. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abre el archivo de código de tu idioma preferido y reemplaza el comentario ***Agregar paquete de Azure OpenAI*** por código para agregar la biblioteca del SDK de Azure OpenAI:

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python**: test-openai-model.py

    ```python
    # Add Azure OpenAI package
    from openai import AzureOpenAI
    ```

1. En el código de aplicación del idioma, reemplaza el comentario ***Inicializar el cliente de Azure OpenAI...*** por el código siguiente para inicializar el cliente y definir el mensaje del sistema.

    **C#** : Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
    ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
    
    // System message to provide context to the model
    string systemMessage = "I am a hiking enthusiast named Forest who helps people discover hikes in their area. If no area is specified, I will default to near Rainier National Park. I will then provide three suggestions for nearby hikes that vary in length. I will also share an interesting fact about the local nature on the hikes when making a recommendation.";
    ```

    **Python**: test-openai-model.py

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2024-02-15-preview"
            )
    
    # Create a system message
    system_message = """I am a hiking enthusiast named Forest who helps people discover hikes in their area. 
        If no area is specified, I will default to near Rainier National Park. 
        I will then provide three suggestions for nearby hikes that vary in length. 
        I will also share an interesting fact about the local nature on the hikes when making a recommendation.
        """
    ```

1. Reemplaza el comentario ***Agregar código para enviar la solicitud...*** por el código necesario para compilar la solicitud; especificando los distintos parámetros para el modelo, como `Temperature` y `MaxOutputTokenCount`.

    **C#** : Program.cs

    ```csharp
    // Add code to send request...
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        [
            new SystemChatMessage(systemMessage),
            new UserChatMessage(inputText)
        ],
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    ```

    **Python**: test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=400,
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": input_text}
        ]
    )
    generated_text = response.choices[0].message.content

    # Print the response
    print("Response: " + generated_text + "\n")
    ```

1. Guarda los cambios en el archivo de código.

## Prueba de la aplicación

Ahora que has configurado la aplicación, ejecútala para enviarle la solicitud al modelo y ver la respuesta.

1. En el panel de terminal interactivo, asegúrate de que el contexto de la carpeta es la carpeta del idioma que prefieras. Después, escribe el siguiente comando para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python test-openai-model.py`

    > **Sugerencia**: puedes usar el icono **Maximizar el tamaño del panel** (**^**) en la barra de herramientas del terminal para ver más del texto de la consola.

1. Cuando se te solicite, introduce el texto `What hike should I do near Rainier?`.
1. Observa la salida, teniendo en cuenta que la respuesta sigue las instrucciones proporcionadas en el mensaje del sistema que agregaste a la matriz de *mensajes*.
1. Proporciona la indicación `Where should I hike near Boise? I'm looking for something of easy difficulty, between 2 to 3 miles, with moderate elevation gain.` y observa la salida.
1. En el archivo de código del idioma que prefieras, cambia el valor del parámetro *temperatura* en la solicitud a **1.0** y guarda el archivo.
1. Vuelve a ejecutar la aplicación con las indicaciones anteriores y observa la salida.

Al aumentar la temperatura, la respuesta suele variar, incluso si se proporciona el mismo texto, debido al aumento de la aleatoriedad. Puedes ejecutarlo varias veces para ver cómo cambia la salida. Prueba usando valores de temperatura diferentes con la misma entrada.

## Mantener el historial de conversaciones

En la mayoría de las aplicaciones del mundo real, la capacidad de hacer referencia a las partes anteriores de la conversación permite una interacción más realista con un agente de IA. La API de Azure OpenAI es sin estado, pero al proporcionar un historial de la conversación en las indicaciones, habilita el modelo de IA para hacer referencia a mensajes anteriores.

1. Vuelve a ejecutar la aplicación y proporciona la indicación `Where is a good hike near Boise?`.
1. Observa la salida y, a continuación, indica `How difficult is the second hike you suggested?`.
1. Es probable que la respuesta del modelo indique que no entiende la caminata a la que hace referencia. Para corregirlo, permitiremos que el modelo tenga los mensajes de conversación anteriores como referencia.
1. En la aplicación, es necesario agregar el mensaje anterior y la respuesta al mensaje futuro que estamos enviando. Debajo de la definición del **mensaje del sistema**, agrega el código siguiente.

    **C#** : Program.cs

    ```csharp
    // Initialize messages list
    var messagesList = new List<ChatMessage>()
    {
        new SystemChatMessage(systemMessage),
    };
    ```

    **Python**: test-openai-model.py

    ```python
    # Initialize messages array
    messages_array = [{"role": "system", "content": system_message}]
    ```

1. En el comentario ***Agregar código para enviar la solicitud...***, reemplaza todo el código del comentario al final del bucle **while** por el código siguiente y, a continuación, guarda el archivo. El código es principalmente el mismo, pero ahora usa la matriz de mensajes para almacenar el historial de conversaciones.

    **C#** : Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    messagesList.Add(new UserChatMessage(inputText));

    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        messagesList,
        chatCompletionOptions
    );

    // Return the response
    string response = completion.Content[0].Text;

    // Add generated text to messages list
    messagesList.Add(new AssistantChatMessage(response));

    Console.WriteLine("Response: " + response + "\n");
    ```

    **Python**: test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    messages_array.append({"role": "user", "content": input_text})

    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=1200,
        messages=messages_array
    )
    generated_text = response.choices[0].message.content
    # Add generated text to messages array
    messages_array.append({"role": "assistant", "content": generated_text})

    # Print generated text
    print("Summary: " + generated_text + "\n")
    ```

1. Guarda el archivo. En el código que agregaste, observa que ahora anexamos la entrada y respuesta anteriores a la matriz de mensajes, permitiendo al modelo comprender el historial de la conversación.
1. En el panel del terminal, escribe el comando siguiente para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python test-openai-model.py`

1. Vuelve a ejecutar la aplicación y proporciona la indicación `Where is a good hike near Boise?`.
1. Observa la salida y, a continuación, indica `How difficult is the second hike you suggested?`.
1. Es probable que obtengas una respuesta sobre la segunda caminata sugerida por el modelo, que proporciona una conversación mucho más realista. Formula preguntas de seguimiento adicionales que hagan referencia a respuestas anteriores y cada vez que el historial proporcione contexto para que el modelo responda.

    > **Sugerencia**: el recuento de tokens solo se establece en 800, por lo que si la conversación continuase demasiado tiempo, la aplicación se quedará sin tokens disponibles, dando lugar a solicitudes incompletas. En usos de producción, limitar la longitud del historial a las entradas y respuestas más recientes ayudará a controlar el número de tokens necesarios.

## Limpiar

Cuando hayas terminado de usar el recurso de Azure OpenAI, recuerda eliminar la implementación o todo el recurso en **Azure Portal**, en `https://portal.azure.com`.
