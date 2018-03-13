---
title: "Introdução ao sugestões proativos"
description: "Este artigo mostra como usar sugestões pró-ativo no aplicativo xamarin ao contrato de unidade, permitindo que o sistema proativamente apresentar informações úteis automaticamente para o usuário."
ms.topic: article
ms.prod: xamarin
ms.assetid: 8DDD084A-0D1E-4DF7-B686-6309DCEFF5D3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 15f649440b2a855189acff33afcef5e8272a0769
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="introduction-to-proactive-suggestions"></a>Introdução ao sugestões proativos

_Este artigo mostra como usar sugestões pró-ativo no aplicativo xamarin ao contrato de unidade, permitindo que o sistema proativamente apresentar informações úteis automaticamente para o usuário._

Novo para o iOS 10 maneiras de notícias presentes pró-ativo sugestões para os usuários a se comunicar com um aplicativo xamarin por proativamente apresentam informações úteis automaticamente para o usuário em momentos apropriados.

iOS 10 apresenta novas maneiras de um contrato para o aplicativo, permitindo que o sistema de forma proativa apresentar informações úteis automaticamente para o usuário em momentos apropriados. Assim como iOS 9 fornecido a capacidade de adicionar a busca detalhada para o aplicativo usando o destaque, entrega e Siri sugestões (consulte [novas APIs de pesquisa](~/ios/platform/search/index.md)), com iOS 10 um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro do seguintes locais:

- O seletor de exibição do aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de QuickType

O aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias como `NSUserActivity`, marcação de web, destaque de núcleo, MapKit, reprodutor de mídia e UIKit. Além disso, fornecendo suporte de sugestão pró-ativo para o aplicativo, ele obtém uma integração mais profunda Siri gratuitamente.

## <a name="location-based-suggestions"></a>Sugestões baseadas em local

Novo para o iOS 10, o `NSUserActivity` classe inclui um `MapItem` propriedade que permite ao desenvolvedor fornecer informações de local que podem ser usadas em outros contextos. Por exemplo, se o aplicativo exibe revisões do restaurante, o desenvolvedor pode definir o `MapItem` propriedade para o local do restaurante que o usuário está exibindo no aplicativo. Se o usuário alterna para o aplicativo de mapas, local do restaurante está disponível automaticamente.

Se o aplicativo dá suporte à pesquisa do aplicativo, ele poderá usar os novos componentes de endereço de `CSSearchableItemAttributesSet` classe para especificar locais que o usuário pode desejar visitar. Definindo o `MapItem` propriedade, as outras propriedades são preenchidos automaticamente.

Além de configuração de `Latitude` e `Longitude` de propriedades do componente de endereço, é recomendável que o aplicativo fornecer a `NamedLocation` e `PhoneNumbers` propriedades muito, portanto Siri pode iniciar uma chamada para o local.

## <a name="web-markup-based-suggestions"></a>Sugestões baseadas em marcação da Web

iOS 9 adicionado a possibilidade de incluir marcação dados estruturados no site que enriquece o conteúdo que os usuários veem nos resultados de pesquisa do Spotlight e Safari (consulte [pesquisa com marcação Web](~/ios/platform/search/web-markup.md)). iOS 10 adiciona a capacidade de incluir marcação com base no local (como [endereço](http://schema.org/PostalAddress) conforme definido pelo [Schema.org](http://schema.org/)) para aprimorar a experiência do usuário. Por exemplo, se um modos de exibição de usuários um local marcado como página no site, o sistema pode sugerir no mesmo local quando abrirem mapas.

## <a name="text-based-suggestions"></a>Sugestões baseadas em texto

UIKit foi expandido no iOS 10 para incluir o [TextContentType](https://developer.apple.com/reference/uikit/uitextinputtraits/1649656-textcontenttype) propriedade o [UITextInputTraits](https://developer.apple.com/reference/uikit/uitextinputtraits) classe para especificar o significado semântico do conteúdo em uma área de texto. Com essas informações em vigor, o sistema pode geralmente automaticamente, selecione o tipo de teclado apropriado, melhorar AutoCorreção sugestões e integrar proativamente as informações de outros aplicativos e sites.

Por exemplo, se o usuário está inserindo texto em um campo de texto marcado `UITextContentType.FullStreetAddress`, o sistema pode sugerir o campo de preenchimento automático com o local em que o usuário foi recentemente exibindo.

## <a name="media-based-suggestions"></a>Sugestões baseadas em mídia

Se o aplicativo executa a mídia usando o [MPPlayableContentManager](https://developer.xamarin.com/api/type/MediaPlayer.MPPlayableContentManager/) API, o iOS 10 permite aos usuários exibir a arte do álbum e reproduzir mídia por meio do aplicativo na tela de bloqueio.

## <a name="contextual-siri-reminders"></a>Lembretes Siri contextuais

Permite que o usuário usar Siri para criar rapidamente um lembrete para exibir o conteúdo que estão exibindo no momento no aplicativo em uma data posterior. Por exemplo, se eles foram exibindo um restaurante no aplicativo, eles poderiam invocar Siri e dizer *"Lembrar-me sobre isso quando recebo home."* Siri geraria o lembrete com um link para a revisão no aplicativo.

## <a name="contact-based-suggestions"></a>Sugestões baseadas em contato

Permite que o aplicativo contatos (e entre em contato com as informações relacionadas) apareçam no **entre em contato com** aplicativo no dispositivo iOS, juntamente com todos os contatos de usuários são armazenados no sistema.

## <a name="ride-sharing-based-suggestions"></a>Sugestões baseadas em jornada de compartilhamento

Se um aplicativo de compartilhamento jornada usa o [MKDirectionsRequest](https://developer.xamarin.com/api/type/MapKit.MKDirectionsRequest/) API, iOS 10 irá apresentá-lo como uma opção no alternador de aplicativo às vezes quando o usuário é provável que queira uma jornada. O aplicativo também deve ser registrado como um aplicativo de compartilhamento jornada especificando o `MKDirectionsModeRideShare` para o [MKDirectionsApplicationSupportedModes](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html) chave em seu `Info.plist` arquivo.

Se o aplicativo só dá suporte a jornada de compartilhamento, a sugestão de sistema começaria com *"Obter uma jornada para..."*, se houver suporte para outros tipos de roteamento direção (como Walking ou bicicleta), o sistema usará *"Obter o caminho para..."*

> [!IMPORTANT]
> **Observação:** o [MKMapItem](https://developer.xamarin.com/api/type/MapKit.MKMapItem/) objeto que recebe o aplicativo não pode incluir informações de longitude e latitude e exigirá geocodificação.

## <a name="implementing-proactive-suggestions"></a>Implementar sugestões proativos

Suporte a um aplicativo xamarin adicionando pró-ativo sugestão é normalmente mais fácil implementar algumas APIs ou expandindo algumas APIs que o aplicativo já pode estar implementando.

Sugestões pró-ativo funcionam com os aplicativos de três maneiras principais:

- **`NSUserActivity` e Schema.org**  -  `NSUserActivity` ajuda o sistema a entender as informações que o usuário estiver atualmente trabalhando na tela. Schema.org adiciona recursos semelhantes para páginas da web.
- **Sugestões de local** - se o aplicativo oferece ou consome informações de localização com base, essas API extensão oferecem novas maneiras de compartilhar informações entre aplicativos.
- **Sugestões de aplicativo de mídia** - o sistema pode promover o aplicativo e seu conteúdo de mídia com base no contexto de interação do usuário com o dispositivo iOS.

E tem suporte no aplicativo Implementando o seguinte:

- **Entrega**  -  `NSUserActivity` foi adicionado no iOS 8 para dar suporte a entrega, o que permite ao desenvolvedor iniciar uma atividade em um dispositivo e, em seguida, continua na outra (consulte [Introdução à entrega](~/ios/platform/handoff.md)).
- **Pesquisa de destaque** -iOS 9 adicionada a capacidade de promover o conteúdo do aplicativo de dentro de resultados da pesquisa do Spotlight usando `NSUserActivity` (consulte [pesquisa com destaque Core](~/ios/platform/search/corespotlight.md)).
- **Contextuais lembretes de Siri** - no iOS 10, `NSUserActivity` foi expandida para permitir que o Siri criar rapidamente um lembrete para exibir o conteúdo que o usuário está exibindo no aplicativo em uma data posterior.
- **Sugestões de local** -iOS 10 aprimora `NSUserActivity` capturar locais exibidos dentro do aplicativo e promovê-los em vários locais em todo o sistema.
- **Solicitações de Siri contextuais**  -  `NSUserActivity` fornece um contexto para as informações apresentadas dentro do aplicativo Siri para que o usuário pode receber instruções ou local de uma chamada ser Siri chamada de dentro do aplicativo.
- **Entre em contato com interações** - novo no iOS 10, `NSUserActivity` permite que aplicativos de comunicações ser promovido de um cartão de visita (no aplicativo contatos) como um método alternativo de comunicação.

Todos esses recursos têm algo em comum, todos eles usarão `NSUserActivity` de uma forma ou de outra para fornecer sua funcionalidade. 

## <a name="nsuseractivity"></a>NSUserActivity

Como mencionado acima, `NSUserActivity` ajuda o sistema a entender as informações que o usuário estiver atualmente trabalhando na tela. `NSUserActivity` é um estado leve mecanismo para capturar a atividade do usuário, como eles navegam por meio do aplicativo de cache. Por exemplo, olhando para um aplicativo do restaurante:

[![](proactive-suggestions-images/activity02.png "O estado de leve NSUserActivity mecanismo de cache")](proactive-suggestions-images/activity02.png#lightbox)

Com as interações do seguintes:

1. Como o usuário trabalha com o aplicativo, um `NSUserActivity` é criado para recriar o estado do aplicativo mais tarde.
2. Se o usuário procura um restaurante, o mesmo padrão de criação de atividades é seguido.
3. E, novamente, quando o usuário exibe um resultado. Neste último caso, o usuário está exibindo um local e no iOS 10, o sistema mais reconhece determinados conceitos (como interações local ou a comunicação).

Analisar mais detalhadamente na última tela:

[![](proactive-suggestions-images/activity03.png "Os detalhes de NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Aqui o aplicativo está criando um `NSUserActivity` e tenha sido populada com informações para recriar o estado mais tarde. O aplicativo também inclui alguns metadados, como o nome e o endereço do local. Com essa atividade criada, o aplicativo permite que iOS Saiba que representa o estado do usuário atual.

O aplicativo, em seguida, decide se a atividade será anunciada sem fio para entrega, salvos como um valor temporário para obter sugestões de local ou adicionada ao índice de destaque no dispositivo para exibir nos resultados da pesquisa.

Para obter mais informações sobre a pesquisa de entrega e destaque, consulte nosso [Introdução à entrega](~/ios/platform/handoff.md) e [iOS 9 novas APIs de pesquisa](~/ios/platform/search/index.md) guias.

### <a name="creating-an-activity"></a>Criando uma atividade

Antes de criar uma atividade, um identificador de tipo de atividade precisa ser criada para identificá-lo. O identificador de tipo de atividade é uma cadeia de caracteres curta adicionada para o `NSUserActivityTypes` matriz do aplicativo `Info.plist` arquivo usado para identificar exclusivamente um determinado tipo de atividade do usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo oferece suporte e expõe para o aplicativo de pesquisa. Consulte nossa [criar referência de identificadores de tipo de atividade](~/ios/platform/search/nsuseractivity.md) para obter mais detalhes.

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

Uma nova atividade é criada usando um identificador de tipo de atividade. Em seguida, alguns metadados que definem a atividade é criado para que esse estado pode ser restaurado em uma data posterior. Em seguida, a atividade é fornecida um título descritivo e anexada para as informações de usuário. Por fim, alguns recursos estão habilitados e a atividade é enviada para o sistema.

O código acima pode ser aprimorado ainda mais para incluir metadados que fornece um contexto para a atividade fazendo as seguintes alterações:

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

### <a name="restoring-an-activity"></a>Restaurando uma atividade

Responder para o usuário tocar em um resultado de pesquisa (`NSUserActivity`) para o aplicativo, edite o **appdelegate. CS** de arquivo e substituir o `ContinueUserActivity` método. Por exemplo:

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

O desenvolvedor precisa garantir que este é o mesmo identificador de tipo de atividade (`com.xamarin.platform`) como a atividade criada acima. O aplicativo usa as informações armazenadas no `NSUserActivity` para restaurar o estado em que o usuário parou.

### <a name="benefits-of-creating-an-activity"></a>Benefícios da criação de uma atividade

Com a quantidade mínima de código apresentado acima, o aplicativo agora é possível tirar proveito dos três novos recursos de iOS 10:

- **Handoff**
- **Pesquisa do Spotlight**
- **Lembretes Siri contextuais**

A seção a seguir dar uma olhada em Habilitar dois outros recursos novos do iOS 10:

- **Sugestões de local**
- **Solicitações de Siri contextuais**

### <a name="location-based-suggestions"></a>Sugestões baseadas em local 

Veja o exemplo de aplicativo de pesquisa do restaurante acima. Se tiver implementado `NSUserActivity` e preenchida corretamente todos os metadados e atributos, o usuário poderá fazer o seguinte:

1. Localize um restaurante no aplicativo que gostaria de atender a um amigo em.
2. Como o usuário sai do aplicativo usando o seletor de exibição do aplicativo de multitarefa, o sistema automaticamente exibirá uma sugestão (na parte inferior da tela) para obter instruções para o restaurante usando seu aplicativo de navegação favorito.
3. Se o usuário alterna para o aplicativo de mensagens e começar a digitar *"Vamos se encontrarem em"*, o teclado QuickType sugerirá automaticamente colá-lo no endereço do restaurante.
4. Se o usuário alterna para o aplicativo de mapas, endereço do restaurante automaticamente será sugerido como um destino.
5. Isso funciona até mesmo para aplicativos de terceiros 3º (que oferecem suporte a `NSUserActivity`), portanto, o usuário pode alternar para um aplicativo de compartilhamento jornada e endereço do restaurante é automaticamente sugerido como um destino lá também.
6. Ele também fornece contexto para Siri, para que o usuário pode invocar Siri dentro do aplicativo do restaurante e peça *"Obter direções …"* Siri serão fornecidas instruções para o restaurante que o usuário está exibindo.

Toda a funcionalidade acima tem algo em comum, todos eles indicam onde a sugestão é originalmente vindo. No caso do exemplo acima, é o aplicativo de análise do restaurante fictícios.

iOS 10 foi aprimorada para habilitar essa funcionalidade para um aplicativo por meio de várias pequenas modificações e adições para as estruturas existentes:

- `NSUserActivity` tem campos adicionais para capturar informações sobre o local que é exibido dentro do aplicativo.
- Foram feitas várias adições MapKit e CoreSpotlight para capturar o local.
- Funcionalidade de reconhecimento de local foi adicionada para Siri, mapas, teclados, multitarefa e outros aplicativos no sistema.

Para implementar as sugestões de localização com base, comece com o mesmo código de atividade apresentado a seguir:

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

Se o aplicativo estiver usando MapKit, ele é tão simple quanto adicionar mapa `MKMapItem` para a atividade:

```csharp
// Save MKMapItem location
activity.MapItem = myMapItem;
```

Se o aplicativo não está usando MapKit, ele pode adotar a pesquisa do aplicativo e especificar os seguintes atributos novo local:

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

Observe o código acima em detalhes. Primeiro, o nome do local é necessário em todas as instâncias:

```csharp
attributes.NamedLocation = "Apple Inc.";
```

Em seguida, a descrição de texto com base do local é necessária para instâncias de texto com base (como o teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

A latitude e longitude são opcionais, mas certifique-se de que o usuário será roteado para o local exato que do aplicativo é desejado enviá-los, ele deve ser incluído:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Definindo os números de telefone, o aplicativo pode obter acesso a Siri para que o usuário pode invocar Siri do aplicativo dizendo algo como *"Chamar este local"*:

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por fim, o aplicativo pode indicar se a instância é adequada para navegação e chamadas telefônicas:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

### <a name="implementing-contact-interactions"></a>Implementação de interações de contato

Novo no iOS 10, aplicativos de comunicação estão profundamente integrados no aplicativo contatos do cartão de contato. Para aplicativos que implementaram interações de contato, o usuário pode adicionar informações do aplicativo fornecido para pessoas específicas de seus contatos. E se, por exemplo, eles pressione o botão de ação rápida na parte superior de um cartão para enviar uma mensagem, o aplicativo anexado será listado como uma opção para enviar a mensagem do.

Se um aplicativo de terceiros 3º for selecionada, pode ser lembrada e apresentado como a forma padrão para a mensagem determinada pessoa na próxima vez que o usuário deseja entrar em contato com eles.

Interações de contato são implementadas no aplicativo usando `NSUserActivity` e a nova estrutura de tentativas introduzido no iOS 10. Para obter mais detalhes sobre como trabalhar com propósitos, consulte nosso [Noções básicas sobre conceitos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) e [SiriKit implementando](~/ios/platform/sirikit/implementing-sirikit.md) guias.

#### <a name="donating-interactions"></a>Interações de doação

Dê uma olhada em como o aplicativo pode Doe interações:

[![](proactive-suggestions-images/activity04.png "Visão geral de interações doação")](proactive-suggestions-images/activity04.png#lightbox)

O aplicativo cria um `INInteraction` objeto que contém um **intenção** (`INIntent`), **participantes** e **metadados**. O **intenção** representa uma ação do usuário, como fazer uma chamada de vídeo ou enviar uma mensagem de texto. O **participantes** incluem as pessoas que recebem a comunicação. O **metadados** define as informações de adição, como enviar com êxito a mensagem, etc.

O desenvolvedor nunca diretamente cria uma instância de `INIntent` ou `INIntentResponse`, eles usarão uma das classes filho específicos (com base na tarefa que o aplicativo é a realização em nome do usuário) que herdam essas classes pai. Por exemplo, `INSendMessageIntent` e `INSendMessageIntentResponse` para enviar uma mensagem de texto. 

Depois que a interação está completamente populada, chamar o `DonateInteraction` método para informar o sistema a interação está disponível para uso.

Quando o usuário interage com o aplicativo de cartão de visita, a interação é agrupada com um `NSUserActivity`, que é usado para iniciar o aplicativo:

[![](proactive-suggestions-images/activity05.png "A interação é agrupada com um NSUserActivity que é usado para iniciar o aplicativo")](proactive-suggestions-images/activity05.png#lightbox)

Observe o seguinte exemplo de uma tentativa de envio de mensagem:

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

Examinar este código detalhadamente, ele cria e preenche uma instância de `NSUserActivity` (conforme o [criando uma atividade](#Creating-an-Activity) seção acima). Em seguida, ele cria uma instância de `INSendMessageIntent` (que herda de `INIntent`) e o preenche com os detalhes da mensagem está sendo enviada:

```csharp
var intent = new INSendMessageIntent (to, text, "", "MonkeyChat", from);
```

Um `INSendMessageIntentResponse` é criado e passado a `NSUserActivity` criado acima:

```csharp
var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, activity);
```

Um `INInteraction` é criado a partir de intenção de mensagem enviar (`INSendMessageIntent`) e de resposta (`INSendMessageIntentResponse`) acabou de criar:

```csharp
var interaction = new INInteraction (intent, response);
```

Por fim, o sistema é notificação da interação:

```csharp
// Donate interaction to the system
interaction.DonateInteraction ((err) => {
    // Handle donation error
    ...
});
```

Um processador de conclusão é passado em que o aplicativo pode responder a doação êxito ou falha.

### <a name="activities-best-practices"></a>Práticas recomendadas de atividades

Apple sugere práticas recomendadas a seguir ao trabalhar com atividades:

- Use `NeedsSave` para atualizações de carga lento.
- Certifique-se de manter uma referência forte para a atividade atual.
- Transferir apenas pequenas cargas que incluem informações suficientes para restaurar o estado.
- Verifique se os identificadores de tipo de atividade estão exclusivo e descritivo, usando a notação inversa de DNS para especificá-los. 

## <a name="schemaorg"></a>Schema.org

Como mostrado acima, `NSUserActivity` ajuda o sistema a entender as informações que o usuário estiver atualmente trabalhando na tela. Schema.org adiciona recursos semelhantes para páginas da web.

Schema.org pode fornecer os mesmos tipos de interações de localização com base para o site. Apple projetado as sugestões local novo funcione tão bem quando exibido no Safari, como em um aplicativo nativo.

Plano de fundo alguns Schema.org:

- Ele fornece um padrão de vocabulário de marcação da web aberto.
- Ele funciona, incluindo metadados estruturados em páginas da web.
- Há mais de 500 esquemas que representa vários conceitos disponíveis.
- Por implementá-la no site, o desenvolvedor pode adquirir alguns dos benefícios do uso de `NSUserActivity` em um aplicativo nativo.

Os esquemas são organizados em uma árvore como estrutura, onde específico tipos como *restaurante*, herdadas de tipos mais genéricos, como *comercial Local*. Para obter mais informações, consulte [Schema.org](http://schema.org).

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

Se o usuário visitou esta página no Safari e, em seguida, alternar para outro aplicativo, as informações de local da página seriam capturadas e oferecidas como uma sugestão de local em outras partes do sistema (como visto nas atividades acima).

Safari extrairá tudo em uma página da web que segue a qualquer uma das propriedades de esquema a seguir:

- **PostalAddress**
- **GeoCoordinates**
- Uma propriedade de telefone.

Para obter mais informações, consulte nosso [pesquisa com marcação Web](~/ios/platform/search/web-markup.md) guia.

## <a name="consuming-location-suggestions"></a>Consumindo sugestões de local

Esta seção abordará consumindo sugestão de local que outras partes do sistema (como o aplicativo de mapas) ou outros aplicativos de terceiros 3º.

Há duas maneiras pelas quais que o aplicativo pode consumir sugestões de local:

- Por meio do teclado QuickType.
- Diretamente, as informações de local em roteamentos aplicativos de consumo.

### <a name="location-suggestions-and-the-quicktype-keyboard"></a>Sugestões de local e o teclado QuickType

Se o aplicativo lida com endereços em formatos de texto com base, o aplicativo pode tirar proveito de sugestões de local por meio de UI QuickType. iOS 10 expande a UI QuickType com os seguintes recursos:

- O aplicativo pode adicionar dicas sobre a intenção semântica para campos de texto na interface de usuário.
- O aplicativo pode obter sugestões pró-ativo no aplicativo.
- O aplicativo pode se beneficiar de AutoCorreção aprimorada.

O novo `TextContentType` propriedade dos controles de campo de texto no iOS 10 permite que o desenvolvedor definir a tentativa de semântica para o valor que o usuário irá inserir em um determinado campo. Por exemplo:

```csharp
var textField = new UITextField();
textField.TextContentType = UITextContentType.FullStreetAddress;
```

Informa o sistema que o aplicativo espera que o usuário insira um endereço completo em determinado campo. Isso permitirá que o teclado QuickType fornecer automaticamente as sugestões de local no teclado quando o usuário insere um valor nesse campo.

A seguir está alguns dos tipos mais comuns disponíveis para o desenvolvedor de `UITextContentType` classe estática:

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

- Para promover o aplicativo no multitarefa.
- Para registrar o aplicativo como um aplicativo de roteamento.
- Para tratar a iniciar o aplicativo com um MapKit `MKDirectionsRequest` objeto.
- Para fornecer iOS a capacidade para sugerir o aplicativo para o usuário em momentos apropriados, com base no contrato de usuário.

Quando o aplicativo é iniciado com um MapKit `MKDirectionsRequest` do objeto, ele deve iniciar automaticamente dando as instruções de usuário para o local solicitado, ou apresentar uma interface do usuário que torna mais fácil para o usuário começar a obter instruções. Por exemplo:


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

Examine esse código em detalhes. Ele testa para ver se ele é uma solicitação de destino válida:

```csharp
if (MKDirectionsRequest.IsDirectionsRequestUrl(url)) {
```

Se ele for, ele cria um `MKDirectionsRequest` da URL:

```csharp
var request = new MKDirectionsRequest(url);
```

Novo no iOS 10, o aplicativo pode ser enviado um endereço que não tenha coordenadas geograficamente, que fazem com que o desenvolvedor precisa codificar o endereço:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="media-app-suggestions"></a>Sugestões de aplicativo de mídia

Se o aplicativo lida com qualquer tipo de mídia como um aplicativo podcast ou um conteúdo de mídia de streaming como áudio ou vídeo, com iOS 10, ele pode aproveitar sugestões de mídia no sistema.

Para aplicativos que lidam com a mídia, iOS suporta os seguintes comportamentos:

- iOS promove os aplicativos que o usuário provavelmente usará com base em seu comportamento anterior.
- Serão apresentados sugestões relacionados ao aplicativo em destaque e a exibição de hoje.
- Se o aplicativo atenda a um dos seguintes disparadores, ele pode ser escalado para uma sugestão de tela de bloqueio:
    - Depois de montar fones de ouvido ou um dispositivo Bluetooth faz uma conexão.
    - Após a obtenção de um carro.
    - Depois que chegam em casa ou trabalho. 

Ao incluir uma chamada de API simples no iOS 10, o desenvolvedor pode criar uma experiência de tela de bloqueio mais atraente para usuários do aplicativo da mídia. Usando o `MPPlayableContentManager` classe para gerenciar a reprodução de mídia, controles de mídia completo (como aqueles que são apresentados pelo aplicativo música) será apresentado na tela de bloqueio para o aplicativo.


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

Este artigo tem coberto sugestões pró-ativo e mostrou como o desenvolvedor pode usá-los para direcionar o tráfego para o aplicativo xamarin. Ele abordado na etapa para implementar as sugestões pró-ativo e apresentadas diretrizes de uso.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
