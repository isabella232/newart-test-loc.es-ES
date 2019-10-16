---
title: Captura de más datos desde Power BI
description: En este artículo se explica cómo habilitar la captura segmentada de grandes conjuntos de datos para objetos visuales de Power BI.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b67977abd93b3aa605430dd2d7fb516ca33bd51c
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424178"
---
# <a name="fetch-more-data-from-power-bi"></a>Captura de más datos desde Power BI

En este artículo se describe cómo cargar más datos para omitir el límite máximo de un punto de datos de 30 KB. Este enfoque proporciona datos en fragmentos. Para mejorar el rendimiento, puede configurar el tamaño del fragmento para que se adapte a su caso de uso.  

## <a name="enable-a-segmented-fetch-of-large-datasets"></a>Habilitación de una captura segmentada de grandes conjuntos de datos

Para el modo de segmento `dataview`, defina un tamaño de ventana para dataReductionAlgorithm en el archivo *capabilities.json* del objeto visual para el elemento dataViewMapping necesario. `count` determina el tamaño de la ventana, que limita el número de las filas de datos nuevas que se pueden anexar a `dataview` en cada actualización.

Agregue el siguiente código en el archivo *capabilities.json*:

```typescript
"dataViewMappings": [
    {
        "table": {
            "rows": {
                "for": {
                    "in": "values"
                },
                "dataReductionAlgorithm": {
                    "window": {
                        "count": 100
                    }
                }
            }
    }
]
```

Los segmentos nuevos se anexan al objeto `dataview` existente y se proporcionan al objeto visual como una llamada a `update`.

## <a name="usage-in-the-power-bi-visual"></a>Uso en el objeto visual de Power BI

Puede determinar si los datos existen al comprobar si `dataView.metadata.segment` existe:

```typescript
    public update(options: VisualUpdateOptions) {
        const dataView = options.dataViews[0];
        console.log(dataView.metadata.segment);
        // output: __proto__: Object
    }
```

También puede comprobar si es la primera actualización o una actualización posterior si comprueba `options.operationKind`. En el código siguiente, `VisualDataChangeOperationKind.Create` hace referencia al primer segmento y `VisualDataChangeOperationKind.Append` hace referencia a los segmentos siguientes.

Para consultar una implementación de ejemplo, vea el fragmento de código siguiente:

```typescript
// CV update implementation
public update(options: VisualUpdateOptions) {
    // indicates this is the first segment of new data.
    if (options.operationKind == VisualDataChangeOperationKind.Create) {

    }

    // on second or subsequent segments:
    if (options.operationKind == VisualDataChangeOperationKind.Append) {

    }

    // complete update implementation
}
```

También puede invocar el método `fetchMoreData` desde un controlador de eventos de la interfaz de usuario, como se muestra aquí:

```typescript
btn_click(){
{
    // check if more data is expected for the current data view
    if (dataView.metadata.segment) {
        //request for more data if available; as a response, Power BI will call update method
        let request_accepted: bool = this.host.fetchMoreData();
        // handle rejection
        if (!request_accepted) {
            // for example, when the 100 MB limit has been reached
        }
    }
}
```

Como respuesta a la llamada al método `this.host.fetchMoreData`, Power BI llama al método `update` del objeto visual con un nuevo segmento de datos.

> [!NOTE]
> Para evitar restricciones de memoria del cliente, actualmente Power BI limita el total de datos recuperados a 100 MB. Puede ver que se ha alcanzado el límite si fetchMoreData() devuelve `false`.
