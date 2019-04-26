---
title: Plataforma Apple (iOS e Mac)
description: 'Este documento descreve os vários tópicos relacionados ao desenvolvimento do xamarin. IOS e xamarin. Mac: código de compartilhamento, a API unificada, de associação Objective-C bibliotecas, referências nativas, tipos nativos e muito mais.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b40758fa562e57415cd3c0818763ef0a7ce5dcca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199761"
---
# <a name="apple-platform-ios-and-mac"></a>Plataforma Apple (iOS e Mac)

## <a name="code-sharing"></a>Compartilhamento de código

Para elementos do seu código que não possuem nenhum elemento de interface do usuário a melhor maneira de compartilhar código entre o iOS e Mac ainda é o uso de [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md).

Para o código que precisa fazer algum trabalho de interface do usuário e ainda assim, você deseja compartilhar, você deve usar [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) que permitem que você coloque o código para compartilhar em um único projeto e que ele seja compilado com o Mac e iOS quando referenciado.

##  <a name="unified-apiunifiedindexmd"></a>[API Unificada](unified/index.md)

A API unificada para projetos do iOS e Mac usa os mesmos namespaces para estruturas, para que o mesmo arquivo de código pode ser usado nas duas plataformas, para o compartilhamento de código contínuo. Ele também permite que builds de bit 32 e 64. A API unificada tem sido o padrão de modelo desde o início de 2015 e é recomendada para todos os novos projetos - *apenas* projetos de API unificada que podem ser enviados para a App Store.

### <a name="classic-apis"></a>APIs clássicas

> [!NOTE]
> **Reprovação do perfil clássico:** Conforme novas plataformas são adicionadas no xamarin. IOS, que estamos começando a substituir gradualmente os recursos do perfil clássico (MonoTouch. dll). Por exemplo, a opção de não-NRC (novo-ref-count) foi removida. NRC sempre foi habilitada para unificada de todos os aplicativos (ou seja, não-NRC nunca foi uma opção) e não tem nenhum problema conhecido. Versões futuras removerá a opção de usar Boehm como o coletor de lixo. Isso também era uma opção nunca disponível para aplicativos unificados. A remoção completa do suporte clássica está agendada para outono de 2016 com o lançamento do xamarin. IOS 10.0.

O xamarin. IOS original (não unificado) e APIs Xamarin.Mac feitas compartilhamento de código mais difícil porque estruturas nativas tinham uma `MonoTouch.` ou `MonoMac.` prefixos de namespace.  Fornecemos alguns namespaces vazio que permite aos desenvolvedores compartilhar o código adicionando `using` instruções que fazem referência a namespaces MonoMac e MonoTouch no mesmo arquivo, mas isso foi um pouco feio. A API clássica só deve continuar a ser usado em aplicativos herdados que são distribuídos internamente (Atualizando para a API unificada é recomendado).


### <a name="updating-from-classic-to-the-unified-api"></a>Atualizando do clássico para a API unificada

Há instruções detalhadas para a atualização de qualquer aplicativo do clássico para a API unificada.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Associação de bibliotecas de Objective-C](binding/index.md)

Xamarin permite que você traga bibliotecas nativas em seus aplicativos com associações. Esta seção explica:

- como funcionam as associações,
- como criar manualmente um projeto de associação que permite que você coloque o código Objective-C do Xamarin, e
- como usar nosso **Sharpie objetivo** ferramenta para ajudar a automatizar o processo.

## <a name="native-referencesnative-referencesmd"></a>[Referências nativas](native-references.md)

##  <a name="macios-native-typesnativetypesmd"></a>[Tipos nativos do Mac/iOS](nativetypes.md)

Para dar suporte ao código de bit 32 e 64 transparente do C# e F#, estamos introduzindo novos tipos de dados.   Saiba mais sobre eles aqui.

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Aplicativos 32 e 64 bits](32-and-64/index.md)

O que você precisa saber para dar suporte a aplicativos de bit 32 e 64.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Trabalhar com tipos nativos em aplicativos de plataforma cruzada](native-types-cross-platform.md)

Este artigo aborda o uso do novo tipos nativos de API unificada do iOS (`nint`, `nuint`, `nfloat`) em um aplicativo de plataforma cruzada em que o código é compartilhado com dispositivos não iOS, como Android ou sistemas operacionais do Windows Phone.
Ele fornece informações sobre quando os tipos nativos devem ser usados e fornece várias soluções possíveis para casos em que o novo tipo deve ser usado com o código de plataforma cruzada.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Pilha de HttpClient e seletor de implementação SSL/TLS](http-stack.md)

O novo seletor de pilha de HttpClient controla qual implementação de HttpClient para usar em seu aplicativo xamarin. IOS, tvos e xamarin. Mac. Você pode agora alternar para uma implementação que usa do iOS, do tvOS ou transportes de x do sistema operacional nativos (`NSUrlSession` ou `CFNetwork` dependendo do sistema operacional).

SSL (Secure Socket Layer) e seu sucessor, TLS (Transport Layer Security) fornecem suporte para HTTP e outras conexões de rede por meio de `System.Net.Security.SslStream`. A nova opção de compilação de implementação SSL/TLS alterna entre a pilha de TLS do Mono e uma plataforma pela pilha TLS da Apple presente no Mac e iOS.
