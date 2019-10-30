---
title: Visão geral da arquitetura
description: Este documento descreve a arquitetura do Xamarin Workbooks, examinando como o agente interativo e o cliente interativo trabalham juntos.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 7b3f2613e315bc05fedfb5b2fa70d11c2874ba65
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029613"
---
# <a name="architecture-overview"></a>Visão geral da arquitetura

O Xamarin Workbooks apresenta dois componentes principais que devem funcionar em conjunto entre si: _agente_ e _cliente_.

## <a name="interactive-agent"></a>Agente interativo

O componente do agente é um assembly de pequena plataforma específico que é executado no contexto de um aplicativo .NET.

O Xamarin Workbooks fornece aplicativos "vazios" pré-criados para várias plataformas, como iOS, Android, Mac e WPF. Esses aplicativos hospedam explicitamente o agente.

Durante a inspeção ao vivo (Xamarin Inspector), o agente é injetado por meio do depurador do IDE em um aplicativo existente como parte do fluxo de trabalho de depuração regular de & de desenvolvimento.

## <a name="interactive-client"></a>Cliente interativo

O cliente é um shell nativo (Cocoa no Mac, WPF no Windows) que hospeda uma superfície de navegador da Web para apresentar a interface de pasta de trabalho/REPL. Do ponto de vista do SDK, todas as integrações do cliente são implementadas em JavaScript e CSS.

O cliente é responsável (via Roslyn) para compilar o código-fonte em pequenos assemblies e enviá-los para o agente conectado para execução. Os resultados da execução são enviados de volta ao cliente para renderização. Cada célula em uma pasta de trabalho produz um assembly que faz referência ao assembly da célula anterior.

Como um agente pode ser executado em qualquer tipo de plataforma .NET e tem acesso a qualquer coisa no aplicativo em execução, deve-se tomar cuidado para serializar os resultados de forma independente da plataforma.
