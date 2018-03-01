---
title: "Limitações"
description: "Algumas limitações do Player Live Xamarin"
ms.topic: article
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 08/04/2017
ms.openlocfilehash: d551c0a82fb8f970ce5a00ec9e64ac7f49c81a44
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="limitations"></a>Limitações

![Recurso de visualização](~/media/shared/preview.png)

## <a name="device-requirements"></a>Requisitos do dispositivo
O aplicativo Xamarin ao vivo Player suporta os seguintes dispositivos:

### <a name="ios"></a>iOS
- iOS 9.0 ou posterior.
- Processador ARM64.
- Verifique o [App Store](https://itunes.apple.com/us/app/xamarin-live-player/id1228841832?mt=8) para obter uma lista de dispositivos com suporte.

### <a name="android"></a>Android
- Android 4.2 ou posterior.
- ARM-v7a, v8a ARM, ARM64-v8a x 86 ou processador x86_64.


## <a name="limitations"></a>Limitações

Há algumas limitações sobre as coisas que xamarin Live Player pode executar, incluindo os itens a seguir:

### <a name="android"></a>Android
- Atualmente, não há suporte para interfaces de usuário do Android projetadas com arquivos AXML.

### <a name="ios"></a>iOS
- Não há suporte para alguns recursos de storyboard do iOS.
- Não há suporte para arquivos XIB iOS.

### <a name="xamarinforms"></a>Xamarin.Forms
- Não há suporte para processadores personalizados.
- Não há suporte para efeitos.
- Não há suporte para controles personalizados com propriedades vinculáveis personalizado.
- Não há suporte para recursos incorporados (ie. incorporação de imagens ou outros recursos em um PCL).

### <a name="misc"></a>Diversos
- Suporte limitado para reflexão (atualmente afeta alguns NuGets populares, como SQLite e Json.NET). Ainda há suporte para outros NuGets.
- Algumas classes de sistema não podem ser substituídos (por exemplo, você não pode implementar uma subclasse).
- Alguns recursos de plataforma que exigem o provisionamento não funcionam no aplicativo Xamarin ao vivo Player (no entanto tiver sido configurada para operações comuns, como acesso de galeria de fotos).
- Destinos personalizados e etapas de compilação são ignoradas. Por exemplo, ferramentas como Fody não podem ser incorporadas.

> [!WARNING]
> **Observação:** Xamarin ao vivo não funciona com Xamarin Studio

Relate problemas adicionais em [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Links relacionados

- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Instalação](~/tools/live-player/install.md)
