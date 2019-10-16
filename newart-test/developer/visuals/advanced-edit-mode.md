---
title: Modo de edición avanzada en objetos visuales de Power BI
description: En este artículo se describe cómo establecer controles de interfaz de usuario avanzados en objetos visuales de Power BI.
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: da72cf603027bc97060e7a00ed4a4e959a3a92e2
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424203"
---
# <a name="advanced-edit-mode-in-power-bi-visuals"></a><span data-ttu-id="6ac5b-103">Modo de edición avanzada en objetos visuales de Power BI</span><span class="sxs-lookup"><span data-stu-id="6ac5b-103">Advanced edit mode in Power BI visuals</span></span>

<span data-ttu-id="6ac5b-104">Si necesita controles de interfaz de usuario avanzados en el objeto visual de Power BI, puede aprovechar el modo de edición avanzada.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-104">If you require advanced UI controls in your Power BI visual, you can take advantage of advanced edit mode.</span></span> <span data-ttu-id="6ac5b-105">Cuando esté en el modo de edición de informes, seleccione el botón **Editar** para establecer el modo de edición en **Avanzado**.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-105">When you're in report editing mode, you select an **Edit** button to set the edit mode to **Advanced**.</span></span> <span data-ttu-id="6ac5b-106">El objeto visual puede usar la marca `EditMode` para determinar si debe mostrar este control de interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-106">The visual can use the `EditMode` flag to determine whether it should display this UI control.</span></span>

<span data-ttu-id="6ac5b-107">De forma predeterminada, el objeto visual no admite el modo de edición avanzada.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-107">By default, the visual doesn't support advanced edit mode.</span></span> <span data-ttu-id="6ac5b-108">Si se requiere otro comportamiento, puede indicarlo de forma explícita en el archivo *capabilities.json* del objeto visual, al establecer la propiedad `advancedEditModeSupport`.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-108">If a different behavior is required, you can explicitly state this in the visual's *capabilities.json* file by setting the `advancedEditModeSupport` property.</span></span>

<span data-ttu-id="6ac5b-109">Los valores posibles son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="6ac5b-109">The possible values are:</span></span>

- <span data-ttu-id="6ac5b-110">`0`: NotSupported</span><span class="sxs-lookup"><span data-stu-id="6ac5b-110">`0` - NotSupported</span></span>

- <span data-ttu-id="6ac5b-111">`1`: SupportedNoAction</span><span class="sxs-lookup"><span data-stu-id="6ac5b-111">`1` - SupportedNoAction</span></span>

- <span data-ttu-id="6ac5b-112">`2`: SupportedInFocus</span><span class="sxs-lookup"><span data-stu-id="6ac5b-112">`2` - SupportedInFocus</span></span>

## <a name="enter-advanced-edit-mode"></a><span data-ttu-id="6ac5b-113">Acceso al modo de edición avanzada</span><span class="sxs-lookup"><span data-stu-id="6ac5b-113">Enter advanced edit mode</span></span>

<span data-ttu-id="6ac5b-114">Se muestra el botón **Editar** si:</span><span class="sxs-lookup"><span data-stu-id="6ac5b-114">An **Edit** button is displayed if:</span></span>

* <span data-ttu-id="6ac5b-115">La propiedad `advancedEditModeSupport` está establecida en el archivo *capabilities.json* en `SupportedNoAction` o `SupportedInFocus`.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-115">The `advancedEditModeSupport` property is set in the *capabilities.json* file to either `SupportedNoAction` or `SupportedInFocus`.</span></span>

* <span data-ttu-id="6ac5b-116">El objeto visual se ve en el modo de edición de informes.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-116">The visual is viewed in report editing mode.</span></span>

<span data-ttu-id="6ac5b-117">Si falta la propiedad `advancedEditModeSupport` en el archivo *capabilities.json* o está establecida en `NotSupported`, no se muestra el botón **Editar**.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-117">If `advancedEditModeSupport` property is missing from the *capabilities.json* file or set to `NotSupported`, the **Edit** button is not displayed.</span></span>

![Acceso al modo de edición](./media/edit-mode.png)

<span data-ttu-id="6ac5b-119">Al seleccionar **Editar**, el objeto visual recibe una llamada de update() con EditMode establecido en `Advanced`.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-119">When you select **Edit**, the visual gets an update() call with EditMode set to `Advanced`.</span></span> <span data-ttu-id="6ac5b-120">Según el valor que se haya establecido en el archivo *capabilities.json*, se producen las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="6ac5b-120">Depending on the value that's set in the *capabilities.json* file, the following actions occur:</span></span>

* <span data-ttu-id="6ac5b-121">`SupportedNoAction`: el host no requiere más acciones.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-121">`SupportedNoAction`: No further action is required by the host.</span></span>
* <span data-ttu-id="6ac5b-122">`SupportedInFocus`: el host muestra el objeto visual en el modo de enfoque.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-122">`SupportedInFocus`: The host pops out the visual into in focus mode.</span></span>

## <a name="exit-advanced-edit-mode"></a><span data-ttu-id="6ac5b-123">Salida del modo de edición avanzada</span><span class="sxs-lookup"><span data-stu-id="6ac5b-123">Exit advanced edit mode</span></span>

<span data-ttu-id="6ac5b-124">Se muestra el botón **Volver al informe** si:</span><span class="sxs-lookup"><span data-stu-id="6ac5b-124">The **Back to report** button is displayed if:</span></span>

* <span data-ttu-id="6ac5b-125">La propiedad `advancedEditModeSupport` está establecida en el archivo *capabilities.json* en `SupportedInFocus`.</span><span class="sxs-lookup"><span data-stu-id="6ac5b-125">The `advancedEditModeSupport` property is set in the *capabilities.json* file to `SupportedInFocus`.</span></span>
