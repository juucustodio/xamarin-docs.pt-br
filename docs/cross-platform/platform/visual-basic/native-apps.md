---
title: Visual Basic no Xamarin. Android e no Xamarin. iOS
description: Este tutorial demonstra como compilar aplicativos Xamarin. iOS e Xamarin. Android nativos que usam a lógica de negócios escrita no Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 77df7d986f63861667c67b9c74b88ea6176f42ad
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458180"
---
# <a name="visual-basic-in-xamarin-android-and-ios"></a>Visual Basic no Xamarin Android e iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)

O aplicativo de exemplo [TaskyVB](/samples/xamarin/mobile-samples/visualbasic-taskyvb/) demonstra como Visual Basic código compilado em uma biblioteca .net Standard pode ser usado com o Xamarin. Aqui estão algumas capturas de tela dos aplicativos resultantes em execução no Android e iOS:

 [![Android e iOS executando um aplicativo criado com Visual Basic](native-apps-images/simulators-sml.png)](native-apps-images/simulators.png#lightbox)

Os projetos do Android e do iOS no exemplo são todos escritos em C#. A interface do usuário para cada aplicativo é criada com tecnologias nativas, enquanto o `TodoItem` gerenciamento é fornecido pela biblioteca Visual Basic .net Standard usando um arquivo XML (para fins de demonstração, não um banco de dados completo).

## <a name="sample-walkthrough"></a>Passo a passo de exemplo

Este guia discute como Visual Basic foi implementado no exemplo do Xamarin [TaskyVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB) para IOS e Android.

> [!NOTE]
> Examine as instruções em [Visual Basic e .net Standard](index.md) antes de continuar com este guia.
>
> Consulte o [Xamarin. Forms usando](xamarin-forms.md) instruções de Visual Basic para ver como criar um aplicativo com código de Visual Basic de interface de usuário compartilhada.

## <a name="visualbasicnetstandard"></a>VisualBasicNetStandard

Visual Basic .NET Standard bibliotecas só podem ser criadas no Visual Studio no Windows.
A biblioteca de exemplo contém os conceitos básicos de nosso aplicativo nesses Visual Basic arquivos:

- TodoItem. vb
- TodoItemManager. vb
- TodoItemRepositoryXML. vb
- Xmlstorage. vb

### <a name="todoitemvb"></a>TodoItem. vb

Essa classe contém o objeto comercial a ser usado em todo o aplicativo. Ele será definido em Visual Basic e compartilhado com os projetos Android e iOS que são escritos em C#.

A definição de classe é mostrada aqui:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

O exemplo usa serialização e desserialização de XML para carregar e salvar os objetos TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager. vb

A classe Manager apresenta a ' API ' para o código portátil. Ele fornece operações CRUD básicas para a `TodoItem` classe, mas nenhuma implementação dessas operações.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String)
        _repository = New TodoItemRepositoryXML(filename, storage)
    End Sub
    Public Function GetTask(id As Integer) As TodoItem
        Return _repository.GetTask(id)
    End Function
    Public Function GetTasks() As List(Of TodoItem)
        Return New List(Of TodoItem)(_repository.GetTasks())
    End Function
    Public Function SaveTask(item As TodoItem) As Integer
        Return _repository.SaveTask(item)
    End Function
    Public Function DeleteTask(item As TodoItem) As Integer
        Return _repository.DeleteTask(item.ID)
    End Function
End Class
```

O construtor usa uma instância de IXmlStorage como um parâmetro. Isso permite que cada plataforma forneça sua própria implementação de trabalho e, ao mesmo tempo, permita que o código portátil Descreva outras funcionalidades que podem ser compartilhadas.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository. vb

A classe Repository contém a lógica para gerenciar a lista de objetos TodoItem. O código completo é mostrado abaixo – a lógica existe principalmente para gerenciar um valor de ID exclusivo em TodoItems à medida que eles são adicionados e removidos da coleção.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String)
        _filename = filename
        _storage = New XmlStorage
        _tasks = _storage.ReadXml(filename)
    End Sub
    ''' <summary>Inefficient search for a Task by ID</summary>
    Public Function GetTask(id As Integer) As TodoItem
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                Return _tasks(t)
            End If
        Next
        Return New TodoItem() With {.ID = id}
    End Function
    ''' <summary>List all the Tasks</summary>
    Public Function GetTasks() As IEnumerable(Of TodoItem)
        Return _tasks
    End Function
    ''' <summary>Save a Task to the Xml file
    ''' Calculates the ID as the max of existing IDs</summary>
    Public Function SaveTask(item As TodoItem) As Integer
        Dim max As Integer = 0
        If _tasks.Count > 0 Then
            max = _tasks.Max(Function(t As TodoItem) t.ID)
        End If
        If item.ID = 0 Then
            item.ID = ++max
            _tasks.Add(item)
        Else
            Dim j = _tasks.Where(Function(t) t.ID = item.ID).First()
            j = item
        End If
        _storage.WriteXml(_tasks, _filename)
        Return max
    End Function
    ''' <summary>Removes the task from the XMl file</summary>
    Public Function DeleteTask(id As Integer) As Integer
        For t As Integer = 0 To _tasks.Count - 1
            If _tasks(t).ID = id Then
                _tasks.RemoveAt(t)
                _storage.WriteXml(_tasks, _filename)
                Return 1
            End If
        Next
        Return -1
    End Function
End Class
```

> [!NOTE]
> Esse código é um exemplo de um mecanismo de armazenamento de dados muito básico.
> Ele é fornecido para demonstrar como uma biblioteca de .NET Standard pode codificar em uma interface para acessar a funcionalidade específica da plataforma (nesse caso, carregar e salvar um arquivo XML). Ele não pretende ser uma alternativa de banco de dados de qualidade de produção.

## <a name="android-and-ios-application-projects"></a>Projetos de aplicativos Android e iOS

### <a name="ios"></a>iOS

No aplicativo iOS, o `TodoItemManager` e o `XmlStorageImplementation` são criados no arquivo **AppDelegate.cs** , conforme mostrado neste trecho de código. As primeiras quatro linhas estão apenas criando o caminho para o arquivo em que os dados serão armazenados; as duas linhas finais mostram as duas classes que estão sendo instanciadas.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

### <a name="android"></a>Android

No aplicativo Android, o `TodoItemManager` e o `XmlStorageImplementation` são criados no arquivo **Application.cs** , conforme mostrado neste trecho de código. As três primeiras linhas estão apenas criando o caminho para o arquivo em que os dados serão armazenados; as duas linhas finais mostram as duas classes que estão sendo instanciadas.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);

TaskMgr = new TodoItemManager(path);
```

O restante do código do aplicativo se preocupa principalmente com a interface do usuário e usando a `TaskMgr` classe para carregar e salvar `TodoItem` classes.

## <a name="visual-studio-2019-for-mac"></a>Visual Studio 2019 para Mac

> [!WARNING]
> Visual Studio para Mac não dá suporte à edição do idioma do Visual Basic – não há itens de menu para criar Visual Basic projetos ou arquivos. Se você abrir um **. vb** , não haverá realce de sintaxe de linguagem, preenchimento automático ou IntelliSense.

O Visual Studio 2019 para Mac _pode_ compilar projetos de .net standard do Visual Studio criados no Windows, para que aplicativos Ios possam fazer referência a esses projetos.

O Visual Studio 2017 _não pode_ Compilar Visual Basic projetos.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir Visual Basic código em aplicativos Xamarin usando o Visual Studio e bibliotecas de .NET Standard. Embora o Xamarin não ofereça suporte a Visual Basic diretamente, a compilação de Visual Basic em uma biblioteca .NET Standard permite que o código escrito com Visual Basic seja incluído nos aplicativos iOS e Android.

## <a name="related-links"></a>Links Relacionados

- [TaskyVB (exemplo de .NET Standard)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyVB)
- [Novidades no .NET Standard](/dotnet/standard/whats-new/whats-new-in-dotnet-standard?tabs=csharp)