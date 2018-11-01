---
title: Criando um aplicativo xamarin. IOS usando a API de reflexão
description: Este documento descreve a MonoTouch baseada em atributo reflexo API, que cria a interface do usuário com base em classes decoradas com atributos.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 9ea31d977352c5cc9609136c74099c99c08bdc30
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675169"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Criando um aplicativo xamarin. IOS usando a API de reflexão

O MT. API de reflexão de D permite que classes ser decorado com atributos que MT. D usa para criar telas automaticamente. A API de reflexão fornece uma associação entre essas classes e o que é exibido na tela. Embora essa API não fornece um controle refinado do que faz os elementos de API, reduz a complexidade criando automaticamente a hierarquia de elementos com base em decoração de classe.

## <a name="setting-up-mtd"></a>Configuração do MT. 1!D

MT. 1!d é distribuído com o xamarin. IOS. Para usá-lo, clique com botão direito no **referências** nó de um xamarin. IOS do projeto no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência para o **MonoTouch 1** assembly. Em seguida, adicione `using MonoTouch.Dialog` instruções em sua fonte de código conforme necessário.

## <a name="getting-started-with-the-reflection-api"></a>Guia de Introdução com a API de reflexão

Usando a API de reflexão é tão simple quanto:

1.  Criando uma classe decorada com MT. Atributos de D.
1.  Criando um `BindingContext` instância, passando uma instância da classe acima. 
1.  Criando um `DialogViewController` , passando-o `BindingContext’s` `RootElement` . 


Vejamos um exemplo para ilustrar como usar a API de reflexão. Neste exemplo, vamos criar uma tela de entrada de dados simples como mostrado abaixo:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "Neste exemplo, vamos criar uma tela de entrada de dados simples como mostrado aqui")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Criando uma classe com MT. Atributos de D

A primeira coisa que precisamos usar a API de reflexão é uma classe decorada com atributos. Esses atributos serão usados pelo MT. 1!d internamente para criar objetos na API de elementos. Por exemplo, considere a seguinte definição de classe:

```csharp
public class Expense
{
        [Section("Expense Entry")]

        [Entry("Enter expense name")]
        public string Name;
        
        [Section("Expense Details")]
  
        [Caption("Description")]
        [Entry]
        public string Details;
        
        [Checkbox]
        public bool IsApproved = true;
}
```

O `SectionAttribute` resultará em seções o `UITableView` que está sendo criado, com o argumento de cadeia de caracteres usado para popular o cabeçalho da seção. Depois que uma seção for declarada, todos os campos que se segue a ele serão incluídos nessa seção, até que outra seção é declarada.
O tipo de elemento de interface do usuário criado para o campo varia de acordo com o tipo do campo e do MT. Atributo D decorando-o.

Por exemplo, o `Name` campo é um `string` e ele é decorado com um `EntryAttribute`. Isso resulta em uma linha que está sendo adicionada à tabela com um campo de entrada de texto e a legenda especificada. Da mesma forma, o `IsApproved` campo é um `bool` com um `CheckboxAttribute`, resultando em uma linha da tabela com uma caixa de seleção à direita da célula da tabela. MT. D usa o nome do campo, adicionando automaticamente um espaço, para criar a legenda nesse caso, uma vez que ele não for especificado em um atributo.

## <a name="adding-the-bindingcontext"></a>Adicionando o BindingContext

Para usar o `Expense` classe, precisamos criar um `BindingContext`. Um `BindingContext` é uma classe que associará os dados da classe atribuída para criar a hierarquia de elementos. Para criar um, simplesmente instanciá-la e passar uma instância da classe atribuída para o construtor.

Por exemplo, para adicionar a interface do usuário que são declarados usando o atributo na `Expense` classe, inclua o seguinte código na `FinishedLaunching` método da `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Tudo o que precisamos fazer para criar a interface do usuário é adicionar o `BindingContext` para o `DialogViewController` e defina-o como o `RootViewController` da janela, conforme mostrado abaixo:

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
{
   
        window = new UIWindow (UIScreen.MainScreen.Bounds);
            
        var expense = new Expense ();
        var bctx = new BindingContext (null, expense, "Create a task");
        var dvc = new DialogViewController (bctx.Root);
            
        window.RootViewController = dvc;
        window.MakeKeyAndVisible ();
            
        return true;
}
```

Executando o aplicativo agora resulta na tela mostrada acima seja exibida.

### <a name="adding-a-uinavigationcontroller"></a>Adicionando um UINavigationController

Observe entretanto que o título "cria uma tarefa" que passamos para o `BindingContext` não é exibido. Isso ocorre porque o `DialogViewController` é não faz parte de um `UINavigatonController`. Vamos alterar o código para adicionar um `UINavigationController` como a janela `RootViewController,` e adicione o `DialogViewController` como a raiz do `UINavigationController` conforme mostrado abaixo:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Agora, quando executamos o aplicativo, o título é exibido no `UINavigationController’s` barra de navegação como a captura de tela abaixo mostra:

 [![](reflection-api-walkthrough-images/02-create-task.png "Agora quando executamos o aplicativo, o título é exibido na barra de navegação UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Com a inclusão de um `UINavigationController`, agora podemos pode tirar proveito de outros recursos do MT. 1!d para o qual a navegação é necessário. Por exemplo, podemos adicionar uma enumeração para o `Expense` classe para definir a categoria de despesas e MT. 1!d criará automaticamente uma tela de seleção. Para demonstrar, modifique a `Expense` classe para incluir um `ExpenseCategory` campo da seguinte maneira:

```csharp
public enum Category
{
        Travel,
        Lodging,
        Books
}
        
public class Expense
{
        …

        [Caption("Category")]
        public Category ExpenseCategory;
}
```

Executando o aplicativo agora resulta em uma nova linha na tabela para a categoria, conforme mostrado:

 [![](reflection-api-walkthrough-images/03-set-details.png "Executando o aplicativo agora resulta em uma nova linha na tabela para a categoria, conforme mostrado")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Selecionar a linha de resultado o aplicativo navegando até uma nova tela com linhas que correspondem à enumeração, conforme mostrado abaixo:

 [![](reflection-api-walkthrough-images/04-set-category.png "Selecionar a linha resulta na navegação de aplicativo para uma nova tela com linhas que correspondem à enumeração")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Resumo

Este artigo apresentou um passo a passo da API de reflexão. Mostramos como adicionar atributos a uma classe para controlar o que é exibido. Também discutimos como usar um `BindingContext` para associar dados de uma classe para a hierarquia de elemento que é criada, e também como MT uso. 1!d com um `UINavigationController`.


## <a name="related-links"></a>Links relacionados

- [MTDReflectionWalkthrough (amostra)](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces do usuário com MonoTouch](http://youtu.be/j7OC5r8ZkYg)
- [Introdução à caixa de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções passo a passo de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Passo a passo de elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
