---
title: Limitações do Xamarin Live Player
description: Este documento descreve as limitações do Xamarin Live Player. Ele discute os requisitos de dispositivo, recursos que ele funciona com tipos de projeto e outros tópicos diversos.
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: aff6990df1b710190f11c2d7fa09c8399e94f8af
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "40251075"
---
# <a name="limitations-of-xamarin-live-player"></a>Limitações do Xamarin Live Player

![Recurso de visualização](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos do dispositivo

O aplicativo Xamarin Live Player dá suporte aos seguintes dispositivos Android:

- Android 4.2 ou posterior.
- ARM-v7a, v8a ARM, ARM64-v8a, x86 ou processador x86_64.

## <a name="limitations"></a>Limitações

Há algumas limitações em coisas que o Xamarin Live Player pode executar, incluindo os itens a seguir:

### <a name="ios"></a>iOS

Live Player não está disponível para iOS.

### <a name="xamarinforms"></a>Xamarin.Forms

- Renderizadores personalizados não têm suporte.
- Efeitos não têm suporte.
- Não há suporte para controles personalizados com propriedades vinculáveis personalizadas.
- Não há suporte para recursos inseridos (ie. inserção de imagens ou outros recursos em uma PCL).
- Estruturas MVVM de terceiros não têm suporte (ie. O Prism, Mvvm cruzada, o Mvvm Light, etc.).

### <a name="other-project-types"></a>Outros tipos de projeto

- Live Player não se destina a projetos do Android nativos (que usam o XML do Android para a interface do usuário).

### <a name="misc"></a>Diversos

- Suporte limitado para reflexão (atualmente afeta alguns NuGets populares, como o SQLite e Json.NET). Outros NuGets ainda podem ter suporte.
- Algumas classes de sistema não podem ser substituídos (por exemplo, você não pode implementar uma subclasse).
- Alguns recursos de plataforma que exigem o provisionamento não funcionam no aplicativo do Xamarin Live Player (no entanto ele foi configurado para operações comuns, como acesso de galeria de fotos).
- Destinos personalizados e etapas de compilação são ignoradas. Por exemplo, ferramentas como o Fody, Refit, AutoFac e AutoMapper não podem ser incorporadas.
- Não há suporte para projetos do F #
- Cenários avançados com interfaces e classes genéricas personalizadas podem não ter suporte.

Use **relatar um problema** na [Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio-2017) ou [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem) relatar quaisquer problemas com o Xamarin Live Player.

## <a name="related-links"></a>Links relacionados

- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Instalação](~/tools/live-player/install.md)
