---
title: Projeto de API
description: Perspectiva sobre o design de API do xamarin
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: b7604633a5dfad6134d7b549299194ab6707a865
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="api-design"></a>Projeto de API

Além das principais bibliotecas de classes Base que fazem parte do Mono, [xamarin](http://www.xamarin.com/iOS) fornecido com associações para iOS várias APIs para permitir que os desenvolvedores criem aplicativos do iOS nativo com Mono.

No núcleo do xamarin, há um mecanismo de interoperabilidade que une o mundo c# com o mundo Objective-C, bem como as associações para o iOS APIs com base em C como CoreGraphics e [OpenGL ES](#OpenGLES).

O tempo de execução de nível baixo para se comunicar com o código Objective-C está em [MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime). Sobre esta, ligações de [Foundation](#MonoTouch.Foundation), CoreFoundation, e [UIKit](#MonoTouch.UIKit) são fornecidos.

## <a name="design-principles"></a>Princípios de design

Estes são alguns dos nossos princípios de design para as ligações de xamarin (eles também se aplicam a Xamarin.Mac, as associações Mono para Objective-C em macOS):

- Siga o [diretrizes de Design de estrutura](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- Permitir que os desenvolvedores a classes de subclasse Objective-C:

  - Derivar de uma classe existente
  - Chame o construtor para a cadeia de base
  - Substituindo métodos deve ser feito com o sistema de substituição do #
  - Subclassificação deve funcionar com c# construtores padrão

- Não exponha os desenvolvedores seletores Objective-C
- Fornecem um mecanismo para chamar a biblioteca de arbitrário Objective-C
- Fazer tarefas comuns de Objective-C fácil e é difícil possíveis de tarefas Objective-C
- Expõe propriedades Objective-C como propriedades do c#
- Expor uma API fortemente tipados:

  - Aumentar a segurança de tipo
  - Minimizar erros de tempo de execução
  - Obter IDE IntelliSense em tipos de retorno
  - Permite a documentação de pop-up IDE

- Incentivamos a exploração no IDE das APIs:

  - Por exemplo, em vez de exposição de uma matriz de tipo fraco assim:
    
    ```objc
    NSArray *getViews
    ```
    Expor um tipo forte, como este:
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    Isso permite que o Visual Studio para Mac para fazer o preenchimento automático ao procurar a API, toda a `System.Array` operações disponíveis no valor retornado e permite que o valor de retorno participar de LINQ.

- Tipos c# nativo:

  - [`NSString` se torna `string`](~/ios/internals/api-design/nsstring.md)
  - Ativar `int` e `uint` parâmetros que devem ter sido enums em c# enumerações e c# enumerações com `[Flags]` atributos
  - Em vez de tipo neutra `NSArray` objetos, expor matrizes como matrizes fortemente tipada.
  - Eventos e notificações, dar aos usuários uma escolha entre:

    - Uma versão fortemente tipada por padrão
    - Uma versão do tipo fraco para casos de uso avançado

- Compatíveis com o padrão de delegado Objective-C:

    - Sistema de eventos do c#
    - Expor c# delegados (lambdas, métodos anônimos, e `System.Delegate`) para Objective-C APIs como blocos

### <a name="assemblies"></a>Assemblies

Xamarin inclui um número de conjuntos de módulos que constituem o *xamarin perfil*. O [Assemblies](~/cross-platform/internals/available-assemblies.md) página tem mais informações.

### <a name="major-namespaces"></a>Namespaces de principal 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

O [ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/) namespace permite aos desenvolvedores de ponte mundos entre c# e objetivo-C.
Isso é uma nova associação, projetada especificamente para o iOS, com base na experiência de Cocoa # e Gtk #.

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

O [Foundation](https://developer.xamarin.com/api/namespace/Foundation/) namespace fornece os tipos de dados básicos projetado para interagir com a estrutura de base Objective-C que faz parte do iOS e é a base para C. o objetivo de programação orientada a objeto

Xamarin espelha em c#, a hierarquia de classes do objetivo-C. Por exemplo, a classe base Objective-C [NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html) pode ser usado no c# via [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/).

Embora esse namespace fornece associações para os tipos base Objective-C subjacentes, em alguns casos, mapeou tipos subjacentes para tipos .NET. Por exemplo:

- Em vez de lidar com [NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html) e [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), o tempo de execução expõe como c# [cadeia de caracteres](https://developer.xamarin.com/api/type/System.String/)s e fortemente tipadas [matriz](https://developer.xamarin.com/api/type/System.Array/)s em a API.

- Auxiliar várias APIs são apresentados para permitir que os desenvolvedores a ligação de terceiros APIs Objective-C, outros iOS APIs ou APIs que atualmente não são associados por xamarin.

Para obter mais detalhes sobre a associação de APIs, consulte o [gerador de associação xamarin](~/cross-platform/macios/binding/binding-types-reference.md) seção.


##### <a name="nsobject"></a>NSObject

O [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) tipo é a base para todas as associações de Objective-C. Tipos de xamarin espelham de duas classes de tipos de CocoaTouch APIs do iOS: os tipos do C (geralmente referenciado como tipos de CoreFoundation) e os tipos de Objective-C (eles todos derivam da classe de NSObject).

Para cada tipo que reflete um tipo não gerenciado, é possível obter o objeto nativo por meio de [tratar](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propriedade.

Enquanto Mono fornecerá a coleta de lixo para todos os seus objetos, o `Foundation.NSObject` implementa o [System. IDisposable](https://developer.xamarin.com/api/type/System.IDisposable/) interface. Isso significa que você pode liberar os recursos de qualquer dado NSObject explicitamente sem precisar aguardar o coletor de lixo de reunião em. Isso é importante quando você estiver usando NSObjects intenso, por exemplo, UIImages que pode conter os ponteiros para blocos grandes de dados.

Se o tipo precisa executar finalização determinística, substituir o [NSObject.Dispose(bool) método](https://developer.xamarin.com/api/type/Foundation.NSObject/%2fM%2fDispose) o parâmetro Dispose for "bool disposing", e se definido como true, ele significa que o método Dispose está sendo chamado porque o usuário chamado explicitamente Dispose () no objeto. Se o valor for false, isso significa que o seu método Dispose (bool disposing) é chamado pelo finalizador no thread do finalizador. []()


##### <a name="categories"></a>Categorias

Iniciando com o xamarin 8.10-é possível criar categorias Objective-C do c#.

Isso é feito usando o `Category` atributo, especificando o tipo a ser estendido como um argumento para o atributo. O exemplo a seguir para a instância estenderá NSString.

    [Category (typeof (NSString))]

Cada método de categoria é usando o mecanismo normal para exportar os métodos para Objective-C usando o `Export` atributo:

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

Todos os métodos de extensão gerenciados devem ser estáticos, mas é possível criar métodos de instância Objective-C usando a sintaxe padrão para os métodos de extensão em c#:

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

e o primeiro argumento para o método de extensão será a instância na qual o método é invocado.

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

Este exemplo adicionará um método de instância toUpper nativo para a classe NSString, que pode ser chamada do objetivo-C.

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

Um cenário onde isso é útil é adicionar um método para um conjunto de classes na Base de código, por exemplo, isso tornará todos os `UIViewController` instâncias de relatório que pode girar:

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

PreserveAttribute é um atributo personalizado que é usado para informar mtouch – a ferramenta de implantação xamarin – para preservar a um tipo ou membro de um tipo, durante a fase de quando o aplicativo é processado para reduzir seu tamanho.

Cada membro que não está vinculado estaticamente pelo aplicativo está sujeito a ser removido. Portanto, esse atributo é usado para marcar membros que não são referenciados estaticamente, mas que ainda são necessários para seu aplicativo.

Por exemplo, se você cria uma instância de tipos dinamicamente, convém preservar o construtor padrão de seus tipos. Se você usar a serialização de XML, você talvez queira preservar as propriedades de seus tipos.

Você pode aplicar esse atributo a todos os membros de um tipo ou ao tipo propriamente dito. Se você quiser preservar o tipo inteiro, você pode usar a sintaxe [preservar (AllMembers = true)] no tipo.

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

O [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) namespace contém um mapeamento para todos os componentes de interface do usuário que compõem o CocoaTouch na forma de classes c#. A API foi modificada para seguir as convenções usadas na linguagem c#.

C# delegados são fornecidos para operações comuns. Consulte o [delegados](#Delegates) para obter mais informações.

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

Para OpenGLES, podemos distribuir um [versão modificada](https://developer.xamarin.com/api/namespace/OpenTK/) do [OpenTK](http://www.opentk.com/) API, uma associação orientada a objeto para OpenGL foi modificado para usar as estruturas e tipos de dados CoreGraphics, bem como expor apenas a funcionalidade disponível no iOS.

1.1 OpenGLES funcionalidade está disponível por meio do tipo ES11.GL, documentado [aqui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/) tipo.

A funcionalidade OpenGLES 2.0 está disponível por meio do tipo ES20.GL, documentado [aqui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/) tipo.

Funcionalidade OpenGLES 3.0 está disponível por meio do tipo ES30.GL, documentado [aqui](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/) tipo.


### <a name="binding-design"></a>Design de associação

Xamarin não é simplesmente uma ligação à plataforma subjacente Objective-C. Ele estende o sistema de tipos do .NET e o sistema de expedição blend melhor c# e objetivo-C.

Assim como P/Invoke é uma ferramenta útil para invocar as bibliotecas nativas em Windows e Linux, ou como IJW suporte pode ser usado para interoperabilidade COM Windows, xamarin amplia o tempo de execução para dar suporte a objetos de associação c# para objetos Objective-C.

A discussão nas próximas seções não é necessária para os usuários que estão criando aplicativos de xamarin, mas ajudarão os desenvolvedores a entender como coisas são feitas e irá ajudá-los durante a criação de aplicativos mais complicados.



#### <a name="types"></a>Tipos

Tipos c# onde fazia sentido, são expostos em vez de tipos de base de baixo nível, para o universo de c#.  Isso significa que [a API usa o tipo "cadeia de caracteres" c# em vez de NSString](~/ios/internals/api-design/nsstring.md) e usa com rigidez de tipos c# matrizes em vez de expor NSArray.

Em geral, no design do xamarin e Xamarin.Mac, subjacente `NSArray` objeto não é exposto. Em vez disso, o tempo de execução converte automaticamente `NSArray`s para matrizes com rigidez de tipos de alguns `NSObject` classe. Portanto, xamarin não expõe um método de tipo fraco como GetViews para retornar um NSArray:

```csharp
NSArray GetViews ();
```

Em vez disso, a associação expõe um valor de retorno fortemente tipado, como este:

```csharp
UIView [] GetViews ();
```

Existem alguns dos métodos expostos na `NSArray`, para os casos de canto em que você talvez queira usar um `NSArray` diretamente, mas seu uso não é recomendado na associação de API.

Além disso, no **API clássica** em vez de expor `CGRect`, `CGPoint` e `CGSize` da API do CoreGraphics, substituímos aqueles com o `System.Drawing` implementações `RectangleF`, `PointF`e `SizeF` conforme eles ajuda os desenvolvedores preservam código OpenGL existente que usa OpenTK. Ao usar o novo 64 bits **API unificada**, a API CoreGraphics devem ser usada.

<a name="Inheritance" />

#### <a name="inheritance"></a>Herança

O design de xamarin API permite que os desenvolvedores estendam tipos Objective-C nativos da mesma forma que eles seriam estenderá um tipo c#, usando a palavra-chave "substituição" em uma classe derivada, bem como a cadeia de para a implementação base usando a palavra-chave "base" do c#.

Esse design permite aos desenvolvedores evitar lidar com seletores Objective-C como parte de seu processo de desenvolvimento, porque todo o sistema Objective-C já é encapsulado dentro de bibliotecas do xamarin.


#### <a name="types-and-interface-builder"></a>Tipos e o construtor da Interface

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

Objective-C e c# têm significados diferentes para o representante de palavras em cada idioma.

No mundo Objective-C e a documentação que você encontrará online sobre CocoaTouch, um delegado é normalmente uma instância de uma classe que responde a um conjunto de métodos. Isso é muito semelhante a uma interface c#, com a diferença de que os métodos nem sempre são obrigatórios.

Esses representantes desempenham um papel importante em UIKit e outras APIs CocoaTouch. Eles são usados para realizar diversas tarefas:

-  Para fornecer notificações em seu código (semelhante a entrega de eventos em c# ou Gtk +).
-  Para implementar modelos para controles de visualização de dados.
-  Para direcionar o comportamento de um controle.


O padrão de programação foi projetado para minimizar a criação de classes derivadas para alterar o comportamento de um controle. Essa solução é semelhante em espírito ao longo dos anos tem feito outros conjuntos de ferramentas de GUI: Gtk sinais, slots Qt, Winforms eventos, eventos WPF/Silverlight e assim por diante. Para evitar ter centenas de interfaces (uma para cada ação) ou exigir que os desenvolvedores implementam muitos métodos não precisam, Objective-C oferece suporte a definições de método opcional. Isso é diferente de interfaces c# que exigem todos os métodos a serem implementados.

Classes de Objective-C, você verá classes que usam esse padrão de programação expõem uma propriedade, geralmente chamada `delegate`, que é necessário para implementar as partes obrigatórias da interface e partes de zero ou mais dos opcional.

No xamarin três mecanismos mutuamente exclusivos para vincular a esses representantes são oferecidos:

1.  [Por meio de eventos](#Via_Events).
2.  [Com rigidez de tipos por meio de um `Delegate` propriedade](#StrongDelegate)
3.  [Menos digitado por meio de um `WeakDelegate` propriedade](#WeakDelegate)

Por exemplo, considere o [UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html) classe. Isso envia para uma [UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html) instância, que é atribuída ao [delegar](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate) propriedade.

<a name="Via_Events" />

##### <a name="via-events"></a>Por meio de eventos

Para muitos tipos, o xamarin criará automaticamente um delegado apropriado que encaminhará o `UIWebViewDelegate` chamadas para eventos de c#. Para `UIWebView`:

-  O [webViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:) método é mapeado para o [UIWebView.LoadStarted](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadStarted/) eventos.
-  O [webViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:) método é mapeado para o [UIWebView.LoadFinished](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadFinished/) eventos.
-  O [webView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:) método é mapeado para o [UIWebView.LoadError](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadError/) eventos.

Por exemplo, esse programa simples registra as horas de início e término quando carregar uma web exibe:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>Propriedades de via

Eventos são úteis quando pode haver mais de um assinante para o evento. Além disso, os eventos são limitados a casos em que não há nenhum valor de retorno do código.

Para casos em que o código deve retornar um valor, podemos foi aceito em vez disso, para propriedades. Isso significa que somente um método pode ser definido em um determinado momento em um objeto.

Por exemplo, você pode usar esse mecanismo para ignorar o teclado na tela em que o manipulador para um `UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

O `UITextField`do `ShouldReturn` propriedade nesse caso usa como um argumento de um delegado que retorna um valor bool e determina se o campo de texto deve fazer algo com o pressionamento do botão de retorno. Em nosso método retornamos *true* para o chamador, mas também removemos o teclado na tela (Isso ocorre quando o campo de texto chama `ResignFirstResponder`).

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>Fortemente tipado por meio de uma propriedade de representante

Se você preferir não usar eventos, você pode fornecer sua própria [UIWebViewDelegate](https://developer.xamarin.com/api/type/UIKit.UIWebViewDelegate/) subclasse e atribuí-la para o [UIWebView.Delegate](https://developer.xamarin.com/api/property/UIKit.UIWebView.Delegate/) propriedade. Depois de atribuir UIWebView.Delegate, o mecanismo de expedição de eventos UIWebView deixará de funcionar, e os métodos UIWebViewDelegate serão invocados quando ocorrem os eventos correspondentes.

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

As opções acima é usada em código como este:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

Acima criará um UIWebViewer e ele instruirá a ele para enviar mensagens a uma instância de notificação, uma classe que são criados para responder a mensagens.

Esse padrão também é usado para controlar o comportamento de certos controles, por exemplo, no caso UIWebView, o [UIWebView.ShouldStartLoad](https://developer.xamarin.com/api/property/UIKit.UIWebView.ShouldStartLoad/) propriedade permite que o `UIWebView` instância para controlar se o `UIWebView` carregará um página ou não.

O padrão também é usado para fornecer os dados sob demanda para alguns controles. Por exemplo, o [UITableView](https://developer.xamarin.com/api/type/UIKit.UITableView/) é um poderoso controle de renderização de tabela – e a aparência e o conteúdo é direcionado por uma instância de um [UITableViewDataSource](https://developer.xamarin.com/api/type/UIKit.UITableView/DataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>Menos digitado por meio da propriedade WeakDelegate

Além da propriedade fortemente tipada, também há um delegado com tipo fraco que permite ao desenvolvedor associar as coisas diferente, se desejado.
Em todos os lugares fortemente tipada `Delegate` propriedade é exposta na associação do xamarin, correspondente `WeakDelegate` propriedade também é exposta.

Ao usar o `WeakDelegate`, você é responsável por decorando corretamente sua classe usando o [exportar](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo para especificar o seletor. Por exemplo:

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

Observe que uma vez a `WeakDelegate` foi atribuída uma propriedade, o `Delegate` propriedade não será usada. Além disso, se você implementar o método em uma classe base herdada que você deseja [exportação], você deve fazer a ele um método público.


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>Mapeamento do delegado Objective-C padrão C&#35;

Quando você ver exemplos Objective-C que ter esta aparência:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

Isso instrui o idioma para criar, construir uma instância da classe "SomethingDelegate" e atribua o valor para a propriedade de delegado na variável foo. Esse mecanismo é suportado pelo xamarin e c# a sintaxe é:

```csharp
foo.Delegate = new SomethingDelegate ();
```

Xamarin temos fornecido classes fortemente tipada que mapeiam o Objective-C delegar classes. Para usá-las, você será subclasses e substituindo os métodos definidos pela implementação do xamarin. Para obter mais informações sobre como eles funcionam, consulte os seção "modelos" abaixo.


##### <a name="mapping-delegates-to-c35"></a>Mapeando delegados para C&#35;

Em geral, UIKit usa delegados Objective-C em duas formas.

O primeiro formulário fornece uma interface para o modelo do componente. Por exemplo, como um mecanismo para fornecer dados sob demanda para uma exibição, como o recurso de armazenamento de dados para uma exibição de lista.  Nesses casos, você sempre deve criar uma instância da classe apropriada e atribuir a variável.

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

O segundo formulário é fornecer notificação de eventos. Nesses casos, embora ainda expomos API no formato descrito acima, nós também fornecemos c# eventos, que devem ser mais simples de usar para operações rápidas e integrado com delegados anônimos e expressões lambda em c#.

Por exemplo, você pode assinar `UIAccelerometer` eventos:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

As duas opções estão disponíveis em que eles fazem sentido, mas como programador, você deve escolher um ou outro. Se você criar sua própria instância do Respondente/representante fortemente tipada e atribuí-la, os eventos c# não estará funcionais. Se você usar os eventos do c#, os métodos na classe Respondente/delegado nunca serão chamados.

O exemplo anterior usado `UIWebView` podem ser escritos usando c# 3.0 lambdas assim:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>Respondendo a eventos

No código Objective-C, às vezes, manipuladores de eventos para vários controles e os provedores de informações para vários controles, será hospedado na mesma classe. Isso é possível porque classes respondem a mensagens e como classes respondem a mensagens, é possível vincular objetos.

Como anteriormente detalhadas, xamarin dá suporte a ambos o c# baseado em evento modelo de programação, e o padrão de delegado Objective-C, onde você pode criar uma nova classe que implementa o delegado e substitui os métodos desejados.

Também é possível oferecer suporte ao padrão do Objective-C onde respondentes para diversas operações diferentes são todos hospedados na mesma instância de uma classe. Para fazer isso no entanto, você terá que usar recursos de nível inferior da associação xamarin.

Por exemplo, se você quisesse sua classe para responder a ambos os `UITextFieldDelegate.textFieldShouldClear`: mensagem e o `UIWebViewDelegate.webViewDidStartLoad`: na mesma instância de uma classe, você precisa usar a declaração de atributo [exportação]:

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

Os nomes do c# para os métodos não são importantes; o que importa são as cadeias de caracteres passadas para o atributo [exportação].

Ao usar esse estilo de programação, certifique-se de que os parâmetros do c# correspondem aos tipos reais que passará o mecanismo de tempo de execução.

<a name="Models" />

#### <a name="models"></a>Modelos

Em instalações de armazenamento UIKit, ou em respondentes que são implementados usando classes auxiliares, eles normalmente são chamados no código Objective-C como representantes e eles são implementados como protocolos.

Protocolos Objective-C são como interfaces, mas eles oferecem suporte a métodos opcionais – ou seja, nem todos os métodos devem ser implementadas para o protocolo funcione.

Há duas maneiras de implementar um modelo. Você pode implementá-lo manualmente ou usar as definições com rigidez de tipos existentes.


O mecanismo manual é necessário quando você tenta implementar uma classe que não foi vinculada por xamarin. É muito fácil:

-  Sinalizador de sua classe para o registro com o tempo de execução
-  Aplique o atributo de exportação] com o nome real do seletor em cada método que você deseja substituir
-  Criar uma instância da classe e passá-lo.

Por exemplo, o seguinte implementa apenas um dos métodos opcionais na definição do protocolo UIApplicationDelegate:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

O nome de seletor Objective-C ("applicationDidFinishLaunching:") é declarada com o atributo de exportação e a classe é registrada com o `[Register]` atributo.

Xamarin fornece fortemente tipadas declarações, pronto para uso, que não exigem associação manual. Para dar suporte a este modelo de programação, o tempo de execução do xamarin suporta o atributo [modelo] em uma declaração de classe. Isso informa o tempo de execução que ele não deve conectar todos os métodos na classe, a menos que os métodos são explicitamente é implementado.

Isso significa que, na UIKit, as classes que representam um protocolo com métodos opcionais são gravadas como este:

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

Quando você deseja implementar um modelo que apenas implementa alguns métodos, tudo o que você precisa fazer é substituir os métodos que você está interessado e ignorar os outros métodos. O tempo de execução irá se conectar somente os métodos substituídos, não os métodos originais para o mundo Objective-C.

É o equivalente ao exemplo anterior manual:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

As vantagens são que não é necessário para examinar os arquivos de cabeçalho Objective-C para localizar o seletor, os tipos de argumentos, ou o mapeamento para c#, e que você obtenha o intellisense no Visual Studio para Mac, juntamente com os tipos de alta seguras


#### <a name="xib-outlets-and-c35"></a>C e XIB tomadas&#35;

> [!IMPORTANT]
> Esta seção explica a integração de IDE com tomadas ao usar arquivos XIB. Ao usar o Designer do Xamarin para iOS, isso é tudo substituído digitando um nome de **identidade > nome** na seção de propriedades do IDE, conforme mostrado abaixo:
>
> [![](images/designeroutlet.png "Inserir um nome de item no Designer de iOS")](images/designeroutlet.png#lightbox)
>
>Para obter mais informações sobre o Designer do iOS, examine o [introdução para o Designer do iOS](~/ios/user-interface/designer/introduction.md#how-it-works) documento.

Isso é uma descrição de nível baixo de como integram o tomadas com c# e é fornecido para usuários avançados de xamarin. Quando usando o Visual Studio para Mac o mapeamento é feito automaticamente em segundo plano usando gerado código no voo para você.

Quando você cria sua interface de usuário com o construtor de Interface, você estará criando apenas a aparência do aplicativo e estabelecerá algumas conexões padrão. Se você quiser buscar informações por meio de programação, alterar o comportamento de um controle em tempo de execução ou modificar o controle em tempo de execução, é necessário vincular alguns dos controles para o código gerenciado.

Isso é feito em algumas etapas:

1.  Adicionar o **declaração de saída** para sua **proprietário do arquivo**.
1.  Conecte-se o controle para o **proprietário do arquivo**.
1.  Armazene a interface do usuário e as conexões para o seu arquivo XIB/PONTA.
1.  Carrega o arquivo NIB em tempo de execução.
1.  Acesse a variável de saída.


As etapas (1) (3) são abordadas na documentação da Apple para a criação de interfaces com o construtor de Interface.

Ao usar xamarin, seu aplicativo precisará criar uma classe que deriva de UIViewController. Ele é implementado-lo assim:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

Em seguida, para carregar seu ViewController de um arquivo de PONTA, você faça o seguinte:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

Isso carrega a interface do usuário da PONTA. Agora, para acessar as tomadas, é necessário informar o tempo de execução que desejamos para acessá-los. Para fazer isso, o `UIViewController` subclasse precisa declarar as propriedades e anotá-los com o atributo [conectar]. Assim:

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

A implementação de propriedade é que, na verdade, busca e armazena o valor para o tipo nativo real.

Você não precisa se preocupar sobre isso, ao usar o Visual Studio para Mac e InterfaceBuilder. O Visual Studio para Mac espelha automaticamente todos os as tomadas declaradas com o código em uma classe parcial que é compilado como parte do seu projeto.

#### <a name="selectors"></a>Seletores

Um conceito de núcleo de programação Objective-C é seletores. Geralmente, você passará em APIs que exigem que você passar um seletor ou espera que seu código para responder a um seletor.

É muito fácil criar novos seletores em c# – você cria uma nova instância da apenas o `ObjCRuntime.Selector` classe e usar o resultado em qualquer lugar na API que exija isso. Por exemplo:

```csharp
var selector_add = new Selector ("add:plus:");
```

Para um c# método responder uma chamada de seletor, ele deve herdar do `NSObject` tipo e o método do c# devem ser decorados com o nome de seletor usando o `[Export]` atributo. Por exemplo:

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

Observe que selector nomes **deve** corresponder exatamente, incluindo todos os dois-pontos à direita e intermediários (":"), se presente.

#### <a name="nsobject-constructors"></a>Construtores de NSObject

A maioria das classes no xamarin que derivam de `NSObject` irá expor construtores específicas para a funcionalidade do objeto, mas eles também irá expor vários construtores que não são evidentes.

Os construtores são usados da seguinte maneira:

```csharp
public Foo (IntPtr handle)
```

Este construtor é usado para criar uma instância de sua classe quando o tempo de execução precisa mapear a classe para uma classe não gerenciada. Isso acontece quando você carregar um arquivo XIB/PONTA.  Neste ponto, o tempo de execução Objective-C terá criado um objeto no mundo gerenciado, e este construtor será chamado para inicializar o lado gerenciado.

Normalmente, tudo o que você precisa fazer é chamar o construtor base com o parâmetro de identificador e, no corpo, faça qualquer inicialização que é necessária.

```csharp
public Foo ()
```

Este é o construtor padrão para uma classe, e em xamarin fornecido classes, isso inicializa a classe Foundation.NSObject e todas as classes entre e no final, encadeia isso o Objective-C `init` método na classe.

```csharp
public Foo (NSObjectFlag x)
```

Este construtor é usado para inicializar a instância, mas impede que o código de chamar o método de "init" Objective-C no final. Você normalmente usa isso quando você já está registrado para a inicialização (quando você usa `[Export]` em seu construtor) ou quando você já fez sua inicialização por meio de outra média.

```csharp
public Foo (NSCoder coder)
```

Este construtor é fornecido para os casos em que o objeto está sendo inicializado de uma instância de NSCoding. Para obter mais informações, consulte da Apple [arquivos e o guia de programação de serialização.](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>Exceções

O projeto de API do xamarin não gera exceções Objective-C como c# exceções. O design impõe que nenhum lixo seja enviado para o mundo Objective-C em primeiro lugar e que todas as exceções que devem ser produzidas são produzidas pela associação em si antes de dados inválidos nunca são passado para o mundo Objective-C.

#### <a name="notifications"></a>Notificações

No iOS e OS X, os desenvolvedores podem assinar as notificações que são transmitidas pela plataforma subjacente. Isso é feito usando o `NSNotificationCenter.DefaultCenter.AddObserver` método. O `AddObserver` método aceita dois parâmetros, um é a notificação que você deseja assinar; a outra é o método a ser invocado quando a notificação é gerada.

Em xamarin e Xamarin.Mac, as chaves para várias notificações são hospedadas na classe que dispara as notificações. Por exemplo, as notificações são gerados pelo `UIMenuController` são hospedados como `static NSString` propriedades o `UIMenuController` classes que terminam com o nome "Notificação".

### <a name="memory-management"></a>Gerenciamento de memória

Xamarin tem um coletor de lixo cuidará de liberação de recursos para você quando eles não estão mais em uso. O coletor de lixo, além de todos os objetos que derivam `NSObject` implementar o `System.IDisposable` interface.

#### <a name="nsobject-and-idisposable"></a>NSObject e IDisposable

Expondo o `IDisposable` interface é uma maneira conveniente de ajudar os desenvolvedores na liberação de objetos que podem encapsular grandes blocos de memória (por exemplo, um `UIImage` pode parecer com apenas um ponteiro inocente, mas pode estar apontando para uma imagem de 2 megabytes ) e outros recursos importantes e finitos (como um buffer de decodificação de vídeo).

NSObject implementa a interface IDisposable e também o [padrão Dispose .NET](http://msdn.microsoft.com/en-us/library/fs2xkftw.aspx). Isso permite que os desenvolvedores que subclasse NSObject para substituir o comportamento de Dispose e liberar seus próprios recursos sob demanda. Por exemplo, considere esse controlador de exibição que mantém em torno de um grupo de imagens:

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

Quando um objeto gerenciado é descartado, ele não é mais útil. Você ainda pode ter uma referência aos objetos, mas o objeto para todos os efeitos é inválido neste momento. Algumas APIs .NET garantir que isso, lançando um ObjectDisposedException se você tentar acessar quaisquer métodos em um objeto descartado, por exemplo:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

Mesmo se você ainda pode acessar a variável "imagem", é realmente uma referência inválida e não aponta para o objeto Objective-C que mantidos a imagem.

Mas, descartar um objeto no c# não significa que o objeto necessariamente será destruído. Tudo que você faz é liberar a referência de c# tinha para o objeto. É possível que o ambiente Cocoa pode manteve uma referência ao redor de seu próprio uso. Por exemplo, se você definir a propriedade de imagem do UIImageView como uma imagem e, em seguida, você descartar a imagem, o UIImageView subjacente levou a própria referência e manterá uma referência a esse objeto até que ele for concluído com ele.

#### <a name="when-to-call-dispose"></a>Quando chamar Dispose

Você deve chamar Dispose quando precisar Mono em descartar o objeto. Um caso de uso possível é quando Mono não tem conhecimento que seu NSObject realmente está mantendo uma referência a um recurso importante, como memória ou um pool de informações. Nesses casos, você deve chamar Dispose para liberar imediatamente a referência na memória, em vez de esperar Mono executar um ciclo de coleta de lixo.

Internamente, quando cria Mono [NSString faz referência de c# cadeias de caracteres](~/ios/internals/api-design/nsstring.md), ele será dispose-los imediatamente para reduzir a quantidade de trabalho que o coletor de lixo precisa fazer. Os objetos menos para lidar com, quanto mais rápido o GC serão executado.

#### <a name="when-to-keep-references-to-objects"></a>Quando manter referências a objetos

Um efeito colateral com gerenciamento automático de memória é que o GC eliminará objetos não utilizados como existem referências a eles. Isso às vezes pode ter efeitos colaterais surpreendentes, por exemplo, se você criar uma variável local para armazenar seu controlador de exibição de nível superior, ou a janela de nível superior e, em seguida, mantendo os desaparecer por trás de seu backup.

Se você não mantiver uma referência em seu estático ou variáveis de instância para os objetos, Mono Felizmente chamará o método Dispose ()-los, e eles irá liberar a referência ao objeto. Como isso pode ser a referência somente pendente, o tempo de execução Objective-C destruirá o objeto para você.

## <a name="related-links"></a>Links relacionados

- [Campos de associação](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
