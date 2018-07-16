---
title: Usar o JSON para criar uma Interface do usuário no xamarin. IOS
description: MonoTouch (MT. D) inclui suporte para a geração dinâmica de interface do usuário por meio de dados JSON. Neste tutorial, vamos examinar como usar um JSONElement para criar uma interface do usuário do JSON que é incluído com um aplicativo, ou carregado a partir de uma Url remota.
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 94cef78bb7eedc03192071f17af765ebb702e260
ms.sourcegitcommit: cb80df345795989528e9df78eea8a5b45d45f308
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39038489"
---
# <a name="using-json-to-create-a-user-interface-in-xamarinios"></a>Usar o JSON para criar uma interface do usuário no xamarin. IOS

_MonoTouch (MT. D) inclui suporte para a geração dinâmica de interface do usuário por meio de dados JSON. Neste tutorial, vamos examinar como usar um JSONElement para criar uma interface do usuário do JSON que é incluído com um aplicativo, ou carregado a partir de uma Url remota._

MT. D oferece suporte a criar interfaces de usuário declarados em JSON. Quando os elementos são declarados usando JSON, MT. 1!d criará os elementos associados para você automaticamente. O JSON pode ser carregado de um arquivo local, um analisado `JsonObject` instância ou até mesmo uma Url remota.

MT. D oferece suporte a gama completa de recursos que estão disponíveis na API de elementos ao usar o JSON. Por exemplo, o aplicativo na seguinte captura de tela é completamente declarado usando JSON:

[![](json-element-walkthrough-images/01-load-from-file.png "Por exemplo, o aplicativo nesta captura de tela é declarado completamente usando JSON") ](json-element-walkthrough-images/01-load-from-file.png#lightbox) [ ![ ] (json-element-walkthrough-images/01-load-from-file.png "por exemplo, o aplicativo nesta captura de tela é declarado completamente usando JSON")](json-element-walkthrough-images/01-load-from-file.png#lightbox)

Vamos examinar novamente o exemplo a partir o [passo a passo de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) tutorial, mostrando como adicionar uma tela de detalhes da tarefa usando JSON.

## <a name="setting-up-mtd"></a>Configuração do MT. 1!D

MT. 1!d é distribuído com o xamarin. IOS. Para usá-lo, clique com botão direito no **referências** nó de um xamarin. IOS do projeto no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência para o **MonoTouch 1** assembly. Em seguida, adicione `using MonoTouch.Dialog` instruções em sua fonte de código conforme necessário.

## <a name="json-walkthrough"></a>Instruções passo a passo do JSON

O exemplo neste passo a passo permite a criação de tarefas. Quando uma tarefa é selecionada na primeira tela, uma tela de detalhes é apresentada como mostrado:

 [![](json-element-walkthrough-images/03-task-list.png "Quando uma tarefa é selecionada na primeira tela, uma tela de detalhes é apresentada como mostrado")](json-element-walkthrough-images/03-task-list.png#lightbox)

## <a name="creating-the-json"></a>Criando o JSON

Neste exemplo, vamos carregar o JSON de um arquivo no projeto chamado `task.json`. MT. 1!d espera que o JSON em conformidade com uma sintaxe que espelha a API de elementos. Assim como usando a API de elementos de código, ao usar o JSON, podemos declarar seções e nessas seções, podemos adicionar elementos. Para declarar seções e elementos em JSON, usamos as cadeias de caracteres "seções" e "elementos" respectivamente como as chaves. Para cada elemento, o tipo do elemento associado é definido usando o `type` chave. Todas as outras propriedades de elementos é definida com o nome da propriedade como a chave.

Por exemplo, o JSON a seguir descreve os elementos para os detalhes da tarefa e seções:

```csharp
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

Observe que o JSON acima inclui uma id para cada elemento. Qualquer elemento pode incluir uma id, para fazer referência a ele em tempo de execução. Veremos como isso é usado em um momento quando vamos mostrar como carregar o JSON no código.

## <a name="loading-the-json-in-code"></a>Carregando o JSON em código

Depois que o JSON tiver sido definido, precisamos carregá-los no MT. D usando o `JsonElement` classe. Supondo que um arquivo com o JSON que criamos acima foi adicionado ao projeto com o nome Sample e recebeu uma ação de build de conteúdo, carregando o `JsonElement` é tão simples quanto chamar a seguinte linha de código:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Uma vez que estamos adicionando isso sob demanda sempre que uma tarefa é criada, podemos modificar o botão clicado pelo exemplo anterior, API de elementos da seguinte maneira:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

## <a name="accessing-elements-at-runtime"></a>Acessando elementos em tempo de execução

Lembre-se de que adicionamos uma id para ambos os elementos quando declaramos no arquivo JSON. Podemos usar a propriedade id para acessar cada elemento em tempo de execução para modificar suas propriedades no código. Por exemplo, o código a seguir faz referência os elementos de entrada e a data para definir os valores do objeto de tarefa:

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

## <a name="loading-json-from-a-url"></a>Carregando o JSON de uma url

MT. D também oferece suporte carregamento dinâmico de JSON de uma Url externa, simplesmente passando a Url para o construtor do `JsonElement`. MT. 1!d expandirá a hierarquia declarada no JSON sob demanda, como navegar entre telas. Por exemplo, considere um arquivo JSON, como a mostrada abaixo localizado na raiz do servidor web local:

```csharp
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

Podemos pode carregar isso usando o `JsonElement` como no código a seguir:

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

Em tempo de execução, o arquivo será recuperado e analisado por MT. 1!d quando o usuário navega para a segunda exibição, conforme mostrado na captura de tela abaixo:

 [![](json-element-walkthrough-images/04-json-web-example.png "O arquivo será recuperado e analisado por MT. 1!d quando o usuário navega para a segunda exibição")](json-element-walkthrough-images/04-json-web-example.png#lightbox)

## <a name="summary"></a>Resumo

Este artigo mostrou como criar um usando a interface com MT. 1!d do JSON. Ele mostrou como a carga JSON incluído em um arquivo com o aplicativo, bem como uma Url remota. Ele também mostrou como acessar os elementos descritos em JSON em tempo de execução.

## <a name="related-links"></a>Links relacionados

- [MTDJsonDemo (sample)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces do usuário com MonoTouch](http://youtu.be/j7OC5r8ZkYg)
- [Introdução ao MonoTouch](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções passo a passo de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Instruções passo a passo de API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
