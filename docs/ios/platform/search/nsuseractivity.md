---
title: Pesquisa com NSUserActivity em xamarin
description: Este documento descreve como indexar um NSUserActivity, tornando pesquisáveis em destaque e Safari. Ele discute como responder a seleção de um NSUserActivity nos resultados da pesquisa.
ms.prod: xamarin
ms.assetid: 0B28B284-C7C9-4C0D-A782-D471FBBC4CAE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 4b053f66e9b6b7715cbe52c4e43d9db32db48f4c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788202"
---
# <a name="search-with-nsuseractivity-in-xamarinios"></a>Pesquisa com NSUserActivity em xamarin

`NSUserActivity` foi introduzido no iOS 8 e é usado para fornecer os dados para entrega.
Ele permite que você crie atividades em partes específicas do aplicativo que pode ser passado para outra instância do seu aplicativo em execução em um dispositivo iOS diferentes. O dispositivo de recepção pode continuar a atividade iniciada no dispositivo anterior, separando direita onde o usuário parou. Para obter mais informações sobre o uso de entrega, consulte nosso [Introdução à entrega](~/ios/platform/handoff.md) documentação.

Novo no iOS 9, `NSUserActivity` podem ser indexadas (publicamente e em particular) e pesquisados na pesquisa do Spotlight e Safari. Marcando uma `NSUserActivity` como metadados de indexáveis adicionando e pesquisável, a atividade pode ser listada nos resultados da pesquisa no dispositivo iOS.

[![](nsuseractivity-images/apphistory01.png "Visão geral do histórico do aplicativo")](nsuseractivity-images/apphistory01.png#lightbox)

Se o usuário seleciona um resultado de pesquisa que pertence a uma atividade do seu aplicativo, o aplicativo será iniciado e a atividade descrito pelo `NSUserActivity` será reiniciado e apresentadas ao usuário.

As seguintes propriedades de `NSUserActivity` são usados para oferecer suporte à pesquisa do aplicativo:

 - `EligibleForHandoff` – Se `true`, essa atividade pode ser usada em uma operação de entrega.
 - `EligibleForSearch` – Se `true`, essa atividade será adicionada ao índice no dispositivo e apresentada nos resultados da pesquisa.
 - `EligibleForPublicIndexing` – Se `true`, essa atividade será adicionada ao índice de baseado em nuvem da Apple e apresentada aos usuários que ainda não tiver instalado o aplicativo em seu dispositivo iOS (por meio de pesquisa). Consulte o [pública indexação de pesquisa](#Public-Search-Indexing) seção abaixo para obter mais detalhes.
 - `Title` – Fornece um título para a atividade e é exibido nos resultados da pesquisa. Os usuários também podem pesquisar o texto do título em si.
 - `Keywords` – É uma matriz de cadeias de caracteres usado para descrever a atividade que será indexada e tornar pesquisável pelo usuário final.
 - `ContentAttributeSet` – É uma `CSSearchableItemAttributeSet` usado para descrever sua atividade detalhadamente ainda mais e fornecer conteúdo formatado nos resultados da pesquisa.
 - `ExpirationDate` – Se desejar que uma atividade para ser exibido somente para uma determinada data, você pode fornecer essa data aqui.
 - `WebpageURL` – Se a atividade pode ser exibida na web ou se seu aplicativo dá suporte a links profundos do Safari, você pode definir o link para visitar aqui.

## <a name="nsuseractivity-quickstart"></a>Guia de início rápido NSUserActivity

Siga estas instruções para implementar um recurso de pesquisa `NSUserActivity` em seu aplicativo:

- [Criação de identificadores de tipo de atividade](#creatingtypeid)
- [Criando uma atividade](#createactivity)
- [Respondendo a uma atividade](#respondactivity)
- [Indexação de pesquisa públicos](#indexing)

Há alguns [benefícios adicionais](#benefits) usando `NSUserActivity` para tornar o seu conteúdo pesquisável.

<a name="creatingtypeid" />

## <a name="creating-activity-type-identifiers"></a>Criação de identificadores de tipo de atividade

Antes de criar uma atividade de pesquisa, você precisará criar um _identificador de tipo de atividade_ para identificá-lo. O identificador de tipo de atividade é uma cadeia de caracteres curta adicionada para o `NSUserActivityTypes` matriz do aplicativo **Info. plist** arquivo usado para identificar exclusivamente um determinado tipo de atividade do usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo oferece suporte e expõe para o aplicativo de pesquisa. 

Apple sugere usando uma notação inversa de DNS de estilo para o identificador de tipo de atividade para evitar colisões. Por exemplo: `com.company-name.appname.activity` para aplicativos específicos com base em atividades ou `com.company-name.activity` para atividades que podem ser executados em vários aplicativos.

O identificador de tipo de atividade é usado ao criar um `NSUserActivity` instância para identificar o tipo de atividade. Quando uma atividade é reiniciada como resultado do usuário em um resultado de pesquisa, o tipo de atividade (junto com a ID do aplicativo Team) determina qual aplicativo para iniciá-lo para continuar a atividade.

Para criar os identificadores de tipo de atividade necessário para dar suporte a esse comportamento, edite o **Info. plist** de arquivo e alterne para o **fonte** exibição. Adicionar um `NSUserActivityTypes` chave e criar identificadores no seguinte formato:

[![](nsuseractivity-images/type01.png "A chave de NSUserActivityTypes e identificadores necessários no editor plist")](nsuseractivity-images/type01.png#lightbox)

No exemplo acima, criamos um novo identificador de tipo de atividade para a atividade de pesquisa (`com.xamarin.platform`). Ao criar seus próprios aplicativos, substitua o conteúdo da `NSUserActivityTypes` matriz com os identificadores de tipo de atividade específica para as atividades do seu aplicativo oferece suporte.

<a name="createactivity" />

## <a name="creating-an-activity"></a>Criando uma atividade

Este é um exemplo de criação de uma atividade para uma pesquisa de índice no dispositivo hospedado:

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

Poderíamos adicionar mais detalhes, definindo o `ContentAttributeSet` propriedade do nosso `NSUserActivity` da seguinte maneira:

[![](nsuseractivity-images/apphistory02.png "Visão geral de detalhes de pesquisa de inclusão")](nsuseractivity-images/apphistory02.png#lightbox)

Usando um `ContentAttributeSet` pode criar resultados de pesquisa avançados que convencer o usuário final para interagir com eles.

<a name="respondactivity" />

## <a name="responding-to-an-activity"></a>Respondendo a uma atividade

Responder para o usuário tocar em um resultado de pesquisa (`NSUserActivity`) para o nosso aplicativo, edite o **appdelegate. CS** de arquivo e substituir o `ContinueUserActivity` método. Por exemplo:

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

Observe que essa é a mesma usada para responder a solicitações de entrega de substituição de método. Agora se o usuário clica em um link de nosso aplicativo nos resultados da pesquisa do Spotlight, nosso aplicativo será colocado em primeiro plano (ou foi iniciado se não estiver em execução) e o conteúdo, a navegação ou o recurso representado por esse link será exibido:

[![](nsuseractivity-images/apphistory03.png "Restaurar o estado anterior da pesquisa")](nsuseractivity-images/apphistory03.png#lightbox)

<a name="indexing" />

## <a name="public-search-indexing"></a>Indexação de pesquisa públicos

Como vimos acima, o iOS 9 facilita fornecer acesso de pesquisa para conteúdo de aplicativo e os recursos que o usuário já descoberto e usado em um dispositivo iOS determinado. Com a indexação público, o iOS 9 fornece uma maneira para os usuários que ainda não descobertas conteúdo ou recursos (ou que ainda não tiver instalado o aplicativo) para obter esses resultados em suas pesquisas muito.

Indexação pública funciona da seguinte maneira:

1. Quando você criar uma atividade para seu aplicativo pode ser marcado como público.
2. Atividades pública são enviadas para a Apple e indexadas na nuvem.
3. Como mais usuários interagem com seu aplicativo em um dispositivo e usam o recurso específico ou representado pela atividade de conteúdo, ele aumenta na classificação.
4. Resultados públicos populares estarão disponíveis para outros usuários, mesmo que eles não tenham o aplicativo instalado.
5. Esses resultados públicos aparecerão na pesquisa do Spotlight e Safari (se a atividade inclui uma URL).

Podemos levar a atividade de pesquisa privada que criamos acima e expandi-la para ser pública:

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

Só porque uma atividade foi definida para indexação público definindo `EligibleForPublicIndexing = true`, isso não significa que ele será automaticamente adicionado ao índice de nuvem pública da Apple. As seguintes condições devem ser atendidas pela primeira vez:

1. Ele deve aparecer nos resultados da pesquisa e ser selecionado por muitos usuários. Os resultados permanecem particulares até que um limite de contrato de atividade foram atendido.
2. Provisionamento de aplicativo impede que todos os dados específicos do usuário sejam indexados e publicado.

<a name="benefits" />

## <a name="additional-benefits"></a>Benefícios adicionais

Com a adoção de pesquisa do aplicativo por meio de `NSUserActivity` em seu aplicativo, você também obter os seguintes recursos:

- **Entrega** - desde que o aplicativo de pesquisa está expondo o conteúdo, navegação e/ou recursos usando o mesmo mecanismo entrega (`NSUserActivity`), você pode facilmente permitir que usuários de seu aplicativo Iniciar uma atividade em um dispositivo e continua na outra.
- **Siri sugestões** -juntamente com as sugestões padrão que normalmente faz Siri sugestões, ativos de seu aplicativo podem ser sugeridos automaticamente.
- **Lembretes inteligente Siri** -os usuários poderão solicitar Siri lembrá-los sobre as atividades do seu aplicativo.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de aplicativo de pesquisa](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
- [Exemplo de & postagem de Blog](https://blog.xamarin.com/improve-discoverability-with-search-in-ios-9/)
