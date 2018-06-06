---
title: Visão geral da arquitetura
description: Este documento descreve a arquitetura de pastas de trabalho do Xamarin, examinar como o agente interativo e o cliente interativo funcionam juntos.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 3cd0d3e8cc47dd7fa43dfa0b910c9f09740fcc9a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794001"
---
# <a name="architecture-overview"></a>Visão geral da arquitetura

Pastas de trabalho do Xamarin apresenta dois componentes principais que devem trabalhar em conjunto com outro: _agente_ e _cliente_.

## <a name="interactive-agent"></a>Agente interativo

O componente do agente é um conjunto específico de plataforma pequeno que é executado no contexto de um aplicativo .NET.

Pastas de trabalho do Xamarin fornece aplicativos "empty" criados previamente para um número de plataformas, como iOS, Android, Mac e WPF. Esses aplicativos explicitamente o agente de host.

Durante a inspeção dinâmica (Xamarin Inspetor), o agente é inserido por meio do depurador do IDE em um aplicativo existente como parte do desenvolvimento regular & depuração de fluxo de trabalho.

## <a name="interactive-client"></a>Cliente interativo

O cliente é um shell nativo (Cocoa no Mac, WPF em Windows) que hospeda uma superfície de navegador da web para apresentar a interface de pasta de trabalho/REPL. De uma perspectiva SDK, todas as integrações do cliente são implementadas em JavaScript e CSS.

O cliente é responsável (via Roslyn) para compilar o código-fonte em assemblies pequenos e enviá-los por agente conectado para execução. Resultados de execução são enviados ao cliente para processamento. Cada célula em uma pasta de trabalho gera um assembly que referencia o assembly da célula anterior.

Como um agente pode ser executado em qualquer tipo de plataforma .NET e tem acesso a nada no aplicativo em execução, deve ter cuidado para serialização de resultados de uma maneira independente de plataforma.
