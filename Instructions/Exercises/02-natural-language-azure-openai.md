---
lab:
  title: Uso de SDK de Azure OpenAI en la aplicación
---

# Uso de las API de Azure OpenAI en la aplicación

Con Azure OpenAI Service, los desarrolladores pueden crear bots de chat, modelos de lenguaje y otras aplicaciones que destaquen en el reconocimiento del lenguaje humano natural. Azure OpenAI proporciona acceso a modelos de inteligencia artificial previamente entrenados, así como a un conjunto de API y herramientas para personalizar y ajustar esos modelos con el fin de satisfacer los requisitos específicos de una aplicación. En este ejercicio, aprenderá a implementar un modelo en Azure OpenAI y a usarlo en su propia aplicación.

En el escenario de este ejercicio, realizará el rol de un desarrollador de software encargado de implementar una aplicación que pueda usar IA generativa que proporcione recomendaciones sobre senderismo. Las técnicas que se usan en el ejercicio se pueden aplicar a cualquier aplicación que quiera usar las API de Azure OpenAI.

Este ejercicio dura aproximadamente **30** minutos.

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

Azure proporciona un portal basado en web denominado **Inteligencia artificial de Azure Studio** que se puede usar para implementar, administrar y explorar modelos. Comenzarás la exploración de Azure OpenAI usando Inteligencia artificial de Azure Studio para implementar un modelo.

> **Nota**: A medida que usas Inteligencia artificial de Azure Studio, es posible que se muestren cuadros de mensaje que sugieren tareas que se van a realizar. Puede cerrarlos y seguir los pasos descritos en este ejercicio.

1. En Azure Portal, en la página **Información general** del recurso de Azure OpenAI, desplázate hacia abajo hasta la sección **Comenzar** y selecciona el botón para ir a **AI Studio**.
1. En Inteligencia artificial de Azure Studio, en el panel de la izquierda, selecciona la página **Implementaciones** y visualiza las implementaciones de modelos existentes. Si aún no tienes una, crea una nueva implementación del modelo **gpt-35-turbo-16k** con la siguiente configuración:
    - **Nombre de implementación**: *nombre único que prefieras*
    - **Modelo**: gpt-35-turbo-16k *(si el modelo 16k no estuviera disponible, elija gpt-35-turbo)*
    - **Versión del modelo**: *uso de la versión predeterminada*
    - **Tipo de implementación**: Estándar
    - **Límite de velocidad de tokens por minuto**: 5000\*
    - **Filtro de contenido**: valor predeterminado
    - **Habilitación de la cuota dinámica**: deshabilitada

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que deja capacidad para otras personas que usan la misma suscripción.

## Preparación para desarrollar una aplicación en Visual Studio Code

Desarrollará la aplicación de Azure OpenAI con Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio **mslearn-openai**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.

    > **Nota**: Si Visual Studio Code muestra un mensaje emergente para solicitarle que confíe en el código que está abriendo, haga clic en la opción **Sí, confío en los autores** en el elemento emergente.

4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

## Configuración de la aplicación

Se han proporcionado aplicaciones para C# y Python. Las dos aplicaciones tienen la misma funcionalidad. En este ejercicio, completará algunas partes clave de la aplicación para habilitarla con el recurso Azure OpenAI.

1. En Visual Studio Code, en el panel **Explorador**, vaya a la carpeta **Labfiles/02-azure-openai-api** y expanda las carpetas **CSharp** o **Python**, según sus preferencias de lenguaje. Cada carpeta contiene los archivos específicos del lenguaje de una aplicación en la que se integrará la funcionalidad de Azure OpenAI.
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
    - El **nombre de implementación** que especificaste para la implementación del modelo (disponible en la página **Implementaciones** de Inteligencia artificial de Azure Studio).
5. Guarde el archivo de configuración.

## Adición de código para usar el servicio Azure OpenAI

Ya está listo para usar el SDK de Azure OpenAI para consumir el modelo implementado.

1. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de código de su idioma preferido y reemplace el comentario ***Agregar paquete de Azure OpenAI*** por código para agregar la biblioteca del SDK de Azure OpenAI:

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```
    
    **Python**: test-openai-model.py
    
    ```python
    # Add Azure OpenAI package
    from openai import AzureOpenAI
    ```

1. En el código de aplicación del lenguaje, reemplace el comentario ***Inicializar el cliente de Azure OpenAI...*** por el código siguiente para inicializar el cliente y definir el mensaje del sistema.

    **C#** : Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    
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

1. Reemplace el comentario ***Agregar código para enviar la solicitud...*** por el código necesario para compilar la solicitud; especificando los distintos parámetros para el modelo, como `messages` y `temperature`.

    **C#** : Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(inputText),
        },
        MaxTokens = 400,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Print the response
    string completion = response.Choices[0].Message.Content;
    Console.WriteLine("Response: " + completion + "\n");
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

1. Guarde los cambios en el archivo de código.

## Prueba de la aplicación

Ahora que ha configurado la aplicación, ejecútela para enviarle la solicitud al modelo y ver la respuesta.

1. En el panel de terminal interactivo, asegúrese de que el contexto de la carpeta es la carpeta del lenguaje que prefiera. Después, escriba el siguiente comando para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python test-openai-model.py`

    > **Sugerencia**: Puede usar el icono **Maximizar el tamaño del panel** (**^**) en la barra de herramientas del terminal para ver más del texto de la consola.

1. Cuando se le solicite, introduzca el texto `What hike should I do near Rainier?`.
1. Observe la salida, teniendo en cuenta que la respuesta sigue las instrucciones proporcionadas en el mensaje del sistema que agregó a la matriz de *mensajes*.
1. Proporcione el `Where should I hike near Boise? I'm looking for something of easy difficulty, between 2 to 3 miles, with moderate elevation gain.` de la solicitud y observe la salida.
1. En el archivo de código del idioma que prefiera, cambie el valor del parámetro *temperatura* en la solicitud para **1.0** y guarde el archivo.
1. Vuelva a ejecutar la aplicación con las indicaciones anteriores y observe la salida.

Al aumentar la temperatura, la respuesta suele variar, incluso si se proporciona el mismo texto, debido al aumento de la aleatoriedad. Puede ejecutarlo varias veces para ver cómo cambia la salida. Pruebe usando valores de temperatura diferentes con la misma entrada.

## Mantener el historial de conversaciones

En la mayoría de las aplicaciones del mundo real, la capacidad de hacer referencia a las partes anteriores de la conversación permite una interacción más realista con un agente de IA. La API de Azure OpenAI es sin estado, pero al proporcionar un historial de la conversación en las indicaciones, habilite el modelo de IA para hacer referencia a mensajes anteriores.

1. Vuelva a ejecutar la aplicación y proporcione las indicaciones `Where is a good hike near Boise?`.
1. Observe la salida y, a continuación, indique `How difficult is the second hike you suggested?`.
1. Es probable que la respuesta del modelo indique que no entiende la caminata a la que hace referencia. Para corregirlo, permitiremos que el modelo tenga los mensajes de conversación anteriores como referencia.
1. En la aplicación, es necesario agregar el mensaje anterior y la respuesta al mensaje futuro que estamos enviando. Debajo de la definición del **mensaje del sistema**, agregue el código siguiente.

    **C#** : Program.cs

    ```csharp
    // Initialize messages list
    var messagesList = new List<ChatRequestMessage>()
    {
        new ChatRequestSystemMessage(systemMessage),
    };
    ```

    **Python**: test-openai-model.py

    ```python
    # Initialize messages array
    messages_array = [{"role": "system", "content": system_message}]
    ```

1. En el comentario ***Agregar código para enviar la solicitud...***, reemplace todo el código del comentario al final del bucle **while** por el código siguiente y, a continuación, guarde el archivo. El código es principalmente el mismo, pero ahora usa la matriz de mensajes para almacenar el historial de conversaciones.

    **C#** : Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    messagesList.Add(new ChatRequestUserMessage(inputText));

    ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
    {
        MaxTokens = 1200,
        Temperature = 0.7f,
        DeploymentName = oaiDeploymentName
    };

    // Add messages to the completion options
    foreach (ChatRequestMessage chatMessage in messagesList)
    {
        chatCompletionsOptions.Messages.Add(chatMessage);
    }

    // Send request to Azure OpenAI model
    ChatCompletions response = client.GetChatCompletions(chatCompletionsOptions);

    // Return the response
    string completion = response.Choices[0].Message.Content;

    // Add generated text to messages list
    messagesList.Add(new ChatRequestAssistantMessage(completion));

    Console.WriteLine("Response: " + completion + "\n");
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

1. Guarde el archivo. En el código que agregó, observe que ahora anexamos la entrada y respuesta anteriores a la matriz de mensajes, permitiendo al modelo comprender el historial de la conversación.
1. En el panel del terminal, escriba el comando siguiente para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python test-openai-model.py`

1. Vuelva a ejecutar la aplicación y proporcione las indicaciones `Where is a good hike near Boise?`.
1. Observe la salida y, a continuación, indique `How difficult is the second hike you suggested?`.
1. Es probable que obtenga una respuesta sobre la segunda caminata sugerida por el modelo, que proporciona una conversación mucho más realista. Formule preguntas de seguimiento adicionales que hagan referencia a respuestas anteriores y cada vez que el historial proporcione contexto para que el modelo responda.

    > **Sugerencia**: El recuento de tokens solo se establece en 1200, por lo que si la conversación continuase demasiado tiempo, la aplicación se quedará sin tokens disponibles, dando lugar a solicitudes incompletas. En usos de producción, limitar la longitud del historial a las entradas y respuestas más recientes ayudará a controlar el número de tokens necesarios.

## Limpiar

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o todo el recurso en **Azure Portal**, en `https://portal.azure.com`.
