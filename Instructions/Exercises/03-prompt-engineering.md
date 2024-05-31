---
lab:
  title: Uso de la ingeniería de mensajes en una aplicación
---

# Uso de la ingeniería de mensajes en una aplicación

Cuando se trabaja con Azure OpenAI Service, el modo en el que los desarrolladores dan forma a los mensajes afecta considerablemente a la forma en la responderá el modelo de IA generativa. Los modelos de Azure OpenAI pueden adaptar y dar formato al contenido si se les solicita de forma clara y concisa. En este ejercicio verá cómo diferentes solicitudes de contenido similar ayudan a dar forma a la respuesta del modelo de IA para satisfacer mejor sus requisitos.

En el escenario de este ejercicio, realizará el papel de un desarrollador de software que trabaja en una campaña de marketing sobre vida silvestre. Está explorando cómo usar IA generativa para mejorar los correos electrónicos publicitarios y clasificar artículos que se pudieran aplicar al equipo. Las técnicas de ingeniería de solicitud usadas en el ejercicio se aplican de forma similar para una variedad de casos de uso.

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

Azure OpenAI proporciona un portal basado en web denominado **Azure OpenAI Studio** que se puede usar para implementar, administrar y explorar modelos. Para iniciar la exploración de Azure OpenAI, use Azure OpenAI Studio para implementar un modelo.

1. En la página **Información general** del recurso Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador.
2. En Azure OpenAI Studio, en la página **Implementaciones**, visualiza las implementaciones de modelos existentes. Si aún no tienes una, crea una nueva implementación del modelo **gpt-35-turbo-16k** con la siguiente configuración:
    - **Modelo**: gpt-35-turbo-16k *(si el modelo 16k no estuviera disponible, elija gpt-35-turbo)*
    - **Versión de Modev**: actualización automática al valor predeterminado.
    - **Nombre de implementación**: *Un nombre único que prefiera. Usará este valor más adelante en este laboratorio.*
    - **Opciones avanzadas**
        - **Filtro de contenido**: valor predeterminado
        - **Tipo de implementación**: Estándar
        - **Límite de velocidad de tokens por minuto**: 5000\*
        - **Habilitación de la cuota dinámica**: habilitado

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que deja capacidad para otras personas que usan la misma suscripción.

## Explorar técnicas de ingeniería de solicitudes

Comencemos explorando algunas técnicas de ingeniería de solicitud en el área de juegos de chat.

1. En **Azure OpenAI Studio**, en `https://oai.azure.com`, en la sección **Área de juegos**, seleccione la página **Chat**. La página del área de juegos de **Chat** consta de tres secciones principales:
    - **Instalación**: se usa para establecer el contexto de las respuestas del modelo.
    - **Sesión de chat**: se usa para enviar mensajes de chat y ver respuestas.
    - **Configuración**: se usa para configurar los valores de la implementación de modelo.
2. En el panel **Configuración**, asegúrese de que la implementación de modelo esté seleccionada.
3. En el área **Configuración**, seleccione la plantilla de mensaje del sistema predeterminada para establecer el contexto de la sesión de chat. El mensaje predeterminado del sistema es *Es un asistente de IA que ayuda a las personas a encontrar información*.
4. En la **sesión de chat**, envíe las siguientes consultas:

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    La respuesta proporciona una descripción del artículo. Sin embargo, supongamos que desea un formato más específico para la categorización de artículos.

5. En la sección **Configuración** cambie el mensaje del sistema por `You are a news aggregator that categorizes news articles.`

6. En el nuevo mensaje del sistema, en la sección **Ejemplos**, seleccione el botón **Agregar**. Después, agregue el siguiente ejemplo.

    **Usuario:**
    
    ```prompt
    What kind of article is this?
    ---
    New York Baseballers Wins Big Against Chicago
    
    New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
    
    Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
    
    The Chicago Cyclones' two hits came in the 2nd and the 5th innings but were unable to get the runner home to score.
    ```
    
    **Asistente:**
    
    ```prompt
    Sports
      ```

7. Agregue otro ejemplo con el siguiente texto.

    **Usuario:**
    
    ```prompt
    Categorize this article:
    ---
    Joyous moments at the Oscars
    
    The Oscars this past week where quite something!
    
    Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
    These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
    
    From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```
    
    **Asistente:**
    
    ```prompt
    Entertainment
    ```

8. Use el botón **Aplicar cambios** que hay situado en la parte superior de la sección **Configuración** para actualizar el mensaje del sistema.

9. En la sección **Sesión de chat**, vuelva a enviar la siguiente solicitud:

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    La combinación de un mensaje del sistema más específico y algunos ejemplos de consultas y respuestas esperadas da como resultado un formato coherente en los resultados.

10. En la sección **Configuración**, vuelva a cambiar el mensaje del sistema por la plantilla predeterminada, que debería ser `You are an AI assistant that helps people find information.` sin ejemplos. A continuación, aplique los cambios.

11. En la sección **Sesión de chat**, envíe la siguiente solicitud:

    ```prompt
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    Es probable que el modelo responda con una respuesta para satisfacer el mensaje, dividida en una lista numerada. Se trata de una respuesta adecuada, pero supongamos que lo que realmente quería era que el modelo escribiera un programa de Python que realizase las tareas descritas.

12. Cambie el mensaje del sistema a `You are a coding assistant helping write python code.` y aplique los cambios.
13. Vuelva a enviar el siguiente mensaje al modelo:

    ```
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    El modelo debería responder correctamente con código de Python haciendo lo solicitado en los comentarios.

## Preparación para desarrollar una aplicación en Visual Studio Code

Ahora exploremos el uso de ingeniería de solicitudes en una aplicación que use el SDK del servicio Azure OpenAI. Desarrollará la aplicación con Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio **mslearn-openai**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.

    > **Nota**: Si Visual Studio Code muestra un mensaje emergente para solicitarle que confíe en el código que está abriendo, haga clic en la opción **Sí, confío en los autores** en el elemento emergente.

4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

## Configuración de la aplicación

Se proporcionaron aplicaciones para C# y Python, y ambas presentan la misma funcionalidad. En primer lugar, completará algunas partes clave de la aplicación para habilitar el uso del recurso Azure OpenAI con llamadas API asincrónicas.

1. En Visual Studio Code, en el panel **Explorador**, vaya a la carpeta **Labfiles/03-prompt-engineering** y expanda las carpetas **CSharp** o **Python**, según sus preferencias de lenguaje. Cada carpeta contiene los archivos específicos del lenguaje de una aplicación en la que va a integrar la funcionalidad de OpenAI de Azure.
2. Haga clic con el botón derecho en la carpeta **CSharp** o **Python** que contiene los archivos de código y abra un terminal integrado. A continuación, instale el paquete del SDK de Azure OpenAI mediante la ejecución del comando adecuado para sus preferencias de lenguaje:

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
5. Guarde el archivo de configuración.

## Adición de código para usar el servicio Azure OpenAI

Ya está listo para usar el SDK de Azure OpenAI para consumir el modelo implementado.

1. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de código de su idioma preferido y reemplace el comentario ***Agregar paquete de Azure OpenAI*** por código para agregar la biblioteca del SDK de Azure OpenAI:

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**: prompt-engineering.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. En el archivo de código, busque el comentario ***Configurar el cliente de Azure OpenAI*** y agregue código para configurarlo:

    **C#** : Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**: prompt-engineering.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. En la función que llama al modelo de Azure OpenAI, en el comentario ***Dar formato y enviar la solicitud al modelo***, agregue el código para dar formato y enviar la solicitud al modelo.

    **C#** : Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(userMessage)
        },
        Temperature = 0.7f,
        MaxTokens = 800,
        DeploymentName = oaiDeploymentName
    };
    
    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);
    ```

    **Python**: prompt-engineering.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )
    ```

4. Guarde los cambios en el archivo del código.

## Ejecución de la aplicación

Ahora que ha configurado la aplicación, ejecútela para enviarle la solicitud al modelo y ver la respuesta. Observe que la única diferencia entre las distintas opciones es el contenido del mensaje. Todos los demás parámetros, como el recuento de tokens y la temperatura, son los mismos para todas las solicitudes.

1. En la carpeta del idioma de su preferencia, abra `system.txt` en Visual Studio Code. Para cada una de las intercalaciones, escribirá el **Mensaje del sistema** en este archivo y lo guardará. Cada iteración se pausará primero para que cambie el mensaje del sistema.
1. En el panel de terminal interactivo, asegúrese de que el contexto de la carpeta es la carpeta del lenguaje que prefiera. Después, escriba el siguiente comando para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python prompt-engineering.py`

    > **Sugerencia**: Puede usar el icono **Maximizar el tamaño del panel** (**^**) en la barra de herramientas del terminal para ver más del texto de la consola.

1. Para la primera iteración, escriba las siguientes indicaciones:

    **Mensaje del sistema**

    ```prompt
    You are an AI assistant
    ```

    **Mensaje de usuario:**

    ```prompt
    Write an intro for a new wildlife Rescue
    ```

1. Observe la salida. Es probable que el modelo de inteligencia artificial produzca una buena introducción genérica a un rescate de la fauna silvestre.
1. A continuación, escriba las siguientes indicaciones, que especifican un formato para la respuesta:

    **Mensaje del sistema**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Mensaje de usuario:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants 
    - Call for donations to be given at our website
    ```

1. Observe la salida. Esta vez, es probable que vea el formato de un correo electrónico con animales específicos incluidos, junto con una petición de donaciones.
1. A continuación, escriba las siguientes indicaciones, que especifican además el contenido:

    **Mensaje del sistema**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Mensaje de usuario:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Observe la salida y vea cómo cambió el correo electrónico en función de sus claras instrucciones.
1. A continuación, escriba las siguientes indicaciones, en las que se agregan detalles sobre el tono al mensaje del sistema:

    **Mensaje del sistema**

    ```prompt
    You are an AI assistant that helps write promotional emails to generate interest in a new business. Your tone is light, chit-chat oriented and you always include at least two jokes.
    ```

    **Mensaje de usuario:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Observe la salida. Esta vez es probable que vea el correo electrónico con un formato similar, pero con un tono mucho más informal. Incluso puede que incluya algún chiste.
1. Para la iteración final, nos desviaremos de la generación de correo electrónico y exploraremos el *contexto base*. Aquí se proporciona un mensaje del sistema sencillo y se cambia la aplicación para proporcionar contexto base al principio del mensaje del usuario. A continuación, la aplicación anexará la entrada de usuario y extraerá información del contexto base para responder a la solicitud del usuario.
1. Abra el archivo `grounding.txt` y lea brevemente el contexto base que insertará.
1. En la aplicación inmediatamente posterior al comentario ***Dar formato y enviar la solicitud al modelo*** y antes de cualquier código existente, agregue el siguiente fragmento de código para leer texto desde `grounding.txt` para aumentar las solicitudes de usuario con contexto base.

    **C#** : Program.cs

    ```csharp
    // Format and send the request to the model
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    userMessage = groundingText + userMessage;
    ```

    **Python**: prompt-engineering.py

    ```python
    # Format and send the request to the model
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    user_message = grounding_text + user_message
    ```

1. Guarde el archivo y vuelva a ejecutar la aplicación.
1. Escriba las siguientes indicaciones (con el **mensaje del sistema** aún escribiéndose y guardándose en `system.txt`).

    **Mensaje del sistema**

    ```prompt
    You're an AI assistant who helps people find information. You'll provide answers from the text provided in the prompt, and respond concisely.
    ```

    **Mensaje de usuario:**

    ```prompt
    What animal is the favorite of children at Contoso?
    ```

> **Sugerencia**: Si quisiera ver la respuesta completa de Azure OpenAI, establezca la variable **printFullResponse** en `True` y vuelva a ejecutar la aplicación.

## Limpiar

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o todo el recurso en **Azure Portal**, en `https://portal.azure.com`.
