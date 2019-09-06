---
title: Sugestões proativas do watchOS no Xamarin
description: Este artigo mostra como usar sugestões proativas em um aplicativo watchOS 3 para impulsionar o envolvimento, permitindo que o sistema apresente informações úteis de forma proativa automaticamente para o usuário.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 68ba09ce74f161c728f0a58276a0b0d98fec7d8c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281284"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>Sugestões proativas do watchOS no Xamarin

_Este artigo mostra como usar sugestões proativas em um aplicativo watchOS 3 para impulsionar o envolvimento, permitindo que o sistema apresente informações úteis de forma proativa automaticamente para o usuário._


Novidade no watchOS 3, as sugestões proativas apresentam notícias sobre como os usuários podem se envolver com um aplicativo Xamarin. iOS proativamente apresenta informações úteis automaticamente para o usuário em momentos apropriados.


## <a name="about-proactive-suggestions"></a>Sobre sugestões proativas

Novo no watchOS 3, `NSUserActivity` inclui uma `MapItem` propriedade que permite que o aplicativo forneça informações de localização que podem ser usadas em outros contextos. Por exemplo, se o aplicativo exibir revisões de Hotel e fornecer `MapItem` um local, se o usuário alternou para o aplicativo Maps, o local do hotel que ele estava exibindo estará disponível.

O aplicativo expõe essa funcionalidade ao sistema usando uma coleção de tecnologias como `NSUserActivity`, MapKit, Media Player e UIKit. Além disso, ao fornecer suporte proativo de sugestão para o aplicativo, ele obtém uma integração de Siri mais profunda gratuitamente.

## <a name="location-based-suggestions"></a>Sugestões baseadas no local

Novo no watchOS 3, a `NSUserActivity` classe inclui uma `MapItem` propriedade que permite ao desenvolvedor fornecer informações de localização que podem ser usadas em outros contextos. Por exemplo, se o aplicativo exibir as revisões de restaurante, o desenvolvedor poderá `MapItem` definir a propriedade como o local do restaurante que o usuário está visualizando no aplicativo. Se o usuário alternar para o aplicativo Maps, o local do restaurante estará automaticamente disponível.

Se o aplicativo oferecer suporte à pesquisa de aplicativo, ele poderá usar os novos componentes `CSSearchableItemAttributesSet` de endereço da classe para especificar os locais que o usuário talvez queira visitar. Ao definir a `MapItem` Propriedade, as outras propriedades são preenchidas automaticamente.

Além de definir `Latitude` o e `Longitude` as propriedades do componente de endereço, é recomendável que o aplicativo forneça as `NamedLocation` propriedades `PhoneNumbers` e também para que Siri possa iniciar uma chamada para o local.

## <a name="contextual-siri-reminders"></a>Lembretes de Siri contextuais

Permite que o usuário use o Siri para fazer um lembrete rapidamente para exibir o conteúdo que ele está exibindo atualmente no aplicativo em uma data posterior. Por exemplo, se eles estivessem exibindo uma revisão de restaurante no aplicativo, poderiam invocar Siri e dizer *"Lembre-me disso quando eu chegar à casa".* Siri geraria o lembrete com um link para a revisão no aplicativo.

## <a name="implementing-proactive-suggestions"></a>Implementando sugestões proativas

Adicionar suporte de sugestão proativa ao aplicativo Xamarin. iOS normalmente é tão fácil quanto implementar algumas APIs ou expandir algumas APIs que o aplicativo já pode estar implementando.

As sugestões proativas funcionam com os aplicativos de três maneiras principais:

- **`NSUserActivity`** -Ajuda o sistema a entender em que informações o usuário está trabalhando no momento na tela.
- **Sugestões de localização** – se o aplicativo oferecer ou consumir informações baseadas na localização, essa extensão de API oferecerá novas maneiras de compartilhar essas informações entre aplicativos.

E tem suporte no aplicativo implementando o seguinte:

- Os **lembretes de Siri contextuais** -no Ios `NSUserActivity` 10, foram expandidos para permitir que o Siri faça um lembrete rapidamente para exibir o conteúdo que ele está exibindo no aplicativo em uma data posterior.
- **Sugestões de localização** -o Ios 10 `NSUserActivity` aprimora os locais de captura exibidos no aplicativo e os promove em vários lugares em todo o sistema.
- **As**  -  solicitações`NSUserActivity` Siri contextuais fornecem o contexto para as informações apresentadas dentro do aplicativo para Siri para que o usuário possa obter direções ou fazer uma chamada que esteja invocando Siri de dentro do aplicativo.

Todos esses recursos têm uma coisa em comum, todos eles usam `NSUserActivity` em uma forma ou em outra para fornecer sua funcionalidade. 

## <a name="nsuseractivity"></a>NSUserActivity

Conforme mencionado acima, `NSUserActivity` o ajuda o sistema a entender em que informações o usuário está trabalhando no momento na tela. `NSUserActivity`é um mecanismo de cache de estado leve para capturar a atividade do usuário à medida que navega pelo aplicativo. Por exemplo, olhando para o aplicativo restaurante:

[![](proactive-suggestions-images/activity02.png "O aplicativo de restaurante")](proactive-suggestions-images/activity02.png#lightbox)

Com as seguintes interações:

1. À medida que o usuário trabalha com o aplicativo `NSUserActivity` , um é criado para recriar o estado do aplicativo mais tarde.
2. Se o usuário procurar um restaurante, o mesmo padrão de criação de atividades será seguido.
3. E novamente, quando o usuário exibe um resultado. Neste último caso, o usuário está exibindo um local e no iOS 10, o sistema está mais ciente de determinados conceitos (como interações de local ou de comunicação).

Dê uma olhada mais atenta na última tela:

[![](proactive-suggestions-images/activity03.png "A carga NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

Aqui, o aplicativo está criando `NSUserActivity` um e foi populado com informações para recriar o estado mais tarde. O aplicativo também incluiu alguns metadados, como o nome e o endereço do local. Com essa atividade criada, o aplicativo permite que o iOS saiba que ele representa o estado atual do usuário.

Em seguida, o aplicativo decide se a atividade será anunciada através do ar para entrega, salva como um valor temporário para sugestões de localização ou adicionada ao índice de destaque no dispositivo para exibição nos resultados da pesquisa.

Para obter mais informações sobre a pesquisa de entrega e destaque, consulte nossa [introdução aos](~/ios/platform/handoff.md) novos guias de APIs de pesquisa de entrega e [Ios 9](~/ios/platform/search/index.md) .

### <a name="creating-an-activity"></a>Criando uma atividade

Antes de criar uma atividade, um identificador de tipo de atividade precisará ser criado para identificá-lo. O identificador de tipo de atividade é uma cadeia de caracteres `NSUserActivityTypes` curta adicionada à matriz do `Info.plist` arquivo do aplicativo usado para identificar exclusivamente um determinado tipo de atividade de usuário. Haverá uma entrada na matriz para cada atividade que o aplicativo suporta e expõe para a pesquisa de aplicativo. Consulte nossa [referência de identificadores de tipo de atividade de criação](~/ios/platform/search/nsuseractivity.md) para obter mais detalhes.

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

Para responder ao usuário tocando em um resultado de pesquisa (`NSUserActivity`) para o aplicativo, edite o arquivo **AppDelegate.cs** e substitua `ContinueUserActivity` o método. Por exemplo:

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

Verifique se esse é o mesmo identificador de tipo`com.xamarin.platform`de atividade () que a atividade criada acima. O aplicativo usa as informações armazenadas no `NSUserActivity` para restaurar o estado de volta para o local em que o usuário parou.

### <a name="benefits-of-creating-an-activity"></a>Benefícios da criação de uma atividade

Com a quantidade mínima de código apresentada acima, o aplicativo agora pode aproveitar três novos recursos do iOS 10:

- **Handoff**
- **Pesquisa de destaque**
- **Lembretes de Siri contextuais**

A seção a seguir configurará a habilitação de dois outros novos recursos do iOS 10:

- **Sugestões de localização**
- **Solicitações Siri contextuais**

### <a name="location-based-suggestions"></a>Sugestões baseadas no local 

Veja o exemplo do aplicativo de pesquisa de restaurante acima. Se ele tiver implementado `NSUserActivity` e preenchido corretamente todos os metadados e atributos, o usuário poderá fazer o seguinte:

1. Encontre um restaurante no aplicativo no qual gostaria de cumprir um amigo.
2. Se o usuário alternar para o aplicativo Maps, o endereço do restaurante será automaticamente Sugerido como destino.
3. Isso mesmo funciona para aplicativos de terceiros (que dão `NSUserActivity`suporte a), portanto, o usuário pode alternar para um aplicativo de compartilhamento de Rides e o endereço do restaurante é automaticamente Sugerido como um destino lá também.
4. Ele também fornece contexto para Siri, para que o usuário possa invocar Siri no aplicativo restaurante e perguntar *"obter direções..."* e Siri fornecerá instruções para o restaurante que o usuário está visualizando.

Toda a funcionalidade acima tem uma coisa em comum, todas elas indicam de onde a sugestão é proveniente. No caso do exemplo acima, é o aplicativo de revisão de restaurante fictício.

o watchOS 3 foi aprimorado para habilitar essa funcionalidade para um aplicativo por meio de várias pequenas modificações e adições a estruturas existentes:

- `NSUserActivity`tem campos adicionais para capturar informações de local que são exibidas dentro do aplicativo.
- Várias adições foram feitas em MapKit e CoreSpotlight para capturar o local.
- A funcionalidade de reconhecimento de local foi adicionada ao Siri, mapas, multitarefa e outros aplicativos no sistema.

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

Se o aplicativo estiver usando MapKit, será tão simples quanto adicionar o mapa `MKMapItem` atual à atividade:

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

Em seguida, a Descrição baseada em texto é necessária para instâncias baseadas em texto (como o teclado do QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

A latitude e a longitude são opcionais, mas certifique-se de que o usuário seja roteado para o local exato para o qual o aplicativo deseja enviá-los:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Ao definir os números de telefone, o aplicativo pode obter acesso ao Siri para que o usuário possa invocar Siri do aplicativo dizendo algo como, * "chamar este local":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por fim, o aplicativo pode indicar se a instância é adequada para navegação e chamadas telefônicas:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```

## <a name="activities-best-practices"></a>Práticas recomendadas de atividades

A Apple sugere as seguintes práticas recomendadas ao trabalhar com atividades:

- Use `NeedsSave` para atualizações de carga lenta.
- Certifique-se de manter uma referência forte para a atividade atual.
- Transfira somente cargas pequenas que incluam apenas informações suficientes para restaurar o estado.
- Certifique-se de que os identificadores de tipo de atividade sejam exclusivos e descritivos usando a notação de DNS reverso para especificá-los. 

## <a name="consuming-location-suggestions"></a>Consumindo sugestões de localização

Esta próxima seção abordará o consumo de sugestões de local provenientes de outras partes do sistema (como o aplicativo Maps) ou outros aplicativos de terceiros.

## <a name="routing-apps-and-locations-suggestions"></a>Sugestões de aplicativos e locais de roteamento

Esta seção configurará as sugestões de localização diretamente de dentro de um aplicativo de roteamento. Para que o aplicativo de roteamento adicione essa funcionalidade, o desenvolvedor usará a `MKDirectionsRequest` estrutura existente da seguinte maneira:

- Para promover o aplicativo em multitarefa.
- Para registrar o aplicativo como um aplicativo de roteamento.
- Para lidar com a inicialização do aplicativo com `MKDirectionsRequest` um objeto MapKit.
- Dê ao watchOS a capacidade de aprender a sugerir o aplicativo com base no envolvimento do usuário.

Quando o aplicativo é iniciado com um objeto `MKDirectionsRequest` MapKit, ele deve começar automaticamente a fornecer instruções ao usuário para o local solicitado, ou apresentar uma interface do usuário que torna mais fácil para o usuário começar a obter direções. Por exemplo:


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

Novo no watchOS 3, o aplicativo pode ser enviado um endereço que não tem coordenadas geográficas, pois isso faz com que o desenvolvedor precise codificar o endereço:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address

});

```

## <a name="summary"></a>Resumo

Este artigo abordou as sugestões proativas e mostrou como o desenvolvedor pode usá-las para direcionar o tráfego para um aplicativo Xamarin. iOS para watchOS. Ele abordou a etapa de implementar sugestões proativas e as diretrizes de uso apresentadas.


## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Guia de programação do SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
