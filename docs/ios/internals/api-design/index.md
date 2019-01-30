---
title: Xamarin.iOS API Design
description: Este documento descreve alguns dos princípios de orientação usada para projetar as APIs de Xamarin.iOS e como eles se relacionam com Objective-C.
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 75904ad91df7795c538e736eabb6c6000847b449
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233647"
---
# <a name="xamarinios-api-design"></a>Xamarin.iOS API Design

Além das bibliotecas de classes Base que fazem parte do Mono, principais [xamarin. IOS](http://www.xamarin.com/iOS) é fornecido com associações para várias APIs para permitir que os desenvolvedores criem aplicativos do iOS nativos com Mono do iOS.

A essência do xamarin. IOS, há um mecanismo de interoperabilidade que liga o mundo em c# com o mundo de Objective-C, bem como as associações para APIs baseadas em C, como CoreGraphics do iOS e [OpenGL ES](#OpenGLES).

O tempo de execução de baixo nível para se comunicar com o código Objective-C está em [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). Na parte superior, associações para [Foundation](#MonoTouch.Foundation), CoreFoundation, e [UIKit](#MonoTouch.UIKit) são fornecidos.

## <a name="design-principles"></a>Princípios de design

Estes são alguns dos nossos princípios de design para as associações do xamarin. IOS (eles também se aplicam a xamarin. Mac, as associações Mono para Objective-C no macOS):

- Siga o [as diretrizes de Design do Framework](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Permitir que os desenvolvedores classes subclasse Objective-C:

  - Derivar de uma classe existente
  - Chame o construtor de base para a cadeia
  - Substituindo métodos deve ser feito com o sistema de substituição do #
  - A criação de subclasses deve trabalhar com construções de linguagem c# padrão

- Não exponha os desenvolvedores a seletores de Objective-C
- Fornecem um mecanismo para chamar arbitrárias bibliotecas de Objective-C
- Tornam as tarefas comuns de Objective-C fácil e difícil possível de tarefas de Objective-C
- Expor propriedades de Objective-C como propriedades do c#
- Expor uma API fortemente tipada:

  - Aumentar a segurança de tipo
  - Minimizar os erros de tempo de execução
  - Obter o IntelliSense do IDE em tipos de retorno
  - Permite a documentação de pop-up do IDE

- Incentive a explorar as APIs no IDE:

  - Por exemplo, em vez de expor uma matriz com tipagem fraca como este:
    
    ```objc
    NSArray *getViews
    ```
    Expor um tipo forte, como este:
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    Isso oferece o Visual Studio para Mac a capacidade de fazer o preenchimento automático ao procurar a API, faz com que todos os `System.Array` operações disponíveis no valor retornado e permite que o valor de retorno participar de LINQ.

- Tipos c# nativos:

  - [`NSString` torna-se `string`](~/ios/internals/api-design/nsstring.md)
  - Transforme `int` e `uint` parâmetros que devem ter sido enums em c# enumerações e enumerações do c# com `[Flags]` atributos
  - Em vez de tipo neutro `NSArray` expor de objetos, matrizes como matrizes fortemente tipada.
  - Para eventos e notificações, dar aos usuários uma escolha entre:

    - Uma versão fortemente tipada, por padrão
    - Uma versão com tipagem fraca para casos de uso avançado

- Padrão de representante de suporte de Objective-C:

    - Sistema de eventos em C#
    - Expor delegados c# (lambdas, métodos anônimos, e `System.Delegate`) às APIs do Objective-C como blocos

### <a name="assemblies"></a>Assemblies

Xamarin. IOS inclui uma série de assemblies que constituem o *perfil do xamarin. IOS*. O [Assemblies](~/cross-platform/internals/available-assemblies.md) página tem mais informações.

### <a name="major-namespaces"></a>Namespaces do principal 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

O [ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/) namespace permite que os desenvolvedores mundos entre c# e Objective-C.
Isso é uma nova associação, projetada especificamente para o iOS, com base na experiência do Cocoa # e Gtk #.

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

O [Foundation](xref:Foundation) namespace fornece os tipos de dados básica é projetada para interoperar com a estrutura de base de Objective-C que faz parte do iOS e é a base para Objective-C. de programação orientada a objeto

Xamarin. IOS espelha em c#, a hierarquia de classes do Objective-C. Por exemplo, a classe base Objective-C [NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) possam ser usados em c# via [Foundation.NSObject](xref:Foundation.NSObject).

Embora esse namespace fornece associações para os tipos subjacentes do Foundation Objective-C, em alguns casos Mapeamos os tipos subjacentes para tipos .NET. Por exemplo:

- Em vez de tratar [NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) e [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), o tempo de execução expõe como c# [cadeia de caracteres](xref:System.String)s e fortemente tipada [matriz](xref:System.Array)s em toda a API.

- APIs de vários auxiliares são apresentados para permitir que os desenvolvedores associar as APIs do Objective-C, outros iOS APIs ou APIs que não estejam atualmente ligadas por xamarin. IOS de terceiros.

Para obter mais detalhes sobre as APIs de associação, consulte o [gerador de associação do xamarin. IOS](~/cross-platform/macios/binding/binding-types-reference.md) seção.


##### <a name="nsobject"></a>NSObject

O [NSObject](xref:Foundation.NSObject) tipo é a base para todas as associações do Objective-C. Tipos de xamarin. IOS espelham duas classes de tipos do CocoaTouch APIs do iOS: os tipos de C (normalmente conhecidos como tipos de CoreFoundation) e os tipos de Objective-C (eles todos derivam da classe de NSObject).

Para cada tipo que espelha um tipo não gerenciado, é possível obter o objeto nativo por meio de [manipular](xref:Foundation.NSObject.Handle) propriedade.

Enquanto o Mono fornecerá a coleta de lixo para todos os seus objetos, o `Foundation.NSObject` implementa o [System. IDisposable](xref:System.IDisposable) interface. Isso significa que você pode liberar explicitamente os recursos de qualquer determinado NSObject sem ter de esperar o coletor de lixo para lançamento no. Isso é importante quando você estiver usando NSObjects pesada, por exemplo, UIImages que pode conter os ponteiros para blocos grandes de dados.

Se seu tipo precisa executar finalização determinística, substituir os [NSObject.Dispose(bool) método](xref:Foundation.NSObject.Dispose(System.Boolean)) o parâmetro Dispose for "bool disposing", e se definido como true, ele significa que seu método Dispose está sendo chamado porque o usuário explicitamente chamado Dispose () no objeto. Se o valor for false, isso significa que seu método Dispose (bool disposing) está sendo chamado no finalizador no thread do finalizador. []()


##### <a name="categories"></a>Categorias

Começando com o xamarin. IOS 8.10 é possível criar categorias de Objective-C do c#.

Isso é feito usando o `Category` atributo, especificando o tipo a ser estendido como um argumento para o atributo. Por exemplo, o exemplo a seguir estenderá NSString.

    [Category (typeof (NSString))]

Cada método de categoria é usando o mecanismo normal para a exportação de métodos para Objective-C usando o `Export` atributo:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Todos os métodos de extensão gerenciada devem ser estáticos, mas é possível criar métodos de instância de Objective-C usando a sintaxe padrão para os métodos de extensão em c#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

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

Este exemplo irá adicionar um método de instância toUpper nativo para a classe NSString, que pode ser chamada do Objective-C.

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

Um cenário em que isso é útil é adicionar um método para todo um conjunto de classes em sua base de código, por exemplo, isso seria fazer todas as `UIViewController` instâncias de relatório que pode girar:

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

PreserveAttribute é um atributo personalizado que é usado para informar o mtouch – a ferramenta de implantação do xamarin. IOS – para preservar um tipo ou membro de um tipo, durante a fase de quando o aplicativo é processado para reduzir seu tamanho.

Cada membro que não está vinculado estaticamente pelo aplicativo está sujeito a ser removido. Portanto, esse atributo é usado para marcar membros que não são referenciados estaticamente, mas que ainda são necessários para seu aplicativo.

Por exemplo, se você cria uma instância de tipos dinamicamente, convém preservar o construtor padrão de seus tipos. Se você usar a serialização de XML, você talvez queira preservar as propriedades de seus tipos.

Você pode aplicar esse atributo a todos os membros de um tipo ou ao tipo propriamente dito. Se você quiser preservar o tipo inteiro, você pode usar a sintaxe [preservar (AllMembers = true)] no tipo.

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

O [UIKit](xref:UIKit) namespace contém um mapeamento individual para todos os componentes de interface do usuário que compõem o CocoaTouch na forma de classes c#. A API foi modificada para seguir as convenções usadas na linguagem c#.

Delegados c# são fornecidos para operações comuns. Consulte a [delegados](#Delegates) seção para obter mais informações.

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

Para OpenGLES, podemos distribuir uma [versão modificada](https://developer.xamarin.com/api/namespace/OpenTK/) da [OpenTK](http://www.opentk.com/) API, uma ligação orientada a objeto OpenGL foi modificado para usar estruturas e tipos de dados CoreGraphics, bem como expor somente o funcionalidade que está disponível no iOS.

A funcionalidade de OpenGLES 1.1 está disponível por meio do tipo ES11.GL, documentado [aqui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/) tipo.

Funcionalidade OpenGLES 2.0 está disponível por meio do tipo ES20.GL, documentado [aqui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/) tipo.

Funcionalidade OpenGLES 3.0 está disponível por meio do tipo ES30.GL, documentado [aqui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/) tipo.


### <a name="binding-design"></a>Design de associação

Xamarin. IOS não é meramente uma associação para a plataforma subjacente do Objective-C. Ele estende o sistema de tipos do .NET e o sistema de expedição para melhor blend c# e Objective-C.

Assim como a P/Invoke é uma ferramenta útil para invocar as bibliotecas nativas no Windows e Linux, ou como IJW suporte pode ser usado para a interoperabilidade COM no Windows, xamarin. IOS estende o tempo de execução para dar suporte a c# objetos de associação a objetos de Objective-C.

A discussão nas próximas seções não é necessário para os usuários que estão criando aplicativos xamarin. IOS, mas ajudarão os desenvolvedores a entender como as coisas são feitas e irá ajudá-los durante a criação de aplicativos mais complicados.



#### <a name="types"></a>Tipos

Tipos do c# em que fazia sentido, são expostos em vez de baixo nível tipos de base para o universo c#.  Isso significa que [a API usa o tipo "string" c# em vez de NSString](~/ios/internals/api-design/nsstring.md) e usa matrizes com rigidez de tipos do c# em vez de expor NSArray.

Em geral, no design do xamarin. IOS e xamarin. Mac, subjacente `NSArray` objeto não é exposto. Em vez disso, o tempo de execução automaticamente converte `NSArray`s para matrizes com rigidez de tipos de alguns `NSObject` classe. Portanto, xamarin. IOS não expõe um método com tipagem fraca como GetViews para retornar um NSArray:

```csharp
NSArray GetViews ();
```

Em vez disso, a associação expõe um valor de retorno fortemente tipado, como este:

```csharp
UIView [] GetViews ();
```

Há alguns dos métodos expostos na `NSArray`, para os casos de canto em que você talvez queira usar um `NSArray` diretamente, mas seu uso não é recomendado na associação de API.

Além disso, na **API clássica** em vez de expor `CGRect`, `CGPoint` e `CGSize` da API CoreGraphics, substituímos aqueles com o `System.Drawing` implementações `RectangleF`, `PointF`e `SizeF` pois elas seriam ajudam os desenvolvedores preservam código OpenGL existente que usa OpenTK. Ao usar o novo 64-bit **API unificada**, a API CoreGraphics deve ser usada.

<a name="Inheritance" />

#### <a name="inheritance"></a>Herança

O design de API do xamarin. IOS permite aos desenvolvedores estender tipos nativos do Objective-C da mesma forma que eles seriam estender um tipo c#, usando a palavra-chave "override" em uma classe derivada, bem como encadeamento de para a implementação base usando a palavra-chave "base" do c#.

Esse design permite que os desenvolvedores a evitar lidar com seletores de Objective-C como parte de seu processo de desenvolvimento, porque todo o sistema de Objective-C já é encapsulado dentro de bibliotecas do xamarin. IOS.


#### <a name="types-and-interface-builder"></a>Tipos e Interface Builder

Quando você cria classes .NET que são instâncias de tipos criadas pelo construtor de Interface, você precisa fornecer um construtor que aceita um único `IntPtr` parâmetro.
Isso é necessário para associar a instância do objeto gerenciado com o objeto não gerenciado.
O código consiste em uma única linha, como este:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>Delegados

Objective-C e c# têm significados diferentes para o delegado do word em cada linguagem.

No mundo do Objective-C e na documentação que você encontrará online sobre CocoaTouch, um delegado é normalmente uma instância de uma classe que irá responder a um conjunto de métodos. Isso é muito semelhante a uma interface c#, com a diferença é que os métodos nem sempre são obrigatórios.

Esses delegados desempenham um papel importante no UIKit e outras APIs CocoaTouch. Eles são usados para realizar diversas tarefas:

-  Para fornecer notificações para seu código (assim como entrega de eventos em c# ou Gtk +).
-  Para implementar os modelos para controles de visualização de dados.
-  Para direcionar o comportamento de um controle.


O padrão de programação foi projetado para minimizar a criação de classes derivadas para alterar o comportamento de um controle. Essa solução é semelhante no espírito ao longo dos anos têm feito outros kits de ferramentas de GUI: GTK sinais, slots de Qt, eventos de Winforms, WPF/Silverlight eventos e assim por diante. Para evitar ter centenas de interfaces (uma para cada ação) ou exigir que os desenvolvedores implementar muitos métodos que eles não precisam, Objective-C oferece suporte a definições de método opcional. Isso é diferente de interfaces do c# que exigem a todos os métodos a serem implementados.

Classes de Objective-C, você verá que as classes que usam esse padrão de programação expõem uma propriedade, geralmente chamada `delegate`, que é necessário para implementar partes obrigatórias da interface e partes de zero ou mais dos opcional.

No xamarin. IOS três mecanismos mutuamente exclusivos para associar a esses delegados são oferecidos:

1.  [Por meio de eventos](#Via_Events).
2.  [Com rigidez de tipos por meio de um `Delegate` propriedade](#StrongDelegate)
3.  [Fracamente tipados por meio de um `WeakDelegate` propriedade](#WeakDelegate)

Por exemplo, considere a [UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) classe. Isso expede para um [UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) instância, que é atribuída para o [delegar](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) propriedade.

<a name="Via_Events" />

##### <a name="via-events"></a>Por meio de eventos

Para muitos tipos, o xamarin. IOS criará automaticamente um delegado apropriado que encaminharão os `UIWebViewDelegate` chamadas para eventos de c#. Para `UIWebView`:

-  O [webViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) método é mapeado para o [UIWebView.LoadStarted](xref:UIKit.UIWebView.LoadStarted) eventos.
-  O [webViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) método é mapeado para o [UIWebView.LoadFinished](xref:UIKit.UIWebView.LoadFinished) eventos.
-  O [webView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) método é mapeado para o [UIWebView.LoadError](xref:UIKit.UIWebView.LoadError) eventos.

Por exemplo, esse simples programa registra as horas de início e término quando o carregamento de uma web exibe:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>Por meio de propriedades

Eventos são úteis quando pode haver mais de um assinante para o evento. Além disso, os eventos são limitados a casos em que não há nenhum valor de retorno do código.

Para casos em que o código deve retornar um valor, podemos optou por em vez disso, para propriedades. Isso significa que apenas um método pode ser definido em um determinado momento em um objeto.

Por exemplo, você pode usar esse mecanismo para ignorar o teclado na tela sobre o manipulador para um `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

O `UITextField`do `ShouldReturn` propriedade nesse caso é usada como argumento um delegado que retorna um valor bool e determina se o campo de texto deve fazer algo com o botão de retorno que está sendo pressionado. Em nosso método, retornamos *true* ao chamador, mas também removemos o teclado na tela (isso acontece quando o campo de texto chama `ResignFirstResponder`).

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>Com rigidez de tipos por meio de uma propriedade de delegado

Se você preferir não usar eventos, você pode fornecer seus próprios [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) subclasse e atribuí-lo para o [UIWebView.Delegate](xref:UIKit.UIWebView.Delegate) propriedade. Depois que tiver sido atribuído UIWebView.Delegate, o mecanismo de expedição de evento UIWebView deixará de funcionar e os métodos UIWebViewDelegate serão invocados quando ocorrem os eventos correspondentes.

Por exemplo, esse tipo simples registra o tempo necessário para carregar uma exibição da web:

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

As opções acima é usado no código como este:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Acima criará um UIWebViewer e ele será instruí-lo para enviar mensagens a uma instância do notificador, uma classe que criamos para responder às mensagens.

Esse padrão também é usado para controlar o comportamento de alguns controles, por exemplo, no caso UIWebView, o [UIWebView.ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) propriedade permite que o `UIWebView` instância de controle se o `UIWebView` carregará um página ou não.

O padrão também é usado para fornecer os dados sob demanda para alguns controles. Por exemplo, o [UITableView](xref:UIKit.UITableView) é um poderoso controle de renderização de tabela – e a aparência e o conteúdo é orientado por uma instância de um [UITableViewDataSource](xref:UIKit.UITableViewDataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Fracamente tipados por meio da propriedade WeakDelegate

Além da propriedade com rigidez de tipos, há também um delegado fraco tipado que permite que o desenvolvedor associar itens de forma diferente, se desejado.
Em todos os lugares fortemente tipado `Delegate` propriedade é exposta na associação do xamarin. IOS, um correspondente `WeakDelegate` propriedade também é exposta.

Ao usar o `WeakDelegate`, você é responsável por corretamente decorando a classe usando o [exportar](xref:Foundation.ExportAttribute) atributo para especificar o seletor. Por exemplo:

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

Observe que uma vez a `WeakDelegate` foi atribuída uma propriedade, o `Delegate` propriedade não será usada. Além disso, se você implementar o método em uma classe base herdada que você deseja [exportação], você deve tornar a ele um método público.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Mapeamento do Objective-C delegado padrão para o C&#35;

Quando você vir os exemplos de Objective-C que ter esta aparência:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Isso instrui o idioma para criar, construir uma instância da classe "SomethingDelegate" e atribua o valor para a propriedade de delegado na variável de foo. Esse mecanismo é compatível com o xamarin. IOS e a sintaxe da linguagem c# é:

```csharp
foo.Delegate = new SomethingDelegate ();
```

No xamarin. IOS nós fornecemos classes de representante de classes fortemente tipadas que são mapeados para o Objective-C. Para usá-las, você será criando subclasses e substituindo os métodos definidos pela implementação do xamarin. IOS. Para obter mais informações sobre como eles funcionam, consulte os seção "modelos" abaixo.


##### <a name="mapping-delegates-to-c35"></a>Mapeamento de delegados para C&#35;

Em geral, UIKit usa delegados de Objective-C em duas formas.

O primeiro formulário fornece uma interface para o modelo de um componente. Por exemplo, como um mecanismo para fornecer dados sob demanda para uma exibição, como o recurso de armazenamento de dados para uma exibição de lista.  Nesses casos, você sempre deve criar uma instância da classe apropriada e atribua a variável.

No exemplo a seguir, fornecemos o `UIPickerView` com uma implementação de um modelo que usa cadeias de caracteres:

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

A segunda forma é fornecer uma notificação de eventos. Nesses casos, embora ainda expomos a API no formato descrito acima, nós também fornecemos c# eventos, que devem ser mais simples de usar para operações rápidas e integrado com delegados anônimos e expressões lambda em c#.

Por exemplo, você pode assinar `UIAccelerometer` eventos:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

As duas opções estão disponíveis em que eles fazem sentido, mas como um programador, você deve escolher um ou outro. Se você criar sua própria instância de um Respondente/delegado com rigidez de tipos e atribuí-lo, os eventos do c# não estará funcionais. Se você usar os eventos em C#, os métodos em sua classe de delegado/Respondente nunca serão chamados.

O exemplo anterior que usado `UIWebView` podem ser escritos usando o c# 3.0 lambdas como este:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Respondendo a eventos

No código de Objective-C, às vezes, manipuladores de eventos para vários controles e provedores de informações para vários controles, serão hospedados na mesma classe. Isso é possível porque classes respondem às mensagens, e desde que as classes respondem às mensagens, é possível vincular a objetos.

Conforme anteriormente detalhado, xamarin. IOS dá suporte a ambos os o c# baseado em evento modelo de programação, e o padrão de delegado Objective-C, onde você pode criar uma nova classe que implementa o delegado e substitui os métodos desejados.

Também é possível oferecer suporte ao padrão do Objective-C em que os respondentes para diversas operações diferentes são todos hospedados na mesma instância de uma classe. Para fazer isso no entanto, você terá que usar os recursos de baixo nível da associação do xamarin. IOS.

Por exemplo, se você quisesse que sua classe para responder a ambos os `UITextFieldDelegate.textFieldShouldClear`: mensagem e o `UIWebViewDelegate.webViewDidStartLoad`: na mesma instância de uma classe, você precisaria usar a declaração de atributo [exportação]:

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

O C# os nomes de métodos não são importantes; tudo o que importa são as cadeias de caracteres passadas para o atributo [Export].

Ao usar esse estilo de programação, certifique-se de que os parâmetros do c# correspondem aos tipos reais que o mecanismo de tempo de execução passará.

<a name="Models" />

#### <a name="models"></a>Modelos

Em instalações de armazenamento de UIKit, ou em respondentes que são implementados usando classes auxiliares, elas geralmente são chamadas no código Objective-C como delegados e eles são implementados como protocolos.

Protocolos de Objective-C são como interfaces, mas eles oferecem suporte a métodos opcionais – ou seja, nem todos os métodos precisam ser implementados para o protocolo funcione.

Há duas maneiras de implementar um modelo. Você pode implementá-lo manualmente ou usar as definições com rigidez de tipos existentes.


O mecanismo manual é necessário quando você tenta implementar uma classe que não tenha sido associada com xamarin. IOS. É muito fácil fazer:

-  Sinalizador de sua classe para o registro com o tempo de execução
-  Aplicar o atributo [Export] com o nome real do seletor em cada método que você deseja substituir
-  Instanciar a classe e passá-lo.

Por exemplo, o seguinte implementa apenas um dos métodos opcionais na definição de protocolo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

O nome do seletor de Objective-C ("applicationDidFinishLaunching:") é declarada com o atributo de exportação e a classe é registrada com o `[Register]` atributo.

Xamarin. IOS fornece com rigidez de tipos declarações, prontos para uso, que não exigem associação manual. Para dar suporte a esse modelo de programação, o tempo de execução do xamarin. IOS dá suporte ao atributo [Model] em uma declaração de classe. Isso informa ao tempo de execução que não devem conectar-se a todos os métodos na classe, a menos que os métodos são explicitamente é implementado.

Isso significa que, no UIKit, as classes que representam um protocolo com métodos opcionais são gravadas como este:

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

Quando você deseja implementar um modelo que apenas implementa alguns métodos, tudo o que você precisa fazer é substituir os métodos que você está interessado e ignorar os outros métodos. O tempo de execução irá se conectar apenas os métodos substituídos, não os métodos originais para o mundo de Objective-C.

É o equivalente ao exemplo anterior manual:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

As vantagens são que não é necessário para examinar os arquivos de cabeçalho Objective-C para localizar o seletor, os tipos de argumentos, ou o mapeamento para c#, e que você obtém o intellisense do Visual Studio para Mac, juntamente com tipos fortes


#### <a name="xib-outlets-and-c35"></a>C e saídas XIB&#35;

> [!IMPORTANT]
> Esta seção explica a integração de IDE com tomadas ao usar os arquivos XIB. Ao usar o Designer do Xamarin para iOS, isso é tudo substituído, inserindo um nome em **identidade > nome** na seção de propriedades do IDE, como mostrado abaixo:
>
> [![](images/designeroutlet.png "Inserir um nome de item no iOS Designer")](images/designeroutlet.png#lightbox)
>
>Para obter mais informações sobre o Designer do iOS, consulte a [Introdução ao Designer do iOS](~/ios/user-interface/designer/introduction.md#how-it-works) documento.

Isso é uma descrição de nível baixo de como integram o tomadas com c# e é fornecido para usuários avançados do xamarin. IOS. Quando usando o Visual Studio para Mac o mapeamento é feito automaticamente em segundo plano usando gerado código sobre o voo para você.

Ao projetar sua interface do usuário com o Interface Builder, você estará criando apenas a aparência do aplicativo e estabelecerá algumas conexões padrão. Se você deseja buscar informações por meio de programação, alterar o comportamento de um controle em tempo de execução ou modificar o controle em tempo de execução, é necessário associar alguns dos controles ao seu código gerenciado.

Isso é feito em algumas etapas:

1.  Adicione a **declaração de saída** para seu **proprietário do arquivo**.
1.  Conectar-se o controle para o **o proprietário do arquivo**.
1.  Store as conexões em seu arquivo XIB/NIB além de interface do usuário.
1.  Carrega o arquivo NIB em tempo de execução.
1.  Acesse a variável de saída.


As etapas (1) a (3) são abordadas na documentação da Apple para a criação de interfaces com o Interface Builder.

Ao usar o xamarin. IOS, seu aplicativo precisará criar uma classe que deriva de UIViewController. Ele é implementado ele como esta:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Em seguida, para carregar seu ViewController de um arquivo NIB, você fazer isso:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Isso carrega a interface do usuário da PONTA. Agora, para acessar as saídas, é necessário informar o tempo de execução que desejamos acessá-los. Para fazer isso, o `UIViewController` subclasse precisa declarar as propriedades e anotá-las com o atributo [Connect]. Assim:

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

A implementação de propriedade é aquele que, na verdade, busca e armazena o valor para o tipo nativo real.

Você não precisa se preocupar sobre isso, ao usar o Visual Studio para Mac e InterfaceBuilder. O Visual Studio para Mac espelha automaticamente todos os as saídas declaradas com o código em uma classe parcial que é compilado como parte do seu projeto.

#### <a name="selectors"></a>Seletores

Um conceito central da programação Objective-C é seletores. Com frequência virão entre as APIs que exigem que você passe um seletor ou espera que seu código para responder a um seletor.

Criando novos seletores no c# é muito fácil – basta você cria uma nova instância do `ObjCRuntime.Selector` de classe e usar o resultado em qualquer lugar na API que precisa dele. Por exemplo:

```csharp
var selector_add = new Selector ("add:plus:");
```

Para um c# responder de método a uma chamada de seletor, ela deve herdar do `NSObject` tipo e o método c# devem ser decoradas com o nome do seletor usando o `[Export]` atributo. Por exemplo:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Observe esse seletor nomes **deve** corresponder exatamente, incluindo todos os dois-pontos intermediários e à direita (":"), se estiver presente.

#### <a name="nsobject-constructors"></a>Construtores de NSObject

A maioria das classes no xamarin. IOS que derivam de `NSObject` irá expor construtores específicas para a funcionalidade do objeto, mas eles também irá expor vários construtores que não são imediatamente óbvios.

Os construtores são usados da seguinte maneira:

```csharp
public Foo (IntPtr handle)
```

Este construtor é usado para instanciar sua classe quando o tempo de execução precisa para mapear sua classe em uma classe não gerenciada. Isso acontece quando você carregar um arquivo XIB/NIB.  Neste ponto, o tempo de execução do Objective-C terá criado um objeto no mundo não gerenciado, e este construtor será chamado para inicializar o lado gerenciado.

Normalmente, tudo o que você precisa fazer é chamar o construtor base com o parâmetro de identificação e no corpo, faça qualquer inicialização que é necessária.

```csharp
public Foo ()
```

Este é o construtor padrão para uma classe e no xamarin. IOS fornecido classes, isso inicializa a classe Foundation.NSObject e todas as classes entre os dois e no final, encadeia isso o Objective-C `init` método na classe.

```csharp
public Foo (NSObjectFlag x)
```

Este construtor é usado para inicializar a instância, mas impedir que o código de chamada ao método Objective-C "inicialização" no final. Você normalmente usa isso quando você já está registrado para a inicialização (quando você usa `[Export]` em seu construtor) ou quando você tiver feito sua inicialização por meio de outro média.

```csharp
public Foo (NSCoder coder)
```

Este construtor é fornecido para os casos em que o objeto está sendo inicializado de uma instância de NSCoding. Para obter mais informações, consulte da Apple [arquivos mortos e guia de programação de serialização.](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Exceções

O design de API do xamarin. IOS não gera exceções Objective-C como exceções em C#. O design impõe que nenhum lixo sejam enviadas para o mundo de Objective-C em primeiro lugar e que todas as exceções que devem ser produzidas são produzidas pela associação em si antes de dados inválidos nunca são passado para o mundo de Objective-C.

#### <a name="notifications"></a>Notificações

No iOS e nos X, os desenvolvedores podem assinar as notificações que são transmitidos pela plataforma subjacente. Isso é feito usando o `NSNotificationCenter.DefaultCenter.AddObserver` método. O `AddObserver` método utiliza dois parâmetros; um é a notificação de que você deseja assinar; a outra é o método a ser invocado quando a notificação é gerada.

No xamarin. IOS e xamarin. Mac, as chaves de várias notificações são hospedadas na classe que dispara as notificações. Por exemplo, as notificações são gerados pelo `UIMenuController` forem hospedados como `static NSString` propriedades no `UIMenuController` classes que terminam com o nome "Notificação".

### <a name="memory-management"></a>Gerenciamento de memória

Xamarin. IOS tem um coletor de lixo se encarregará de liberação de recursos para você quando eles não estão mais em uso. Além do coletor de lixo, todos os objetos que derivam `NSObject` implementar o `System.IDisposable` interface.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Expondo o `IDisposable` interface é uma maneira conveniente de auxiliar os desenvolvedores na liberação de objetos que podem encapsular grandes blocos de memória (por exemplo, um `UIImage` pode parecer com apenas um ponteiro inocente, mas poderia estar apontando para uma imagem de 2 MB ) e outros recursos importantes e finitos (como um buffer de decodificação de vídeo).

NSObject implementa a interface IDisposable e também a [padrão de descarte de .NET](http://msdn.microsoft.com/library/fs2xkftw.aspx). Isso permite que os desenvolvedores que a subclasse NSObject para substituir o comportamento de Dispose e liberar seus próprios recursos sob demanda. Por exemplo, considere esse controlador de exibição que mantém em torno de uma porção de imagens:

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

Quando um objeto gerenciado é descartado, ele não é mais útil. Você ainda pode ter uma referência a objetos, mas o objeto para todas as intenções e finalidades é inválido neste momento. Algumas APIs .NET garantir isso gerando um ObjectDisposedException se você tentar acessar quaisquer métodos em um objeto descartado, por exemplo:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Mesmo se você ainda pode acessar a variável "image", é realmente uma referência inválida e não aponta para o objeto Objective-C que mantinha a imagem.

Mas o descarte um objeto na linguagem c# não significa que o objeto necessariamente será destruído. Tudo que você faz é liberar a referência de c# tinha para o objeto. É possível que o ambiente do Cocoa talvez manteve uma referência ao redor para seu próprio uso. Por exemplo, se você definir a propriedade de imagem de um UIImageView para uma imagem e, em seguida, descarte a imagem, o UIImageView subjacente que tinha pegado sua própria referência e manterá uma referência a esse objeto até que ela seja concluída usá-lo.

#### <a name="when-to-call-dispose"></a>Quando chamar Dispose

Você deve chamar Dispose quando precisar Mono nos livrar-se de seu objeto. Um caso de uso possível é quando Mono não tem nenhum conhecimento de que seu NSObject, na verdade, está mantendo uma referência a um recurso importante, como memória ou um pool de informações. Nesses casos, você deve chamar Dispose para liberar imediatamente a referência na memória, em vez de aguardar o Mono executar um ciclo de coleta de lixo.

Internamente, quando cria Mono [NSString faz referência de cadeias de caracteres c#](~/ios/internals/api-design/nsstring.md), ele descartará-los imediatamente para reduzir a quantidade de trabalho que o coletor de lixo precisa fazer. Quanto menos objetos para lidar com, mais rápido, o GC serão executado.

#### <a name="when-to-keep-references-to-objects"></a>Quando se deve manter referências a objetos

Um efeito colateral com gerenciamento automático de memória é que o GC se livrar dos objetos não utilizados desde que nenhuma referência a eles. Isso às vezes, pode ter efeitos colaterais surpreendentes, por exemplo, se você criar uma variável local para armazenar seu controlador de exibição de nível superior, ou a janela de nível superior e, em seguida, acionando esses desaparecer por trás de seu backup.

Se você não mantiver uma referência em seu estático ou variáveis de instância para seus objetos, Mono Felizmente chamará o método Dispose ()-los e eles vão lançar a referência ao objeto. Como isso pode ser a referência somente pendente, o tempo de execução do Objective-C destruirá o objeto para você.

## <a name="related-links"></a>Links relacionados

- [Campos de associação](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
