---
title: Requisitos do Xamarin.Forms
description: Requisitos de sistema de plataforma e desenvolvimento para Xamarim.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 75e6d25f95a0a3f18c83fe73f67ad4a7797f0924
ms.sourcegitcommit: c024f29ff730ae20c15e99bfe0268a0e1c9d41e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/23/2018
ms.locfileid: "34470324"
---
# <a name="xamarinforms-requirements"></a>Requisitos do Xamarin.Forms

_Requisitos de sistema de plataforma e desenvolvimento para Xamarim.Forms._

Consulte o artigo de [Instalação](~/cross-platform/get-started/installation/index.md) para obter uma visão geral da instalação e das práticas recomendadas aplicáveis entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Aplicativos Xamarin.Forms podem ser gravados para os seguintes sistemas operacionais:

- iOS 8 ou superior
- Android 4.0.3 (API 15) ou superior ([mais detalhes](#android))
- Plataforma Universal do Windows do Windows 10 ([mais detalhes](#windows10))

Presume-se que os desenvolvedores estejam familiarizados com o [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) e com [Projetos Compartilhados](~/cross-platform/app-fundamentals/shared-projects.md).

### <a name="additional-platform-support"></a>Suporte adicional da plataforma

O status dessas plataformas está disponível no [GitHub do Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="platforms-from-earlier-versions"></a>Plataformas de versões anteriores

Não há suporte para uso do Xamarin.Forms 3.0 nas seguintes plataformas:

- *WinRT do Windows 8.1/Windows Phone 8.1*
- *Windows Phone 8 Silverlight*

### <a name="android"></a>Android

Você precisa ter a plataforma das Ferramentas do SDK do Android e da API do Android mais recente instalada. É possível atualizar para as versões mais recentes usando o [Gerenciador de SDK do Android](~/android/get-started/installation/android-sdk.md).

Além disso, a versão de destino/compilação para projetos do Android **deve** ser definida como *Usar a plataforma mais recente instalada*. No entanto, a versão mínima pode ser definida como API 15, de modo que você pode continuar dando suporte a dispositivos que usam o Android 4.0.3 e mais recentes. Esses valores são definidos nas **Opções de Projeto**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**Opções de Projeto > Aplicativo > Propriedades do Aplicativo**

![](installation-images/options-android-vs-sml.png "Opções de build no Visual Studio")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

**Build > Geral**

![](installation-images/options-general-sml.png "Build > Geral")

**Build > Aplicativo Android**

![](installation-images/options-android-sml.png "Build > Aplicativo Android")

-----

## <a name="development-system-requirements"></a>Requisitos do sistema de desenvolvimento

Aplicativos Xamarin.Forms podem ser desenvolvidos no macOS e no Windows. No entanto, o Windows e o Visual Studio são necessários para produzir versões do Windows do aplicativo.

## <a name="mac-system-requirements"></a>Requisitos do sistema do Mac

Você pode usar o Visual Studio para Mac para desenvolver aplicativos Xamarin.Forms no OS X El Capitan (10.11) ou mais recente. Para desenvolver aplicativos iOS, é recomendável ter pelo menos o SDK do iOS 10 e o Xcode 8 instalados.

> [!NOTE]
>  Aplicativos do Windows não podem ser desenvolvidos no macOS.

## <a name="windows-system-requirements"></a>Requisitos do sistema do Windows

Aplicativos Xamarin.Forms para iOS e Android podem ser compilados em qualquer instalação do Windows que dê suporte a desenvolvimento do Xamarin. É necessário ter o Visual Studio 2017 ou mais recente em execução no Windows 7 ou superior. Um Mac em rede é necessário para o desenvolvimento do iOS.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

O desenvolvimento de aplicativos Xamarin.Forms para UWP requer:

- Windows 10 (é recomendável o Fall Creators Update)

- Visual Studio 2017

- [SDK do Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Os projetos da UWP são incluídos em soluções do Xamarin.Forms criadas no Visual Studio 2017, exceto as soluções criadas no Visual Studio 2017 para Mac.
Você pode também [adicionar um aplicativo UWP (Plataforma Universal do Windows)](~/xamarin-forms/platform/windows/installation/index.md) a uma solução Xamarin.Forms existente a qualquer momento.
