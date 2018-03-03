---
title: iOS e Mac
description: "Nesta seção, abordaremos estratégias para compartilhar código entre seus projetos xamarin e Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: a5711b5ac8332ac518ba5b639f05de557229af6e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="ios-and-mac"></a>iOS e Mac

_Nesta seção, abordaremos estratégias para compartilhar código entre seus projetos xamarin e Xamarin.Mac._

## <a name="code-sharing"></a>Compartilhamento de código

Para elementos do seu código com nenhum elemento de interface do usuário a melhor maneira de compartilhar o código entre iOS e Mac ainda é o uso de [bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md).

Para o código que tem algum trabalho de interface de usuário e ainda, você deseja compartilhar, você deve usar [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) que permitem que você coloque o código para compartilhar em um único projeto e que ele seja compilado com Mac e iOS, quando referenciadas.

##  <a name="unified-apiunifiedindexmd"></a>[API unificada](unified/index.md)

A API unificada para projetos de Mac e iOS usa os mesmos namespaces estruturas para que o mesmo arquivo de código pode ser usado entre plataformas, para compartilhar código perfeita. Ele também permite compilações de bit de 32 e 64. A API Unified tem sido o padrão de modelo desde o início de 2015 e é recomendada para todos os novos projetos - *somente* projetos unificado API podem ser enviados para a loja de aplicativos.

### <a name="classic-apis"></a>APIs clássico

> [!NOTE]
> **Substituição de perfil clássica:** conforme são adicionadas novas plataformas xamarin começamos gradualmente substituir recursos do perfil clássico (monotouch.dll). Por exemplo, a opção de não-NRC (novo-ref count) foi removida. NRC sempre foi habilitada para unificada de todos os aplicativos (ou seja, não-NRC nunca foi uma opção) e não tem nenhum problema conhecido. Versões futuras removerá a opção de usar Boehm como o coletor de lixo. Isso também era uma opção nunca disponível para aplicativos unificados. A remoção completa de suporte clássico é agendada para outono 2016 com a versão do xamarin 10.0.

As APIs de Xamarin.Mac e xamarin original (não unificado) feitas compartilhamento de código mais difícil porque estruturas nativas tinham um `MonoTouch.` ou `MonoMac.` prefixos de namespace.  Fornecemos alguns namespaces vazio que permite aos desenvolvedores compartilhar código adicionando `using` instruções que fazem referência a namespaces MonoMac e MonoTouch no mesmo arquivo, mas isso foi um pouco feio. A API clássica somente deverá continuar a ser usado em aplicativos herdados que são distribuídos internamente (Atualizando para a API unificada recomendado).


### <a name="updating-from-classic-to-the-unified-api"></a>Atualização de clássico para a API unificada

Há instruções detalhadas para a atualização de qualquer aplicativo clássica para a API unificada.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Associação de bibliotecas Objective-C](binding/index.md)

Xamarin permite que você coloque bibliotecas nativas em seus aplicativos com associações. Esta seção explica:

- funcionam associações
- como criar manualmente um projeto de associação que permite que você coloque o código Objective-C em Xamarin, e
- como usar nosso **Sharpie objetivo** ferramenta para ajudar a automatizar o processo.

## <a name="native-referencesnative-referencesmd"></a>[Referências nativo](native-references.md)



##  <a name="macios-native-typesnativetypesmd"></a>[Tipos nativos iOS/Mac](nativetypes.md)

Para oferecer suporte de 32 e 64 bits código transparente de c# e F #, estamos introduzindo novos tipos de dados.   Saiba mais sobre eles aqui.

##  <a name="building-32-and-64-bit-apps32-and-64md"></a>[Compilando 32 e 64 bits aplicativos](32-and-64.md)

O que você precisa saber para dar suporte a 32 e 64 bits de aplicativos.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Trabalhando com tipos nativos em aplicativos de plataforma cruzada](native-types-cross-platform.md)

Este artigo aborda o uso do iOS novos tipos nativos de API unificada (`nint`, `nuint`, `nfloat`) em um aplicativo de plataforma cruzada onde o código é compartilhado com dispositivos iOS não, como Android ou sistemas operacionais do Windows Phone.
Ele fornece informações sobre quando os tipos nativos devem ser usados e fornece várias soluções possíveis para casos em que o novo tipo deve ser usado com o código de plataforma cruzada.


## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Pilha de HttpClient e seletor de implementação de SSL/TLS](http-stack.md)

O novo seletor de pilha HttpClient controla qual implementação HttpClient para usar em seu aplicativo xamarin, Xamarin.tvOS e Xamarin.Mac. Você agora pode alternar para uma implementação que usa do iOS, do tvOS ou transportes nativo OS x (`NSUrlSession` ou `CFNetwork` dependendo do sistema operacional).

SSL (Secure Socket Layer) e seu sucessor, o TLS (Transport Layer Security), oferecem suporte para HTTP e outras conexões de rede por meio de `System.Net.Security.SslStream`. A nova opção de compilação de implementação de SSL/TLS alterna entre a pilha TLS do Mono e com a pilha TLS da Apple presente no Mac e iOS.
