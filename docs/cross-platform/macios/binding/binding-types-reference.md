---
title: Guia de referência de tipos de associação
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: e064eda3db9aa0156869cf1c7392823553af9bd2
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="binding-types-reference-guide"></a>Guia de referência de tipos de associação

Este documento descreve a lista de atributos que você pode usar para anotar os arquivos de contrato de API para a associação da unidade e o código gerado

Contratos xamarin e Xamarin.Mac API são gravados em c# principalmente como definições de interface que define a maneira que o código Objective-C é exposto ao c#. O processo envolve uma mistura de declarações de interface mais algumas definições de tipo básico que pode exigir que o contrato de API. Para obter uma introdução aos tipos de associação, consulte nosso guia complementar [associação Objective-C bibliotecas](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definições de tipo

Sintaxe:

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Cada interface em sua definição de contrato que tem o [ `[BaseType]` ](#BaseTypeAttribute) atributo que declara o tipo base para o objeto gerado. A declaração acima um `MyType` classe c# tipo será gerado que é associado a um tipo Objective-C chamado `MyType`.

Se você especificar quaisquer tipos após o typename (no exemplo acima `Protocol1` e `Protocol2`) usando a sintaxe de herança de interface o conteúdo dessas interfaces será embutido como se tivessem sido parte do contrato de `MyType`.
A maneira como um tipo adota um protocolo de superfícies de xamarin por inlining todos os métodos e propriedades que foram declaradas no protocolo para o próprio tipo.

A seguir mostra como a declaração Objective-C `UITextField` seria definido em um contrato de xamarin:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Deve ser escrito assim como um contrato de API do c#:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Você pode controlar muitos outros aspectos da geração de código, aplicar outros atributos para a interface, bem como configurar o [ `[BaseType]` ](#BaseTypeAttribute) atributo.


### <a name="generating-events"></a>Gerando eventos

Um recurso do design xamarin e Xamarin.Mac API é que podemos mapear classes de delegate Objective-C como c# eventos e retornos de chamada. Os usuários podem escolher se desejam adotar o padrão de programação Objective-C, atribuindo a propriedades, como em uma base por instância `Delegate` uma instância de uma classe que implementa os vários métodos que o tempo de execução Objective-C chamaria ou por escolhendo o c#-eventos e propriedades de estilo.

Vamos ver um exemplo de como usar o modelo Objective-C:

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

No exemplo acima, você pode ver que escolhemos para substituir os dois métodos, uma notificação de que um evento de rolagem obteve local e o segundo que haja um retorno de chamada que deve retornar um valor booliano instruindo o `scrollView` deve rolar para a principais ou não.

O modelo c# permite que o usuário da sua biblioteca ouvir as notificações usando a sintaxe de evento c# ou a propriedade para ligar retornos de chamada que são esperados para retornar valores.

Isso é como o código c# para o mesmo recurso aparência usando lambdas:

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

Como os eventos não retornam valores (eles têm um tipo de retorno void), você pode conectar várias cópias. O `ShouldScrollToTop` não é um evento, em vez disso, é uma propriedade com o tipo de `UIScrollViewCondition` que possui essa assinatura:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Ele retorna um `bool` valor, nesse caso a sintaxe lambda nos permite retornar apenas o valor de `MakeDecision` função.

O gerador de associação oferece suporte à geração de eventos e propriedades que vinculam uma classe como `UIScrollView` com seus `UIScrollViewDelegate` (também chamada de esses a classe de modelo), isso é feito com a anotação seu [ `[BaseType]` ](#BaseTypeAttribute) definição com o `Events` e `Delegates` parâmetros (descritos abaixo). Além de anotar o [ `[BaseType]` ](#BaseTypeAttribute) com esses parâmetros, é necessário informar o gerador de alguns componentes mais.

Para eventos que levam mais de um parâmetro (em Objective-C a convenção é que o primeiro parâmetro em uma classe de delegado a instância do objeto do remetente) você deve fornecer o nome que você gostaria de gerado `EventArgs` classe. Isso é feito com o [ `[EventArgs]` ](#EventArgsAttribute) atributo na declaração de método em sua classe de modelo. Por exemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

A declaração acima gerará um `UIImagePickerImagePickedEventArgs` classe que deriva de `EventArgs` e pacotes de ambos os parâmetros, o `UIImage` e `NSDictionary`. O gerador de produz isso:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Expõe o seguinte, em seguida, o `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Métodos de modelo que retornam um valor associados diferente. Aqueles exigem um nome para o gerado c# delegado (a assinatura do método) e também um valor padrão retornar caso o usuário não fornecer uma implementação se. Por exemplo, o `ShouldScrollToTop` definição é este:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

As opções acima criará um `UIScrollViewCondition` delegar com a assinatura que foi mostrado acima, e se o usuário não fornecer uma implementação, o valor de retorno será true.

Além de [ `[DefaultValue]` ](#DefaultValueAttribute) atributo, você também pode usar o [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute) atributo que direciona o gerador para retornar o valor do parâmetro especificado na chamada ou a [ `[NoDefaultValue]` ](#NoDefaultValueAttribute) parâmetro que instrui o gerador de que não há nenhum valor padrão.

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

Você usa o `Name` propriedade para controlar o nome desse tipo associada no mundo Objective-C. Isso normalmente é usado para dar um nome que está em conformidade com as diretrizes de Design do .NET Framework, mas que mapeia para um nome em Objective-C que não segue essa convenção de tipo c#.

Exemplo, no caso a seguir é mapear o Objective-C `NSURLConnection` tipo `NSUrlConnection`, conforme as diretrizes de Design do .NET Framework usar "Url" em vez de "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

O nome especificado é usado como o valor para gerado `[Register]` atributo na associação. Se `Name` não for especificado, o nome do tipo curto é usado como o valor para o `[Register]` atributo na saída gerada.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events e BaseType.Delegates

Essas propriedades são usadas para a geração do c#-estilo de eventos em classes geradas. Eles são usados para vincular a uma determinada classe com sua classe de delegado Objective-C. Você encontrará muitos casos em que uma classe usa uma classe delegate para enviar notificações e eventos. Por exemplo um `BarcodeScanner` teria um complemento `BardodeScannerDelegate` classe. O `BarcodeScanner` classe normalmente têm um `Delegate` propriedade que você atribui uma instância de `BarcodeScannerDelegate` para enquanto isso funciona, talvez você queira expor para seus usuários c#-como a interface de eventos de estilo e, nesses casos, você usaria o `Events`e `Delegates` propriedades do [ `[BaseType]` ](#BaseTypeAttribute) atributo.

Essas propriedades são sempre definidas juntos e devem ter o mesmo número de elementos e ser mantidas em sincronia. O `Delegates` matriz contém uma cadeia de caracteres para cada representante de tipo fraco que você deseja incluir, e o `Events` matriz contém um tipo para cada tipo que você deseja associar a ele.

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

Se você aplicar esse atributo quando novas instâncias dessa classe são criadas, a instância do objeto será mantida até que o método referenciado pelo `KeepRefUntil` foi chamado. Isso é útil para melhorar a usabilidade de suas APIs, quando você não desejar que o usuário para manter uma referência a um objeto em torno de usar seu código. O valor dessa propriedade é o nome de um método no `Delegate` de classe, você deve usar isso em combinação com o `Events` e `Delegates` propriedades também.

O exemplo a seguir mostram como isso é usado por `UIActionSheet` em xamarin:

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

Quando esse atributo é aplicado à definição da interface, ele impedirá o gerador de produzir o construtor padrão.

Use esse atributo quando é necessário o objeto a ser inicializado com um dos construtores de classe.


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Quando esse atributo é aplicado à definição da interface, ele marcará o construtor padrão como particular. Isso significa que você ainda pode instanciar o objeto dessa classe internamente do seu arquivo de extensão, mas ele apenas não estar acessível a usuários da sua classe.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Use esse atributo em uma definição de tipo para associar categorias Objective-C e expor como métodos de extensão c# para espelhar a maneira que Objective-C expõe a funcionalidade.

Categorias são um mecanismo de Objective-C usado para estender o conjunto de métodos e propriedades disponíveis em uma classe.   Na prática, eles são usados para estender a funcionalidade de uma classe base (por exemplo `NSObject`) quando uma estrutura específica estiver vinculada (por exemplo `UIKit`), tornando seus métodos disponíveis, mas somente se a nova estrutura está vinculada.   Em alguns casos, eles são usados para organizar os recursos em uma classe por funcionalidade.   Eles são semelhantes em espírito para métodos de extensão do c#.

Isso é a aparência uma categoria no objetivo-c:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

O exemplo acima é encontrado em uma biblioteca que se estenderá instâncias do `UIView` com o método `makeBackgroundRed`.

Para associar os, você pode usar o [ `[Category]` ](#CategoryAttribute) atributo em uma definição de interface.   Ao usar o [ `[Category]` ](#CategoryAttribute) de atributo, o significado do [ `[BaseType]` ](#BaseTypeAttribute) atributo muda de que está sendo usado para especificar a classe base para estender a ser o tipo a ser estendido.

A seguir mostra como o `UIView` extensões são vinculadas e transformadas em métodos de extensão do c#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

As opções acima criará uma `MyUIViewExtension` uma classe que contém o `MakeBackgroundRed` método de extensão.   Isso significa que, agora, você pode chamar `MakeBackgroundRed` em qualquer `UIView` subclasse, fornecendo a mesma funcionalidade que você obteria no objetivo-C.

Em alguns casos você encontrará **estático** membros dentro de categorias, como no exemplo a seguir:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Isso levará a uma **incorreto** definição de interface categoria c#:

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

Isso está incorreto porque a usar o `BoolMethod` extensão você precisa de uma instância do `FooObject` , mas você está associando um ObjC **estático** extensão, este é um efeito colateral devido ao fato de como os métodos de extensão do c# são implementados.

É a única maneira de usar as definições acima pelo feio código a seguir:

```csharp
(null as FooObject).BoolMethod (range);
```

A recomendação para evitar isso é embutido a `BoolMethod` definição dentro de `FooObject` definição de interface em si, isso permitirá que você possa chamar essa extensão como destina-se `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Podemos emitirá um aviso (BI1117) sempre que encontramos uma [ `[Static]` ](#StaticAttribute) membro dentro de um [ `[Category]` ](#CategoryAttribute) definição. Se você realmente deseja ter [ `[Static]` ](#StaticAttribute) membros dentro de sua [ `[Category]` ](#CategoryAttribute) definições o aviso pode ser silêncio usando `[Category (allowStaticMembers: true)]` ou decorando o membro ou [ `[Category]` ](#CategoryAttribute) definição com de interface [ `[Internal]` ](#InternalAttribute).

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

Quando esse atributo é aplicado a uma classe apenas gerará uma classe estática, o que não deriva de `NSObject`, portanto, o [ `[BaseType]` ](#BaseTypeAttribute) atributo é ignorado. Classes static são usadas para hospedar C variáveis públicas que você deseja expor.

Por exemplo:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Irá gerar uma classe c# com a seguinte API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Definições de protocolo/modelo

Normalmente, os modelos são usados pela implementação de protocolo.
Elas diferem em que o tempo de execução somente registrará Objective-C os métodos que realmente foram substituídos.
Caso contrário, o método não será registrado.

Em geral, isso significa que, quando você subclasse uma classe que foi sinalizada com o `ModelAttribute`, você não deve chamar o método base.   Chamar o método lançará uma exceção, você deve para implementar o comportamento de inteiro na sua subclasse de quaisquer métodos que você substituir.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

Por padrão, os membros que fazem parte de um protocolo não são obrigatórios. Isso permite que os usuários criem uma subclasse do `Model` objeto simplesmente derivar da classe em c# e substituindo apenas os métodos que importam. Às vezes, o contrato Objective-C requer que o usuário fornece uma implementação deste método (aqueles serão marcadas com o `@required` diretiva em Objective-C). Nesses casos, você deve sinalizar esses métodos com o `[Abstract]` atributo.

O `[Abstract]` atributo pode ser aplicado a métodos ou propriedades e faz com que o gerador de sinalizador de membro gerado como abstract e a classe seja uma classe abstrata.

O seguinte é obtido da xamarin:

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

Especifica o valor padrão a ser retornada por um método de modelo se o usuário não fornecer um método para esse método específico no objeto de modelo

Sintaxe:

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

Por exemplo, na classe de delegado imaginário seguinte para uma `Camera` classe, fornecemos um `ShouldUploadToServer` que deve ser exposto como uma propriedade no `Camera` classe. Se o usuário da `Camera` classe não define explicitamente um o valor de uma expressão lambda que pode responder true ou false, o valor padrão retorno nesse caso seria falso, o valor que são especificados no `DefaultValue` atributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Se o usuário definir um manipulador na classe imaginária, esse valor será ignorado:

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

Esse atributo quando fornecido em um método que retorna um valor em uma classe de modelo instruirá o gerador para retornar o valor do parâmetro especificado se o usuário não forneceu seu próprio método ou lambda.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

No caso acima se o usuário o `NSAnimation` decidiu usar qualquer uma das c# eventos/propriedades de classe e não definiu `NSAnimation.ComputeAnimationCurve` para um método ou lambda, o valor de retorno seria o valor passado no parâmetro de andamento.

Consulte também: [ `[NoDefaultValue]` ](#NoDefaultValueAttribute), [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

Às vezes, faz sentido não expõe um evento ou delegar a propriedade de uma classe de modelo para a classe de host para que adicionar esse atributo instruirá o gerador para evitar a geração de qualquer método decorado com ele.

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

Este atributo é usado em métodos de modelo que retornam valores para definir o nome da assinatura do delegado a ser usado.

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

Este atributo é usado para permitir que o gerador de alterar o nome da propriedade gerado na classe do host. Às vezes é útil quando o nome do método da classe FooDelegate faz sentido para a classe Delegate, mas seria ímpar na classe de host como uma propriedade.

Também isso é realmente útil (e necessários) quando você tiver dois ou mais métodos de sobrecarga que faça sentido para manterem-los chamado como na classe FooDelegate mas deseja expô-los na classe de host com um determinado nome melhor.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Com a definição acima, o gerador de produzirá a seguinte declaração de pública na classe do host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

Para eventos que levam mais de um parâmetro (em Objective-C a convenção é que o primeiro parâmetro em uma classe de delegado a instância do objeto do remetente) você deve fornecer o nome que você deseja que a classe EventArgs gerado ser. Isso é feito com o `[EventArgs]` atributo na declaração de método no seu `Model` classe.

Por exemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

A declaração acima gerará um `UIImagePickerImagePickedEventArgs` classe que deriva de EventArgs e pacotes de ambos os parâmetros, o `UIImage` e `NSDictionary`. O gerador de produz isso:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Expõe o seguinte, em seguida, o `UIImagePickerController` classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

Este atributo é usado para permitir que o gerador de alterar o nome de um evento ou propriedade gerado na classe. Às vezes é útil quando o nome do método da classe de modelo faz sentido para a classe de modelo, mas seria ímpar na classe de origem como um evento ou propriedade.

Por exemplo, o `UIWebView` usa o seguinte trecho do `UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

O expõe acima `LoadingFinished` como o método o `UIWebViewDelegate`, mas `LoadFinished` como o evento até conectar em um `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

Quando você aplica o `[Model]` atributo a uma definição de tipo no seu contrato de API, o tempo de execução irá gerar código especial que só será superficial invocações de métodos na classe se o usuário substituiu um método na classe. Normalmente, esse atributo é aplicado a todas as APIs que envolvem a uma classe de representante Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Especifica que o método no modelo não fornece um valor de retorno padrão.

Isso funciona com o tempo de execução Objective-C respondendo `false` para a solicitação de tempo de execução Objective-C para determinar se o seletor especificado é implementado nesta classe.

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

O conceito de protocolo Objective-C não realmente existe em c#. Protocolos são semelhantes às interfaces c#, mas diferem no que nem todos os métodos e as propriedades declaradas em um protocolo devem ser implementados pela classe que adota a ele. Em vez disso, alguns dos métodos e propriedades são opcionais.

Alguns protocolos são geralmente usados como classes de modelo, aqueles devem ser vinculados usando o [ `[Model]` ](#ModelAttribute) atributo.

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

Começando com o xamarin 7.0 um funcionalidade de ligação de protocolo novo e aprimorado incorporado.  Qualquer definição que contém o `[Protocol]` atributo irá gerar três classes de suporte que melhoram a maneira que você usar protocolos:

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

O **implementação da classe** fornece uma classe abstrata completa, você pode substituir métodos individuais e obter a segurança de tipo completo. Mas devido a c# não dá suporte à herança múltipla, há situações em que você pode exigir uma classe base diferente, mas ainda deseja implementar uma interface.

Isso é onde gerado **definição de interface** entram em ação.  É uma interface que tem todos os métodos necessários do protocolo.  Isso permite que os desenvolvedores que desejam implementar o protocolo para simplesmente implementar a interface.  O tempo de execução registrará automaticamente o tipo como adotar o protocolo.

Observe que a interface somente lista os métodos necessários e expor os métodos opcionais.   Isso significa que as classes que adotam o protocolo obterá completo verificação de tipo para os métodos necessários, mas terão que recorrer a digitação fraca (manualmente usando os atributos de exportação e a assinatura de correspondência) para os métodos de protocolo opcional.

Para que seja conveniente para consumir uma API que usa protocolos, a ferramenta de associação também produzirá uma classe de método de extensões que expõe todos os métodos opcionais.   Isso significa que enquanto está consumindo uma API, você será capaz de tratar protocolos como tendo todos os métodos.

Se você quiser usar as definições de protocolo em sua API, precisará escrever esqueleto interfaces vazias em sua definição de API.   Se você quiser usar o MeuProtocolo em uma API, você precisa fazer isso:

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

As opções acima é necessária porque no tempo de ligação de `IMyProtocol` não existirá, que é por que você precisa fornecer uma interface vazia.

### <a name="adopting-protocol-generated-interfaces"></a>Adotando gerado pelo protocolo de interfaces

Sempre que você implemente uma das interfaces geradas para os protocolos, como este:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

A implementação para os métodos de interface é exportada automaticamente com o nome apropriado, portanto, é equivalente a esta:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Não importa se a interface é implementada implícita ou explicitamente.

### <a name="protocol-inlining"></a>Protocolo inlining

Enquanto você associa tipos Objective-C existentes que foram declarados como adotando um protocolo, você desejará embutido o protocolo diretamente. Para fazer isso, declare simplesmente o protocolo como uma interface sem nenhuma [ `[BaseType]` ](#BaseTypeAttribute) de atributo e o protocolo na lista de interfaces base para a sua interface de lista.

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

Os atributos nesta seção são aplicados aos membros individuais de um tipo: propriedades e declarações de método.


### <a name="alignattribute"></a>AlignAttribute

Usado para especificar o valor do alinhamento para tipos de retorno da propriedade. Certas propriedades levam ponteiros para os endereços que devem ser alinhados em determinados limites (em xamarin, por exemplo, isso ocorre com alguns `GLKBaseEffect` alinhado de propriedades que devem ser de 16 bytes). Você pode usar essa propriedade para decorar o getter e usar o valor de alinhamento. Isso geralmente é usado com o `OpenTK.Vector4` e `OpenTK.Matrix4` tipos quando integrado com APIs de Objective-C.

Exemplo:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

O `[Appearance]` atributo está limitado a iOS 5, em que o Gerenciador de aparência foi introduzido.

O `[Appearance]` atributo pode ser aplicado a qualquer método ou propriedade que participam do `UIAppearance` framework. Quando esse atributo é aplicado a um método ou propriedade em uma classe, ele instruirá o gerador de associação para criar uma classe fortemente tipada aparência que é usada para definir o estilo de todas as instâncias dessa classe ou as instâncias que correspondem a certos critérios.

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

As opções acima deve gerar o código a seguir UIToolbar:

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

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (xamarin 5.4)

Use o `[AutoReleaseAttribute]` com os métodos e propriedades para encapsular a invocação do método para o método em um `NSAutoReleasePool`.

Há alguns métodos que retornam valores que são adicionados para o padrão em Objective-C `NSAutoReleasePool`. Por padrão, esses deve ir para o thread `NSAutoReleasePool`, mas como xamarin também mantém uma referência aos objetos de como o objeto gerenciado reside, não convém manter uma referência extra no `NSAutoReleasePool` que apenas obter descarregado até que o thread Retorna o controle para o próximo segmento ou volte para o loop principal.

Esse atributo é aplicado por exemplo, nas propriedades pesadas (por exemplo `UIImage.FromFile`) que retorna objetos que foram adicionados para o padrão `NSAutoReleasePool`. Sem esse atributo, as imagens serão retidas desde que o thread não retornou o controle para o loop principal. UF o thread foi algum tipo de ferramenta de download em segundo plano que está sempre ativo e aguardando o trabalho, as imagens nunca serão liberadas.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

O `[ForcedTypeAttribute]` é usado para impor a criação de um tipo gerenciado, mesmo se o objeto não gerenciado retornado corresponde ao tipo descrito na definição de associação.

Isso é útil quando o tipo descrito em um cabeçalho não coincide com o tipo retornado do método nativo, por exemplo, usar a seguinte definição Objective-C de `NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

Indica claramente que retornará um `NSURLSessionDownloadTask` instância, mas ainda- **retorna** um `NSURLSessionTask`, que é uma superclasse e, portanto, não pode ser convertido para `NSURLSessionDownloadTask`. Como estamos em um contexto de segurança de tipo um `InvalidCastException` ocorrerá.

Para estar de acordo com a descrição de cabeçalho e evitar o `InvalidCastException`, o `[ForcedTypeAttribute]` é usado.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

O `[ForcedTypeAttribute]` também aceita um valor booleano chamado `Owns` que é `false` por padrão `[ForcedType (owns: true)]`. O possui parâmetro é usado para acompanhar o [política de propriedade](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) para **Foundation Core** objetos.

O `[ForcedTypeAttribute]` é válida somente em parâmetros, propriedades e valor de retorno.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

O `[BindAsAttribute]` permite associação `NSNumber`, `NSValue` e `NSString`(enums) em tipos c# mais precisos. O atributo pode ser usado para criar o melhor e mais precisas, API .NET sobre a API nativa.

Você pode decorar métodos (no valor de retorno), parâmetros e propriedades com `BindAs`. A única restrição é que o membro **não** dentro de um `[Protocol]` ou [ `[Model]` ](#ModelAttribute) interface.

Por exemplo:

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Geraria:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

Internamente faremos o `bool?`  <->  `NSNumber` e `CGRect`  <->  `NSValue` conversões.

Os tipos atuais de encapsulamento com suporte são:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

Os seguintes tipos de dados em c# têm suporte para ser encapsulado de/para `NSValue`:

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

Os seguintes tipos de dados em c# têm suporte para ser encapsulado de/para `NSNumber`:

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

[`[BindAs]`](#BindAsAttribute) funciona em conjuntion com [enums feito por uma constante NSString](#enum-attributes) para que você possa criar melhor .NET API, por exemplo:

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

Geraria:

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

Podemos tratará o `enum`  <->  `NSString` conversão somente se o tipo de enumeração fornecida para [ `[BindAs]` ](#BindAsAttribute) é [feito por uma constante NSString](#enum-attributes).

#### <a name="arrays"></a>Matrizes

[`[BindAs]`](#BindAsAttribute) também oferece suporte a matrizes de qualquer um dos tipos com suporte, você pode ter a seguinte definição de API como um exemplo:

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

Geraria:

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

O `rects` parâmetro será encapsulado em uma `NSArray` que contém um `NSValue` para cada `CGRect` e de retorno, você receberá uma matriz de `CAScroll?` que foi criado usando os valores de retornado `NSArray` que contém `NSStrings`.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

O `[Bind]` atributo tem dois usos um quando aplicado a um método ou declaração de propriedade e outra quando aplicado à individual getter ou setter em uma propriedade.

Quando usado para um método ou propriedade, o efeito do `[Bind]` atributo é gerar um método que invoca o seletor especificado. Mas o método gerado resultante não é decorado com o [ `[Export]` ](#ExportAttribute) atributo, o que significa que não podem participar em Substituir o método. Isso geralmente é usado em combinação com o `[Target]` atributo para implementar os métodos de extensão Objective-C.

Por exemplo:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Quando usado em um getter ou setter, o `[Bind]` atributo é usado para alterar os padrões inferidos pelo gerador de código ao gerar nomes de seletor getter e setter Objective-C para uma propriedade. Por padrão, quando você sinalizar uma propriedade com o nome `fooBar`, o gerador deve gerar um `fooBar` exportar para o getter e `setFooBar:` para setter. Em alguns casos, Objective-C não seguir essa convenção, normalmente eles altere o nome de getter seja `isFooBar`.
Você usaria esse atributo para informar o gerador de isso.

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

Somente disponível no xamarin 6.3 e mais recente.

Esse atributo pode ser aplicado a métodos que usam um processador de conclusão como último argumento.

Você pode usar o `[Async]` atributo em métodos cujo último argumento é um retorno de chamada.  Quando você se aplicam a um método, o gerador de associação irá gerar uma versão do método com o sufixo `Async`.  Se o retorno de chamada não usa nenhum parâmetro, o valor de retorno será uma `Task`, se o retorno de chamada usa um parâmetro, o resultado será um `Task<T>`.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

O exemplo a seguir gerará este método assíncrono:

```csharp
Task LoadFileAsync (string file);
```

Se o retorno de chamada usa vários parâmetros, você deve definir o `ResultType` ou `ResultTypeName` para especificar o nome desejado do tipo gerado que armazenará todas as propriedades.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

O seguinte irá gerar este método assíncrono, onde `FileLoading` contém propriedades para acessar `files` e `byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

Se o último parâmetro do retorno de chamada é um `NSError`, em seguida, gerado `Async` método verificará se o valor não for null, e se esse for o caso, o método assíncrono gerado definirá a exceção de tarefa.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

As opções acima gera o seguinte método assíncrono:

```csharp
Task<string> UploadAsync (string file);
```

E o erro, a tarefa resultante terá a exceção definida como um `NSErrorException` que encapsula resultante `NSError`.

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Use esta propriedade para especificar o valor para o retorno `Task` objeto.   Esse parâmetro usa um tipo existente, assim ele precisa ser definida em uma de suas definições de api do núcleo.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Use esta propriedade para especificar o valor para o retorno `Task` objeto.   Esse parâmetro usa o nome de seu tipo desejado, o gerador de produzirá uma série de propriedades, um para cada parâmetro que usa o retorno de chamada.

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

Use essa propriedade para personalizar o nome dos métodos assíncronos gerado.   O padrão é usar o nome do método e acrescente o texto "Async", você pode usar isso para alterar esse padrão.

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Esse atributo é aplicado a parâmetros de cadeia de caracteres ou propriedades de cadeia de caracteres e instrui o gerador de código para não usar a cadeia de caracteres de cópia de zero de empacotamento para esse parâmetro e em vez disso, crie uma nova instância de NSString da cadeia de c#.
Esse atributo só é necessário em cadeias de caracteres se você instruir o gerador de usar o empacotamento de cadeia de caracteres de cópia de zero usando o `--zero-copy` opção de linha de comando ou definindo o atributo de nível de assembly `ZeroCopyStringsAttribute`.

Isso é necessário em casos em que a propriedade é declarada em Objective-C para ser um `retain` ou `assign` propriedade em vez de um `copy` propriedade. Normalmente, esses ocorrem em bibliotecas de terceiros que foram incorretamente "otimizadas" por desenvolvedores. Em geral, `retain` ou `assign` `NSString` propriedades estão incorretas desde `NSMutableString` ou classes derivadas de usuário de `NSString` pode alterar o conteúdo de cadeias de caracteres sem o conhecimento do código de biblioteca, um pouco de quebra a aplicativo. Normalmente, isso acontece devido à otimização prematura.

O seguinte exemplo mostra duas propriedades no objetivo-c:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

Quando você aplica o `[DisposeAttribute]` uma classe, fornecer um trecho de código será adicionado para o `Dispose()` implementação de método da classe.

Como o `Dispose` método é gerado automaticamente pelo `bmac-native` e `btouch-native` ferramentas, você precisa usar o `[Dispose]` atributo injetar alguns códigos em gerado `Dispose` implementação do método.

Por exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

O `[Export]` atributo é usado para sinalizar um método ou propriedade a ser exposto no tempo de execução Objective-C. Esse atributo é compartilhado entre a ferramenta de associação e os tempos de execução de xamarin e Xamarin.Mac reais. Para métodos, o parâmetro é passado textual para o código gerado, para exportações de propriedades, um getter e setter são geradas com base na declaração de base (consulte a seção sobre o [ `[BindAttribute]` ](#BindAttribute) para obter informações sobre como alterar o comportamento da ferramenta de associação).

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

O [seletor](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) representa o nome do método Objective-C subjacente ou da propriedade que está sendo associada.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

Este atributo é usado para expor uma variável global C como um campo que é carregado sob demanda e expostos ao código c#. Normalmente isso é necessário para obter os valores de constantes que são definidas em C ou Objective-C e que pode ser qualquer um dos tokens utilizados em algumas APIs, ou cujos valores são opacas e devem ser usadas como-é pelo código do usuário.

Sintaxe:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

O `symbolName` é o símbolo de C para vincular com. Por padrão, isso será carregado de uma biblioteca cujo nome é inferido do namespace em que o tipo é definido. Se isso não é a biblioteca onde o símbolo é pesquisado, você deve transmitir o `libraryName` parâmetro. Se você está vinculando uma biblioteca estática, use `__Internal` como o `libraryName` parâmetro.

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

Setters não têm suporte para [enums com o apoio de constantes NSString](#enum-attributes), mas eles podem ser manualmente associados se necessário.

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

O `[Internal]` atributo pode ser aplicado a métodos ou propriedades e tem o efeito de sinalizar o código gerado com o `internal` palavra-chave c# tornar o código somente é acessível para o código no assembly gerado. Isso normalmente é usado para ocultar as APIs que são muito baixo nível ou forneça uma API pública de qualidade inferior que você deseja melhorar após ou APIs que não são suportados pelo gerador de e requerem alguma codificação de mão.

Quando você criar a associação, normalmente você ocultar o método ou propriedade que usam esse atributo e fornecer um nome diferente para o método ou propriedade e, em seguida, em seu arquivo de suporte complementar c#, você adicionaria um wrapper fortemente tipada que expõe o funcionalidade subjacente.

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

Esse atributo sinaliza o campo existente para uma propriedade a ser anotado com o .NET `[ThreadStatic]` atributo. Isso é útil se o campo for uma variável estática de thread.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (xamarin 6.0.6)

Esse atributo faz um exceções do método suporte nativo (Objective-C).
Em vez de chamar `objc_msgSend` diretamente, a invocação passará por um trampoline personalizado que captura exceções ObjectiveC e empacota-los em exceções gerenciadas.

No momento apenas alguns `objc_msgSend` assinaturas têm suporte (você vai descobrir se uma assinatura não tem suporte quando a vinculação nativo de um aplicativo que usa a associação falhará com um monotouch_ ausente *_objc_msgSend* símbolo), mas mais podem ser adicionada à solicitação.


### <a name="newattribute"></a>Novoatributo

Esse atributo é aplicado a métodos e propriedades para ter o gerador de geram o `new` palavra-chave na frente da declaração.

Ele é usado para evitar avisos do compilador quando o mesmo método ou o nome da propriedade é introduzido em uma subclasse que já existia em uma classe base.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

Você pode aplicar esse atributo para campos tenham produzir o gerador uma classe de notificações de auxiliar fortemente tipada.

Esse atributo pode ser usado sem argumentos para notificações que não realizar nenhuma carga útil, ou você pode especificar um `System.Type` que faz referência a outra interface na definição de API, normalmente com o nome termina com "EventArgs". O gerador de transformará a interface em uma classe que as subclasses `EventArgs` e incluirá todas as propriedades listadas. O [ `[Export]` ](#ExportAttribute) atributo deve ser usado o `EventArgs` classe para listar o nome da chave usada para pesquisar o dicionário Objective-C para buscar o valor.

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

Os usuários do seu código, em seguida, podem facilmente assinar notificações lançadas para o [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) usando código como este:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Ou para definir um objeto específico para observar. Se você passar `null` para `objectToObserve` este método se comportarão como seus outros pares.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

O valor retornado de `ObserveDidStart` pode ser usado para facilmente interromper o recebimento de notificações, como este:

```csharp
token.Dispose ();
```

Ou você pode chamar [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject//) e passar o token. Se a notificação contiver parâmetros, você deve especificar um auxiliar `EventArgs` interface, como este:

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

Acima gerará um `MyScreenChangedEventArgs` classe com o `ScreenX` e `ScreenY` propriedades buscará os dados do [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) usando as chaves de dicionário `ScreenXKey` e `ScreenYKey` respectivamente e aplicar as conversões apropriadas. O `[ProbePresence]` atributo é usado para o gerador de investigar se a chave for definida no `UserInfo`, em vez de tentar extrair o valor. Isso é usado para casos em que a presença da chave é o valor (normalmente por valores booleanos).

Isso permite que você escreva código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

Em alguns casos, não há nenhuma constante associada com o valor passado no dicionário.  Apple, às vezes, usa constantes de símbolos públicos e, às vezes, as constantes de cadeia de caracteres.  Por padrão o [ `[Export]` ](#ExportAttribute) atributo no seu fornecido `EventArgs` classe usará o nome especificado como um símbolo público a ser pesquisado em tempo de execução.  Se isso não for o caso e, em vez disso, ele deve ser pesquisado como uma constante de cadeia de caracteres, em seguida, passar o `ArgumentSemantic.Assign` valor para o atributo de exportação.

**Novo no xamarin 8.4**

Às vezes, as notificações começará vida sem argumentos, assim o uso de [ `[Notification]` ](#NotificationAttribute) sem argumentos é aceitável.  Mas, às vezes, os parâmetros para a notificação serão apresentados.  Para dar suporte a esse cenário, o atributo pode ser aplicado mais de uma vez.

Se você estiver desenvolvendo uma associação e para evitar quebrar o código de usuário existente, você deve ativar uma notificação existente do:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

Em uma versão que lista o atributo de notificação duas vezes, como este:

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

Quando isso é aplicado a uma propriedade, ele marca a propriedade permitir o valor `null` a ser atribuído a ele. Isso só é válido para tipos de referência.

Quando isso é aplicado a um parâmetro em uma assinatura de método indica que o parâmetro especificado pode ser nulo e que nenhuma verificação deve ser executada para passar `null` valores.

Se o tipo de referência não tem esse atributo, a ferramenta de associação irá gerar uma verificação para o valor que está sendo atribuído antes de passá-lo para Objective-C e irá gerar uma seleção que emitirá um `ArgumentNullException` se o valor atribuído é `null`.

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

Você pode usar esse atributo para inserir um código a ser inserido após os parâmetros de entrada foram validados, mas antes de chamar o código em C. o objetivo

Exemplo:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Você pode usar esse atributo para inserir um código a ser inserido antes de qualquer um dos parâmetros são validados no método gerado.

Exemplo:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Instrui o gerador de associação para invocar a propriedade especificada desta classe para buscar um valor de-lo.

Essa propriedade normalmente é usada para atualizar o cache que aponta para os objetos de referência que mantêm o gráfico do objeto referenciado. Geralmente ele aparece no código que tem operações, como adicionar ou remover. Esse método é usado para que depois que elementos são adicionados ou removidos do que o cache interno atualizadas para garantir que estamos mantendo gerenciadas referências a objetos que estão realmente em uso. Isso é possível porque a ferramenta de associação gera um campo de backup para todos os objetos de referência em uma associação de determinado.

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

Nesse caso, o `Dependencies` propriedade será invocada após a adição ou remoção de dependências do `NSOperation` objeto, assegurando que temos um gráfico que representa o valor real carregado objetos, impedindo vazamentos de memória, bem como corrupção de memória.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

Você pode usar esse atributo para injetar algum código-fonte c# a ser inserido depois que o código foi invocado o método Objective-C subjacente

Exemplo:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Esse atributo é aplicado para retornar valores para o sinalizador-los como objetos de proxy. Alguns objetos de proxy retorno Objective-C APIs que não podem ser diferenciados de associações de usuário. O efeito desse atributo é sinalizar o objeto como sendo um `DirectBinding` objeto. Para um cenário Xamarin.Mac, você pode ver o [discussão sobre esse bug](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Instrui o gerador de manter uma referência gerenciada para o parâmetro ou remover uma referência interna para o parâmetro. Isso é usado para manter os objetos referenciados.

Sintaxe:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se o valor de `doAdd` for true, o parâmetro é adicionado para o `__mt_{0}_var List<NSObject>;`. Onde `{0}` é substituído com o determinado `listName`. Você deve declarar este campo existente na sua classe parcial complementar à API.

Para obter um exemplo, consulte [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (xamarin 6.0)

Isso pode ser aplicado para retornar tipos para indicar que o gerador deve chamar `Release` no objeto antes de retorná-lo. Isso é necessário apenas quando um método fornece um objeto retido (em vez de um objeto autoreleased, que é o cenário mais comum)

Exemplo:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Além desse atributo é propagado para o código gerado, para que o tempo de execução do xamarin Saiba que ele deve manter o objeto ao retornar para Objective-C de uma função.


### <a name="sealedattribute"></a>SealedAttribute

Instrui o gerador para sinalizar o método gerado como fechado. Se esse atributo não for especificado, o padrão é gerar um método virtual (um método virtual, um método abstrato ou uma substituição dependendo de como os outros atributos são usados).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

Quando o `[Static]` atributo é aplicado a um método ou propriedade, isso gera um método estático ou propriedade. Se esse atributo não for especificado, o gerador de produz uma propriedade ou método de instância.


### <a name="transientattribute"></a>TransientAttribute

Use esse atributo para propriedades de sinalizador cujos valores são transitórios, ou seja, os objetos que são criados temporariamente pelo iOS, mas não são de vida longa. Quando esse atributo é aplicado a uma propriedade, o gerador não cria um campo existente para essa propriedade, o que significa que a classe gerenciada não mantém uma referência ao objeto.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

No design de associações Xamarin.iOS/Xamarin.Mac, o `[Wrap]` atributo é usado para encapsular um objeto de tipo fraco com um objeto fortemente tipado. Isso é usado principalmente com objetos de delegado Objective-C que normalmente são declarados como sendo do tipo `id` ou `NSObject`. A convenção usada por xamarin e Xamarin.Mac é expor as fontes de dados ou delegados como sendo do tipo `NSObject` e são nomeados usando a convenção "Weak" + o nome que está sendo exposto. Um `id delegate` propriedade Objective-C deve ser exposta como um `NSObject WeakDelegate { get; set; }` propriedade no arquivo do contrato de API.

Mas, normalmente o valor atribuído a este delegado é de um tipo forte, portanto, o tipo forte de superfície e aplicar o `[Wrap]` atributo, isso significa que os usuários podem escolher usar tipos fracos, se precisar de algum controle fine ou se eles precisarem de recurso de baixo nível tric links, ou pode usar a propriedade fortemente tipado para a maioria do trabalho.

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

Isso é como o usuário seria usar a versão de tipo fraco do representante:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

E isso é decorar de como o usuário deve usar a versão fortemente tipada, observe que o usuário tira proveito do sistema de tipo do # e está usando a palavra-chave override para declarar sua intenção e que ele não precisa manualmente o método com `[Export]`, uma vez que fizemos que trabalhar na associação para o usuário:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Outro uso do `[Wrap]` atributo for dar suporte a versão fortemente tipada de métodos.  Por exemplo:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Os membros gerados pelo `[Wrap]` não `virtual` por padrão, se você precisar de um `virtual` membro pode ser definido como `true` opcional `isVirtual` parâmetro.

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

Esta seção descreve os atributos que você pode aplicar para os parâmetros em uma definição de método, bem como a `[NullAttribute]` que se aplica a uma propriedade como um todo.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Esse atributo é aplicado aos tipos de parâmetro nas declarações de delegado do c# para notificar o associador de que o parâmetro em questão está de acordo com o bloco de Objective-C convenção de chamada e deve empacotar dessa maneira.

Isso é normalmente usado para retornos de chamada que são definidos como esta no objetivo-c:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Consulte também: [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Esse atributo é aplicado aos tipos de parâmetro nas declarações de delegado do c# para notificar o associador de que o parâmetro em questão está de acordo com a convenção de chamada de ponteiro de função C ABI e deve empacotar dessa maneira.

Isso é normalmente usado para retornos de chamada que são definidos como esta no objetivo-c:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Consulte também: [BlockCallback](#BlockCallback).

### <a name="params"></a>Params

Você pode usar o `[Params]` o último parâmetro de matriz de uma definição de método para que o gerador de injetar um "params" na definição do atributo.   Isso permite que a associação permitir facilmente parâmetros opcionais.

Por exemplo, a seguinte definição:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Permite que o código a seguir ser gravado:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Isso tem a vantagem adicional de que ela requer que os usuários criem uma matriz totalmente para a passagem de elementos.

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

Você pode usar o `[PlainString]` atributo na frente de parâmetros de cadeia de caracteres para instruir o gerador de associação para passar a cadeia de caracteres como uma cadeia de caracteres C, em vez de passar o parâmetro como um `NSString`.

A maioria das APIs de Objective-C consumir `NSString` parâmetros, mas algumas APIs expor um `char *` API para passar cadeias de caracteres, em vez do `NSString` variação.
Use `[PlainString]` nesses casos.

Por exemplo, as seguintes declarações Objective-C:

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

Instrui o gerador de manter uma referência para o parâmetro especificado. O gerador de fornecerá o armazenamento de backup para esse campo ou você pode especificar um nome (o `WrapName`) para armazenar o valor. Isso é útil para manter uma referência a um objeto gerenciado que é passado como um parâmetro para Objective-C e quando você sabe que Objective-C manterá somente esta cópia do objeto. Por exemplo, uma API como `SetDisplay (SomeObject)` usaria esse atributo é provável que o SetDisplay pode exibir apenas um objeto por vez. Se você precisar manter o controle de mais de um objeto (por exemplo, para uma API como pilha) você usaria o `[RetainList]` atributo.

Sintaxe:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

Instrui o gerador de manter uma referência gerenciada para o parâmetro ou remover uma referência interna para o parâmetro. Isso é usado para manter os objetos referenciados.

Sintaxe:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se o valor de `doAdd` for true, o parâmetro é adicionado para o `__mt_{0}_var List<NSObject>`. Onde `{0}` é substituído com o determinado `listName`. Você deve declarar este campo existente na sua classe parcial complementar à API.

Para obter um exemplo, consulte [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

Esse atributo é aplicado a parâmetros e é usado somente durante a transição de Objective-C para c#.  Durante as transições de vários Objective-C `NSObject` parâmetros são encapsulados em uma representação gerenciada do objeto.

O tempo de execução será levar uma referência ao objeto nativo e manter a referência até que a última referência gerenciada para o objeto será excluída e o GC tem uma chance de ser executado.

Em alguns casos, é importante para o tempo de execução c# para não manter uma referência ao objeto nativo.  Às vezes, isso ocorre quando o código nativo subjacente anexou um comportamento especial para o ciclo de vida do parâmetro.  Por exemplo: o destruidor para o parâmetro executam alguma ação de limpeza ou descartar alguns recursos preciosos.

Esse atributo informa o tempo de execução que você deseja que o objeto a ser descartado se possível, ao retornar para Objective-C de seu método substituído.

A regra é simple: se teve o tempo de execução criar uma nova representação gerenciada do objeto nativo, em seguida, no final da função, a contagem de manter para o objeto nativo será removida e a propriedade do identificador do objeto gerenciado será limpo.   Isso significa que, se você manteve uma referência ao objeto gerenciado, que fazem referência tornará inútil (chamar métodos nela lançará uma exceção).

Se o objeto passado não foi criado, ou se já houver uma representação gerenciada pendente do objeto, o descarte forçado não ocorrerá. 


## <a name="property-attributes"></a>Atributos de propriedade

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Este atributo é usado para dar suporte a um idioma Objective-C em que uma propriedade com um getter é apresentada em uma classe base e uma subclasse mutável apresenta um setter.

Como o c# não dá suporte a esse modelo, a classe base deve ter o setter e getter e pode usar uma subclasse de [OverrideAttribute](#OverrideAttribute).

Esse atributo é usado somente em setters de propriedade e é usado para oferecer suporte o idioma mutável no objetivo-C.

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

Mapeamento de `NSString` constantes para valores de enumeração é uma maneira fácil de criar o melhor API do .NET. Ele:

* permite a conclusão de código ser mais útil, mostrando **somente** os valores corretos para a API;
* Adiciona o tipo de segurança, você não pode usar outro `NSString` constante em um contexto incorreto; e
* permite para ocultar algumas constantes, tornando a conclusão de código Mostrar lista menor de API sem perda de funcionalidade.

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

Da definição de associação acima cria o gerador de `enum` em si e também criará um `*Extensions` tipo estático que inclui os métodos de duas maneiras de conversão entre os valores de enum e o `NSString` constantes. Isso significa que as constantes permanecem disponíveis para os desenvolvedores mesmo se eles não fazem parte da API.

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

Você pode decorar **um** valor enum com esse atributo. Isso tornará a constante retornada se o valor de enum não é conhecido.

No exemplo acima:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Se nenhum valor de enum é decorado um `NotSupportedException` será lançada.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Códigos de erro são associados como uma enumeração de valores. Geralmente é um erro de domínio para eles e nem sempre é fácil de localizar qual delas se aplica (ou se ainda houver).

Você pode usar esse atributo para associar o domínio de erro com a enumeração em si.

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

Esse é o mesmo `[Field]` usado para constantes de tipo de atributo. Ele também pode ser usado dentro de enums para mapear um valor com uma constante específico.

Um `null` valor pode ser usado para especificar qual valor de enum deve ser retornado se um `null` `NSString` constante for especificada.

No exemplo acima:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Se nenhum `null` valor está presente e em seguida, um `ArgumentNullException` será lançada.

## <a name="global-attributes"></a>Atributos globais

Atributos globais ou são aplicados usando o `[assembly:]` modificador do atributo como o [ `[LinkWithAttribute]` ](#LinkWithAttribute) ou pode ser usado em qualquer lugar, como o [ `[Lion]` ](#SinceAndLionAttributes) e [ `[Since]` ](#SinceAndLionAttributes) atributos.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Este é um atributo de nível de assembly que permite que os desenvolvedores especifiquem a vinculação sinalizadores necessários para reutilizar uma biblioteca associada sem forçar o consumidor da biblioteca para configurar manualmente o gcc_flags e mtouch extra argumentos passados para uma biblioteca.

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

Esse atributo é aplicado no nível de assembly, por exemplo, este é o que o [CorePlot associações](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) usar:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Quando você usa o `[LinkWith]` atributo especificado `libraryName` são inseridos no assembly resultante, permitindo que os usuários enviar uma única DLL que contém as dependências não gerenciadas, bem como os sinalizadores de linha de comando necessários para consumir corretamente o biblioteca de xamarin.

Também é possível fornecer uma `libraryName`, caso em que o `LinkWith` atributo pode ser usado para especificar apenas os sinalizadores do vinculador adicionais:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Construtores de LinkWithAttribute

Esses construtores permitem que você especifique a biblioteca para vincular com e incorporar o assembly resultante, os destinos com suporte que oferece suporte a biblioteca e os sinalizadores de biblioteca opcional que são necessários para vincular com a biblioteca.

Observe que o `LinkTarget` argumento é inferido pela xamarin e não precisa ser definido.

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

O `ForceLoad` propriedade é usada para decidir se ou não o `-force_load` link sinalizador é usado para vincular a biblioteca nativa. Por enquanto, isso deve ser verdadeiro.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Se a biblioteca que está sendo associada tem um requisito de disco rígido em nenhuma estrutura (diferente de `Foundation` e `UIKit`), você deve definir o `Frameworks` propriedade como uma cadeia de caracteres que contém uma lista delimitada por espaço de estruturas de plataforma necessárias. Por exemplo, se você está associando uma biblioteca que requer `CoreGraphics` e `CoreText`, defina o `Frameworks` propriedade `"CoreGraphics CoreText"`.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Defina essa propriedade como true se o executável resultante precisa ser compilado usando um compilador de C++ em vez do padrão, que é um compilador C. Use-o se a biblioteca que você está associando foi escrita em C++.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

O nome da biblioteca não gerenciado para agrupar. Este é um arquivo com a extensão ".a" e pode conter código de objeto para várias plataformas (por exemplo, ARM e x86 do simulador).

Versões anteriores do xamarin verificada a `LinkTarget` propriedade para determinar a plataforma de sua biblioteca de suporte, mas isso agora é detectado automaticamente e o `LinkTarget` propriedade será ignorada.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

O `LinkerFlags` cadeia de caracteres fornece uma maneira para que os autores de associação especificar os sinalizadores do vinculador adicionais necessários ao vincular a biblioteca nativa no aplicativo.

Por exemplo, se a biblioteca nativa requer libxml2 e zlib, você definiria o `LinkerFlags` de cadeia de caracteres para `"-lxml2 -lz"`.

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

Versões anteriores do xamarin verificada a `LinkTarget` propriedade para determinar a plataforma de sua biblioteca de suporte, mas isso agora é detectado automaticamente e o `LinkTarget` propriedade será ignorada.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Defina essa propriedade como true se a biblioteca que você está vinculando requer a biblioteca de tratamento de exceção GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

O `SmartLink` propriedade deve ser definida como true para permitir que o xamarin determine se `ForceLoad` é necessário ou não.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

O `WeakFrameworks` propriedade funciona da mesma forma que o `Frameworks` propriedade, exceto que no link-time, o `-weak_framework` especificador é passado para a gcc para cada as estruturas listadas.

`WeakFrameworks` torna possível para bibliotecas e aplicativos para link fraco em estruturas para a plataforma para que eles podem usá-los, opcionalmente, se eles estão disponíveis, mas não têm uma dependência difícil neles que é útil se sua biblioteca destina-se para adicionar recursos extras no mais recente versões do iOS. Para obter mais informações sobre como vincular fraco, consulte a documentação da Apple em [vinculação fraca](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Bons candidatos para vinculação fraca seria `Frameworks` como contas, `CoreBluetooth`, `CoreImage`, `GLKit`, `NewsstandKit` e `Twitter` desde que eles só estão disponíveis no iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) e LionAttribute (macOS)

Você usa o `[Since]` a APIs do sinalizador de atributo como tendo que está sendo introduzida em um determinado ponto no tempo. O atributo deve ser usado apenas para sinalizar tipos e métodos que podem causar um problema de tempo de execução se a classe base, método ou propriedade não está disponível.

Sintaxe:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Ela deve em geral não ser aplicada a enumerações, restrições ou novas estruturas como aqueles não pode causar um erro de tempo de execução se forem executados em um dispositivo com uma versão mais antiga do sistema operacional.

Exemplo quando aplicado a um tipo:

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

Exemplo quando aplicado a um novo membro:

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

O `[Lion]` atributo é aplicado, da mesma maneira, mas para tipos introduzidos com Lion. A razão para usar `[Lion]` versus o número de versão mais específico que é usado no iOS é que iOS seja revisado com frequência, enquanto as versões principais OS X ocorrerem raramente e é mais fácil de lembrar o sistema operacional por seu codinome de por seu número de versão

### <a name="adviceattribute"></a>AdviceAttribute

Use esse atributo para dar aos desenvolvedores uma dica sobre outras APIs que podem ser mais conveniente para uso.   Por exemplo, se você fornecer uma versão fortemente tipada de uma API, você pode usar esse atributo no atributo de tipo fraco para direcionar o desenvolvedor para a API melhor.

As informações do atributo são exibidas na documentação e ferramentas podem ser desenvolvidas para fornecer sugestões de usuário sobre como melhorar

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Somente disponível no xamarin 5.4 e mais recente.

Esse atributo instrui o gerador de que a associação para esta biblioteca específica (se forem aplicadas com `[assembly:]`) ou o tipo deve usar o empacotamento de cadeia de caracteres de cópia de zero rápido. Esse atributo é equivalente a passar a opção de linha de comando `--zero-copy` para o gerador.

Ao usar a cópia de zero para cadeias de caracteres, o gerador de efetivamente usa a mesma cadeia c# como a cadeia de caracteres que consome Objective-C sem recorrer a criação de um novo `NSString` objeto e evitando copiar os dados de cadeias de caracteres c# para a cadeia de caracteres Objective-C. A única desvantagem de usar cadeias de caracteres de cópia de Zero é que você deve garantir que qualquer propriedade de cadeia de caracteres que encapsular que acontece ser sinalizada como `retain` ou `copy` tem o `[DisableZeroCopy]` conjunto de atributos. Isso é necessário porque o identificador de cadeias de caracteres de cópia de zero é alocado na pilha e é inválido após o retorno de função.

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

Você também pode aplicar o atributo no nível de assembly e se aplicará a todos os tipos do assembly:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Dicionários fortemente tipada

Com o xamarin 8.0 é introduziu suporte para facilmente criar classes fortemente tipada que wrap `NSDictionaries`.

Embora sempre tenha sido possível usar o [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) de tipo de dados junto com uma API manual, agora é muito mais simples de fazer isso.  Para obter mais informações, consulte [adicionar tipos de alta segurança](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Quando esse atributo é aplicado a uma interface, o gerador de produzirá uma classe com o mesmo nome que a interface que deriva de [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) e transforma cada propriedade definida na interface em fortemente tipado getter e setter para o dicionário.

Isso gera automaticamente uma classe que pode ser instanciada de uma já existente `NSDictionary` ou que foi criado novo.

Este atributo recebe um parâmetro, o nome da classe que contém as chaves que são usadas para acessar os elementos no dicionário.   Por padrão, cada propriedade na interface com o atributo será pesquisar um membro no tipo especificado para um nome com o sufixo "Chave".

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

No caso acima, o `MyOption` classe produzirá uma propriedade de cadeia de caracteres para `Name` que usará o `MyOptionKeys.NameKey` como a chave no dicionário para recuperar uma cadeia de caracteres.   E usará o `MyOptionKeys.AgeKey` como a chave no dicionário para recuperar um `NSNumber` que contém um int.

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

Os seguintes tipos de dados têm suporte no `StrongDictionary` definição:

|Tipo de Interface c#|`NSDictionary` Tipo de armazenamento|
|---|---|
|`bool`|`Boolean` armazenados em um `NSNumber`|
|Valores de enumeração|inteiro são armazenados em um `NSNumber`|
|`int`|inteiro de 32 bits armazenado em um `NSNumber`|
|`uint`|inteiro de 32 bits armazenado em um `NSNumber`|
|`nint`|`NSInteger` armazenados em um `NSNumber`|
|`nuint`|`NSUInteger` armazenados em um `NSNumber`|
|`long`|inteiro de 64 bits armazenado em um `NSNumber`|
|`float`|inteiro de 32 bits armazenado como um `NSNumber`|
|`double`|inteiro de 64 bits armazenado como um `NSNumber`|
|`NSObject` e subclasses|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C# `Array` de `NSObject`|`NSArray`|
|C# `Array` de enumerações|`NSArray` contendo `NSNumber` valores|
