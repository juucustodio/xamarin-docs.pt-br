---
title: Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: c74cac6a6d669385855999a565711a3fdc85f3b7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019536"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13

## <a name="about-the-android-support-libraries"></a>Sobre as bibliotecas de suporte do Android

O Google criou bibliotecas de suporte para disponibilizar novos recursos para versões mais antigas do Android. Em geral, as bibliotecas de suporte recebem um número de versão em seu nome, que é o nível mais baixo de API do Android com o qual elas são compatíveis (por exemplo: support-v4 só pode ser usado no nível de API 4 e superior. Mais informações neste [Stack Overflow discussão](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Duas das bibliotecas de suporte: `Support-v4` e `Support-v13` não podem ser usadas juntas no mesmo aplicativo, ou seja, são mutuamente exclusivas. Isso ocorre porque `Support-v13` realmente contém todos os tipos e a implementação de `Support-v4`. Se você tentar fazer referência a ambos no mesmo projeto, encontrará erros de tipo duplicados.

## <a name="problems-with-referencing"></a>Problemas com a referência

Como `Support-v4` se tornou tão popular, muitas bibliotecas de terceiros agora dependem dela. Eles poderiam ter optado por depender do suporte-v13, mas é mais comum depender do _v4_ , uma vez que isso fornece aos aplicativos que usam essas bibliotecas de terceiros a opção de dar suporte a níveis de API até 4.

Se uma biblioteca de terceiros do Xamarin referenciar a associação de `Xamarin.Android.Support.v4.dll` para `Support-v4`, qualquer aplicativo que usa essa biblioteca também deverá fazer referência a `Xamarin.Android.Support.v4.dll`. Isso se torna um problema quando o mesmo aplicativo também deseja usar algumas das funcionalidades da Associação de `Xamarin.Android.Support.v13.dll` para `Support-v13`. Se você referenciar ambas as associações, encontrará erros de tipo duplicados.

## <a name="type-forwarded-v4-binding-assembly"></a>Assembly de associação v4 de tipo encaminhado

Para contornar esse problema, criamos um assembly de `Xamarin.Android.Support.v4.dll` especial que não tem implementação, mas simplesmente `[assembly: TypeForwardedTo (..)]` atributos que encaminham todos os tipos de `Support-v4` para a implementação dentro do assembly `Xamarin.Android.Support.v13.dll`.

Isso significa que um desenvolvedor pode fazer referência a esse assembly _encaminhado por tipo_ em seu aplicativo, o que atenderá à referência a `Xamarin.Android.Support.v4.dll` por quaisquer bibliotecas de terceiros, enquanto ainda permite que `Xamarin.Android.Support.v13.dll` seja usado no aplicativo.

## <a name="nuget-assistance"></a>Assistência do NuGet

Embora um desenvolvedor pudesse adicionar manualmente as referências corretas necessárias, podemos usar o NuGet para ajudar a escolher o assembly correto (a ligação _v4_ normal ou o assembly _v4_ de tipo encaminhado) quando o pacote NuGet é instalado.

Portanto, o pacote NuGet `Xamarin.Android.Support.v4` agora contém a seguinte lógica:

Se seu aplicativo estiver direcionando o nível de API 13 (Gingerbread 3,2) ou superior:

* `Xamarin.Android.Support.v13` NuGet será adicionado automaticamente como uma dependência
* O `Xamarin.Android.Support.v4.dll` de _encaminhamento de tipo_ será referenciado no projeto

Se seu aplicativo estiver direcionando a qualquer item inferior ao nível da API 13, você obterá a associação de `Xamarin.Android.Support.v4.dll` normal referenciada em seu projeto.

## <a name="do-i-have-to-use-support-v13"></a>Preciso usar o support-v13?

Se seu aplicativo estiver direcionando o nível de API 13 ou superior e você optar por usar o `Xamarin Android Support-v4` pacote NuGet, o `Xamarin Android Support v13` pacote NuGet será uma dependência necessária.

Sentimos o menor aumento no tamanho do aplicativo (os dois arquivos. jar diferem por 17kb) vale a pena a compatibilidade e menos dores de cabeça que eles resultam.

Se você estiver inflexível sobre como usar `Support-v4` em um aplicativo que tenha como destino o nível de API 13 ou superior, sempre será possível baixar manualmente o `.nupkg`, extraí-lo e fazer referência ao assembly.
