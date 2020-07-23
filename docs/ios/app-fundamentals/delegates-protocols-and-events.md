---
title: Eventos, protocolos e delegados no Xamarin. iOS
description: Este documento descreve como trabalhar com eventos, protocolos e delegados no Xamarin. iOS. Esses conceitos fundamentais são onipresentes no desenvolvimento do Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/17/2017
ms.openlocfilehash: 1a7d7ec017bb226efb05014dc7ac80160aeaae48
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938319"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Eventos, protocolos e delegados no Xamarin. iOS

O Xamarin. iOS usa controles para expor eventos para a maioria das interações do usuário.
Os aplicativos Xamarin. iOS consomem esses eventos praticamente da mesma forma que os aplicativos .NET tradicionais. Por exemplo, a classe UIButton Xamarin. iOS tem um evento chamado TouchUpInside e consome esse evento da mesma forma que se essa classe e evento estivesse em um aplicativo .NET.

Além dessa abordagem do .NET, o Xamarin. iOS expõe outro modelo que pode ser usado para interação mais complexa e Associação de dados. Essa metodologia usa o que a Apple chama de delegados e protocolos. Delegados são semelhantes em conceito a delegados em C#, mas em vez de definir e chamar um único método, um delegado em Objective-C é uma classe inteira que está em conformidade com um protocolo. Um protocolo é semelhante a uma interface em C#, exceto pelo fato de que seus métodos podem ser opcionais. Por exemplo, para popular um UITableView com dados, você criaria uma classe delegada que implementa os métodos definidos no protocolo UITableViewDataSource que o UITableView chamaria para popular a si mesmo.

Neste artigo, você aprenderá sobre todos esses tópicos, oferecendo uma base sólida para lidar com cenários de retorno de chamada no Xamarin. iOS, incluindo:

- **Eventos** – usando eventos .NET com controles UIKit.
- **Protocolos** – aprendendo quais são os protocolos e como eles são usados e criando um exemplo que fornece dados para uma anotação de mapa.
- **Delegados** – aprendendo sobre delegados do Objective-C estendendo o exemplo de mapa para lidar com a interação do usuário que inclui uma anotação, aprendendo a diferença entre delegados fortes e fracos e quando usar cada um deles.

Para ilustrar protocolos e delegados, criaremos um aplicativo de mapa simples que adiciona uma anotação a um mapa, conforme mostrado aqui:

[ ![ Um exemplo de um aplicativo de mapa simples que adiciona uma anotação a um mapa](delegates-protocols-and-events-images/01-map-sml.png)](delegates-protocols-and-events-images/01-map.png#lightbox) 
 [ ![ uma anotação de exemplo adicionada a um mapa](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png)](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Antes de lidar com esse aplicativo, vamos começar examinando eventos .NET sob o UIKit.

## <a name="net-events-with-uikit"></a>Eventos .NET com UIKit

O Xamarin. iOS expõe eventos .NET em controles UIKit. Por exemplo, UIButton tem um evento TouchUpInside, que você manipula normalmente no .NET, conforme mostrado no código a seguir que usa uma expressão lambda em C#:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

Você também pode implementar isso com um método anônimo em estilo C# 2,0 como este:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

O código anterior é conectado no `ViewDidLoad` método de UIViewController. A `aButton` variável faz referência a um botão, que pode ser adicionado no designer do Ios ou com código. A figura a seguir mostra um botão que foi adicionado no designer do iOS:

[![Um botão adicionado ao designer do iOS](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png)](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

O Xamarin. iOS também dá suporte ao estilo de ação de destino para conectar seu código a uma interação que ocorre com um controle. Para criar uma ação de destino para o botão **Olá** , clique duas vezes nele no designer do Ios. O arquivo code-behind do UIViewController será exibido e será solicitado que o desenvolvedor selecione um local para inserir o método de conexão:

[![O arquivo code-behind UIViewControllers](delegates-protocols-and-events-images/03-interface-builder-action-sml.png)](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Depois que um local é selecionado, um novo método é criado e conectado ao controle. No exemplo a seguir, uma mensagem será gravada no console quando o botão for clicado:

[![Uma mensagem será gravada no console quando o botão for clicado](delegates-protocols-and-events-images/05-interface-builder-action-sml.png)](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Para obter mais detalhes sobre o padrão de ação de destino do iOS, consulte a seção Target-Action das [competências do aplicativo principal para IOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) na biblioteca do desenvolvedor do IOS da Apple.

Para obter mais informações sobre como usar o designer do iOS com Xamarin. iOS, consulte a documentação de [visão geral do designer do IOS](~/ios/user-interface/designer/index.md) .

## <a name="events"></a>Eventos

Se você quiser interceptar eventos de UIControl, terá uma variedade de opções: de usar as lambdas C# e delegar funções para usar as APIs Objective-C de baixo nível.

A seção a seguir mostra como você capturaria o evento TouchDown em um botão, dependendo de quanto controle você precisa.

## <a name="c-style"></a>Estilo C#

Usando a sintaxe de delegado:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Se você gostar de lambdas, em vez disso:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Se você quiser que vários botões usem o mesmo manipulador para compartilhar o mesmo código:

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

## <a name="monitoring-more-than-one-kind-of-event"></a>Monitorando mais de um tipo de evento

Os eventos do C# para sinalizadores UIControlEvent têm um mapeamento de um para um para sinalizadores individuais. Quando você quiser que a mesma parte do código manipule dois ou mais eventos, use o `UIControl.AddTarget` método:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Usando a sintaxe lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Se você precisar usar recursos de baixo nível do Objective-C, como conectar-se a uma determinada instância de objeto e invocar um seletor específico:

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Observe que, se você implementar o método de instância em uma classe base herdada, ela deverá ser um método público.

## <a name="protocols"></a>Protocolos

Um protocolo é um recurso de linguagem Objective-C que fornece uma lista de declarações de método. Ele atende a uma finalidade semelhante a uma interface em C#, sendo a principal diferença de que um protocolo pode ter métodos opcionais. Os métodos opcionais não serão chamados se a classe que adotar um protocolo não os implementar. Além disso, uma única classe no Objective-C pode implementar vários protocolos, assim como uma classe C# pode implementar várias interfaces.

A Apple usa protocolos em todo o iOS para definir contratos para classes a adotar, ao mesmo tempo em que abstrai a classe de implementação do chamador, operando assim como uma interface C#. Os protocolos são usados em cenários não delegados (como com o `MKAnnotation` exemplo mostrado a seguir) e com delegados (conforme apresentado posteriormente neste documento, na seção de delegados).

### <a name="protocols-with-xamarinios"></a>Protocolos com Xamarin. Ios

Vamos dar uma olhada em um exemplo usando um protocolo Objective-C do Xamarin. iOS. Para este exemplo, usaremos o `MKAnnotation` protocolo, que faz parte da `MapKit` estrutura. `MKAnnotation`é um protocolo que permite que qualquer objeto que o adote para fornecer informações sobre uma anotação que pode ser adicionada a um mapa. Por exemplo, um objeto que implementa `MKAnnotation` fornece o local da anotação e o título associado a ele.

Dessa forma, o `MKAnnotation` protocolo é usado para fornecer dados pertinentes que acompanham uma anotação. A exibição real da anotação em si é criada a partir dos dados no objeto que adota o `MKAnnotation` protocolo. Por exemplo, o texto do texto explicativo que aparece quando o usuário toca na anotação (conforme mostrado na captura de tela abaixo) vem da `Title` Propriedade na classe que implementa o protocolo:

 [![Texto de exemplo para o texto explicativo quando o usuário toca na anotação](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png)](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Conforme descrito na próxima seção, os [protocolos](#protocols-deep-dive)aprofundam-se, o Xamarin. Ios associa os protocolos a classes abstratas. Para o `MKAnnotation` protocolo, a classe C# associada é nomeada `MKAnnotation` para imitar o nome do protocolo e é uma subclasse de `NSObject` , a classe base raiz para Cocoatouch. O protocolo requer que um getter e setter sejam implementados para a coordenada; no entanto, um título e subtítulo são opcionais. Portanto, na `MKAnnotation` classe, a `Coordinate` propriedade é *abstrata*, exigindo que ela seja implementada e as `Title` `Subtitle` Propriedades e são marcadas como *virtuais*, tornando-as opcionais, conforme mostrado abaixo:

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

Qualquer classe pode fornecer dados de anotação simplesmente derivando de `MKAnnotation` , desde que pelo menos a `Coordinate` propriedade seja implementada. Por exemplo, aqui está uma classe de exemplo que usa a coordenada no construtor e retorna uma cadeia de caracteres para o título:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

Por meio do protocolo ao qual ele está associado, qualquer classe que as subclasses `MKAnnotation` possa fornecer dados relevantes que serão usados pelo mapa quando ele criar a exibição da anotação. Para adicionar uma anotação a um mapa, basta chamar o `AddAnnotation` método de uma `MKMapView` instância, conforme mostrado no código a seguir:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

A variável MAP aqui é uma instância de um `MKMapView` , que é a classe que representa o próprio mapa. O `MKMapView` usará os `Coordinate` dados derivados da `SampleMapAnnotation` instância para posicionar o modo de exibição de anotação no mapa.

O `MKAnnotation` protocolo fornece um conjunto conhecido de recursos em todos os objetos que o implementam, sem o consumidor (o mapa, nesse caso) que precisam saber sobre os detalhes da implementação. Isso simplifica a adição de uma variedade de anotações possíveis a um mapa.

### <a name="protocols-deep-dive"></a>Aprofundamento dos protocolos

Como as interfaces C# não dão suporte a métodos opcionais, o Xamarin. iOS mapeia os protocolos para classes abstratas. Portanto, a adoção de um protocolo em Objective-C é realizada no Xamarin. iOS derivando da classe abstrata que está associada ao protocolo e implementando os métodos necessários. Esses métodos serão expostos como métodos abstratos na classe. Os métodos opcionais do protocolo serão associados aos métodos virtuais da classe C#.

Por exemplo, aqui está uma parte do `UITableViewDataSource` protocolo como associado no Xamarin. Ios:

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

Observe que a classe é abstrata. O Xamarin. iOS torna a classe abstrata para dar suporte a métodos opcionais/obrigatórios em protocolos.
No entanto, diferentemente dos protocolos Objective-C, (ou interfaces C#), as classes C# não oferecem suporte a várias heranças. Isso afeta o design do código C# que usa protocolos e geralmente leva a classes aninhadas. Mais informações sobre esse problema serão abordadas posteriormente neste documento, na seção delegados.

 `GetCell(…)`é um método abstrato, associado ao *seletor*de Objective-C, `tableView:cellForRowAtIndexPath:` , que é um método necessário do `UITableViewDataSource` protocolo. Seletor é o termo Objective-C para o nome do método. Para impor o método conforme necessário, o Xamarin. iOS o declara como abstrato. O outro método, `NumberOfSections(…)` , está associado a `numberOfSectionsInTableview:` . Esse método é opcional no protocolo, portanto, Xamarin. iOS o declara como virtual, tornando-o opcional para substituir em C#.

O Xamarin. iOS cuida de toda a associação do iOS para você. No entanto, se você precisar associar um protocolo de Objective-C manualmente, poderá fazer isso decorando uma classe com o `ExportAttribute` . Esse é o mesmo método usado pelo Xamarin. iOS em si.

Para obter mais informações sobre como associar tipos Objective-C no Xamarin. iOS, consulte o artigo [associando os tipos Objective-c](~/ios/platform/binding-objective-c/index.md).

No entanto, ainda não estamos com protocolos. Eles também são usados no iOS como base para delegados de Objective-C, que é o tópico da próxima seção.

## <a name="delegates"></a>Delegados

o iOS usa delegados Objective-C para implementar o padrão de delegação, no qual um objeto passa a ser usado para outro. O objeto que faz o trabalho é o delegado do primeiro objeto. Um objeto informa seu delegado para fazer o trabalho enviando mensagens de ti depois que determinadas coisas acontecem. Enviar uma mensagem como essa em Objective-C é funcionalmente equivalente a chamar um método em C#. Um delegado implementa métodos em resposta a essas chamadas e, portanto, fornece funcionalidade ao aplicativo.

Os delegados permitem que você estenda o comportamento de classes sem a necessidade de criar subclasses. Os aplicativos no iOS geralmente usam delegados quando uma classe retorna para outra após a ocorrência de uma ação importante. Por exemplo, a `MKMapView` classe retorna ao seu delegado quando o usuário toca em uma anotação em um mapa, dando ao autor da classe de representante a oportunidade de responder dentro do aplicativo. Você pode trabalhar com um exemplo desse tipo de uso delegado mais adiante neste artigo, em exemplo, usando um delegado com Xamarin. iOS.

Neste ponto, você deve estar se perguntando como uma classe determina quais métodos devem ser chamados em seu delegado. Esse é outro lugar em que você usa protocolos. Normalmente, os métodos disponíveis para um delegado são provenientes dos protocolos que eles adotam.

### <a name="how-protocols-are-used-with-delegates"></a>Como os protocolos são usados com delegados

Vimos anteriormente como os protocolos são usados para dar suporte à adição de anotações a um mapa.
Os protocolos também são usados para fornecer um conjunto conhecido de métodos para classes chamar depois que determinados eventos ocorrem, como depois que o usuário toca uma anotação em um mapa ou seleciona uma célula em uma tabela. As classes que implementam esses métodos são conhecidas como os delegados das classes que as chamam.

As classes que dão suporte à delegação fazem isso expondo uma propriedade delegate, à qual uma classe que implementa o delegado é atribuída. Os métodos que você implementar para o delegado dependerão do protocolo que o delegado específico adotar. Para o `UITableView` método, você implementa o `UITableViewDelegate` protocolo, para o `UIAccelerometer` método, você implementaria `UIAccelerometerDelegate` e assim por diante para todas as outras classes em todos os Ios para os quais você desejaria expor um delegado.

A `MKMapView` classe que vimos em nosso exemplo anterior também tem uma propriedade chamada delegate, que será chamada depois que vários eventos ocorrerem. O delegado para `MKMapView` é do tipo `MKMapViewDelegate` .
Você usará isso em breve em um exemplo para responder à anotação depois que ela for selecionada, mas primeiro vamos discutir a diferença entre delegados fortes e fracos.

### <a name="strong-delegates-vs-weak-delegates"></a>Delegados fortes versus delegados fracos

Os delegados que vimos até agora são delegados fortes, o que significa que eles são fortemente tipados. As associações do Xamarin. iOS são fornecidas com uma classe fortemente tipada para cada protocolo delegado no iOS. No entanto, o iOS também tem o conceito de um delegado fraco. Em vez de subclasser uma classe associada ao protocolo Objective-C para um determinado delegado, o iOS também permite que você opte por associar os métodos de protocolo a você mesmo em qualquer classe que você desejar que derive de NSObject, decorar seus métodos com o ExportAttribute e, em seguida, fornecendo os seletores apropriados.
Ao usar essa abordagem, você atribui uma instância da classe à propriedade WeakDelegate em vez de à propriedade delegar. Um delegado fraco oferece a você a flexibilidade de levar sua classe de representante para uma hierarquia de herança diferente. Vejamos um exemplo do Xamarin. iOS que usa delegados fortes e fracos.

### <a name="example-using-a-delegate-with-xamarinios"></a>Exemplo usando um delegado com Xamarin. iOS

Para executar o código em resposta ao usuário tocando na anotação em nosso exemplo, podemos subclassear `MKMapViewDelegate` e atribuir uma instância à `MKMapView` Propriedade do `Delegate` . O `MKMapViewDelegate` protocolo contém apenas métodos opcionais.
Portanto, todos os métodos são virtuais associados a esse protocolo na classe Xamarin. iOS `MKMapViewDelegate` . Quando o usuário selecionar uma anotação, a `MKMapView` instância enviará a `mapView:didSelectAnnotationView:` mensagem para seu delegado. Para lidar com isso no Xamarin. iOS, precisamos substituir o `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` método na subclasse MKMapViewDelegate desta forma:

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

A classe SampleMapDelegate mostrada acima é implementada como uma classe aninhada no controlador que contém a `MKMapView` instância. Em Objective-C, muitas vezes você verá que o controlador adota vários protocolos diretamente dentro da classe. No entanto, como os protocolos são associados a classes no Xamarin. iOS, as classes que implementam delegados com rigidez de tipos geralmente são incluídas como classes aninhadas.

Com a implementação da classe delegada em vigor, você só precisa instanciar uma instância do delegado no controlador e atribuí-la à `MKMapView` `Delegate` Propriedade do, como mostrado aqui:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

Para usar um delegado fraco para realizar a mesma coisa, você precisa associar o método a si mesmo em qualquer classe derivada de `NSObject` e atribuí-lo à `WeakDelegate` Propriedade do `MKMapView` . Como a `UIViewController` classe basicamente deriva de `NSObject` (como todas as classes Objective-C em Cocoatouch), podemos simplesmente implementar um método ligado `mapView:didSelectAnnotationView:` diretamente no controlador e atribuir o controlador a `MKMapView` `WeakDelegate` , evitando a necessidade da classe aninhada extra. O código a seguir demonstra essa abordagem:

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

Ao executar esse código, o aplicativo se comporta exatamente como fazia ao executar a versão do delegado com rigidez de tipos. O benefício desse código é que o delegado fraco não requer a criação da classe extra que foi criada quando usamos o delegado fortemente tipado. No entanto, isso é fornecido com a despesa de segurança de tipo. Se você cometer um erro no seletor que foi passado para o, não `ExportAttribute` descobrirá até o tempo de execução.

### <a name="events-and-delegates"></a>Eventos e delegados

Os delegados são usados para retornos de chamada no iOS da mesma forma que o .NET usa eventos. Para fazer com que as APIs do iOS e a maneira como usam os delegados Objective-C pareçam com o .NET, o Xamarin. iOS expõe eventos .NET em muitos locais em que os delegados são usados no iOS.

Por exemplo, a implementação anterior em que o `MKMapViewDelegate` respondido a uma anotação selecionada também poderia ser implementada no Xamarin. Ios usando um evento .net. Nesse caso, o evento seria definido em `MKMapView` e chamado `DidSelectAnnotationView` . Ele teria uma `EventArgs` subclasse de Type `MKMapViewAnnotationEventsArgs` . A `View` propriedade de `MKMapViewAnnotationEventsArgs` forneceria uma referência à exibição de anotação, na qual você poderia prosseguir com a mesma implementação que tinha anteriormente, conforme ilustrado aqui:

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

## <a name="summary"></a>Resumo

Este artigo abordou como usar eventos, protocolos e delegados no Xamarin. iOS. Vimos como o Xamarin. iOS expõe eventos normais de estilo do .NET para controles.
Em seguida, aprendemos sobre os protocolos Objective-C, incluindo como eles são diferentes das interfaces C# e como o Xamarin. iOS os usa. Por fim, examinamos delegados Objective-C de uma perspectiva do Xamarin. iOS. Vimos como o Xamarin. iOS dá suporte a delegados de tipo forte e fraco, e como associar eventos do .NET a métodos delegados.

## <a name="related-links"></a>Links Relacionados

- [Protocolos, delegados e eventos (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/protocols-delegates-events)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Ligando tipos de Objective-C](~/ios/platform/binding-objective-c/index.md)
- [A linguagem de programação Objective-C](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Criando interfaces do usuário no Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competências de aplicativos principais para iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
