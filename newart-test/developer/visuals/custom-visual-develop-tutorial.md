---
title: Desarrollar un objeto visual de Power BI
description: Un tutorial sobre cómo desarrollar un objeto visual personalizado de Power BI
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: tutorial
ms.date: 03/15/2019
ms.openlocfilehash: 1aa269bc738b873ac36498e2ecf52f2cf06c209d
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424483"
---
# <a name="tutorial-developing-a-power-bi-visual"></a>Tutorial: desarrollar un Power BI visual

Permitimos que los desarrolladores agreguen fácilmente a Power BI objetos visuales de Power BI para usarlos en los paneles e informes. Para ayudarle a comenzar, publicamos en GitHub el código de todas nuestras visualizaciones.

Junto con el marco de trabajo de visualización, incluimos nuestro conjunto de pruebas y herramientas para ayudar a la comunidad a crear objetos visuales de Power BI de alta calidad para Power BI.

Este tutorial le muestra cómo desarrollar un objeto visual personalizado de Power BI llamado Circle Card para mostrar un valor de medida formateado dentro de un círculo. El objeto visual Circle Card admite la personalización del color de relleno y el grosor de su contorno.

En el informe de Power BI Desktop, las tarjetas se modifican para convertirse en tarjetas circulares.

  ![Salida de ejemplo de objetos visuales personalizados de Power BI](media/custom-visual-develop-tutorial/circle-cards.png)

En este tutorial, obtendrá información sobre cómo:
> [!div class="checklist"]
> * Crear un objeto visual de Power BI.
> * Desarrollar el objeto visual personalizado con elementos visuales de D3.
> * Configurar el enlace de datos con los elementos visuales.
> * Dar formato a los valores de datos.

## <a name="prerequisites"></a>Requisitos previos

* Si no está registrado en **Power BI Pro**, [regístrese para obtener una evaluación gratuita](https://powerbi.microsoft.com/pricing/) antes de empezar.
* Necesita tener [Visual Studio Code](https://www.visualstudio.com/) instalado.
* Necesita la versión 4 de [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6) o una posterior para los usuarios de Windows o [Terminal](https://macpaw.com/how-to/use-terminal-on-mac) para los usuarios de OSX.

## <a name="setting-up-the-developer-environment"></a>Configuración del entorno de desarrollo

Además de los requisitos previos, hay algunas herramientas más que necesita instalar.

### <a name="installing-nodejs"></a>Instalación de node.js

1. Para instalar Node.js en un explorador web, vaya a [Node.js](https://nodejs.org).

2. Descargue la última característica del instalador MSI.

3. Ejecute al instalador y, después, siga los pasos de instalación. Acepte los términos del contrato de licencia y todos los valores predeterminados.

   ![ Configuración de Node.js](media/custom-visual-develop-tutorial/node-js-setup.png)

4. Reinicie el equipo.

### <a name="installing-packages"></a>Instalación de paquetes

Ahora tiene que instalar el paquete **pbiviz**.

1. Una vez reiniciado el equipo, abra Windows PowerShell.

2. Para instalar pbiviz, escriba el siguiente comando.

    ```powershell
    npm i -g powerbi-visuals-tools
    ```

### <a name="creating-and-installing-a-certificate"></a>Creación e instalación de un certificado

#### <a name="windows"></a>Windows

1. Para crear e instalar un certificado, escriba el siguiente comando.

    ```powershell
    pbiviz --install-cert
    ```

    Devuelve un resultado que genera una *frase de contraseña*. En este caso, la *frase de contraseña* es **_15105661266553327_** . También inicia el Asistente para importar certificados.

    ![Certificado creado a través de PowerShell](media/custom-visual-develop-tutorial/cert-create.png)

2. En el Asistente para importar certificados, compruebe que la ubicación del almacén se establece en el usuario actual. Después, seleccione *Siguiente*.

      ![Instalación del certificado](media/custom-visual-develop-tutorial/install-cert-PowerShell.png)

3. En el paso **Archivo para importar**, seleccione *Siguiente*.

4. En el paso **protección de clave privada** , en el cuadro contraseña, pegue la frase de contraseña que recibió al crear el certificado.  De nuevo, en este caso es **_15105661266553327_** .

      ![Copia de la frase de contraseña](media/custom-visual-develop-tutorial/cert-install-wizard-show-passphrase.png)

5. En el paso **Almacén de certificados**, seleccione la opción **Colocar todos los certificados en el siguiente almacén**. Después, seleccione *Examinar*.

      ![Todos los certificados del siguiente almacén](media/custom-visual-develop-tutorial/all-certs-in-the-following-store.png)

6. En la ventana **Seleccionar almacén de certificados**, seleccione **Entidades de certificación raíz de confianza** y luego seleccione *Aceptar*. A continuación, seleccione *Siguiente* en la pantalla **Almacén de certificados**.

      ![Certificado de raíz de confianza](media/custom-visual-develop-tutorial/trusted-root-cert.png)

7. Para completar la importación, seleccione **Finalizar**.

8. Si recibe una advertencia de seguridad, seleccione **Sí**.

    ![Advertencia de seguridad](media/custom-visual-develop-tutorial/cert-security-warning.png)

9. Cuando se le notifique que la importación se ha realizado correctamente, seleccione **Aceptar**.

    ![Importación correcta del certificado](media/custom-visual-develop-tutorial/cert-import-successful.png)

> [!Important]
> No cierre la sesión de Windows PowerShell.

#### <a name="osx"></a>OSX

1. Si el candado de la parte superior izquierda está bloqueado, selecciónelo para desbloquearlo. Busque *localhost* y haga doble clic en el certificado.

    ![Instalar un certificado SSL 1 en OSX](media/custom-visual-develop-tutorial/install-ssl-certificate-osx.png)

2. Seleccione **Confiar siempre** y cierre la ventana.

    ![Instalar un certificado SSL 2 en OSX](media/custom-visual-develop-tutorial/install-ssl-certificate-osx2.png)

3. Escriba el nombre de usuario y contraseña. Seleccione **Actualizar configuración**.

    ![Instalar un certificado SSL 3 en OSX](media/custom-visual-develop-tutorial/install-ssl-certificate-osx3.png)

4. Cierre los exploradores que tenga abiertos.

> [!NOTE]
> Si no se reconoce el certificado, deberá reiniciar el equipo.

## <a name="creating-a-custom-visual"></a>Creación de un objeto visual personalizado

Ahora que ha configurado el entorno, es momento de crear el objeto visual personalizado.

También puede [descargar](https://github.com/Microsoft/PowerBI-visuals-circlecard) el código fuente completo para este tutorial.

1. Compruebe que se ha instalado el paquete de herramientas de objetos visuales de Power BI.

    ```powershell
    pbiviz
    ```
    Debería ver el resultado de la ayuda.

    <pre><code>
        +syyso+/
    oms/+osyhdhyso/
    ym/       /+oshddhys+/
    ym/              /+oyhddhyo+/
    ym/                     /osyhdho
    ym/                           sm+
    ym/               yddy        om+
    ym/         shho /mmmm/       om+
        /    oys/ +mmmm /mmmm/       om+
    oso  ommmh +mmmm /mmmm/       om+
    ymmmy smmmh +mmmm /mmmm/       om+
    ymmmy smmmh +mmmm /mmmm/       om+
    ymmmy smmmh +mmmm /mmmm/       om+
    +dmd+ smmmh +mmmm /mmmm/       om+
            /hmdo +mmmm /mmmm/ /so+//ym/
                /dmmh /mmmm/ /osyhhy/
                    //   dmmd
                        ++

        PowerBI Custom Visual Tool

    Usage: pbiviz [options] [command]

    Commands:

    new [name]        Create a new visual
    info              Display info about the current visual
    start             Start the current visual
    package           Package the current visual into a pbiviz file
    update [version]  Updates the api definitions and schemas in the current visual. Changes the version if specified
    help [cmd]        display help for [cmd]

    Options:

    -h, --help      output usage information
    -V, --version   output the version number
    --install-cert  Install localhost certificate
    </code></pre>

    <a name="ssl-setup"></a>

2. Revise la salida, incluida la lista de comandos admitidos.

    ![Comandos admitidos](media/custom-visual-develop-tutorial/PowerShell-supported-commands.png)

3. Para crear un proyecto de objetos visuales personalizados, escriba el siguiente comando. **CircleCard** es el nombre del proyecto.

    ```PowerShell
    pbiviz new CircleCard
    ```
    ![Nuevo resultado de CircleCard](media/custom-visual-develop-tutorial/new-circle-card-result.png)

    > [!Note]
    > El nuevo proyecto se crea en la ubicación actual de la confirmación.

4. Vaya a la carpeta del proyecto.

    ```powershell
    cd CircleCard
    ```
5. Inicie el objeto visual personalizado. Su objeto visual CircleCard ahora se ejecuta mientras está hospedado en el equipo.

    ```powershell
    pbiviz start
    ```

    ![Inicio de la ejecución del objeto visual personalizado](media/custom-visual-develop-tutorial/start-running-custom-visual-PowerShell.png)

> [!Important]
> No cierre la sesión de Windows PowerShell.

### <a name="testing-the-custom-visual"></a>Prueba del objeto visual personalizado

En esta sección, vamos a probar el objeto visual personalizado CircleCard al cargar un informe de Power BI Desktop y luego editar el informe para mostrar el objeto visual personalizado.

1. Inicie sesión en [PowerBI.com](https://powerbi.microsoft.com/) > vaya al **icono de engranaje** > y seleccione **Settings** (Configuración).

      ![Configuración de Power BI](media/custom-visual-develop-tutorial/power-bi-settings.png)

2. Seleccione **Desarrollador** y luego active la casilla **Habilitar objeto visual de desarrollador para realizar pruebas**.

    ![Configuración de la página para desarrolladores](media/custom-visual-develop-tutorial/developer-page-settings.png)

3. Cargue un informe de Power BI Desktop.  

    Obtener datos > Archivos > Archivo local.

    También puede [descargar](https://microsoft.github.io/PowerBI-visuals/docs/step-by-step-lab/images/US_Sales_Analysis.pbix) un informe de ejemplo de Power BI Desktop si aún no ha creado uno.

    ![Obtener datos](media/custom-visual-develop-tutorial/get-data.png) ![Archivo local](media/custom-visual-develop-tutorial/local-file.png)

    Ahora, para ver el informe, seleccione **US_Sales_Analysis** en la sección **Informe** del panel de navegación de la izquierda.

    ![Ejemplo de un objeto visual personalizado para la aplicación de escritorio](media/custom-visual-develop-tutorial/custom-visual-sample.png)

4. Ahora tiene que editar el informe en el servicio Power BI.

    Vaya a **Editar informe**.

    ![Editar informe](media/custom-visual-develop-tutorial/edit-report.png)

5. Seleccione el **objeto visual de desarrollador** en el panel **Visualizaciones**.

    ![Objeto visual de desarrollador](media/custom-visual-develop-tutorial/developer-visual.png)

    > [!Note]
    > Esta visualización representa el objeto visual personalizado que inició en el equipo. Solo está disponible cuando se ha habilitado la configuración del desarrollador.

6. Tenga en cuenta que se ha agregado una visualización al lienzo del informe.

    ![Nuevo objeto visual](media/custom-visual-develop-tutorial/new-visual-in-report.png)

    > [!Note]
    > Este es un objeto visual muy simple que muestra el número de veces que se ha llamado al método update. En esta fase, el objeto visual todavía no recupera los datos.

7. Al seleccionar el nuevo objeto visual en el informe, vaya al panel Campos > expanda Ventas > seleccione Cantidad.

    ![Ventas - Cantidad](media/custom-visual-develop-tutorial/quantity-sales.png)

8. Después, para probar el nuevo objeto visual, cambie el tamaño del objeto visual y observe los incrementos de valor de actualización.

    ![Cambio del tamaño de un objeto visual](media/custom-visual-develop-tutorial/resize-visual.png)

Para detener la ejecución del objeto visual personalizado en PowerShell, introduzca Ctrl+C. Cuando se le solicite que finalice el trabajo por lotes, escriba S y presione ENTRAR.

## <a name="adding-visual-elements"></a>Adición de elementos visuales

Ahora tiene que instalar la **biblioteca JavaScript D3**. D3 es una biblioteca de JavaScript para producir visualizaciones de datos dinámicos e interactivos en exploradores web. Utiliza estándares SVG HTML5 y CSS ampliamente implementados.

Ahora puede desarrollar el objeto visual personalizado para mostrar un círculo con texto.

> [!Note]
> Muchas de las entradas de texto de este tutorial se pueden copiar desde [aquí](https://github.com/Microsoft/powerbi-visuals-circlecard).

1. Para instalar la **biblioteca D3** en PowerShell, escriba el siguiente comando.

    ```powershell
    npm i d3@^5.0.0 --save
    ```

    ```powershell
    PS C:\circlecard>npm i d3@^5.0.0 --save
    + d3@5.11.0
    added 179 packages from 169 contributors and audited 306 packages in 33.25s
    found 0 vulnerabilities

    PS C:\circlecard>
    ```

2. Para instalar las definiciones de tipo para la **biblioteca D3**, escriba el siguiente comando.

    ```powershell
    npm i @types/d3@^5.0.0 --save
    ```

    ```powershell
    PS C:\circlecard>npm i @types/d3@^5.0.0 --save
    + @types/d3@5.7.2
    updated 1 package and audited 306 packages in 2.217s
    found 0 vulnerabilities

    PS C:\circlecard>
    ```

    Este comando instala las definiciones de TypeScript basadas en archivos JavaScript, lo que le permite desarrollar el objeto visual personalizado en TypeScript (que es un superconjunto de JavaScript). Visual Studio Code es el entorno de desarrollo integrado perfecto para desarrollar aplicaciones de TypeScript.

3. Para instalar **core-js** en PowerShell, escriba el siguiente comando.

    ```powershell
    npm i core-js@3.2.1 --save
    ```

    ```powershell
    PS C:\circlecard> npm i core-js@3.2.1 --save

    > core-js@3.2.1 postinstall F:\circlecard\node_modules\core-js
    > node scripts/postinstall || echo "ignore"

    Thank you for using core-js ( https://github.com/zloirock/core-js ) for polyfilling JavaScript standard library!

    The project needs your help! Please consider supporting of core-js on Open Collective or Patreon:
    > https://opencollective.com/core-js
    > https://www.patreon.com/zloirock

    + core-js@3.2.1
    updated 1 package and audited 306 packages in 6.051s
    found 0 vulnerabilities

    PS C:\circlecard>
    ```

    Este comando instala la biblioteca estándar modular de JavaScript. Incluye polyfill para ECMAScript hasta 2019. En [`core-js`](https://www.npmjs.com/package/core-js) encontrará más información al respecto.

4. Para instalar **powerbi-visual-api** en PowerShell, escriba el siguiente comando.

    ```powershell
    npm i powerbi-visuals-api --save-dev
    ```

    ```powershell
    PS C:\circlecard>npm i powerbi-visuals-api --save-dev

    + powerbi-visuals-api@2.6.1
    updated 1 package and audited 306 packages in 2.139s
    found 0 vulnerabilities

    PS C:\circlecard>
    ```

    Este comando instala definiciones de API de objetos visuales de Power BI.

5. Inicie [Visual Studio Code](https://code.visualstudio.com/).

    Puede iniciar **Visual Studio Code** desde PowerShell mediante el comando siguiente.

    ```powershell
    code .
    ```

6. En el **panel Explorador**, expanda la carpeta **node_modules** para comprobar que la **biblioteca D3** se ha instalado.

    ![Biblioteca D3 en Visual Studio Code](media/custom-visual-develop-tutorial/d3-library.png)

7. Expanda node_modules >  **> d3 en el** panel Explorador@types y asegúrese de que el archivo **index.d.ts** se ha agregado.

    ![Archivo index.d.ts](media/custom-visual-develop-tutorial/index-d-ts.png)

### <a name="developing-the-visual-elements"></a>Desarrollo de los elementos visuales

Ahora podemos explorar cómo desarrollar el objeto visual personalizado para mostrar un círculo y un texto de ejemplo.

1. En el **panel Explorador**, expanda la carpeta **src** y, después, seleccione **visual.ts**.

    > [!Note]
    > Tenga en cuenta los comentarios de la parte superior del archivo **visual.ts**. El permiso para utilizar los paquetes de objetos visuales personalizados de Power BI se concede sin cargo en virtud de los términos de la licencia MIT. Como parte del contrato, debe dejar los comentarios en la parte superior del archivo.

2. Quite la siguiente lógica del objeto visual personalizado predeterminada de la clase Visual.
    * Las cuatro declaraciones privadas de variables a nivel de clase.
    * Todas las líneas de código desde el constructor.
    * Todas las líneas de código desde el método update.
    * Todas las líneas restantes dentro del módulo, incluidos los métodos parseSettings y enumerateObjectInstances.

    Compruebe que el código del módulo tiene un aspecto similar al siguiente.

    ```typescript
    "use strict";
    import "core-js/stable";
    import "../style/visual.less";
    import powerbi from "powerbi-visuals-api";
    import IVisual = powerbi.extensibility.IVisual;
    import VisualConstructorOptions = powerbi.extensibility.visual.VisualConstructorOptions;
    import VisualUpdateOptions = powerbi.extensibility.visual.VisualUpdateOptions;

    import * as d3 from "d3";
    type Selection<T extends d3.BaseType> = d3.Selection<T, any,any, any>;

    export class Visual implements IVisual {

        constructor(options: VisualConstructorOptions) {

        }

        public update(options: VisualUpdateOptions) {

        }
    }
    ```

3. Debajo de la declaración de clase *Visual*, inserte las siguientes propiedades de nivel de clase.

    ```typescript
    export class Visual implements IVisual {
        // ...
        private host: IVisualHost;
        private svg: Selection<SVGElement>;
        private container: Selection<SVGElement>;
        private circle: Selection<SVGElement>;
        private textValue: Selection<SVGElement>;
        private textLabel: Selection<SVGElement>;
        // ...
    }
    ```

    ![Propiedades de nivel de clase del archivo Visual.ts](media/custom-visual-develop-tutorial/visual-ts-file-class-level-properties.png)

4. Agregue el código siguiente al *constructor*.

    ```typescript
    this.svg = d3.select(options.element)
        .append('svg')
        .classed('circleCard', true);
    this.container = this.svg.append("g")
        .classed('container', true);
    this.circle = this.container.append("circle")
        .classed('circle', true);
    this.textValue = this.container.append("text")
        .classed("textValue", true);
    this.textLabel = this.container.append("text")
        .classed("textLabel", true);
    ```

    Este código agrega un grupo de SVG dentro del objeto visual y, después, agrega tres formas: un círculo y dos elementos de texto.

    Para dar formato al código en el documento, haga clic con el botón derecho en cualquier parte del documento **Visual Studio Code** y, después, seleccione **Dar formato al documento**.

      ![Aplicación de formato al documento](media/custom-visual-develop-tutorial/format-document.png)

    Para mejorar la legibilidad, se recomienda que dé formato al documento cada vez que se peguen fragmentos de código.

5. Agregue el código siguiente al método *update*.

    ```typescript
    let width: number = options.viewport.width;
    let height: number = options.viewport.height;
    this.svg.attr("width", width);
    this.svg.attr("height", height);
    let radius: number = Math.min(width, height) / 2.2;
    this.circle
        .style("fill", "white")
        .style("fill-opacity", 0.5)
        .style("stroke", "black")
        .style("stroke-width", 2)
        .attr("r", radius)
        .attr("cx", width / 2)
        .attr("cy", height / 2);
    let fontSizeValue: number = Math.min(width, height) / 5;
    this.textValue
        .text("Value")
        .attr("x", "50%")
        .attr("y", "50%")
        .attr("dy", "0.35em")
        .attr("text-anchor", "middle")
        .style("font-size", fontSizeValue + "px");
    let fontSizeLabel: number = fontSizeValue / 4;
    this.textLabel
        .text("Label")
        .attr("x", "50%")
        .attr("y", height / 2)
        .attr("dy", fontSizeValue / 1.2)
        .attr("text-anchor", "middle")
        .style("font-size", fontSizeLabel + "px");
    ```

    *Este código establece el ancho y la altura del objeto visual, y después inicializa los atributos y estilos de los elementos visuales.*

6. Guarde el archivo **visual.ts**.

7. Seleccione el archivo **capabilities.json**.

    En la línea 14, quite el elemento objects completo (líneas 14-60).

8. Guarde el archivo **capabilities.json**.

9. En PowerShell, inicie el objeto visual personalizado.

    ```powershell
    pbiviz start
    ```

### <a name="toggle-auto-reload"></a>Activar recarga automática

1. Vuelva al informe de Power BI.
2. En la barra de herramientas flotante sobre el objeto visual de desarrollador, seleccione **Activar recarga automática**.

    ![Activar recarga automática](media/custom-visual-develop-tutorial/toggle-auto-reload.png)

    Esta opción asegura que el objeto visual se recargue automáticamente cada vez que guarde los cambios en el proyecto.

3. En el **panel Campos**, arrastre el campo **Cantidad** al objeto visual de desarrollador.

4. Compruebe que el objeto visual tiene un aspecto similar al siguiente.

    ![Objeto visual circular de desarrollador](media/custom-visual-develop-tutorial/circle-developer-visual.png)

5. Cambie el tamaño del objeto visual.

    Observe que las escalas de los valores del círculo y texto se ajustan a la dimensión disponible del objeto visual.

    El método update se llama continuamente con el cambio de tamaño del objeto visual y tiene como resultado un cambio de escala fluido de los elementos visuales.

    Ahora ha desarrollado los elementos visuales.

6. Continúe ejecutando el objeto visual.

## <a name="process-data-in-the-visual-code"></a>Procesar datos en el código del elemento visual

Defina los roles de datos y las asignaciones de vista de datos, y después modifique la lógica del objeto visual personalizado para mostrar el valor y el nombre para mostrar de una medida.

### <a name="configuring-the-capabilities"></a>Configuración de las funcionalidades

Modifique el archivo **capabilities.json** para definir el rol de datos y las asignaciones de vistas de datos.

1. En Visual Studio Code, en el archivo **capabilities.json**, desde dentro de la matriz **dataRoles**, quite todo el contenido (líneas 3-12).

2. Dentro de la matriz **dataRoles**, inserte el código siguiente.

    ```json
    {
        "displayName": "Measure",
        "name": "measure",
        "kind": "Measure"
    }
    ```

    La matriz **dataRoles** define ahora un rol de datos único de tipo **measure**, que se denomina **measure** y se muestra como **Measure**. Este rol de datos permite pasar un campo de medida o un campo integrado.

3. Desde dentro de la matriz **dataViewMappings**, quite todo el contenido (líneas 10-31).

4. Dentro de la matriz **dataViewMappings**, inserte el siguiente contenido.

    ```json
    {
        "conditions": [
            { "measure": { "max": 1 } }
        ],
        "single": {
            "role": "measure"
        }
    }
    ```

    La matriz **dataViewMappings** ahora define un campo se puede pasar al rol de datos denominado **measure**.

5. Guarde el archivo **capabilities.json**.

6. En Power BI, observe que el objeto visual ahora puede configurarse con **Measure**.

    ![Medida - Cantidad](media/custom-visual-develop-tutorial/quantity_measure.png)

    > [!Note]
    > El proyecto de objeto visual todavía no incluye la lógica de enlace de datos.

### <a name="exploring-the-dataview"></a>Exploración de la vista de datos

1. En la barra de herramientas flotante encima del objeto visual, seleccione **Mostrar vista de datos**.

    ![Mostrar vista de datos](media/custom-visual-develop-tutorial/show-dataview-toolbar.png)

2. Desplácese hacia abajo a **single** y observe el valor.

    ![Desplazamiento hasta el valor](media/custom-visual-develop-tutorial/value-display-in-visual.png)

3. Vaya a **metadata** y, después, en la matriz **columns** observe en particular los valores **format** y **displayName**.

    ![Valor de displayName](media/custom-visual-develop-tutorial/displayname-and-format-metadata.png)

4. Para volver al objeto visual, en la barra de herramientas flotante sobre el objeto visual, seleccione **Mostrar vista de datos**.

    ![Activación de la alternancia](media/custom-visual-develop-tutorial/show-dataview-toolbar-revert.png)

### <a name="consume-data-in-the-visual-code"></a>Consumir datos en el código del elemento visual

1. En **Visual Studio Code**, en el archivo **visual.ts**,

    importe la interfaz `DataView` desde el módulo `powerbi`

    ```typescript
    import DataView = powerbi.DataView;
    ```

    y agregue la siguiente instrucción como la primera instrucción del método update.

    ```typescript
    let dataView: DataView = options.dataViews[0];
    ```

    ![Vista de datos de matriz de actualización](media/custom-visual-develop-tutorial/dataview-in-update-array.png)

    Esta instrucción asigna el objeto *dataView* a una variable para facilitar el acceso y declara la variable para hacer referencia a dicho objeto *dataView*.

2. En el método **update**, reemplace **.text("Value")** por lo siguiente.

    ```typescript
    .text(<string>dataView.single.value)
    ```

    ![Reemplazo de textValue](media/custom-visual-develop-tutorial/text-value-replace.png)

3. En el método **update**, reemplace **.text("Label")** por lo siguiente.

    ```typescript
    .text(dataView.metadata.columns[0].displayName)
    ```

    ![Reemplazo de textLabel](media/custom-visual-develop-tutorial/text-label-replace.png)

4. Guarde el archivo **visual.ts**.

5. En **Power BI**, revise el objeto visual, que ahora muestra el valor y el nombre para mostrar.

Ahora ha configurado los roles de datos y ha enlazado el objeto visual con la vista de datos.

En el siguiente tutorial aprenderá a agregar opciones de formato al objeto visual personalizado.

## <a name="debugging"></a>Depuración

Para obtener sugerencias sobre cómo depurar el objeto visual personalizado, consulte la [guía de depuración](https://microsoft.github.io/PowerBI-visuals/docs/how-to-guide/how-to-debug/).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Adición de opciones de formato](custom-visual-develop-tutorial-format-options.md)
