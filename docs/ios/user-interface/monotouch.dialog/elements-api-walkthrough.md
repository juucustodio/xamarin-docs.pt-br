---
title: Criando um aplicativo xamarin. IOS usando a API de elementos
description: Este artigo tem como base as informações apresentadas na introdução ao artigo de caixa de diálogo MonoTouch. Ele apresenta um passo a passo que mostra como usar o MonoTouch (MT. D) elementos de API para rapidamente começar a criar um aplicativo com MT. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: dcd6f1260be3414c515010c2fd615910c7b5c054
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038336"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Criando um aplicativo xamarin. IOS usando a API de elementos

_Este artigo tem como base as informações apresentadas na introdução ao artigo de caixa de diálogo MonoTouch. Ele apresenta um passo a passo que mostra como usar o MonoTouch (MT. D) elementos de API para rapidamente começar a criar um aplicativo com MT. D._

Neste passo a passo, usaremos o MT. API de elementos de D para criar um estilo de mestre / detalhes do aplicativo que exibe uma lista de tarefas. Quando o usuário seleciona o <span class="ui"> + </span> botão na barra de navegação, uma nova linha será adicionada à tabela para a tarefa. Selecionar a linha navegará para a tela de detalhes que nos permite atualizar a descrição da tarefa e a data de vencimento, conforme ilustrado abaixo:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Selecionar a linha navegará para a tela de detalhes que nos permite atualizar a descrição da tarefa e a data de vencimento")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>Configuração do MT. 1!D

MT. 1!d é distribuído com o xamarin. IOS. Para usá-lo, clique com botão direito no **referências** nó de um xamarin. IOS do projeto no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência para o **MonoTouch 1** assembly. Em seguida, adicione `using MonoTouch.Dialog` instruções em sua fonte de código conforme necessário.

## <a name="elements-api-walkthrough"></a>Instruções passo a passo de elementos de API

No [Introdução à caixa de diálogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) artigo, obtivemos uma compreensão sólida de diferentes partes do MT. D. Vamos usar a API de elementos para colocá-los juntos em um aplicativo.

## <a name="setting-up-the-multi-screen-application"></a>Como configurar o aplicativo com várias tela

Para iniciar o processo de criação de tela, MonoTouch cria uma `DialogViewController`e, em seguida, adiciona um `RootElement`.

Para criar um aplicativo com várias tela com MonoTouch, é preciso:

1.  Criar um `UINavigationController.`
1.  Criar um `DialogViewController.`
1.  Adicionar o `DialogViewController` como a raiz das  `UINavigationController.` 
1.  Adicionar um `RootElement` para o  `DialogViewController.`
1.  Adicione `Sections` e `Elements` para o  `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Usando um UINavigationController

Para criar um aplicativo de estilo de navegação, precisamos criar uma `UINavigationController`e, em seguida, adicioná-lo como o `RootViewController` na `FinishedLaunching` método da `AppDelegate`. Para tornar o `UINavigationController` trabalhar com MonoTouch, podemos adicionar um `DialogViewController` para o `UINavigationController` conforme mostrado abaixo:

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

O código anterior cria uma instância de um `RootElement` e o passa para o `DialogViewController`. O `DialogViewController` sempre tem um `RootElement` na parte superior da sua hierarquia. Neste exemplo, o `RootElement` é criado com a cadeia de caracteres "Lista de tarefas pendentes," que serve como o título na barra de navegação do controlador de navegação. Neste ponto, executando o aplicativo poderia apresentar a tela mostrada abaixo:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Executando o aplicativo apresentará a tela mostrada aqui")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Vamos ver como usar uma estrutura hierárquica do MonoTouch `Sections` e `Elements` para adicionar mais telas.

### <a name="creating-the-dialog-screens"></a>Criar as telas de diálogo

Um `DialogViewController` é um `UITableViewController` subclasse MonoTouch usa para adicionar telas. MonoTouch cria telas, adicionando um `RootElement` para um `DialogViewController`, como vimos anteriormente. O `RootElement` pode ter `Section` instâncias que representam as seções de uma tabela.
As seções são compostas de elementos, outras seções, ou até mesmo outros `RootElements`. Aninhando `RootElements`, MonoTouch cria automaticamente um aplicativo de estilo de navegação, como veremos em seguida.

### <a name="using-dialogviewcontroller"></a>Usando DialogViewController

O `DialogViewController`, sendo uma `UITableViewController` subclasse, tem um `UITableView` como sua exibição. Neste exemplo, queremos adicionar itens à tabela de cada vez que o <span class="ui"> + </span> botão é tocado. Uma vez que o `DialogViewController` foi adicionado a uma `UINavigationController`, podemos usar o `NavigationItem`do `RightBarButton` propriedade a ser adicionada a <span class="ui"> + </span> botão, conforme mostrado abaixo:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Quando criamos o `RootElement` anterior, passamos a ela uma única `Section` instância forma que pudéssemos adicionar elementos como o <span class="ui"> + </span> botão é tocado pelo usuário. Podemos usar o código a seguir para realizar no evento manipulador do botão:

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

Esse código cria um novo `Task` objeto cada vez que o botão é tocado. A seguir mostra a implementação simple dos `Task` classe:

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

A tarefa `Name` propriedade é usada para criar o `RootElement`da legenda, juntamente com uma variável de contador chamada `n` que é incrementado para cada tarefa. MonoTouch transforma os elementos em linhas que são adicionadas para o `TableView` quando cada `taskElement` é adicionado.

## <a name="presenting-and-managing-dialog-screens"></a>Apresentação e gerenciamento de telas de diálogo

Usamos um `RootElement` para que o MonoTouch seria criar uma nova tela para obter detalhes de cada tarefa e navegue até ele, quando uma linha é selecionada automaticamente.

A tela de detalhes de tarefa em si é composta por duas seções; cada uma dessas seções contém um único elemento. O primeiro elemento é criado de um `EntryElement` para fornecer uma linha editável para a tarefa `Description` propriedade. Quando o elemento é selecionado, um teclado para edição de texto é apresentado como mostrado abaixo:

 [![](elements-api-walkthrough-images/03-create-task.png "Quando o elemento é selecionado, um teclado para edição de texto é apresentado como mostrado")](elements-api-walkthrough-images/03-create-task.png#lightbox)

A segunda seção contém um `DateElement` que nos permite gerenciar a tarefa `DueDate` propriedade. Selecionar a data automaticamente carrega um seletor de data, conforme mostrado:

 [![](elements-api-walkthrough-images/04-date-picker.png "Selecionar a data automaticamente carrega um seletor de data como")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

Em ambos os `EntryElement` e `DateElement` casos (ou de qualquer elemento de entrada de dados no MonoTouch), todas as alterações para os valores são preservadas automaticamente. Podemos demonstrar isso editando a data e, em seguida, navegar e voltar entre a tela raiz e vários detalhes da tarefa, em que os valores nas telas de detalhe são preservados.

## <a name="summary"></a>Resumo

Este artigo apresentou um passo a passo que mostrou como usar a API de elementos MonoTouch. Ele abordou as etapas básicas para criar um aplicativo com várias tela com MT. 1!d, incluindo como usar um `DialogViewController` e como adicionar elementos e seções para criar telas. Além disso, ele mostrou como usar MT. 1!d em conjunto com um `UINavigationController`.

## <a name="related-links"></a>Links relacionados

- [MTDWalkthrough (amostra)](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces do usuário com MonoTouch](http://youtu.be/j7OC5r8ZkYg)
- [Introdução ao MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções passo a passo de API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Passo a passo de elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
