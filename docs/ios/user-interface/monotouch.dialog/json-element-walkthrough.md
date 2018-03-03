---
title: "Passo a passo: Usando um elemento de JSON para criar uma Interface do usuário"
description: "MonoTouch.Dialog (MT. D) inclui suporte para geração dinâmica de interface do usuário por meio de dados JSON. Neste tutorial, examinaremos como usar um JSONElement para criar uma interface de usuário do JSON que é incluído com um aplicativo ou carregado de uma Url remota."
ms.topic: article
ms.prod: xamarin
ms.assetid: E353DF14-51D7-98E3-59EA-16683C770C23
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 63faa0c46abfb509a6834efa647f23ad0ed7f454
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough-using-a-json-element-to-create-a-user-interface"></a>Passo a passo: Usando um elemento de JSON para criar uma Interface do usuário

_MonoTouch.Dialog (MT. D) inclui suporte para geração dinâmica de interface do usuário por meio de dados JSON. Neste tutorial, examinaremos como usar um JSONElement para criar uma interface de usuário do JSON que é incluído com um aplicativo ou carregado de uma Url remota._


MT. D oferece suporte a criar interfaces de usuário declaradas em JSON. Quando os elementos são declarados usando JSON, MT. D criará os elementos associados para você automaticamente. O JSON pode ser carregado de um arquivo local, um analisado `JsonObject` instância ou até mesmo uma Url remota.

MT. D oferece suporte a gama completa de recursos que estão disponíveis na API de elementos ao usar JSON. Por exemplo, o aplicativo na seguinte captura de tela é completamente declarado usando JSON:

[ ![](json-element-walkthrough-images/01-load-from-file.png "Por exemplo, o aplicativo nesta captura de tela é declarado completamente usando JSON") ](json-element-walkthrough-images/01-load-from-file.png) [ ![ ] (json-element-walkthrough-images/02-load-from-file-details.png "por exemplo, o aplicativo nesta captura de tela é declarado completamente usando JSON")](json-element-walkthrough-images/02-load-from-file-details.png)

Vamos examinar novamente o exemplo da [passo a passo de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) tutorial, mostrando como adicionar uma tela de detalhes da tarefa usando JSON.

## <a name="json-walkthrough"></a>Instruções passo a passo JSON

O exemplo neste passo a passo permite a criação de tarefas. Quando uma tarefa está selecionada na primeira tela, uma tela de detalhes é apresentada como mostrado:

 [ ![](json-element-walkthrough-images/03-task-list.png "Quando uma tarefa está selecionada na primeira tela, uma tela de detalhes é apresentada como mostrado")](json-element-walkthrough-images/03-task-list.png)

## <a name="creating-the-json"></a>Criando o JSON

Neste exemplo, carregaremos o JSON de um arquivo no projeto nomeado `task.json`. MT. D espera o JSON de acordo com uma sintaxe que reflete a API de elementos. Como usar a API de elementos de código, ao usar JSON, declaramos seções e nessas seções, adicionamos elementos. Para declarar seções e elementos em JSON, usamos as cadeias de caracteres "seções" e "elementos" respectivamente como as chaves. Para cada elemento, o tipo de elemento associado é definido usando o `type` chave. Todas as outras propriedades de elementos é definida com o nome da propriedade como a chave.

Por exemplo, o JSON a seguir descreve as seções e os elementos para os detalhes da tarefa:

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

Observe o JSON acima inclui uma id para cada elemento. Qualquer elemento pode incluir uma id, para fazer referência a ele em tempo de execução. Vamos ver como isso é usado em um momento quando vamos mostrar como carregar o JSON no código.

 <a name="Loading_the_JSON_in_Code" />


## <a name="loading-the-json-in-code"></a>Carregando o JSON no código

Depois que o JSON tiver sido definido, precisamos carregá-lo no MT. D usando o `JsonElement` classe. Supondo que um arquivo com o JSON que criamos acima foi adicionado ao projeto com o nome sample.json e recebeu uma ação de criação de conteúdo, carregando o `JsonElement` é tão simples quanto chamar a seguinte linha de código:

```csharp
var taskElement = JsonElement.FromFile ("task.json");
```

Já que estamos adicionando este sob demanda sempre que uma tarefa é criada, podemos modificar botão clicado do exemplo anterior da API de elementos da seguinte maneira:

```csharp
_addButton.Clicked += (sender, e) => {

        ++n;

        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};

        var taskElement = JsonElement.FromFile ("task.json");

        _rootElement [0].Add (taskElement);
};
```

 <a name="Accessing_Elements_at_Runtime" />


## <a name="accessing-elements-at-runtime"></a>Acessando elementos em tempo de execução

Lembre-se de que adicionamos uma id para os dois elementos quando é declarado-las no arquivo JSON. Podemos usar a propriedade id para acessar cada elemento em tempo de execução para modificar suas propriedades no código. Por exemplo, o código a seguir faz referência os elementos de entrada e a data para definir os valores do objeto de tarefa:

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

 <a name="Loading_JSON_from_a_Url" />


## <a name="loading-json-from-a-url"></a>Carregando o JSON de uma Url

MT. D também oferece suporte carregar dinamicamente o JSON de uma Url externa, simplesmente passando a Url ao construtor do `JsonElement`. MT. D expandirá a hierarquia declarada em JSON sob demanda como navegar entre telas. Por exemplo, considere um arquivo JSON, como mostrado abaixo localizado na raiz do servidor web local:

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

Pode carregar isso usando o `JsonElement` como no código a seguir:

```csharp
_rootElement = new RootElement ("Json Example"){
        new Section (""){ new JsonElement ("Load from url",
                "http://localhost/sample.json")
        }
};
```

Em tempo de execução, o arquivo será recuperado e analisado por MT. D quando o usuário navega para o segundo modo de exibição, conforme mostrado na captura de tela abaixo:

 [ ![](json-element-walkthrough-images/04-json-web-example.png "O arquivo será recuperado e analisado por MT. D quando o usuário navega para o segundo modo de exibição")](json-element-walkthrough-images/04-json-web-example.png)

 <a name="Summary" />


## <a name="summary"></a>Resumo

Este artigo mostrou como criar um usando a interface com MT. D do JSON. Ele mostrou como carga JSON incluído em um arquivo com o aplicativo, bem como uma Url remota. Ele também mostrou como acessar elementos descritos em JSON em tempo de execução.


## <a name="related-links"></a>Links relacionados

- [MTDJsonDemo (sample)](https://developer.xamarin.com/samples/MTDJsonDemo/)
- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces de usuário com MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introdução ao MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Instruções passo a passo de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Instruções passo a passo de API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Aplicativo de TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Referência de classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
