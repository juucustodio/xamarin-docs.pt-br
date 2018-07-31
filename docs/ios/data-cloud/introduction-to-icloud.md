---
title: Usando o iCloud com xamarin. IOS
description: Este documento descreve o iCloud e seu uso em aplicativos xamarin. IOS. Ele aborda o armazenamento de chave-valor, armazenamento de documentos e Backup do iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/09/2016
ms.openlocfilehash: b72ecc40994d9336c4941f3db700796edd80e81f
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353211"
---
# <a name="using-icloud-with-xamarinios"></a>Usando o iCloud com xamarin. IOS

A API de armazenamento no iCloud no iOS 5 permite que os aplicativos salvar documentos do usuário e dados específicos do aplicativo em um local central e acessar esses itens em todos os dispositivos do usuário.

Há quatro tipos de armazenamento disponíveis:

- **Armazenamento de chave-valor** – compartilhar pequenas quantidades de dados com o seu aplicativo em outros dispositivos de um usuário.

- **Armazenamento de UIDocument** – para armazenar documentos e outros dados na conta do iCloud do usuário usando uma subclasse de UIDocument.

- **CoreData** -armazenamento de banco de dados SQLite.

- **Arquivos e diretórios individuais** - para gerenciamento de muitos arquivos diferentes diretamente no sistema de arquivos.

Este documento aborda os dois primeiros tipos – pares chave-valor e subclasses de UIDocument – e como usar esses recursos no xamarin. IOS.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="requirements"></a>Requisitos

- A versão estável mais recente do xamarin. IOS
- Xcode 8 ou mais recente
- O Visual Studio para Mac ou Visual Studio 2015 e versões mais recente.

## <a name="preparing-for-icloud-development"></a>Preparando para o desenvolvimento do iCloud

Aplicativos devem ser configurados para usar o iCloud na [Portal de provisionamento da Apple](https://developer.apple.com/account/ios/overview.action) e o projeto em si. Antes de desenvolver para o iCloud (ou experimentando os exemplos) siga as etapas abaixo.

Para configurar corretamente um aplicativo para acessar o iCloud:

-   **Localizar seu TeamID** -faça logon no [developer.apple.com](http://developer.apple.com) e visite o **Central de membros > sua conta > Resumo da conta de desenvolvedor** obter sua ID de equipe (ou ID Individual para desenvolvedores únicos ). É uma cadeia de caracteres de 10 caracteres ( **A93A5CM278** por exemplo)-isso faz parte da "identificador de contêiner".

-   **Criar uma nova ID de aplicativo** – para criar uma ID do aplicativo, siga as etapas descritas na [provisionamento para a seção de tecnologias de Store do guia de provisionamento de dispositivos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)e não se esqueça de verificar **iCloud** como um serviço permitido:

 [![](introduction-to-icloud-images/icloud-sml.png "Verifique o iCloud como um serviço permitido")](introduction-to-icloud-images/icloud.png#lightbox)

- **Criar um novo perfil de provisionamento** – para criar um perfil de provisionamento, siga as etapas descritas em de [guia de provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Adicione o identificador do contêiner ao Entitlements. plist** -o formato do identificador de contêiner é `TeamID.BundleID`. Para obter mais informações, consulte o [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

- **Configurar as propriedades do projeto** - no Info. plist arquivo Certifique-se o **identificador de pacote** corresponde a **ID do pacote** definida quando [criando uma ID do aplicativo ](~/ios/deploy-test/provisioning/capabilities/index.md); O iOS assinatura de pacote usa uma **perfil de provisionamento** que contêm uma ID de aplicativo com o serviço de aplicativo, iCloud e o **direitos personalizados** arquivo selecionado. Isso pode ser feito no Visual Studio sob o painel de propriedades do projeto.

- **Habilitar iCloud no dispositivo** – acesse **Configurações > iCloud** e certifique-se de que o dispositivo está conectado.
Selecionar e ativar o **documentos e dados** opção.

- **Você deve usar um dispositivo para testar o iCloud** -ele não funcionará no simulador.
Na verdade, você realmente precisa de dois ou mais dispositivos de todos os conectado com a mesma ID Apple para ver o iCloud em ação.


## <a name="key-value-storage"></a>Armazenamento de chave-valor

Armazenamento de chave-valor destina-se a pequenas quantidades de dados que um usuário poderia desejar persistidas entre dispositivos - como a última página que eles exibiram em um livro ou uma revista. Armazenamento de chave-valor não deve ser usado para fazer o backup de dados.

Existem algumas limitações a serem consideradas ao usar o armazenamento de chave-valor:

- **Tamanho máximo da chave** -nomes de chave não podem ter mais de 64 bytes.

- **Tamanho do valor máximo** -não é possível armazenar mais de 64 KB em um único valor.

- **Tamanho do repositório de chave-valor máximo para um aplicativo** -aplicativos só podem armazenar até 64 KB de dados de chave-valor no total. Tentativa de definir chaves além desse limite falhará e o valor anterior serão mantidas.

- **Tipos de dados** - somente os tipos básicos, como cadeias de caracteres, números e booleanos podem ser armazenados.

O **iCloudKeyValue** exemplo demonstra como ele funciona. O código de exemplo cria uma chave chamada para cada dispositivo: você pode definir essa chave em um dispositivo e observar o valor são propagadas a outros usuários. Ele também cria uma chave chamada "Shared", que pode ser editado em qualquer dispositivo – se você editar em vários dispositivos ao mesmo tempo, iCloud será decidir qual valor "vence" (usando um carimbo de hora em que a alteração) e obtém propagada.

Esta captura de tela mostra o exemplo em uso. Quando as notificações de alteração são recebidas do iCloud, eles são impressos na exibição de texto de rolagem na parte inferior da tela e atualizados nos campos de entrada.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "O fluxo de mensagens entre dispositivos")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Configurando e recuperando dados

Este código mostra como definir um valor de cadeia de caracteres.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

Chamar Synchronize garante que o valor é persistido apenas o armazenamento de disco local. A sincronização com o iCloud ocorre em segundo plano e não pode ser "forçada" pelo código do aplicativo. Com boa conectividade de rede a sincronização geralmente acontece dentro de 5 segundos, no entanto, se a rede é ruim (ou desconectado) uma atualização pode levar muito mais.

Você pode recuperar um valor com este código:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

O valor é recuperado do armazenamento de dados local – esse método não tenta contatar servidores iCloud para obter o valor "mais recente". iCloud atualizará o armazenamento de dados local acordo com seu próprio cronograma.

### <a name="deleting-data"></a>Excluindo dados

Para remover completamente um par chave-valor, use o método Remove como este:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Observar as alterações

Um aplicativo também pode receber notificações quando valores são alterados por iCloud, adicionando um observador para o `NSNotificationCenter.DefaultCenter`.
O código a seguir da **KeyValueViewController.cs** `ViewWillAppear` método mostra como detectar essas notificações e criar uma lista dos quais teclas foram alteradas:

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

Seu código, em seguida, pode levar a alguma ação com a lista de chaves alteradas, como atualizar uma cópia local deles ou atualizar a interface do usuário com os novos valores.

Alteração de possíveis motivos é: ServerChange (0), InitialSyncChange (1) ou QuotaViolationChange (2). Você pode acessar o motivo e executar processamento diferente, se necessário (por exemplo, você pode precisar remover algumas chaves como resultado de uma *QuotaViolationChange*).

## <a name="document-storage"></a>Armazenamento de documentos

Armazenamento de documentos do iCloud é projetado para gerenciar os dados que são importantes para seu aplicativo (e o usuário). Ele pode ser usado para gerenciar arquivos e outros dados que seu aplicativo precisa ser executado, e, ao mesmo tempo fornecendo o backup do iCloud e compartilhamento de recursos entre todos os dispositivos do usuário.

Este diagrama mostra como tudo isso se ajusta. Cada dispositivo tem dados salvos no armazenamento local (o UbiquityContainer) e o Daemon se encarrega de enviar e receber dados na nuvem do iCloud do sistema operacional. Todo o acesso de arquivo para o UbiquityContainer deve ser feito por meio de FilePresenter/FileCoordinator para evitar o acesso simultâneo. O `UIDocument` classe implementa para você; Este exemplo mostra como usar UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "A visão de geral do armazenamento de documento")](introduction-to-icloud-images/icloud-overview.png#lightbox)

O exemplo de iCloudUIDoc implementa um simples `UIDocument` subclasse que contém um único campo de texto. O texto é renderizado em um `UITextView` e edições são propagadas por iCloud para outros dispositivos com uma mensagem de notificação mostrada em vermelho. O código de exemplo não lidar com recursos mais avançados do iCloud, como resolução de conflitos.

Esta captura de tela mostra o aplicativo de exemplo – depois de alterar o texto e pressionando **UpdateChangeCount** o documento é sincronizado por meio do iCloud para outros dispositivos.

 [![](introduction-to-icloud-images/iclouduidoc.png "Esta captura de tela mostra o aplicativo de exemplo após alterar o texto e pressionando UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Há cinco partes para o exemplo de iCloudUIDoc:

1. **Acessando o UbiquityContainer** -determinar se o iCloud estiver habilitado e nesse caso o caminho para a área de armazenamento do iCloud do seu aplicativo.

1. **Criação de uma subclasse de UIDocument** -criar uma classe para intermediar entre armazenamento iCloud e seus objetos de modelo.

1. **Localizar e abrir documentos do iCloud** -use `NSFileManager` e `NSPredicate` para localizar documentos do iCloud e abri-los.

1. **Exibindo documentos do iCloud** -expor as propriedades de seu `UIDocument` para que você pode interagir com controles de interface do usuário.

1. **Salvando documentos do iCloud** -Certifique-se de que as alterações feitas na interface do usuário são persistentes no disco e iCloud.

Todas as operações de iCloud executar (ou deve ser executado) assincronamente para que eles não bloqueiam enquanto aguarda que algo aconteça. Você verá três diferentes maneiras de realizar isso no exemplo:

 **Threads** - no `AppDelegate.FinishedLaunching` a chamada inicial para `GetUrlForUbiquityContainer` é feito em outro thread para evitar o bloqueio do thread principal.

 **NotificationCenter** - registrar para receber notificações quando assíncrona operações, como `NSMetadataQuery.StartQuery` completa.

 **Manipuladores de conclusão** – métodos para serem executados após a conclusão das operações assíncronas, como passando `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Acessando o UbiquityContainer

A primeira etapa no uso do armazenamento de documentos do iCloud é determinar se o iCloud está habilitado e nesse caso o local do "onipresença contêiner" (o diretório onde os arquivos habilitados para iCloud são armazenados no dispositivo).

Esse código está no `AppDelegate.FinishedLaunching` método de exemplo.

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

Embora o exemplo não faz isso, a Apple recomenda chamando GetUrlForUbiquityContainer sempre que um aplicativo vem em primeiro plano.

### <a name="creating-a-uidocument-subclass"></a>Criação de uma subclasse de UIDocument

Todos os arquivos do iCloud e diretórios (ie. qualquer coisa armazenada no diretório UbiquityContainer) deve ser gerenciado usando métodos NSFileManager, Implementando o protocolo NSFilePresenter e gravação por meio de um NSFileCoordinator.
A maneira mais simples de fazer tudo isso não é gravá-lo por conta própria, mas subclasse UIDocument que faz tudo isso para você.

Há apenas dois métodos que você deve implementar em uma subclasse de UIDocument para trabalhar com o iCloud:

- **LoadFromContents** -passa a NSData do conteúdo do arquivo para que você desempacote em sua classe de modelo/es.

- **ContentsForType** -solicitação para fornecer a representação de NSData de sua classe de modelo/es para salvar em disco (e a nuvem).

Esse código de exemplo da **iCloudUIDoc\MonkeyDocument.cs** mostra como implementar UIDocument.

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

Nesse caso, o modelo de dados é muito simple - um único campo de texto. O modelo de dados pode ser tão complexo quanto necessário, como um documento Xml ou dados binários. A função principal da implementação UIDocument é converter entre suas classes de modelo e uma representação de NSData que pode ser salvos/carregada no disco.

### <a name="finding-and-opening-icloud-documents"></a>Localizando e abrir documentos do iCloud

O aplicativo de exemplo lida apenas com um único arquivo - Test. txt - portanto, o código em **AppDelegate.cs** cria um `NSPredicate` e `NSMetadataQuery` procurar especificamente para esse nome de arquivo. O `NSMetadataQuery` executa de forma assíncrona e envia uma notificação quando ela for concluída. `DidFinishGathering` é chamado pelo observador de notificação, interrompe a consulta e chama LoadDocument, que usa o `UIDocument.Open` método com um manipulador de conclusão para tentar carregar o arquivo e exibi-lo em um `MonkeyDocumentViewController`.

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>Exibindo documentos do iCloud

Exibir um UIDocument não deve ser diferente a qualquer outra classe de modelo
- propriedades são exibidas em controles de interface do usuário, possivelmente editado pelo usuário e, em seguida, Write-back para o modelo.

No exemplo **iCloudUIDoc\MonkeyDocumentViewController.cs** exibe o texto MonkeyDocument em um `UITextView`. `ViewDidLoad` a notificação enviada escuta a `MonkeyDocument.LoadFromContents` método. `LoadFromContents` é chamado quando o iCloud tem novos dados para o arquivo, para que a notificação indica que o documento foi atualizado.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

O manipulador de notificação de código de exemplo chama um método para atualizar a interface do usuário – nesse caso, sem nenhuma detecção de conflito ou resolução.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Salvando documentos do iCloud

Para adicionar um UIDocument com o iCloud, você pode chamar `UIDocument.Save` diretamente (para os novos documentos somente) ou mover um arquivo existente usando `NSFileManager.DefaultManager.SetUbiquitious`. O código de exemplo cria um novo documento diretamente no contêiner onipresença com esse código (há dois manipuladores conclusão aqui, um para o `Save` operação e outro para a abertura):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

As alterações subsequentes no documento não são "salvas" diretamente, em vez disso, podemos dizer o `UIDocument` que ele foi alterado com `UpdateChangeCount`, e ele será automaticamente agendar uma opção Salvar para operação de disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gerenciamento de documentos do iCloud

Os usuários podem gerenciar documentos do iCloud na **documentos** diretório de "contêiner onipresença" fora do seu aplicativo por meio de configurações; eles podem exibir a lista de arquivos e passe o dedo para excluir. O código do aplicativo deve ser capaz de lidar com a situação em que os documentos são excluídos pelo usuário. Não armazene dados de aplicativos internos na **documentos** directory.

 [![](introduction-to-icloud-images/icloudstorage.png "Gerenciando o fluxo de trabalho de documentos do iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Os usuários também receberá avisos diferentes ao tentar remover um aplicativo habilitado para iCloud do seu dispositivo, para informá-los do status de documentos do iCloud relacionados a esse aplicativo.

 [![](introduction-to-icloud-images/icloud-delete1.png "Caixa de diálogo de exemplo quando o usuário tenta remover um aplicativo habilitado para iCloud do seu dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Caixa de diálogo de exemplo quando o usuário tenta remover um aplicativo habilitado para iCloud do seu dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Backup do iCloud

Enquanto o backup no iCloud não é um recurso que é acessado diretamente pelos desenvolvedores, a maneira de criar seu aplicativo pode afetar a experiência do usuário.
A Apple fornece [diretrizes de armazenamento de dados do iOS](http://developer.apple.com/icloud/documentation/data-storage/) para os desenvolvedores seguirem em seus aplicativos do iOS.

O aspecto mais importante é se o seu aplicativo armazena arquivos grandes que não são gerados pelo usuário (por exemplo, um aplicativo de leitor de revista que armazena hundred-plus megabytes de conteúdo por problema). Apple prefere que você não armazenar esse tipo de dados em que ele será ser armazenado em backup no iCloud e preencher desnecessariamente cota do iCloud do usuário.

Aplicativos que armazenam grandes quantidades de dados, como isso devem armazená-lo ou em um dos diretórios de usuário que não é armazenado em backup (por exemplo Os caches ou tmp) ou usar `NSFileManager.SetSkipBackupAttribute` para aplicar um sinalizador para esses arquivos, de modo que ignora, iCloud durante operações de backup.

## <a name="summary"></a>Resumo

Este artigo apresentou o novo recurso do iCloud incluído no iOS 5. Ele examinou as etapas necessárias para configurar seu projeto para usar o iCloud e, em seguida, fornecidos exemplos de como implementar recursos do iCloud.

O exemplo de armazenamento de chave-valor demonstrou como iCloud pode ser usado para armazenar uma pequena quantidade de dados de forma semelhantes a como NSUserPreferences são armazenados. O exemplo de UIDocument mostrou como mais dados complexos podem ser armazenados e sincronizados em vários dispositivos por meio do iCloud.

Por fim, ele incluído uma breve discussão sobre como a adição de Backup do iCloud deve influenciar o design do aplicativo.



## <a name="related-links"></a>Links relacionados

- [Introdução ao iCloud (amostra)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [Código de exemplo seminário do iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Seminário Slides do iCloud](http://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Armazenamento do iCloud](http://support.apple.com/kb/HT4847)
