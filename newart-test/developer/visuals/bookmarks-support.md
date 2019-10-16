---
title: Adición de compatibilidad de los marcadores para los objetos visuales de Power BI
description: Los objetos visuales de Power BI pueden controlar el cambio de marcadores.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: c19b67a59d0ecb4cbfbcf5ad8dd18886f440e164
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424228"
---
# <a name="add-bookmark-support-for-power-bi-visuals"></a><span data-ttu-id="0cd5d-103">Adición de compatibilidad de los marcadores para los objetos visuales de Power BI</span><span class="sxs-lookup"><span data-stu-id="0cd5d-103">Add bookmark support for Power BI visuals</span></span>

<span data-ttu-id="0cd5d-104">Con los marcadores de los informes de Power BI, puede capturar una vista configurada de una página de informes, el estado de selección y el estado de filtrado del objeto visual.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-104">With Power BI report bookmarks, you can capture a configured view of a report page, the selection state, and the filtering state of the visual.</span></span> <span data-ttu-id="0cd5d-105">Con todo, los objetos visuales de Power BI requieren una acción más para admitir los marcadores y poder reaccionar correctamente a los cambios.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-105">But it requires additional action from the Power BI visuals side to support the bookmark and react correctly to changes.</span></span>

<span data-ttu-id="0cd5d-106">Para obtener más información sobre los marcadores, consulte [Uso de marcadores para compartir información detallada y crear historias en Power BI](https://docs.microsoft.com/power-bi/desktop-bookmarks).</span><span class="sxs-lookup"><span data-stu-id="0cd5d-106">For more information about bookmarks, see [Use bookmarks to share insights and build stories in Power BI](https://docs.microsoft.com/power-bi/desktop-bookmarks).</span></span>

## <a name="report-bookmarks-support-in-your-visual"></a><span data-ttu-id="0cd5d-107">Compatibilidad de los marcadores de un informe en un objeto visual</span><span class="sxs-lookup"><span data-stu-id="0cd5d-107">Report bookmarks support in your visual</span></span>

<span data-ttu-id="0cd5d-108">Si el objeto visual interactúa con otros objetos visuales, selecciona puntos de datos o filtra otros objetos visuales, tiene que restaurar el estado de las propiedades.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-108">If your visual interacts with other visuals, selects data points, or filters other visuals, you need to restore the state from properties.</span></span>

## <a name="add-report-bookmarks-support"></a><span data-ttu-id="0cd5d-109">Adición de la compatibilidad con los marcadores de un informe</span><span class="sxs-lookup"><span data-stu-id="0cd5d-109">Add report bookmarks support</span></span>

1. <span data-ttu-id="0cd5d-110">Instale (o actualice) la utilidad requerida: [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/), versión 3.0.0 o posteriores.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-110">Install (or update) the required utility, [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/) version 3.0.0 or later.</span></span> <span data-ttu-id="0cd5d-111">Contiene clases adicionales para la manipulación con el filtro o la selección de estado.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-111">It contains additional classes to manipulate with the state selection or filter.</span></span> <span data-ttu-id="0cd5d-112">Se necesita para los objetos visuales de filtro y cualquiera que use `InteractivityService`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-112">It's required for filter visuals and any visual that uses `InteractivityService`.</span></span>

2. <span data-ttu-id="0cd5d-113">Actualice la API del objeto visual a la versión 1.11.0 para usar `registerOnSelectCallback` en una instancia de `SelectionManager`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-113">Update the visual API to version 1.11.0 to use `registerOnSelectCallback` in an instance of `SelectionManager`.</span></span> <span data-ttu-id="0cd5d-114">Se necesita para los objetos visuales que no sean de filtro y que usen `SelectionManager` estándar en lugar de `InteractivityService`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-114">It's required for non-filter visuals that use the plain `SelectionManager` rather than `InteractivityService`.</span></span>

### <a name="how-power-bi-visuals-interact-with-power-bi-in-report-bookmarks"></a><span data-ttu-id="0cd5d-115">Interacción de los objetos visuales de Power BI con Power BI en los marcadores de un informe</span><span class="sxs-lookup"><span data-stu-id="0cd5d-115">How Power BI visuals interact with Power BI in report bookmarks</span></span>

<span data-ttu-id="0cd5d-116">Veamos el siguiente escenario: quiere crear varios marcadores en la página de informes, con un estado de selección diferente en cada marcador.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-116">Let's consider the following scenario: you want to create several bookmarks on the report page, with a different selection state in each bookmark.</span></span>

<span data-ttu-id="0cd5d-117">En primer lugar, seleccionará un punto de datos en el objeto visual.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-117">First, you select a data point in your visual.</span></span> <span data-ttu-id="0cd5d-118">El objeto visual interactúa con Power BI y otros objetos visuales y pasa las selecciones al host.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-118">The visual interacts with Power BI and other visuals by passing selections to the host.</span></span> <span data-ttu-id="0cd5d-119">Luego, seleccione **Agregar** en el panel **Marcador** y Power BI guarda las selecciones actuales del nuevo marcador.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-119">You then select **Add** in the **Bookmark** pane, and Power BI saves the current selections for the new bookmark.</span></span>

<span data-ttu-id="0cd5d-120">Esto ocurre cada vez que usted cambia la selección y agrega nuevos marcadores.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-120">It happens repeatedly as you change the selection and add new bookmarks.</span></span> <span data-ttu-id="0cd5d-121">Después de crear los marcadores, puede cambiar entre ellos.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-121">After you create the bookmarks, you can switch between them.</span></span>

<span data-ttu-id="0cd5d-122">Al seleccionar un marcador, Power BI restaura el estado de selección o filtro guardado y lo pasa a los objetos visuales.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-122">When you select a bookmark, Power BI restores the saved filter or selection state and passes it to the visuals.</span></span> <span data-ttu-id="0cd5d-123">Los demás objetos visuales se resaltan o filtran según el estado almacenado en el marcador.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-123">Other visuals are highlighted or filtered according to the state that's stored in the bookmark.</span></span> <span data-ttu-id="0cd5d-124">El host de Power BI es responsable de las acciones.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-124">The Power BI host is responsible for the actions.</span></span> <span data-ttu-id="0cd5d-125">El objeto visual es responsable de reflejar correctamente el nuevo estado de selección (por ejemplo, cambiar el color de los puntos de datos representados).</span><span class="sxs-lookup"><span data-stu-id="0cd5d-125">Your visual is responsible for correctly reflecting the new selection state (for example, for changing the colors of rendered data points).</span></span>

<span data-ttu-id="0cd5d-126">El nuevo estado de selección se comunica al objeto visual a través del método `update`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-126">The new selection state is communicated to the visual through the `update` method.</span></span> <span data-ttu-id="0cd5d-127">El argumento `options` contiene una propiedad especial, `options.jsonFilters`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-127">The `options` argument contains a special property, `options.jsonFilters`.</span></span> <span data-ttu-id="0cd5d-128">Su propiedad JSONFilter puede contener `Advanced Filter` y `Tuple Filter`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-128">Its JSONFilter property can contain `Advanced Filter` and `Tuple Filter`.</span></span>

<span data-ttu-id="0cd5d-129">El objeto visual debe restaurar los valores de filtro para mostrar el estado correspondiente del objeto visual para el marcador seleccionado.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-129">The visual should restore the filter values to display the corresponding state of the visual for the selected bookmark.</span></span> <span data-ttu-id="0cd5d-130">O bien, si el objeto visual solo usa selecciones, puede utilizar la llamada a la función de devolución de llamada especial como el método `registerOnSelectCallback` de ISelectionManager.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-130">Or, if the visual uses selections only, you can use the special callback function call that's registered as the `registerOnSelectCallback` method of ISelectionManager.</span></span>

### <a name="visuals-with-selection"></a><span data-ttu-id="0cd5d-131">Objetos visuales con selecciones</span><span class="sxs-lookup"><span data-stu-id="0cd5d-131">Visuals with Selection</span></span>

<span data-ttu-id="0cd5d-132">Si el objeto visual interactúa con otros objetos visuales mediante [Selection](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md), puede agregar marcadores de una de estas dos maneras:</span><span class="sxs-lookup"><span data-stu-id="0cd5d-132">If your visual interacts with other visuals by using [Selection](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md), you can add bookmarks in either of two ways:</span></span>

* <span data-ttu-id="0cd5d-133">Si el objeto visual aún no ha usado [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md), puede utilizar el método `FilterManager.restoreSelectionIds`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-133">If the visual hasn't already used [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md), you can use the `FilterManager.restoreSelectionIds` method.</span></span>

* <span data-ttu-id="0cd5d-134">Si el objeto visual ya ha usado [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) para administrar selecciones, debe utilizar el método `applySelectionFromFilter` en la instancia de `InteractivityService`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-134">If the visual already uses [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) to manage selections, you should use the `applySelectionFromFilter` method in the instance of `InteractivityService`.</span></span>

#### <a name="use-iselectionmanagerregisteronselectcallback"></a><span data-ttu-id="0cd5d-135">Uso de ISelectionManager.registerOnSelectCallback</span><span class="sxs-lookup"><span data-stu-id="0cd5d-135">Use ISelectionManager.registerOnSelectCallback</span></span>

<span data-ttu-id="0cd5d-136">Al seleccionar un marcador, Power BI llama al método `callback` del objeto visual con las selecciones correspondientes.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-136">When you select a bookmark, Power BI calls the `callback` method of the visual with the corresponding selections.</span></span> 

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        //called when a selection was set by Power BI
    });
);
```

<span data-ttu-id="0cd5d-137">Supongamos que tiene un punto de datos en el objeto visual que ha creado en el método [visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74).</span><span class="sxs-lookup"><span data-stu-id="0cd5d-137">Let's assume that you have a data point in your visual that was created in the [visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74) method.</span></span>

<span data-ttu-id="0cd5d-138">Además, `datapoints` tiene el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="0cd5d-138">And `datapoints` looks like:</span></span>

```typescript
visualDataPoints.push({
    category: categorical.categories[0].values[i],
    color: getCategoricalObjectValue<Fill>(categorical.categories[0], i, 'colorSelector', 'fill', defaultColor).solid.color,
    selectionId: host.createSelectionIdBuilder()
        .withCategory(categorical.categories[0], i)
        .createSelectionId(),
    selected: false
});
```

<span data-ttu-id="0cd5d-139">Ahora tiene `visualDataPoints` como puntos de datos y la matriz `ids` que ha pasado a la función `callback`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-139">You now have `visualDataPoints` as your data points and the `ids` array passed to the `callback` function.</span></span>

<span data-ttu-id="0cd5d-140">En este punto, el objeto visual debe comparar la matriz de `ISelectionId[]` con la selección de la matriz de `visualDataPoints` y marcar como seleccionados los puntos de datos que correspondan.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-140">At this point, the visual should compare the `ISelectionId[]` array with the selections in your `visualDataPoints` array and mark the corresponding data points as selected.</span></span>

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        visualDataPoints.forEach(dataPoint => {
            ids.forEach(bookmarkSelection => {
                if (bookmarkSelection.equals(dataPoint.selectionId)) {
                    dataPoint.selected = true;
                }
            });
        });
    });
);
```

<span data-ttu-id="0cd5d-141">Tras actualizarlos, los puntos de datos reflejarán el estado de selección actual almacenado en el objeto `filter`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-141">After you update the data points, they'll reflect the current selection state that's stored in the `filter` object.</span></span> <span data-ttu-id="0cd5d-142">Luego, cuando los puntos de datos se representen, el estado de selección del objeto visual personalizado coincidirá con el del marcador.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-142">Then, when the data points are rendered, the custom visual's selection state will match the state of the bookmark.</span></span>

### <a name="use-interactivityservice-for-control-selections-in-the-visual"></a><span data-ttu-id="0cd5d-143">Uso de InteractivityService para el control de selecciones en el objeto visual</span><span class="sxs-lookup"><span data-stu-id="0cd5d-143">Use InteractivityService for control selections in the visual</span></span>

<span data-ttu-id="0cd5d-144">Si el objeto visual usa `InteractivityService`, no tiene que hacer nada más para admitir los marcadores en el objeto visual.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-144">If your visual uses `InteractivityService`, you don't need any additional actions to support the bookmarks in your visual.</span></span>

<span data-ttu-id="0cd5d-145">Al seleccionar marcadores, la utilidad controla el estado de selección del objeto visual.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-145">When you select bookmarks, the utility handles the visual's selection state.</span></span>

### <a name="visuals-with-a-filter"></a><span data-ttu-id="0cd5d-146">Objetos visuales con un filtro</span><span class="sxs-lookup"><span data-stu-id="0cd5d-146">Visuals with a filter</span></span>

<span data-ttu-id="0cd5d-147">Supongamos que el objeto visual crea un filtro de datos por intervalo de fechas.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-147">Let's assume that the visual creates a filter of data by date range.</span></span> <span data-ttu-id="0cd5d-148">Tiene `startDate` y `endDate` como las fechas de inicio y finalización del intervalo.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-148">You have `startDate` and `endDate` as the start and end dates of the range.</span></span>

<span data-ttu-id="0cd5d-149">El objeto visual crea un filtro avanzado y llama al método de host `applyJsonFilter` para filtrar los datos según las condiciones pertinentes.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-149">The visual creates an advanced filter and calls the host method `applyJsonFilter` to filter data by the relevant conditions.</span></span>

<span data-ttu-id="0cd5d-150">El destino es la tabla que se usa para filtrar.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-150">The target is the table that's used for filtering.</span></span>

```typescript
import { AdvancedFilter } from "powerbi-models";

const filter: IAdvancedFilter = new AdvancedFilter(
    target,
    "And",
    {
        operator: "GreaterThanOrEqual",
        value: startDate
            ? startDate.toJSON()
            : null
    },
    {
        operator: "LessThanOrEqual",
        value: endDate
            ? endDate.toJSON()
            : null
    });

this.host.applyJsonFilter(
    filter,
    "general",
    "filter",
    (startDate && endDate)
        ? FilterAction.merge
        : FilterAction.remove
);
```

<span data-ttu-id="0cd5d-151">Cada vez que selecciona un marcador, el objeto visual personalizado recibe una llamada de `update`.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-151">Each time you select a bookmark, the custom visual gets an `update` call.</span></span>

<span data-ttu-id="0cd5d-152">El objeto visual personalizado debe comprobar el filtro en el objeto:</span><span class="sxs-lookup"><span data-stu-id="0cd5d-152">The custom visual should check the filter in the object:</span></span>

```typescript
const filter: IAdvancedFilter = FilterManager.restoreFilter(
    && options.jsonFilters
    && options.jsonFilters[0] as any
) as IAdvancedFilter;
```

<span data-ttu-id="0cd5d-153">Si el objeto `filter` no es NULL, el objeto visual debe restaurar las condiciones de filtro del objeto:</span><span class="sxs-lookup"><span data-stu-id="0cd5d-153">If the `filter` object isn't null, the visual should restore the filter conditions from the object:</span></span>

```typescript
const jsonFilters: AdvancedFilter = this.options.jsonFilters as AdvancedFilter[];

if (jsonFilters
    && jsonFilters[0]
    && jsonFilters[0].conditions
    && jsonFilters[0].conditions[0]
    && jsonFilters[0].conditions[1]
) {
    const startDate: Date = new Date(`${jsonFilters[0].conditions[0].value}`);
    const endDate: Date = new Date(`${jsonFilters[0].conditions[1].value}`);

    // apply restored conditions
} else {
    // apply default settings
}
```

<span data-ttu-id="0cd5d-154">Después, el objeto visual debe cambiar su estado interno para reflejar las condiciones actuales.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-154">After that, the visual should change its internal state to reflect the current conditions.</span></span> <span data-ttu-id="0cd5d-155">El estado interno incluye los puntos de datos y los objetos de visualización (líneas, rectángulos, etc.).</span><span class="sxs-lookup"><span data-stu-id="0cd5d-155">The internal state includes the data points and visualization objects (lines, rectangles, and so on).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0cd5d-156">En el caso de los marcadores de informe, el objeto visual no tiene que llamar a `applyJsonFilter` para filtrar los demás objetos visuales.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-156">In the report bookmarks scenario, the visual shouldn't call `applyJsonFilter` to filter the other visuals.</span></span> <span data-ttu-id="0cd5d-157">Power BI se encargará de filtrarlos.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-157">They will already be filtered by Power BI.</span></span>

<span data-ttu-id="0cd5d-158">El objeto visual de segmentación de la escala de tiempo cambia el selector de intervalo a los intervalos de datos correspondientes.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-158">The Timeline Slicer visual changes the range selector to the corresponding data ranges.</span></span>

<span data-ttu-id="0cd5d-159">Para obtener más información, consulte el [repositorio de segmentación de escala de tiempo](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df).</span><span class="sxs-lookup"><span data-stu-id="0cd5d-159">For more information, see the [Timeline Slicer repository](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df).</span></span>

### <a name="filter-the-state-to-save-visual-properties-in-bookmarks"></a><span data-ttu-id="0cd5d-160">Filtro del estado para guardar las propiedades del objeto visual en los marcadores</span><span class="sxs-lookup"><span data-stu-id="0cd5d-160">Filter the state to save visual properties in bookmarks</span></span>

<span data-ttu-id="0cd5d-161">La propiedad `filterState` crea una propiedad de una parte del filtrado.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-161">The `filterState` property makes a property of a part of filtering.</span></span> <span data-ttu-id="0cd5d-162">El objeto visual puede almacenar varios valores en marcadores.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-162">The visual can store a variety of values in bookmarks.</span></span>

<span data-ttu-id="0cd5d-163">Para guardar el valor de la propiedad como un estado de filtro, marque la propiedad del objeto como `"filterState": true` en el archivo *capabilities.json*.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-163">To save the property value as a filter state, mark the object property as `"filterState": true` in the *capabilities.json* file.</span></span>

<span data-ttu-id="0cd5d-164">Por ejemplo, la segmentación de la escala de tiempo almacena los valores de la propiedad `Granularity` en un filtro.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-164">For example, the Timeline Slicer stores the `Granularity` property values in a filter.</span></span> <span data-ttu-id="0cd5d-165">Permite cambiar la granularidad actual mientras cambia los marcadores.</span><span class="sxs-lookup"><span data-stu-id="0cd5d-165">It allows the current granularity to change as you change the bookmarks.</span></span>

<span data-ttu-id="0cd5d-166">Para obtener más información, consulte el [repositorio de segmentación de escala de tiempo](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334).</span><span class="sxs-lookup"><span data-stu-id="0cd5d-166">For more information, see the [Timeline Slicer repository](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334).</span></span>
