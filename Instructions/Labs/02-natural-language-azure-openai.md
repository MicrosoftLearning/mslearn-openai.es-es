---
lab:
  title: Integración de Azure OpenAI en la aplicación
---

# Integración de Azure OpenAI en la aplicación

Con Azure OpenAI Service, los desarrolladores pueden crear bots de chat, modelos de lenguaje y otras aplicaciones que destaquen en el reconocimiento del lenguaje humano natural. Azure OpenAI proporciona acceso a modelos de inteligencia artificial previamente entrenados, así como a un conjunto de API y herramientas para personalizar y ajustar esos modelos con el fin de satisfacer los requisitos específicos de una aplicación. En este ejercicio aprenderá a implementar un modelo en Azure OpenAI y a usarlo en su propia aplicación para resumir texto.

Este ejercicio dura aproximadamente **30** minutos.

## Antes de comenzar

Necesitará una suscripción de Azure que tenga acceso a Azure OpenAI Service.

- Para registrarse para obtener una suscripción gratuita de Azure, vaya a [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acceso a Azure OpenAI Service, vaya a [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Aprovisionamiento de un recurso de Azure OpenAI

Para poder usar modelos de Azure OpenAI, debe aprovisionar un recurso de Azure OpenAI en su suscripción de Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: una suscripción de Azure que tenga acceso a Azure OpenAI Service.
    - **Grupo de recursos**: cree un grupo de recursos con el nombre que prefiera.
    - **Región**: elija cualquier región disponible.
    - **Nombre**: elija un nombre único.
    - **Plan de tarifa**: estándar S0
3. Espere a que la implementación finalice. A continuación, vaya al recurso de Azure OpenAI implementado en Azure Portal.
4. Vaya a la página **Claves y punto de conexión** y guarde esos datos en un archivo de texto para usarlos más adelante.

## Implementar un modelo

Para usar la API de Azure OpenAI, primero debe implementar un modelo para usarlo con **Azure OpenAI Studio**. Una vez implementado, haremos referencia a ese modelo en la aplicación.

1. En la página **Información general** del recurso de Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador.
2. En Azure OpenAI Studio, cree una nueva implementación con la siguiente configuración:
    - **Modelo**: gpt-35-turbo
    - **Versión del modelo**: *use la versión predeterminada*
    - **Nombre de implementación**: text-turbo

> **Nota**: Cada modelo de Azure OpenAI está optimizado para un equilibrio de funcionalidad y rendimiento diferente. En este ejercicio usaremos la serie de modelos **3.5 Turbo** en la familia de modelos **GPT-3**, que tiene una alta capacidad de reconocimiento del lenguaje. En este ejercicio se usa solamente un único modelo, pero la implementación y el uso de otros modelos que implemente funcionarán de la misma manera.

## Configuración de una aplicación en Cloud Shell

Para mostrar cómo integrar un modelo de Azure OpenAI, usaremos una breve aplicación de la línea de comandos que se ejecuta en Cloud Shell en Azure. Abra una nueva pestaña del explorador para trabajar con Cloud Shell.

1. En [Azure Portal](https://portal.azure.com?azure-portal=true), seleccione el botón **[>_]** (*Cloud Shell*) en la parte superior de la página, a la derecha del cuadro de búsqueda. Se abrirá un panel de Cloud Shell en la parte inferior del portal.

    ![Captura de pantalla de cómo se inicia de Cloud Shell haciendo clic en el icono situado a la derecha del cuadro de búsqueda superior.](../media/cloudshell-launch-portal.png#lightbox)

2. La primera vez que abra Cloud Shell, es posible que se le pida que elija el tipo de shell que desea usar (*Bash* o *PowerShell*). Seleccione **Bash**. Si no ve esta opción, omita el paso.  

3. Si se le pide que cree almacenamiento para Cloud Shell, asegúrese de que se haya especificado la suscripción y seleccione **Crear almacenamiento**. A continuación, espere un minuto más o menos a que se cree el almacenamiento.

4. Asegúrese de que el tipo de shell indicado en la parte superior izquierda del panel de Cloud Shell se cambia a *Bash*. Si es *PowerShell*, cambie a *Bash* mediante el menú desplegable.

5. Cuando se inicie el terminal, escriba el siguiente comando para descargar la aplicación de ejemplo y guárdela en una carpeta denominada `azure-openai`.

    ```bash
   rm -r azure-openai -f
   git clone https://github.com/MicrosoftLearning/mslearn-openai azure-openai
    ```
  
6. Los archivos se descargan en una carpeta denominada **azure-openai**. Vaya a los archivos del laboratorio para este ejercicio con el siguiente comando.

    ```bash
   cd azure-openai/Labfiles/02-nlp-azure-openai
    ```

Se han proporcionado aplicaciones para C# y Python, así como un archivo de texto de ejemplo que usará para probar el resumen. Las dos aplicaciones tienen la misma funcionalidad.

Abra el editor de código integrado, donde verá el archivo de texto que va a resumir con el modelo en `text-files/sample-text.txt`. Use el siguiente comando para abrir los archivos del laboratorio en el editor de código.

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
   dotnet add package Azure.AI.OpenAI --prerelease
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

5. Abra el código de la aplicación para el lenguaje y agregue el código necesario para crear la solicitud, que especifica los parámetros del modelo, como `prompt` y `temperature`.

    **C#**

    ```csharp
   // Initialize the Azure OpenAI client
   OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));

   // Build completion options object
   ChatCompletionsOptions chatCompletionsOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
          new ChatMessage(ChatRole.System, "You are a helpful assistant. Summarize the following text in 60 words or less."),
          new ChatMessage(ChatRole.User, text),
       },
       MaxTokens = 120,
       Temperature = 0.7f,
   };

   // Send request to Azure OpenAI model
   ChatCompletions response = client.GetChatCompletions(
       deploymentOrModelName: oaiModelName, 
       chatCompletionsOptions);
   string completion = response.Choices[0].Message.Content;

   Console.WriteLine("Summary: " + completion + "\n");
    ```

    **Python**

    ```python
   # Set OpenAI configuration settings
   openai.api_type = "azure"
   openai.api_base = azure_oai_endpoint
   openai.api_version = "2023-03-15-preview"
   openai.api_key = azure_oai_key

   # Send request to Azure OpenAI model
   print("Sending request for summary to Azure OpenAI endpoint...\n\n")
   response = openai.ChatCompletion.create(
       engine=azure_oai_model,
       temperature=0.7,
       max_tokens=120,
       messages=[
          {"role": "system", "content": "You are a helpful assistant. Summarize the following text in 60 words or less."},
           {"role": "user", "content": text}
       ]
   )

   print("Summary: " + response.choices[0].message.content + "\n")
    ```

## Ejecución de la aplicación

Ahora que ha configurado la aplicación, ejecútela para enviarle la solicitud al modelo y ver la respuesta.

1. En el terminal de Bash en Cloud Shell, vaya a la carpeta del lenguaje que prefiera.
1. Ejecute la aplicación.

    - **C#** : `dotnet run`
    - **Python**: `python test-openai-model.py`

1. Observe el resumen del archivo de texto de ejemplo.
1. Vaya al archivo de código para el lenguaje que prefiera y cambie el valor de *temperatura* a `1`. Guarde el archivo.
1. Vuelva a ejecutar la aplicación y observe la salida.

Al aumentar la temperatura, el resumen suele variar, incluso si se proporciona el mismo texto, debido al aumento de la aleatoriedad. Puede ejecutarlo varias veces para ver cómo cambia la salida. Pruebe usando valores de temperatura diferentes con la misma entrada.

## Limpieza

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o el recurso entero en [Azure Portal](https://portal.azure.com?azure-portal=true).
