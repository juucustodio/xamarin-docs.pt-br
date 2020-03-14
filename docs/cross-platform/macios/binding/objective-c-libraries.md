---
title: Associando bibliotecas de Objective-C
description: Este documento fornece uma visão geral de alto nível de como criar C# associações para código Objective-C, descrevendo como associar eventos, métodos, controles personalizados e muito mais.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 6841e94ad13357c51e6ccf59e35c659dfb9954aa
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304621"
---
# <a name="binding-objective-c-libraries"></a>Associando bibliotecas de Objective-C

Ao trabalhar com o Xamarin. iOS ou Xamarin. Mac, você pode encontrar casos em que deseja consumir uma biblioteca de Objective-C de terceiros. Nessas situações, você pode usar projetos de associação do Xamarin para C# criar uma associação com as bibliotecas Objective-C nativas. O projeto usa as mesmas ferramentas que usamos para reunir as APIs de iOS e Mac C#.

Este documento descreve como associar APIs de Objective-C, se você estiver ligando apenas a APIs C, você deve usar o mecanismo .NET padrão para isso, [a estrutura P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Detalhes sobre como vincular estaticamente uma biblioteca C estão disponíveis na página de [bibliotecas nativas de vinculação](~/ios/platform/native-interop.md) .

Consulte nosso [Guia de referência de tipos de ligações](~/cross-platform/macios/binding/binding-types-reference.md)complementares.
Além disso, se você quiser saber mais sobre o que acontece nos bastidores, consulte nossa página de [visão geral de associação](~/cross-platform/macios/binding/overview.md) .

Associações podem ser criadas para bibliotecas iOS e Mac.
Esta página descreve como trabalhar em uma associação do iOS, no entanto, as ligações Mac são muito semelhantes.

**Código de exemplo para iOS**

Você pode usar o projeto de [exemplo de associação do IOS](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) para fazer experiências com associações.

<a name="Getting_Started" />

## <a name="getting-started"></a>Introdução

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

A maneira mais fácil de criar uma associação é criar um projeto de associação do Xamarin. iOS.
Você pode fazer isso em Visual Studio para Mac selecionando o tipo de projeto, biblioteca do **iOS > > biblioteca de associações**:

[![](objective-c-libraries-images/00-sml.png "Do this from Visual Studio for Mac by selecting the project type, iOS Library Bindings Library")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

A maneira mais fácil de criar uma associação é criar um projeto de associação do Xamarin. iOS.
Você pode fazer isso no Visual Studio no Windows selecionando o tipo de projeto, o **visual C# > Ios > biblioteca de associações (Ios)** :

[![](objective-c-libraries-images/00vs-sml.png "iOS Bindings Library iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Observação: os projetos de associação para **Xamarin. Mac** só têm suporte no Visual Studio para Mac.

-----

O projeto gerado contém um modelo pequeno que você pode editar, ele contém dois arquivos: `ApiDefinition.cs` e `StructsAndEnums.cs`.

O `ApiDefinition.cs` é onde você definirá o contrato de API, esse é o arquivo que descreve como a API Objective-C subjacente é projetada C#no. A sintaxe e o conteúdo desse arquivo são o principal tópico da discussão deste documento e o conteúdo dele é limitado a C# interfaces e C# declarações de delegado. O arquivo de `StructsAndEnums.cs` é o arquivo em que você inserirá as definições exigidas pelas interfaces e pelos delegados. Isso inclui valores de enumeração e estruturas que seu código pode usar.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Ligando uma API

Para fazer uma ligação abrangente, você desejará entender a definição da API Objective-C e se familiarizar com as diretrizes de design de .NET Framework.

Para associar sua biblioteca, você normalmente começará com um arquivo de definição de API. Um arquivo de definição de API é C# meramente um arquivo de C# origem que contém interfaces que foram anotadas com alguns atributos que ajudam a orientar a associação.  Esse arquivo é o que define o que o C# contrato entre e o Objective-C é.

Por exemplo, esse é um arquivo de API trivial para uma biblioteca:

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

O exemplo acima define uma classe chamada `Cocos2D.Camera` que deriva do tipo base `NSObject` (esse tipo vem de `Foundation.NSObject`) e que define uma propriedade estática (`ZEye`), dois métodos que não usam argumentos e um método que usa três argumentos.

Uma discussão detalhada do formato do arquivo de API e dos atributos que você pode usar é abordada na seção arquivo de [definição de API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) abaixo.

Para produzir uma associação completa, você normalmente lida com quatro componentes:

- O arquivo de definição de API (`ApiDefinition.cs` no modelo).
- Opcional: quaisquer enums, tipos, structs exigidos pelo arquivo de definição de API (`StructsAndEnums.cs` no modelo).
- Opcional: fontes extras que podem expandir a associação gerada ou fornecer uma API mais C# amigável (todos C# os arquivos que você adicionar ao projeto).
- A biblioteca nativa que você está associando.

Este gráfico mostra a relação entre os arquivos:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "This chart shows the relationship between the files")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

O arquivo de definição de API só conterá namespaces e definições de interface (com todos os membros que uma interface pode conter) e não deve conter classes, enumerações, delegados ou structs. O arquivo de definição de API é meramente o contrato que será usado para gerar a API.

Qualquer código extra necessário, como enumerações ou classes de suporte, deve ser hospedado em um arquivo separado, no exemplo acima, "Cameramode" é um valor de enumeração que não existe no arquivo CS e deve ser hospedado em um arquivo separado, por exemplo `StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

O arquivo de `APIDefinition.cs` é combinado com a classe `StructsAndEnum` e são usados para gerar a associação de núcleo da biblioteca. Você pode usar a biblioteca resultante no estado em que se encontra, mas, normalmente, você desejará ajustar a biblioteca C# resultante para adicionar alguns recursos para o benefício dos seus usuários. Alguns exemplos incluem a implementação de um método `ToString()` C# , fornecer indexadores, adicionar conversões implícitas para e de alguns tipos nativos ou fornecer versões fortemente tipadas de alguns métodos. Esses aprimoramentos são armazenados em C# arquivos adicionais. Basta adicionar os C# arquivos ao seu projeto e eles serão incluídos nesse processo de compilação.

Isso mostra como você implementaria o código em seu arquivo de `Extra.cs`. Observe que você usará classes parciais, pois elas aumentam as classes parciais que são geradas a partir da combinação das `ApiDefinition.cs` e da Associação de `StructsAndEnums.cs` Core:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Criar a biblioteca produzirá sua associação nativa.

Para concluir essa associação, você deve adicionar a biblioteca nativa ao projeto.  Você pode fazer isso adicionando a biblioteca nativa ao seu projeto, arrastando e soltando a biblioteca nativa do localizador para o projeto no Gerenciador de soluções ou clicando com o botão direito do mouse no projeto e escolhendo **adicionar** > **Adicionar arquivos** para selecionar a biblioteca nativa.
Bibliotecas nativas por convenção começam com a palavra "lib" e terminam com a extensão ". a". Quando você fizer isso, Visual Studio para Mac adicionará dois arquivos: o arquivo. a e um arquivo preenchido C# automaticamente, que contém informações sobre o que a biblioteca nativa contém:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Native libraries by convention start with the word lib and end with the extension .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

O conteúdo do arquivo de `libMagicChord.linkwith.cs` tem informações sobre como essa biblioteca pode ser usada e instrui o IDE a empacotar esse binário no arquivo DLL resultante:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Detalhes completos sobre como usar o [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
o atributo é documentado no [Guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md).

Agora, ao compilar o projeto, você acabará com um arquivo `MagicChords.dll` que contém a associação e a biblioteca nativa. Você pode distribuir este projeto ou a DLL resultante para outros desenvolvedores para seu próprio uso.

Às vezes, você pode achar que precisa de alguns valores de enumeração, delegar definições ou outros tipos. Não coloque-os no arquivo de definições de API, pois isso é meramente um contrato

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>O arquivo de definição de API

O arquivo de definição de API consiste em várias interfaces. As interfaces na definição de API serão transformadas em uma declaração de classe e deverão ser decoradas com o atributo [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) para especificar a classe base para a classe.

Você deve estar se perguntando por que não usamos classes em vez de interfaces para a definição do contrato. Escolhemos interfaces porque nos permitimos escrever o contrato para um método sem precisar fornecer um corpo de método no arquivo de definição de API ou ter que fornecer um corpo que tinha que lançar uma exceção ou retornar um valor significativo.

Mas como estamos usando a interface como um esqueleto para gerar uma classe, tivemos que recorrer à decoração de várias partes do contrato com atributos para direcionar a ligação.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Métodos de associação

A ligação mais simples que você pode fazer é associar um método. Basta declarar um método na interface com as convenções C# de nomenclatura e decorar o método com o [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
Attribute. O atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) é o que vincula C# seu nome com o nome Objective-C no tempo de execução do Xamarin. Ios. O parâmetro do [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
atributo é o nome do seletor de Objective-C. Alguns exemplos:

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

Os exemplos acima mostram como você pode associar métodos de instância. Para associar métodos estáticos, você deve usar o atributo [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) , da seguinte maneira:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Isso é necessário porque o contrato faz parte de uma interface e as interfaces não têm noção de declarações de instância estática vs, portanto, é necessário mais uma vez para recorrer aos atributos. Se você quiser ocultar um método específico da associação, poderá decorar o método com o atributo [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) .

O comando `btouch-native` apresentará verificações para que os parâmetros de referência não sejam nulos. Se você quiser permitir valores nulos para um parâmetro específico, use o [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
atributo no parâmetro, desta forma:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Ao exportar um tipo de referência, com a palavra-chave [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) , você também pode especificar a semântica de alocação. Isso é necessário para garantir que nenhum dado seja vazado.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propriedades de associação

Assim como os métodos, as propriedades Objective-C são associadas usando o [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
atributo e mapeie diretamente para C# Propriedades. Assim como os métodos, as propriedades podem ser decoradas com o [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
e o [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
atributos.

Quando você usa o atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) em uma propriedade em capas btouch – Native realmente liga dois métodos: o getter e o setter. O nome que você fornece para exportar é o **basename** e o setter é calculado por meio da palavra "set", transformando a primeira letra do **basename** em maiúsculas e fazendo com que o seletor use um argumento. Isso significa que `[Export ("label")]` aplicado em uma propriedade, na verdade, associa os métodos "Label" e "setlabel:" Objective-C.

Às vezes, as propriedades Objective-C não seguem o padrão descrito acima e o nome é substituído manualmente. Nesses casos, você pode controlar a forma como a associação é gerada usando o [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
atributo no getter ou setter, por exemplo:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Isso então associa "isMenuVisible" e "setMenuVisible:". Opcionalmente, uma propriedade pode ser associada usando a seguinte sintaxe:

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

Onde getter e setter são explicitamente definidos como nas associações `name` e `setName` acima.

Além de oferecer suporte a propriedades estáticas usando [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), você pode decorar as propriedades de thread estático com [`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), por exemplo:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Assim como os métodos permitem que alguns parâmetros sejam sinalizados com [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), você pode aplicar [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
para uma propriedade para indicar que NULL é um valor válido para a propriedade, por exemplo:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

O parâmetro [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) também pode ser especificado diretamente no setter:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Advertências da Associação de controles personalizados

As seguintes advertências devem ser consideradas ao configurar a associação para um controle personalizado:

1. **As propriedades de associação devem ser estáticas** -ao definir a associação de propriedades, o atributo [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) deve ser usado.
2. Os **nomes de propriedade devem corresponder exatamente** -o nome usado para associar a propriedade deve corresponder exatamente ao nome da propriedade no controle personalizado.
3. Os **tipos de propriedade devem corresponder exatamente** -o tipo de variável usado para associar a propriedade deve corresponder exatamente ao tipo da propriedade no controle personalizado.
4. **Os pontos de interrupção e os pontos de interrupção getter/setter** colocados nos métodos getter ou setter da propriedade nunca serão atingidos.
5. **Observar retornos de chamada** -você precisará usar retornos de chamada de observação para ser notificado sobre alterações nos valores de propriedade de controles personalizados.

Falha ao observar qualquer uma das advertências listadas acima pode resultar na falha da Associação silenciosamente em tempo de execução.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Padrões mutáveis de Objective-C e propriedades

As estruturas Objective-C usam um idioma em que algumas classes são imutáveis com uma subclasse mutável. Por exemplo `NSString` é a versão imutável, enquanto `NSMutableString` é a subclasse que permite a mutação.

Nessas classes, é comum ver que a classe base imutável contém propriedades com um getter, mas sem setter. E para a versão mutável para apresentar o setter. Como isso não é realmente possível com C#o, tivemos que mapear esse idioma em um idioma com C#o qual funcionaria.

A maneira como isso é mapeado C# é adicionando o getter e o setter na classe base, mas sinalizando o setter com um [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
Attribute.

Em seguida, na subclasse mutável, você usa o [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
atributo na propriedade para garantir que a propriedade esteja, na verdade, substituindo o comportamento do pai.

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

A ferramenta `btouch-native` gerará automaticamente quatro construtores em sua classe, para uma determinada classe `Foo`, ele gera:

- `Foo ()`: o construtor padrão (mapeia para o Construtor "init" de Objective-C)
- `Foo (NSCoder)`: o construtor usado durante a desserialização de arquivos NIB (mapeia para o Construtor "initWithCoder:" de Objective-C).
- `Foo (IntPtr handle)`: o construtor para a criação baseada em identificador, isso é invocado pelo tempo de execução quando o tempo de execução precisa expor um objeto gerenciado de um objeto não gerenciado.
- `Foo (NSEmptyFlag)`: isso é usado por classes derivadas para evitar a inicialização dupla.

Para os construtores que você definir, eles precisam ser declarados usando a seguinte assinatura dentro da definição de interface: eles devem retornar um valor de `IntPtr` e o nome do método deve ser Construtor. Por exemplo, para associar o construtor de `initWithFrame:`, isso é o que você usaria:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolos de associação

Conforme descrito no documento de design de API, na seção [discutindo modelos e protocolos](~/ios/internals/api-design/index.md#models), o Xamarin. Ios mapeia os protocolos Objective-C em classes que foram sinalizadas com o [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
Attribute. Normalmente, isso é usado ao implementar classes delegate Objective-C.

A grande diferença entre uma classe vinculada regular e uma classe delegate é que a classe delegate pode ter um ou mais métodos opcionais.

Por exemplo, considere a classe `UIKit` `UIAccelerometerDelegate`, é assim que ela está associada no Xamarin. iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Como esse é um método opcional na definição para `UIAccelerometerDelegate` não há mais nada a fazer. Mas se houver um método necessário no protocolo, você deverá adicionar o [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
atributo para o método. Isso forçará o usuário da implementação a realmente fornecer um corpo para o método.

Em geral, os protocolos são usados em classes que respondem às mensagens. Isso normalmente é feito em Objective-C, atribuindo à propriedade "delegate" uma instância de um objeto que responde aos métodos no protocolo.

A Convenção no Xamarin. iOS é oferecer suporte ao estilo de acoplamento flexível de Objective-C, em que qualquer instância de um `NSObject` pode ser atribuída ao delegado e também expor uma versão fortemente tipada. Por esse motivo, normalmente fornecemos uma propriedade `Delegate` que é fortemente tipada e uma `WeakDelegate` que está com rigidez de tipos. Geralmente, ligamos a versão com tipo flexível com [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)e usamos o atributo [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) para fornecer a versão fortemente tipada.

Isso mostra como vinculamos a classe `UIAccelerometer`:

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

**Novo no MonoTouch 7,0**

A partir do MonoTouch 7,0, uma nova e aprimorada funcionalidade de ligação de protocolo foi incorporada.  Esse novo suporte torna mais simples usar os idiomas Objective-C para a adoção de um ou mais protocolos em uma determinada classe.

Para cada definição de protocolo `MyProtocol` em Objective-C, agora há uma interface `IMyProtocol` que lista todos os métodos necessários do protocolo, bem como uma classe de extensão que fornece todos os métodos opcionais.  A seguir, combinada com o novo suporte no editor de Xamarin Studio permite que os desenvolvedores implementem métodos de protocolo sem precisar usar as subclasses separadas das classes de modelo abstrata anteriores.

Qualquer definição que contenha o atributo [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) irá gerar, na verdade, três classes de suporte que melhoram a maneira como você consome os protocolos:

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

A **implementação de classe** fornece uma classe abstrata completa que você pode substituir métodos individuais do e obter segurança de tipo completo.  Mas, devido C# à não compatibilidade com várias heranças, há situações em que talvez seja necessário ter uma classe base diferente, mas você ainda deseja implementar uma interface, ou seja, onde o

A **definição de interface** gerada entra em inglês.  É uma interface que tem todos os métodos necessários do protocolo.  Isso permite aos desenvolvedores que desejam implementar seu protocolo para meramente implementar a interface.  O tempo de execução registrará automaticamente o tipo como adotando o protocolo.

Observe que a interface lista apenas os métodos necessários e expõe os métodos opcionais.  Isso significa que as classes que adotam o protocolo obterão verificação de tipo completa para os métodos necessários, mas precisarão recorrer à digitação fraca (manualmente usando [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
atributos e correspondência da assinatura) para os métodos de protocolo opcionais.

Para tornar conveniente consumir uma API que usa protocolos, a ferramenta de associação também produzirá uma classe de método de extensões que expõe todos os métodos opcionais.  Isso significa que, desde que você esteja consumindo uma API, você poderá tratar os protocolos como tendo todos os métodos.

Se você quiser usar as definições de protocolo em sua API, será necessário gravar interfaces vazias de esqueleto na sua definição de API.  Se você quiser usar o MyProtocol em uma API, precisará fazer isso:

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

A parte acima é necessária porque, no momento da associação, o `IMyProtocol` não existia, é por isso que você precisa fornecer uma interface vazia.

#### <a name="adopting-protocol-generated-interfaces"></a>Adotando interfaces geradas pelo protocolo

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

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Extensões de classe de associação

Em Objective-C, é possível estender classes com novos métodos, semelhante aos C#métodos de extensão de espírito. Quando um desses métodos estiver presente, você poderá usar o [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
atributo para sinalizar o método como sendo o destinatário da mensagem Objective-C.

Por exemplo, no Xamarin. iOS, vinculamos os métodos de extensão que são definidos em `NSString` quando `UIKit` é importado como métodos na `NSStringDrawingExtensions`, desta forma:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Lista de argumentos de Objective-C de associação

Objective-C dá suporte a argumentos Variadic. Por exemplo:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Para invocar esse método C# do, você desejará criar uma assinatura como esta:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Isso declara o método como interno, ocultando a API acima dos usuários, mas expondo-o à biblioteca. Em seguida, você pode escrever um método como este:

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

Às vezes, você vai querer acessar campos públicos que foram declarados em uma biblioteca.

Normalmente, esses campos contêm cadeias de caracteres ou valores inteiros que devem ser referenciados. Normalmente, eles são usados como cadeias de caracteres que representam uma notificação específica e como chaves em dicionários.

Para associar um campo, adicione uma propriedade ao seu arquivo de definição de interface e decore a propriedade com o atributo [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) . Esse atributo usa um parâmetro: o nome do C do símbolo a ser pesquisado. Por exemplo:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Se desejar encapsular vários campos em uma classe estática que não derive de `NSObject`, você poderá usar o [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
atributo na classe, desta forma:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

O acima gerará um `LonelyClass` que não deriva de `NSObject` e conterá uma ligação com o `NSSomeEventNotification`
`NSString` exposto como um `NSString`.

O atributo [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) pode ser aplicado aos seguintes tipos de dados:

- referências de `NSString` (somente propriedades somente leitura)
- referências de `NSArray` (somente propriedades somente leitura)
- 32 bits ints (`System.Int32`)
- 64 bits ints (`System.Int64`)
- floats de 32 bits (`System.Single`)
- floats de 64 bits (`System.Double`)
- `System.Drawing.SizeF`
- `CGSize`

Além do nome do campo nativo, você pode especificar o nome da biblioteca em que o campo está localizado, passando o nome da biblioteca:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Se você estiver vinculando estaticamente, não há nenhuma biblioteca para associar, portanto, você precisa usar o nome do `__Internal`:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enums de associação

Você pode adicionar `enum` diretamente em seus arquivos de associação para facilitar seu uso dentro de definições de API – sem usar um arquivo de origem diferente (que precisa ser compilado nas associações e no projeto final).

Exemplo:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

Também é possível criar seus próprios enums para substituir `NSString` constantes. Nesse caso, o gerador criará **automaticamente** os métodos para converter valores enums e constantes NSString para você.

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

No exemplo acima, você poderia decidir decorar `void Perform (NSString mode);` com um atributo [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) . Isso **ocultará** a API baseada em constantes dos consumidores de ligação.

No entanto, isso limitará a subclasse do tipo, uma vez que a alternativa da API mais interessante usa um atributo [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) . Esses métodos gerados não são `virtual`, ou seja, você não poderá substituí-los-que podem ou não ser uma boa opção.

Uma alternativa é marcar a definição original, baseada em `NSString`, como `[Protected]`. Isso permitirá que a subclasse funcione, quando necessário, e a versão wrap'ed continuará funcionando e chamará o método substituído.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Associação `NSValue`, `NSNumber`e `NSString` a um tipo melhor

O atributo [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) permite a associação `NSNumber`, `NSValue` e `NSString`(Enums) em tipos C# mais precisos. O atributo pode ser usado para criar uma API .NET melhor e mais precisa sobre a API nativa.

Você pode decorar métodos (em valor de retorno), parâmetros e propriedades com [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). A única restrição é que o membro **não deve** estar dentro de um [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
ou [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interface.

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

Internamente, faremos a `bool?` <-> `NSNumber` e `CGRect` <-> conversões de `NSValue`.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) também oferece suporte a matrizes de `NSNumber` `NSValue` e `NSString`(Enums).

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

`CAScroll` for uma `NSString` enumeração com suporte, buscaremos o valor de `NSString` correto e lidaremos com a conversão de tipo.

Consulte a documentação do [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) para ver os tipos de conversão com suporte.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notificações de associação

As notificações são mensagens que são postadas no `NSNotificationCenter.DefaultCenter` e são usadas como um mecanismo para transmitir mensagens de uma parte do aplicativo para outra. Os desenvolvedores se inscrevem em notificações normalmente usando o método [Addobservar](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) do [NSNotificationCenter](xref:Foundation.NSNotificationCenter). Quando um aplicativo publica uma mensagem no centro de notificações, ele normalmente contém uma carga armazenada no dicionário [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) . Esse dicionário é de tipo fraco e a obtenção de informações sobre ele é propenso a erros, pois os usuários normalmente precisam ler na documentação quais chaves estão disponíveis no dicionário e os tipos de valores que podem ser armazenados no dicionário. Às vezes, a presença de chaves também é usada como um booliano.

O gerador de associação do Xamarin. iOS fornece suporte para os desenvolvedores ligarem notificações. Para fazer isso, defina o [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
o atributo em uma propriedade que também foi marcada com um [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
Propriedade (pode ser pública ou privada).

Esse atributo pode ser usado sem argumentos para notificações que não contêm carga ou você pode especificar um `System.Type` que faça referência a outra interface na definição de API, normalmente com o nome que termina com "EventArgs". O gerador transformará a interface em uma classe que as subclasses `EventArgs` e incluirá todas as propriedades listadas aqui. O atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) deve ser usado na classe EventArgs para listar o nome da chave usada para pesquisar o dicionário Objective-C para buscar o valor.

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

Os usuários do seu código podem, então, assinar facilmente as notificações postadas no [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando um código como este:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

O valor retornado de `ObserveDidStart` pode ser usado para interromper facilmente o recebimento de notificações, assim:

```csharp
token.Dispose ();
```

Ou você pode chamar [NSNotification. defaultcenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) e passar o token. Se sua notificação contiver parâmetros, você deverá especificar uma interface auxiliar `EventArgs`, como esta:

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

O acima gerará uma classe de `MyScreenChangedEventArgs` com as propriedades `ScreenX` e `ScreenY` que obterão os dados do dicionário [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) usando as chaves "ScreenXKey" e "ScreenYKey", respectivamente, e aplicará as conversões apropriadas. O atributo `[ProbePresence]` é usado para o gerador investigar se a chave é definida na `UserInfo`, em vez de tentar extrair o valor. Isso é usado para casos em que a presença da chave é o valor (normalmente para valores Boolianos).

Isso permite que você escreva um código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorias de associação

Categorias são um mecanismo Objective-C usado para estender o conjunto de métodos e propriedades disponíveis em uma classe.   Na prática, eles são usados para estender a funcionalidade de uma classe base (por exemplo `NSObject`) quando uma estrutura específica é vinculada (por exemplo, `UIKit`), disponibilizando seus métodos, mas somente se a nova estrutura estiver vinculada.   Em alguns outros casos, eles são usados para organizar recursos em uma classe por funcionalidade.   Eles são semelhantes em espírito aos C# métodos de extensão. Essa é a aparência de uma categoria no Objective-C:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

O exemplo acima, se encontrado em uma biblioteca, estenderia instâncias de `UIView` com o método `makeBackgroundRed`.

Para associá-los, você pode usar o atributo [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) em uma definição de interface.  Ao usar o [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
atributo, o significado do [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
as alterações de atributo são usadas para especificar a classe base a ser estendida, para ser o tipo a ser estendido.

O seguinte mostra como as extensões de `UIView` são ligadas e transformadas em C# métodos de extensão:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

Acima, você criará um `MyUIViewExtension` uma classe que contém o método de extensão `MakeBackgroundRed`.  Isso significa que agora você pode chamar "MakeBackgroundRed" em qualquer subclasse `UIView`, dando a você a mesma funcionalidade que obteria no Objective-C. Em alguns outros casos, as categorias são usadas para não estender uma classe de sistema, mas para organizar a funcionalidade, puramente para fins de decoração.  Dessa forma:

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Embora você possa usar o [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
atributo também para esse estilo de decoração de declarações, você pode também adicionar todas elas à definição de classe.  Ambos poderiam atingir o mesmo:

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

Isso é apenas mais curto nesses casos para mesclar as categorias:

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

Os blocos são um novo constructo introduzido pela Apple para trazer o C# equivalente funcional de métodos anônimos a Objective-C. Por exemplo, a classe `NSSet` agora expõe esse método:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

A descrição acima declara um método chamado `enumerateObjectsUsingBlock:` que usa um argumento chamado `block`. Esse bloco é semelhante a um C# método anônimo no qual ele tem suporte para capturar o ambiente atual (o ponteiro "This", o acesso a variáveis locais e parâmetros). O método acima no `NSSet` invoca o bloco com dois parâmetros um `NSObject` (a parte `id obj`) e um ponteiro para uma parte booliana (a `BOOL *stop`).

Para associar esse tipo de API a btouch, primeiro você precisa declarar a assinatura de tipo de bloco como C# um delegado e, em seguida, fazer referência a ela de um ponto de entrada de API, desta forma:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

E agora seu código pode chamar sua função de C#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

Você também pode usar lambdas se preferir:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Métodos assíncronos

O gerador de associação pode transformar uma determinada classe de métodos em métodos amigáveis para Async (métodos que retornam uma tarefa ou tarefa&lt;T&gt;).

Você pode usar o [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
atributo em métodos que retornam void e cujo último argumento é um retorno de chamada.  Quando você aplica isso a um método, o gerador de associação irá gerar uma versão desse método com o sufixo `Async`.  Se o retorno de chamada não usar parâmetros, o valor de retorno será um `Task`, se o retorno de chamada usar um parâmetro, o resultado será um `Task<T>`.  Se o retorno de chamada usar vários parâmetros, defina o `ResultType` ou `ResultTypeName` para especificar o nome desejado do tipo gerado que conterá todas as propriedades.

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

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Identificando tipos fortes para parâmetros de NSDictionary fraco

Em muitos lugares na API Objective-C, os parâmetros são passados como APIs de `NSDictionary` de tipo fraco com chaves e valores específicos, mas eles são propensos a erros (você pode passar chaves inválidas e não obter nenhum aviso; você pode passar valores inválidos e não obter nenhum aviso) e frustrante para usar, pois eles exigem várias viagens para a documentação para pesquisar os possíveis nomes

A solução é fornecer uma versão fortemente tipada que forneça a versão fortemente tipada da API e, em segundo plano, mapeia as várias chaves e valores subjacentes.

Por exemplo, se a API Objective-C aceitar um `NSDictionary` e ele estiver documentado como pegar a chave `XyzVolumeKey` que usa um `NSNumber` com um valor de volume de 0,0 a 1,0 e uma `XyzCaptionKey` que usa uma cadeia de caracteres, você desejaria que os usuários tenham uma boa API parecida com esta:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

A propriedade `Volume` é definida como float anulável, pois a Convenção em Objective-C não exige que esses dicionários tenham o valor, portanto, há cenários em que o valor pode não ser definido.

Para fazer isso, você precisa fazer algumas coisas:

- Crie uma classe fortemente tipada, que subclasses [DictionaryContainer](xref:Foundation.DictionaryContainer) e forneça os vários getters e setters para cada propriedade.
- Declare sobrecargas para os métodos que levam `NSDictionary` para obter a nova versão fortemente tipada.

Você pode criar a classe fortemente tipada manualmente ou usar o gerador para fazer o trabalho para você.  Primeiro, exploraremos como fazer isso manualmente para que você entenda o que está acontecendo e, em seguida, a abordagem automática.

Você precisa criar um arquivo de suporte para isso, ele não entra em sua API de contrato.  Isso é o que você teria de escrever para criar sua classe XyzOptions:

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

Em seguida, você deve fornecer um método wrapper que superfícies a API de alto nível, além da API de nível baixo.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Se sua API não precisar ser substituída, você poderá ocultar com segurança a API baseada em NSDictionary usando o [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
Attribute.

Como você pode ver, usamos o [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
para superfície de um novo ponto de entrada de API e nós o surfacemos usando nossa classe de `XyzOptions` fortemente tipada.  O método wrapper também permite que NULL seja passado.

Agora, uma coisa que não mencionamos é de onde vêm os valores de `XyzOptionsKeys`.  Normalmente, você agruparia as chaves que uma API superfícies em uma classe estática como `XyzOptionsKeys`, desta forma:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Vamos examinar o suporte automático para criar esses dicionários fortemente tipados.  Isso evita bastante o timbre, e você pode definir o dicionário diretamente no seu contrato de API, em vez de usar um arquivo externo.

Para criar um dicionário fortemente tipado, introduza uma interface em sua API e decore-a com o atributo [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) .  Isso informa ao gerador que ele deve criar uma classe com o mesmo nome da interface que derivará de `DictionaryContainer` e fornecerá acessadores de tipo forte para ela.

O atributo [`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) usa um parâmetro, que é o nome da classe estática que contém suas chaves de dicionário.  Em seguida, cada propriedade da interface se tornará um acessador fortemente tipado.  Por padrão, o código usará o nome da propriedade com o sufixo "Key" na classe estática para criar o acessador.

Isso significa que a criação de seu acessador fortemente tipado não requer mais um arquivo externo, nem precisa criar getters e setters manualmente para cada propriedade, nem precisar pesquisar as chaves manualmente.

Esta é a aparência de toda a sua ligação:

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

Caso você precise fazer referência em seu `XyzOption` Membros um campo diferente (que não seja o nome da propriedade com o sufixo `Key`), você pode decorar a propriedade com um [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
atributo com o nome que você deseja usar.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Mapeamentos de tipo

Esta seção aborda como os tipos Objective-C são C# mapeados para tipos.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipos simples

A tabela a seguir mostra como você deve mapear tipos de Objective-C e CocoaTouch World para o mundo do Xamarin. iOS:

|Nome do tipo Objective-C|Tipo de API Unificada do Xamarin. iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([mais sobre associação NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (consulte também: [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Tipos CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Tipos de fundação (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>Matrizes

O tempo de execução do Xamarin. iOS cuida automaticamente C# da conversão de matrizes para `NSArrays` e de fazer a conversão de volta, por exemplo, o método Objective-C imaginário que retorna uma `NSArray` de `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

É associado desta forma:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

A ideia é usar uma matriz fortemente tipada C# , pois isso permitirá que o IDE forneça a conclusão de código adequada com o tipo real sem forçar o usuário a adivinhar ou pesquisar a documentação para descobrir o tipo real dos objetos contidos na matriz.

Nos casos em que não é possível rastrear o tipo real mais derivado contido na matriz, você pode usar `NSObject []` como o valor de retorno.

<a name="Selectors" />

### <a name="selectors"></a>Seletores

Os seletores aparecem na API Objective-C como o tipo especial `SEL`. Ao associar um seletor, você mapearia o tipo para `ObjCRuntime.Selector`.  Normalmente, os seletores são expostos em uma API com um objeto, o objeto de destino e um seletor para invocar no objeto de destino. Fornecer ambos basicamente corresponde ao C# delegado: algo que encapsula o método a ser invocado, bem como o objeto para invocar o método.

Essa é a aparência da associação:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

E é assim que o método seria normalmente usado em um aplicativo:

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

Para tornar a ligação mais atraente para C# os desenvolvedores, você normalmente fornecerá um método que usa um parâmetro `NSAction`, que C# permite que os delegados e lambdas sejam usados em vez da `Target+Selector`. Para fazer isso, você normalmente ocultaria o método `SetTarget` sinalizando-o com um [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
e, em seguida, você exporia um novo método auxiliar, como este:

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

Agora, o código de usuário pode ser escrito da seguinte maneira:

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

Ao ligar um método que usa um `NSString`, você pode substituí-lo por um C# tipo de cadeia de caracteres em tipos de retorno e parâmetros.

O único caso em que você talvez queira usar um `NSString` diretamente é quando a cadeia de caracteres é usada como um token. Para obter mais informações sobre cadeias de caracteres e `NSString`, leia o [design de API no documento NSString](~/ios/internals/api-design/nsstring.md) .

Em alguns casos raros, uma API pode expor uma cadeia de caracteres C-like (`char *`) em vez de uma cadeia de caracteres Objective-C (`NSString *`). Nesses casos, você pode anotar o parâmetro com o [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
Attribute.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>parâmetros out/ref

Algumas APIs retornam valores em seus parâmetros ou passam parâmetros por referência.

Normalmente, a assinatura tem esta aparência:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

O primeiro exemplo mostra um idioma Objective-C comum para retornar códigos de erro, um ponteiro para um `NSError` ponteiro é passado e, após retornar, o valor é definido.   O segundo método mostra como um método Objective-C pode pegar um objeto e modificar seu conteúdo.   Isso seria uma passagem por referência, em vez de um valor de saída puro.

Sua associação ficaria assim:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Atributos de gerenciamento de memória

Quando você usa o atributo [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) e está passando dados que serão retidos pelo método chamado, você pode especificar a semântica do argumento passando-o como um segundo parâmetro, por exemplo:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

O anterior sinalizaria o valor como tendo a semântica "Retain". A semântica disponível é:

- Atribuir
- Cópia
- Manter

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Diretrizes de estilo

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Usando [interno]

Você pode usar o [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
atributo para ocultar um método da API pública. Talvez você queira fazer isso nos casos em que a API exposta é muito baixa e você deseja fornecer uma implementação de alto nível em um arquivo separado com base nesse método.

Você também pode usar isso quando se deparar com limitações no gerador de associação, por exemplo, alguns cenários avançados podem expor tipos que não estão associados e você deseja associar de sua própria maneira e você deseja encapsular esses tipos por conta própria.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Manipuladores de eventos e retornos de chamada

As classes Objective-C normalmente transmitem notificações ou informações de solicitação enviando uma mensagem em uma classe delegate (delegado Objective-C).

Esse modelo, embora com suporte total e na superfície do Xamarin. iOS, às vezes pode ser complicado. O Xamarin. iOS expõe C# o padrão de evento e um sistema de retorno de chamada de método na classe que pode ser usada nessas situações. Isso permite que um código como este seja executado:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

O gerador de associação é capaz de reduzir a quantidade de digitação necessária para mapear o padrão Objective C# -C para o padrão.

A partir do Xamarin. iOS 1,4, será possível também instruir o gerador a produzir associações para um delegado Objective-C específico e expor o delegado como C# eventos e propriedades no tipo de host.

Há duas classes envolvidas nesse processo, a classe de host que será a que emite eventos no momento e os envia para o `Delegate` ou `WeakDelegate` e a classe delegate real.

Considerando a seguinte configuração:

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

- Na sua classe de host, adicione ao seu [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   declaração o tipo que está agindo como seu delegado e o C# nome que você expôs. Em nosso exemplo acima, eles são `typeof (MyClassDelegate)` e `WeakDelegate`, respectivamente.
- Em sua classe de representante, em cada método que tem mais de dois parâmetros, você precisa especificar o tipo que deseja usar para a classe EventArgs gerada automaticamente.

O gerador de associação não está limitado a encapsular apenas um único destino de evento, é possível que algumas classes Objective-C emitam mensagens para mais de um delegado, portanto, você precisará fornecer matrizes para dar suporte a essa configuração. A maioria das configurações não precisará dela, mas o gerador está pronto para dar suporte a esses casos.

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

O `EventArgs` é usado para especificar o nome da classe de `EventArgs` a ser gerada. Você deve usar uma por assinatura (neste exemplo, o `EventArgs` conterá uma propriedade `With` do tipo Nint).

Com as definições acima, o gerador produzirá o seguinte evento no MyClass gerado:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Agora você pode usar o código como este:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Os retornos de chamada são como invocações de evento, a diferença é que, em vez de ter vários assinantes potenciais (por exemplo, vários métodos podem se conectar a um evento `Clicked` ou a um evento de `DownloadFinished`) os retornos de chamada só podem ter um único assinante.

O processo é idêntico, a única diferença é que, em vez de expor o nome da classe de `EventArgs` que será gerada, o EventArgs realmente é usado para nomear o C# nome do delegado resultante.

Se o método na classe delegate retornar um valor, o gerador de associação irá mapeá-lo em um método delegate na classe pai em vez de em um evento. Nesses casos, você precisa fornecer o valor padrão que deve ser retornado pelo método se o usuário não se conectar ao delegado. Você faz isso usando o [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
ou [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) atributos.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) codificará um valor de retorno, enquanto [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
é usado para especificar qual argumento de entrada será retornado.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Enumerações e tipos base

Você também pode referenciar enumerações ou tipos base que não são suportados diretamente pelo sistema de definição de interface btouch. Para fazer isso, coloque as enumerações e os tipos principais em um arquivo separado e inclua isso como parte de um dos arquivos extras que você fornece ao btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Vinculando as dependências

Se você estiver associando APIs que não fazem parte do seu aplicativo, precisará certificar-se de que seu executável está vinculado a essas bibliotecas.

Você precisa informar ao Xamarin. iOS como vincular suas bibliotecas, isso pode ser feito alterando sua configuração de compilação para invocar o comando `mtouch` com alguns argumentos de Build adicionais que especificam como vincular com as novas bibliotecas usando a opção "-gcc_flags", seguida por uma cadeia de caracteres entre aspas que contém todas as bibliotecas adicionais necessárias para o seu programa, desta forma:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

O exemplo acima irá vincular `libMyLibrary.a`, `libSystemLibrary.dylib` e a biblioteca do `CFNetwork` Framework ao seu executável final.

Ou você pode aproveitar o [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)no nível do assembly, que pode ser inserido em seus arquivos de contrato (como `AssemblyInfo.cs`).
Ao usar o [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), você precisará ter sua biblioteca nativa disponível no momento em que fizer a associação, pois isso incorporará a biblioteca nativa ao seu aplicativo. Por exemplo:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Você deve estar se perguntando, por que precisa de `-force_load` comando, e o motivo é que o sinalizador-ObjC, embora ele compile o código em, ele não preserva os metadados necessários para dar suporte a categorias (o vinculador/a eliminação de código inativo retira a ti) que você precisa em tempo de execução para Xamarin. iOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Referências assistidas

Alguns objetos transitórios como folhas de ação e caixas de alerta são incômodos para acompanhar os desenvolvedores e o gerador de associação pode ajudar um pouco.

Por exemplo, se você tivesse uma classe que mostrou uma mensagem e, em seguida, gerou um evento de `Done`, a maneira tradicional de lidar com isso seria:

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

No cenário acima, o desenvolvedor precisa manter a referência ao próprio objeto e vazar ou limpar ativamente a referência do Box por conta própria.  Ao ligar o código, o gerador dá suporte ao controle da referência para você e o limpa quando um método especial é invocado, o código acima se tornaria:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Observe como não é mais necessário manter a variável em uma instância, que funciona com uma variável local e que não é necessário limpar a referência quando o objeto se tornar inativo.

Para tirar proveito disso, sua classe deve ter uma propriedade Events definida na Declaração [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) e também a variável `KeepUntilRef` definida como o nome do método que é invocado quando o objeto conclui seu trabalho, da seguinte forma:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Herdando protocolos

A partir do Xamarin. iOS v 3.2, damos suporte à herança de protocolos que foram marcados com a propriedade [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) . Isso é útil em determinados padrões de API, como em `MapKit` em que o protocolo `MKOverlay`, herda do protocolo `MKAnnotation` e é adotado por um número de classes que herdam de `NSObject`.

Historicamente, precisávamos copiar o protocolo para cada implementação, mas nesses casos agora podemos ter a classe de `MKShape` herdada do protocolo `MKOverlay` e ele irá gerar todos os métodos necessários automaticamente.

## <a name="related-links"></a>Links relacionados

- [Exemplo de associação](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
