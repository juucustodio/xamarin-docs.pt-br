---
title: iCloud
description: A Apple introduziu iCloud no iOS 5 como um serviço para permitir que aplicativos armazenar dados em servidores da Apple e sincronizados em todos os dispositivos usados pela mesma pessoa (por meio de sua ID da Apple). Ele também tem um componente de backup, onde os dados em seus dispositivos é feito um backup para servidores da Apple. Este documento descreve como usar alguns do iCloud APIs fornecidas pela Apple para armazenar e recuperar dados de seus servidores, com exemplos em c# para armazenar pares de dados de chave-valor pequenos e para armazenar documentos. Ele também discute como o Backup do iCloud pode influenciar o design do seu aplicativo.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/09/2016
ms.openlocfilehash: a62d4621a8f3ace64401d64e35c806317a591c03
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="icloud"></a>iCloud

_A Apple introduziu iCloud no iOS 5 como um serviço para permitir que aplicativos armazenar dados em servidores da Apple e sincronizados em todos os dispositivos usados pela mesma pessoa (por meio de sua ID da Apple). Ele também tem um componente de backup, onde os dados em seus dispositivos é feito um backup para servidores da Apple. Este documento descreve como usar alguns do iCloud APIs fornecidas pela Apple para armazenar e recuperar dados de seus servidores, com exemplos em c# para armazenar pares de dados de chave-valor pequenos e para armazenar documentos. Ele também discute como o Backup do iCloud pode influenciar o design do seu aplicativo._

A API de armazenamento no iCloud no iOS 5 permite que aplicativos salvar os documentos do usuário e dados específicos do aplicativo para um local central e acessar esses itens em todos os dispositivos do usuário.

Há quatro tipos de armazenamento disponíveis:

- **Armazenamento de chave-valor** - compartilhar pequenas quantidades de dados com o seu aplicativo em um usuário de outros dispositivos.

- **Armazenamento UIDocument** - para armazenar documentos e outros dados na conta do iCloud do usuário usando uma subclasse de UIDocument.

- **CoreData** -armazenamento de banco de dados SQLite.

- **Arquivos e diretórios individuais** - para gerenciamento de muitos arquivos diferentes diretamente no sistema de arquivos.

Este documento aborda os dois primeiros tipos - pares chave-valor e subclasses UIDocument - e como usar esses recursos em xamarin.

> [!IMPORTANT]
> Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com a norma de proteção de dados geral (GDPR) da União Europeia.

## <a name="requirements"></a>Requisitos

- A versão estável mais recente do xamarin
- Xcode 8 ou mais recente
- Visual Studio para Mac ou o Visual Studio 2015 e mais recente.

## <a name="preparing-for-icloud-development"></a>Preparando para o desenvolvimento no iCloud

Aplicativos devem ser configurados para usar iCloud no [Portal de provisionamento de Apple](https://developer.apple.com/account/ios/overview.action) e o projeto em si. Antes de desenvolver para iCloud (ou experimentar os exemplos) siga as etapas abaixo.

Para configurar corretamente um aplicativo para acessar o iCloud:

-   **Localizar seu TeamID** -logon [developer.apple.com](http://developer.apple.com) e visite o **membro Center > sua conta > Resumo da conta de desenvolvedor** para obter sua equipe ID (ou individuais de desenvolvedores único ). É uma cadeia de caracteres de 10 ( **A93A5CM278** por exemplo)-isso faz parte do "identificador de contêiner".

-   **Criar uma nova ID de aplicativo** - para criar uma ID do aplicativo, siga as etapas descritas no [provisionamento para a seção de tecnologias de armazenamento do guia de provisionamento do dispositivo](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)e certifique-se de verificar **iCloud** como um serviço permitido:

 [![](introduction-to-icloud-images/icloud-sml.png "Verifique o iCloud como um serviço permitido")](introduction-to-icloud-images/icloud.png#lightbox)

- **Criar um novo perfil de provisionamento** - para criar um perfil de provisionamento, siga as etapas descritas no [aprovisionamento guia](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile) .

- **Adicione o identificador do contêiner para Entitlements.plist** -o formato do identificador de contêiner é `TeamID.BundleID`. Para obter mais informações, consulte o [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

- **Configurar as propriedades do projeto** - no Info. plist Certifique-se de arquivo o **identificador de pacote** corresponde a **ID do pacote** definido quando [criação de uma ID de aplicativo ](~/ios/deploy-test/provisioning/capabilities/index.md); O iOS a assinatura de pacote usa um **perfil de provisionamento de** que contêm uma ID de aplicativo com o iCloud do serviço de aplicativo e o **personalizado direitos** arquivo selecionado. Isso pode todas ser feito no Visual Studio em Painel de propriedades do projeto.

- **Habilitar iCloud em seu dispositivo** - acesse **Configurações > iCloud** e certifique-se de que o dispositivo está conectado.
Selecione e ative o **documentos e dados** opção.

- **Você deve usar um dispositivo para testar iCloud** -ele não funcionará no simulador.
Na verdade, você realmente precisa de dois ou mais dispositivos conectados com a mesma ID Apple para ver iCloud em ação.


## <a name="key-value-storage"></a>Armazenamento de chave-valor

Armazenamento de chave-valor destina-se a pequenas quantidades de dados que um usuário poderia desejar persistente entre dispositivos - como a última página que eles exibiram em um livro ou magazine. Armazenamento de chave-valor não deve ser usado para fazer o backup de dados.

Existem algumas limitações a serem consideradas ao usar o armazenamento de chave-valor:

- **Tamanho máximo da chave** -nomes de chave não podem ter mais de 64 bytes.

- **Tamanho máximo do valor** -não é possível armazenar mais de 64 KB em um único valor.

- **Tamanho do repositório de chave-valor máximo para um aplicativo** -aplicativos só podem armazenar até 64 kilobytes de dados de valor de chave no total. Tentativa de definir chaves além desse limite falhará e o valor anterior será mantido.

- **Tipos de dados** - somente os tipos básicos como cadeias de caracteres, números e valores booleanos podem ser armazenados.

O **iCloudKeyValue** exemplo demonstra como ele funciona. O código de exemplo cria uma chave chamada para cada dispositivo: você pode definir essa chave em um dispositivo e observe o valor são propagados para outros usuários. Ele também cria uma chave chamada "Shared", que pode ser editado em qualquer dispositivo - se você editar uma vez em vários dispositivos, iCloud será decidir qual valor "wins" (usando um carimbo de hora em que a alteração) e obtém propagadas.

Esta captura de tela mostra o exemplo em uso. Após o recebimento de notificações de alteração do iCloud, eles são impressos na exibição de texto de rolagem na parte inferior da tela e atualizados nos campos de entrada.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "O fluxo de mensagens entre dispositivos")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Configurando e recuperando dados

Este código mostra como definir um valor de cadeia de caracteres.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

Chamar Synchronize garante que o valor é mantido somente o armazenamento de disco local. A sincronização com o iCloud ocorre em segundo plano e não pode ser "forçada" pelo código do aplicativo. Com boa conectividade de rede a sincronização geralmente ocorrerá em 5 segundos, no entanto, se a rede estiver baixo (ou desconectado) uma atualização pode levar muito mais.

Você pode recuperar um valor com este código:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

O valor é recuperado do armazenamento de dados local – esse método não tenta contatar servidores iCloud para obter o valor de "mais recente". iCloud atualizará o armazenamento de dados local de acordo com a sua própria agenda.

### <a name="deleting-data"></a>Excluindo dados

Para remover completamente um par chave-valor, use o método Remove como este:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Observando as alterações

Um aplicativo também pode receber notificações quando os valores são alterados no iCloud adicionando um observador para o `NSNotificationCenter.DefaultCenter`.
O código a seguir de **KeyValueViewController.cs** `ViewWillAppear` método mostra como escutar das notificações e criar uma lista do que as chaves foram alteradas:

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

Seu código pode, em seguida, executar alguma ação com a lista de chaves alteradas, como atualizar uma cópia local deles ou atualizar a interface do usuário com os novos valores.

Alteração possíveis motivos são: ServerChange (0), InitialSyncChange (1) ou QuotaViolationChange (2). Você pode acessar o motivo e executar processamento diferente, se necessário (por exemplo, você pode precisar remover algumas chaves como resultado de uma *QuotaViolationChange*).

## <a name="document-storage"></a>Armazenamento de documentos

iCloud armazenamento de documento foi projetado para gerenciar os dados que são importantes para seu aplicativo (e para o usuário). Ele pode ser usado para gerenciar arquivos e outros dados que seu aplicativo precisa executar, enquanto, ao mesmo tempo fornecer backup com base no iCloud e funcionalidade de compartilhamento em todos os dispositivos do usuário.

Este diagrama mostra como tudo se encaixa em conjunto. Cada dispositivo tem dados salvos no armazenamento local (o UbiquityContainer) e iCloud do sistema operacional que daemon cuida de envio e recebimento de dados na nuvem. Todo o acesso de arquivo para o UbiquityContainer deve ser feito via FilePresenter/FileCoordinator para evitar o acesso simultâneo. O `UIDocument` classe implementa para você; Este exemplo mostra como usar UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "Visão geral de armazenamento de documento")](introduction-to-icloud-images/icloud-overview.png#lightbox)

O exemplo iCloudUIDoc implementa um simples `UIDocument` subclasse que contém um único campo de texto. O texto é renderizado em um `UITextView` e edições são propagadas por iCloud para outros dispositivos com uma mensagem de notificação mostrada em vermelho. O código de exemplo não lidar com recursos mais avançados, iCloud como resolução de conflitos.

Esta captura de tela mostra o aplicativo de exemplo - depois de alterar o texto e pressionando **UpdateChangeCount** o documento é sincronizado por meio do iCloud a outros dispositivos.

 [![](introduction-to-icloud-images/iclouduidoc.png "Esta captura de tela mostra o aplicativo de exemplo após alterar o texto e pressionando UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Há cinco partes para o exemplo de iCloudUIDoc:

1. **Acessando o UbiquityContainer** -determine se iCloud estiver habilitado e nesse caso o caminho para a área de armazenamento no iCloud do seu aplicativo.

1. **Criação de uma subclasse de UIDocument** -criar uma classe para intermediar os seus objetos de modelo e armazenamento no iCloud.

1. **Localizar e abrir documentos iCloud** -usar `NSFileManager` e `NSPredicate` para localizar documentos iCloud e abri-los.

1. **Exibindo documentos iCloud** -expõem as propriedades de seu `UIDocument` para que você pode interagir com os controles de interface do usuário.

1. **Salvando documentos iCloud** -Certifique-se de que as alterações feitas na interface de usuário são persistentes no disco e no iCloud.

Todas as operações de iCloud executar (ou deve ser executado) assincronamente para que não bloqueiam ao aguardar uma ação. Você verá três maneiras diferentes de conseguir isso no exemplo:

 **Threads** - no `AppDelegate.FinishedLaunching` a chamada inicial para `GetUrlForUbiquityContainer` é feito em outro thread para evitar o bloqueio do thread principal.

 **NotificationCenter** - registrar notificações quando assíncrona de operações, como `NSMetadataQuery.StartQuery` concluída.

 **Manipuladores de conclusão** - passando métodos a serem executados após a conclusão das operações assíncronas como `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Acessando o UbiquityContainer

Usando o armazenamento de documentos do iCloud a primeira etapa é determinar se iCloud está habilitado e, nesse caso o local do "contêiner onipresença" (o diretório onde os arquivos habilitados para iCloud são armazenados no dispositivo).

Esse código está no `AppDelegate.FinishedLaunching` método do exemplo.

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

Embora o exemplo não fizer isso, a Apple recomenda chamando GetUrlForUbiquityContainer sempre que um aplicativo é fornecido para o primeiro plano.

### <a name="creating-a-uidocument-subclass"></a>Criação de uma subclasse de UIDocument

Todos os iCloud arquivos e diretórios (ie. nada armazenado no diretório UbiquityContainer) deve ser gerenciado usando métodos NSFileManager, implementar o protocolo NSFilePresenter e gravação por meio de um NSFileCoordinator.
A maneira mais simples de fazer tudo isso não é gravá-la por conta própria, mas subclasse UIDocument que faz isso tudo isso para você.

Há apenas dois métodos que você deve implementar uma subclasse de UIDocument para trabalhar com o iCloud:

- **LoadFromContents** -passa no NSData do conteúdo do arquivo para que você desempacote em sua classe de modelo/es.

- **ContentsForType** -solicitação para fornecer a representação de NSData de sua classe de modelo/es para salvar em disco (e a nuvem).

Esse código de exemplo do **iCloudUIDoc\MonkeyDocument.cs** mostra como implementar UIDocument.

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

Nesse caso, o modelo de dados é muito simple - um único campo de texto. O modelo de dados pode ser tão complexo quanto necessário, como um documento Xml ou dados binários. A função principal da implementação UIDocument é converter entre suas classes de modelo e uma representação de NSData que pode ser salvas/carregadas no disco.

### <a name="finding-and-opening-icloud-documents"></a>Localizar e abrir documentos do iCloud

O aplicativo de exemplo lida apenas com um único arquivo - Test. txt - para que o código em **appdelegate. CS** cria um `NSPredicate` e `NSMetadataQuery` para pesquisar especificamente para esse nome de arquivo. O `NSMetadataQuery` executa de modo assíncrono e envia uma notificação quando ela estiver concluída. `DidFinishGathering` é chamado pelo observador de notificação, interrompe a consulta e chama LoadDocument, que usa o `UIDocument.Open` método com um processador de conclusão para tentar carregar o arquivo e exibi-lo em um `MonkeyDocumentViewController`.

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

### <a name="displaying-icloud-documents"></a>Exibindo documentos iCloud

Exibir um UIDocument não deve ser diferente para qualquer outra classe de modelo
- propriedades são exibidas em controles de interface do usuário, possivelmente editado pelo usuário e, em seguida, Write-back para o modelo.

O exemplo **iCloudUIDoc\MonkeyDocumentViewController.cs** exibe o texto MonkeyDocument em um `UITextView`. `ViewDidLoad` escuta a notificação enviada no `MonkeyDocument.LoadFromContents` método. `LoadFromContents` é chamado quando iCloud tem novos dados para o arquivo, para que a notificação indica que o documento foi atualizado.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

O manipulador de notificação de código de exemplo chama um método para atualizar a interface do usuário - nesse caso, sem detecção de conflito ou resolução.

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

Para adicionar um UIDocument para iCloud, você pode chamar `UIDocument.Save` diretamente (de novos documentos somente) ou mover um arquivo existente usando `NSFileManager.DefaultManager.SetUbiquitious`. O exemplo de código cria um novo documento diretamente no contêiner onipresença com este código (há dois manipuladores conclusão aqui, uma para o `Save` operação e outro para a abrir):

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

Alterações subsequentes para o documento não são "salvos" diretamente, em vez disso, podemos dizer o `UIDocument` que ele foi alterado com `UpdateChangeCount`, e ele agendará automaticamente salvar a operação de disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gerenciando documentos no iCloud

Os usuários podem gerenciar documentos iCloud o **documentos** diretório do "contêiner onipresença" fora de seu aplicativo por meio de configurações; eles podem exibir a lista de arquivos e passe o dedo para excluir. Código do aplicativo deve ser capaz de lidar com a situação em que os documentos são excluídos pelo usuário. Não armazene dados de aplicativo interno no **documentos** directory.

 [![](introduction-to-icloud-images/icloudstorage.png "Gerenciando o fluxo de trabalho de documentos no iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Os usuários também receberá avisos diferentes quando eles tentam remover um aplicativo habilitado para iCloud do seu dispositivo, para informar o status do iCloud documentos relacionados a esse aplicativo.

 [![](introduction-to-icloud-images/icloud-delete1.png "Caixa de diálogo de exemplo quando o usuário tenta remover um aplicativo habilitado para iCloud do seu dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Caixa de diálogo de exemplo quando o usuário tenta remover um aplicativo habilitado para iCloud do seu dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Backup do iCloud

Enquanto o backup no iCloud não é um recurso que é acessado diretamente por desenvolvedores, a maneira como você projetar seu aplicativo pode afetar a experiência do usuário.
Apple fornece [iOS diretrizes de armazenamento de dados](http://developer.apple.com/icloud/documentation/data-storage/) para desenvolvedores a seguir em seus aplicativos do iOS.

O aspecto mais importante é se o seu aplicativo armazena arquivos grandes que não são gerados pelo usuário (por exemplo, um aplicativo de leitor de revista que armazena hundred-plus megabytes de conteúdo por problema). Apple prefere que você não armazenar esse tipo de dados, onde ele será ser submetidos a backup no icloud e preencher desnecessariamente cota do iCloud do usuário.

Aplicativos que armazenam grandes quantidades de dados, como isso devem ou armazená-lo em um dos diretórios de usuário que não é feito backup (por exemplo. Caches ou tmp) ou usar `NSFileManager.SetSkipBackupAttribute` para aplicar um sinalizador para esses arquivos para que o iCloud ignora durante operações de backup.

## <a name="summary"></a>Resumo

Este artigo introduziu o novo recurso no iCloud incluído no iOS 5. Ele examinado as etapas necessárias para configurar seu projeto para usar iCloud e fornecidos exemplos de como implementar recursos de iCloud.

O exemplo de armazenamento de chave-valor demonstrou como o iCloud pode ser usado para armazenar uma pequena quantidade de dados semelhantes ao modo que nsuserpreferences são armazenados. O exemplo de UIDocument mostrou como mais dados complexos podem ser armazenados e sincronizados em vários dispositivos por meio do iCloud.

Finalmente, ele incluído uma breve discussão sobre como a adição de Backup do iCloud deve influenciar o design do aplicativo.



## <a name="related-links"></a>Links relacionados

- [Introdução ao iCloud (exemplo)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [o código de exemplo seminário do iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud seminário Slides](http://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [iCloud armazenamento](http://support.apple.com/kb/HT4847)
