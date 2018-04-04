---
title: 'Passo a passo: Criando um aplicativo usando a API de reflexão'
description: Além da API de elementos, MonoTouch.Dialog (MT. D) também inclui uma API de reflexão com base em atributo. Criando telas com MT. faz com que a API de reflexão D tão fácil quanto a decoração de classes com atributos. Este artigo fornece um passo a passo mostra como criar um aplicativo usando a API de reflexão.
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: e56eaeccb2e09d9f1ad84245bf41e2a4bf1b56f1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough-creating-an-application-using-the-reflection-api"></a>Passo a passo: Criando um aplicativo usando a API de reflexão

_Além da API de elementos, MonoTouch.Dialog (MT. D) também inclui uma API de reflexão com base em atributo. Criando telas com MT. faz com que a API de reflexão D tão fácil quanto a decoração de classes com atributos. Este artigo fornece um passo a passo mostra como criar um aplicativo usando a API de reflexão._


O MT. API de reflexão D permite que classes ser decorado com atributos que MT. D usa para criar telas automaticamente. A API de reflexão fornece uma associação entre essas classes e o que é exibido na tela. Embora essa API não fornece um controle refinado do que os elementos de API, ele reduz a complexidade criando automaticamente a hierarquia de elementos com base em decoração de classe.

 <a name="Getting_Started_with_the_Reflection_API" />


## <a name="getting-started-with-the-reflection-api"></a>Introdução à API de reflexão

Usando a API de reflexão é tão simple quanto:

1.  Criando uma classe decorada com MT. Atributos de D.
1.  Criando um `BindingContext` instância, passando uma instância da classe acima. 
1.  Criando um `DialogViewController` , passando o `BindingContext’s` `RootElement` . 


Vejamos um exemplo para ilustrar como usar a API de reflexão. Neste exemplo, criaremos uma tela de entrada de dados simples como mostrado abaixo:

 [![](reflection-api-walkthrough-images/01-expense-entry.png "Neste exemplo, criaremos uma tela de entrada de dados simples como mostrado aqui")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

 <a name="Creating_a_Class_with_MT.D_Attributes" />


## <a name="creating-a-class-with-mtd-attributes"></a>Criando uma classe com MT. Atributos de D

A primeira coisa que precisamos usar a API de reflexão é uma classe decorada com atributos. Esses atributos serão usados por MT. Objetos 3D internamente para criar da API de elementos. Por exemplo, considere a seguinte definição de classe:

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

O `SectionAttribute` resultará nas seções de `UITableView` que está sendo criado com o argumento de cadeia de caracteres usado para popular o cabeçalho da seção. Depois que uma seção é declarada, cada campo que o segue será incluído nessa seção, até que outra seção foi declarada.
O tipo de elemento de interface de usuário criado para o campo dependem do tipo do campo e o MT. Atributo D decorando-o.

Por exemplo, o `Name` campo é um `string` e ela está decorada com um `EntryAttribute`. Isso resulta em uma linha que está sendo adicionada à tabela com um campo de entrada de texto e a legenda especificada. Da mesma forma, o `IsApproved` campo é um `bool` com um `CheckboxAttribute`, resultando em uma linha da tabela com uma caixa de seleção à direita da célula da tabela. MT. D usa o nome do campo, automaticamente adicionando um espaço, para criar a legenda nesse caso, pois ele não for especificado em um atributo.

 <a name="Adding_the_BindingContext" />


## <a name="adding-the-bindingcontext"></a>Adicionando BindingContext

Para usar o `Expense` classe, precisamos criar um `BindingContext`. A `BindingContext` é uma classe que irá associar os dados da classe de atributo para criar a hierarquia de elementos. Para criar um, simplesmente instanciá-la e passar uma instância da classe atribuída ao construtor.

Por exemplo, para adicionar a interface do usuário que são declarados usando o atributo no `Expense` classe, inclua o seguinte código no `FinishedLaunching` método do `AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

Tudo o que precisamos fazer para criar a interface do usuário é adicionar a `BindingContext` para o `DialogViewController` e defina-o como o `RootViewController` da janela, conforme mostrado abaixo:

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

 <a name="Adding_a_UINavigationController" />


### <a name="adding-a-uinavigationcontroller"></a>Adicionando um UINavigationController

No entanto observe que o título "cria uma tarefa" que é passado para o `BindingContext` não é exibido. Isso ocorre porque o `DialogViewController` é não fazem parte de um `UINavigatonController`. Vamos alterar o código para adicionar um `UINavigationController` como a janela `RootViewController,` e adicione o `DialogViewController` como a raiz do `UINavigationController` conforme mostrado abaixo:

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

Agora, quando o aplicativo é executado, o título é exibido no `UINavigationController’s` barra de navegação como a captura de tela abaixo mostra:

 [![](reflection-api-walkthrough-images/02-create-task.png "Agora quando o aplicativo é executado, o título é exibido na barra de navegação UINavigationControllers")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

Incluindo um `UINavigationController`, agora podem tirar proveito de outros recursos do MT. D para o qual a navegação é necessária. Por exemplo, podemos adicionar uma enumeração para o `Expense` classe para definir a categoria de despesas e MT. D criará automaticamente uma tela de seleção. Para demonstrar, modifique o `Expense` classe para incluir um `ExpenseCategory` campo da seguinte maneira:

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

Selecionar a linha resulta no aplicativo navegando para uma nova tela com linhas que correspondem à enumeração, conforme mostrado abaixo:

 [![](reflection-api-walkthrough-images/04-set-category.png "Selecionar a linha resulta no aplicativo navegando para uma nova tela com linhas que correspondem à enumeração")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>Resumo

Este artigo apresentado um passo a passo da API de reflexão. Mostramos como adicionar atributos a uma classe para controlar o que é exibido. Também discutimos como usar um `BindingContext` para associar dados de uma classe para a hierarquia de elemento que é criada, e como MT use. D com um `UINavigationController`.


## <a name="related-links"></a>Links relacionados

- [MTDReflectionWalkthrough (exemplo)](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces de usuário com MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introdução à caixa de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções passo a passo de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Instruções passo a passo de elemento JSON](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
