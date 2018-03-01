---
title: Suporte a mais inteligente Xamarin Android v4 / v13 pacotes do NuGet
ms.topic: article
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: ab5ec19498b3c61e988adc959e1751b96f60210d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Suporte a mais inteligente Xamarin Android v4 / v13 pacotes do NuGet

## <a name="about-the-android-support-libraries"></a>Sobre as bibliotecas de suporte do Android

Google criou bibliotecas de suporte para disponibilizar os novos recursos para versões mais antigas do Android. Em geral, as bibliotecas de suporte recebem um número de versão no nome, que é o nível de API do Android mais baixo são compatíveis com (por exemplo: suporte v4 só pode ser usado no API nível 4 e superior. Para obter mais informações na [discussão de estouro de pilha](http://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Duas das bibliotecas de suporte: `Support-v4` e `Support-v13` não podem ser usados juntos no mesmo aplicativo, ou seja, eles são mutuamente exclusivos. Isso ocorre porque `Support-v13` realmente contém todos os tipos e a implementação de `Support-v4`. Se você tentar e referência ambos no mesmo projeto, você encontrará erros de tipo duplicado.

## <a name="problems-with-referencing"></a>Problemas com a referência

Como `Support-v4` tornou tão popular, muitas das bibliotecas de terceiros 3º agora dependem dele. Eles ter escolhido depende de suporte v13 em vez disso, mas é mais comum para depender _v4_ desde que permite que todos os aplicativos usando essas bibliotecas de terceiros 3º de dar suporte a níveis de API até 4.

Se fizer referência a uma biblioteca de terceiros 3º Xamarin o `Xamarin.Android.Support.v4.dll` associando a `Support-v4`, também deve fazer referência a qualquer aplicativo que usa essa biblioteca `Xamarin.Android.Support.v4.dll`. Isso se torna um problema quando o mesmo aplicativo também quer usar algumas das funcionalidades do `Xamarin.Android.Support.v13.dll` associando a `Support-v13`. Se você fizer referência a ambas as associações, você encontrará erros de tipo duplicado.

## <a name="type-forwarded-v4-binding-assembly"></a>Tipo encaminhado v4 associação de Assembly

Para contornar esse problema, criamos um especial `Xamarin.Android.Support.v4.dll` assembly que não tem nenhuma implementação, mas simplesmente `[assembly: TypeForwardedTo (..)]` atributos que encaminham todo o `Support-v4` tipos para implementação no `Xamarin.Android.Support.v13.dll` assembly.

Isso significa que um desenvolvedor pode fazer referência a ele _tipo encaminhado_ assembly em seu aplicativo que atenderá a referência ao `Xamarin.Android.Support.v4.dll` por qualquer 3º bibliotecas de terceiros, enquanto ainda permite que `Xamarin.Android.Support.v13.dll` a ser usado no aplicativo.

## <a name="nuget-assistance"></a>Assistência do NuGet

Enquanto um desenvolvedor pode adicionar manualmente as referências corretas necessárias, somos capazes de usar o NuGet para ajudar a escolher o conjunto correto (ou o normal _v4_ associação ou o tipo encaminhado _v4_ assembly) quando o pacote do NuGet é instalado.

Portanto, o `Xamarin.Android.Support.v4` pacote NuGet agora contém a lógica a seguir:

Se seu aplicativo está direcionando 13 de nível de API (biscoito 3.2) ou superior:

*   `Xamarin.Android.Support.v13` NuGet será adicionado automaticamente como uma dependência
*   O _tipo encaminhado_ `Xamarin.Android.Support.v4.dll` será referenciado no projeto

Se seu aplicativo está direcionando nada menor que 13 de nível de API, você obterá o normal `Xamarin.Android.Support.v4.dll` associação referenciada em seu projeto.

## <a name="do-i-have-to-use-support-v13"></a>É necessário usar v13 suporte?

Se seu aplicativo está direcionando o nível de API 13 ou superior e você optar por usar o `Xamarin Android Support-v4` pacote NuGet, em seguida, o `Xamarin Android Support v13` pacote do NuGet é uma dependência necessária.

Sentimos muito pequeno aumento no tamanho do aplicativo (os dois arquivos. jar diferem por 17kb) é vale a pena a compatibilidade e o resultado de problemas menos.

Se você estiver usando inflexível `Support-v4` em um aplicativo que tem como alvo 13 de nível de API ou superior, você pode baixar manualmente o `.nupkg`, extraí-lo e fazer referência ao assembly.
