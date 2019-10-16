---
title: Objetos y propiedades de objetos visuales de Power BI
description: En este artículo se describen las propiedades personalizables de los objetos visuales de Power BI.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b2043c6727e4cf8c5c46c4e277b01a9ea04a969b
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424123"
---
# <a name="objects-and-properties-of-power-bi-visuals"></a>Objetos y propiedades de objetos visuales de Power BI

Los objetos describen propiedades personalizables que están asociadas a un objeto visual. Un objeto puede tener varias propiedades, y cada propiedad tiene un tipo asociado que describe cuál será la propiedad. En este artículo se proporciona información sobre los objetos y los tipos de propiedad.

`myCustomObject` es el nombre interno usado para hacer referencia al objeto en `dataView` y `enumerateObjectInstances`.

```json
"objects": {
    "myCustomObject": {
        "displayName": "My Object Name",
        "properties": { ... }
    }
}
```

## <a name="display-name"></a>Nombre para mostrar

`displayName` es el nombre que se mostrará en el panel de propiedades.

## <a name="properties"></a>Propiedades

`properties` es un mapa de propiedades que define el desarrollador.

```json
"properties": {
    "myFirstProperty": {
        "displayName": "firstPropertyName",
        "type": ValueTypeDescriptor | StructuralTypeDescriptor
    }
}
```

> [!NOTE]
> `show` es una propiedad especial que habilita un botón de alternancia para cambiar el valor del objeto.

Ejemplo:

```json
"properties": {
    "show": {
        "displayName": "My Property Switch",
        "type": {"bool": true}
    }
}
```

### <a name="property-types"></a>Tipos de propiedad

Hay dos tipos de propiedades: `ValueTypeDescriptor` y `StructuralTypeDescriptor`.

#### <a name="value-type-descriptor"></a>Descriptor de tipo de valor

Los tipos `ValueTypeDescriptor` son en su mayoría primitivos y suelen usarse como un objeto estático.

Estos son algunos de los elementos comunes de `ValueTypeDescriptor`:

```typescript
export interface ValueTypeDescriptor {
    text?: boolean;
    numeric?: boolean;
    integer?: boolean;
    bool?: boolean;
}
```

#### <a name="structural-type-descriptor"></a>Descriptor de tipo estructural

Los tipos `StructuralTypeDescriptor` se usan principalmente para objetos enlazados a datos.
El tipo más común de `StructuralTypeDescriptor` es *fill*.

```typescript
export interface StructuralTypeDescriptor {
    fill?: FillTypeDescriptor;
}
```

## <a name="gradient-property"></a>Propiedad de degradado

La propiedad de degradado no se puede establecer como una propiedad estándar. En su lugar, tiene que configurar una regla para sustituir la propiedad del selector de colores (tipo *fill*).

En el código siguiente se muestra un ejemplo:

```json
"properties": {
    "showAllDataPoints": {
        "displayName": "Show all",
        "displayNameKey": "Visual_DataPoint_Show_All",
        "type": {
            "bool": true
        }
    },
    "fill": {
        "displayName": "Fill",
        "displayNameKey": "Visual_Fill",
        "type": {
            "fill": {
                "solid": {
                    "color": true
                }
            }
        }
    },
    "fillRule": {
        "displayName": "Color saturation",
        "displayNameKey": "Visual_ColorSaturation",
        "type": {
            "fillRule": {}
        },
        "rule": {
            "inputRole": "Gradient",
            "output": {
                "property": "fill",
                    "selector": [
                        "Category"
                    ]
            }
        }
    }
}
```

Preste atención a las propiedades *fill* y *fillRule*. La primera es el selector de colores y la segunda es la regla de sustitución del degradado que reemplazará a la *propiedad fill*, `visually`, cuando se cumplan las condiciones de la regla.

Este vínculo entre la propiedad *fill* y la regla de sustitución se establece en la sección `"rule"`>`"output"` de la propiedad *fillRule*.

La propiedad `"Rule"`>`"InputRole"` establece el rol de datos que desencadena la regla (condición). En este ejemplo, si el rol de datos `"Gradient"` contiene datos, la regla se aplica en la propiedad `"fill"`.

En el siguiente código se muestra un ejemplo del rol de datos que desencadena la regla de relleno (`the last item`):

```json
{
    "dataRoles": [
            {
                "name": "Category",
                "kind": "Grouping",
                "displayName": "Details",
                "displayNameKey": "Role_DisplayName_Details"
            },
            {
                "name": "Series",
                "kind": "Grouping",
                "displayName": "Legend",
                "displayNameKey": "Role_DisplayName_Legend"
            },
            {
                "name": "Gradient",
                "kind": "Measure",
                "displayName": "Color saturation",
                "displayNameKey": "Role_DisplayName_Gradient"
            }
    ]
}
```

## <a name="the-enumerateobjectinstances-method"></a>Método enumerateObjectInstances

Para usar los objetos correctamente, necesita una función en el objeto visual personalizado denominada `enumerateObjectInstances`. Esta función rellena el panel de propiedades con objetos y también determina dónde deben enlazarse los objetos en el elemento dataView.  

Este es el aspecto de una configuración típica:

```typescript
public enumerateObjectInstances(options: EnumerateVisualObjectInstancesOptions): VisualObjectInstanceEnumeration {
    let objectName: string = options.objectName;
    let objectEnumeration: VisualObjectInstance[] = [];

    switch( objectName ) {
        case 'myCustomObject':
            objectEnumeration.push({
                objectName: objectName,
                properties: { ... },
                selector: { ... }
            });
            break;
    };

    return objectEnumeration;
}
```

### <a name="properties"></a>Propiedades

Las propiedades de `enumerateObjectInstances` reflejan las propiedades que ha definido en sus funcionalidades. Para consultar un ejemplo, vaya al final de este artículo.

### <a name="objects-selector"></a>Selector de objetos

El selector de `enumerateObjectInstances` determina dónde se enlaza cada objeto en el elemento dataView. Hay cuatro opciones disponibles.

#### <a name="static"></a>static

Este objeto se enlaza a los metadatos `dataviews[index].metadata.objects`, como se muestra aquí.

```typescript
selector: null
```

#### <a name="columns"></a>columns

Este objeto se enlaza a columnas con los metadatos `QueryName` correspondientes.

```typescript
selector: {
    metadata: 'QueryName'
}
```

#### <a name="selector"></a>selector

Este objeto se enlaza al elemento para el que ha creado un elemento `selectionID`. En este ejemplo, daremos por hecho que hemos creado elementos `selectionID` para algunos puntos de datos y que los estamos recorriendo en bucle.

```typescript
for (let dataPoint in dataPoints) {
    ...
    selector: dataPoint.selectionID.getSelector()
}
```

#### <a name="scope-identity"></a>Identidad del ámbito

Este objeto se enlaza a valores específicos en la intersección de grupos. Por ejemplo, si tiene las categorías `["Jan", "Feb", "March", ...]` y las series `["Small", "Medium", "Large"]`, puede que quiera tener un objeto en la intersección de los valores que coincidan con `Feb` y `Large`. Para lograrlo, podría obtener el valor `DataViewScopeIdentity` de ambas columnas, insertarlos en la variable `identities` y, después, usar esta sintaxis con el selector.

```typescript
selector: {
    data: <DataViewScopeIdentity[]>identities
}
```

##### <a name="example"></a>Ejemplo

En el siguiente ejemplo, se muestra la apariencia de un elemento objectEnumeration para un objeto customColor con una propiedad, *fill*. Queremos que este objeto se enlace estáticamente a `dataViews[index].metadata.objects`, tal y como se muestra:

```typescript
objectEnumeration.push({
    objectName: "customColor",
    displayName: "Custom Color",
    properties: {
        fill: {
            solid: {
                color: dataPoint.color
            }
        }
    },
    selector: null
});
```
