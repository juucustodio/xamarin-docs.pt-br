---
title: Usando o iCloud com Xamarin. iOS
description: Este documento descreve o iCloud e seu uso em aplicativos Xamarin. iOS. Ele aborda o armazenamento de chave-valor, o armazenamento de documentos e o backup do iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/09/2016
ms.openlocfilehash: f2bc6ce6c709f99c744554d80b065e34961904ac
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105849"
---
# <a name="using-icloud-with-xamarinios"></a>Usando o iCloud com Xamarin. iOS

A API de armazenamento do iCloud no iOS 5 permite que os aplicativos salvem os documentos do usuário e os dados específicos do aplicativo em um local central e acessem esses itens de todos os dispositivos do usuário.

Há quatro tipos de armazenamento disponíveis:

- **Armazenamento de chave-valor** -para compartilhar pequenas quantidades de dados com seu aplicativo nos outros dispositivos de um usuário.

- **Armazenamento UIDocument** -para armazenar documentos e outros dados na conta do icloud do usuário usando uma subclasse de UIDocument.

- **CoreData** -armazenamento de banco de dados SQLite.

- **Arquivos e diretórios individuais** -para gerenciar vários arquivos diferentes diretamente no sistema de arquivos.

Este documento aborda os dois primeiros tipos – pares chave-valor e subclasses UIDocument-e como usar esses recursos no Xamarin. iOS.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="requirements"></a>Requisitos

- A versão estável mais recente do Xamarin. iOS
- Xcode 10
- Visual Studio para Mac ou o Visual Studio 2019.

## <a name="preparing-for-icloud-development"></a>Preparando para o desenvolvimento do iCloud

Os aplicativos devem ser configurados para usar o iCloud no [portal de provisionamento da Apple](https://developer.apple.com/account/ios/overview.action) e o próprio projeto. Antes de desenvolver para o iCloud (ou experimentar os exemplos), siga as etapas abaixo.

Para configurar corretamente um aplicativo para acessar o iCloud:

- **Localize seu TeamID** – faça logon no [Developer.Apple.com](https://developer.apple.com) e visite o **centro de membros > sua conta > Resumo da conta de desenvolvedor** para obter sua ID de equipe (ou ID individual para desenvolvedores únicos). Será uma cadeia de 10 caracteres ( **A93A5CM278** , por exemplo)-esta parte do "identificador do contêiner".

- **Criar uma nova ID do aplicativo** – para criar uma ID de aplicativo, siga as etapas descritas na [seção provisionamento para tecnologias de armazenamento do guia de provisionamento de dispositivos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)e certifique-se de verificar o **icloud** como um serviço permitido:

 [![](introduction-to-icloud-images/icloud-sml.png "Verifique o iCloud como um serviço permitido")](introduction-to-icloud-images/icloud.png#lightbox)

- **Criar um novo perfil de provisionamento** -para criar um perfil de provisionamento, siga as etapas descritas no guia de provisionamento de [dispositivos](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Adicione o identificador de contêiner a direitos. plist** -o formato do identificador de contêiner `TeamID.BundleID`é. Para obter mais informações, consulte o guia [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) .

- **Configurar as propriedades do projeto** – no arquivo info. plist, verifique se o **identificador do pacote** corresponde ao conjunto de **ID do pacote** ao [criar uma ID do aplicativo](~/ios/deploy-test/provisioning/capabilities/index.md); A assinatura do pacote iOS usa um **perfil de provisionamento** que contém uma ID do aplicativo com o serviço de aplicativo do icloud e o arquivo de **direitos personalizado** selecionado. Isso pode ser feito no Visual Studio no painel de propriedades do projeto.

- **Habilitar icloud em seu dispositivo** -vá para **Configurações > icloud** e verifique se o dispositivo está conectado.
Selecione e ative a opção **documentos & dados** .

- **Você deve usar um dispositivo para testar o iCloud** -ele não funcionará no simulador.
Na verdade, você realmente precisa de dois ou mais dispositivos conectados com a mesma ID da Apple para ver o iCloud em ação.

## <a name="key-value-storage"></a>Armazenamento de chave-valor

O armazenamento de chave-valor é destinado a pequenas quantidades de dados que um usuário deseja manter entre dispositivos, como a última página que eles exibiram em um livro ou revista. O armazenamento de chave-valor não deve ser usado para fazer backup de dados.

Há algumas limitações a serem consideradas ao usar o armazenamento de chave-valor:

- **Tamanho máximo da chave** -nomes de chave não podem ter mais de 64 bytes.

- **Tamanho máximo do valor** -você não pode armazenar mais de 64 quilobytes em um único valor.

- **Tamanho máximo do repositório de chave-valor para um aplicativo** -os aplicativos só podem armazenar até 64 kilobytes de dados de valor de chave no total. As tentativas de definir chaves além desse limite falharão e o valor anterior persistirá.

- **Tipos de dados** – somente tipos básicos como cadeias de caracteres, números e boolianos podem ser armazenados.

O exemplo **iCloudKeyValue** demonstra como funciona. O código de exemplo cria uma chave chamada para cada dispositivo: você pode definir essa chave em um dispositivo e observar que o valor é propagado para outras pessoas. Ele também cria uma chave chamada "Shared" que pode ser editada em qualquer dispositivo – se você editar em vários dispositivos de uma vez, o iCloud decidirá qual valor "WINS" (usando um carimbo de data/hora na alteração) e será propagado.

Esta captura de tela mostra o exemplo em uso. Quando as notificações de alteração são recebidas do iCloud, elas são impressas na exibição de texto de rolagem na parte inferior da tela e atualizadas nos campos de entrada.

 [![](introduction-to-icloud-images/icloud-kv-arrows.png "O fluxo de mensagens entre dispositivos")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Configurando e recuperando dados

Este código mostra como definir um valor de cadeia de caracteres.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

Chamar Synchronize garante que o valor seja persistido somente no armazenamento em disco local. A sincronização para o iCloud ocorre em segundo plano e não pode ser "forçada" pelo código do aplicativo. Com boa conectividade de rede, a sincronização geralmente ocorrerá em 5 segundos, no entanto, se a rede for ruim (ou desconectada), uma atualização poderá demorar muito mais.

Você pode recuperar um valor com este código:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

O valor é recuperado do armazenamento de dados local-esse método não tenta contatar os servidores do iCloud para obter o valor "mais recente". o iCloud atualizará o armazenamento de dados local de acordo com sua própria agenda.

### <a name="deleting-data"></a>Excluindo dados

Para remover completamente um par chave-valor, use o método remove da seguinte maneira:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Observando as alterações

Um aplicativo também pode receber notificações quando os valores são alterados pelo iCloud adicionando um observador ao `NSNotificationCenter.DefaultCenter`.
O código a seguir do método **KeyValueViewController.cs** `ViewWillAppear` mostra como escutar essas notificações e criar uma lista de quais chaves foram alteradas:

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

Em seguida, seu código pode executar alguma ação com a lista de chaves alteradas, como atualizar uma cópia local delas ou atualizar a interface do usuário com os novos valores.

Os possíveis motivos de alteração são: ServerChange (0), InitialSyncChange (1) ou QuotaViolationChange (2). Você pode acessar o motivo e executar um processamento diferente, se necessário (por exemplo, talvez seja necessário remover algumas chaves como resultado de um *QuotaViolationChange*).

## <a name="document-storage"></a>Armazenamento de documentos

o armazenamento de documentos do iCloud foi projetado para gerenciar dados que são importantes para seu aplicativo (e para o usuário). Ele pode ser usado para gerenciar arquivos e outros dados que seu aplicativo precisa executar, enquanto ao mesmo tempo fornece o backup baseado em iCloud e a funcionalidade de compartilhamento em todos os dispositivos do usuário.

Este diagrama mostra como todos se encaixam. Cada dispositivo tem dados salvos no armazenamento local (o UbiquityContainer) e o daemon iCloud do sistema operacional cuida do envio e recebimento de dados na nuvem. Todo o acesso ao arquivo para o UbiquityContainer deve ser feito por meio de filepresenter/filecoordinator para impedir o acesso simultâneo. A `UIDocument` classe implementa os para você; este exemplo mostra como usar UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "A visão geral do armazenamento de documentos")](introduction-to-icloud-images/icloud-overview.png#lightbox)

O exemplo iCloudUIDoc implementa uma subclasse `UIDocument` simples que contém um único campo de texto. O texto é renderizado em `UITextView` a e as edições são propagadas pelo icloud para outros dispositivos com uma mensagem de notificação mostrada em vermelho. O código de exemplo não lida com recursos mais avançados do iCloud, como a resolução de conflitos.

Esta captura de tela mostra o aplicativo de exemplo-depois de alterar o texto e pressionar **UpdateChangeCount** , o documento é sincronizado por meio do icloud para outros dispositivos.

 [![](introduction-to-icloud-images/iclouduidoc.png "Esta captura de tela mostra o aplicativo de exemplo depois de alterar o texto e pressionar UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Há cinco partes no exemplo de iCloudUIDoc:

1. **Acessando o UbiquityContainer** -determine se o iCloud está habilitado e, nesse caso, o caminho para a área de armazenamento do icloud do aplicativo.

1. **Criando uma subclasse UIDocument** -crie uma classe para intermediária entre o armazenamento iCloud e seus objetos de modelo.

1. **Localizando e abrindo documentos** do icloud `NSFileManager` – `NSPredicate` use e para localizar documentos do icloud e abri-los.

1. **Exibindo documentos do icloud** – expor propriedades `UIDocument` de seu para que você possa interagir com controles de interface do usuário.

1. **Salvando documentos do icloud** – Verifique se as alterações feitas na interface do usuário são persistidas no disco e no iCloud.

Todas as operações do iCloud são executadas (ou devem ser executadas) de forma assíncrona para que não sejam bloqueadas enquanto aguardam algo acontecer. Você verá três maneiras diferentes de realizar isso no exemplo:

 **Threads** - `AppDelegate.FinishedLaunching` na chamada inicial para `GetUrlForUbiquityContainer` é feito em outro thread para impedir o bloqueio do thread principal.

 **NotificationCenter** -registrando-se para notificações quando operações assíncronas, `NSMetadataQuery.StartQuery` como concluídas.

 **Manipuladores de conclusão** – passando métodos para execução na conclusão de operações assíncronas como `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Acessando o UbiquityContainer

A primeira etapa do uso do armazenamento de documentos do iCloud é determinar se o iCloud está habilitado e, em caso positivo, o local do "contêiner onipresença" (o diretório em que os arquivos habilitados para iCloud são armazenados no dispositivo).

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

Embora o exemplo não faça isso, a Apple recomenda chamar GetUrlForUbiquityContainer sempre que um aplicativo chegar ao primeiro plano.

### <a name="creating-a-uidocument-subclass"></a>Criando uma subclasse UIDocument

Todos os arquivos e diretórios do iCloud (isto é, qualquer coisa armazenada no diretório UbiquityContainer) devem ser gerenciados usando métodos NSFileManager, implementando o protocolo NSFilePresenter e gravando por meio de um NSFileCoordinator.
A maneira mais simples de fazer tudo isso não é escrevê-lo, mas a subclasse UIDocument que faz tudo para você.

Há apenas dois métodos que você deve implementar em uma subclasse UIDocument para trabalhar com o iCloud:

- **LoadFromContents** – passa o NSData do conteúdo do arquivo para desempacotar em sua classe de modelo/es.

- **ContentsForType** -solicitação para fornecer a representação NSData de sua classe de modelo/es para salvar em disco (e a nuvem).

Este código de exemplo de **iCloudUIDoc\MonkeyDocument.cs** mostra como implementar UIDocument.

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

O modelo de dados, nesse caso, é muito simples, um único campo de texto. Seu modelo de dados pode ser tão complexo quanto necessário, como um documento XML ou dados binários. A função principal da implementação UIDocument é converter entre suas classes de modelo e uma representação NSData que pode ser salva/carregada no disco.

### <a name="finding-and-opening-icloud-documents"></a>Localizando e abrindo documentos do iCloud

O aplicativo de exemplo lida apenas com um único arquivo-Test. txt, de modo que o código em `NSPredicate` AppDelegate.cs `NSMetadataQuery` cria um e para procurar especificamente esse nome do arquivo. O `NSMetadataQuery` é executado de forma assíncrona e envia uma notificação quando ela é concluída. `DidFinishGathering`é chamado pelo observador de notificação, interrompe a consulta e chama loaddocument, que usa `UIDocument.Open` o método com um manipulador de conclusão para tentar carregar o arquivo e exibi-lo `MonkeyDocumentViewController`em um.

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

A exibição de um UIDocument não deve ser diferente de qualquer outra classe de modelo – as propriedades são exibidas nos controles da interface do usuário, possivelmente editadas pelo utilizador e gravadas novamente no modelo.

No exemplo **iCloudUIDoc\MonkeyDocumentViewController.cs** , exibe o texto MonkeyDocument em um `UITextView`. `ViewDidLoad`Escuta a notificação enviada no `MonkeyDocument.LoadFromContents` método. `LoadFromContents`é chamado quando o iCloud tem novos dados para o arquivo, para que a notificação indique que o documento foi atualizado.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

O manipulador de notificação de código de exemplo chama um método para atualizar a interface do usuário, nesse caso, sem nenhuma detecção ou resolução de conflitos.

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

Para adicionar um UIDocument ao iCloud, você pode `UIDocument.Save` chamar diretamente (apenas para novos documentos) ou mover um arquivo existente `NSFileManager.DefaultManager.SetUbiquitious`usando. O código de exemplo cria um novo documento diretamente no contêiner onipresença com esse código (há dois manipuladores de conclusão aqui, um para a `Save` operação e outro para o aberto):

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

Alterações subsequentes no documento não são "salvas" diretamente, em vez disso `UIDocument` , dizemos ao que `UpdateChangeCount`ele mudou e ele agendará automaticamente uma operação salvar em disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gerenciando documentos do iCloud

Os usuários podem gerenciar documentos do iCloud no diretório **documentos** do "contêiner onipresença" fora do seu aplicativo por meio de configurações; Eles podem exibir a lista de arquivos e passar o dedo para excluir. O código do aplicativo deve ser capaz de lidar com a situação em que os documentos são excluídos pelo usuário. Não armazene dados de aplicativos internos no diretório **documentos** .

 [![](introduction-to-icloud-images/icloudstorage.png "Gerenciando fluxo de trabalho de documentos do iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)

Os usuários também receberão avisos diferentes quando tentarem remover um aplicativo habilitado para iCloud de seu dispositivo, para informá-los sobre o status dos documentos do iCloud relacionados a esse aplicativo.

 [![](introduction-to-icloud-images/icloud-delete1.png "Caixa de diálogo de exemplo quando o usuário tenta remover um aplicativo habilitado para iCloud de seu dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Caixa de diálogo de exemplo quando o usuário tenta remover um aplicativo habilitado para iCloud de seu dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Backup do iCloud

Embora o backup no iCloud não seja um recurso que é acessado diretamente pelos desenvolvedores, a maneira como você cria seu aplicativo pode afetar a experiência do usuário.
A Apple fornece [diretrizes de armazenamento de dados do IOS](https://developer.apple.com/icloud/documentation/data-storage/) para os desenvolvedores seguirem em seus aplicativos Ios.

A consideração mais importante é se seu aplicativo armazena arquivos grandes que não são gerados pelo usuário (por exemplo, um aplicativo de leitor de revista que armazena centenas de megabytes de conteúdo por problema). A Apple prefere que você não armazene esse tipo de dados onde será feito backup no iCloud e preencha desnecessariamente a cota do iCloud do usuário.

Os aplicativos que armazenam grandes quantidades de dados como esse devem armazená-los em um dos diretórios de usuário cujo backup não seja feito (por exemplo, Caches ou tmp) ou use `NSFileManager.SetSkipBackupAttribute` para aplicar um sinalizador a esses arquivos para que o iCloud os ignore durante operações de backup.

## <a name="summary"></a>Resumo

Este artigo apresentou o novo recurso iCloud incluído no iOS 5. Ele examinou as etapas necessárias para configurar seu projeto para usar o iCloud e, em seguida, forneceu exemplos de como implementar recursos do iCloud.

O exemplo de armazenamento de valor-chave demonstrou como o iCloud pode ser usado para armazenar uma pequena quantidade de dados semelhante à maneira como os NSUserPreferences são armazenados. O exemplo UIDocument mostrou como dados mais complexos podem ser armazenados e sincronizados em vários dispositivos por meio do iCloud.

Por fim, incluía uma breve discussão sobre como a adição do backup do iCloud deve influenciar o design do aplicativo.

## <a name="related-links"></a>Links relacionados

- [Introdução ao iCloud (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introductiontoicloud)
- [Código de exemplo do seminário do iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Slides do seminário do iCloud](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Armazenamento do iCloud](https://support.apple.com/kb/HT4847)
