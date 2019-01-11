---
title: Recursos da plataforma xamarin. Forms
description: Este guia explica como tirar proveito dos recursos específicos da plataforma em aplicativos xamarin. Forms usando uma variedade de técnicas.
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 3f0156926f8d7a31e2e80318d7b05a909f158653
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207720"
---
# <a name="xamarinforms-platform-features"></a>Recursos da plataforma xamarin. Forms

Xamarin. Forms é extensível e permite que você incorpore recursos específicos à plataforma usando [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md), [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md), o [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)e muito mais.

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

Este guia descreve as Android-especificidades da plataforma fornecidas pelo xamarin. Forms e como implementar o Design de Material Atualizando aplicativos existentes do Android do xamarin. Forms.

## <a name="device-classdevicemd"></a>[Classe de dispositivo](device.md)

Como usar o `Device` classe para criar o comportamento específico de plataforma em código compartilhado e a interface do usuário (incluindo usando XAML). Também aborda `BeginInvokeOnMainThread` que é essencial ao modificar controles de interface do usuário de threads em segundo plano.

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

Este guia descreve as iOS-especificidades da plataforma fornecidas pelo xamarin. Forms e como executar adicionais criando estilos por meio do iOS **Info. plist** e o `UIAppearance` API.

## <a name="native-formsnative-formsmd"></a>[Formulários nativos](native-forms.md)

Formulários nativos permitem que o xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivado de páginas a ser consumido por projetos nativos do xamarin. IOS, xamarin. Android e plataforma Universal do Windows (UWP).

## <a name="native-viewsnative-viewsindexmd"></a>[Exibições nativas](native-views/index.md)

Exibições nativas do iOS, Android e plataforma Universal do Windows podem ser referenciadas diretamente do xamarin. Forms. Propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e eles podem interagir com exibições do xamarin. Forms.

## <a name="platform-specificsplatform-specificsindexmd"></a>[Especificidades da plataforma](platform-specifics/index.md)

Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem a necessidade de renderizadores personalizados ou efeitos. Além disso, os fornecedores podem criar seus próprios itens específicos à plataforma com efeitos.

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Este guia descreve as Windows-especificidades da plataforma fornecidas pelo xamarin. Forms e como adicionar um projeto UWP a uma solução xamarin. Forms existente.
