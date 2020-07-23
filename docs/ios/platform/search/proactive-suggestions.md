---
title: Introdução às sugestões proativas no Xamarin. iOS
description: Este artigo mostra como usar sugestões proativas no aplicativo Xamarin. iOS para conduzir o envolvimento, permitindo que o sistema apresente informações úteis de forma proativa automaticamente para o usuário.
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 363427a410a2e4bc40348c6f50e2920e552f31fe
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939432"
---
# <a name="introduction-to-proactive-suggestions-in-xamarinios"></a>Introdução às sugestões proativas no Xamarin. iOS

_Este artigo mostra como usar sugestões proativas no aplicativo Xamarin. iOS para conduzir o envolvimento, permitindo que o sistema apresente informações úteis de forma proativa automaticamente para o usuário._

Novidade no iOS 10, as sugestões proativas apresentam notícias sobre como os usuários podem se envolver com um aplicativo Xamarin. iOS proativamente apresenta informações úteis automaticamente para o usuário em momentos apropriados.

o iOS 10 apresenta novas maneiras de conduzir o envolvimento ao aplicativo, permitindo que o sistema apresente informações úteis de forma proativa automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecia a capacidade de adicionar pesquisa profunda ao aplicativo usando as sugestões de Spotlight, entrega e Siri (consulte [novas APIs de pesquisa](~/ios/platform/search/index.md)), com o Ios 10, um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro dos seguintes locais:

- O alternador de aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de apelidos

O aplicativo expõe essa funcionalidade ao sistema usando uma coleção de tecnologias como `NSUserActivity` , Web Markup, Core Spotlight, MapKit, Media Player e UIKit. Além disso, ao fornecer suporte proativo de sugestão para o aplicativo, ele obtém uma integração de Siri mais profunda gratuitamente.

## <a name="location-based-suggestions"></a>Sugestões baseadas no local

Novo no iOS 10, a `NSUserActivity` classe inclui uma `MapItem` propriedade que permite ao desenvolvedor fornecer informações de localização que podem ser usadas em outros contextos. Por exemplo, se o aplicativo exibir as revisões de restaurante, o desenvolvedor poderá definir a `MapItem` propriedade como o local do restaurante que o usuário está visualizando no aplicativo. Se o usuário alternar para o aplicativo Maps, o local do restaurante estará automaticamente disponível.

Se o aplicativo oferecer suporte à pesquisa de aplicativo, ele poderá usar os novos componentes de endereço da `CSSearchableItemAttributesSet` classe para especificar os locais que o usuário talvez queira visitar. Ao definir a `MapItem` propriedade, as outras propriedades são preenchidas automaticamente.

Além de definir o `Latitude` e as `Longitude` Propriedades do componente de endereço, é recomendável que o aplicativo forneça as `NamedLocation` Propriedades e também para que `PhoneNumbers` Siri possa iniciar uma chamada para o local.

## <a name="web-markup-based-suggestions"></a>Sugestões baseadas na marcação da Web

o iOS 9 foi adicionado à capacidade de incluir marcação de dados estruturados no site que enriquece o conteúdo que os usuários veem nos resultados do Spotlight e da pesquisa do Safari (consulte [Pesquisar com marcação da Web](~/ios/platform/search/web-markup.md)). o iOS 10 adiciona a capacidade de incluir marcação baseada em local (como [PostalAddress](https://schema.org/PostalAddress) conforme definido pelo [Schema.org](https://schema.org/)) para aprimorar ainda mais a experiência do usuário. Por exemplo, se um usuário exibir uma página marcada como local no site, o sistema poderá sugerir o mesmo local ao abrir mapas.

## <a name="text-based-suggestions"></a>Sugestões baseadas em texto

UIKit foi expandido no iOS 10 para incluir a propriedade [Textcontenttype](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) da classe [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) para especificar o significado semântico do conteúdo em uma área de texto. Com essas informações em vigor, o sistema geralmente pode selecionar automaticamente o tipo de teclado apropriado, melhorar as sugestões de correção automática e integrar proativamente as informações de outros aplicativos e sites.

Por exemplo, se o usuário estiver inserindo texto em um campo de texto marcado `UITextContentType.FullStreetAddress` , o sistema poderá sugerir o preenchimento automático do campo com o local em que o usuário foi visualizado recentemente.

## <a name="media-based-suggestions"></a>Sugestões baseadas em mídia

Se o aplicativo reproduzir a mídia usando a API [MPPlayableContentManager](xref:MediaPlayer.MPPlayableContentManager) , o Ios 10 permitirá que os usuários exibam a arte do álbum e executem mídia por meio do aplicativo na tela de bloqueio.

## <a name="contextual-siri-reminders"></a>Lembretes de Siri contextuais

Permite que o usuário use o Siri para fazer um lembrete rapidamente para exibir o conteúdo que ele está exibindo atualmente no aplicativo em uma data posterior. Por exemplo, se eles estivessem exibindo uma revisão de restaurante no aplicativo, poderiam invocar Siri e dizer *"Lembre-me disso quando eu chegar à casa".* Siri geraria o lembrete com um link para a revisão no aplicativo.

## <a name="contact-based-suggestions"></a>Sugestões baseadas em contato

Permite que os contatos do aplicativo (e informações relacionadas a contato) apareçam no aplicativo de **contato** no dispositivo IOS junto com todos os contatos de usuários armazenados no sistema.

## <a name="ride-sharing-based-suggestions"></a>Sugestões com base no compartilhamento de Rides

Se um aplicativo de compartilhamento de Rides usar a API [MKDirectionsRequest](xref:MapKit.MKDirectionsRequest) , o Ios 10 irá apresentá-lo como uma opção no alternador de aplicativo em momentos em que o usuário provavelmente desejará uma jornada. O aplicativo também deve ser registrado como um aplicativo de compartilhamento de Rides especificando o `MKDirectionsModeRideShare` para a chave [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) em seu `Info.plist` arquivo.

Se o aplicativo der suporte apenas ao compartilhamento de Rides, a sugestão do sistema começaria com *"obter um passe para..."*, se outros tipos de direção de roteamento (como movimentação ou bicicleta) tiverem suporte, o sistema usará *"obter instruções para..."*

> [!IMPORTANT]
> O objeto [MKMapItem](xref:MapKit.MKMapItem) que o aplicativo recebe pode não incluir informações de longitude e latitude e exigirá geocodificação.

## <a name="implementing-proactive-suggestions"></a>Implementando sugestões proativas

Adicionar suporte de sugestão proativa a um aplicativo Xamarin. iOS normalmente é tão fácil quanto implementar algumas APIs ou expandir algumas APIs que o aplicativo já pode estar implementando.

As sugestões proativas funcionam com os aplicativos de três maneiras principais:

- ** `NSUserActivity` e Schema.org**  -  `NSUserActivity` ajuda o sistema a entender em que informações o usuário está trabalhando no momento na tela. O Schema.org adiciona habilidades semelhantes às páginas da Web.
- **Sugestões de localização** – se o aplicativo oferecer ou consumir informações baseadas na localização, essa extensão de API oferecerá novas maneiras de compartilhar essas informações entre aplicativos.
- **Sugestões de aplicativos de mídia** -o sistema pode promover o aplicativo e seu conteúdo de mídia com base no contexto da interação do usuário com o dispositivo IOS.

E tem suporte no aplicativo implementando o seguinte:

- **Handoff**  -  Entrega `NSUserActivity` foi adicionado no iOS 8 para dar suporte à entrega, que permite que o desenvolvedor inicie uma atividade em um dispositivo e, em seguida, continue em outro (Confira [introdução à entrega](~/ios/platform/handoff.md)).
- **Pesquisa de destaque** -o Ios 9 adicionou a capacidade de promover o conteúdo do aplicativo de dentro dos resultados da pesquisa de destaque usando `NSUserActivity` (consulte [Pesquisar com o destaque de núcleo](~/ios/platform/search/corespotlight.md)).
- Os **lembretes de Siri contextuais** -no Ios 10, `NSUserActivity` foram expandidos para permitir que o Siri faça um lembrete rapidamente para exibir o conteúdo que o usuário está visualizando no aplicativo no momento posteriormente.
- **Sugestões de localização** -o Ios 10 aprimora os `NSUserActivity` locais de captura exibidos no aplicativo e os promove em vários lugares em todo o sistema.
- **Contextual Siri Requests**  -  Solicitações `NSUserActivity` Siri contextuais fornece o contexto para as informações apresentadas dentro do aplicativo para Siri para que o usuário possa obter direções ou fazer uma chamada chamando Siri de dentro do aplicativo.
- **Entre em contato com as interações** -novas no Ios 10, `NSUserActivity` permite que os aplicativos de comunicação sejam promovidos de um cartão de contato (no aplicativo de contatos) como um método de comunicação alternativo.

Todos esses recursos têm uma coisa em comum, todos eles usam `NSUserActivity` em uma forma ou em outra para fornecer sua funcionalidade. 

## <a name="nsuseractivity"></a>NSUserActivity

Conforme mencionado acima, `NSUserActivity` o ajuda o sistema a entender em que informações o usuário está trabalhando no momento na tela. `NSUserActivity`é um mecanismo de cache de estado leve para capturar a atividade do usuário à medida que navega pelo aplicativo. Por exemplo, olhando para um aplicativo de restaurante:

[![O mecanismo de cache de estado de leve peso NSUserActivity](proactive-suggestions-images/activity02.png)](proactive-suggestions-images/activity02.png#lightbox)

Com as seguintes interações:

1. À medida que o usuário trabalha com o aplicativo, um `NSUserActivity` é criado para recriar o estado do aplicativo mais tarde.
2. Se o usuário procurar um restaurante, o mesmo padrão de criação de atividades será seguido.
3. E novamente, quando o usuário exibe um resultado. Neste último caso, o usuário está exibindo um local e no iOS 10, o sistema está mais ciente de determinados conceitos (como interações de local ou de comunicação).

Dê uma olhada mais atenta na última tela:

[![Os detalhes do NSUserActivity](proactive-suggestions-images/activity03.png)](proactive-suggestions-images/activity03.png#lightbox)

Aqui, o aplicativo está criando um `NSUserActivity` e foi populado com informações para recriar o estado mais tarde. O aplicativo também incluiu alguns metadados, como o nome e o endereço do local. Com essa atividade criada, o aplicativo permite que o iOS saiba que ele representa o estado atual do usuário.

Em seguida, o aplicativo decide se a atividade será anunciada através do ar para entrega, salva como um valor temporário para sugestões de localização ou adicionada ao índice de destaque no dispositivo para exibição nos resultados da pesquisa.

Para obter mais informações sobre a pesquisa de entrega e destaque, consulte nossa [introdução aos](~/ios/platform/handoff.md) novos guias de APIs de pesquisa de entrega e [Ios 9](~/ios/platform/search/index.md) .

### <a name="creating-an-activity"></a>Criando uma atividade

Antes de criar uma atividade, um identificador de tipo de atividade precisará ser criado para identificá-lo. O identificador de tipo de atividade é uma cadeia de caracteres curta adicionada à `NSUserActivityTypes` matriz do `Info.plist` arquivo do aplicativo usado para identificar exclusivamente um determinado tipo de atividade de usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo suporta e expõe para a pesquisa de aplicativo. Consulte nossa [referência de identificadores de tipo de atividade de criação](~/ios/platform/search/nsuseractivity.md) para obter mais detalhes.

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

Uma nova atividade é criada usando um identificador de tipo de atividade. Em seguida, alguns metadados que definem a atividade são criados para que esse estado possa ser restaurado em uma data posterior. Em seguida, a atividade recebe um título significativo e anexada às informações do usuário. Por fim, alguns recursos estão habilitados e a atividade é enviada para o sistema.

O código acima poderia ser aprimorado para incluir metadados que fornecem contexto para a atividade fazendo as seguintes alterações:

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

Se o desenvolvedor tiver um site capaz de exibir as mesmas informações que o aplicativo, o aplicativo poderá incluir a URL e o conteúdo poderá ser exibido em outros dispositivos que não têm o aplicativo instalado (via entrega):

```csharp
// Restore on the web
activity.WebPageUrl = new NSUrl("http://xamarin.com/platform");
```

### <a name="restoring-an-activity"></a>Restaurando uma atividade

Para responder ao usuário tocando em um resultado de pesquisa ( `NSUserActivity` ) para o aplicativo, edite o arquivo **AppDelegate.cs** e substitua o `ContinueUserActivity` método. Por exemplo:

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

O desenvolvedor precisará garantir que esse é o mesmo identificador de tipo de atividade ( `com.xamarin.platform` ) que a atividade criada acima. O aplicativo usa as informações armazenadas no `NSUserActivity` para restaurar o estado de volta para o local em que o usuário parou.

### <a name="benefits-of-creating-an-activity"></a>Benefícios da criação de uma atividade

Com a quantidade mínima de código apresentada acima, o aplicativo agora pode aproveitar três novos recursos do iOS 10:

- **Entrega**
- **Pesquisa de destaque**
- **Lembretes de Siri contextuais**

A seção a seguir configurará a habilitação de dois outros novos recursos do iOS 10:

- **Sugestões de localização**
- **Solicitações Siri contextuais**

### <a name="location-based-suggestions"></a>Sugestões baseadas no local 

Veja o exemplo do aplicativo de pesquisa de restaurante acima. Se ele tiver implementado `NSUserActivity` e preenchido corretamente todos os metadados e atributos, o usuário poderá fazer o seguinte:

1. Encontre um restaurante no aplicativo no qual gostaria de cumprir um amigo.
2. À medida que o usuário sai do aplicativo usando o alternador de aplicativo multitarefa, o sistema exibirá automaticamente uma sugestão (na parte inferior da tela) para obter instruções para o restaurante usando seu aplicativo de navegação favorito.
3. Se o usuário mudar para o aplicativo de mensagens e começar a digitar *"Vamos encontrar em"*, o teclado de rápidotype irá sugerir automaticamente a colagem no endereço do restaurante.
4. Se o usuário alternar para o aplicativo Maps, o endereço do restaurante será automaticamente Sugerido como destino.
5. Isso mesmo funciona para aplicativos de terceiros (que dão suporte a `NSUserActivity` ), portanto, o usuário pode alternar para um aplicativo de compartilhamento de Rides e o endereço do restaurante é automaticamente Sugerido como um destino lá também.
6. Ele também fornece contexto para Siri, para que o usuário possa invocar Siri no aplicativo restaurante e perguntar *"obter direções..."* e Siri fornecerá instruções para o restaurante que o usuário está visualizando.

Toda a funcionalidade acima tem uma coisa em comum, todas elas indicam de onde a sugestão é proveniente. No caso do exemplo acima, é o aplicativo de revisão de restaurante fictício.

o iOS 10 foi aprimorado para habilitar essa funcionalidade para um aplicativo por meio de várias pequenas modificações e adições a estruturas existentes:

- `NSUserActivity`tem campos adicionais para capturar informações de local que são exibidas dentro do aplicativo.
- Várias adições foram feitas em MapKit e CoreSpotlight para capturar o local.
- A funcionalidade de reconhecimento de local foi adicionada a Siri, mapas, teclados, multitarefa e outros aplicativos no sistema.

Para implementar sugestões baseadas no local, comece com o mesmo código de atividade apresentado acima:

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

Se o aplicativo estiver usando MapKit, será tão simples quanto adicionar o mapa atual `MKMapItem` à atividade:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Se o aplicativo não estiver usando o MapKit, ele poderá adotar a pesquisa de aplicativo e especificar os novos atributos a seguir para o local:

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

Dê uma olhada no código acima em detalhes. Primeiro, o nome do local é necessário em cada instância:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Em seguida, a Descrição baseada em texto do local é necessária para instâncias baseadas em texto (como o teclado do QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

A latitude e a longitude são opcionais, mas certifique-se de que o usuário seja roteado para o local exato para o qual o aplicativo deseja enviá-los, portanto, ele deve ser incluído:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Ao definir os números de telefone, o aplicativo pode obter acesso ao Siri para que o usuário possa invocar Siri do aplicativo dizendo algo como *"chamar este local"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por fim, o aplicativo pode indicar se a instância é adequada para navegação e chamadas telefônicas:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementando interações de contato

Novidades no iOS 10, os aplicativos de comunicação estão profundamente integrados ao aplicativo contatos do cartão de visita. Para aplicativos que implementaram interações de contato, o usuário pode adicionar as informações do aplicativo específico a pessoas específicas em seus contatos. E se, por exemplo, eles atingirem o botão de ação rápida na parte superior de um cartão para enviar uma mensagem, o aplicativo anexado será listado como uma opção para enviar a mensagem.

Se um aplicativo de terceiros for selecionado, ele poderá ser lembrado e apresentado como a maneira padrão de mensagens da pessoa em questão na próxima vez que o usuário quiser contatá-las.

As interações de contato são implementadas no aplicativo usando `NSUserActivity` o e a nova estrutura de tentativas introduzida no Ios 10. Para obter mais detalhes sobre como trabalhar com tentativas, consulte nossos [conceitos de entendendo o SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) e [implementando](~/ios/platform/sirikit/implementing-sirikit.md) os guias de SiriKit.

#### <a name="donating-interactions"></a>Doando interações

Veja como o aplicativo pode doar interações:

[![Visão geral das interações de doação](proactive-suggestions-images/activity04.png)](proactive-suggestions-images/activity04.png#lightbox)

O aplicativo cria um `INInteraction` objeto que contém uma **intenção** ( `INIntent` ), **participantes** e **metadados**. A **intenção** representa uma ação do usuário, como fazer uma chamada de vídeo ou enviar uma mensagem de texto. Os **participantes** incluem as pessoas que recebem a comunicação. Os **metadados** definem informações de adição, como o envio bem-sucedido da mensagem, etc.

O desenvolvedor nunca cria uma instância do `INIntent` ou `INIntentResponse` , ele usará uma das classes filho específicas (com base na tarefa que o aplicativo está realizando em nome do usuário) que herdam dessas classes pai. Por exemplo, `INSendMessageIntent` e `INSendMessageIntentResponse` para enviar uma mensagem de texto. 

Quando a interação estiver totalmente preenchida, chame o `DonateInteraction` método para informar ao sistema que a interação está disponível para uso.

Quando o usuário interage com o aplicativo do cartão de contato, a interação é agrupada com um `NSUserActivity` , que é usado para iniciar o aplicativo:

[![A interação é agrupada com um NSUserActivity que é usado para iniciar o aplicativo](proactive-suggestions-images/activity05.png)](proactive-suggestions-images/activity05.png#lightbox)

Veja o seguinte exemplo de uma intenção de envio de mensagem:

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

Observando esse código em detalhes, ele cria e popula uma instância do `NSUserActivity` (conforme mostrado na seção [criando uma atividade](#creating-an-activity) acima). Em seguida, ele cria uma instância do `INSendMessageIntent` (que herda de `INIntent` ) e a popula com os detalhes da mensagem que está sendo enviada:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Um `INSendMessageIntentResponse` é criado e passado para o `NSUserActivity` criado acima:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Um `INInteraction` é criado a partir da tentativa de mensagem de envio ( `INSendMessageIntent` ) e da resposta ( `INSendMessageIntentResponse` ) recém-criada:

```csharp
var interaction = new INInteraction (intent, response);
```

Por fim, o sistema é uma notificação da interação:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
  // Handle donation error
  ...
});
```

Um manipulador de conclusão é passado em que o aplicativo pode responder à doação com êxito ou falha.

### <a name="activities-best-practices"></a>Práticas recomendadas de atividades

A Apple sugere as seguintes práticas recomendadas ao trabalhar com atividades:

- Use `NeedsSave` para atualizações de carga lenta.
- Certifique-se de manter uma referência forte para a atividade atual.
- Transfira somente cargas pequenas que incluam apenas informações suficientes para restaurar o estado.
- Certifique-se de que os identificadores de tipo de atividade sejam exclusivos e descritivos usando a notação de DNS reverso para especificá-los. 

## <a name="schemaorg"></a>Schema.org

Como mostrado acima, `NSUserActivity` o ajuda o sistema a entender em que informações o usuário está trabalhando no momento na tela. O Schema.org adiciona habilidades semelhantes às páginas da Web.

O Schema.org pode fornecer os mesmos tipos de interações baseadas no local para o site. A Apple projetou as sugestões de localização para trabalhar tão bem quando visualizadas no Safari como em um aplicativo nativo.

Algum plano de fundo do Schema.org:

- Ele fornece um padrão de vocabulário de marcação da Web aberto.
- Ele funciona incluindo metadados estruturados em páginas da Web.
- Há mais de 500 esquemas que representam vários conceitos disponíveis.
- Ao implementá-lo no site, o desenvolvedor pode adquirir alguns dos benefícios de usar o `NSUserActivity` em um aplicativo nativo.

Os esquemas são organizados em uma árvore como estrutura, em que tipos específicos, como *restaurante*, herdam de tipos mais genéricos, como *negócios locais*. Para obter mais informações, consulte [Schema.org](https://schema.org).

Por exemplo, se a página da Web incluía os seguintes dados:

```html
<script type="application/ld+json">
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

Se o usuário visitou esta página no Safari e, em seguida, mudou para outro aplicativo, as informações de local da página seriam capturadas e oferecidas como uma sugestão de local em outras partes do sistema (como visto nas atividades acima).

O Safari extrairá qualquer coisa em uma página da Web que esteja de acordo com qualquer uma das seguintes propriedades de esquema:

- **PostalAddress**
- **GeoCoordinates**
- Uma propriedade de telefone.

Para obter mais informações, consulte nosso guia [de pesquisa com a marcação da Web](~/ios/platform/search/web-markup.md) .

## <a name="consuming-location-suggestions"></a>Consumindo sugestões de localização

Esta próxima seção abordará o consumo de sugestões de local provenientes de outras partes do sistema (como o aplicativo Maps) ou outros aplicativos de terceiros.

Há duas maneiras principais pelas quais o aplicativo pode consumir sugestões de localização:

- Por meio do teclado de modo rápido.
- Diretamente, consumindo as informações de localização em aplicativos de roteamento.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Sugestões de localização e o teclado do QuickType

Se o aplicativo lida com endereços em formatos baseados em texto, o aplicativo pode aproveitar as sugestões de localização por meio da interface do usuário do QuickType. o iOS 10 expande a interface do usuário do QuickType com os seguintes recursos:

- O aplicativo pode adicionar dicas sobre a intenção semântica para campos de texto na interface do usuário.
- O aplicativo pode obter sugestões proativas no aplicativo.
- O aplicativo pode se beneficiar da correção automática aprimorada.

A nova `TextContentType` propriedade dos controles de campo de texto no Ios 10 permite que o desenvolvedor defina a intenção semântica para o valor que o usuário vai inserir em um determinado campo. Por exemplo:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Informa ao sistema que o aplicativo espera que o usuário insira um endereço completo no campo fornecido. Isso permitirá que o teclado do modo rápido forneça sugestões de localização automaticamente no teclado quando o usuário estiver inserindo um valor nesse campo.

A seguir estão alguns dos tipos mais comuns disponíveis para o desenvolvedor na `UITextContentType` classe estática:

- `Name`
- `GivenName`
- `FamilyName`
- `Location`
- `FullStreetAddress`
- `AddressCityAndState`
- `TelephoneNumber`
- `EmailAddress`

### <a name="routing-apps-and-locations-suggestions"></a>Sugestões de aplicativos e locais de roteamento

Esta seção configurará as sugestões de localização diretamente de dentro de um aplicativo de roteamento. Para que o aplicativo de roteamento adicione essa funcionalidade, o desenvolvedor usará a `MKDirectionsRequest` estrutura existente da seguinte maneira:

- Para promover o aplicativo em multitarefa.
- Para registrar o aplicativo como um aplicativo de roteamento.
- Para lidar com a inicialização do aplicativo com um `MKDirectionsRequest` objeto MapKit.
- Para dar ao iOS a capacidade de aprender a sugerir o aplicativo para o usuário em momentos apropriados, com base no envolvimento do usuário.

Quando o aplicativo é iniciado com um `MKDirectionsRequest` objeto MapKit, ele deve começar automaticamente a fornecer instruções ao usuário para o local solicitado, ou apresentar uma interface do usuário que torna mais fácil para o usuário começar a obter direções. Por exemplo:

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

Dê uma olhada nesse código detalhadamente. Ele testa para ver se é uma solicitação de destino válida:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Se for, ele criará um `MKDirectionsRequest` da URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Novo no iOS 10, o aplicativo pode ser enviado um endereço que não tem coordenadas geográficas, no que faz com que o desenvolvedor precise codificar o endereço:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
  // Handle the display of the address
  
});

```

## <a name="media-app-suggestions"></a>Sugestões de aplicativos de mídia

Se o aplicativo manipula qualquer tipo de mídia como um aplicativo de podcast ou um conteúdo de mídia de streaming como áudio ou vídeo, com iOS 10, ele pode aproveitar as sugestões de mídia no sistema.

Para aplicativos que lidam com mídia, o iOS dá suporte aos seguintes comportamentos:

- o iOS promove aplicativos que o usuário provavelmente usará com base em seu comportamento anterior.
- As sugestões relacionadas ao aplicativo serão apresentadas em destaque e na exibição atual.
- Se o aplicativo atender a um dos seguintes gatilhos, ele poderá ser elevado a uma sugestão de tela de bloqueio:
  - Depois de conectar os fones de ouvido ou um dispositivo Bluetooth, faça uma conexão.
  - Depois de entrar em um carro.
  - Depois de chegar em casa ou trabalho. 

Ao incluir uma chamada de API simples no iOS 10, o desenvolvedor pode criar uma experiência de tela de bloqueio mais atraente para os usuários do aplicativo de mídia. Usando a `MPPlayableContentManager` classe para gerenciar a reprodução de mídia, os controles de mídia completos (como os apresentados pelo aplicativo de música) serão apresentados na tela de bloqueio do aplicativo.

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

Este artigo abordou as sugestões proativas e mostrou como o desenvolvedor pode usá-las para direcionar o tráfego para o aplicativo Xamarin. iOS. Ele abordou a etapa de implementar sugestões proativas e as diretrizes de uso apresentadas.

## <a name="related-links"></a>Links Relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [Guia de programação do SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
