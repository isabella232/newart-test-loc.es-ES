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
# <a name="highlight-data-points-in-power-bi-visuals"></a><span data-ttu-id="72e75-103">Resaltado de puntos de datos en objetos visuales de Power BI</span><span class="sxs-lookup"><span data-stu-id="72e75-103">Highlight data points in Power BI Visuals</span></span>

<span data-ttu-id="72e75-104">De forma predeterminada, cada vez que se selecciona un elemento, la matriz `values` del objeto `dataView` se filtra solo por los valores seleccionados.</span><span class="sxs-lookup"><span data-stu-id="72e75-104">By default whenever an element is selected the `values` array in the `dataView` object will be filtered to just the selected values.</span></span> <span data-ttu-id="72e75-105">Hará que todos los demás objetos visuales de la página muestren solo los datos seleccionados.</span><span class="sxs-lookup"><span data-stu-id="72e75-105">It will cause all other visuals on the page to display just the selected data.</span></span>

![Comportamiento predeterminado del resaltado de "DataView"](./media/highlight-dataview.png)

<span data-ttu-id="72e75-107">Si establece la propiedad `supportsHighlight` de `capabilities.json` en `true`, recibirá la matriz `values` completa sin filtrar junto con una matriz `highlights`.</span><span class="sxs-lookup"><span data-stu-id="72e75-107">If you set the `supportsHighlight` property in your `capabilities.json` to `true`, you'll receive the full unfiltered `values` array along with a `highlights` array.</span></span> <span data-ttu-id="72e75-108">La matriz `highlights` tendrá la misma longitud que la matriz de valores y todos los valores no seleccionados se establecerán en `null`.</span><span class="sxs-lookup"><span data-stu-id="72e75-108">The `highlights` array will be the same length as the values array and any non-selected values will be set to `null`.</span></span> <span data-ttu-id="72e75-109">Con esta propiedad habilitada, el objeto visual es el responsable de resaltar los datos adecuados mediante la comparación de la matriz `values` con la matriz `highlights`.</span><span class="sxs-lookup"><span data-stu-id="72e75-109">With this property enabled it's the visual's responsibility to highlight the appropriate data by comparing the `values` array to the `highlights` array.</span></span>

![Compatibilidad con el resaltado en la vista de datos](./media/highlight-dataview-supports.png)

<span data-ttu-id="72e75-111">En el ejemplo, observará que la barra 1 está seleccionada.</span><span class="sxs-lookup"><span data-stu-id="72e75-111">In the example, you'll notice the 1 bar that is selected.</span></span> <span data-ttu-id="72e75-112">Y es el único valor de la matriz highlights.</span><span class="sxs-lookup"><span data-stu-id="72e75-112">And it's the only value in the highlights array.</span></span> <span data-ttu-id="72e75-113">También es importante tener en cuenta que puede haber varias selecciones y resaltado parcial.</span><span class="sxs-lookup"><span data-stu-id="72e75-113">It's also important to note there could be multiple selections and partial highlight.</span></span> <span data-ttu-id="72e75-114">Existe el valor numérico correspondiente en los valores y las matrices highlights estarán presentes pero serán distintas.</span><span class="sxs-lookup"><span data-stu-id="72e75-114">There's the corresponding numeric value in the values and highlights arrays will be present but different.</span></span>
