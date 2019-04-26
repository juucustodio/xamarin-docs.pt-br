---
title: Sugestões proativas no Xamarin do watchOS
description: Este artigo mostra como usar sugestões proativas em um aplicativo do watchOS 3 para estimular a participação, permitindo que o sistema de forma proativa apresentam informações úteis automaticamente para o usuário.
ms.prod: xamarin
ms.assetid: 10CC9F16-963C-44F1-8B98-F09FB2310DFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 979b103db478e3888d3a3c20df6afbd91d0c37d8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386509"
---
# <a name="watchos-proactive-suggestions-in-xamarin"></a>Sugestões proativas no Xamarin do watchOS

_Este artigo mostra como usar sugestões proativas em um aplicativo do watchOS 3 para estimular a participação, permitindo que o sistema de forma proativa apresentam informações úteis automaticamente para o usuário._


Novo no watchOS 3, maneiras de notícias presentes sugestões proativas para os usuários a se envolver com um aplicativo xamarin. IOS por proativamente apresentam informações úteis automaticamente para o usuário em momentos apropriados.


## <a name="about-proactive-suggestions"></a>Sobre sugestões proativas

Novo no watchOS 3, `NSUserActivity` inclui um `MapItem` propriedade que permite que o aplicativo fornecer informações de local que podem ser usadas em outros contextos. Por exemplo, se o hotel aplicativo exibido revisões e fornece um `MapItem` local, se o usuário é alternado para o aplicativo de mapas, o local do hotel estava apenas visualizando estaria disponível.

O aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias, como `NSUserActivity`, MapKit, Media Player e UIKit. Além disso, fornecendo suporte proativo sugestão para o aplicativo, ele obtém uma integração mais profunda Siri gratuitamente.

## <a name="location-based-suggestions"></a>Sugestões baseados na localização

Novo no watchOS 3, o `NSUserActivity` classe inclui um `MapItem` propriedade que permite ao desenvolvedor fornecer informações de local que podem ser usadas em outros contextos. Por exemplo, se o aplicativo exibe resenhas de restaurantes, o desenvolvedor pode definir o `MapItem` propriedade para o local do restaurante que o usuário está exibindo no aplicativo. Se o usuário alterna para o aplicativo de mapas, local do restaurante está automaticamente disponível.

Se o aplicativo dá suporte à pesquisa de aplicativo, ele poderá usar os novos componentes de endereço do `CSSearchableItemAttributesSet` classe para especificar locais que o usuário pode querer visitar. Definindo o `MapItem` propriedade, as outras propriedades são preenchidos automaticamente.

Além de configuração de `Latitude` e `Longitude` das propriedades do componente de endereço, é recomendável que o aplicativo forneça o `NamedLocation` e `PhoneNumbers` propriedades também, portanto, Siri pode iniciar uma chamada para o local.

## <a name="contextual-siri-reminders"></a>Siri contextuais lembretes

Permite ao usuário usar Siri para fazer rapidamente um lembrete para exibir o conteúdo que está sendo exibido no aplicativo em uma data posterior. Por exemplo, se eles foram exibindo uma revisão de um restaurante no aplicativo, eles poderiam invocar Siri e dizer *"Lembrar-me sobre isso quando eu chegar em casa."* Siri geraria o lembrete com um link para a revisão no aplicativo.

## <a name="implementing-proactive-suggestions"></a>Implementação de sugestões proativas

Suporte para o aplicativo xamarin. IOS adicionando sugestão proativo é geralmente tão fácil quanto implementar algumas APIs ou expandir em algumas APIs que o aplicativo já pode estar implementando.

Sugestões proativas funcionam com os aplicativos de três maneiras principais:

- **`NSUserActivity`** -Ajuda o sistema a entender quais informações o usuário está atualmente trabalhando na tela.
- **Sugestões de local** - se o aplicativo oferece ou consome informações baseadas na localização, essas API extensão oferta novas maneiras de compartilhar essas informações entre aplicativos.

E é compatível com o aplicativo com a implementação a seguir:

- **Lembretes de Siri contextuais** - no iOS 10, `NSUserActivity` foi expandido para permitir que o Siri fazer rapidamente um lembrete para exibir o conteúdo que está sendo exibido no aplicativo em uma data posterior.
- **Sugestões de local** -iOS 10 aprimora `NSUserActivity` capturar locais exibidos dentro do aplicativo e promovê-los em vários locais em todo o sistema.
- **Solicitações do Siri contextuais**  -  `NSUserActivity` fornece um contexto para as informações apresentadas dentro do aplicativo Siri para que o usuário possa obter direções ou local de uma chamada ser Siri invocando dentro do aplicativo.

Todos esses recursos têm uma coisa em comum, todos eles usam `NSUserActivity` em um formato ou outro para fornecer sua funcionalidade. 

## <a name="nsuseractivity"></a>NSUserActivity

Conforme mencionado acima, `NSUserActivity` ajuda o sistema a entender quais informações o usuário está atualmente trabalhando na tela. `NSUserActivity` é um estado leve mecanismo para capturar a atividade do usuário enquanto navega por meio do aplicativo de cache. Por exemplo, verificando o aplicativo do restaurante:

[![](proactive-suggestions-images/activity02.png "O aplicativo de restaurante")](proactive-suggestions-images/activity02.png#lightbox)

Com as interações do seguintes:

1. Conforme o usuário trabalha com o aplicativo, um `NSUserActivity` é criado para recriar o estado do aplicativo mais tarde.
2. Se o usuário procura um restaurante, o mesmo padrão de criação de atividades é seguido.
3. E, novamente, quando o usuário exibe um resultado. Nesse último caso, o usuário está exibindo um local e no iOS 10, o sistema é mais ciente das determinados conceitos (como interações local ou de comunicação).

Dê uma olhada mais próxima na última tela:

[![](proactive-suggestions-images/activity03.png "A carga NSUserActivity")](proactive-suggestions-images/activity03.png#lightbox)

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

Certifique-se de que isso é o mesmo identificador de tipo de atividade (`com.xamarin.platform`) como a atividade criada acima. O aplicativo usa as informações armazenadas em do `NSUserActivity` para restaurar o estado para onde o usuário parou.

### <a name="benefits-of-creating-an-activity"></a>Benefícios da criação de uma atividade

Com a quantidade mínima de código apresentado acima, o aplicativo agora é possível tirar proveito dos três novos recursos do iOS 10:

- **Handoff**
- **Pesquisa de destaque**
- **Siri contextuais lembretes**

A seção a seguir será dar uma olhada habilitando dois outros novos recursos do iOS 10:

- **Sugestões de local**
- **Solicitações do Siri contextuais**

### <a name="location-based-suggestions"></a>Sugestões baseados na localização 

Veja o exemplo de aplicativo de pesquisa o restaurante acima. Se ele tiver implementado `NSUserActivity` e preenchida corretamente todos os metadados e atributos, o usuário poderia fazer o seguinte:

1. Encontre um restaurante no aplicativo que desejarem para atender a um amigo da.
4. Se o usuário alterna para o aplicativo de mapas, o endereço do restaurante automaticamente será sugerido como um destino.
5. Isso funciona até mesmo para aplicativos de terceiros 3ª (que dão suporte a `NSUserActivity`), portanto, o usuário pode alternar para um aplicativo de compartilhamento e o endereço do restaurante é sugerido automaticamente como um destino lá também.
6. Ela também fornece contexto para Siri, portanto, o usuário pode invocar Siri no aplicativo do restaurante e pergunte *"Obter direções..."* e Siri fornecerá instruções para o restaurante que o usuário está exibindo.

Toda a funcionalidade acima tem uma coisa em comum, todos eles indicam onde a sugestão é originalmente provenientes. No caso do exemplo acima, é o aplicativo de análise de restaurante fictícia.

watchOS 3 foi aprimorado para habilitar essa funcionalidade para um aplicativo por meio de várias pequenas modificações e adições para as estruturas existentes:

- `NSUserActivity` tem campos adicionais para capturar informações de local que são exibidas dentro do aplicativo.
- Foram feitas várias adições MapKit e CoreSpotlight para capturar o local.
- Funcionalidade de reconhecimento de local foi adicionada para Siri, mapas, multitarefa e outros aplicativos no sistema.

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

Em seguida, o texto de descrição com base em necessária para o texto com base em instâncias (por exemplo, o teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

A latitude e longitude são opcionais, mas certifique-se de que o usuário seja roteado para o local exato que o aplicativo é quando você deseja enviá-las para:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Definindo os números de telefone, o aplicativo pode obter acesso a Siri para que o usuário pode invocar Siri do aplicativo dizendo algo como * "Telefonar para este local":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por fim, o aplicativo pode indicar se a instância é adequada para chamadas telefônicas e de navegação:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>Práticas recomendadas de atividades

A Apple sugere as seguintes práticas recomendadas ao trabalhar com atividades:

- Use `NeedsSave` para atualizações de carga lenta.
- Certifique-se de manter uma referência forte para a atividade atual.
- Transferir apenas pequenas cargas que incluem informações suficientes para restaurar o estado.
- Verifique se os identificadores de tipo de atividade exclusivo e descritivo, usando a notação de DNS reverso para especificá-los. 

## <a name="consuming-location-suggestions"></a>Consumindo sugestões de local

Esta seção abordará consumindo sugestão local oriundos de outras partes do sistema (por exemplo, o aplicativo de mapas) ou outros aplicativos de terceiros 3ª.

## <a name="routing-apps-and-locations-suggestions"></a>Aplicativos de roteamentos e sugestões de locais

Esta seção será dar uma olhada no consumo de sugestões de local diretamente de dentro de um aplicativo de roteamento. Para o aplicativo de roteamento adicionar essa funcionalidade, o desenvolvedor aproveitará existente `MKDirectionsRequest` framework da seguinte maneira:

- Para promover o aplicativo em execução multitarefa.
- Para registrar o aplicativo como um aplicativo de roteamento.
- Para tratar a iniciar o aplicativo com um MapKit `MKDirectionsRequest` objeto.
- Oferecem a capacidade de aprender sugerir o aplicativo com base no contrato de usuário de watchOS.

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

Novo no watchOS 3, o aplicativo pode ser enviado um endereço que não tenha coordenadas geográficas, que fazem com que o desenvolvedor precisa codificar o endereço:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>Resumo

Este artigo foi coberto sugestões proativas e mostrou como o desenvolvedor pode usá-las para direcionar o tráfego para um aplicativo xamarin. IOS para watchOS. Ele abordado na etapa para implementar as sugestões proativas e apresentadas diretrizes de uso.


## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
