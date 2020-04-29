---
title: Como definir um tema para um aplicativo Xamarin.Forms
description: Os aplicativos Xamarin. Forms dão suporte a eles criando um ResourceDictionary para cada tema e, em seguida, carregando os recursos com a extensão de marcação DynamicResource.
ms.prod: xamarin
ms.assetId: BF92AEDD-EF23-4D08-A972-B089066E75F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2020
ms.openlocfilehash: 5988437b40ac875b8b59f9af0f25d4b5c60ded97
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517137"
---
# <a name="theming-a-xamarinforms-application"></a>Um aplicativo Xamarin. Forms

## <a name="theme-an-application"></a>[Aplicar tema a um aplicativo](theming.md)

Eles podem ser implementados em aplicativos Xamarin. Forms criando um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) para cada tema e, em seguida, carregando os recursos com `DynamicResource` a extensão de marcação.

## <a name="respond-to-system-theme-changes"></a>[Responder às alterações do tema do sistema](system-theme-changes.md)

Os dispositivos normalmente incluem temas leves e escuros, que se referem a um amplo conjunto de preferências de aparência que podem ser definidas no nível do sistema operacional. Os aplicativos devem respeitar esses temas do sistema e responder imediatamente quando o tema do sistema for alterado.
