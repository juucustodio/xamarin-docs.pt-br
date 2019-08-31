---
title: Plataforma Apple (iOS e Mac)
description: 'Este documento descreve vários tópicos relacionados ao desenvolvimento do Xamarin. iOS e do Xamarin. Mac: compartilhamento de código, o API Unificada, bibliotecas de Objective-C, referências nativas, tipos nativos e muito mais.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d57e69f3cb7662b1ff6e1e7fe1645605d7861b9
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199413"
---
# <a name="apple-platform-ios-and-mac"></a>Plataforma Apple (iOS e Mac)

## <a name="code-sharing"></a>Compartilhamento de código

Para elementos do seu código que não têm elementos de interface do usuário, a melhor maneira de compartilhar código entre o iOS e o Mac ainda é o uso de [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md).

Para o código que precisa fazer parte do trabalho da interface do usuário e, ainda assim, você deseja compartilhar, você deve usar [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) que permitem que você coloque o código para compartilhar em um único projeto e faça com que ele seja compilado com Mac e Ios quando referenciado.

## <a name="unified-apiunifiedindexmd"></a>[API Unificada](unified/index.md)

O API Unificada para projetos iOS e Mac usa os mesmos namespaces para estruturas para que o mesmo arquivo de código possa ser usado em ambas as plataformas, para o compartilhamento de código contínuo. Ele também permite compilações de 32 e 64 bits. O API Unificada tem sido o padrão de modelo desde o início 2015 e é recomendado para todos os novos projetos – *somente* API unificada projetos podem ser enviados para a loja de aplicativos.

### <a name="classic-apis"></a>APIs clássicas

> [!NOTE]
> **Preterimento de perfil clássico:** Conforme novas plataformas são adicionadas no Xamarin. iOS, estamos começando a substituir gradualmente os recursos do perfil clássico (MonoTouch. dll). Por exemplo, a opção não NRC (New-ref-Count) foi removida. O NRC sempre foi habilitado para todos os aplicativos unificados (ou seja, não NRC nunca é uma opção) e não tem problemas conhecidos. Versões futuras removerão a opção de usar Boehm como o coletor de lixo. Essa também era uma opção nunca disponível para aplicativos unificados. A remoção completa do suporte clássico está agendada para o outono de 2016 com o lançamento do Xamarin. iOS 10,0.

As APIs do xamarin. Ios e xamarin. Mac originais (não unificadas) tornaram o `MonoTouch.` compartilhamento de código mais difícil porque as estruturas nativas tinham ou `MonoMac.` prefixos de namespace.  Fornecemos alguns namespaces vazios que permitem aos desenvolvedores compartilhar código adicionando `using` instruções que fazem referência a namespaces MonoMac e MonoTouch no mesmo arquivo, mas isso era um pouco ruim. O API Clássica só deve continuar a ser usado em aplicativos herdados distribuídos internamente (a atualização para a API Unificada é recomendada).


### <a name="updating-from-classic-to-the-unified-api"></a>Atualizando do clássico para o API Unificada

Há instruções detalhadas para atualizar qualquer aplicativo do clássico para o API Unificada.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Associação de bibliotecas de Objective-C](binding/index.md)

O Xamarin permite que você traga bibliotecas nativas para seus aplicativos com associações. Esta seção explica:

- como as ligações funcionam,
- como criar manualmente um projeto de associação que permite que você traga o código de Objective-C para o Xamarin e
- como usar nossa ferramenta de **nitidez objetiva** para ajudar a automatizar o processo.

## <a name="native-referencesnative-referencesmd"></a>[Referências nativas](native-references.md)

## <a name="macios-native-typesnativetypesmd"></a>[Tipos nativos Mac/iOS](nativetypes.md)

Para dar suporte de código de bits 32 e 64 de C# e F#, estamos introduzindo novos tipos de dados.   Saiba mais sobre eles aqui.

## <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Criando aplicativos de 32 e 64 bits](32-and-64/index.md)

O que você precisa saber para dar suporte a aplicativos de 32 e 64 bits.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Trabalhar com tipos nativos em aplicativos de plataforma cruzada](native-types-cross-platform.md)

Este artigo aborda o uso dos novos tipos nativos do IOS`nint`API unificada `nuint`( `nfloat`,,) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não Ios, como Android ou Windows Phone sistemas operacionais.
Ele fornece informações sobre quando os tipos nativos devem ser usados e fornece várias soluções possíveis para casos em que o novo tipo deve ser usado com código de plataforma cruzada.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Pilha de HttpClient e seletor de implementação SSL/TLS](http-stack.md)

O seletor de nova pilha HttpClient controla qual implementação de HttpClient usar em seu aplicativo Xamarin. iOS, Xamarin. tvOS e Xamarin. Mac. Agora você pode alternar para uma implementação que usa transportes nativos do IOS, tvOS ou os X (`NSUrlSession` ou `CFNetwork` dependendo do sistema operacional).

SSL (camada de soquete seguro) e seu sucessor, TLS (segurança de camada de transporte), fornecer suporte para HTTP e `System.Net.Security.SslStream`outras conexões de rede via. A nova opção de compilação de implementação de SSL/TLS alterna entre a própria pilha TLS do mono e uma da plataforma da pilha TLS da Apple presente no Mac e no iOS.
