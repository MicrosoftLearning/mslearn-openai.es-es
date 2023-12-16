---
lab:
  title: Generación y mejora del código con Azure OpenAI Service
---

# Generación y mejora del código con Azure OpenAI Service

Los modelos de Azure OpenAI Service pueden generar código usando mensajes en lenguaje natural, corrigiendo errores en código finalizado y proporcionado comentarios sobre el código. Estos modelos también pueden explicar y simplificar el código, lo que le ayuda a saber lo que hace y cómo mejorarlo.

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

Para usar Azure OpenAI API para generar código, primero hay que implementar un modelo para usarlo con **Azure OpenAI Studio**. Una vez implementado, usaremos el modelo con el área de juegos y haremos referencia a él en la aplicación.

1. En la página **Información general** del recurso de Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador. Pero tenga en cuenta que también puede ir directamente a [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true).
2. En Azure OpenAI Studio, en la página **Implementaciones**, visualiza las implementaciones de modelos existentes. Si aún no tienes una, crea una nueva implementación del modelo **gpt-35-turbo-16k** con la siguiente configuración:
    - **Modelo**: gpt-35-turbo-16k
    - **Versión de Modev**: actualización automática al valor predeterminado.
    - **Nombre de implementación**: *nombre único que prefieras*
    - **Opciones avanzadas**
        - **Filtro de contenido**: valor predeterminado
        - **Límite de velocidad de tokens por minuto**: 5000\*
        - **Habilitación de la cuota dinámica**: habilitado

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que deja capacidad para otras personas que usan la misma suscripción.

> **Nota**: en algunas regiones, la nueva interfaz de implementación de modelos no muestra la opción **Versión del modelo**. En este caso, no te preocupes y continúa sin establecer la opción

## Generación de código en el área de juegos de chat

Antes de usarlo en la aplicación, examine la forma en que Azure OpenAI puede generar y explicar código en el área de juegos de chat.

1. En [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true), vaya al área de juegos de **chat** del panel izquierdo.
1. 1. En Configuración****, asegúrate de que la implementación del modelo está seleccionada.
1. En la sección **Configuración del asistente** de la parte superior, seleccione la plantilla de mensaje del sistema **Predeterminado**.
1. En la sección **Sesión de chat**, escriba el siguiente mensaje y presione *Entrar*.

    ```code
   Write a function in python that takes a character and string as input, and returns how many times that character appears in the string
    ```

1. Es probable que el modelo responda con una función, que explique lo que hace la función y que indique cómo llamarla.
1. A continuación, envíe el mensaje `Do the same thing, but this time write it in C#`.
1. Observe la salida. Es probable que el modelo responda de forma muy similar a la primera vez, pero esta vez con código de C#. Puede volver a pedirlo para cualquier otro lenguaje o una función para completar una tarea diferente, como revertir la cadena de entrada.
1. Ahora vamos a explorar el uso de la inteligencia artificial para comprender el código con este ejemplo de una función aleatoria escrita en Ruby. Envíe lo siguiente como mensaje del usuario.

    ```code
    What does the following function do?  
    ---  
    def random_func(n)
      start = [0, 1]
      (n - 2).times do
        start << start[-1] + start[-2]
      end
      start.shuffle.each do |num|
        puts num
      end
    end
    ```

1. Observe la salida, que explica lo que hace la función en lenguaje natural. Pruebe a solicitar al modelo que vuelva a escribirla en un lenguaje que conozca.

## Configuración de aplicaciones en Cloud Shell

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
    cd azure-openai/Labfiles/04-code-generation
    ```

7. Abre el editor de código integrado ejecutando el comando siguiente:

    ```bash
    code .
    ```

8. En el editor de código, expande la carpeta **sample-code** y revisa los archivos de código que la aplicación usará para mejorar el modelo.

    > **Sugerencia**: consulta la [documentación del editor de código de Azure Cloud Shell](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor) para más información sobre cómo usarla para trabajar con archivos en el entorno de Azure Cloud Shell.

## Configuración de la aplicación

En este ejercicio, completará algunas partes clave de la aplicación para habilitarla con el recurso de Azure OpenAI. Se han proporcionado aplicaciones para C# y Python.

1. En el editor de código, expanda la carpeta del lenguaje que prefiera.

2. Abra el archivo de configuración de dicho lenguaje.

    - **C#** : `appsettings.json`
    - **Python**: `.env`

3. Actualiza los valores de configuración para incluir el **punto de conexión** y la **clave** del recurso de Azure OpenAI que has creado, así como el nombre de la implementación. Guarde el archivo.

4. En el panel de consola, escribe los siguientes comandos para ir a la carpeta del idioma preferido e instalar los paquetes necesarios.

    **C#**

    ```bash
    cd CSharp
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.9
    ```

    **Python**

    ```bash
    cd Python
    pip install python-dotenv
    pip install openai==1.2.0
    ```

5. Seleccione en esta carpeta el archivo de código del lenguaje y agregue las bibliotecas necesarias.

    **C#** : Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**: code-generation.py

    ```python
    # Add OpenAI import
    from openai import AzureOpenAI
    ```

6. Agregue el código necesario para configurar el cliente.

    **C#** : Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**: code-generation.py

    ```python
    # Set OpenAI configuration settings
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2023-05-15"
            )
    ```

7. En la función que llama al modelo de Azure OpenAI, agregue el código para darle formato a la solicitud y enviársela al modelo.

    **C#** : Program.cs

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
        MaxTokens = 1000,
        DeploymentName = oaiModelName
    };

    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);

    ChatCompletions completions = response.Value;
    string completion = completions.Choices[0].Message.Content;
    ```

    **Python**: code-generation.py

    ```python
    # Build the messages array
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    # Call the Azure OpenAI model
    response = client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=1000
    )
    ```

## Ejecución de la aplicación

Ahora que ha configurado la aplicación, ejecútela para intentar generar código para cada caso de uso. El caso de uso está numerado en la aplicación y se puede ejecutar en cualquier orden.

> **Nota**: Algunos usuarios pueden experimentar una limitación de volumen si se llama al modelo con demasiada frecuencia. Si se produce un error relacionado con el límite de volumen de tokens, espere un minuto y vuelva a intentarlo.

1. En el editor de código, expanda la carpeta `sample-code` y observe brevemente la función y la aplicación para el lenguaje. Estos archivos se usarán para las tareas de la aplicación.
1. En el terminal de Bash de Cloud Shell, vaya a la carpeta del lenguaje que prefiera.
1. Ejecute la aplicación.

    - **C#** : `dotnet run`
    - **Python**: `python code-generation.py`

1. Elija la opción **1** para agregar comentarios al código. Tenga en cuenta que la respuesta puede tardar unos segundos.
1. Los resultados se colocarán en `result/app.txt`. Abra ese archivo y compárelo con el archivo de la función que está en `sample-code`.
1. Después, elija la opción **2** para escribir pruebas unitarias para esa misma función.
1. Los resultados reemplazarán lo que había en `result/app.txt` e indican que hay cuatro pruebas unitarias para esa función.
1. Luego, elija la opción **3** para corregir errores en una aplicación para jugar a Go Fish.
1. Los resultados reemplazarán lo que había en `result/app.txt` y deben tener un código muy similar con algunas cosas corregidas.

    - **C#** : se realizan correcciones en las líneas 30 y 59.
    - **Python**: se realizan correcciones en las líneas 18 y 31.

La aplicación para jugar a Go Fish que está en `sample-code` se puede ejecutar, siempre que reemplace las líneas con errores por la respuesta de Azure OpenAI. Si la ejecuta sin las correcciones, no funcionará correctamente.

Es importante tener en cuenta que, aunque se ha corregido una parte de la sintaxis en el código de esta aplicación Go Fish, no es una representación estrictamente precisa del juego. Si la observa detenidamente, verá que no se comprueba si quedan cartas en el mazo antes de robar, no desaparecen las parejas a los jugadores cuando las forman, así como algunos errores más que requieren conocer los juegos de cartas para darse cuenta. Este es un excelente ejemplo de lo útiles que pueden ser los modelos de inteligencia artificial generativa como ayuda para la generación de código, pero no se puede confiar en ellos completamente, el desarrollador debe realizar una última comprobación.

Si desea ver la respuesta completa de Azure OpenAI, establezca la variable `printFullResponse` en `True` y vuelva a ejecutar la aplicación.

## Limpieza

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o el recurso entero en [Azure Portal](https://portal.azure.com/?azure-portal=true).
