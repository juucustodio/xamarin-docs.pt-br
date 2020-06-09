---
title: Regras de análise do Xamarin. iOS
description: Este documento descreve um conjunto de regras de análise que verificam as configurações do projeto Xamarin. iOS para ajudar a determinar se há configurações mais otimizadas e com otimização de melhor disponibilidade.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 4a7a1c5a9fccb14a84e78e5854bcd8507394ce4a
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574477"
---
# <a name="xamarinios-analysis-rules"></a>Regras de análise do Xamarin. iOS

A análise do Xamarin. iOS é um conjunto de regras que verifica as configurações do seu projeto para ajudá-lo a determinar se há configurações melhores/mais otimizadas disponíveis.

Execute as regras de análise o mais frequentemente possível para encontrar possíveis aprimoramentos no início e economize tempo de desenvolvimento.

Para executar as regras, no menu de Visual Studio para Mac, selecione **projeto > executar análise de código**.

> [!NOTE]
> A análise do Xamarin. iOS é executada somente na configuração selecionada no momento. É altamente recomendável executar a ferramenta para configurações de depuração **e** versão.

<a name="XIA0001"></a>

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **Problema:** O vinculador está desabilitado no dispositivo para o modo de depuração.
- **Correção:** Você deve tentar executar seu código com o vinculador para evitar surpresas.
Para configurá-lo, vá para projeto > o comportamento do vinculador do > do iOS.

<a name="XIA0002"></a>

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **Problema:** As compilações de aplicativos que inicializam o agente de Test Cloud serão rejeitadas pela Apple quando enviadas, pois usam a API privada.
- **Correção:** Adicione ou corrija o #if necessário e define no código.

<a name="XIA0003"></a>

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **Problema:** A configuração de depuração que usa chaves de assinatura do desenvolvedor não deve gerar um IPA, pois ele só é necessário para a distribuição, que agora usa o assistente de publicação.
- **Correção:** Desabilite a compilação IPA nas opções de projeto para a configuração de depuração.

<a name="XIA0004"></a>

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **Problema:** A arquitetura com suporte para "Release | dispositivo "não é compatível com 64 bits, ARM64 ausente. Isso é um problema, pois a Apple não aceita 32 bits somente aplicativos iOS no AppStore.
- **Correção:** Clique duas vezes no seu projeto do iOS, vá para compilar > Build do iOS e altere as arquiteturas com suporte para que ele tenha ARM64.

<a name="XIA0005"></a>

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **Problema:** Não usar a opção float32 (--AOT-Options =-O = float32) leva a pesada custo de desempenho, especialmente em dispositivos móveis em que a matemática de precisão dupla é melhorado mais lenta. Observe que o .NET usa a precisão dupla internamente, mesmo para float, portanto, habilitar essa opção afeta a precisão e, possivelmente, a compatibilidade.
- **Correção:** Clique duas vezes em seu projeto do iOS, vá para compilar > iOS Build e desmarque a operação "executar todas as operações float de 32 bits como 64-bit float".

<a name="XIA0006"></a>

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **Problema:** É recomendável usar o manipulador HttpClient nativo em vez do gerenciado um para melhorar o desempenho, o tamanho do executável menor e para dar suporte fácil aos padrões mais recentes.
- **Correção:** Clique duas vezes no seu projeto do iOS, vá para compilar > Build do iOS e altere a implementação do HttpClient para NSUrlSession (iOS 7 +) ou CFNetwork para oferecer suporte à versão anterior ao iOS 7.

<a name="XIA0007"></a>

## <a name="xia0007-usellvmrule"></a>XIA0007: UseLLVMRule

- **Problema:** Para o Release | configuração do iPhone, é recomendável habilitar o compilador LLVM, que gera um código mais rápido para ser executado às custas do tempo de compilação.
- **Correção:** Clique duas vezes no seu projeto do iOS, vá para compilar > Build do iOS e para versão | iPhone, verifique a opção LLVM optimizing compiler.
