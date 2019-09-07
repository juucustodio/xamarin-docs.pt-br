---
title: Guia de referência de tipos de associação
description: Este guia de referência descreve vários atributos e conceitos que são necessários para entender ao C# criar associações para bibliotecas Objective-C.
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: ef94c90cec11c374b24ddfb159674adb468e72de
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765783"
---
# <a name="binding-types-reference-guide"></a>Guia de referência de tipos de associação

Este documento descreve a lista de atributos que você pode usar para anotar seus arquivos de contrato de API para direcionar a associação e o código gerado

Os contratos de API xamarin. iOS e Xamarin. Mac são C# escritos em grande parte como definições de interface que definem a forma como o código Objective-C é exibido C#. O processo envolve uma combinação de declarações de interface mais algumas definições básicas de tipo que o contrato de API pode exigir. Para obter uma introdução aos tipos de associação, consulte nosso guia complementar [bibliotecas Objective-C de associação](~/cross-platform/macios/binding/objective-c-libraries.md).

## <a name="type-definitions"></a>Definições de tipo

Sintaxe:

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

Cada interface em sua definição de contrato que tem [`[BaseType]`](#BaseTypeAttribute) o atributo que declara o tipo base para o objeto gerado. Na declaração acima, será `MyType` gerado C# um tipo de classe que é associado a um tipo Objective-C `MyType`chamado.

Se você especificar qualquer tipo após o TypeName (no exemplo acima `Protocol1` e `Protocol2`) usando a sintaxe de herança de interface, o conteúdo dessas interfaces será embutido como se tivesse feito parte do contrato para `MyType`o.
A maneira como o Xamarin. iOS é a superfície de que um tipo adota um protocolo é ao incolocar todos os métodos e propriedades que foram declarados no protocolo no próprio tipo.

O seguinte mostra como a declaração Objective-C `UITextField` para seria definida em um contrato do Xamarin. Ios:

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

Seria escrito como um C# contrato de API:

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

Você pode controlar muitos outros aspectos da geração de código aplicando outros atributos à interface, bem como Configurando [`[BaseType]`](#BaseTypeAttribute) o atributo.

### <a name="generating-events"></a>Gerando eventos

Um recurso do design da API Xamarin. iOS e Xamarin. Mac é que mapeamos as classes delegadas Objective C# -C como eventos e retornos de chamada. Os usuários podem escolher em uma base por instância se desejam adotar o padrão de programação Objective-c, atribuindo a propriedades como `Delegate` uma instância de uma classe que implementa os vários métodos que o tempo de execução do Objective-c chamaria ou por escolhendo os C#eventos e as propriedades do estilo.

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

No exemplo acima, você pode ver que optamos por substituir dois métodos, uma notificação de que um evento de rolagem ocorreu e o segundo que é um retorno de chamada que deve retornar um valor booliano instruindo `scrollView` se ele deve rolar para o superior ou não.

O C# modelo permite que o usuário da sua biblioteca escute as notificações usando a C# sintaxe de evento ou a sintaxe de propriedade para conectar retornos de chamada que são esperados para retornar valores.

É assim que o C# código para o mesmo recurso se parece usando lambdas:

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

Como os eventos não retornam valores (eles têm um tipo de retorno nulo), você pode conectar várias cópias. O `ShouldScrollToTop` não é um evento, em vez disso, é uma propriedade com `UIScrollViewCondition` o tipo que tem essa assinatura:

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

Ele retorna um `bool` valor, nesse caso, a sintaxe lambda nos permite apenas retornar o valor `MakeDecision` da função.

O gerador de associação dá suporte à geração de eventos e propriedades que `UIScrollView` vinculam `UIScrollViewDelegate` uma classe como com seu (bem, chamam essas classes de modelo), isso [`[BaseType]`](#BaseTypeAttribute) é feito anotando sua definição com o e o `Events` `Delegates`parâmetros (descritos abaixo). Além de anotar o [`[BaseType]`](#BaseTypeAttribute) com esses parâmetros, é necessário informar o gerador de mais alguns componentes.

Para eventos que usam mais de um parâmetro (em Objective-C, a Convenção é que o primeiro parâmetro em uma classe delegate é a instância do objeto Sender), você deve fornecer o nome que deseja para a classe `EventArgs` gerada. Isso é feito com o [`[EventArgs]`](#EventArgsAttribute) atributo na declaração do método em sua classe de modelo. Por exemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

A declaração acima gerará uma `UIImagePickerImagePickedEventArgs` classe que deriva de `EventArgs` e `UIImage` compacta os dois `NSDictionary`parâmetros, e. O gerador produz isso:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Em seguida, ele expõe o seguinte `UIImagePickerController` na classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

Os métodos de modelo que retornam um valor são vinculados de forma diferente. Eles exigem um nome para o delegado gerado C# (a assinatura do método) e também um valor padrão a ser retornado caso o usuário não forneça uma implementação por si só. Por exemplo, a `ShouldScrollToTop` definição é esta:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

Acima, você criará `UIScrollViewCondition` um delegado com a assinatura que foi mostrada acima e, se o usuário não fornecer uma implementação, o valor de retorno será true.

Além do [`[DefaultValue]`](#DefaultValueAttribute) atributo, você também pode usar o [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) atributo que direciona o gerador para retornar o valor do parâmetro especificado na chamada ou o [`[NoDefaultValue]`](#NoDefaultValueAttribute) parâmetro que instrui o gerador de que não há nenhum padrão valor.

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

Você usa a `Name` propriedade para controlar o nome ao qual esse tipo se associará no mundo Objective-C. Normalmente, isso é usado para dar C# ao tipo um nome que seja compatível com as diretrizes de design .NET Framework, mas que mapeia para um nome em Objective-C que não segue essa convenção.

Exemplo, no caso a seguir, mapeamos o tipo Objective- `NSUrlConnection`C `NSURLConnection` para, pois as diretrizes de design de .NET Framework usam "URL" em vez de "URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

O nome especificado é usado como o valor para o atributo `[Register]` gerado na associação. Se `Name` não for especificado, o nome curto do tipo será usado como o valor para o `[Register]` atributo na saída gerada.

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. Events e BaseType. delegates

Essas propriedades são usadas para orientar a geração de C#eventos de estilo nas classes geradas. Eles são usados para vincular uma determinada classe à sua classe delegate Objective-C. Você encontrará muitos casos em que uma classe usa uma classe delegate para enviar notificações e eventos. Por exemplo, `BarcodeScanner` um teria uma classe `BardodeScannerDelegate` complementar. A `BarcodeScanner` classe normalmente teria uma `Delegate` Propriedade à qual você atribuiria uma instância do `BarcodeScannerDelegate` , enquanto isso funciona, talvez você queira expor à interface de evento de estilo C#semelhante aos usuários e, nesses casos, usaria o `Events` e Propriedades`Delegates` [do`[BaseType]`](#BaseTypeAttribute) atributo.

Essas propriedades são sempre definidas juntas e devem ter o mesmo número de elementos e ser mantidas em sincronia. A `Delegates` matriz contém uma cadeia de caracteres para cada delegado de tipo fraco que você deseja encapsular e a `Events` matriz contém um tipo para cada tipo que você deseja associar a ele.

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

Se você aplicar esse atributo quando novas instâncias dessa classe forem criadas, a instância desse objeto será mantida até que o método referenciado pelo `KeepRefUntil` tenha sido invocado. Isso é útil para melhorar a usabilidade de suas APIs, quando você não deseja que seu usuário mantenha uma referência a um objeto para usar seu código. O valor dessa propriedade é o nome de um método na `Delegate` classe, portanto, você deve usá-lo em combinação com as `Events` Propriedades e `Delegates` também.

O exemplo a seguir mostra como isso é usado `UIActionSheet` pelo no Xamarin. Ios:

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

<a name="DesignatedDefaultCtorAttribute" />

### <a name="designateddefaultctorattribute"></a>DesignatedDefaultCtorAttribute

Quando esse atributo é aplicado à definição de interface, ele gerará `[DesignatedInitializer]` um atributo no construtor padrão (gerado), que é mapeado para `init` o seletor.

<a name="DisableDefaultCtorAttribute" />

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

Quando esse atributo é aplicado à definição de interface, ele impedirá que o gerador produza o construtor padrão.

Use esse atributo quando precisar que o objeto seja inicializado com um dos outros construtores na classe.

<a name="PrivateDefaultCtorAttribute" />

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

Quando esse atributo é aplicado à definição de interface, ele sinalizará o construtor padrão como privado. Isso significa que você ainda pode criar uma instância de objeto dessa classe internamente a partir do seu arquivo de extensão, mas ele só fica acessível aos usuários da sua classe.

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

Use esse atributo em uma definição de tipo para associar categorias de Objective-C e para C# expô-las como métodos de extensão para espelhar a forma como o Objective-c expõe a funcionalidade.

Categorias são um mecanismo Objective-C usado para estender o conjunto de métodos e propriedades disponíveis em uma classe.   Na prática, eles são usados para estender a funcionalidade de uma classe base (por exemplo `NSObject`) quando uma estrutura específica é vinculada (por exemplo `UIKit`), disponibilizando seus métodos, mas somente se a nova estrutura estiver vinculada.   Em alguns outros casos, eles são usados para organizar recursos em uma classe por funcionalidade.   Eles são semelhantes em espírito aos C# métodos de extensão.

Essa é a aparência de uma categoria no Objective-C:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

O exemplo acima é encontrado em uma biblioteca que estende as instâncias de `UIView` com o método `makeBackgroundRed`.

Para associá-los, você pode usar [`[Category]`](#CategoryAttribute) o atributo em uma definição de interface.   Ao usar o [`[Category]`](#CategoryAttribute) atributo, o significado [`[BaseType]`](#BaseTypeAttribute) do atributo é alterado de sendo usado para especificar a classe base a ser estendida, para ser o tipo a ser estendido.

O seguinte mostra como as `UIView` extensões são ligadas e transformadas em C# métodos de extensão:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Acima, você criará `MyUIViewExtension` uma classe que contém o `MakeBackgroundRed` método de extensão.   Isso significa que agora você pode chamar `MakeBackgroundRed` em qualquer `UIView` subclasse, dando a você a mesma funcionalidade que obteria no Objective-C.

Em alguns casos, você encontrará Membros **estáticos** dentro de categorias, como no exemplo a seguir:

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

Isso resultará em uma definição de C# interface de categoria **incorreta** :

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

Isso está incorreto porque, para `BoolMethod` usar a extensão, você precisa `FooObject` de uma instância do, mas está associando uma extensão **estática** ObjC, esse é um efeito colateral C# devido ao fato de como os métodos de extensão são implementados.

A única maneira de usar as definições acima é pelo seguinte código feio:

```csharp
(null as FooObject).BoolMethod (range);
```

A recomendação para evitar isso é embutir a `BoolMethod` definição dentro da `FooObject` própria definição de interface; isso permitirá que você chame essa extensão como pretendida `FooObject.BoolMethod (range)`.

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

Emitiremos um aviso (BI1117) sempre que encontrarmos um [`[Static]`](#StaticAttribute) membro dentro de [`[Category]`](#CategoryAttribute) uma definição. Se você realmente quiser ter [`[Static]`](#StaticAttribute) Membros dentro de suas [`[Category]`](#CategoryAttribute) definições, poderá silenciar o aviso usando `[Category (allowStaticMembers: true)]` ou decorando sua definição de membro ou [`[Category]`](#CategoryAttribute) de interface [`[Internal]`](#InternalAttribute)com.

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>Staticattribute

Quando esse atributo é aplicado a uma classe, ele apenas gerará uma classe estática, que não deriva de `NSObject`, portanto, o [`[BaseType]`](#BaseTypeAttribute) atributo será ignorado. As classes estáticas são usadas para hospedar as variáveis públicas C que você deseja expor.

Por exemplo:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

Irá gerar uma C# classe com a seguinte API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>Definições de protocolo/modelo

Normalmente, os modelos são usados pela implementação de protocolo.
Eles diferem que o tempo de execução registrará somente com Objective-C os métodos que realmente foram substituídos.
Caso contrário, o método não será registrado.

Em geral, isso significa que, quando você subclasse de uma classe que foi sinalizada `ModelAttribute`com o, não deve chamar o método base.   Chamar esse método gerará uma exceção, você deve implementar o comportamento inteiro em sua subclasse para quaisquer métodos que você substituir.

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>Abstractattribute

Por padrão, os membros que fazem parte de um protocolo não são obrigatórios. Isso permite que os usuários criem uma subclasse do `Model` objeto simplesmente derivando da classe no C# e substituindo apenas os métodos que eles se preocupam. Às vezes, o contrato Objective-C exige que o usuário forneça uma implementação para esse método (eles são `@required` sinalizados com a diretiva em Objective-C). Nesses casos, você deve sinalizar esses métodos com o `[Abstract]` atributo.

O `[Abstract]` atributo pode ser aplicado a métodos ou propriedades e faz com que o gerador sinalize o membro gerado como abstrato e a classe seja uma classe abstrata.

O seguinte é obtido do Xamarin. iOS:

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

Por exemplo, na seguinte classe delegate imaginário para uma `Camera` classe, fornecemos um `ShouldUploadToServer` que seria exposto `Camera` como uma propriedade na classe. Se o usuário da `Camera` classe não definir explicitamente um valor para um lambda que possa responder a true ou false, o valor padrão retornado nesse caso seria false, o valor que especificamos `DefaultValue` no atributo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

Se o usuário definir um manipulador na classe imaginário, esse valor será ignorado:

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

Consulte também: [`[NoDefaultValue]`](#NoDefaultValueAttribute), [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute).

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

Sintaxe:

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

Esse atributo, quando fornecido em um método que retorna um valor em uma classe de modelo, instruirá o gerador a retornar o valor do parâmetro especificado se o usuário não fornecer seu próprio método ou lambda.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

No caso acima, se o usuário da `NSAnimation` classe optar por usar qualquer um dos C# eventos/Propriedades e não tiver definido `NSAnimation.ComputeAnimationCurve` como um método ou lambda, o valor de retorno será o valor passado no parâmetro Progress.

Consulte também: [`[NoDefaultValue]`](#NoDefaultValueAttribute),[`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

Às vezes, faz sentido não expor um evento ou uma propriedade delegada de uma classe de modelo para a classe de host, de modo que adicionar esse atributo instruirá o gerador a evitar a geração de qualquer método decorado com ele.

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

Esse atributo é usado em métodos de modelo que retornam valores para definir o nome da assinatura delegada a ser usada.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

Com a definição acima, o gerador produzirá a seguinte declaração pública:

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

Esse atributo é usado para permitir que o gerador altere o nome da propriedade gerada na classe de host. Às vezes, é útil quando o nome do método de classe FooDelegate faz sentido para a classe delegate, mas pareceria estranha na classe host como uma propriedade.

Além disso, isso é realmente útil (e necessário) quando você tem dois ou mais métodos de sobrecarga que fazem sentido mantê-los nomeados como estão na classe FooDelegate, mas você deseja expô-los na classe host com um nome melhor.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

Com a definição acima, o gerador produzirá a seguinte declaração pública na classe de host:

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

Para eventos que usam mais de um parâmetro (em Objective-C, a Convenção é que o primeiro parâmetro em uma classe delegate é a instância do objeto Sender), você deve fornecer o nome que deseja para a classe EventArgs gerada. Isso é feito com o `[EventArgs]` atributo na declaração do método em sua `Model` classe.

Por exemplo:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

A declaração acima gerará uma `UIImagePickerImagePickedEventArgs` classe que deriva de EventArgs e packs ambos os parâmetros `UIImage` , e `NSDictionary`. O gerador produz isso:

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

Em seguida, ele expõe o seguinte `UIImagePickerController` na classe:

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>EventNameAttribute

Esse atributo é usado para permitir que o gerador altere o nome de um evento ou propriedade gerada na classe. Às vezes, é útil quando o nome do método de classe de modelo faz sentido para a classe de modelo, mas pareceria estranha na classe de origem como um evento ou uma propriedade.

Por exemplo, o `UIWebView` usa o seguinte bit `UIWebViewDelegate`do:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

O anterior expõe `LoadingFinished` como o método `UIWebViewDelegate`no, mas `LoadFinished` como o evento a ser conectado em um `UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

Quando você aplica o `[Model]` atributo a uma definição de tipo em sua API de contrato, o tempo de execução irá gerar um código especial que só irá orientar invocações para métodos na classe se o usuário tiver substituído um método na classe. Esse atributo é normalmente aplicado a todas as APIs que encapsulam uma classe delegate Objective-C.

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

Especifica que o método no modelo não fornece um valor de retorno padrão.

Isso funciona com o tempo de execução Objective- `false` c respondendo à solicitação de tempo de execução Objective-c para determinar se o seletor especificado está implementado nessa classe.

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

Consulte também: [`[DefaultValue]`](#DefaultValueAttribute),[`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>Protocolos

O conceito de protocolo Objective-C não existe realmente C#no. Os protocolos são semelhantes C# às interfaces, mas diferem em que nem todos os métodos e propriedades declarados em um protocolo devem ser implementados pela classe que o adota. Em vez disso, alguns métodos e propriedades são opcionais.

Alguns protocolos geralmente são usados como classes de modelo, que devem ser associados usando [`[Model]`](#ModelAttribute) o atributo.

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

A partir do Xamarin. iOS 7,0, uma nova e aprimorada funcionalidade de associação de protocolo foi incorporada.  Qualquer definição que contenha `[Protocol]` o atributo gerará, na verdade, três classes de suporte que melhoram a maneira de consumir protocolos:

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

A **implementação de classe** fornece uma classe abstrata completa que você pode substituir métodos individuais do e obter segurança de tipo completo. Mas, devido C# ao não suporte a várias heranças, há cenários em que você pode exigir uma classe base diferente, mas ainda quer implementar uma interface.

É aí que entra a **definição de interface** gerada.  É uma interface que tem todos os métodos necessários do protocolo.  Isso permite aos desenvolvedores que desejam implementar seu protocolo para meramente implementar a interface.  O tempo de execução registrará automaticamente o tipo como adotando o protocolo.

Observe que a interface lista apenas os métodos necessários e expõe os métodos opcionais.   Isso significa que as classes que adotam o protocolo obterão verificação de tipo completo para os métodos necessários, mas precisarão recorrer à digitação fraca (manualmente usando atributos de exportação e correspondência da assinatura) para os métodos de protocolo opcionais.

Para tornar conveniente consumir uma API que usa protocolos, a ferramenta de associação também produzirá uma classe de método de extensões que expõe todos os métodos opcionais.   Isso significa que, desde que você esteja consumindo uma API, você poderá tratar os protocolos como tendo todos os métodos.

Se você quiser usar as definições de protocolo em sua API, será necessário gravar interfaces vazias de esqueleto na sua definição de API.   Se você quiser usar o MyProtocol em uma API, precisará fazer isso:

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

O acima é necessário porque, no momento da `IMyProtocol` associação, não existe, é por isso que você precisa fornecer uma interface vazia.

### <a name="adopting-protocol-generated-interfaces"></a>Adotando interfaces geradas pelo protocolo

Sempre que você implementar uma das interfaces geradas para os protocolos, desta forma:

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

Não importa se a interface é implementada implicitamente ou explicitamente.

### <a name="protocol-inlining"></a>Inalinhamento de protocolo

Enquanto você associa os tipos Objective-C existentes que foram declarados como adotando um protocolo, você desejará embutir o protocolo diretamente. Para fazer isso, basta declarar o protocolo como uma interface sem qualquer [`[BaseType]`](#BaseTypeAttribute) atributo e listar o protocolo na lista de interfaces base para sua interface.

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

Os atributos nesta seção são aplicados a membros individuais de um tipo: propriedades e declarações de método.

### <a name="alignattribute"></a>Alignattribute

Usado para especificar o valor de alinhamento para tipos de retorno de propriedade. Certas propriedades têm ponteiros para endereços que devem ser alinhados a determinados limites (no Xamarin. Ios isso acontece por exemplo `GLKBaseEffect` , com algumas propriedades que devem ser alinhadas em 16 bytes). Você pode usar essa propriedade para decorar o getter e usar o valor de alinhamento. Normalmente, isso é usado com `OpenTK.Vector4` os `OpenTK.Matrix4` tipos e quando integrado às APIs Objective-C.

Exemplo:

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>Appearanceattribute

O `[Appearance]` atributo é limitado ao iOS 5, no qual o Gerenciador de aparência foi introduzido.

O `[Appearance]` atributo pode ser aplicado a qualquer método ou propriedade que participe da `UIAppearance` estrutura. Quando esse atributo é aplicado a um método ou propriedade em uma classe, ele direciona o gerador de associação para criar uma classe de aparência fortemente tipada que é usada para estilizar todas as instâncias dessa classe ou as instâncias que correspondem a determinados critérios.

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

O anterior geraria o seguinte código em UIToolbar:

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

### <a name="autoreleaseattribute-xamarinios-54"></a>Autolançamentoattribute (Xamarin. iOS 5,4)

Use os `[AutoReleaseAttribute]` métodos on e Properties para encapsular a invocação de método para o método `NSAutoReleasePool`em um.

Em Objective-C, há alguns métodos que retornam valores que são adicionados ao `NSAutoReleasePool`padrão. Por padrão, eles vão para seu thread `NSAutoReleasePool`, mas como o Xamarin. Ios também mantém uma referência aos seus objetos desde que o objeto gerenciado resida, talvez você não queira manter uma referência extra `NSAutoReleasePool` no que só será descarregada até seu thread Retorna o controle para o próximo thread ou você volta para o loop principal.

Esse atributo é aplicado, por exemplo, em Propriedades pesadas `UIImage.FromFile`(por exemplo) que retorna objetos que foram adicionados ao `NSAutoReleasePool`padrão. Sem esse atributo, as imagens seriam retidas desde que seu thread não retornasse o controle ao loop principal. UF seu thread foi algum tipo de Downloader de segundo plano que está sempre ativo e aguardando trabalho, as imagens nunca seriam liberadas.

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

O `[ForcedTypeAttribute]` é usado para impor a criação de um tipo gerenciado, mesmo que o objeto não gerenciado retornado não corresponda ao tipo descrito na definição de associação.

Isso é útil quando o tipo descrito em um cabeçalho não corresponde ao tipo retornado do método nativo; por exemplo, use a seguinte definição de Objective-C `NSURLSession`de:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

`NSURLSessionDownloadTask` Ele declara claramente que retornará uma instância, mas, ainda assim, **retorna** um `NSURLSessionTask`, que é `NSURLSessionDownloadTask`uma superclasse e, portanto, não é conversível. Como estamos em um contexto `InvalidCastException` de tipo seguro, ocorrerá.

Para obedecer à descrição do cabeçalho e evitar o `InvalidCastException`, o `[ForcedTypeAttribute]` é usado.

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

O `[ForcedTypeAttribute]` também aceita um valor booliano `Owns` chamado que `false` é, `[ForcedType (owns: true)]`por padrão. O parâmetro proprietário é usado para seguir a [política de propriedade](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html) para objetos **principais do Foundation** .

O `[ForcedTypeAttribute]` é válido somente em parâmetros, propriedades e valor de retorno.

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

O `[BindAsAttribute]` permite `NSNumber` C# associação e (`NSString`enums) em tipos mais precisos. `NSValue` O atributo pode ser usado para criar uma API .NET melhor e mais precisa sobre a API nativa.

Você pode decorar métodos (em valor de retorno), parâmetros e propriedades `BindAs`com. A única restrição é que o membro **não deve** estar dentro de `[Protocol]` uma [`[Model]`](#ModelAttribute) interface ou.

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

Internamente, faremos as `bool?`  <->  `NSNumber` conversões e `CGRect`.  <->  `NSValue`

Os tipos de encapsulamento com suporte atuais são:

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

Os seguintes C# tipos de dados têm suporte para serem encapsulados de/ `NSValue`para:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint/PointF
* CGRect / RectangleF
* CGSize/SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

Os seguintes C# tipos de dados têm suporte para serem encapsulados de/ `NSNumber`para:

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

[`[BindAs]`](#BindAsAttribute)funciona em conjuntion com [enums apoiados por uma constante NSString](#enum-attributes) para que você possa criar uma API .net melhor, por exemplo:

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

Trataremos a conversão `enum` <-> `NSString` somente se o tipo de enumeração fornecido [`[BindAs]`](#BindAsAttribute) for [apoiado por uma constante NSString](#enum-attributes).

#### <a name="arrays"></a>Matrizes

[`[BindAs]`](#BindAsAttribute)também dá suporte a matrizes de qualquer um dos tipos com suporte, você pode ter a seguinte definição de API como um exemplo:

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

O `rects` parâmetro será encapsulado em um `NSArray` que contém um `NSValue` para cada `CGRect` e, em retorno, você obterá uma matriz `CAScroll?` de que foi criada usando os valores de retornado `NSArray` que `NSStrings`contém.

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

O `[Bind]` atributo tem dois usa um quando aplicado a uma declaração de método ou propriedade e outro quando aplicado ao getter ou setter individual em uma propriedade.

Quando usado para um método ou uma propriedade, o efeito do `[Bind]` atributo é gerar um método que invoque o seletor especificado. Mas o método gerado resultante não é decorado com [`[Export]`](#ExportAttribute) o atributo, o que significa que ele não pode participar da substituição de método. Normalmente, isso é usado em combinação com `[Target]` o atributo para implementar métodos de extensão Objective-C.

Por exemplo:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

Quando usado em um getter ou setter, o `[Bind]` atributo é usado para alterar os padrões inferidos pelo gerador de código ao gerar os nomes de seletor de Objective-C de getter e setter para uma propriedade. Por padrão, quando você sinaliza uma propriedade com o `fooBar`nome, o gerador geraria `fooBar` uma exportação para o getter `setFooBar:` e para o setter. Em alguns casos, o Objective-C não segue essa convenção, geralmente eles alteram o nome getter para `isFooBar`.
Você usaria esse atributo para informar o gerador disso.

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

Disponível somente no Xamarin. iOS 6,3 e mais recente.

Esse atributo pode ser aplicado a métodos que usam um manipulador de conclusão como seu último argumento.

Você pode usar o `[Async]` atributo em métodos cujo último argumento é um retorno de chamada.  Quando você aplica isso a um método, o gerador de associação irá gerar uma versão desse método com o sufixo `Async`.  Se o retorno de chamada não usar parâmetros, o valor de retorno `Task`será um, se o retorno de chamada usar um parâmetro, o `Task<T>`resultado será um.

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

O seguinte irá gerar esse método assíncrono:

```csharp
Task LoadFileAsync (string file);
```

Se o retorno de chamada usar vários parâmetros, defina `ResultType` ou `ResultTypeName` para especificar o nome desejado do tipo gerado que conterá todas as propriedades.

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

O seguinte gerará esse método assíncrono, onde `FileLoading` contém propriedades para `files` acessar e `byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

Se o último parâmetro do retorno de chamada for `NSError`um, o método `Async` gerado verificará se o valor não é nulo e, se for o caso, o método assíncrono gerado definirá a exceção da tarefa.

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

O acima gera o seguinte método assíncrono:

```csharp
Task<string> UploadAsync (string file);
```

E, se houver erro, a tarefa resultante terá a exceção definida como `NSErrorException` um que encapsula o resultado `NSError`.

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

Use essa propriedade para especificar o valor para o objeto `Task` de retorno.   Esse parâmetro usa um tipo existente, portanto, ele precisa ser definido em uma das suas definições de API principal.

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

Use essa propriedade para especificar o valor para o objeto `Task` de retorno.   Esse parâmetro usa o nome do seu nome de tipo desejado, o gerador produzirá uma série de propriedades, uma para cada parâmetro que o retorno de chamada usa.

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

Use essa propriedade para personalizar o nome dos métodos assíncronos gerados.   O padrão é usar o nome do método e acrescentar o texto "Async", você pode usá-lo para alterar esse padrão.

<a name="DesignatedInitializerAttribute" />

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

Quando esse atributo é aplicado a um construtor, ele gerará o `[DesignatedInitializer]` mesmo no assembly da plataforma final. Isso é para ajudar o IDE a indicar qual Construtor deve ser usado em subclasses.

Isso deve mapear o uso de Objective-C `__attribute__((objc_designated_initializer))`/clang de.

<a name="DisableZeroCopyAttribute" />

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

Esse atributo é aplicado a parâmetros de cadeia de caracteres ou propriedades de cadeia de caracteres e instrui o gerador de código a não usar o marshaling de cadeia de caracteres de cópia zero para esse parâmetro e C# , em vez disso, criar uma nova instância de NSString a partir da cadeia de caracteres.
Esse atributo só será necessário em cadeias de caracteres se você instruir o gerador a usar marshaling de cadeia de `--zero-copy` cópia zero usando a opção de linha de comando ou `ZeroCopyStringsAttribute`Configurando o atributo de nível de assembly.

Isso é necessário nos casos em que a propriedade é declarada em Objective- `retain` C `assign` para ser uma propriedade `copy` ou em vez de uma propriedade. Normalmente, elas ocorrem em bibliotecas de terceiros que foram incorretamente "otimizadas" pelos desenvolvedores. Em geral, `retain` ou `assign` `NSString` as propriedades estão incorretas, uma vez que `NSString` `NSMutableString` as classes derivadas do usuário podem alterar o conteúdo das cadeias de caracteres sem o conhecimento do código da biblioteca, dividindo sutilmente o aplicativo. Normalmente, isso ocorre devido à otimização prematura.

O exemplo a seguir mostra duas propriedades em Objective-C:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute" />

### <a name="disposeattribute"></a>Disposeattribute

Ao aplicar o `[DisposeAttribute]` a uma classe, você fornece um trecho de código que será adicionado `Dispose()` à implementação do método da classe.

Como o `Dispose` método é gerado automaticamente `bmac-native` pelas ferramentas e `btouch-native` , você precisa usar o `[Dispose]` atributo para injetar algum código na implementação do método `Dispose` gerado.

Por exemplo:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

O `[Export]` atributo é usado para sinalizar um método ou uma propriedade a ser exposta ao tempo de execução Objective-C. Esse atributo é compartilhado entre a ferramenta de ligação e os tempos de execução reais do Xamarin. iOS e Xamarin. Mac. Para métodos, o parâmetro é passado textualmente para o código gerado, para propriedades, as exportações getter e setter são geradas com base na declaração base (consulte [`[BindAttribute]`](#BindAttribute) a seção no para obter informações sobre como alterar o comportamento da ferramenta de ligação).

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

O [seletor](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html) representa o nome do método ou da propriedade de Objective-C subjacente que está sendo associada.

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>Fieldattribute

Esse atributo é usado para expor uma variável C global como um campo que é carregado sob demanda e exposto ao C# código. Normalmente, isso é necessário para obter os valores de constantes que são definidos em C ou Objective-C e que podem ser os tokens usados em algumas APIs ou cujos valores são opacos e devem ser usados no estado em que se encontram pelo código do usuário.

Sintaxe:

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

O `symbolName` é o símbolo C com o qual vincular. Por padrão, isso será carregado de uma biblioteca cujo nome é inferido do namespace onde o tipo é definido. Se essa não for a biblioteca em que o símbolo é pesquisado, você deverá passar `libraryName` o parâmetro. Se você estiver vinculando uma biblioteca estática, `__Internal` use como `libraryName` o parâmetro.

As propriedades geradas são sempre estáticas.

As propriedades sinalizadas com o atributo Field podem ser dos seguintes tipos:

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* Enums

Não há suporte para setters para [enumerações apoiadas por constantes NSString](#enum-attributes), mas elas podem ser vinculadas manualmente, se necessário.

Exemplo:

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>Internoattribute

O `[Internal]` atributo pode ser aplicado a métodos ou propriedades e tem o efeito de sinalizar o código gerado com a `internal` C# palavra-chave que torna o código acessível somente para código no assembly gerado. Normalmente, isso é usado para ocultar as APIs que são muito baixas ou fornecer uma API pública de qualidade inferior que você deseja melhorar ou para APIs que não têm suporte no gerador e exigem uma codificação manual.

Ao projetar a associação, você normalmente ocultaria o método ou a propriedade usando esse atributo e forneceria um nome diferente para o método ou a propriedade e, em C# seguida, em seu arquivo de suporte complementar, adicionaria um wrapper fortemente tipado que expõe o funcionalidade subjacente.

Por exemplo:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

Em seguida, em seu arquivo de suporte, você pode ter algum código como este:

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

Esse atributo sinaliza o campo de backup de uma propriedade a ser anotada com o atributo `[ThreadStatic]` .net. Isso será útil se o campo for uma variável estática de thread.

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin. iOS 6.0.6)

Esse atributo fará com que um método dê suporte a exceções nativas (Objective-C).
Em vez de `objc_msgSend` chamar diretamente, a invocação passará por um trampoline personalizado que captura exceções de ObjectiveC e as empacota em exceções gerenciadas.

Atualmente, apenas algumas `objc_msgSend` assinaturas têm suporte (você descobrirá se uma assinatura não tem suporte quando a vinculação nativa de um aplicativo que usa a associação falha com um símbolo monotouch_ *_objc_msgSend* ausente), mas mais pode ser adicionado na solicitação.

### <a name="newattribute"></a>NewAttribute

Esse atributo é aplicado a métodos e propriedades para que o gerador gere a `new` palavra-chave na frente da declaração.

Ele é usado para evitar avisos de compilador quando o mesmo método ou nome de propriedade é introduzido em uma subclasse que já existia em uma classe base.

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>Notificationattribute

Você pode aplicar esse atributo a campos para que o gerador produza uma classe de notificações auxiliar fortemente tipada.

Esse atributo pode ser usado sem argumentos para notificações que não contêm carga ou você pode especificar um `System.Type` que faça referência a outra interface na definição de API, normalmente com o nome que termina com "EventArgs". O gerador transformará a interface em uma classe que subclasses `EventArgs` e incluirá todas as propriedades listadas ali. O [`[Export]`](#ExportAttribute) atributo deve ser usado `EventArgs` na classe para listar o nome da chave usada para pesquisar o dicionário Objective-C para buscar o valor.

Por exemplo:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

O código acima irá gerar uma classe `MyClass.Notifications` aninhada com os seguintes métodos:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Os usuários do seu código podem, então, assinar facilmente as notificações postadas no [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando um código como este:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

Ou para definir um objeto específico a ser observado. Se você passar `null` para `objectToObserve` esse método se comportará exatamente como seu outro par.

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

O valor retornado de `ObserveDidStart` pode ser usado para interromper facilmente o recebimento de notificações, assim:

```csharp
token.Dispose ();
```

Ou você pode chamar [NSNotification. defaultcenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) e passar o token. Se sua notificação contiver parâmetros, você deverá especificar uma `EventArgs` interface auxiliar, como esta:

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

O acima gerará uma `MyScreenChangedEventArgs` classe com as `ScreenX` propriedades `ScreenY` e que obterão os dados do dicionário [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) usando as chaves `ScreenXKey` e `ScreenYKey` , respectivamente, e aplicará o conversões apropriadas. O `[ProbePresence]` atributo é usado para o gerador investigar se a chave for definida `UserInfo`no, em vez de tentar extrair o valor. Isso é usado para casos em que a presença da chave é o valor (normalmente para valores Boolianos).

Isso permite que você escreva um código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

Em alguns casos, não há nenhuma constante associada ao valor passado no dicionário.  Às vezes, a Apple usa constantes de símbolo público e às vezes usa constantes de cadeia de caracteres.  Por padrão, [`[Export]`](#ExportAttribute) o atributo na classe `EventArgs` fornecida usará o nome especificado como um símbolo público a ser pesquisado no tempo de execução.  Se esse não for o caso e, em vez disso, ele deve ser pesquisado como uma constante de cadeia de `ArgumentSemantic.Assign` caracteres e, em seguida, passar o valor para o atributo de exportação.

**Novidade no Xamarin. iOS 8,4**

Às vezes, as notificações começarão a vida sem argumentos, portanto, [`[Notification]`](#NotificationAttribute) o uso de sem argumentos é aceitável.  Mas, às vezes, os parâmetros para a notificação serão introduzidos.  Para dar suporte a esse cenário, o atributo pode ser aplicado mais de uma vez.

Se você estiver desenvolvendo uma associação e quiser evitar a interrupção do código de usuário existente, você transformará uma notificação existente de:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

Em uma versão que lista o atributo de notificação duas vezes, desta forma:

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

Quando isso é aplicado a uma propriedade, ele sinaliza a propriedade como permitindo que `null` o valor seja atribuído a ela. Isso só é válido para tipos de referência.

Quando isso é aplicado a um parâmetro em uma assinatura de método, ele indica que o parâmetro especificado pode ser nulo e que nenhuma verificação deve ser executada `null` para passar valores.

Se o tipo de referência não tiver esse atributo, a ferramenta de associação gerará uma verificação para o valor que está sendo atribuído antes de passá-lo para Objective-C e gerará `ArgumentNullException` uma verificação que emitirá `null`um se o valor atribuído for.

Por exemplo:

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>Overrideattribute

Use esse atributo para instruir o gerador de associação que a associação para esse método específico deve ser sinalizada com uma `override` palavra-chave.

### <a name="presnippetattribute"></a>PreSnippetAttribute

Você pode usar esse atributo para injetar um código a ser inserido depois que os parâmetros de entrada tiverem sido validados, mas antes de o código chamar o Objective-C.

Exemplo:

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

Você pode usar esse atributo para injetar um código a ser inserido antes que qualquer um dos parâmetros seja validado no método gerado.

Exemplo:

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

Instrui o gerador de associação a invocar a propriedade especificada dessa classe para buscar um valor dela.

Essa propriedade normalmente é usada para atualizar o cache que aponta para objetos de referência que mantêm o grafo de objeto referenciado. Normalmente, ele aparece no código que tem operações como adicionar/remover. Esse método é usado de modo que, depois que os elementos são adicionados ou removidos, o cache interno seja atualizado para garantir que estamos mantendo referências gerenciadas a objetos que estão realmente em uso. Isso é possível porque a ferramenta de associação gera um campo de backup para todos os objetos de referência em uma determinada associação.

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

Nesse caso, a `Dependencies` propriedade será invocada após adicionar ou remover dependências `NSOperation` do objeto, garantindo que tenhamos um grafo que represente os objetos reais carregados, impedindo vazamentos de memória, bem como corrupção de memória.

### <a name="postsnippetattribute"></a>PostSnippetAttribute

Você pode usar esse atributo para injetar C# um código-fonte a ser inserido depois que o código tiver invocado o método Objective-C subjacente

Exemplo:

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

Esse atributo é aplicado para retornar valores para sinalizá-los como objetos proxy. Algumas APIs Objective-C retornam objetos de proxy que não podem ser diferenciados das associações de usuário. O efeito desse atributo é sinalizar o objeto como sendo um `DirectBinding` objeto. Para um cenário no Xamarin. Mac, você pode ver a [discussão sobre esse bug](https://bugzilla.novell.com/show_bug.cgi?id=670844).

### <a name="retainlistattribute"></a>RetainListAttribute

Instrui o gerador a manter uma referência gerenciada para o parâmetro ou remover uma referência interna para o parâmetro. Isso é usado para manter os objetos referenciados.

Sintaxe:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se o valor de `doAdd` for true, o parâmetro será adicionado `__mt_{0}_var List<NSObject>;`ao. Onde `{0}` é substituído pelo especificado `listName`. Você deve declarar esse campo de apoio em sua classe parcial complementar para a API.

Para obter um exemplo, consulte [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>Releaseattribute (Xamarin. iOS 6,0)

Isso pode ser aplicado a tipos de retorno para indicar que o gerador deve `Release` chamar no objeto antes de retorná-lo. Isso só é necessário quando um método fornece um objeto retido (em oposição a um objeto autolançado, que é o cenário mais comum)

Exemplo:

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

Além disso, esse atributo é propagado para o código gerado, de forma que o tempo de execução do Xamarin. iOS saiba que ele deve reter o objeto ao retornar para Objective-C dessa função.

### <a name="sealedattribute"></a>Sealattribute

Instrui o gerador a sinalizar o método gerado como lacrado. Se esse atributo não for especificado, o padrão será gerar um método virtual (um método virtual, um método abstract ou uma substituição dependendo de como outros atributos são usados).

<a name="StaticAttribute" />

### <a name="staticattribute"></a>Staticattribute

Quando o `[Static]` atributo é aplicado a um método ou propriedade, isso gera um método ou uma propriedade estática. Se esse atributo não for especificado, o gerador produzirá um método ou uma propriedade de instância.

### <a name="transientattribute"></a>Transitóriaattribute

Use esse atributo para sinalizar propriedades cujos valores são transitórios, ou seja, objetos que são criados temporariamente pelo iOS, mas que não têm vida longa. Quando esse atributo é aplicado a uma propriedade, o gerador não cria um campo de apoio para essa propriedade, o que significa que a classe gerenciada não mantém uma referência ao objeto.

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>Wrapattribute

No design das associações do xamarin. Ios/xamarin. Mac, o `[Wrap]` atributo é usado para encapsular um objeto de tipo fraco com um objeto fortemente tipado. Isso entra em cena principalmente com objetos delegados Objective-C, que normalmente são declarados `id` como `NSObject`sendo do tipo ou. A convenção usada pelo xamarin. Ios e xamarin. Mac é expor esses delegados ou fontes de dados como sendo do tipo `NSObject` e são nomeados usando a Convenção "fraca" + o nome que está sendo exposto. Uma `id delegate` propriedade de Objective-C seria exposta como uma `NSObject WeakDelegate { get; set; }` Propriedade no arquivo de contrato da API.

Mas, normalmente, o valor atribuído a esse delegado é de um tipo forte, portanto, percorremos o tipo forte e `[Wrap]` aplicamos o atributo, isso significa que os usuários podem optar por usar tipos fracos se precisarem de algum controle fino ou se precisarem recorrer a Tric de baixo nível KS ou podem usar a propriedade fortemente tipada para a maioria de seu trabalho.

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

É assim que o usuário usaria a versão de tipo fraco do delegado:

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

E é assim que o usuário usaria a versão fortemente tipada, observe que o usuário aproveita o sistema de C#tipos e está usando a palavra-chave override para declarar sua intenção e que ele não precisa decorar manualmente o método com `[Export]`, já que nós Isso funcionou na associação para o usuário:

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

Outro uso do `[Wrap]` atributo é dar suporte à versão fortemente tipada dos métodos.  Por exemplo:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Quando o `[Wrap]` atributo é aplicado em um método dentro de um tipo decorado `[Category]` com um atributo, você precisa `This` incluir como o primeiro argumento, uma vez que um método de extensão está sendo gerado. Por exemplo:

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

Os membros gerados por `[Wrap]` não `virtual` são por padrão, se você precisar de `virtual` um membro, você pode `true` definir para `isVirtual` o parâmetro opcional.

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]`também pode ser usado diretamente em getters e setters de propriedade.
Isso permite ter controle total sobre eles e ajustar o código conforme necessário.
Por exemplo, considere a seguinte definição de API que usa enums inteligentes:

```csharp
// Smart enum.
enum PersonRelationship {
        [Field (null)]
        None,

        [Field ("FMFather", "__Internal")]
        Father,

        [Field ("FMMother", "__Internal")]
        Mother
}
```

Definição da interface:

```csharp
// Property definition.

[Export ("presenceType")]
NSString _PresenceType { get; set; }

PersonRelationship PresenceType {
    [Wrap ("PersonRelationshipExtensions.GetValue (_PresenceType)")]
    get;
    [Wrap ("_PresenceType = value.GetConstant ()")]
    set;
}
```

## <a name="parameter-attributes"></a>Atributos de parâmetro

Esta seção descreve os atributos que você pode aplicar aos parâmetros em uma definição de método, bem como o `[NullAttribute]` que se aplica a uma propriedade como um todo.

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

Esse atributo é aplicado a tipos de parâmetro C# em declarações de delegado para notificar o fichário de que o parâmetro em questão está em conformidade com a Convenção de chamada de bloco Objective-C e deve empacotá-lo dessa maneira.

Normalmente, isso é usado para retornos de chamada que são definidos desta forma em Objective-C:

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Confira também:  [CCallback](#CCallback).

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

Esse atributo é aplicado a tipos de parâmetro C# em declarações de delegado para notificar o fichário de que o parâmetro em questão está em conformidade com a Convenção de chamada de ponteiro de função da Abi C e deve empacotá-lo dessa maneira.

Normalmente, isso é usado para retornos de chamada que são definidos desta forma em Objective-C:

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

Confira também:  [BlockCallback](#BlockCallback).

### <a name="params"></a>Params

Você pode usar o `[Params]` atributo no último parâmetro de matriz de uma definição de método para que o gerador Insira um "params" na definição.   Isso permite que a associação permita facilmente parâmetros opcionais.

Por exemplo, a seguinte definição:

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

Permite que o seguinte código seja escrito:

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

Isso tem a vantagem adicional de que ele não exige que os usuários criem uma matriz puramente para passar elementos.

<a name="plainstring" />

### <a name="plainstring"></a>Plainvalue

Você pode usar o `[PlainString]` atributo na frente dos parâmetros de cadeia de caracteres para instruir o gerador de associação a passar a cadeia de caracteres como uma cadeia de caracteres `NSString`C, em vez de passar o parâmetro como um.

A maioria das APIs Objective `NSString` -C consome parâmetros, mas algumas APIs expõem uma `char *` API para passar `NSString` cadeias de caracteres, em vez da variação.
Use `[PlainString]` nesses casos.

Por exemplo, as seguintes declarações Objective-C:

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

Deve ser associado desta forma:

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>Retidoattribute

Instrui o gerador a manter uma referência para o parâmetro especificado. O gerador fornecerá o repositório de backup para esse campo ou você poderá especificar um nome (o `WrapName`) para armazenar o valor em. Isso é útil para manter uma referência a um objeto gerenciado que é passado como um parâmetro para Objective-C e quando você sabe que Objective-C manterá apenas essa cópia do objeto. Por exemplo, uma API como `SetDisplay (SomeObject)` usaria esse atributo, pois é provável que setdisplay pudesse exibir apenas um objeto de cada vez. Se você precisar controlar mais de um objeto (por exemplo, para uma API do tipo pilha), você usaria o `[RetainList]` atributo.

Sintaxe:

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

### <a name="retainlistattribute"></a>RetainListAttribute

Instrui o gerador a manter uma referência gerenciada para o parâmetro ou remover uma referência interna para o parâmetro. Isso é usado para manter os objetos referenciados.

Sintaxe:

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

Se o valor de `doAdd` for true, o parâmetro será adicionado `__mt_{0}_var List<NSObject>`ao. Onde `{0}` é substituído pelo especificado `listName`. Você deve declarar esse campo de apoio em sua classe parcial complementar para a API.

Para obter um exemplo, consulte [Foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) e [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>Transitóriaattribute

Esse atributo é aplicado aos parâmetros e é usado somente ao fazer a transição de Objective- C#C para.  Durante essas transições, os vários parâmetros Objective-C `NSObject` são encapsulados em uma representação gerenciada do objeto.

O tempo de execução usará uma referência ao objeto nativo e manterá a referência até que a última referência gerenciada para o objeto tenha sido eliminada e o GC tenha a oportunidade de ser executado.

Em alguns casos, é importante que o C# tempo de execução não mantenha uma referência ao objeto nativo.  Isso às vezes acontece quando o código nativo subjacente anexou um comportamento especial ao ciclo de vida do parâmetro.  Por exemplo: o destruidor para o parâmetro executará alguma ação de limpeza ou descartará um recurso precioso.

Esse atributo informa o tempo de execução que você deseja que o objeto seja descartado, se possível, ao retornar para Objective-C do método substituído.

A regra é simples: se o tempo de execução tivesse que criar uma nova representação gerenciada a partir do objeto nativo, no final da função, a contagem de retenção para o objeto nativo será descartada e a propriedade Handle do objeto gerenciado será limpa.   Isso significa que, se você mantiver uma referência ao objeto gerenciado, essa referência se tornará inútil (invocar métodos nele gerará uma exceção).

Se o objeto passado não tiver sido criado, ou se já havia uma representação gerenciada pendente do objeto, a disposição forçada não ocorrerá. 

## <a name="property-attributes"></a>Atributos de propriedade

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

Esse atributo é usado para dar suporte a um idioma Objective-C em que uma propriedade com um getter é introduzida em uma classe base e uma subclasse mutável apresenta um setter.

Como o C# não oferece suporte a esse modelo, a classe base precisa ter setter e getter, e uma subclasse pode usar o [overrideattribute](#OverrideAttribute).

Esse atributo só é usado em setters de propriedade e é usado para dar suporte ao idioma mutável em Objective-C.

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

## <a name="enum-attributes"></a>Atributos de enumeração

O `NSString` mapeamento de constantes para valores de enumeração é uma maneira fácil de criar uma API .net melhor. It:

* permite que a conclusão de código seja mais útil, mostrando **apenas** os valores corretos para a API;
* adiciona segurança de tipo, você não pode `NSString` usar outra constante em um contexto incorreto; e
* permite ocultar algumas constantes, fazendo com que o auto-completar de código mostre uma lista de API mais curta sem perder a funcionalidade.

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

Da definição de associação acima, o gerador criará `enum` o próprio e também criará `*Extensions` um tipo estático que inclui métodos de conversão de duas maneiras entre os valores de `NSString` enumeração e as constantes. Isso significa que as constantes permanecem disponíveis para os desenvolvedores, mesmo que não façam parte da API.

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

Você pode decorar **um** valor de enumeração com este atributo. Isso se tornará a constante que será retornada se o valor de enumeração não for conhecido.

No exemplo acima:

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

Se nenhum valor de enumeração for decorado `NotSupportedException` , um será gerado.

### <a name="errordomainattribute"></a>ErrorDomainAttribute

Os códigos de erro são associados como valores de enumeração. Em geral, há um domínio de erro para eles e nem sempre é fácil descobrir qual deles se aplica (ou se um mesmo existir).

Você pode usar esse atributo para associar o domínio de erro ao próprio enum.

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

Em seguida, você pode chamar o `GetDomain` método de extensão para obter a constante de domínio de qualquer erro.

### <a name="fieldattribute"></a>Fieldattribute

Esse é o mesmo `[Field]` atributo usado para constantes dentro do tipo. Ele também pode ser usado dentro de enums para mapear um valor com uma constante específica.

Um `null` valor pode ser usado para especificar qual valor de enumeração deve ser retornado se `null` uma `NSString` constante for especificada.

No exemplo acima:

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

Se nenhum `null` valor estiver presente, um `ArgumentNullException` será lançado.

## <a name="global-attributes"></a>Atributos globais

Os atributos globais são aplicados usando o `[assembly:]` modificador de atributo [`[LinkWithAttribute]`](#LinkWithAttribute) como o ou podem ser usados em qualquer [`[Lion]`](#SinceAndLionAttributes) lugar [`[Since]`](#SinceAndLionAttributes) , como os atributos e.

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

Esse é um atributo de nível de assembly que permite aos desenvolvedores especificar os sinalizadores de vinculação necessários para reutilizar uma biblioteca associada sem forçar o consumidor da biblioteca a configurar manualmente os argumentos gcc_flags e mTouch extras passados para uma biblioteca.

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

Esse atributo é aplicado no nível do assembly, por exemplo, isso é o que as [associações CorePlot](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) usam:

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

Quando você usa o `[LinkWith]` atributo, o especificado `libraryName` é inserido no assembly resultante, permitindo que os usuários enviem uma única DLL que contenha as dependências não gerenciadas, bem como os sinalizadores de linha de comando necessários para consumir corretamente o biblioteca do Xamarin. iOS.

Também é possível não fornecer um `libraryName`, caso em que o `LinkWith` atributo pode ser usado para especificar apenas sinalizadores de vinculador adicionais:

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>Construtores LinkWithAttribute

Esses construtores permitem que você especifique a biblioteca a ser vinculada e inserida em seu assembly resultante, os destinos com suporte que a biblioteca dá suporte e quaisquer sinalizadores de biblioteca opcionais necessários para vincular com a biblioteca.

Observe que o `LinkTarget` argumento é inferido pelo Xamarin. Ios e não precisa ser definido.

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

A `ForceLoad` propriedade é usada para decidir se o `-force_load` sinalizador de link é usado para vincular a biblioteca nativa. Por enquanto, isso deve ser sempre verdadeiro.

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

Se a biblioteca que está sendo associada tiver um requisito rígido em qualquer estrutura (diferente `Foundation` de `UIKit`e), você deverá definir `Frameworks` a propriedade como uma cadeia de caracteres que contenha uma lista delimitada por espaço das estruturas de plataforma necessárias. Por exemplo, se você estiver associando uma biblioteca que `CoreGraphics` requer `CoreText`e, você definiria `Frameworks` a propriedade `"CoreGraphics CoreText"`como.

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

Defina essa propriedade como true se o executável resultante precisar ser compilado usando um C++ compilador em vez do padrão, que é um compilador C. Use esta se a biblioteca na C++qual você está associando foi gravada.

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

O nome da biblioteca não gerenciada a ser agrupada. Este é um arquivo com a extensão ". a" e pode conter código de objeto para várias plataformas (por exemplo, ARM e x86 para o simulador).

As versões anteriores do Xamarin. Ios verificaram a `LinkTarget` propriedade para determinar a plataforma com suporte da biblioteca, mas isso agora é detectado automaticamente e a `LinkTarget` propriedade é ignorada.

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

A `LinkerFlags` cadeia de caracteres fornece uma maneira de associar autores para especificar qualquer sinalizador de vinculador adicional necessário ao vincular a biblioteca nativa ao aplicativo.

Por exemplo, se a biblioteca nativa exigir libxml2 e zlib, você definiria a `LinkerFlags` cadeia de `"-lxml2 -lz"`caracteres como.

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

As versões anteriores do Xamarin. Ios verificaram a `LinkTarget` propriedade para determinar a plataforma com suporte da biblioteca, mas isso agora é detectado automaticamente e a `LinkTarget` propriedade é ignorada.

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

Defina essa propriedade como true se a biblioteca que você está vinculando exigir a biblioteca de tratamento de exceção GCC (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

A `SmartLink` propriedade deve ser definida como true para permitir que o Xamarin. Ios `ForceLoad` determine se é necessário ou não.

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

A `WeakFrameworks` Propriedade funciona da mesma maneira que a `Frameworks` Propriedade, exceto que, em tempo de vinculação, `-weak_framework` o especificador é passado para gcc para cada uma das estruturas listadas.

`WeakFrameworks`possibilita que bibliotecas e aplicativos vinculem de forma fraca as estruturas da plataforma para que possam, opcionalmente, usá-las se estiverem disponíveis, mas não fizerem uma dependência difícil delas, o que será útil se a sua biblioteca se destinar a adicionar recursos extras no mais recente versões do iOS. Para obter mais informações sobre vinculação fraca, consulte a documentação da Apple sobre [vinculação fraca](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html).

Bons candidatos à vinculação `Frameworks` fraca seriam como contas `CoreImage`, `NewsstandKit` `CoreBluetooth` `GLKit`,, e `Twitter` já que estão disponíveis apenas no iOS 5.

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>Since (iOS) e Lionattribute (macOS)

Você usa o `[Since]` atributo para sinalizar as APIs como tendo sido introduzidas em um determinado ponto no tempo. O atributo só deve ser usado para sinalizar tipos e métodos que poderiam causar um problema de tempo de execução se a classe subjacente, o método ou a propriedade não estiver disponível.

Sintaxe:

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

Em geral, ele não deve ser aplicado a enumerações, restrições ou novas estruturas, pois elas não causarão um erro de tempo de execução se forem executadas em um dispositivo com uma versão mais antiga do sistema operacional.

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

O `[Lion]` atributo é aplicado da mesma maneira, mas para os tipos introduzidos com Lion. O motivo para usar `[Lion]` versus o número de versão mais específico usado no IOS é que o IOS é revisado com muita frequência, enquanto as versões principais do os X acontecem raramente e é mais fácil lembrar o sistema operacional pelo seu codinome do que pelo número de versão

### <a name="adviceattribute"></a>Conselhoattribute

Use esse atributo para fornecer aos desenvolvedores uma dica sobre outras APIs que podem ser mais convenientes para uso.   Por exemplo, se você fornecer uma versão fortemente tipada de uma API, poderá usar esse atributo no atributo de tipo fraco para direcionar o desenvolvedor para a melhor API.

As informações desse atributo são mostradas na documentação e ferramentas que podem ser desenvolvidas para fornecer sugestões ao usuário sobre como melhorar

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

Essa é uma subclasse especializada do `[Advice]` atributo que pode ser usada para a dica ao desenvolvedor que a substituição de um método **requer** uma chamada para o método base (substituído).

Isso corresponde a `clang`[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

Disponível somente no Xamarin. iOS 5,4 e mais recente.

Esse atributo instrui o gerador de que a associação para essa biblioteca específica (se aplicada com `[assembly:]`) ou o tipo deve usar o marshaling rápido da cadeia de cópia zero. Esse atributo é equivalente a passar a opção `--zero-copy` de linha de comando para o gerador.

Ao usar cópia zero para cadeias de caracteres, o gerador usa efetivamente C# a mesma cadeia de caracteres que o Objective-C consome sem incorrer na criação de um `NSString` novo objeto e evitar copiar os dados das C# cadeias de caracteres para o Cadeia de caracteres Objective-C. A única desvantagem do uso de cadeias de caracteres de cópia zero é que você deve garantir que qualquer propriedade de cadeia de caracteres que `retain` você `copy` encapsular `[DisableZeroCopy]` seja sinalizada como ou que tenha o atributo definido. Isso é necessário porque o identificador para cadeias de caracteres de cópia zero é alocado na pilha e é inválido no retorno da função.

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

Você também pode aplicar o atributo no nível do assembly e ele será aplicado a todos os tipos do assembly:

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>Dicionários fortemente tipados

Com o Xamarin. iOS 8,0, introduzimos o suporte para criar facilmente classes fortemente tipadas que encapsulam `NSDictionaries`.

Embora sempre tenha sido possível usar o tipo de dados [DictionaryContainer](xref:Foundation.DictionaryContainer) junto com uma API manual, agora é muito mais simples fazer isso.  Para obter mais informações, consulte [tipos fortes do identificando](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types).

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

Quando esse atributo é aplicado a uma interface, o gerador produzirá uma classe com o mesmo nome da interface que deriva de [DictionaryContainer](xref:Foundation.DictionaryContainer) e transforma cada propriedade definida na interface em um getter e setter fortemente tipados para o violação.

Isso gera automaticamente uma classe que pode ser instanciada a partir de `NSDictionary` uma existente ou que tenha sido criada de novo.

Esse atributo usa um parâmetro, o nome da classe que contém as chaves que são usadas para acessar os elementos no dicionário.   Por padrão, cada propriedade na interface com o atributo procurará um membro no tipo especificado para um nome com o sufixo "Key".

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

No caso acima, a `MyOption` classe produzirá uma propriedade de cadeia de caracteres para `Name` que usará o `MyOptionKeys.NameKey` como a chave para o dicionário para recuperar uma cadeia de caracteres.   E usará o `MyOptionKeys.AgeKey` como a chave no dicionário para recuperar um `NSNumber` que contém um int.

Se você quiser usar uma chave diferente, poderá usar o atributo de exportação na propriedade, por exemplo:

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

#### <a name="strong-dictionary-types"></a>Tipos de dicionário forte

Os seguintes tipos de dados têm suporte na `StrongDictionary` definição:

|C#Tipo de interface|`NSDictionary`Tipo de armazenamento|
|---|---|
|`bool`|`Boolean`armazenado em um`NSNumber`|
|Valores de enumeração|inteiro armazenado em um`NSNumber`|
|`int`|inteiro de 32 bits armazenado em um`NSNumber`|
|`uint`|inteiro de 32 bits sem sinal armazenado em um`NSNumber`|
|`nint`|`NSInteger`armazenado em um`NSNumber`|
|`nuint`|`NSUInteger`armazenado em um`NSNumber`|
|`long`|inteiro de 64 bits armazenado em um`NSNumber`|
|`float`|inteiro de 32 bits armazenado como um`NSNumber`|
|`double`|inteiro de 64 bits armazenado como um`NSNumber`|
|`NSObject`e subclasses|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array` de`NSObject`|`NSArray`|
|C#`Array` de enumerações|`NSArray`contendo `NSNumber` valores|
