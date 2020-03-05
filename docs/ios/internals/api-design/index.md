---
title: Design de API do Xamarin. iOS
description: Princípios de orientação que foram usados para arquitetar as APIs do Xamarin. iOS e como elas se relacionam a Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a2435b30b7d5b468fca6c55d295c87b9a0d20652
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292927"
---
# <a name="xamarinios-api-design"></a>Design de API do Xamarin. iOS

Além das principais bibliotecas de classes base que fazem parte do mono, o [Xamarin. Ios](~/ios/index.yml) é fornecido com associações para várias APIs do IOS para permitir que os desenvolvedores criem aplicativos Ios nativos com o mono.

No núcleo do Xamarin. iOS, há um mecanismo de interoperabilidade que une C# o mundo com o mundo Objective-C, bem como associações para as APIs baseadas em Ios c, como CoreGraphics e [OpenGL ES](#opengles).

O tempo de execução de baixo nível para se comunicar com o código Objective-C está em [MonoTouch. ObjCRuntime](#objcruntime). Além disso, são fornecidas associações para [Foundation](#foundation), CoreFoundation e [UIKit](#uikit) .

## <a name="design-principles"></a>Princípios de design

Esses são alguns dos princípios de design para as associações do Xamarin. iOS (eles também se aplicam ao Xamarin. Mac, as associações mono para Objective-C no macOS):

- Siga as [diretrizes de design da estrutura](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Permitir que os desenvolvedores criem classes Objective-C de subclasse:

  - Derivar de uma classe existente
  - Chamar o construtor base para cadeia
  - Métodos de substituição devem ser feitos C#com o sistema de substituição
  - A subclasse deve funcionar com C# construções padrão

- Não expor os desenvolvedores aos seletores de Objective-C
- Fornecer um mecanismo para chamar bibliotecas de Objective-C arbitrários
- Tornar as tarefas comuns de Objective-C fáceis e rígidas-C possíveis
- Expor propriedades Objective-C C# como propriedades
- Expor uma API fortemente tipada:

  - Aumentar a segurança do tipo
  - Minimizar erros de tempo de execução
  - Obter o IntelliSense do IDE em tipos de retorno
  - Permite documentação de popup do IDE

- Incentive a exploração no IDE das APIs:

  - Por exemplo, em vez de expor uma matriz de tipo fraco como esta:

    ```objc
    NSArray *getViews
    ```

    Expor um tipo forte, desta forma:

    ```csharp
    NSView [] Views { get; set; }
    ```

    Isso dá Visual Studio para Mac a capacidade de fazer a conclusão automática ao navegar pela API, disponibilizar todas as operações de `System.Array` no valor retornado e permitir que o valor de retorno participe no LINQ.

- Tipos C# nativos:

  - [`NSString` se torna `string`](~/ios/internals/api-design/nsstring.md)
  - Ativar `int` e `uint` parâmetros que devem ter sido enumerados em C# enumerações e C# enumerações com atributos de `[Flags]`
  - Em vez de objetos `NSArray` de tipo neutro, expõem matrizes como matrizes fortemente tipadas.
  - Para eventos e notificações, dê aos usuários uma opção entre:

    - Uma versão fortemente tipada por padrão
    - Uma versão de tipo fraco para casos de uso avançados

- Suporte ao padrão delegado Objective-C:

  - C#sistema de eventos
  - Expor C# delegados (lambdas, métodos anônimos e `System.Delegate`) para APIs de Objective-C como blocos

### <a name="assemblies"></a>Assemblies

O xamarin. iOS inclui vários assemblies que constituem o *perfil do Xamarin. Ios*. A página [assemblies](~/cross-platform/internals/available-assemblies.md) tem mais informações.

### <a name="major-namespaces"></a>Principais namespaces

#### <a name="objcruntime"></a>ObjCRuntime

O namespace [ObjCRuntime](xref:ObjCRuntime) permite aos desenvolvedores ligar os mundos entre C# o e o Objective-C.
Essa é uma nova associação, projetada especificamente para o iOS, com base na experiência do Cocoa # e do GTK #.

#### <a name="foundation"></a>Foundation

O namespace [Foundation](xref:Foundation) fornece os tipos de dados básicos projetados para interoperar com a estrutura objetiva-c Foundation que faz parte do IOS e é a base para a programação orientada a objeto em Objective-c.

O Xamarin. iOS é C# espelhado na hierarquia de classes de Objective-C. Por exemplo, a classe base Objective-C NSObject é utilizável C# de via [Foundation. NSObject](xref:Foundation.NSObject).

Embora esse namespace forneça associações para os tipos base Objective-C, em alguns casos, mapeamos os tipos subjacentes para tipos .NET. Por exemplo:

- Em vez de lidar com NSString [e NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), o tempo de execução C#expõe isso como [cadeia de caracteres](xref:System.String)s e [matrizes](xref:System.Array)com rigidez de tipos em toda a API.

- Várias APIs auxiliares são expostas aqui para permitir que os desenvolvedores associem APIs Objective-C de terceiros, outras APIs do iOS ou APIs que atualmente não estão associadas ao Xamarin. iOS.

Para obter mais detalhes sobre APIs de associação, consulte a seção [gerador de associação do Xamarin. Ios](~/cross-platform/macios/binding/binding-types-reference.md) .

##### <a name="nsobject"></a>NSObject

O tipo [NSObject](xref:Foundation.NSObject) é a base para todas as associações de Objective-C. Os tipos Xamarin. iOS espelham duas classes de tipos das APIs do iOS CocoaTouch: os tipos C (normalmente chamados de tipos CoreFoundation) e os tipos Objective-C (eles derivam da classe NSObject).

Para cada tipo que espelha um tipo não gerenciado, é possível obter o objeto nativo por meio da propriedade [Handle](xref:Foundation.NSObject.Handle) .

Embora o mono forneça coleta de lixo para todos os seus objetos, o `Foundation.NSObject` implementa a interface [System. IDisposable](xref:System.IDisposable) . Isso significa que você pode liberar explicitamente os recursos de qualquer NSObject determinado sem precisar aguardar o coletor de lixo entrar. Isso é importante quando você está usando NSObjects pesadas, por exemplo, UIImages que pode conter ponteiros para grandes blocos de dados.

Se seu tipo precisa executar a finalização determinística, substitua o [método NSObject. Dispose (bool)](xref:Foundation.NSObject.Dispose(System.Boolean)) o parâmetro a ser descartado é "descarte de bool" e, se definido como verdadeiro, significa que o método Dispose está sendo chamado porque o usuário chamou explicitamente Dispose () no objeto. Se o valor for false, isso significa que o método Dispose (descartar bool) está sendo chamado do finalizador no thread do finalizador.

##### <a name="categories"></a>Categorias

A partir do Xamarin. iOS 8,10, é possível criar categorias Objective-C C#de.

Isso é feito usando o atributo `Category`, especificando o tipo a ser estendido como um argumento para o atributo. O exemplo a seguir fará com que a instância estenda o NSString.

```csharp
[Category (typeof (NSString))]
```

Cada método de categoria está usando o mecanismo normal para exportar métodos para Objective-C usando o atributo `Export`:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Todos os métodos de extensão gerenciados devem ser estáticos, mas é possível criar métodos de instância Objective-C usando a sintaxe padrão C#para métodos de extensão no:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

e o primeiro argumento para o método de extensão será a instância na qual o método foi invocado.

Exemplo completo:

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

Este exemplo adicionará um método de instância toUpper nativo à classe NSString, que pode ser invocado de Objective-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Um cenário em que isso é útil é adicionar um método a um conjunto inteiro de classes em sua base de código, por exemplo, fazer com que todas as instâncias de `UIViewController` relatem que podem girar:

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

##### <a name="preserveattribute"></a>Preservarattribute

Presirvaattribute é um atributo personalizado que é usado para informar mTouch – a ferramenta de implantação do Xamarin. iOS – para preservar um tipo, ou um membro de um tipo, durante a fase em que o aplicativo é processado para reduzir seu tamanho.

Cada membro que não está vinculado estaticamente pelo aplicativo está sujeito a ser removido. Portanto, esse atributo é usado para marcar Membros que não são referenciados estaticamente, mas que ainda são necessários para seu aplicativo.

Por exemplo, se você cria uma instância de tipos dinamicamente, convém preservar o construtor padrão de seus tipos. Se você usar a serialização de XML, você talvez queira preservar as propriedades de seus tipos.

Você pode aplicar esse atributo a todos os membros de um tipo ou ao tipo propriamente dito. Se você quiser preservar o tipo inteiro, poderá usar a sintaxe [preserve (mymembers = true)] no tipo.

#### <a name="uikit"></a>UIKit

O namespace [UIKit](xref:UIKit) contém um mapeamento de um para um para todos os componentes da interface do usuário que compõem Cocoatouch na forma de C# classes. A API foi modificada para seguir as convenções usadas no C# idioma.

C#os delegados são fornecidos para operações comuns. Consulte a seção [delegados](#delegates) para obter mais informações.

#### <a name="opengles"></a>OpenGLES

Para OpenGLs, distribuímos uma [versão modificada](xref:OpenTK) da API do [OpenTK](https://opentk.net/) , uma ligação orientada a objeto para OpenGL que foi modificada para usar tipos de dados e estruturas do CoreGraphics, bem como apenas expor a funcionalidade que está disponível no Ios.

A funcionalidade do OpenGLs 1,1 está disponível por meio do [tipo ES11.gl](xref:OpenTK.Graphics.ES11.GL).

A funcionalidade do OpenGLs 2,0 está disponível por meio do [tipo ES20.gl](xref:OpenTK.Graphics.ES20.GL).

A funcionalidade do OpenGLs 3,0 está disponível por meio do [tipo ES30.gl](xref:OpenTK.Graphics.ES30.GL).

### <a name="binding-design"></a>Design de associação

O Xamarin. iOS não é meramente uma associação à plataforma Objective-C subjacente. Ele estende o sistema de tipos .NET e o sistema de expedição C# para melhor Blend e Objective-C.

Assim como P/Invoke é uma ferramenta útil para invocar bibliotecas nativas no Windows e no Linux, ou como o suporte a IJW pode ser usado para interoperabilidade COM no Windows, o Xamarin C# . Ios estende o tempo de execução para dar suporte a objetos de associação para objetos Objective-C.

A discussão nas próximas seções não é necessária para os usuários que estão criando aplicativos Xamarin. iOS, mas ajudarão os desenvolvedores a entenderem como as coisas são feitas e irão auxiliá-las durante a criação de aplicativos mais complicados.

#### <a name="types"></a>Tipos

Onde fazia sentido, C# os tipos são expostos em vez de tipos de base de nível baixo, C# para o universo.  Isso significa que [a API usa o C# tipo "String" em vez de NSString](~/ios/internals/api-design/nsstring.md) e usa matrizes C# com rigidez de tipos em vez de expor NSArray.

Em geral, no design do Xamarin. iOS e Xamarin. Mac, o objeto de `NSArray` subjacente não é exposto. Em vez disso, o tempo de execução converte automaticamente `NSArray`s em matrizes fortemente tipadas de alguma classe `NSObject`. Portanto, o Xamarin. iOS não expõe um método de tipo fraco, como GetViews, para retornar um NSArray:

```csharp
NSArray GetViews ();
```

Em vez disso, a associação expõe um valor de retorno fortemente tipado, desta forma:

```csharp
UIView [] GetViews ();
```

Há alguns métodos expostos em `NSArray`, para os casos de canto em que você talvez queira usar um `NSArray` diretamente, mas seu uso é desencorajado na associação de API.

Além disso, na **API clássica** em vez de expor `CGRect`, `CGPoint` e `CGSize` da API CoreGraphics, substituímos aqueles pelas implementações `System.Drawing` `RectangleF`, `PointF` e `SizeF`, pois eles ajudarão os desenvolvedores a preservar o código OpenGL existente que usa OpenTK. Ao usar o novo **API unificada**de 64 bits, a API CoreGraphics deve ser usada.

#### <a name="inheritance"></a>Herança

O design de API do Xamarin. iOS permite que os desenvolvedores estendam tipos de Objective-C nativos da mesma C# forma que estendem um tipo, usando a palavra-chave "override" em uma classe derivada, bem como encadeando a implementação de C# base usando a palavra-chave "base".

Esse design permite que os desenvolvedores evitem lidar com seletores Objective-C como parte de seu processo de desenvolvimento, pois todo o sistema Objective-C já está encapsulado dentro das bibliotecas Xamarin. iOS.

#### <a name="types-and-interface-builder"></a>Tipos e Interface Builder

Quando você cria classes do .NET que são instâncias de tipos criadas por Interface Builder, você precisa fornecer um construtor que usa um único parâmetro `IntPtr`.
Isso é necessário para associar a instância de objeto gerenciado ao objeto não gerenciado.
O código consiste em uma única linha, como esta:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>Delega

Objective-C C# e tem significados diferentes para o delegado de palavra em cada idioma.

No mundo Objective-C e na documentação que você encontrará online sobre CocoaTouch, um delegado normalmente é uma instância de uma classe que responderá a um conjunto de métodos. Isso é muito semelhante a uma C# interface, com a diferença de que os métodos nem sempre são obrigatórios.

Esses delegados desempenham um papel importante no UIKit e em outras APIs do CocoaTouch. Eles são usados para realizar várias tarefas:

- Para fornecer notificações para seu código (semelhante à entrega de eventos C# no ou no GTK +).
- Para implementar modelos para controles de visualização de dados.
- Para orientar o comportamento de um controle.

O padrão de programação foi projetado para minimizar a criação de classes derivadas para alterar o comportamento de um controle. Essa solução é semelhante em relação ao que os outros kits de trabalho de GUI fizeram ao longo dos anos: sinais do GTK, slots Qt, eventos WinForms, eventos do WPF/Silverlight e assim por diante. Para evitar ter centenas de interfaces (uma para cada ação) ou exigir que os desenvolvedores implementem muitos métodos que não precisam, o Objective-C dá suporte a definições de métodos opcionais. Isso é diferente das C# interfaces que exigem a implementação de todos os métodos.

Nas classes Objective-C, você verá que as classes que usam esse padrão de programação expõem uma propriedade, geralmente chamada de `delegate`, que é necessária para implementar as partes obrigatórias da interface e zero, ou mais, das partes opcionais.

No Xamarin. iOS, são oferecidos três mecanismos mutuamente exclusivos para associar a esses delegados:

1. [Por meio de eventos](#via-events).
2. [Fortemente tipado por meio de uma propriedade `Delegate`](#strongly-typed-via-a-delegate-property)
3. [Tipo flexível por meio de uma propriedade `WeakDelegate`](#loosely-typed-via-the-weakdelegate-property)

Por exemplo, considere a classe UIWebView. Isso é expedido para uma instância UIWebViewDelegate, que é atribuída à propriedade delegar.

##### <a name="via-events"></a>Por meio de eventos

Para muitos tipos, o Xamarin. iOS criará automaticamente um delegado apropriado que encaminhará as chamadas C# de `UIWebViewDelegate` para eventos. Para `UIWebView`:

- O método webViewDidStartLoad é mapeado para o evento [UIWebView. LoadStarted](xref:UIKit.UIWebView.LoadStarted) .
- O método webViewDidFinishLoad é mapeado para o evento [UIWebView. Loadfinalized](xref:UIKit.UIWebView.LoadFinished) .
- O método WebView: didFailLoadWithError é mapeado para o evento [UIWebView. LoadError](xref:UIKit.UIWebView.LoadError) .

Por exemplo, esse programa simples registra os horários de início e término ao carregar uma exibição da Web:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Por meio de propriedades

Os eventos são úteis quando pode haver mais de um assinante para o evento. Além disso, os eventos são limitados a casos em que não há nenhum valor de retorno do código.

Para casos em que o código deve retornar um valor, optamos por propriedades. Isso significa que apenas um método pode ser definido em um determinado momento em um objeto.

Por exemplo, você pode usar esse mecanismo para descartar o teclado na tela no manipulador para um `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

A propriedade `ShouldReturn` do `UITextField`nesse caso usa como um argumento um delegado que retorna um valor bool e determina se o TextField deve fazer algo com o botão de retorno sendo pressionado. Em nosso método, retornamos *true* para o chamador, mas também removemos o teclado da tela (isso acontece quando textfield chama `ResignFirstResponder`).

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortemente tipado por meio de uma propriedade delegate

Se você preferir não usar eventos, poderá fornecer sua própria subclasse [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) e atribuí-la à propriedade [UIWebView. delegate](xref:UIKit.UIWebView.Delegate) . Depois que UIWebView. delegate tiver sido atribuído, o mecanismo de expedição do evento UIWebView não funcionará mais, e os métodos UIWebViewDelegate serão invocados quando os eventos correspondentes ocorrerem.

Por exemplo, esse tipo simples registra o tempo necessário para carregar uma exibição da Web:

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

O exemplo acima é usado em um código como este:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Acima, você criará um UIWebViewer e ele o instruirá a enviar mensagens para uma instância do notificador, uma classe que criamos para responder às mensagens.

Esse padrão também é usado para controlar o comportamento de determinados controles, por exemplo, no caso UIWebView, a propriedade [UIWebView. ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) permite que a instância de `UIWebView` controle se o `UIWebView` carregará ou não uma página.

O padrão também é usado para fornecer os dados sob demanda para alguns controles. Por exemplo, o controle [UITableView](xref:UIKit.UITableView) é um poderoso controle de renderização de tabela – e a aparência e o conteúdo são controlados por uma instância de um [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Tipo flexível por meio da propriedade WeakDelegate

Além da propriedade fortemente tipada, há também um delegado tipado fraco que permite ao desenvolvedor associar as coisas de forma diferente, se desejado.
Em qualquer lugar, uma propriedade de `Delegate` fortemente tipada é exposta na associação do Xamarin. iOS, uma propriedade `WeakDelegate` correspondente também é exposta.

Ao usar o `WeakDelegate`, você é responsável por decorar corretamente sua classe usando o atributo de [exportação](xref:Foundation.ExportAttribute) para especificar o seletor. Por exemplo:

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

Observe que depois que a propriedade `WeakDelegate` tiver sido atribuída, a propriedade `Delegate` não será usada. Além disso, se você implementar o método em uma classe base herdada que deseja [exportar], deverá torná-lo um método público.

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>Mapeamento do padrão de delegado Objective-C para C\#

Quando você vir exemplos de Objective-C semelhantes a:

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

Isso instrui o idioma a criar e construir uma instância da classe "SomethingDelegate" e atribuir o valor à propriedade delegate na variável foo. Esse mecanismo tem suporte do Xamarin. iOS e C# a sintaxe é:

```csharp
foo.Delegate = new SomethingDelegate ();
```

No Xamarin. iOS, fornecemos classes fortemente tipadas que mapeiam para as classes delegadas Objective-C. Para usá-los, você estará subclasseando e substituindo os métodos definidos pela implementação do Xamarin. iOS. Para obter mais informações sobre como eles funcionam, consulte a seção "modelos" abaixo.

### <a name="mapping-delegates-to-c"></a>Mapeando delegados para C\#

UIKit em geral usa delegados de Objective-C em duas formas.

O primeiro formulário fornece uma interface para o modelo de um componente. Por exemplo, como um mecanismo para fornecer dados sob demanda para uma exibição, como o recurso de armazenamento de dados para uma exibição de lista.  Nesses casos, você sempre deve criar uma instância da classe apropriada e atribuir a variável.

No exemplo a seguir, fornecemos o `UIPickerView` com uma implementação para um modelo que usa cadeias de caracteres:

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

O segundo formulário é fornecer notificação para eventos. Nesses casos, embora ainda exponhamos a API no formulário descrito acima, também fornecemos C# eventos, que devem ser mais simples de usar para operações rápidas e integrados com delegados anônimos e expressões lambda no. C#

Por exemplo, você pode assinar eventos de `UIAccelerometer`:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

As duas opções estão disponíveis onde fazem sentido, mas como um programador, você deve escolher uma ou outra. Se você criar sua própria instância de um Respondente/delegado com rigidez de tipos e atribuí- C# la, os eventos não serão funcionais. Se você usar os C# eventos, os métodos em sua classe de respondente/delegado nunca serão chamados.

O exemplo anterior que usou `UIWebView` pode ser escrito usando C# lambdas 3,0 como este:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>Respondendo a eventos

No código Objective-C, às vezes, manipuladores de eventos para vários controles e provedores de informações para vários controles, serão hospedados na mesma classe. Isso é possível porque as classes respondem às mensagens e, desde que as classes respondam às mensagens, é possível vincular objetos juntos.

Conforme detalhado anteriormente, o Xamarin. iOS dá suporte C# ao modelo de programação baseado em evento e ao padrão delegado Objective-C, no qual você pode criar uma nova classe que implementa o delegado e substitui os métodos desejados.

Também é possível dar suporte ao padrão de Objective-C, onde respondentes para várias operações diferentes são hospedados na mesma instância de uma classe. Para fazer isso, você precisará usar recursos de baixo nível da associação do Xamarin. iOS.

Por exemplo, se você quisesse que sua classe respondesse às `UITextFieldDelegate.textFieldShouldClear`: Message e a `UIWebViewDelegate.webViewDidStartLoad`: na mesma instância de uma classe, você precisaria usar a declaração de atributo [Export]:

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

Os C# nomes dos métodos não são importantes; Tudo o que importa são as cadeias de caracteres passadas para o atributo [Export].

Ao usar esse estilo de programação, verifique se os C# parâmetros correspondem aos tipos reais que o mecanismo de tempo de execução irá passar.

#### <a name="models"></a>Modelos

Em instalações de armazenamento UIKit ou em respondentes que são implementados usando classes auxiliares, elas geralmente são referenciadas no código Objective-C como delegados e são implementadas como protocolos.

Os protocolos Objective-C são como interfaces, mas oferecem suporte a métodos opcionais – ou seja, nem todos os métodos precisam ser implementados para que o protocolo funcione.

Há duas maneiras de implementar um modelo. Você pode implementá-lo manualmente ou usar as definições de tipo forte existentes.

O mecanismo manual é necessário quando você tenta implementar uma classe que não foi associada pelo Xamarin. iOS. É muito fácil:

- Sinalizar sua classe para o registro com o tempo de execução
- Aplique o atributo [Export] com o nome do seletor real em cada método que você deseja substituir
- Crie uma instância da classe e passe-a.

Por exemplo, as seguintes implementam apenas um dos métodos opcionais na definição do protocolo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

O nome do seletor Objective-C ("applicationDidFinishLaunching:") é declarado com o atributo de exportação e a classe é registrada com o atributo `[Register]`.

O Xamarin. iOS fornece declarações fortemente tipadas, prontas para uso, que não exigem associação manual. Para dar suporte a esse modelo de programação, o tempo de execução do Xamarin. iOS dá suporte ao atributo [Model] em uma declaração de classe. Isso informa ao tempo de execução que ele não deve conectar todos os métodos na classe, a menos que os métodos sejam explicitamente implementados.

Isso significa que, em UIKit, as classes que representam um protocolo com métodos opcionais são escritas da seguinte maneira:

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

Quando você deseja implementar um modelo que só implemente alguns dos métodos, tudo o que você precisa fazer é substituir os métodos nos quais está interessado e ignorar os outros métodos. O tempo de execução conectará apenas os métodos substituídos, não os métodos originais ao mundo Objective-C.

O equivalente ao exemplo manual anterior é:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

As vantagens são que não há necessidade de se aprofundar nos arquivos de cabeçalho Objective-C para localizar o seletor, os tipos dos argumentos ou o mapeamento C#para, e que você obtém o intellisense de Visual Studio para Mac, juntamente com tipos fortes

#### <a name="xib-outlets-and-c"></a>XIB saídas e C\#

> [!IMPORTANT]
> Esta seção explica a integração do IDE com saídas ao usar arquivos XIB. Ao usar o Xamarin Designer para iOS, isso é substituído inserindo-se um nome em **identidade > nome** na seção de propriedades do IDE, conforme mostrado abaixo:
>
> [![](images/designeroutlet.png "Entering an item Name in the iOS Designer")](images/designeroutlet.png#lightbox)
>
>Para obter mais informações sobre o designer do iOS, consulte a [introdução ao documento do designer do IOS](~/ios/user-interface/designer/introduction.md#how-it-works) .

Esta é uma descrição de baixo nível de como as informações C# se integram ao e são fornecidas para usuários avançados do Xamarin. Ios. Ao usar Visual Studio para Mac o mapeamento é feito automaticamente em segundo plano usando o código gerado no vôo para você.

Ao projetar sua interface do usuário com Interface Builder, você só estará criando a aparência do aplicativo e estabelecerá algumas conexões padrão. Se você quiser obter informações programaticamente, alterar o comportamento de um controle em tempo de execução ou modificar o controle em tempo de execução, será necessário associar alguns dos controles ao seu código gerenciado.

Isso é feito em algumas etapas:

1. Adicione a **declaração de tomada** ao **proprietário do arquivo**.
1. Conecte seu controle ao **proprietário do arquivo**.
1. Armazene a interface do usuário mais as conexões em seu arquivo XIB/NIB.
1. Carregue o arquivo NIB em tempo de execução.
1. Acesse a variável de tomada.

As etapas (1) a (3) são abordadas na documentação da Apple para a criação de interfaces com Interface Builder.

Ao usar o Xamarin. iOS, seu aplicativo precisará criar uma classe derivada de UIViewController. Ele é implementado da seguinte maneira:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Em seguida, para carregar o ViewController de um arquivo NIB, faça o seguinte:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Isso carrega a interface do usuário do NIB. Agora, para acessar as saídas, é necessário informar ao tempo de execução que queremos acessá-las. Para fazer isso, a subclasse `UIViewController` precisa declarar as propriedades e anotá-las com o atributo [Connect]. Dessa forma:

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

A implementação de propriedade é aquela que realmente busca e armazena o valor para o tipo nativo real.

Você não precisa se preocupar com isso ao usar Visual Studio para Mac e InterfaceBuilder. Visual Studio para Mac automaticamente espelha todas as saídas declaradas com código em uma classe parcial que é compilada como parte do seu projeto.

#### <a name="selectors"></a>Seletores

Um conceito básico de programação Objective-C é seletores. Muitas vezes, você entrará em APIs que exigem que você passe um seletor ou espera que seu código responda a um seletor.

Criar novos seletores no C# é muito fácil – basta criar uma nova instância da classe `ObjCRuntime.Selector` e usar o resultado em qualquer lugar na API que o exija. Por exemplo:

```csharp
var selector_add = new Selector ("add:plus:");
```

Para um C# método responder a uma chamada de seletor, ele deve herdar do tipo `NSObject` C# e o método deve ser decorado com o nome do seletor usando o atributo `[Export]`. Por exemplo:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Observe que os nomes de seletor **devem** corresponder exatamente, incluindo todos os dois-pontos intermediários e finais (":"), se estiverem presentes.

#### <a name="nsobject-constructors"></a>Construtores NSObject

A maioria das classes no Xamarin. iOS que derivam de `NSObject` expõem construtores específicos à funcionalidade do objeto, mas também expõem vários construtores que não são imediatamente óbvios.

Os construtores são usados da seguinte maneira:

```csharp
public Foo (IntPtr handle)
```

Esse construtor é usado para instanciar sua classe quando o tempo de execução precisa mapear sua classe para uma classe não gerenciada. Isso acontece quando você carrega um arquivo XIB/NIB.  Neste ponto, o tempo de execução do Objective-C criará um objeto no mundo não gerenciado, e esse construtor será chamado para inicializar o lado gerenciado.

Normalmente, tudo o que você precisa fazer é chamar o construtor base com o parâmetro Handle e, no corpo, fazer qualquer inicialização necessária.

```csharp
public Foo ()
```

Esse é o construtor padrão para uma classe e nas classes fornecidas pelo Xamarin. iOS, isso Inicializa a classe Foundation. NSObject e todas as classes entre e no final, encadeia isso para o método de `init` Objective-C na classe.

```csharp
public Foo (NSObjectFlag x)
```

Esse construtor é usado para inicializar a instância, mas impede que o código chame o método Objective-C "init" no final. Normalmente, você usa isso quando você já tiver registrado para inicialização (quando você usa `[Export]` no Construtor) ou quando você já fez a inicialização por outra média.

```csharp
public Foo (NSCoder coder)
```

Esse construtor é fornecido para os casos em que o objeto está sendo inicializado a partir de uma instância de NSCoding.

#### <a name="exceptions"></a>Exceções

O design da API do Xamarin. iOS não gera exceções de Objective C# -C como exceções. O design impõe que nenhum lixo seja enviado ao mundo Objective-C em primeiro lugar e que todas as exceções que devem ser produzidas sejam produzidas pela associação em si antes que dados inválidos sejam passados para o mundo Objective-C.

#### <a name="notifications"></a>Notificações

No iOS e no OS X, os desenvolvedores podem assinar as notificações que são transmitidas pela plataforma subjacente. Isso é feito usando o método `NSNotificationCenter.DefaultCenter.AddObserver`. O método `AddObserver` usa dois parâmetros; uma é a notificação que você deseja assinar; o outro é o método a ser invocado quando a notificação é gerada.

No Xamarin. iOS e no Xamarin. Mac, as chaves para as várias notificações são hospedadas na classe que dispara as notificações. Por exemplo, as notificações geradas pelo `UIMenuController` são hospedadas como `static NSString` Propriedades nas classes de `UIMenuController` que terminam com o nome "notificação".

### <a name="memory-management"></a>Gerenciamento de memória

O Xamarin. iOS tem um coletor de lixo que cuidará da liberação de recursos para você quando eles não estiverem mais em uso. Além do coletor de lixo, todos os objetos que derivam de `NSObject` implementam a interface `System.IDisposable`.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Expor a interface `IDisposable` é uma maneira conveniente de auxiliar os desenvolvedores na liberação de objetos que podem encapsular grandes blocos de memória (por exemplo, um `UIImage` pode parecer apenas um ponteiro inocente, mas pode estar apontando para uma imagem de 2 megabytes) e outros recursos importantes e finitos (como um buffer de decodificação de vídeo).

NSObject implementa a interface IDisposable e também o [padrão de descarte .net](https://msdn.microsoft.com/library/fs2xkftw.aspx). Isso permite aos desenvolvedores que a subclasse NSObject substitua o comportamento Dispose e libere seus próprios recursos sob demanda. Por exemplo, considere esse controlador de exibição que mantém um monte de imagens:

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

Quando um objeto gerenciado é Descartado, ele não é mais útil. Você ainda pode ter uma referência aos objetos, mas o objeto é para todas as intenções e finalidades inválidas neste ponto. Algumas APIs do .NET garantem isso lançando um ObjectDisposedException se você tentar acessar qualquer método em um objeto descartado, por exemplo:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Mesmo que você ainda possa acessar a variável "Image", ela é, na verdade, uma referência inválida e não aponta mais para o objeto Objective-C que ocupou a imagem.

Mas descartar um C# objeto no não significa que o objeto será necessariamente destruído. Tudo o que você faz é liberar a C# referência que tinha ao objeto. É possível que o ambiente Cocoa tenha mantido uma referência para seu próprio uso. Por exemplo, se você definir a propriedade Image de UIImageView como uma imagem e, em seguida, descartar a imagem, o UIImageView subjacente terá obtido sua própria referência e manterá uma referência a esse objeto até que ele seja concluído usando-o.

#### <a name="when-to-call-dispose"></a>Quando chamar Dispose

Você deve chamar Dispose Quando precisar de mono para livrar-se do seu objeto. Um possível caso de uso é quando o mono não tem conhecimento de que sua NSObject está realmente mantendo uma referência a um recurso importante, como memória, ou um pool de informações. Nesses casos, você deve chamar Dispose para liberar imediatamente a referência à memória, em vez de aguardar que o mono execute um ciclo de coleta de lixo.

Internamente, quando o mono cria [referências NSString C# de cadeias de caracteres](~/ios/internals/api-design/nsstring.md), ele as descartará imediatamente para reduzir a quantidade de trabalho que o coletor de lixo precisa fazer. Quanto menos objetos forem abordados, mais rápido será a execução do GC.

#### <a name="when-to-keep-references-to-objects"></a>Quando manter referências a objetos

Um efeito colateral de que o gerenciamento automático de memória tem é que o GC se livrar de objetos não utilizados, desde que não haja nenhuma referência a eles. Isso às vezes pode ter efeitos colaterais surpreendentes, por exemplo, se você criar uma variável local para manter seu controlador de exibição de nível superior ou sua janela de nível superior e, em seguida, os desaparecerem por trás de volta.

Se você não mantiver uma referência em suas variáveis estática ou de instância para seus objetos, o mono chamará o método Dispose () neles, e eles liberarão a referência ao objeto. Como essa pode ser a única referência pendente, o tempo de execução do Objective-C destruirá o objeto para você.

## <a name="related-links"></a>Links relacionados

- [Campos de associação](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
