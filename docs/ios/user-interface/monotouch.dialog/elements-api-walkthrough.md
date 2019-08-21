---
title: Criando um aplicativo Xamarin. iOS usando a API de elementos
description: Este artigo se baseia nas informações apresentadas no artigo da caixa de diálogo introdução ao MonoTouch. Ele apresenta uma explicação que mostra como usar a caixa de diálogo MonoTouch. (MT. D) a API de elementos para começar a criar rapidamente um aplicativo com o MT. 3D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 88823aa2d86b7cc5db72b3949453cd6aa464bd74
ms.sourcegitcommit: 3434624a36a369986b6aeed7959dae60f7112a14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69629634"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Criando um aplicativo Xamarin. iOS usando a API de elementos

_Este artigo se baseia nas informações apresentadas no artigo da caixa de diálogo introdução ao MonoTouch. Ele apresenta uma explicação que mostra como usar a caixa de diálogo MonoTouch. (MT. D) a API de elementos para começar a criar rapidamente um aplicativo com o MT. 3D._

Neste tutorial, usaremos o MT. A API D Elements para criar um estilo de detalhes mestre do aplicativo que exibe uma lista de tarefas. Quando o usuário selecionar o **+** botão na barra de navegação, uma nova linha será adicionada à tabela para a tarefa. A seleção da linha navegará até a tela de detalhes que nos permite atualizar a descrição da tarefa e a data de vencimento, conforme ilustrado abaixo:

[![](elements-api-walkthrough-images/01-task-list-app.png "A seleção da linha navegará até a tela de detalhes que nos permite atualizar a descrição da tarefa e a data de vencimento")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>Configurando o MT. 3D

MT. A D é distribuída com o Xamarin. iOS. Para usá-lo, clique com o botão direito do mouse no nó **referências** de um projeto Xamarin. Ios no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência ao assembly **MonoTouch. caixa de diálogo-1** . Em seguida, `using MonoTouch.Dialog` adicione instruções em seu código-fonte, conforme necessário.

## <a name="elements-api-walkthrough"></a>Instruções da API de elementos

No artigo da [caixa de diálogo introdução ao MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) , obtivemos uma compreensão sólida das diferentes partes do Mt. 3D. Vamos usar a API Elements para colocá-las juntas em um aplicativo.

## <a name="setting-up-the-multi-screen-application"></a>Configurando o aplicativo de várias telas

Para iniciar o processo de criação de tela, o MonoTouch. `DialogViewController`Dialog cria um e, `RootElement`em seguida, adiciona um.

Para criar um aplicativo de várias telas com MonoTouch. dialog, precisamos:

1. Criar um`UINavigationController.`
1. Criar um`DialogViewController.`
1. Adicione o `DialogViewController` como a raiz do`UINavigationController.` 
1. Adicione um `RootElement` ao`DialogViewController.`
1. Adicionar `Sections` e `Elements` ao`RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Usando um UINavigationController

Para criar um aplicativo estilo de navegação, `UINavigationController`precisamos criar um e, em seguida, adicioná-lo `RootViewController` como `AppDelegate`no `FinishedLaunching` método do. Para fazer com `UINavigationController` que o trabalhe com MonoTouch. caixa de diálogo `DialogViewController` , adicionamos uma ao `UINavigationController` conforme mostrado abaixo:

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    _rootElement = new RootElement ("To Do List"){new Section ()};

    // code to create screens with MT.D will go here …

    _rootVC = new DialogViewController (_rootElement);
    _nav = new UINavigationController (_rootVC);
    _window.RootViewController = _nav;
    _window.MakeKeyAndVisible ();
            
    return true;
}
```

O código acima cria uma instância de a `RootElement` e a transmite para o `DialogViewController`. O `DialogViewController` sempre tem um `RootElement` na parte superior de sua hierarquia. Neste exemplo, o `RootElement` é criado com a cadeia de caracteres "lista de tarefas pendentes", que serve como o título na barra de navegação do controlador de navegação. Neste ponto, a execução do aplicativo apresentaria a tela mostrada abaixo:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "A execução do aplicativo apresentará a tela mostrada aqui")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Vamos ver como usar a estrutura hierárquica do MonoTouch. da caixa de `Sections` diálogo `Elements` do e para adicionar mais telas.

### <a name="creating-the-dialog-screens"></a>Criando as telas de diálogo

Um `DialogViewController` é uma `UITableViewController` subclasse que o MonoTouch. Dialog usa para adicionar telas. MonoTouch. Dialog cria telas adicionando um `RootElement` `DialogViewController`a, como vimos acima. O `RootElement` pode ter `Section` instâncias que representam as seções de uma tabela.
As seções são constituídas de elementos, outras seções ou até mesmo outras `RootElements`. Ao aninhar `RootElements`, o MonoTouch. Dialog cria automaticamente um aplicativo estilo de navegação, como veremos a seguir.

### <a name="using-dialogviewcontroller"></a>Usando DialogViewController

O `DialogViewController`, sendo uma `UITableViewController` subclasse, tem um `UITableView` como sua exibição. Neste exemplo, queremos adicionar itens à tabela cada vez que o **+** botão é tocado. Como o `DialogViewController` foi adicionado a um `UINavigationController`, podemos usar a `NavigationItem`Propriedade do `RightBarButton` para adicionar o **+** botão, como mostrado abaixo:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Quando criamos o `RootElement` anterior, passamos a ele uma `Section` única instância para que possamos adicionar elementos à medida **+** que o botão é tocado pelo usuário. Podemos usar o código a seguir para fazer isso no manipulador de eventos para o botão:

```csharp
_addButton.Clicked += (sender, e) => {                
    ++n;
                
    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
    var taskElement = new RootElement (task.Name) {
        new Section () {
            new EntryElement (task.Name, "Enter task description", task.Description)
        },
        new Section () {
            new DateElement ("Due Date", task.DueDate)
        }
    };
    _rootElement [0].Add (taskElement);
};
```

Esse código cria um novo `Task` objeto cada vez que o botão é tocado. O seguinte mostra a implementação simples da `Task` classe:

```csharp
public class Task
{   
    public Task ()
    {
    }
      
    public string Name { get; set; }
        
    public string Description { get; set; }

    public DateTime DueDate { get; set; }
}
```

A propriedade da `Name` tarefa é usada para criar a `RootElement`legenda do, junto com uma variável de `n` contador chamada que é incrementada para cada nova tarefa. MonoTouch. Dialog transforma os elementos nas linhas que são adicionadas `TableView` ao quando cada um `taskElement` é adicionado.

## <a name="presenting-and-managing-dialog-screens"></a>Apresentação e gerenciamento de telas de diálogo

Usamos um `RootElement` para que MonoTouch. Dialog crie automaticamente uma nova tela para os detalhes de cada tarefa e navegue até ela quando uma linha é selecionada.

A própria tela de detalhes da tarefa é composta por duas seções; cada uma dessas seções contém um único elemento. O primeiro elemento é criado a partir `EntryElement` de um para fornecer uma linha editável para a `Description` propriedade da tarefa. Quando o elemento é selecionado, um teclado para edição de texto é apresentado como mostrado abaixo:

 [![](elements-api-walkthrough-images/03-create-task.png "Quando o elemento é selecionado, um teclado para edição de texto é apresentado conforme mostrado")](elements-api-walkthrough-images/03-create-task.png#lightbox)

A segunda seção contém um `DateElement` que nos permite gerenciar a propriedade da `DueDate` tarefa. A seleção da data carrega automaticamente um seletor de data conforme mostrado:

 [![](elements-api-walkthrough-images/04-date-picker.png "A seleção da data carrega automaticamente um seletor de data como")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

Nos casos `DateElement` e (ou para qualquer elemento de entrada de dados em MonoTouch. Dialog), todas as alterações nos valores são preservadas automaticamente. `EntryElement` Podemos demonstrar isso editando a data e, em seguida, navegando para frente e para trás entre a tela raiz e vários detalhes da tarefa, em que os valores nas telas de detalhes são preservados.

## <a name="summary"></a>Resumo

Este artigo apresentou uma explicação que mostrou como usar a API de elementos de caixa de diálogo MonoTouch. Ele abordou as etapas básicas para criar um aplicativo de várias telas com o MT. D, incluindo como usar um `DialogViewController` e como adicionar elementos e seções para criar telas. Além disso, ele mostrou como usar o MT. D em conjunto com um `UINavigationController`.

## <a name="related-links"></a>Links relacionados

- [MTDWalkthrough (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Introdução à caixa de diálogo MonoTouch.](~/ios/user-interface/monotouch.dialog/index.md)
- [Explicação da API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Instruções do elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Caixa de diálogo MonoTouch no github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
