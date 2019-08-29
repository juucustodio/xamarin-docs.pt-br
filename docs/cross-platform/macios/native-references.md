---
title: Projetos do iOS, Mac e bindings de referências nativas
description: Referências nativas fornecem a capacidade de inserir uma estrutura nativa em um projeto Xamarin. iOS, Xamarin. Mac ou Binding.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e5e232ffa8a41f7adbffae595b85341a10d8667a
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065267"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Referências nativas em projetos do iOS, Mac e bindings

_Referências nativas fornecem a capacidade de inserir uma estrutura nativa em um projeto do Xamarin. iOS ou Xamarin. Mac ou um projeto de associação._

Desde o iOS 8,0, é possível criar uma estrutura inserida para compartilhar código entre extensões de aplicativo e o aplicativo principal no Xcode. Usando o recurso de referência nativa, será possível consumir essas estruturas inseridas (criadas com o Xcode) no Xamarin. iOS.
 
> [!IMPORTANT]
> Não será possível criar estruturas inseridas de qualquer tipo de projetos Xamarin. iOS ou Xamarin. Mac, as referências nativas permitem apenas o consumo de estruturas nativas (Objective-C) existentes.

<a name="Terminology" />

## <a name="terminology"></a>Terminologia

No iOS 8 (e posterior), as **estruturas inseridas** podem ser estruturas incorporadas estaticamente e vinculadas dinamicamente. Para distribuí-los corretamente, você deve torná-los em estruturas "Fat" que incluíam todas as suas fatias para cada arquitetura de dispositivo para a qual você deseja dar suporte com seu aplicativo.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Estático vs. Estruturas dinâmicas

As **estruturas estáticas** são vinculadas no momento da compilação em que as **estruturas dinâmicas** são vinculadas no tempo de execução e, portanto, podem ser modificadas sem a necessidade de vincular novamente. Se você tiver usado qualquer estrutura de terceiros antes do iOS 8, você estava usando uma **estrutura estática** que foi compilada em seu aplicativo. Consulte a documentação de [programação da biblioteca dinâmica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) da Apple para obter mais detalhes.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Incorporado vs. Estruturas do sistema

As **estruturas inseridas** são incluídas em seu pacote de aplicativos e só podem ser acessadas pelo seu aplicativo específico por meio de sua área restrita. As **estruturas do sistema** são armazenadas no nível do sistema operacional e estão disponíveis para todos os aplicativos no dispositivo. Atualmente, apenas a Apple tem a capacidade de criar estruturas de nível de sistema operacional.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Fino versus Estruturas de Fat

**Estruturas finas** contêm apenas o código compilado para uma arquitetura de sistema específica em que as **estruturas de Fat** contêm código para várias arquiteturas. Cada base de código específica da arquitetura compilada em uma estrutura é conhecida como uma _fatia_. Portanto, por exemplo, se tivéssemos uma estrutura que foi compilada para as duas arquiteturas do simulador do iOS (i386 e x86_64), ela conteria duas fatias.

Se você tentou distribuir essa estrutura de exemplo com seu aplicativo, ela seria executada corretamente no simulador, mas falha no dispositivo, pois a estrutura não contém nenhuma fatia específica de código para um dispositivo iOS. Para garantir que uma estrutura funcione em todas as instâncias, ela também precisaria incluir fatias específicas do dispositivo, como arm64, ARMv7 e armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Trabalhando com estruturas inseridas

Há duas etapas que devem ser concluídas para trabalhar com estruturas inseridas em um aplicativo Xamarin. iOS ou Xamarin. Mac: Criar uma estrutura de FAT e inserir a estrutura.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Criando uma estrutura de Fat

Conforme mencionado acima, para poder consumir uma estrutura incorporada em seu aplicativo, ele deve ser uma estrutura de Fat que inclui todas as fatias de arquiteturas do sistema para os dispositivos em que seu aplicativo será executado.

Quando a estrutura e o aplicativo de consumo estiverem no mesmo projeto do Xcode, isso não será um problema, pois o Xcode criará a estrutura e o aplicativo usando as mesmas configurações de compilação. Como os aplicativos Xamarin não podem criar estruturas inseridas, essa técnica não pode ser usada.

Para resolver esse problema, a `lipo` ferramenta de linha de comando pode ser usada para mesclar duas ou mais estruturas em uma estrutura de Fat que contém todas as fatias necessárias. Para obter mais informações sobre como trabalhar `lipo` com o comando, consulte nossa documentação [vinculando bibliotecas nativas](~/ios/platform/native-interop.md) .

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Inserindo uma estrutura

A etapa a seguir é necessária para inserir uma estrutura em um projeto Xamarin. iOS ou Xamarin. Mac usando referências nativas:

1. Crie um novo ou abra um projeto Xamarin. iOS, Xamarin. Mac ou Binding existente.
2. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Adicionar** > **Adicionar referência nativa**: 

    [![](native-references-images/ref01.png "Na Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto e selecione Adicionar referência nativa")](native-references-images/ref01.png#lightbox)
3. Na caixa de diálogo **abrir** , selecione o nome da estrutura nativa que você deseja inserir e clique no botão **abrir** : 

    [![](native-references-images/ref02.png "Selecione o nome da estrutura nativa a ser inserida e clique no botão abrir")](native-references-images/ref02.png#lightbox)
4. A estrutura será adicionada à árvore do projeto: 

    [![](native-references-images/ref03.png "A estrutura será adicionada à árvore de projetos")](native-references-images/ref03.png#lightbox)

Quando o projeto for compilado, a estrutura nativa será inserida no pacote do aplicativo.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Extensões de aplicativo e estruturas inseridas

O Xamarin. iOS internamente pode aproveitar esse recurso para vincular com o tempo de execução do mono como uma estrutura (quando o destino da implantação é > = iOS 8,0), reduzindo o tamanho do aplicativo significativamente para aplicativos com extensões (já que o tempo de execução do mono será incluído apenas uma vez para todo o pacote de aplicativo, em vez de uma vez para o aplicativo de contêiner e uma vez para cada extensão).

As extensões serão vinculadas ao tempo de execução do mono como uma estrutura, pois todas as extensões exigem iOS 8,0.

Aplicativos que não têm extensões e aplicativos destinados ao iOS 

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo deu uma olhada detalhada na inserção de uma estrutura nativa em um aplicativo Xamarin. iOS ou Xamarin. Mac.

