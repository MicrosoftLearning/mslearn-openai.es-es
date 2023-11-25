---
lab:
  title: Uso de la ingeniería de mensajes en una aplicación
---

# Uso de la ingeniería de mensajes en una aplicación

Con Azure OpenAI Service, los desarrolladores pueden crear bots de chat, modelos de lenguaje y otras aplicaciones que destaquen en el reconocimiento del lenguaje humano natural. Azure OpenAI proporciona acceso a modelos de inteligencia artificial previamente entrenados, así como a un conjunto de API y herramientas para personalizar y ajustar esos modelos con el fin de satisfacer los requisitos específicos de una aplicación. En este ejercicio aprenderá a implementar un modelo en Azure OpenAI y a usarlo en su propia aplicación para resumir texto.

Cuando se trabaja con Azure OpenAI Service, el modo en el que los desarrolladores dan forma a los mensajes afecta considerablemente a la forma en la responderá el modelo de IA generativa. Los modelos de Azure OpenAI pueden adaptar y dar formato al contenido si se les solicita de forma clara y concisa. En este ejercicio verá cómo diferentes solicitudes de contenido similar ayudan a dar forma a la respuesta del modelo de IA para satisfacer mejor sus requisitos.

Este ejercicio dura aproximadamente **25** minutos.

## Antes de comenzar

Necesitará una suscripción de Azure que tenga acceso a Azure OpenAI Service.

- Para registrarse para obtener una suscripción gratuita de Azure, vaya a [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acceso a Azure OpenAI Service, vaya a [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Aprovisionamiento de un recurso de Azure OpenAI

Para poder usar modelos de Azure OpenAI, debe aprovisionar un recurso de Azure OpenAI en su suscripción de Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: una suscripción de Azure que tenga acceso a Azure OpenAI Service.
    - **Grupo de recursos**: elija un grupo de recursos existente o cree uno nuevo con un nombre de su elección.
    - **Región**: elija cualquier región disponible.
    - **Nombre**: elija un nombre único.
    - **Plan de tarifa**: estándar S0
3. Espere a que la implementación finalice. A continuación, vaya al recurso de Azure OpenAI implementado en Azure Portal.
4. Vaya a la página **Claves y punto de conexión** y guarde esos datos en un archivo de texto para usarlos más adelante.

## Implementar un modelo

Para usar la API de Azure OpenAI, primero debe implementar un modelo para usarlo con **Azure OpenAI Studio**. Una vez implementado, haremos referencia a ese modelo en la aplicación.

1. En la página **Información general** del recurso de Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador. También puede ir directamente a [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true).
2. En Azure OpenAI Studio, cree una nueva implementación con la siguiente configuración:
    - **Modelo**: gpt-35-turbo
    - **Versión del modelo**: *use la versión predeterminada*
    - **Nombre de implementación**: text-turbo

> **Nota**: Cada modelo de Azure OpenAI está optimizado para un equilibrio de funcionalidad y rendimiento diferente. En este ejercicio usaremos la serie de modelos **3.5 Turbo** en la familia de modelos **GPT-3**, que tiene una alta capacidad de reconocimiento del lenguaje. En este ejercicio se usa solamente un único modelo, pero la implementación y el uso de otros modelos que implemente funcionarán de la misma manera.

## Aplicación de la ingeniería de mensajes en el área de juegos de chat

Antes de usar la aplicación, vea cómo la ingeniería de mensajes mejora la respuesta del modelo en el área de juegos. En este primer ejemplo, imagine que está intentando escribir una aplicación de Python de animales con nombres divertidos.

1. En [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true), vaya al área de juegos **Chat** del panel izquierdo.
1. En la sección **Configuración del Asistente** de la parte superior, escriba `You are a helpful AI assistant` como mensaje del sistema.
1. En la sección **Sesión de chat**, escriba el siguiente mensaje y presione *Entrar*.

    ```code
   1. Create a list of animals
   2. Create a list of whimsical names for those animals
   3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. Es probable que el modelo responda con una respuesta para satisfacer el mensaje, dividida en una lista numerada. Esta es una buena respuesta, pero no es lo que estamos buscando.
1. A continuación, actualice el mensaje del sistema para incluir las instrucciones `You are an AI assistant helping write python code. Complete the app based on provided comments`. Haga clic en **Guardar cambios**.
1. Dé a las instrucciones el formato de comentarios de Python. Envíe el siguiente mensaje al modelo.

    ```code
   # 1. Create a list of animals
   # 2. Create a list of whimsical names for those animals
   # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

1. El modelo debe responder correctamente con código de Python completo que haga lo que se solicitaba en los comentarios.
1. A continuación, veremos el impacto que tiene el uso de mensajes con algunos ejemplos (few shot prompting) al intentar clasificar artículos. Vuelva al mensaje del sistema, escriba `You are a helpful AI assistant` de nuevo y guarde los cambios. Esto creará una nueva sesión de chat.
1. Envíe el siguiente mensaje al modelo.

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. Es probable que la respuesta sea alguna información sobre la sequía en California. Aunque no es una mala respuesta, no es la clasificación que estamos buscando.
1. En la sección **Configuración del asistente** cerca del mensaje del sistema, seleccione el botón **Agregar un ejemplo**. Agregue el siguiente ejemplo.

    **Usuario:**

    ```code
   New York Baseballers Wins Big Against Chicago
   
   New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
   
   Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
   
   The Chicago Cyclones' two hits came in the 2nd and the 5th innings, but were unable to get the runner home to score.
    ```

    **Asistente:**

    ```code
   Sports
    ```

1. Agregue otro ejemplo con el siguiente texto.

    **Usuario:**

    ```code
   Joyous moments at the Oscars

   The Oscars this past week where quite something!
   
   Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
   These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
   
   From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```

    **Asistente:**

    ```code
   Entertainment
    ```

1. Guarde los modificados en la configuración del asistente y envíe el mismo mensaje sobre la sequía de California, que se proporciona aquí de nuevo para mayor comodidad.

    ```code
   Severe drought likely in California

   Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
   
   In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
   
   Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

1. Esta vez el modelo debe responder con una clasificación adecuada, incluso sin instrucciones.

## Configuración de una aplicación en Cloud Shell

Para mostrar cómo integrar un modelo de Azure OpenAI, usaremos una breve aplicación de la línea de comandos que se ejecuta en Cloud Shell en Azure. Abra una nueva pestaña del explorador para trabajar con Cloud Shell.

1. En [Azure Portal](https://portal.azure.com?azure-portal=true), seleccione el botón **[>_]** (*Cloud Shell*) en la parte superior de la página, a la derecha del cuadro de búsqueda. Se abrirá un panel de Cloud Shell en la parte inferior del portal.

    ![Captura de pantalla de cómo se inicia de Cloud Shell haciendo clic en el icono situado a la derecha del cuadro de búsqueda superior.](../media/cloudshell-launch-portal.png#lightbox)

2. La primera vez que abra Cloud Shell, es posible que se le pida que elija el tipo de shell que desea usar (*Bash* o *PowerShell*). Seleccione **Bash**. Si no ve esta opción, omita el paso.  

3. Si se le pide que cree almacenamiento para Cloud Shell, seleccione **Mostrar configuración avanzada** y seleccione la siguiente configuración:
    - **Suscripción**: Su suscripción
    - **Regiones de Cloud Shell**: elija cualquier región disponible
    - No está seleccionado **Mostrar conjuntos de aislamiento de red virtual**
    - **Grupo de recursos**: use el grupo de recursos existente en el que aprovisionó el recurso de Azure OpenAI
    - **Cuenta de almacenamiento**: cree una cuenta de almacenamiento nueva con un nombre único
    - **Recurso compartido de archivos**: cree un nuevo recurso compartido de archivos con un nombre único

    A continuación, espere un minuto más o menos a que se cree el almacenamiento.

    > **Nota**: Si ya tiene una instancia de Cloud Shell configurada en su suscripción de Azure, es posible que tenga que usar la opción **Restablecer configuración de usuario** en el menú ⚙️ para asegurarse de que están instaladas las versiones más recientes de Python y .NET Framework.

4. Asegúrese de que el tipo de shell indicado en la parte superior izquierda del panel de Cloud Shell sea *Bash*. Si es *PowerShell*, cambie a *Bash* mediante el menú desplegable.

5. Cuando se inicie el terminal, escriba el siguiente comando para descargar la aplicación de ejemplo y guárdela en una carpeta denominada `azure-openai`.

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```

6. Los archivos se descargan en una carpeta denominada **azure-openai**. Vaya a los archivos del laboratorio para este ejercicio con el siguiente comando.

    ```bash
   cd azure-openai/Labfiles/03-prompt-engineering
    ```

    Se han proporcionado aplicaciones para C# y Python, así como archivos de texto que proporcionan los mensajes. Las dos aplicaciones tienen la misma funcionalidad.

    Abra el editor de código integrado, donde puede ver los archivos de mensajes que va a usar en `prompts`. Use el siguiente comando para abrir los archivos del laboratorio en el editor de código.

    ```bash
   code .
    ```

## Configuración de la aplicación

En este ejercicio, completará algunas partes clave de la aplicación para habilitarla con el recurso de Azure OpenAI.

1. En el editor de código, expanda la carpeta **CSharp** o **Python**, según el lenguaje que prefiera.

2. Abra el archivo de configuración para el lenguaje.

    - C#: `appsettings.json`
    - Python: `.env`
    
3. Actualice los valores de configuración para incluir el **punto de conexión** y la **clave** del recurso de Azure OpenAI que ha creado, así como el nombre del modelo que ha implementado, `text-turbo`. Guarde el archivo.

4. Vaya a la carpeta del lenguaje que prefiera e instale los paquetes necesarios.

    **C#**

    ```bash
   cd CSharp
   dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.5
    ```

    **Python**

    ```bash
   cd Python
   pip install python-dotenv
   pip install openai
    ```

5. Vaya a la carpeta del lenguaje que prefiera, seleccione el archivo de código y agregue las bibliotecas necesarias.

    **C#**

    ```csharp
   // Add Azure OpenAI package
   using Azure.AI.OpenAI;
    ```

    **Python**

    ```python
   # Add OpenAI import
   import openai
    ```

5. Abra el código de la aplicación para el lenguaje y agregue el código necesario para configurar el cliente.

    **C#**

    ```csharp
   // Initialize the Azure OpenAI client
   OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**

    ```python
   # Set OpenAI configuration settings
   openai.api_type = "azure"
   openai.api_base = azure_oai_endpoint
   openai.api_version = "2023-03-15-preview"
   openai.api_key = azure_oai_key
    ```

6. En la función que llama al modelo de Azure OpenAI, agregue el código para darle formato a la solicitud y enviársela al modelo.

    **C#**

    ```csharp
   // Create chat completion options
   var chatCompletionsOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
          new ChatMessage(ChatRole.System, systemPrompt),
          new ChatMessage(ChatRole.User, userPrompt)
       },
       Temperature = 0.7f,
       MaxTokens = 800,
   };

   // Get response from Azure OpenAI
   Response<ChatCompletions> response = await client.GetChatCompletionsAsync(
       oaiModelName,
       chatCompletionsOptions
   );

   ChatCompletions completions = response.Value;
   string completion = completions.Choices[0].Message.Content;
    ```

    **Python**

    ```python
   # Build the messages array
   messages =[
       {"role": "system", "content": system_message},
       {"role": "user", "content": user_message},
   ]

   # Call the Azure OpenAI model
   response = openai.ChatCompletion.create(
       engine=model,
       messages=messages,
       temperature=0.7,
       max_tokens=800
   )
    ```

## Ejecución de la aplicación

Ahora que ha configurado la aplicación, ejecútela para enviarle la solicitud al modelo y ver la respuesta. Observe que la única diferencia entre las distintas opciones es el contenido del mensaje. Todos los demás parámetros, como el recuento de tokens y la temperatura, son los mismos para todas las solicitudes.

Cada mensaje se muestra en la consola conforme se envía para que vea cómo los distintos mensajes generan respuestas diferentes.

1. En el terminal de Bash en Cloud Shell, vaya a la carpeta del lenguaje que prefiera.
1. Ejecute la aplicación y expanda el terminal para que ocupe la mayor parte de la ventana del explorador.

    - **C#** : `dotnet run`
    - **Python**: `python prompt-engineering.py`

1. Elija la opción **1** para usar el mensaje más básico.
1. Observe el mensaje de entrada y la salida generada. Es probable que el modelo de inteligencia artificial produzca una buena introducción genérica a un rescate de la fauna silvestre.
1. Después, elija la opción **2** para darle un mensaje que le pida un correo electrónico de introducción, junto con algunos detalles sobre el rescate de la fauna silvestre.
1. Observe el mensaje de entrada y la salida generada. Esta vez, es probable que vea el formato de un correo electrónico con animales específicos incluidos, junto con una petición de donaciones.
1. A continuación, elija la opción **3** para solicitar un correo electrónico similar al anterior, pero que incluya una tabla con formato con más animales.
1. Observe el mensaje de entrada y la salida generada. Esta vez es probable que vea un correo electrónico similar con texto con un formato específico (en este caso, una tabla cerca del final) que muestra cómo los modelos de IA generativa pueden dar formato a la salida cuando se les solicita.
1. Después, elija la opción **4** para solicitar un correo electrónico similar, pero esta vez especificando un tono diferente en el mensaje del sistema.
1. Observe el mensaje de entrada y la salida generada. Esta vez es probable que vea el correo electrónico con un formato similar, pero con un tono mucho más informal. Incluso puede que incluya algún chiste.

Al aumentar la temperatura, la respuesta suele variar, incluso si se proporciona el mismo mensaje, debido al aumento de la aleatoriedad. Puede ejecutarlo varias veces con valores de temperatura diferentes o top_p para ver cómo afecta a la respuesta para el mismo mensaje.

Si desea ver la respuesta completa de Azure OpenAI, establezca la variable `printFullResponse` en `True` y vuelva a ejecutar la aplicación.

## Limpieza

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o el recurso entero en [Azure Portal](https://portal.azure.com/?azure-portal=true).
