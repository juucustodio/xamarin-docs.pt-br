---
title: Visão geral da arquitetura
description: Este documento descreve a arquitetura de pastas de trabalho do Xamarin, examinando como o agente interativa e o cliente interativo funcionam juntos.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 352e8d0191512184ffd7d82fa0dfa0bc79fa24ca
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107255"
---
# <a name="architecture-overview"></a>Visão geral da arquitetura

Xamarin Workbooks apresenta dois componentes principais que devem trabalhar em conjunto com uns aos outros: _agente_ e _cliente_.

## <a name="interactive-agent"></a>Agente interativo

O componente do agente é um assembly específico da plataforma pequeno que é executado no contexto de um aplicativo .NET.

Xamarin Workbooks fornece aplicativos "empty" pré-criados para um número de plataformas, como iOS, Android, Mac e WPF. Esses aplicativos hospedam explicitamente o agente.

Durante a inspeção ao vivo (Xamarin Inspector), o agente é injetado por meio do depurador do IDE em um aplicativo existente como parte do desenvolvimento regular & depuração de fluxo de trabalho.

## <a name="interactive-client"></a>Cliente interativo

O cliente é um shell nativo (Cocoa no Mac, o WPF em Windows) que hospeda uma superfície de navegador da web para apresentar a interface do REPL/pasta de trabalho. Da perspectiva do SDK, todas as integrações de cliente são implementadas em JavaScript e CSS.

O cliente é responsável (por meio do Roslyn) para compilar o código-fonte em assemblies pequenos e enviá-las para a execução do agente conectada. Resultados da execução são enviados ao cliente para renderização. Cada célula em uma pasta de trabalho gera um assembly que referencia o assembly da célula anterior.

Como um agente pode ser executado em qualquer tipo de plataforma .NET e tem acesso a qualquer coisa no aplicativo em execução, deve ter cuidado para serializar os resultados de uma maneira independente de plataforma.
