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
2. En Azure OpenAI Studio, cree una nueva implementación con la siguiente configuración:
    - **Nombre del modelo**: gpt-35-turbo
    - **Versión del modelo**: *usar la versión predeterminada*
    - **Nombre de implementación**: text-turbo

## Observación del comportamiento normal del chat sin agregar sus propios datos

Antes de conectar Azure OpenAI a los datos, observe primero cómo responde el modelo base a las consultas sin datos de base.

1. Vaya al área de juegos **Chat** y asegúrese de que el modelo `gpt-35-turbo` que implementó está seleccionado en el panel **Configuración** (debe ser el valor predeterminado, si solo tiene un modelo implementado).
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

## Limpieza

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar el recurso en [Azure Portal](https://portal.azure.com/?azure-portal=true). Asegúrese de incluir también la cuenta de almacenamiento y el recurso de búsqueda, ya que pueden suponer un costo relativamente grande.
