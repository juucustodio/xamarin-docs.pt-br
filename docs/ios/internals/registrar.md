---
title: Tipo de registrador para xamarin. IOS
description: Este documento descreve o registrador de tipo do xamarin. IOS, que torna o C# classes disponíveis para o tempo de execução do Objective-C.
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 8/29/2018
ms.openlocfilehash: cd9e92e2c96d0a0696633e49869f2661e410d343
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233582"
---
# <a name="type-registrar-for-xamarinios"></a>Registrador de tipo para xamarin. IOS

Este documento descreve o sistema de registro do tipo usado pelo xamarin. IOS.

## <a name="registration-of-managed-classes-and-methods"></a>Registro de métodos e classes gerenciadas

Durante a inicialização, irá registrar o xamarin. IOS:

- Classes com um [[registrar]](xref:Foundation.RegisterAttribute) atributo como classes de Objective-C.
- Classes com um [[Category]](xref:ObjCRuntime.CategoryAttribute) atributo como categorias de Objective-C.
- Interfaces com um [[Protocol]](xref:Foundation.ProtocolAttribute) atributo como protocolos de Objective-C.
- Os membros com uma [[exportar]](xref:Foundation.ExportAttribute), tornando possível para Objective-C para acessá-los.

Por exemplo, considere o gerenciado `Main` método comum em aplicativos xamarin. IOS:

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

Esse código informa o tempo de execução do Objective-C para usar o tipo chamado `AppDelegate` como classe de representante do aplicativo. Para o tempo de execução do Objective-C poder criar uma instância do C# `AppDelegate` de classe, que a classe deve ser registrado.

Xamarin. IOS executa automaticamente, registro em tempo de execução (registro dinâmico) ou em tempo de compilação (registro estático).

Registro dinâmico usa reflexão na inicialização para localizar todas as classes e métodos para registrar, passá-los para o tempo de execução do Objective-C. Registro dinâmico é usado por padrão para compilações de simulador.

Em tempo de compilação, registro estático inspeciona os assemblies usados pelo aplicativo. Ele determina as classes e métodos para registrar Objective-C e gera um mapa, que é inserido em seu binário.
Em seguida, na inicialização, ele registra o mapa com o tempo de execução do Objective-C. Registro estático é usado para compilações de dispositivo.

### <a name="categories"></a>Categorias

Começando com o xamarin. IOS 8.10, é possível criar categorias de Objective-C usando C# sintaxe.

Para criar uma categoria, use o `[Category]` de atributos e especificar o tipo a ser estendido. Por exemplo, o código a seguir estende `NSString`:

```csharp
[Category (typeof (NSString))]
```

Cada um dos métodos de uma categoria tem um `[Export]` atributo, tornando-o disponível para o tempo de execução do Objective-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Todos os métodos de extensão gerenciada devem ser estáticos, mas é possível criar métodos de instância de Objective-C usando o padrão C# sintaxe de métodos de extensão:

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

Este exemplo adicionará um nativo `toUpper` instância de método para o `NSString` classe. Esse método pode ser chamado de Objective-c:

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

Começando com o xamarin. IOS 8.10, faz interface com o `[Protocol]` atributo será exportado para Objective-C como protocolos:

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

Esse código exporta `IMyProtocol` para Objective-C como um protocolo de chamada `MyProtocol` e uma classe chamada `MyClass` que implementa o protocolo.

## <a name="new-registration-system"></a>Novo sistema de registro

Começando com o 6.2.6 estável versão e a versão beta 6.3.4, adicionamos um novo registrador estático. No 7.2.1 versão, fizemos o registrador de novo o padrão.

Esse novo sistema de registro oferece os seguintes recursos novos:

- Detecção de tempo de compilação de erros do programador:
    - Duas classes que está sendo registradas com o mesmo nome.
    - Mais de um método exportado para responder ao mesmo seletor
- Remoção de código nativo não utilizado:
    - O novo sistema de registro adicionará referências fortes para o código usado em bibliotecas estáticas, permitindo que o vinculador nativo a retirar o código nativo não utilizado do binário resultante. Em associações de exemplo do Xamarin, a maioria dos aplicativos tornam-se pelo menos 300k menores.

- Suporte para subclasses genéricas de `NSObject`; consulte [classes genéricas de NSObject](~/ios/internals/api-design/nsobject-generics.md) para obter mais informações. Além disso, o novo sistema de registro irá capturar sem suporte construções genéricas que anteriormente teria causado um comportamento aleatório no tempo de execução.

### <a name="errors-caught-by-the-new-registrar"></a>Erros capturados pelo registrador de novo

Abaixo estão alguns exemplos dos erros capturados pelo registrador de novo.

- Exportando o mesmo seletor de mais de uma vez na mesma classe:

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

- Exportação de mais de uma classe gerenciada com o mesmo nome de Objective-C:

    ```csharp
    [Register ("Class")]
    class MyClass : NSObject {}

    [Register ("Class")]
    class YourClass : NSObject {}
    ```

- Exportando os métodos genéricos:

    ```csharp
    [Register]
    class MyDemo : NSObject
    {
        [Export ("foo")]
        void Foo<T> () {}
    }
    ```

### <a name="limitations-of-the-new-registrar"></a>Limitações do registrador de novo

Algumas coisas para ter em mente sobre o registrador novo:

- Algumas bibliotecas de terceiros devem ser atualizadas para funcionar com o novo sistema de registro. Ver [necessárias modificações](#required_modifications) abaixo para obter mais detalhes.

- Uma desvantagem de curto prazo é também que Clang deve ser usada se a estrutura de contas é usada (Isso ocorre porque da Apple **accounts.h** cabeçalho só pode ser compilado pelo Clang). Adicionar `--compiler:clang` aos argumentos adicionais do mtouch usar Clang, se você estiver usando o Xcode 4.6 ou anterior (xamarin. IOS automaticamente selecionará Clang no Xcode 5.0 ou posterior.)

- Se o Xcode 4.6 (ou anterior) é usada, GCC / G + + deve ser selecionada se o tipo exportado nomes contêm caracteres não ASCII (Isso é porque a versão do Clang fornecido com o Xcode 4.6 não oferece suporte a caracteres não ASCII dentro de identificadores no código Objective-C). Adicionar `--compiler:gcc` aos argumentos adicionais do mtouch usar GCC.

## <a name="selecting-a-registrar"></a>Selecionando um registrador

Você pode selecionar um registrador diferente, adicionando uma das opções a seguir para os argumentos adicionais do mtouch do projeto **Build do iOS** configurações:

- `--registrar:static` – o padrão para compilações de dispositivo
- `--registrar:dynamic` – o padrão para compilações de simulador

> [!NOTE]
> API clássica do Xamarin suporte para outras opções, como `--registrar:legacystatic` e `--registrar:legacydynamic`. No entanto, essas opções não são suportadas pela API unificada.

## <a name="shortcomings-in-the-old-registration-system"></a>Limitações no antigo sistema de registro

O antigo sistema de registro tem as seguintes desvantagens:

- Não havia nenhuma referência estática (nativa) como Objective-C classes e métodos em bibliotecas nativas produtos de terceiros, que significa que pedimos não foi possível que o vinculador nativo a remover o código nativo de terceiros que, na verdade, não foi usado (porque tudo o que seria removido). Esse é o motivo para o `-force_load libNative.a` que precisei fazer todas as ligações de terceiros (ou equivalente `ForceLoad=true` no `[LinkWith]` atributo).
- Você pode exportar dois tipos gerenciados com o mesmo nome de Objective-C sem aviso. Um cenário raro foi acabar com dois `AppDelegate` classes nos namespaces diferentes. Em tempo de execução seria completamente aleatório qual delas foi obtido (na verdade, ele variam entre as execuções de um aplicativo ainda não tiver sido recriado - feita para uma experiência de depuração muito confusa e frustrante).
- Você pode exportar os dois métodos com a mesma assinatura do Objective-C. Mais uma vez qual delas será chamado de Objective-C foi aleatório (mas esse problema não era tão comum que o anterior, principalmente porque a única maneira de experimentar, na verdade, esse bug foi substituir o método gerenciado sorte).
- O conjunto de métodos que foi exportado foi um pouco diferentes entre compilações dinâmicas e estáticas.
- Ele não funciona corretamente ao exportar as classes genéricas (qual implementação genérica exata executada em tempo de execução seria aleatória, resultando efetivamente em um comportamento indeterminado).

## <a name="new-registrar-required-changes-to-bindings"></a>Novo registrador: exigiu alterações às associações

Esta seção descreve as alterações de associações que devem ser feitas para trabalhar com o registrador de novo.

### <a name="protocols-must-have-the-protocol-attribute"></a>Protocolos devem ter o atributo [Protocol]

Protocolos agora devem ter o `[Protocol]` atributo. Se você não fizer isso, você será um erro de vinculador nativo, como:

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>Seletores devem ter um número válido de parâmetros

Todos os seletores devem indicar o número de parâmetros corretamente. Anteriormente, esses erros foram ignorados e pode causar problemas de tempo de execução.

Em resumo, o número de dois-pontos deve corresponder ao número de parâmetros:

- Sem parâmetros: `foo`
- Um parâmetro: `foo:`
- Dois parâmetros: `foo:parameterName2:`

Estes são os usos incorretos:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Use o parâmetro IsVariadic na exportação

Funções Variadic devem usar o `IsVariadic` argumento para o `[Export]` atributo:

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Deve vincular aos símbolos existentes

Não é possível associar classes que não existem na biblioteca nativa.
Se uma classe foi removida ou renomeada na biblioteca do native, certifique-se de atualizar as associações para corresponder.
