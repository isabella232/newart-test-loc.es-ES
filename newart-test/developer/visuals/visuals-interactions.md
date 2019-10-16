---
title: Interacciones de objetos visuales en Power BI
description: En este artículo se describe cómo comprobar si los objetos visuales de Power BI deben permitir interacciones de objetos visuales.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 0155f0fce1bc0fec5c96aef1c7e1dc9cf64b122f
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424643"
---
# <a name="visual-interactions-in-power-bi-visuals"></a><span data-ttu-id="bf9b9-103">Interacciones de objetos visuales en Power BI</span><span class="sxs-lookup"><span data-stu-id="bf9b9-103">Visual interactions in Power BI visuals</span></span>

<span data-ttu-id="bf9b9-104">Los objetos visuales pueden consultar el valor de la marca `allowInteractions`, que indica si el objeto visual debe permitir interacciones de objetos visuales.</span><span class="sxs-lookup"><span data-stu-id="bf9b9-104">Visuals can query the value of the `allowInteractions` flag, which indicates whether the visual should allow visual interactions.</span></span> <span data-ttu-id="bf9b9-105">Por ejemplo, los objetos visuales son interactivos durante la visualización o edición de informes, pero no cuando se ven en un panel.</span><span class="sxs-lookup"><span data-stu-id="bf9b9-105">For example, visuals are interactive during report viewing or editing, but not interactive when they're viewed in a dashboard.</span></span> <span data-ttu-id="bf9b9-106">Estas interacciones son las de *clic*, *desplazamiento lateral*, *zoom*, *selección* y otras.</span><span class="sxs-lookup"><span data-stu-id="bf9b9-106">These interactions are *click*, *pan*, *zoom*, *selection*, and others.</span></span> 

> [!NOTE]
> <span data-ttu-id="bf9b9-107">Debe habilitar la información sobre herramientas en todos los escenarios, independientemente de la marca que se indique.</span><span class="sxs-lookup"><span data-stu-id="bf9b9-107">You should enable tooltips in all scenarios, regardless of which flag is indicated.</span></span>

<span data-ttu-id="bf9b9-108">La marca `allowInteractions` se pasa como un booleano durante la inicialización del objeto visual, como miembro de la interfaz IVisualHost.</span><span class="sxs-lookup"><span data-stu-id="bf9b9-108">The `allowInteractions` flag is passed as a Boolean during the initialization of the visual, as a member of the IVisualHost interface.</span></span>

<span data-ttu-id="bf9b9-109">En cualquier escenario de Power BI en el que se requiera que los objetos visuales no sean interactivos (por ejemplo, iconos de panel), la marca `allowInteractions` se establece en `false`.</span><span class="sxs-lookup"><span data-stu-id="bf9b9-109">In any Power BI scenario that requires that the visuals not be interactive (for example, dashboard tiles), the `allowInteractions` flag is set to `false`.</span></span> <span data-ttu-id="bf9b9-110">En caso contrario (por ejemplo, en un informe), `allowInteractions` se establece en `true`.</span><span class="sxs-lookup"><span data-stu-id="bf9b9-110">Otherwise (for example, Report), `allowInteractions` is set to `true`.</span></span>

<span data-ttu-id="bf9b9-111">Para más información, consulte el [repositorio del objeto visual SampleBarChart](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001).</span><span class="sxs-lookup"><span data-stu-id="bf9b9-111">For more information, see the [SampleBarChart visual repository](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001).</span></span>

```typescript
   ...
   let allowInteractions = options.host.allowInteractions;
   bars.on('click', function(d) {
       if (allowInteractions) {
           selectionManager.select(d.selectionId);
           ...
       }
   });
```
