---
title: Requisitos do Xamarin.Forms
description: Requisitos de sistema de plataforma e desenvolvimento para Xamarim.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 66785fcd4b38f29ca0358e1e8885e6b7da59f5a7
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854736"
---
# <a name="xamarinforms-requirements"></a>Requisitos do Xamarin.Forms

_Requisitos de sistema de plataforma e desenvolvimento para Xamarim.Forms._

Consulte o artigo de [Instalação](installation/index.md) para obter uma visão geral da instalação e das práticas recomendadas aplicáveis entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Aplicativos Xamarin.Forms podem ser gravados para os seguintes sistemas operacionais:

- iOS 8 ou superior
- Android 4.4 (API 19) ou superior ([mais detalhes](#android))
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

Além disso, a versão de destino/compilação para projetos do Android **deve** ser definida como *Usar a plataforma mais recente instalada*. No entanto, a versão mínima pode ser definida como API 19, de modo que você pode continuar dando suporte a dispositivos que usam Android 4.4 e mais recentes. Esses valores são definidos nas **Opções de Projeto**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

**Opções de Projeto > Aplicativo > Propriedades do Aplicativo**

![Opções de build no Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

**Build > Geral**

![Selecione a estrutura de destino mais recente](requirements-images/options-general-sml.png)

**Build > Aplicativo Android**

![Selecione o mínimo e versões do Android para o aplicativo de destino](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Requisitos do sistema de desenvolvimento

Aplicativos Xamarin.Forms podem ser desenvolvidos no macOS e no Windows. No entanto, o Windows e o Visual Studio são necessários para produzir versões do Windows do aplicativo.

## <a name="mac-system-requirements"></a>Requisitos do sistema do Mac

Você pode usar o Visual Studio para Mac para desenvolver aplicativos xamarin. Forms em macOS High Sierra (10.13) ou mais recente. Para desenvolver aplicativos iOS, é recomendável ter pelo menos o iOS 10 SDK e o Xcode 9 instalado.

> [!NOTE]
>  Aplicativos do Windows não podem ser desenvolvidos no macOS.

## <a name="windows-system-requirements"></a>Requisitos do sistema do Windows

Aplicativos Xamarin.Forms para iOS e Android podem ser compilados em qualquer instalação do Windows que dê suporte a desenvolvimento do Xamarin. É necessário ter o Visual Studio 2017 ou mais recente em execução no Windows 7 ou superior. Um Mac em rede é necessário para o desenvolvimento do iOS.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

O desenvolvimento de aplicativos Xamarin.Forms para UWP requer:

- Windows 10 (versão mais recente recomendada, mínimo Fall Creators Update)

- Visual Studio de 2019 recomendado (Visual Studio 2017 versão 15,8 mínimo)

- [SDK do Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Você pode também [adicionar um aplicativo UWP (Plataforma Universal do Windows)](~/xamarin-forms/platform/windows/installation/index.md) a uma solução Xamarin.Forms existente a qualquer momento.
