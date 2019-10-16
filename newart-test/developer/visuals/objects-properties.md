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
# <a name="objects-and-properties-of-power-bi-visuals"></a><span data-ttu-id="36c29-103">Objetos y propiedades de objetos visuales de Power BI</span><span class="sxs-lookup"><span data-stu-id="36c29-103">Objects and properties of Power BI visuals</span></span>

<span data-ttu-id="36c29-104">Los objetos describen propiedades personalizables que están asociadas a un objeto visual.</span><span class="sxs-lookup"><span data-stu-id="36c29-104">Objects describe customizable properties that are associated with a visual.</span></span> <span data-ttu-id="36c29-105">Un objeto puede tener varias propiedades, y cada propiedad tiene un tipo asociado que describe cuál será la propiedad.</span><span class="sxs-lookup"><span data-stu-id="36c29-105">An object can have multiple properties, and each property has an associated type that describes what the property will be.</span></span> <span data-ttu-id="36c29-106">En este artículo se proporciona información sobre los objetos y los tipos de propiedad.</span><span class="sxs-lookup"><span data-stu-id="36c29-106">This article provides information about objects and property types.</span></span>

<span data-ttu-id="36c29-107">`myCustomObject` es el nombre interno usado para hacer referencia al objeto en `dataView` y `enumerateObjectInstances`.</span><span class="sxs-lookup"><span data-stu-id="36c29-107">`myCustomObject` is the internal name that's used to reference the object within `dataView` and `enumerateObjectInstances`.</span></span>

```json
"objects": {
    "myCustomObject": {
        "displayName": "My Object Name",
        "properties": { ... }
    }
}
```

## <a name="display-name"></a><span data-ttu-id="36c29-108">Nombre para mostrar</span><span class="sxs-lookup"><span data-stu-id="36c29-108">Display name</span></span>

<span data-ttu-id="36c29-109">`displayName` es el nombre que se mostrará en el panel de propiedades.</span><span class="sxs-lookup"><span data-stu-id="36c29-109">`displayName` is the name that will be shown in the property pane.</span></span>

## <a name="properties"></a><span data-ttu-id="36c29-110">Propiedades</span><span class="sxs-lookup"><span data-stu-id="36c29-110">Properties</span></span>

<span data-ttu-id="36c29-111">`properties` es un mapa de propiedades que define el desarrollador.</span><span class="sxs-lookup"><span data-stu-id="36c29-111">`properties` is a map of properties that are defined by the developer.</span></span>

```json
"properties": {
    "myFirstProperty": {
        "displayName": "firstPropertyName",
        "type": ValueTypeDescriptor | StructuralTypeDescriptor
    }
}
```

> [!NOTE]
> <span data-ttu-id="36c29-112">`show` es una propiedad especial que habilita un botón de alternancia para cambiar el valor del objeto.</span><span class="sxs-lookup"><span data-stu-id="36c29-112">`show` is a special property that enables a switch to toggle the object.</span></span>

<span data-ttu-id="36c29-113">Ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36c29-113">Example:</span></span>

```json
"properties": {
    "show": {
        "displayName": "My Property Switch",
        "type": {"bool": true}
    }
}
```

### <a name="property-types"></a><span data-ttu-id="36c29-114">Tipos de propiedad</span><span class="sxs-lookup"><span data-stu-id="36c29-114">Property types</span></span>

<span data-ttu-id="36c29-115">Hay dos tipos de propiedades: `ValueTypeDescriptor` y `StructuralTypeDescriptor`.</span><span class="sxs-lookup"><span data-stu-id="36c29-115">There are two property types: `ValueTypeDescriptor` and `StructuralTypeDescriptor`.</span></span>

#### <a name="value-type-descriptor"></a><span data-ttu-id="36c29-116">Descriptor de tipo de valor</span><span class="sxs-lookup"><span data-stu-id="36c29-116">Value type descriptor</span></span>

<span data-ttu-id="36c29-117">Los tipos `ValueTypeDescriptor` son en su mayoría primitivos y suelen usarse como un objeto estático.</span><span class="sxs-lookup"><span data-stu-id="36c29-117">`ValueTypeDescriptor` types are mostly primitive and are ordinarily used as a static object.</span></span>

<span data-ttu-id="36c29-118">Estos son algunos de los elementos comunes de `ValueTypeDescriptor`:</span><span class="sxs-lookup"><span data-stu-id="36c29-118">Here are some of the common `ValueTypeDescriptor` elements:</span></span>

```typescript
export interface ValueTypeDescriptor {
    text?: boolean;
    numeric?: boolean;
    integer?: boolean;
    bool?: boolean;
}
```

#### <a name="structural-type-descriptor"></a><span data-ttu-id="36c29-119">Descriptor de tipo estructural</span><span class="sxs-lookup"><span data-stu-id="36c29-119">Structural type descriptor</span></span>

<span data-ttu-id="36c29-120">Los tipos `StructuralTypeDescriptor` se usan principalmente para objetos enlazados a datos.</span><span class="sxs-lookup"><span data-stu-id="36c29-120">`StructuralTypeDescriptor` types are mostly used for data-bound objects.</span></span>
<span data-ttu-id="36c29-121">El tipo más común de `StructuralTypeDescriptor` es *fill*.</span><span class="sxs-lookup"><span data-stu-id="36c29-121">The most common `StructuralTypeDescriptor` type is *fill*.</span></span>

```typescript
export interface StructuralTypeDescriptor {
    fill?: FillTypeDescriptor;
}
```

## <a name="gradient-property"></a><span data-ttu-id="36c29-122">Propiedad de degradado</span><span class="sxs-lookup"><span data-stu-id="36c29-122">Gradient property</span></span>

<span data-ttu-id="36c29-123">La propiedad de degradado no se puede establecer como una propiedad estándar.</span><span class="sxs-lookup"><span data-stu-id="36c29-123">The gradient property is a property that can't be set as a standard property.</span></span> <span data-ttu-id="36c29-124">En su lugar, tiene que configurar una regla para sustituir la propiedad del selector de colores (tipo *fill*).</span><span class="sxs-lookup"><span data-stu-id="36c29-124">Instead, you need to set a rule for the substitution of the color picker property (*fill* type).</span></span>

<span data-ttu-id="36c29-125">En el código siguiente se muestra un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="36c29-125">An example is shown in the following code:</span></span>

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

<span data-ttu-id="36c29-126">Preste atención a las propiedades *fill* y *fillRule*.</span><span class="sxs-lookup"><span data-stu-id="36c29-126">Pay attention to the *fill* and *fillRule* properties.</span></span> <span data-ttu-id="36c29-127">La primera es el selector de colores y la segunda es la regla de sustitución del degradado que reemplazará a la *propiedad fill*, `visually`, cuando se cumplan las condiciones de la regla.</span><span class="sxs-lookup"><span data-stu-id="36c29-127">The first is the color picker, and the second is the substitution rule for the gradient that will replace the *fill property*, `visually`, when the rule conditions are met.</span></span>

<span data-ttu-id="36c29-128">Este vínculo entre la propiedad *fill* y la regla de sustitución se establece en la sección `"rule"`>`"output"` de la propiedad *fillRule*.</span><span class="sxs-lookup"><span data-stu-id="36c29-128">This link between the *fill* property and the substitution rule is set in the `"rule"`>`"output"` section of the *fillRule* property.</span></span>

<span data-ttu-id="36c29-129">La propiedad `"Rule"`>`"InputRole"` establece el rol de datos que desencadena la regla (condición).</span><span class="sxs-lookup"><span data-stu-id="36c29-129">`"Rule"`>`"InputRole"` property sets which data role triggers the rule (condition).</span></span> <span data-ttu-id="36c29-130">En este ejemplo, si el rol de datos `"Gradient"` contiene datos, la regla se aplica en la propiedad `"fill"`.</span><span class="sxs-lookup"><span data-stu-id="36c29-130">In this example, if data role `"Gradient"` contains data, the rule is applied for the `"fill"` property.</span></span>

<span data-ttu-id="36c29-131">En el siguiente código se muestra un ejemplo del rol de datos que desencadena la regla de relleno (`the last item`):</span><span class="sxs-lookup"><span data-stu-id="36c29-131">An example of the data role that triggers the fill rule (`the last item`) is shown in the following code:</span></span>

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

## <a name="the-enumerateobjectinstances-method"></a><span data-ttu-id="36c29-132">Método enumerateObjectInstances</span><span class="sxs-lookup"><span data-stu-id="36c29-132">The enumerateObjectInstances method</span></span>

<span data-ttu-id="36c29-133">Para usar los objetos correctamente, necesita una función en el objeto visual personalizado denominada `enumerateObjectInstances`.</span><span class="sxs-lookup"><span data-stu-id="36c29-133">To use objects effectively, you need a function in your custom visual called `enumerateObjectInstances`.</span></span> <span data-ttu-id="36c29-134">Esta función rellena el panel de propiedades con objetos y también determina dónde deben enlazarse los objetos en el elemento dataView.</span><span class="sxs-lookup"><span data-stu-id="36c29-134">This function populates the property pane with objects and also determines where your objects should be bound within the dataView.</span></span>  

<span data-ttu-id="36c29-135">Este es el aspecto de una configuración típica:</span><span class="sxs-lookup"><span data-stu-id="36c29-135">Here is what a typical setup looks like:</span></span>

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

### <a name="properties"></a><span data-ttu-id="36c29-136">Propiedades</span><span class="sxs-lookup"><span data-stu-id="36c29-136">Properties</span></span>

<span data-ttu-id="36c29-137">Las propiedades de `enumerateObjectInstances` reflejan las propiedades que ha definido en sus funcionalidades.</span><span class="sxs-lookup"><span data-stu-id="36c29-137">The properties in `enumerateObjectInstances` reflect the properties that you defined in your capabilities.</span></span> <span data-ttu-id="36c29-138">Para consultar un ejemplo, vaya al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="36c29-138">For an example, go to the end of this article.</span></span>

### <a name="objects-selector"></a><span data-ttu-id="36c29-139">Selector de objetos</span><span class="sxs-lookup"><span data-stu-id="36c29-139">Objects selector</span></span>

<span data-ttu-id="36c29-140">El selector de `enumerateObjectInstances` determina dónde se enlaza cada objeto en el elemento dataView.</span><span class="sxs-lookup"><span data-stu-id="36c29-140">The selector in `enumerateObjectInstances` determines where each object is bound in the dataView.</span></span> <span data-ttu-id="36c29-141">Hay cuatro opciones disponibles.</span><span class="sxs-lookup"><span data-stu-id="36c29-141">There are four distinct options.</span></span>

#### <a name="static"></a><span data-ttu-id="36c29-142">static</span><span class="sxs-lookup"><span data-stu-id="36c29-142">static</span></span>

<span data-ttu-id="36c29-143">Este objeto se enlaza a los metadatos `dataviews[index].metadata.objects`, como se muestra aquí.</span><span class="sxs-lookup"><span data-stu-id="36c29-143">This object is bound to metadata `dataviews[index].metadata.objects`, as shown here.</span></span>

```typescript
selector: null
```

#### <a name="columns"></a><span data-ttu-id="36c29-144">columns</span><span class="sxs-lookup"><span data-stu-id="36c29-144">columns</span></span>

<span data-ttu-id="36c29-145">Este objeto se enlaza a columnas con los metadatos `QueryName` correspondientes.</span><span class="sxs-lookup"><span data-stu-id="36c29-145">This object is bound to columns with the matching `QueryName`.</span></span>

```typescript
selector: {
    metadata: 'QueryName'
}
```

#### <a name="selector"></a><span data-ttu-id="36c29-146">selector</span><span class="sxs-lookup"><span data-stu-id="36c29-146">selector</span></span>

<span data-ttu-id="36c29-147">Este objeto se enlaza al elemento para el que ha creado un elemento `selectionID`.</span><span class="sxs-lookup"><span data-stu-id="36c29-147">This object is bound to the element that you created a `selectionID` for.</span></span> <span data-ttu-id="36c29-148">En este ejemplo, daremos por hecho que hemos creado elementos `selectionID` para algunos puntos de datos y que los estamos recorriendo en bucle.</span><span class="sxs-lookup"><span data-stu-id="36c29-148">In this example, let's assume that we've created `selectionID`s for some data points, and that we're looping through them.</span></span>

```typescript
for (let dataPoint in dataPoints) {
    ...
    selector: dataPoint.selectionID.getSelector()
}
```

#### <a name="scope-identity"></a><span data-ttu-id="36c29-149">Identidad del ámbito</span><span class="sxs-lookup"><span data-stu-id="36c29-149">Scope identity</span></span>

<span data-ttu-id="36c29-150">Este objeto se enlaza a valores específicos en la intersección de grupos.</span><span class="sxs-lookup"><span data-stu-id="36c29-150">This object is bound to particular values at the intersection of groups.</span></span> <span data-ttu-id="36c29-151">Por ejemplo, si tiene las categorías `["Jan", "Feb", "March", ...]` y las series `["Small", "Medium", "Large"]`, puede que quiera tener un objeto en la intersección de los valores que coincidan con `Feb` y `Large`.</span><span class="sxs-lookup"><span data-stu-id="36c29-151">For example, if you have categories `["Jan", "Feb", "March", ...]` and series `["Small", "Medium", "Large"]`, you might want to have an object at the intersection of values that match `Feb` and `Large`.</span></span> <span data-ttu-id="36c29-152">Para lograrlo, podría obtener el valor `DataViewScopeIdentity` de ambas columnas, insertarlos en la variable `identities` y, después, usar esta sintaxis con el selector.</span><span class="sxs-lookup"><span data-stu-id="36c29-152">To accomplish this, you could get the `DataViewScopeIdentity` of both columns, push them to variable `identities`, and use this syntax with the selector.</span></span>

```typescript
selector: {
    data: <DataViewScopeIdentity[]>identities
}
```

##### <a name="example"></a><span data-ttu-id="36c29-153">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="36c29-153">Example</span></span>

<span data-ttu-id="36c29-154">En el siguiente ejemplo, se muestra la apariencia de un elemento objectEnumeration para un objeto customColor con una propiedad, *fill*.</span><span class="sxs-lookup"><span data-stu-id="36c29-154">The following example shows what one objectEnumeration would look like for a customColor object with one property, *fill*.</span></span> <span data-ttu-id="36c29-155">Queremos que este objeto se enlace estáticamente a `dataViews[index].metadata.objects`, tal y como se muestra:</span><span class="sxs-lookup"><span data-stu-id="36c29-155">We want this object bound statically to `dataViews[index].metadata.objects`, as shown:</span></span>

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
