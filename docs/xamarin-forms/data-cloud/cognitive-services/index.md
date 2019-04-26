---
title: Adicionando inteligência com os serviços Cognitivos
description: Este artigo fornece uma introdução a um aplicativo de exemplo que demonstra como invocar algumas das APIs de serviços Cognitivos da Microsoft.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 0c09063b55a14f9f22feb91d2a6f9d3f9417ecee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330825"
---
# <a name="adding-intelligence-with-cognitive-services"></a>Adicionando inteligência com os serviços Cognitivos

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_Serviços Cognitivos da Microsoft são um conjunto de APIs, SDKs e serviços disponíveis para desenvolvedores para tornar seus aplicativos mais inteligentes com a adição de recursos, como reconhecimento facial, reconhecimento de fala e reconhecimento vocal. Este artigo fornece uma introdução ao aplicativo de exemplo que demonstra como invocar algumas das APIs de serviços Cognitivos da Microsoft._

## <a name="overview"></a>Visão geral

O exemplo que acompanha este artigo é um aplicativo de lista de tarefas que fornece funcionalidade para:

- Exiba uma lista de tarefas.
- Adicionar e editar tarefas por meio do teclado virtual, ou realizando o reconhecimento de fala com a API de fala da Microsoft. Para obter mais informações sobre como executar o reconhecimento de fala, consulte [reconhecimento de fala usando a API de fala da Microsoft](speech-recognition.md).
- Usando a API de verificação de ortografia do Bing de tarefas de verificação de ortografia. Para obter mais informações, consulte [ortográfica usando a API de verificação de ortografia do Bing](spell-check.md).
- Converter tarefas do inglês para o alemão usando a API de tradução. Para obter mais informações, consulte [tradução de texto usando a API do Translator](text-translation.md).
- Exclua tarefas.
- Defina o status da tarefa para 'concluído'.
- Avalie o aplicativo com reconhecimento de emoções, usando a API de detecção facial. Para obter mais informações, consulte [reconhecimento de emoções usando a API de detecção facial](emotion-recognition.md).

Tarefas são armazenadas em um banco de dados SQLite local. Para obter mais informações sobre como usar um banco de dados SQLite local, consulte [trabalhando com um banco de dados Local](~/xamarin-forms/app-fundamentals/databases.md).

O `TodoListPage` é exibida quando o aplicativo é iniciado. Esta página exibe uma lista de quaisquer tarefas armazenados no banco de dados local e permite que o usuário para criar uma nova tarefa ou para avaliar o aplicativo:

![](images/sample-application-1.png "TodoListPage")

Novos itens podem ser criados, clicando na *+* botão, que navega para o `TodoItemPage`. Esta página também pode ser navegada, selecionando uma tarefa:

![](images/sample-application-2.png "TodoItemPage")

O `TodoItemPage` permite que as tarefas a serem criados, editados, uma verificação ortográfica, traduzida, salva e excluída. Reconhecimento de fala pode ser usado para criar ou editar uma tarefa. Isso é obtido pressionando o botão de microfone para iniciar a gravação e pressionando o botão mesmo uma segunda vez para parar a gravação, que envia a gravação para a API de reconhecimento de fala do Bing.

Clicar no botão smilies na `TodoListPage` navega para o `RateAppPage`, que é usado para realizar o reconhecimento de emoções em uma imagem de uma expressão facial:

![](images/sample-application-3.png "RateAppPage")

O `RateAppPage` permite que o usuário tirar uma foto do seu face, que é enviado à API de detecção facial com a detecção de emoções retornada que está sendo exibida.

## <a name="understanding-the-application-anatomy"></a>Noções básicas sobre a anatomia do aplicativo

O projeto de biblioteca de classe portátil (PCL) para o aplicativo de exemplo consiste em cinco pastas principais:

|Pasta|Finalidade|
|--- |--- |
|Modelos|Contém as classes de modelo de dados para o aplicativo. Isso inclui o `TodoItem` classe, que modela um único item de dados usados pelo aplicativo. A pasta também contém classes usadas para modelar respostas JSON retornadas de diferentes APIs serviços Cognitivos da Microsoft.|
|Repositórios|Contém o `ITodoItemRepository` interface e `TodoItemRepository` classe que são usados para executar operações de banco de dados.|
|Serviços|Contém as interfaces e classes que são usadas para acessar diferentes Microsoft APIs de serviços Cognitivos, juntamente com as interfaces que são usadas pelo `DependencyService` classe para localizar as classes que implementam as interfaces em projetos de plataforma.|
|Utils|Contém o `Timer` classe, que é usado pelo `AuthenticationService` classe para renovar um token de acesso JWT cada 9 minutos.|
|Exibições|Contém as páginas do aplicativo.|

O projeto PCL também contém alguns arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|O `Constants` classe, que especifica as chaves de API e os pontos de extremidade para o Microsoft Cognitive APIs de serviços que são invocados. As constantes de chave de API exigem atualização para acessar as APIs de serviços Cognitivos diferentes.|
|App.xaml.cs|O `App` classe é responsável por instanciar os dois a primeira página que será exibida pelo aplicativo em cada plataforma, e o `TodoManager` classe que é usado para invocar operações de banco de dados.|

### <a name="nuget-packages"></a>Pacotes NuGet

O aplicativo de exemplo usa os seguintes pacotes NuGet:

- `Newtonsoft.Json` – Fornece uma estrutura JSON para .NET.
- `PCLStorage` – Fornece um conjunto de arquivos local de plataforma cruzada APIs de e/s.
- `sqlite-net-pcl` – Fornece armazenamento de banco de dados SQLite.
- `Xam.Plugin.Media` – Fornece levando de foto de plataforma cruzada e APIs de separação.

Além disso, esses pacotes do NuGet também instalam suas próprias dependências.

### <a name="modeling-the-data"></a>Modelagem de dados

O aplicativo de exemplo usa o `TodoItem` classe para modelar os dados que são exibidos e armazenados no banco de dados SQLite local. O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

O `ID` propriedade é usada para identificar exclusivamente cada `TodoItem` da instância e é decorado com atributos de SQLite que tornam a propriedade de uma chave primária de incremento automático no banco de dados.

### <a name="invoking-database-operations"></a>Chamando as operações de banco de dados

O `TodoItemRepository` classe implementa as operações de banco de dados e uma instância da classe pode ser acessada por meio de `App.TodoManager` propriedade. O `TodoItemRepository` classe fornece os seguintes métodos para chamar operações de banco de dados:

- **GetAllItemsAsync** – recupera todos os itens do banco de dados SQLite local.
- **GetItemAsync** – recupera um item especificado do banco de dados SQLite local.
- **SaveItemAsync** – cria ou atualiza um item no banco de dados SQLite local.
- **DeleteItemAsync** – exclui o item especificado do banco de dados SQLite local.

### <a name="platform-project-implementations"></a>Implementações de projeto de plataforma

O `Services` pasta no projeto PCL contém o `IFileHelper` e `IAudioRecorderService` interfaces que são usadas pelo `DependencyService` classe para localizar as classes que implementam as interfaces em projetos de plataforma.

O `IFileHelper` interface é implementada pelo `FileHelper` classe em cada projeto da plataforma. Essa classe consiste em um único método, `GetLocalFilePath`, que retorna um caminho de arquivo local para armazenar o banco de dados SQLite.

O `IAudioRecorderService` interface é implementada pelo `AudioRecorderService` classe em cada projeto da plataforma. Essa classe consiste `StartRecording`, `StopRecording`e que dão suporte a métodos, que usam as APIs da plataforma para gravar áudio do microfone do dispositivo e armazená-lo como um arquivo wav. No iOS, o `AudioRecorderService` usa o `AVFoundation` API gravar áudio. No Android, o `AudioRecordService` usa o `AudioRecord` API gravar áudio. Na Universal Windows Platform (UWP), o `AudioRecorderService` usa o `AudioGraph` API gravar áudio.

### <a name="invoking-cognitive-services"></a>Invocando serviços Cognitivos

O aplicativo de exemplo invoca os seguintes serviços Cognitivos da Microsoft:

- API de fala do Microsoft. Para obter mais informações, consulte [reconhecimento de fala usando a API de fala da Microsoft](speech-recognition.md).
- API de verificação de ortografia de Bing. Para obter mais informações, consulte [ortográfica usando a API de verificação de ortografia do Bing](spell-check.md).
- Converter API. Para obter mais informações, consulte [tradução de texto usando a API do Translator](text-translation.md).
- API de detecção facial. Para obter mais informações, consulte [reconhecimento de emoções usando a API de detecção facial](emotion-recognition.md).

## <a name="related-links"></a>Links relacionados

- [Documentação dos serviços Cognitivos da Microsoft](https://www.microsoft.com/cognitive-services/documentation)
- [Serviços Cognitivos de tarefas pendentes (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
