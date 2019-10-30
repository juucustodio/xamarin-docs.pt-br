---
title: File system access in Xamarin.iOS
description: This document describes how to work with the file system in Xamarin.iOS. It discusses directories, reading files, XML and JSON serialization, the application sandbox, sharing files through iTunes, and more.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/12/2018
ms.openlocfilehash: c9e5b2504fd8af8b4232eea0dcf39d9c4760b555
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010613"
---
# <a name="file-system-access-in-xamarinios"></a>File system access in Xamarin.iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)

You can use Xamarin.iOS and the `System.IO` classes in the *.NET Base Class Library (BCL)* to access the iOS file system. A classe `File` permite criar, excluir e ler arquivos e a classe `Directory` permite criar, excluir ou enumerar o conteúdo de diretórios. You can also use `Stream` subclasses, which can provide a greater degree of control over file operations (such as compression or position search within a file).

iOS imposes some restrictions on what an application can do with the file system to preserve the security of an application’s data, and to protect users from malignant apps. These restrictions are part of the *Application Sandbox* – a set of rules that limits an application’s access to files, preferences, network resources, hardware, etc. An application is limited to reading and writing files within its home directory (installed location); it cannot access another application’s files.

iOS also has some file system-specific features: certain directories require special treatment with respect to backups and upgrades, and applications can also share files with each other and the **Files** app (since iOS 11), and via iTunes.

This article discusses the features and restrictions of the iOS file system, and includes a sample application that demonstrates how to use Xamarin.iOS to execute some simple file system operations:

[![A sample of iOS executing some simple file system operations](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>General file access

Xamarin.iOS allows you to use the .NET `System.IO` classes for file system operations on iOS.

The following code snippets illustrate some common file operations. You’ll find them all below in the **SampleCode.cs** file, in the sample application for this article.

### <a name="working-with-directories"></a>Working with directories

This code enumerates the subdirectories in the current directory (specified by the "./" parameter), which is the location of your application executable.
Your output will be a list of all the files and folders that are deployed with your application (displayed in the console window while you are debugging).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>Reading files

To read a text file, you only need a single line of code. This example will display the contents of a text file in the Application Output window.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>Serialização XML

Although working with the complete `System.Xml` namespace is beyond the scope of this article, you can easily deserialize an XML document from the file system by using a StreamReader like this code snippet:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

For more information, see the documentation for [System.Xml](xref:System.Xml) and [serialization](xref:System.Xml.Serialization). See the [Xamarin.iOS documentation](~/ios/deploy-test/linker.md) on the linker – often you will need to add the `[Preserve]` attribute to classes you intend to serialize.

### <a name="creating-files-and-directories"></a>Creating files and directories

This sample shows how to use the `Environment` class to access the Documents folder where we can create files and directories.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

A criação de um diretório é um processo semelhante:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Para obter mais informações, consulte a [referência da API do System.Io](xref:System.IO).

### <a name="serializing-json"></a>Serializando JSON

[JSON.net](http://www.newtonsoft.com/json) é uma estrutura JSON de alto desempenho que funciona com o Xamarin. Ios e está disponível no NuGet. Adicione o pacote NuGet ao seu projeto de aplicativo, usando **Adicionar NuGet** no Visual Studio para Mac:

[![](file-system-images/json01.png "Adding the NuGet package to the applications project")](file-system-images/json01.png#lightbox)

Em seguida, adicione uma classe para atuar como o modelo de dados para serialização/desserialização (neste caso `Account.cs`):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }

        public Account() {
        }
    }
}
```

Por fim, crie uma instância da classe `Account`, Serialize-a para dados JSON e grave-a em um arquivo:

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```

Para obter mais informações sobre como trabalhar com dados JSON em um aplicativo .NET, consulte a [documentação](https://www.newtonsoft.com/json/help)do JSON. net.

## <a name="special-considerations"></a>Considerações especiais

Apesar das semelhanças entre as operações de arquivo do Xamarin. iOS e do .NET, o iOS e o Xamarin. iOS são diferentes do .NET de algumas maneiras importantes.

### <a name="making-project-files-accessible-at-runtime"></a>Tornando os arquivos de projeto acessíveis em tempo de execução

Por padrão, se você adicionar um arquivo ao seu projeto, ele não será incluído no assembly final e, portanto, não estará disponível para seu aplicativo. Para incluir um arquivo no assembly, você deve marcá-lo com uma ação de compilação especial, chamada conteúdo.

Para marcar um arquivo para inclusão, clique com o botão direito do mouse nos arquivos e escolha **criar ação &gt; conteúdo** em Visual Studio para Mac. Você também pode alterar a **ação de Build** na folha de **Propriedades** do arquivo.

### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

É importante entender que o sistema de arquivos iOS diferencia *maiúsculas de minúsculas*. Diferenciação de maiúsculas e minúsculas significa que os nomes de arquivos e diretórios devem corresponder exatamente – **README. txt** e **README. txt** seriam considerados nomes de arquivo diferentes.

Isso pode ser confuso para os desenvolvedores do .NET que estão mais familiarizados com o sistema de arquivos do Windows, que não diferencia *maiúsculas de minúsculas* – **arquivos**, **arquivos**e **arquivos** se refereriam ao mesmo diretório.

> [!WARNING]
> O simulador do iOS não diferencia maiúsculas de minúsculas.
> Se o nome de arquivo estiver em maiúsculas e minúsculas entre o próprio arquivo e as referências a ele no código, seu código ainda poderá funcionar no simulador, mas ele falhará em um dispositivo real. Esse é um dos motivos pelos quais é importante implantar e testar em um dispositivo real cedo e com frequência durante o desenvolvimento do iOS.

### <a name="path-separator"></a>Separador de caminho

o iOS usa a barra '/' como o separador de caminho (que é diferente do Windows, que usa a barra invertida ' \ ').

Devido a essa diferença confusa, é uma boa prática usar o método `System.IO.Path.Combine`, que se ajusta à plataforma atual, em vez de codificar um separador de caminho específico. Essa é uma etapa simples que torna seu código mais portátil para outras plataformas.

## <a name="application-sandbox"></a>Área restrita do aplicativo

O acesso do seu aplicativo ao sistema de arquivos (e outros recursos, como os recursos de rede e hardware) é limitado por motivos de segurança. Essa restrição é conhecida como *área restrita do aplicativo*. Em termos do sistema de arquivos, seu aplicativo é limitado à criação e exclusão de arquivos e diretórios em seu diretório base.

O diretório base é um local exclusivo no sistema de arquivos onde o aplicativo e todos os seus dados são armazenados. Você não pode escolher (ou alterar) o local do diretório base para seu aplicativo; no entanto, o iOS e o Xamarin. iOS fornecem propriedades e métodos para gerenciar os arquivos e diretórios dentro do.

## <a name="the-application-bundle"></a>O pacote de aplicativos

O *pacote de aplicativos* é a pasta que contém o aplicativo.
Ele é diferenciado de outras pastas, fazendo com que o sufixo. app seja adicionado ao nome do diretório. Seu pacote de aplicativos contém o arquivo executável e todo o conteúdo (arquivos, imagens, etc.) necessários para seu projeto.

Quando você navega para seu pacote de aplicativos em Mac OS, ele aparece com um ícone diferente do que você vê em outros diretórios (e o sufixo **. app** é oculto); no entanto, é apenas um diretório regular que o sistema operacional está exibindo de forma diferente.

Para exibir o pacote de aplicativos para o código de exemplo, clique com o botão direito do mouse no projeto em **Visual Studio para Mac** e selecione **revelar no Finder**. Em seguida, navegue até o diretório **bin/** no qual você deve encontrar um ícone de aplicativo (semelhante à captura de tela abaixo).

![Navegue pelo diretório bin para encontrar um ícone de aplicativo semelhante a esta captura de tela](file-system-images/40-bundle.png)

Clique com o botão direito do mouse nesse ícone e escolha **Mostrar conteúdo do pacote** para procurar o conteúdo do diretório do pacote de aplicativos. O conteúdo aparece exatamente como o conteúdo de um diretório regular, como mostrado aqui:

[![o conteúdo do pacote de aplicativos](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

The application bundle is what’s installed on the simulator or on your device during testing, and ultimately it is what’s submitted to Apple for inclusion in the App Store.

## <a name="application-directories"></a>Application directories

When your application is installed on a device, the operating system creates a home directory for your application, and creates a number of directories within the application root directory that are available for use. Since iOS 8, the user-accessible directories are [NOT located](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) within the application root, so you can't derive the paths for the application bundle from the user directories, or vice versa.

These directories, how to determine their path, and their purposes are listed below:

&nbsp;

|Diretório|Descrição|
|---|---|
|[ApplicationName].app/|**In iOS 7 and earlier**, this is the `ApplicationBundle` directory where your application executable is stored. The directory structure that you create in your app exists in this directory (for example, images and other file types that you’ve marked as Resources in your Visual Studio for Mac project).<br /><br />If you need to access the content files inside your Application Bundle, the path to this directory is available via the `NSBundle.MainBundle.BundlePath` property.|
|Documents/|Use this directory to store user documents and application data files.<br /><br />The contents of this directory can be made available to the user through iTunes file sharing (although this is disabled by default). Add a `UIFileSharingEnabled` Boolean key to the Info.plist file to allow users to access these files.<br /><br />Even if an application doesn’t immediately enable file sharing, you should avoid placing files that should be hidden from your users in this directory (such as database files, unless you intend to share them). As long as sensitive files remain hidden, these files will not be exposed (and potentially moved, modified, or deleted by iTunes) if file sharing is enabled in a future version.<br /><br /> You can use the `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` method to get the path to the Documents directory for your application.<br /><br />The contents of this directory are backed up by iTunes.|
|Library/|The Library directory is a good place to store files that are not created directly by the user, such as databases or other application-generated files. The contents of this directory are never exposed to the user via iTunes.<br /><br />You can create your own subdirectories in Library; however, there are already some system-created directories here that you should be aware of, including Preferences and Caches.<br /><br />The contents of this directory (except for the Caches subdirectory) are backed up by iTunes. Custom directories that you create in Library will be backed up.|
|Library/Preferences/|Application-specific preference files are stored in this directory. Do not create these files directly. Instead, use the `NSUserDefaults` class.<br /><br />The contents of this directory are backed up by iTunes.|
|Library/Caches/|The Caches directory is a good place to store data files that can help your application run, but that can be easily re-created. The application should create and delete these files as needed and be able to re-create these files if necessary. iOS 5 may also delete these files (under low storage situations), however it will not do so while the application is running.<br /><br />The contents of this directory are NOT backed up by iTunes, which means they will not be present if the user restores a device, and they may not be present after an updated version of your application is installed.<br /><br />For instance, in case your application can't connect to the network, you might use the Caches directory to store data or files to provide a good offline experience. The application can save and retrieve this data quickly while waiting for network responses, but it doesn’t need to be backed up and can easily be recovered or re-created after a restore or version update.|
|tmp/|Applications can store temporary files that are only needed for a short period in this directory. To conserve space, files should be deleted when they are no longer required. The operating system may also delete files from this directory when an application is not running.<br /><br />The contents of this directory are NOT backed up by iTunes.<br /><br />For example, the tmp directory might be used to store temporary files that are downloaded for display to the user (such as Twitter avatars or email attachments), but that could be deleted once they've been viewed (and downloaded again if they are required in the future).|

This screenshot shows the directory structure in a Finder window:

[![](file-system-images/08-library-directory.png "This screenshot shows the directory structure in a Finder window")](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>Accessing other directories programmatically

The earlier directory and file examples accessed the `Documents` directory. Para gravar em outro diretório, você deve construir um caminho usando a sintaxe "..", conforme mostrado aqui:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

A criação de um diretório é semelhante:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Caminhos para os diretórios `Caches` e `tmp` podem ser construídos desta forma:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>Compartilhamento com o aplicativo de arquivos

o iOS 11 introduziu o aplicativo **arquivos** – um navegador de arquivos para IOS que permite ao usuário ver e interagir com seus arquivos no iCloud e também armazenados por qualquer aplicativo que ofereça suporte a ele. Para permitir que o usuário acesse os arquivos diretamente em seu aplicativo, crie uma nova chave booliana no arquivo **info. plist** `LSSupportsOpeningDocumentsInPlace` e defina-a como `true`, como aqui:

![Definir LSSupportsOpeningDocumentsInPlace no info. plist](file-system-images/51-supports-opening.png)

O diretório de **documentos** do aplicativo agora estará disponível para navegação no aplicativo **arquivos** . No aplicativo **arquivos** , navegue até **em meu iPhone** e cada aplicativo com arquivos compartilhados ficará visível. As capturas de tela abaixo mostram a aparência do [aplicativo de exemplo FileSystem](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode) :

![aplicativo de arquivos do iOS 11](file-system-images/50-files-app-1-sml.png) ![Procurar meus arquivos do iPhone](file-system-images/50-files-app-2-sml.png) ![Arquivos de aplicativo de exemplo](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Compartilhando arquivos com o usuário por meio do iTunes

Os usuários podem acessar os arquivos no diretório de documentos do seu aplicativo editando `Info.plist` e criando um **aplicativo que dá suporte à entrada do iTunes Sharing** (`UIFileSharingEnabled`) na exibição da **fonte** , como mostrado aqui:

[![adicionar o aplicativo dá suporte à propriedade de compartilhamento do iTunes](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Esses arquivos podem ser acessados no iTunes quando o dispositivo estiver conectado e o usuário escolher a guia `Apps`. Por exemplo, a captura de tela a seguir mostra os arquivos no aplicativo selecionado compartilhado por meio do iTunes:

[![esta captura de tela mostra os arquivos no aplicativo selecionado compartilhado por meio do iTunes](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Os usuários só podem acessar os itens de nível superior neste diretório por meio do iTunes. Eles não podem ver o conteúdo de nenhum subdiretório (embora possam copiá-los para seu computador ou excluí-los). Por exemplo, com arquivos GoodReader, PDF e EPUB podem ser compartilhados com o aplicativo para que os usuários possam lê-los em seus dispositivos iOS.

Os usuários que modificarem o conteúdo de suas pastas de documentos poderão causar problemas se não forem cuidadosos. Seu aplicativo deve levar isso em consideração e ser resiliente a atualizações destrutivas da pasta documentos.

O código de exemplo deste artigo cria um arquivo e uma pasta na pasta documentos (em **SampleCode.cs**) e habilita o compartilhamento de arquivos no arquivo **info. plist** . Esta captura de tela mostra como eles aparecem no iTunes:

[![esta captura de tela mostra como os arquivos aparecem no iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Consulte o artigo [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) para obter informações sobre como definir ícones para o aplicativo e para qualquer tipo de documento personalizado que você criar.

Se a chave de `UIFileSharingEnabled` for falsa ou não estiver presente, o compartilhamento de arquivos será, por padrão, desabilitado e os usuários não poderão interagir com o diretório de documentos.

## <a name="backup-and-restore"></a>Backup e restauração

Quando um dispositivo é submetido a backup pelo iTunes, todos os diretórios criados no diretório base do seu aplicativo serão salvos, exceto os seguintes diretórios:

- **[ApplicationName]. app** – não grave nesse diretório, pois ele está assinado e, portanto, deve permanecer inalterado após a instalação. Ele pode conter recursos que você acessa do seu código, mas eles não exigem backup, pois eles seriam restaurados rebaixando o aplicativo.
- **Biblioteca/caches** – o diretório de cache destina-se a arquivos de trabalho que não precisam de backup.
- **tmp** – esse diretório é usado para arquivos temporários que são criados e excluídos quando não são mais necessários ou para arquivos que o Ios exclui quando precisa de espaço.

O backup de uma grande quantidade de dados pode levar muito tempo. Se você decidir que precisa fazer backup de qualquer documento ou dado específico, seu aplicativo deverá usar os documentos e as pastas de biblioteca. Para dados transitórios ou arquivos que podem ser recuperados facilmente da rede, use os caches ou o diretório tmp.

> [!NOTE]
> o iOS limpará o sistema de arquivos quando um dispositivo for executado criticamente baixo no espaço em disco.
> Esse processo removerá todos os arquivos da biblioteca/caches e da pasta tmp de aplicativos que não estão em execução no momento.

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>Conformidade com as restrições de backup de iCloud do iOS 5

> [!NOTE]
> Embora essa política tenha sido introduzida pela primeira vez com o iOS 5 (que parece muito tempo atrás), a diretriz ainda é relevante para os aplicativos atuais.

A Apple introduziu a funcionalidade de *backup do icloud* com iOS 5. Quando o backup do iCloud está habilitado, todos os arquivos no diretório base do aplicativo (excluindo os diretórios que normalmente não são incluídos no backup, por exemplo, o pacote de aplicativos, `Caches`e `tmp`) são submetidos a backup em servidores iCloud. Esse recurso fornece ao usuário um backup completo caso seu dispositivo seja perdido, roubado ou danificado.

Como o iCloud fornece apenas 5 GB de espaço livre para cada usuário e para evitar o uso desnecessário de largura de banda, a Apple espera que os aplicativos apenas faça backup de dados essenciais gerados pelo usuário. Para obedecer às diretrizes de armazenamento de dados do iOS, você deve limitar a quantidade de dados que são submetidos a backup ao aderir aos seguintes itens:

- Armazene somente dados gerados pelo usuário ou dados que não podem ser recriados, no diretório documentos (cujo backup é feito).
- Armazene quaisquer outros dados que possam ser facilmente recriados ou rebaixados em `Library/Caches` ou em `tmp` (o que não é feito backup e pode ser ' limpo ').
- Se você tiver arquivos que podem ser apropriados para a pasta `Library/Caches` ou `tmp`, mas não quiser que o seja ' limpo ', armazene-os em outro lugar (como `Library/YourData`) e aplique o atributo ' não fazer backup ' para impedir que os arquivos usem a largura de banda de backup do iCloud e Stora espaço GE. Esses dados ainda usam espaço no dispositivo, portanto, você deve gerenciá-lo cuidadosamente e excluí-lo quando possível.

O atributo ' não fazer backup ' é definido usando a classe `NSFileManager`. Verifique se sua classe está `using Foundation` e chame `SetSkipBackupAttribute` assim:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Quando `SetSkipBackupAttribute` é `true` o backup do arquivo não será feito, independentemente do diretório no qual ele está armazenado (até mesmo o diretório de `Documents`). Você pode consultar o atributo usando o método `GetSkipBackupAttribute` e pode redefini-lo chamando o método `SetSkipBackupAttribute` com `false`, da seguinte maneira:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Compartilhando dados entre aplicativos iOS e extensões de aplicativo

Como as extensões de aplicativo são executadas como parte de um aplicativo host (em oposição ao aplicativo que as contém), o compartilhamento de dados não é automaticamente incluído para que o trabalho extra seja necessário. Grupos de aplicativos são o mecanismo que o iOS usa para permitir que aplicativos diferentes compartilhem dados. Se os aplicativos tiverem sido configurados corretamente com os direitos e o provisionamento corretos, eles poderão acessar um diretório compartilhado fora da área restrita do iOS normal.

### <a name="configure-an-app-group"></a>Configurar um grupo de aplicativos

O local compartilhado é configurado usando um [grupo de aplicativos](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que é configurado na seção **Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)** no [iOS Dev Center (Centro de Desenvolvimento iOS)](https://developer.apple.com/devcenter/ios/). Esse valor também deve ser referenciado no **Entitlements.plist** de cada projeto.

Para obter informações sobre como criar e configurar um grupo de aplicativos, consulte o guia de [recursos do grupo de aplicativos](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

### <a name="files"></a>Arquivos

O aplicativo iOS e a extensão também podem compartilhar arquivos usando um caminho de arquivo comum (já que eles foram configurados corretamente com os direitos e o provisionamento corretos):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> Se o caminho do grupo retornado for `null`, verifique a configuração dos direitos e o perfil de provisionamento e verifique se eles estão corretos.

## <a name="application-version-updates"></a>Atualizações de versão do aplicativo

Quando uma nova versão do seu aplicativo é baixada, o iOS cria um novo diretório base e armazena o novo pacote de aplicativos nele. em seguida, o iOS move as seguintes pastas da versão anterior do seu pacote de aplicativos para o novo diretório base:

- **Documentos**
- **Library**

Outros diretórios também podem ser copiados e colocados em seu novo diretório base, mas não há garantia de que eles sejam copiados, de modo que seu aplicativo não deve depender desse comportamento do sistema.

## <a name="summary"></a>Resumo

Este artigo mostrou que as operações do sistema de arquivos com o Xamarin. iOS são semelhantes a qualquer outro aplicativo .NET. Ele também introduziu a área restrita do aplicativo e examinou as implicações de segurança que ela causa. Em seguida, ele explorou o conceito de um pacote de aplicativos. Por fim, ele enumerou os diretórios especializados disponíveis para seu aplicativo e explicou suas funções durante upgrades e backups de aplicativos.

## <a name="related-links"></a>Links relacionados

- [Código de exemplo FileSystem](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)
- [Guia de programação do sistema de arquivos](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registrando os tipos de arquivo com suporte no seu aplicativo](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
