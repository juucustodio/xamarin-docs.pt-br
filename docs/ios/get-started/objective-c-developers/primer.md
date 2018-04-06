---
title: Manual de introdução a C# para Desenvolvedores de Objective-C
description: O Xamarin.iOS permite que código independente de plataforma escrito em C# seja compartilhado entre plataformas. No entanto, convém que os aplicativos existentes do iOS aproveitem o código em Objective-C que já foi criado. Este artigo é um breve manual para desenvolvedores do Objective-C que desejam migrar para o Xamarin e a linguagem C#.
ms.prod: xamarin
ms.assetid: 00285CBD-AE5E-4126-8F22-6B231B9467EA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 717e131dd4025ea07780f6e52d8171fd15272dac
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="c-primer-for-objective-c-developers"></a>Manual de introdução a C# para Desenvolvedores de Objective-C

_O Xamarin.iOS permite que código independente de plataforma escrito em C# seja compartilhado entre plataformas. No entanto, convém que os aplicativos existentes do iOS aproveitem o código em Objective-C que já foi criado. Este artigo é um breve manual para desenvolvedores do Objective-C que desejam migrar para o Xamarin e a linguagem C#._

Aplicativos do iOS e do OS X desenvolvidos em Objective-C podem se beneficiar do Xamarin aproveitando o C# em locais em que o código específico da plataforma não é necessário. Assim, o código pode ser usado em dispositivos não Apple. Itens como serviços Web, JSON e análise de XML e algoritmos personalizados podem então ser usados em plataforma cruzada.

Para tirar proveito do Xamarin, mantendo ativos de Objective-C existentes, o primeiro item pode ser exposto ao C# em uma tecnologia do Xamarin conhecida como associações, que levam o código em Objective-C para o mundo gerenciado do C#. Além disso, se desejado, também é possível fazer a portabilidade do código linha por linha para C#. No entanto, independentemente da abordagem, seja ela associação ou portabilidade, é necessário algum conhecimento de Objective-C e C# para aproveitar de maneira eficaz o código existente em Objective-C com o Xamarin.iOS.

## <a name="objective-c-interop"></a>Interoperabilidade de Objective-C

Atualmente, não há um mecanismo com suporte para criar uma biblioteca em C# usando o Xamarin.iOS que possa ser chamada do Objective-C. O principal motivo disso é que o tempo de execução Mono também é necessário, além da associação. No entanto, você ainda pode criar a maior parte da lógica em Objective-C, incluindo interfaces do usuário. Para isso, encapsule o código em Objective-C em uma biblioteca e crie uma associação para ela. O Xamarin.iOS é necessário para iniciar o aplicativo (o que significa que deve criar o ponto de entrada `Main`). Depois disso, qualquer outra lógica pode ser em Objective-C, exposta para C#, por meio da associação (ou via P/Invoke). Assim, você pode manter a lógica específica da plataforma em Objective-C e desenvolver as partes independentes de plataforma em C#.

Este artigo destaca algumas semelhanças-chave e aponta várias diferenças em ambas as linguagens para servir como um manual de introdução ao mudar para C# com o Xamarin.iOS, por meio de associação ao código existente em Objective-C ou portabilidade para C#.

Para obter detalhes sobre a criação de associações, confira os outros documentos em [Associação de Objective-C](~/ios/platform/binding-objective-c/index.md).

## <a name="language-comparison"></a>Comparação de linguagens

Objective-C e C# são linguagens muito diferentes do ponto de vista sintático e de tempo de execução. Objective-C é uma linguagem dinâmica e usa um esquema de passagem de mensagem, enquanto C# é estaticamente tipado. Em relação à sintaxe, o Objective-C é como o Smalltalk, enquanto o C# deriva grande parte de sua sintaxe fundamental do Java, embora tenha se desenvolvido para incluir vários recursos além do Java nos últimos anos.

Dito isso, há vários recursos de linguagem do Objective-C e do C# que têm função semelhante. Ao criar uma associação para código de Objective-C em C# ou ao fazer a portabilidade de Objective-C para C#, é útil entender essas semelhanças.

### <a name="protocols-vs-interfaces"></a>Protocolos vs. Interfaces

Objective-C e C# são linguagens de herança única. No entanto, ambas as linguagens têm suporte para implementar várias interfaces em uma classe específica. No Objective-C, essas interfaces lógicas são chamadas de *protocolos*, enquanto em C# são chamadas de *interfaces*. Em termos de implementação, a principal diferença entre uma interface em C# e um protocolo em Objective-C é que o segundo pode ter métodos opcionais. Para obter mais informações, confira o artigo [Eventos, representantes e protocolos](~/ios/app-fundamentals/delegates-protocols-and-events.md).

### <a name="categories-vs-extension-methods"></a>Categorias vs. Métodos de extensão

Objective-C permite que métodos sejam adicionados a uma classe para a qual talvez você não tenha o código de implementação usando *Categorias*. No C#, um conceito semelhante está disponível por meio de algo conhecido como *métodos de extensão*.

Métodos de extensão permitem que você adicione métodos estáticos a uma classe, em que métodos estáticos em C# são análogos a métodos de classe em Objective-C. Por exemplo, o código a seguir adiciona um método chamado `ScrollToBottom` à classe `UITextView`, que, por sua vez, é uma classe gerenciada que está associada à classe `UITextView` de Objective-C de UIKit:

```csharp
public static class UITextViewExtensions
{
    public static void ScrollToBottom (this UITextView textView)
    {
        // code to scroll textView
    }
}
```

Em seguida, quando uma instância de `UITextView` for criada no código, o método estará disponível na lista de preenchimento automático, conforme mostrado abaixo:

 ![](primer-images/01-extensionmethodintellisense.png "O método disponível no preenchimento automático")

Quando o método de extensão é chamado, a instância é passada para o argumento, como `textView` neste exemplo.

### <a name="frameworks-vs-assemblies"></a>Estruturas vs. Assemblies

Classes relacionadas a pacotes em Objective-C em diretórios especiais conhecidos como estruturas. No entanto, em C# e .NET, assemblies são usados para fornecer código pré-compilado reutilizável. Em ambientes fora do iOS, assemblies contêm código de IL (linguagem intermediária) que é compilado JIT (just-in-time) no tempo de execução. No entanto, a Apple não permite JIT em aplicativos do iOS. Portanto, o código em C# voltado para o iOS com o Xamarin é compilado AOT (antecipadamente), produzindo um único executável Unix juntamente com arquivos de metadados que estão incluídos no pacote do aplicativo.

### <a name="selectors-vs-named-parameters"></a>Seletores vs. Parâmetros nomeados

Métodos de Objective-C incluem inerentemente nomes de parâmetro em seletores, por sua própria natureza. Por exemplo, um seletor como `AddCrayon:WithColor:` esclarece o que significa cada parâmetro quando usado no código. Opcionalmente, C# também dá suporte a argumentos nomeados.

Por exemplo, um código semelhante em C# usando argumentos nomeados seria:

```csharp
AddCrayon (crayon: myCrayon, color: UIColor.Blue);
```

Embora C# tenha adicionado esse suporte na versão 4.0 da linguagem, na prática, ele não é usado com frequência. No entanto, se você quiser ser explícito no código, o suporte para isso existe.

### <a name="headers-and-namespaces"></a>Cabeçalhos e Namespaces

Sendo um superconjunto de C, o Objective-C usa cabeçalhos para declarações públicas que são separadas do arquivo de implementação. C# não usa arquivos de cabeçalho. Diferentemente de Objective-C, o código em C# está contido em namespaces. Se quiser incluir código disponível em algum namespace, você deve adicionar uma diretiva using à parte superior do arquivo de implementação ou qualificar o tipo com o namespace completo.

Por exemplo, o código a seguir inclui o namespace `UIKit`, tornando todas as classes no namespace disponíveis para a implementação:

```csharp
using UIKit
namespace MyAppNamespace
{
    // implementation of classes
}
```

Além disso, a palavra-chave de namespace no código acima define o namespace usado para o próprio arquivo de implementação. Se vários arquivos de implementação compartilharem o mesmo namespace, não será necessário incluir o namespace em uma diretiva using também, pois isso estará implícito.

### <a name="properties"></a>Propriedades

Objective-C e C# têm o conceito de propriedades para fornecer uma abstração de alto nível em torno de métodos de acessador. Em Objective-C, a diretiva de compilador @property é usada para gerar de forma eficaz os métodos acessadores. Por outro lado, C# inclui suporte para propriedades na própria linguagem. Uma propriedade de C# pode ser implementada usando um estilo mais longo que acessa um campo de suporte ou usando uma sintaxe de propriedade automática menor, conforme mostrado nos seguintes exemplos:

```csharp
// automatic property syntax
public string Name { get; set; }

// property implemented with a backing field
string address;
public string Address {
    get {
        // could add additional code here
        return address;
    }
    set {
        address = value;
    }
}
```

### <a name="static-keyword"></a>Palavra-chave Static

A palavra-chave *static* tem um significado muito diferente entre Objective-C e C#. Em Objective-C, funções estáticas são usadas para limitar o escopo de uma função ao arquivo atual. No entanto, em C#, o escopo é mantido por meio das palavras-chave *public*, *private* e *internal*.

Quando a palavra-chave static é aplicada a uma variável em Objective-C, a variável mantém seu valor em chamadas de função.

C# também tem uma palavra-chave static. Quando aplicada a um método, ela faz o mesmo que o modificador `+` faz em Objective-C. Ou seja, cria um método de classe. Da mesma forma, quando aplicada a outros constructos, como campos, propriedades e eventos, torna-os parte do tipo em que são declarados, em vez de com qualquer instância desse tipo. Você também pode criar uma classe estática, em que todos os métodos definidos na classe também devem ser estáticos.

### <a name="nsarray-vs-list-initialization"></a>NSArray vs. Inicialização de lista

Objective-C agora inclui a sintaxe literal para uso com `NSArray`, tornando mais fácil inicializar. Porém, C# tem um tipo mais avançado, chamado `List`, que é *genérico*. Isso significa que o tipo que a lista contém pode ser fornecido pelo código que cria a lista (como modelos em C++). Além disso, listas dão suporte à sintaxe de inicialização automática conforme mostrado abaixo:

```csharp
MyClass object1 = new MyClass ();
MyClass object2 = new MyClass ();
List<MyClass> myList = new List<MyClass>{ object1, object2 };
```

### <a name="blocks-vs-lambda-expressions"></a>Blocos vs. Expressões lambda

Objective-C usa *blocos* para criar fechamentos, em que você pode criar uma função embutida que pode fazer uso do estado onde é colocada. C# tem um conceito semelhante, com o uso de expressões lambda. Em C#, expressões lambda são criadas com o operador `=>`, conforme mostrado abaixo:

```csharp
(args) => {
    //  implementation code
};
```

Para obter mais informações sobre expressões lambda, confira o [Guia de Programação em C#](http://msdn.microsoft.com/en-us/library/vstudio/bb397687.aspx) da Microsoft.

## <a name="summary"></a>Resumo

Neste artigo, uma variedade de recursos de linguagem foram comparados entre Objective-C e C#. Em alguns casos, foram indicados recursos análogos que existem entre as duas linguagens, como blocos e expressões lambda e categorias e métodos de extensão. Além disso, ele comparou as diferenças entre as linguagens, como namespaces em C# e o significado da palavra-chave static.
