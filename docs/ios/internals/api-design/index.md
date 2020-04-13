---
title: Xamarin.iOS API Design
description: Princípios orientadores que foram usados para arquitetar as APIs Xamarin.iOS e como estes se relacionam com o Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a2435b30b7d5b468fca6c55d295c87b9a0d20652
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304796"
---
# <a name="xamarinios-api-design"></a>Xamarin.iOS API Design

Além das bibliotecas de classe base principais que fazem parte do Mono, [xamarin.iOS](~/ios/index.yml) envia com vinculações para várias APIs do iOS para permitir que os desenvolvedores criem aplicativos nativos para iOS com Mono.

No núcleo do Xamarin.iOS, há um motor interop que conecta o mundo C# com o mundo Objective-C, bem como ligações para as APIs baseadas no iOS C como CoreGraphics e [OpenGL ES](#opengles).

O tempo de execução de baixo nível para se comunicar com o código Objective-C está no [MonoTouch.ObjCRuntime](#objcruntime). Além disso, são fornecidas vinculações para [Fundação,](#foundation)CoreFoundation e [UIKit.](#uikit)

## <a name="design-principles"></a>Princípios de design

Estes são alguns dos nossos princípios de design para as ligações Xamarin.iOS (eles também se aplicam ao Xamarin.Mac, as ligações Mono para Objective-C no macOS):

- Siga as [Diretrizes de Projeto-Quadro](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Permitir que os desenvolvedores subclassitem classes Objective-C:

  - Derivade de uma classe existente
  - Chame o construtor base para cadeia
  - Métodos de substituição devem ser feitos com o sistema de substituição de C#
  - A subclassificação deve funcionar com construções padrão C#

- Não exponha os desenvolvedores aos seletores Objective-C
- Forneça um mecanismo para chamar bibliotecas Objetivas-C arbitrárias
- Torne as tarefas objetivas-C comuns fáceis e difíceis de objetivo-C possíveis
- Expor propriedades Objective-C como propriedades C#
- Exponha uma API fortemente digitada:

  - Aumentar a segurança do tipo
  - Minimizar erros de tempo de execução
  - Obtenha IDE IntelliSense em tipos de retorno
  - Permite a documentação pop-up do IDE

- Incentivar a exploração in-IDE das APIs:

  - Por exemplo, em vez de expor uma matriz de tipo fraco como esta:

    ```objc
    NSArray *getViews
    ```

    Expor um tipo forte, como este:

    ```csharp
    NSView [] Views { get; set; }
    ```

    Isso dá ao Visual Studio para Mac a capacidade de fazer a `System.Array` auto-conclusão enquanto navega na API, disponibiliza todas as operações no valor devolvido e permite que o valor de retorno participe do LINQ.

- Tipos C# nativos:

  - [`NSString`Fica`string`](~/ios/internals/api-design/nsstring.md)
  - Turn `int` `uint` and parameters that should have been enums into C# `[Flags]` enumerations and C# enumerations with attributes
  - Em vez de `NSArray` objetos neutros de tipo, exponha as matrizes como matrizes fortemente digitadas.
  - Para eventos e notificações, dê aos usuários uma escolha entre:

    - Uma versão fortemente digitada por padrão
    - Uma versão fracamente digitada para casos de uso avançado

- Suporte ao padrão de delegado Objective-C:

  - C# sistema de eventos
  - Expor os delegados C# (lambdas, `System.Delegate`métodos anônimos e ) a APIs Objective-C como blocos

### <a name="assemblies"></a>Assemblies

Xamarin.iOS inclui uma série de conjuntos que constituem o *Perfil Xamarin.iOS*. A página [assembléias](~/cross-platform/internals/available-assemblies.md) tem mais informações.

### <a name="major-namespaces"></a>Principais Namespaces

#### <a name="objcruntime"></a>ObjCRuntime

O [namespace ObjCRuntime](xref:ObjCRuntime) permite que os desenvolvedores fazem a ponte entre C# e Objective-C.
Esta é uma nova ligação, projetada especificamente para o iOS, com base na experiência de Cacau# e Gtk#.

#### <a name="foundation"></a>Foundation

O namespace [da Fundação](xref:Foundation) fornece os tipos básicos de dados projetados para interoperar com a estrutura da Fundação Objective-C que faz parte do iOS e é a base para programação orientada a objetos no Objective-C.

Xamarin.iOS espelha em C# a hierarquia de classes do Objective-C. Por exemplo, a classe base Objetivo-C NSObject é utilizável a partir de C# via [Foundation.NSObject](xref:Foundation.NSObject).

Embora este namespace forneça vinculações para os tipos de Fundação Objective-C subjacentes, em alguns casos mapeamos os tipos subjacentes para tipos .NET. Por exemplo:

- Em vez de lidar com NSString e [NSArray,](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)o tempo de execução expõe-os como [strings](xref:System.String)C# e [matrizes](xref:System.Array)fortemente digitadas em toda a API.

- Várias APIs auxiliares são expostas aqui para permitir que os desenvolvedores vinculem APIs Objective-C de terceiros, outras APIs ou APIs do iOS que não estão atualmente vinculadas ao Xamarin.iOS.

Para obter mais detalhes sobre as APIs de vinculação, consulte a seção [Gerador de Ligação Xamarin.iOS.](~/cross-platform/macios/binding/binding-types-reference.md)

##### <a name="nsobject"></a>NSObject

O tipo [NSObject](xref:Foundation.NSObject) é a base para todas as vinculações Objective-C. Os tipos Xamarin.iOS espelham duas classes de tipos das APIs do iOS CocoaTouch: os tipos C (tipicamente referidos como tipos CoreFoundation) e os tipos Objective-C (todos eles derivam da classe NSObject).

Para cada tipo que espelha um tipo não gerenciado, é possível obter o objeto nativo através da propriedade [Handle.](xref:Foundation.NSObject.Handle)

Enquanto a Mono fornecerá coleta de `Foundation.NSObject` lixo para todos os seus objetos, a interface [System.IDisposable.](xref:System.IDisposable) Isso significa que você pode liberar explicitamente os recursos de qualquer NSObject sem ter que esperar o Coletor de Lixo iniciar o kick-in. Isso é importante quando você está usando NSObjects pesados, por exemplo, UIImages que podem conter ponteiros para grandes blocos de dados.

Se o seu tipo precisar realizar a finalização determinística, anule o [método NSObject.Dispose(bool)](xref:Foundation.NSObject.Dispose(System.Boolean)) O parâmetro para Descartar é "descarte bool", e se definido como verdadeiro significa que seu método Descarte está sendo chamado porque o usuário explicitamente chamado Descarte () no objeto. Se o valor for falso, isso significa que o método Descarte (descarte bool) está sendo chamado do finalizador no segmento finalizador.

##### <a name="categories"></a>Categorias

A partir de Xamarin.iOS 8.10 é possível criar categorias Objective-C a partir de C#.

Isso é feito `Category` usando o atributo, especificando o tipo para estender como um argumento para o atributo. O exemplo a seguir, por exemplo, estenderá o NSString.

```csharp
[Category (typeof (NSString))]
```

Cada método de categoria está usando o mecanismo normal para `Export` exportar métodos para O Objetivo-C usando o atributo:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Todos os métodos de extensão gerenciados devem ser estáticos, mas é possível criar métodos de instância Objective-C usando a sintaxe padrão para métodos de extensão em C#:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

e o primeiro argumento para o método de extensão será a instância em que o método foi invocado.

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

Este exemplo adicionará um método de instância nativo toUpper à classe NSString, que pode ser invocado a partir de Objective-C.

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

Um cenário em que isso é útil é adicionar um método a um conjunto `UIViewController` inteiro de classes em sua base de código, por exemplo, isso faria com que todas as instâncias relatassem que elas podem girar:

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

##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute é um atributo personalizado que é usado para dizer ao mtouch – a ferramenta de implantação Xamarin.iOS – para preservar um tipo, ou um membro de um tipo, durante a fase em que o aplicativo é processado para reduzir seu tamanho.

Cada membro que não está vinculado estaticamente pelo aplicativo está sujeito a ser removido. Assim, esse atributo é usado para marcar membros que não são referenciados estáticamente, mas que ainda são necessários pelo seu aplicativo.

Por exemplo, se você cria uma instância de tipos dinamicamente, convém preservar o construtor padrão de seus tipos. Se você usar a serialização de XML, você talvez queira preservar as propriedades de seus tipos.

Você pode aplicar esse atributo a todos os membros de um tipo ou ao tipo propriamente dito. Se você quiser preservar todo o tipo, você pode usar a sintaxe [Preserve (AllMembers = true)] no tipo.

#### <a name="uikit"></a>UiKit

O [namespace do UIKit](xref:UIKit) contém um mapeamento um-para-um para todos os componentes de IA que compõem o CocoaTouch na forma de classes C#. A API foi modificada para seguir as convenções usadas na língua C#.

C# delegados são fornecidos para operações comuns. Consulte a seção [de delegados](#delegates) para obter mais informações.

#### <a name="opengles"></a>OpenGLES

Para o OpenGLES, distribuímos uma [versão modificada](xref:OpenTK) da API [OpenTK,](https://opentk.net/) uma vinculação orientada a objetos para OpenGL que foi modificada para usar tipos e estruturas de dados CoreGraphics, além de expor apenas a funcionalidade disponível no iOS.

A funcionalidade OpenGLES 1.1 está disponível através do [ES11.GL tipo](xref:OpenTK.Graphics.ES11.GL).

A funcionalidade OpenGLES 2.0 está disponível através do [ES20.GL tipo](xref:OpenTK.Graphics.ES20.GL).

A funcionalidade OpenGLES 3.0 está disponível através do [tipo ES30.GL](xref:OpenTK.Graphics.ES30.GL).

### <a name="binding-design"></a>Projeto de vinculação

Xamarin.iOS não é apenas uma vinculação à plataforma Objetiva-C subjacente. Ele estende o sistema de tipo .NET e o sistema de despacho para melhor misturar C# e Objective-C.

Assim como o P/Invoke é uma ferramenta útil para invocar bibliotecas nativas no Windows e Linux, ou como o suporte ao IJW pode ser usado para interop COM no Windows, o Xamarin.iOS estende o tempo de execução para suportar objetos C# de ligação a objetos Objective-C.

A discussão nas próximas seções não é necessária para os usuários que estão criando aplicativos Xamarin.iOS, mas ajudará os desenvolvedores a entender como as coisas são feitas e irá ajudá-los ao criar aplicativos mais complicados.

#### <a name="types"></a>Tipos

Onde fazia sentido, os tipos C# são expostos em vez de tipos de Fundação de baixo nível, para o universo C#.  Isso significa que [a API usa o tipo C# "string" em vez de NSString](~/ios/internals/api-design/nsstring.md) e usa matrizes C# fortemente digitadas em vez de expor NSArray.

Em geral, no design Xamarin.iOS e Xamarin.Mac, o objeto subjacente `NSArray` não está exposto. Em vez disso, o `NSArray`tempo de execução converte `NSObject` automaticamente s em matrizes fortemente digitadas de alguma classe. Assim, o Xamarin.iOS não expõe um método de digitação fraca como o GetViews para retornar um NSArray:

```csharp
NSArray GetViews ();
```

Em vez disso, a vinculação expõe um valor de retorno fortemente digitado, como este:

```csharp
UIView [] GetViews ();
```

Existem alguns métodos expostos em `NSArray`, para os casos `NSArray` de canto onde você pode querer usar um diretamente, mas seu uso é desencorajado na vinculação da API.

Além disso, na **API clássica** `CGRect`em `CGPoint` `CGSize` vez de expor , e da API CoreGraphics, substituímos aqueles com as `System.Drawing` implementações, `RectangleF` `PointF` e `SizeF` como eles ajudariam os desenvolvedores a preservar o código OpenGL existente que usa OpenTK. Ao usar a nova **API Unificada**de 64 bits, a API CoreGraphics deve ser usada.

#### <a name="inheritance"></a>Herança

O design da API Xamarin.iOS permite que os desenvolvedores ampliem os tipos objetivos-C nativos da mesma forma que estenderiam um tipo C#, usando a palavra-chave "sobreposição" em uma classe derivada, bem como acorrentando até a implementação base usando a palavra-chave "base" C#.

Esse design permite que os desenvolvedores evitem lidar com seletores Objective-C como parte de seu processo de desenvolvimento, pois todo o sistema Objective-C já está envolvido dentro das bibliotecas Xamarin.iOS.

#### <a name="types-and-interface-builder"></a>Tipos e Construtor de Interfaces

Quando você cria classes .NET que são instâncias de tipos criados pelo Interface `IntPtr` Builder, você precisa fornecer um construtor que leve um único parâmetro.
Isso é necessário para vincular a instância do objeto gerenciado com o objeto não gerenciado.
O código consiste em uma única linha, como esta:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>Delegados

Objective-C e C# têm significados diferentes para a palavra delegado em cada língua.

No mundo Objective-C, e na documentação que você encontrará online sobre o CocoaTouch, um delegado é tipicamente uma instância de uma classe que responderá a um conjunto de métodos. Isso é muito semelhante a uma interface C#, com a diferença sendo que os métodos nem sempre são obrigatórios.

Esses delegados desempenham um papel importante no UIKit e em outras APIs do CocoaTouch. Eles são usados para realizar várias tarefas:

- Para fornecer notificações ao seu código (Semelhante à entrega de eventos em C# ou Gtk+).
- Para implementar modelos para controles de visualização de dados.
- Para conduzir o comportamento de um controle.

O padrão de programação foi projetado para minimizar a criação de classes derivadas para alterar o comportamento para um controle. Esta solução é semelhante em espírito ao que outros kits de ferramentas de GUI têm feito ao longo dos anos: sinais de Gtk, slots Qt, eventos Winforms, eventos WPF/Silverlight e assim por diante. Para evitar ter centenas de interfaces (uma para cada ação) ou exigir que os desenvolvedores implementem muitos métodos de que não precisam, o Objective-C suporta definições opcionais de métodos. Isso é diferente das interfaces C# que exigem que todos os métodos sejam implementados.

Nas classes Objective-C, você verá que as classes que usam `delegate`esse padrão de programação expõem uma propriedade, geralmente chamada, que é necessária para implementar as partes obrigatórias da interface e zero, ou mais, das peças opcionais.

No Xamarin.iOS são oferecidos três mecanismos mutuamente exclusivos para vincular a esses delegados:

1. [Através de eventos.](#via-events)
2. [Fortemente digitado `Delegate` através de uma propriedade](#strongly-typed-via-a-delegate-property)
3. [Vagamente digitado `WeakDelegate` através de uma propriedade](#loosely-typed-via-the-weakdelegate-property)

Por exemplo, considere a classe UIWebView. Isso é despachado para uma instância UIWebViewDelegate, que é atribuída à propriedade dele.

##### <a name="via-events"></a>Via Eventos

Para muitos tipos, o Xamarin.iOS criará automaticamente `UIWebViewDelegate` um delegado apropriado que encaminhará as chamadas para eventos C#. Para `UIWebView`:

- O método webViewDidStartLoad é mapeado para o evento [UIWebView.LoadStarted.](xref:UIKit.UIWebView.LoadStarted)
- O método webViewDidFinishLoad é mapeado para o evento [UIWebView.LoadFinished.](xref:UIKit.UIWebView.LoadFinished)
- O método webView:didFailLoadWithError é mapeado para o evento [UIWebView.LoadError.](xref:UIKit.UIWebView.LoadError)

Por exemplo, este programa simples registra os tempos de início e término ao carregar uma exibição web:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Via Propriedades

Eventos são úteis quando pode haver mais de um assinante do evento. Além disso, os eventos são limitados aos casos em que não há valor de retorno do código.

Para os casos em que o código é esperado para devolver um valor, optamos por propriedades. Isso significa que apenas um método pode ser definido em um determinado momento em um objeto.

Por exemplo, você pode usar este mecanismo para descartar o `UITextField`teclado na tela do manipulador para um :

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

A `UITextField`propriedade `ShouldReturn` ', neste caso, toma como argumento um delegado que retorna um valor bool e determina se o TextField deve fazer algo com o botão 'Return sendo pressionado. Em nosso método, retornamos *fiéis* ao chamador, mas também removemos o `ResignFirstResponder`teclado da tela (isso acontece quando o campo de texto chama ).

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortemente digitado através de uma propriedade delegada

Se você preferir não usar eventos, você pode fornecer sua própria subclasse [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) e atribuí-la à propriedade [UIWebView.Delegate.](xref:UIKit.UIWebView.Delegate) Uma vez que o UIWebView.Delegate tenha sido atribuído, o mecanismo de despacho de eventos UIWebView não funcionará mais e os métodos UIWebViewDelegate serão invocados quando os eventos correspondentes ocorrerem.

Por exemplo, este tipo simples registra o tempo necessário para carregar uma visualização web:

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

O acima é usado em código como este:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

O acima criará um UIWebViewer e ele irá instruí-lo a enviar mensagens para uma instância do Notificador, uma classe que criamos para responder às mensagens.

Esse padrão também é usado para controlar o comportamento de certos controles, por exemplo, no caso UIWebView, `UIWebView` a propriedade [UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) permite que a `UIWebView` instância controle se a página carregará uma página ou não.

O padrão também é usado para fornecer os dados sob demanda para alguns controles. Por exemplo, o controle [UITableView](xref:UIKit.UITableView) é um poderoso controle de renderização de tabela – e tanto o visual quanto o conteúdo são impulsionados por uma instância de um [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Digitado vagamente através da Propriedade Delegado Fraco

Além da propriedade fortemente digitada, há também um delegado de digitação fraca que permite ao desenvolvedor vincular as coisas de forma diferente, se desejar.
Em todos os lugares `Delegate` uma propriedade fortemente digitada é exposta na `WeakDelegate` vinculação do Xamarin.iOS, uma propriedade correspondente também é exposta.

Ao usar `WeakDelegate`o , você é responsável por decorar corretamente sua classe usando o atributo [Exportar](xref:Foundation.ExportAttribute) para especificar o seletor. Por exemplo:

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

Observe que `WeakDelegate` uma vez que a `Delegate` propriedade tenha sido atribuída, a propriedade não será usada. Além disso, se você implementar o método em uma classe base herdada que você deseja [Exportar], você deve torná-lo um método público.

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>Mapeamento do padrão de delegado Objetivo-C para C\#

Quando você vê amostras Objetivas-C que se parecem com isso:

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

Isso instrui o idioma a criar e construir uma instância da classe "SomethingDelegate" e atribuir o valor à propriedade do delegado na variável foo. Este mecanismo é suportado por Xamarin.iOS e C# a sintaxe é:

```csharp
foo.Delegate = new SomethingDelegate ();
```

No Xamarin.iOS fornecemos classes fortemente digitadas que mapeiam para as classes de delegados Objective-C. Para usá-los, você estará subclassificando e substituindo os métodos definidos pela implementação do Xamarin.iOS. Para obter mais informações sobre como eles funcionam, consulte a seção "Modelos" abaixo.

### <a name="mapping-delegates-to-c"></a>Mapeamento de Delegados para C\#

O UIKit em geral usa delegados Objective-C em duas formas.

O primeiro formulário fornece uma interface para o modelo de um componente. Por exemplo, como um mecanismo para fornecer dados sob demanda para uma exibição, como o mecanismo de armazenamento de dados para uma exibição de lista.  Nesses casos, você deve sempre criar uma instância da classe adequada e atribuir a variável.

No exemplo a seguir, `UIPickerView` fornecemos a implementação de um modelo que usa strings:

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

O segundo formulário é fornecer notificação para eventos. Nesses casos, embora ainda exponhamos a API na forma descrita acima, também fornecemos eventos C#, que devem ser mais simples de usar para operações rápidas e integrados com delegados anônimos e expressões lambda em C#.

Por exemplo, você `UIAccelerometer` pode se inscrever em eventos:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

As duas opções estão disponíveis onde fazem sentido, mas como programador você deve escolher uma ou outra. Se você criar sua própria instância de um respondente/delegado fortemente digitado e atribuí-lo, os eventos C# não serão funcionais. Se você usar os eventos C#, os métodos da sua classe de responder/delegado nunca serão chamados.

O exemplo anterior `UIWebView` usado pode ser escrito usando lambdas c# 3.0 como este:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>Respondendo a eventos

No código Objective-C, às vezes os manipuladores de eventos para múltiplos controles e provedores de informações para controles múltiplos, serão hospedados na mesma classe. Isso é possível porque as aulas respondem às mensagens, e enquanto as aulas respondem às mensagens, é possível vincular objetos juntos.

Como detalhado anteriormente, o Xamarin.iOS suporta tanto o modelo de programação baseado em eventos C# quanto o padrão de delegado Objective-C, onde você pode criar uma nova classe que implemente o delegado e sobrepõe os métodos desejados.

Também é possível suportar o padrão Objective-C onde os respondentes para várias operações diferentes são todos hospedados na mesma instância de uma classe. Para fazer isso, porém, você terá que usar recursos de baixo nível da vinculação Xamarin.iOS.

Por exemplo, se você quisesse que `UITextFieldDelegate.textFieldShouldClear`sua classe `UIWebViewDelegate.webViewDidStartLoad`respondesse tanto à : mensagem quanto à: na mesma instância de uma classe, você teria que usar a declaração de atributo [Exportar]:

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

Os nomes C# para os métodos não são importantes; tudo o que importa são as strings passadas para o atributo [Export].

Ao usar este estilo de programação, certifique-se de que os parâmetros C# correspondem aos tipos reais que o motor de tempo de execução passará.

#### <a name="models"></a>Modelos

Em instalações de armazenamento UIKit, ou em respondentes que são implementados usando classes auxiliares, estes geralmente são referidos no código Objective-C como delegados, e eles são implementados como protocolos.

Os protocolos Objective-C são como interfaces, mas suportam métodos opcionais – ou seja, nem todos os métodos precisam ser implementados para que o protocolo funcione.

Há duas maneiras de implementar um modelo. Você pode implementá-lo manualmente ou usar as definições fortemente digitadas existentes.

O mecanismo manual é necessário quando você tenta implementar uma classe que não tenha sido vinculada ao Xamarin.iOS. É muito fácil de fazer:

- Marque sua classe para registro com o tempo de execução
- Aplique o atributo [Exportar] com o nome do seletor real em cada método que você deseja substituir
- Instanciar a classe, e passá-la.

Por exemplo, o seguinte implementar apenas um dos métodos opcionais na definição do protocolo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

O nome do seletor Objective-C ("applicationDidFinishLaunching:") é declarado com `[Register]` o atributo Exportar e a classe é registrada com o atributo.

Xamarin.iOS fornece declarações fortemente digitadas, prontas para uso, que não requerem vinculação manual. Para suportar este modelo de programação, o tempo de execução xamarin.iOS suporta o atributo [Modelo] em uma declaração de classe. Isso informa o tempo de execução que não deve conectar todos os métodos da classe, a menos que os métodos sejam explicitamente implementados.

Isso significa que, no UIKit, as classes que representam um protocolo com métodos opcionais são escritas assim:

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

Quando você quer implementar um modelo que só implemente alguns dos métodos, tudo o que você precisa fazer é substituir os métodos que você está interessado e ignorar os outros métodos. O tempo de execução só ligará os métodos sobreescritos, não os métodos originais ao mundo Objetivo-C.

O equivalente à amostra manual anterior é:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

As vantagens são que não há necessidade de cavar nos arquivos de cabeçalho Objective-C para encontrar o seletor, os tipos dos argumentos ou o mapeamento para C#, e que você obtenha intellisense do Visual Studio para Mac, juntamente com tipos fortes

#### <a name="xib-outlets-and-c"></a>XiB Outlets e C\#

> [!IMPORTANT]
> Esta seção explica a integração do IDE com as tomadas ao usar arquivos XIB. Ao usar o Xamarin Designer para iOS, tudo isso é substituído inserindo um nome em **Identidade > Nome** na seção Propriedades do seu IDE, conforme mostrado abaixo:
>
> [![](images/designeroutlet.png "Entering an item Name in the iOS Designer")](images/designeroutlet.png#lightbox)
>
>Para obter mais informações sobre o iOS Designer, por favor, revise a Introdução ao documento [do iOS Designer.](~/ios/user-interface/designer/introduction.md#how-it-works)

Esta é uma descrição de baixo nível de como os Outlets se integram com c# e é fornecido para usuários avançados do Xamarin.iOS. Ao usar o Visual Studio para Mac, o mapeamento é feito automaticamente nos bastidores usando código gerado no voo para você.

Quando você projeta sua interface de usuário com o Interface Builder, você estará apenas projetando o visual do aplicativo e estabelecerá algumas conexões padrão. Se você quiser buscar informações programáticamente, alterar o comportamento de um controle em tempo de execução ou modificar o controle em tempo de execução, é necessário vincular alguns dos controles ao seu código gerenciado.

Isso é feito em alguns passos:

1. Adicione a **declaração de saída** ao **proprietário do seu Arquivo**.
1. Conecte seu controle ao **proprietário do Arquivo**.
1. Armazene a ui mais as conexões no arquivo XIB/NIB.
1. Carregue o arquivo NIB em tempo de execução.
1. Acesse a variável de saída.

As etapas (1) a (3) estão cobertas na documentação da Apple para interfaces de construção com o Interface Builder.

Ao usar o Xamarin.iOS, seu aplicativo precisará criar uma classe derivada do UIViewController. É implementado assim:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Em seguida, para carregar seu ViewController a partir de um arquivo NIB, você faz isso:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Isso carrega a interface do usuário do NIB. Agora, para acessar as tomadas, é necessário informar o tempo de execução que queremos acessá-los. Para fazer isso, a `UIViewController` subclasse precisa declarar as propriedades e anotá-las com o atributo [Conectar]. Dessa forma:

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

A implementação da propriedade é aquela que realmente busca e armazena o valor para o tipo nativo real.

Você não precisa se preocupar com isso ao usar o Visual Studio para Mac e InterfaceBuilder. O Visual Studio for Mac espelha automaticamente todas as tomadas declaradas com código em uma classe parcial que é compilada como parte do seu projeto.

#### <a name="selectors"></a>Seletores

Um conceito central de programação Objective-C são seletores. Muitas vezes você se depara com APIs que exigem que você passe um seletor ou espera que seu código responda a um seletor.

Criar novos seletores em C# é muito fácil – `ObjCRuntime.Selector` basta criar uma nova instância da classe e usar o resultado em qualquer lugar da API que o exija. Por exemplo:

```csharp
var selector_add = new Selector ("add:plus:");
```

Para um método C# responder a uma chamada `NSObject` do seletor, ele deve herdar do `[Export]` tipo e o método C# deve ser decorado com o nome do seletor usando o atributo. Por exemplo:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Observe que os nomes seletores **devem** corresponder exatamente, incluindo todos os cólons intermediários e de arrasto (":"), se presentes.

#### <a name="nsobject-constructors"></a>Construtores NSObject

A maioria das classes em Xamarin.iOS que derivam `NSObject` exporá construtores específicos da funcionalidade do objeto, mas eles também exporão vários construtores que não são imediatamente óbvios.

Os construtores são usados da seguinte forma:

```csharp
public Foo (IntPtr handle)
```

Este construtor é usado para instanciar sua classe quando o tempo de execução precisa mapear sua classe para uma classe não gerenciada. Isso acontece quando você carrega um arquivo XIB/NIB.  Neste ponto, o tempo de execução Objective-C terá criado um objeto no mundo não gerenciado, e este construtor será chamado para inicializar o lado gerenciado.

Normalmente, tudo o que você precisa fazer é chamar o construtor base com o parâmetro da alça, e no corpo, fazer qualquer inicialização que seja necessária.

```csharp
public Foo ()
```

Este é o construtor padrão para uma classe, e no Xamarin.iOS forneceu aulas, isso inicializa a classe Foundation.NSObject e todas `init` as classes entre elas, e no final, acorrenta isso ao método Objective-C na classe.

```csharp
public Foo (NSObjectFlag x)
```

Este construtor é usado para inicializar a instância, mas impedir que o código chame o método Objective-C de "init" no final. Você normalmente usa isso quando já se registrou para `[Export]` inicialização (quando você usa em seu construtor) ou quando você já fez sua inicialização através de outra média.

```csharp
public Foo (NSCoder coder)
```

Este construtor é fornecido para os casos em que o objeto está sendo inicializado a partir de uma instância NSCoding.

#### <a name="exceptions"></a>Exceções

O design da API Xamarin.iOS não aumenta as exceções Objective-C como exceções C#. O projeto impõe que nenhum lixo seja enviado ao mundo Objetivo-C em primeiro lugar e que quaisquer exceções que devem ser produzidas sejam produzidas pela própria vinculação antes que os dados inválidos sejam passados para o mundo Objetivo-C.

#### <a name="notifications"></a>Notificações

Tanto no iOS quanto no OS X, os desenvolvedores podem assinar notificações que são transmitidas pela plataforma subjacente. Isso é feito `NSNotificationCenter.DefaultCenter.AddObserver` usando o método. O `AddObserver` método leva dois parâmetros; uma delas é a notificação à qual você deseja se inscrever; o outro é o método a ser invocado quando a notificação é levantada.

Tanto no Xamarin.iOS quanto no Xamarin.Mac, as chaves para as várias notificações estão hospedadas na classe que aciona as notificações. Por exemplo, as notificações `UIMenuController` levantadas `static NSString` pelo são `UIMenuController` hospedadas como propriedades nas classes que terminam com o nome "Notificação".

### <a name="memory-management"></a>Gerenciamento de memória

Xamarin.iOS tem um coletor de lixo que cuidará da liberação de recursos para você quando eles não estiverem mais em uso. Além do coletor de lixo, todos `NSObject` os `System.IDisposable` objetos que derivam da implementação da interface.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Expor a `IDisposable` interface é uma maneira conveniente de ajudar os desenvolvedores na liberação de `UIImage` objetos que podem encapsular grandes blocos de memória (por exemplo, um pode parecer apenas um ponteiro inocente, mas pode estar apontando para uma imagem de 2 megabytes) e outros recursos importantes e finitos (como um buffer de decodificação de vídeo).

O NSObject implementa a interface IDisposable e também o [padrão .NET Dispose](https://msdn.microsoft.com/library/fs2xkftw.aspx). Isso permite que os desenvolvedores que subclasse NSObject sobreponham o comportamento de Descarte e liberem seus próprios recursos sob demanda. Por exemplo, considere este controlador de exibição que mantém em torno de um monte de imagens:

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

Quando um objeto gerenciado é eliminado, ele não é mais útil. Você ainda pode ter uma referência aos objetos, mas o objeto é para todos os efeitos inválidos neste momento. Algumas APIs .NET garantem isso lançando uma 'ObjectDisposedException' se você tentar acessar quaisquer métodos em um objeto descartado, por exemplo:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Mesmo que você ainda possa acessar a variável "imagem", ela é realmente uma referência inválida e não aponta mais para o objeto Objective-C que segurava a imagem.

Mas descartar um objeto em C# não significa que o objeto será necessariamente destruído. Tudo o que você faz é liberar a referência que C# tinha para o objeto. É possível que o ambiente cacaueiro tenha mantido uma referência em torno de seu próprio uso. Por exemplo, se você definir a propriedade Image de um UIImageView em uma imagem e, em seguida, descartar a imagem, o UIImageView subjacente terá tomado sua própria referência e manterá uma referência a este objeto até que ele seja concluído usando-o.

#### <a name="when-to-call-dispose"></a>Quando chamar Descartar

Você deve chamar Descarte quando precisar de Mono para se livrar do seu objeto. Um possível caso de uso é quando a Mono não tem conhecimento de que seu NSObject está realmente segurando uma referência a um recurso importante como memória ou um pool de informações. Nesses casos, você deve ligar para descartar para liberar imediatamente a referência à memória, em vez de esperar que mono realize um ciclo de coleta de lixo.

Internamente, quando a Mono cria [referências NSString a partir de strings C#,](~/ios/internals/api-design/nsstring.md)ele irá descartá-las imediatamente para reduzir a quantidade de trabalho que o coletor de lixo tem que fazer. Quanto menos objetos ao redor para lidar, mais rápido o GC será executado.

#### <a name="when-to-keep-references-to-objects"></a>Quando manter referências a objetos

Um efeito colateral que o gerenciamento automático de memória tem é que o GC se livrará de objetos não utilizados, desde que não haja referências a eles. Isso às vezes pode ter efeitos colaterais surpreendentes, por exemplo, se você criar uma variável local para segurar seu controlador de visualização de nível superior, ou sua janela de nível superior, e, em seguida, tê-los desaparecer atrás de suas costas.

Se você não mantiver uma referência em suas variáveis estáticas ou instâncias para seus objetos, a Mono chamará alegremente o método Descarte() neles, e eles liberarão a referência ao objeto. Uma vez que esta pode ser a única referência excepcional, o tempo de execução Objective-C destruirá o objeto para você.

## <a name="related-links"></a>Links relacionados

- [Campos de Ligação](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
