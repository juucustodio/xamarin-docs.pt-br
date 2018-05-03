---
title: Requisitos do Xamarin.Forms
description: Requisitos de sistema de plataforma e desenvolvimento para Xamarim.Forms.
ms.prod: xamarin
ms.assetid: eecaf6a5-567c-49b2-ac83-2a195596c5bf
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/19/2018
ms.openlocfilehash: d2125c1ddaa3edc3e2ee76d8e03e384efdca42c6
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2018
---
# <a name="xamarinforms-requirements"></a>Requisitos do Xamarin.Forms

_Requisitos de sistema de plataforma e desenvolvimento para Xamarim.Forms._

Consulte o artigo de [Instalação](~/cross-platform/get-started/installation/index.md) para obter uma visão geral da instalação e das práticas recomendadas aplicáveis entre plataformas.

## <a name="target-platforms"></a>Plataformas de destino

Aplicativos Xamarin.Forms podem ser gravados para os seguintes sistemas operacionais:

-  iOS 8 ou superior
-  Android 4.0.3 (API 15) ou superior ([mais detalhes](#android))
-  Plataforma Universal do Windows do Windows 10 ([mais detalhes](#windows10))
-  WinRT do Windows 8.1/Windows Phone 8.1 ([mais detalhes](#windows))
-  *Windows Phone 8 Silverlight (PRETERIDO)*

Supõe-se que os desenvolvedores estejam familiarizados com [Bibliotecas de Classes Portáteis](~/cross-platform/app-fundamentals/pcl.md) e [Projetos Compartilhados](~/cross-platform/app-fundamentals/shared-projects.md).

<a name="android" />

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


<a name="windows10" />

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Projetos da UWP do Windows 10 não são adicionados quando uma solução é criada no macOS. Para obter instruções de como adicionar esses projetos a uma solução existente, consulte [Adicionando um Aplicativo da UWP (Plataforma Universal do Windows)](~/xamarin-forms/platform/windows/installation/universal.md).


<a name="windows" />

### <a name="windows-81--windows-phone-81-winrt"></a>WinRT do Windows 8.1/Windows Phone 8.1

Projetos do WinRT do Windows 8.1/Windows Phone 8.1 não são adicionados quando uma solução é criada no macOS. Para obter instruções de como adicionar esses projetos a uma solução existente, consulte [Adicionando um aplicativo Windows Phone](~/xamarin-forms/platform/windows/installation/phone.md) e [Adicionando um aplicativo do Windows](~/xamarin-forms/platform/windows/installation/tablet.md).


## <a name="development-system-requirements"></a>Requisitos do sistema de desenvolvimento

Aplicativos Xamarin.Forms podem ser desenvolvidos no macOS e no Windows. No entanto, o Windows e o Visual Studio são necessários para produzir versões do Windows do aplicativo.

## <a name="mac-system-requirements"></a>Requisitos de sistema Mac

Você pode usar o Visual Studio para Mac para desenvolver aplicativos Xamarin.Forms no OS X El Capitan (10.11) ou mais recente. Para desenvolver aplicativos iOS, é recomendável ter pelo menos o SDK do iOS 10 e o Xcode 8 instalados.

> [!NOTE]
>  Aplicativos do Windows não podem ser desenvolvidos no macOS.

## <a name="windows-system-requirements"></a>Requisitos de sistema Windows

Aplicativos Xamarin.Forms para iOS e Android podem ser compilados em qualquer instalação do Windows que dê suporte a desenvolvimento do Xamarin. É necessário ter o Visual Studio 2017 ou mais recente em execução no Windows 7 ou superior. Um Mac em rede é necessário para o desenvolvimento do iOS.

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

O desenvolvimento de aplicativos Xamarin.Forms para UWP requer:

* Windows 10 (é recomendável o Fall Creators Update)

* Visual Studio 2017

* [SDK do Windows 10](https://dev.windows.com/downloads/windows-10-sdk)

Projetos da UWP são incluídos em soluções Xamarin.Forms criadas no Visual Studio 2015 e no Visual Studio 2017.
Você também pode [adicionar um aplicativo UWP (Plataforma Universal do Windows)](~/xamarin-forms/platform/windows/installation/universal.md) a uma solução Xamarin.Forms existente.

