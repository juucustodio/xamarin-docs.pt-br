---
title: IOS, Mac e vinculações de projetos referências nativas
description: Referências nativas lhe dá a capacidade de inserir uma estrutura nativa em um projeto de associação, o xamarin. Mac ou o xamarin. IOS.
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3a497d0bb4674014b8063cb1fbc91eec6e7ae5ea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61201521"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>Referências nativas no iOS, Mac e associações de projetos

_Referências nativas lhe dá a capacidade de inserir uma estrutura nativa em um projeto xamarin. IOS ou xamarin. Mac ou o projeto de associação._

Desde o iOS 8.0 foi possível criar uma estrutura inserida para compartilhar código entre extensões de aplicativo e o aplicativo principal no Xcode. Usando o recurso de referência nativa será possível consumir essas estruturas incorporadas (criadas com o Xcode) no xamarin. IOS.
 
> [!IMPORTANT]
> Referências nativas não será possível criar estruturas incorporadas a partir de qualquer tipo de projetos do xamarin. IOS ou xamarin. Mac, permitir somente para o consumo das estruturas de (Objective-C) nativos existentes.

<a name="Terminology" />

## <a name="terminology"></a>Terminologia

No iOS 8 (e posterior), **estruturas incorporadas** pode ser incorporado estruturas vinculadas estaticamente e vinculadas dinamicamente. Para distribuí-las, você precisa torná-los com as estruturas de "fat" que incluídos todos os seus _fatias_ para cada arquitetura de dispositivo que você deseja dar suporte com seu aplicativo.

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>Portas estáticas vs. Estruturas dinâmicas

**Estruturas estáticas** vinculadas no tempo de compilação em que **estruturas dinâmico** vinculadas no tempo de execução e, portanto, pode ser modificado sem vinculá-lo novamente. Se você tiver usado qualquer estrutura de terceiros 3º antes do iOS 8, você estava usando um **Framework estático** que foi compilado em seu aplicativo. Consulte da Apple [programação de biblioteca dinâmica](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1) documentação para obter mais detalhes.

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Vs incorporados. Estruturas de sistema

**Inserido estruturas** estão incluídos no pacote de aplicativos e só são acessíveis ao seu aplicativo específico por meio de sua área restrita. **Estruturas de sistema** são armazenadas no nível do sistema operacional e estão disponíveis para todos os aplicativos no dispositivo. Atualmente, somente o Apple tem a capacidade de criar estruturas de nível de sistema operacional.

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>Vs finas. Estruturas FAT

**Thin estruturas** contêm somente o código compilado para uma arquitetura de sistema específica em que **estruturas Fat** contêm código para várias arquiteturas. Cada base de código específicos de arquitetura compilado em uma estrutura é conhecido como um _fatia_. Portanto, por exemplo, se tivéssemos uma estrutura que foi compilada para o duas iOS arquiteturas de simulador (i386 e X86_64), ele conteria duas fatias.

Se você tentar distribuir esse exemplo Framework com seu aplicativo, ele seria executado corretamente no simulador, mas falhar no dispositivo, pois a estrutura não contém as fatias de código específico para um dispositivo iOS. Para garantir que uma estrutura funcionarão em todas as instâncias, ele também precisará incluir fatias específicas de dispositivo, como arm64, armv7 e armv7s.

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>Trabalhando com estruturas incorporadas

Há duas etapas que devem ser concluídas para trabalhar com estruturas incorporadas em um aplicativo xamarin. IOS ou xamarin. Mac: Criando uma estrutura Fat e incorporação do Framework.

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>Criando uma estrutura Fat

Conforme mencionado acima, para ser capaz de consumir uma estrutura inserida em seu aplicativo, ele deve ser uma estrutura Fat que inclui todas as arquiteturas de sistema fatias para que seu aplicativo será executado em dispositivos.

Quando a estrutura e o aplicativo de consumo estão no mesmo projeto Xcode, isso não é um problema de como o Xcode criará a estrutura e o aplicativo usando as mesmas configurações de compilação. Uma vez que os aplicativos Xamarin não é possível criar estruturas incorporadas, essa técnica não pode ser usada.

Para resolver esse problema, o `lipo` ferramenta de linha de comando pode ser usada para mesclar dois ou mais estruturas em uma estrutura Fat, que contém todas as fatias necessárias. Para obter mais informações sobre como trabalhar com o `lipo` de comando, consulte nossos [vinculação a bibliotecas nativas](~/ios/platform/native-interop.md) documentação.

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>Inserção de uma estrutura

Etapa a seguir são necessários para inserir uma estrutura em um projeto xamarin. IOS ou xamarin. Mac usando referências nativas:

1. Criar um novo ou abrir um projeto existente do xamarin. IOS, xamarin. Mac ou associação.
2. No **Gerenciador de soluções**, clique com botão direito no nome do projeto e selecione **Add** > **adicionar referência nativa**: 

    [![](native-references-images/ref01.png "No Gerenciador de soluções, clique com botão direito no nome do projeto e selecione Adicionar referência nativa")](native-references-images/ref01.png#lightbox)
3. Dos **aberto** caixa de diálogo, selecione o nome da estrutura nativa que você deseja inserir e clique no **abrir** botão: 

    [![](native-references-images/ref02.png "Selecione o nome da estrutura nativa para inserir e clique no botão Abrir")](native-references-images/ref02.png#lightbox)
4. A estrutura será adicionada à árvore do projeto: 

    [![](native-references-images/ref03.png "A estrutura será adicionada à árvore de projetos")](native-references-images/ref03.png#lightbox)

Quando o projeto é compilado, a estrutura nativa será inserida no pacote do aplicativo.

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>Extensões de aplicativo e estruturas incorporadas

Internamente o xamarin. IOS podem tirar proveito desse recurso para vincular com o tempo de execução Mono, como uma estrutura (quando o destino da implantação for > = iOS 8.0), reduzindo o tamanho do aplicativo significativamente para aplicativos com extensões (já que o tempo de execução Mono será incluído apenas uma vez para todo pacote de aplicativo, em vez de uma vez para o aplicativo de contêiner e uma vez para cada extensão).

Extensões vinculará com tempo de execução Mono como uma estrutura, porque todas as extensões requerem iOS 8.0.

Aplicativos que não têm extensões e aplicativos se iOS de destino 

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como inserir uma estrutura nativa em um aplicativo xamarin. IOS ou xamarin. Mac.

