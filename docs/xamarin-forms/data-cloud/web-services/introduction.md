---
Título: " Xamarin.Forms introdução aos serviços Web": "este guia fornece uma explicação do Xamarin.Forms aplicativo de exemplo que demonstra como se comunicar com diferentes serviços Web. Embora cada serviço Web Use um aplicativo de exemplo separado, eles são funcionalmente semelhantes e compartilham classes comuns. "
MS. Prod: xamarin MS. AssetID: A3FEB262-0D79-42E6-8F8B-A565618C490B MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 02/28/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-web-services-introduction"></a>Xamarin.FormsIntrodução aos serviços Web

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Este tópico fornece uma explicação do Xamarin.Forms aplicativo de exemplo que demonstra como se comunicar com diferentes serviços Web. Embora cada serviço Web Use um aplicativo de exemplo separado, eles são funcionalmente semelhantes e compartilham classes comuns._

O aplicativo de lista de tarefas pendentes de exemplo descrito abaixo é usado para demonstrar como acessar diferentes tipos de back-ends de serviço Web com o Xamarin.Forms . Ele fornece funcionalidade para:

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
|Dados|Contém as classes e interfaces usadas para gerenciar itens de dados e se comunicar com o serviço Web. Isso inclui, no mínimo, a `TodoItemManager` classe, que é exposta por meio de uma propriedade na `App` classe para invocar operações de serviço Web.|
|Modelos|Contém as classes de modelo de dados para o aplicativo. Isso inclui, no mínimo, a `TodoItem` classe, que modela um único item de dados usado pelo aplicativo. A pasta também pode incluir qualquer classe adicional usada para modelar dados de usuário.|
|Exibições|Contém as páginas do aplicativo. Isso geralmente consiste nas `TodoListPage` classes e e `TodoItemPage` quaisquer classes adicionais usadas para fins de autenticação.|

O projeto de código compartilhado para cada aplicativo também consiste em vários arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|A `Constants` classe, que especifica qualquer constante usada pelo aplicativo para se comunicar com o serviço Web. Essas constantes exigem a atualização para acessar seu serviço de back-end pessoal criado em um provedor.|
|ITextToSpeech.cs|A `ITextToSpeech` interface, que especifica que o `Speak` método deve ser fornecido por qualquer classe de implementação.|
|Todo.cs|A `App` classe que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma e a `TodoItemManager` classe usada para invocar operações de serviço Web.|

### <a name="view-pages"></a>Exibir páginas

A maioria dos aplicativos de exemplo contém pelo menos duas páginas:

- **TodoListPage** – esta página exibe uma lista de `TodoItem` instâncias e um ícone de tique se a `TodoItem.Done` propriedade for `true` . Ao clicar em um item, navega para o `TodoItemPage` . Além disso, novos itens podem ser criados clicando-se no *+* símbolo.
- **TodoItemPage** – esta página exibe os detalhes do selecionado `TodoItem` e permite que ele seja editado, salvo, excluído e falado.

Além disso, alguns aplicativos de exemplo contêm páginas adicionais que são usadas para gerenciar o processo de autenticação do usuário.

### <a name="model-the-data"></a>Modelar os dados

Cada aplicativo de exemplo usa a `TodoItem` classe para modelar os dados que são exibidos e enviados para o serviço Web para armazenamento. O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
    public string ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

A `ID` propriedade é usada para identificar exclusivamente cada `TodoItem` instância e é usada por cada serviço Web para identificar os dados a serem atualizados ou excluídos.

### <a name="invoke-web-service-operations"></a>Invocar operações de serviço Web

As operações de serviço Web são acessadas por meio da `TodoItemManager` classe e uma instância da classe pode ser acessada por meio da `App.TodoManager` propriedade. A `TodoItemManager` classe fornece os seguintes métodos para invocar operações de serviço Web:

- **GetTasksAsync** – esse método é usado para popular o `ListView` controle no `TodoListPage` com as `TodoItem` instâncias recuperadas do serviço Web.
- **SaveTaskAsync** – esse método é usado para criar ou atualizar uma `TodoItem` instância no serviço Web.
- **DeleteTaskAsync** – esse método é usado para excluir uma `TodoItem` instância no serviço Web.

Além disso, alguns aplicativos de exemplo contêm métodos adicionais na `TodoItemManager` classe, que são usados para gerenciar o processo de autenticação do usuário.

Em vez de invocar as operações de serviço Web diretamente, os `TodoItemManager` métodos invocam métodos em uma classe dependente que é injetada no `TodoItemManager` Construtor. Por exemplo, um aplicativo de exemplo injeta a `RestService` classe no `TodoItemManager` construtor para fornecer a implementação que usa APIs REST para acessar dados.

## <a name="related-links"></a>Links relacionados

- [ASMX (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todoasmx)
- [WCF (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [REST (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
