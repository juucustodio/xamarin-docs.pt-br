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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019536"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13

## <a name="about-the-android-support-libraries"></a>Sobre as Bibliotecas de Suporte ao Android

O Google criou bibliotecas de suporte para disponibilizar novos recursos para versões mais antigas do Android. Em geral, as Bibliotecas de Suporte recebem um número de versão em seu nome, que é o nível de API android mais baixo com o qual são compatíveis (por exemplo: o Support-v4 só pode ser usado em API Nível 4 ou superior. Mais informações nesta [discussão Stack Overflow](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Duas das bibliotecas `Support-v4` de `Support-v13` suporte: e não podem ser usadas juntas no mesmo aplicativo, ou seja, são mutuamente exclusivas. Isso porque, `Support-v13` na verdade, contém `Support-v4`todos os tipos e implementação de . Se você tentar referenciar ambos no mesmo projeto, você encontrará erros de tipo duplicados.

## <a name="problems-with-referencing"></a>Problemas com referências

Desde `Support-v4` que se tornou tão popular, muitas bibliotecas de terceiros agora dependem disso. Eles poderiam ter escolhido depender do Support-v13 em vez disso, mas é mais comum depender _do v4,_ já que isso dá a qualquer aplicativo que use essas bibliotecas de terceiros a opção de suportar níveis de API até 4.

Se uma biblioteca de terceiros Xamarin fizer referência à `Xamarin.Android.Support.v4.dll` vinculação, `Support-v4` `Xamarin.Android.Support.v4.dll`qualquer aplicativo que use esta biblioteca também deve fazer referência . Isso se torna um problema quando o mesmo aplicativo também `Xamarin.Android.Support.v13.dll` quer `Support-v13`usar algumas das funcionalidades da vinculação para . Se você referenciar ambas as vinculações, encontrará erros de tipo duplicados.

## <a name="type-forwarded-v4-binding-assembly"></a>Conjunto de vinculação v4 encaminhado por tipo

Para contornar esse problema, criamos uma `Xamarin.Android.Support.v4.dll` assembleia especial que não `[assembly: TypeForwardedTo (..)]` tem implementação, `Support-v4` mas simplesmente atributos que encaminham todos os tipos para a implementação dentro da `Xamarin.Android.Support.v13.dll` montagem.

Isso significa que um desenvolvedor pode referenciar esse _conjunto encaminhado_ `Xamarin.Android.Support.v4.dll` em seu aplicativo que irá satisfazer `Xamarin.Android.Support.v13.dll` a referência de qualquer biblioteca de terceiros, enquanto ainda permite ser usado no aplicativo.

## <a name="nuget-assistance"></a>Assistência nuget

Embora um desenvolvedor possa adicionar manualmente as referências corretas necessárias, podemos usar o NuGet para ajudar a escolher o conjunto certo (ou a vinculação _v4_ normal ou o conjunto _v4_ encaminhado por tipo) quando o pacote NuGet estiver instalado.

Assim, o `Xamarin.Android.Support.v4` pacote NuGet agora contém a seguinte lógica:

Se o seu aplicativo estiver mirando a API Nível 13 (Gingerbread 3.2) ou superior:

* `Xamarin.Android.Support.v13`NuGet será automaticamente adicionado como uma dependência
* O _tipo encaminhado_ `Xamarin.Android.Support.v4.dll` será referenciado no projeto

Se o seu aplicativo estiver mirando algo abaixo do Nível `Xamarin.Android.Support.v4.dll` 13 da API, você terá a vinculação normal referenciada em seu projeto.

## <a name="do-i-have-to-use-support-v13"></a>Tenho que usar o Support-v13?

Se o seu aplicativo estiver mirando a API Nível `Xamarin Android Support-v4` 13 ou superior `Xamarin Android Support v13` e você optar por usar o pacote NuGet, então o pacote NuGet é uma dependência necessária.

Sentimos que o aumento muito pequeno no tamanho do aplicativo (os dois arquivos .jar diferem em 17kb) vale a pena a compatibilidade e menos dores de cabeça que resulta.

Se você for `Support-v4` inflexível quanto ao uso em um aplicativo que tenha como alvo `.nupkg`a API Nível 13 ou superior, você sempre pode baixar manualmente o , extrai-o e faz referência ao conjunto.
