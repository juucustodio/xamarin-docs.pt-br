---
title: Recursos da plataforma
description: Tirar proveito dos recursos específicos da plataforma com xamarin. Forms
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/20/2017
ms.openlocfilehash: 454969d8a59128423d632452033b8ba84ee0bc98
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="platform-features"></a>Recursos da plataforma

Xamarin. Forms é extensível e permite que você incorpore recursos específicos de plataforma usando [efeitos](~/xamarin-forms/app-fundamentals/effects/index.md), [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md), o [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)e muito mais.

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

Este guia descreve como implementar o Design de Material Atualizando aplicativos do Android xamarin. Forms existentes.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Indexação de aplicativo e vinculação profunda](deep-linking.md)

Indexação de aplicativo permite que os aplicativos que seriam esquecidos caso contrário, depois de alguns usa permaneça relevante por apareçam nos resultados da pesquisa. Vinculação profunda permite que aplicativos responder a um resultado de pesquisa que contém dados de aplicativos, normalmente, navegando até uma página referenciada por um link profundo.

## <a name="device-classdevicemd"></a>[Classe de dispositivo](device.md)

Como usar o `Device` classe para criar o comportamento específico de plataforma em código compartilhado e a interface do usuário (incluindo usando XAML). Também abrange `BeginInvokeOnMainThread` que é essencial ao modificar controles de interface do usuário de threads em segundo plano.

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

Alguns estilos de iOS podem ser executados por meio de **Info. plist** e `UIAppearance` API. Este guia inclui exemplos de como incluir iOS 9 recursos para o aplicativo do iOS de uma solução xamarin. Forms, incluindo pesquisa do Spotlight Core.

## <a name="macmacmd"></a>[Mac](mac.md)

Xamarin. Forms agora tem suporte para aplicativos macOS visualização.

## <a name="native-formsnative-formsmd"></a>[Formulários nativos](native-forms.md)

Formulários nativo permitem xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivado páginas a serem consumidos por projetos nativos do xamarin, xamarin e Windows UWP (plataforma Universal).

## <a name="native-viewsnative-viewsindexmd"></a>[Exibições nativas](native-views/index.md)

Exibições nativo do iOS, Android e a plataforma Universal do Windows podem ser referenciadas diretamente do xamarin. Forms. Propriedades e manipuladores de eventos podem ser definidos em exibições nativo, e eles podem interagir com exibições xamarin. Forms.

## <a name="platform-specificsplatform-specificsindexmd"></a>[Especificações de plataforma](platform-specifics/index.md)

Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem a necessidade de processadores personalizados ou efeitos.

## <a name="pluginspluginsmd"></a>[Plugins](plugins.md)

Há uma ampla variedade de código-fonte aberto plug-ins disponíveis no Github, Nuget e o armazenamento do componente Xamarin para ajudar a estender aplicativos xamarin. Forms.

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Xamarin. Forms tem suporte para quatro tipos diferentes de projeto do Windows:

* Windows Phone 8 Silverlight (original Windows plataforma suportada por xamarin. Forms),
* Windows Phone 8.1 (WinRT),
* Windows 8.1 (WinRT), e
* Plataforma universal do Windows (Windows 10).

Esta seção descreve as diferenças entre elas e como adicioná-los a uma solução existente do xamarin. Forms.
