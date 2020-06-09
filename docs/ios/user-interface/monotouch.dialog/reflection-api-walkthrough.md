---
title: Criando um aplicativo Xamarin. iOS usando a API de reflexão
description: Este documento descreve a API de reflexão baseada em atributo MonoTouch. dialog, que cria a interface do usuário com base em classes decoradas com atributos.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 1a6391c0e626c60fe35acee61f55f2f202f077b8
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573437"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>Criando um aplicativo Xamarin. iOS usando a API de reflexão

O MT. A API de reflexão D permite que as classes sejam decoradas com atributos que MT. D usa para criar telas automaticamente. A API de reflexão fornece uma associação entre essas classes e o que é exibido na tela. Embora essa API não forneça o controle refinado que a API de elementos faz, ela reduz a complexidade ao criar automaticamente a hierarquia de elementos com base na decoração de classes.

## <a name="setting-up-mtd"></a>Configurando o MT. 3D

MT. A D é distribuída com o Xamarin. iOS. Para usá-lo, clique com o botão direito do mouse no nó **referências** de um projeto Xamarin. Ios no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência ao assembly **MonoTouch. caixa de diálogo-1** . Em seguida, adicione `using MonoTouch.Dialog` instruções em seu código-fonte, conforme necessário.

## <a name="getting-started-with-the-reflection-api"></a>Introdução à API de reflexão

Usar a API de reflexão é tão simples quanto:

1. Criando uma classe decorada com MT. Atributos de D.
1. Criar uma `BindingContext` instância, passando uma instância da classe acima. 
1. Criando um `DialogViewController` , passando-o para o `BindingContext’s` `RootElement` . 

Vejamos um exemplo para ilustrar como usar a API de reflexão. Neste exemplo, criaremos uma tela simples de entrada de dados, conforme mostrado abaixo:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "In this example, we'll build a simple data entry screen as shown here")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>Criando uma classe com MT. Atributos de D

A primeira coisa que precisamos usar a API de reflexão é uma classe decorada com atributos. Esses atributos serão usados pelo MT. D internamente para criar objetos a partir da API de elementos. Por exemplo, considere a seguinte definição de classe:

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

O `SectionAttribute` resultará em seções do `UITableView` que estão sendo criadas, com o argumento de cadeia de caracteres usado para preencher o cabeçalho da seção. Depois que uma seção é declarada, cada campo que a segue será incluído nessa seção até que outra seção seja declarada.
O tipo de elemento de interface do usuário criado para o campo dependerá do tipo do campo e do MT. Atributo D decoração dele.

Por exemplo, o `Name` campo é um `string` e é decorado com um `EntryAttribute` . Isso resulta em uma linha adicionada à tabela com um campo de entrada de texto e a legenda especificada. Da mesma forma, o `IsApproved` campo é um `bool` com `CheckboxAttribute` , resultando em uma linha de tabela com uma caixa de seleção à direita da célula da tabela. MT. D usa o nome do campo, adicionando automaticamente um espaço para criar a legenda nesse caso, já que ele não é especificado em um atributo.

## <a name="adding-the-bindingcontext"></a>Adicionando o BindingContext

Para usar a `Expense` classe, precisamos criar um `BindingContext` . Um `BindingContext` é uma classe que associará os dados da classe atribuída para criar a hierarquia de elementos. Para criar um, basta instanciá-lo e passar uma instância da classe atribuída para o construtor.

Por exemplo, para adicionar a interface do usuário que declaramos usando o atributo na `Expense` classe, inclua o seguinte código no `FinishedLaunching` método do `AppDelegate` :

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Então, tudo o que precisamos fazer para criar a interface do usuário é adicionar o `BindingContext` ao `DialogViewController` e defini-lo como o `RootViewController` da janela, como mostrado abaixo:

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
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

Executar o aplicativo agora faz com que a tela mostrada acima seja exibida.

### <a name="adding-a-uinavigationcontroller"></a>Adicionando um UINavigationController

No entanto, observe que o título "criar uma tarefa" que passamos para o `BindingContext` não é exibido. Isso ocorre porque o `DialogViewController` não faz parte de um `UINavigatonController` . Vamos alterar o código para adicionar um `UINavigationController` como a janela `RootViewController,` e adicionar o `DialogViewController` como a raiz do `UINavigationController` , conforme mostrado abaixo:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Agora, quando executamos o aplicativo, o título é exibido na `UINavigationController’s` barra de navegação, conforme mostrado na captura de tela abaixo:

 [![](reflection-api-walkthrough-images/02-create-task.png "Now when we run the application, the title appears in the UINavigationControllers navigation bar")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Ao incluir um `UINavigationController` , agora podemos aproveitar outros recursos do Mt. D para o qual a navegação é necessária. Por exemplo, podemos adicionar uma enumeração à `Expense` classe para definir a categoria para a despesa e Mt. D criará automaticamente uma tela de seleção. Para demonstrar, modifique a `Expense` classe para incluir um `ExpenseCategory` campo da seguinte maneira:

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

Executar o aplicativo agora resulta em uma nova linha na tabela para a categoria, conforme mostrado:

 [![](reflection-api-walkthrough-images/03-set-details.png "Running the application now results in a new row in the table for the category as shown")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

Selecionar os resultados da linha no aplicativo navegando para uma nova tela com linhas correspondentes à enumeração, conforme mostrado abaixo:

 [![](reflection-api-walkthrough-images/04-set-category.png "Selecting the row results in the application navigating to a new screen with rows corresponding to the enumeration")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo apresentou uma explicação da API de reflexão. Mostramos como adicionar atributos a uma classe para controlar o que é exibido. Também discutimos como usar um `BindingContext` para associar dados de uma classe à hierarquia de elementos que é criada, bem como usar o Mt. D com um `UINavigationController` .

## <a name="related-links"></a>Links relacionados

- [MTDReflectionWalkthrough (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdreflectionwalkthrough)
- [Introdução à caixa de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções da API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Instruções do elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Caixa de diálogo MonoTouch no github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
