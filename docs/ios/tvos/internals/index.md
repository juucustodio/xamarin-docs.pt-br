---
title: tvOS no Xamarin – recursos internos
description: Documentos que descrevem o funcionamento interno do tvOS no Xamarin, que se baseia no xamarin. IOS. Link de conteúdo aborda a assemblies, estruturas de destino e relacionadas a conceitos de iOS.
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 3eca425e38a01053f084ddbc5ad2edb93f6f6427
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61395111"
---
# <a name="tvos-in-xamarin-internals"></a>tvOS no Xamarin – recursos internos 

##  <a name="assembliesiostvosinternalsassembliesmd"></a>[Assemblies](~/ios/tvos/internals/assemblies.md)

Lista de assemblies com suporte pelo Xamarin para seus aplicativos xamarin. tvos.

##  <a name="target-frameworksiostvosinternalsframeworksmd"></a>[Estruturas de Destino](~/ios/tvos/internals/frameworks.md)

Este artigo aborda os tipos de estruturas de destino (bibliotecas de classes Base) que estão disponíveis no xamarin. tvos e as implicações da seleção de um destino específico para seu aplicativo xamarin. tvos.

## <a name="related-ios-articles"></a>Artigos relacionados do iOS

Os artigos a seguir são específicos para iOS mas relevantes para tvOS (já que tvOS 9 é um subconjunto do iOS 9).

###  <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[API Unificada](~/cross-platform/macios/unified/index.md)

Apresenta as novas APIs de Unificação que permitir para bases de código mais simples do compartilhamento de código entre Apple TV e iOS, bem como a introdução do suporte para APIs de 64 bits e a compilação de 64 bits.  

###  <a name="api-designiosinternalsapi-designindexmd"></a>[Design de API](~/ios/internals/api-design/index.md)

Explica os princípios de design por trás de API de associação.

###  <a name="limitationsiosinternalslimitationsmd"></a>[Limitações](~/ios/internals/limitations.md)

Esta seção ilustra as armadilhas e as limitações a serem consideradas com relação xamarin. IOS, muitas delas são aplicáveis ao xamarin. tvos.

###  <a name="linkeriosdeploy-testlinkermd"></a>[Vinculador](~/ios/deploy-test/linker.md)

Explica como funciona o vinculador para garantir que o pacote de aplicativos possível menor, bem como para modificar as configurações e uso de TI.

###  <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[Internacionalização e localização](~/ios/app-fundamentals/localization/index.md)

Este guia aborda a adição de codificações para um aplicativo xamarin. IOS para dar suporte à internacionalização.

###  <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

Notas e informações sobre o mtouch.exe, a ferramenta de linha de comando que compila seu projeto em um aplicativo que pode ser usado em iOS.

###  <a name="linking-native-librariesiosplatformnative-interopmd"></a>[Bibliotecas nativas de vinculação](~/ios/platform/native-interop.md)

Xamarin. IOS dá suporte à vinculação com bibliotecas de C nativas e bibliotecas de Objective-C. Este documento aborda como vincular suas bibliotecas C nativas com o seu projeto xamarin. IOS. Para obter informações sobre como fazer o mesmo para bibliotecas de Objective-C, consulte o&nbsp; [tipos de associação de Objective-C](~/ios/platform/binding-objective-c/index.md)&nbsp;documento.

##  <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)

Notas e uso para chamar diretamente o seletores de Objective-C (métodos).

###  <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

Informações e instruções sobre como usar o System. Data para acessar o sistema de banco de dados SQLite interno.

###  <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Observações sobre o uso de threading em aplicativos xamarin. IOS.

###  <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[Geração de código XIB](~/ios/internals/xib-code-generation.md)

Como o Visual Studio para Mac se integra com o Interface Builder do Xcode para que você possa usar o construtor de Interface para o design da interface do usuário.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
