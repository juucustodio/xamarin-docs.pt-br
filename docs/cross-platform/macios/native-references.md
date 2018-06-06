---
title: Native iOS, Mac e projetos de associações de referências
description: Referências nativo fornece a capacidade de inserir uma estrutura nativo em um projeto de associação, Xamarin.Mac ou xamarin.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3a497d0bb4674014b8063cb1fbc91eec6e7ae5ea
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781712"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Referências nativo no iOS, Mac e associações de projetos

_Referências nativo fornece a capacidade de inserir uma estrutura nativo em um projeto xamarin ou Xamarin.Mac ou associação._

Desde o iOS 8.0 foi possível criar uma estrutura incorporada para compartilhar código entre as extensões de aplicativo e o aplicativo principal no Xcode. Usando o recurso de referência nativa será possível consumir essas estruturas inseridas (criadas com o Xcode) em xamarin.
 
> [!IMPORTANT]
> Referências nativo não será possível criar estruturas inseridas de qualquer tipo de xamarin ou Xamarin.Mac projetos, permitir apenas para o consumo de estruturas existentes de nativo (Objective-C).

<a name="Terminology" />

## <a name="terminology"></a>Terminologia

No iOS 8 (e posterior), **estruturas inseridas** pode ser incorporado estruturas estaticamente vinculadas e vinculadas dinamicamente. Para distribuí-las, você deve torná-los em estruturas "fat" que incluídos todos os seus _fatias_ para cada arquitetura de dispositivo que você deseja dar suporte ao seu aplicativo.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Portas estáticas vs. Estruturas dinâmicas

**Estruturas estáticas** são vinculados em tempo de compilação onde **estruturas dinâmico** são vinculados em tempo de execução e, portanto, pode ser modificada sem vinculá-lo novamente. Se você usou qualquer estrutura 3ª parte antes de iOS 8, você estava usando um **Framework estático** que foi compilado em seu aplicativo. Consulte da Apple [programação dinâmica de biblioteca](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) documentação para obter mais detalhes.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Vs incorporados. Estruturas de sistema

**Inserido estruturas** estão incluídos no pacote de aplicativos e só são acessíveis para o aplicativo específico por meio de sua área restrita. **Estruturas de sistema** são armazenadas no nível do sistema operacional e estão disponíveis para todos os aplicativos no dispositivo. Atualmente, apenas a Apple tem a capacidade de criar estruturas de nível de sistema operacional.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Thin vs. Estruturas FAT

**Thin estruturas** contém apenas o código compilado para uma arquitetura de sistema específica onde **estruturas Fat** contém código para várias arquiteturas. Cada base de código específicos de arquitetura compilado em uma estrutura é conhecido como um _fatia_. Assim, por exemplo, se tivéssemos uma estrutura que foi compilada para os duas iOS arquiteturas de simulador (i386 e X86_64), ele conteria duas fatias.

Se você tentou distribuir esse exemplo Framework com seu aplicativo, ele seria executado corretamente no simulador, mas falhar no dispositivo, porque a estrutura contém as fatias de código específico para um dispositivo iOS. Para garantir que uma estrutura funcione em todas as instâncias, ele também precisa incluir fatias de dispositivo específico, como arm64, armv7 e armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Trabalhando com estruturas inseridas

Há duas etapas que devem ser concluídas para trabalhar com estruturas inseridas em um aplicativo xamarin ou Xamarin.Mac: Criando uma estrutura Fat e inserindo a estrutura.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Criando uma estrutura Fat

Como mencionado acima, para poder consumir uma estrutura inseridos em seu aplicativo, ele deve ser uma estrutura Fat que inclui todas as arquiteturas de sistema fatias para os dispositivos que o aplicativo será executado em.

Quando a estrutura e o aplicativo de consumo estão no mesmo projeto Xcode, isso não é um problema como Xcode criará a estrutura e o aplicativo usando as mesmas configurações de compilação. Como aplicativos Xamarin não é possível criar estruturas inserida, essa técnica não pode ser usada.

Para resolver esse problema, o `lipo` ferramenta de linha de comando pode ser usada para mesclar duas ou mais estruturas em uma única estrutura Fat que contém todas as fatias necessário. Para obter mais informações sobre como trabalhar com o `lipo` de comando, consulte nosso [vinculação bibliotecas nativas](~/ios/platform/native-interop.md) documentação.

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Inserindo uma estrutura

A etapa de acompanhamento são necessárias para incorporar uma estrutura em um projeto de xamarin ou Xamarin.Mac usando referências nativo:

1. Criar um novo ou abrir um projeto existente de xamarin, Xamarin.Mac ou associação.
2. No **Solution Explorer**, com o botão direito no nome do projeto e selecione **adicionar** > **adicionar referência nativa**: 

    [![](native-references-images/ref01.png "No Gerenciador de soluções, clique com botão direito no nome do projeto e selecione Adicionar referência nativa")](native-references-images/ref01.png#lightbox)
3. Do **abrir** caixa de diálogo, selecione o nome do Framework nativo que você deseja inserir e clique no **abrir** botão: 

    [![](native-references-images/ref02.png "Selecione o nome da estrutura nativo para inserir e clique no botão Abrir")](native-references-images/ref02.png#lightbox)
4. A estrutura será adicionada à árvore do projeto: 

    [![](native-references-images/ref03.png "A estrutura será adicionada à árvore de projetos")](native-references-images/ref03.png#lightbox)

Quando o projeto é compilado, o Framework nativo será inserido no pacote do aplicativo.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Extensões de aplicativo e estruturas inseridas

Internamente xamarin pode tirar proveito desse recurso para vincular com o tempo de execução Mono como uma estrutura (quando o destino de implantação é > = iOS 8.0), reduzindo o tamanho do aplicativo significativamente para aplicativos com extensões (desde o tempo de execução Mono será incluído apenas uma vez para o aplicativo inteiro pacote, em vez de uma vez para o aplicativo de contêiner e uma vez para cada extensão).

Extensões serão vinculado com o tempo de execução Mono como uma estrutura, porque todas as extensões exigem iOS 8.0.

Aplicativos que não têm extensões e aplicativos que iOS de destino 

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de inserir uma estrutura nativo em um aplicativo xamarin ou Xamarin.Mac.

