---
title: Acesso ao sistema de arquivos no Xamarin. iOS
description: Este documento descreve como trabalhar com o sistema de arquivos no Xamarin. iOS. Ele aborda diretórios, leitura de arquivos, serialização XML e JSON, área restrita do aplicativo, compartilhamento de arquivos por meio do iTunes e muito mais.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/12/2018
ms.openlocfilehash: e52f9abb31090f3acc361eb5a3f9ae2e12600b36
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653523"
---
# <a name="file-system-access-in-xamarinios"></a>Acesso ao sistema de arquivos no Xamarin. iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)

Você pode usar o Xamarin. Ios e `System.IO` as classes na *BCL (base Class Library) do .net* para acessar o sistema de arquivos do Ios. A classe `File` permite criar, excluir e ler arquivos e a classe `Directory` permite criar, excluir ou enumerar o conteúdo de diretórios. Você também pode usar `Stream` subclasses, o que pode fornecer um grau maior de controle sobre as operações de arquivo (como a compactação ou a pesquisa de posição dentro de um arquivo).

o iOS impõe algumas restrições sobre o que um aplicativo pode fazer com o sistema de arquivos para preservar a segurança dos dados de um aplicativo e para proteger os usuários de aplicativos malignant. Essas restrições fazem parte da *área restrita do aplicativo* – um conjunto de regras que limita o acesso de um aplicativo a arquivos, preferências, recursos de rede, hardware, etc. Um aplicativo é limitado à leitura e gravação de arquivos em seu diretório base (local instalado); Ele não pode acessar os arquivos de outro aplicativo.

o iOS também tem alguns recursos específicos do sistema de arquivos: determinados diretórios exigem tratamento especial em relação a backups e atualizações, e os aplicativos também podem compartilhar arquivos entre si e o aplicativo de **arquivos** (desde o Ios 11) e via iTunes.

Este artigo aborda os recursos e as restrições do sistema de arquivos do iOS e inclui um aplicativo de exemplo que demonstra como usar o Xamarin. iOS para executar algumas operações simples do sistema de arquivos:

[![Um exemplo de iOS executando algumas operações simples do sistema de arquivos](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>Acesso geral a arquivos

O Xamarin. Ios permite que você use as `System.IO` classes .net para operações do sistema de arquivos no Ios.

Os trechos de código a seguir ilustram algumas operações de arquivo comuns. Você encontrará todos eles abaixo no arquivo **SampleCode.cs** , no aplicativo de exemplo deste artigo.

### <a name="working-with-directories"></a>Trabalhando com diretórios

Esse código enumera os subdiretórios no diretório atual (especificado pelo parâmetro "./"), que é o local do seu aplicativo executável.
Sua saída será uma lista de todos os arquivos e pastas implantados com seu aplicativo (exibido na janela do console enquanto você estiver Depurando).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>Lendo arquivos

Para ler um arquivo de texto, você só precisa de uma única linha de código. Este exemplo exibirá o conteúdo de um arquivo de texto na janela de saída do aplicativo.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>Serialização XML

Embora trabalhar com o namespace `System.Xml` completo esteja além do escopo deste artigo, você pode facilmente desserializar um documento XML do sistema de arquivos usando um StreamReader como este trecho de código:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Para obter mais informações, consulte a documentação de [System. xml](xref:System.Xml) and [Serialization](xref:System.Xml.Serialization). Consulte a [documentação do Xamarin. Ios](~/ios/deploy-test/linker.md) no vinculador – muitas vezes, você precisará adicionar `[Preserve]` o atributo às classes que pretende serializar.

### <a name="creating-files-and-directories"></a>Criando arquivos e diretórios

Este exemplo mostra como usar a `Environment` classe para acessar a pasta documentos onde podemos criar arquivos e diretórios.

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

[![](file-system-images/json01.png "Adicionando o pacote NuGet ao projeto de aplicativos")](file-system-images/json01.png#lightbox)

Em seguida, adicione uma classe para atuar como o modelo de dados para serialização/desserialização (nesse `Account.cs`caso):

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

Por fim, crie uma instância da `Account` classe, Serialize-a para dados JSON e grave-a em um arquivo:

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

Para obter mais informações sobre como trabalhar com dados JSON em um aplicativo .NET, consulte a [documentação](http://www.newtonsoft.com/json/help)do JSON. net.

## <a name="special-considerations"></a>Considerações especiais

Apesar das semelhanças entre as operações de arquivo do Xamarin. iOS e do .NET, o iOS e o Xamarin. iOS são diferentes do .NET de algumas maneiras importantes.

### <a name="making-project-files-accessible-at-runtime"></a>Tornando os arquivos de projeto acessíveis em tempo de execução

Por padrão, se você adicionar um arquivo ao seu projeto, ele não será incluído no assembly final e, portanto, não estará disponível para seu aplicativo. Para incluir um arquivo no assembly, você deve marcá-lo com uma ação de compilação especial, chamada conteúdo.

Para marcar um arquivo para inclusão, clique com o botão direito do mouse nos arquivos e escolha **criar conteúdo &gt; da ação** em Visual Studio para Mac. Você também pode alterar a **ação de Build** na folha de **Propriedades** do arquivo.

### <a name="case-sensitivity"></a>Diferenciação de maiúsculas e minúsculas

É importante entender que o sistema de arquivos iOS diferencia maiúsculas de *minúsculas*. Diferenciação de maiúsculas e minúsculas significa que os nomes de arquivos e diretórios devem corresponder exatamente – **README. txt** e **README. txt** seriam considerados nomes de arquivo diferentes.

Isso pode ser confuso para os desenvolvedores do .NET que estão mais familiarizados com o sistema de arquivos do Windows, que não diferencia maiúsculas de *minúsculas* – **arquivos**, **arquivos**e **arquivos** se refereriam ao mesmo diretório.

> [!WARNING]
> O simulador do iOS não diferencia maiúsculas de minúsculas.
> Se o nome de arquivo estiver em maiúsculas e minúsculas entre o próprio arquivo e as referências a ele no código, seu código ainda poderá funcionar no simulador, mas ele falhará em um dispositivo real. Esse é um dos motivos pelos quais é importante implantar e testar em um dispositivo real cedo e com frequência durante o desenvolvimento do iOS.

### <a name="path-separator"></a>Separador de caminho

o iOS usa a barra '/' como o separador de caminho (que é diferente do Windows, que usa a barra invertida ' \ ').

Devido a essa diferença confusa, é uma boa prática usar o `System.IO.Path.Combine` método, que se ajusta à plataforma atual, em vez de codificar um separador de caminho específico. Essa é uma etapa simples que torna seu código mais portátil para outras plataformas.

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

[![O conteúdo do pacote de aplicativo](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

O pacote de aplicativos é o que está instalado no simulador ou em seu dispositivo durante o teste e, por fim, é o que é enviado à Apple para inclusão na loja de aplicativos.

## <a name="application-directories"></a>Diretórios de aplicativos

Quando seu aplicativo é instalado em um dispositivo, o sistema operacional cria um diretório base para seu aplicativo e cria um número de diretórios dentro do diretório raiz do aplicativo que estão disponíveis para uso. Desde o iOS 8, os diretórios acessíveis pelo usuário [não estão localizados](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) na raiz do aplicativo, portanto, você não pode derivar os caminhos para o pacote de aplicativos dos diretórios de usuário, ou vice-versa.

Esses diretórios, como determinar seu caminho e suas finalidades são listadas abaixo:

&nbsp;

|Diretório|Descrição|
|---|---|
|[ApplicationName].app/|**No Ios 7 e versões anteriores**, esse é `ApplicationBundle` o diretório onde o executável do aplicativo está armazenado. A estrutura de diretório que você cria em seu aplicativo existe nesse diretório (por exemplo, imagens e outros tipos de arquivo que você marcou como recursos em seu projeto de Visual Studio para Mac).<br /><br />Se você precisar acessar os arquivos de conteúdo dentro de seu pacote de aplicativo, o caminho para esse diretório estará disponível `NSBundle.MainBundle.BundlePath` por meio da propriedade.|
|Documento|Use esse diretório para armazenar documentos de usuário e arquivos de dados de aplicativo.<br /><br />O conteúdo desse diretório pode ser disponibilizado para o usuário por meio do compartilhamento de arquivos do iTunes (embora isso esteja desabilitado por padrão). Adicione uma `UIFileSharingEnabled` chave booliana ao arquivo info. plist para permitir que os usuários acessem esses arquivos.<br /><br />Mesmo que um aplicativo não habilite imediatamente o compartilhamento de arquivos, você deve evitar colocar os arquivos que devem ser ocultados dos usuários nesse diretório (como arquivos de banco de dados, a menos que você pretenda compartilhá-los). Desde que os arquivos confidenciais permaneçam ocultos, esses arquivos não serão expostos (e potencialmente movidos, modificados ou excluídos pelo iTunes) se o compartilhamento de arquivos estiver habilitado em uma versão futura.<br /><br /> Você pode usar o `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` método para obter o caminho para o diretório de documentos para seu aplicativo.<br /><br />O conteúdo desse diretório é submetido a backup pelo iTunes.|
|Biblioteca|O diretório de biblioteca é um bom local para armazenar arquivos que não são criados diretamente pelo usuário, como bancos de dados ou outros arquivos gerados pelo aplicativo. O conteúdo desse diretório nunca é exposto ao usuário por meio do iTunes.<br /><br />Você pode criar seus próprios subdiretórios na biblioteca; no entanto, já existem alguns diretórios criados pelo sistema aqui que você deve estar atento, incluindo preferências e caches.<br /><br />O conteúdo desse diretório (exceto para o subdiretório caches) é submetido a backup pelo iTunes. Os diretórios personalizados criados na biblioteca serão submetidos a backup.|
|Biblioteca/Preferências/|Os arquivos de preferência específicos do aplicativo são armazenados nesse diretório. Não crie esses arquivos diretamente. Em vez disso, `NSUserDefaults` use a classe.<br /><br />O conteúdo desse diretório é submetido a backup pelo iTunes.|
|Biblioteca/caches/|O diretório caches é um bom local para armazenar arquivos de dados que podem ajudar seu aplicativo a ser executado, mas que podem ser facilmente recriados. O aplicativo deve criar e excluir esses arquivos conforme necessário e ser capaz de recriar esses arquivos, se necessário. o iOS 5 também pode excluir esses arquivos (em situações de armazenamento baixo), no entanto, ele não fará isso enquanto o aplicativo estiver em execução.<br /><br />O conteúdo desse diretório não será submetido a backup pelo iTunes, o que significa que eles não estarão presentes se o usuário restaurar um dispositivo e eles não estiverem presentes depois que uma versão atualizada do seu aplicativo for instalada.<br /><br />Por exemplo, caso seu aplicativo não possa se conectar à rede, você pode usar o diretório caches para armazenar dados ou arquivos para fornecer uma boa experiência offline. O aplicativo pode salvar e recuperar esses dados rapidamente enquanto aguarda respostas de rede, mas não precisa fazer backup e pode ser facilmente recuperado ou recriado após uma atualização de restauração ou de versão.|
|tmp/|Os aplicativos podem armazenar arquivos temporários que são necessários apenas por um curto período nesse diretório. Para conservar espaço, os arquivos devem ser excluídos quando não forem mais necessários. O sistema operacional também pode excluir arquivos desse diretório quando um aplicativo não está em execução.<br /><br />Não é feito backup do conteúdo deste diretório pelo iTunes.<br /><br />Por exemplo, o diretório tmp pode ser usado para armazenar arquivos temporários que são baixados para exibição para o usuário (como avatars do Twitter ou anexos de email), mas que podem ser excluídos depois de serem exibidos (e baixados novamente se forem necessários no futuro) .|

Esta captura de tela mostra a estrutura do diretório em uma janela do Finder:

[![](file-system-images/08-library-directory.png "Esta captura de tela mostra a estrutura de diretório em uma janela do localizador")](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>Acessando outros diretórios programaticamente

Os exemplos de diretório e arquivo anteriores acessaram o `Documents` diretório. Para gravar em outro diretório, você deve construir um caminho usando a sintaxe "..", conforme mostrado aqui:

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

Os caminhos para `Caches` os `tmp` diretórios e podem ser construídos desta forma:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>Compartilhamento com o aplicativo de arquivos

o iOS 11 introduziu o aplicativo **arquivos** – um navegador de arquivos para IOS que permite ao usuário ver e interagir com seus arquivos no iCloud e também armazenados por qualquer aplicativo que ofereça suporte a ele. Para permitir que o usuário acesse os arquivos diretamente em seu aplicativo, crie uma nova chave booliana no arquivo `LSSupportsOpeningDocumentsInPlace` **info. plist** e defina `true`-a como, como aqui:

![Definir LSSupportsOpeningDocumentsInPlace no info. plist](file-system-images/51-supports-opening.png)

O diretório de **documentos** do aplicativo agora estará disponível para navegação no aplicativo **arquivos** . No aplicativo **arquivos** , navegue até **em meu iPhone** e cada aplicativo com arquivos compartilhados ficará visível. As capturas de tela abaixo mostram a aparência do [aplicativo de exemplo FileSystem](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode) :

![aplicativo de arquivos do iOS 11](file-system-images/50-files-app-1-sml.png) ![Procurar meus arquivos do iPhone](file-system-images/50-files-app-2-sml.png) ![Arquivos de aplicativo de exemplo](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Compartilhando arquivos com o usuário por meio do iTunes

Os usuários podem acessar os arquivos no diretório de documentos do seu aplicativo `Info.plist` editando e criando um **aplicativo que dá suporte à entrada iTunes Sharing** (`UIFileSharingEnabled`) na exibição da **fonte** , como mostrado aqui:

[![Adicionar o aplicativo dá suporte à propriedade de compartilhamento do iTunes](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Esses arquivos podem ser acessados no iTunes quando o dispositivo estiver conectado e o usuário `Apps` escolher a guia. Por exemplo, a captura de tela a seguir mostra os arquivos no aplicativo selecionado compartilhado por meio do iTunes:

[![Esta captura de tela mostra os arquivos no aplicativo selecionado compartilhado por meio do iTunes](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Os usuários só podem acessar os itens de nível superior neste diretório por meio do iTunes. Eles não podem ver o conteúdo de nenhum subdiretório (embora possam copiá-los para seu computador ou excluí-los). Por exemplo, com arquivos GoodReader, PDF e EPUB podem ser compartilhados com o aplicativo para que os usuários possam lê-los em seus dispositivos iOS.

Os usuários que modificarem o conteúdo de suas pastas de documentos poderão causar problemas se não forem cuidadosos. Seu aplicativo deve levar isso em consideração e ser resiliente a atualizações destrutivas da pasta documentos.

O código de exemplo deste artigo cria um arquivo e uma pasta na pasta documentos (em **SampleCode.cs**) e habilita o compartilhamento de arquivos no arquivo **info. plist** . Esta captura de tela mostra como eles aparecem no iTunes:

[![Esta captura de tela mostra como os arquivos aparecem no iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Consulte o artigo [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) para obter informações sobre como definir ícones para o aplicativo e para qualquer tipo de documento personalizado que você criar.

Se a `UIFileSharingEnabled` chave for falsa ou não estiver presente, o compartilhamento de arquivos será, por padrão, desabilitado e os usuários não poderão interagir com o diretório de documentos.

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

A Apple introduziu a funcionalidade de *backup do icloud* com iOS 5. Quando o backup do icloud está habilitado, todos os arquivos no diretório base do aplicativo (excluindo os diretórios que normalmente não são incluídos no backup, por exemplo, o `Caches`pacote de `tmp`aplicativo, e) são submetidos a backup em servidores icloud. Esse recurso fornece ao usuário um backup completo caso seu dispositivo seja perdido, roubado ou danificado.

Como o iCloud fornece apenas 5 GB de espaço "livre" para cada usuário e para evitar o uso desnecessário de largura de banda, a Apple espera que os aplicativos apenas faça backup de dados essenciais gerados pelo usuário. Para obedecer às diretrizes de armazenamento de dados do iOS, você deve limitar a quantidade de dados que são submetidos a backup ao aderir aos seguintes itens:

- Armazene somente dados gerados pelo usuário ou dados que não podem ser recriados, no diretório documentos (cujo backup é feito).
- Armazene quaisquer outros dados que possam ser facilmente recriados ou rebaixados `Library/Caches` no `tmp` ou no (cujo backup não é feito e pode ser ' limpo ').
- Se você tiver arquivos que podem ser apropriados para a `Library/Caches` pasta `tmp` ou, mas não quiser que `Library/YourData`eles sejam "limpos", armazene-os em outro lugar (como) e aplique o atributo "não fazer backup" para impedir que os arquivos usem o backup do icloud largura de banda e espaço de armazenamento. Esses dados ainda usam espaço no dispositivo, portanto, você deve gerenciá-lo cuidadosamente e excluí-lo quando possível.

O atributo ' não fazer backup ' é definido usando a `NSFileManager` classe. Verifique se sua classe `using Foundation` é e `SetSkipBackupAttribute` se chama da seguinte maneira:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Quando `SetSkipBackupAttribute` o arquivo não será submetido a backup, independentemente do diretório no qual ele está armazenado (mesmo o `Documents` diretório). `true` Você pode consultar o atributo usando o `GetSkipBackupAttribute` método e pode redefini-lo chamando o método `SetSkipBackupAttribute` com `false`, da seguinte maneira:

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
> Se o caminho do grupo retornado `null`for, verifique a configuração dos direitos e o perfil de provisionamento e certifique-se de que eles estão corretos.

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
