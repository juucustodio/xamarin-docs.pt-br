---
title: Guia de referência de tipos de associação
description: Este guia de referência descreve vários atributos e conceitos que são necessárias para entender ao criar C# as associações para bibliotecas de Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 9c5a3cdbc8a8d5a046db90ffa48b12709359da98
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55234024"
---
# <a name="binding-types-reference-guide"></a>Guia de referência de tipos de associação

Este documento descreve a lista de atributos que você pode usar para anotar os arquivos de contrato de API para conduzir a associação e o código gerado

Contratos de xamarin. IOS e a API do xamarin. Mac são escritos em C# principalmente como definições de interface que definem o modo como o código Objective-C é exposto ao C#. O processo envolve uma mistura de declarações de interface mais algumas definições de tipo básico que pode exigir que o contrato de API. Para obter uma introdução aos tipos de associação, consulte nosso guia complementar [bibliotecas de Objective-C associação](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definições de tipo

Sintaxe:

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Cada interface em sua definição de contrato que tem o [ `[BaseType]` ](#BaseTypeAttribute) atributo que declara o tipo base para o objeto gerado. Na declaração acima uma `MyType` classe C# tipo será gerado que associa a um tipo de Objective-C chamado `MyType`.

Se você especificar quaisquer tipos após o typename (no exemplo acima `Protocol1` e `Protocol2`) usando a sintaxe de herança da interface o conteúdo dessas interfaces será embutido como se tivessem sido parte do contrato para `MyType`.
A maneira como as superfícies do xamarin. IOS que um tipo adota um protocolo por embutindo todos os métodos e propriedades que foram declaradas no protocolo para o próprio tipo.

A seguir mostra como a declaração de Objective-C para `UITextField` seria definido em um contrato de xamarin. IOS:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Seria escrito assim como um C# contrato de API:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Você pode controlar muitos outros aspectos da geração do código, aplicação de outros atributos para a interface, bem como a configuração de [ `[BaseType]` ](#BaseTypeAttribute) atributo.


### <a name="generating-events"></a>Gerando eventos

Um recurso do projeto xamarin. IOS e xamarin. Mac API é que podemos mapear as classes de delegado de Objective-C como C# eventos e retornos de chamada. Os usuários podem escolher se desejam adotar o padrão de programação Objective-C, atribuindo a propriedades, como em uma base por instância `Delegate` uma instância de uma classe que implementa vários métodos que chamaria o tempo de execução do Objective-C, ou por escolhendo o C#-eventos e propriedades de estilo.

Vamos ver um exemplo de como usar o modelo de Objective-C:

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

No exemplo acima, você pode ver que optamos por substituir os dois métodos, uma notificação de que um evento de rolagem teve lugar e a segunda que haja um retorno de chamada deve retornar um valor booliano instruindo o `scrollView` deve rolar para o principais ou não.

O C# modelo permite que o usuário da sua biblioteca escutar as notificações usando o C# sintaxe de evento ou propriedade para ligar os retornos de chamada que são esperados como valores de retorno.

Isso é como o C# de código para o mesmo recurso é semelhante a usar lambdas:

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

Uma vez que os eventos não retornam valores (eles têm um tipo de retorno nulo), você pode conectar várias cópias. O `ShouldScrollToTop` não é um evento, em vez disso, é uma propriedade com o tipo `UIScrollViewCondition` que tem esta assinatura:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Ele retorna um `bool` valor, nesse caso, a sintaxe lambda nos permite retornar apenas o valor de `MakeDecision` função.

O gerador de associação dá suporte à geração de eventos e propriedades que se vinculam a uma classe, como `UIScrollView` com seus `UIScrollViewDelegate` (também chamamos a classe de modelo), isso é feito anotando seu [ `[BaseType]` ](#BaseTypeAttribute) definição com o `Events` e `Delegates` parâmetros (descritos abaixo). Além de anotar o [ `[BaseType]` ](#BaseTypeAttribute) com esses parâmetros, é necessário informar o gerador de alguns componentes mais.

Para eventos que levam mais de um parâmetro (em Objective-C a convenção é que o primeiro parâmetro em uma classe de delegado é a instância do objeto sender) você deve fornecer o nome que você gostaria para gerado `EventArgs` à classe. Isso é feito com o [ `[EventArgs]` ](#EventArgsAttribute) atributo na declaração de método na classe de modelo. Por exemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

A declaração acima vai gerar uma `UIImagePickerImagePickedEventArgs` classe que deriva `EventArgs` e pacotes de ambos os parâmetros, o `UIImage` e o `NSDictionary`. O gerador produz isso:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Ele expõe, em seguida, o seguinte no `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Métodos que retornam um valor do modelo são vinculados diferente. Eles exigir um nome para o gerado C# delegado (a assinatura do método) e também um valor padrão para retornar no caso do usuário não fornecer uma implementação de si próprio. Por exemplo, o `ShouldScrollToTop` definição é este:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

As opções acima criará um `UIScrollViewCondition` delegar com a assinatura que foi mostrado acima, e se o usuário não fornecer uma implementação, o valor de retorno será true.

Além de [ `[DefaultValue]` ](#DefaultValueAttribute) atributo, você também pode usar os [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute) atributo que direciona o gerador para retornar o valor do parâmetro especificado na chamada ou o [ `[NoDefaultValue]` ](#NoDefaultValueAttribute) parâmetro que instrui o gerador que não há nenhum valor padrão.

<a name="BaseTypeAttribute" />

### <a name="basetypeattribute"></a>BaseTypeAttribute

Sintaxe:

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

Você usa o `Name` propriedade para controlar o nome que esse tipo associará no mundo do Objective-C. Isso normalmente é usado para fornecer o C# digite um nome que está em conformidade com as diretrizes de Design do .NET Framework, mas que é mapeado para um nome em Objective-C que não segue essa convenção.

O exemplo, no caso a seguir, podemos mapear o Objective-C `NSURLConnection` de tipo para `NSUrlConnection`, conforme as diretrizes de Design do .NET Framework usam "Url" em vez de "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

O nome especificado é usado como o valor para o gerado `[Register]` atributo na associação. Se `Name` não for especificado, o nome do tipo curto é usado como o valor para o `[Register]` atributo na saída gerada.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events e BaseType.Delegates

Essas propriedades são usadas para orientar a geração de C#-eventos de estilo em classes geradas. Eles são usados para vincular a uma determinada classe com sua classe de delegado de Objective-C. Você encontrará muitos casos em que uma classe usa uma classe delegate para enviar notificações e eventos. Por exemplo uma `BarcodeScanner` teria um complemento `BardodeScannerDelegate` classe. O `BarcodeScanner` classe normalmente teria um `Delegate` propriedade que você atribui uma instância do `BarcodeScannerDelegate` para, embora isso funciona, você pode querer expor para seus usuários uma C#-como a interface de eventos de estilo e, nesses casos, você usaria o `Events` e `Delegates` propriedades da [ `[BaseType]` ](#BaseTypeAttribute) atributo.

Essas propriedades são sempre definidas em conjunto e devem ter o mesmo número de elementos e mantidas em sincronia. O `Delegates` matriz contém uma cadeia de caracteres para cada representante com tipagem fraca que você deseja quebrar, e o `Events` matriz contém um tipo para cada tipo que você deseja associar a ele.

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```


#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

Se você aplicar esse atributo quando novas instâncias dessa classe são criadas, a instância desse objeto será mantida até que o método referenciado pelo `KeepRefUntil` foi invocado. Isso é útil para melhorar a usabilidade de suas APIs, quando você não deseja que seu usuário para manter uma referência a um objeto em torno de usar seu código. O valor dessa propriedade é o nome de um método na `Delegate` classe, portanto, você deve usar isso em combinação com o `Events` e `Delegates` propriedades também.

O exemplo a seguir mostram como isso é usado pelo `UIActionSheet` no xamarin. IOS:

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```


### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Quando esse atributo é aplicado à definição da interface ele impedirá que o gerador de produzir o construtor padrão.

Use esse atributo quando você precisa do objeto a ser inicializado com um dos construtores na classe.


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Quando esse atributo é aplicado à definição da interface sinalizará o construtor padrão como particular. Isso significa que você pode ainda criar uma instância de objeto desta classe internamente do seu arquivo de extensão, mas não quer apenas ser acessíveis aos usuários da sua classe.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Use esse atributo em uma definição de tipo para associar as categorias de Objective-C e expor isso como C# métodos de extensão para espelhar a forma como Objective-C expõe a funcionalidade.

Categorias são um mecanismo de Objective-C usado para estender o conjunto de métodos e propriedades disponíveis em uma classe.   Na prática, eles são usados para estender a funcionalidade de uma classe base (por exemplo `NSObject`) quando uma estrutura específica estiver vinculada (por exemplo `UIKit`), tornando seus métodos disponíveis, mas somente se a nova estrutura é vinculada.   Em alguns casos, eles são usados para organizar recursos em uma classe pela funcionalidade.   Elas são semelhantes no espírito para C# métodos de extensão.

Isso é como uma categoria ficaria em Objective-c:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

O exemplo acima é encontrado em uma biblioteca que estenderia instâncias do `UIView` com o método `makeBackgroundRed`.

Para associar os, você pode usar o [ `[Category]` ](#CategoryAttribute) atributo em uma definição de interface.   Ao usar o [ `[Category]` ](#CategoryAttribute) de atributo, o significado dos [ `[BaseType]` ](#BaseTypeAttribute) atributo muda de serem usados para especificar a classe base para estender, como sendo do tipo a ser estendido.

A seguir mostra como o `UIView` extensões são vinculadas e transformadas em C# métodos de extensão:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

As opções acima criará um `MyUIViewExtension` uma classe que contém o `MakeBackgroundRed` método de extensão.   Isso significa que, agora, você pode chamar `MakeBackgroundRed` em qualquer `UIView` subclasse, dando a você a mesma funcionalidade que você obteria em Objective-C.

Em alguns casos você encontrará **estático** membros dentro de categorias, como no exemplo a seguir:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Isso levará a um **incorreta** categoria C# definição de interface:

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Isso está incorreto porque usar o `BoolMethod` extensão você precisa de uma instância do `FooObject` , mas você está associando um ObjC **estático** extensão, esse é um efeito colateral devido ao fato de como C# métodos de extensão são implementados .

O seguinte código feio é a única maneira de usar as definições acima:

```csharp
(null as FooObject).BoolMethod (range);
```

A recomendação para evitar que isso é embutir a `BoolMethod` definição dentro de `FooObject` definição de interface em si, isso permitirá que você chame esta extensão, como ele se destina `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Podemos emitirá um aviso (BI1117) sempre que encontramos uma [ `[Static]` ](#StaticAttribute) membro dentro de uma [ `[Category]` ](#CategoryAttribute) definição. Se você realmente deseja ter [ `[Static]` ](#StaticAttribute) membros dentro de seu [ `[Category]` ](#CategoryAttribute) definições que você pode silenciar o aviso usando `[Category (allowStaticMembers: true)]` ou de decoração de seu membro ou uma [ `[Category]` ](#CategoryAttribute) definição com de interface [ `[Internal]` ](#InternalAttribute).

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

Quando esse atributo é aplicado a uma classe simplesmente gerará uma classe estática, que não deriva de `NSObject`, portanto, o [ `[BaseType]` ](#BaseTypeAttribute) atributo é ignorado. Classes estáticas são usadas para hospedar C variáveis públicas que você deseja expor.

Por exemplo:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Gerará uma C# classe com a seguinte API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Modelo de definições de protocolo /

Normalmente, os modelos são usados pela implementação de protocolo.
Elas diferem em que o tempo de execução só serão registrados com Objective-C os métodos que realmente foram substituídos.
Caso contrário, o método não será registrado.

Em geral, isso significa que, quando você subclasse uma classe que foi sinalizada com o `ModelAttribute`, você não deve chamar o método base.   Chamar o método lançará uma exceção, você deve para implementar o comportamento inteiro em sua subclasse para métodos que você substituir.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

Por padrão, os membros que fazem parte de um protocolo não são obrigatórios. Isso permite que os usuários criem uma subclasse do `Model` objeto, simplesmente derivando da classe no C# e substituindo apenas os métodos que ele está preocupado. Às vezes, o contrato de Objective-C requer que o usuário fornece uma implementação desse método (esses são marcados com o `@required` diretiva em Objective-C). Nesses casos, você deve sinalizar esses métodos com o `[Abstract]` atributo.

O `[Abstract]` pode ser aplicado a métodos ou propriedades de atributo e faz com que o gerador sinalizar o membro gerado como abstrato e a classe seja uma classe abstrata.

O exemplo a seguir é obtido da xamarin. IOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

Especifica o valor padrão a ser retornado por um método de modelo se o usuário não fornecer um método para esse método específico no objeto de modelo

Sintaxe:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Por exemplo, na classe imaginário delegado para um `Camera` classe, fornecemos uma `ShouldUploadToServer` que deve ser exposto como uma propriedade no `Camera` classe. Se o usuário do `Camera` classe não definir explicitamente um o valor a ser um lambda que pode responder true ou false, o valor padrão retornado nesse caso seria falso, o valor que especificamos no `DefaultValue` atributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Se o usuário define um manipulador na classe imaginária, esse valor seria ignorado:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

Consulte também: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute).

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Sintaxe:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Esse atributo quando fornecido em um método que retorna um valor em uma classe de modelo instruirá o gerador para retornar o valor do parâmetro especificado, se o usuário não forneceu seu próprio lambda ou método.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

No caso acima se o usuário dos `NSAnimation` classe decidiu usar qualquer um do C# propriedades/eventos e não definiu `NSAnimation.ComputeAnimationCurve` para um método ou lambda, o valor de retorno seria o valor passado no parâmetro de progresso.

Consulte também: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

Às vezes, faz sentido não expõe um evento ou delegar a propriedade de uma classe de modelo para a classe do host para que adicionar esse atributo instruirá o gerador para evitar a geração de qualquer método decorado com ele.

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

Esse atributo é usado em métodos de modelo que retornam valores para definir o nome da assinatura do delegado para usar.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Com a definição acima, o gerador de produzirá a seguinte declaração de pública:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Este atributo é usado para permitir que o gerador alterar o nome da propriedade gerada na classe de host. Às vezes é útil quando o nome do método da classe FooDelegate faz sentido para a classe de delegado, mas seria parecer estranho na classe de host como uma propriedade.

Além disso, este é realmente útil (e necessário) quando você tiver dois ou mais métodos de sobrecarga que faz sentido manterem-los nomeado como está na classe FooDelegate, mas você deseja expô-los na classe de host com um determinado nome melhor.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Com a definição acima, o gerador de produzirá a seguinte declaração de pública na classe de host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

Para eventos que levam mais de um parâmetro (em Objective-C a convenção é que o primeiro parâmetro em uma classe de delegado é a instância do objeto sender) você deve fornecer o nome que você deseja para a classe EventArgs gerada seja. Isso é feito com o `[EventArgs]` atributo na declaração de método no seu `Model` classe.

Por exemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

A declaração acima vai gerar uma `UIImagePickerImagePickedEventArgs` classe que deriva de EventArgs e pacotes de ambos os parâmetros, o `UIImage` e o `NSDictionary`. O gerador produz isso:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Ele expõe, em seguida, o seguinte no `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

Este atributo é usado para permitir que o gerador alterar o nome de um evento ou propriedade gerada na classe. Às vezes é útil quando o nome do método de classe de modelo faz sentido para a classe de modelo, mas ficaria ímpar na classe de origem como um evento ou propriedade.

Por exemplo, o `UIWebView` usa o seguinte trecho do `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

O expõe acima `LoadingFinished` como o método na `UIWebViewDelegate`, mas `LoadFinished` como o evento conectar até em uma `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

Quando você aplica o `[Model]` atributo a uma definição de tipo em seu contrato de API, o tempo de execução irá gerar um código especial que só serão exibidos invocações de métodos na classe se o usuário substituiu um método na classe. Normalmente, esse atributo é aplicado a todas as APIs que encapsulam uma classe de delegado de Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Especifica que o método no modelo não fornece um valor de retorno padrão.

Isso funciona com o tempo de execução do Objective-C respondendo `false` à solicitação de tempo de execução do Objective-C para determinar se o seletor especificado é implementado nesta classe.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Consulte também: [ `[DefaultValue]` ](#DefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>Protocolos

O conceito de protocolo do Objective-C não realmente existe no C#. Protocolos são semelhantes a C# interfaces, mas eles diferem em que nem todos os métodos e propriedades declaradas em um protocolo devem ser implementadas pela classe que adota a ele. Em vez disso, alguns dos métodos e propriedades são opcionais.

Alguns protocolos são geralmente usados como classes de modelo, aqueles devem ser associados usando o [ `[Model]` ](#ModelAttribute) atributo.

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}
```

Começando com o xamarin. IOS 7.0 um funcionalidade de ligação de protocolo novo e aprimorado incorporado.  Qualquer definição que contém o `[Protocol]` atributo gerará, na verdade, três classes de suporte que melhoram muito a maneira que você consuma protocolos:

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

O **implementação da classe** fornece uma classe abstrata completa que você pode substituir métodos individuais do e obter a segurança de tipo completo. Mas, devido ao C# não oferecer suporte a várias heranças, há cenários em que você pode exigir uma classe base diferente, mas ainda deseja implementar uma interface.

É aí que o gerado **definição de interface** chega.  É uma interface que tem todos os métodos necessários do protocolo.  Isso permite que os desenvolvedores que desejam implementar o protocolo para simplesmente implementar a interface.  O tempo de execução registrará automaticamente o tipo como adotar o protocolo.

Observe que a interface somente lista os métodos necessários e expor os métodos opcionais.   Isso significa que as classes que adotam o protocolo obterá completo verificação de tipo para os métodos necessários, mas serão preciso recorrer a digitação fraca (manualmente usando os atributos de exportação e a assinatura de correspondência) para os métodos de protocolo opcional.

Para torná-lo conveniente para consumir uma API que usa os protocolos, a ferramenta de associação também produzirá uma classe de método de extensões que expõe todos os métodos opcionais.   Isso significa que enquanto você estiver consumindo uma API, você será capaz de tratar protocolos como tendo todos os métodos.

Se você quiser usar as definições de protocolo em sua API, você precisará escrever interfaces vazias esqueleto em sua definição de API.   Se você quiser usar o MeuProtocolo em uma API, você precisa fazer isso:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

As opções acima é necessária porque no tempo de ligação a `IMyProtocol` não existiria, que é por que você precisa fornecer uma interface vazia.

### <a name="adopting-protocol-generated-interfaces"></a>Adoção de interfaces gerado pelo protocolo

Sempre que você implemente uma das interfaces geradas para os protocolos, como este:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

A implementação para os métodos de interface obtém exportada automaticamente com o nome apropriado, portanto, é equivalente a esta:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Não importa se a interface é implementada de forma implícita ou explícita.

### <a name="protocol-inlining"></a>Inlining de protocolo

Enquanto você associa os tipos de Objective-C existentes que foram declarados como adotar um protocolo, você desejará embutido o protocolo diretamente. Para fazer isso, simplesmente declare seu protocolo como uma interface sem nenhuma [ `[BaseType]` ](#BaseTypeAttribute) de atributo e listar o protocolo na lista de interfaces base para a sua interface.

Exemplo:

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```


## <a name="member-definitions"></a>Definições de membro

Os atributos nesta seção são aplicados aos membros individuais de um tipo: as propriedades e declarações de método.


### <a name="alignattribute"></a>AlignAttribute

Usado para especificar o valor de alinhamento para tipos de retorno da propriedade. Certas propriedades usam ponteiros para os endereços que devem estar alinhados em determinados limites (no xamarin. IOS, por exemplo, isso acontece com alguns `GLKBaseEffect` alinhado de propriedades que devem ser de 16 bytes). Você pode usar essa propriedade para decorar o getter e use o valor de alinhamento. Isso normalmente é usado com o `OpenTK.Vector4` e `OpenTK.Matrix4` tipos quando integrado com as APIs do Objective-C.

Exemplo:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

O `[Appearance]` atributo é limitado para o iOS 5, em que o Gerenciador de aparência foi introduzido.

O `[Appearance]` atributo pode ser aplicado a qualquer método ou propriedade que participam de `UIAppearance` framework. Quando esse atributo é aplicado a um método ou propriedade em uma classe, ele instruirá o gerador de associação para criar uma classe fortemente tipada aparência que é usada para definir o estilo de todas as instâncias dessa classe ou as instâncias que correspondem a determinados critérios.

Exemplo:

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

As opções acima geraria o seguinte código no UIToolbar:

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (xamarin. IOS 5.4)

Use o `[AutoReleaseAttribute]` em métodos e propriedades para encapsular a invocação do método ao método em um `NSAutoReleasePool`.

Em Objective-C, existem alguns métodos que retornam valores que são adicionados ao padrão `NSAutoReleasePool`. Por padrão, eles vão para o seu thread `NSAutoReleasePool`, mas como xamarin. IOS também mantém uma referência a seus objetos, desde que reside o objeto gerenciado, você talvez não queira manter uma referência extra no `NSAutoReleasePool` que apenas obter esvaziado até que seu thread Retorna o controle para o próximo segmento ou voltar para o loop principal.

Esse atributo é aplicado por exemplo, nas propriedades pesadas (por exemplo `UIImage.FromFile`) que retorna objetos que foram adicionados para o padrão `NSAutoReleasePool`. Sem esse atributo, as imagens serão retidas desde que o thread não retornou o controle para o loop principal. UF seu thread foi algum tipo de ferramenta de download em segundo plano que está sempre ativo e aguardando o trabalho, as imagens nunca serão liberadas.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

O `[ForcedTypeAttribute]` é usado para impor a criação de um tipo gerenciado, mesmo se o objeto retornado não gerenciado não coincide com o tipo descrito na definição de associação.

Isso é útil quando o tipo descrito em um cabeçalho não coincide com o tipo retornado do método nativo, por exemplo, veja a seguinte definição de Objective-C do `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Ele informe claramente o que ele retornará um `NSURLSessionDownloadTask` da instância, mas ainda ele **retorna** um `NSURLSessionTask`, que é uma superclasse e, portanto, não pode ser convertido para `NSURLSessionDownloadTask`. Como estamos em um contexto de segurança de tipos um `InvalidCastException` ocorrerá.

Para estar de acordo com a descrição do cabeçalho e evitar a `InvalidCastException`, o `[ForcedTypeAttribute]` é usado.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

O `[ForcedTypeAttribute]` também aceita um valor booleano chamado `Owns` que é `false` por padrão `[ForcedType (owns: true)]`. O possui parâmetro é usado para seguir a [política de propriedade](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) para **Foundation Core** objetos.

O `[ForcedTypeAttribute]` é válido somente em parâmetros, propriedades e valor de retorno.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

O `[BindAsAttribute]` permite que a associação `NSNumber`, `NSValue` e `NSString`(enums) em mais precisas C# tipos. O atributo pode ser usado para criar melhores e mais precisos, a API .NET sobre a API nativa.

Você pode decorar métodos (no valor de retorno), parâmetros e propriedades com `BindAs`. A única restrição é que o membro **não deve** estar dentro de uma `[Protocol]` ou [ `[Model]` ](#ModelAttribute) interface.

Por exemplo:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Seria a saída:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente que vamos fazer a `bool?`  <->  `NSNumber` e `CGRect`  <->  `NSValue` conversões.

Os tipos atuais de encapsulamento com suporte são:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

O seguinte C# tipos de dados têm suporte para ser encapsulado de/para `NSValue`:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect / RectangleF
* CGSize / SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

O seguinte C# tipos de dados têm suporte para ser encapsulado de/para `NSNumber`:

* bool
* byte
* double
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* Enums

#### <a name="nsstring"></a>NSString

[`[BindAs]`](#BindAsAttribute) funciona em conjuntion com [enums apoiada por uma constante NSString](#enum-attributes) para que você possa criar melhor API do .NET, por exemplo:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Seria a saída:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Controlaremos o `enum`  <->  `NSString` conversão somente se o tipo de enumeração fornecida a [ `[BindAs]` ](#BindAsAttribute) é [apoiada por uma constante NSString](#enum-attributes).

#### <a name="arrays"></a>Matrizes

[`[BindAs]`](#BindAsAttribute) também dá suporte a matrizes de qualquer um dos tipos com suporte, você pode ter a seguinte definição de API como um exemplo:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Seria a saída:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

O `rects` parâmetro será encapsulado em um `NSArray` que contém uma `NSValue` para cada `CGRect` e, em troca, você obterá uma matriz de `CAScroll?` que foi criado usando os valores de retornado `NSArray` que contém `NSStrings`.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

O `[Bind]` atributo tem dois usos um quando aplicado a um método ou declaração de propriedade e outra quando aplicado a individual getter ou setter em uma propriedade.

Quando usado para um método ou propriedade, o efeito do `[Bind]` atributo é gerar um método que invoca o seletor especificado. Mas o método gerado resultante não é decorado com o [ `[Export]` ](#ExportAttribute) atributo, o que significa que ele não pode participar em Substituir o método. Isso normalmente é usado em combinação com o `[Target]` atributo para a implementação de métodos de extensão do Objective-C.

Por exemplo:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Quando usado em um getter ou setter, o `[Bind]` atributo é usado para alterar os padrões inferidos pelo gerador de código ao gerar os nomes de seletor de Objective-C getter e setter para uma propriedade. Por padrão, quando você sinaliza uma propriedade com o nome `fooBar`, o gerador deve gerar um `fooBar` exportar para o getter e `setFooBar:` do setter. Em alguns casos, o Objective-C não segue essa convenção, normalmente eles alterarem o nome de getter ser `isFooBar`.
Você usaria esse atributo para informar o gerador isso.

Por exemplo:

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>AsyncAttribute

Somente disponível no xamarin. IOS 6.3 e mais recente.

Esse atributo pode ser aplicado a métodos que usam um manipulador de conclusão como o último argumento.

Você pode usar o `[Async]` atributo cujo último argumento é um retorno de chamada de métodos.  Quando você aplicá-la a um método, o gerador de associação irá gerar uma versão desse método com o sufixo `Async`.  Se o retorno de chamada não usa nenhum parâmetro, o valor de retorno será uma `Task`, se o retorno de chamada usa um parâmetro, o resultado será um `Task<T>`.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

O exemplo a seguir gera este método assíncrono:

```csharp
Task LoadFileAsync (string file);
```

Se o retorno de chamada usa vários parâmetros, você deve definir a `ResultType` ou `ResultTypeName` para especificar o nome desejado do tipo gerado que manterá todas as propriedades.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

A seguir gerará este método assíncrono, onde `FileLoading` contém propriedades para acessar ambas `files` e `byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

Se o último parâmetro do retorno de chamada é um `NSError`, em seguida, gerado `Async` método verificará se o valor não for nulo e, se esse for o caso, o método assíncrono gerado definirá a exceção de tarefa.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

As opções acima gera o seguinte método assíncrono:

```csharp
Task<string> UploadAsync (string file);
```

E, em erro, a tarefa resultante terá a exceção definida como um `NSErrorException` que encapsula resultante `NSError`.

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Use essa propriedade para especificar o valor para o retorno de `Task` objeto.   Esse parâmetro usa um tipo existente, assim, ele precisa ser definido em uma das suas definições de api do núcleo.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Use essa propriedade para especificar o valor para o retorno de `Task` objeto.   Esse parâmetro usa o nome de seu tipo desejado, o gerador de produzirá uma série de propriedades, um para cada parâmetro que usa o retorno de chamada.

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

Use essa propriedade para personalizar o nome dos métodos async gerado.   O padrão é usar o nome do método e acrescente o texto "Async", você pode usar isso para alterar esse padrão.

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Esse atributo é aplicado a parâmetros de cadeia de caracteres ou propriedades de cadeia de caracteres e instrui o gerador de código não usar o marshaling de cadeia de caracteres de cópia de zero para esse parâmetro e, em vez disso, crie uma nova instância de NSString do C# cadeia de caracteres.
Esse atributo só é necessário em cadeias de caracteres, se você instruir o gerador de usar o marshaling de cadeia de caracteres de cópia do zero usando qualquer um de `--zero-copy` opção de linha de comando ou definindo o atributo de nível de assembly `ZeroCopyStringsAttribute`.

Isso é necessário em casos em que a propriedade é declarada em Objective-C para ser um `retain` ou `assign` propriedade em vez de um `copy` propriedade. Eles normalmente ocorrem nas bibliotecas de terceiros que foram incorretamente "otimizadas" pelos desenvolvedores. Em geral, `retain` ou `assign` `NSString` propriedades estão incorretas desde `NSMutableString` ou classes derivadas de usuário de `NSString` pode alterar o conteúdo das cadeias de caracteres sem o conhecimento do código de biblioteca, sutilmente quebra a aplicativo. Normalmente, isso acontece devido a otimização prematura.

O exemplo a seguir mostra duas propriedades em Objective-c:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

Quando você aplica a `[DisposeAttribute]` a uma classe, você fornece um trecho de código que será adicionado ao `Dispose()` implementação do método da classe.

Uma vez que o `Dispose` método é gerado automaticamente pelo `bmac-native` e `btouch-native` ferramentas, você precisa usar o `[Dispose]` atributo para injetar um código em gerado `Dispose` implementação do método.

Por exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

O `[Export]` atributo é usado para sinalizar um método ou propriedade a ser exposta para o tempo de execução do Objective-C. Esse atributo é compartilhado entre a ferramenta de associação e os tempos de execução de xamarin. IOS e xamarin. Mac reais. Para métodos, o parâmetro é passado textual para o código gerado, para propriedades, um getter e setter exportações são geradas com base na declaração de base (consulte a seção sobre o [ `[BindAttribute]` ](#BindAttribute) para obter informações sobre como alterar o comportamento da ferramenta de associação).

Sintaxe:

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

O [seletor](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) representa o nome do método subjacente do Objective-C ou da propriedade que está sendo associada.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

Esse atributo é usado para expor uma variável global de C como um campo que é carregado sob demanda e exposto ao C# código. Normalmente, isso é necessário para obter os valores de constantes que são definidos em C ou Objective-C e que pode ser qualquer um dos tokens utilizados em algumas APIs ou cujos valores são opacos e devem ser usados como-está pelo código do usuário.

Sintaxe:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

O `symbolName` é o símbolo de C para vincular com. Por padrão, isso será carregado de uma biblioteca cujo nome é inferido do namespace em que o tipo é definido. Se isso não for a biblioteca em que o símbolo é pesquisado, você deve passar o `libraryName` parâmetro. Se você estiver vinculando a uma biblioteca estática, use `__Internal` como o `libraryName` parâmetro.

As propriedades geradas sempre são estáticas.

Propriedades sinalizadas com o atributo de campo podem ser dos seguintes tipos:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enums

Setters não têm suporte para [enums apoiado por constantes NSString](#enum-attributes), mas eles podem ser manualmente associados se necessário.

Exemplo:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

O `[Internal]` atributo pode ser aplicado a métodos ou propriedades e tem o efeito de sinalizar o código gerado com o `internal` C# palavra-chave, tornando o código acessível apenas ao código no assembly gerado. Isso normalmente é usado para ocultar as APIs que estão em um nível muito baixo ou fornecer uma API pública de qualidade inferior que você deseja melhorar após ou para as APIs que não têm suporte pelo gerador e requerem alguma codificação de mão.

Ao projetar a associação, normalmente seria ocultar o método ou propriedade utilizando esse atributo e forneça um nome diferente para a propriedade ou método e, em seguida, no seu C# arquivo de suporte complementares, você adicionaria um wrapper fortemente tipado que expõe o funcionalidade subjacente.

Por exemplo:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Em seguida, em seu arquivo de suporte, você pode ter um código como este:

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

Esse atributo sinaliza o campo de suporte para uma propriedade a ser anotado com o .NET `[ThreadStatic]` atributo. Isso é útil se o campo for uma variável estática de thread.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (xamarin. IOS 6.0.6)

Esse atributo fará com que um método suporte nativo (Objective-C) as exceções.
Em vez de chamar `objc_msgSend` diretamente, a invocação de percorrer um trampoline personalizado que captura exceções ObjectiveC e empacota-los em exceções gerenciadas.

Atualmente, apenas alguns `objc_msgSend` há suporte para assinaturas (você irá descobrir se uma assinatura não tem suporte quando nativo de vinculação de um aplicativo que usa a associação falha com um monotouch_ ausente *_objc_msgSend* símbolo), mas mais podem ser adicionado na solicitação.


### <a name="newattribute"></a>Novoatributo

Esse atributo é aplicado aos métodos e propriedades para ter o gerador de geram o `new` palavra-chave na frente da declaração.

Ele é usado para evitar avisos do compilador quando o mesmo método ou o nome da propriedade é introduzido em uma subclasse que já existia em uma classe base.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

Você pode aplicar esse atributo para os campos para ter a produção gerador para uma classe de notificações auxiliar fortemente tipada.

Esse atributo pode ser usado sem argumentos para notificações de que não executar nenhum conteúdo, ou você pode especificar um `System.Type` que faz referência a outra interface na definição de API, normalmente com o nome que termina com "EventArgs". O gerador transformará a interface em uma classe que pode efetuar subclasses `EventArgs` e incluirá todas as propriedades na lista. O [ `[Export]` ](#ExportAttribute) atributo deve ser usado no `EventArgs` classe para listar o nome da chave usada para pesquisar o dicionário de Objective-C para buscar o valor.

Por exemplo:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

O código acima irá gerar uma classe aninhada `MyClass.Notifications` com os seguintes métodos:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Os usuários do seu código, em seguida, podem facilmente assinar notificações lançadas para o [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando código como este:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Ou para definir um objeto específico a ser observado. Se você passar `null` para `objectToObserve` esse método se comportarão como seu outra ponto a ponto.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

O valor retornado de `ObserveDidStart` pode ser usado para facilmente interromper o recebimento de notificações, como este:

```csharp
token.Dispose ();
```

Ou você pode chamar [NSNotification.DefaultCenter.RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) e passar o token. Se a notificação contiver parâmetros, você deve especificar um auxiliar `EventArgs` interface como esta:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

Acima vai gerar uma `MyScreenChangedEventArgs` classe com o `ScreenX` e `ScreenY` propriedades que buscará os dados do [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) usando as chaves de dicionário `ScreenXKey` e `ScreenYKey` respectivamente e aplicar as conversões apropriadas. O `[ProbePresence]` atributo é usado para o gerador para investigar se a chave é definida `UserInfo`, em vez de tentar extrair o valor. Isso é usado para casos em que a presença da chave é o valor (normalmente para valores boolianos).

Isso permite que você escreva código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

Em alguns casos, não há nenhuma constante associada com o valor passado no dicionário.  Apple, às vezes, usa constantes de símbolos públicos e, às vezes, usa constantes de cadeia de caracteres.  Por padrão o [ `[Export]` ](#ExportAttribute) atributo no seu fornecido `EventArgs` classe usará o nome especificado como um símbolo de público a ser pesquisado em tempo de execução.  Se isso não for o caso e, em vez disso, ele deve ser pesquisado como uma constante de cadeia de caracteres, em seguida, passar o `ArgumentSemantic.Assign` valor para o atributo de exportação.

**Novo no xamarin. IOS 8.4**

Às vezes, as notificações começará a vida sem nenhum argumento, portanto, o uso de [ `[Notification]` ](#NotificationAttribute) sem argumentos é aceitável.  Mas, às vezes, os parâmetros para a notificação serão introduzidos.  Para dar suporte a esse cenário, o atributo pode ser aplicado mais de uma vez.

Se você estiver desenvolvendo uma associação, e você deseja evitar a interrupção de código de usuário existente, você se voltaria uma notificação existente do:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

Em uma versão que lista o atributo de notificação de duas vezes, como este:

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

Quando isso é aplicado a uma propriedade ele sinaliza que a propriedade como permitir que o valor `null` a ser atribuído a ele. Isso só é válido para tipos de referência.

Quando isso é aplicado a um parâmetro em uma assinatura de método, ele indica que o parâmetro especificado pode ser nulo e que nenhuma verificação deve ser realizada para passar `null` valores.

Se o tipo de referência não tem esse atributo, a ferramenta de associação irá gerar uma verificação para o valor que está sendo atribuído antes de passá-lo para Objective-C e gerará uma verificação que gerará um `ArgumentNullException` se o valor atribuído é `null`.

Por exemplo:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

Use esse atributo para instruir o gerador de associação que a associação para esse método específico deve ser sinalizada com um `override` palavra-chave.

### <a name="presnippetattribute"></a>PreSnippetAttribute

Você pode usar esse atributo para injetar um código a ser inserido depois que os parâmetros de entrada forem validados, mas antes de chamar o código em Objective-C.

Exemplo:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Você pode usar esse atributo para injetar um código a ser inserido antes de qualquer um dos parâmetros são validados no método gerado.

Exemplo:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Instrui o gerador de associação para invocar a propriedade especificada desta classe para buscar um valor de-lo.

Essa propriedade normalmente é usada para atualizar o cache que aponta para objetos de referência que mantêm o grafo do objeto referenciado. Normalmente, ele aparece no código que tem operações como adicionar ou remover. Esse método é usado para que depois que os elementos são adicionados ou removidos do que o cache interno atualizado para garantir que estamos mantendo gerenciadas referências a objetos que estão realmente em uso. Isso é possível porque a ferramenta de vinculação gera um campo de suporte para todos os objetos de referência em uma determinada vinculação.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

Nesse caso, o `Dependencies` propriedade será invocada após a adição ou remoção de dependências do `NSOperation` objetos, impedir vazamentos de memória, bem como a corrupção da memória de carregados do objeto, assegurando que temos um gráfico que representa o valor real.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

Você pode usar esse atributo para injetar algum C# código-fonte para ser inserido depois que o código tenha chamado o método subjacente do Objective-C

Exemplo:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Esse atributo é aplicado para retornar valores para sinalizá-los como sendo objetos de proxy. Alguns objetos de retorno do proxy de APIs do Objective-C que não podem ser diferenciados de associações de usuário. O efeito desse atributo é sinalizar o objeto como sendo um `DirectBinding` objeto. Para um cenário no xamarin. Mac, você pode ver os [discussão sobre esse bug](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Instrui o gerador para manter uma referência gerenciada para o parâmetro ou remover uma referência interna para o parâmetro. Isso é usado para manter os objetos referenciados.

Sintaxe:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se o valor de `doAdd` for true, o parâmetro é adicionado para o `__mt_{0}_var List<NSObject>;`. Em que `{0}` é substituído com o determinado `listName`. Você deve declarar este campo de suporte em sua classe parcial complementar à API.

Para obter um exemplo, consulte [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (xamarin. IOS 6.0)

Isso pode ser aplicado para retornar tipos para indicar que o gerador deve chamar `Release` no objeto antes de retorná-lo. Isso é necessário somente quando um método fornece um objeto retido (em vez de um objeto autoreleased, que é o cenário mais comum)

Exemplo:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Além disso, esse atributo é propagado para o código gerado, para que o tempo de execução do xamarin. IOS Saiba que ele deve manter o objeto após o retorno para Objective-C de uma função.


### <a name="sealedattribute"></a>SealedAttribute

Instrui o gerador para sinalizar o método gerado como sealed. Se esse atributo não for especificado, o padrão é gerar um método virtual (um método virtual, um método abstrato ou uma substituição, dependendo de como os outros atributos são usados).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

Quando o `[Static]` atributo é aplicado a um método ou propriedade, isso gera um método estático ou propriedade. Se esse atributo não for especificado, o gerador produz uma propriedade ou método de instância.


### <a name="transientattribute"></a>TransientAttribute

Use esse atributo para propriedades de sinalizador cujos valores são transitórios, ou seja, os objetos que são criados temporariamente pelo iOS, mas não têm longa duração. Quando esse atributo é aplicado a uma propriedade, o gerador não cria um campo de suporte para essa propriedade, o que significa que a classe gerenciada não mantém uma referência ao objeto.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

No design das associações Xamarin.iOS/Xamarin.Mac, o `[Wrap]` atributo é usado para encapsular um objeto com tipagem fraca com um objeto fortemente tipado. Isso é usado principalmente com objetos de delegados de Objective-C que normalmente são declarados como sendo do tipo `id` ou `NSObject`. A convenção usada pelo xamarin. IOS e xamarin. MAC é expor essas fontes de dados ou delegados como sendo do tipo `NSObject` e são nomeadas usando a convenção "Weak" + o nome que está sendo exposto. Uma `id delegate` propriedade do Objective-C deve ser exposta como um `NSObject WeakDelegate { get; set; }` propriedade no arquivo do contrato de API.

Mas normalmente o valor que é atribuído a esse delegado é de um tipo forte, portanto, o tipo forte de superfície e aplicar o `[Wrap]` atributo, isso significa que os usuários podem escolher usar tipos fracos, se precisar de algum fine-controle ou caso eles precisem recorrer a tric de baixo nível ks, ou se eles podem usar a propriedade fortemente tipados para a maioria de seu trabalho.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

Isso é como o usuário usaria a versão com tipagem fraca do delegado:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

E isso é como o usuário usa a versão fortemente tipada, observe que o usuário tira proveito do C#do sistema de tipos e está usando a palavra-chave override declarar sua intenção e que ele não tem manualmente decorar o método com `[Export]`, desde que criamos funcionou na associação para o usuário:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Outro uso do `[Wrap]` atributo deve dar suporte a versão fortemente tipada dos métodos.  Por exemplo:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Os membros gerados pelo `[Wrap]` não são `virtual` por padrão, se você precisar de uma `virtual` membro pode ser definido como `true` opcional `isVirtual` parâmetro.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

## <a name="parameter-attributes"></a>Atributos de parâmetro

Esta seção descreve os atributos que você pode aplicar aos parâmetros em uma definição de método, bem como a `[NullAttribute]` que se aplica a uma propriedade como um todo.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Esse atributo é aplicado a tipos de parâmetro em C# declarações de delegado para notificar o associador que o parâmetro em questão está de acordo com o Objective-C bloquear a convenção de chamada e deve empacotar dessa maneira.

Isso normalmente é usado para retornos de chamada que são definidos como este em Objective-c:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Confira também:  [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Esse atributo é aplicado a tipos de parâmetro em C# delegar declarações para notificar o associador que o parâmetro em questão está de acordo com o ponteiro de função C ABI convenção de chamada e deve empacotar dessa maneira.

Isso normalmente é usado para retornos de chamada que são definidos como este em Objective-c:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Confira também:  [BlockCallback](#BlockCallback).

### <a name="params"></a>param. autom.

Você pode usar o `[Params]` o último parâmetro de matriz de uma definição de método tenha o gerador de injetar um "params" na definição do atributo.   Isso permite que a associação permitir facilmente para parâmetros opcionais.

Por exemplo, a seguinte definição:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Permite que o código a seguir a ser gravado:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Isso tem a vantagem adicional que não exige que os usuários criem uma matriz puramente para a passagem de elementos.

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

Você pode usar o `[PlainString]` atributo na frente de parâmetros de cadeia de caracteres para instruir o gerador de associação para passar a cadeia de caracteres como uma cadeia de caracteres do C, em vez de passar o parâmetro como um `NSString`.

A maioria das APIs do Objective-C consumir `NSString` parâmetros, mas um punhado de APIs que expõem uma `char *` API para passar cadeias de caracteres, em vez do `NSString` variação.
Use `[PlainString]` nesses casos.

Por exemplo, as seguintes declarações de Objective-C:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Deve ser associado como este:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

Instrui o gerador para manter uma referência ao parâmetro especificado. O gerador fornecerá o repositório de backup para esse campo ou você pode especificar um nome (o `WrapName`) para armazenar o valor no. Isso é útil para manter uma referência para um objeto gerenciado que é passado como um parâmetro para Objective-C e quando você sabe que o Objective-C apenas irá manter esta cópia do objeto. Por exemplo, uma API como `SetDisplay (SomeObject)` usaria esse atributo é provável que o SetDisplay poderia exibir apenas um objeto por vez. Se você precisar manter o controle de mais de um objeto (por exemplo, para uma API semelhante a pilha) você usaria o `[RetainList]` atributo.

Sintaxe:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

Instrui o gerador para manter uma referência gerenciada para o parâmetro ou remover uma referência interna para o parâmetro. Isso é usado para manter os objetos referenciados.

Sintaxe:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se o valor de `doAdd` for true, o parâmetro é adicionado para o `__mt_{0}_var List<NSObject>`. Em que `{0}` é substituído com o determinado `listName`. Você deve declarar este campo de suporte em sua classe parcial complementar à API.

Para obter um exemplo, consulte [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

Esse atributo é aplicado a parâmetros e é usado somente quando a transição do Objective-C para C#.  Durante as transições de Objective-C várias `NSObject` parâmetros são encapsulados em uma representação gerenciada do objeto.

O tempo de execução se uma referência ao objeto nativo e manter a referência até que a última referência gerenciada para o objeto não existe mais, e o GC tem uma chance de ser executado.

Em alguns casos, é importante para o C# tempo de execução para não manter uma referência ao objeto nativo.  Às vezes, isso acontece quando o código nativo subjacente foi anexado a um comportamento especial para o ciclo de vida do parâmetro.  Por exemplo: o destruidor para o parâmetro realizar alguma ação de limpeza ou algum recurso precioso de descarte.

Esse atributo informa o tempo de execução que você deseja que o objeto a ser descartado se possível, ao retornar para Objective-C do seu método substituído.

A regra é simples: se o tempo de execução tinha que criar uma nova representação gerenciada do objeto nativo, em seguida, no final da função, a contagem de retenção para o objeto nativo será removida e a propriedade do identificador do objeto gerenciado será limpo.   Isso significa que, se você manteve uma referência ao objeto gerenciado, que fazem referência tornará inútil (invocar métodos nele irá acionar uma exceção).

Se o objeto passado não foi criado, ou se já houver uma representação gerenciada pendente do objeto, o descarte forçado não ocorrerão. 


## <a name="property-attributes"></a>Atributos de propriedade

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Este atributo é usado para dar suporte a um idioma de Objective-C em que uma propriedade com um getter é introduzida em uma classe base e uma subclasse mutável apresenta um setter.

Uma vez que C# não oferece suporte nesse modelo, a classe base precisa ter o setter e getter, e uma subclasse pode usar o [OverrideAttribute](#OverrideAttribute).

Esse atributo é usado somente em setters de propriedade e é usado para oferecer suporte a linguagem mutável em Objective-C.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Atributos Enum

Mapeando `NSString` constantes para valores de enumeração é uma maneira fácil de criar o melhor de API .NET. It:

* permite que a conclusão de código ser mais útil, mostrando **apenas** os valores corretos para a API;
* Adiciona segurança de tipos, você não pode usar outro `NSString` constante em um contexto incorreto; e
* permite ocultar algumas constantes, fazendo com que o preenchimento de código mostram a lista mais curta de API sem perda de funcionalidade.

Exemplo:

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}
```

Da definição de associação acima criará o gerador de `enum` em si e também criará um `*Extensions` tipo estático que inclui os métodos de duas maneiras de conversão entre os valores de enumeração e a `NSString` constantes. Isso significa que as constantes permanece disponível para desenvolvedores, mesmo se eles não fazem parte da API.

Exemplos:

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

Você pode decorar **um** valor de enumeração com esse atributo. Isso se tornará a constante que está sendo retornada se o valor de enum não é conhecido.

No exemplo acima:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Se nenhum valor de enum é decorado, um `NotSupportedException` será lançada.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Códigos de erro são associados como uma enumeração de valores. Geralmente é um erro de domínio para eles e nem sempre é fácil de localizar qual delas se aplica (ou se ainda houver).

Você pode usar esse atributo para associar o domínio de erro enum em si.

Exemplo:

```csharp
    [Native]
    [ErrorDomain ("AVKitErrorDomain")]
    public enum AVKitError : nint {
        None = 0,
        Unknown = -1000,
        PictureInPictureStartFailed = -1001
    }
```

Em seguida, você pode chamar o método de extensão `GetDomain` para obter o domínio constante de qualquer erro.

### <a name="fieldattribute"></a>FieldAttribute

Isso é o mesmo `[Field]` usado para constantes de tipo de atributo. Ele também pode ser usado dentro de enumerações para mapear um valor com uma constante específico.

Um `null` valor pode ser usado para especificar qual valor de enum deve ser retornado se um `null` `NSString` constante for especificada.

No exemplo acima:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Se nenhum `null` valor está presente e em seguida, um `ArgumentNullException` será lançada.

## <a name="global-attributes"></a>Atributos globais

Atributos globais também são aplicados usando o `[assembly:]` modificador de atributo, como o [ `[LinkWithAttribute]` ](#LinkWithAttribute) ou podem ser usados em qualquer lugar, como o [ `[Lion]` ](#SinceAndLionAttributes) e [ `[Since]` ](#SinceAndLionAttributes) atributos.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Este é um atributo de nível de assembly, que permite aos desenvolvedores especificar os sinalizadores de vinculação necessários reutilizar uma biblioteca acoplada sem forçar o consumidor da biblioteca para configurar manualmente o gcc_flags e argumentos mtouch adicionais passados para uma biblioteca.

Sintaxe:

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

Esse atributo é aplicado no nível de assembly, por exemplo, esse é o que o [CorePlot associações](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) usar:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Quando você usa o `[LinkWith]` atributo especificado `libraryName` está incorporado no assembly resultante, permitindo que os usuários para o envio de uma única DLL que contém as dependências não gerenciadas, bem como os sinalizadores de linha de comando necessários para consumir corretamente o biblioteca do xamarin. IOS.

Também é possível fornecer um `libraryName`, caso em que o `LinkWith` atributo pode ser usado para especificar somente os sinalizadores do vinculador adicionais:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Construtores de LinkWithAttribute

Esses construtores permitem que você especifique a biblioteca para vincular com e inserir em seu assembly resultante, os destinos com suporte que dá suporte à biblioteca e os sinalizadores de biblioteca opcional que são necessários para vincular à biblioteca.

Observe que o `LinkTarget` argumento é inferido pelo xamarin. IOS e não precisa ser definido.

Exemplos:

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```

#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

O `ForceLoad` propriedade é usada para decidir se ou não o `-force_load` sinalizador de link é usado para vincular a biblioteca nativa. Por enquanto, isso deve ser verdadeiro.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Se a biblioteca que está sendo associada tem um requisito rígido em quaisquer estruturas (diferente de `Foundation` e `UIKit`), você deve definir o `Frameworks` propriedade como uma cadeia de caracteres que contém uma lista delimitada por espaço das estruturas de plataforma necessárias. Por exemplo, se você estiver associando a uma biblioteca que requer `CoreGraphics` e `CoreText`, você definiria o `Frameworks` propriedade `"CoreGraphics CoreText"`.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Defina essa propriedade como true se o executável resultante precisa ser compilado usando um compilador de C++ em vez do padrão, o que é um compilador C. Use essa opção se a biblioteca que você está associando foi escrita em C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

O nome da biblioteca não gerenciada para agrupar. Este é um arquivo com a extensão "1).a" e pode conter código de objeto para várias plataformas (por exemplo, ARM e x86 para o simulador).

Versões anteriores do xamarin. IOS verificada a `LinkTarget` propriedade para determinar a plataforma de sua biblioteca com suporte, mas isso agora é detectado automaticamente e o `LinkTarget` propriedade será ignorada.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

O `LinkerFlags` cadeia de caracteres fornece uma maneira para autores de associação especificar os sinalizadores do vinculador adicionais necessários ao vincular a biblioteca nativa no aplicativo.

Por exemplo, se a biblioteca nativa requer libxml2 e zlib, você definiria o `LinkerFlags` de cadeia de caracteres para `"-lxml2 -lz"`.

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

Versões anteriores do xamarin. IOS verificada a `LinkTarget` propriedade para determinar a plataforma de sua biblioteca com suporte, mas isso agora é detectado automaticamente e o `LinkTarget` propriedade será ignorada.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Defina essa propriedade como true se a biblioteca que você está vinculando requer a biblioteca de tratamento de exceção de GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

O `SmartLink` propriedade deve ser definida como true para permitir que o xamarin. IOS determine se `ForceLoad` é necessário ou não.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

O `WeakFrameworks` propriedade funciona da mesma forma que o `Frameworks` propriedade, exceto que em tempo de vinculação, o `-weak_framework` especificador é passado para a gcc para cada estrutura listada.

`WeakFrameworks` torna possível para bibliotecas e aplicativos para vincular de modo fraco em relação a estruturas de plataforma para que eles podem usá-los, opcionalmente, se eles estão disponíveis, mas não terão uma dependência neles que é útil se sua biblioteca destina-se para adicionar recursos extras nos mais recentes versões do iOS. Para obter mais informações sobre como vincular fraca, consulte a documentação da Apple no [vinculação fraca](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Bons candidatos para vinculação fraca seria `Frameworks` , como contas, `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` e `Twitter` , pois eles só estão disponíveis no iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) e LionAttribute (macOS)

Você usa o `[Since]` atributo ao sinalizador APIs como tendo que estão sendo introduzidos em um determinado ponto no tempo. O atributo deve ser usado apenas para sinalizar tipos e métodos que podem causar um problema de tempo de execução se a classe, método ou propriedade subjacente não estiver disponível.

Sintaxe:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Ela deve em geral não ser aplicada a enumerações, restrições ou novas estruturas como aqueles não pode causar um erro de tempo de execução se forem executados em um dispositivo com uma versão mais antiga do sistema operacional.

Exemplo, quando aplicado a um tipo:

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Exemplo de quando aplicado a um novo membro:

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

O `[Lion]` atributo é aplicado da mesma forma, mas para tipos introduzidos com Lion. A razão para usar `[Lion]` versus o número de versão mais específico que é usado no iOS é se iOS é revisado com muita frequência, enquanto as versões principais dos X raramente ocorrem e é mais fácil lembrar-se o sistema operacional por seu codinome que por seus números de versão

### <a name="adviceattribute"></a>AdviceAttribute

Use esse atributo para dar aos desenvolvedores uma dica sobre outras APIs que podem ser mais conveniente para que eles possam usar.   Por exemplo, se você fornecer uma versão fortemente tipada de uma API, você pode usar esse atributo do atributo com tipagem fraca para direcionar o desenvolvedor para a API melhor.

As informações desse atributo são mostradas na documentação e ferramentas podem ser desenvolvidas para oferecer sugestões do usuário sobre como melhorar

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Somente disponível no xamarin. IOS 5.4 e mais recente.

Esse atributo instrui o gerador de que a associação para esta biblioteca específica (se forem aplicadas com `[assembly:]`) ou o tipo deve usar o marshaling de cadeia de caracteres de cópia de zero rápido. Esse atributo é equivalente a passar a opção de linha de comando `--zero-copy` para o gerador.

Ao usar a cópia de zero para cadeias de caracteres, o gerador de efetivamente usa o mesmo C# cadeia de caracteres como a cadeia de caracteres que consome de Objective-C sem incorrer a criação de um novo `NSString` objeto e evitando a cópia dos dados do C# cadeias de caracteres à Cadeia de caracteres de Objective-C. A única desvantagem do uso de cadeias de caracteres de cópia de Zero é que você deve garantir que qualquer propriedade de cadeia de caracteres que você encapsule que tenta ser sinalizada como `retain` ou `copy` tem o `[DisableZeroCopy]` conjunto de atributos. Isso é necessário porque o identificador para cadeias de caracteres de zero-cópia está alocado na pilha e é inválido após o retorno da função.

Exemplo:

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

Você também pode aplicar o atributo no nível de assembly, e ele aplicará a todos os tipos do assembly:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Dicionários fortemente tipados

Com o xamarin. IOS 8.0 que introduzimos o suporte para criar facilmente classes fortemente tipadas que wrap `NSDictionaries`.

Embora sempre foi possível usar o [DictionaryContainer](xref:Foundation.DictionaryContainer) de tipo de dados junto com uma API manual, agora é muito mais simples de fazer isso.  Para obter mais informações, consulte [identificando forte tipos](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Quando esse atributo é aplicado a uma interface, o gerador de produzirá uma classe com o mesmo nome que a interface que deriva de [DictionaryContainer](xref:Foundation.DictionaryContainer) e transforma cada propriedade definida na interface em fortemente tipado getter e setter para o dicionário.

Isso gera automaticamente uma classe que pode ser instanciada de uma já existente `NSDictionary` ou que foi criado novo.

Esse atributo aceita um parâmetro, o nome da classe que contém as chaves que são usadas para acessar os elementos no dicionário.   Por padrão, cada propriedade na interface com o atributo será pesquisar um membro no tipo especificado para um nome com o sufixo "Chave".

Por exemplo:

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

No caso acima, o `MyOption` classe produzirá uma propriedade de cadeia de caracteres para `Name` que usará o `MyOptionKeys.NameKey` como a chave no dicionário para recuperar uma cadeia de caracteres.   E usará o `MyOptionKeys.AgeKey` como a chave no dicionário para recuperar um `NSNumber` que contém um inteiro.

Se você quiser usar uma chave diferente, você pode usar o atributo de exportação na propriedade, por exemplo:

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>Tipos de dicionário de alta segurança

Os seguintes tipos de dados dá suporte a `StrongDictionary` definição:

|C#Tipo de interface|`NSDictionary` Tipo de armazenamento|
|---|---|
|`bool`|`Boolean` armazenados em um `NSNumber`|
|Valores de enumeração|inteiro são armazenados em um `NSNumber`|
|`int`|inteiro de 32 bits armazenado em um `NSNumber`|
|`uint`|o inteiro sem sinal de 32 bits armazenado em um `NSNumber`|
|`nint`|`NSInteger` armazenados em um `NSNumber`|
|`nuint`|`NSUInteger` armazenados em um `NSNumber`|
|`long`|inteiro de 64 bits armazenado em um `NSNumber`|
|`float`|inteiro de 32 bits armazenado como um `NSNumber`|
|`double`|inteiro de 64 bits armazenado como um `NSNumber`|
|`NSObject` e subclasses|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array` de `NSObject`|`NSArray`|
|C#`Array` de enumerações|`NSArray` que contém `NSNumber` valores|
