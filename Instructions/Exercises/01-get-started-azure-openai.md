---
lab:
  title: Introducción a Azure OpenAI Service
  status: stale
---

# Introducción a Azure OpenAI Service

Azure OpenAI Service proporciona los modelos de inteligencia artificial generativa desarrollados por OpenAI para la plataforma Azure, lo que permite desarrollar eficaces soluciones de inteligencia artificial que se benefician de seguridad, escalabilidad e integración de los servicios que proporciona la plataforma en la nube Azure. En este ejercicio, aprenderás cómo empezar a trabajar con Azure OpenAI. Para ello, aprovisionarás el servicio como un recurso de Azure y usarás Azure AI Foundry para implementar y explorar modelos de IA generativa.

En el escenario de este ejercicio, desempeñará el rol de un desarrollador de software que se ha encargado de implementar un agente de inteligencia artificial que puede usar inteligencia artificial generativa para ayudar a una organización de marketing a mejorar su eficacia a la hora de llegar a los clientes y anunciar nuevos productos. Las técnicas que se usan en el ejercicio se pueden aplicar a cualquier escenario en el que una organización quiera usar modelos de IA generativas para ayudar a los empleados a ser más eficaces y productivos.

Este ejercicio dura aproximadamente **30** minutos.

## Aprovisionamiento de un recurso de Azure OpenAI

Si aún no tienes uno, aprovisiona un recurso de Azure OpenAI en la suscripción a Azure.

1. Inicia sesión en **Azure Portal** en `https://portal.azure.com`.
2. Crea un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: *selecciona una suscripción a Azure aprobada para acceder al servicio Azure OpenAI*
    - **Grupo de recursos**: *elige o crea un grupo de recursos*
    - **Región**: *elige de forma **aleatoria** cualquiera de las siguientes regiones*\*
        - Este de EE. UU.
        - Este de EE. UU. 2
        - Centro-Norte de EE. UU
        - Centro-sur de EE. UU.
        - Centro de Suecia
        - Oeste de EE. UU.
        - Oeste de EE. UU. 3
    - **Nombre**: *nombre único que prefieras*
    - **Plan de tarifa**: estándar S0

    > \* Los recursos de Azure OpenAI están restringidos por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

3. Espera a que la implementación finalice. Luego, ve al recurso de Azure OpenAI implementado en Azure Portal.

## Implementar un modelo

Azure proporciona un portal basado en web denominado **Portal de Azure AI Foundry** que puedes usar para implementar, administrar y explorar modelos. Para iniciar la exploración de Azure OpenAI, usa Portal de Azure AI Foundry para implementar un modelo.

> **Nota**: a medida que usas Portal de Azure AI Foundry, es posible que se muestren cuadros de mensaje que sugieren tareas que se van a realizar. Puedes cerrarlos y seguir los pasos descritos en este ejercicio.

1. En Azure Portal, en la página **Información general** del recurso de Azure OpenAI, desplázate hacia abajo hasta la sección **Comenzar** y selecciona el botón para ir a **Portal de Azure AI Foundry** (anteriormente AI Studio).
1. En Portal de Azure AI Foundry, en el panel de la izquierda, selecciona la página **Implementaciones** y consulta las implementaciones de modelos existentes. Si aún no tienes una, crea una nueva implementación del modelo **gpt-4o** con la siguiente configuración:
    - **Nombre de implementación**: *nombre único que prefieras*
    - **Modelo**: gpt-4o
    - **Versión del modelo**: *usa la versión predeterminada*
    - **Tipo de implementación**: Estándar
    - **Límite de velocidad de tokens por minuto**: 5000\*
    - **Filtro de contenido**: valor predeterminado
    - **Habilitación de la cuota dinámica**: deshabilitada

    > \* Un límite de velocidad de 5000 tokens por minuto es más que adecuado para completar este ejercicio, al tiempo que dejas capacidad para otras personas que usan la misma suscripción.

## Uso del área de juegos de chat

Ahora que ha implementado un modelo, puede usarlo para generar respuestas basadas en mensajes de lenguaje natural. El área de juegos *Chat* en el Portal de la Fundición de IA de Azure proporciona una interfaz de bot de chat para los modelos GPT 4 y posteriores.

> **Nota:** el área de juegos *Chat* utiliza la API *ChatCompletions* en lugar de la antigua API *Completions* que utiliza el área de juegos *Completions*. El área de juegos Completions se proporciona para la compatibilidad con los modelos anteriores.

1. En la sección **Área de juego**, selecciona la página **Chat**. La página **Chat** del área de juegos consta de una fila de botones y dos paneles principales (que se pueden organizar horizontalmente de derecha a izquierda o verticalmente de arriba a abajo en función de la resolución de pantalla):
    - **Configuración**: se usa para seleccionar la implementación, definir el mensaje del sistema y establecer parámetros para interactuar con la implementación.
    - **Sesión de chat**: se usa para enviar mensajes de chat y ver respuestas.
1. En **Implementaciones**, asegúrate de que la implementación del modelo gpt-4o esté seleccionada.
1. Revisa el **Mensaje predeterminado del sistema**, que debería ser *Eres un asistente de IA que ayuda a las personas a encontrar información.* El mensaje del sistema se incluye en las indicaciones enviadas al modelo y proporciona contexto para las respuestas del modelo; establecer expectativas sobre cómo un agente de IA basado en el modelo debe interactuar con el usuario.
1. En el panel **Sesión de chat**, introduce la consulta del usuario `How can I use generative AI to help me market a new product?`

    > **Nota**: puedes recibir una respuesta en la que se indica que la implementación de la API aún no está lista. En ese caso, espera unos minutos y vuelve a intentarlo.

1. Revisa la respuesta, teniendo en cuenta que el modelo ha generado una respuesta de lenguaje natural cohesivo que es relevante para la consulta con la que se le solicita.
1. Escribe la consulta de usuario `What skills do I need if I want to develop a solution to accomplish this?`.
1. Revisa la respuesta, teniendo en cuenta que la sesión de chat ha conservado el contexto conversacional (por lo que "esto" se interpreta como una solución de IA generativa para el marketing). Esta contextualización se logra mediante la inclusión del historial de conversaciones reciente en cada envío sucesivo de indicación, por lo que la indicación enviada al modelo para la segunda consulta incluía la consulta original y la respuesta, así como la nueva entrada del usuario.
1. En la barra de herramientas del panel **Sesión de chat**, selecciona **Borrar chat** y confirma que deseas reiniciar la sesión de chat.
1. Escribe la consulta `Can you help me find resources to learn those skills?` y revisa la respuesta, que debe ser una respuesta de lenguaje natural válida, pero, dado que se ha perdido el historial de chat anterior, es probable que la respuesta se trate de buscar recursos genéricos de aptitudes en lugar de estar relacionados con las aptitudes específicas necesarias para crear una solución de marketing de IA generativa.

## Experimentar con mensajes del sistema, indicaciones y ejemplos de pocas capturas

Hasta ahora, has participado en una conversación de chat con el modelo en función del mensaje predeterminado del sistema. Puedes personalizar la configuración del sistema para tener más control sobre los tipos de respuestas generadas por el modelo.

1. En la barra de herramientas principal, selecciona los **ejemplos de solicitudes** y usa la plantilla de solicitudes **Asistente de escritura de marketing**.
1. Revisa el nuevo mensaje del sistema, que describe cómo un agente de IA debe usar el modelo para responder.
1. En el panel **Sesión de chat**, introduce la consulta del usuario `Create an advertisement for a new scrubbing brush`.
1. Revisa la respuesta, que debe incluir la copia de publicidad para un pincel de limpieza. La copia puede ser bastante extensa y creativa.

    En un escenario real, es probable que un profesional de marketing ya conozca el nombre del producto de pincel de limpieza, así como algunas ideas sobre las características clave que deben resaltarse en un anuncio. Para obtener los resultados más útiles de un modelo de IA generativa, los usuarios deben diseñar sus indicaciones para incluir la mayor cantidad de información pertinente posible.

1. Escribe el símbolo del sistema `Revise the advertisement for a scrubbing brush named "Scrubadub 2000", which is made of carbon fiber and reduces cleaning times by half compared to ordinary scrubbing brushes`.
1. Revisa la respuesta, que debe tener en cuenta la información adicional que proporcionaste sobre el producto de pincel de limpieza.

    La respuesta debería ser ahora más útil, pero para tener aún más control sobre la salida del modelo, puedes proporcionar uno o varios ejemplos *cortos* en los que se basen las respuestas.

1. En el cuadro de texto **Mensaje del sistema**, expande el elemento desplegable de **Agregar sección** y selecciona **Ejemplos**. A continuación, escribe el mensaje y la respuesta siguientes en los cuadros designados:

    **Usuario:**

    ```prompt
    Write an advertisement for the lightweight "Ultramop" mop, which uses patented absorbent materials to clean floors.
    ```

    **Asistente:**

    ```prompt
    Welcome to the future of cleaning!
    
    The Ultramop makes light work of even the dirtiest of floors. Thanks to its patented absorbent materials, it ensures a brilliant shine. Just look at these features:
    - Lightweight construction, making it easy to use.
    - High absorbency, enabling you to apply lots of clean soapy water to the floor.
    - Great low price.
    
    Check out this and other products on our website at www.contoso.com.
    ```

1. Usa el botón **Aplicar cambios** para guardar los ejemplos e iniciar una nueva sesión.
1. En la sección **Sesión de chat**, introduce la consulta del usuario `Create an advertisement for the Scrubadub 2000 - a new scrubbing brush made of carbon fiber that reduces cleaning time by half`.
1. Revisa la respuesta, que debe ser un anuncio nuevo para el "Scrubadub 2000" que se modela en el ejemplo "Ultramop" proporcionado en la configuración del sistema.

## Experimento con parámetros

Has explorado cómo el mensaje del sistema, los ejemplos y las indicaciones pueden ayudar a refinar las respuestas devueltas por el modelo. También puedes usar parámetros para controlar el comportamiento del modelo.

1. En el panel **Configuración**, selecciona la pestaña **Parámetros** y ajusta los siguientes valores de los parámetros:
    - **Respuesta máx.**: 1000
    - **Temperatura**: 1

1. En la sección **Sesión de chat**, usa el botón **Borrar chat** para restablecer la sesión de chat. A continuación, introduce la consulta del usuario `Create an advertisement for a cleaning sponge` y revisa la respuesta. La copia del anuncio resultante debe incluir un máximo de 1000 tokens de texto e incluir algunos elementos creativos; por ejemplo, el modelo puede haber inventado un nombre de producto para la esponja y hacer algunas afirmaciones sobre sus características.
1. Usa el botón **Borrar chat** para restablecer la sesión de chat de nuevo y, a continuación, vuelve a escribir la misma consulta que antes (`Create an advertisement for a cleaning sponge`) y revisa la respuesta. La respuesta puede ser diferente de la respuesta anterior.
1. En el panel **Configuración**, en la pestaña **Parámetros**, cambie el valor del parámetro **Temperatura** a 0.
1. En la sección **Sesión de chat**, use el botón **Borrar chat** para restablecer de nuevo la sesión de chat y, a continuación, vuelva a introducir la misma consulta que antes (`Create an advertisement for a cleaning sponge`) y revise la respuesta. Esta vez, es posible que la respuesta no sea tan creativa.
1. Use el botón **Borrar chat** para restablecer la sesión de chat una vez más y, a continuación, vuelva a escribir la misma consulta que antes (`Create an advertisement for a cleaning sponge`) y revise la respuesta; que debe ser muy similar (si no es idéntica) a la respuesta anterior.

    El parámetro **Temperature** controla el grado en que el modelo puede ser creativo en su generación de una respuesta. Un valor bajo da como resultado una respuesta coherente con poca variación aleatoria, mientras que un valor alto anima al modelo a agregar elementos creativos a su salida; que puede afectar a la precisión y al realismo de la respuesta.

## Implementación del modelo en una aplicación web

Ahora que has explorado algunas de las funcionalidades de un modelo de IA generativa en el área de juegos de Azure AI Foundry, puedes implementar una aplicación web de Azure para proporcionar una interfaz básica del agente de IA a través de la cual los usuarios podrán chatear con el modelo.

> **Nota**: para algunos usuarios, no es posible la implementación en la aplicación web debido a un error en la plantilla de Studio. En ese caso, omite esta sección.

1. En la parte superior derecha de la página del área de juegos de **Chat**, en el menú **Implementar en**, seleccione **Una nueva aplicación web**.
1. En el cuadro de diálogo **Implementar en una aplicación web**, cree una nueva aplicación web con la siguiente configuración:
    - **Nombre**: *un nombre único*.
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *El grupo de recursos en el que aprovisionó el recurso de Azure OpenAI*
    - **Ubicaciones**: *La región en la que aprovisionó el recurso de Azure OpenAI*
    - **Plan de precios**: Gratis (F1): *Si no está disponible, seleccione Básico (B1)*
    - **Habilitar el historial de chats en la aplicación web**: **No** seleccionada
    - **Confirmo que las aplicaciones web incurrirán en el uso de mi cuenta**: seleccionado.
1. Implemente la nueva aplicación web y espere a que se complete la implementación (lo que puede tardar 10 minutos o así)
1. Una vez que su aplicación web se haya implementado correctamente, use el botón situado en la parte superior derecha de la página del área de juegos **Chat** para iniciar la aplicación web. La aplicación puede tardar unos minutos en iniciarse. Si se le pide, acepte la solicitud de permisos.
1. En la aplicación web, escriba el siguiente mensaje de chat:

    ```prompt
    Write an advertisement for the new "WonderWipe" cloth that attracts dust particulates and can be used to clean any household surface.
    ```

1. Revise la respuesta.

    > **Nota**: Ha implementado el *modelo* en una aplicación web, pero esta implementación no incluye la configuración del sistema ni los parámetros que estableció en el área de juegos, por lo que es posible que la respuesta no refleje los ejemplos que especificó en el área de juegos. En un escenario real, agregaría lógica a la aplicación para modificar el símbolo del sistema para que incluya los datos contextuales adecuados para los tipos de respuesta que desea generar. Este tipo de personalización está fuera del ámbito de este ejercicio introductorio, pero puede obtener información sobre las técnicas de ingeniería rápida y las API de Azure OpenAI en otros ejercicios y documentación del producto.

1. Cuando hayas terminado de experimentar con el modelo en la aplicación web, cierra la pestaña de la aplicación web del explorador para volver al Portal de la Fundición de IA de Azure.

## Limpieza

Cuando hayas terminado de usar el recurso de Azure OpenAI, recuerda eliminar la implementación o todo el recurso en **Azure Portal**, en `https://portal.azure.com`.
