---
title: Xamarin.iOS Analysis Rules
ms.topic: article
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/26/2017
ms.openlocfilehash: 7cf627f369b666bb54d0f512dc1361d2a685a057
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinios-analysis-rules"></a>Xamarin.iOS Analysis Rules


## <a name="a-namexia0001xia0001-disabledlinkerrule"></a><a name="XIA0001"/>XIA0001: DisabledLinkerRule

- **Problema:** o vinculador está desabilitado no dispositivo para o modo de depuração.
- **Correção:** deve tentar executar seu código com o vinculador para evitar quaisquer surpresas.
Para configurá-lo, vá para o projeto > iOS compilação > comportamento do vinculador.

## <a name="a-namexia0002xia0002-testcloudagentreleaserule"></a><a name="XIA0002"/>XIA0002: TestCloudAgentReleaseRule

- **Problema:** compilações de aplicativo que inicializar o agente de teste nuvem serão rejeitadas pelo Apple quando enviado, que usam API privada.
- **Correção:** adicionar ou corrigir as #if necessárias e define no código.

## <a name="a-namexia0003xia0003-ipadebugbuildsrule"></a><a name="XIA0003"/>XIA0003: IPADebugBuildsRule

- **Problema:** configuração de depuração que usa chaves de assinatura de desenvolvedor não deve gerar um IPA conforme ela é necessária somente para distribuição, que agora usa o Assistente de publicação.
- **Correção:** desabilitar a compilação de IPA nas opções de projeto para a configuração de depuração.

## <a name="a-namexia0004xia0004-missing64bitsupportrule"></a><a name="XIA0004"/>XIA0004: Missing64BitSupportRule

- **Problema:** a arquitetura com suporte para "versão | dispositivo"não é compatível, ausente ARM64 de 64 bits. Este é um problema, como Apple não aceita aplicativos iOS somente de 32 bits na AppStore.
- **Correção:** duplo clique no seu projeto do iOS, vá para a compilação > iOS criar e alterar as arquiteturas com suporte para que ele tenha ARM64.

## <a name="a-namexia0005xia0005-float32rule"></a><a name="XIA0005"/>XIA0005: Float32Rule

- **Problema:** não usando a opção float32 (– aot-opções-O = = float32) leva a desempenho pesado custo, especialmente no dispositivo móvel, em que é mais lenta, matemática de precisão dupla. Observe que .NET usa precisão dupla internamente, mesmo para float, para que habilitar essa opção afeta a precisão e, possivelmente, compatibilidade.
- **Correção:** duplo clique no seu projeto do iOS, vá para a compilação > iOS compilar e desmarque o "Executar todas as operações de float de 32 bits como float de 64 bits".
