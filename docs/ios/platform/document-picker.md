---
title: Seletor de documento no Xamarin. iOS
description: Este documento descreve o seletor de documentos do iOS e discute como usá-lo no Xamarin. iOS. Ele dá uma olhada no iCloud, documentos, código de instalação comum, extensões de provedor de documentos e muito mais.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2017
ms.openlocfilehash: 67ee8f1ef7f254eb77cfb186a478993b6fd3847c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280031"
---
# <a name="document-picker-in-xamarinios"></a>Seletor de documento no Xamarin. iOS

O seletor de documento permite que os documentos sejam compartilhados entre aplicativos. Esses documentos podem ser armazenados no iCloud ou em um diretório de aplicativo diferente. Os documentos são compartilhados por meio do conjunto de [extensões de provedor de documento](~/ios/platform/extensions.md) que o usuário instalou em seu dispositivo. 

Devido à dificuldade de manter os documentos sincronizados entre aplicativos e a nuvem, eles introduzem uma determinada quantidade de complexidade necessária.

## <a name="requirements"></a>Requisitos

O seguinte é necessário para concluir as etapas apresentadas neste artigo:

- **Xcode 7 e Ios 8 ou mais recente** – as APIs do Xcode 7 e Ios 8 ou mais recentes da Apple precisam ser instaladas e configuradas no computador do desenvolvedor.
- **Visual Studio ou Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada.
- **dispositivo IOS** – um dispositivo IOS executando o Ios 8 ou superior.

## <a name="changes-to-icloud"></a>Alterações no iCloud

Para implementar os novos recursos do selecionador de documento, as seguintes alterações foram feitas no serviço iCloud da Apple:

- O daemon do iCloud foi completamente reescrito usando CloudKit.
- Os recursos existentes do iCloud foram renomeados para a unidade iCloud.
- Foi adicionado suporte para o sistema operacional Microsoft Windows ao iCloud.
- Uma pasta iCloud foi adicionada no localizador de Mac OS.
- os dispositivos iOS podem acessar o conteúdo da pasta Mac OS iCloud.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="what-is-a-document"></a>O que é um documento?

Ao fazer referência a um documento no iCloud, ele é uma entidade única e autônoma e deve ser percebido como tal pelo usuário. Um usuário pode desejar modificar o documento ou compartilhá-lo com outros usuários (usando email, por exemplo).

Há vários tipos de arquivos que o usuário reconhecerá imediatamente como documentos, como páginas, arquivos de palestras ou números. No entanto, o iCloud não está limitado a esse conceito. Por exemplo, o estado de um jogo (como uma correspondência de xadrez) pode ser tratado como um documento e armazenado no iCloud. Esse arquivo pode ser passado entre os dispositivos de um usuário e permitir que ele pegue um jogo no qual parou em um dispositivo diferente.

## <a name="dealing-with-documents"></a>Lidando com documentos

Antes de mergulhar no código necessário para usar o seletor de documento com o Xamarin, este artigo abordará as práticas recomendadas para trabalhar com documentos do iCloud e várias das modificações feitas nas APIs existentes necessárias para dar suporte ao seletor de documento.

### <a name="using-file-coordination"></a>Usando a coordenação de arquivos

Como um arquivo pode ser modificado de vários locais diferentes, a coordenação deve ser usada para evitar a perda de dados.

 [![](document-picker-images/image1.png "Usando a coordenação de arquivos")](document-picker-images/image1.png#lightbox)

Vamos dar uma olhada na ilustração acima:

1. Um dispositivo iOS usando a coordenação de arquivos cria um novo documento e o salva na pasta iCloud.
2. iCloud salva o arquivo modificado na nuvem para distribuição para cada dispositivo.
3. Um Mac conectado vê o arquivo modificado na pasta iCloud e usa a coordenação de arquivos para copiar as alterações para o arquivo.
4. Um dispositivo que não usa a coordenação de arquivos faz uma alteração no arquivo e salva-o na pasta iCloud. Essas alterações são replicadas instantaneamente para os outros dispositivos.

Suponha que o dispositivo iOS original ou o Mac estivesse editando o arquivo, agora suas alterações são perdidas e substituídas pela versão do arquivo do dispositivo não coordenado. Para evitar a perda de dados, a coordenação de arquivos é necessário ao trabalhar com documentos baseados em nuvem.

### <a name="using-uidocument"></a>Usando UIDocument

 `UIDocument`torna as coisas simples ( `NSDocument` ou no MacOS) fazendo todo o trabalho pesado para o desenvolvedor. Ele fornece a coordenação de arquivos interna com filas de plano de fundo para impedir o bloqueio da interface do usuário do aplicativo.

 `UIDocument`expõe várias APIs de alto nível que facilitam o esforço de desenvolvimento de um aplicativo Xamarin para qualquer finalidade exigida pelo desenvolvedor.

O código a seguir cria uma subclasse de `UIDocument` para implementar um documento genérico baseado em texto que pode ser usado para armazenar e recuperar texto do icloud:

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

A `GenericTextDocument` classe apresentada acima será usada em todo este artigo ao trabalhar com o seletor de documento e documentos externos em um aplicativo Xamarin. Ios 8.

## <a name="asynchronous-file-coordination"></a>Coordenação de arquivos assíncronos

o iOS 8 fornece vários novos recursos de coordenação de arquivos assíncronos por meio das novas APIs de coordenação de arquivos. Antes do iOS 8, todas as APIs de coordenação de arquivos existentes eram totalmente síncronas. Isso significava que o desenvolvedor era responsável por implementar seu próprio enfileiramento de fundo para impedir que a coordenação de arquivos bloqueie a interface do usuário do aplicativo.

A nova `NSFileAccessIntent` classe contém uma URL que aponta para o arquivo e várias opções para controlar o tipo de coordenação necessário. O código a seguir demonstra como mover um arquivo de um local para outro usando tentativas:

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>Descobrindo e listando documentos

A maneira de descobrir e listar documentos é usando as APIs `NSMetadataQuery` existentes. Esta seção abordará os novos recursos adicionados `NSMetadataQuery` ao que tornam o trabalho com documentos ainda mais fácil do que antes.

### <a name="existing-behavior"></a>Comportamento existente

Antes do IOS 8, `NSMetadataQuery` estava lento para fazer alterações no arquivo local, como: exclusões, criações e renomeações.

 [![](document-picker-images/image2.png "Visão geral das alterações do arquivo local NSMetadataQuery")](document-picker-images/image2.png#lightbox)

No diagrama acima:

1. Para arquivos que já existem no contêiner de aplicativos, `NSMetadataQuery` o `NSMetadata` tem registros previamente criados e colocados em spool para que estejam instantaneamente disponíveis para o aplicativo.
1. O aplicativo cria um novo arquivo no contêiner do aplicativo.
1. Há um atraso antes `NSMetadataQuery` de ver a modificação no contêiner do aplicativo e criar o registro necessário. `NSMetadata`


Devido ao atraso na criação do `NSMetadata` registro, o aplicativo tinha que ter duas fontes de dados abertas: uma para as alterações de arquivo local e outra para as alterações baseadas em nuvem.

### <a name="stitching"></a>Junção

No Ios 8, `NSMetadataQuery` é mais fácil de usar diretamente com um novo recurso chamado costura:

 [![](document-picker-images/image3.png "NSMetadataQuery com um novo recurso chamado costura")](document-picker-images/image3.png#lightbox)

Usando costura no diagrama acima:

1. Como antes, para arquivos que já existem no contêiner de aplicativos, `NSMetadataQuery` o `NSMetadata` tem registros previamente criados e colocados em spool.
1. O aplicativo cria um novo arquivo no contêiner de aplicativo usando a coordenação de arquivos.
1. Um gancho no contêiner do aplicativo vê a modificação e as `NSMetadataQuery` chamadas para criar o `NSMetadata` registro necessário.
1. O `NSMetadata` registro é criado diretamente após o arquivo e é disponibilizado para o aplicativo.


O uso de costura no aplicativo não precisa mais abrir uma fonte de dados para monitorar alterações de arquivos locais e na nuvem. Agora o aplicativo pode depender `NSMetadataQuery` diretamente.

> [!IMPORTANT]
> A junção só funcionará se o aplicativo estiver usando a coordenação de arquivos, conforme apresentado na seção acima. Se a coordenação de arquivos não estiver sendo usada, as APIs utilizarão como padrão o comportamento anterior ao iOS 8.




### <a name="new-ios-8-metadata-features"></a>Novos recursos de metadados do iOS 8

Os novos recursos a seguir foram adicionados ao `NSMetadataQuery` no Ios 8:

- `NSMetatadataQuery`Agora, é possível listar documentos não locais armazenados na nuvem.
- Novas APIs foram adicionadas para acessar informações de metadados nos documentos baseados em nuvem. 
- Há uma nova `NSUrl_PromisedItems` API que vai acessar os atributos de arquivo de arquivos que podem ou não ter seu conteúdo disponível localmente.
- Use o `GetPromisedItemResourceValue` método para obter informações sobre um determinado arquivo ou use o `GetPromisedItemResourceValues` método para obter informações sobre mais de um arquivo de cada vez.


Dois novos sinalizadores de coordenação de arquivo foram adicionados para lidar com metadados:

- `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
- `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Com os sinalizadores acima, o conteúdo do arquivo de documento não precisa estar disponível localmente para ser usado.

O segmento de código a seguir mostra como `NSMetadataQuery` usar o para consultar a existência de um arquivo específico e criar o arquivo, caso ele não exista:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;


#region Static Properties
public const string TestFilename = "test.txt"; 
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),             NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path 
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>Miniaturas de documento

A Apple sente que a melhor experiência de usuário ao listar documentos para um aplicativo é usar visualizações. Isso fornece ao contexto dos usuários finais, para que eles possam identificar rapidamente o documento com o qual desejam trabalhar.

Antes do iOS 8, mostrar as visualizações de documento exigia uma implementação personalizada. Novo no iOS 8 são atributos do sistema de arquivos que permitem que o desenvolvedor Trabalhe rapidamente com miniaturas de documento.

#### <a name="retrieving-document-thumbnails"></a>Recuperando miniaturas de documento 

Ao chamar os `GetPromisedItemResourceValue` métodos `GetPromisedItemResourceValues` ou, `NSUrl_PromisedItems` API, a `NSUrlThumbnailDictionary`, é retornado. A única chave atualmente neste dicionário é o `NSThumbnial1024X1024SizeKey` e sua correspondência. `UIImage`

#### <a name="saving-document-thumbnails"></a>Salvando miniaturas de documento

A maneira mais fácil de salvar uma miniatura é usar `UIDocument`. Ao chamar o `GetFileAttributesToWrite` método `UIDocument` de e definir a miniatura, ela será salva automaticamente quando o arquivo de documento for. O daemon do iCloud verá essa alteração e a propagará para o iCloud. Em Mac OS X, as miniaturas são geradas automaticamente para o desenvolvedor pelo plug-in de aparência rápida.

Com as noções básicas de como trabalhar com documentos baseados no iCloud, juntamente com as modificações feitas na API existente, estamos prontos para implementar o controlador de exibição do seletor de documentos em um aplicativo móvel do Xamarin iOS 8.


## <a name="enabling-icloud-in-xamarin"></a>Habilitando o iCloud no Xamarin

Antes que o seletor de documento possa ser usado em um aplicativo Xamarin. iOS, o suporte do iCloud precisa ser habilitado em seu aplicativo e por meio da Apple. 

As etapas a seguir explicam o processo de provisionamento para o iCloud.

1. Crie um contêiner do iCloud.
2. Crie uma ID de aplicativo que contenha o serviço de aplicativo iCloud.
3. Crie um perfil de provisionamento que inclua essa ID de aplicativo.

O guia [trabalhando com recursos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) percorre as duas primeiras etapas. Para criar um perfil de provisionamento, siga as etapas no guia de [perfil de provisionamento](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .



As etapas a seguir explicam o processo de configuração do aplicativo para o iCloud:

Faça o seguinte:

1. Abra o projeto no Visual Studio para Mac ou no Visual Studio.
2. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione opções.
3. Na caixa de diálogo opções, selecione **aplicativo IOS**, verifique se o **identificador do pacote** corresponde ao que foi definido na ID do **aplicativo** criada acima para o aplicativo. 
4. Selecione **assinatura de pacote do IOS**, selecione a **identidade do desenvolvedor** e o **perfil de provisionamento** criado acima.
5. Clique no botão **OK** para salvar as alterações e fechar a caixa de diálogo.
6. Clique `Entitlements.plist` com o botão direito do mouse no **Gerenciador de soluções** para abri-lo no editor.

    > [!IMPORTANT]
    > No Visual Studio, talvez seja necessário abrir o editor de direitos clicando com o botão direito do mouse nele, selecionando **abrir com...** e selecionando editor de lista de propriedades

7. Marque **habilitar icloud** , **documentos do icloud** , **armazenamento de chave-valor** e **CloudKit** .
8. Verifique se o **contêiner** existe para o aplicativo (como criado acima). Exemplo: `iCloud.com.your-company.AppName`
9. Salve as alterações no arquivo.

Para obter mais informações sobre direitos, consulte o guia [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) .

Com a configuração acima em vigor, o aplicativo agora pode usar documentos baseados em nuvem e o novo controlador de exibição do selecionador de documentos.

## <a name="common-setup-code"></a>Código de instalação comum

Antes de começar a usar o controlador de exibição do seletor de documentos, há um código de instalação padrão necessário. Comece modificando o arquivo do `AppDelegate.cs` aplicativo e faça com que ele se pareça com o seguinte:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt"; 
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                 new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }


        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");    

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path 
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {    
                    // Yes, inform caller and save location the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();
                
        }
        
        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }
        
        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }
        
        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }
        
        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> O código acima inclui o código da seção descobrindo e listando documentos acima. Ele é apresentado aqui em sua totalidade, como apareceria em um aplicativo real. Para simplificar, este exemplo funciona apenas com um único arquivo embutido em código`test.txt`().

O código acima expõe vários atalhos de unidade do iCloud para facilitar o trabalho com eles no restante do aplicativo.

Em seguida, adicione o seguinte código a qualquer exibição ou contêiner de exibição que usará o seletor de documentos ou trabalhando com documentos baseados em nuvem:

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Isso adiciona um atalho para chegar ao `AppDelegate` e acessar os atalhos do icloud criados acima.

Com esse código em vigor, vamos dar uma olhada na implementação do controlador de exibição do seletor de documento em um aplicativo Xamarin iOS 8.

## <a name="using-the-document-picker-view-controller"></a>Usando o controlador de exibição do seletor de documento

Antes do iOS 8, era muito difícil acessar documentos de outro aplicativo, pois não havia como descobrir documentos fora do aplicativo de dentro do aplicativo.

### <a name="existing-behavior"></a>Comportamento existente

 [![](document-picker-images/image31.png "Visão geral do comportamento existente")](document-picker-images/image31.png#lightbox)

Vamos dar uma olhada no acesso a um documento externo antes do iOS 8:

1. Primeiro, o usuário precisaria abrir o aplicativo que originalmente criou o documento.
1. O documento é selecionado e o `UIDocumentInteractionController` é usado para enviar o documento para o novo aplicativo.
1. Por fim, uma cópia do documento original é colocada no contêiner do novo aplicativo.


A partir daí, o documento está disponível para o segundo aplicativo abrir e editar.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Descobrindo documentos fora do contêiner de um aplicativo

No iOS 8, um aplicativo é capaz de acessar documentos fora de seu próprio contêiner de aplicativo com facilidade:

 [![](document-picker-images/image32.png "Descobrindo documentos fora do contêiner de um aplicativo")](document-picker-images/image32.png#lightbox)

Usando o novo selecionador de documentos `UIDocumentPickerViewController`do icloud (), um aplicativo IOS pode descobrir e acessar diretamente fora do seu contêiner de aplicativos. O `UIDocumentPickerViewController` fornece um mecanismo para que o usuário conceda acesso e edite esses documentos descobertos por meio de permissões.

Um aplicativo deve aceitar que seus documentos apareçam no selecionador de documentos iCloud e estejam disponíveis para que outros aplicativos descubram e trabalhem com eles. Para que um aplicativo Xamarin Ios 8 Compartilhe seu contêiner de aplicativo, edite `Info.plist` o arquivo em um editor de texto padrão e adicione as duas linhas a seguir à parte inferior do dicionário (entre as `<dict>...</dict>` marcas):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

O `UIDocumentPickerViewController` fornece uma ótima nova interface de usuário que permite ao usuário escolher documentos. Para exibir o controlador de exibição do seletor de documento em um aplicativo Xamarin iOS 8, faça o seguinte:

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> O desenvolvedor deve chamar o `StartAccessingSecurityScopedResource` método `NSUrl` de antes que um documento externo possa ser acessado. O `StopAccessingSecurityScopedResource` método deve ser chamado para liberar o bloqueio de segurança assim que o documento tiver sido carregado.

### <a name="sample-output"></a>Saída de Exemplo

Aqui está um exemplo de como o código acima exibiria um seletor de documento quando executado em um dispositivo iPhone:

1. O usuário inicia o aplicativo e a interface principal é exibida:   
 
    [![](document-picker-images/image33.png "A interface principal é exibida")](document-picker-images/image33.png#lightbox)
1. O usuário toca no botão de **ação** na parte superior da tela e é solicitado a selecionar um **provedor de documento** na lista de provedores disponíveis:   
 
    [![](document-picker-images/image34.png "Selecione um provedor de documentos na lista de provedores disponíveis")](document-picker-images/image34.png#lightbox)
1. O **controlador de exibição do seletor de documentos** é exibido para o **provedor de documentos**selecionado:   
 
    [![](document-picker-images/image35.png "O controlador de exibição do seletor de documento é exibido")](document-picker-images/image35.png#lightbox)
1. O usuário toca em uma **pasta de documento** para exibir seu conteúdo:   
 
    [![](document-picker-images/image36.png "O conteúdo da pasta do documento")](document-picker-images/image36.png#lightbox)
1. O usuário seleciona um **documento** e o **seletor de documento** é fechado.
1. A interface principal é exibida novamente, o **documento** é carregado do contêiner externo e seu conteúdo exibido.


A exibição real do controlador de exibição do seletor de documentos depende dos provedores de documentos que o usuário instalou no dispositivo e do modo de seleção de documento que foi implementado. O exemplo acima está usando o modo de abertura, os outros tipos de modo serão discutidos em detalhes abaixo.

## <a name="managing-external-documents"></a>Gerenciando documentos externos

Conforme discutido acima, antes do iOS 8, um aplicativo podia acessar apenas os documentos que faziam parte de seu contêiner de aplicativos. No iOS 8, um aplicativo pode acessar documentos de fontes externas:

 [![](document-picker-images/image37.png "Visão geral do gerenciamento de documentos externos")](document-picker-images/image37.png#lightbox)

Quando o usuário seleciona um documento de uma fonte externa, um documento de referência é gravado no contêiner do aplicativo que aponta para o documento original.

Para ajudar a adicionar essa nova capacidade a aplicativos existentes, vários novos recursos foram adicionados à `NSMetadataQuery` API. Normalmente, um aplicativo usa o escopo de documento onipresente para listar documentos que residem em seu contêiner de aplicativos. Usando esse escopo, somente documentos dentro do contêiner de aplicativo continuarão a ser exibidos.

Usar o novo escopo de documento externo onipresente retornará documentos que residem fora do contêiner de aplicativo e retornam os metadados para eles. O `NSMetadataItemUrlKey` apontará para a URL onde o documento está realmente localizado.

Às vezes, um aplicativo não quer trabalhar com os documentos que estão sendo apontados por referência. Em vez disso, o aplicativo quer trabalhar diretamente com o documento de referência. Por exemplo, o aplicativo pode querer exibir o documento na pasta do aplicativo na interface do usuário ou para permitir que o usuário mova as referências dentro de uma pasta.

No Ios 8, um novo `NSMetadataItemUrlInLocalContainerKey` foi fornecido para acessar o documento de referência diretamente. Essa chave aponta para a referência real para o documento externo em um contêiner de aplicativo.

O `NSMetadataUbiquitousItemIsExternalDocumentKey` é usado para testar se um documento é externo ou não ao contêiner de um aplicativo. O `NSMetadataUbiquitousItemContainerDisplayNameKey` é usado para acessar o nome do contêiner que está hospedando a cópia original de um documento externo.

### <a name="why-document-references-are-required"></a>Por que as referências de documento são necessárias

O principal motivo pelo qual o iOS 8 usa referências para acessar documentos externos é a segurança. Nenhum aplicativo recebe acesso ao contêiner de qualquer outro aplicativo. Somente o seletor de documento pode fazer isso, porque o está executando fora do processo e tem acesso amplo ao sistema.

A única maneira de obter um documento fora do contêiner do aplicativo é usando o seletor de documento e, se a URL retornada pelo seletor tiver escopo de segurança. A URL com escopo de segurança contém apenas informações suficientes para selecionar o documento junto com os direitos de escopo necessários para conceder a um aplicativo acesso ao documento.

É importante observar que, se a URL com escopo de segurança foi serializada em uma cadeia de caracteres e, em seguida, desserializada, as informações de segurança seriam perdidas e o arquivo estaria inacessível a partir da URL. O recurso de referência de documento fornece um mecanismo para retornar aos arquivos apontados por essas URLs.

Portanto, se o aplicativo adquire um `NSUrl` de um dos documentos de referência, ele já tem o escopo de segurança anexado e pode ser usado para acessar o arquivo. Por esse motivo, é altamente recomendável que o desenvolvedor Use `UIDocument` , pois ele lida com todas essas informações e processos para eles.

### <a name="using-bookmarks"></a>Usando indicadores

Nem sempre é viável enumerar os documentos de um aplicativo para retornar a um documento específico, por exemplo, ao fazer a restauração de estado. o iOS 8 fornece um mecanismo para criar indicadores que se destinam diretamente a um determinado documento.

O código a seguir criará um indicador de `UIDocument`uma `FileUrl` propriedade de uma:

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

A API de indicador existente é usada para criar um indicador em relação `NSUrl` a um existente que pode ser salvo e carregado para fornecer acesso direto a um arquivo externo. O código a seguir irá restaurar um indicador que foi criado acima:

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>Abrir vs. Modo de importação e o seletor de documento

O controlador de exibição do seletor de documentos apresenta dois modos diferentes de operação:

1. **Modo aberto** – nesse modo, quando o usuário seleciona e documento externo, o selecionador de documento criará um indicador com escopo de segurança no contêiner do aplicativo.   
 
    [![](document-picker-images/image37.png "Um indicador com escopo de segurança no contêiner do aplicativo")](document-picker-images/image37.png#lightbox)
1. **Modo de importação** – nesse modo, quando o usuário seleciona um documento externo, o seletor de documento não cria um indicador, mas, em vez disso, copia o arquivo em um local temporário e fornece o acesso do aplicativo ao documento neste local:   
 
    [![](document-picker-images/image38.png "O seletor de documento copiará o arquivo em um local temporário e fornecerá o acesso do aplicativo ao documento neste local")](document-picker-images/image38.png#lightbox)   
 Quando o aplicativo termina por qualquer motivo, o local temporário é esvaziado e o arquivo é removido. Se o aplicativo precisar manter o acesso ao arquivo, ele deverá fazer uma cópia e colocá-lo em seu contêiner de aplicativo.


O modo aberto é útil quando o aplicativo deseja colaborar com outro aplicativo e compartilhar as alterações feitas no documento com esse aplicativo. O modo de importação é usado quando o aplicativo não deseja compartilhar suas modificações em um documento com outros aplicativos.

## <a name="making-a-document-external"></a>Tornando um documento externo

Conforme observado acima, um aplicativo iOS 8 não tem acesso a contêineres fora de seu próprio contêiner de aplicativo. O aplicativo pode gravar em seu próprio contêiner localmente ou em um local temporário e, em seguida, usar um modo de documento especial para mover o documento resultante para fora do contêiner do aplicativo para um local escolhido pelo usuário.

Para mover um documento para um local externo, faça o seguinte:

1. Primeiro, crie um novo documento em um local local ou temporário.
1. Crie um `NSUrl` que aponte para o novo documento.
1. Abra um novo controlador de exibição do selecionador de documentos `NSUrl` e passe-o `MoveToService` com o modo de. 
1. Depois que o usuário escolher um novo local, o documento será movido de seu local atual para o novo local.
1. Um documento de referência será gravado no contêiner do aplicativo do aplicativo para que o arquivo ainda possa ser acessado pelo aplicativo de criação.


O código a seguir pode ser usado para mover um documento para um local externo:`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

O documento de referência retornado pelo processo acima é exatamente o mesmo que um criado pelo modo aberto do seletor de documento. No entanto, há ocasiões em que o aplicativo pode desejar mover um documento sem manter uma referência a ele.

Para mover um documento sem gerar uma referência, use o `ExportToService` modo. Exemplo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Ao usar o `ExportToService` modo, o documento é copiado para o contêiner externo e a cópia existente é deixada em seu local original.

## <a name="document-provider-extensions"></a>Extensões do provedor de documentos

Com o iOS 8, a Apple quer que o usuário final seja capaz de acessar qualquer um de seus documentos baseados em nuvem, independentemente de onde eles realmente existem. Para atingir essa meta, o iOS 8 fornece um novo mecanismo de extensão do provedor de documentos.

### <a name="what-is-a-document-provider-extension"></a>O que é uma extensão de provedor de documento?

Simplesmente declarado, uma extensão de provedor de documento é uma maneira para um desenvolvedor, ou de terceiros, fornecer a um armazenamento de documentos alternativo de aplicativo que pode ser acessado exatamente da mesma maneira que o local de armazenamento do iCloud existente.

O usuário pode selecionar um desses locais de armazenamento alternativos no seletor de documentos e pode usar exatamente os mesmos modos de acesso (abrir, importar, mover ou exportar) para trabalhar com arquivos nesse local.

Isso é implementado usando duas extensões diferentes:

- **Extensão de seletor** de documento `UIViewController` – fornece uma subclasse que fornece uma interface gráfica para o usuário escolher um documento de um local de armazenamento alternativo. Essa subclasse será exibida como parte do controlador de exibição do seletor de documento.
- **Extensão de fornecimento de arquivo** – essa é uma extensão que não é da interface do usuário que lida com o conteúdo dos arquivos. Essas extensões são fornecidas por meio da coordenação `NSFileCoordinator` de arquivos (). Esse é outro caso importante em que a coordenação de arquivos é necessária.


O diagrama a seguir mostra o fluxo de dados típico ao trabalhar com extensões de provedor de documento:

 [![](document-picker-images/image39.png "Este diagrama mostra o fluxo de dados típico ao trabalhar com extensões de provedor de documentos")](document-picker-images/image39.png#lightbox)

O seguinte processo ocorre:

1. O aplicativo apresenta um controlador de seletor de documento para permitir que o usuário selecione um arquivo com o qual trabalhar.
1. O usuário seleciona um local de arquivo alternativo e a `UIViewController` extensão personalizada é chamada para exibir a interface do usuário.
1. O usuário seleciona um arquivo desse local e a URL é passada de volta para o seletor de documento.
1. O seletor de documento seleciona a URL do arquivo e a retorna para o aplicativo no qual o usuário deve trabalhar.
1. A URL é passada para o coordenador de arquivo para retornar o conteúdo dos arquivos para o aplicativo.
1. O coordenador de arquivo chama a extensão do provedor de arquivo personalizado para recuperar o arquivo.
1. O conteúdo do arquivo é retornado para o coordenador de arquivos.
1. O conteúdo do arquivo é retornado para o aplicativo.


### <a name="security-and-bookmarks"></a>Segurança e indicadores

Esta seção configurará rapidamente como a segurança e o acesso a arquivos persistentes por meio de indicadores funcionam com as extensões do provedor de documentos. Ao contrário do provedor de documentos do iCloud, que salva automaticamente a segurança e os indicadores no contêiner do aplicativo, as extensões do provedor de documentos não são uma parte do sistema de referência do documento.

Por exemplo: em uma configuração empresarial que fornece seu próprio armazenamento de dados seguro em toda a empresa, os administradores não querem informações corporativas confidenciais acessadas ou processadas pelos servidores do iCloud público. Portanto, o sistema de referência de documento interno não pode ser usado.

O sistema de indicadores ainda pode ser usado e é responsabilidade da extensão do provedor de arquivos processar corretamente uma URL com indicador e retornar o conteúdo do documento apontado por ela.

Para fins de segurança, o iOS 8 tem uma camada de isolamento que persiste as informações sobre qual aplicativo tem acesso a qual identificador dentro do provedor de arquivos. Deve-se observar que todo o acesso ao arquivo é controlado por essa camada de isolamento.

O diagrama a seguir mostra o fluxo de dados ao trabalhar com indicadores e uma extensão de provedor de documento:

 [![](document-picker-images/image40.png "Este diagrama mostra o fluxo de dados ao trabalhar com indicadores e uma extensão de provedor de documentos")](document-picker-images/image40.png#lightbox)

O seguinte processo ocorre:

1. O aplicativo está prestes a entrar no plano de fundo e precisa persistir seu estado. Ele chama `NSUrl` para criar um indicador para um arquivo no armazenamento alternativo.
1. `NSUrl`chama a extensão do provedor de arquivo para obter uma URL persistente para o documento. 
1. A extensão do provedor de arquivos retorna a URL como uma cadeia `NSUrl` de caracteres para o.
1. O `NSUrl` agrupa a URL em um indicador e a retorna ao aplicativo.
1. Quando o aplicativo está ativado de estar em segundo plano e precisa restaurar o estado, ele passa o indicador para `NSUrl` .
1. `NSUrl`chama a extensão do provedor de arquivos com a URL do arquivo.
1. O provedor de extensão de arquivo acessa o arquivo e retorna o local do arquivo para `NSUrl` .
1. O local do arquivo é agrupado com informações de segurança e retornado ao aplicativo.


A partir daqui, o aplicativo pode acessar o arquivo e trabalhar com ele normalmente.

### <a name="writing-files"></a>Gravando arquivos

Esta seção configurará rapidamente como funciona a gravação de arquivos em um local alternativo com uma extensão de provedor de documentos. O aplicativo iOS usará a coordenação de arquivos para salvar informações em disco dentro do contêiner do aplicativo. Logo após o arquivo ter sido gravado com êxito, a extensão do provedor de arquivos será notificada sobre a alteração.

Neste ponto, a extensão do provedor de arquivos pode começar a carregar o arquivo para o local alternativo (ou marcar o arquivo como sujo e exigir upload).

### <a name="creating-new-document-provider-extensions"></a>Criando novas extensões de provedor de documento

A criação de novas extensões de provedor de documento está fora do escopo deste artigo introdutório. Essas informações são fornecidas aqui para mostrar que, com base nas extensões que um usuário carregou em seu dispositivo iOS, um aplicativo pode ter acesso a locais de armazenamento de documentos fora do local do iCloud fornecido pela Apple.

O desenvolvedor deve estar ciente desse fato ao usar o seletor de documento e trabalhar com documentos externos. Eles não devem pressupor que esses documentos estejam hospedados no iCloud.

Para obter mais informações sobre como criar uma extensão de provedor de armazenamento ou de seletor de documento, consulte o documento [introdução ao app Extensions](~/ios/platform/extensions.md) .

## <a name="migrating-to-icloud-drive"></a>Migrando para a unidade iCloud

No iOS 8, os usuários podem optar por continuar usando o sistema de documentos do iCloud existente usado no iOS 7 (e sistemas anteriores) ou podem optar por migrar os documentos existentes para o novo mecanismo do iCloud Drive.

Na Mac OS X Yosemite, a Apple não fornece a compatibilidade com versões anteriores para que todos os documentos precisem ser migrados para a unidade iCloud ou não sejam mais atualizados em dispositivos.

Depois que a conta de um usuário tiver sido migrada para a unidade iCloud, somente os dispositivos que usam a unidade iCloud poderão propagar as alterações nos documentos nesses dispositivos.

> [!IMPORTANT]
> Os desenvolvedores devem estar cientes de que os novos recursos abordados neste artigo só estarão disponíveis se a conta do usuário tiver sido migrada para a unidade iCloud. 

## <a name="summary"></a>Resumo

Este artigo abordou as alterações nas APIs do iCloud existentes necessárias para dar suporte à unidade iCloud e ao novo controlador de exibição do selecionador de documentos. Ele abordou a coordenação de arquivos e por que é importante ao trabalhar com documentos baseados em nuvem. Ele abordou a configuração necessária para habilitar documentos baseados em nuvem em um aplicativo Xamarin. iOS e ter uma visão introdutória de como trabalhar com documentos fora do contêiner de aplicativo de um aplicativo usando o controlador de exibição do seletor de documento.

Além disso, este artigo abordou brevemente as extensões do provedor de documentos e por que o desenvolvedor deve estar ciente delas ao escrever aplicativos que possam lidar com documentos baseados em nuvem.

## <a name="related-links"></a>Links relacionados

- [DocPicker (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-docpicker)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introdução às extensões de aplicativo](~/ios/platform/extensions.md)
