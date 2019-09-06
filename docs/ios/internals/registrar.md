---
title: Tipo registrador para Xamarin. iOS
description: Este documento descreve o registrador do tipo Xamarin. iOS, C# que torna as classes disponíveis para o tempo de execução Objective-C.
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 0d8e16c2a651df293b13e7f7586d5a643caa1c9c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291836"
---
# <a name="type-registrar-for-xamarinios"></a>Tipo registrador para Xamarin. iOS

Este documento descreve o sistema de registro de tipo usado pelo Xamarin. iOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registro de classes e métodos gerenciados

Durante a inicialização, o Xamarin. iOS será registrado:

- Classes com um atributo [[Register]](xref:Foundation.RegisterAttribute) como classes Objective-C.
- Classes com um atributo [[Category]](xref:ObjCRuntime.CategoryAttribute) como categorias Objective-C.
- Interfaces com um atributo [[Protocol]](xref:Foundation.ProtocolAttribute) como protocolos Objective-C.
- Membros com uma [[exportação]](xref:Foundation.ExportAttribute), possibilitando que o Objective-C os acesse.

Por exemplo, considere o método `Main` gerenciado comum em aplicativos Xamarin. Ios:

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

Esse código informa ao tempo de execução Objective-C para usar `AppDelegate` o tipo chamado como a classe delegada do aplicativo. Para que o tempo de execução do Objective-C possa criar uma instância C# `AppDelegate` da classe, essa classe deve ser registrada.

O Xamarin. iOS executa o registro automaticamente, em tempo de execução (registro dinâmico) ou em tempo de compilação (registro estático).

O registro dinâmico usa a reflexão na inicialização para localizar todas as classes e métodos a serem registrados, passando-os para o tempo de execução Objective-C. O registro dinâmico é usado por padrão para compilações do simulador.

O registro estático inspeciona, no momento da compilação, os assemblies usados pelo aplicativo. Ele determina as classes e os métodos a serem registrados com Objective-C e gera um mapa, que é inserido em seu binário.
Em seguida, na inicialização, ele registra o mapa com o tempo de execução Objective-C. O registro estático é usado para compilações de dispositivos.

### <a name="categories"></a>Categorias

A partir do Xamarin. iOS 8,10, é possível criar categorias Objective-C usando C# a sintaxe.

Para criar uma categoria, use o `[Category]` atributo e especifique o tipo a ser estendido. Por exemplo, o código a seguir `NSString`estende:

```csharp
[Category (typeof (NSString))]
```

Cada um dos métodos de uma categoria tem `[Export]` um atributo, disponibilizando-o para o tempo de execução do Objective-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Todos os métodos de extensão gerenciados devem ser estáticos, mas é possível criar métodos de instância Objective- C# C usando a sintaxe padrão para métodos de extensão:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

O primeiro argumento para o método de extensão é a instância na qual o método foi invocado:

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

Este exemplo adicionará um método `toUpper` de instância nativa `NSString` à classe. Esse método pode ser chamado de Objective-C:

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

### <a name="protocols"></a>Protocolos

A partir do Xamarin. Ios 8,10, as interfaces `[Protocol]` com o atributo serão exportadas para Objective-C como protocolos:

```csharp
[Protocol ("MyProtocol")]
interface IMyProtocol
{
    [Export ("method")]
    void Method ();
}

class MyClass : IMyProtocol
{
    void Method ()
    {
    }
}
```

Esse código exporta `IMyProtocol` para Objective-C como um protocolo `MyProtocol` chamado e uma classe `MyClass` chamada que implementa o protocolo.

## <a name="new-registration-system"></a>Novo sistema de registro

A partir da versão 6.2.6 estável e da versão Beta 6.3.4, adicionamos um novo registrador estático. Na versão 7.2.1, tornamos o novo registrador o padrão.

Esse novo sistema de registro oferece os seguintes novos recursos:

- Detecção de erros de programador de tempo de compilação:
  - Duas classes sendo registradas com o mesmo nome.
  - Mais de um método exportado para responder ao mesmo seletor
- Remoção de código nativo não utilizado:
  - O novo sistema de registro adicionará referências fortes ao código usado em bibliotecas estáticas, permitindo que o vinculador nativo remova o código nativo não utilizado do binário resultante. Nas associações de exemplo do Xamarin, a maioria dos aplicativos se torna pelo menos 300 mil menor.

- Suporte para subclasses genéricas de `NSObject`; consulte os [genéricos do NSObject](~/ios/internals/api-design/nsobject-generics.md) para obter mais informações. Além disso, o novo sistema de registro capturará construções genéricas sem suporte que anteriormente causaram um comportamento aleatório no tempo de execução.

### <a name="errors-caught-by-the-new-registrar"></a>Erros capturados pelo novo registrador

Abaixo estão alguns exemplos de erros detectados pelo novo registrador.

- Exportando o mesmo seletor mais de uma vez na mesma classe:

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo:")]
      void Foo (NSString str);
      [Export ("foo:")]
      void Foo (string str)
  }
  ```

- Exportando mais de uma classe gerenciada com o mesmo nome Objective-C:

  ```csharp
  [Register ("Class")]
  class MyClass : NSObject {}

  [Register ("Class")]
  class YourClass : NSObject {}
  ```

- Exportando métodos genéricos:

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo")]
      void Foo<T> () {}
  }
  ```

### <a name="limitations-of-the-new-registrar"></a>Limitações do novo registrador

Algumas coisas para ter em mente sobre o novo registrador:

- Algumas bibliotecas de terceiros devem ser atualizadas para funcionar com o novo sistema de registro. Consulte as [modificações necessárias](#required-modifications) abaixo para obter mais detalhes.

- Uma desvantagem de curto prazo também é que Clang deve ser usado se a estrutura de contas for usada (isso ocorre porque o cabeçalho de **contas. h** da Apple só pode ser compilado por Clang). Adicione `--compiler:clang` aos argumentos adicionais do mTouch para usar o Clang se você estiver usando o Xcode 4,6 ou anterior (o Xamarin. Ios selecionará automaticamente Clang no Xcode 5,0 ou posterior.)

- Se o Xcode 4,6 (ou anterior) for usado, GCC/G + + deverá ser selecionado se os nomes de tipo exportados contiverem caracteres não ASCII (isso ocorre porque a versão do Clang fornecida com o Xcode 4,6 não oferece suporte a caracteres não ASCII dentro de identificadores no código Objective-C). Adicione `--compiler:gcc` aos argumentos mTouch adicionais para usar o gcc.

## <a name="selecting-a-registrar"></a>Selecionando um registrador

Você pode selecionar um registrador diferente adicionando uma das seguintes opções aos argumentos mTouch adicionais nas configurações de **Build do IOS** do projeto:

- `--registrar:static`– padrão para Builds de dispositivo
- `--registrar:dynamic`– padrão para compilações do simulador

> [!NOTE]
> O API clássica do Xamarin oferece suporte a `--registrar:legacystatic` outras opções, como e. `--registrar:legacydynamic` No entanto, essas opções não são suportadas pelo API Unificada.

## <a name="shortcomings-in-the-old-registration-system"></a>Deficiências no sistema de registro antigo

O antigo sistema de registro tem as seguintes desvantagens:

- Não havia nenhuma referência estática (nativa) para as classes e métodos Objective-C em bibliotecas nativas de terceiros, o que significa que não pudemos solicitar que o vinculador nativo remova o código nativo de terceiros que não foi realmente usado (porque tudo seria removido). Esse é o motivo para o `-force_load libNative.a` que cada associação de terceiros teve de fazer (ou o equivalente `ForceLoad=true` no `[LinkWith]` atributo).
- Você pode exportar dois tipos gerenciados com o mesmo nome Objective-C sem aviso. Um cenário raro era acabar com duas `AppDelegate` classes em namespaces diferentes. Em tempo de execução, seria completamente aleatório qual foi escolhido (na verdade, ele variava entre as execuções de um aplicativo que não havia sido recriada, o que fez para uma experiência de depuração muito enigmático e frustrante).
- Você poderia exportar dois métodos com a mesma assinatura Objective-C. Mais uma vez, o que seria chamado de Objective-C era aleatório (mas esse problema não era tão comum quanto o anterior, principalmente porque a única maneira de realmente experimentar esse bug era substituir o método gerenciado sem sorte).
- O conjunto de métodos exportados era ligeiramente diferente entre compilações dinâmicas e estáticas.
- Ele não funciona corretamente ao exportar classes genéricas (que a implementação genérica exata executada em tempo de execução seria aleatória, resultando efetivamente em um comportamento indeterminado).

<a name="required-modifications" />

## <a name="new-registrar-required-changes-to-bindings"></a>Novo registrador: alterações necessárias às associações

Esta seção descreve as alterações de associações que devem ser feitas para trabalhar com o novo registrador.

### <a name="protocols-must-have-the-protocol-attribute"></a>Os protocolos devem ter o atributo [Protocol]

Os protocolos agora devem ter `[Protocol]` o atributo. Se você não fizer isso, será um erro de vinculador nativo, como:

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>Os seletores devem ter um número válido de parâmetros

Todos os seletores devem indicar o número de parâmetros corretamente. Anteriormente, esses erros foram ignorados e poderiam causar problemas de tempo de execução.

Em suma, o número de dois-pontos deve corresponder ao número de parâmetros:

- Sem parâmetros:`foo`
- Um parâmetro:`foo:`
- Dois parâmetros:`foo:parameterName2:`

Estes são os usos incorretos:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Usar o parâmetro IsVariadic na exportação

As funções Variadic devem usar `IsVariadic` o argumento para `[Export]` o atributo:

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Deve vincular a símbolos existentes

É impossível associar classes que não existem na biblioteca nativa.
Se uma classe tiver sido removida ou renomeada na biblioteca nativa, certifique-se de atualizar as ligações para que correspondam.
