---
title: Usando o JSON para criar uma interface do usuário no Xamarin. iOS
description: MonoTouch. caixa de diálogo (MT. D) inclui suporte para geração de interface do usuário dinâmica por meio de dados JSON. Neste tutorial, veremos como usar um JSONelement para criar uma interface do usuário do JSON que está incluído em um aplicativo ou carregado a partir de uma URL remota.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: ad2386d912dba28041c02c4fb4a8046d341a85ed
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002261"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Usando o JSON para criar uma interface do usuário no Xamarin. iOS

_MonoTouch. caixa de diálogo (MT. D) inclui suporte para geração de interface do usuário dinâmica por meio de dados JSON. Neste tutorial, veremos como usar um JSONelement para criar uma interface do usuário do JSON que está incluído em um aplicativo ou carregado a partir de uma URL remota._

MT. D dá suporte à criação de interfaces de usuário declaradas em JSON. Quando os elementos são declarados usando JSON, MT. D criará os elementos associados automaticamente para você. O JSON pode ser carregado a partir de um arquivo local, uma instância de `JsonObject` analisada ou até mesmo uma URL remota.

MT. O D oferece suporte à gama completa de recursos que estão disponíveis na API de elementos ao usar o JSON. Por exemplo, o aplicativo na captura de tela a seguir é completamente declarado usando JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Por exemplo, o aplicativo nesta captura de tela é completamente declarado usando JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)[![](json-element-walkthrough-images/01-load-from-file.png "Por exemplo, o aplicativo nesta captura de tela é completamente declarado usando JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Vamos revisitar o exemplo do tutorial sobre a [API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) , mostrando como adicionar uma tela de detalhes da tarefa usando JSON.

## <a name="setting-up-mtd"></a>Configurando o MT. 3D

MT. A D é distribuída com o Xamarin. iOS. Para usá-lo, clique com o botão direito do mouse no nó **referências** de um projeto Xamarin. Ios no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência ao assembly **MonoTouch. caixa de diálogo-1** . Em seguida, adicione `using MonoTouch.Dialog` instruções em seu código-fonte, conforme necessário.

## <a name="json-walkthrough"></a>Instruções de JSON

O exemplo deste passo a passos permite que as tarefas sejam criadas. Quando uma tarefa é selecionada na primeira tela, uma tela de detalhes é apresentada como mostrado:

 [![](json-element-walkthrough-images/03-task-list.png "When a task is selected on the first screen, a detail screen is presented as shown")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Criando o JSON

Para este exemplo, carregaremos o JSON de um arquivo no projeto chamado `task.json`. MT. D espera que o JSON esteja em conformidade com uma sintaxe que espelhe a API dos elementos. Assim como o uso da API Elements do código, ao usar o JSON, declaramos seções e dentro dessas seções adicionamos elementos. Para declarar seções e elementos em JSON, usamos as cadeias de caracteres "Sections" e "Elements", respectivamente, como as chaves. Para cada elemento, o tipo de elemento associado é definido usando a chave `type`. Todas as outras propriedades de elementos são definidas com o nome da propriedade como a chave.

Por exemplo, o JSON a seguir descreve as seções e os elementos dos detalhes da tarefa:

```json
{
    "title": "Task",
    "sections": [
        {
            "elements" : [
                {
                    "id" : "task-description",
                    "type": "entry",
                    "placeholder": "Enter task description"
                },
                {
                    "id" : "task-duedate",
                    "type": "date",
                    "caption": "Due Date",
                    "value": "00:00"
                }
            ]
        }
    ]
}
```

Observe que o JSON acima inclui uma ID para cada elemento. Qualquer elemento pode incluir uma ID para fazer referência a ela em tempo de execução. Veremos como isso é usado em um momento quando mostramos como carregar o JSON no código.

## <a name="loading-the-json-in-code"></a>Carregando o JSON no código

Depois que o JSON tiver sido definido, precisamos carregá-lo em MT. D usando a classe `JsonElement`. Supondo que um arquivo com o JSON criado acima tenha sido adicionado ao projeto com o nome Sample. JSON e receba uma ação de compilação de conteúdo, carregar o `JsonElement` é tão simples quanto chamar a seguinte linha de código:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Como estamos adicionando isso sob demanda toda vez que uma tarefa é criada, podemos modificar o botão clicado no exemplo de API de elementos anteriores da seguinte maneira:

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>Acessando elementos em tempo de execução

Lembre-se de que adicionamos uma ID a ambos os elementos quando os declaramos no arquivo JSON. Podemos usar a propriedade ID para acessar cada elemento em tempo de execução para modificar suas propriedades no código. Por exemplo, o código a seguir faz referência aos elementos ENTRY e Date para definir os valores do objeto Task:

```csharp
_addButton.Clicked += (sender, e) => {
    ++n;

    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

    var taskElement = JsonElement.FromFile ("task.json");

    taskElement.Caption = task.Name;

    var description = taskElement ["task-description"] as EntryElement;

    if (description != null) {
        description.Caption = task.Name;
        description.Value = task.Description;       
    }

    var duedate = taskElement ["task-duedate"] as DateElement;

    if (duedate != null) {                
        duedate.DateValue = task.DueDate;
    }
    _rootElement [0].Add (taskElement);
};
```

## <a name="loading-json-from-a-url"></a>Carregando JSON de uma URL

MT. O D também dá suporte ao carregamento dinâmico de JSON a partir de uma URL externa simplesmente passando a URL para o construtor do `JsonElement`. MT. D expandirá a hierarquia declarada no JSON sob demanda à medida que você navegar entre telas. Por exemplo, considere um arquivo JSON como aquele abaixo localizado na raiz do servidor Web local:

```json
{
    "type": "root",
    "title": "home",
    "sections": [
        {
            "header": "Nested view!",
            "elements": [
                {
                    "type": "boolean",
                    "caption": "Just a boolean",
                    "id": "first-boolean",
                    "value": false
                },
                {
                    "type": "string",
                    "caption": "Welcome to the nested controller"
                }
            ]
        }
    ]
}
```

Podemos carregá-lo usando o `JsonElement` como no código a seguir:

```csharp
_rootElement = new RootElement ("Json Example") {
    new Section ("") {
        new JsonElement ("Load from url", "http://localhost/sample.json")
    }
};
```

Em tempo de execução, o arquivo será recuperado e analisado por MT. D quando o usuário navega para a segunda exibição, conforme mostrado na captura de tela abaixo:

 [![](json-element-walkthrough-images/04-json-web-example.png "The file will be retrieved and parsed by MT.D when the user navigates to the second view")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Resumo

Este artigo mostrou como criar um usando uma interface com MT. D do JSON. Ele mostrou como carregar JSON incluído em um arquivo com o aplicativo, bem como de uma URL remota. Ele também mostrou como acessar elementos descritos em JSON em tempo de execução.

## <a name="related-links"></a>Links relacionados

- [MTDJsonDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdjsondemo)
- [Introdução à caixa de diálogo MonoTouch.](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções da API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Explicação da API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Caixa de diálogo MonoTouch no github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
