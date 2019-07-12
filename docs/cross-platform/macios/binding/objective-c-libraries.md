---
title: Associação de bibliotecas de Objective-C
description: Este documento fornece uma visão geral de como criar C# associações a código Objective-C, que descreve como associar a eventos, métodos, controles personalizados e muito mais.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 206379b162c7778663ee2baf64dfeb1d33666ab4
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831459"
---
# <a name="binding-objective-c-libraries"></a>Associação de bibliotecas de Objective-C

Ao trabalhar com o xamarin. IOS ou xamarin. Mac, você poderá encontrar casos em que você deseja consumir uma biblioteca do Objective-C de terceiros. Nessas situações, você pode usar os projetos de associação do Xamarin para criar um C# de associação para as bibliotecas de Objective-C nativas. O projeto usa as mesmas ferramentas que usamos para trazer o iOS e Mac APIs para C#.

Este documento descreve como vincular as APIs do Objective-C, se você estiver associando apenas as APIs de C, você deve usar o mecanismo do .NET standard para isso, [a estrutura de P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Detalhes sobre como vincular estaticamente uma biblioteca C estão disponíveis na [vinculação a bibliotecas nativas](~/ios/platform/native-interop.md) página.

Consulte nossa complementar [guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md).
Além disso, se você quiser saber mais sobre o que acontece nos bastidores, verifique nossos [visão geral de associação](~/cross-platform/macios/binding/overview.md) página.

Associações podem ser criadas para iOS e bibliotecas de Mac.
Esta página descreve como trabalhar em um iOS de associação, no entanto, as associações de Mac são muito semelhantes.

**Exemplo de código para iOS**

Você pode usar o [exemplo de associação de iOS](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) projeto para fazer experiências com associações.

<a name="Getting_Started" />

## <a name="getting-started"></a>Introdução

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A maneira mais fácil para criar uma associação é criar um projeto de associação do xamarin. IOS.
Você pode fazer isso no Visual Studio para Mac selecionando o tipo de projeto **iOS > Biblioteca > biblioteca de associações**:

[![](objective-c-libraries-images/00-sml.png "Fazer isso no Visual Studio para Mac selecionando o tipo de projeto, a biblioteca de associações de biblioteca do iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A maneira mais fácil para criar uma associação é criar um projeto de associação do xamarin. IOS.
Você pode fazer isso do Visual Studio no Windows, selecionando o tipo de projeto **Visual C# > iOS > biblioteca de associações (iOS)** :

[![](objective-c-libraries-images/00vs-sml.png "Biblioteca de associações de iOS do iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Observação: Projetos para de associação **xamarin. Mac** só tem suporte no Visual Studio para Mac.

-----

O projeto gerado contém um modelo pequeno que pode ser editado, ele contém dois arquivos: `ApiDefinition.cs` e `StructsAndEnums.cs`.

O `ApiDefinition.cs` é onde você definirá o contrato de API, esse é o arquivo que descreve como a API básica do Objective-C é projetada em C#. A sintaxe e o conteúdo desse arquivo é o tópico principal de discussão deste documento e o conteúdo dele é limitado a C# interfaces e C# declarações de delegado. O `StructsAndEnums.cs` é o arquivo onde você vai inserir quaisquer definições que são necessárias, as interfaces e delegados. Isso inclui os valores de enumeração e estruturas que seu código pode usar.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Uma API de associação

Para fazer uma associação abrangente, você desejará compreender a definição de API do Objective-C e se familiarizar com as diretrizes de Design do .NET Framework.

Para associar sua biblioteca que você normalmente começa com um arquivo de definição de API. Um arquivo de definição de API é simplesmente um C# arquivo de origem que contém C# interfaces que foram anotados com alguns atributos que ajudam a orientar a associação.  Esse arquivo é o que define o que o contrato entre C# e é de Objective-C.

Por exemplo, isso é um arquivo simples de api para uma biblioteca:

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

O exemplo acima define uma classe chamada `Cocos2D.Camera` que deriva de `NSObject` tipo de base (esse tipo é proveniente `Foundation.NSObject`) e que define uma propriedade estática (`ZEye`), dois métodos que usam nenhum argumento e um método que usa três argumentos.

Uma discussão detalhada sobre o formato do arquivo API e os atributos que você pode usar é abordada a [arquivo de definição de API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) seção abaixo.

Para produzir uma vinculação completa, normalmente você lidará com a quatro componentes:

-  O arquivo de definição de API (`ApiDefinition.cs` no modelo).
-  Opcional: qualquer enums, tipos, estruturas necessárias pelo arquivo de definição de API (`StructsAndEnums.cs` no modelo).
-  Opcional: fontes de extras que podem expandir a associação gerada ou fornecer uma mais C# API amigável (qualquer C# arquivos que você adicionar ao projeto).
-  A biblioteca nativa que você está associando.

Este gráfico mostra a relação entre os arquivos:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Este gráfico mostra a relação entre os arquivos")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

O arquivo de definição de API conterá apenas as definições de interface e de namespaces (com os membros que uma interface pode conter) e não deve conter classes, enumerações, delegados ou structs. O arquivo de definição de API é meramente o contrato que será usado para gerar a API.

Nenhum código extra que você precisa, como enumerações ou classes de suporte devem ser hospedada em um arquivo separado, no exemplo acima, "CameraMode" é um valor de enumeração que não existe no arquivo CS e deve ser hospedado em um arquivo separado, por exemplo `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

O `APIDefinition.cs` arquivo é combinado com o `StructsAndEnum` de classe e são usados para gerar a associação de núcleo da biblioteca. Você pode usar a biblioteca resultante como-está, mas normalmente, você desejará ajustar a biblioteca resultante para adicionar alguns C# recursos para o benefício de seus usuários. Alguns exemplos incluem a implementação de um `ToString()` método, fornecer C# indexadores, adicione as conversões implícitas de e para alguns tipos nativos ou fornecer versões fortemente tipada de alguns métodos. Esses aprimoramentos são armazenados no extra C# arquivos. Simplesmente adicionar o C# arquivos ao seu projeto e eles serão incluídos neste processo de compilação.

Isso mostra como você implementaria o código no seu `Extra.cs` arquivo. Observe que você usará as classes parciais conforme eles aumentam as classes parciais que são geradas a partir da combinação da `ApiDefinition.cs` e o `StructsAndEnums.cs` núcleo de associação:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Compilar a biblioteca produzirá a associação nativa.

Para concluir essa associação, você deve adicionar a biblioteca nativa ao projeto.  Você pode fazer isso adicionando a biblioteca nativa ao seu projeto, arrastando e soltando a biblioteca nativa do localizador para o projeto no Gerenciador de soluções ou clicando com botão direito no projeto e escolhendo **Add**  >  **Adicionar arquivos** para selecionar a biblioteca nativa.
Bibliotecas nativas por convenção, iniciam com a palavra "lib" e terminam com a extensão "1).a". Quando você fizer isso, o Visual Studio para Mac adicionará dois arquivos: o arquivo 1).a e automaticamente preenchida C# arquivo que contém informações sobre o que contém a biblioteca nativa:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Bibliotecas nativas por convenção, começam com a biblioteca do word e terminam com a extensão 1).a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

O conteúdo do `libMagicChord.linkwith.cs` arquivo tem informações sobre como essa biblioteca pode ser usada e instrui seu IDE para este binário do pacote no arquivo DLL resultante:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Detalhes sobre como usar completos a [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
atributo estão documentados a [guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md).

Agora, quando você compila o projeto você acabará com uma `MagicChords.dll` arquivo que contém a associação e a biblioteca nativa. Você pode distribuir esse projeto ou usar a DLL resultante para outros desenvolvedores para seus próprios.

Às vezes, você pode achar que precisa alguns valores de enumeração, definições de delegado ou outros tipos. Não coloque-os no arquivo de definições de API, pois esse é simplesmente um contrato

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>O arquivo de definição de API

O arquivo de definição de API consiste em um número de interfaces. As interfaces na definição de API serão transformadas em uma declaração de classe e eles devem ser decorados com o [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) atributo para especificar a classe base para a classe.

Você deve estar se perguntando por que não usamos classes em vez de interfaces para a definição de contrato. Escolhemos interfaces porque ele nos permitiu gravar o contrato para um método sem ter que fornecer um corpo de método no arquivo de definição de API, ou ter que fornecer um corpo que era necessário gerar uma exceção ou retorna um valor significativo.

Mas, como estamos usando a interface como um esqueleto para gerar uma classe que tivemos de recorrer a decoração de várias partes do contrato com os atributos para orientar a associação.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Métodos de associação

É a associação mais simples, que você pode fazer associar um método. Basta declarar um método na interface com o C# convenções de nomenclatura e decorar o método com o [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
atributo. O [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo é o que vincula seu C# nome com o nome de Objective-C em tempo de execução do xamarin. IOS. O parâmetro das [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
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

Os exemplos acima mostram como você pode vincular métodos de instância. Para associar a métodos estáticos, você deve usar o [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) atributo, como este:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Isso é necessário porque o contrato é parte de uma interface e interfaces não ter nenhuma noção de declarações de instância estática do vs, portanto, é necessário mais uma vez que recorrer aos atributos. Se você quiser ocultar um método específico de associação, você pode decorar o método com o [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo.

O `btouch-native` comando apresentará verificações para parâmetros de referência não seja nulo. Se você quiser permitir valores nulos para um parâmetro específico, use o [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
atributo de parâmetro, como este:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Ao exportar um tipo de referência, com o [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) palavra-chave, você também pode especificar a semântica de alocação. Isso é necessário para garantir que nenhum dado é perdido.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propriedades de associação

Assim como métodos, propriedades de Objective-C são associadas usando o [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
atributo e mapeadas diretamente para C# propriedades. Assim como métodos, propriedades poderão ser decoradas com o [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
e o [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
Atributos.

Quando você usa o [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo em uma propriedade de bastidores btouch-nativo vincula, na verdade, dois métodos: o getter e setter. O nome que você fornecer exportar é o **basename** e o setter é computado acrescentando a palavra "set", a primeira letra de a ativação a **basename** em letras maiusculas e fazer com que o seletor de levar um argumento. Isso significa que `[Export ("label")]` aplicado em uma propriedade, na verdade, associa o rótulo"" e "setLabel:" Métodos de Objective-C.

Às vezes, as propriedades de Objective-C não seguem o padrão descrito acima e o nome será substituído manualmente. Nesses casos, você pode controlar a maneira que a associação é gerada usando o [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
atributo no getter ou setter, por exemplo:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Associação "isMenuVisible" e "setMenuVisible:". Opcionalmente, uma propriedade pode ser associada usando a seguinte sintaxe:

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

Em que o getter e setter são explicitamente definidos conforme a `name` e `setName` associações acima.

Além do suporte para propriedades estáticas usando [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), você pode decorar propriedades de thread estático com [ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), por exemplo:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Assim como métodos permitem que seja sinalizado com alguns parâmetros [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), você pode aplicar [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
para uma propriedade para indicar que null é um valor válido para a propriedade, por exemplo:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

O [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) parâmetro também pode ser especificado diretamente no setter:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Advertências de associar controles personalizados

As seguintes condições devem ser consideradas ao configurar a associação para um controle personalizado:

1. **Propriedades de associação devem ser estáticas** - ao definir a associação de propriedades, o [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) atributo deve ser usado.
2. **Nomes de propriedade devem corresponder exatamente** -o nome usado para associar a propriedade deve corresponder exatamente ao nome da propriedade no controle personalizado.
3. **Tipos de propriedade devem corresponder exatamente** -o tipo de variável usado para associar a propriedade deve corresponder exatamente o tipo da propriedade no controle personalizado.
4. **Pontos de interrupção e o getter/setter** – pontos de interrupção colocado no getter ou métodos setter da propriedade nunca serão atingidos.
5. **Observar os retornos de chamada** -você precisará usar retornos de chamada de Observação para ser notificado de alterações nos valores de propriedade de controles personalizados.

Falha para observar qualquer uma das condições listadas acima pode resultar na associação silenciosamente falhar em tempo de execução.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>As propriedades e o padrão de mutável de objective-C

Estruturas de Objective-C usam um idioma em que algumas classes são imutáveis por uma subclasse mutável. Por exemplo `NSString` é a versão imutável, enquanto `NSMutableString` é a subclasse que permite a mutação.

Nessas classes, é comum ver a classe base imutável contêm propriedades com um getter, mas nenhum setter. E para a versão mutável introduzir o setter. Uma vez que isso não é realmente possível com C#, tivemos que mapear essa linguagem em um idioma que funcionaria com C#.

A maneira como isso é mapeado para C# está adicionando o getter e setter na classe base, mas sinalizando o setter com um [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
atributo.

Em seguida, em que a subclasse mutável, use o [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
atributo na propriedade para garantir que a propriedade, na verdade, está substituindo o comportamento do pai.

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

O `btouch-native` ferramenta irá gerar automaticamente construtores de quatros em sua classe, para uma determinada classe `Foo`, ele gera:

-  `Foo ()`: o construtor padrão (mapeado para o construtor de "inicialização" do Objective-C)
-  `Foo (NSCoder)`: o construtor usado durante a desserialização de arquivos NIB (mapeia para Objective-C "initWithCoder:" construtor).
-  `Foo (IntPtr handle)`: o construtor para a criação com base no identificador, invocado pelo tempo de execução quando o tempo de execução precisa expor um objeto gerenciado de um objeto não gerenciado.
-  `Foo (NSEmptyFlag)`: isso é usado por classes derivadas para impedir a inicialização dupla.

Para construtores de que você define, eles precisam ser declarados usando a assinatura a seguir dentro da definição da Interface: eles devem retornar um `IntPtr` valor e o nome do método devem ser o construtor. Por exemplo, para associar o `initWithFrame:` construtor, isso é o que você usaria:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocolos de associação

Conforme descrito no documento de design de API, na seção [discutindo modelos e protocolos](~/ios/internals/api-design/index.md#Models), xamarin. IOS mapeia os protocolos de Objective-C em classes que foram sinalizadas com o [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
atributo. Isso normalmente é usado ao implementar classes de representante de Objective-C.

A grande diferença entre uma classe de associação regular e uma classe de delegado é que a classe delegate pode ter um ou mais métodos opcionais.

Por exemplo considere a `UIKit` classe `UIAccelerometerDelegate`, isso é como ele está vinculado no xamarin. IOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Como esse é um método opcional na definição para `UIAccelerometerDelegate` há mais nada a fazer. Mas se houver um método exigido no protocolo, você deve adicionar o [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
o atributo ao método. Isso forçará o usuário da implementação, na verdade, forneça um corpo para o método.

Em geral, os protocolos são usados nas classes que respondem às mensagens. Normalmente, isso é feito em Objective-C por meio da atribuição à propriedade "delegate" uma instância de um objeto que responde aos métodos no protocolo.

A convenção no xamarin. IOS deve dar suporte a ambos os o Objective-C fracamente acoplados estilo em que qualquer instância de um `NSObject` pode ser atribuído ao delegado e também expõem uma versão fortemente tipada dele. Por esse motivo, podemos fornecer os dois normalmente um `Delegate` propriedade fortemente tipado e uma `WeakDelegate` que é vagamente tipada. Geralmente ligamos a versão com tipagem [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), e podemos usar os [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) atributo para fornecer a versão fortemente tipada.

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

Começando com o MonoTouch 7.0 um funcionalidade de ligação de protocolo novo e aprimorado incorporado.  Esse novo suporte torna mais simples de usar expressões de Objective-C para a adoção de um ou mais protocolos em uma determinada classe.

Para cada definição de protocolo `MyProtocol` em Objective-C, agora há um `IMyProtocol` interface que lista todos os métodos necessários do protocolo, bem como uma classe de extensão que fornece os métodos opcionais.  Acima, combinados com novo suporte no Xamarin Studio editor permite aos desenvolvedores implementar métodos de protocolo sem precisar usar as subclasses separadas das classes de modelo abstrato anterior.

Qualquer definição que contém o [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) atributo gerará, na verdade, três classes de suporte que melhoram muito a maneira que você consuma protocolos:

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

O **implementação da classe** fornece uma classe abstrata completa que você pode substituir métodos individuais do e obter a segurança de tipo completo.  Mas, devido ao C# não oferecer suporte a várias heranças, há cenários em que você pode precisa ter uma classe base diferente, mas ainda desejar implementar uma interface, que é o local onde o

Gerado **definição de interface** chega.  É uma interface que tem todos os métodos necessários do protocolo.  Isso permite que os desenvolvedores que desejam implementar o protocolo para simplesmente implementar a interface.  O tempo de execução registrará automaticamente o tipo como adotar o protocolo.

Observe que a interface somente lista os métodos necessários e expor os métodos opcionais.  Isso significa que as classes que adotam o protocolo obterá completo verificação de tipo para os métodos necessários, mas serão preciso recorrer a digitação fraca (manualmente usando [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
atributos e a assinatura de correspondência) para os métodos de protocolo opcional.

Para torná-lo conveniente para consumir uma API que usa os protocolos, a ferramenta de associação também produzirá uma classe de método de extensões que expõe todos os métodos opcionais.  Isso significa que enquanto você estiver consumindo uma API, você será capaz de tratar protocolos como tendo todos os métodos.

Se você quiser usar as definições de protocolo em sua API, você precisará escrever interfaces vazias esqueleto em sua definição de API.  Se você quiser usar o MeuProtocolo em uma API, você precisa fazer isso:

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

#### <a name="adopting-protocol-generated-interfaces"></a>Adoção de interfaces gerado pelo protocolo

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

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Extensões de classe de associação

Em Objective-C, é possível estender as classes com métodos novos, semelhantes no espírito para C#de métodos de extensão. Quando um desses métodos está presente, você pode usar o [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
atributo para sinalizar o método como sendo o receptor da mensagem de Objective-C.

Por exemplo, no xamarin. IOS é acoplada os métodos de extensão que estão definidos no `NSString` quando `UIKit` é importada como métodos no `NSStringDrawingExtensions`, semelhante a esta:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Associação de listas de argumentos de Objective-C

Objective-C oferece suporte a argumentos variadic. Por exemplo:

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Para invocar esse método de C# você desejará criar uma assinatura como esta:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Isso declara o método como interno, ocultando a API acima dos usuários, mas expô-lo na biblioteca. Em seguida, você pode escrever um método como este:

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

Às vezes você desejará acessar campos públicos que foram declarados em uma biblioteca.

Normalmente, esses campos contêm valores de cadeias de caracteres ou inteiros que devem ser referenciados. Eles são usados como cadeia de caracteres que representam uma notificação específica e como chaves em dicionários.

Para vincular um campo, adicione uma propriedade ao seu arquivo de definição de interface e decorar a propriedade com o [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) atributo. Esse atributo usa um parâmetro: o nome de C do símbolo para pesquisa. Por exemplo:

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Se você deseja encapsular vários campos em uma classe estática que não derivam de `NSObject`, você pode usar o [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
atributo da classe, como este:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

Acima vai gerar uma `LonelyClass` que não é derivado de `NSObject` e conterá uma associação para o `NSSomeEventNotification` 
 `NSString` exposto como um `NSString`.

O [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) atributo pode ser aplicado aos seguintes tipos de dados:

-  `NSString` referências (somente propriedades somente leitura)
-  `NSArray` referências (somente propriedades somente leitura)
-  inteiros de 32 bits (`System.Int32`)
-  inteiros de 64 bits (`System.Int64`)
-  flutuações de 32 bits (`System.Single`)
-  flutuações de 64 bits (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

Além do nome de campo nativo, você pode especificar o nome da biblioteca em que o campo está localizado, passando o nome da biblioteca:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Se você estiver vinculado estaticamente, não há nenhuma biblioteca ao vincular, portanto, você precisa usar o `__Internal` nome:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Enumerações de associação

Você pode adicionar `enum` diretamente em sua associação de arquivos a serem torna mais fácil usá-los dentro de definições de API – sem usar um arquivo de origem diferente (que precisa ser compilado nas associações e o final do projeto).

Exemplo:

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

Também é possível criar seus próprios enums substituir `NSString` constantes. Nesse caso, o gerador serão **automaticamente** criar métodos para converter valores de enumerações e constantes NSString para você.

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

No exemplo acima, você poderia optar por decorar `void Perform (NSString mode);` com um [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) atributo. Isso impedirá **ocultar** a API baseada em constante de seus consumidores de associação.

No entanto isso limita o tipo da subclasse como a API mais agradável alternativa usa uma [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) atributo. Esses métodos gerados não são `virtual`, ou seja, você não vai ser capaz de substituí-las - que podem ser, ou não, ser uma boa opção.

Uma alternativa é marcar o original `NSString`-com base em definição como `[Protected]`. Isso permitirá a criação de subclasses funcione, quando necessário, e a versão wrap'ed ainda funcionarão e chame o método substituído.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Associando `NSValue`, `NSNumber`, e `NSString` para um tipo melhor

O [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) atributo permite que a associação `NSNumber`, `NSValue` e `NSString`(enums) em mais precisas C# tipos. O atributo pode ser usado para criar melhores e mais precisos, a API .NET sobre a API nativa.

Você pode decorar métodos (no valor de retorno), parâmetros e propriedades com [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). A única restrição é que o membro **não deve** estar dentro de um [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
ou [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interface.

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

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) também dá suporte a matrizes de `NSNumber` `NSValue` e `NSString`(enums).

Por exemplo:

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Seria a saída:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` é um `NSString` feito enum, podemos buscará direita `NSString` valor e lidar com a conversão de tipo.

Consulte a [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) documentação para ver os tipos de conversão com suporte.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notificações de associação

As notificações são mensagens que são postadas o `NSNotificationCenter.DefaultCenter` e são usados como um mecanismo para transmitir mensagens de uma parte do aplicativo para outro. Os desenvolvedores de assinam notificações normalmente usando o [NSNotificationCenter](xref:Foundation.NSNotificationCenter)do [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) método. Quando um aplicativo posta uma mensagem para o Centro de notificação, ele normalmente contém um conteúdo armazenado na [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) dicionário. Esse dicionário é digitado sem rigidez e obtenção de informações para fora dela está sujeita a erros, conforme os usuários geralmente precisam ler na documentação do quais teclas estão disponíveis no dicionário e os tipos dos valores que podem ser armazenados no dicionário. A presença das chaves, às vezes, é usada como um valor booliano também.

O gerador de associação do xamarin. IOS dá suporte para os desenvolvedores associar as notificações. Para fazer isso, você deve definir o [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
atributo em uma propriedade que também foi marcada com um [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
propriedade (pode ser público ou privado).

Esse atributo pode ser usado sem argumentos para notificações de que não executar nenhum conteúdo, ou você pode especificar um `System.Type` que faz referência a outra interface na definição de API, normalmente com o nome que termina com "EventArgs". O gerador transformará a interface em uma classe que pode efetuar subclasses `EventArgs` e incluirá todas as propriedades na lista. O [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo deve ser usado na classe EventArgs para listar o nome da chave usada para pesquisar o dicionário de Objective-C para buscar o valor.

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

Os usuários do seu código, em seguida, podem facilmente assinar notificações lançadas para o [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) usando código como este:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
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

Acima vai gerar uma `MyScreenChangedEventArgs` classe com o `ScreenX` e `ScreenY` propriedades que buscará os dados das [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) dicionário usando as chaves "ScreenXKey" e "ScreenYKey" respectivamente e aplicar as conversões apropriadas. O `[ProbePresence]` atributo é usado para o gerador para investigar se a chave é definida `UserInfo`, em vez de tentar extrair o valor. Isso é usado para casos em que a presença da chave é o valor (normalmente para valores boolianos).

Isso permite que você escreva código como este:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Categorias de associação

Categorias são um mecanismo de Objective-C usado para estender o conjunto de métodos e propriedades disponíveis em uma classe.   Na prática, eles são usados para estender a funcionalidade de uma classe base (por exemplo `NSObject`) quando uma estrutura específica estiver vinculada (por exemplo `UIKit`), tornando seus métodos disponíveis, mas somente se a nova estrutura é vinculada.   Em alguns casos, eles são usados para organizar recursos em uma classe pela funcionalidade.   Elas são semelhantes no espírito para C# métodos de extensão. Isso é como uma categoria ficaria em Objective-c:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

O exemplo acima se encontrado em uma biblioteca estenderia instâncias do `UIView` com o método `makeBackgroundRed`.

Para associar os, você pode usar o [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) atributo em uma definição de interface.  Ao usar o [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
atributo, o significado das [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
atributo altera-se de que está sendo usado para especificar a classe base para estender, para que seja o tipo a ser estendido.

A seguir mostra como o `UIView` extensões são vinculadas e transformadas em C# métodos de extensão:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

As opções acima criará um `MyUIViewExtension` uma classe que contém o `MakeBackgroundRed` método de extensão.  Isso significa que agora você pode chamar "MakeBackgroundRed" em qualquer `UIView` subclasse, dando a você a mesma funcionalidade que você obteria em Objective-C. Em alguns casos, as categorias são usadas para estender uma classe de sistema não, mas para organizar funcionalidades puramente para fins de decoração.  Assim:

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
atributo também para esse estilo de decoração de declarações, você pode também apenas adicioná-los todos para a definição de classe.  Desses dois obteria o mesmo:

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

Ele é apenas menor nesses casos para mesclar as categorias:

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

Blocos são uma nova construção de introduzidos pela Apple para trazer o equivalente funcional do C# métodos anônimos para Objective-C. Por exemplo, o `NSSet` classe agora expõe esse método:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

A descrição acima declara um método chamado `enumerateObjectsUsingBlock:` que usa um argumento nomeado `block`. Este bloco é semelhante a um C# método anônimo em que ele tem suporte para capturar o ambiente atual (o ponteiro "this", o acesso aos parâmetros e variáveis locais). O método acima no `NSSet` invoca o bloco com dois parâmetros de um `NSObject` (o `id obj` parte) e um ponteiro para um valor booliano (o `BOOL *stop`) parte.

Para associar esse tipo de API com btouch, você precisa primeiro declarar a assinatura de tipo de bloco como um C# delegado e, em seguida, referenciá-la em um ponto de entrada de API, como este:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

E agora o seu código pode chamar sua função de C#:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

Se você preferir, você também pode usar lambdas:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Métodos assíncronos

O gerador de associação pode transformar uma determinada classe de métodos em amigável para async métodos (métodos que retornam uma Task ou Task&lt;T&gt;).

Você pode usar o [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
atributo em métodos que retornam void e cujo último argumento é um retorno de chamada.  Quando você aplicá-la a um método, o gerador de associação irá gerar uma versão desse método com o sufixo `Async`.  Se o retorno de chamada não usa nenhum parâmetro, o valor de retorno será uma `Task`, se o retorno de chamada usa um parâmetro, o resultado será um `Task<T>`.  Se o retorno de chamada usa vários parâmetros, você deve definir a `ResultType` ou `ResultTypeName` para especificar o nome desejado do tipo gerado que manterá todas as propriedades.

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

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Identificando tipos fortes para parâmetros de NSDictionary fracos

Em muitos lugares na API do Objective-C, os parâmetros são passados como com tipagem fraca `NSDictionary` APIs com chaves específicas e valores, mas eles são propenso (você pode passar chaves inválidas e não obter nenhum aviso; você pode passar valores inválidos e não obter nenhum aviso) e frustrante Para usar pois elas exigem que várias viagens e a documentação para pesquisar os possíveis nomes de chave e valores.

A solução é fornecer uma versão fortemente tipada que fornece que a versão fortemente tipada da API e em segundo plano mapeia as várias subjacente chaves e valores.

Então, por exemplo, se a API de Objective-C aceita uma `NSDictionary` e está documentado como colocar a chave `XyzVolumeKey` que utiliza um `NSNumber` com um valor de volume de 0,0 a 1,0 e um `XyzCaptionKey` que usa uma cadeia de caracteres, você desejaria que os usuários tenham uma boa API Isso se parece com isso:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

O `Volume` está definida como float que permitem valor nulo, conforme a convenção em Objective-C não requer esses dicionários para que o valor, portanto, há cenários em que o valor não pode ser definido.

Para fazer isso, você precisa fazer algumas coisas:

* Crie uma classe fortemente tipada, que pode efetuar subclasses [DictionaryContainer](xref:Foundation.DictionaryContainer) e fornece vários getters e setters para cada propriedade.
* Declarar sobrecargas para os métodos que aceitam `NSDictionary` para levar a nova versão fortemente tipada.

Você pode criar a classe fortemente tipada seja manualmente ou usar o gerador para fazer o trabalho para você.  Primeiro, exploramos como fazer isso manualmente, para que você entender o que está acontecendo e, em seguida, o método automático.

Você precisa criar um arquivo de suporte para isso, ele não entrará no seu contrato de API.  Este é o que você teria de escrever para criar sua classe XyzOptions:

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

Em seguida, você deve fornecer um método de wrapper que expõe a API de alto nível sobre a API de nível baixo.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Se sua API não precisa ser substituído, você pode ocultar a API baseada em dicionário NSDictionary com segurança usando o [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
atributo.

Como você pode ver, usamos o [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
atributo para um novo ponto de entrada da API de superfície e estamos usando nossa fortemente tipada de superfície `XyzOptions` classe.  O método de invólucro também permite que null seja passado.

Agora, uma coisa que podemos não mencionasse é onde o `XyzOptionsKeys` vem de valores.  Normalmente você agruparia as chaves que uma API traz à tona em uma classe estática, como `XyzOptionsKeys`, semelhante a esta:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Vamos examine o suporte automático para criar esses dicionários fortemente tipada.  Isso evita que muito do trabalho repetitivo, e você pode definir o dicionário diretamente no seu contrato de API, em vez de usar um arquivo externo.

Para criar um dicionário fortemente tipado, introduzir uma interface em sua API e decorá-lo com o [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) atributo.  Isso informa o gerador de que ele deve criar uma classe com o mesmo nome que a interface que será derivado `DictionaryContainer` e fornece acessadores tipados forte para ele.

O [ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) atributo utiliza um parâmetro, que é o nome da classe estática que contém as chaves de dicionário.  Em seguida, cada propriedade da interface se tornará um acessador fortemente tipadas.  Por padrão, o código usará o nome da propriedade com o sufixo "Chave" na classe estática para criar o acessador.

Isso significa que a criação de seu acessador fortemente tipadas não requer mais um arquivo externo, nem ter que criar manualmente os getters e setters para todas as propriedades, nem necessidade de pesquisar as chaves manualmente por conta própria.

Este é o que a associação inteira pareceria com:

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

Caso seja necessário fazer referência a no seu `XyzOption` membros de um campo diferente (que é não o nome da propriedade com o sufixo `Key`), você pode decorar a propriedade com um [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
atributo com o nome que você deseja usar.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Mapeamentos de tipo

Esta seção aborda como os tipos de Objective-C são mapeados para C# tipos.

<a name="Simple_Types" />

### <a name="simple-types"></a>Tipos simples

A tabela a seguir mostra como você deve mapear os tipos de Objective-C e do mundo CocoaTouch para o mundo do xamarin. IOS:

|Nome do tipo Objective-C|Tipo de API unificada do xamarin. IOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([mais informações sobre associação NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (Consulte também: [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Tipos de CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Tipos de base (`NS*`)|`Foundation.NS*`|
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

O tempo de execução do xamarin. IOS cuida automaticamente da conversão de C# matrizes para `NSArrays` e fazer a conversão, por exemplo, o método Objective-C imaginária que retorna um `NSArray` dos `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

Está associado como este:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

A ideia é usar um fortemente tipada C# pois isso permitirá que o IDE fornecer a conclusão de código apropriada com o tipo real sem forçar o usuário adivinhar ou pesquisar a documentação para descobrir o tipo real dos objetos contidos na matriz de matriz.

Em casos em que você pode não rastrear o tipo mais derivado real contido na matriz, você pode usar `NSObject []` como o valor retornado.

<a name="Selectors" />

### <a name="selectors"></a>Seletores

Seletores aparecem na API do Objective-C, como o tipo de especial `SEL`. Ao associar um seletor, seriam mapeados para o tipo de `ObjCRuntime.Selector`.  Normalmente os seletores são expostos em uma API com um objeto, o objeto de destino e um seletor para invocar o objeto de destino. Fornecimento desses dois basicamente corresponde ao C# delegar: algo que encapsula o método a invocar bem como para invocar o método no objeto.

Este é o que a associação se parece com:

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

Para fazer a associação mais agradável para C# desenvolvedores, você normalmente fornece um método que usa um `NSAction` parâmetro, que permite que C# delegados e lambdas a ser usado em vez da `Target+Selector`. Para fazer isso, você normalmente deve ocultar a `SetTarget` método sinalizando-o com um [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
atributo e, em seguida, você poderia expor um novo método auxiliar, como este:

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

Portanto, agora seu código de usuário pode ser escrito da seguinte forma:

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

Quando você estiver associando um método que usa um `NSString`, você pode substituí-lo com um C# tipo, nos tipos de retorno e parâmetros de cadeia de caracteres.

O único caso, quando você talvez queira usar um `NSString` é diretamente quando a cadeia de caracteres é usada como um token. Para obter mais informações sobre cadeias de caracteres e `NSString`, leia as [Design de API em NSString](~/ios/internals/api-design/nsstring.md) documento.

Em alguns casos raros, uma API pode expor uma cadeia de caracteres de tipo C (`char *`) em vez de uma cadeia de caracteres de Objective-C (`NSString *`). Nesses casos, é possível anotar o parâmetro com o [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
atributo.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>-out / parâmetros ref

Algumas APIs retornam valores em seus parâmetros, ou passam parâmetros por referência.

Normalmente, a assinatura tem esta aparência:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

O primeiro exemplo mostra uma linguagem comum do Objective-C para retornar códigos de erro, um ponteiro para um `NSError` ponteiro é passado e, após retornar o valor é definido.   O segundo método mostra como um método de Objective-C pode levar a um objeto e modificar seu conteúdo.   Isso seria uma passagem por referência, em vez de um valor de saída pura.

A associação teria esta aparência:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Atributos de gerenciamento de memória

Quando você usa o [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) atributo e você estiver passando dados que serão mantidos pelo método chamado, você pode especificar a semântica do argumento, passando-o como um segundo parâmetro, por exemplo:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

As opções acima seria sinalizar o valor como tendo a semântica de "Reter". A semântica disponível é:

-  Assign
-  Copiar
-  Manter

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Diretrizes de estilo

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Usando [interno]

Você pode usar o [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
atributo para ocultar um método de API pública. Você talvez queira fazer isso em casos em que a API exposta é um nível muito baixo e você deseja fornecer uma implementação de alto nível em um arquivo separado com base neste método.

Você também pode usar isso quando você deparar com limitações no gerador de associação, por exemplo alguns cenários avançados podem expor os tipos que não são associados, você deseja associar sua própria maneira e você deseja encapsular esses tipos de sua própria maneira.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Retornos de chamada e manipuladores de eventos

Normalmente, as classes de Objective-C transmitir notificações ou solicitam informações enviando uma mensagem em uma classe de delegado (delegado de Objective-C).

Nesse modelo, embora totalmente compatível e exibidos pelo xamarin. IOS podem, às vezes, ser complicado. Xamarin. IOS expõe o C# padrão de evento e um sistema de retorno de chamada de método na classe que pode ser usado nessas situações. Isso permite que o código como este para executar:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

O gerador de associação é capaz de reduzir a quantidade de digitação necessária para mapear o padrão de Objective-C para o C# padrão.

Começando com o xamarin. IOS 1.4 será possível também instruir o gerador para produzir associações para um específico delegados de Objective-C e expor o delegado como C# eventos e propriedades no tipo de host.

Há duas classes envolvido nesse processo, a classe de host que serão é aquele que atualmente emite eventos e envia-los na `Delegate` ou `WeakDelegate` e a classe de delegado real.

Considerando a configuração a seguir:

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

-  Em sua classe de host, adicione sua [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   o tipo que está atuando como seu delegado de declaração e o C# nome exposto por você. Em nosso exemplo acima, esses são `typeof (MyClassDelegate)` e `WeakDelegate` , respectivamente.
-  Em sua classe de delegado, em cada método que tem mais de dois parâmetros, você precisa especificar o tipo que você deseja usar para a classe EventArgs gerada automaticamente.

O gerador de associação não está limitado a apenas um destino de evento único de encapsulamento, é possível que algumas classes de Objective-C para emitir mensagens para mais de um delegado, portanto, você terá que fornecer matrizes para dar suporte a essa configuração. A maioria das configurações não será necessário, mas o gerador estiver pronto para dar suporte nesses casos.

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

O `EventArgs` é usado para especificar o nome da `EventArgs` classe a ser gerado. Você deve usar um por assinatura (neste exemplo, o `EventArgs` conterá um `With` propriedade do tipo nint).

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

Portanto, agora você pode usar o código como este:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Retornos de chamada são como invocações de eventos, a diferença é que, em vez de ter vários assinantes em potencial (por exemplo, vários métodos podem conectar uma `Clicked` evento ou um `DownloadFinished` evento) retornos de chamada podem ter apenas um único assinante.

O processo é idêntico, a única diferença é que, em vez de expor o nome da `EventArgs` classe que será gerado, EventArgs, na verdade, é usado para nomear resultante C# nome do delegado.

Se o método na classe de delegado retornar um valor, o gerador de associação mapeará isso em um método delegado na classe pai, em vez de um evento. Nesses casos, você precisará fornecer o valor padrão que deve ser retornado pelo método se o usuário não vincular ao delegado. Você faz isso usando o [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
ou [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) atributos.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) codificaremos um valor de retorno, enquanto [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
é usado para especificar o argumento de entrada será retornado.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Enumerações e tipos base

Você também pode referenciar tipos base que não são diretamente suportados pelo sistema de definição de interface btouch ou enumerações. Para fazer isso, coloque seu enumerações e tipos de núcleo em um arquivo separado e incluí-lo como parte de um dos arquivos extras que você fornece para btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>As dependências de vinculação

Se você estiver associando as APIs que não fazem parte do seu aplicativo, você precisa certificar-se de que seu executável está vinculado em relação a essas bibliotecas.

Você precisará informar ao xamarin. IOS como vincular suas bibliotecas, isso pode ser feito alterando sua configuração de compilação para invocar o `mtouch` comando com alguns extras criar argumentos que especificam como vincular com as novas bibliotecas usando o "-gcc_flags" opção, seguido por uma cadeia de caracteres entre aspas que contém todas as bibliotecas adicionais que são necessárias para o seu programa, como este:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

O exemplo acima vinculará `libMyLibrary.a`, `libSystemLibrary.dylib` e o `CFNetwork` biblioteca framework em seu arquivo executável final.

Ou você pode tirar proveito de nível de assembly [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), que você pode incorporar em seus arquivos de contrato (como `AssemblyInfo.cs`).
Quando você usa o [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), você precisará ter sua biblioteca nativa disponível no momento de fazer a associação, pois isso irá incorporar a biblioteca nativa com o seu aplicativo. Por exemplo:

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Você deve estar se perguntando por que você precisa `-force_load` comando e o motivo é que o ObjC - sinalizador embora ele compila o código na, não preserva os metadados necessários para dar suporte a categorias (a eliminação de código morto do compilador/vinculador separa-) que você necessário em tempo de execução para xamarin. IOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Referências assistidas

Alguns objetos transitórios, como folhas de ação e caixas de alerta são difíceis de controlar para desenvolvedores e o gerador de associação pode ajudar um pouco aqui.

Por exemplo, se você tiver uma classe que mostrou uma mensagem e, em seguida, é gerado um `Done` evento, a maneira tradicional de lidar com isso seria:

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

No cenário anterior o desenvolvedor precisa manter a referência ao objeto de si mesmo e o vazamento ou limpar ativamente a referência para caixa na sua própria.  Enquanto o código de associação, o gerador dá suporte a controlar a referência para você e claro quando um método especial é invocado, o código acima, em seguida, seria:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Observe como não é necessário para manter a variável em uma instância, que ele funciona com uma variável local e que não é necessário limpar a referência ao objeto se tornar inativo.

Para tirar proveito disso, sua classe deve ter uma propriedade de eventos definida [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) declaração e também o `KeepUntilRef` variável definido como o nome do método que é invocado quando o objeto tiver concluído seu trabalho, como Isso:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Protocolos de herança

A partir da versão 3.2 do xamarin. IOS, damos suporte herdando de protocolos que foram marcados com o [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) propriedade. Isso é útil em determinados padrões de API, como em `MapKit` onde o `MKOverlay` de protocolo, herda o `MKAnnotation` TCP/IP e é adotada por um número de classes que herdam de `NSObject`.

Historicamente, exigimos copiando o protocolo para cada implementação, mas nesses casos agora podemos ter o `MKShape` herdam o `MKOverlay` protocolo e ele irá gerar todos os métodos necessários automaticamente.

## <a name="related-links"></a>Links relacionados

- [Exemplo de associação](https://developer.xamarin.com/samples/monotouch/BindingSample/)
