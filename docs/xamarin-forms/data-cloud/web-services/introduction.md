---
title: Introdução aos serviços Web do Xamarin. Forms
description: Este guia fornece uma explicação do aplicativo de exemplo Xamarin. Forms que demonstra como se comunicar com diferentes serviços Web. Enquanto a cada serviço web usa um aplicativo de exemplo separado, elas são funcionalmente semelhantes e compartilharem classes comuns.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68656635"
---
# <a name="xamarinforms-web-services-introduction"></a>Introdução aos serviços Web do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Este tópico fornece um passo a passo do aplicativo de exemplo do xamarin. Forms que demonstra como se comunicar com serviços web diferentes. Enquanto a cada serviço web usa um aplicativo de exemplo separado, elas são funcionalmente semelhantes e compartilharem classes comuns._

O aplicativo de lista de tarefas pendentes de exemplo descrito abaixo é usado para demonstrar como acessar os diferentes tipos de back-ends da web de serviço com o xamarin. Forms. Ele fornece funcionalidade para:

- Exiba uma lista de tarefas.
- Adicionar, editar e excluir tarefas.
- Defina o status da tarefa para 'concluído'.
- Fale os campos de nome e Observações da tarefa.

Em todos os casos, as tarefas são armazenadas em um back-end que é acessado por meio de um serviço web.

Quando o aplicativo é iniciado, é exibida uma página que lista as tarefas recuperadas do serviço web e permite que o usuário crie uma nova tarefa. Clicar em uma tarefa navega o aplicativo em uma segunda página em que a tarefa pode ser editada, salvo, excluída e falada. O aplicativo final é mostrado abaixo:

![](introduction-images/app-example-1.png "Todo aplicativo – primeira página")
![](introduction-images/app-example-2.png "Todo aplicativo - segunda página")

Cada tópico deste guia fornece um link de download para um *diferentes* versão do aplicativo que demonstra um tipo específico de back-end de serviço web. Baixe o código de exemplo relevante na página relacionadas ao estilo de cada serviço web.

## <a name="understand-the-application-anatomy"></a>Entender a anatomia do aplicativo

O projeto de código compartilhado para cada aplicativo de exemplo consiste em três pastas principais:

|Pasta|Finalidade|
|--- |--- |
|Dados|Contém as classes e interfaces usadas para gerenciar itens de dados e se comunicar com o serviço web. No mínimo, isso inclui o `TodoItemManager` classe, que é exposta por meio de uma propriedade no `App` classe para invocar operações de serviço web.|
|Modelos|Contém as classes de modelo de dados para o aplicativo. No mínimo, isso inclui o `TodoItem` classe, que modela um único item de dados usados pelo aplicativo. A pasta também pode incluir quaisquer classes adicionais usados para modelar dados de usuário.|
|Exibições|Contém as páginas do aplicativo. Isso geralmente consiste de `TodoListPage` e `TodoItemPage` classes e as classes adicionais usadas para fins de autenticação.|

O projeto de código compartilhado para cada aplicativo também consiste em vários arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|O `Constants` classe, que especifica as constantes usadas pelo aplicativo para se comunicar com o serviço web. Essas constantes exigem a atualização para acessar seu serviço de back-end pessoal criada em um provedor.|
|ITextToSpeech.cs|O `ITextToSpeech` interface, que especifica que o `Speak` método deve ser fornecido por qualquer classe de implementação.|
|Todo.cs|O `App` classe que é responsável por instanciar os dois a primeira página que será exibida pelo aplicativo em cada plataforma, e o `TodoItemManager` classe que é usado para chamar operações de serviço web.|

### <a name="view-pages"></a>Exibir páginas

A maioria dos aplicativos de exemplo contém pelo menos duas páginas:

- **TodoListPage** – esta página exibe uma lista de `TodoItem` instâncias e um ícone de tique se o `TodoItem.Done` é de propriedade `true`. Clicar em um item navega para o `TodoItemPage`. Além disso, novos itens podem ser criados, clicando na *+* símbolo.
- **TodoItemPage** – esta página exibe os detalhes para o selecionado `TodoItem`e permite que ele seja editado, salvo, excluído e falado.

Além disso, alguns aplicativos de exemplo contêm páginas adicionais que são usadas para gerenciar o processo de autenticação do usuário.

### <a name="model-the-data"></a>Modelar os dados

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

### <a name="invoke-web-service-operations"></a>Invocar operações de serviço Web

Operações de serviço Web são acessadas por meio de `TodoItemManager` classe e uma instância da classe podem ser acessados por meio do `App.TodoManager` propriedade. O `TodoItemManager` classe fornece os seguintes métodos para chamar operações de serviço web:

- **GetTasksAsync** – esse método é usado para preencher a `ListView` control na `TodoListPage` com o `TodoItem` instâncias recuperadas do serviço web.
- **SaveTaskAsync** – esse método é usado para criar ou atualizar um `TodoItem` instância no serviço web.
- **DeleteTaskAsync** – esse método é usado para excluir um `TodoItem` instância no serviço web.

Além disso, alguns aplicativos de exemplo contêm métodos adicionais no `TodoItemManager` classe, que são usados para gerenciar o processo de autenticação do usuário.

Em vez de invocar as operações de serviço web diretamente, o `TodoItemManager` métodos de invocam métodos em uma classe dependente que é injetado no `TodoItemManager` construtor. Por exemplo, um aplicativo de exemplo injeta a `RestService` de classe para o `TodoItemManager` construtor para fornecer a implementação que usa APIs REST para acessar os dados.

## <a name="related-links"></a>Links relacionados

- [ASMX (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
