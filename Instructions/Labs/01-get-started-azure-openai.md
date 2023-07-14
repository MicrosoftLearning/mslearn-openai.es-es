---
lab:
  title: Introducción a Azure OpenAI
---

# Introducción a Azure OpenAI Service

Azure OpenAI Service proporciona los modelos de inteligencia artificial generativa desarrollados por OpenAI a la plataforma Azure, lo que permite desarrollar soluciones de inteligencia artificial muy eficaces que se benefician de la seguridad, la escalabilidad y la integración de servicios que proporciona la plataforma en la nube Azure. En este ejercicio, verá cómo empezar a trabajar con Azure OpenAI. Para ello, aprovisionará el servicio como un recurso de Azure y usará Azure OpenAI Studio para implementar y explorar modelos de OpenAI.

Este ejercicio dura aproximadamente **30** minutos.

## Antes de comenzar

Necesitará una suscripción de Azure que tenga acceso a Azure OpenAI Service.

- Para registrarse para obtener una suscripción gratuita de Azure, vaya a [https://azure.microsoft.com/free](https://azure.microsoft.com/free).
- Para solicitar acceso a Azure OpenAI Service, vaya a [https://aka.ms/oaiapply](https://aka.ms/oaiapply).

## Aprovisionamiento de un recurso de Azure OpenAI

Para poder usar modelos de Azure OpenAI, debe aprovisionar un recurso de Azure OpenAI en su suscripción de Azure.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: una suscripción de Azure que tenga acceso a Azure OpenAI Service.
    - **Grupo de recursos**: cree un grupo de recursos con el nombre que prefiera.
    - **Región**: elija cualquier región disponible.
    - **Nombre**: elija un nombre único.
    - **Plan de tarifa**: estándar S0
3. Espere a que la implementación finalice. A continuación, vaya al recurso de Azure OpenAI implementado en Azure Portal.

## Implementar un modelo

Azure OpenAI proporciona un portal basado en web denominado **Azure OpenAI Studio**, que puede usar para implementar, administrar y explorar modelos. Comenzará la exploración de Azure OpenAI usando Azure OpenAI Studio para implementar un modelo.

1. En la página **Información general** del recurso de Azure OpenAI, use el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del explorador.
2. En Azure OpenAI Studio, cree una nueva implementación con la siguiente configuración:
    - **Nombre del modelo**: text-davinci-003
    - **Nombre de implementación**: text-davinci

> **Nota**: Azure OpenAI incluye varios modelos, cada uno optimizado para un equilibrio de funcionalidad y rendimiento diferente. En este ejercicio, empezará con el modelo **Davinci** de la familia de modelos de generación de texto **GPT-3**. **text-davinci-003** es un buen modelo general para resumir y generar lenguaje natural. Si desea obtener más información sobre los modelos disponibles en Azure OpenAI, consulte [Modelos](https://learn.microsoft.com/azure/cognitive-services/openai/concepts/models) en la documentación de Azure OpenAI.

## Exploración de un modelo en el área de juegos Finalizaciones

Las *áreas de juegos* son interfaces muy útiles de Azure OpenAI Studio que puede usar para experimentar con los modelos implementados sin necesidad de desarrollar su propia aplicación cliente.

1. En Azure OpenAI Studio, en el panel izquierdo, en **Área de juegos**, seleccione **Finalizaciones**.
2. En la página **Finalizaciones**, asegúrese de que esté seleccionada la implementación **text-davinci** y, en la lista **Ejemplos**, seleccione **Summarize an article (abstractive)** .

    El ejemplo de texto resumido consta de un *mensaje* que proporciona un texto que comienza con la línea **Provide a summary of the text below...** . Al empezar el mensaje con esta frase, indica al modelo que resuma el siguiente bloque de texto.

3. Al final de la página, observe el número de *tokens* detectados en el texto. Los tokens son las unidades básicas de un mensaje, fundamentalmente palabras o partes de palabras del texto.
4. Use el botón **Generar** para enviar el mensaje al modelo y obtener una respuesta.

    La respuesta consta de un resumen del texto original. El resumen debe comunicar los puntos clave del texto original con un lenguaje menos detallado.

5. Use el botón **Regenerar** para volver a enviar el mensaje y vea cómo puede variar la respuesta respecto a la original. Un modelo de inteligencia artificial generativa puede producir un nuevo lenguaje cada vez que se le llama.
6. En la respuesta resumida, agregue una línea nueva y escriba el siguiente texto:

    *How has AI advanced?*

7. Use el botón **Generar** para enviar el nuevo mensaje y revisar la respuesta. El mensaje y la respuesta anteriores proporcionan contexto en un diálogo continuo con el modelo, lo que permite al modelo generar una respuesta adecuada a su pregunta.
8. Reemplace todo el contenido del mensaje por el siguiente texto:

    *Provide a summary of the text below that captures its main idea.* 
    
    *Azure OpenAI Service provides REST API access to OpenAI's powerful language models including the GPT-4, Codex and Embeddings model series. These models can be easily adapted to your specific task including but not limited to content generation, summarization, semantic search, and natural language to code translation. Users can access the service through REST APIs, Python SDK, or our web-based interface in the Azure OpenAI Studio.*

9. Use el botón **Generar** para enviar el nuevo mensaje y compruebe que el modelo resume el texto correctamente.

## Uso de un modelo para clasificar texto

Hasta ahora, ha visto cómo usar un modelo para resumir texto. Sin embargo, los modelos generativos de Azure OpenAI pueden ayudar a realizar una gran variedad de tareas. Veamos otro ejemplo, ahora de *clasificación de texto*.

1. En la página **Finalizaciones**, asegúrese de que esté seleccionada la implementación **text-davinci** y, en la lista **Ejemplos**, seleccione **Classify text**.

    El mensaje de ejemplo de clasificación de texto describe el contexto para el modelo en forma de instrucción para clasificar un artículo de noticias en una de una serie de categorías. Después, proporciona el texto del artículo de noticias (con el prefijo *News article:* ) y termina con *Classified category:* . La intención es que el modelo "complete" la última línea del mensaje con la predicción de la categoría adecuada.

2. Use el botón **Generar** para enviar el mensaje al modelo y obtener una respuesta. El modelo debe predecir una categoría adecuada para el artículo de noticias.
3. En la categoría predicha, agregue el siguiente texto:

    *News article: Microsoft releases Azure OpenAI service. Microsoft corporation has released an Azure service that makes OpenAI models available for application developers building apps and services in the Azure cloud.*

    *Classified category:*

4. Use el botón **Generar** para continuar el diálogo con el modelo y generar una categorización adecuada para el nuevo artículo de noticias.

## Exploración de mensajes y parámetros

Hasta ahora, ha basado los mensajes en los ejemplos que se proporcionan en Azure OpenAI Studio. Vamos a probar algo diferente.

1. Reemplace todo el texto del área del mensaje por el siguiente texto:

    *You are a teacher creating a test for your students.*

    *Write three multiple choice questions based on the following text.*

    *La mayoría de las soluciones de visión artificial se basan en modelos de Machine Learning que se pueden aplicar a la entrada visual de cámaras, videos o imágenes.*

    *\- Image classification involves training a machine learning model to classify images based on their contents. For example, in a traffic monitoring solution you might use an image classification model to classify images based on the type of vehicle they contain, such as taxis, buses, cyclists, and so on.*

    *\- Object detection machine learning models are trained to classify individual objects within an image, and identify their location with a bounding box. For example, a traffic monitoring solution might use object detection to identify the location of different classes of vehicle.*

    *\- Semantic segmentation is an advanced machine learning technique in which individual pixels in the image are classified according to the object to which they belong. For example, a traffic monitoring solution might overlay traffic images with "mask" layers to highlight different vehicles using specific colors.*

2. En el panel **Parámetros**, establezca los siguientes valores de parámetro:
    - **Temperatura**: 0
    - **Longitud máxima (tokens)** : 500
    - **Texto anterior a la respuesta**: Preguntas generadas automáticamente. Valide la configuración antes de usarla en una prueba:
3. Use el botón **Generar** para enviar el mensaje y revisar los resultados, que deben estar formados por el valor del parámetro *Texto anterior a la respuesta* seguido de preguntas de varias opciones que un profesor podría usar para poner a prueba a sus alumnos en los temas de visión artificial del mensaje. La respuesta entera debe ser menor que la longitud máxima que ha especificado como parámetro.

    Observe lo siguiente sobre el mensaje y los parámetros que ha usado:

    - El mensaje incluye información de contexto en lenguaje natural que indica al modelo cómo comportarse. En concreto, indica que el modelo debe asumir el rol de un profesor que crea una prueba para sus alumnos.
    - Los parámetros incluyen *Temperatura*, que controla hasta qué punto la generación de respuestas incluye un elemento de aleatoriedad. El valor **0** usado en este envío minimiza la aleatoriedad, lo que da lugar a respuestas estables y predecibles.

4. Use el botón **Regenerar** para volver a generar la respuesta. Debe ser similar a la respuesta anterior.
5. Cambie el valor del parámetro **Temperatura** a **0,9** y use el botón **Regenerar** para volver a generar la respuesta. Esta vez el mayor grado de aleatoriedad debería dar lugar a una respuesta diferente. Sin embargo, es más probable que estas preguntas contengan imprecisiones que las de la respuesta anterior.

## Exploración de la generación de código

El modelo **text-davinci** que ha implementado es un buen modelo general que puede controlar bien la mayoría de las tareas. Sin embargo, en algunos casos es mejor elegir un modelo optimizado para un tipo de tarea específico. Por ejemplo, los modelos de Azure OpenAI se pueden usar para generar código informático en lugar de texto en lenguaje natural, y algunos modelos se han optimizado para esa tarea.

1. En Azure OpenAI Studio, vea la página **Modelos**, que enumera todos los modelos disponibles en el recurso de Azure OpenAI Service.
2. Seleccione el modelo **code-davinci-002** y use el botón **Implementar modelo** para implementarlo con el nombre de implementación **code-davinci**.
3. Una vez completada la implementación, en Azure OpenAI Studio, vea la página **Implementaciones**, que enumera los modelos que ha implementado.
4. Seleccione la implementación de modelo **code-davinci** y use el botón **Abrir en el área de juegos** para abrirlo en el área de juegos.
5. En la página **Finalizaciones**, asegúrese de que está seleccionada la implementación **code-davinci** y, en la lista **Ejemplos**, seleccione **Lenguaje natural para SQL**.

    El mensaje del ejemplo de lenguaje natural a SQL proporciona detalles de las tablas de una base de datos y una descripción de la consulta necesaria seguida de la palabra clave `SELECT`. La intención es que el modelo complete la instrucción `SELECT` para crear una consulta que cumpla el requisito.

6. Use el botón **Generar** para enviar el mensaje al modelo y obtener una respuesta, que consta de una consulta `SELECT` de SQL.
7. Reemplace todo el mensaje y la respuesta por el siguiente mensaje:

    *# Python 3*

    *# Create a function to print "Hello " and a specified string*

    *def print_hello(s):*

8. Use el botón **Generar** para enviar el mensaje y ver el código que se genera. El mensaje incluía una indicación del lenguaje de programación que debe generarse (Python 3), un comentario que describe la funcionalidad deseada y la primera parte de la definición de la función. El modelo **code-davinci** debe haber completado la función con el código de Python adecuado.

## Exploración de modelos para chat

ChatGPT es un bot de chat desarrollado por OpenAI que puede proporcionar una amplia variedad de respuestas en lenguaje natural en un escenario conversacional. El modelo que usa ChatGPT y las API para usarlo están incluidos en Azure OpenAI.

1. En Azure OpenAI Studio, vea la página **Modelos**, que enumera todos los modelos disponibles en el recurso de Azure OpenAI Service.
2. Seleccione el modelo **gpt-35-turbo** y use el botón **Implementar modelo** para implementarlo con el nombre de implementación **gpt-chat**.
3. Una vez implementado el modelo, en la sección **Área de juegos**, seleccione la página **Chat** y asegúrese de que está seleccionado el modelo **gpt-chat** en el panel de la derecha.
4. En la sección **Configuración del asistente**, en el cuadro **Mensaje del sistema**, reemplace el texto actual por el siguiente:

    *The system is an AI teacher that helps people learn about AI*

5. Debajo del cuadro **Mensaje del sistema**, haga clic en **Agregar algunos ejemplos** y escriba el mensaje y la respuesta siguientes en los cuadros designados:

    Usuario: *What are different types of artificial intelligence?*

    Asistente: *There are three main types of artificial intelligence: Narrow or Weak AI (such as virtual assistants like Siri or Alexa, image recognition software, and spam filters), General or Strong AI (AI designed to be as intelligent as a human being. This type of AI does not currently exist and is purely theoretical), and Artificial Superintelligence (AI that is more intelligent than any human being and can perform tasks that are beyond human comprehension. This type of AI is also purely theoretical and has not yet been developed).*

    > **Nota**: Se usan algunos ejemplos para mostrarle al modelo los tipos de respuesta que se esperan. El modelo intentará reflejar el tono y el estilo de los ejemplos en sus propias respuestas.

6. Guarde los cambios para iniciar una nueva sesión y establecer el contexto conductual del sistema de chat.

7. En el cuadro de consulta al final de la página, escriba el siguiente texto:

    *What is artificial intelligence?*

8. Use el botón **Enviar** para enviar el mensaje y ver la respuesta.

    > **Nota**: Puede recibir una respuesta indicando que la implementación de la API aún no está lista. En ese caso, espere unos minutos y vuelva a intentarlo.

9. Revise la respuesta y envíe el siguiente mensaje para continuar la conversación:

    *How is it related to machine learning?*

10. Revise la respuesta y observe que se mantiene el contexto de la interacción anterior, por lo que el modelo entiende que "it" hace referencia a la inteligencia artificial.

En este ejercicio ha aprendido a aprovisionar Azure OpenAI Service en una suscripción de Azure y a usar Azure OpenAI Studio para implementar y explorar modelos.

Como desarrollador, puede usar la interfaz REST y las API específicas del lenguaje para crear aplicaciones y servicios que consuman modelos de Azure OpenAI, lo que le permite aprovechar modelos de inteligencia artificial generativa en sus propias aplicaciones. La programación con Azure OpenAI se trata en otros ejercicios.
