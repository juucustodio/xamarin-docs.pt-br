---
title: Introdução ao macOS Mojave
description: Este documento fornece um alto nível de visão geral dos recursos novos e atualizados no macOS Mojave.
ms.prod: xamarin
ms.assetid: 4A41CD85-C807-44C9-85AB-B5441B145A73
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 9c88d0a90cf5f1cae0e27a05aeaa71f726ddb4de
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511686"
---
# <a name="introduction-to-macos-mojave"></a>Introdução ao macOS Mojave

Este documento fornece um alto nível de visão geral dos recursos novos e atualizados no macOS Mojave.

Para começar a criar aplicativos macOS Mojave com o Xamarin, dê uma olhada no [Guia de introdução](~/mac/platform/introduction-to-macos-mojave/get-started.md) do [xamarin. Mac 5,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/mac/xamarin.mac_5/xamarin.mac_5.0.md).

## <a name="dark-mode"></a>Modo escuro

O modo escuro é um tema escuro em todo o sistema no macOS Mojave, que usa um esquema de cores cinza dinâmico e escuro para exibir elementos da interface do usuário. Ele também apresenta novas cores de destaque, efeitos de cor e cores de tonalidade de conteúdo para ajudar aplicativos de terceiros parecerem bons, independentemente das configurações de cores do usuário.

## <a name="user-notifications-framework"></a>Estrutura de notificações do usuário

A estrutura de notificações do usuário está incluída no macOS Mojave, alterando as APIs que os aplicativos Mac usam para trabalhar com as notificações do usuário.

## <a name="natural-language-framework"></a>Estrutura de linguagem natural

A estrutura de linguagem natural permite que os aplicativos executem vários tipos de análise de linguagem. Por exemplo, ele pode ser usado para identificar partes de fala e determinar o idioma representado por um bloco de texto.

## <a name="vision-framework"></a>Estrutura de visão

A estrutura de visão inclui um detector facial aprimorado que pode detectar rostos em várias orientações. Além disso, as revisões de solicitação agora podem ser usadas para selecionar uma revisão de algoritmo do Vision Framework específica.

## <a name="network-framework"></a>Estrutura de rede

A estrutura de rede, a pilha de `URLSession` rede subjacente às APIs normalmente usadas em aplicativos Ios, agora está disponível como uma estrutura autônoma, facilitando o trabalho com TCP, UDP, TLS, IPv4/IPv6 e muito mais.

## <a name="deprecations"></a>Substituições

Com o macOS Mojave, a Apple preteriu OpenGL ES e OpenCL, [incentivando os desenvolvedores](https://developer.apple.com/macos/whats-new/) a adotar sombreadores de desempenho de metal e de metal.

## <a name="related-links"></a>Links relacionados

- [Amostras do Xamarin. Mac](https://developer.xamarin.com/samples/mac/)
- [macOS – desenvolvedor da Apple](https://developer.apple.com/macos/)
- [Notas de versão do Xamarin. Mac 5,0](https://docs.microsoft.com/xamarin/mac/release-notes/5/5.0/)
