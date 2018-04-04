---
title: Eventos, protocolos e delegados
description: Este artigo apresenta as tecnologias de iOS chave usadas para receber retornos de chamada e preencher os controles de interface de usuário com dados. Essas tecnologias são eventos, protocolos e delegados. Este artigo explica o que cada um deles for e como cada um é usada no c#. Ele demonstra como xamarin usa controles de iOS para expor familiares eventos de .NET, bem como xamarin oferece suporte para conceitos de Objective-C como protocolos e delegados (delegados Objective-C não devem ser confundidos com c# delegados). Este artigo também fornece exemplos que mostram como os protocolos são usados – como a base para delegados Objective-C e em cenários de não delegado.
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 4c2888eb2d0b1ae79e10ca764e7bf14a1afb6c59
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="events-protocols-and-delegates"></a>Eventos, protocolos e delegados

_Este artigo apresenta as tecnologias de iOS chave usadas para receber retornos de chamada e preencher os controles de interface de usuário com dados. Essas tecnologias são eventos, protocolos e delegados. Este artigo explica o que cada um deles for e como cada um é usada no c#. Ele demonstra como xamarin usa controles de iOS para expor familiares eventos de .NET, bem como xamarin oferece suporte para conceitos de Objective-C como protocolos e delegados (delegados Objective-C não devem ser confundidos com c# delegados). Este artigo também fornece exemplos que mostram como os protocolos são usados – como a base para delegados Objective-C e em cenários de não delegado._

Xamarin usa os controles para expor os eventos para a maioria das interações do usuário.
Aplicativos xamarin consomem esses eventos da mesma forma como os aplicativos tradicionais do .NET. Por exemplo, a classe xamarin UIButton tem um evento chamado TouchUpInside e consome este evento, como se foram a essa classe e o evento em um aplicativo .NET.

Essa abordagem .NET, além de xamarin expõe outro modelo que pode ser usado para interação mais complexa e associação de dados. Essa metodologia usa o que chama Apple delegados e protocolos. Delegados são semelhantes ao conceito de delegates em c#, mas em vez de definir e chamando um método único, um delegado em Objective-C é uma classe inteira que é compatível com um protocolo. Um protocolo é semelhante a uma interface em c#, exceto que os seus métodos podem ser opcionais. Por exemplo, para preencher um UITableView com dados, você criaria uma classe de delegado que implementa os métodos definidos no protocolo de UITableViewDataSource a UITableView chamaria seja preenchida automaticamente.

Neste artigo, você saberá mais sobre estes tópicos, fornecendo uma base sólida para lidar com cenários de retorno de chamada no xamarin, incluindo:

-  **Eventos** – eventos usando .NET com controles de UIKit.
-  **Protocolos** – aprender quais protocolos estão e como eles são usados e criando um exemplo que fornece dados para uma anotação de mapa.
-  **Delegados** – aprendizado sobre delegados Objective-C estender o exemplo de mapa para manipular a interação do usuário que inclui uma anotação, em seguida, a diferença entre os delegados fortes e fracos e quando usar cada um de aprendizado.


Para ilustrar os protocolos e delegados, criaremos um aplicativo simples de mapa que adiciona uma anotação a um mapa, conforme mostrado aqui:

 [![](delegates-protocols-and-events-images/01-map.png "Um exemplo de um aplicativo simples de mapa que adiciona uma anotação a um mapa") ](delegates-protocols-and-events-images/01-map.png#lightbox) [ ![ ] (delegates-protocols-and-events-images/04-annotation-with-callout.png "uma anotação de exemplo adicionada a um mapa")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Antes de lidar com esse aplicativo, vamos começar examinando o UIKit eventos .NET.

 <a name=".NET_Events_with_UIKit" />


## <a name="net-events-with-uikit"></a>Eventos do .NET com UIKit

Xamarin expõe eventos do .NET em controles de UIKit. Por exemplo, UIButton tem um evento TouchUpInside que tratar como faria normalmente no .NET, conforme mostrado no código a seguir que usa uma expressão lambda c#:

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

Você também pode implementar isso com um estilo 2.0 método anônimo c# como esta:

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

O código anterior está conectado no método ViewDidLoad a UIViewContoller. A variável aButton faz referência a um botão, que você pode adicionar no iOS Designer ou com o código. A figura a seguir mostra esse botão quando ele é adicionado no iOS Designer, extraído do exemplo que acompanha este artigo:

 [![](delegates-protocols-and-events-images/02-interface-builder-outlet.png "Um botão adicionado no Designer do iOS")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin também suporta o estilo de ação de destino de conexão de seu código com uma interação que ocorre com um controle. Para criar uma ação de destino para o botão Hello, clique nele duas vezes no Designer de iOS. Arquivo de code-behind do UIViewController será exibido e o desenvolvedor será solicitado a selecionar um local para inserir o método de conexão:

 [![](delegates-protocols-and-events-images/03-interface-builder-action.png "O arquivo de code-behind UIViewControllers")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

Depois de selecionar um local, um novo método é criado e com fio-up ao controle. No exemplo a seguir, uma mensagem será gravada no console quando o botão é clicado:

 [![](delegates-protocols-and-events-images/05-interface-builder-action.png "Uma mensagem será gravada no console quando o botão é clicado")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

Para obter mais detalhes sobre o padrão de ação de destino do iOS, consulte a seção de ação de destino de " [competências de aplicativo para iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)" na biblioteca do desenvolvedor de iOS da Apple.

Para obter mais informações sobre como usar o Designer do iOS com o xamarin, consulte o " [iOS visão geral do Designer](~/ios/user-interface/designer/index.md)" documentação.

 <a name="Events" />


## <a name="events"></a>Eventos

Se você quiser interceptar eventos de UIControl, você tem uma variedade de opções: usando o c# lambdas e delegar funções usando as APIs de C do objetivo de nível baixo.

A seção a seguir mostra como capturar o evento TouchDown em um botão, dependendo de quanto você precisa de controle.

 <a name="C#_Style" />


## <a name="c-style"></a>Estilo de c#

Usando a sintaxe de delegado:

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {    
    Console.WriteLine ("Touched");
};
```

Se desejar lambdas em vez disso:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

Se você quiser ter vários botões de usam o mesmo manipulador para compartilhar o mesmo código de erro:

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

<a name="Monitoring_more_than_one_kind_of_Event" />


## <a name="monitoring-more-than-one-kind-of-event"></a>Mais de um tipo de evento de monitoramento

Os eventos do c# para sinalizadores UIControlEvent tem um mapeamento para sinalizadores individuais. Quando você deseja ter a mesma parte do código de lidar com dois ou mais eventos, use o `UIControl.AddTarget` método:

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Usando a sintaxe de lambda:

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

Se você precisar usar recursos de nível inferior do Objective-C, como conectar a uma instância de objeto específico e invocar um seletor específico:

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

 <a name="Protocols" />


## <a name="protocols"></a>Protocolos

Um protocolo é um recurso de idioma Objective-C que fornece uma lista de declarações de método. Ele serve a uma finalidade semelhante a uma interface em c#, a principal diferença é que um protocolo pode ter métodos opcionais. Métodos opcionais não forem chamados se a classe que adota um protocolo não implementá-los. Além disso, uma única classe em Objective-C pode implementar vários protocolos, como uma classe c# pode implementar várias interfaces.

Apple usa protocolos ao longo do iOS para definir contratos para classes de adotar, enquanto extraindo-se a classe de implementação do chamador, operacional, portanto, assim como uma interface c#. Os protocolos são usados em cenários de não delegado (como com o `MKAnnotation` exemplo mostrado a seguir) e com delegados (como apresentado neste documento, na seção delegados).

 <a name="Protocols_with_Monotouch" />


### <a name="protocols-with-xamarinios"></a>Protocolos com xamarin

Vamos dar uma olhada em um exemplo que usa um protocolo Objective-C do xamarin. Neste exemplo, usaremos o `MKAnnotation` protocolo, que é parte do `MapKit` framework. `MKAnnotation` é um protocolo que permite que qualquer objeto que adota para fornecer informações sobre uma anotação que pode ser adicionada a um mapa. Por exemplo, um objeto que implementa `MKAnnotation` fornece o local da anotação e o título associado a ele.

Dessa forma, o `MKAnnotation` protocolo é usado para fornecer dados pertinentes que acompanha uma anotação. O modo de exibição atual para a anotação em si é criado a partir de dados no objeto que adota o `MKAnnotation` protocolo. Por exemplo, o texto para o texto explicativo que aparece quando o usuário toca na anotação (conforme mostrado na captura de tela abaixo) é proveniente do `Title` propriedade na classe que implementa o protocolo:

 [![](delegates-protocols-and-events-images/04-annotation-with-callout.png "Exemplo de texto para o texto explicativo quando o usuário toca na anotação")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

Conforme descrito na próxima seção, mergulho profundo de protocolos, xamarin associa protocolos para classes abstratas. Para o `MKAnnotation` protocolo, em que a classe do c# associada é nomeada `MKAnnotation` simular o nome do protocolo e é uma subclasse de `NSObject`, a classe de base raiz para CocoaTouch. O protocolo requer um getter e setter a ser implementada para a coordenada; No entanto, um título e subtítulo são opcionais. Portanto, o `MKAnnotation` classe, o `Coordinate` é de propriedade *abstrata*, solicitá-la para ser implementada e o `Title` e `Subtitle` propriedades são marcadas *virtual* , tornando-os opcional, conforme mostrado abaixo:

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

Qualquer classe pode fornecer dados de anotação derivando simplesmente `MKAnnotation`, desde que pelo menos o `Coordinate` propriedade é implementada. Por exemplo, aqui está um exemplo de classe que recebe a coordenada no construtor e retorna uma cadeia de caracteres para o título:

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string _title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        _title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return _title;
        }
    }
}
```

Por meio do protocolo está associado a, qualquer classe que as subclasses `MKAnnotation` pode fornecer dados relevantes que serão usados pelo mapa quando ele cria a exibição da anotação. Para adicionar uma anotação a um mapa, simplesmente chamar o `AddAnnotation` método de um `MKMapView` de instância, conforme mostrado no código a seguir:

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
new CLLocationCoordinate2D (42.3467512, -71.0969456);

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

A variável de mapa aqui é uma instância de um `MKMapView`, que é a classe que representa o mapa propriamente dito. O `MKMapView` usará o `Coordinate` dados derivados da `SampleMapAnnotation` instância para posicionar a exibição de anotação no mapa.

O `MKAnnotation` protocolo fornece um conjunto conhecido de recursos entre todos os objetos que implementam a ele, sem o consumidor (mapa neste caso) necessidade de conhecer os detalhes de implementação. Isso simplifica a adição de uma variedade de possíveis anotações a um mapa.

 <a name="Protocols_Deep_Dive" />


### <a name="protocols-deep-dive"></a>Mergulho profundo protocolos

Como interfaces c# não oferece suporte a métodos opcionais, xamarin mapeia protocolos para classes abstratas. Portanto, adotar um protocolo em Objective-C é realizada em xamarin derivar da classe abstrata que está associado ao protocolo e implementando os métodos necessários. Esses métodos serão expostos como métodos abstratos na classe. Os métodos opcionais do protocolo serão associados ao virtuais métodos da classe c#.

Por exemplo, aqui está uma parte do `UITableViewDataSource` protocolo como xamarin associada em:

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

Observe que a classe é abstrata. Xamarin faz com que a classe abstrata para dar suporte a métodos opcional/necessárias em protocolos.
No entanto, ao contrário de protocolos Objective-C (ou interfaces de c#), classes c# não oferecem suporte a várias heranças. Isso afeta o design de código em c# que usa protocolos e normalmente resulta em classes aninhadas. Mais sobre esse problema é abordado neste documento, na seção delegados.

 `GetCell(…)` é um método abstrato, associado para o Objective-C *seletor*, `tableView:cellForRowAtIndexPath:`, que é um método necessário o `UITableViewDataSource` protocolo. Seletor é o termo Objective-C para o nome do método. Para aplicar o método conforme necessário, xamarin ele declara como abstrato. O outro método, `NumberOfSections(…)`, está associado a `numberOfSectionsInTableview:`. Esse método é opcional no protocolo, portanto xamarin declara como virtual, facilitando opcional para substituir em c#.

Xamarin cuida de todos os associação de iOS para você. No entanto, se você alguma vez precisar associar um protocolo de Objective-C manualmente, você pode fazer isso de decoração de uma classe com o `ExportAttribute`. Este é o mesmo método usado pelo xamarin em si.

Para obter mais informações sobre como associar tipos Objective-C no xamarin, consulte o artigo [associação Objective-C tipos](~/ios/platform/binding-objective-c/index.md).

Não estamos por meio de com protocolos ainda, embora. Eles também são usados no iOS como base para delegados Objective-C, que é o tópico da próxima seção.

 <a name="Delegates" />


## <a name="delegates"></a>Delegados

iOS usa delegados Objective-C para implementar o padrão de delegação, em que um objeto passa trabalho para outro. O objeto fazendo o trabalho é o representante do primeiro objeto. Um objeto informa seu delegado para trabalhar, enviando mensagens de depois certas coisas acontecem. Enviar uma mensagem como esta em Objective-C é funcionalmente equivalente a chamar um método em c#. Um delegado implementa métodos em resposta a essas chamadas e então fornece funcionalidade para o aplicativo.

Delegados permitem estender o comportamento de classes sem a necessidade de criar subclasses. Aplicativos em iOS geralmente usam delegados quando uma classe chama outro após a ocorrência de uma ação importante. Por exemplo, o `MKMapView` chamadas para o seu representante de classe quando o usuário toca uma anotação em um mapa, fornecendo o autor da classe delegate a oportunidade de responder dentro do aplicativo. Você pode trabalhar em um exemplo desse tipo de delegado uso neste artigo, no exemplo usando um delegado com xamarin.

Neste ponto, você pode estar se perguntando como uma classe determina quais métodos para chamar em seu representante. Este é outro lugar onde você usa protocolos. Normalmente, os métodos disponíveis para um representante vêm dos protocolos que elas adotam.

 <a name="How_Protocols_are_used_with_Delegates" />


### <a name="how-protocols-are-used-with-delegates"></a>Como os protocolos são usados com delegados

Vimos anteriormente como protocolos são usados para oferecer suporte a anotações adicionando a um mapa.
Protocolos também são usados para fornecer um conjunto conhecido de métodos para classes de chamada após a determinados eventos ocorrem, como após uma anotação em um mapa de toques de usuário ou seleciona uma célula em uma tabela. As classes que implementam esses métodos são conhecidas como os delegados das classes chamá-los.

Classes que oferecem suporte à delegação fazem-lo ao expor uma propriedade de delegado, para que uma classe que implementa o delegado é atribuída. Os métodos que implementam o representante dependerá do protocolo específico de delegado adota. Para o `UITableView` método, você implementa o `UITableViewDelegate` protocolo, para o `UIAccelerometer` método, você poderia implementar `UIAccelerometerDelegate`, e assim por diante para todas as outras classes ao longo do iOS para o qual você deseja exponham um representante.

O `MKMapView` classe vimos em nosso exemplo anterior também tem uma propriedade chamada delegado, que chamará depois da ocorrência de vários eventos. O representante para `MKMapView` é do tipo `MKMapViewDelegate`.
Você usará isso em breve exemplo para responder à anotação após ter sido selecionado, mas primeiro vamos discutir a diferença entre os delegados fortes e fracos.

 <a name="Strong_Delegates_vs._Weak_Delegates" />


### <a name="strong-delegates-vs-weak-delegates"></a>Delegados forte vs. Delegados fracos

Os delegados que vimos até agora são fortes delegados, o que significa que eles são fortemente tipados. As associações de xamarin fornecido com uma classe fortemente tipada para cada protocolo delegado no iOS. No entanto, o iOS também tem o conceito de um delegado fraco. Em vez de subclasses de uma classe associada ao protocolo Objective-C para um representante específico, iOS também permite que você escolha associar os métodos de protocolo em qualquer classe que você deseja que deriva de NSObject, decorando seus métodos com o ExportAttribute e, em seguida, fornecendo os seletores apropriados.
Quando você usar essa abordagem, você atribuir uma instância da classe para a propriedade WeakDelegate em vez de para a propriedade de delegado. Um delegado fraco oferece a flexibilidade para tomar sua classe delegate para baixo de uma hierarquia de herança diferente. Vejamos um exemplo de xamarin que usa delegados fortes e fracos.

 <a name="Example_using_a_Delegate_with_Xamarin.iOS" />


### <a name="example-using-a-delegate-with-xamarinios"></a>Exemplo usando um delegado com xamarin

Para executar o código de resposta para o usuário tocar a anotação em nosso exemplo, podemos subclasse `MKMapViewDelegate` e atribuir uma instância para o `MKMapView`do `Delegate` propriedade. O `MKMapViewDelegate` protocolo contém apenas os métodos opcionais.
Portanto, todos os métodos são virtuais que estão associados a esse protocolo no xamarin `MKMapViewDelegate` classe. Quando o usuário seleciona uma anotação de `MKMapView` instância enviará o `mapView:didSelectAnnotationView:` mensagem para o seu representante. Para lidar com isso em xamarin, é preciso substituir o `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` método na subclasse MKMapViewDelegate como este:

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

A classe SampleMapDelegate mostrada acima é implementada como uma classe aninhada no controlador que contém o `MKMapView` instância. Em Objective-C, você geralmente verá o controlador adotar vários protocolos diretamente dentro da classe. No entanto, desde que os protocolos são vinculados para as classes em xamarin, as classes que implementam com rigidez de tipos delegados são normalmente incluídas como classes aninhadas.

Com a implementação da classe delegate em vigor, basta criar uma instância do delegado no controlador e atribuí-lo para o `MKMapView`do `Delegate` propriedade, como mostrado aqui:

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

Para usar um delegado fraco para realizar a mesma coisa, você precisa associar o método em qualquer classe que deriva de `NSObject` e atribuí-la para o `WeakDelegate` propriedade o `MKMapView`. Como o `UIViewController` classe, por fim, é derivada de `NSObject` (como cada Objective-C classe no CocoaTouch), podemos simplesmente implementar um método associado ao `mapView:didSelectAnnotationView:` diretamente no controlador e atribua o controlador de `MKMapView`do `WeakDelegate`, evitando a necessidade de classe aninhada extra. O código a seguir demonstra essa abordagem:

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

Ao executar esse código, o aplicativo se comporta exatamente como ao executar a versão fortemente tipada delegado. A vantagem desse código é que o delegado de baixa segurança não exige a criação da classe extra que foi criado quando usamos o representante fortemente tipado. No entanto, isso ocorre às custas de segurança de tipo. Se você fosse cometer um erro no seletor de foi passado para o `ExportAttribute`, você não encontrar até o tempo de execução.

 <a name="Events_and_Delegates" />


### <a name="events-and-delegates"></a>Eventos e delegados

Delegados são usados para retornos de chamada no iOS, de maneira semelhante à forma como .NET usa eventos. Para tornar o iOS de forma que os delegados Objective-C e APIs parecer mais .NET, xamarin expõe eventos do .NET em muitos lugares em que os delegados são usados no iOS.

Por exemplo, a implementação anterior em que o `MKMapViewDelegate` respondeu a uma anotação selecionada também pode ser implementada em xamarin usando um evento .NET. Nesse caso, o evento deve ser definido em `MKMapView` e chamado `DidSelectAnnotationView`. Ela terá um `EventArgs` subclasse do tipo `MKMapViewAnnotationEventsArgs`. O `View` propriedade `MKMapViewAnnotationEventsArgs` resultará em uma referência para o modo de exibição de anotação, do qual você prosseguir com a mesma implementação você tinha anteriormente, como ilustrado aqui:

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

 <a name="Summary" />


## <a name="summary"></a>Resumo

Este artigo abordados como usar eventos, protocolos e delegados em xamarin. Vimos como xamarin expõe eventos normais de estilo do .NET para controles.
Em seguida, nós aprendemos sobre protocolos Objective-C, incluindo como eles são diferentes de interfaces c# e como o xamarin usa. Por fim, examinamos delegados Objective-C de uma perspectiva de xamarin. Vimos como xamarin dá suporte a ambos fortemente e delegados de tipo fraco e como associar eventos .NET para delegar métodos.


## <a name="related-links"></a>Links relacionados

- [Protocolos, representantes e eventos (exemplo)](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Associação de tipos Objective-C](~/ios/platform/binding-objective-c/index.md)
- [A linguagem de programação Objective-C](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [Criando Interfaces do usuário no Xcode 4](http://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [Competências de aplicativo para iOS](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)
