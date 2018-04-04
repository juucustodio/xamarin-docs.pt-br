---
title: Tipo de registro
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 2504ab36ed9d387cdfccd5ac4d01c85d9bec7cf3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="type-registrar"></a>Tipo de registro

Este documento descreve o sistema de registro do tipo usado pelo xamarin.

## <a name="registration-of-managed-classes-and-methods"></a>Registro de métodos e classes gerenciadas

Durante a inicialização, registrará xamarin:

  - Classes com um [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo como classes Objective-C.
  - Classes com um [[Category]](https://developer.xamarin.com/api/type/CRuntime.CategoryAttribute) atributo como categorias Objective-C.
  - Interfaces com um [[Protocol]](https://developer.xamarin.com/api/type/Foundation.ProtocolAttribute/) atributo como protocolos Objective-C.

em cada membros de casos com um [[exportar]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo são exportados para o objetivo-C. Isso permite que classes gerenciadas ser criado e gerenciado métodos a serem chamados de Objective-C e é a maneira que os métodos e propriedades estão vinculadas entre o mundo c# e Objective-C, um.

Um exemplo muito simple é a classe AppDelegate que tem todos os aplicativos. Você deve se lembrar de que o método Main gerenciado tem uma linha como esta:

    UIApplication.Main (args, null, "AppDelegate");

Isso informa o tempo de execução Objective-C para criar o tipo chamado "AppDelegate" como classe de representante do aplicativo.  Para o tempo de execução Objective-C saber como criar uma instância da classe "AppDelegate" escrita em c#, essa classe deve ser registrado.

Tempo de execução do xamarin cuidará do registro para você, internamente, esse registro pode ser feito inteiramente em tempo de execução (registro dinâmico) ou pode ser feita em tempo de compilação (registro estático).  A abordagem dinâmica envolve usando reflexão na inicialização para localizar todas as classes e métodos para registrar e passe-os para o tempo de execução Objective-C.  A abordagem estática inspeciona os assemblies usados pelo aplicativo em tempo de compilação.  Ele determina as classes e métodos para registrar Objective-C e gera um mapa que é inserido em seu binário.  Em seguida, na inicialização, nós registramos o mapa com o tempo de execução Objective-C.

### <a name="categories"></a>Categorias

Começando com o xamarin 8.10 será possível criar categorias Objective-C usando a sintaxe do c#.

Isso é feito usando o atributo [Category], especificando o tipo a ser estendido como um argumento para o atributo.
O exemplo a seguir para a instância estenderá NSString:

    [Category (typeof (NSString))]

Cada método de categoria é usando o mecanismo normal para exportar os métodos para Objective-C usando o atributo [exportação]:

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

    [Category (typeof (NSString))]
    public static class MyStringCategory
    {
        [Export ("toUpper")]
        static string ToUpper (this NSString self)
        {
            return self.ToString ().ToUpper ();
        }
    }

Este exemplo adicionará um método de instância toUpper nativo para a classe NSString, que pode ser chamada do objetivo-C.

    [Category (typeof (UIViewController))]
    public static class MyViewControllerCategory
    {
        [Export ("shouldAutoRotate")]
        static bool GlobalRotate ()
        {
            return true;
        }
    }

### <a name="protocols"></a>Protocolos

Começando com o xamarin 8.10 interfaces com o atributo [Protocol] serão exportados para Objective-C como protocolos.

Exemplo:

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

Isso será exportado para Objective-C como um protocolo (MeuProtocolo) e uma classe (MyClass) que implementa o protocolo.

 **Registro dinâmico**

é usado para compilações do simulador, como ele acelera o ciclo de compilação/debug.  Este é o resultado de eliminar as etapas que gera o mapeamento de classe e a compilação dessa tabela de mapa em seu aplicativo de inicialização sempre que iniciar seu aplicativo e, em vez disso, um iniciador de finalidade geral é usado sempre.  O computador tenha muita energia para executar o tempo de execução varredura de classes rapidamente, portanto desempenho nunca é um problema.

 **Registro estático**

destina-se para compilações do dispositivo como dispositivos móveis são mais lentos que computadores desktop e execução varredura estiver lenta.  Como compilações do dispositivo sempre será necessário criar um novo binário, o ciclo de compilação/depuração não é afetado pela criação do mapeamento de registro.

## <a name="new-registration-system"></a>Novo sistema de registro

Começando com o 6.2.6 estável versão e a versão beta 6.3.4 adicionamos um novo registrador estático. No 7.2.1 versão fizemos registrador de novo o padrão.

Esse novo sistema de registro oferece os seguintes recursos novos:

- Detecção de erros do programador de tempo de compilação:
    - Duas classes que está sendo registrados com o mesmo nome.
    - Mais de um método exportado para responder a mesmo seletor



- Pode remover o código nativo não utilizado
    - O novo sistema de registro adicionará referências fortes para o código usado em bibliotecas estáticas, permitindo que o vinculador nativo para retirar o código nativo não utilizado do binário resultante.
      Em associações de exemplo do Xamarin, a maioria dos aplicativos, tornam-se pelo menos 300k menores.

- Suporte para genéricos subclasses de NSObject. Consulte [NSObject genéricos](~/ios/internals/api-design/nsobject-generics.md) para obter mais informações. Além disso, o novo sistema de registro capturará construções sem suporte de genéricas que teria causado anteriormente aleatório comportamento em tempo de execução.

Estes são alguns exemplos dos erros detectados pelo registar novo:

Exportando o mesmo seletor de mais de uma vez na mesma classe.

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo:")]
    void Foo (NSString str);
    [Export ("foo:")]
    void Foo (string str)
}
```

Exportação de mais de uma classe gerenciada com o mesmo nome Objective-C.

```csharp
[Register ("Class")]
class MyClass : NSObject {}

[Register ("Class")]
class YourClass : NSObject {}
```

Exportando métodos genéricos.

```csharp
[Register]
class MyDemo : NSObject {
    [Export ("foo")]
    void Foo<T> () {}
}
```



Algumas coisas a serem lembrados sobre o registro de novo:
- Alguns terceiros bibliotecas precisam ser atualizados para funcionar com o novo sistema de registro, consulte a seção [necessárias modificações abaixo](#required_modifications) para obter mais detalhes.
- Uma desvantagem de curto prazo é também que Clang deve ser usado se a estrutura de contas é usada (Isso ocorre porque o cabeçalho de accounts.h da Apple só pode ser compilado pelo Clang). Adicionar <code>--compiler:clang</code> aos argumentos mtouch adicionais para usar o Clang se o estiver usando o Xcode 4.6 ou anterior (xamarin automaticamente selecionará Clang no Xcode 5.0 ou posterior.)

    <li>Se o Xcode 4.6 (ou anterior) é usado, GCC ou G + + deve ser selecionado se o tipo exportado nomes contêm caracteres não ascii (Isso é porque a versão do Clang fornecido com o Xcode 4.6 não oferece suporte a caracteres não ascii em identificadores no código Objective-C). Adicionar <code>--compiler:gcc</code> aos argumentos adicionais mtouch usar GCC.


## <a name="selecting-a-registrar"></a>Selecionando um registrador

Você pode selecionar um registrador diferente, adicionando uma das opções a seguir para os argumentos adicionais mtouch no iOS do projeto opções de compilação:

-  `--registrar:static` : o padrão para compilações do dispositivo
-  `--registrar:dynamic` : o padrão para compilações do simulador
-  `--registrar:legacystatic` : o padrão para compilações de dispositivo até xamarin 7.2.1
-  `--registrar:legacydynamic` : o padrão para compilações de simulador até xamarin 7.2.1


## <a name="shortcomings-in-the-old-registration-system"></a>Limitações no antigo sistema de registro

O sistema de registro antigo tem as seguintes desvantagens:

-  Não houve nenhuma referência estática (nativa) para Objective-C classes e métodos em bibliotecas nativas de terceiros, que significa que não foi possível pedimos que o vinculador nativo para remover o código nativo de terceiros que não foi realmente usado (como tudo o que seria removido). Este é o motivo para a "-force_load libNative.a" que todas as associações de terceiros tiveram que fazer (ou o equivalente ForceLoad = true no atributo [LinkWith]).
-  Você pode exportar os dois tipos gerenciados com o mesmo nome Objective-C, sem qualquer aviso. Um cenário raro era acabar com duas classes AppDelegate (em namespaces diferentes). Em tempo de execução pode ser completamente aleatório que foi separado (na verdade que ele variadas entre as execuções de um aplicativo ainda não foi recriado - feitas para uma experiência de depuração muito confusa e frustrante).
-  Você pode exportar os dois métodos com a mesma assinatura Objective-C. Novamente qual delas será chamado de Objective-C foi aleatório (mas esse problema não era tão comum que o anterior, principalmente porque a única maneira de experimentar realmente esse bug foi substituir o método gerenciado sorte).
-  O conjunto de métodos que foi exportado foi ligeiramente diferente entre as compilações dinâmicas e estáticas.
-  Ele não funciona corretamente ao exportar classes genéricas (qual implementação genérica exata executada em tempo de execução seria aleatória, resultando efetivamente em comportamento indeterminado).


 <a name="required_modifications" />


## <a name="new-registrar-required-changes-to-bindings"></a>Novo registrador: As alterações necessárias associações


As associações existentes Objective-C talvez precise ser atualizado para funcionar com o novo registar.

Aqui está uma lista das alterações que precisam ser feitas.

### <a name="protocols-must-have-the-protocol-attribute"></a>Protocolos devem ter o atributo [Protocol]

Implementações de protocolos agora devem ter o atributo [Protocol] aplicado a eles.  Se você não fizer isso, você será um erro de vinculador nativo como este:

```csharp
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>Seletores devem ter um número válido de parâmetros

Todos os seletores devem indicar o número de parâmetros corretamente.  Anteriormente, esses erros foram ignorados e podem causar problemas de tempo de execução.

Em resumo, o número de dois-pontos deve corresponder ao número de parâmetros.

Por exemplo:

-  Sem parâmetros: 'foo'
-  Um parâmetro: ' foo:'
-  Dois parâmetros: ' foo:parameterName2:'


A seguir é usados incorretos:

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Use o parâmetro IsVariadic na exportação

Funções de Variadic devem dizer isso no seu atributo de exportação (Isso ocorre porque o seletor é incorreto sobre o número de argumentos, ou seja, do ponto de 2. acima for violada):

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Deve vincular a existente de símbolos

Não é possível associar as classes que não existem na biblioteca nativa.

Se você tentar associar classes não existente, você obterá um erro de vinculador nativo.

Isso geralmente ocorre quando uma associação existe há algum tempo, e o código nativo foi modificado durante esse tempo para que uma determinada classe nativo foi removida ou renomeada, enquanto a associação não foi atualizada.

