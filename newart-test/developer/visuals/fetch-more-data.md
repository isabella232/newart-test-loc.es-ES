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
# <a name="fetch-more-data-from-power-bi"></a><span data-ttu-id="87625-103">Captura de más datos desde Power BI</span><span class="sxs-lookup"><span data-stu-id="87625-103">Fetch more data from Power BI</span></span>

<span data-ttu-id="87625-104">En este artículo se describe cómo cargar más datos para omitir el límite máximo de un punto de datos de 30 KB.</span><span class="sxs-lookup"><span data-stu-id="87625-104">This article discusses how to load more data to bypass the hard limit of a 30-KB data point.</span></span> <span data-ttu-id="87625-105">Este enfoque proporciona datos en fragmentos.</span><span class="sxs-lookup"><span data-stu-id="87625-105">This approach provides data in chunks.</span></span> <span data-ttu-id="87625-106">Para mejorar el rendimiento, puede configurar el tamaño del fragmento para que se adapte a su caso de uso.</span><span class="sxs-lookup"><span data-stu-id="87625-106">To improve performance, you can configure the chunk size to accommodate your use case.</span></span>  

## <a name="enable-a-segmented-fetch-of-large-datasets"></a><span data-ttu-id="87625-107">Habilitación de una captura segmentada de grandes conjuntos de datos</span><span class="sxs-lookup"><span data-stu-id="87625-107">Enable a segmented fetch of large datasets</span></span>

<span data-ttu-id="87625-108">Para el modo de segmento `dataview`, defina un tamaño de ventana para dataReductionAlgorithm en el archivo *capabilities.json* del objeto visual para el elemento dataViewMapping necesario.</span><span class="sxs-lookup"><span data-stu-id="87625-108">For the `dataview` segment mode, you define a window size for dataReductionAlgorithm in the visual's *capabilities.json* file for the required dataViewMapping.</span></span> <span data-ttu-id="87625-109">`count` determina el tamaño de la ventana, que limita el número de las filas de datos nuevas que se pueden anexar a `dataview` en cada actualización.</span><span class="sxs-lookup"><span data-stu-id="87625-109">The `count` determines the window size, which limits the number of new data rows that can be appended to the `dataview` in each update.</span></span>

<span data-ttu-id="87625-110">Agregue el siguiente código en el archivo *capabilities.json*:</span><span class="sxs-lookup"><span data-stu-id="87625-110">Add the following code in the *capabilities.json* file:</span></span>

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

<span data-ttu-id="87625-111">Los segmentos nuevos se anexan al objeto `dataview` existente y se proporcionan al objeto visual como una llamada a `update`.</span><span class="sxs-lookup"><span data-stu-id="87625-111">New segments are appended to the existing `dataview` and provided to the visual as an `update` call.</span></span>

## <a name="usage-in-the-power-bi-visual"></a><span data-ttu-id="87625-112">Uso en el objeto visual de Power BI</span><span class="sxs-lookup"><span data-stu-id="87625-112">Usage in the Power BI visual</span></span>

<span data-ttu-id="87625-113">Puede determinar si los datos existen al comprobar si `dataView.metadata.segment` existe:</span><span class="sxs-lookup"><span data-stu-id="87625-113">You can determine whether data exists by checking the existence of `dataView.metadata.segment`:</span></span>

```typescript
    public update(options: VisualUpdateOptions) {
        const dataView = options.dataViews[0];
        console.log(dataView.metadata.segment);
        // output: __proto__: Object
    }
```

<span data-ttu-id="87625-114">También puede comprobar si es la primera actualización o una actualización posterior si comprueba `options.operationKind`.</span><span class="sxs-lookup"><span data-stu-id="87625-114">You can also check to see whether it's the first update or a subsequent update by checking `options.operationKind`.</span></span> <span data-ttu-id="87625-115">En el código siguiente, `VisualDataChangeOperationKind.Create` hace referencia al primer segmento y `VisualDataChangeOperationKind.Append` hace referencia a los segmentos siguientes.</span><span class="sxs-lookup"><span data-stu-id="87625-115">In the following code, `VisualDataChangeOperationKind.Create` refers to the first segment, and `VisualDataChangeOperationKind.Append` refers to subsequent segments.</span></span>

<span data-ttu-id="87625-116">Para consultar una implementación de ejemplo, vea el fragmento de código siguiente:</span><span class="sxs-lookup"><span data-stu-id="87625-116">For a sample implementation, see the following code snippet:</span></span>

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

<span data-ttu-id="87625-117">También puede invocar el método `fetchMoreData` desde un controlador de eventos de la interfaz de usuario, como se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="87625-117">You can also invoke the `fetchMoreData` method from a UI event handler, as shown here:</span></span>

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

<span data-ttu-id="87625-118">Como respuesta a la llamada al método `this.host.fetchMoreData`, Power BI llama al método `update` del objeto visual con un nuevo segmento de datos.</span><span class="sxs-lookup"><span data-stu-id="87625-118">As a response to calling the `this.host.fetchMoreData` method, Power BI calls the `update` method of the visual with a new segment of data.</span></span>

> [!NOTE]
> <span data-ttu-id="87625-119">Para evitar restricciones de memoria del cliente, actualmente Power BI limita el total de datos recuperados a 100 MB.</span><span class="sxs-lookup"><span data-stu-id="87625-119">To avoid client memory constraints, Power BI currently limits the fetched data total to 100 MB.</span></span> <span data-ttu-id="87625-120">Puede ver que se ha alcanzado el límite si fetchMoreData() devuelve `false`.</span><span class="sxs-lookup"><span data-stu-id="87625-120">You can see that the limit has been reached when fetchMoreData() returns `false`.</span></span>
