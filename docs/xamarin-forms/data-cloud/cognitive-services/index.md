---
title: Adicionando inteligência com serviços Cognitivos
description: Serviços Cognitivos da Microsoft são um conjunto de APIs, SDKs e serviços disponíveis para os desenvolvedores a criar aplicativos mais inteligente adicionando recursos, como reconhecimento facial, reconhecimento de fala e compreensão de idioma. Este artigo fornece uma introdução ao aplicativo de exemplo que demonstra como invocar algumas das APIs Microsoft cognitivas serviço.
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 86253e42db7da2da6eb8b03e2d4a4b3c943b7e17
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="adding-intelligence-with-cognitive-services"></a>Adicionando inteligência com serviços Cognitivos

_Serviços Cognitivos da Microsoft são um conjunto de APIs, SDKs e serviços disponíveis para os desenvolvedores a criar aplicativos mais inteligente adicionando recursos, como reconhecimento facial, reconhecimento de fala e compreensão de idioma. Este artigo fornece uma introdução ao aplicativo de exemplo que demonstra como invocar algumas das APIs Microsoft cognitivas serviço._

## <a name="overview"></a>Visão geral

O exemplo a seguir é um aplicativo de lista de tarefas que fornece funcionalidade para:

- Exiba uma lista de tarefas.
- Adicionar e editar tarefas por meio do teclado virtual, ou realizando o reconhecimento de fala com a API de fala da Microsoft. Para obter mais informações sobre como executar o reconhecimento de fala, consulte [reconhecimento de fala, usando a API de fala Microsoft](speech-recognition.md).
- Tarefas de verificação usando a API do Bing ortográfica Verifique a ortografia. Para obter mais informações, consulte [verificação ortográfica usando a API do Bing ortográfica verificar](spell-check.md).
- Converte tarefas do inglês para o alemão, usando a API do conversor. Para obter mais informações, consulte [conversão de texto usando a API de conversor](text-translation.md).
- Exclua tarefas.
- Defina o status da tarefa para 'done'.
- O aplicativo com reconhecimento de emoção, usando a API de Face de taxa. Para obter mais informações, consulte [emoção reconhecimento usando a API de Face](emotion-recognition.md).

Tarefas são armazenadas em um banco de dados local do SQLite. Para obter mais informações sobre como usar um banco de dados local do SQLite, consulte [trabalhando com um banco de dados Local](~/xamarin-forms/app-fundamentals/databases.md).

O `TodoListPage` é exibida quando o aplicativo é iniciado. Esta página exibe uma lista de todas as tarefas no banco de dados local e permite que o usuário para criar uma nova tarefa ou para classificar o aplicativo:

![](images/sample-application-1.png "TodoListPage")

Novos itens podem ser criados, basta clicar no *+* botão, que navega para o `TodoItemPage`. Essa página também pode ser navegada, selecionando uma tarefa:

![](images/sample-application-2.png "TodoItemPage")

O `TodoItemPage` permite as tarefas a serem criados, editados, uma verificação ortográfica, convertido, salva e excluída. O reconhecimento de fala pode ser usado para criar ou editar uma tarefa. Isso é conseguido pressionando o botão de microfone para iniciar a gravação e pressionando o botão mesmo uma segunda vez para parar a gravação, que envia a gravação para a API de reconhecimento de fala do Bing.

Clicar no botão de smilies no `TodoListPage` navega para o `RateAppPage`, que é usada para executar o reconhecimento de emoção em uma imagem de uma expressão facial:

![](images/sample-application-3.png "RateAppPage")

O `RateAppPage` permite que o usuário tirar uma foto dos seus consumidores, que é enviada para a API de Face com a emoção retornada que está sendo exibida.

## <a name="understanding-the-application-anatomy"></a>Noções básicas sobre a anatomia do aplicativo

O projeto de biblioteca de classe portátil (PCL) para o aplicativo de exemplo consiste em cinco pastas principais:

|Pasta|Finalidade|
|--- |--- |
|Modelos|Contém as classes de modelo de dados para o aplicativo. Isso inclui o `TodoItem` classe, que modela um único item de dados usados pelo aplicativo. A pasta também inclui classes usadas para respostas em JSON modelo retornadas de APIs de serviço cognitivas Microsoft diferentes.|
|Repositórios|Contém o `ITodoItemRepository` interface e `TodoItemRepository` classe que são usados para executar operações de banco de dados.|
|Serviços|Contém as interfaces e classes que são usadas para acessar diferentes Microsoft cognitivas APIs de serviço, juntamente com as interfaces que são usadas pelo `DependencyService` classe para localizar as classes que implementam as interfaces em projetos de plataforma.|
|Utilitários|Contém o `Timer` classe, que é usado pelo `AuthenticationService` classe para renovar um token de acesso JWT cada 9 minutos.|
|Exibições|Contém as páginas do aplicativo.|

O projeto PCL também contém alguns arquivos importantes:

|Arquivo|Finalidade|
|--- |--- |
|Constants.cs|O `Constants` classe, que especifica as chaves de API e os pontos de extremidade para o Microsoft cognitivas APIs de serviço que são invocados. As constantes de chave de API exigem atualização para acessar as APIs de serviço cognitivas diferentes.|
|App.xaml.cs|O `App` classe é responsável pela instanciação ambos a primeira página que será exibida no aplicativo em cada plataforma e o `TodoManager` classe que é usado para chamar operações de banco de dados.|

### <a name="nuget-packages"></a>Pacotes NuGet

O aplicativo de exemplo usa os seguintes pacotes do NuGet:

- `Newtonsoft.Json` – Fornece uma estrutura JSON para .NET.
- `PCLStorage` – Fornece um conjunto de arquivos local da plataforma cruzada APIs de e/s.
- `sqlite-net-pcl` – Fornece armazenamento de banco de dados SQLite.
- `Xam.Plugin.Media` – Fornece pegando de foto de plataforma cruzada e APIs de separação.

Além disso, esses pacotes do NuGet instalam suas próprias dependências.

### <a name="modeling-the-data"></a>Modelagem de dados

O aplicativo de exemplo usa o `TodoItem` classe para modelar os dados que são exibidos e armazenados no banco de dados local do SQLite. O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

O `ID` propriedade é usada para identificar exclusivamente cada `TodoItem` instância e está decorado com atributos SQLite que definir a propriedade de uma chave primária de incremento automático no banco de dados.

### <a name="invoking-database-operations"></a>Invocar operações de banco de dados

O `TodoItemRepository` classe implementa as operações de banco de dados e uma instância da classe pode ser acessada por meio de `App.TodoManager` propriedade. O `TodoItemRepository` classe fornece os seguintes métodos para chamar operações de banco de dados:

- **GetAllItemsAsync** – recupera todos os itens do banco de dados local do SQLite.
- **GetItemAsync** – recupera um item especificado do banco de dados local do SQLite.
- **SaveItemAsync** – cria ou atualiza um item no banco de dados local do SQLite.
- **DeleteItemAsync** – exclui o item especificado do banco de dados local do SQLite.

### <a name="platform-project-implementations"></a>Implementações de projeto de plataforma

O `Services` pasta no projeto PCL contém o `IFileHelper` e `IAudioRecorderService` interfaces que são usadas pelo `DependencyService` classe para localizar as classes que implementam as interfaces em projetos de plataforma.

O `IFileHelper` interface é implementada pelo `FileHelper` classe em cada projeto da plataforma. Essa classe consiste em um único método, `GetLocalFilePath`, que retorna um caminho de arquivo local para armazenar o banco de dados SQLite.

O `IAudioRecorderService` interface é implementada pelo `AudioRecorderService` classe em cada projeto da plataforma. Essa classe consiste `StartRecording`, `StopRecording`e dando suporte a métodos que usam APIs de plataforma para gravar áudio do microfone do dispositivo e armazená-lo como um arquivo wav. No iOS, o `AudioRecorderService` usa o `AVFoundation` API para gravar áudio. No Android, o `AudioRecordService` usa o `AudioRecord` API para gravar áudio. Sobre o Windows UWP (plataforma Universal), o `AudioRecorderService` usa o `AudioGraph` API para gravar áudio.

### <a name="invoking-cognitive-services"></a>Invocando serviços Cognitivos

O aplicativo de exemplo invoca os seguintes serviços cognitivas da Microsoft:

- Microsoft Speech API. Para obter mais informações, consulte [reconhecimento de fala, usando a API de fala Microsoft](speech-recognition.md).
- Verificação de ortografia Bing API. Para obter mais informações, consulte [verificação ortográfica usando a API do Bing ortográfica verificar](spell-check.md).
- Traduz API. Para obter mais informações, consulte [conversão de texto usando a API de conversor](text-translation.md).
- API de face. Para obter mais informações, consulte [emoção reconhecimento usando a API de Face](emotion-recognition.md).

## <a name="related-links"></a>Links relacionados

- [Documentação do Microsoft serviços Cognitivos](https://www.microsoft.com/cognitive-services/documentation)
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
