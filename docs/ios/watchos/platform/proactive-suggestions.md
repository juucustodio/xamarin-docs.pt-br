---
title: "Sugestões proativos"
description: "Este artigo mostra como usar pró-ativo sugestões em um aplicativo watchOS 3 para o contrato de unidade, permitindo que o sistema proativamente apresentar informações úteis automaticamente para o usuário."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: ca2476eef120c7d86b939934ec4b286e871d6a78
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="proactive-suggestions"></a>Sugestões proativos

_Este artigo mostra como usar pró-ativo sugestões em um aplicativo watchOS 3 para o contrato de unidade, permitindo que o sistema proativamente apresentar informações úteis automaticamente para o usuário._


Novo para watchOS 3, modos de notícias presentes pró-ativo sugestões para os usuários a se comunicar com aplicativos xamarin por proativamente apresentam informações úteis automaticamente para o usuário em momentos apropriados.


## <a name="about-proactive-suggestions"></a>Sobre sugestões proativos

Novo para watchOS 3, `NSUserActivity` inclui um `MapItem` propriedade que permite que o aplicativo fornecer informações de local que podem ser usadas em outros contextos. Por exemplo, se o hotel aplicativo exibido analisa e fornece um `MapItem` local, se o usuário alternar para o aplicativo de mapas, o local do hotel estava apenas visualizando estaria disponível.

O aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias como `NSUserActivity`, MapKit, reprodutor de mídia e UIKit. Além disso, fornecendo suporte de sugestão pró-ativo para o aplicativo, ele obtém uma integração mais profunda Siri gratuitamente.

## <a name="location-based-suggestions"></a>Sugestões baseadas em local

Novo para watchOS 3, o `NSUserActivity` classe inclui um `MapItem` propriedade que permite ao desenvolvedor fornecer informações de local que podem ser usadas em outros contextos. Por exemplo, se o aplicativo exibe revisões do restaurante, o desenvolvedor pode definir o `MapItem` propriedade para o local do restaurante que o usuário está exibindo no aplicativo. Se o usuário alterna para o aplicativo de mapas, local do restaurante está disponível automaticamente.

Se o aplicativo dá suporte à pesquisa do aplicativo, ele poderá usar os novos componentes de endereço de `CSSearchableItemAttributesSet` classe para especificar locais que o usuário pode desejar visitar. Definindo o `MapItem` propriedade, as outras propriedades são preenchidos automaticamente.

Além de configuração de `Latitude` e `Longitude` de propriedades do componente de endereço, é recomendável que o aplicativo fornecer a `NamedLocation` e `PhoneNumbers` propriedades muito, portanto Siri pode iniciar uma chamada para o local.

## <a name="contextual-siri-reminders"></a>Lembretes Siri contextuais

Permite que o usuário usar Siri para criar rapidamente um lembrete para exibir o conteúdo que estão exibindo no momento no aplicativo em uma data posterior. Por exemplo, se eles foram exibindo um restaurante no aplicativo, eles poderiam invocar Siri e dizer *"Lembrar-me sobre isso quando recebo home."* Siri geraria o lembrete com um link para a revisão no aplicativo.

## <a name="implementing-proactive-suggestions"></a>Implementar sugestões proativos

Suporte para o aplicativo xamarin adicionando pró-ativo sugestão é normalmente mais fácil implementar algumas APIs ou expandindo algumas APIs que o aplicativo já pode estar implementando.

Sugestões pró-ativo funcionam com os aplicativos de três maneiras principais:

- **`NSUserActivity`** -Ajuda o sistema entender quais informações o usuário estiver atualmente trabalhando na tela.
- **Sugestões de local** - se o aplicativo oferece ou consome informações de localização com base, essas API extensão oferecem novas maneiras de compartilhar informações entre aplicativos.

E tem suporte no aplicativo Implementando o seguinte:

- **Contextuais lembretes de Siri** - no iOS 10, `NSUserActivity` foi expandida para permitir que o Siri criar rapidamente um lembrete para exibir o conteúdo que estão exibindo no momento no aplicativo em uma data posterior.
- **Sugestões de local** -iOS 10 aprimora `NSUserActivity` capturar locais exibidos dentro do aplicativo e promovê-los em vários locais em todo o sistema.
- **Solicitações de Siri contextuais**  -  `NSUserActivity` fornece um contexto para as informações apresentadas dentro do aplicativo Siri para que o usuário pode receber instruções ou local de uma chamada ser Siri chamada de dentro do aplicativo.

Todos esses recursos têm algo em comum, todos eles usarão `NSUserActivity` de uma forma ou de outra para fornecer sua funcionalidade. 

## <a name="nsuseractivity"></a>NSUserActivity

Como mencionado acima, `NSUserActivity` ajuda o sistema a entender as informações que o usuário estiver atualmente trabalhando na tela. `NSUserActivity` é um estado leve mecanismo para capturar a atividade do usuário, como eles navegam por meio do aplicativo de cache. Por exemplo, olhando para o aplicativo do restaurante:

[ ![](proactive-suggestions-images/activity02.png "O aplicativo do restaurante")](proactive-suggestions-images/activity02.png)

Com as interações do seguintes:

1. Como o usuário trabalha com o aplicativo, um `NSUserActivity` é criado para recriar o estado do aplicativo mais tarde.
2. Se o usuário procura um restaurante, o mesmo padrão de criação de atividades é seguido.
3. E, novamente, quando o usuário exibe um resultado. Neste último caso, o usuário está exibindo um local e no iOS 10, o sistema mais reconhece determinados conceitos (como interações local ou a comunicação).

Analisar mais detalhadamente na última tela:

[ ![](proactive-suggestions-images/activity03.png "A carga de NSUserActivity")](proactive-suggestions-images/activity03.png)

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

Certifique-se de que este é o mesmo identificador de tipo de atividade (`com.xamarin.platform`) como a atividade criada acima. O aplicativo usa as informações armazenadas no `NSUserActivity` para restaurar o estado em que o usuário parou.

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
4. Se o usuário alterna para o aplicativo de mapas, endereço do restaurante automaticamente será sugerido como um destino.
5. Isso funciona até mesmo para aplicativos de terceiros 3º (que oferecem suporte a `NSUserActivity`), portanto, o usuário pode alternar para um aplicativo de compartilhamento jornada e endereço do restaurante é automaticamente sugerido como um destino lá também.
6. Ele também fornece contexto para Siri, para que o usuário pode invocar Siri dentro do aplicativo do restaurante e peça *"Obter direções …"* Siri serão fornecidas instruções para o restaurante que o usuário está exibindo.

Toda a funcionalidade acima tem algo em comum, todos eles indicam onde a sugestão é originalmente vindo. No caso do exemplo acima, é o aplicativo de análise do restaurante fictícios.

watchOS 3 foi aprimorada para habilitar essa funcionalidade para um aplicativo por meio de várias pequenas modificações e adições para as estruturas existentes:

- `NSUserActivity` tem campos adicionais para capturar informações sobre o local que é exibido dentro do aplicativo.
- Foram feitas várias adições MapKit e CoreSpotlight para capturar o local.
- Funcionalidade de reconhecimento de local foi adicionada para Siri, mapas, multitarefa e outros aplicativos no sistema.

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

Em seguida, o texto obrigatório a descrição com base em texto com base em instâncias (por exemplo, o teclado QuickType):

```csharp
attributes.SubThoroughfare = "1";
attributes.Thoroughfare = "Infinite Loop";
attributes.City = "Cupertino";
attributes.StateOrProvince = "CA";
attributes.Country = "United States";
```

A latitude e longitude são opcionais, mas certifique-se de que o usuário será roteado para o local exato que é desejarem enviá-los para o aplicativo:

```csharp
attributes.Latitude = 37.33072;
attributes.Longitude = 122.029674;
```

Definindo os números de telefone, o aplicativo pode obter acesso a Siri para que o usuário pode invocar Siri do aplicativo dizendo algo como * "Chamar este local":

```csharp
attributes.PhoneNumbers = new string[]{"(800) 275-2273"};
```

Por fim, o aplicativo pode indicar se a instância é adequada para navegação e chamadas telefônicas:

```csharp
attributes.SupportsPhoneCalls = true;
attributes.SupportsNavigation = true;
```
## <a name="activities-best-practices"></a>Práticas recomendadas de atividades

Apple sugere práticas recomendadas a seguir ao trabalhar com atividades:

- Use `NeedsSave` para atualizações de carga lento.
- Certifique-se de manter uma referência forte para a atividade atual.
- Transferir apenas pequenas cargas que incluem informações suficientes para restaurar o estado.
- Verifique se os identificadores de tipo de atividade estão exclusivo e descritivo, usando a notação inversa de DNS para especificá-los. 

## <a name="consuming-location-suggestions"></a>Consumindo sugestões de local

Esta seção abordará consumindo sugestão de local que outras partes do sistema (como o aplicativo de mapas) ou outros aplicativos de terceiros 3º.

## <a name="routing-apps-and-locations-suggestions"></a>Aplicativos de roteamentos e sugestões de locais

Esta seção será dar uma olhada no consumo de sugestões de local diretamente de dentro de um aplicativo de roteamento. Para o aplicativo de roteamento adicionar essa funcionalidade, o desenvolvedor aproveitará existente `MKDirectionsRequest` framework da seguinte maneira:

- Para promover o aplicativo no multitarefa.
- Para registrar o aplicativo como um aplicativo de roteamento.
- Para tratar a iniciar o aplicativo com um MapKit `MKDirectionsRequest` objeto.
- Dê watchOS a capacidade de saber para sugerir o aplicativo com base no contrato de usuário.

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

Novo no watchOS 3, o aplicativo pode ser enviado um endereço que não tenha coordenadas geograficamente, que fazem com que o desenvolvedor precisa codificar o endereço:

```csharp
var geocoder = new CLGeocoder();
geocoder.GeocodeAddress(address, (place, err)=> {
    // Handle the display of the address
    
});

```

## <a name="summary"></a>Resumo

Este artigo tem coberto sugestões pró-ativo e mostrou como o desenvolvedor pode usá-los para direcionar o tráfego para um aplicativo xamarin para watchOS. Ele abordado na etapa para implementar as sugestões pró-ativo e apresentadas diretrizes de uso.


## <a name="related-links"></a>Links relacionados

- [Exemplos de watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
