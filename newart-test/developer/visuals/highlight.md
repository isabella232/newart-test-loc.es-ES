---
title: Resaltado
description: Resaltado de selecciones de puntos de datos en objetos visuales de Power BI
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 4ff2187dc99d4e790b08c11f55a37e31e85693ad
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424283"
---
# <a name="highlight-data-points-in-power-bi-visuals"></a>Resaltado de puntos de datos en objetos visuales de Power BI

De forma predeterminada, cada vez que se selecciona un elemento, la matriz `values` del objeto `dataView` se filtra solo por los valores seleccionados. Hará que todos los demás objetos visuales de la página muestren solo los datos seleccionados.

![Comportamiento predeterminado del resaltado de "DataView"](./media/highlight-dataview.png)

Si establece la propiedad `supportsHighlight` de `capabilities.json` en `true`, recibirá la matriz `values` completa sin filtrar junto con una matriz `highlights`. La matriz `highlights` tendrá la misma longitud que la matriz de valores y todos los valores no seleccionados se establecerán en `null`. Con esta propiedad habilitada, el objeto visual es el responsable de resaltar los datos adecuados mediante la comparación de la matriz `values` con la matriz `highlights`.

![Compatibilidad con el resaltado en la vista de datos](./media/highlight-dataview-supports.png)

En el ejemplo, observará que la barra 1 está seleccionada. Y es el único valor de la matriz highlights. También es importante tener en cuenta que puede haber varias selecciones y resaltado parcial. Existe el valor numérico correspondiente en los valores y las matrices highlights estarán presentes pero serán distintas.
