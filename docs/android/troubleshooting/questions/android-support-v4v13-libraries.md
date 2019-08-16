---
title: Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 64ceacc37a303e69b0dd7073ec6547ed344af51d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523434"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13

## <a name="about-the-android-support-libraries"></a>Sobre as bibliotecas de suporte do Android

O Google criou bibliotecas de suporte para disponibilizar novos recursos para versões mais antigas do Android. Em geral, as bibliotecas de suporte recebem um número de versão em seu nome, que é o nível mais baixo de API do Android com o qual elas são compatíveis (por exemplo: Support-v4 só pode ser usado no nível de API 4 e superior. Mais informações neste [Stack Overflow discussão](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Duas das bibliotecas de suporte: `Support-v4` e `Support-v13` não podem ser usadas juntas no mesmo aplicativo, ou seja, são mutuamente exclusivas. Isso ocorre porque `Support-v13` na verdade contém todos os tipos e a implementação `Support-v4`do. Se você tentar fazer referência a ambos no mesmo projeto, encontrará erros de tipo duplicados.

## <a name="problems-with-referencing"></a>Problemas com a referência

Como `Support-v4` o tornou-se tão popular, muitas bibliotecas de terceiros agora dependem dela. Eles poderiam ter optado por depender do suporte-v13, mas é mais comum depender do _v4_ , uma vez que isso fornece aos aplicativos que usam essas bibliotecas de terceiros a opção de dar suporte a níveis de API até 4.

Se uma biblioteca de terceiros do Xamarin referenciar a `Support-v4`associação a, qualquer aplicativo que usa essa biblioteca também `Xamarin.Android.Support.v4.dll`deverá fazer referência a `Xamarin.Android.Support.v4.dll` . Isso se torna um problema quando o mesmo aplicativo também deseja usar algumas das funcionalidades da `Xamarin.Android.Support.v13.dll` associação a. `Support-v13` Se você referenciar ambas as associações, encontrará erros de tipo duplicados.

## <a name="type-forwarded-v4-binding-assembly"></a>Assembly de associação v4 de tipo encaminhado

Para contornar esse problema, criamos um assembly especial `Xamarin.Android.Support.v4.dll` que não tem implementação, mas simplesmente `[assembly: TypeForwardedTo (..)]` atributos `Support-v4` que encaminham todos os tipos para a implementação dentro do `Xamarin.Android.Support.v13.dll` assembly.

Isso significa que um desenvolvedor pode fazer referência a esse assembly encaminhado por _tipo_ em seu aplicativo, o que `Xamarin.Android.Support.v4.dll` atenderá à referência a todas as bibliotecas de `Xamarin.Android.Support.v13.dll` terceiros, permitindo, ao mesmo tempo, ser usado no aplicativo.

## <a name="nuget-assistance"></a>Assistência do NuGet

Embora um desenvolvedor pudesse adicionar manualmente as referências corretas necessárias, podemos usar o NuGet para ajudar a escolher o assembly correto (a ligação _v4_ normal ou o assembly _v4_ de tipo encaminhado) quando o pacote NuGet é instalado.

Portanto, o `Xamarin.Android.Support.v4` pacote NuGet agora contém a seguinte lógica:

Se seu aplicativo estiver direcionando o nível de API 13 (Gingerbread 3,2) ou superior:

* `Xamarin.Android.Support.v13`O NuGet será adicionado automaticamente como uma dependência
* O _tipo encaminhado_ `Xamarin.Android.Support.v4.dll` será referenciado no projeto

Se seu aplicativo estiver direcionando qualquer coisa inferior ao nível de API 13, você obterá a associação normal `Xamarin.Android.Support.v4.dll` referenciada em seu projeto.

## <a name="do-i-have-to-use-support-v13"></a>Preciso usar o support-v13?

Se seu aplicativo estiver direcionando o nível de API 13 ou superior e você optar `Xamarin Android Support-v4` por usar o pacote NuGet `Xamarin Android Support v13` , o pacote NuGet será uma dependência necessária.

Sentimos o menor aumento no tamanho do aplicativo (os dois arquivos. jar diferem por 17kb) vale a pena a compatibilidade e menos dores de cabeça que eles resultam.

Se você estiver inflexível sobre como `Support-v4` usar em um aplicativo que tem como destino o nível de API 13 ou superior, sempre `.nupkg`será possível baixar manualmente o, extraí-lo e fazer referência ao assembly.
