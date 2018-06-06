---
title: Criando um aplicativo xamarin usando a API de elementos
description: Este artigo tem como base as informações apresentadas na introdução ao artigo MonoTouch caixa de diálogo. Ele apresenta uma explicação passo a passo que mostra como usar o MonoTouch.Dialog (MT. D) elementos API para começar rapidamente a criação de um aplicativo com MT. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 64f407288da72dbf51fb3388ddf36caea377f09c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790426"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Criando um aplicativo xamarin usando a API de elementos

_Este artigo tem como base as informações apresentadas na introdução ao artigo MonoTouch caixa de diálogo. Ele apresenta uma explicação passo a passo que mostra como usar o MonoTouch.Dialog (MT. D) elementos API para começar rapidamente a criação de um aplicativo com MT. D._

Neste passo a passo, usaremos o MT. API de elementos de D para criar um estilo de mestre-detalhes do aplicativo que exibe uma lista de tarefas. Quando o usuário seleciona o <span class="ui"> + </span> botão na barra de navegação, uma nova linha será adicionada à tabela para a tarefa. Selecionar a linha navegará para a tela de detalhes que nos permite atualizar a descrição da tarefa e a data de vencimento, conforme ilustrado abaixo:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Selecionar a linha navegará para a tela de detalhes que permite atualizar a descrição da tarefa e a data de vencimento")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 <a name="Elements_API_Walkthrough" />


## <a name="elements-api-walkthrough"></a>Instruções passo a passo de API de elementos

No [Introdução à caixa de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) artigo, estamos obtendo uma compreensão sólida de diferentes partes de MT. D. Vamos usar a API de elementos para colocá-los juntos em um aplicativo.

 <a name="Setting_up_the_Multi-Screen_Application" />


## <a name="setting-up-the-multi-screen-application"></a>Configurando o aplicativo de multi-tela

Para iniciar o processo de criação da tela, MonoTouch.Dialog cria um `DialogViewController`e, em seguida, adiciona um `RootElement`.

Para criar um aplicativo de multi-tela com MonoTouch.Dialog, é preciso:

1.  Criar um  `UINavigationController.`
1.  Criar um  `DialogViewController.`
1.  Adicionar o `DialogViewController` como a raiz das  `UINavigationController.` 
1.  Adicionar um `RootElement` para o  `DialogViewController.`
1.  Adicionar `Sections` e `Elements` para o  `RootElement.` 


 <a name="Using_A_UINavigationController" />


### <a name="using-a-uinavigationcontroller"></a>Usando um UINavigationController

Para criar um aplicativo estilo de navegação, precisamos criar um `UINavigationController`e, em seguida, adicioná-lo como o `RootViewController` no `FinishedLaunching` método o `AppDelegate`. Para fazer o `UINavigationController` trabalhar com MonoTouch.Dialog, adicionamos uma `DialogViewController` para o `UINavigationController` conforme mostrado abaixo:

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
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

O código acima cria uma instância de um `RootElement` e o transmite para o `DialogViewController`. O `DialogViewController` sempre tem um `RootElement` na parte superior da sua hierarquia. Neste exemplo, o `RootElement` é criada com a cadeia de caracteres "Lista de tarefas pendentes," que serve como o título na barra de navegação do controlador de navegação. Neste ponto, executando o aplicativo apresentar a tela abaixo:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Executando o aplicativo apresentará a tela mostrada aqui")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Vamos ver como usar a estrutura hierárquica do MonoTouch.Dialog de `Sections` e `Elements` para adicionar mais telas.

 <a name="Creating_the_Dialog_Screens" />


### <a name="creating-the-dialog-screens"></a>Criando telas a caixa de diálogo

Um `DialogViewController` é um `UITableViewController` subclasse MonoTouch.Dialog usa para adicionar telas. MonoTouch.Dialog cria telas adicionando um `RootElement` para um `DialogViewController`, conforme vimos acima. O `RootElement` pode ter `Section` instâncias que representam as seções de uma tabela.
As seções são compostas de elementos, outras seções, ou até mesmo outros `RootElements`. Aninhando `RootElements`, MonoTouch.Dialog cria automaticamente um aplicativo de navegação de estilo, como você verá em seguida.

 <a name="Using_DialogViewController" />


### <a name="using-dialogviewcontroller"></a>Usando DialogViewController

O `DialogViewController`, sendo uma `UITableViewController` subclasse, tem um `UITableView` como sua exibição. Neste exemplo, queremos adicionar itens à tabela de cada vez que o <span class="ui"> + </span> botão é tocado. Desde que o `DialogViewController` foi adicionado para um `UINavigationController`, podemos usar o `NavigationItem`do `RightBarButton` propriedade a ser adicionada a <span class="ui"> + </span> botão, conforme mostrado abaixo:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Quando criamos o `RootElement` anteriormente, passamos a ele um único `Section` instância para que poderíamos adicionar elementos como o <span class="ui"> + </span> botão tocado pelo usuário. Podemos usar o código a seguir para realizar nesse evento manipulador para o botão:

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

Esse código cria um novo `Task` objeto cada vez que o botão é tocado. A seguir mostra a implementação simple do `Task` classe:

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

 []()

A tarefa `Name` propriedade é usada para criar o `RootElement`da legenda junto com uma variável de contador chamada `n` que é incrementada para cada tarefa. MonoTouch.Dialog transforma os elementos em linhas que são adicionadas para o `TableView` quando cada `taskElement` é adicionado.

 <a name="Presenting_and_Managing_Dialog_Screens" />


## <a name="presenting-and-managing-dialog-screens"></a>Apresentando e telas de caixa de diálogo de gerenciamento

Usamos uma `RootElement` para que MonoTouch.Dialog seria criar uma nova tela de detalhes de cada tarefa e navegue até ele, quando uma linha é selecionada automaticamente.

A tela de detalhes de tarefas em si é composta de duas seções; cada uma dessas seções contém um único elemento. O primeiro elemento é criado a partir uma `EntryElement` para fornecer uma linha editável para a tarefa `Description` propriedade. Quando o elemento é selecionado, um teclado para edição de texto é apresentado como mostrado abaixo:

 [![](elements-api-walkthrough-images/03-create-task.png "Quando o elemento é selecionado, um teclado para edição de texto é apresentado como mostrado")](elements-api-walkthrough-images/03-create-task.png#lightbox)

A segunda seção contém um `DateElement` que nos permite gerenciar a tarefa `DueDate` propriedade. Selecionar a data automaticamente carrega um seletor de data, conforme mostrado:

 [![](elements-api-walkthrough-images/04-date-picker.png "Selecionar a data carregará automaticamente como um seletor de data")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

Em ambos os `EntryElement` e `DateElement` casos (ou para qualquer elemento de entrada de dados em MonoTouch.Dialog), as alterações para os valores são preservadas automaticamente. Podemos demonstrar isso editando a data e, em seguida, navegar e para trás entre a tela raiz e vários detalhes da tarefa, em que os valores nas telas detalhes são preservados.

 <a name="Summary" />


## <a name="summary"></a>Resumo

Este artigo apresentado um passo a passo que mostrou como usar a API de elementos MonoTouch.Dialog. Ele trata as etapas básicas para criar um aplicativo de multi-tela com MT. D, inclusive como usar um `DialogViewController` e como adicionar elementos e seções para criar telas. Além disso, ele mostrou como usar MT. D em conjunto com um `UINavigationController`.


## <a name="related-links"></a>Links relacionados

- [MTDWalkthrough (exemplo)](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces de usuário com MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introdução ao MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções passo a passo de API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Instruções passo a passo de elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
