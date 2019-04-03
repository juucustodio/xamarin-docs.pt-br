---
title: Introdução ao sugestões proativas no xamarin. IOS
description: Este artigo mostra como usar sugestões proativas no aplicativo xamarin. IOS para estimular a participação, permitindo que o sistema de forma proativa apresentam informações úteis automaticamente para o usuário.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2ab0147f918b36dc47ef6eed7d9bf1b6295d9733
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870398"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Introdução ao sugestões proativas no xamarin. IOS

_Este artigo mostra como usar sugestões proativas no aplicativo xamarin. IOS para estimular a participação, permitindo que o sistema de forma proativa apresentam informações úteis automaticamente para o usuário._

Novo no iOS 10, maneiras de notícias presentes sugestões proativas para os usuários a se envolver com um aplicativo xamarin. IOS por proativamente apresentam informações úteis automaticamente para o usuário em momentos apropriados.

iOS 10 apresenta novas maneiras de condução engagement no aplicativo, permitindo que o sistema de forma proativa apresentam informações úteis automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecido a capacidade de adicionar a pesquisa aprofundada ao aplicativo usando o destaque, entrega e sugestões da Siri (consulte [novas APIs de pesquisa](~/ios/platform/search/index.md)), com iOS 10 um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro do dos seguintes locais:

- O seletor de aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de QuickType

O aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias, como `NSUserActivity`, marcação de web, destaque principal, MapKit, Media Player e UIKit. Além disso, fornecendo suporte proativo sugestão para o aplicativo, ele obtém uma integração mais profunda Siri gratuitamente.

## <a name="location-based-suggestions"></a>Sugestões baseados na localização

Novo no iOS 10, o `NSUserActivity` classe inclui um `MapItem` propriedade que permite ao desenvolvedor fornecer informações de local que podem ser usadas em outros contextos. Por exemplo, se o aplicativo exibe resenhas de restaurantes, o desenvolvedor pode definir o `MapItem` propriedade para o local do restaurante que o usuário está exibindo no aplicativo. Se o usuário alterna para o aplicativo de mapas, local do restaurante está automaticamente disponível.

Se o aplicativo dá suporte à pesquisa de aplicativo, ele poderá usar os novos componentes de endereço do `CSSearchableItemAttributesSet` classe para especificar locais que o usuário pode querer visitar. Definindo o `MapItem` propriedade, as outras propriedades são preenchidos automaticamente.

Além de configuração de `Latitude` e `Longitude` das propriedades do componente de endereço, é recomendável que o aplicativo forneça o `NamedLocation` e `PhoneNumbers` propriedades também, portanto, Siri pode iniciar uma chamada para o local.

## <a name="web-markup-based-suggestions"></a>Marcação da Web com base em sugestões

iOS 9 adicionado a capacidade de incluir a marcação de dados estruturados no site que enriquece o conteúdo que os usuários veem nos resultados de pesquisa de destaque e Safari (consulte [pesquisa com a marcação da Web](~/ios/platform/search/web-markup.md)). iOS 10 adiciona a capacidade de incluir marcação baseados na localização (como [PostalAddress](http://schema.org/PostalAddress) conforme definido pela [Schema.org](http://schema.org/)) para aprimorar ainda mais a experiência do usuário. Por exemplo, se um modos de exibição de usuários um local marcado página no site, o sistema pode sugerir o mesmo local quando abrirem mapas.

## <a name="text-based-suggestions"></a>Sugestões baseadas em texto

UIKit foi expandido no iOS 10 para incluir a [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) propriedade da [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) classe para especificar o significado semântico do conteúdo em uma área de texto. Com essas informações em vigor, o sistema poderá geralmente automaticamente, selecione o tipo de teclado apropriado, melhorar as sugestões de correção automática e integrar proativamente as informações de outros aplicativos e sites.

Por exemplo, se o usuário está inserindo texto em um campo de texto marcado `UITextContentType.FullStreetAddress`, o sistema pode sugerir o campo de preenchimento automático com o local em que o usuário foi recentemente exibindo.

## <a name="media-based-suggestions"></a>Sugestões baseadas em mídia

Se o aplicativo reproduz a mídia usando o [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) API, o iOS 10 permite aos usuários exibir a arte do álbum e reproduzir mídia por meio do aplicativo na tela de bloqueio.

## <a name="contextual-siri-reminders"></a>Siri contextuais lembretes

Permite ao usuário usar Siri para fazer rapidamente um lembrete para exibir o conteúdo que está sendo exibido no aplicativo em uma data posterior. Por exemplo, se eles foram exibindo uma revisão de um restaurante no aplicativo, eles poderiam invocar Siri e dizer *"Lembrar-me sobre isso quando eu chegar em casa."* Siri geraria o lembrete com um link para a revisão no aplicativo.

## <a name="contact-based-suggestions"></a>Entre em contato com base em sugestões

Permite que o aplicativo contatos (e entre em contato com informações relacionadas) apareçam na **entre em contato com** aplicativo no dispositivo iOS, juntamente com todos os contatos de usuários armazenados no sistema.

## <a name="ride-sharing-based-suggestions"></a>Jornada de compartilhamento com base em sugestões

Se um aplicativo de compartilhamento usa o [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) API, o iOS 10 apresentará como uma opção no alternador de aplicativo nos momentos em que o usuário é provável que queira uma jornada. O aplicativo também deve ser registrado como um aplicativo de compartilhamento, especificando o `MKDirectionsModeRideShare` para o [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) chave no seu `Info.plist` arquivo.

Se o aplicativo dá suporte apenas a jornada de compartilhamento, a sugestão de sistema começaria com *"Obter uma jornada para..."*, se houver suporte para outros tipos de roteamento direção (como Walking ou bicicletas), o sistema usará *"Obter direções para..."*

> [!IMPORTANT]
> O [MKMapItem](xref:MapKit.MKMapItem) objeto que recebe o aplicativo não pode incluir informações de longitude e latitude e exigirá a geocodificação.

## <a name="implementing-proactive-suggestions"></a>Implementação de sugestões proativas

Suporte a um aplicativo xamarin. IOS adicionando sugestão proativo é geralmente tão fácil quanto implementar algumas APIs ou expandir em algumas APIs que o aplicativo já pode estar implementando.

Sugestões proativas funcionam com os aplicativos de três maneiras principais:

- **`NSUserActivity` e Schema.org**  -  `NSUserActivity` ajuda o sistema a entender quais informações o usuário está atualmente trabalhando na tela. Schema.org adiciona capacidades semelhantes às páginas da web.
- **Sugestões de local** - se o aplicativo oferece ou consome informações baseadas na localização, essas API extensão oferta novas maneiras de compartilhar essas informações entre aplicativos.
- **Sugestões de aplicativos de mídia** – o sistema pode promover o aplicativo e seu conteúdo de mídia com base no contexto de interação do usuário com o dispositivo iOS.

E é compatível com o aplicativo com a implementação a seguir:

- **Entrega**  -  `NSUserActivity` foi adicionada no iOS 8 para dar suporte à entrega, que permite que o desenvolvedor iniciar uma atividade em um dispositivo, em seguida, continua na outra (consulte [Introdução à entrega](~/ios/platform/handoff.md)).
- **Pesquisa de destaque** -iOS 9 adicionada a capacidade de promover o conteúdo do aplicativo de dentro os resultados de pesquisa do Spotlight usando `NSUserActivity` (consulte [pesquisa com destaque principal](~/ios/platform/search/corespotlight.md)).
- **Lembretes de Siri contextuais** - no iOS 10, `NSUserActivity` foi expandido para permitir que o Siri fazer rapidamente um lembrete para exibir o conteúdo que o usuário está exibindo no momento em que o aplicativo em uma data posterior.
- **Sugestões de local** -iOS 10 aprimora `NSUserActivity` capturar locais exibidos dentro do aplicativo e promovê-los em vários locais em todo o sistema.
- **Solicitações do Siri contextuais**  -  `NSUserActivity` fornece um contexto para as informações apresentadas dentro do aplicativo Siri para que o usuário possa obter direções ou local de uma chamada ser Siri invocando dentro do aplicativo.
- **Entre em contato com interações** – nova no iOS 10, `NSUserActivity` permite que os aplicativos de comunicação seja promovido de um cartão de visita (no aplicativo contatos) como um método alternativo de comunicação.

Todos esses recursos têm uma coisa em comum, todos eles usam `NSUserActivity` em um formato ou outro para fornecer sua funcionalidade. 

## <a name="nsuseractivity"></a>NSUserActivity

Conforme mencionado acima, `NSUserActivity` ajuda o sistema a entender quais informações o usuário está atualmente trabalhando na tela. `NSUserActivity` é um estado leve mecanismo para capturar a atividade do usuário enquanto navega por meio do aplicativo de cache. Por exemplo, verificando um aplicativo do restaurante:

[![](proactive-suggestions-images/activity02.png "O estado de leve NSUserActivity mecanismo de cache")](proactive-suggestions-images/activity02.png#lightbox)

Com as interações do seguintes:

1. Conforme o usuário trabalha com o aplicativo, um `NSUserActivity` é criado para recriar o estado do aplicativo mais tarde.
2. Se o usuário procura um restaurante, o mesmo padrão de criação de atividades é seguido.
3. E, novamente, quando o usuário exibe um resultado. Nesse último caso, o usuário está exibindo um local e no iOS 10, o sistema é mais ciente das determinados conceitos (como interações local ou de comunicação).

Dê uma olhada mais próxima na última tela:

[![](proactive-suggestions-images/activity03.png "Os detalhes de NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Aqui o aplicativo está criando um `NSUserActivity` e tenha sido populada com informações para recriar o estado mais tarde. O aplicativo também incluiu alguns metadados, como o nome e o endereço do local. Com essa atividade é criada, o aplicativo permite que iOS Saiba que ele representa o estado do usuário atual.

O aplicativo, em seguida, decide se a atividade será anunciada via satélite para entrega, salvo como um valor temporário para obter sugestões de local ou adicionada ao índice de destaque no dispositivo para exibir nos resultados da pesquisa.

Para obter mais informações sobre a pesquisa de destaque e de entrega, consulte nosso [Introdução à entrega](~/ios/platform/handoff.md) e [iOS 9 novas APIs de pesquisa](~/ios/platform/search/index.md) guias.

### <a name="creating-an-activity"></a>Criando uma atividade

Antes de criar uma atividade, um identificador de tipo de atividade precisava ser criado para identificá-lo. O identificador de tipo de atividade é uma cadeia de caracteres curta adicionada para o `NSUserActivityTypes` matriz do aplicativo `Info.plist` arquivo usado para identificar exclusivamente um determinado tipo de atividade do usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo oferece suporte e expõe para o aplicativo de pesquisa. Consulte nosso [referência de identificadores de tipo de atividade criando](~/ios/platform/search/nsuseractivity.md) para obter mais detalhes.

Veja um exemplo de uma atividade:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Inform system of Activity
activity.BecomeCurrent();
```

Uma nova atividade é criada usando um identificador de tipo de atividade. Em seguida, alguns metadados que definem a atividade é criado para que esse estado pode ser restaurado em uma data posterior. Em seguida, a atividade é fornecida um título significativo e anexada a informações do usuário. Por fim, alguns recursos são habilitados e a atividade é enviada para o sistema.

O código acima ainda mais poderia ser melhorado para incluir metadados que fornecem contexto para a atividade, fazendo as seguintes alterações:

```csharp
...

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Inform system of Activity
activity.BecomeCurrent();
```

Se o desenvolvedor tem um site que é capaz de exibir as mesmas informações que o aplicativo, o aplicativo pode incluir a URL e o conteúdo pode ser exibido em outros dispositivos que não têm o aplicativo instalado (por meio de entrega):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Restauração de uma atividade

Para responder ao usuário tocar em um resultado de pesquisa (`NSUserActivity`) para o aplicativo, edite o **AppDelegate.cs** do arquivo e substituir o `ContinueUserActivity` método. Por exemplo:

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

O desenvolvedor precisa garantir que este é o mesmo identificador de tipo de atividade (`com.xamarin.platform`) como a atividade criada acima. O aplicativo usa as informações armazenadas em do `NSUserActivity` para restaurar o estado para onde o usuário parou.

### <a name="benefits-of-creating-an-activity"></a>Benefícios da criação de uma atividade

Com a quantidade mínima de código apresentado acima, o aplicativo agora é possível tirar proveito dos três novos recursos do iOS 10:

- **Entrega**
- **Pesquisa de destaque**
- **Siri contextuais lembretes**

A seção a seguir será dar uma olhada habilitando dois outros novos recursos do iOS 10:

- **Sugestões de local**
- **Solicitações do Siri contextuais**

### <a name="location-based-suggestions"></a>Sugestões baseados na localização 

Veja o exemplo de aplicativo de pesquisa o restaurante acima. Se ele tiver implementado `NSUserActivity` e preenchida corretamente todos os metadados e atributos, o usuário poderia fazer o seguinte:

1. Encontre um restaurante no aplicativo que desejarem para atender a um amigo da.
2. Quando o usuário move para fora do aplicativo usando o seletor de aplicativo de multitarefa, o sistema exibirá automaticamente uma sugestão (na parte inferior da tela) para obter instruções para o restaurante usando seu aplicativo de navegação favorito.
3. Se o usuário alterna para o aplicativo de mensagens e começa a digitar *"Vamos nos encontrar à"*, o teclado QuickType sugerirá automaticamente colá-lo no endereço do restaurante.
4. Se o usuário alterna para o aplicativo de mapas, o endereço do restaurante automaticamente será sugerido como um destino.
5. Isso funciona até mesmo para aplicativos de terceiros 3ª (que dão suporte a `NSUserActivity`), portanto, o usuário pode alternar para um aplicativo de compartilhamento e o endereço do restaurante é sugerido automaticamente como um destino lá também.
6. Ela também fornece contexto para Siri, portanto, o usuário pode invocar Siri no aplicativo do restaurante e pergunte *"Obter direções..."* e Siri fornecerá instruções para o restaurante que o usuário está exibindo.

Toda a funcionalidade acima tem uma coisa em comum, todos eles indicam onde a sugestão é originalmente provenientes. No caso do exemplo acima, é o aplicativo de análise de restaurante fictícia.

iOS 10 foi aprimorado para habilitar essa funcionalidade para um aplicativo por meio de várias pequenas modificações e adições para as estruturas existentes:

- `NSUserActivity` tem campos adicionais para capturar informações de local que são exibidas dentro do aplicativo.
- Foram feitas várias adições MapKit e CoreSpotlight para capturar o local.
- Funcionalidade de reconhecimento de local foi adicionada para Siri, mapas, teclados, multitarefa e outros aplicativos no sistema.

Para implementar as sugestões baseadas na localização, comece com o mesmo código de atividade apresentado acima:

```csharp
// Create App Activity
var activity = new NSUserActivity ("com.xamarin.platform");

// Define details
var info = new NSMutableDictionary ();
info.Add(new NSString("link"),new NSString("http://xamarin.com/platform"));

// Populate Activity
activity.Title = "The Xamarin Platform";
activity.UserInfo = info;

// Enable capabilities
activity.EligibleForSearch = true;
activity.EligibleForHandoff = true;
activity.EligibleForPublicIndexing = true;

// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
attributes.ThumbnailUrl = myThumbnailURL;
attributes.Keywords = new string [] { "software", "mobile", "language" }; 
activity.ContentAttributeSet = attributes;

// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");

// Inform system of Activity
activity.BecomeCurrent();
```

Se o aplicativo estiver usando MapKit, ele é tão simple quanto adicionar o mapa atual `MKMapItem` à atividade:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Se o aplicativo não está usando MapKit, ele pode adotar o aplicativo de pesquisa e especificar os novos atributos para o local a seguir:

```csharp
// Provide context
var attributes = new CSSearchableItemAttributeSet ("com.xamarin.location");
...

attributes.NamedLocation = "Apple Inc.";
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

Examine o código acima em detalhes. Primeiro, o nome do local é necessário em todas as instâncias:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Em seguida, a descrição de texto com base do local é necessária para instâncias de baseado em texto (por exemplo, o teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

A latitude e longitude são opcionais, mas certifique-se de que o usuário seja roteado para o local exato que o aplicativo é quando você deseja enviá-los, ela deve ser incluída:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Definindo os números de telefone, o aplicativo pode obter acesso a Siri para que o usuário pode invocar Siri do aplicativo dizendo algo como *"Telefonar para este lugar"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por fim, o aplicativo pode indicar se a instância é adequada para chamadas telefônicas e de navegação:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementação de interações de contato

Novo no iOS 10, aplicativos de comunicação estão profundamente integrados no aplicativo de contatos do cartão de contato. Para aplicativos que implementaram interações de contato, o usuário pode adicionar informações do aplicativo fornecido para pessoas específicas em seus contatos. E se, por exemplo, eles pressionar o botão de ação rápida na parte superior de um cartão para enviar uma mensagem, o aplicativo anexado será listado como uma opção para enviar a mensagem do.

Se um aplicativo de terceiros 3º for selecionada, pode ser lembrada e apresentado como a forma padrão para a mensagem determinada pessoa na próxima vez que o usuário deseja entrar em contato com eles.

Entre em contato com interações são implementadas no aplicativo usando `NSUserActivity` e a nova estrutura de intenções introduzido no iOS 10. Para obter mais detalhes sobre como trabalhar com as intenções, consulte nosso [Noções básicas sobre os conceitos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) e [implementar SiriKit](~/ios/platform/sirikit/implementing-sirikit.md) guias.

#### <a name="donating-interactions"></a>Interações de doação

Dê uma olhada em como o aplicativo pode fazer uma doação interações:

[![](proactive-suggestions-images/activity04.png "Visão geral de interações de doação")](proactive-suggestions-images/activity04.png#lightbox)

O aplicativo cria uma `INInteraction` objeto que contém um **intenção** (`INIntent`), **participantes** e **metadados**. O **intenção** representa uma ação do usuário, como fazer uma chamada de vídeo ou enviando uma mensagem de texto. O **participantes** incluem as pessoas que recebem a comunicação. O **metadados** define as informações de adição como enviar com êxito a mensagem, etc.

O desenvolvedor nunca diretamente cria uma instância do `INIntent` ou `INIntentResponse`, elas usam uma das classes filho específico (com base na tarefa de realização do aplicativo em nome do usuário) que herdam essas classes pai. Por exemplo, `INSendMessageIntent` e `INSendMessageIntentResponse` para enviar uma mensagem de texto. 

Depois que a interação está completamente populada, chame o `DonateInteraction` método para informar o sistema que a interação está disponível para uso.

Quando o usuário interage com o aplicativo de cartão de visita, a interação obtém agrupada com um `NSUserActivity`, que é usado para iniciar o aplicativo:

[![](proactive-suggestions-images/activity05.png "A interação obtém agrupada com um NSUserActivity que é usado para iniciar o aplicativo")](proactive-suggestions-images/activity05.png#lightbox)

Dê uma olhada no exemplo a seguir de uma intenção de mensagem enviar:

```csharp
using System;
using Foundation;
using UIKit;
using Intents;

namespace MonkeyNotification
{
    public class DonateInteraction
    {
        #region Constructors
        public DonateInteraction ()
        {
        }
        #endregion

        #region Public Methods
        public void SendMessageIntent (string text, INPerson from, INPerson[] to)
        {

            // Create App Activity
            var activity = new NSUserActivity ("com.xamarin.message");

            // Define details
            var info = new NSMutableDictionary ();
            info.Add (new NSString ("message"), new NSString (text));

            // Populate Activity
            activity.Title = "Sent MonkeyChat Message";
            activity.UserInfo = info;

            // Enable capabilities
            activity.EligibleForSearch = true;
            activity.EligibleForHandoff = true;
            activity.EligibleForPublicIndexing = true;

            // Inform system of Activity
            activity.BecomeCurrent ();

            // Create message Intent
            var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);

            // Create Intent Reaction
            var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);

            // Create interaction
            var interaction = new INInteraction (intent, response);

            // Donate interaction to the system
            interaction.DonateInteraction ((err) => {
                // Handle donation error
                ...
            });
        }
        #endregion
    }
}
```

Examinar esse código detalhadamente, ele cria e preenche uma instância de `NSUserActivity` (conforme mostrado na [criando uma atividade](#creating-an-activity) seção acima). Em seguida, ele cria uma instância do `INSendMessageIntent` (que herda de `INIntent`) e a preenche com os detalhes da mensagem sendo enviada:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Uma `INSendMessageIntentResponse` é criado e passado a `NSUserActivity` criado acima:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Uma `INInteraction` baseia-se de que a intenção de mensagem enviar (`INSendMessageIntent`) e a resposta (`INSendMessageIntentResponse`) acabou de criar:

```csharp
var interaction = new INInteraction (intent, response);
```

Por fim, o sistema é a notificação da interação:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

Um manipulador de conclusão é passado em que o aplicativo pode responder a doação bem-sucedida ou falhar.

### <a name="activities-best-practices"></a>Práticas recomendadas de atividades

A Apple sugere as seguintes práticas recomendadas ao trabalhar com atividades:

- Use `NeedsSave` para atualizações de carga lenta.
- Certifique-se de manter uma referência forte para a atividade atual.
- Transferir apenas pequenas cargas que incluem informações suficientes para restaurar o estado.
- Verifique se os identificadores de tipo de atividade exclusivo e descritivo, usando a notação de DNS reverso para especificá-los. 

## <a name="schemaorg"></a>Schema.org

Como mostrado acima, `NSUserActivity` ajuda o sistema a entender quais informações o usuário está atualmente trabalhando na tela. Schema.org adiciona capacidades semelhantes às páginas da web.

Schema.org pode fornecer os mesmos tipos de interações baseadas na localização para o site. Apple projetado as sugestões de local novo funcione tão bem quando visualizado no Safari, como fazem em um aplicativo nativo.

Some Schema.org background:

- Ele fornece um padrão de vocabulário de marcação de web aberta.
- Ele funciona, incluindo metadados estruturados em páginas da web.
- Há mais de 500 esquemas que representam vários conceitos disponíveis.
- Implementando-o no site, o desenvolvedor pode adquirir alguns dos benefícios de usar `NSUserActivity` em um aplicativo nativo.

Os esquemas são organizados em uma árvore como estrutura, onde específicos de tipos, como *restaurante*, herdar de tipos mais genéricos, como *comercial*. Para obter mais informações, consulte [Schema.org](http://schema.org).

Por exemplo, se a página da web incluído os seguintes dados:

```xml
<script type="application/ld+json>
{
    "@context":"http://schema.org",
    "@type":"Restaurant",
    "telephone":"(415) 781-1111",
    "url":"https://www.yanksing.com",
    "address":{
        "@type":"PostalAddress",
        "streetAddress":"101 Spear St",
        "addressLocality":"San Francisco",
        "postalCode":"94105",
        "addressRegion":"CA"
    },
    "aggregateRating":{
        "@type":"AggregateRating",
        "ratingValue":"3.5",
        "reviewCount":"2022"
    }
}
</script>
```

Se o usuário visita esta página no Safari e, em seguida, é alternado para outro aplicativo, as informações de localização da página seriam capturadas e oferecidas como uma sugestão de localização em outras partes do sistema (como visto nas atividades acima).

Safari irá extrair tudo em uma página da web que obedeça a qualquer uma das propriedades de esquema a seguir:

- **PostalAddress**
- **GeoCoordinates**
- Uma propriedade de telefone.

Para obter mais informações, consulte nosso [pesquisa com a marcação da Web](~/ios/platform/search/web-markup.md) guia.

## <a name="consuming-location-suggestions"></a>Consumindo sugestões de local

Esta seção abordará consumindo sugestão local oriundos de outras partes do sistema (por exemplo, o aplicativo de mapas) ou outros aplicativos de terceiros 3ª.

Há duas maneiras principais que o aplicativo pode consumir as sugestões de local:

- Por meio do teclado QuickType.
- Diretamente ao consumir as informações de localização nos aplicativos de roteamentos.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Sugestões de local e o teclado QuickType

Se o aplicativo lida com endereços em formatos de texto com base, o aplicativo pode tirar proveito de sugestões de local por meio de UI QuickType. iOS 10 expande a UI QuickType com os seguintes recursos:

- O aplicativo pode adicionar dicas sobre a finalidade semântica para campos de texto na interface do usuário.
- O aplicativo pode obter sugestões proativas no aplicativo.
- O aplicativo pode se beneficiar da correção automática aprimorada.

O novo `TextContentType` propriedade dos controles de campo de texto no iOS 10 permite que o desenvolvedor definir a finalidade semântica para o valor que o usuário vai inserir em um determinado campo. Por exemplo:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Informa o sistema que o aplicativo espera que o usuário insira um endereço de rua completo no campo fornecido. Isso permitirá que o teclado QuickType automaticamente fornecer sugestões de local do teclado quando o usuário está inserindo um valor neste campo.

A seguir estão alguns dos tipos mais comuns disponíveis para o desenvolvedor a `UITextContentType` classe estática:

* `Name`
* `GivenName`
* `FamilyName`
* `Location`
* `FullStreetAddress`
* `AddressCityAndState`
* `TelephoneNumber`
* `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Aplicativos de roteamentos e sugestões de locais

Esta seção será dar uma olhada no consumo de sugestões de local diretamente de dentro de um aplicativo de roteamento. Para o aplicativo de roteamento adicionar essa funcionalidade, o desenvolvedor aproveitará existente `MKDirectionsRequest` framework da seguinte maneira:

- Para promover o aplicativo em execução multitarefa.
- Para registrar o aplicativo como um aplicativo de roteamento.
- Para tratar a iniciar o aplicativo com um MapKit `MKDirectionsRequest` objeto.
- Para fornecer iOS a capacidade de aprender sugerir o aplicativo para o usuário em momentos apropriados, com base no envolvimento do usuário.

Quando o aplicativo é iniciado com um MapKit `MKDirectionsRequest` do objeto, ele deve iniciar automaticamente dando as instruções de usuário para o local solicitado, ou apresentar uma interface do usuário que torna mais fácil para começar a obter direções ao usuário. Por exemplo:


```csharp
using System;
using Foundation;
using UIKit;
using MapKit;
using CoreLocation;

namespace MonkeyChat
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate, IUISplitViewControllerDelegate
    {
        ...
        
        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            if (MKDirectionsRequest.IsDirectionsRequestUrl (url)) {
                var request = new MKDirectionsRequest (url);
                var coordinate = request.Destination?.Placemark.Location?.Coordinate;
                var address = request.Destination.Placemark.AddressDictionary;
                if (coordinate.IsValid()) {
                    var geocoder = new CLGeocoder ();
                    geocoder.GeocodeAddress (address, (place, err) => {
                        // Handle the display of the address

                    });
                }
            }

            return true;
        }
    }       
}
```

Examine esse código detalhadamente. Ele testa para ver se ela é uma solicitação de destino válido:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Se ele for, ele cria um `MKDirectionsRequest` da URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Novo no iOS 10, o aplicativo pode ser enviado um endereço que não tenha coordenadas geográficas, que fazem com que o desenvolvedor precisa codificar o endereço:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>Sugestões de aplicativos de mídia

Se o aplicativo lida com qualquer tipo de mídia como um aplicativo podcast ou um conteúdo de mídia de streaming, como áudio ou vídeo, com iOS 10, ele pode tirar proveito de sugestões de mídia no sistema.

Para aplicativos que lidam com a mídia, o iOS suporta os seguintes comportamentos:

- iOS promove a aplicativos que o usuário provavelmente usará com base em seu comportamento anterior.
- Sugestões relacionados para o aplicativo serão apresentadas em destaque e o modo de exibição hoje.
- Se o aplicativo atenda a um dos seguintes disparadores, ele pode ser elevado para uma sugestão de tela de bloqueio:
    - Depois de montar fones de ouvido ou um dispositivo Bluetooth faz uma conexão.
    - Depois de obter de um carro.
    - Depois que chega em casa ou de trabalho. 

Incluindo uma simple chamada à API no iOS 10, o desenvolvedor pode criar uma experiência mais agradável de tela de bloqueio para usuários do aplicativo da mídia. Usando o `MPPlayableContentManager` classe para gerenciar a reprodução de mídia, controles de mídia completo (como os apresentados pelo aplicativo de música) será apresentado na tela de bloqueio para o aplicativo.


```csharp
using System;
using MediaPlayer;
using UIKit;

namespace MonkeyPlayer
{
    public class PlayableContentDelegate : MPPlayableContentDelegate
    {
        #region Constructors
        public PlayableContentDelegate ()
        {
        }
        #endregion

        #region Override methods
        public override void InitiatePlaybackOfContentItem (MPPlayableContentManager contentManager, Foundation.NSIndexPath indexPath, Action<Foundation.NSError> completionHandler)
        {
            // Access the media item to play
            var item = LoadMediaItem (indexPath);

            // Populate the lock screen
            PopulateNowPlayingItem (item);

            // Prep item to be played
            var status = PreparePlayback (item);

            // Call completion handler
            completionHandler (null);
        }
        #endregion

        #region Public Methods
        public MPMediaItem LoadMediaItem (Foundation.NSIndexPath indexPath)
        {
            var item = new MPMediaItem ();

            // Load item from media store
            ...

            return item;
        }

        public void PopulateNowPlayingItem (MPMediaItem item)
        {
            // Get Info Center and album art
            var infoCenter = MPNowPlayingInfoCenter.DefaultCenter;
            var albumArt = (item.Artwork == null) ? new MPMediaItemArtwork (UIImage.FromFile ("MissingAlbumArt.png")) : item.Artwork;

            // Populate Info Center
            infoCenter.NowPlaying.Title = item.Title;
            infoCenter.NowPlaying.Artist = item.Artist;
            infoCenter.NowPlaying.AlbumTitle = item.AlbumTitle;
            infoCenter.NowPlaying.PlaybackDuration = item.PlaybackDuration;
            infoCenter.NowPlaying.Artwork = albumArt;
        }

        public bool PreparePlayback (MPMediaItem item)
        {
            // Prepare media item for playback
            ...

            // Return results
            return true;
        }
        #endregion
    }
}
```

## <a name="summary"></a>Resumo

Este artigo foi coberto sugestões proativas e mostrou como o desenvolvedor pode usá-las para direcionar o tráfego para o aplicativo xamarin. IOS. Ele abordado na etapa para implementar as sugestões proativas e apresentadas diretrizes de uso.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
