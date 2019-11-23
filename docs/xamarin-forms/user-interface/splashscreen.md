---
title: Tela inicial do Xamarin. Forms
description: Este artigo explica como criar uma tela inicial para um aplicativo Xamarin. Forms.
ms.prod: xamarin
ms.assetId: 338C8F60-90F2-4B3D-BB47-7F846AE8DC6C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/1/2019
ms.openlocfilehash: 2624c3f35a9cfac122a0b36ea8c1684d30f57824
ms.sourcegitcommit: 5110d1279809a2af58d3d66cd14c78113bb51436
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035751"
---
# <a name="xamarinforms-splash-screen"></a>Tela inicial do Xamarin. Forms

Os aplicativos geralmente têm um atraso de inicialização enquanto o aplicativo conclui seu processo de inicialização. Os desenvolvedores podem querer oferecer uma experiência com marca, normalmente chamada de tela inicial, enquanto o aplicativo está sendo iniciado. Este artigo explica como criar telas de abertura para aplicativos Xamarin. Forms.

O Xamarin. Forms é inicializado em cada plataforma após a conclusão da sequência de inicialização nativa. Xamarin. Forms é inicializado:

- No método `OnCreate` da classe `MainActivity` no Android.
- No método `FinishedLaunching` da classe `AppDelegate` no iOS.
- No método `OnLaunched` da classe `App` no UWP.

A tela inicial deve ser mostrada assim que possível quando o aplicativo é iniciado, mas o Xamarin. Forms não é inicializado até o final da sequência de inicialização, o que significa que a tela inicial deve ser implementada fora do Xamarin. Forms em cada plataforma. As seções a seguir explicam como criar telas de abertura em cada plataforma.

## <a name="xamarinforms-android-splash-screen"></a>Tela inicial do Xamarin. Forms para Android

A criação de uma tela inicial no Android requer a criação de um `Activity` inicial como o `MainLauncher` com um tema especial. Assim que o `Activity` inicial é iniciado, ele inicia o `Activity` principal com o tema do aplicativo normal.

Para obter mais informações sobre telas de abertura no Xamarin. Android, consulte a [tela inicial do xamarin. Android](~/android/user-interface/splash-screen.md).

## <a name="xamarinforms-ios-splash-screen"></a>Tela inicial do iOS para o Xamarin. Forms

Uma tela inicial no iOS é conhecida como tela de inicialização. A criação de uma tela de inicialização no iOS requer a criação de um Storyboard que defina a interface do usuário da tela de inicialização e, em seguida, a configuração do storyboard como a tela de inicialização no **info. plist**.

Para obter mais informações sobre telas de inicialização no Xamarin. iOS, consulte [tela de inicialização do xamarin. Ios](~/ios/app-fundamentals/images-icons/launch-screens.md).

## <a name="xamarinforms-uwp-splash-screen"></a>Tela inicial da UWP do Xamarin. Forms

Em UWP, o **Package. appxmanifest** contém uma guia **ativos visuais** com um submenu **tela inicial** . Os elementos gráficos da tela inicial podem ser especificados neste menu:

[tela inicial de configuração de ![no UWP](splashscreen-images/uwp-splashscreen-cropped.png)](splashscreen-images/uwp-splashscreen.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Tela inicial do Xamarin. Android](~/android/user-interface/splash-screen.md)
- [Tela de inicialização do Xamarin. iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)
