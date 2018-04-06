---
title: Desempenho do Xamarin.iOS
description: Há várias técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.iOS. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo. Esse artigo descreve e aborda essas técnicas.
ms.prod: xamarin
ms.assetid: 02b1f628-52d9-49de-8479-f2696546ca3f
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/29/2016
ms.openlocfilehash: 3fc6263aa99edb94ae69f1ce8f87835043477392
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinios-performance"></a>Desempenho do Xamarin.iOS

_Há várias técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.iOS. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo. Esse artigo descreve e aborda essas técnicas._

O baixo desempenho de aplicativo se apresenta de várias maneiras. Ele pode fazer com que o aplicativo pareça não responder, deixar a rolagem lenta ou reduzir a vida útil da bateria. No entanto, a otimização do desempenho engloba mais do que apenas a implementação de um código eficiente. A experiência do usuário quanto ao desempenho do aplicativo também deve ser considerada. Por exemplo, garantir que as operações sejam executadas sem impedir o usuário de realizar outras atividades pode ajudar a melhorar a experiência do usuário.

Há várias técnicas para aumentar o desempenho, bem como o desempenho observado, dos aplicativos criados com o Xamarin.iOS. Entre elas estão:

- [Evitar os ciclos de referência fortes](#avoidcircularreferences)
- [Otimizar os modos de exibição da tabela](#optimizetableviews)
- [Usar exibições opacas](#opaqueviews)
- [Evitar FAT XIBs](#avoidfatxibs)
- [Otimizar os recursos de imagem](#optimizeimages)
- [Testar nos dispositivos](#testondevices)
- [Sincronizar animações com a atualização da exibição](#synchronizeanimations)
- [Evitar transparência de animação de núcleo](#avoidtransparency)
- [Evitar a geração de código](#avoidcodegeneration)

> [!NOTE]
> Antes de ler esse artigo, você deve primeiro ler [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md), que discute técnicas que não são específicas a uma plataforma para melhorar o uso de memória e o desempenho de aplicativos criados usando a plataforma Xamarin.

<a name="avoidcircularreferences" />

## <a name="avoid-strong-circular-references"></a>Evitar referências circulares fortes

Em algumas situações, é possível criar ciclos de referência fortes que podem impedir que os objetos tenham sua memória recuperada pelo coletor de lixo. Por exemplo, considere o caso no qual uma subclasse derivada de [`NSObject`](https://developer.xamarin.com/api/type/Foundation.NSObject/), como uma classe que herda de [`UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/), é adicionada a um contêiner derivado de `NSObject` e é altamente referenciado do Objective-C, conforme mostrado no exemplo de código a seguir:

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

Além disso, a chamada para `container.AddSubview` aumentará a contagem de referência na instância `MyView` não gerenciada. Quando isso acontece, o tempo de execução do Xamarin.iOS cria uma instância `GCHandle` para manter o objeto `MyView` ativo no código gerenciado, uma vez que não há nenhuma garantia de que qualquer objeto gerenciado manterá uma referência a ele. De uma perspectiva de código gerenciado, o objeto `MyView` seria recuperado depois que a chamada [`AddSubview`](https://developer.xamarin.com/api/member/UIKit.UIView.AddSubview/p/UIKit.UIView/) não fosse para o `GCHandle`.

O objeto `MyView` não gerenciado terá um `GCHandle` apontando para o objeto gerenciado, conhecido como um *link forte*. O objeto gerenciado terá uma referência para a instância `Container`. Por sua vez, a instância `Container` terá uma referência gerenciada ao objeto `MyView`.

Em casos nos quais um objeto contido mantém um link para seu contêiner, há várias opções disponíveis para lidar com a referência circular:

-  Interrompa manualmente o ciclo ao definir o link para o contêiner para `null`.
-  Remova manualmente o objeto contido do contêiner.
-  Chame `Dispose` nos objetos.
-  Evite que a referência circular mantenha uma referência fraca ao contêiner. Para obter mais informações sobre referências fracas.

### <a name="using-weakreferences"></a>Usando WeakReferences

Uma maneira de impedir um ciclo é usar uma referência fraca do filho para o pai. Por exemplo, o código acima poderia ser escrito da seguinte maneira:

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

Isso significa que o objeto contido não manterá o pai ativo, mas somente o pai manterá o filho ativo por meio da chamada feita a `container.AddSubView`.

Essa linguagem também acontece em APIs do iOS que usam o padrão delegado ou de fonte de dados, em que uma classe par contém a implementação, por exemplo, ao definir a propriedade [`Delegate`](https://developer.xamarin.com/api/property/MonoTouch.UIKit.UITableView.Delegate/) ou a [`DataSource`](https://developer.xamarin.com/api/property/MonoTouch.UIKit.UITableView.DataSource/) na classe [`UITableView`](https://developer.xamarin.com/api/type/UIKit.UITableView/).

No caso das classes que são criadas somente para implementar um protocolo, por exemplo a [`IUITableViewDataSource`](https://developer.xamarin.com/api/type/MonoTouch.UIKit.IUITableViewDataSource/), o que você pode fazer é, em vez de criar uma subclasse, apenas implementar a interface na classe e substituir o método, atribuindo a propriedade `DataSource` à `this`.

### <a name="disposing-of-objects-with-strong-references"></a>Descarte de objetos com referências fortes

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
    class MyChild : UIView {
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
Há também uma boa discussão nesta postagem de blog: [Xamarin.iOS, the garbage collector and me](http://krumelur.me/2015/04/27/xamarin-ios-the-garbage-collector-and-me/) (Xamarin.iOS, o coletor de lixo e eu).

### <a name="more-information"></a>Mais informações

Para obter mais informações, confira [Rules to Avoid Retain Cycles](http://www.cocoawithlove.com/2009/07/rules-to-avoid-retain-cycles.html) (Regras para evitar a retenção de ciclos) em Cocoa With Love, [Is this a bug in MonoTouch GC](http://stackoverflow.com/questions/13058521/is-this-a-bug-in-monotouch-gc) (Isso é um bug no MonoTouch GC?) em StackOverflow e [Why can't MonoTouch GC kill managed objects with refcount > 1?](http://stackoverflow.com/questions/13064669/why-cant-monotouch-gc-kill-managed-objects-with-refcount-1) (Por que o MonoTouch GC não pode eliminar objetos gerenciados com contagem de referência > 1?) no StackOverflow.


<a name="optimizetableviews" />

## <a name="optimize-table-views"></a>Otimizar os modos de exibição da tabela

Os usuários desejam obter uma rolagem suave e tempos de carregamento rápidos nas instâncias [`UITableView`](https://developer.xamarin.com/api/type/UIKit.UITableView/). No entanto, o desempenho de rolagem pode ser prejudicado quando as células tiverem hierarquias do modo de exibição profundamente aninhadas ou quando as células tiverem layouts complexos. Contudo, existem técnicas que podem ser usadas para evitar o baixo desempenho de `UITableView`:

- Células de reutilização. Para saber mais, confira [Células de Reutilização](#reusecells).
- Reduzir o número de subexibições.
- Armazene em cache o conteúdo da célula que for recuperado de um serviço Web.
- Armazene a altura das linhas em cache, se elas não forem idênticas.
- Torne a célula e quaisquer exibições, opacas.
- Evite gradientes e a colocação da imagem em escala.

Coletivamente, essas técnicas podem ajudar a manter a rolagem das instâncias [`UITableView`](https://developer.xamarin.com/api/type/UIKit.UITableView/) suave.

<a name="reusecells" />

### <a name="reuse-cells"></a>Células de Reutilização

Ao exibir centenas de linhas em um [`UITableView`](https://developer.xamarin.com/api/type/UIKit.UITableView/), seria um desperdício de memória criar centenas de objetos [`UITableViewCell`](https://developer.xamarin.com/api/type/UIKit.UITableViewCell/) quando apenas um pequeno número deles é exibido na tela ao mesmo tempo. Em vez disso, apenas as células visíveis na tela podem ser carregadas na memória, com o **conteúdo** sendo carregado nessas células reutilizadas. Isso impede a instanciação de centenas de objetos adicionais, economizando tempo e memória.

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

Conforme o usuário rola, o [`UITableView`](https://developer.xamarin.com/api/type/UIKit.UITableView/) chama a substituição `GetCell` para solicitar novos modos de exibição para mostrar. Essa substituição, então, chama o método [`DequeueReusableCell`](https://developer.xamarin.com/api/member/UIKit.UITableView.DequeueReusableCell/p/Foundation.NSString/) e, se uma célula estiver disponível para reutilização, ele será retornado.

Para obter mais informações, confira [Reutilização de Célula](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) em [Populating a Table with Data](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Preenchendo uma Tabela usando dados).

<a name="opaqueviews" />

## <a name="use-opaque-views"></a>Usar exibições opacas

Certifique-se de que todas as exibições que não apresentem transparência definida tenham sua propriedade [`Opaque`](https://developer.xamarin.com/api/property/UIKit.UIView.Opaque/) configurada. Isso garantirá que os modos de exibição sejam processados de forma ideal pelo sistema de desenho. Isso é particularmente importante quando uma exibição é inserida em um [`UIScrollView`](https://developer.xamarin.com/api/type/UIKit.UIScrollView/) ou faz parte de uma animação complexa. Caso contrário, o sistema de desenho alinhará as exibições com outros tipos de conteúdo, o que pode afetar visivelmente no desempenho.

<a name="avoidfatxibs" />

## <a name="avoid-fat-xibs"></a>Evitar FAT XIBs

Embora os XIBs tenham sido amplamente substituídos pelos storyboards, há algumas circunstâncias nas quais os XIBs ainda podem ser usados. Quando um XIB é carregado na memória, todo seu conteúdo é carregado na memória, incluindo todas as imagens. Se o XIB tiver um modo de exibição que não estiver sendo usado imediatamente, então a memória estará sendo desperdiçada. Portanto, ao usar XIBs, verifique se há apenas um XIB por controlador de exibição e, se possível, coloque a hierarquia do modo de exibição do controlador de exibição em XIBs separados.

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Otimizar os recursos de imagem

As imagens são alguns dos recursos mais caros que os aplicativos usam e, geralmente, são capturadas em alta resolução. Portanto, ao exibir uma imagem do pacote do aplicativo em um [`UIImageView`](https://developer.xamarin.com/api/type/UIKit.UIImageView/), certifique-se de que a imagem e o `UIImageView` sejam do mesmo tamanho. A colocação de imagens em escala no tempo de execução pode ser uma operação cara, especialmente se o `UIImageView` for inserido em uma [`UIScrollView`](https://developer.xamarin.com/api/type/UIKit.UIScrollView/).

Para obter mais informações, confira [Otimizar os recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) na guia [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md).

<a name="testondevices" />

## <a name="test-on-devices"></a>Testar nos dispositivos

Comece a implantar e a testar um aplicativo em um dispositivo físico o quanto antes. Os simuladores não correspondem perfeitamente com os comportamentos e as limitações dos dispositivos. Desse modo, é importante fazer um teste em um cenário de dispositivo real o mais rápido possível.

Em particular, o simulador não simula de forma alguma a memória ou as restrições de CPU de um dispositivo físico.

<a name="synchronizeanimations" />

## <a name="synchronize-animations-with-the-display-refresh"></a>Sincronizar animações com a atualização da exibição

Os jogos tendem a ter loops comprimidos para executar a lógica do jogo e atualizar a tela. As taxas de quadro típicas variam de trinta a sessenta quadros por segundo. Alguns desenvolvedores acreditam que devem atualizar a tela quantas vezes forem possíveis por segundo, combinando a simulação de jogo com as atualizações na tela e podem se sentir motivados a ir além dos sessenta quadros por segundo.

No entanto, o servidor de exibição executa atualizações de tela em um limite de sessenta vezes por segundo. Portanto, a tentativa de atualizar a tela de modo mais rápido do que esse limite pode causar subdivisão e intermitências da tela. É melhor estruturar o código para que as atualizações de tela sejam sincronizadas com a atualização da exibição. Isso pode ser obtido usando a classe [`CoreAnimation.CADisplayLink`](https://developer.xamarin.com/api/type/CoreAnimation.CADisplayLink/), que é um temporizador adequado para visualização e jogos executados em sessenta quadros por segundo.

<a name="avoidtransparency" />

## <a name="avoid-core-animation-transparency"></a>Evitar transparência de animação de núcleo

Evitar a transparência de animação de núcleo melhora o desempenho da composição de bitmap. Em geral, evite camadas transparentes e bordas borradas, se possível.

<a name="avoidcodegeneration" />

## <a name="avoid-code-generation"></a>Evitar a geração de código

A geração de código de modo dinâmico com o `System.Reflection.Emit` ou com o *Dynamic Language Runtime* deve ser evitada, uma vez que o kernel iOS impede a execução do código dinâmico.

## <a name="summary"></a>Resumo

Esse artigo descreve e discute técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.iOS. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

## <a name="related-links"></a>Links relacionados

- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)
