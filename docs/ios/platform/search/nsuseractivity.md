---
title: Pesquisar com NSUserActivity no Xamarin. iOS
description: Este documento descreve como indexar um NSUserActivity, tornando-o pesquisável em destaque e Safari. Ele aborda como responder à seleção de um NSUserActivity nos resultados da pesquisa.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 29ccf115facf9a086db473301f7dfb548a80dc9f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431108"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Pesquisar com NSUserActivity no Xamarin. iOS

`NSUserActivity` foi introduzido no iOS 8 e é usado para fornecer os dados para entrega.
Ele permite que você crie atividades em partes específicas de seu aplicativo que podem ser passadas para outra instância do seu aplicativo em execução em um dispositivo iOS diferente. O dispositivo receptor pode continuar a atividade iniciada no dispositivo anterior, selecionando o direito onde o usuário parou. Para obter mais informações sobre como usar a entrega, consulte nossa [introdução à documentação de entrega](~/ios/platform/handoff.md) .

Novo no iOS 9, `NSUserActivity` pode ser indexado (de forma pública e privada) e pesquisado na pesquisa de destaque e no Safari. Ao marcar um `NSUserActivity` como pesquisável e adicionar metadados indexáveis, a atividade pode ser listada nos resultados da pesquisa no dispositivo IOS.

[![A visão geral do histórico do aplicativo](nsuseractivity-images/apphistory01.png)](nsuseractivity-images/apphistory01.png#lightbox)

Se o usuário selecionar um resultado de pesquisa que pertença a uma atividade do seu aplicativo, o aplicativo será iniciado e a atividade descrita pelo `NSUserActivity` será reiniciada e apresentada ao usuário.

As seguintes propriedades do `NSUserActivity` são usadas para dar suporte à pesquisa de aplicativo:

- `EligibleForHandoff` – Se `true` , essa atividade pode ser usada em uma operação de entrega.
- `EligibleForSearch` – Se `true` , essa atividade será adicionada ao índice no dispositivo e apresentada nos resultados da pesquisa.
- `EligibleForPublicIndexing` – Se `true` , essa atividade será adicionada ao índice baseado na nuvem da Apple e apresentada aos usuários (por meio de pesquisa) que ainda não instalaram seu aplicativo em seu dispositivo IOS. Consulte a seção [indexação de pesquisa pública](#public-search-indexing) abaixo para obter mais detalhes.
- `Title` – Fornece um título para sua atividade e é exibido nos resultados da pesquisa. Os usuários também podem pesquisar o texto do título em si.
- `Keywords` – É uma matriz de cadeias de caracteres usada para descrever sua atividade que será indexada e tornada pesquisável pelo usuário final.
- `ContentAttributeSet` – É `CSSearchableItemAttributeSet` usado para descrever ainda mais sua atividade em detalhes e fornecer conteúdo avançado nos resultados da pesquisa.
- `ExpirationDate` – Se você quiser que uma atividade seja mostrada apenas até uma determinada data, você pode fornecer essa data aqui.
- `WebpageURL` – Se a atividade puder ser exibida na Web ou se seu aplicativo der suporte a links profundos do Safari, você poderá definir o link para visitar aqui.

## <a name="nsuseractivity-quickstart"></a>Início rápido do NSUserActivity

Siga estas instruções para implementar um pesquisável `NSUserActivity` em seu aplicativo:

- [Criando identificadores de tipo de atividade](#creatingtypeid)
- [Criando uma atividade](#createactivity)
- [Respondendo a uma atividade](#respondactivity)
- [Indexação de pesquisa pública](#indexing)

Há alguns [benefícios adicionais](#benefits) ao usar `NSUserActivity` o para tornar seu conteúdo pesquisável.

<a name="creatingtypeid"></a>

## <a name="creating-activity-type-identifiers"></a>Criando identificadores de tipo de atividade

Para poder criar uma atividade de pesquisa, você precisará criar um _identificador de tipo de atividade_ para identificá-lo. O identificador de tipo de atividade é uma cadeia de caracteres curta adicionada à `NSUserActivityTypes` matriz do arquivo **info. plist** do aplicativo usado para identificar exclusivamente um determinado tipo de atividade de usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo suporta e expõe para a pesquisa de aplicativo. 

A Apple sugere o uso de uma notação de estilo DNS reverso para o identificador de tipo de atividade para evitar colisões. Por exemplo: `com.company-name.appname.activity` para atividades específicas baseadas em aplicativo ou `com.company-name.activity` para atividades que podem ser executadas em vários aplicativos.

O identificador de tipo de atividade é usado ao criar uma `NSUserActivity` instância para identificar o tipo de atividade. Quando uma atividade é continuada como resultado do usuário tocar em um resultado de pesquisa, o tipo de atividade (junto com a ID de equipe do aplicativo) determina qual aplicativo deve ser iniciado para continuar a atividade.

Para criar os identificadores de tipo de atividade necessários para dar suporte a esse comportamento, edite o arquivo **info. plist** e alterne para o modo de exibição de **origem** . Adicione uma `NSUserActivityTypes` chave e crie identificadores no seguinte formato:

[![A chave NSUserActivityTypes e os identificadores necessários no editor do plist](nsuseractivity-images/type01.png)](nsuseractivity-images/type01.png#lightbox)

No exemplo acima, criamos um novo identificador de tipo de atividade para a atividade de pesquisa ( `com.xamarin.platform` ). Ao criar seus próprios aplicativos, substitua o conteúdo da `NSUserActivityTypes` matriz pelos identificadores de tipo de atividade específicos das atividades às quais seu aplicativo dá suporte.

<a name="createactivity"></a>

## <a name="creating-an-activity"></a>Criando uma atividade

Veja a seguir um exemplo de criação de uma atividade para uma pesquisa hospedada no índice no dispositivo:

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

Poderíamos adicionar mais detalhes Configurando a `ContentAttributeSet` propriedade da seguinte `NSUserActivity` maneira:

[![Visão geral dos detalhes da pesquisa de adição](nsuseractivity-images/apphistory02.png)](nsuseractivity-images/apphistory02.png#lightbox)

Usando um `ContentAttributeSet` , você pode criar resultados de pesquisa avançados que atraiam o usuário final para interagir com eles.

<a name="respondactivity"></a>

## <a name="responding-to-an-activity"></a>Respondendo a uma atividade

Para responder ao usuário tocando em um resultado de pesquisa ( `NSUserActivity` ) para nosso aplicativo, edite o arquivo **AppDelegate.cs** e substitua o `ContinueUserActivity` método. Por exemplo:

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

Observe que essa é a mesma substituição de método usada para responder às solicitações de entrega. Agora, se o usuário clicar em um link de nosso aplicativo nos resultados da pesquisa de destaque, nosso aplicativo será levado para o primeiro plano (ou iniciado se ainda não estiver em execução) e o conteúdo, a navegação ou o recurso representado por esse link será exibido:

[![Restaurar estado anterior da pesquisa](nsuseractivity-images/apphistory03.png)](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing"></a>

## <a name="public-search-indexing"></a>Indexação de pesquisa pública

Como vimos acima, o iOS 9 facilita a tarefa de fornecer acesso de pesquisa ao conteúdo do aplicativo e aos recursos que o usuário já descobriu e usado em um determinado dispositivo iOS. Com a indexação pública, o iOS 9 fornece uma maneira para os usuários que ainda não descobriram conteúdo ou recursos (ou que nem mesmo instalaram o aplicativo) para obter esses resultados em suas pesquisas também.

A indexação pública funciona da seguinte maneira:

1. Quando você cria uma atividade para seu aplicativo, você pode marcá-la como pública.
2. As atividades públicas são enviadas à Apple e indexadas na nuvem.
3. À medida que mais usuários interagem com seu aplicativo em um dispositivo e usam o recurso específico ou o conteúdo representado pela atividade, ele aumenta a classificação.
4. Os resultados públicos populares estarão disponíveis para outros usuários, mesmo que não tenham o aplicativo instalado.
5. Esses resultados públicos aparecerão na pesquisa de destaque e no Safari (se a atividade incluir uma URL).

Podemos pegar a atividade de pesquisa privada que criamos acima e expandi-la para ser pública:

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

Só porque uma atividade foi definida para indexação pública por meio da configuração `EligibleForPublicIndexing = true` , isso não significa que ela será adicionada automaticamente ao índice de nuvem pública da Apple. As seguintes condições devem ser atendidas primeiro:

1. Ele deve aparecer nos resultados da pesquisa e ser selecionado por muitos usuários. Os resultados permanecem privados até que um limite do engajamento de atividades seja atingido.
2. O provisionamento de aplicativos impede que qualquer dado específico do usuário seja indexado e tornado público.

<a name="benefits"></a>

## <a name="additional-benefits"></a>Benefícios adicionais

Ao adotar a pesquisa de aplicativo via `NSUserActivity` em seu aplicativo, você também obtém os seguintes recursos:

- **Entrega** -como a pesquisa de aplicativos está expondo conteúdo, navegação e/ou recursos usando o mesmo mecanismo que a entrega ( `NSUserActivity` ), você pode facilmente permitir que os usuários do seu aplicativo iniciem uma atividade em um dispositivo e continuem em outro.
- **Siri sugestões** – juntamente com as sugestões padrão que as sugestões de Siri normalmente fazem, os ativos de seu aplicativo podem ser sugeridos automaticamente.
- **Lembretes inteligentes do Siri** -os usuários poderão pedir ao Siri para lembrá-los sobre as atividades do seu aplicativo.

## <a name="related-links"></a>Links Relacionados

- [Amostras do iOS 9](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de pesquisa de aplicativo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Postagem do blog & exemplo](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)