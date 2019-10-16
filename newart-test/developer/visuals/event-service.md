---
title: Representación de eventos en objetos visuales de Power BI
description: Los objetos visuales de Power BI pueden notificar a Power BI que están listos para la exportación a PowerPoint o PDF.
author: Yarovinsky
ms.author: alexyar
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b481ce94e5025045466a05d71e30a00f02be7ead
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424303"
---
# <a name="render-events-in-power-bi-visuals"></a><span data-ttu-id="2918f-103">Representación de eventos en objetos visuales de Power BI</span><span class="sxs-lookup"><span data-stu-id="2918f-103">Render events in Power BI visuals</span></span>

<span data-ttu-id="2918f-104">La nueva API consta de tres métodos (`started`, `finished` o `failed`) a los que se debe llamar durante la representación.</span><span class="sxs-lookup"><span data-stu-id="2918f-104">The new API consists of three methods (`started`, `finished`, or `failed`) that should be called during rendering.</span></span>

<span data-ttu-id="2918f-105">Cuando se inicia la representación, el código del objeto visual de Power BI llama al método `renderingStarted` para indicar que se ha iniciado el proceso de representación.</span><span class="sxs-lookup"><span data-stu-id="2918f-105">When rendering starts, the Power BI visual code calls the `renderingStarted` method to indicate that the rendering process has started.</span></span>

<span data-ttu-id="2918f-106">Si la representación se completa correctamente, el código del objeto visual de Power BI llama inmediatamente al método `renderingFinished` para notificar a los clientes de escucha (principalmente *Exportar a PDF* y *Exportar a PowerPoint*) que la imagen del objeto visual está lista para exportarse.</span><span class="sxs-lookup"><span data-stu-id="2918f-106">If rendering is completed successfully, the Power BI visual code immediately calls the `renderingFinished` method, notifying the listeners (primarily, *export to PDF* and *export to PowerPoint*) that the visual's image is ready for export.</span></span>

<span data-ttu-id="2918f-107">Si se produce un problema durante el proceso, se impide que el objeto visual de Power BI se represente correctamente.</span><span class="sxs-lookup"><span data-stu-id="2918f-107">If a problem occurs during the process, the Power BI visual is prevented from being rendered successfully.</span></span> <span data-ttu-id="2918f-108">Para notificar a los clientes de escucha que el proceso de representación no se ha completado, el código del objeto visual de Power BI debe llamar al método `renderingFailed`.</span><span class="sxs-lookup"><span data-stu-id="2918f-108">To notify the listeners that the rendering process hasn't been completed, the Power BI visual code should call the `renderingFailed` method.</span></span> <span data-ttu-id="2918f-109">Este método también proporciona una cadena opcional para proporcionar un motivo del error.</span><span class="sxs-lookup"><span data-stu-id="2918f-109">This method also provides an optional string to provide a reason for the failure.</span></span>

## <a name="usage"></a><span data-ttu-id="2918f-110">Usage (Uso)</span><span class="sxs-lookup"><span data-stu-id="2918f-110">Usage</span></span>

```typescript
export interface IVisualHost extends extensibility.IVisualHost {
    eventService: IVisualEventService ;
}

/**
 * An interface for reporting rendering events
 */
export interface IVisualEventService {
    /**
     * Should be called just before the actual rendering starts, 
     * usually at the start of the update method
     *
     * @param options - the visual update options received as an update parameter
     */
    renderingStarted(options: VisualUpdateOptions): void;

    /**
     * Should be called immediately after rendering finishes successfully
     * 
     * @param options - the visual update options received as an update parameter
     */
    renderingFinished(options: VisualUpdateOptions): void;

    /**
     * Called when rendering fails, with an optional reason string
     * 
     * @param options - the visual update options received as an update parameter
     * @param reason - the optional failure reason string
     */
    renderingFailed(options: VisualUpdateOptions, reason?: string): void;
}
```

### <a name="sample-the-visual-displays-no-animations"></a><span data-ttu-id="2918f-111">Ejemplo: El objeto visual no muestra animaciones</span><span class="sxs-lookup"><span data-stu-id="2918f-111">Sample: The visual displays no animations</span></span>

```typescript
    export class Visual implements IVisual {
        ...
        private events: IVisualEventService;
        ...

        constructor(options: VisualConstructorOptions) {
            ...
            this.events = options.host.eventService;
            ...
        }

        public update(options: VisualUpdateOptions) {
            this.events.renderingStarted(options);
            ...
            this.events.renderingFinished(options);
        }
```

### <a name="sample-the-visual-displays-animations"></a><span data-ttu-id="2918f-112">Ejemplo: El objeto visual muestra animaciones</span><span class="sxs-lookup"><span data-stu-id="2918f-112">Sample: The visual displays animations</span></span>

<span data-ttu-id="2918f-113">Si el objeto visual tiene animaciones o funciones asincrónicas para la representación, se debe llamar al método `renderingFinished` después de la animación o dentro de la función asincrónica.</span><span class="sxs-lookup"><span data-stu-id="2918f-113">If the visual has animations or async functions for rendering, the `renderingFinished` method should be called after the animation or inside async function.</span></span>

```typescript
    export class Visual implements IVisual {
        ...
        private events: IVisualEventService;
        private element: HTMLElement;
        ...

        constructor(options: VisualConstructorOptions) {
            ...
            this.events = options.host.eventService;
            this.element = options.element;
            ...
        }

        public update(options: VisualUpdateOptions) {
            this.events.renderingStarted(options);
            ...
            // Learn more at https://github.com/d3/d3-transition/blob/master/README.md#transition_end
            d3.select(this.element).transition().duration(100).style("opacity","0").end().then(() => {
                // renderingFinished called after transition end
                this.events.renderingFinished(options);
            });
        }
```

## <a name="rendering-events-for-visual-certification"></a><span data-ttu-id="2918f-114">Representación de eventos para la certificación de objetos visuales</span><span class="sxs-lookup"><span data-stu-id="2918f-114">Rendering events for visual certification</span></span>

<span data-ttu-id="2918f-115">Un requisito de certificación de objetos visuales es la compatibilidad del objeto visual con la representación de eventos.</span><span class="sxs-lookup"><span data-stu-id="2918f-115">One requirement of visuals certification is the support of rendering events by the visual.</span></span> <span data-ttu-id="2918f-116">Para obtener más información, consulte [Requisitos de certificación](https://docs.microsoft.com/power-bi/power-bi-custom-visuals-certified?#certification-requirements).</span><span class="sxs-lookup"><span data-stu-id="2918f-116">For more information, see [certification requirements](https://docs.microsoft.com/power-bi/power-bi-custom-visuals-certified?#certification-requirements).</span></span>
