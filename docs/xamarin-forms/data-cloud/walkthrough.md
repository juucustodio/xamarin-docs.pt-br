---
title: "Noções básicas sobre o exemplo"
description: "Este tópico fornece um passo a passo do aplicativo de exemplo xamarin. Forms que demonstra como se comunicar com serviços web diferentes. Enquanto cada serviço web usa um aplicativo de exemplo separados, eles são funcionalmente semelhantes e compartilharem classes comuns."
ms.topic: article
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: ab44cb7a065164bb5b7501ac63bd0321b612b7ca
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="understanding-the-sample"></a>Noções básicas sobre o exemplo

_Este tópico fornece um passo a passo do aplicativo de exemplo xamarin. Forms que demonstra como se comunicar com serviços web diferentes. Enquanto cada serviço web usa um aplicativo de exemplo separados, eles são funcionalmente semelhantes e compartilharem classes comuns._

O aplicativo de lista de tarefas pendentes de exemplo descrito a seguir é usado para demonstrar como acessar tipos diferentes de back-ends da web de serviço com xamarin. Forms. Ele fornece funcionalidade para:

- Exiba uma lista de tarefas.
- Adicionar, editar e excluir tarefas.
- Defina o status da tarefa para 'done'.
- Fale os campos de nome e Observações da tarefa.

Em todos os casos, as tarefas são armazenadas em um back-end que é acessado por meio de um serviço web.

Quando o aplicativo é iniciado, uma página é exibida que lista as tarefas recuperadas do serviço da web e permite que o usuário criar uma nova tarefa. Clicar em uma tarefa navega o aplicativo para uma segunda página em que a tarefa pode ser editada, salvo, excluída e falada. O aplicativo final é mostrado abaixo:

![](walkthrough-images/app-example-1.png "Todo aplicativo - da primeira página")
![](walkthrough-images/app-example-2.png "Todo aplicativo - segunda página")

Cada tópico deste guia fornece um link de download para um *diferentes* versão do aplicativo que demonstra um tipo específico de back-end do web service. Baixe o código de exemplo relevante na página relacionadas a cada serviço web de estilo.

## <a name="understanding-the-application-anatomy"></a>Noções básicas sobre a anatomia do aplicativo

O projeto PCL para cada aplicativo de exemplo consiste em três pastas principais:

|Pasta|Finalidade|
|--- |--- |
|Dados|Contém as classes e interfaces usadas para gerenciar itens de dados e se comunicar com o serviço web. No mínimo, isso inclui o `TodoItemManager` classe, que é exposto por meio de uma propriedade no `App` classe para chamar operações de serviço web.|
|Modelos|Contém as classes de modelo de dados para o aplicativo. No mínimo, isso inclui o `TodoItem` classe, que modela um único item de dados usados pelo aplicativo. A pasta também pode incluir as classes adicionais usadas para modelar dados de usuário.|
|Exibições|Contém as páginas do aplicativo. Isso normalmente consiste de `TodoListPage` e `TodoItemPage` classes e as classes adicionais usadas para fins de autenticação.|

O projeto PCL para cada aplicativo também consiste em um número de arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|O `Constants` classe, que especifica as constantes usadas pelo aplicativo para se comunicar com o serviço web. Constantes exigem atualização para acessar seu serviço de back-end pessoal criado em um provedor.|
|ITextToSpeech.cs|O `ITextToSpeech` interface, que especifica que o `Speak` método deve ser fornecido por qualquer classe de implementação.|
|Todo.cs|O `App` classe que é responsável pela instanciação ambos a primeira página que será exibida no aplicativo em cada plataforma e o `TodoItemManager` classe que é usado para chamar operações de serviço web.|

### <a name="viewing-pages"></a>Exibir páginas

A maioria dos aplicativos de exemplo contém pelo menos duas páginas:

- **TodoListPage** – esta página exibe uma lista de `TodoItem` instâncias e um ícone de tique se o `TodoItem.Done` é de propriedade `true`. Clicar em um item navega para o `TodoItemPage`. Além disso, novos itens podem ser criados clicando no  *+*  símbolo.
- **TodoItemPage** – esta página exibe os detalhes para selecionado `TodoItem`e permite que ela seja editada, salvo, excluído e falado.

Além disso, alguns aplicativos de exemplo contêm páginas adicionais que são usadas para gerenciar o processo de autenticação de usuário.

### <a name="modeling-the-data"></a>Modelagem de dados

Cada aplicativo de exemplo usa o `TodoItem` classe para modelar os dados que são exibidos e enviados para o serviço da web para o armazenamento. O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

O `ID` propriedade é usada para identificar exclusivamente cada `TodoItem` instância e é usada por cada serviço web para identificar os dados a ser atualizada ou excluída.

### <a name="invoking-web-service-operations"></a>Chamar operações de serviço Web

Operações de serviço Web são acessadas por meio de `TodoItemManager` classe e uma instância da classe podem ser acessados por meio do `App.TodoManager` propriedade. O `TodoItemManager` classe fornece os seguintes métodos para chamar operações de serviço web:

- **GetTasksAsync** – esse método é usado para popular o `ListView` control no `TodoListPage` com o `TodoItem` instâncias recuperados do serviço da web.
- **SaveTaskAsync** – esse método é usado para criar ou atualizar um `TodoItem` instância no serviço da web.
- **DeleteTaskAsync** – esse método é usado para excluir um `TodoItem` instância no serviço da web.

Além disso, alguns aplicativos de exemplo contêm métodos adicionais de `TodoItemManager` classe, que são usados para gerenciar o processo de autenticação de usuário.

Em vez de chamar as operações de serviço da web diretamente, o `TodoItemManager` métodos invocar métodos em uma classe dependente que é injetada o `TodoItemManager` construtor. Por exemplo, um aplicativo de exemplo injeta o `SimpleDBStorage` de classe para o `TodoItemManager` construtor para fornecer a implementação que invoca as operações no serviço de SimpleDB da Amazon.

### <a name="translating-text-to-speech"></a>Convertendo texto em fala

A maioria dos aplicativos de exemplo contêm funcionalidade de texto em fala (TTS) Fale os valores de `TodoItem.Name` e `TodoItem.Notes` propriedades. Isso é feito usando o `OnSpeakActivated` manipulador de eventos a `TodoItemPage` classe, conforme mostrado no exemplo de código a seguir:

```csharp
void OnSpeakActivated (object sender, EventArgs e)
{
    var todoItem = (TodoItem)BindingContext;
    App.Speech.Speak(todoItem.Name + " " + todoItem.Notes);
}
```

Este método simplesmente chama o `Speak` método é implementado por uma determinada plataforma `Speech` classe. Cada `Speech` classe implementa o `ITextToSpeech` interface, e código de inicialização específica de plataforma cria uma instância do `Speech` classe que pode ser acessado por meio de `App.Speech` propriedade.

## <a name="summary"></a>Resumo

Este tópico forneceu um passo a passo do aplicativo de exemplo xamarin. Forms que é usado para demonstrar como se comunicar com serviços web diferentes. Embora cada serviço web usa um aplicativo de exemplo separado, eles se baseiam na mesma lógica de negócios e a interface do usuário conforme descrito acima - somente o mecanismo de armazenamento do web service dados é diferente.


## <a name="related-links"></a>Links relacionados

- [Versão ASMX (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoASMX)
- [Versão do WCF (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF)
- [Versão do REST (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST)
- [Versão do Azure (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzure)
- [Versão do Amazon Web Services (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAWS)
