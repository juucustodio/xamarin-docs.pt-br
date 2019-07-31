---
title: tvOS no Xamarin – interno
description: Documentos que descrevem o funcionamento interno do tvOS no Xamarin, que se baseia no Xamarin. iOS. O conteúdo do link discute assemblies, estruturas de destino e conceitos de iOS relacionados.
ms.prod: xamarin
ms.assetid: 8C076FED-9C03-44DE-9723-0E20272DD16B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 45341418779405e52251eb07576c8e5fa9f09297
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647109"
---
# <a name="tvos-in-xamarin-internals"></a>tvOS no Xamarin – interno 

## <a name="assembliesiostvosinternalsassembliesmd"></a>[Assemblies](~/ios/tvos/internals/assemblies.md)

Lista dos assemblies com suporte do Xamarin para seus aplicativos Xamarin. tvOS.

## <a name="target-frameworksiostvosinternalsframeworksmd"></a>[Estruturas de Destino](~/ios/tvos/internals/frameworks.md)

Este artigo aborda os tipos de estruturas de destino (bibliotecas de classes base) que estão disponíveis no Xamarin. tvOS e as implicações de selecionar um destino específico para seu aplicativo Xamarin. tvOS.

## <a name="related-ios-articles"></a>Artigos do iOS relacionados

Os artigos a seguir são específicos para iOS, mas relevantes para tvOS (desde que o tvOS 9 seja um subconjunto do iOS 9).

### <a name="unified-apicross-platformmaciosunifiedindexmd"></a>[API Unificada](~/cross-platform/macios/unified/index.md)

Apresenta as novas APIs unificadas que permitem o compartilhamento de código mais simples entre as bases de TV da Apple e do iOS, além de introduzir o suporte para APIs de 64 bits e a compilação de 64 bits.  

### <a name="api-designiosinternalsapi-designindexmd"></a>[Design de API](~/ios/internals/api-design/index.md)

Explica os princípios de design por trás da Associação de API.

### <a name="limitationsiosinternalslimitationsmd"></a>[Limitações](~/ios/internals/limitations.md)

Esta seção ilustra as armadilhas e limitações a serem consideradas com relação ao Xamarin. iOS, muitas das quais são aplicáveis ao Xamarin. tvOS.

### <a name="linkeriosdeploy-testlinkermd"></a>[Vinculador](~/ios/deploy-test/linker.md)

Explica como o vinculador funciona para garantir o menor pacote de aplicativos possível, bem como modificar as configurações e o uso.

### <a name="localization-and-internationalizationiosapp-fundamentalslocalizationindexmd"></a>[Localização e internacionalização](~/ios/app-fundamentals/localization/index.md)

Este guia aborda a adição de codificações a um aplicativo Xamarin. iOS para dar suporte à internacionalização.

### <a name="mtouchiosdeploy-testmtouchmd"></a>[mtouch](~/ios/deploy-test/mtouch.md)

Notas e informações sobre o mtouch.exe, a ferramenta de linha de comando que compila seu projeto em um aplicativo que pode ser usado em iOS.

### <a name="linking-native-librariesiosplatformnative-interopmd"></a>[Vinculando bibliotecas nativas](~/ios/platform/native-interop.md)

O Xamarin. iOS dá suporte à vinculação com bibliotecas C nativas e bibliotecas objec-C. Este documento discute como vincular suas bibliotecas nativas do C ao seu projeto do Xamarin. iOS. Para obter informações sobre como fazer o mesmo para bibliotecas Objective-c&nbsp; , consulte o documento Associação de&nbsp; [tipos Objective-c](~/ios/platform/binding-objective-c/index.md).

## <a name="objective-c-selectorsiosinternalsobjective-c-selectorsmd"></a>[Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)

Observações e uso para chamar os seletores de Objective-C (métodos) diretamente.

### <a name="systemdataiosdata-cloudsystemdatamd"></a>[System.Data](~/ios/data-cloud/system.data.md)

Informações e instruções sobre como usar System. Data para acessar o sistema de banco de dados SQLite interno.

### <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Observações sobre o uso de Threading em aplicativos Xamarin. iOS.

### <a name="xib-code-generationiosinternalsxib-code-generationmd"></a>[Geração de código XIB](~/ios/internals/xib-code-generation.md)

Como o Visual Studio para Mac se integra ao Interface Builder do Xcode para permitir que você use Interface Builder para criar a interface do usuário.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
