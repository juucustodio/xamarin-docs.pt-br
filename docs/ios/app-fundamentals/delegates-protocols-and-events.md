---
title: Eventos, protocolos e delegados no xamarin. IOS
description: Este documento descreve como trabalhar com eventos, protocolos e delega no xamarin. IOS. Esses conceitos fundamentais são ubíquos no desenvolvimento do xamarin. IOS.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: 5ccefdb5e527e67338714896905734c74278d00a
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671866"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Eventos, protocolos e delegados no xamarin. IOS

Xamarin. IOS usa controles para expor os eventos para a maioria das interações do usuário.
Aplicativos xamarin. IOS consumam esses eventos da mesma forma como os aplicativos tradicionais do .NET. Por exemplo, a classe UIButton do xamarin. IOS tem um evento chamado TouchUpInside e consome este evento, como se foram a essa classe e eventos em um aplicativo .NET.

Além dessa abordagem de .NET, xamarin. IOS expõe outro modelo que pode ser usado para vinculação de dados e interação mais complexa. Essa metodologia usa o que chama Apple representantes e protocolos. Representantes são semelhantes em conceito a delegados no C#, mas em vez de definir e chamar um único método, um representante em Objective-C é uma classe inteira que está de acordo com um protocolo. Um protocolo é semelhante a uma interface no C#, exceto que seus métodos podem ser opcionais. Por exemplo, para preencher um UITableView com os dados, você criaria uma classe de representante que implementa os métodos definidos no protocolo UITableViewDataSource que chamaria a UITableView para preencher a mesmo.

Neste artigo, você saberá mais sobre todos esses tópicos, dando a você uma base sólida para lidar com cenários de retorno de chamada no xamarin. IOS, incluindo:

- **Eventos** – eventos usando o .NET com controles UIKit.
- **Protocolos** – aprender quais protocolos são e como eles são usados e criando um exemplo que fornece dados para uma anotação de mapa.
- **Delegados** – aprender sobre delegados de Objective-C estendendo o exemplo de mapa para manipular a interação do usuário que inclui uma anotação e, em seguida, aprendendo a diferença entre delegados fortes e fracos e quando usar cada um deles.

Para ilustrar os protocolos e delegados, vamos criar um aplicativo de mapa simples que adiciona uma anotação a um mapa, conforme mostrado aqui:

[![](delegates-protocols-and-events-images/01-map-sml.png "Um exemplo de um aplicativo de mapa simples que adiciona uma anotação a um mapa")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "uma anotação de exemplo adicionada a um mapa")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Antes de lidar com esse aplicativo, vamos começar examinando eventos .NET sob o UIKit.

## <a name="net-events-with-uikit"></a>Eventos do .NET com UIKit

Xamarin. IOS expõe eventos .NET nos controles UIKit. Por exemplo, UIButton tem um evento TouchUpInside, que manipulam como faria normalmente no .NET, conforme mostrado no código a seguir usa um C# expressão lambda:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

Você também pode implementar isso com um C# 2.0-estilo método anônimo como esta:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

O código anterior está conectado `ViewDidLoad` método da UIViewController. O `aButton` variável faz referência a um botão, que você pode adicionar no iOS Designer ou com o código. A figura a seguir mostra um botão que foi adicionado no Designer do iOS:

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "Um botão adicionado no Designer do iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin. IOS também dá suporte para o estilo de ação de destino de conexão com o seu código de uma interação que ocorre com um controle. Para criar uma ação de destino para o **Hello** botão, clique duas vezes no Designer do iOS. Arquivo de code-behind do UIViewController será exibido e o desenvolvedor será solicitado a selecionar um local para inserir o método de conexão:

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "O arquivo de code-behind UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Depois de selecionar um local, um novo método é criado e com fio-up para o controle. No exemplo a seguir, uma mensagem será gravada no console quando o botão é clicado:

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "Uma mensagem será gravada no console quando o botão é clicado")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Para obter mais detalhes sobre o padrão de ação de destino do iOS, consulte a seção de ação de destino de [competências de aplicativo para iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html) na biblioteca do desenvolvedor do iOS da Apple.

Para obter mais informações sobre como usar o Designer do iOS com xamarin. IOS, consulte a [visão geral do Designer do iOS](~/ios/user-interface/designer/index.md) documentação.

## <a name="events"></a>Eventos

Se você quiser capturar eventos de UIControl, você tem uma variedade de opções: usa o C# lambdas e funções de delegado usando as APIs de Objective-C de nível inferior.

A seção a seguir mostra como capturar o evento TouchDown em um botão, dependendo do quanto você precisa de controle.

## <a name="c-style"></a>C#Estilo

Usando a sintaxe de delegado:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

Se você gostar lambdas em vez disso:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Se você quiser ter vários botões usam o mesmo manipulador para compartilhar o mesmo código:

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

## <a name="monitoring-more-than-one-kind-of-event"></a>Mais de um tipo de evento de monitoramento

O C# eventos para sinalizadores de UIControlEvent têm um mapeamento individual para sinalizadores individuais. Quando você deseja ter o mesmo trecho de código lidar com dois ou mais eventos, use o `UIControl.AddTarget` método:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Usando a sintaxe lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Se você precisar usar recursos de nível baixo de Objective-C, como associando a uma determinada instância de objeto e invocação de um seletor específico:

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

Observe que, se você implementar o método de instância de uma classe base herdada, ele deve ser um método público.

## <a name="protocols"></a>Protocolos

Um protocolo é um recurso de linguagem do Objective-C que fornece uma lista de declarações de método. Ele serve a uma finalidade similar a uma interface no C#, a principal diferença é que um protocolo pode ter métodos opcionais. Métodos opcionais não são chamados se a classe que adota um protocolo não implementá-los. Além disso, uma única classe em Objective-C pode implementar vários protocolos, assim como um C# classe pode implementar várias interfaces.

Apple usa protocolos em todo o iOS para definir contratos para classes adotar, ao mesmo tempo, eliminando-se a classe de implementação do chamador, portanto operacional exatamente como um C# interface. Os protocolos são usados em cenários não delegado (como com o `MKAnnotation` exemplo mostrado a seguir) e com delegados (como apresentado neste documento, na seção de delegados).

### <a name="protocols-with-xamarinios"></a>Protocolos com xamarin. IOS

Vamos dar uma olhada em um exemplo usando um protocolo de Objective-C do xamarin. IOS. Neste exemplo, vamos usar o `MKAnnotation` protocolo, que é parte do `MapKit` framework. `MKAnnotation` é um protocolo que permite que qualquer objeto que adota para fornecer informações sobre uma anotação que pode ser adicionada a um mapa. Por exemplo, um objeto que implementa `MKAnnotation` fornece a localização de anotação e a descrição associada a ele.

Dessa forma, o `MKAnnotation` protocolo é usado para fornecer dados pertinentes que acompanha uma anotação. O modo de exibição real para a anotação em si é criado a partir de dados no objeto que adota o `MKAnnotation` protocolo. Por exemplo, o texto para o balão que aparece quando o usuário toca na anotação (conforme mostrado na captura de tela abaixo) é proveniente de `Title` propriedade na classe que implementa o protocolo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Texto de exemplo para o balão quando o usuário toca na anotação")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Conforme descrito na próxima seção [protocolos Deep Dive](#protocols-deep-dive), xamarin. IOS vincula protocolos para classes abstratas. Para o `MKAnnotation` protocolo, o limite C# classe é nomeada `MKAnnotation` imitar o nome do protocolo e ele é uma subclasse de `NSObject`, a classe de base raiz para CocoaTouch. O protocolo exige um getter e setter para ser implementada para a coordenada; No entanto, um título e subtítulo são opcionais. Portanto, nos `MKAnnotation` classe, o `Coordinate` é de propriedade *abstrata*, solicitá-la para ser implementada e o `Title` e `Subtitle` propriedades foram marcadas *virtual* , tornando-os opcionais, conforme mostrado abaixo:

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

Qualquer classe pode fornecer dados de anotação derivando simplesmente `MKAnnotation`, desde que pelo menos o `Coordinate` propriedade é implementada. Por exemplo, aqui está um exemplo de classe que usa a coordenada no construtor e retorna uma cadeia de caracteres para o título:

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

Por meio do protocolo está associado a, qualquer classe que pode efetuar subclasses `MKAnnotation` pode fornecer dados relevantes que serão usados pelo mapa de quando ele cria a exibição da anotação. Para adicionar uma anotação a um mapa, simplesmente chame o `AddAnnotation` método de um `MKMapView` da instância, conforme mostrado no código a seguir:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

A variável de mapa aqui é uma instância de um `MKMapView`, que é a classe que representa o mapa em si. O `MKMapView` usará o `Coordinate` dados derivados da `SampleMapAnnotation` instância para posicionar a exibição de anotação no mapa.

O `MKAnnotation` protocolo fornece um conjunto conhecido de recursos entre todos os objetos que implementação-lo, sem o consumidor (o mapa neste caso), necessidade de saber sobre os detalhes de implementação. Isso simplifica a adição de uma variedade de possíveis anotações a um mapa.

### <a name="protocols-deep-dive"></a>Protocolos de aprofundamento

Uma vez que C# interfaces não dão suporte a métodos opcionais, xamarin. IOS mapeia protocolos para classes abstratas. Portanto, a adoção de um protocolo em Objective-C é realizado no xamarin. IOS derivando da classe abstrata que está associado ao protocolo e implementar os métodos necessários. Esses métodos serão expostos como métodos abstratos na classe. Métodos opcionais do protocolo serão associados a métodos virtuais do C# classe.

Por exemplo, aqui está uma parte do `UITableViewDataSource` protocolo como associado no xamarin. IOS:

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

Observe que a classe é abstrata. Xamarin. IOS torna a classe abstrata para dar suporte a métodos obrigatório/opcional em protocolos.
No entanto, ao contrário de protocolos de Objective-C (ou C# interfaces), C# classes não dão suporte a várias heranças. Isso afeta o design do C# aninhados de código que usa protocolos e normalmente resulta em classes. Mais sobre esse problema é abordado mais adiante neste documento, na seção de delegados.

 `GetCell(…)` é um método abstrato, vinculado ao Objective-C *seletor*, `tableView:cellForRowAtIndexPath:`, que é um método exigido do `UITableViewDataSource` protocolo. Seletor é o termo de Objective-C para o nome do método. Para impor o método conforme necessário, o xamarin. IOS o declara como abstrato. O outro método, `NumberOfSections(…)`, está associado a `numberOfSectionsInTableview:`. Esse método é opcional no protocolo, portanto, xamarin. IOS declara-o como virtual, tornando-se opcional para substituir em C#.

Xamarin. IOS se encarrega de associação de todos os iOS para você. No entanto, se você precisar associar um protocolo do Objective-C manualmente, você poderá fazer isso decorando uma classe com o `ExportAttribute`. Isso é o mesmo método usado pelo xamarin. IOS em si.

Para obter mais informações sobre como associar tipos de Objective-C no xamarin. IOS, consulte o artigo [tipos de associação de Objective-C](~/ios/platform/binding-objective-c/index.md).

Ainda não estamos por meio com protocolos, no entanto. Eles também são usados no iOS como base para os representantes de Objective-C, que é o tópico da próxima seção.

## <a name="delegates"></a>Delegados

o iOS usa delegados de Objective-C para implementar o padrão de delegação, em que um objeto passa trabalho a outro. O objeto fazendo o trabalho é o representante do primeiro objeto. Um objeto diz ao seu delegado para fazer o trabalho por meio do envio de mensagens depois de determinadas coisas acontecem. Enviar uma mensagem como esta em Objective-C é funcionalmente equivalente a chamar um método C#. Um delegado implementa métodos em resposta a essas chamadas e então fornece a funcionalidade para o aplicativo.

Os representantes permitem estender o comportamento das classes sem a necessidade de criar subclasses. Aplicativos no iOS geralmente usam delegados quando uma classe chama de volta para outro após uma ação importante. Por exemplo, o `MKMapView` chamadas de volta ao seu delegado de classe quando o usuário toca uma anotação em um mapa, oferecendo o autor da classe delegada a oportunidade de responder dentro do aplicativo. Você pode trabalhar em um exemplo desse tipo de delegado uso neste artigo, no exemplo do uso de um delegado com xamarin. IOS.

Neste ponto, você deve estar imaginando como uma classe determina quais métodos para chamar em seu delegado. Isso é outro lugar em que você use protocolos. Normalmente, os métodos disponíveis para um delegado vêm dos protocolos de adotar.

### <a name="how-protocols-are-used-with-delegates"></a>Como os protocolos são usados com delegados

Vimos anteriormente como os protocolos são usados para dar suporte a adicionar anotações a um mapa.
Protocolos também são usados para fornecer um conjunto conhecido de métodos para as classes a ser chamada após determinados eventos ocorrem, como após a usuário toca uma anotação em um mapa ou seleciona uma célula em uma tabela. As classes que implementam esses métodos são conhecidas como os delegados das classes que chamá-los.

Classes que oferecem suporte à delegação fazê-lo ao expor uma propriedade de delegado, à qual uma classe que implementa o representante é atribuída. Os métodos que implementam o delegado varia de acordo com o protocolo que adota o delegado específico. Para o `UITableView` método, você implementa o `UITableViewDelegate` protocolo, para o `UIAccelerometer` método, você poderia implementar `UIAccelerometerDelegate`, e assim por diante para quaisquer outras classes ao longo do iOS para o qual você desejaria exponham um delegado.

O `MKMapView` classe que vimos no nosso exemplo anterior também tem uma propriedade chamada delegado, que irá chamar após eventos diversos ocorrem. O representante `MKMapView` é do tipo `MKMapViewDelegate`.
Você usará isso em breve um exemplo para responder à anotação depois que ele for selecionado, mas primeiro vamos discutir a diferença entre delegados fortes e fracos.

### <a name="strong-delegates-vs-weak-delegates"></a>Delegados forte vs. Delegados fracos

Os delegados que vimos até agora são delegados forte, que significa que eles são fortemente tipados. As associações do xamarin. IOS são fornecidos com uma classe fortemente tipada para cada protocolo de delegado no iOS. No entanto, o iOS também tem o conceito de um delegado fraco. Em vez de uma classe associada ao protocolo Objective-C para um representante específico de subclasses iOS também permite que você escolher para associar os métodos de protocolo em qualquer classe que você deseja que deriva de NSObject, decorar os métodos com o ExportAttribute e, em seguida, fornecendo os seletores apropriados.
Ao usar essa abordagem, você atribui uma instância da classe à propriedade WeakDelegate em vez de para a propriedade de delegado. Um delegado fraco oferece a flexibilidade para levar sua classe de delegado para baixo de uma hierarquia de herança diferente. Vamos examinar um exemplo de xamarin. IOS que usa delegados fortes e fracos.

### <a name="example-using-a-delegate-with-xamarinios"></a>Exemplo usando um delegado com xamarin. IOS

Para executar código em resposta ao usuário tocando a anotação em nosso exemplo, podemos subclasse `MKMapViewDelegate` e atribua uma instância para o `MKMapView`do `Delegate` propriedade. O `MKMapViewDelegate` protocolo contém apenas os métodos opcionais.
Portanto, todos os métodos são virtuais que estão associados a esse protocolo em que o xamarin. IOS `MKMapViewDelegate` classe. Quando o usuário seleciona uma anotação, o `MKMapView` instância enviará o `mapView:didSelectAnnotationView:` mensagem para o seu delegado. Para lidar com isso no xamarin. IOS, precisamos substituir o `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` método na subclasse MKMapViewDelegate como este:

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

A classe SampleMapDelegate mostrada acima é implementada como uma classe aninhada no controlador que contém o `MKMapView` instância. Em Objective-C, você verá com frequência o controlador de adotar vários protocolos diretamente dentro da classe. No entanto, uma vez que os protocolos estão associados às classes no xamarin. IOS, as classes que implementam delegados fortemente tipados são normalmente incluídas como classes aninhadas.

Com a implementação da classe de delegado em vigor, você só precisa instanciar uma instância do delegado no controlador e atribuí-lo para o `MKMapView`do `Delegate` propriedade conforme mostrado aqui:

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

Para usar um delegado fraco para realizar a mesma coisa, você precisa associar o método em qualquer classe que deriva de `NSObject` e atribuí-lo para o `WeakDelegate` propriedade do `MKMapView`. Uma vez que o `UIViewController` classe, por fim, deriva de `NSObject` (como cada classe Objective-C em CocoaTouch), podemos simplesmente implementar um método associado a `mapView:didSelectAnnotationView:` diretamente no controlador e atribuir o controlador de `MKMapView`do `WeakDelegate`, evitando a necessidade de classe aninhada extra. O código a seguir demonstra essa abordagem:

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

Ao executar esse código, o aplicativo se comporta exatamente como ao executar a versão com rigidez de tipos de delegado. O benefício desse código é que o delegado fraco não exige a criação da classe extra que foi criada quando usamos o delegado com rigidez de tipos. No entanto, isso ocorre às custas de segurança de tipos. Se você fosse cometer um erro no seletor de que foi passado para o `ExportAttribute`, você não encontrar até o tempo de execução.

### <a name="events-and-delegates"></a>Delegados e eventos

Delegados são usados para retornos de chamada no iOS de maneira semelhante à forma como o .NET usa eventos. Para tornar o iOS APIs e a maneira como eles usam Objective-C delegados parecer mais .NET, xamarin. IOS expõe eventos .NET em muitos lugares em que os delegados são usados no iOS.

Por exemplo, a implementação anterior em que o `MKMapViewDelegate` respondeu a uma anotação selecionada também poderia ser implementada no xamarin. IOS usando um evento do .NET. Nesse caso, o evento seria definido na `MKMapView` e chamado `DidSelectAnnotationView`. Ele teria um `EventArgs` subclasse do tipo `MKMapViewAnnotationEventsArgs`. O `View` propriedade de `MKMapViewAnnotationEventsArgs` oferecerá a você uma referência para o modo de exibição de anotação, de que você pode prosseguir com a mesma implementação você tinha anteriormente, como ilustrado aqui:

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

Este artigo abordou como usar eventos, protocolos e delegados no xamarin. IOS. Nós vimos como o xamarin. IOS expõe eventos normais de estilo do .NET para controles.
Em seguida aprendemos sobre protocolos de Objective-C, incluindo como eles são diferentes de C# interfaces e como o xamarin. IOS usa. Por fim, examinamos os delegados de Objective-C de uma perspectiva de xamarin. IOS. Nós vimos como xamarin. IOS dá suporte a ambos fortemente e fracamente tipado delegados e como associar eventos .NET para delegar métodos.

## <a name="related-links"></a>Links relacionados

- [Protocolos, delegados e eventos (exemplo)](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Tipos de Objective-C de associação](~/ios/platform/binding-objective-c/index.md)
- [A linguagem de programação Objective-C](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Criando Interfaces do usuário no Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competências de aplicativo para iOS](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
