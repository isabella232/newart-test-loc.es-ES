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
# <a name="add-bookmark-support-for-power-bi-visuals"></a>Adición de compatibilidad de los marcadores para los objetos visuales de Power BI

Con los marcadores de los informes de Power BI, puede capturar una vista configurada de una página de informes, el estado de selección y el estado de filtrado del objeto visual. Con todo, los objetos visuales de Power BI requieren una acción más para admitir los marcadores y poder reaccionar correctamente a los cambios.

Para obtener más información sobre los marcadores, consulte [Uso de marcadores para compartir información detallada y crear historias en Power BI](https://docs.microsoft.com/power-bi/desktop-bookmarks).

## <a name="report-bookmarks-support-in-your-visual"></a>Compatibilidad de los marcadores de un informe en un objeto visual

Si el objeto visual interactúa con otros objetos visuales, selecciona puntos de datos o filtra otros objetos visuales, tiene que restaurar el estado de las propiedades.

## <a name="add-report-bookmarks-support"></a>Adición de la compatibilidad con los marcadores de un informe

1. Instale (o actualice) la utilidad requerida: [powerbi-visuals-utils-interactivityutils](https://github.com/Microsoft/PowerBI-visuals-utils-interactivityutils/), versión 3.0.0 o posteriores. Contiene clases adicionales para la manipulación con el filtro o la selección de estado. Se necesita para los objetos visuales de filtro y cualquiera que use `InteractivityService`.

2. Actualice la API del objeto visual a la versión 1.11.0 para usar `registerOnSelectCallback` en una instancia de `SelectionManager`. Se necesita para los objetos visuales que no sean de filtro y que usen `SelectionManager` estándar en lugar de `InteractivityService`.

### <a name="how-power-bi-visuals-interact-with-power-bi-in-report-bookmarks"></a>Interacción de los objetos visuales de Power BI con Power BI en los marcadores de un informe

Veamos el siguiente escenario: quiere crear varios marcadores en la página de informes, con un estado de selección diferente en cada marcador.

En primer lugar, seleccionará un punto de datos en el objeto visual. El objeto visual interactúa con Power BI y otros objetos visuales y pasa las selecciones al host. Luego, seleccione **Agregar** en el panel **Marcador** y Power BI guarda las selecciones actuales del nuevo marcador.

Esto ocurre cada vez que usted cambia la selección y agrega nuevos marcadores. Después de crear los marcadores, puede cambiar entre ellos.

Al seleccionar un marcador, Power BI restaura el estado de selección o filtro guardado y lo pasa a los objetos visuales. Los demás objetos visuales se resaltan o filtran según el estado almacenado en el marcador. El host de Power BI es responsable de las acciones. El objeto visual es responsable de reflejar correctamente el nuevo estado de selección (por ejemplo, cambiar el color de los puntos de datos representados).

El nuevo estado de selección se comunica al objeto visual a través del método `update`. El argumento `options` contiene una propiedad especial, `options.jsonFilters`. Su propiedad JSONFilter puede contener `Advanced Filter` y `Tuple Filter`.

El objeto visual debe restaurar los valores de filtro para mostrar el estado correspondiente del objeto visual para el marcador seleccionado. O bien, si el objeto visual solo usa selecciones, puede utilizar la llamada a la función de devolución de llamada especial como el método `registerOnSelectCallback` de ISelectionManager.

### <a name="visuals-with-selection"></a>Objetos visuales con selecciones

Si el objeto visual interactúa con otros objetos visuales mediante [Selection](https://github.com/Microsoft/PowerBI-visuals/blob/master/Tutorial/Selection.md), puede agregar marcadores de una de estas dos maneras:

* Si el objeto visual aún no ha usado [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md), puede utilizar el método `FilterManager.restoreSelectionIds`.

* Si el objeto visual ya ha usado [InteractivityService](https://github.com/Microsoft/powerbi-visuals-utils-interactivityutils/blob/master/docs/api/interactivityService.md) para administrar selecciones, debe utilizar el método `applySelectionFromFilter` en la instancia de `InteractivityService`.

#### <a name="use-iselectionmanagerregisteronselectcallback"></a>Uso de ISelectionManager.registerOnSelectCallback

Al seleccionar un marcador, Power BI llama al método `callback` del objeto visual con las selecciones correspondientes. 

```typescript
this.selectionManager.registerOnSelectCallback(
    (ids: ISelectionId[]) => {
        //called when a selection was set by Power BI
    });
);
```

Supongamos que tiene un punto de datos en el objeto visual que ha creado en el método [visualTransform](https://github.com/Microsoft/PowerBI-visuals-sampleBarChart/blob/master/src/barChart.ts#L74).

Además, `datapoints` tiene el siguiente aspecto:

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

Ahora tiene `visualDataPoints` como puntos de datos y la matriz `ids` que ha pasado a la función `callback`.

En este punto, el objeto visual debe comparar la matriz de `ISelectionId[]` con la selección de la matriz de `visualDataPoints` y marcar como seleccionados los puntos de datos que correspondan.

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

Tras actualizarlos, los puntos de datos reflejarán el estado de selección actual almacenado en el objeto `filter`. Luego, cuando los puntos de datos se representen, el estado de selección del objeto visual personalizado coincidirá con el del marcador.

### <a name="use-interactivityservice-for-control-selections-in-the-visual"></a>Uso de InteractivityService para el control de selecciones en el objeto visual

Si el objeto visual usa `InteractivityService`, no tiene que hacer nada más para admitir los marcadores en el objeto visual.

Al seleccionar marcadores, la utilidad controla el estado de selección del objeto visual.

### <a name="visuals-with-a-filter"></a>Objetos visuales con un filtro

Supongamos que el objeto visual crea un filtro de datos por intervalo de fechas. Tiene `startDate` y `endDate` como las fechas de inicio y finalización del intervalo.

El objeto visual crea un filtro avanzado y llama al método de host `applyJsonFilter` para filtrar los datos según las condiciones pertinentes.

El destino es la tabla que se usa para filtrar.

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

Cada vez que selecciona un marcador, el objeto visual personalizado recibe una llamada de `update`.

El objeto visual personalizado debe comprobar el filtro en el objeto:

```typescript
const filter: IAdvancedFilter = FilterManager.restoreFilter(
    && options.jsonFilters
    && options.jsonFilters[0] as any
) as IAdvancedFilter;
```

Si el objeto `filter` no es NULL, el objeto visual debe restaurar las condiciones de filtro del objeto:

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

Después, el objeto visual debe cambiar su estado interno para reflejar las condiciones actuales. El estado interno incluye los puntos de datos y los objetos de visualización (líneas, rectángulos, etc.).

> [!IMPORTANT]
> En el caso de los marcadores de informe, el objeto visual no tiene que llamar a `applyJsonFilter` para filtrar los demás objetos visuales. Power BI se encargará de filtrarlos.

El objeto visual de segmentación de la escala de tiempo cambia el selector de intervalo a los intervalos de datos correspondientes.

Para obtener más información, consulte el [repositorio de segmentación de escala de tiempo](https://github.com/Microsoft/powerbi-visuals-timeline/commit/606f1152f59f82b5b5a367ff3b117372d129e597?diff=unified#diff-b6ef9a9ac3a3225f8bd0de84bee0a0df).

### <a name="filter-the-state-to-save-visual-properties-in-bookmarks"></a>Filtro del estado para guardar las propiedades del objeto visual en los marcadores

La propiedad `filterState` crea una propiedad de una parte del filtrado. El objeto visual puede almacenar varios valores en marcadores.

Para guardar el valor de la propiedad como un estado de filtro, marque la propiedad del objeto como `"filterState": true` en el archivo *capabilities.json*.

Por ejemplo, la segmentación de la escala de tiempo almacena los valores de la propiedad `Granularity` en un filtro. Permite cambiar la granularidad actual mientras cambia los marcadores.

Para obtener más información, consulte el [repositorio de segmentación de escala de tiempo](https://github.com/microsoft/powerbi-visuals-timeline/commit/8b7d82dd23cd2bd71817f1bc5d1e1732347a185e#diff-290828b604cfa62f1cb310f2e90c52fdR334).
