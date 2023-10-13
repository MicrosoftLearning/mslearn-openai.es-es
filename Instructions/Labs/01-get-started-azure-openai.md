---
lab:
  title: Introducción a Azure OpenAI
---

# Introducción a Azure OpenAI Service

Azure OpenAI Service proporciona los modelos de inteligencia artificial generativa desarrollados por OpenAI para la plataforma Azure, lo que permite desarrollar eficaces soluciones de inteligencia artificial que se benefician de seguridad, escalabilidad e integración de los servicios que proporciona la plataforma en la nube Azure. En este ejercicio, verá cómo empezar a trabajar con Azure OpenAI. Para ello, aprovisionará el servicio como un recurso de Azure y usará Azure OpenAI Studio para implementar y explorar modelos de OpenAI.

Este ejercicio dura aproximadamente **30** minutos.

## Antes de comenzar

Necesitará una suscripción de Azure que pueda acceder a Azure OpenAI Service.

- Para registrarse para obtener una suscripción gratuita de Azure, vaya a [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acceso a Azure OpenAI Service, vaya a [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Aprovisionamiento de un recurso de Azure OpenAI

Para poder usar modelos de Azure OpenAI, es preciso aprovisionar un recurso de Azure OpenAI en una suscripción de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: una suscripción de Azure que tenga acceso a Azure OpenAI Service.
    - **Grupo de recursos**: cree un grupo de recursos con el nombre que prefiera.
    - **Región**: elija cualquier región disponible.
    - **Nombre**: el nombre único que elija.
    - **Plan de tarifa**: estándar S0
3. Espere a que la implementación finalice. A continuación, vaya al recurso de Azure OpenAI implementado en Azure Portal.

## Implementar un modelo

Azure OpenAI proporciona un portal basado en web denominado **Azure OpenAI Studio** que se puede usar para implementar, administrar y explorar modelos. Para iniciar la exploración de Azure OpenAI, use Azure OpenAI Studio para implementar un modelo.

1. En la página **Información general** del recurso Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador.
2. En Azure OpenAI Studio, cree una nueva implementación con la siguiente configuración:
    - **Modelo**: gpt-35-turbo
    - **Versión de Modev**: actualización automática al valor predeterminado.
    - **Nombre de implementación**: my-gpt-model

> **Nota**: Azure OpenAI incluye varios modelos, cada uno de ellos optimizado para lograr un equilibrio de funcionalidades y rendimiento diferente. En este ejercicio, usará el modelo **GPT-35-Turbo**, que es un buen modelo general para resumir y generar código y lenguaje natural. Para más información sobre los modelos disponibles en Azure OpenAI, consulte [Modelos](https://learn.microsoft.com/azure/cognitive-services/openai/concepts/models) en la documentación de Azure OpenAI.

## Exploración de un modelo en el área de juegos Finalizaciones

Las *áreas de juegos* son interfaces de Azure OpenAI Studio muy útiles que puede usar para experimentar con los modelos implementados sin necesidad de desarrollar su propia aplicación cliente.

1. En Azure OpenAI Studio, en el panel izquierdo, en **Área de juegos**, seleccione **Finalizaciones**.
2. En la página **Finalizaciones**, asegúrese de que esté seleccionada la implementación **my-gpt-model** y, en la lista **Ejemplos**, seleccione **Generar un cuestionario**.

    El ejemplo de texto resumido consta de un *mensaje* que proporciona texto para indicar al modelo qué tipo de respuesta es necesaria e incluir cierta información contextual.

3. Al final de la página, observe el número de *tokens* detectados en el texto. Los tokens son las unidades básicas de un mensaje, fundamentalmente palabras o partes de palabras del texto.
4. Use el botón **Generar** para enviar el mensaje al modelo y obtener una respuesta.

    La respuesta consta de un cuestionario basado en el ejemplo del mensaje.

5. Use el botón **Regenerar** para volver a enviar el mensaje y observe que la respuesta puede no ser idéntica a la original. Un modelo de inteligencia artificial generativa puede generar un nuevo lenguaje cada vez que se le llama.
6. Use el botón **Ver código** para ver el código que usaría una aplicación cliente para enviar el mensaje. Puede seleccionar el lenguaje de programación que prefiera. El mensaje contiene el texto que envió al modelo. La solicitud se envía a la API *Completions* de Azure OpenAI Service.

## Uso del área de juegos de chat

El área de juegos de *chat* proporciona una interfaz de bot de chat para los modelos GPT 3.5 y superiores. Usa la API *ChatCompletions*, en lugar de la antigua API *Completions*.

1. En la sección **Área de juegos**, seleccione la página **Chat** y asegúrese de que el modelo **my-gpt-model** está seleccionado en el panel de configuración de la derecha.
2. En la sección **Configuración del asistente**, en el cuadro **Mensaje del sistema**, reemplace el texto actual por la siguiente instrucción: `The system is an AI teacher that helps people learn about AI`.

3. Debajo del cuadro **Mensaje del sistema**, haga clic en **Agregar algunos ejemplos** y escriba el mensaje y la respuesta siguientes en los cuadros designados:

    - **Usuario**: `What are different types of artificial intelligence?`
    - **Asistente**: `There are three main types of artificial intelligence: Narrow or Weak AI (such as virtual assistants like Siri or Alexa, image recognition software, and spam filters), General or Strong AI (AI designed to be as intelligent as a human being. This type of AI does not currently exist and is purely theoretical), and Artificial Superintelligence (AI that is more intelligent than any human being and can perform tasks that are beyond human comprehension. This type of AI is also purely theoretical and has not yet been developed).`

    > **Nota**: Se usan algunos ejemplos para proporcionar al modelo ejemplos de los tipos de respuesta que se esperan. El modelo intentará reflejar el tono y estilo de los ejemplos en sus propias respuestas.

4. Guarde los cambios para iniciar una nueva sesión y establecer el contexto conductual del sistema de chat.
5. En el cuadro de consulta de la parte inferior de la página, escriba el texto `What is artificial intelligence?`
6. Pulse el botón **Enviar** para enviar el mensaje y ver la respuesta.

    > **Nota**: Puede recibir una respuesta en la que se indica que la implementación de la API aún no está lista. En ese caso, espere unos minutos y vuelva a intentarlo.

7. Examine la respuesta y envíe el siguiente mensaje para continuar la conversación: `How is it related to machine learning?`
8. Examine la respuesta y observe que se mantiene el contexto de la interacción anterior (por lo que el modelo entiende que "it" hace referencia a la inteligencia artificial).
9. Use el botón **Ver código** para ver el código de la interacción. El mensaje consta del mensaje del *sistema*, los ejemplos de mensajes de *usuario* y *asistente*, y la secuencia de *mensajes de usuario* y *asistente* en la sesión de chat hasta ese momento.

## Exploración de mensajes y parámetros

Puede usar el mensaje y los parámetros para maximizar la probabilidad de generar la respuesta que necesita.

1. En el panel **Parámetros**, establezca los siguientes valores de los parámetros:
    - **Temperatura**: 0
    - **Longitud máxima (tokens)** : 500

2. Envíe el siguiente mensaje

    ```
    Write three multiple choice questions based on the following text.

    Most computer vision solutions are based on machine learning models that can be applied to visual input from cameras, videos, or images.*

    - Image classification involves training a machine learning model to classify images based on their contents. For example, in a traffic monitoring solution you might use an image classification model to classify images based on the type of vehicle they contain, such as taxis, buses, cyclists, and so on.*

    - Object detection machine learning models are trained to classify individual objects within an image, and identify their location with a bounding box. For example, a traffic monitoring solution might use object detection to identify the location of different classes of vehicle.*

    - Semantic segmentation is an advanced machine learning technique in which individual pixels in the image are classified according to the object to which they belong. For example, a traffic monitoring solution might overlay traffic images with "mask" layers to highlight different vehicles using specific colors.
    ```

3. Examine los resultados, que deben constar de preguntas de opción múltiple que un profesor podría usar para probar a los alumnos en los temas de Computer Vision en el mensaje. La respuesta entera debe ser menor que la longitud máxima que ha especificado como parámetro.

    Observe lo siguiente sobre el mensaje y los parámetros que ha usado:

    - El mensaje indica específicamente que la salida deseada debe ser tres preguntas de opción múltiple.
    - Los parámetros incluyen *Temperatura*, que controla hasta qué punto la generación de respuestas incluye un elemento de aleatoriedad. El valor **0** usado en este envío minimiza la aleatoriedad, lo que da lugar a respuestas estables y predecibles.

## Exploración de la generación de código

Además de generar respuestas de lenguaje natural, los modelos GPT se pueden usar para generar código.

1. En el panel **Configuración del asistente**, seleccione la plantilla **Ejemplo vacío** para restablecer el mensaje del sistema.
2. Escriba el mensaje del sistema: `You are a Python developer.` y guarde los cambios.
3. En el panel **Sesión de chat**, seleccione **Borrar chat** para borrar el historial del chat e iniciar una nueva sesión.
4. Envíe el siguiente mensaje de usuario:

    ```
    Write a Python function named Multiply that multiplies two numeric parameters.
    ```

5. Examine la respuesta, que debe incluir código de Python de ejemplo que cumpla el requisito en el mensaje.

## Limpieza

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o el recurso entero en [Azure Portal](https://portal.azure.com).
