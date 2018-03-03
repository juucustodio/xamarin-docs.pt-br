---
title: Trabalhando com o sistema de arquivos
description: "Xamarin pode usar as mesmas classes System.IO para trabalhar com arquivos e diretórios no iOS que você usaria em qualquer aplicativo .NET. No entanto, apesar dos métodos e classes familiares, iOS implementa algumas restrições sobre os arquivos que podem ser acessados ou criados e também fornece recursos especiais para determinados diretórios. Este artigo descreve essas restrições e recursos e demonstra como o acesso a arquivos funciona em um aplicativo xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: a2c3ce9e19340067d77a8bc131b5a247806ecfa1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-the-file-system"></a>Trabalhando com o sistema de arquivos

_Xamarin pode usar as mesmas classes System.IO para trabalhar com arquivos e diretórios no iOS que você usaria em qualquer aplicativo .NET. No entanto, apesar dos métodos e classes familiares, iOS implementa algumas restrições sobre os arquivos que podem ser acessados ou criados e também fornece recursos especiais para determinados diretórios. Este artigo descreve essas restrições e recursos e demonstra como o acesso a arquivos funciona em um aplicativo xamarin._

Você pode usar o xamarin e o `System.IO` classes de *.NET biblioteca BCL (Base Class)* para acessar o sistema de arquivos iOS. O `File` classe permite que você criar, excluir e ler arquivos e o `Directory` classe permite que você criar, excluir ou enumerar o conteúdo de pastas. Você também pode usar `Stream` subclasses, que podem fornecer um nível maior de controle sobre as operações de arquivo (como pesquisa de compactação ou a posição dentro de um arquivo).

iOS impõe algumas restrições sobre o que um aplicativo pode fazer com o sistema de arquivos para preservar a segurança dos dados de um aplicativo e para proteger usuários de aplicativos malignant. Essas restrições são parte do *de proteção do aplicativo* – um conjunto de regras que limita o acesso do aplicativo para arquivos de preferências, recursos de rede, hardware, etc. Um aplicativo é limitado a leitura e gravação de arquivos em seu diretório base (local de instalação); ele não pode acessar arquivos de outro aplicativo.

iOS também tem alguns recursos específicos do sistema de arquivos: determinados diretórios exigem tratamento especial em relação a backups e atualizações e aplicativos também podem compartilhar arquivos por meio do iTunes.

Este artigo aborda os recursos e restrições do iOS, sistema de arquivos em detalhes e inclui um aplicativo de exemplo que demonstra como usar o xamarin para executar algumas operações de sistema de arquivos simples:

 [ ![](file-system-images/05-sampleapp.png "Um exemplo de iOS executar algumas operações de sistema de arquivos simples")](file-system-images/05-sampleapp.png)

 <a name="General_File_Access" />


## <a name="general-file-access"></a>Acesso a arquivos geral

Xamarin permite que você use o .NET `System.IO` classes para operações de sistema de arquivos no iOS.

Os trechos de código a seguir ilustram algumas operações de arquivo comuns. Você encontrará todos abaixo no `SampleCode.cs` arquivo, o aplicativo de exemplo para este artigo.

 <a name="Working_with_directories" />


### <a name="working-with-directories"></a>Trabalhando com pastas

Esse código enumera os subdiretórios no diretório atual (especificado pelo ". /" parâmetro), que é o local do executável do aplicativo.
A saída será uma lista de todos os arquivos e pastas que são implantadas com o aplicativo (exibido na janela do console enquanto você está depurando).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>Lendo arquivos

Para ler um arquivo de texto, você só precisa de uma única linha de código. Este exemplo exibirá o conteúdo de um arquivo de texto na janela de saída do aplicativo.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>Serialização XML

Embora a trabalhar com o completo `System.Xml` namespace está além do escopo deste artigo, você pode facilmente desserializar um documento XML do sistema de arquivos usando um StreamReader assim:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Consulte a documentação do MSDN para o [System](http://msdn.microsoft.com/en-us/library/system.xml.aspx) namespace para obter mais informações sobre [serialização](http://msdn.microsoft.com/en-us/library/system.xml.serialization.aspx). Você também deve revisar o [xamarin documentação](~/ios/deploy-test/linker.md) no vinculador – normalmente você precisará adicionar o `[Preserve]` de atributo para classes que deseja serializar.

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>Criando arquivos e diretórios

Este exemplo mostra como usar o `Environment` classe para acessar a pasta de documentos onde podemos criar arquivos e diretórios.

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

Para obter mais informações sobre o namespace System.IO, consulte o [documentação MSDN](http://msdn.microsoft.com/en-us/library/system.io.aspx).


### <a name="serializing-json"></a>Serialização Json

Trabalhar com Json dados em um aplicativo xamarin são muito fácil usando o [Json.NET](http://www.newtonsoft.com/json) estrutura JSON de alto desempenho para o pacote NuGet .NET. Basta adicione o pacote NuGet ao projeto do aplicativo: 

[ ![](file-system-images/json01.png "Adicionar o pacote NuGet para o projeto de aplicativos")](file-system-images/json01.png)

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

Finalmente, crie uma instância do `Account` classe, serializá-lo para dados json e gravá-la em um arquivo:

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
Consulte Json .NET [documentação](http://www.newtonsoft.com/json/help) para obter mais informações sobre como trabalhar com dados json em um aplicativo .NET.

<a name="Special_Considerations" />

## <a name="special-considerations"></a>Considerações especiais

Apesar das semelhanças entre as operações de arquivo do xamarin e .NET, iOS e xamarin diferir do .NET em alguns aspectos importantes.

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>Tornar arquivos de projeto acessíveis em tempo de execução

Por padrão, se você adicionar um arquivo ao seu projeto, ele não será incluído no assembly final e, portanto, não estarão disponível para seu aplicativo. Para incluir um arquivo no assembly, marque-a com uma ação de compilação especial, chamada de conteúdo.

Para marcar um arquivo de inclusão, clique duas vezes em que o arquivo (s) e escolha **ação de compilação &gt; conteúdo** no Visual Studio para Mac. Você também pode alterar o **ação de compilação** no arquivo de **propriedades** folha.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

É importante entender que o sistema de arquivos iOS é *diferencia maiusculas de minúsculas*. Isso significa que os nomes de arquivos e diretórios devem corresponder exatamente – Leiame. txt e readme.txt seriam considerados nomes de arquivo diferentes.

Isso pode ser confuso para desenvolvedores de .NET que estão mais familiarizados com o sistema de arquivos do Windows, que é *maiusculas de minúsculas*– "Files", "FILES" e "arquivos" faz referência ao mesmo diretório.

Portanto, embora dispositivos iOS diferenciam maiusculas de minúsculas e seu código deve ser escrito com isso em mente, o simulador é do iOS caso não diferencia por padrão. Isso significa que se seu maiusculas e minúsculas do nome do arquivo for diferente entre o arquivo propriamente dito e as referências a ele no código, seu código ainda poderá funcionar no simulador, mas que ele falhará em um dispositivo real. Essa é uma das razões por que é importante implantar em um dispositivo real inicial e muitas vezes durante o desenvolvimento do iOS.

 <a name="Path_Separator" />


### <a name="path-separator"></a>Separador de caminho

iOS usa a barra '/' como o separador de caminho (que é diferente do Windows, que usa a barra invertida ' \').

Devido a essa diferença confusa, é recomendável usar o `System.IO.Path.Combine` método, que ajusta a plataforma atual, em vez de codificar um separador de caminho específico. Essa é uma etapa simple que torna seu código mais portáteis para outras plataformas.

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>Proteção do aplicativo

Acesso do aplicativo para o sistema de arquivos (e outros recursos, como os recursos de hardware e de rede) é limitado por motivos de segurança. Essa restrição é conhecida como o *de proteção do aplicativo*. Em termos de sistema de arquivos, o aplicativo é limitado a criação e exclusão de arquivos e diretórios em seu diretório base.

O diretório base é um local exclusivo no sistema de arquivos onde seu aplicativo e todos os seus dados estão armazenados. Você não pode escolher (ou alterar) o local da pasta base para o seu aplicativo; No entanto, iOS e xamarin fornecem propriedades e métodos para gerenciar os arquivos e diretórios dentro.

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>O pacote de aplicativo

O *pacote de aplicativo* é a pasta que contém seu aplicativo.
Ele é diferenciado de outras pastas fazendo com que o sufixo. App adicionado ao nome de diretório. Pacote de aplicativo contém o arquivo executável e todo o conteúdo (arquivos, imagens, etc.) necessário para seu projeto.

Quando você navega para o pacote de aplicativo no Mac OS, ele aparece com um ícone diferente que você pode ver em outros diretórios (e o sufixo. App está oculto); No entanto, é um diretório regular que o sistema operacional está exibindo diferente.

Para exibir o pacote de aplicativo para o código de exemplo, com o botão direito no projeto no Visual Studio para Mac e selecione **abrir a pasta que contém**. Navegue até **bin/Debug/** onde você deve encontrar um ícone do aplicativo (semelhante a captura de tela abaixo).

 [ ![](file-system-images/40-bundle.png "Navegue para bin/Debug para encontrar um ícone do aplicativo semelhante a esta captura de tela")](file-system-images/40-bundle.png)

Clique nesse ícone e escolha **exibir o conteúdo do pacote** para procurar o conteúdo do diretório do pacote de aplicativo. O conteúdo é exibido como o conteúdo de um diretório regular, conforme mostrado aqui:

 [ ![](file-system-images/45-bundle.png "O conteúdo do pacote de aplicativo")](file-system-images/45-bundle.png)

O pacote de aplicativo é o que é instalado no simulador ou no seu dispositivo durante o teste e, por fim, é o que é enviado para a Apple para inclusão na loja de aplicativos.

 <a name="Application_Directories" />


## <a name="application-directories"></a>Diretórios de aplicativo

Quando seu aplicativo é instalado em um dispositivo, o sistema operacional cria seu diretório base e coloca o pacote de aplicativo dentro. Seu código pode acessar o pacote de aplicativo para ler dados, mas nada deve ser escrito para o diretório raiz, como ele está assinado e todas as modificações serão invalidar o seu aplicativo e impedir que ele seja executado.

Portanto, embora nada deve ser escrito para o diretório raiz, <b>no iOS 7 e versões anteriores</b> cria um número de diretórios no diretório raiz do aplicativo que estão disponíveis para uso. <b>No iOS 8 os diretórios acessível ao usuário são <a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">não localizado</a> dentro da raiz do aplicativo</b>.

Esses diretórios e seus propósitos estão listados abaixo:

&nbsp;

<table>
  <tbody>
    <tr>
      <td>
Diretório </td>
      <td>
Descrição </td>
    </tr>
    <tr>
      <td>
        <p>[ApplicationName].app/</p>
      </td>
      <td>
        <p><b>No iOS 7 e versões anteriores</b> este é o <code>ApplicationBundle</code> diretório onde o executável do aplicativo está armazenado. A estrutura de diretório que você cria no seu aplicativo existe neste diretório (por exemplo, imagens e outros tipos de arquivo que você marcou como recursos no seu Visual Studio para o projeto de Mac).</p>
        <p>Se você precisar acessar os arquivos de conteúdo dentro de seu pacote de aplicativo, o caminho para este diretório está disponível por meio de <code>NSBundle.MainBundle.BundlePath</code> propriedade.</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>Documentos /</p>
      </td>
      <td>
        <p>Use esse diretório para armazenar os documentos do usuário e arquivos de dados do aplicativo.</p>
        <p>O conteúdo deste diretório pode ser disponibilizado para o usuário iTunes compartilhamento de arquivos (embora isso está desabilitado por padrão). Adicionar um <code>UIFileSharingEnabled</code> chave Boolean para o arquivo Info. plist que permite aos usuários acessar esses arquivos.</p>
        <p>Mesmo se um aplicativo imediatamente não habilitar o compartilhamento de arquivos, você deve evitar colocar arquivos que devem ser ocultos dos usuários nesse diretório (como arquivos de banco de dados, a menos que você queira compartilhá-los). Contanto que os arquivos confidenciais permaneçam ocultos, esses arquivos não ser expostos (e potencialmente movidos, modificados ou excluídos pelo iTunes) se o compartilhamento de arquivos estiver habilitado em uma versão futura.</p>
        <p>Você pode usar o <code>Environment.GetFolderPath
(Environment.SpecialFolder.MyDocuments)</code> método para obter o caminho para o diretório de documentos para seu aplicativo.</p>
        <p>O conteúdo deste diretório é feito pelo iTunes.</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>Biblioteca /</p>
      </td>
      <td>
        <p>O diretório de biblioteca é um bom lugar para armazenar arquivos que não são criados diretamente pelo usuário, como bancos de dados ou outros arquivos gerados pelo aplicativo.
O conteúdo deste diretório nunca é exposto para o usuário por meio do iTunes.</p>
        <p>Você pode criar suas próprias subpastas na biblioteca; No entanto, já existem alguns criado pelo sistema diretórios aqui que você deve estar atento, incluindo preferências e Caches.</p>
        <p>O conteúdo deste diretório (exceto o subdiretório de Caches) é feito pelo iTunes. Serão feitos diretórios personalizados que você criar na biblioteca.</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>Preferências de biblioteca / /</p>
      </td>
      <td>
        <p>Arquivos de preferências específicas do aplicativo são armazenados nesse diretório. Não cria esses arquivos diretamente. Em vez disso, use o <code>NSUserDefaults</code> classe.</p>
        <p>O conteúdo deste diretório é feito pelo iTunes.</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>Biblioteca/Caches /</p>
      </td>
      <td>
        <p>O diretório de Caches é um bom lugar para armazenar arquivos de dados que podem ajudar o seu aplicativo é executado, mas que podem ser facilmente recriados se necessário. O aplicativo deve criar e excluir esses arquivos, conforme necessário e ser capaz de recriar esses arquivos, se necessário. iOS 5 também pode excluir esses arquivos (em situações de armazenamento muito baixa), no entanto, ele não fará isso enquanto o aplicativo está em execução.</p>
        <p>O conteúdo deste diretório não é feito pela iTunes, o que significa que eles não estarão presentes se o usuário restaura um dispositivo, e eles podem não estar presentes após a instalação de uma versão atualizada de seu aplicativo.</p>
        <p>Por exemplo, caso o aplicativo não pode se conectar à rede, você pode usar o diretório de Caches para armazenar arquivos de dados ou para fornecer uma boa experiência offline. O aplicativo pode salvar e recuperar esses dados rapidamente enquanto aguarda as respostas de rede, mas ele não precisa ser feito e pode facilmente ser recuperado ou criado novamente após uma restauração ou uma versão de atualização.</p>
      </td>
    </tr>
    <tr>
      <td>
        <p>tmp /</p>
      </td>
      <td>
        <p>Aplicativos podem armazenar arquivos temporários que são necessários apenas por um curto período neste diretório. Para economizar espaço, os arquivos devem ser excluídos quando eles não são mais necessários. O sistema operacional também pode excluir arquivos nesse diretório quando um aplicativo não está em execução.</p>
        <p>O conteúdo deste diretório não é feito pelo iTunes.</p>
        <p>Por exemplo, o diretório tmp pode ser usado para armazenar arquivos temporários que são baixados para exibição para o usuário (como o Twitter avatares ou anexos de email), mas que pode ser excluído depois que já foram exibidas (e baixados novamente se forem necessários no futuro ).</p>
      </td>
    </tr>
  </tbody>
</table>

Esta captura de tela mostra a estrutura de diretórios em uma janela de localizador:

 [ ![](file-system-images/08-library-directory.png "Esta captura de tela mostra a estrutura de diretórios em uma janela de localizador")](file-system-images/08-library-directory.png)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>Acessando outros diretórios programaticamente

Os exemplos anteriores de diretório e arquivo acessados o `Documents` directory. Para gravar para outro diretório, você deve construir um caminho usando o "..." sintaxe conforme mostrado aqui:

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

Caminhos para a `Caches` e `tmp` diretórios podem ser construídos como este:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

 <a name="Sharing_Files_with_the_User_through_iTunes" />


## <a name="sharing-files-with-the-user-through-itunes"></a>Compartilhamento de arquivos com o usuário através do iTunes

Os usuários podem acessar os arquivos no diretório de documentos do aplicativo por meio da edição `Info.plist` e criando uma **aplicativo oferece suporte ao compartilhamento de iTunes** (`UIFileSharingEnabled`) entrada no **fonte** modo de exibição, como mostrado aqui:

 [ ![](file-system-images/09-uifilesharingenabled-plist.png "Adicionar o aplicativo dá suporte a compartilhamento de propriedade do iTunes")](file-system-images/09-uifilesharingenabled-plist.png)

Esses arquivos podem ser acessados na iTunes quando o dispositivo está conectado e o usuário escolhe o `Apps` guia. Por exemplo, a captura de tela a seguir mostra os arquivos no aplicativo selecionado compartilhado por meio da iTunes:

 [ ![](file-system-images/10-itunes-file-sharing.png "Esta captura de tela mostra os arquivos no aplicativo selecionado compartilhado por meio da iTunes")](file-system-images/10-itunes-file-sharing.png)

Usuários podem acessar apenas os itens de nível superior neste diretório por meio do iTunes. Eles não podem ver o conteúdo de todas as subpastas (embora eles podem copiá-los ao seu computador ou excluí-los). Por exemplo, com GoodReader, arquivos PDF e EPUB podem ser compartilhados com o aplicativo para que os usuários podem ler em seus dispositivos iOS.

Os usuários que modifiquem o conteúdo da pasta dos documentos podem causar problemas se não tiver cuidadosos. Seu aplicativo deve levar isso em consideração e sejam resilientes a destrutivas atualizações da pasta documentos.

O código de exemplo para este artigo cria um arquivo e uma pasta na pasta documentos (em **SampleCode.cs**) e permite o compartilhamento de arquivos o **Info. plist** arquivo. Esta captura de tela mostra como eles aparecem na iTunes:

 [ ![](file-system-images/15-itunes-file-sharing-example.png "Esta captura de tela mostra como os arquivos exibidos na iTunes")](file-system-images/15-itunes-file-sharing-example.png)

Consulte o [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) artigo para obter informações sobre como definir ícones para o aplicativo e de qualquer tipo de documento personalizadas que você criar.

Se o `UIFileSharingEnabled` chave for false ou não está presente, em seguida, o compartilhamento de arquivos é desativada por padrão, e os usuários não poderão interagir com seu Documentsdirectory.

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>Backup e restauração

Quando um dispositivo é feito pelo iTunes, todos os diretórios criados no diretório base do aplicativo serão salvo exceto o seguinte:

-   **[ApplicationName] App** – o pacote de aplicativo *does* é feito, mas somente quando o pacote foi alterado (quando uma atualização de aplicativo é instalada, por exemplo). Você não deve modificar esse diretório assim mesmo, pois ele está assinado e, portanto, devem ser alteradas após a instalação. 
-   **Biblioteca/Caches** – o diretório de cache destina-se a arquivos de trabalho que não precisam de backup. 
-   **tmp** – esse diretório é usado para arquivos temporários que são criados e excluídos quando forem mais necessários, ou para arquivos que iOS exclui quando ele precisa de espaço. 


Fazendo backup de uma grande quantidade de dados pode levar muito tempo. Se você decidir que você precisa fazer backup de qualquer documento específico ou dados, seu aplicativo só deve usar os documentos e a biblioteca de pastas para isso. Para dados transitórios ou arquivos que podem ser facilmente recuperados da rede, use os Caches ou o diretório tmp.

Lembre-se de que iOS 'limpará' do sistema de arquivos quando um dispositivo é executado muito pouco espaço em disco. Esse processo removerá todos os arquivos da biblioteca/Caches e tmp pasta de aplicativos que não estão em execução no momento.

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>Conformidade com o iCloud iOS5 restrições de Backup

Apple introduzida *iCloud Backup* funcionalidade com o iOS 5. Quando o Backup do iCloud está habilitado, todos os arquivos no diretório base do aplicativo (excluindo diretórios que não são normalmente fez backup, por exemplo, o pacote de aplicativos, `Caches` e `tmp`) são submetidos a backup para iCloud servidores. Esse recurso fornece ao usuário com um backup completo no caso do dispositivo é perdido, roubado ou danificado.

Como o iCloud oferece apenas 5Gb de espaço 'gratuito' para cada usuário e para evitar desnecessariamente usando a largura de banda, Apple espera aplicativos somente backup essenciais gerado pelo usuário com dados. Para estar de acordo com as diretrizes de armazenamento de dados do iOS, você deve limitar a quantidade de dados que passam por backup seguindo os itens a seguir:

-  Armazene apenas dados gerados pelo usuário ou dados que não podem ser recriados, no diretório de documentos (que é armazenado em backup). 
-  Armazenar todos os dados que podem ser facilmente recriados ou baixados novamente no `Library/Caches` ou `tmp` (que não é armazenado em backup e pode ser 'limpo'). 
-  Se você tiver arquivos que podem ser apropriados para o `Library/Caches` ou `tmp` pasta, mas você não deseja 'limpa' out, armazená-los em outro lugar (como `Library/YourData` ) e aplicar a ' não fizer backup de ' atributo para impedir que os arquivos usando ba de Backup no iCloud ndwidth e espaço de armazenamento. Esses dados ainda usam o espaço no dispositivo, para que você deve gerenciá-lo com cuidado e excluí-la quando possível. 

O ' não fazer backup de ' atributo é definido usando o `NSFileManager` classe. Certifique-se de que a classe é `using Foundation` e chame `SetSkipBackupAttribute` esta aparência:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Quando `SetSkipBackupAttribute` é `true` o arquivo não será submetido a backup, independentemente do diretório que são armazenadas em (até mesmo o `Documents` diretório). Você pode consultar o atributo usando o `GetSkipBackupAttribute` método e você poderá redefini-lo chamando o `SetSkipBackupAttribute` método com `false`, assim:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>IOS de dados entre aplicativos e extensões de aplicativo de compartilhamento

Como extensões de aplicativo é executado como parte de um aplicativo de host (em vez de seu aplicativo recipiente), o compartilhamento de dados não é automática incluído para que o trabalho adicional é necessário. Grupos de aplicativos são que o mecanismo do iOS usa para permitir que aplicativos diferentes compartilhar dados. Se os aplicativos foram configurados corretamente com os direitos corretos e provisionamento, eles podem acessar um diretório compartilhado fora da área de segurança iOS normal.

### <a name="configure-an-app-group"></a>Configurar um grupo de aplicativos

O local compartilhado é configurado usando um [grupo de aplicativos](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que é configurado na seção **Certificates, Identifiers & Profiles (Certificados, Identificadores e Perfis)** no [iOS Dev Center (Centro de Desenvolvimento iOS)](https://developer.apple.com/devcenter/ios/). Esse valor também deve ser referenciado no **Entitlements.plist** de cada projeto.

Para obter informações sobre como criar e configurar um grupo de aplicativos, consulte o [recursos de grupo do aplicativo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) guia.

### <a name="files"></a>Arquivos

O aplicativo do iOS e a extensão também podem compartilhar arquivos usando um caminho de arquivo comuns (considerando estão adequadamente configurados com os direitos corretos e provisionamento):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```
> [!IMPORTANT]
> **Observação:** se o caminho do grupo retornado é `null`, verifique a configuração dos direitos e o perfil de provisionamento e certifique-se de que elas estão corretas.


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>Atualizações de versão do aplicativo

Quando uma nova versão do seu aplicativo é baixada, o iOS cria um novo diretório inicial e armazena o novo pacote de aplicativo. iOS move as seguintes pastas da versão anterior do seu pacote de aplicativo para o novo diretório inicial:

-  **Documentos**
-  **Library**


Outros diretórios também podem ser copiados e colocar em seu novo diretório inicial, mas não estão garantidas a serem copiados, para que seu aplicativo não deve depender esse comportamento do sistema.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo mostrou que operações de sistema de arquivos são tão simples com xamarin, assim como acontece com qualquer outro aplicativo .NET. Ele também introduziu a proteção do aplicativo e examinou as implicações de segurança que faz com que. Em seguida, ele explorou o conceito de um pacote de aplicativo. Finalmente, ele enumerados especializados diretórios disponíveis para seu aplicativo e explicados suas funções durante backups e atualizações de aplicativo.


## <a name="related-links"></a>Links relacionados

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [Guia de programação do sistema de arquivos](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registrar o arquivo de tipos de seu aplicativo suporta](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
