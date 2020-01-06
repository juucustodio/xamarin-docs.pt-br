---
title: Xamarin. Requisitos de formulários
description: Requisitos do sistema de plataforma e desenvolvimento para Xamarin. Formas.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: d12daa358917399fc5fd1febf02d4f96a647f360
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607848"
---
# <a name="opno-locxamarinforms-requirements"></a>Xamarin. Requisitos de formulários

_Requisitos do sistema de plataforma e desenvolvimento para Xamarin. Formas._

Consulte o artigo de [Instalação](installation/index.md) para obter uma visão geral da instalação e das práticas recomendadas aplicáveis entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Xamarin. Os aplicativos de formulários podem ser escritos para os seguintes sistemas operacionais:

- iOS 9 ou superior
- Android 4.4 (API 19) ou superior ([mais detalhes](#android))
- Plataforma Universal do Windows do Windows 10 ([mais detalhes](#windows10))

No entanto, o Android 5,0 (API 21) é recomendado como a API mínima. Isso garante a compatibilidade total com todas as bibliotecas de suporte do Android, embora ainda visando a maioria dos dispositivos Android.

Supõe-se que os desenvolvedores tenham familiaridade com [.net Standard](~/cross-platform/app-fundamentals/net-standard.md).

### <a name="additional-platform-support"></a>Suporte adicional da plataforma

O status dessas plataformas está disponível no [Xamarin. GitHub de formulários](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support):

- Samsung Tizen
- macOS
- GTK#
- WPF

### <a name="android"></a>Android

Você precisa ter a plataforma das Ferramentas do SDK do Android e da API do Android mais recente instalada. É possível atualizar para as versões mais recentes usando o [Gerenciador de SDK do Android](~/android/get-started/installation/android-sdk.md).

Além disso, a versão de destino/compilação para projetos do Android **deve** ser definida como *Usar a plataforma mais recente instalada*. No entanto, a versão mínima pode ser definida como API 19, de modo que você pode continuar dando suporte a dispositivos que usam Android 4.4 e mais recentes. Esses valores são definidos nas **Opções de Projeto**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

**Opções de Projeto > Aplicativo > Propriedades do Aplicativo**

![Opções de Build do Android no Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

**Build > Geral**

![Selecione a estrutura de destino mais recente](requirements-images/options-general-sml.png)

**Build > Aplicativo Android**

![Selecione as versões mínima e de destino do Android para seu aplicativo](requirements-images/options-android-sml.png)

-----

## <a name="development-system-requirements"></a>Requisitos do sistema de desenvolvimento

Xamarin. Os aplicativos de formulários podem ser desenvolvidos no macOS e no Windows. No entanto, o Windows e o Visual Studio são necessários para produzir versões do Windows do aplicativo.

## <a name="mac-system-requirements"></a>Requisitos do sistema do Mac

Você pode usar Visual Studio para Mac para desenvolver Xamarin. Aplicativos de formulários no macOS High Sierra (10,13) ou mais recente. Para desenvolver aplicativos iOS, recomendamos o uso da versão mais recente do Xcode, do iOS e do macOS. Para obter os requisitos específicos de versão, consulte as últimas notas de versão do [Xamarin. Ios](/xamarin/ios/release-notes/).

> [!NOTE]
> Aplicativos do Windows não podem ser desenvolvidos no macOS.

## <a name="windows-system-requirements"></a>Requisitos do sistema do Windows

Xamarin. Os aplicativos de formulários para iOS e Android podem ser criados em qualquer instalação do Windows que dê suporte ao desenvolvimento de Xamarin. Para obter suporte completo dos recursos da plataforma atual, use a versão mais recente do Visual Studio. 

Um Mac em rede é necessário para o desenvolvimento do iOS usando a versão mais recente do Xcode e a versão mínima do macOS especificada pela Apple.

<a name="windows10" />

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Desenvolvendo Xamarin. Os aplicativos de formulários para UWP exigem:

- Windows 10 (versão mais recente recomendada, mínimo de atualizações para criadores de outono)

- Visual Studio 2019 recomendado

- [SDK do Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Você pode [Adicionar um aplicativo plataforma universal do Windows (UWP)](~/xamarin-forms/platform/windows/installation/index.md) a um Xamarinexistente. A solução de formulários a qualquer momento.

## <a name="deprecated-platforms"></a>Plataformas preteridas

Não há suporte para essas plataformas ao usar Xamarin. Formulários 3,0 ou mais recentes:

- *WinRT do Windows 8.1/Windows Phone 8.1*
- *Windows Phone 8 Silverlight*
