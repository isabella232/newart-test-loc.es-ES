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
# <a name="visual-interactions-in-power-bi-visuals"></a>Interacciones de objetos visuales en Power BI

Los objetos visuales pueden consultar el valor de la marca `allowInteractions`, que indica si el objeto visual debe permitir interacciones de objetos visuales. Por ejemplo, los objetos visuales son interactivos durante la visualización o edición de informes, pero no cuando se ven en un panel. Estas interacciones son las de *clic*, *desplazamiento lateral*, *zoom*, *selección* y otras. 

> [!NOTE]
> Debe habilitar la información sobre herramientas en todos los escenarios, independientemente de la marca que se indique.

La marca `allowInteractions` se pasa como un booleano durante la inicialización del objeto visual, como miembro de la interfaz IVisualHost.

En cualquier escenario de Power BI en el que se requiera que los objetos visuales no sean interactivos (por ejemplo, iconos de panel), la marca `allowInteractions` se establece en `false`. En caso contrario (por ejemplo, en un informe), `allowInteractions` se establece en `true`.

Para más información, consulte el [repositorio del objeto visual SampleBarChart](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/commit/59a47935d8f5272ce145fe804193599ddb7e2001).

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
