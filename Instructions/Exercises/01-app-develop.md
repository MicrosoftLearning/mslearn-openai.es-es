---
lab:
  title: Desarrollo de aplicaciones con Azure OpenAI Service
  status: new
---

# Desarrollo de aplicaciones con Azure OpenAI Service

Con el Azure OpenAI Service, los desarrolladores pueden crear bots de chat y otras aplicaciones que destaquen en el reconocimiento del lenguaje humano natural mediante el uso de API de REST o SDK específicos del lenguaje. Cuando se trabaja con estos modelos de lenguaje, el modo en que los desarrolladores dan forma a sus solicitudes afecta considerablemente a la forma en que responderá el modelo de IA generativa. Los modelos de Azure OpenAI pueden adaptar y dar formato al contenido si se les solicita de forma clara y concisa. En este ejercicio, verás cómo conectar tu aplicación a Azure OpenAI y cómo diferentes solicitudes de contenido similar ayudan a dar forma a la respuesta del modelo de IA para satisfacer mejor tus requisitos.

En el escenario de este ejercicio, asumirás el rol de un desarrollador de software que trabaja en una campaña de marketing sobre vida silvestre. Está explorando cómo usar IA generativa para mejorar los correos electrónicos publicitarios y clasificar artículos que se pudieran aplicar al equipo. Las técnicas de ingeniería de solicitud usadas en el ejercicio se aplican de forma similar para una variedad de casos de uso.

Este ejercicio dura aproximadamente **30** minutos.

## Clonación del repositorio para este curso

Si aún no lo ha hecho, debe clonar el repositorio de código para este curso:

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.
4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

## Aprovisionamiento de un recurso de Azure OpenAI

Si aún no tiene uno, aprovisione un recurso de Azure OpenAI en la suscripción de Azure.

1. Inicie sesión en **Azure Portal** en `https://portal.azure.com`.

1. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: *Selección de una suscripción de Azure aprobada para acceder al servicio Azure OpenAI*
    - **Grupo de recursos**: *elija o cree un grupo de recursos*
    - **Región**: *Elija de forma **aleatoria** cualquiera de las siguientes regiones*\*
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

Después, implementarás un recurso de modelo de Azure OpenAI desde la CLI. Consulta este ejemplo y reemplaza las siguientes variables por tus propios valores:

```dotnetcli
az cognitiveservices account deployment create \
   -g *Your resource group* \
   -n *Name of your OpenAI service* \
   --deployment-name gpt-35-turbo \
   --model-name gpt-35-turbo \
   --model-version 0125  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

    > \* Sku-capacity is measured in thousands of tokens per minute. A rate limit of 5,000 tokens per minute is more than adequate to complete this exercise while leaving capacity for other people using the same subscription.

> [!NOTE]
> Ignora las advertencias sobre la falta de soporte técnico del framework net7.0 en este ejercicio.

## Configuración de la aplicación

Se proporcionaron aplicaciones para C# y Python, y ambas presentan la misma funcionalidad. En primer lugar, completará algunas partes clave de la aplicación para habilitar el uso del recurso Azure OpenAI con llamadas API asincrónicas.

1. En Visual Studio Code, en el panel **Explorador**, ve a la carpeta **Labfiles/01-app-develop** y expande la carpeta **CSharp** o **Python** según tus preferencias de lenguaje. Cada carpeta contiene los archivos específicos del lenguaje de una aplicación en la que va a integrar la funcionalidad de OpenAI de Azure.
2. Haga clic con el botón derecho en la carpeta **CSharp** o **Python** que contiene los archivos de código y abra un terminal integrado. A continuación, instale el paquete del SDK de Azure OpenAI mediante la ejecución del comando adecuado para sus preferencias de lenguaje:

    **C#:**

    ```
    dotnet add package Azure.AI.OpenAI --version 2.0.0
    ```

    **Python**:

    ```
    pip install openai==1.54.3
    ```

3. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de configuración para su lenguaje preferido.

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Actualiza los valores de configuración para incluir:
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - El **nombre de implementación** que has especificado para la implementación de tu modelo.
5. Guarde el archivo de configuración.

## Adición de código para usar el servicio Azure OpenAI

Ya está listo para usar el SDK de Azure OpenAI para consumir el modelo implementado.

1. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de código de su idioma preferido y reemplace el comentario ***Agregar paquete de Azure OpenAI*** por código para agregar la biblioteca del SDK de Azure OpenAI:

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python**: application.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. En el archivo de código, busque el comentario ***Configurar el cliente de Azure OpenAI*** y agregue código para configurarlo:

    **C#** : Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
    ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
    ```

    **Python**: application.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. En la función que llama al modelo de Azure OpenAI, en el comentario ***Obtener respuesta de Azure OpenAI***, agrega el código para darle formato a la solicitud y enviársela al modelo.

    **C#**: Program.cs

    ```csharp
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        [
            new SystemChatMessage(systemMessage),
            new UserChatMessage(userMessage)
        ],
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    ```

    **Python**: application.py

    ```python
    # Get response from Azure OpenAI
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

1. En la carpeta del idioma de su preferencia, abra `system.txt` en Visual Studio Code. Para cada interacción, introducirás el **Mensaje del sistema** en este archivo y lo guardarás. Cada iteración se pausará primero para que cambie el mensaje del sistema.
1. En el panel de terminal interactivo, asegúrese de que el contexto de la carpeta es la carpeta del lenguaje que prefiera. Después, escriba el siguiente comando para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python application.py`

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

    > **Consejo**: puede que la escritura automática en la VM no funcione bien con consultas de varias líneas. Si tienes ese problema, copia toda la consulta y pégala en Visual Studio Code.

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

    **Python**: application.py

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
