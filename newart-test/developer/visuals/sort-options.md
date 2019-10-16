---
title: Opciones de ordenación para objetos visuales de Power BI
description: En este artículo se describe el comportamiento de ordenación predeterminado de los objetos visuales de Power BI.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: a360f619428a01c4e7a30481374d042b8e368682
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424613"
---
# <a name="sorting-options-for-power-bi-visuals"></a>Opciones de ordenación para objetos visuales de Power BI

En este artículo se describe cómo las opciones de *ordenación* especifican el comportamiento de ordenación de los objetos visuales de Power BI. 

La funcionalidad de ordenación requiere uno de los siguientes parámetros.

## <a name="default-sorting"></a>Ordenación predeterminada

La opción `default` es la forma más simple. Permite ordenar los datos presentados en la sección “DataMappings”. La opción permite ordenar las asignaciones de datos por el usuario y especifica la dirección de ordenación.

```json
    "sorting": {
        "default": {   }
    }
```

![Opciones de ordenación en el menú contextual](./media/sorting.png)

## <a name="implicit-sorting"></a>Ordenación implícita

La ordenación implícita ordena con el parámetro de matriz `clauses`, que describe la ordenación de cada rol de datos. `implicit` significa que el usuario del objeto visual no puede cambiar el criterio de ordenación. Power BI no muestra las opciones de ordenación en el menú del objeto visual, pero sí que ordena los datos según la configuración especificada.

Los parámetros de `clauses` pueden contener varios objetos con dos parámetros:

- `role`: determina `DataMapping` para la ordenación.
- `direction`: determina la dirección de ordenación (1 = ascendente; 2 = descendente).

```json
    "sorting": {
        "implicit": {
            "clauses": [
                {
                    "role": "category",
                    "direction": 1
                },
                {
                    "role": "measure",
                    "direction": 2
                }
            ]
        }
    }
```

## <a name="custom-sorting"></a>Ordenación personalizada

La ordenación personalizada implica que el desarrollador administra la ordenación en el código del objeto visual.
