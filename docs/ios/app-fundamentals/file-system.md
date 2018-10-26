---
title: Trabalhando com o sistema de arquivos no xamarin. IOS
description: Este documento descreve como trabalhar com o sistema de arquivos no xamarin. IOS. Ele aborda os diretórios, leitura de arquivos, a serialização de XML e JSON, a proteção do aplicativo, compartilhamento de arquivos por meio do iTunes e muito mais.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: cf595b57906cf1c47acdcdbcddf04bfbdc963393
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113424"
---
# <a name="working-with-the-file-system-in-xamarinios"></a>Trabalhando com o sistema de arquivos no xamarin. IOS

Você pode usar o xamarin. IOS e o `System.IO` as classes a *.NET Base Class Library (BCL)* para acessar o sistema de arquivos do iOS. O `File` classe permite que você criar, excluir e ler arquivos e o `Directory` classe permite que você criar, excluir ou enumerar o conteúdo dos diretórios. Você também pode usar `Stream` subclasses, que podem fornecer um maior grau de controle sobre as operações de arquivo (por exemplo, a pesquisa de compactação ou a posição dentro de um arquivo).

iOS impõe algumas restrições sobre o que um aplicativo pode fazer com o sistema de arquivos para preservar a segurança dos dados de um aplicativo e para proteger os usuários de aplicativos malignant. Essas restrições são parte dos *de proteção do aplicativo* – um conjunto de regras que limita o acesso de um aplicativo para arquivos, preferências, recursos de rede, hardware, etc. Um aplicativo é limitado a ler e gravar arquivos em seu diretório base (local de instalação); ele não pode acessar arquivos de outro aplicativo.

iOS também tem alguns recursos específicos do sistema de arquivos: determinados diretórios exigem tratamento especial em relação aos backups e as atualizações e aplicativos também podem compartilhar arquivos por meio do iTunes.

Este artigo aborda os recursos e restrições do iOS, sistema de arquivos em detalhes e inclui um aplicativo de exemplo que demonstra como usar o xamarin. IOS para executar algumas operações de sistema de arquivos simples:

 [![](file-system-images/05-sampleapp.png "Um exemplo de iOS para executar algumas operações de sistema de arquivos simples")](file-system-images/05-sampleapp.png#lightbox)

 <a name="General_File_Access" />

## <a name="general-file-access"></a>Acesso a arquivos geral

Xamarin. IOS permite que você use o .NET `System.IO` classes para operações de sistema de arquivos no iOS.

Os trechos de código a seguir ilustram algumas operações de arquivo comuns. Você encontrará nelas tudo abaixo no `SampleCode.cs` arquivo no aplicativo de exemplo para este artigo.

<a name="Working_with_directories" />

### <a name="working-with-directories"></a>Trabalhar com diretórios

Esse código enumera os subdiretórios no diretório atual (especificado pelo ". /" parâmetro), que é o local do executável do aplicativo.
Sua saída será uma lista de todos os arquivos e pastas que são implantadas com seu aplicativo (exibido na janela do console durante a depuração).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>Leitura de arquivos

Para ler um arquivo de texto, você só precisa de uma única linha de código. Este exemplo exibirá o conteúdo de um arquivo de texto na janela de saída do aplicativo.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>Serialização XML

Embora a trabalhar com o completo `System.Xml` namespace está além do escopo deste artigo, você poderá desserializar facilmente um documento XML do sistema de arquivos usando um StreamReader como este:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Consulte a documentação do MSDN para o [System. XML](http://msdn.microsoft.com/library/system.xml.aspx) namespace para obter mais informações sobre [serialização](http://msdn.microsoft.com/library/system.xml.serialization.aspx). Você também deve examinar a [documentação do xamarin. IOS](~/ios/deploy-test/linker.md) sobre o vinculador – normalmente, você precisará adicionar o `[Preserve]` atributo às classes que deseja serializar.

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>Criando arquivos e diretórios

Este exemplo mostra como usar o `Environment` classe para acessar a pasta de documentos em que podemos criar arquivos e diretórios.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

Criar um diretório é um processo muito semelhante:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Para obter mais informações sobre o namespace System.IO, consulte o [documentação do MSDN](http://msdn.microsoft.com/library/system.io.aspx).


### <a name="serializing-json"></a>Serialização Json

Trabalhando com Json dados em um aplicativo xamarin. IOS são muito fácil usando o [Json.NET](http://www.newtonsoft.com/json) estrutura JSON de alto desempenho para o pacote NuGet do .NET. Basta adicione o pacote NuGet ao projeto do seu aplicativo: 

[![](file-system-images/json01.png "Adicionar o pacote NuGet ao projeto de aplicativos")](file-system-images/json01.png#lightbox)

Em seguida, adicione uma classe para atuar como o modelo de dados para a serialização/desserialização (nesse caso, `Account.cs`):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        #region Computed Properties
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }
        #endregion

        #region Constructors
        public Account() {

        }
        #endregion
    }
}
```

Por fim, crie uma instância da `Account` de classe, serializá-lo em dados json e gravá-lo em um arquivo:

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
Consulte o Json .NET [documentação](http://www.newtonsoft.com/json/help) para obter mais informações sobre como trabalhar com dados json em um aplicativo .NET.

<a name="Special_Considerations" />

## <a name="special-considerations"></a>Considerações especiais

Apesar das semelhanças entre as operações de arquivo do xamarin. IOS e .NET, iOS e xamarin. IOS diferem do .NET em alguns aspectos importantes.

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>Tornar os arquivos de projeto acessível no tempo de execução

Por padrão, se você adicionar um arquivo ao seu projeto, ele não será incluído no assembly final e, portanto, não estarão disponível para seu aplicativo. Para incluir um arquivo no assembly, você deve marcá-lo com uma ação de compilação especial, chamada de conteúdo.

Para marcar um arquivo de inclusão, clique duas vezes em que o arquivo (s) e escolha **ação de compilação &gt; conteúdo** no Visual Studio para Mac. Você também pode alterar o **ação de compilação** no arquivo de **propriedades** folha.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

É importante entender que o sistema de arquivos do iOS *diferencia maiusculas de minúsculas*. Isso significa que seus nomes de arquivos e diretórios devem corresponder exatamente – Readme. txt e readme. txt seriam considerados nomes de arquivo diferentes.

Isso pode ser confuso para desenvolvedores de .NET que estão mais familiarizados com o sistema de arquivos do Windows, que é *diferencia maiusculas de minúsculas*– "Arquivos", "Arquivos" e "arquivos" referência todos no mesmo diretório.

Portanto, embora dispositivos iOS diferenciam maiusculas de minúsculas e seu código deve ser escrito com isso em mente, o iOS que Simulator é caso não minúsculas por padrão. Isso significa que se seu maiusculas e minúsculas do nome do arquivo for diferente entre o arquivo propriamente dito e as referências a ele no código, seu código ainda pode funcionar no simulador, mas que ele falharia em um dispositivo real. Essa é uma das razões por que é importante implantar em um dispositivo real cedo e frequentemente durante o desenvolvimento de iOS.

 <a name="Path_Separator" />


### <a name="path-separator"></a>Separador de caminho

o iOS usa a barra '/' como separador de caminho (que é diferente do Windows, que usa a barra invertida ' \').

Por causa dessa diferença confusa, é recomendável usar o `System.IO.Path.Combine` método, que é ajustada para a plataforma atual, em vez de codificar um separador de caminho específico. Essa é uma etapa simple que faz com que seu código mais portátil para outras plataformas.

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>Proteção do aplicativo

Acesso do seu aplicativo para o sistema de arquivos (e outros recursos, como os recursos de hardware e rede) é limitado por motivos de segurança. Essa restrição é conhecida como o *de proteção do aplicativo*. Em termos de sistema de arquivos, seu aplicativo é limitado a criação e exclusão de arquivos e diretórios no diretório base.

O diretório base é um local exclusivo no sistema de arquivos onde seu aplicativo e todos os seus dados estão armazenados. Você não pode escolher (ou alterar) o local da pasta base para seu aplicativo. No entanto, iOS e xamarin. IOS fornecem propriedades e métodos para gerenciar os arquivos e diretórios dentro.

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>O pacote de aplicativo

O *pacote de aplicativo* é a pasta que contém seu aplicativo.
Ele é diferenciado de outras pastas, fazendo com que o sufixo. App adicionado ao nome do diretório. Seu pacote de aplicativo contém seu arquivo executável e todo o conteúdo (arquivos, imagens, etc.) necessário para seu projeto.

Quando você navegar para seu pacote de aplicativo no Mac OS, ela será exibida com um ícone diferente do que você vê em outros diretórios (e o sufixo. App está oculto); No entanto, é apenas um diretório regular o sistema operacional está exibindo de maneira diferente.

Para exibir o pacote de aplicativo para o código de exemplo, clique com botão direito no projeto no Visual Studio para Mac e selecione **Abrir pasta recipiente**. Em seguida, navegue até **bin/Debug/** onde você deve encontrar um ícone do aplicativo (semelhante à captura de tela abaixo).

 [![](file-system-images/40-bundle.png "Navegue para bin/Debug para encontrar um ícone do aplicativo semelhante a esta captura de tela")](file-system-images/40-bundle.png#lightbox)

Clique nesse ícone e escolha **exibir pacote de conteúdo** para procurar o conteúdo do diretório do pacote de aplicativo. O conteúdo exibido assim como o conteúdo de um diretório regular, conforme mostrado aqui:

 [![](file-system-images/45-bundle.png "O conteúdo do pacote de aplicativo")](file-system-images/45-bundle.png#lightbox)

O pacote de aplicativo é o que é instalado no simulador ou no dispositivo durante o teste e, por fim, é o que é enviado à Apple para inclusão na Store do aplicativo.

 <a name="Application_Directories" />


## <a name="application-directories"></a>Diretórios de aplicativos

Quando seu aplicativo é instalado em um dispositivo, o sistema operacional cria seu diretório base e coloca dentro de seu pacote de aplicativo. Seu código possa acessar o pacote de aplicativo para ler dados, mas nada deve ser escrito para o diretório raiz, conforme ele é assinado e todas as modificações serão invalidar seu aplicativo e impedir que ele inicie.

Portanto, embora nada deve ser escrito para o diretório raiz de <b>no iOS 7 e anteriores</b> cria um número de diretórios dentro do diretório raiz do aplicativo que estão disponíveis para uso. <b>No iOS 8 são os diretórios de usuário acessível <a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">não localizado</a> dentro da raiz do aplicativo</b>.

Esses diretórios e seus objetivos estão listados abaixo:

&nbsp;

|Diretório|Descrição|
|---|---|
|[ApplicationName].app/|**No iOS 7 e anteriores** esse é o `ApplicationBundle` diretório onde o executável do aplicativo está armazenado. A estrutura do diretório que você cria no seu aplicativo existe neste diretório (por exemplo, imagens e outros tipos de arquivo que você marcou como recursos no Visual Studio para o projeto do Mac).<br /><br />Se você precisar acessar os arquivos de conteúdo dentro de seu pacote de aplicativo, o caminho para este diretório está disponível por meio de `NSBundle.MainBundle.BundlePath` propriedade.|
|Documentos /|Use esse diretório para armazenar documentos do usuário e arquivos de dados do aplicativo.<br /><br />O conteúdo desse diretório pode ser disponibilizado para o usuário por meio do compartilhamento (embora isso está desabilitado por padrão) de arquivos do iTunes. Adicionar um `UIFileSharingEnabled` chave Boolean no arquivo Info. plist para permitir que os usuários acessar esses arquivos.<br /><br />Mesmo se um aplicativo imediatamente não habilita o compartilhamento de arquivos, você deve evitar colocar os arquivos que os usuários neste diretório devem ficar oculto (como arquivos de banco de dados, a menos que você pretende compartilhá-los). Contanto que os arquivos confidenciais permaneçam ocultos, esses arquivos não ser expostos (e possivelmente movidos, modificadas ou excluídas pelo iTunes) se o compartilhamento de arquivos estiver habilitado em uma versão futura.<br /><br /> Você pode usar o `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` método para obter o caminho para o diretório de documentos para o seu aplicativo.<br /><br />O conteúdo deste diretório é feito backup por iTunes.|
|Biblioteca /|O diretório de biblioteca é um bom lugar para armazenar arquivos que não são criados diretamente pelo usuário, como bancos de dados ou outros arquivos gerados pelo aplicativo. O conteúdo deste diretório nunca é exposto ao usuário por meio do iTunes.<br /><br />Você pode criar seus próprios subdiretórios na biblioteca; No entanto, já há alguns criado pelo sistema diretórios aqui que você deve estar atento, incluindo as preferências e Caches.<br /><br />O conteúdo deste diretório (exceto para o subdiretório de Caches) é feito backup por iTunes. Diretórios personalizados criados por você na biblioteca serão feitos backup.|
|Biblioteca/preferências /|Arquivos de preferências específicas do aplicativo são armazenados nesse diretório. Não crie esses arquivos diretamente. Em vez disso, use o `NSUserDefaults` classe.<br /><br />O conteúdo deste diretório é feito backup por iTunes.|
|Caches/biblioteca /|O diretório de Caches é um bom lugar para armazenar arquivos de dados que podem ajudar seu aplicativo é executado, mas que podem ser facilmente recriados se necessário. O aplicativo deve criar e excluir esses arquivos conforme necessário e ser capaz de recriar esses arquivos, se necessário. iOS 5 também pode excluir esses arquivos (em situações de armazenamento extremamente baixa), no entanto, ele não fará isso enquanto o aplicativo está em execução.<br /><br />O conteúdo deste diretório não é feito backup por iTunes, o que significa que eles não estarão presentes se o usuário restaura um dispositivo, e eles podem não estar presentes após a instalação de uma versão atualizada do seu aplicativo.<br /><br />Por exemplo, no caso de seu aplicativo não pode se conectar à rede, você pode usar o diretório de Caches para armazenar arquivos de dados ou para fornecer uma boa experiência offline. O aplicativo pode salvar e recuperar esses dados rapidamente, enquanto aguarda as respostas de rede, mas ele não precisa ser submetido ao backup e pode facilmente ser recuperado ou criado novamente após uma restauração ou a versão da atualização.|
|tmp /|Aplicativos podem armazenar arquivos temporários que são necessários apenas por um curto período neste diretório. Para economizar espaço, os arquivos devem ser excluídos quando eles não são mais necessários. O sistema operacional também pode excluir arquivos nesse diretório quando um aplicativo não está em execução.<br /><br />O conteúdo deste diretório não é feito backup por iTunes.<br /><br />Por exemplo, o diretório tmp pode ser usado para armazenar arquivos temporários que são baixados para exibição para o usuário (como o Twitter avatares ou anexos de email), mas que pode ser excluído depois que já foram exibidos (e baixados novamente se eles forem necessários no futuro ).|

Esta captura de tela mostra a estrutura de diretórios em uma janela do localizador:

 [![](file-system-images/08-library-directory.png "Esta captura de tela mostra a estrutura de diretórios em uma janela do localizador")](file-system-images/08-library-directory.png#lightbox)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>Acessando outros diretórios de forma programática

Os exemplos anteriores de diretório e arquivo acessados o `Documents` directory. Para gravar em outro diretório, você deve construir um caminho usando o ".." sintaxe conforme mostrado aqui:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

Criar um diretório é muito semelhante:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Caminhos para o `Caches` e `tmp` diretórios podem ser construídos como este:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

 <a name="Sharing_Files_with_the_User_through_iTunes" />


## <a name="sharing-files-with-the-user-through-itunes"></a>Compartilhamento de arquivos com o usuário por meio do iTunes

Os usuários podem acessar os arquivos no diretório de documentos do seu aplicativo por meio da edição `Info.plist` e criando um **aplicativo dá suporte ao compartilhamento do iTunes** (`UIFileSharingEnabled`) entrada no **origem** modo de exibição, como mostrado aqui:

 [![](file-system-images/09-uifilesharingenabled-plist.png "Adicionando o aplicativo dá suporte à propriedade de compartilhamento do iTunes")](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Esses arquivos podem ser acessados no iTunes quando o dispositivo está conectado e o usuário escolhe o `Apps` guia. Por exemplo, a captura de tela a seguir mostra os arquivos no aplicativo selecionado compartilhado por meio do iTunes:

 [![](file-system-images/10-itunes-file-sharing.png "Esta captura de tela mostra os arquivos no aplicativo selecionado compartilhado por meio do iTunes")](file-system-images/10-itunes-file-sharing.png#lightbox)

Os usuários só podem acessar os itens de nível superior neste diretório por meio do iTunes. Eles não podem ver o conteúdo de todos os subdiretórios (embora eles podem copiá-los para seu computador ou excluí-los). Por exemplo, com GoodReader, PDF e EPUB arquivos podem ser compartilhados com o aplicativo para que os usuários possam lê-los em seus dispositivos iOS.

Usuários que modificam o conteúdo da pasta dos documentos podem causar problemas se não tiver cuidadosos. Seu aplicativo deve levar isso em consideração e ser resiliente a atualizações destrutivas da pasta documentos.

O código de exemplo para este artigo cria um arquivo e uma pasta na pasta documentos (no **SampleCode.cs**) e permite o compartilhamento de arquivos a **Info. plist** arquivo. Esta captura de tela mostra como eles aparecem na iTunes:

 [![](file-system-images/15-itunes-file-sharing-example.png "Esta captura de tela mostra como os arquivos são exibidos no iTunes")](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Consulte a [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) artigo para obter informações sobre como definir ícones para o aplicativo e para qualquer tipo de documento personalizadas que você cria.

Se o `UIFileSharingEnabled` chave for falsa ou não estiver presente, em seguida, compartilhamento de arquivos está desativada por padrão, e os usuários não poderão interagir com seu Documentsdirectory.

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>Backup e restauração

Quando um dispositivo é feito backup pelo iTunes, todos os diretórios criados no diretório base do seu aplicativo serão salvo, exceto o seguinte:

-   **. [ApplicationName] App** – o pacote de aplicativo *faz* é feito, mas somente quando o pacote foi alterado (quando uma atualização de aplicativo é instalada, por exemplo). Você não deve modificar esse diretório de qualquer forma, uma vez que ele seja assinado e, portanto, devem ser alteradas após a instalação. 
-   **Biblioteca/Caches** – o diretório de cache destina-se a arquivos de trabalho que não precisam de backup. 
-   **tmp** – esse diretório é usado para arquivos temporários que são criados e excluídos quando não for mais necessário, ou para arquivos se iOS exclui quando ele precisa de espaço. 


Fazendo backup de uma grande quantidade de dados pode levar muito tempo. Se você decidir que precisa fazer backup de quaisquer dados ou documento em particular, seu aplicativo use apenas os documentos e a biblioteca de pastas para isso. Para dados transitórios ou arquivos que podem ser facilmente recuperados da rede, use os Caches ou o diretório tmp.

Lembre-se de que iOS será "Limpar" o sistema de arquivos quando um dispositivo é executado criticamente baixo espaço em disco. Esse processo irá remover todos os arquivos da biblioteca/Caches e tmp pasta de aplicativos que não estão em execução no momento.

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>Estar em conformidade com as restrições de Backup do iCloud iOS5

A Apple apresentou *Backup do iCloud* funcionalidade com o iOS 5. Quando o Backup do iCloud é habilitado, todos os arquivos no diretório base do seu aplicativo (excluindo os diretórios que são normalmente um backup, por exemplo, o pacote de aplicativos `Caches` e `tmp`) são backup para iCloud servidores. Esse recurso fornece ao usuário com um backup completo no caso do dispositivo é perdido, roubado ou danificado.

Como o iCloud fornece apenas 5Gb de espaço 'gratuito' para cada usuário e para evitar o uso desnecessário da largura de banda, Apple espera que aplicativos somente backup essenciais dados gerados pelo usuário. Para estar em conformidade com as diretrizes de armazenamento de dados do iOS, você deve limitar a quantidade de dados que o backup são feitos ao aderir a dos seguintes itens:

-  Armazene apenas dados gerados pelo usuário ou dados que não podem ser recriados, no diretório Documents (que é armazenado em backup). 
-  Store quaisquer outros dados que podem ser facilmente recriados ou baixados novamente na `Library/Caches` ou `tmp` (que não é armazenado em backup e poderia ser 'limpa'). 
-  Se você tiver arquivos que podem ser apropriados para o `Library/Caches` ou `tmp` pasta, mas você não deseja ser 'limpa' out, armazená-los em outro lugar (como `Library/YourData` ) e aplique o ' não fizer backup de ' atributo para impedir que os arquivos usando o ba de Backup do iCloud ndwidth e espaço de armazenamento. Esses dados ainda usam o espaço no dispositivo, portanto, você deve gerenciá-lo com cuidado e excluí-lo sempre que possível. 

O ' não fizer backup de ' atributo é definido usando o `NSFileManager` classe. Certifique-se de que sua classe for `using Foundation` e chamar `SetSkipBackupAttribute` semelhante a esta:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Quando `SetSkipBackupAttribute` está `true` o arquivo não será feito backup, independentemente do diretório em que ele é armazenado em (até mesmo o `Documents` directory). Você pode consultar o atributo usando o `GetSkipBackupAttribute` método e você poderá redefini-lo chamando o `SetSkipBackupAttribute` método com `false`, semelhante a esta:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Compartilhando dados entre o iOS aplicativos e extensões de aplicativo

Como extensões de aplicativo é executada como parte de um aplicativo de host (em vez de seu aplicativo contém), o compartilhamento de dados não é automático incluído para que o trabalho adicional é necessário. Grupos de aplicativos são que o mecanismo do iOS usa para permitir que aplicativos diferentes para compartilhar dados. Se os aplicativos tenham sido configurados corretamente com os direitos corretos e provisionamento, eles podem acessar um diretório compartilhado fora da área de segurança normal do iOS.

### <a name="configure-an-app-group"></a>Configurar um grupo de aplicativos

O local compartilhado é configurado usando um [grupo de aplicativos](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que é configurado na seção **Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)** no [iOS Dev Center (Centro de Desenvolvimento iOS)](https://developer.apple.com/devcenter/ios/). Esse valor também deve ser referenciado no **Entitlements.plist** de cada projeto.

Para obter informações sobre como criar e configurar um grupo de aplicativos, consulte o [recursos do grupo de aplicativo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) guia.

### <a name="files"></a>Arquivos

O aplicativo do iOS e a extensão também podem compartilhar arquivos usando um caminho de arquivo comuns (considerando que foram corretamente configurados com os direitos corretos e provisionamento):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```
> [!IMPORTANT]
> Se o caminho do grupo retornado for `null`, verifique a configuração dos direitos e o perfil de provisionamento e certifique-se de que estejam corretos.


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>Atualizações de versão do aplicativo

Quando uma nova versão do seu aplicativo é baixada, o iOS cria um novo diretório inicial e armazena o novo pacote de aplicativo nela. iOS, em seguida, move as seguintes pastas da versão anterior do seu pacote de aplicativo para seu novo diretório inicial:

-  **Documentos**
-  **Library**


Outros diretórios também podem ser copiados e colocar em seu novo diretório base, mas eles não são garantidos para ser copiado, para que seu aplicativo não deve confiar nesse comportamento do sistema.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo mostrou que as operações de sistema de arquivos são tão simples com xamarin. IOS, assim como acontece com qualquer outro aplicativo .NET. Ele também introduziu a proteção do aplicativo e examinado as implicações de segurança que faz com que. Em seguida, ele explorou o conceito de um pacote de aplicativo. Por fim, ele enumerados especializados diretórios disponíveis para seu aplicativo e explicados suas funções durante backups e atualizações de aplicativo.


## <a name="related-links"></a>Links relacionados

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [Guia de programação do sistema de arquivos](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registrando o arquivo de tipos de suporte do aplicativo](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
