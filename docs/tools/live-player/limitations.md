---
title: Limitações do Player ao vivo do Xamarin
description: Este documento descreve as limitações do Player Xamarin ao vivo. Discute os requisitos de dispositivo, ele funciona com tipos de projeto e outros tópicos de diversos recursos.
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: topgenorth
ms.author: toopge
ms.date: 03/29/2018
ms.openlocfilehash: ea71391382f9e1ecb80cbf5f2d5bf127e0d6d1be
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793669"
---
# <a name="limitations-of-xamarin-live-player"></a>Limitações do Player ao vivo do Xamarin

![Recurso de visualização](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos do dispositivo
O aplicativo Xamarin ao vivo Player suporta os seguintes dispositivos:

# <a name="androidtabandroid"></a>[Android](#tab/android)

- Android 4.2 ou posterior.
- ARM-v7a, v8a ARM, ARM64-v8a x 86 ou processador x86_64.

# <a name="iostabios"></a>[iOS](#tab/ios)

- iOS 9.0 ou posterior.
- Processador ARM64.

-----

## <a name="limitations"></a>Limitações

Há algumas limitações sobre as coisas que xamarin Live Player pode executar, incluindo os itens a seguir:

### <a name="xamarinforms"></a>Xamarin.Forms

- Não há suporte para processadores personalizados.
- Não há suporte para efeitos.
- Não há suporte para controles personalizados com propriedades vinculáveis personalizado.
- Não há suporte para recursos incorporados (ie. incorporação de imagens ou outros recursos em um PCL).
- Estruturas MVVM de terceiros não têm suporte (ie. Prisma Mvvm Cross, Mvvm Light, etc.).
- Não há suporte para o catálogo de ativos no iOS.

### <a name="other-project-types"></a>Outros tipos de projeto

- Player dinâmica não se destina para nativo Android ou iOS projetos (usam XML Android ou Storyboards para a interface do usuário).

### <a name="misc"></a>Diversos

- Suporte limitado para reflexão (atualmente afeta alguns NuGets populares, como SQLite e Json.NET). Outros NuGets ainda podem ter suporte.
- Algumas classes de sistema não podem ser substituídos (por exemplo, você não pode implementar uma subclasse).
- Alguns recursos de plataforma que exigem o provisionamento não funcionam no aplicativo Xamarin ao vivo Player (no entanto tiver sido configurada para operações comuns, como acesso de galeria de fotos).
- Destinos personalizados e etapas de compilação são ignoradas. Por exemplo, ferramentas como Fody, Refit, AutoFac e AutoMapper não podem ser incorporadas.
- Não há suporte para projetos F # no Android e suporte no iOS limitado
- Cenários avançados com interfaces e classes genéricas personalizadas podem não ter suporte.

Relate problemas adicionais em [bugzilla](https://aka.ms/live-player-report-issue).

## <a name="related-links"></a>Links relacionados

- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Instalação](~/tools/live-player/install.md)
