---
lab:
  title: Generación de imágenes con el modelo DALL-E
---

# Generación de imágenes con el modelo DALL-E

Azure OpenAI Service incluye un modelo de generación de imágenes denominado DALL-E. Puede usar este modelo para enviar mensajes en lenguaje natural que describan una imagen deseada y el modelo generará una imagen original basada en la descripción que proporcione.

En este ejercicio, usará un modelo DALL-E versión 3 para generar imágenes basadas en avisos de lenguaje natural.

Este ejercicio dura aproximadamente **25** minutos.

## Aprovisionamiento de un recurso de Azure OpenAI

Para poder usar Azure OpenAI para generar imágenes, debe aprovisionar un recurso de Azure OpenAI en su suscripción de Azure. El recurso debe estar en una región donde se admiten los modelos DALL-E.

1. Inicie sesión en **Azure Portal** en `https://portal.azure.com`.
2. Cree un recurso de **Azure OpenAI** con la siguiente configuración:
    - **Suscripción**: *Seleccione una suscripción de Azure que se haya aprobado para acceder al servicio Azure OpenAI, incluido DALL-E*
    - **Grupo de recursos**: *elija o cree un grupo de recursos*
    - **Región**: ***Este de EE. UU.** o **Centro de Suecia***\*
    - **Nombre**: *nombre único que prefiera*
    - **Plan de tarifa**: estándar S0

    > Los modelos DALL-E 3 \* solo están disponibles en los recursos del servicio OpenAI de Azure en las regiones de **Este de EE. UU.** y **Centro de Suecia**.

3. Espere a que la implementación finalice. A continuación, vaya al recurso de Azure OpenAI implementado en Azure Portal.

## Exploración de la generación de imágenes en el área de juegos de DALL-E

Puede usar el área de juegos de DALL-E en **Azure OpenAI Studio** para experimentar con la generación de imágenes.

1. En el Azure Portal, en la página **información general** de su recurso Azure OpenAI, utilice el botón **Explorar** para abrir Azure OpenAI Studio en una nueva pestaña del navegador. Como alternativa, vaya a [Azure OpenAI Studio](https://oai.azure.com) directamente en `https://oai.azure.com`.
2. En la sección **Área de juegos**, seleccione el área de juegos **DALL-E**. Se creará automáticamente una implementación del modelo DALL-E denominado *Dalle3*.
3. En el cuadro **Mensaje**, escriba una descripción de una imagen que desee generar. Por ejemplo, `An elephant on a skateboard` A continuación, seleccione **Generar** y vea la imagen que se genera.

    ![Área de juegos de DALL-E en Azure OpenAI Studio con una imagen generada.](../media/dall-e-playground.png)

4. Modifique el mensaje para proporcionar una descripción más específica. Por ejemplo, `An elephant on a skateboard in the style of Picasso`. A continuación, genere la nueva imagen y revise los resultados.

    ![Área de juegos de DALL-E en Azure OpenAI Studio con dos imágenes generadas.](../media/dall-e-playground-new-image.png)

## Uso de la API REST para generar imágenes

Azure OpenAI Service proporciona una API REST que puede usar para enviar mensajes para la generación de contenido, incluidas imágenes generadas por el modelo DALL-E.

### Preparación para desarrollar una aplicación en Visual Studio Code

Ahora vamos a explorar cómo crear una aplicación personalizada que use el servicio Azure OpenAI para generar imágenes. Desarrollará la aplicación con Visual Studio Code. Los archivos de código de la aplicación se han proporcionado en un repositorio de GitHub.

> **Sugerencia**: Si ya ha clonado el repositorio de **mslearn-openai**, ábralo en Visual Studio Code. De lo contrario, siga estos pasos para clonarlo en el entorno de desarrollo.

1. Inicie Visual Studio Code.
2. Abra la paleta (Mayús + Ctrl + P) y ejecute un comando **Git: Clone** para clonar el repositorio `https://github.com/MicrosoftLearning/mslearn-openai` en una carpeta local (no importa qué carpeta).
3. Cuando se haya clonado el repositorio, abra la carpeta en Visual Studio Code.

    > **Nota**: Si Visual Studio Code muestra un mensaje emergente para solicitarle que confíe en el código que está abriendo, haga clic en **Sí, confío en la opción autores** en el elemento emergente.

4. Espere mientras se instalan archivos adicionales para admitir los proyectos de código de C# en el repositorio.

    > **Nota**: Si se le pide que agregue los recursos necesarios para compilar y depurar, seleccione **Ahora no**.

### Configuración de la aplicación

Se han proporcionado aplicaciones para C# y Python. Las dos aplicaciones tienen la misma funcionalidad. En primer lugar, agregará el punto de conexión y la clave del recurso de Azure OpenAI al archivo de configuración de la aplicación.

1. En Visual Studio Code, en el panel **Explorador**, vaya a la carpeta **Labfiles/05-image-generation** y expanda la carpeta **C-Sharp** o **Python** según sus preferencias de lenguaje. Cada carpeta contiene los archivos específicos del lenguaje de una aplicación en la que va a integrar la funcionalidad de OpenAI de Azure.
2. En el panel **Explorador**, en la carpeta **CSharp** o **Python**, abra el archivo de configuración para su lenguaje preferido.

    - **C#**: appsettings.json
    - **Python**: .env
    
3. Actualice los valores de configuración para incluir el **punto de conexión** y la **clave** del recurso Azure OpenAI que ha creado (disponible en la página **claves y punto de conexión** de su recurso Azure OpenAI en el Azure Portal).
4. Guarde el archivo de configuración.

### Visualización del código de la aplicación

Ya está listo para explorar el código que se usa para llamar a la API REST y generar una imagen.

1. En el panel **Explorador**, seleccione el archivo de código principal de la aplicación:

    - C#: `Program.cs`
    - Python: `generate-image.py`

2. Revise el código que contiene el archivo y observe las siguientes características clave:
    - El código realiza una solicitud https al punto de conexión del servicio, incluida la clave del servicio en el encabezado. Estos dos valores se obtienen del archivo de configuración.
    - La solicitud incluye algunos parámetros, incluida la solicitud de en la imagen, el número de imágenes que se van a generar y el tamaño de las imágenes generadas.
    - La respuesta incluye una solicitud revisada que el modelo DALL-E extrapola del mensaje proporcionado por el usuario para que sea más descriptivo y la dirección URL de la imagen generada.

### Ejecutar la aplicación

Ahora que ha revisado el código, es el momento de ejecutarlo y generar algunas imágenes.

1. Haga clic con el botón derecho en la carpeta **CSharp** o **Python** que contiene los archivos de código y abra un terminal integrado. A continuación, escriba el comando adecuado para ejecutar la aplicación:

   **C#**
   ```
   dotnet run
   ```
   
   **Python**
   ```
   pip install requests
   python generate-image.py
   ```

3. Cuando se le pida, escriba la descripción de una imagen. Por ejemplo, *A giraffe flying a kite*.

4. Espere a que se genere la imagen: se mostrará un hipervínculo en el panel de terminal. Seleccione el hipervínculo para abrir una nueva pestaña del explorador y revise la imagen que se ha generado.

   > **SUGERENCIA**: Si la aplicación no devuelve una respuesta, espere un minuto e inténtelo de nuevo. Los recursos recién implementados pueden tardar hasta 5 minutos en estar disponibles.

5. Cierre la pestaña del explorador que contiene la imagen generada y vuelva a ejecutar la aplicación para generar una nueva imagen con un símbolo del sistema diferente.

## Limpiar

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar el recurso en **Azure Portal** en `https://portal.azure.com`.
