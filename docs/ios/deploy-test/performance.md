---
title: Desempenho do Xamarin.iOS
description: Este documento descreve técnicas que podem ser usadas para melhorar o desempenho e o uso de memória em aplicativos Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 02b1f628-52d9-49de-8479-f2696546ca3f
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 01/29/2016
ms.openlocfilehash: b41916a65e091fb7d5198a6f06a5e91aa9d6bb81
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292349"
---
# <a name="xamarinios-performance"></a>Desempenho do Xamarin.iOS

O baixo desempenho de aplicativo se apresenta de várias maneiras. Ele pode fazer com que o aplicativo pareça não responder, deixar a rolagem lenta ou reduzir a vida útil da bateria. No entanto, a otimização do desempenho engloba mais do que apenas a implementação de um código eficiente. A experiência do usuário quanto ao desempenho do aplicativo também deve ser considerada. Por exemplo, garantir que as operações sejam executadas sem impedir o usuário de realizar outras atividades pode ajudar a melhorar a experiência do usuário.

Este documento descreve técnicas que podem ser usadas para melhorar o desempenho e o uso de memória em aplicativos Xamarin.iOS.

> [!NOTE]
> Antes de ler esse artigo, você deve primeiro ler [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md), que discute técnicas que não são específicas a uma plataforma para melhorar o uso de memória e o desempenho de aplicativos criados usando a plataforma Xamarin.

## <a name="avoid-strong-circular-references"></a>Evitar referências circulares fortes

Em algumas situações, é possível criar ciclos de referência fortes que podem impedir que os objetos tenham sua memória recuperada pelo coletor de lixo. Por exemplo, considere o caso no qual uma subclasse derivada de [`NSObject`](xref:Foundation.NSObject), como uma classe que herda de [`UIView`](xref:UIKit.UIView), é adicionada a um contêiner derivado de `NSObject` e é altamente referenciado do Objective-C, conforme mostrado no exemplo de código a seguir:

```csharp
class Container : UIView
{
    public void Poke ()
    {
    // Call this method to poke this object
    }
}

class MyView : UIView
{
    Container parent;
    public MyView (Container parent)
    {
        this.parent = parent;
    }

    void PokeParent ()
    {
        parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

Quando esse código cria a instância `Container`, o objeto C# terá uma referência forte a um objeto Objective-C. Da mesma forma, a instância `MyView` também terá uma referência forte a um objeto Objective-C.

Além disso, a chamada para `container.AddSubview` aumentará a contagem de referência na instância `MyView` não gerenciada. Quando isso acontece, o tempo de execução do Xamarin.iOS cria uma instância `GCHandle` para manter o objeto `MyView` ativo no código gerenciado, uma vez que não há nenhuma garantia de que qualquer objeto gerenciado manterá uma referência a ele. De uma perspectiva de código gerenciado, o objeto `MyView` seria recuperado depois que a chamada [`AddSubview`](xref:UIKit.UIView.AddSubview(UIKit.UIView)) não fosse para o `GCHandle`.

O objeto `MyView` não gerenciado terá um `GCHandle` apontando para o objeto gerenciado, conhecido como um *link forte*. O objeto gerenciado terá uma referência para a instância `Container`. Por sua vez, a instância `Container` terá uma referência gerenciada ao objeto `MyView`.

Em casos nos quais um objeto contido mantém um link para seu contêiner, há várias opções disponíveis para lidar com a referência circular:

- Interrompa manualmente o ciclo ao definir o link para o contêiner para `null`.
- Remova manualmente o objeto contido do contêiner.
- Chame `Dispose` nos objetos.
- Evite que a referência circular mantenha uma referência fraca ao contêiner. Para obter mais informações sobre referências fracas.

### <a name="using-weakreferences"></a>Usando WeakReferences

Uma maneira de evitar um ciclo é usar uma referência fraca do filho para o pai. Por exemplo, o código acima poderia ser escrito da seguinte forma:

```csharp
class Container : UIView
{
    public void Poke ()
    {
        // Call this method to poke this object
    }
}

class MyView : UIView
{
    WeakReference<Container> weakParent;
    public MyView (Container parent)
    {
        this.weakParent = new WeakReference<Container> (parent);
    }

    void PokeParent ()
    {
        if (weakParent.TryGetTarget (out var parent))
            parent.Poke ();
    }
}

var container = new Container ();
container.AddSubview (new MyView (container));
```

Aqui, o objeto contido não manterá o pai ativo. No entanto, o pai mantém o filho ativo por meio da chamada feita para `container.AddSubView`.

Isso também ocorre em APIs do iOS que usam o padrão delegado ou de fonte de dados, em que uma classe par contém a implementação, por exemplo, ao definir a propriedade [`Delegate`](xref:UIKit.UITableView.Delegate*)
ou a [`DataSource`](xref:UIKit.UITableView.DataSource*)
na classe [`UITableView`](xref:UIKit.UITableView).

No caso das classes que são criadas somente para implementar um protocolo, por exemplo a [`IUITableViewDataSource`](xref:UIKit.IUITableViewDataSource), o que você pode fazer é, em vez de criar uma subclasse, apenas implementar a interface na classe e substituir o método, atribuindo a propriedade `DataSource` à `this`.

#### <a name="weak-attribute"></a>Atributo fraco

O [Xamarin.iOS 11.10](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_11/xamarin.ios_11.10.md#WeakAttribute) introduziu o atributo `[Weak]`. Como `WeakReference <T>`, `[Weak]` pode ser usado para interromper [referências circulares fortes](https://docs.microsoft.com/xamarin/ios/deploy-test/performance#avoid-strong-circular-references), mas com ainda menos código.

Considere o seguinte código, que usa `WeakReference <T>`:

```csharp
public class MyFooDelegate : FooDelegate {
    WeakReference<MyViewController> controller;
    public MyFooDelegate (MyViewController ctrl) => controller = new WeakReference<MyViewController> (ctrl);
    public void CallDoSomething ()
    {
        MyViewController ctrl;
        if (controller.TryGetTarget (out ctrl)) {
            ctrl.DoSomething ();
        }
    }
}
```

O código equivalente usando `[Weak]` é muito mais conciso:

```csharp
public class MyFooDelegate : FooDelegate {
    [Weak] MyViewController controller;
    public MyFooDelegate (MyViewController ctrl) => controller = ctrl;
    public void CallDoSomething () => controller.DoSomething ();
}
```

Veja abaixo outro exemplo do uso de `[Weak]` no contexto do padrão de [delegação](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Delegation.html):

```csharp
public class MyViewController : UIViewController
{
    WKWebView webView;

    protected MyViewController (IntPtr handle) : base (handle) { }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        webView = new WKWebView (View.Bounds, new WKWebViewConfiguration ());
        webView.UIDelegate = new UIDelegate (this);
        View.AddSubview (webView);
    }
}

public class UIDelegate : WKUIDelegate
{
    [Weak] MyViewController controller;

    public UIDelegate (MyViewController ctrl) => controller = ctrl;

    public override void RunJavaScriptAlertPanel (WKWebView webView, string message, WKFrameInfo frame, Action completionHandler)
    {
        var msg = $"Hello from: {controller.Title}";
        var alertController = UIAlertController.Create (null, msg, UIAlertControllerStyle.Alert);
        alertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
        controller.PresentViewController (alertController, true, null);
        completionHandler ();
    }
}
```

### <a name="disposing-of-objects-with-strong-references"></a>Descartando objetos com referências fortes

Se houver uma referência forte e for difícil remover a dependência, faça um método `Dispose` limpar o ponteiro pai.

Para os contêineres, substitua o método `Dispose` para remover os objetos contidos, conforme mostrado no exemplo de código a seguir:

```csharp
class MyContainer : UIView
{
    public override void Dispose ()
    {
        // Brute force, remove everything
        foreach (var view in Subviews)
        {
              view.RemoveFromSuperview ();
        }
        base.Dispose ();
    }
}
```

Para um objeto filho que mantém uma referência forte ao seu pai, limpe a referência ao pai na implementação `Dispose`:

```csharp
class MyChild : UIView
{
    MyContainer container;
    public MyChild (MyContainer container)
    {
        this.container = container;
    }
    public override void Dispose ()
    {
        container = null;
    }
}
```

Para obter mais informações sobre a liberação de referências fortes, confira [Liberar recursos IDisposable](~/cross-platform/deploy-test/memory-perf-best-practices.md#idisposable).
Você também encontra uma boa discussão nesta postagem do blog: [Xamarin.iOS, o coletor de lixo e eu](http://c-sharx.net/2015-04-27-xamarin-ios-the-garbage-collector-and-me).

### <a name="more-information"></a>Mais informações

Para obter mais informações, confira [Rules to Avoid Retain Cycles](http://www.cocoawithlove.com/2009/07/rules-to-avoid-retain-cycles.html) (Regras para evitar a retenção de ciclos) em Cocoa With Love, [Is this a bug in MonoTouch GC](https://stackoverflow.com/questions/13058521/is-this-a-bug-in-monotouch-gc) (Isso é um bug no MonoTouch GC?) em StackOverflow e [Why can't MonoTouch GC kill managed objects with refcount > 1?](https://stackoverflow.com/questions/13064669/why-cant-monotouch-gc-kill-managed-objects-with-refcount-1) (Por que o MonoTouch GC não pode eliminar objetos gerenciados com contagem de referência > 1?) no StackOverflow.

## <a name="optimize-table-views"></a>Otimizar modos de exibição de tabela

Os usuários desejam obter uma rolagem suave e tempos de carregamento rápidos nas instâncias [`UITableView`](xref:UIKit.UITableView). No entanto, o desempenho de rolagem pode ser prejudicado quando as células tiverem hierarquias do modo de exibição profundamente aninhadas ou quando as células tiverem layouts complexos. Contudo, existem técnicas que podem ser usadas para evitar o baixo desempenho de `UITableView`:

- Células de reutilização. Para saber mais, confira [Células de Reutilização](#reuse-cells).
- Reduzir o número de subexibições.
- Armazene em cache o conteúdo da célula que for recuperado de um serviço Web.
- Armazene a altura das linhas em cache, se elas não forem idênticas.
- Torne a célula e quaisquer exibições, opacas.
- Evite gradientes e a colocação da imagem em escala.

Coletivamente, essas técnicas podem ajudar a manter a rolagem das instâncias [`UITableView`](xref:UIKit.UITableView) suave.

### <a name="reuse-cells"></a>Reutilizar células

Ao exibir centenas de linhas em um [`UITableView`](xref:UIKit.UITableView), seria um desperdício de memória criar centenas de objetos [`UITableViewCell`](xref:UIKit.UITableViewCell) quando apenas um pequeno número deles é exibido na tela ao mesmo tempo. Em vez disso, apenas as células visíveis na tela podem ser carregadas na memória, com o **conteúdo** sendo carregado nessas células reutilizadas. Isso impede a instanciação de centenas de objetos adicionais, economizando tempo e memória.

Portanto, quando uma célula desaparece da tela, seu modo de exibição poderá ser colocado em uma fila para reutilização, conforme mostrado no exemplo de código a seguir:

```csharp
class MyTableSource : UITableViewSource
{
    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        // iOS will create a cell automatically if one isn't available in the reuse pool
        var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

        // Perform required cell actions
        return cell;
    }
}
```

Conforme o usuário rola, o [`UITableView`](xref:UIKit.UITableView) chama a substituição `GetCell` para solicitar novos modos de exibição para mostrar. Essa substituição, então, chama o método [`DequeueReusableCell`](xref:UIKit.UITableView.DequeueReusableCell(Foundation.NSString)) e, se uma célula estiver disponível para reutilização, ele será retornado.

Para obter mais informações, confira [Reutilização de Célula](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) em [Populating a Table with Data](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Preenchendo uma Tabela usando dados).

## <a name="use-opaque-views"></a>Usar exibições opacas

Certifique-se de que todas as exibições que não apresentem transparência definida tenham sua propriedade [`Opaque`](xref:UIKit.UIView.Opaque) configurada. Isso garantirá que os modos de exibição sejam processados de forma ideal pelo sistema de desenho. Isso é particularmente importante quando uma exibição é inserida em um [`UIScrollView`](xref:UIKit.UIScrollView) ou faz parte de uma animação complexa. Caso contrário, o sistema de desenho alinhará as exibições com outros tipos de conteúdo, o que pode afetar visivelmente no desempenho.

## <a name="avoid-fat-xibs"></a>Evitar XIBs pesados

Embora os XIBs tenham sido amplamente substituídos pelos storyboards, há algumas circunstâncias nas quais os XIBs ainda podem ser usados. Quando um XIB é carregado na memória, todo seu conteúdo é carregado na memória, incluindo todas as imagens. Se o XIB tiver um modo de exibição que não estiver sendo usado imediatamente, então a memória estará sendo desperdiçada. Portanto, ao usar XIBs, verifique se há apenas um XIB por controlador de exibição e, se possível, coloque a hierarquia do modo de exibição do controlador de exibição em XIBs separados.

## <a name="optimize-image-resources"></a>Otimizar os recursos de imagem

As imagens são alguns dos recursos mais caros que os aplicativos usam e, geralmente, são capturadas em alta resolução. Portanto, ao exibir uma imagem do pacote do aplicativo em um [`UIImageView`](xref:UIKit.UIImageView), certifique-se de que a imagem e o `UIImageView` sejam do mesmo tamanho. A colocação de imagens em escala no tempo de execução pode ser uma operação cara, especialmente se o `UIImageView` for inserido em uma [`UIScrollView`](xref:UIKit.UIScrollView).

Para obter mais informações, confira [Otimizar os recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) na guia [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="test-on-devices"></a>Testar nos dispositivos

Comece a implantar e a testar um aplicativo em um dispositivo físico o quanto antes. Os simuladores não correspondem perfeitamente com os comportamentos e as limitações dos dispositivos. Desse modo, é importante fazer um teste em um cenário de dispositivo real o mais rápido possível.

Em particular, o simulador não simula de forma alguma a memória ou as restrições de CPU de um dispositivo físico.

## <a name="synchronize-animations-with-the-display-refresh"></a>Sincronizar animações com a atualização da exibição

Os jogos tendem a ter loops comprimidos para executar a lógica do jogo e atualizar a tela. As taxas de quadro típicas variam de trinta a sessenta quadros por segundo. Alguns desenvolvedores acreditam que devem atualizar a tela quantas vezes forem possíveis por segundo, combinando a simulação de jogo com as atualizações na tela e podem se sentir motivados a ir além dos sessenta quadros por segundo.

No entanto, o servidor de exibição executa atualizações de tela em um limite de sessenta vezes por segundo. Portanto, a tentativa de atualizar a tela de modo mais rápido do que esse limite pode causar subdivisão e intermitências da tela. É melhor estruturar o código para que as atualizações de tela sejam sincronizadas com a atualização da exibição. Isso pode ser obtido usando a classe [`CoreAnimation.CADisplayLink`](xref:CoreAnimation.CADisplayLink), que é um temporizador adequado para visualização e jogos executados em sessenta quadros por segundo.

## <a name="avoid-core-animation-transparency"></a>Evitar transparência de animação de núcleo

Evitar a transparência de animação de núcleo melhora o desempenho da composição de bitmap. Em geral, evite camadas transparentes e bordas borradas, se possível.

## <a name="avoid-code-generation"></a>Evitar a geração de código

A geração de código de modo dinâmico com o `System.Reflection.Emit` ou com o *Dynamic Language Runtime* deve ser evitada, uma vez que o kernel iOS impede a execução do código dinâmico.

## <a name="summary"></a>Resumo

Esse artigo descreve e discute técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.iOS. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

## <a name="related-links"></a>Links relacionados

- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)
