---
title: Pesquisa com NSUserActivity no xamarin. IOS
description: Este documento descreve como indexar um NSUserActivity, tornando pesquisáveis em destaque e Safari. Ele discute como responder a seleção de um NSUserActivity nos resultados da pesquisa.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: b853bd3bc55a2d4cb613a9f0079aebae9f57027b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357000"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Pesquisa com NSUserActivity no xamarin. IOS

`NSUserActivity` foi introduzido no iOS 8 e é usado para fornecer os dados para entrega.
Ele permite que você criar atividades em partes específicas do aplicativo que pode ser passado para outra instância do seu aplicativo em execução em um dispositivo iOS diferente. Dispositivo de recebimento, em seguida, pode continuar a atividade iniciada no dispositivo anterior, pegar a direita em que o usuário parou. Para obter mais informações sobre como usar a entrega, consulte nosso [Introdução à entrega](~/ios/platform/handoff.md) documentação.

Novo no iOS 9, `NSUserActivity` podem ser indexadas (publicamente e em particular) e pesquisada da pesquisa de destaque e Safari. Marcando um `NSUserActivity` como pesquisável e a adição de metadados indexáveis, a atividade pode ser listada nos resultados da pesquisa no dispositivo iOS.

[![](nsuseractivity-images/apphistory01.png "Visão geral do histórico do aplicativo")](nsuseractivity-images/apphistory01.png#lightbox)

Se o usuário seleciona um resultado de pesquisa que pertence a uma atividade do seu aplicativo, o aplicativo será iniciado e a atividade descrito pelo `NSUserActivity` será reiniciado e apresentada ao usuário.

As seguintes propriedades de `NSUserActivity` são usados para dar suporte à pesquisa de aplicativo:

 - `EligibleForHandoff` – Se `true`, essa atividade pode ser usada em uma operação de entrega.
 - `EligibleForSearch` – Se `true`, essa atividade será adicionada ao índice no dispositivo e verá nos resultados da pesquisa.
 - `EligibleForPublicIndexing` – Se `true`, essa atividade será adicionada ao índice de baseado em nuvem da Apple e apresentada aos usuários que ainda não tiver instalado o aplicativo em seu dispositivo iOS (por meio de pesquisa). Consulte a [indexação de pesquisa público](#public-search-indexing) seção abaixo para obter mais detalhes.
 - `Title` – Fornece um título para que sua atividade e é exibido nos resultados da pesquisa. Os usuários também podem pesquisar o texto do título em si.
 - `Keywords` – É uma matriz de cadeias de caracteres usada para descrever sua atividade que será indexada e tornar pesquisável pelo usuário final.
 - `ContentAttributeSet` – É um `CSSearchableItemAttributeSet` usado para descrever sua atividade em detalhes ainda mais e fornecer conteúdo avançado nos resultados da pesquisa.
 - `ExpirationDate` – Se você quiser que uma atividade para ser mostrado somente para uma determinada data, você pode fornecer essa data aqui.
 - `WebpageURL` – Se a atividade pode ser exibida na web, ou se seu aplicativo dá suporte a links profundos do Safari, você pode definir o link para visitar aqui.

## <a name="nsuseractivity-quickstart"></a>Guia de início rápido NSUserActivity

Siga estas instruções para implementar um recurso de pesquisa `NSUserActivity` em seu aplicativo:

- [Criação de identificadores de tipo de atividade](#creatingtypeid)
- [Criando uma atividade](#createactivity)
- [Respondendo a uma atividade](#respondactivity)
- [Indexação de pesquisa pública](#indexing)

Há algumas [benefícios adicionais](#benefits) usando `NSUserActivity` para que o conteúdo pesquisável.

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>Criação de identificadores de tipo de atividade

Antes de criar uma atividade de pesquisa, você precisará criar uma _identificador de tipo de atividade_ para identificá-lo. O identificador de tipo de atividade é uma cadeia de caracteres curta adicionada para o `NSUserActivityTypes` matriz do aplicativo **Info. plist** arquivo usado para identificar exclusivamente um determinado tipo de atividade do usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo oferece suporte e expõe para o aplicativo de pesquisa. 

Apple sugere o uso de uma notação de estilo de DNS reverso para o identificador de tipo de atividade para evitar colisões. Por exemplo: `com.company-name.appname.activity` para o aplicativo específico com base em atividades ou `com.company-name.activity` para atividades que podem ser executados em vários aplicativos.

O identificador de tipo de atividade é usado ao criar um `NSUserActivity` instância para identificar o tipo de atividade. Quando uma atividade é continuada como resultado do usuário tocar em um resultado de pesquisa, o tipo de atividade (juntamente com a ID do aplicativo Team) determina qual do aplicativo ser iniciado para continuar a atividade.

Para criar os identificadores de tipo de atividade necessário para dar suporte a esse comportamento, edite o **Info. plist** do arquivo e mude para o **origem** modo de exibição. Adicionar um `NSUserActivityTypes` de chave e criar identificadores no seguinte formato:

[![](nsuseractivity-images/type01.png "A chave NSUserActivityTypes e identificadores necessários no editor de plist")](nsuseractivity-images/type01.png#lightbox)

No exemplo acima, criamos um novo identificador de tipo de atividade para a atividade de pesquisa (`com.xamarin.platform`). Ao criar seus próprios aplicativos, substitua o conteúdo do `NSUserActivityTypes` de matriz com os identificadores de tipo de atividade específica para as atividades do seu aplicativo dá suporte.

<a name="createactivity" />

## <a name="creating-an-activity"></a>Criando uma atividade

Este é um exemplo de como criar uma atividade para uma pesquisa de índice no dispositivo hospedado:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.BecomeCurrent();
```

Poderíamos adicionar ainda mais detalhes, definindo o `ContentAttributeSet` propriedade do nosso `NSUserActivity` da seguinte maneira:

[![](nsuseractivity-images/apphistory02.png "Visão geral de detalhes de pesquisa de adição")](nsuseractivity-images/apphistory02.png#lightbox)

Usando um `ContentAttributeSet` você pode criar resultados de pesquisa avançada que convencer o usuário final interaja com eles.

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>Respondendo a uma atividade

Para responder ao usuário tocar em um resultado de pesquisa (`NSUserActivity`) para nosso aplicativo, edite o **AppDelegate.cs** do arquivo e substituir o `ContinueUserActivity` método. Por exemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    }

    return true;
}
```

Observe que isso é a mesma substituição do método usada para responder às solicitações de entrega. Agora se o usuário clica em um link de nosso aplicativo nos resultados da pesquisa de destaque, nosso aplicativo será colocado em primeiro plano (ou foi iniciado se não estiver em execução) e o conteúdo, a navegação ou o recurso representado por esse link será exibido:

[![](nsuseractivity-images/apphistory03.png "Restaurar o estado anterior da pesquisa")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>Indexação de pesquisa pública

Como vimos anteriormente, o iOS 9 facilita fornecer acesso de pesquisa ao conteúdo do aplicativo e os recursos que o usuário já tiver descoberto e usado em um determinado dispositivo iOS. Com indexação público, o iOS 9 fornece uma maneira para os usuários que ainda não tiver descoberto o conteúdo ou recursos ainda (ou até mesmo que ainda não instalou o aplicativo) para obter esses resultados em suas pesquisas muito.

Indexação pública funciona da seguinte maneira:

1. Quando você cria uma atividade para seu aplicativo pode ser marcado como público.
2. Atividades públicas são enviadas para a Apple e indexadas na nuvem.
3. Como mais usuários interajam com seu aplicativo em um dispositivo e usam o recurso específico ou o conteúdo representado pela atividade, ele sobe na classificação.
4. Resultados públicos populares estará disponíveis para outros usuários, mesmo se não tiverem o aplicativo instalado.
5. Esses resultados públicos aparecerá na pesquisa de destaque e Safari (se a atividade inclui uma URL).

Podemos levar a atividade de pesquisa privado que criamos acima e expandi-la para ser pública:

```csharp
// Create App Search Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Add App Search ability
activity.EligibleForSearch = true;
activity.EligibleForPublicIndexing = true;
activity.BecomeCurrent();
```

Só porque uma atividade foi definida para indexação de público definindo `EligibleForPublicIndexing = true`, isso não significa que ele será automaticamente adicionado ao índice de nuvem pública da Apple. As seguintes condições devem ser atendidas pela primeira vez:

1. Ele deve aparecer nos resultados da pesquisa e ser selecionado por muitos usuários. Os resultados permanecerão privados até que um limite de compromisso de atividade foi atendido.
2. Provisionamento de aplicativo impede que todos os dados específicos do usuário do que está sendo indexado e tornar público.

<a name="benefits" />

## <a name="additional-benefits"></a>Benefícios adicionais

Adotando o aplicativo de pesquisa por meio de `NSUserActivity` em seu aplicativo, você também obtém os seguintes recursos:

- **Entrega** – uma vez que o aplicativo de pesquisa está expondo o conteúdo, navegação e/ou recursos usando o mesmo mecanismo que entrega (`NSUserActivity`), você pode facilmente permitir que os usuários do seu aplicativo Iniciar uma atividade em um dispositivo e continuá-lo em outro.
- **Sugestões de Siri** -juntamente com as sugestões padrão que normalmente faz sugestões Siri, funcionários ativos do seu aplicativo podem ser sugeridos automaticamente.
- **Lembretes de Smart Siri** -os usuários poderão solicitar Siri para lembrá-los sobre as atividades do seu aplicativo.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de aplicativo de pesquisa](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [& Postagem no Blog de exemplo](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
