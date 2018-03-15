---
title: "Associação de bibliotecas Objective-C"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: f0e8dabc47352213d18d079ee9f8abb3e557b868
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="binding-objective-c-libraries"></a>Associação de bibliotecas Objective-C

Ao trabalhar com xamarin ou Xamarin.Mac, você pode encontrar casos em que você deseja consumir uma biblioteca de Objective-C de terceiros. Nessas situações, você pode usar os projetos de associação Xamarin para criar uma associação do c# para as bibliotecas Objective-C nativo. O projeto usa as mesmas ferramentas que usamos para colocar as APIs de Mac e iOS em c#.

Este documento descreve como associar APIs Objective-C, se você estiver associando apenas as APIs de C, você deve usar o mecanismo padrão do .NET para isso, [a estrutura de P/Invoke](http://mono-project.com/Dllimport).
Detalhes sobre como vincular estaticamente uma biblioteca C estão disponíveis no [vinculação bibliotecas nativas](~/ios/platform/native-interop.md) página.

Consulte o nosso complementar [guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md).
Além disso, se você quiser saber mais sobre o que acontece nos bastidores, verifique nossa [Binding Overview](~/cross-platform/macios/binding/overview.md) página.

Associações podem ser criadas para iOS e bibliotecas de Mac.
Esta página descreve como trabalhar em um iOS associação, no entanto, associações de Mac são muito semelhantes.

**Código de exemplo para iOS**

Você pode usar o [iOS associação exemplo](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) projeto para fazer experiências com associações.

<a name="Getting_Started" />

## <a name="getting-started"></a>Introdução

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

É a maneira mais fácil para criar uma associação criar um projeto de associação xamarin.
Você pode fazer isso no Visual Studio para Mac, selecionando o tipo de projeto, **iOS > Biblioteca > biblioteca associações**:

[![](objective-c-libraries-images/00-sml.png "Fazer isso no Visual Studio para Mac, selecionando o tipo de projeto, biblioteca de associações do iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

É a maneira mais fácil para criar uma associação criar um projeto de associação xamarin.
Você pode fazer isso no Visual Studio no Windows, selecionando o tipo de projeto, **Visual C# > iOS > biblioteca de associações (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "Biblioteca de associações iOS do iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Observação: A associação de projetos para **Xamarin.Mac** só tem suporte no Visual Studio para Mac.

-----

Projeto gerado contém um modelo pequeno que pode ser editado, ele contém dois arquivos: `ApiDefinition.cs` e `StructsAndEnums.cs`.

O `ApiDefinition.cs` é onde você definirá o contrato de API, esse é o arquivo que descreve como a API Objective-C subjacente é projetada em c#. A sintaxe e o conteúdo desse arquivo é o principal tópico de discussão deste documento e o conteúdo dele é limitado às interfaces do c# e declarações de delegado do c#. O `StructsAndEnums.cs` é o arquivo onde você digitará as definições que são necessárias, as interfaces e delegados. Isso inclui valores de enumeração e estruturas que possam usar seu código.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Associação de uma API

Para fazer uma ligação abrangente, você deve entender a definição de API Objective-C e familiarizar-se com as diretrizes de Design do .NET Framework.

Para associar sua biblioteca você normalmente começa com um arquivo de definição de API. Um arquivo de definição de API é simplesmente um C# arquivo de origem que contém a associação de interfaces c# anotados com uma série de atributos que ajudam a unidade.  Esse arquivo é o que define o que é o contrato entre c# e Objective-C.

Por exemplo, este é um arquivo de api simples para uma biblioteca:

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

O exemplo acima define uma classe chamada `Cocos2D.Camera` que deriva de `NSObject` tipo base (esse tipo vem de `Foundation.NSObject`) e que define uma propriedade estática (`ZEye`), dois métodos que usam nenhum argumento e um método que usa três argumentos.

Abrange uma discussão detalhada sobre o formato de arquivo da API e os atributos que você pode usar o [arquivo de definição de API](~/cross-platform/macios/binding/objective-c-libraries.md) seção abaixo.

Para produzir uma associação concluída, normalmente você lidará com quatro componentes:

-  O arquivo de definição de API (`ApiDefinition.cs` no modelo).
-  Opcional: qualquer enumerações, tipos, estruturas necessárias para o arquivo de definição de API (`StructsAndEnums.cs` no modelo).
-  Opcionais: fontes extras que podem expandir a associação gerada ou fornecer uma mais amigável API c# (c# arquivos que você adicionar ao projeto).
-  A biblioteca nativa que você está associando.

Este gráfico mostra a relação entre os arquivos:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Este gráfico mostra a relação entre os arquivos")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

O arquivo de definição de API: conterá apenas as definições de interface e namespaces (com os membros que pode conter uma interface) e não deve conter classes, enumerações, delegados ou estruturas. O arquivo de definição de API é simplesmente o contrato que será usado para gerar a API.

Qualquer código extra que você precisa como enumerações ou classes de suporte devem ser hospedado em um arquivo separado, no exemplo acima "CameraMode" é um valor de enumeração que não existe no arquivo CS e deve ser hospedado em um arquivo separado, por exemplo `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

O `APIDefinition.cs` arquivo é combinado com o `StructsAndEnum` de classe e são usados para gerar a associação de núcleo da biblioteca. Você pode usar a biblioteca resultante como-é, mas em geral, convém ajustar a biblioteca resultante para adicionar alguns recursos do c# para o benefício de seus usuários. Alguns exemplos incluem implementar um `ToString()` método, fornecer indexadores c#, adicione conversões implícitas de e para alguns tipos nativos ou fornecer versões fortemente tipada de alguns métodos. Essas melhorias são armazenadas em arquivos extras do c#. Simplesmente adicione arquivos c# ao seu projeto e eles serão incluídos neste processo de compilação.

Isso mostra como você poderia implementar o código no seu `Extra.cs` arquivo. Observe que você estiver usando classes parciais como esses aumentar classes parciais que são geradas pela combinação da `ApiDefinition.cs` e `StructsAndEnums.cs` principal da associação:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Criando a biblioteca produzirá a associação nativo.

Para concluir essa associação, você deve adicionar a biblioteca nativa para o projeto.  Você pode fazer isso adicionando a biblioteca nativa ao seu projeto, arrastando e soltando a biblioteca nativa de localizador para o projeto no Gerenciador de soluções, ou clicando duas vezes no projeto e escolhendo **adicionar**  >  **Adicionar arquivos** para selecionar a biblioteca nativa.
Bibliotecas nativas por convenção, iniciam com a palavra "lib" e terminam com a extensão ".a". Quando você fizer isso, o Visual Studio para Mac adicionará dois arquivos: o `.a` arquivo e um arquivo c# preenchido automaticamente que contém informações sobre o que contém a biblioteca nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Bibliotecas nativas por convenção começam com a biblioteca de palavra e terminam com a extensão .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

O conteúdo do `libMagicChord.linkwith.cs` arquivo tem informações sobre como essa biblioteca pode ser usada e instrui o IDE para empacotar este binário para o arquivo DLL resultante:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Detalhes completos sobre como usar o atributo LinkWith estão documentados em nosso [guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md).

Agora quando você compilar o projeto você acabará com uma `MagicChords.dll` arquivo que contém a associação e a biblioteca nativa. Você pode distribuir esse projeto ou usar a DLL resultante para outros desenvolvedores para seus próprios.

Às vezes, você pode achar que precisa de alguns valores de enumeração, definições de delegado ou outros tipos. Não coloque-os no arquivo de definições de API, pois esta é meramente um contrato

 <a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>O arquivo de definição de API

O arquivo de definição de API consiste em um número de interfaces. As interfaces na definição de API serão transformadas em uma declaração de classe e eles devem ser decorados com o [[BaseType]](~/cross-platform/macios/binding/binding-types-reference.md) atributo para especificar a classe base para a classe.

Você deve estar se perguntando por que não usamos classes em vez de interfaces para a definição de contrato. Escolhemos interfaces porque permitiu gravar o contrato para um método sem ter que fornecer um corpo de método no arquivo de definição de API ou ter que fornecer um corpo que tiveram de lançar uma exceção ou retornar um valor significativo.

Mas, como estamos usando a interface como um esqueleto para gerar uma classe que tivemos que recorrer a decoração de várias partes do contrato com os atributos para a associação da unidade.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Métodos de associação

A associação mais simples, que você pode fazer é um método bind. Basta declarar um método na interface com as convenções de nomenclatura c# e decorar o método com o [[exportar]](~/cross-platform/macios/binding/binding-types-reference.md) atributo. O atributo [exportação] é o que vincula o seu nome com o nome de Objective-C em tempo de execução do xamarin c#. O parâmetro do atributo de exportação é o nome do seletor Objective-C, alguns exemplos:

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

Os exemplos acima mostram como você pode vincular os métodos de instância. Para associar os métodos estáticos, você deve usar o `[Static]` atributo, como este:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Isso é necessário porque o contrato faz parte de uma interface e interfaces não tem nenhuma noção de declarações de instância estático vs, portanto, é necessário uma vez para recurso de atributos. Se você quiser ocultar um método específico de associação, você pode decorar o método com o [[interno]](~/cross-platform/macios/binding/binding-types-reference.md) atributo.

O `btouch-native` comando apresentará verificações para parâmetros de referência não pode ser nulo. Se você quiser permitir valores nulos para um parâmetro específico, use o [[NullAllowed]](~/cross-platform/macios/binding/binding-types-reference.md) atributo no parâmetro, como este:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Ao exportar um tipo de referência, com o `[Export]` palavra-chave, você também pode especificar a semântica de alocação. Isso é necessário para garantir que nenhum dado será perdido.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propriedades de associação

Assim como os métodos, propriedades Objective-C são associadas usando o [[exportar]](~/cross-platform/macios/binding/binding-types-reference.md) atributo e diretamente para as propriedades do c#. Assim como os métodos, propriedades podem ser decoradas com o [[Static]](~/cross-platform/macios/binding/binding-types-reference.md) e [[interno]](~/cross-platform/macios/binding/binding-types-reference.md) atributos.

Quando você usa o `[Export]` atributo em uma propriedade em btouch-nativo abrange realmente vincula os dois métodos: o getter e setter. O nome que você fornecer para exportar o **nome base** e o setter é computada acrescentando a palavra "set", transformando a primeira letra da **nome base** em letras maiusculas e fazer com que o seletor de tirar um argumento. Isso significa que `[Export ("label")]` aplicada em uma propriedade realmente associa o rótulo"" e "setLabel:" métodos Objective-C.

Às vezes, as propriedades Objective-C não seguem o padrão descrito acima, e o nome será substituído manualmente. Nesses casos, você pode controlar a maneira que a associação é gerada usando o `[Bind]` atributo no getter ou setter, por exemplo:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Essa, em seguida, associa "isMenuVisible" e "setMenuVisible:". Opcionalmente, uma propriedade pode ser vinculada usando a seguinte sintaxe:

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

Onde o getter e setter são explicitamente definidos como no `name` e `setName` associações acima.

Além de suporte para propriedades estáticas usando `[Static]`, você pode decorar propriedades de thread estático com [[IsThreadStatic]](~/cross-platform/macios/binding/binding-types-reference.md), por exemplo:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Assim como métodos permitem alguns parâmetros para ser sinalizadas com [[NullAllowed]](~/cross-platform/macios/binding/binding-types-reference.md), você pode aplicar [[NullAllowed]](~/cross-platform/macios/binding/binding-types-reference.md) a uma propriedade para indicar que null é um valor válido para a propriedade, por exemplo:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

O [[NullAllowed]](~/cross-platform/macios/binding/binding-types-reference.md) parâmetro também pode ser especificado diretamente em setter:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Condições de associação de controles personalizados

As seguintes condições devem ser consideradas ao configurar a associação para um controle personalizado:

1. **Propriedades de associação devem ser estáticas** - ao definir a associação de propriedades, o `Static` atributo deve ser usado.
2. **Nomes de propriedade devem corresponder exatamente** -o nome usado para associar a propriedade deve corresponder ao nome da propriedade no controle personalizado exatamente.
3. **Tipos de propriedade devem corresponder exatamente** -o tipo de variável usado para associar a propriedade deve corresponder exatamente o tipo de propriedade no controle personalizado.
4. **Pontos de interrupção e o getter/setter** - pontos de interrupção são colocados no getter ou métodos setter da propriedade nunca serão atingidos.
5. **Observe os retornos de chamada** -você precisará usar retornos de chamada de Observação para ser notificado sobre alterações nos valores de propriedade de controles personalizados.

Falha ao observar qualquer as condições listadas acima pode resultar na associação silenciosamente falhar em tempo de execução.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Propriedades e padrão mutável objective-C

Estruturas Objective-C usam um idioma em que algumas classes são imutáveis com uma subclasse mutável.   Por exemplo `NSString` é a versão imutável, enquanto `NSMutableString` é a subclasse que permite mutação.

Essas classes é comum ver a classe base imutável conter propriedades com um getter, mas nenhum setter.   E para a versão mutável introduzir o setter.   Já que isso não é possível com c#, tivemos que mapear este idioma em um idioma que trabalharia com c#.

A maneira que é mapeada para c# é adicionando o getter e setter da classe base, mas sinalizar o setter com um `[NotImplemented]` atributo.

Em seguida, na subclasse mutável, use o `[Override]` atributo na propriedade para garantir que a propriedade realmente está substituindo o comportamento do pai.

Exemplo:

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

 <a name="Binding_Constructors" />

### <a name="binding-constructors"></a>Construtores de associação

O **btouch nativo** ferramenta gerará automaticamente construtores de quatro na sua classe, para uma determinada classe `Foo`, ele gera:

-  `Foo ()`: o construtor padrão (mapeado para o construtor de "init" do Objective-C)
-  `Foo (NSCoder)`: o construtor usado durante a desserialização de arquivos NIB (mapeado para Objective-C "initWithCoder:" construtor).
-  `Foo (IntPtr handle)`: o construtor para criação de identificador, isso é ser invocado pelo tempo de execução quando o tempo de execução precisa expor um objeto gerenciado de um objeto não gerenciado.
-  `Foo (NSEmptyFlag)`: isso é usado por classes derivadas para impedir a inicialização dupla.

Construtores definidos, eles precisam ser declarados usando a seguinte assinatura dentro da definição da Interface: eles devem retornar um `IntPtr` valor e o nome do método devem ser o construtor. Por exemplo, para associar o `initWithFrame:` construtor, este é o que você usaria:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

 <a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolos de associação

Conforme descrito no documento de design de API, na seção [discutindo modelos e protocolos](~/ios/internals/api-design/index.md), xamarin mapeia os protocolos Objective-C em classes que foram sinalizados com o [[modelo]](~/cross-platform/macios/binding/binding-types-reference.md) atributo. Isso normalmente é usado durante a implementação de classes de delegate Objective-C.

A grande diferença entre uma classe associada regular e uma classe de representante é que a classe delegate pode ter um ou mais métodos opcionais.

Por exemplo considere o `UIKit` classe `UIAccelerometerDelegate`, isso é como ele está vinculado no xamarin:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Como este é um método opcional na definição para `UIAccelerometerDelegate` há mais nada a fazer. Mas se houver um método necessário no protocolo, você deve adicionar o [[abstrata]](~/cross-platform/macios/binding/binding-types-reference.md) atributo ao método. Isso forçará o usuário da implementação, na verdade, forneça um corpo para o método.

Em geral, os protocolos são usados nas classes que respondem às mensagens. Normalmente, isso é feito em Objective-C pela atribuição à propriedade "representante" de uma instância de um objeto que responde aos métodos no protocolo.

A convenção de xamarin é oferecer suporte a ambos o Objective-C livremente acoplada estilo onde qualquer instância de um `NSObject` pode ser atribuída ao delegado e também expor uma versão fortemente tipada dele. Por esse motivo, é normalmente fornecem uma propriedade "Representante" que é fortemente tipada e um "WeakDelegate" que é digitado sem rigidez. Ligamos geralmente a versão menos tipada com a exportação e usamos o [[Wrap]](~/cross-platform/macios/binding/binding-types-reference.md) atributo para fornecer a versão fortemente tipada.

Isso mostra como é associada a `UIAccelerometer` classe:

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

 <a name="iOS7ProtocolSupport" />

**Novo no MonoTouch 7.0**

Começando com MonoTouch 7.0 um funcionalidade de ligação de protocolo novo e aprimorado incorporado.  Esse novo suporte torna mais simples usar linguagens Objective-C para adotar a um ou mais protocolos em uma determinada classe.

Para cada definição de protocolo `MyProtocol` em Objective-C, agora há uma `IMyProtocol` interface que lista todos os métodos necessários do protocolo, bem como uma classe de extensão que fornece os métodos opcionais.  Acima, combinados com novo suporte no Xamarin Studio editor permite que os desenvolvedores a implementar os métodos de protocolo sem precisar usar as subclasses separadas das classes de modelo abstrato anterior.

Qualquer definição que contém o `[Protocol]` atributo irá gerar três classes de suporte que melhoram a maneira que você usar protocolos:

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

O **implementação da classe** fornece uma classe abstrata completa, você pode substituir métodos individuais e obter a segurança de tipo completo.  Mas devido a c# não dá suporte à herança múltipla, há cenários em que você pode precisa ter uma classe base diferente, mas você ainda deseja implementar uma interface, que é o local onde o

Gerado **definição de interface** entram em ação.  É uma interface que tem todos os métodos necessários do protocolo.  Isso permite que os desenvolvedores que desejam implementar o protocolo para simplesmente implementar a interface.  O tempo de execução registrará automaticamente o tipo como adotar o protocolo.

Observe que a interface somente lista os métodos necessários e expor os métodos opcionais.  Isso significa que as classes que adotam o protocolo obterá completo verificação de tipo para os métodos necessários, mas terão que recorrer a digitação fraca (manualmente usando os atributos de exportação e a assinatura de correspondência) para os métodos de protocolo opcional.

Para que seja conveniente para consumir uma API que usa protocolos, a ferramenta de associação também produzirá uma classe de método de extensões que expõe todos os métodos opcionais.  Isso significa que enquanto está consumindo uma API, você será capaz de tratar protocolos como tendo todos os métodos.

Se você quiser usar as definições de protocolo em sua API, precisará escrever esqueleto interfaces vazias em sua definição de API.  Se você quiser usar o MeuProtocolo em uma API, você precisa fazer isso:

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

#### <a name="adopting-protocol-generated-interfaces"></a>Adotando gerado pelo protocolo de interfaces

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

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Associando extensões de classe

<!--In Objective-C it is possible to extend classes with new methods,
similar in spirit to C#'s extension methods. When one of these methods
is present, you can use the `[Target]` attribute to flag the first
parameter of a method as being the receiver of the Objective-C
message.

For example, in Xamarin.iOS we bound the extension methods that are defined on
`NSString` when `UIKit` is imported as methods in the `UIView`, like this:

```csharp
[BaseType (typeof (UIResponder))]
interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

-->

Em Objective-C, é possível estender classes com novos métodos semelhantes em espírito para métodos de extensão do #. Quando um desses métodos está presente, você pode usar o `BaseType` atributo para sinalizar o método como sendo o receptor da mensagem Objective-C.

Por exemplo, no xamarin é vinculado os métodos de extensão que estão definidos no `NSString` quando `UIKit` são importados como métodos no `NSStringDrawingExtensions`, assim:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

 <a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Associação de listas de argumentos Objective-C

Objective-C oferece suporte a argumentos variadic, você pode usar a técnica a seguir descrita por Zach Gris no [esta postagem](http://forums.monotouch.net/yaf_postst311_SOLVED-Binding-ObjectiveC-Argument-Lists.aspx).

Uma mensagem Objective-C tem esta aparência:

```csharp
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Para chamar esse método em c#, você desejará criar uma assinatura como este:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Isso declara o método como interno, ocultando a API acima de usuários, mas expô-lo para a biblioteca. Em seguida, você pode escrever um método como este:

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

 <a name="Binding_Fields" />

### <a name="binding-fields"></a>Campos de associação

Às vezes, você desejará acessar campos públicos que foram declarados em uma biblioteca.

Normalmente esses campos contêm valores de cadeias de caracteres ou inteiros que devem ser referenciados. Eles são usados como cadeia de caracteres que representam uma notificação específica e como as chaves no dicionário.

Para vincular um campo, adicionar uma propriedade ao seu arquivo de definição de interface e decorar a propriedade com o [[Field]](~/cross-platform/macios/binding/binding-types-reference.md) atributo. Este atributo utiliza um parâmetro: o nome de C do símbolo para pesquisa. Por exemplo:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Se você quiser encapsular vários campos em uma classe estática que não derivam de `NSObject`, você pode usar o `[Static]` atributo da classe, como este:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Acima gerará um `LonelyClass` que não é derivado de `NSObject` e conterá uma associação para o `NSSomeEventNotification` 
 `NSString` exposto como um `NSString`.

O `[Field]` atributo pode ser aplicado aos seguintes tipos de dados:

-  `NSString` referências (apenas para propriedades somente leitura)
-  `NSArray` referências (apenas para propriedades somente leitura)
-  ints de 32 bits (`System.Int32`)
-  ints de 64 bits (`System.Int64`)
-  floats de 32 bits (`System.Single`)
-  flutuações de 64 bits (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

Além do nome do campo nativo, você pode especificar o nome da biblioteca em que o campo está localizado, passando o nome da biblioteca:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Se você está vinculando estaticamente, não há nenhuma biblioteca para vincular, portanto você precisa usar o `__Internal` nome:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enumerações de associação

Você pode adicionar `enum` diretamente na sua associação de arquivos para torna mais fácil para usá-los dentro de definições de API - sem usar um arquivo de origem diferente (que precisa ser compilado nas associações e o final do projeto).

Exemplo:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

Também é possível criar seu próprio enums substituir `NSString` constantes. Nesse caso o gerador será **automaticamente** criar métodos para converter valores de enumerações e constantes NSString para você.

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

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

No exemplo acima, você pode decidir decorar `void Perform (NSString mode);` com um `[Internal]` atributo. Esta será **ocultar** a API baseada em constante de seus consumidores de associação.

No entanto isso limitará subclasses o tipo como a API melhor alternativa usa uma `[Wrap]` atributo. Esses métodos gerados não são `virtual`, ou seja, você não poderá substituí-los - que pode, ou não, uma boa escolha.

Uma alternativa é marcar o original, `NSString`-com base em definição como `[Protected]`. Isso permitirá subclassificação de trabalho, quando necessário, e a versão de wrap'ed ainda funcionarão e chame o método substituído.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Associando NSValue, NSNumber e NSString a um tipo melhor

O [[BindAs]](~/cross-platform/macios/binding/binding-types-reference.md) atributo permite associação `NSNumber`, `NSValue` e `NSString`(enums) em tipos c# mais precisos. O atributo pode ser usado para criar o melhor e mais precisas, API .NET sobre a API nativa.

Você pode decorar métodos (no valor de retorno), parâmetros e propriedades com [[BindAs]](~/cross-platform/macios/binding/binding-types-reference.md). A única restrição é que o membro **não** dentro de um `[Protocol]` ou `[Model]` interface.

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

[[BindAs] ](~/cross-platform/macios/binding/binding-types-reference.md) também oferece suporte a matrizes de `NSNumber` `NSValue` e `NSString`(enums).

Por exemplo:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Geraria:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` é um `NSString` feito enum, podemos buscará direita `NSString` valor e lidar com a conversão de tipo.

Consulte [[BindAs] documentação](~/cross-platform/macios/binding/binding-types-reference.md) ver os tipos de conversão com suporte.

 <a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notificações de associação

As notificações são mensagens que são postadas para o `NSNotificationCenter.DefaultCenter` e são usados como um mecanismo para difundir mensagens de uma parte do aplicativo para outro. Os desenvolvedores de assinam notificações normalmente usando o [NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/)do [AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/) método. Quando um aplicativo envia uma mensagem para o Centro de notificação, ele normalmente contém uma carga armazenada no [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) dicionário. Esse dicionário é digitado sem rigidez e Obtendo informações fora dele está sujeito a erros, como os usuários geralmente precisam ler na documentação do qual as chaves estão disponíveis no dicionário e os tipos dos valores que podem ser armazenados no dicionário. A presença de chaves, às vezes, é usada como um booliano também.

O gerador de associação xamarin oferece suporte para os desenvolvedores associar as notificações. Para fazer isso, você deve definir o [[notificação]](~/cross-platform/macios/binding/binding-types-reference.md) atributo em uma propriedade que também foi marcado com um [[Field]](~/cross-platform/macios/binding/binding-types-reference.md) propriedade (pode ser público ou privado).

Esse atributo pode ser usado sem argumentos para notificações que não realizar nenhuma carga útil, ou você pode especificar um `System.Type` que faz referência a outra interface na definição de API, normalmente com o nome termina com "EventArgs". O gerador de transformará a interface em uma classe que as subclasses `EventArgs` e incluirá todas as propriedades listadas. O `[Export]` atributo deve ser usado na classe EventArgs para listar o nome da chave usada para pesquisar o dicionário Objective-C para buscar o valor.

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
   }
}
```

Os usuários do seu código, em seguida, podem facilmente assinar notificações lançadas para o [NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/) usando código como este:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O valor retornado de `ObserveDidStart` pode ser usado para facilmente interromper o recebimento de notificações, como este:

```csharp
token.Dispose ();
```

Ou você pode chamar [NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/) e passar o token. Se a notificação contiver parâmetros, você deve especificar um auxiliar `EventArgs` interface, como este:

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

Acima gerará um `MyScreenChangedEventArgs` classe com o `ScreenX` e `ScreenY` propriedades buscará os dados do [NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) dicionário usando as teclas "ScreenXKey" e "ScreenYKey" respectivamente e aplicar as conversões apropriadas. O `[ProbePresence]` atributo é usado para o gerador de investigar se a chave for definida no `UserInfo`, em vez de tentar extrair o valor. Isso é usado para casos em que a presença da chave é o valor (normalmente por valores booleanos).

Isso permite que você escreva código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

 <a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorias de associação

Categorias são um mecanismo de Objective-C usado para estender o conjunto de métodos e propriedades disponíveis em uma classe.   Na prática, eles são usados para estender a funcionalidade de uma classe base (por exemplo `NSObject`) quando uma estrutura específica estiver vinculada (por exemplo `UIKit`), tornando seus métodos disponíveis, mas somente se a nova estrutura está vinculada.   Em alguns casos, eles são usados para organizar os recursos em uma classe por funcionalidade.   Eles são semelhantes em espírito para métodos de extensão do c#. Isso é a aparência uma categoria no objetivo-c:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

O exemplo acima se encontrado em uma biblioteca se estenderá instâncias do `UIView` com o método `makeBackgroundRed`.

Para associar os, você pode usar o `[Category]` atributo em uma definição de interface.  Quando usar a categoria de atributo, o significado do `[BaseType]` atributo muda de que está sendo usado para especificar a classe base para estender, para ser o tipo a ser estendido.

A seguir mostra como o `UIView` extensões são vinculadas e transformadas em métodos de extensão do c#:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

As opções acima criará uma `MyUIViewExtension` uma classe que contém o `MakeBackgroundRed` método de extensão.  Isso significa que agora você pode chamar "MakeBackgroundRed" em qualquer `UIView` subclasse, fornecendo a mesma funcionalidade que você obteria no objetivo-C. Em alguns casos, as categorias são usadas não estender uma classe de sistema, mas para organizar funcionalidade meramente para fins de decoração.  Assim:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Embora você possa usar o `Category` atributo também para esse estilo de decoração de declarações, você pode também apenas adicioná-los todos para a definição de classe.  Ambos seriam alcançar o mesmo:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

É apenas mais curto para mesclar as categorias nesses casos:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

 <a name="Binding_Blocks" />

### <a name="binding-blocks"></a>Blocos de associação

Os blocos são uma nova construção de introduzido pela Apple para trazer o equivalente funcional de métodos anônimos c# para objetivo-C. Por exemplo, a `NSSet` classe agora expõe esse método:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

A descrição acima declara um método chamado "*enumerateObjectsUsingBlock:*" que usa um argumento nomeado *bloco*. Esse bloco é semelhante a um método anônimo c#, porque tem suporte para capturar o ambiente atual (o ponteiro "this", o acesso aos parâmetros e variáveis locais). O método acima em `NSSet` invoca o bloco com dois parâmetros um `NSObject` (a parte "obj id") e um ponteiro para um valor booliano (o "BOOL * parar") parte.

Para associar esse tipo de API com btouch, você precisa primeiro declarar a assinatura de tipo de bloco como c# delegar e, em seguida, fazer referência a ela, de um ponto de entrada de API, como este:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

E agora o seu código pode chamar a função do c#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

Se preferir, você também pode usar lambdas:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

 <a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Métodos assíncronos

O gerador de associação pode transformar uma determinada classe de métodos em métodos async amigáveis (métodos que retornam uma tarefa ou tarefa&lt;T&gt;).

Você pode usar o `[Async]` atributo em métodos que retornam void e cujo último argumento é um retorno de chamada.  Quando você se aplicam a um método, o gerador de associação irá gerar uma versão do método com o sufixo `Async`.  Se o retorno de chamada não usa nenhum parâmetro, o valor de retorno será uma `Task`, se o retorno de chamada usa um parâmetro, o resultado será um `Task<T>`.  Se o retorno de chamada usa vários parâmetros, você deve definir o `ResultType` ou `ResultTypeName` para especificar o nome desejado do tipo gerado que armazenará todas as propriedades.

Exemplo:

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

O código acima irá gerar o método LoadFile, bem como:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Adicionar tipos fortes para parâmetros de NSDictionary fracos

Em muitos lugares na API Objective-C, os parâmetros são passados como levemente digitado `NSDictionary` APIs com chaves específicas e valores, mas eles são propenso (você pode passar de chaves inválidas e não obter nenhum aviso, você pode passar valores inválidos e não obter nenhum aviso) e frustrante Para usar como eles exigem várias viagens e a documentação para pesquisar os possíveis nomes de chave e valores.

A solução é fornecer uma versão fortemente tipada que fornece a versão fortemente tipada da API e nos bastidores mapeia vários subjacente chaves e valores.

Portanto, por exemplo, se a API Objective-C aceita um `NSDictionary` e ele é documentado como colocar a chave "XyzVolumeKey", que usa um `NSNumber` com um valor de volume de 0,0 1.0 e um "XyzCaptionKey" que usa uma cadeia de caracteres, você desejaria que os usuários tenham uma boa API que tem esta aparência:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

O `Volume` está definida como anulável float, conforme a convenção em Objective-C não requer esses dicionários para que o valor, portanto, há cenários em que o valor não pode ser definido.

Para fazer isso, você precisa fazer algumas coisas:

* Crie uma classe fortemente tipada, que as subclasses [DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/) e fornece vários getters e setters para cada propriedade.
* Declarar sobrecargas para os métodos fazendo `NSDictionary` para levar a nova versão fortemente tipada.

Você pode criar a classe fortemente tipada ou manualmente ou usar o gerador para o trabalho para você.  Primeiro, exploraremos como fazer isso manualmente, para que você entender o que está acontecendo e, em seguida, o método automático.

Você precisa criar um arquivo de suporte para isso, ele não entrará no seu contrato de API.  Este é o que você teria de gravação criar sua classe XyzOptions:

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

Em seguida, você deve fornecer um método de invólucro que reproduz a API de alto nível sobre a API de nível inferior.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Se a sua API não precisa ser substituído, você pode ocultar a API baseada em NSDictionary com segurança usando o [interno](~/cross-platform/macios/binding/binding-types-reference.md) atributo.

Como você pode ver, usamos o `[Wrap]` de atributo para um novo ponto de entrada da API de superfície e podemos fazê-lo usando nossa classe XyzOptions fortemente tipada.
O método wrapper também permite null deve ser passado.

Agora, uma coisa que não mencionamos é onde o `XyzOptionsKeys` valores veio.  Normalmente, você agruparia as chaves que uma superfície de API em uma classe estática como XyzOptionsKeys, como este:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Vamos examine o suporte automático para a criação desses dicionários fortemente tipados.  Isso evita que muito do trabalho repetitivo e você pode definir o dicionário diretamente no seu contrato de API, em vez de usar um arquivo externo.

Para criar um dicionário fortemente tipado, apresentar uma interface de sua API e decorar com o [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md) atributo.  Isso informa o gerador de que ele deve criar uma classe com o mesmo nome que a interface que será derivado `DictionaryContainer` e fornecerá acessadores de tipo fortes para ele.

O `StrongDictionary` atributo tem um parâmetro, que é o nome da classe estática que contém as chaves de dicionário.  Em seguida, cada propriedade da interface se tornará um acessador com rigidez de tipos.  Por padrão, o código usará o nome da propriedade com o sufixo "Chave" na classe estática para criar o acessador.

Isso significa que criar o acessador com rigidez de tipos não requer mais um arquivo externo, nem ter que criar manualmente os getters e setters para todas as propriedades, nem precisar pesquisar as chaves manualmente por conta própria.

Isso é como a associação inteira seria:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

No caso de você precisar fazer referência em seu `XyzOption` membros um campo diferente (que é não o nome da propriedade com o sufixo `Key`), pode decorar a propriedade com um `Export` atributo com o nome que você deseja usar.

 <a name="Type_mappings" />

## <a name="type-mappings"></a>Mapeamentos de tipo

Esta seção aborda como Objective-C tipos são mapeados para tipos c#.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipos simples

A tabela a seguir mostra como você deve mapear tipos de Objective-C e do mundo CocoaTouch para o mundo de xamarin:

<table border="1" cellpadding="1" cellspacing="1" width="80%">
      <caption> Mapeamentos de tipo </caption>
      <tbody>
        <tr>
          <td>
Nome do tipo Objective-C </td>
          <td>
Tipo de API unificada xamarin </td>
        </tr>
        <tr>
          <td>
BOOL, GLboolean </td>
          <td>
bool </td>
        </tr>
        <tr>
          <td>
NSInteger </td>
          <td>
nint </td>
        </tr>
        <tr>
          <td>
NSUInteger </td>
          <td>
nuint </td>
        </tr>
        <tr>
          <td>
CFTimeInterval / NSTimeInterval </td>
          <td>
double </td>
        </tr>
        <tr>
          <td>
NSString (<a href="~/ios/internals/api-design/nsstring.md">mais informações sobre associação NSString</a>) </td>
          <td>
cadeia de caracteres </td>
        </tr>
        <tr>
          <td>
char * </td>
          <td>
            <a href="~/cross-platform/macios/binding/binding-types-reference.md#plainstring"> [PlainString] </a> cadeia de caracteres </td>
        </tr>
        <tr>
          <td>
CGRect </td>
          <td>
CGRect </td>
        </tr>
        <tr>
          <td>
CGPoint </td>
          <td>
CGPoint </td>
        </tr>
        <tr>
          <td>
CGSize </td>
          <td>
CGSize </td>
        </tr>
        <tr>
          <td>
CGFloat, GLfloat </td>
          <td>
nfloat </td>
        </tr>
        <tr>
          <td>
Tipos de CoreFoundation (CF *) </td>
          <td>
CoreFoundation.CF* </td>
        </tr>
        <tr>
          <td>
GLint </td>
          <td>
nint </td>
        </tr>
        <tr>
          <td>
GLfloat </td>
          <td>
nfloat </td>
        </tr>
        <tr>
          <td>
Tipos de base (NS *) </td>
          <td>
Foundation.NS* </td>
        </tr>
        <tr>
          <td>
id </td>
          <td>
Foundation.NSObject </td>
        </tr>
        <tr>
          <td>
NSGlyph </td>
          <td>
nint </td>
        </tr>
        <tr>
          <td>
NSSize </td>
          <td>
CGSize </td>
        </tr>
        <tr>
          <td>
NSTextAlignment </td>
          <td>
UITextAlignment </td>
        </tr>
        <tr>
          <td>
SEL. </td>
          <td>
ObjCRuntime.Selector </td>
        </tr>
        <tr>
          <td>
dispatch_queue_t </td>
          <td>
CoreFoundation.DispatchQueue </td>
        </tr>
        <tr>
          <td>
CFTimeInterval </td>
          <td>
double </td>
        </tr>
        <tr>
          <td>
CFIndex </td>
          <td>
nint </td>
        </tr>
        <tr>
          <td>
NSGlyph </td>
          <td>
nuint </td>
        </tr>
      </tbody>
    </table>

 <a name="Arrays" />

### <a name="arrays"></a>Matrizes

O tempo de execução do xamarin automaticamente se encarrega da conversão c# matrizes para `NSArrays` e fazer a conversão, por exemplo o método Objective-C imaginária que retorna um `NSArray` de `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

É vinculado como este:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

A ideia é usar uma matriz fortemente tipada do c# que permite o IDE fornecer a conclusão de código correta com o tipo real sem fazer com que o usuário adivinhar ou consultar a documentação para descobrir o tipo real dos objetos contidos na matriz.

Em casos onde você pode não rastrear o tipo mais derivado real contido na matriz, você pode usar `NSObject []` como o valor de retorno.

 <a name="Selectors" />

### <a name="selectors"></a>Seletores

Seletores aparecem na API Objective-C como o tipo de especial "SEL". Quando um seletor de associação, você deve mapear o tipo para `ObjCRuntime.Selector`.  Normalmente os seletores são expostos em uma API com um objeto, o objeto de destino e um seletor para invocar o objeto de destino. Fornecer ambos basicamente corresponde ao representante c#: algo que encapsula o método a ser invocado como o objeto para chamar o método em.

Esta é a aparência de associação:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

E isso é como o método costumam ser usado em um aplicativo:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

Para fazer a associação melhor os desenvolvedores do c#, você normalmente fornecem um método que utiliza um `NSAction` parâmetro, que permite que os delegados do c# e lambdas a ser usado em vez do `Target+Selector`. Para fazer isso você normalmente deve ocultar o método "SetTarget" sinalizando-lo com um atributo "Interno" e, em seguida, você poderia expor um método auxiliar novo, como este:

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

Agora o código do usuário pode ser escrito como este:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

 <a name="Strings" />

### <a name="strings"></a>Cadeias de caracteres

Quando você está associando um método que utiliza um `NSString`, você pode substituir que com um tipo de cadeia de caracteres c#, ambos em tipos de retorno e parâmetros.

O único caso, quando você talvez queira usar um `NSString` é diretamente quando a cadeia de caracteres é usada como um token. Para obter mais informações sobre cadeias de caracteres e `NSString`, leia o [Design de API em NSString](~/ios/internals/api-design/nsstring.md) documento.

Em alguns casos raros, uma API pode expor uma cadeia de caracteres de tipo C (`char *`) em vez de uma cadeia de caracteres Objective-C (`NSString *`). Nesses casos, você pode anotar o parâmetro com o [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring) atributo.

 <a name="outref_parameters" />

### <a name="outref-parameters"></a>out / parâmetros ref

Algumas APIs de valores de retorno em seus parâmetros ou passam parâmetros por referência.

Normalmente a assinatura tem esta aparência:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

O primeiro exemplo mostra um idioma Objective-C comuns para retornar códigos de erro, um ponteiro para um `NSError` ponteiro é passado e após retornar o valor está definido.   O segundo método mostra como um método Objective-C pode levar a um objeto e modificar o conteúdo.   Isso seria uma passagem por referência, em vez de um valor de saída puro.

A associação deve ter esta aparência:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

 <a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Atributos de gerenciamento de memória

Quando você usa o `[Export]` atributo e você estiver passando dados que serão mantidos pelo método chamado, você pode especificar a semântica de argumento passando-o como um segundo parâmetro, por exemplo:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

As opções acima seriam sinalizar o valor como tendo a semântica de "Reter". A semântica disponível é:

-  Atribua:
-  Copie:
-  Manter:

 <a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Diretrizes de estilo

 <a name="Using_[Internal]" />

#### <a name="using-internal"></a>Usando [interno]

Você pode usar o [[interno]](~/cross-platform/macios/binding/binding-types-reference.md) atributo para ocultar um método da API pública. Você talvez queira fazer isso nos casos em que a API exposta é muito baixo nível e você deseja fornecer uma implementação de alto nível em um arquivo separado com base nesse método.

Você também pode usar essa opção quando você se deparar com limitações no gerador de associação, por exemplo alguns cenários avançados podem expor os tipos que não são associados e você deseja associar sua própria maneira e você deseja incluir esses tipos de sua própria maneira.

 <a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Manipuladores de eventos e retornos de chamada

Normalmente, as classes de Objective-C difusão notificações ou solicitam informações enviando uma mensagem em uma classe de representante (delegate Objective-C).

Nesse modelo, enquanto totalmente suportados e exibidos pelo xamarin pode às vezes ser trabalhoso. Xamarin expõe o padrão de evento do c# e um sistema de retorno de chamada de método na classe que pode ser usado nessas situações. Isso permite que o código como este para executar:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

O gerador de associação é capaz de reduzir a quantidade de digitação necessária para mapear o padrão de Objective-C no padrão de c#.

Começando com o xamarin 1.4 será possível também instruir o gerador de produzir associações para um delegados Objective-C específico e expor o delegado como c# eventos e propriedades no tipo de host.

Há duas classes envolvidas nesse processo, a classe de host que é o que emite eventos atualmente e envia-os para o `Delegate` ou `WeakDelegate` e a classe delegate real.

Considerar a seguinte configuração:

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

Para encapsular a classe, você deve:

-  Em sua classe de host, adicione à sua `[BaseType]` declaração de tipo que está atuando como o nome do seu representante e c# exposto. Em nosso exemplo acima esses são "typeof (MyClassDelegate)" e "WeakDelegate" respectivamente.
-  Na sua classe delegate, em cada método que tem mais de dois parâmetros, você precisa especificar o tipo que você deseja usar para a classe EventArgs gerada automaticamente.

O gerador de associação não está limitado a apenas um destino de evento único de disposição, é possível que algumas classes Objective-C para emitir mensagens para mais de um delegado, portanto, você terá que fornecer matrizes para dar suporte a esta instalação. A maioria das configurações não será necessário, mas o gerador estiver pronto para dar suporte a esses casos.

O código resultante será:

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

O `EventArgs` é usado para especificar o nome do `EventArgs` classe a ser gerado. Você deve usar um por assinatura (neste exemplo, o `EventArgs` conterá uma propriedade "Com" do tipo nint).

Com as definições acima, o gerador de produzirá o seguinte evento no MyClass gerado:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Até agora você pode usar o código como este:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Retornos de chamada são como invocações de eventos, a diferença é que, em vez de ter vários assinantes possíveis (por exemplo, vários métodos podem conectar-se em um evento de "Clicado" ou um "Download concluído") retornos de chamada podem ter apenas um único assinante.

O processo é idêntico, a única diferença é que, em vez de expor o nome da classe EventArgs que será gerada, EventArgs, na verdade, é usado para nomear o nome de representante c# resultante.

Se o método na classe delegate retorna um valor, o gerador de associação serão mapeados isso em um método delegado na classe pai, em vez de um evento. Nesses casos, você precisa fornecer o valor padrão que deve ser retornado pelo método se o usuário não ligar para o representante. Fazer isso usando o `[DefaultValue]` ou `[DefaultValueFromArgument]` atributos.

DefaultValue será codificar um valor de retorno, enquanto `[DefaultValueFromArgument]` é usado para especificar o argumento de entrada será retornado.

 <a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Tipos base e enumerações

Você também pode referenciar tipos base que não são diretamente suportados pelo sistema de definição de interface btouch ou enumerações. Para fazer isso, coloque os enumerações e os tipos de núcleos em um arquivo separado e incluir como parte de um dos arquivos extras que você fornecer para btouch.

 <a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>As dependências de vinculação

Se você estiver associando APIs que não fazem parte do seu aplicativo, você precisa certificar-se de que o executável é vinculado em relação a essas bibliotecas.

Você precisará informar ao xamarin como vincular suas bibliotecas, isso pode ser feito alterando a configuração de compilação para invocar o comando mtouch com alguns argumentos de compilação extra que especificam como criar links com as novas bibliotecas usando o "-gcc_flags" opção, seguida por uma cadeia de caracteres entre aspas que contém todas as bibliotecas adicionais que são necessárias para seu programa, como este:

```csharp
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

O exemplo acima vinculará `libMyLibrary.a`, `libSystemLibrary.dylib` e `CFNetwork` biblioteca framework em seu executável final.

Ou você pode tirar proveito de nível de assembly `LinkWithAttribute`, que você pode incorporar em seus arquivos de contrato (como `AssemblyInfo.cs`). Quando você usa o `LinkWithAttribute`, você precisará ter a biblioteca nativa disponível no momento de fazer a associação, pois isso incorporará a biblioteca nativa com o seu aplicativo. Por exemplo:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Você deve estar se perguntando por que você precisa de comando "force_load", e o motivo é que o ObjC - sinalizador embora ele compila o código, não preserva os metadados necessários para dar suporte a categorias (a eliminação de código inativo do compilador/vinculador separa-), que é necessário em tempo de execução xamarin.

 <a name="Assisted_References" />

## <a name="assisted-references"></a>Referências assistidas

Alguns objetos transitórios como folhas de ação e caixas de alerta são difíceis de manter o controle de para desenvolvedores e o gerador de associação pode ajudar um pouco aqui.

Por exemplo, se você tivesse uma classe que é mostrada uma mensagem e, em seguida, gerou um evento de "Concluído", o modo tradicional de lidar com isso seria:

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

No cenário anterior o desenvolvedor precisa manter a referência ao objeto ele mesmo e o vazamento ou desmarque ativamente a referência para a caixa no seu próprio.  Enquanto dá suporte a código de associação, o gerador de controlar a referência para você e claro, quando um método é chamado, o código acima, em seguida, se torna:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Observe como não é mais necessário para manter a variável em uma instância, que funciona com uma variável local e que não é necessário eliminar a referência ao falhar, o objeto.

Para aproveitar isso, sua classe deve ter uma propriedade de eventos definida `[BaseType]` declaração e também o `KeepUntilRef` variável definida para o nome do método que é invocado quando o objeto concluiu seu trabalho, como este:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

 <a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Protocolos de herança

A partir da versão 3.2 xamarin, damos suporte a herança de protocolos que foram marcados com o `[Model]` propriedade. Isso é útil em determinados padrões de API, tal como `MapKit` onde o `MKOverlay` de protocolo, herda o `MKAnnotation` de protocolo e é adotada por um número de classes que herdam de `NSObject`.

Historicamente, exigimos copiando o protocolo para cada implementação, mas nesses casos agora podemos ter o `MKShape` herdam a classe a `MKOverlay` protocolo e ele irá gerar todos os métodos necessários automaticamente.

## <a name="related-links"></a>Links relacionados

- [Exemplo de associação](https://developer.xamarin.com/samples/BindingSample/)
