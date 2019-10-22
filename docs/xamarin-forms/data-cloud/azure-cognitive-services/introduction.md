---
title: Introdução aos serviços do Xamarin. Forms e do Azure cognitiva
description: Este artigo fornece uma introdução a um aplicativo de exemplo que demonstra como invocar algumas das APIs de serviço cognitiva da Microsoft.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 52774b387644b14e3d4612dffa6d3c3b28a37f25
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68652307"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Introdução aos serviços do Xamarin. Forms e do Azure cognitiva

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Os serviços cognitivas da Microsoft são um conjunto de APIs, SDKs e serviços disponíveis aos desenvolvedores para tornar seus aplicativos mais inteligentes, adicionando recursos como reconhecimento facial, reconhecimento de fala e compreensão da linguagem. Este artigo fornece uma introdução ao aplicativo de exemplo que demonstra como invocar algumas das APIs de serviço cognitiva da Microsoft._

## <a name="overview"></a>Visão Geral

O exemplo a seguir é um aplicativo de lista de tarefas pendentes que fornece funcionalidade para:

- Exibir uma lista de tarefas.
- Adicione e edite tarefas por meio do teclado soft ou executando o reconhecimento de fala com o Microsoft Speech API. Para obter mais informações sobre como executar o reconhecimento de fala, consulte [reconhecimento de fala usando o Speech API da Microsoft](speech-recognition.md).
- Tarefas de verificação ortográfica usando a API Verificação Ortográfica do Bing. Para obter mais informações, consulte [verificação ortográfica usando a API de verificação ortográfica do Bing](spell-check.md).
- Traduza tarefas de Inglês para alemão usando a API do tradutor. Para obter mais informações, consulte [conversão de texto usando a API do tradutor](text-translation.md).
- Excluir tarefas.
- Defina o status de uma tarefa como ' done '.
- Classifique o aplicativo com o reconhecimento de emoções usando o API de Detecção Facial. Para obter mais informações, consulte [reconhecimento de emoções usando o API de detecção facial](emotion-recognition.md).

As tarefas são armazenadas em um banco de dados SQLite local. Para obter mais informações sobre como usar um banco de dados SQLite local, consulte [trabalhando com um banco de dados local](~/xamarin-forms/data-cloud/data/databases.md).

O `TodoListPage` é exibido quando o aplicativo é iniciado. Esta página exibe uma lista de todas as tarefas armazenadas no banco de dados local e permite que o usuário crie uma nova tarefa ou avalie o aplicativo:

![](introduction-images/sample-application-1.png "TodoListPage")

Novos itens podem ser criados clicando no botão *+* , que navega até o `TodoItemPage`. Essa página também pode ser navegada selecionando uma tarefa:

![](introduction-images/sample-application-2.png "TodoItemPage")

O `TodoItemPage` permite que as tarefas sejam criadas, editadas, verificadas por ortografia, convertidas, salvas e excluídas. O reconhecimento de fala pode ser usado para criar ou editar uma tarefa. Isso é feito pressionando o botão de microfone para iniciar a gravação e pressionando o mesmo botão uma segunda vez para parar a gravação, o que envia a gravação para a API de reconhecimento de Fala do Bing.

Clicar no botão smils na `TodoListPage` navega para o `RateAppPage`, que é usado para executar o reconhecimento de emoções em uma imagem de uma expressão facial:

![](introduction-images/sample-application-3.png "RateAppPage")

A `RateAppPage` permite que o usuário tire uma foto de sua face, que é enviada para o API de Detecção Facial com a emoção retornada sendo exibida.

## <a name="understand-the-application-anatomy"></a>Entender a anatomia do aplicativo

O projeto de código compartilhado para o aplicativo de exemplo consiste em cinco pastas principais:

|Pasta|Finalidade|
|--- |--- |
|Modelos|Contém as classes de modelo de dados para o aplicativo. Isso inclui a classe `TodoItem`, que modela um único item de dados usado pelo aplicativo. A pasta também inclui classes usadas para modelar respostas JSON retornadas de diferentes APIs de serviço cognitiva da Microsoft.|
|Repositórios|Contém a interface de `ITodoItemRepository` e `TodoItemRepository` classe que são usadas para executar operações de banco de dados.|
|Serviços|Contém as interfaces e classes que são usadas para acessar diferentes APIs de serviço cognitiva da Microsoft, juntamente com interfaces que são usadas pela classe `DependencyService` para localizar as classes que implementam as interfaces em projetos de plataforma.|
|utils|Contém a classe `Timer`, que é usada pela classe `AuthenticationService` para renovar um token de acesso JWT a cada 9 minutos.|
|Exibições|Contém as páginas do aplicativo.|

O projeto de código compartilhado também contém alguns arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|A classe `Constants`, que especifica as chaves de API e os pontos de extremidade para as APIs de serviço cognitiva da Microsoft que são invocadas. As constantes de chave de API exigem atualização para acessar as diferentes APIs de serviço cognitiva.|
|App.xaml.cs|A classe `App` é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma e a classe `TodoManager` usada para invocar operações de banco de dados.|

### <a name="nuget-packages"></a>Pacotes NuGet

O aplicativo de exemplo usa os seguintes pacotes NuGet:

- `Newtonsoft.Json` – fornece uma estrutura JSON para .NET.
- `PCLStorage` – fornece um conjunto de APIs de e/s de arquivo local de plataforma cruzada.
- `sqlite-net-pcl` – fornece armazenamento de banco de dados SQLite.
- `Xam.Plugin.Media` – fornece APIs de separação e criação de fotos entre plataformas.

Além disso, esses pacotes NuGet também instalam suas próprias dependências.

### <a name="model-the-data"></a>Modelar os dados

O aplicativo de exemplo usa a classe `TodoItem` para modelar os dados que são exibidos e armazenados no banco de dados SQLite local. O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

A propriedade `ID` é usada para identificar exclusivamente cada instância de `TodoItem` e é decorada com atributos SQLite que tornam a propriedade uma chave primária de incremento automático no banco de dados.

### <a name="invoke-database-operations"></a>Invocar operações de banco de dados

A classe `TodoItemRepository` implementa operações de banco de dados e uma instância da classe pode ser acessada por meio da propriedade `App.TodoManager`. A classe `TodoItemRepository` fornece os seguintes métodos para invocar operações de banco de dados:

- **GetAllItemsAsync** – recupera todos os itens do banco de dados SQLite local.
- **GetItemAsync** – recupera um item especificado do banco de dados SQLite local.
- **SaveItemAsync** – cria ou atualiza um item no banco de dados SQLite local.
- **DeleteItemAsync** – exclui o item especificado do banco de dados SQLite local.

### <a name="platform-project-implementations"></a>Implementações de projeto de plataforma

A pasta `Services` no projeto de código compartilhado contém as interfaces `IFileHelper` e `IAudioRecorderService` que são usadas pela classe `DependencyService` para localizar as classes que implementam as interfaces em projetos de plataforma.

A interface de `IFileHelper` é implementada pela classe `FileHelper` em cada projeto de plataforma. Essa classe consiste em um único método, `GetLocalFilePath`, que retorna um caminho de arquivo local para armazenar o banco de dados SQLite.

A interface de `IAudioRecorderService` é implementada pela classe `AudioRecorderService` em cada projeto de plataforma. Essa classe consiste em `StartRecording`, `StopRecording` e métodos de suporte, que usam APIs de plataforma para gravar áudio do microfone do dispositivo e armazená-lo como um arquivo WAV. No iOS, o `AudioRecorderService` usa a API `AVFoundation` para gravar áudio. No Android, o `AudioRecordService` usa a API `AudioRecord` para gravar áudio. No Plataforma Universal do Windows (UWP), o `AudioRecorderService` usa a API do `AudioGraph` para gravar áudio.

### <a name="invoke-cognitive-services"></a>Invocar serviços cognitivas

O aplicativo de exemplo invoca os seguintes serviços cognitivas da Microsoft:

- Microsoft Speech API. Para obter mais informações, consulte [reconhecimento de fala usando o Microsoft Speech API](speech-recognition.md).
- API Verificação Ortográfica do Bing. Para obter mais informações, consulte [verificação ortográfica usando a API de verificação ortográfica do Bing](spell-check.md).
- Converta a API. Para obter mais informações, consulte [conversão de texto usando a API do tradutor](text-translation.md).
- API de Detecção Facial. Para obter mais informações, consulte [reconhecimento de emoções usando o API de detecção facial](emotion-recognition.md).

## <a name="related-links"></a>Links relacionados

- [Documentação dos serviços cognitivas da Microsoft](https://www.microsoft.com/cognitive-services/documentation)
- [Serviços cognitivas todo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
