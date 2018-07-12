---
title: Limitações do Xamarin Live Player
description: Este documento descreve as limitações do Xamarin Live Player. Ele discute os requisitos de dispositivo, recursos que ele funciona com tipos de projeto e outros tópicos diversos.
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: ea71391382f9e1ecb80cbf5f2d5bf127e0d6d1be
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815276"
---
# <a name="limitations-of-xamarin-live-player"></a>Limitações do Xamarin Live Player

![Recurso de visualização](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos do dispositivo
O aplicativo Xamarin Live Player suporta os seguintes dispositivos:

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 ou posterior.
- ARM-v7a, v8a ARM, ARM64-v8a, x86 ou processador x86_64.

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 ou posterior.
- Processador ARM64.

-----

## <a name="limitations"></a>Limitações

Há algumas limitações em coisas que o Xamarin Live Player pode executar, incluindo os itens a seguir:

### <a name="xamarinforms"></a>Xamarin.Forms

- Renderizadores personalizados não têm suporte.
- Efeitos não têm suporte.
- Não há suporte para controles personalizados com propriedades vinculáveis personalizadas.
- Não há suporte para recursos inseridos (ie. inserção de imagens ou outros recursos em uma PCL).
- Estruturas MVVM de terceiros não têm suporte (ie. O Prism, Mvvm cruzada, o Mvvm Light, etc.).
- Não há suporte para catálogos de ativos no iOS.

### <a name="other-project-types"></a>Outros tipos de projeto

- Não se destina a Live Player para Android nativo ou projetos do iOS (que usam Android XML ou Storyboards de interface do usuário).

### <a name="misc"></a>Diversos

- Suporte limitado para reflexão (atualmente afeta alguns NuGets populares, como o SQLite e Json.NET). Outros NuGets ainda podem ter suporte.
- Algumas classes de sistema não podem ser substituídos (por exemplo, você não pode implementar uma subclasse).
- Alguns recursos de plataforma que exigem o provisionamento não funcionam no aplicativo do Xamarin Live Player (no entanto ele foi configurado para operações comuns, como acesso de galeria de fotos).
- Destinos personalizados e etapas de compilação são ignoradas. Por exemplo, ferramentas como o Fody, Refit, AutoFac e AutoMapper não podem ser incorporadas.
- Não há suporte para projetos do F # no Android e suporte no iOS limitado
- Cenários avançados com interfaces e classes genéricas personalizadas podem não ter suporte.

Relate quaisquer problemas adicionais no [bugzilla](https://aka.ms/live-player-report-issue).

## <a name="related-links"></a>Links relacionados

- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Instalação](~/tools/live-player/install.md)
