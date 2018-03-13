---
title: Visual Basic.NET em Xamarin iOS e Android
description: "XThis passo a passo demonstra como criar aplicativos nativos de xamarin e xamarin que utilizam a lógica de negócios escrita em Visual Basic.NET."
ms.topic: article
ms.prod: xamarin
ms.assetid: 455fda67-3879-4299-8036-b12840e6a498
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 95817c2ec22c4c27f0f4a933db54105614e54030
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="visual-basicnet-in-xamarin-ios-and-android"></a>Visual Basic.NET em Xamarin iOS e Android

O [TaskyPortable](/samples/mobile/VisualBasic/TaskyPortableVB/) aplicativo de exemplo demonstra como o código do Visual Basic compilado em uma biblioteca de classes portátil pode ser usado com o Xamarin. Aqui estão algumas capturas de tela dos aplicativos resultantes em execução no iOS, Android e Windows Phone:

 [![](native-apps-images/image5.png "iOS, Android e Windows phones executando um aplicativo compilado com o Visual Basic")](native-apps-images/image5.png#lightbox)

O iOS, Android e Windows Phone projetos de exemplo são todos escritos em c#. A interface do usuário para cada aplicativo é compilada com tecnologias nativas (Storyboards, Xml e Xaml respectivamente), enquanto o `TodoItem` gerenciamento é fornecido pela biblioteca de classe portátil do Visual Basic usando um `IXmlStorage` a implementação o projeto nativo.

## <a name="sample-walkthrough"></a>Exemplo passo a passo

Este guia aborda como Visual Basic foi implementada no [TaskyPortableVB](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB) Xamarin de exemplo para iOS e Android.

> [!NOTE]
> Analise as instruções na [PCLs do Visual Basic.NET](/guides/cross-platform/application_fundamentals/pcl/portable_visual_basic_net/) antes de continuar com este guia.

## <a name="visualbasicportablelibrary"></a>VisualBasicPortableLibrary

Bibliotecas de classes portáteis do Visual Basic só pode ser criadas no Visual Studio.
A biblioteca de exemplo contém as Noções básicas do nosso aplicativo em quatro arquivos do Visual Basic:

-  IXmlStorage.vb
-  TodoItem.vb
-  TodoItemManager.vb
-  TodoItemRepositoryXML.vb


### <a name="ixmlstoragevb"></a>IXmlStorage.vb

Como comportamentos de acesso de arquivo variam significativamente entre plataformas, bibliotecas de classes portáteis não fornecem `System.IO` APIs de armazenamento em todos os perfis de arquivo. Isso significa que, se quisermos interagir diretamente com o sistema de arquivos em nosso código portátil, é necessário retornar chamada para nossos projetos nativos em cada plataforma.  Ao escrever nosso código do Visual Basic em relação a uma interface simples que pode ser implementada em c# em cada plataforma, podemos ter compartilhável código do Visual Basic que ainda tem acesso ao sistema de arquivos.

O código de exemplo usa essa interface muito simple que contém apenas dois métodos: para ler e gravar um arquivo Xml serializado.

```vb
Public Interface IXmlStorage
    Function ReadXml(filename As String) As List(Of TodoItem)
    Sub WriteXml(tasks As List(Of TodoItem), filename As String)
End Interface
```

iOS, Android e Windows Phone implementações dessa interface serão mostradas posteriormente no guia.

### <a name="todoitemvb"></a>TodoItem.vb

Essa classe contém o objeto comercial para ser usado em todo o aplicativo. Ele será definido no Visual Basic e compartilhado com o iOS, Android e Windows Phone projetos que são escritos em c#.

A definição de classe é mostrada aqui:

```vb
Public Class TodoItem
    Property ID() As Integer
    Property Name() As String
    Property Notes() As String
    Property Done() As Boolean
End Class
```

O exemplo usa o XML de serialização e desserialização para carregar e salvar os objetos TodoItem.

### <a name="todoitemmanagervb"></a>TodoItemManager.vb

A classe Manager apresenta a API' ' para o código portátil. Fornece operações CRUD básicas para o `TodoItem` classe, mas nenhuma implementação dessas operações.

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

O construtor usa uma instância de IXmlStorage como um parâmetro. Isso permite que cada plataforma fornecer sua própria implementação de trabalho enquanto ainda permite que o código portátil descrevem outras funcionalidades que podem ser compartilhadas.

### <a name="todoitemrepositoryvb"></a>TodoItemRepository.vb

A classe do repositório contém a lógica para gerenciar a lista de objetos TodoItem. O código completo é mostrado abaixo – a lógica existe principalmente para gerenciar um valor de ID exclusivo entre a TodoItems conforme eles são adicionados e removidos da coleção.

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
> Esse código é um exemplo de um mecanismo de armazenamento de dados muito básica.
> Ele é fornecido para demonstrar como uma biblioteca de classes portátil pode código em relação a uma interface para acessar a funcionalidade específica de plataforma (nesse caso, carregar e salvar um arquivo Xml). -Ele não se destina a ser uma alternativa de banco de dados de qualidade de produção.

## <a name="ios-android-and-windows-phone-application-projects"></a>iOS, Android e projetos de aplicativo do Windows Phone

Esta seção contém as implementações de plataforma específica para a interface IXmlStorage e mostra como ele é usado em cada aplicativo. Os projetos de aplicativo são todos escritos em c#.

### <a name="ios-and-android-ixmlstorage"></a>iOS e Android IXmlStorage

Xamarin e xamarin fornecem completo `System.IO` funcionalidade para carregar e salvar o arquivo Xml usando a seguinte classe:

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

No aplicativo do iOS a `TodoItemManager` e `XmlStorageImplementation` são criados no **appdelegate. CS** arquivo conforme mostrado neste trecho de código. As quatro primeiras linhas apenas estão criando o caminho para o arquivo onde os dados serão armazenados; as duas últimas linhas mostram as duas classes que está sendo criadas.

```csharp
var xmlFilename = "TodoList.xml";
string documentsPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine(documentsPath, "..", "Library"); // Library folder
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

No aplicativo do Android a `TodoItemManager` e `XmlStorageImplementation` são criados no **Application.cs** arquivo conforme mostrado neste trecho de código. As três primeiras linhas apenas estão criando o caminho para o arquivo onde os dados serão armazenados; as duas últimas linhas mostram as duas classes que está sendo criadas.

```csharp
var xmlFilename = "TodoList.xml";
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
var path = Path.Combine(libraryPath, xmlFilename);
var xmlStorage = new AndroidTodo.XmlStorageImplementation();
TaskMgr = new TodoItemManager(path, xmlStorage);
```

O restante do código do aplicativo é principalmente preocupado com a interface do usuário e usar o `TaskMgr` classe para carregar e salvar `TodoItem` classes.

### <a name="windows-phone-ixmlstorage"></a>Windows Phone IXmlStorage

Windows Phone não fornece acesso completo ao sistema de arquivos do dispositivo, em vez disso, expondo a API IsolatedStorage. O `IXmlStorage` implementação para Windows Phone tem esta aparência:

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

O `TodoItemManager` e `XmlStorageImplementation` são criados no **App.xaml.cs** arquivo conforme mostrado neste trecho de código.

```csharp
var filename = "TodoList.xml";
var xmlStorage = new XmlStorageImplementation();
TodoMgr = new TodoItemManager(filename, xmlStorage);
```

O restante do aplicativo do Windows Phone consiste em Xaml e c# para criar a interface do usuário e usar o `TodoMgr` classe para carregar e salvar `TodoItem` objetos.

## <a name="visual-basic-pcl-in-visual-studio-for-mac"></a>Visual Basic PCL no Visual Studio para Mac

O Visual Studio para Mac não oferece suporte a linguagem Visual Basic, você não pode criar ou compilar projetos do Visual Basic no Visual Studio para Mac.

O Visual Studio para o suporte do Mac para bibliotecas de classes portáteis significa que ele pode fazer referência a assemblies PCL que foram criados a partir do Visual Basic.

Esta seção explica como compilar um assembly PCL no Visual Studio e, em seguida, certifique-se que ela será armazenada em um sistema de controle de versão e referenciada por outros projetos.

### <a name="keeping-the-pcl-output-from-visual-studio"></a>Mantendo a saída PCL do Visual Studio

Por padrão, a maioria dos sistemas de controle de versão (incluindo o TFS e Git) serão configurados para ignorar o **/bin/** diretório, que significa que o assembly PCL não será armazenado. Isso significa que você precisa copiar em qualquer computador executando o Visual Studio para Mac adicionar uma referência a ele.

Para garantir que o sistema de controle de versão pode armazenar a saída de assembly PCL, você pode criar um script de pós-compilação que copia para a raiz do projeto. Essa etapa de pós-compilação ajuda a garantir o assembly pode ser facilmente adicionado ao controle de origem e compartilhado com outros projetos.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

1. Clique com botão direito no projeto e escolha o **Propriedades > eventos de Build** seção.

2. Adicionar um _pós-compilação_ script que copia a DLL de saída do projeto para o diretório raiz do projeto (que está fora do **/bin/**). Dependendo de sua configuração de controle de versão, a DLL agora deve ser capaz de ser adicionado ao controle de origem.

  [![](native-apps-images/image6-vs-sml.png "Script de compilação de postagem para copiar VB DLL de eventos de build")](native-apps-images/image6-vs.png#lightbox)

#### <a name="visual-studio-2015"></a>Visual Studio 2015

1.  Clique com botão direito no projeto e escolha **Propriedades > compilar** , em seguida, certifique-se de que todas as configurações está selecionada na caixa de combinação de superior esquerdo. Clique o **eventos de compilação...**  botão na parte inferior direita.

    [![](native-apps-images/image6.png "A seção de compilação de propriedades do projeto")](native-apps-images/image6.png#lightbox)

1.  Adicionar um script de pós-compilação que copia a DLL de saída do projeto para o diretório raiz do projeto (que está fora do **/bin/** ). Dependendo de sua configuração de controle de versão, a DLL agora deve ser capaz de ser adicionado ao controle de origem.

    [![](native-apps-images/image7.png "Janela eventos de build")](native-apps-images/image7.png#lightbox)

#### <a name="all-versions"></a>Todas as versões

Próxima vez que você compilar o projeto, o assembly de biblioteca de classes portátil será copiado para a raiz do projeto, e quando a seleção-em/commit/enviar por push as alterações a DLL será armazenado (de forma que ele pode ser baixado em um Mac com o Visual Studio para Mac).

  [![](native-apps-images/image8-sml.png "Local do arquivo de Visual Basic do assembly de saída")](native-apps-images/image8.png#lightbox)


Este assembly, em seguida, pode ser adicionado a projetos de Xamarin no Visual Studio para Mac, embora a linguagem do Visual Basic em si não tem suporte em projetos Android ou iOS Xamarin.

### <a name="referencing-the-pcl-in-visual-studio-for-mac"></a>Referenciando PCL no Visual Studio para Mac

Porque Xamarin não oferece suporte a Visual Basic não pode carregar o projeto PCL (nem o aplicativo do Windows Phone) conforme mostrado nesta captura de tela:

 [![](native-apps-images/image9.png "O Visual Studio para solução de Mac")](native-apps-images/image9.png#lightbox)

Ainda estamos podem incluir a DLL do assembly PCL do Visual Basic nos projetos xamarin e xamarin:

1.  Clique com botão direito no **referências** nó e selecione **Editar referências...**

    [![](native-apps-images/image10.png "Menu de referências do projeto editar")](native-apps-images/image10.png#lightbox)

1.  Selecione o **.Net Assembly** guia e navegue para a saída de DLL no diretório do projeto do Visual Basic. Embora o Visual Studio para Mac não é possível abrir o projeto, todos os arquivos devem existir do controle de origem. Clique em **adicionar** , em seguida, **Okey** para adicionar esse assembly para os aplicativos iOS e Android.

    [![](native-apps-images/image11-sml.png "Clique em Adicionar e Okey para adicionar esse assembly para aplicativos do Android e iOS")](native-apps-images/image11.png#lightbox)

1.  Os aplicativos iOS e Android agora podem incluir a lógica do aplicativo fornecida pela biblioteca de classe portátil do Visual Basic. Esta captura de tela mostra um aplicativo iOS que referencia o PCL do Visual Basic e tiver um código que usa a funcionalidade da biblioteca.

    [![](native-apps-images/image12-sml.png "Editar referências adicionar janela de assembly do .NET")](native-apps-images/image12.png#lightbox)


Se forem feitas alterações para o projeto do Visual Basic no Visual Studio lembrar compilar o projeto, armazene o assembly resultante DLL no controle de origem e, em seguida, extrair essa nova DLL do controle de origem para o Mac para que o Visual Studio para Mac compila contém a versão mais recente funcionalidade.


## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir o código do Visual Basic em aplicativos Xamarin usando o Visual Studio e bibliotecas de classes portáteis. Embora Xamarin não oferece suporte diretamente Visual Basic, Compilando o Visual Basic em um PCL permite código gravado com Visual Basic para ser incluído em aplicativos iOS e Android.

## <a name="related-links"></a>Links relacionados

- [TaskyPortableVB (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
