---
title: Regras de análise do xamarin
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: d942e21208d5a66da3dd849d217be2489311b9a0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinios-analysis-rules"></a>Regras de análise do xamarin

Análise de xamarin é um conjunto de regras que verificam as configurações do projeto para ajudá-lo a determinar se as configurações de otimização melhor/mais disponíveis.

Execute as regras de análise sempre que possível para localizar possíveis melhorias desde o início e economizar tempo de desenvolvimento.

Para executar as regras, no Visual Studio para abrir o menu do Mac, selecione **projeto > Executar análise de código**.

> [!NOTE]
> Análise de xamarin só é executada em sua configuração selecionada no momento. É altamente recomendável executar a ferramenta de depuração **e** configurações de versão.

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** o vinculador está desabilitado no dispositivo para o modo de depuração.
- **Correção:** deve tentar executar seu código com o vinculador para evitar quaisquer surpresas.
Para configurá-lo, vá para o projeto > iOS compilação > comportamento do vinculador.

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** compilações de aplicativo que inicializar o agente de teste nuvem serão rejeitadas pelo Apple quando enviado, que usam API privada.
- **Correção:** adicionar ou corrigir as #if necessárias e define no código.

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** configuração de depuração que usa chaves de assinatura de desenvolvedor não deve gerar um IPA conforme ela é necessária somente para distribuição, que agora usa o Assistente de publicação.
- **Correção:** desabilitar a compilação de IPA nas opções de projeto para a configuração de depuração.

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** a arquitetura com suporte para "versão | dispositivo"não é compatível, ausente ARM64 de 64 bits. Este é um problema, como Apple não aceita aplicativos iOS somente de 32 bits na AppStore.
- **Correção:** duplo clique no seu projeto do iOS, vá para a compilação > iOS criar e alterar as arquiteturas com suporte para que ele tenha ARM64.

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** não usando a opção float32 (– aot-opções-O = = float32) leva a desempenho pesado custo, especialmente no dispositivo móvel, em que é mais lenta, matemática de precisão dupla. Observe que .NET usa precisão dupla internamente, mesmo para float, para que habilitar essa opção afeta a precisão e, possivelmente, compatibilidade.
- **Correção:** duplo clique no seu projeto do iOS, vá para a compilação > iOS compilar e desmarque o "Executar todas as operações de float de 32 bits como float de 64 bits".

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** é recomendável usar o manipulador de HttpClient nativo em vez de gerenciado para melhorar o desempenho, executável menores e para suportar facilmente os padrões mais recentes.
- **Correção:** duplo clique no seu projeto do iOS, vá para a compilação > iOS criar e alterar a implementação de HttpClient NSUrlSession (iOS 7 +) ou CFNetwork para dar suporte a versão anterior do iOS 7.
