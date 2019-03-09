---
title: Visual Basic.NET no Xamarin iOS e Android
description: Este passo a passo demonstra como criar aplicativos xamarin. IOS e xamarin. Android nativos que usam a lógica de negócios escrita em Visual Basic.NET.
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 55c08c1bd0d82f8458e0c07b4202b6275600ff38
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671098"
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>Visual Basic.NET no Xamarin iOS e Android

O [TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB) aplicativo de exemplo demonstra como o código do Visual Basic compilado em uma biblioteca de classes portátil pode ser usado com o Xamarin. Aqui estão algumas capturas de tela dos aplicativos resultantes que executam o iOS, Android e Windows Phone:

 [![](native-apps-images/image5.png "iOS, Android e Windows phones executando um aplicativo criado com o Visual Basic")](native-apps-images/image5.png#lightbox)

O iOS, Android e Windows Phone projetos no exemplo são escritos em C#. A interface do usuário para cada aplicativo é compilada com tecnologias nativas (Storyboards, Xml e Xaml, respectivamente), enquanto o `TodoItem` gerenciamento é fornecido pela biblioteca de classes portátil Visual Basic usando um `IXmlStorage` implementação fornecida pelo o projeto nativo.

## <a name="sample-walkthrough"></a>Exemplo passo a passo

Este guia aborda como Visual Basic foi implementado de [TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB) exemplo de Xamarin para iOS e Android.

> [!NOTE]
> Analise as instruções na [portátil Visual Basic.NET](index.md) antes de continuar com este guia.

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

Bibliotecas de classes portáteis do Visual Basic só podem ser criadas no Visual Studio.
A biblioteca de exemplo contém as Noções básicas de nosso aplicativo nos quatro arquivos do Visual Basic:

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

Como comportamentos de acesso de arquivo variam significativamente entre plataformas, bibliotecas de classes portáteis não fornecem `System.IO` APIs de armazenamento em qualquer perfil de arquivos. Isso significa que se quisermos interagir diretamente com o sistema de arquivos em nosso código portátil, precisamos retornar a chamada para nossos projetos nativos em cada plataforma.  Ao escrever nosso código do Visual Basic em relação a uma interface simples que pode ser implementado em C# em cada plataforma, podemos ter código do Visual Basic podem ser compartilhado que ainda tem acesso ao sistema de arquivos.

O código de exemplo usa essa interface muito simple que contém apenas dois métodos: para ler e gravar um arquivo Xml serializado.

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS, Android e Windows Phone implementações dessa interface serão mostradas posteriormente no guia.

### <a name="todoitemvb"></a>TodoItem.vb

Essa classe contém o objeto comercial a ser usado em todo o aplicativo. Ele será definido no Visual Basic e compartilhado com o iOS, Android e Windows Phone projetos que são escritos em C#.

A definição de classe é mostrada aqui:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

O exemplo usa o XML de serialização e desserialização para carregar e salvar os objetos de TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

A classe Manager apresenta 'API' para o código portátil. Ele fornece operações CRUD básicas para o `TodoItem` classe, mas nenhuma implementação dessas operações.

```vb
Public Class TodoItemManager
    Private _repository As TodoItemRepositoryXML
    Public Sub New(filename As String, storage As IXmlStorage)
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

O construtor usa uma instância de IXmlStorage como um parâmetro. Isso permite que cada plataforma fornecer sua própria implementação de trabalhar enquanto ainda permite que o código portátil descrevem outras funcionalidades que podem ser compartilhadas.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

A classe de repositório contém a lógica para gerenciar a lista de objetos de TodoItem. O código completo é mostrado abaixo – a lógica existe principalmente para gerenciar um valor de ID exclusivo entre as TodoItems conforme eles são adicionados e removidos da coleção.

```vb
Public Class TodoItemRepositoryXML
    Private _filename As String
    Private _storage As IXmlStorage
    Private _tasks As List(Of TodoItem)

    ''' <summary>Constructor</summary>
    Public Sub New(filename As String, storage As IXmlStorage)
        _filename = filename
        _storage = storage
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
> Esse código é um exemplo de um mecanismo muito básica de armazenamento de dados.
> Ele é fornecido para demonstrar como uma biblioteca de classes portátil pode codificar uma interface para acessar a funcionalidade específica da plataforma (nesse caso, carregar e salvar um arquivo Xml). -Ele não se destina a ser uma alternativa de banco de dados de qualidade de produção.

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS, Android e projetos de aplicativos do Windows Phone

Esta seção contém as implementações específicas da plataforma para a interface IXmlStorage e mostra como ele é usado em cada aplicativo. Os projetos de aplicativos são escritos em C#.

### <a name="ios-and-android-ixmlstorage"></a>iOS e Android IXmlStorage

Xamarin. IOS e xamarin. Android fornecem total `System.IO` funcionalidade, portanto, você pode facilmente carregar e salvar o arquivo Xml usando a seguinte classe:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        if (File.Exists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new FileStream(filename, FileMode.Open))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(filename))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

No aplicativo iOS a `TodoItemManager` e o `XmlStorageImplementation` são criados na **AppDelegate.cs** conforme mostrado neste trecho de código do arquivo. As quatro primeiras linhas são simplesmente criar o caminho para o arquivo onde os dados serão armazenados; as duas últimas linhas mostram as duas classes seja instanciadas.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

No aplicativo Android a `TodoItemManager` e o `XmlStorageImplementation` são criados na **Application.cs** conforme mostrado neste trecho de código do arquivo. As três primeiras linhas são simplesmente criar o caminho para o arquivo onde os dados serão armazenados; as duas últimas linhas mostram as duas classes seja instanciadas.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

O restante do código do aplicativo é principalmente preocupado com a interface do usuário e usando o `TaskMgr` classe para carregar e salvar `TodoItem` classes.

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone não fornece acesso completo ao sistema de arquivos do dispositivo, em vez disso, expor a API do IsolatedStorage. O `IXmlStorage` implementação para Windows Phone tem esta aparência:

```csharp
public class XmlStorageImplementation : IXmlStorage
{
    public XmlStorageImplementation(){}
    public List<TodoItem> ReadXml(string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        if (fileStorage.FileExists(filename))
        {
            var serializer = new XmlSerializer(typeof(List<TodoItem>));
            using (var stream = new StreamReader(new IsolatedStorageFileStream(filename, FileMode.Open, fileStorage)))
            {
                return (List<TodoItem>)serializer.Deserialize(stream);
            }
        }
        return new List<TodoItem>();
    }
    public void WriteXml(List<TodoItem> tasks, string filename)
    {
        IsolatedStorageFile fileStorage = IsolatedStorageFile.GetUserStoreForApplication();
        var serializer = new XmlSerializer(typeof(List<TodoItem>));
        using (var writer = new StreamWriter(new IsolatedStorageFileStream(filename, FileMode.OpenOrCreate, fileStorage)))
        {
            serializer.Serialize(writer, tasks);
        }
    }
}
```

O `TodoItemManager` e o `XmlStorageImplementation` são criados na **App.xaml.cs** conforme mostrado neste trecho de código do arquivo.

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

O restante do aplicativo do Windows Phone é formado por Xaml e C# para criar a interface do usuário e usar o `TodoMgr` classe para carregar e salvar `TodoItem` objetos.

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic PCL no Visual Studio para Mac

O Visual Studio para Mac não oferece suporte a linguagem Visual Basic – é possível criar ou compilar projetos do Visual Basic com o Visual Studio para Mac.

O Visual Studio para o suporte do Mac para bibliotecas de classes portáteis significa que ele pode fazer referência a assemblies PCL que foram criados a partir do Visual Basic.

Esta seção explica como compilar um assembly PCL no Visual Studio e, em seguida, certifique-se de que ele será armazenado em um sistema de controle de versão e referenciado por outros projetos.

### <a name="keeping-the-pcl-output-from-visual-studio"></a>Manter a saída PCL no Visual Studio

Por padrão, a maioria dos sistemas de controle de versão (incluindo o TFS e Git) será configurado para ignorar a **/bin/** diretório, que significa que o assembly compilado de PCL não será armazenado. Isso significa que você precisaria manualmente copiá-lo para todos os computadores executando o Visual Studio para Mac para adicionar uma referência a ele.

Para garantir que seu sistema de controle de versão pode armazenar a saída do assembly PCL, você pode criar um script de pós-compilação que copia para a raiz do projeto. Essa etapa de pós-compilação ajuda a garantir que o assembly pode ser facilmente adicionado ao controle do código-fonte e compartilhado com outros projetos.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. Clique com botão direito no projeto e escolha o **Propriedades > Build Events** seção.

2. Adicionar um _pós-build_ script que copia a DLL de saída desse projeto no diretório raiz do projeto (que está fora do **/bin/**). Dependendo da sua configuração de controle de versão, a DLL deve agora ser poderão ser adicionados ao controle de origem.

  [![](native-apps-images/image6-vs-sml.png "Script de compilação de post e copiar DLL VB de eventos de build")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  Clique com botão direito no projeto e escolha **Propriedades > compilar** , em seguida, verifique se todas as configurações está selecionada na caixa de combinação de canto superior esquerdo. Clique o **eventos de Build...**  botão no canto inferior direito.

    [![](native-apps-images/image6.png "A seção de compilação de propriedades do projeto")](native-apps-images/image6.png#lightbox)

1.  Adicionar um script de pós-compilação que copia a DLL de saída desse projeto no diretório raiz do projeto (que está fora do **/bin/** ). Dependendo da sua configuração de controle de versão, a DLL deve agora ser poderão ser adicionados ao controle de origem.

    [![](native-apps-images/image7.png "Janela eventos de build")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>Todas as versões

Próxima vez que você compilar o projeto, o assembly de biblioteca de classes portátil será copiado para a raiz do projeto e ao check-no/confirmar/efetuar push suas alterações a DLL serão armazenados (de modo que ele pode ser baixado em um Mac com o Visual Studio para Mac).

  [![](native-apps-images/image8-sml.png "Local do arquivo de Visual Basic do assembly de saída")](native-apps-images/image8.png#lightbox)


Esse assembly, em seguida, pode ser adicionado a projetos do Xamarin no Visual Studio para Mac, mesmo que a linguagem Visual Basic em si não tem suporte em projetos Android ou Xamarin iOS.

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>Fazendo referência a PCL no Visual Studio para Mac

Porque o Xamarin não oferece suporte a Visual Basic não pode carregar o projeto PCL (nem o aplicativo do Windows Phone) conforme mostrado nesta captura de tela:

 [![](native-apps-images/image9.png "O Visual Studio para solução de Mac")](native-apps-images/image9.png#lightbox)

Ainda podemos incluir a DLL do assembly de PCL do Visual Basic nos projetos xamarin. IOS e xamarin. Android:

1.  Clique com botão direito no **referências** nó e selecione **Editar referências...**

    [![](native-apps-images/image10.png "Menu do projeto Editar referências")](native-apps-images/image10.png#lightbox)

1.  Selecione o **.Net Assembly** guia e navegue até a saída DLL no diretório do projeto do Visual Basic. Embora o Visual Studio para Mac não é possível abrir o projeto, todos os arquivos deveriam estar lá de controle de origem. Clique em **Add** , em seguida, **Okey** para adicionar esse assembly para os aplicativos iOS e Android.

    [![](native-apps-images/image11-sml.png "Clique em Adicionar e Okey para adicionar esse assembly para os aplicativos iOS e Android")](native-apps-images/image11.png#lightbox)

1.  Os aplicativos iOS e Android agora podem incluir a lógica do aplicativo fornecida pela biblioteca de classes portátil Visual Basic. Esta captura de tela mostra um aplicativo iOS que faz referência a PCL do Visual Basic e tem código que usa a funcionalidade da biblioteca.

    [![](native-apps-images/image12-sml.png "Editar referências adicionar janela do assembly .NET")](native-apps-images/image12.png#lightbox)


Se forem feitas alterações ao projeto do Visual Basic no Visual Studio Lembre-se de compilar o projeto, a DLL do assembly resultante da loja no controle de origem e, em seguida, efetuar pull dessa nova DLL de controle de origem em seu Mac para que o Visual Studio para Mac cria contêm a versão mais recente funcionalidade.


## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir o código do Visual Basic em aplicativos do Xamarin usando o Visual Studio e bibliotecas de classes portáteis. Mesmo que o Xamarin não oferece suporte diretamente Visual Basic, Compilando o Visual Basic em uma PCL permite que o código escrito com o Visual Basic a ser incluído em aplicativos iOS e Android.

## <a name="related-links"></a>Links relacionados

- [TaskyPortableVB (amostra)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
