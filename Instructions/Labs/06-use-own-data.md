---
lab:
  title: Uso de sus propios datos con Azure OpenAI
---

# Uso de sus propios datos con Azure OpenAI

Azure OpenAI Service le permite usar sus propios datos con la inteligencia de LLM subyacentes. Puede limitar el modelo para que use solamente los datos para temas pertinentes o combinarlos con los resultados del modelo entrenado previamente.

Este ejercicio dura aproximadamente **20** minutos.

## Antes de comenzar

Necesitará una suscripción de Azure que tenga acceso a Azure OpenAI Service. 

- Para registrarse para obtener una suscripción gratuita de Azure, vaya a [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acceso a Azure OpenAI Service, vaya a [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Aprovisionamiento de un recurso de Azure OpenAI

Para poder usar modelos de Azure OpenAI, debe aprovisionar un recurso de Azure OpenAI en su suscripción de Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com?azure-portal=true).
2. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: una suscripción de Azure que tenga acceso a Azure OpenAI Service.
    - **Grupo de recursos**: elija un grupo de recursos existente o cree uno nuevo con un nombre de su elección.
    - **Región**: elija cualquier región disponible.
    - **Nombre**: elija un nombre único.
    - **Plan de tarifa**: estándar S0
3. Espere a que la implementación finalice. Luego, vaya al recurso de Azure OpenAI implementado en Azure Portal.

## Implementar un modelo

Para chatear con Azure OpenAI, primero debe implementar un modelo para usarlo con **Azure OpenAI Studio**. Una vez implementado, usaremos el modelo con el área de juegos y usaremos nuestros datos para encaminar sus respuestas.

1. En la página **Información general** del recurso de Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador. También puede ir directamente a [Azure OpenAI Studio](https://oai.azure.com/?azure-portal=true).
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

## Observación del comportamiento normal del chat sin agregar sus propios datos

Antes de conectar Azure OpenAI a los datos, observe primero cómo responde el modelo base a las consultas sin datos de base.

1. Ve al área de juegos **Chat** y asegúrate de que el modelo que has implementado está seleccionado en el panel **Configuración** (debe ser el valor predeterminado si solo tienes un modelo implementado).
1. Escriba las siguientes preguntas y observe la salida.

    ```code
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```code
    What are some facts about New York?
    ```

1. Pruebe preguntas similares sobre el turismo y los lugares para alojarse en otras ubicaciones que se incluirán en nuestros datos de base, como Londres o San Francisco. Es probable que obtenga respuestas completas sobre áreas o vecindarios y algunos hechos generales sobre la ciudad.

## Conexión de los datos en el área de juegos de chat

A continuación, agregue los datos en el área de juegos de chat para ver cómo responde con los datos como base.

1. [Descargue los datos](https://aka.ms/own-data-brochures) que usará desde GitHub. Extraiga los archivos PDF del elemento `.zip` proporcionado.
1. Vaya al área de juegos **Chat** y seleccione *Agregar los datos* en el panel Configuración del asistente.
1. Seleccione **Agregar un origen de datos** y elija *Cargar archivos* en la lista desplegable.
1. Deberá crear una cuenta de almacenamiento y un recurso de Azure Cognitive Search. En la lista desplegable del recurso de almacenamiento, seleccione **Crear un nuevo recurso de Azure Blob Storage** y cree una cuenta de almacenamiento con la siguiente configuración. Todo lo que no se especifique se deja con el valor predeterminado.

    - **Suscripción**: *la misma suscripción que el recurso de Azure OpenAI*
    - **Grupo de recursos**: *el mismo grupo de recursos que el recurso de Azure Cognitive Search*
    - **Nombre de la cuenta de almacenamiento**: *escriba un nombre globalmente único*
    - **Región**: *misma región que el recurso de Azure OpenAI*
    - **Redundancia**: almacenamiento con redundancia local (LRS)

1. Una vez creado el recurso, vuelva a Azure OpenAI Studio y seleccione **Crear un nuevo recurso de Azure Cognitive Search** con la siguiente configuración. Todo lo que no se especifique se deja con el valor predeterminado.

    - **Suscripción**: *la misma suscripción que el recurso de Azure OpenAI*
    - **Grupo de recursos**: *el mismo grupo de recursos que el recurso de Azure Cognitive Search*
    - **Nombre del servicio**: *escriba el nombre globalmente único*
    - **Ubicación**: *misma ubicación que el recurso de Azure OpenAI*
    - **Plan de tarifa**: básico

1. Espere hasta que se haya implementado el recurso de búsqueda y, a continuación, vuelva a Azure AI Studio y actualice la página.
1. En **Agregar datos**, escriba los valores siguientes para el origen de datos y seleccione **Siguiente**.

    - **Seleccionar origen de datos**: cargar archivos
    - **Seleccionar recurso de Azure Blob Storage**: *elija el recurso de almacenamiento que ha creado*
        - Activación de CORS cuando se le solicite
    - **Seleccionar el recurso de Azure Cognitive Search**: *elija el recurso de búsqueda que creó*
    - **Escribir el nombre del índice**: margiestravel
    - **Agregar búsqueda vectorial a este recurso de búsqueda**: no comprobado
    - **Reconozco que la conexión a una cuenta de Azure Cognitive Search conllevará el uso de mi cuenta**: comprobado

1. En la página **Cargar archivos**, cargue los archivos PDF que descargó y, a continuación, seleccione **Siguiente**.
1. En la página **Administración de datos**, seleccione el tipo de búsqueda **Palabra clave** en la lista desplegable y, a continuación, seleccione **Siguiente**.
1. En la página **Revisar y finalizar**, seleccione **Guardar y cerrar**, lo que agregará los datos. Esta operación puede tardar unos minutos, durante los cuales debe dejar abierta la ventana. Una vez completado, verá el origen de datos, el recurso de búsqueda y el índice especificados en el panel **Configuración del asistente**.

## Chat con un modelo basado en los datos

Ahora que ha agregado los datos, realice las mismas preguntas que antes y vea cómo difiere la respuesta.

```
I'd like to take a trip to New York. Where should I stay?
```

```
What are some facts about New York?
```

Esta vez, observará una respuesta muy diferente, con detalles sobre determinados hoteles y una mención de Margie's Travel, así como referencias a donde procede la información proporcionada. Si abre la referencia en PDF que aparece en la respuesta, verá los mismos hoteles que el modelo proporcionó.

Pruebe a preguntar sobre otras ciudades incluidas en los datos de base, que son Dubái, Las Vegas, Londres y San Francisco.

> **Nota**: **Agregar los datos** todavía está en versión preliminar y es posible que no siempre se comporte según lo previsto para esta característica, como proporcionar la referencia incorrecta para una ciudad no incluida en los datos de base.

## Vamos a conectar la aplicación a tus propios datos

A continuación, explora cómo conectar la aplicación para usar tus propios datos.

### Configuración de una aplicación en Cloud Shell

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
    cd azure-openai/Labfiles/06-use-own-data
    ```

7. Abre el editor de código integrado ejecutando el comando siguiente:

    ```bash
    code .
    ```

    > **Sugerencia**: consulta la [documentación del editor de código de Azure Cloud Shell](https://learn.microsoft.com/azure/cloud-shell/using-cloud-shell-editor) para más información sobre cómo usarla para trabajar con archivos en el entorno de Azure Cloud Shell.

## Configuración de la aplicación

En este ejercicio, completará algunas partes clave de la aplicación para habilitarla con el recurso de Azure OpenAI. Se han proporcionado aplicaciones para C# y Python. Las dos aplicaciones tienen la misma funcionalidad.

1. En el editor de código, expanda la carpeta **CSharp** o **Python**, según el lenguaje que prefiera.

2. Abra el archivo de configuración para el lenguaje.

    - C#: `appsettings.json`
    - Python: `.env`
    
3. Actualiza los valores de configuración para incluir:
    - El **punto de conexión** y una **clave** del recurso de Azure OpenAI que has creado (disponible en la página **Claves y punto de conexión** del recurso de Azure OpenAI en Azure Portal)
    - Nombre que has especificado para la implementación del modelo (disponible en la página **Implementaciones** de Azure OpenAI Studio).
    - Punto de conexión del servicio de búsqueda (el valor de dirección **URL** de la página de información general del recurso de búsqueda en Azure Portal).
    - Una **clave** para el recurso de búsqueda (disponible en la página **Claves** del recurso de búsqueda en Azure Portal: puedes usar cualquiera de las claves de administración).
    - Nombre del índice de búsqueda (que debe ser `margiestravel`).
    
4. Guarde el archivo de configuración actualizado.

5. En el panel de consola, escribe los siguientes comandos para ir a la carpeta del idioma preferido e instalar los paquetes necesarios.

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

6. En el editor de código, ve a la carpeta del idioma que prefieras, selecciona el archivo de código y añade las bibliotecas necesarias.

    **C#**: OwnData.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**: ownData.py

    ```python
    # Add OpenAI import
    from openai import AzureOpenAI
    ```

7. Revisa el archivo de código, específicamente donde se utilizan los valores de búsqueda al completar los parámetros de la llamada API.

## Ejecución de la aplicación

Ahora que ha configurado la aplicación, ejecútela para enviarle la solicitud al modelo y ver la respuesta. Observarás que la respuesta se basa ahora en los datos de forma similar a la experiencia de Studio.

1. En el terminal de Bash en Cloud Shell, vaya a la carpeta del lenguaje que prefiera.
1. Expande el terminal para que ocupe la mayor parte de la ventana del explorador y ejecuta la aplicación.

    - **C#** : `dotnet run`
    - **Python**: `python ownData.py`

1. Envía el mensaje `Tell me about London`, tras lo cual, deberías ver la respuesta que hace referencia a los datos.

## Limpieza

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar el recurso en [Azure Portal](https://portal.azure.com/?azure-portal=true). Asegúrese de incluir también la cuenta de almacenamiento y el recurso de búsqueda, ya que pueden suponer un costo relativamente grande.
