---
title: Introdução aos serviços Web do Xamarin. Forms
description: Este guia fornece uma explicação do aplicativo de exemplo Xamarin. Forms que demonstra como se comunicar com diferentes serviços Web. Embora cada serviço Web Use um aplicativo de exemplo separado, eles são funcionalmente semelhantes e compartilham classes comuns.
ms.prod: xamarin
ms.assetid: A3FEB262-0D79-42E6-8F8B-A565618C490B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: bbeab6a6ab0d4a9d0e3a962240317fc0d54f9e25
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656635"
---
# <a name="xamarinforms-web-services-introduction"></a>Introdução aos serviços Web do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Este tópico fornece uma explicação do aplicativo de exemplo Xamarin. Forms que demonstra como se comunicar com diferentes serviços Web. Embora cada serviço Web Use um aplicativo de exemplo separado, eles são funcionalmente semelhantes e compartilham classes comuns._

O aplicativo de lista de tarefas pendentes de exemplo descrito abaixo é usado para demonstrar como acessar diferentes tipos de back-ends de serviço Web com Xamarin. Forms. Ele fornece funcionalidade para:

- Exibir uma lista de tarefas.
- Adicionar, editar e excluir tarefas.
- Defina o status de uma tarefa como ' done '.
- Fale os campos nome e anotações da tarefa.

Em todos os casos, as tarefas são armazenadas em um back-end que é acessado por meio de um serviço Web.

Quando o aplicativo é iniciado, é exibida uma página que lista todas as tarefas recuperadas do serviço Web e permite que o usuário crie uma nova tarefa. Clicar em uma tarefa navega o aplicativo para uma segunda página em que a tarefa pode ser editada, salva, excluída e falada. O aplicativo final é mostrado abaixo:

![](introduction-images/app-example-1.png "Todo application - first page")
![](introduction-images/app-example-2.png "Todo application - second page")

Cada tópico neste guia fornece um link de download para uma versão *diferente* do aplicativo que demonstra um tipo específico de back-end de serviço Web. Baixe o código de exemplo relevante na página relacionada a cada estilo de serviço Web.

## <a name="understand-the-application-anatomy"></a>Entender a anatomia do aplicativo

O projeto de código compartilhado para cada aplicativo de exemplo consiste em três pastas principais:

|Pasta|Finalidade|
|--- |--- |
|Dados|Contém as classes e interfaces usadas para gerenciar itens de dados e se comunicar com o serviço Web. No mínimo, isso inclui a classe `TodoItemManager`, que é exposta por meio de uma propriedade na classe `App` para invocar operações de serviço Web.|
|Modelos|Contém as classes de modelo de dados para o aplicativo. Isso inclui, no mínimo, a classe `TodoItem`, que modela um único item de dados usado pelo aplicativo. A pasta também pode incluir qualquer classe adicional usada para modelar dados de usuário.|
|Exibições|Contém as páginas do aplicativo. Isso geralmente consiste nas classes `TodoListPage` e `TodoItemPage` e quaisquer classes adicionais usadas para fins de autenticação.|

O projeto de código compartilhado para cada aplicativo também consiste em vários arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|A classe `Constants`, que especifica qualquer constante usada pelo aplicativo para se comunicar com o serviço Web. Essas constantes exigem a atualização para acessar seu serviço de back-end pessoal criado em um provedor.|
|ITextToSpeech.cs|A interface `ITextToSpeech`, que especifica que o método de `Speak` deve ser fornecido por qualquer classe de implementação.|
|Todo.cs|A classe `App` que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma e a classe `TodoItemManager` usada para invocar operações de serviço Web.|

### <a name="view-pages"></a>Exibir páginas

A maioria dos aplicativos de exemplo contém pelo menos duas páginas:

- **TodoListPage** – esta página exibe uma lista de instâncias de `TodoItem` e um ícone de tique se a propriedade `TodoItem.Done` for `true`. Clicar em um item navega para a `TodoItemPage`. Além disso, novos itens podem ser criados clicando no símbolo de *+* .
- **TodoItemPage** – esta página exibe os detalhes do `TodoItem` selecionado e permite que ele seja editado, salvo, excluído e falado.

Além disso, alguns aplicativos de exemplo contêm páginas adicionais que são usadas para gerenciar o processo de autenticação do usuário.

### <a name="model-the-data"></a>Modelar os dados

Cada aplicativo de exemplo usa a classe `TodoItem` para modelar os dados que são exibidos e enviados para o serviço Web para armazenamento. O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

A propriedade `ID` é usada para identificar exclusivamente cada instância de `TodoItem` e é usada por cada serviço Web para identificar os dados a serem atualizados ou excluídos.

### <a name="invoke-web-service-operations"></a>Invocar operações de serviço Web

As operações de serviço Web são acessadas por meio da classe `TodoItemManager`, e uma instância da classe pode ser acessada por meio da propriedade `App.TodoManager`. A classe `TodoItemManager` fornece os seguintes métodos para invocar operações de serviço Web:

- **GetTasksAsync** – esse método é usado para popular o controle de `ListView` no `TodoListPage` com as instâncias de `TodoItem` recuperadas do serviço Web.
- **SaveTaskAsync** – esse método é usado para criar ou atualizar uma instância de `TodoItem` no serviço Web.
- **DeleteTaskAsync** – esse método é usado para excluir uma instância de `TodoItem` no serviço Web.

Além disso, alguns aplicativos de exemplo contêm métodos adicionais na classe `TodoItemManager`, que são usados para gerenciar o processo de autenticação do usuário.

Em vez de invocar as operações de serviço Web diretamente, os métodos de `TodoItemManager` invocam métodos em uma classe dependente que é injetada no construtor de `TodoItemManager`. Por exemplo, um aplicativo de exemplo injeta a classe `RestService` no Construtor `TodoItemManager` para fornecer a implementação que usa APIs REST para acessar dados.

## <a name="related-links"></a>Links relacionados

- [ASMX (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
