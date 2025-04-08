---
lab:
  title: Generación y mejora del código con Azure OpenAI Service
  status: stale
---

# Generación y mejora del código con Azure OpenAI Service

Los modelos de Azure OpenAI Service pueden generar código usando mensajes en lenguaje natural, corrigiendo errores en código finalizado y proporcionado comentarios sobre el código. Estos modelos también pueden explicar y simplificar el código, lo que le ayuda a saber lo que hace y cómo mejorarlo.

En el escenario de este ejercicio, desempeñará el rol de un desarrollador de software que explora cómo usar inteligencia artificial generativa para facilitar y mejorar la eficacia de las tareas de codificación. Las técnicas que se usan en el ejercicio se pueden aplicar a otros archivos de código, lenguajes de programación y casos de uso.

Este ejercicio dura aproximadamente **25** minutos.

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

Azure proporciona un portal basado en web denominado **Portal de Azure AI Foundry** que puedes usar para implementar, administrar y explorar modelos. Para iniciar la exploración de Azure OpenAI, usa Portal de Azure AI Foundry para implementar un modelo.

> **Nota**: a medida que usas Portal de Azure AI Foundry, es posible que se muestren cuadros de mensaje que sugieren tareas que se van a realizar. Puede cerrarlos y seguir los pasos descritos en este ejercicio.

1. En Azure Portal, en la página **Información general** del recurso de Azure OpenAI, desplázate hacia abajo hasta la sección **Comenzar** y selecciona el botón para ir a **Portal de Azure AI Foundry** (anteriormente AI Studio).
1. En Portal de Azure AI Foundry, en el panel de la izquierda, selecciona la página **Implementaciones** y consulta las implementaciones de modelos existentes. Si aún no tienes una, crea una nueva implementación del modelo **gpt-4o** con la siguiente configuración:
    - **Nombre de implementación**: *nombre único que prefieras*
    - **Modelo**: gpt-4o
    - **Versión del modelo**: *usar la versión predeterminada*
    - **Tipo de implementación**: Estándar
    - **Límite de velocidad de tokens por minuto**: 5000\*
    - **Filtro de contenido**: valor predeterminado
    - **Habilitación de la cuota dinámica**: deshabilitada

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que deja capacidad para otras personas que usan la misma suscripción.

## Generación de código en el área de juegos de chat

Antes de usarlo en la aplicación, examine la forma en que Azure OpenAI puede generar y explicar código en el área de juegos de chat.

1. En la sección **Área de juego**, seleccione la página **Chat**. La página **Chat** del área de juegos consta de una fila de botones y dos paneles principales (que se pueden organizar horizontalmente de derecha a izquierda o verticalmente de arriba a abajo en función de la resolución de pantalla):
    - **Configuración**: se usa para seleccionar la implementación, definir el mensaje del sistema y establecer parámetros para interactuar con la implementación.
    - **Sesión de chat**: se usa para enviar mensajes de chat y ver respuestas.
1. En **Implementaciones**, asegúrate de que la implementación del modelo esté seleccionada.
1. En el área **Mensaje del sistema**, establece el mensaje del sistema en `You are a programming assistant helping write code` y aplica los cambios.
1. En la sección **Sesión de chat**, envíe la siguiente consulta:

    ```prompt
    Write a function in python that takes a character and a string as input, and returns how many times the character appears in the string
    ```

    Es probable que el modelo responda con una función, que explique lo que hace la función y que indique cómo llamarla.

1. A continuación, envíe el mensaje `Do the same thing, but this time write it in C#`.

    Es probable que el modelo responda de forma muy similar a la primera vez, pero esta vez con código de C#. Puede volver a pedirlo para cualquier otro lenguaje o una función para completar una tarea diferente, como revertir la cadena de entrada.

1. Ahora vamos a explorar el uso de inteligencia artificial para comprender el código. Envíe el siguiente mensaje de usuario.

    ```prompt
    What does the following function do?  
    ---  
    def multiply(a, b):  
        result = 0  
        negative = False  
        if a < 0 and b > 0:  
            a = -a  
            negative = True  
        elif a > 0 and b < 0:  
            b = -b  
            negative = True  
        elif a < 0 and b < 0:  
            a = -a  
            b = -b  
        while b > 0:  
            result += a  
            b -= 1      
        if negative:  
            return -result  
        else:  
            return result  
    ```

    El modelo debe describir qué hace la función, que es multiplicar dos números usando un bucle.

1. Envíe la solicitud `Can you simplify the function?`.

    El modelo debe escribir una versión más sencilla de la función.

1. Envíe la solicitud: `Add some comments to the function.`

    El modelo agrega comentarios al código.

## Preparación para desarrollar una aplicación en Visual Studio Code

Ahora vamos a explorar cómo crear una aplicación personalizada que use el servicio Azure OpenAI para generar código. Desarrollará la aplicación con Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio **mslearn-openai**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abre la paleta de comandos (Mayús + Ctrl + P) o **View** > **Command Palette...**) y ejecuta un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abre la carpeta en Visual Studio Code.

    > **Nota**: si Visual Studio Code muestra un mensaje emergente para solicitarte que confíes en el código que estás abriendo, haz clic en la opción **Yes, I trust the authors** en el elemento emergente.

4. Espera mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: si se te pide que agregues los recursos necesarios para compilar y depurar, selecciona **Ahora no**.

## Configuración de la aplicación

Se han proporcionado aplicaciones para C# y Python, así como un archivo de texto de ejemplo que usará para probar el resumen. Las dos aplicaciones tienen la misma funcionalidad. En este ejercicio, completará algunas partes clave de la aplicación para habilitarla con el recurso de Azure OpenAI.

1. En Visual Studio Code, en el panel **Explorador**, vaya a la carpeta **Labfiles/04-code-generation** y expanda la carpeta **CSharp** o **Python** según sus preferencias de lenguaje. Cada carpeta contiene los archivos específicos del lenguaje de una aplicación en la que va a integrar la funcionalidad de OpenAI de Azure.
2. Haga clic con el botón derecho en la carpeta **CSharp** o **Python** que contiene los archivos de código y abra un terminal integrado. A continuación, instale el paquete del SDK de Azure OpenAI mediante la ejecución del comando adecuado para sus preferencias de lenguaje:

    **C#:**

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
    ```

    **Python**:

    ```powershell
    pip install openai==1.65.2
    ```

3. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de configuración para su lenguaje preferido.

    - **C#**: appsettings.json
    - **Python**: .env

4. Actualiza los valores de configuración para incluir:
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - El **nombre de implementación** que especificaste para la implementación del modelo (disponible en la página **Implementaciones** de Inteligencia artificial de Portal de Azure AI Foundry).
5. Guarde el archivo de configuración.

## Adición de código para usar el modelo de servicio de Azure OpenAI

Ahora está listo para usar el SDK de Azure OpenAI para consumir el modelo implementado.

1. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de código para su lenguaje preferido. En la función que llama al modelo de Azure OpenAI, en el comentario ***Dar formato y enviar la solicitud al modelo***, agregue el código para dar formato y enviar la solicitud al modelo.

    **C#** : Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };
    
    // Get response from Azure OpenAI
    ChatCompletion response = await chatClient.CompleteChatAsync(
        [
            new SystemChatMessage(systemPrompt),
            new UserChatMessage(userPrompt),
        ],
        chatCompletionsOptions);
    ```

    **Python**: code-generation.py

    ```python
    # Format and send the request to the model
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

1. Guarde los cambios en el archivo del código.

## Ejecución de la aplicación

Ahora que ha configurado la aplicación, ejecútela para intentar generar código para cada caso de uso. El caso de uso está numerado en la aplicación y se puede ejecutar en cualquier orden.

> **Nota**: Algunos usuarios pueden experimentar una limitación de volumen si se llama al modelo con demasiada frecuencia. Si se produce un error relacionado con el límite de volumen de tokens, espere un minuto y vuelva a intentarlo.

1. En el panel **Explorador**, expanda la carpeta **Labfiles/04-code-generation/sample-code** y revise la función y la aplicación *go-fish* para su lenguaje. Estos archivos se usarán para las tareas de la aplicación.
2. En el panel de terminal interactivo, asegúrese de que el contexto de la carpeta es la carpeta del lenguaje que prefiera. Después, escriba el siguiente comando para ejecutar la aplicación.

    - **C#**: `dotnet run`
    - **Python**: `python code-generation.py`

    > **Sugerencia**: Puede usar el icono **Maximizar el tamaño del panel** (**^**) en la barra de herramientas del terminal para ver más del texto de la consola.

3. Elija la opción **1** para agregar comentarios al código y escriba la siguiente solicitud. Tenga en cuenta que la respuesta puede tardar unos segundos.

    ```prompt
    Add comments to the following function. Return only the commented code.\n---\n
    ```

    Los resultados se colocarán en **result/app.txt**. Abra ese archivo y compárelo con el archivo de la función que está en **sample-code**.

4. Después, elija la opción **2** para escribir pruebas unitarias para esa misma función y escriba la siguiente indicación.

    ```prompt
    Write four unit tests for the following function.\n---\n
    ```

    Los resultados reemplazarán lo que había en **result/app.txt** e indican que hay cuatro pruebas unitarias para esa función.

5. Luego, elija la opción **3** para corregir errores en una aplicación para jugar a Go Fish. Escriba lo siguiente.

    ```prompt
    Fix the code below for an app to play Go Fish with the user. Return only the corrected code.\n---\n
    ```

    Los resultados reemplazarán lo que había en **result/app.txt** y deben tener un código muy similar con algunas cosas corregidas.

    - **C#** : se realizan correcciones en las líneas 30 y 59.
    - **Python**: se realizan correcciones en las líneas 18 y 31.

    La aplicación para jugar a Go Fish que está en **sample-code** se puede ejecutar, siempre que reemplace las líneas que contienen errores por la respuesta de Azure OpenAI. Si la ejecuta sin las correcciones, no funcionará correctamente.

    > **Nota**: Es importante tener en cuenta que, aunque se ha corregido una parte de la sintaxis en el código de esta aplicación Go Fish, no es una representación estrictamente precisa del juego. Si la observa detenidamente, verá que no se comprueba si quedan cartas en el mazo antes de robar, no desaparecen las parejas a los jugadores cuando las forman, así como algunos errores más que requieren conocer los juegos de cartas para darse cuenta. Este es un excelente ejemplo de lo útiles que pueden ser los modelos de inteligencia artificial generativa como ayuda para la generación de código, pero no se puede confiar en ellos completamente, el desarrollador debe realizar una última comprobación.

    Si quisiera ver la respuesta completa de Azure OpenAI, establezca la variable **printFullResponse** en `True` y vuelva a ejecutar la aplicación.

## Limpiar

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o todo el recurso en **Azure Portal**, en `https://portal.azure.com`.
