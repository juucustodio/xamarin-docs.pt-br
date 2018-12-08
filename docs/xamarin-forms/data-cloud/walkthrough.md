---
title: Noções básicas da amostra
description: Este tópico fornece um passo a passo do aplicativo de exemplo do xamarin. Forms que demonstra como se comunicar com serviços web diferentes. Enquanto a cada serviço web usa um aplicativo de exemplo separado, elas são funcionalmente semelhantes e compartilharem classes comuns.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bfd7330fa1eee5f80a9043341d9760058d99d48b
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053445"
---
# <a name="understanding-the-sample"></a>Noções básicas da amostra

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)

_Este tópico fornece um passo a passo do aplicativo de exemplo do xamarin. Forms que demonstra como se comunicar com serviços web diferentes. Enquanto a cada serviço web usa um aplicativo de exemplo separado, elas são funcionalmente semelhantes e compartilharem classes comuns._

O aplicativo de lista de tarefas pendentes de exemplo descrito abaixo é usado para demonstrar como acessar os diferentes tipos de back-ends da web de serviço com o xamarin. Forms. Ele fornece funcionalidade para:

- Exiba uma lista de tarefas.
- Adicionar, editar e excluir tarefas.
- Defina o status da tarefa para 'concluído'.
- Fale os campos de nome e Observações da tarefa.

Em todos os casos, as tarefas são armazenadas em um back-end que é acessado por meio de um serviço web.

Quando o aplicativo é iniciado, é exibida uma página que lista as tarefas recuperadas do serviço web e permite que o usuário crie uma nova tarefa. Clicar em uma tarefa navega o aplicativo em uma segunda página em que a tarefa pode ser editada, salvo, excluída e falada. O aplicativo final é mostrado abaixo:

![](walkthrough-images/app-example-1.png "Todo aplicativo – primeira página")
![](walkthrough-images/app-example-2.png "Todo aplicativo - segunda página")

Cada tópico deste guia fornece um link de download para um *diferentes* versão do aplicativo que demonstra um tipo específico de back-end de serviço web. Baixe o código de exemplo relevante na página relacionadas ao estilo de cada serviço web.

## <a name="understanding-the-application-anatomy"></a>Noções básicas sobre a anatomia do aplicativo

O projeto PCL para cada aplicativo de exemplo consiste em três pastas principais:

|Pasta|Finalidade|
|--- |--- |
|Dados|Contém as classes e interfaces usadas para gerenciar itens de dados e se comunicar com o serviço web. No mínimo, isso inclui o `TodoItemManager` classe, que é exposta por meio de uma propriedade no `App` classe para invocar operações de serviço web.|
|Modelos|Contém as classes de modelo de dados para o aplicativo. No mínimo, isso inclui o `TodoItem` classe, que modela um único item de dados usados pelo aplicativo. A pasta também pode incluir quaisquer classes adicionais usados para modelar dados de usuário.|
|Exibições|Contém as páginas do aplicativo. Isso geralmente consiste de `TodoListPage` e `TodoItemPage` classes e as classes adicionais usadas para fins de autenticação.|

O projeto PCL para cada aplicativo também consiste em um número de arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|O `Constants` classe, que especifica as constantes usadas pelo aplicativo para se comunicar com o serviço web. Essas constantes exigem a atualização para acessar seu serviço de back-end pessoal criada em um provedor.|
|ITextToSpeech.cs|O `ITextToSpeech` interface, que especifica que o `Speak` método deve ser fornecido por qualquer classe de implementação.|
|Todo.cs|O `App` classe que é responsável por instanciar os dois a primeira página que será exibida pelo aplicativo em cada plataforma, e o `TodoItemManager` classe que é usado para chamar operações de serviço web.|

### <a name="viewing-pages"></a>Exibindo páginas

A maioria dos aplicativos de exemplo contém pelo menos duas páginas:

- **TodoListPage** – esta página exibe uma lista de `TodoItem` instâncias e um ícone de tique se o `TodoItem.Done` é de propriedade `true`. Clicar em um item navega para o `TodoItemPage`. Além disso, novos itens podem ser criados, clicando na *+* símbolo.
- **TodoItemPage** – esta página exibe os detalhes para o selecionado `TodoItem`e permite que ele seja editado, salvo, excluído e falado.

Além disso, alguns aplicativos de exemplo contêm páginas adicionais que são usadas para gerenciar o processo de autenticação do usuário.

### <a name="modeling-the-data"></a>Modelagem de dados

Cada aplicativo de exemplo usa o `TodoItem` classe para modelar os dados que são exibidos e enviados para o serviço web para o armazenamento. O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

O `ID` propriedade é usada para identificar exclusivamente cada `TodoItem` instância e são usados por cada serviço da web para identificar os dados a serem atualizados ou excluídos.

### <a name="invoking-web-service-operations"></a>Invocar operações de serviço Web

Operações de serviço Web são acessadas por meio de `TodoItemManager` classe e uma instância da classe podem ser acessados por meio do `App.TodoManager` propriedade. O `TodoItemManager` classe fornece os seguintes métodos para chamar operações de serviço web:

- **GetTasksAsync** – esse método é usado para preencher a `ListView` control na `TodoListPage` com o `TodoItem` instâncias recuperadas do serviço web.
- **SaveTaskAsync** – esse método é usado para criar ou atualizar um `TodoItem` instância no serviço web.
- **DeleteTaskAsync** – esse método é usado para excluir um `TodoItem` instância no serviço web.

Além disso, alguns aplicativos de exemplo contêm métodos adicionais no `TodoItemManager` classe, que são usados para gerenciar o processo de autenticação do usuário.

Em vez de invocar as operações de serviço web diretamente, o `TodoItemManager` métodos de invocam métodos em uma classe dependente que é injetado no `TodoItemManager` construtor. Por exemplo, um aplicativo de exemplo injeta a `RestService` de classe para o `TodoItemManager` construtor para fornecer a implementação que usa APIs REST para acessar os dados.

### <a name="translating-text-to-speech"></a>Converter texto em fala

A maioria dos aplicativos de exemplo contêm funcionalidade de texto em fala (TTS) falar os valores de `TodoItem.Name` e `TodoItem.Notes` propriedades. Isso é feito usando o `OnSpeakActivated` manipulador de eventos no `TodoItemPage` de classe, conforme mostrado no exemplo de código a seguir:

```csharp
void OnSpeakActivated (object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    App.Speech.Speak(todoItem.Name + " " + todoItem.Notes);
}
```

Esse método simplesmente chama o `Speak` método é implementado por uma plataforma específica `Speech` classe. Cada `Speech` classe implementa o `ITextToSpeech` interface, e código de inicialização específica da plataforma cria uma instância da `Speech` classe que pode ser acessado por meio do `App.Speech` propriedade.

## <a name="summary"></a>Resumo

Este tópico forneceu um passo a passo do aplicativo de exemplo de xamarin. Forms é usado para demonstrar como se comunicar com serviços web diferentes. Enquanto a cada serviço web usa um aplicativo de exemplo separados, eles se baseiam na mesma lógica de negócios e de interface do usuário conforme descrito acima - apenas o mecanismo de armazenamento do web service dados é diferente.


## <a name="related-links"></a>Links relacionados

- [Versão ASMX (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX)
- [Versão do WCF (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF)
- [Versão REST (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)
- [Versão do Azure (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure)
