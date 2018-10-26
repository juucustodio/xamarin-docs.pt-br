---
title: Suporte a mais inteligente do Xamarin Android v4 / pacotes do NuGet v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 43627884c2f8bc4d9e5b5faa2c3af08f74487b65
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114633"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Suporte a mais inteligente do Xamarin Android v4 / pacotes do NuGet v13

## <a name="about-the-android-support-libraries"></a>Sobre as bibliotecas de suporte do Android

Google criou a bibliotecas de suporte para tornar os novos recursos disponíveis para versões mais antigas do Android. Em geral, bibliotecas de suporte recebem um número de versão em seu nome, o que é o menor nível da API Android eles são compatíveis com (por exemplo: v4 suporte só pode ser usado no API nível 4 e superior. Obter mais informações desta [discussão de Stack Overflow](http://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Duas das bibliotecas de suporte: `Support-v4` e `Support-v13` não podem ser usados juntos no mesmo aplicativo, ou seja, eles são mutuamente exclusivos. Isso ocorre porque `Support-v13` , na verdade, contém todos os tipos e a implementação de `Support-v4`. Se você tentar fazer referência a ambos no mesmo projeto, você encontrará erros de tipo duplicado.

## <a name="problems-with-referencing"></a>Problemas com a referência

Uma vez que `Support-v4` se tornou tão popular, muitas bibliotecas de terceiros 3ª agora dependem dele. Eles poderia ter optado por dependem v13 suporte em vez disso, mas é mais comum para dependem _v4_ , pois isso dá a todos os aplicativos usando essas bibliotecas de terceiros 3ª a opção de dar suporte a níveis de API até 4.

Se fizer referência a uma biblioteca de terceiros Xamarin 3º a `Xamarin.Android.Support.v4.dll` associando a `Support-v4`, também deve fazer referência a qualquer aplicativo que usa essa biblioteca `Xamarin.Android.Support.v4.dll`. Isso se torna um problema quando o mesmo aplicativo também queira usar algumas das funcionalidades do `Xamarin.Android.Support.v13.dll` associando a `Support-v13`. Se você fizer referência a ambas as associações, você encontrará erros de tipo duplicado.

## <a name="type-forwarded-v4-binding-assembly"></a>O tipo encaminhado v4 associação de Assembly

Para contornar esse problema, criamos um especial `Xamarin.Android.Support.v4.dll` assembly que não tem nenhuma implementação, mas apenas `[assembly: TypeForwardedTo (..)]` atributos que encaminham todo o `Support-v4` tipos para a implementação dentro a `Xamarin.Android.Support.v13.dll` assembly.

Isso significa que um desenvolvedor pode fazer referência a esse _tipo encaminhado_ assembly em seu aplicativo que vai satisfazer a referência ao `Xamarin.Android.Support.v4.dll` por quaisquer bibliotecas de terceiros 3ª, enquanto ainda permite que `Xamarin.Android.Support.v13.dll` a ser usado no aplicativo.

## <a name="nuget-assistance"></a>Assistência de NuGet

Embora um desenvolvedor pode adicionar manualmente referências corretas necessárias, somos capazes de usar o NuGet para ajudar a escolher o conjunto correto (a interface de normal _v4_ associação ou o tipo encaminhado _v4_ assembly) quando o pacote do NuGet está instalado.

Portanto, o `Xamarin.Android.Support.v4` pacote NuGet agora contém a lógica a seguir:

Se seu aplicativo for direcionado a API nível 13 (Gingerbread 3.2) ou superior:

*   `Xamarin.Android.Support.v13` NuGet será adicionado automaticamente como uma dependência
*   O _tipo encaminhado_ `Xamarin.Android.Support.v4.dll` será referenciado no projeto

Se seu aplicativo for direcionado a qualquer coisa menor do que a API nível 13, você obterá o normal `Xamarin.Android.Support.v4.dll` referenciada no seu projeto de associação.

## <a name="do-i-have-to-use-support-v13"></a>É necessário usar v13 suporte?

Se seu aplicativo for direcionado a API nível 13 ou superior e você optar por usar o `Xamarin Android Support-v4` pacote do NuGet, em seguida, a `Xamarin Android Support v13` pacote do NuGet é uma dependência necessária.

Sentimos muito pequeno aumento no tamanho de aplicativo (os dois arquivos. jar diferem por 17kb) é vale a pena a compatibilidade e menos dores de cabeça, em que isso resulta.

Se você estiver inflexível usando `Support-v4` em um aplicativo que tem como alvo API nível 13 ou superior, você pode baixar manualmente o `.nupkg`, extraí-lo e fazer referência ao assembly.
