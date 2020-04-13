---
title: Plataformas compatíveis com o Xamarin.Forms
description: Requisitos de sistema de plataforma e desenvolvimento para Xamarim.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2020
ms.openlocfilehash: 5751983bf4a818e8ee91af98b0c05658efaca8b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304271"
---
# <a name="xamarinforms-supported-platforms"></a>Plataformas compatíveis com o Xamarin.Forms

Aplicativos Xamarin.Forms podem ser gravados para os seguintes sistemas operacionais:

- iOS 9 ou superior.
- Android 4.4 (API 19) ou superior ([mais detalhes](#android-platform-support)). No entanto, o Android 5.0 (API 21) é recomendado como a API mínima. Isso garante a compatibilidade total com todas as bibliotecas compatíveis com Android, embora ainda visando a maioria dos dispositivos Android.
- Plataforma Universal do Windows para o Windows 10.

Os aplicativos Xamarin.Forms para iOS, Android e a UWP (Plataforma Universal do Windows) podem ser criados no Visual Studio. Porém, um Mac em rede é necessário para o desenvolvimento do iOS usando a versão mais recente do Xcode e a versão mínima do macOS especificada pela Apple. Para obter mais informações, veja [Requisitos do Windows](~/cross-platform/get-started/requirements.md#windows-requirements).

Os aplicativos Xamarin.Forms para iOS e Android podem ser incorporados ao Visual Studio para Mac. Para obter mais informações, veja [Requisitos do macOS](~/cross-platform/get-started/requirements.md#macos-requirements).

> [!NOTE]
> O desenvolvimento de aplicativos usando o Xamarin.Forms requer familiaridade com o [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="additional-platform-support"></a>Suporte adicional da plataforma

O Xamarin.Forms é compatível com plataformas adicionais além de iOS, Android e Windows:

- Samsung Tizen
- macOS
- GTK#
- WPF

O status dessas plataformas está disponível no [wiki de suporte à plataforma do GitHub do Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support).

## <a name="android-platform-support"></a>Suporte à plataforma Android

Você precisa ter a plataforma das Ferramentas do SDK do Android e da API do Android mais recente instalada. É possível atualizar para as versões mais recentes usando o [Gerenciador de SDK do Android](~/android/get-started/installation/android-sdk.md).

Além disso, a versão de destino/compilação para projetos do Android **deve** ser definida como *Usar a plataforma mais recente instalada*. No entanto, a versão mínima pode ser definida como API 19, de modo que você pode continuar dando suporte a dispositivos que usam Android 4.4 e mais recentes. Esses valores são definidos nas Opções de **Projeto:**

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

**Opções de Projeto > Aplicativo > Propriedades do Aplicativo**

![Opções de build do Android no Visual Studio](requirements-images/options-android-vs-sml.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

**Build > Geral**

![Selecione a estrutura de destino mais recente](requirements-images/options-general-sml.png)

**Build > Aplicativo Android**

![Selecione as versões mínima e alvo do Android para seu aplicativo](requirements-images/options-android-sml.png)

-----

## <a name="deprecated-platforms"></a>Plataformas preteridas

Não há suporte para uso do Xamarin.Forms 3.0 ou mais recente nas plataformas a seguir:

- *WinRT do Windows 8.1/Windows Phone 8.1*
- *Windows Phone 8 Silverlight*
