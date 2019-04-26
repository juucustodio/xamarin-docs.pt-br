---
title: Regras de análise do xamarin. IOS
description: Este documento descreve um conjunto de regras de análise que verifique as configurações de projeto do xamarin. IOS para ajudar a determinar se mais/better-optimized configurações estarão disponíveis.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 8a4990ce7b2bcacbd4b97b214458531b3d94122e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416377"
---
# <a name="xamarinios-analysis-rules"></a>Regras de análise do xamarin. IOS

Análise de xamarin. IOS é um conjunto de regras que verificam as configurações do projeto para ajudá-lo a determinar se existem configurações de melhor/mais otimizadas.

Execute as regras de análise sempre que possível para localizar possíveis melhorias desde o início e economizar tempo de desenvolvimento.

Para executar as regras, no Visual Studio para o menu do Mac, selecione **Project > Executar análise de código**.

> [!NOTE]
> Análise de xamarin. IOS só é executada em sua configuração selecionada no momento. É altamente recomendável executar a ferramenta de depuração **e** configurações de versão.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** O vinculador está desabilitado no dispositivo para o modo de depuração.
- **Correção:** Você deve tentar executar seu código com o vinculador a evitar quaisquer surpresas.
Para configurá-lo, vá para o projeto > Build do iOS > comportamento do vinculador.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** Compilações de aplicativo que inicializam o agente de teste de nuvem serão rejeitadas pelo Apple quando enviado, que usam a API privada.
- **Correção:** Adicionar ou corrigir o #if necessário e define no código.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** Configuração de depuração que usa chaves de assinatura de desenvolvedor não deve gerar um IPA, conforme ele é necessário apenas para distribuição, que agora usa o Assistente de publicação.
- **Correção:** Desabilite a compilação de IPA nas opções de projeto para a configuração de depuração.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** A arquitetura com suporte para a "versão | dispositivo"não é compatível, ausente ARM64 de 64 bits. Este é um problema, como Apple não aceita aplicativos iOS somente de 32 bits em que a loja de aplicativos.
- **Correção:** Clique duas vezes em seu projeto do iOS, vá para a compilação > iOS Build e altere as arquiteturas com suporte para que ele tenha ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** Não usando a opção float32 (– opções de aot e / S = = float32) leva a pesada o desempenho, especialmente em dispositivos móveis, onde a matemática de precisão dupla é consideravelmente mais lenta. Observe que .NET usa precisão dupla internamente, mesmo para float, portanto, a habilitação dessa opção afeta a precisão e, possivelmente, compatibilidade.
- **Correção:** Clique duas vezes em seu projeto do iOS, vá para a compilação > iOS Build e desmarque o "Executar todas as operações de float de 32 bits como float de 64 bits".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** É recomendável usar o manipulador de HttpClient nativo em vez do gerenciado para melhorar o desempenho, tamanho do executável menor e para suportar facilmente padrões mais recentes.
- **Correção:** Clique duas vezes em seu projeto do iOS, vá para a compilação > iOS Build e alterar a implementação de HttpClient NSUrlSession (iOS 7 +) ou CFNetwork para dar suporte à versão anterior do iOS 7.
