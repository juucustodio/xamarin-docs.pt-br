---
title: Seletor de documento no xamarin. IOS
description: Este documento descreve o seletor de documento do iOS e discute como usá-la no xamarin. IOS. Ele examina o iCloud, documentos, código de configuração comum, extensões de provedor de documento e muito mais.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: ac77bbc27784d1b836f4a1d26365acd65ac63ae7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111389"
---
# <a name="document-picker-in-xamarinios"></a>Seletor de documento no xamarin. IOS

O seletor de documento permite que os documentos sejam compartilhados entre aplicativos. Esses documentos podem ser armazenados no iCloud ou no diretório do aplicativo um diferente. Documentos são compartilhados por meio do conjunto de [extensões de provedor de documento](~/ios/platform/extensions.md) o usuário instalou em seu dispositivo. 

Devido à dificuldade de manter documentos sincronizados em todos os aplicativos e a nuvem, eles apresentam uma determinada quantidade de complexidade necessária.

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para concluir as etapas apresentadas neste artigo:

-  **Xcode 7 e iOS 8 ou mais recente** – do Apple Xcode 7 e iOS 8 ou APIs mais recentes precisarão ser instalado e configurado no computador do desenvolvedor.
-  **Visual Studio ou Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada.
-  **Dispositivo iOS** – um dispositivo iOS que executam o iOS 8 ou superior.

## <a name="changes-to-icloud"></a>Alterações com o iCloud

Para implementar os novos recursos do seletor de documento, as seguintes alterações foram feitas ao serviço do iCloud da Apple:

-  O Daemon do iCloud foi completamente reescrito usando o CloudKit.
-  O iCloud existente recursos tem sido renomeado iCloud Drive.
-  Foi adicionado suporte para o sistema operacional do Microsoft Windows com o iCloud.
-  Uma pasta iCloud foi adicionada no Finder do sistema operacional Mac.
-  dispositivos iOS podem acessar o conteúdo da pasta do iCloud do Mac OS.

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

## <a name="what-is-a-document"></a>O que é um documento?

Ao fazer referência a um documento no iCloud, ele é uma entidade única e autônoma e deve ser percebido como tal pelo usuário. Um usuário poderá modificar o documento ou compartilhá-lo com outros usuários (por email, por exemplo).

Há vários tipos de arquivos que o usuário imediatamente reconhecerá como documentos, como páginas, arquivos de apresentação ou números. No entanto, o iCloud não está limitado a esse conceito. Por exemplo, o estado de um jogo (como uma correspondência de xadrez) pode ser tratado como um documento e armazenado no iCloud. Esse arquivo podem ser passados entre os dispositivos do usuário e lhes permitem escolher um jogo de onde parou em um dispositivo diferente.

## <a name="dealing-with-documents"></a>Lidando com documentos

Antes de mergulharmos no código necessário para usar o seletor de documento com o Xamarin, este artigo vai cobrir as práticas recomendadas para trabalhar com documentos do iCloud e várias das modificações feitas com APIs existentes necessários para dar suporte ao seletor de documento.

### <a name="using-file-coordination"></a>Usando a coordenação de arquivo

Como um arquivo pode ser modificado de vários locais, coordenação deve ser usada para evitar a perda de dados.

 [![](document-picker-images/image1.png "Usando a coordenação de arquivo")](document-picker-images/image1.png#lightbox)

Vamos dar uma olhada na ilustração acima:

1.  Um dispositivo iOS usando a coordenação de arquivo cria um novo documento e salva-o para o pasta do iCloud.
2.  iCloud salva o arquivo modificado para a nuvem para distribuição a cada dispositivo.
3.  Um Mac conectado vê o arquivo modificado no pasta do iCloud e usa coordenação de arquivo anote as alterações no arquivo.
4.  Um dispositivo usando o arquivo de coordenação não faz uma alteração no arquivo e salva-o para o pasta do iCloud. Instantaneamente, essas alterações são replicadas para outros dispositivos.

Suponha que o original dispositivo iOS ou Mac estava editando o arquivo, agora, suas alterações são perdidas e substituídas pela versão do arquivo do dispositivo não coordenado. Para evitar a perda de dados, a coordenação de arquivo é uma necessidade ao trabalhar com documentos baseados em nuvem.

### <a name="using-uidocument"></a>Usando UIDocument

 `UIDocument` torna as coisas simples (ou `NSDocument` no macOS) por fazendo todo o trabalho pesado para o desenvolvedor. Ele fornece a coordenação de arquivo interno com filas de plano de fundo para impedir o bloqueio da interface do usuário do aplicativo.

 `UIDocument` expõe várias APIs de alto nível que facilitam o esforço de desenvolvimento de um aplicativo Xamarin para qualquer finalidade que o desenvolvedor requer.

O código a seguir cria uma subclasse de `UIDocument` para implementar um documento baseado em texto genérico que pode ser usado para armazenar e recuperar texto a partir do iCloud:

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

O `GenericTextDocument` classe apresentado a seguir será usada ao longo deste artigo ao trabalhar com o seletor de documento e os documentos externos em um aplicativo xamarin. IOS 8.

## <a name="asynchronous-file-coordination"></a>Coordenação de arquivo assíncrona

iOS 8 fornece vários novos recursos de coordenação de arquivo assíncrona por meio das APIs de coordenação de arquivo novo. Antes de iOS 8, todas as APIs de coordenação de arquivo existentes foram totalmente síncronas. Isso significava que o desenvolvedor era responsável por implementar seu próprio plano de fundo enfileiramento de mensagens para impedir que o arquivo coordenação bloqueando a interface do usuário do aplicativo.

O novo `NSFileAccessIntent` classe contém uma URL apontando para o arquivo e várias opções para controlar o tipo de coordenação necessárias. O código a seguir demonstra a movimentação de um arquivo de um local para outro usando as intenções:

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

A maneira de descobrir e lista de documentos é usando existente `NSMetadataQuery` APIs. Esta seção abordará os novos recursos adicionados ao `NSMetadataQuery` que tornar o trabalho com documentos ainda mais fácil do que antes.

### <a name="existing-behavior"></a>Comportamento de existente

Antes do iOS 8, `NSMetadataQuery` era lento para alterações de arquivo local de retirada, como: exclui, cria e renomeia.

 [![](document-picker-images/image2.png "Visão geral de alterações de arquivo local NSMetadataQuery")](document-picker-images/image2.png#lightbox)

No diagrama acima:

1.  Para arquivos que já existem no contêiner de aplicativo, `NSMetadataQuery` existente tiver `NSMetadata` registros criado previamente e em spool para que fiquem disponíveis imediatamente para o aplicativo.
1.  O aplicativo cria um novo arquivo no contêiner do aplicativo.
1.  Há um atraso antes `NSMetadataQuery` vê a modificação para o contêiner de aplicativo e cria o necessária `NSMetadata` registro.


Por causa o atraso na criação do `NSMetadata` registros, o aplicativo tinha que ter duas fontes de dados abertas: uma para alterações de arquivo local e outra para a nuvem com base em alterações.

### <a name="stitching"></a>A união

No iOS 8, `NSMetadataQuery` é mais fácil de usar diretamente com um novo recurso chamado costura:

 [![](document-picker-images/image3.png "NSMetadataQuery com um novo recurso chamado costura")](document-picker-images/image3.png#lightbox)

Usando costura no diagrama acima:

1.  Como antes, para arquivos que já existem no contêiner de aplicativo, `NSMetadataQuery` existente tiver `NSMetadata` registros previamente criados e colocados no spool.
1.  O aplicativo cria um novo arquivo no contêiner de aplicativo usando o arquivo de coordenação.
1.  Um gancho no contêiner do aplicativo vê a modificação e chama `NSMetadataQuery` para criar os necessários `NSMetadata` registro.
1.  O `NSMetadata` registro é criado diretamente após o arquivo e é disponibilizado para o aplicativo.


Usando costura o aplicativo não tem mais para abrir uma fonte de dados para monitorar o local e as alterações de arquivo baseado em nuvem. Agora o aplicativo pode depender de `NSMetadataQuery` diretamente.

> [!IMPORTANT]
> Costura só funciona se o aplicativo está usando o arquivo coordenação conforme apresentado na seção acima. Se a coordenação de arquivo não está sendo usada, as APIs padrão o comportamento do iOS 8 pré existente.




### <a name="new-ios-8-metadata-features"></a>Novos recursos de metadados do iOS 8

Os seguintes recursos novos foram adicionados para `NSMetadataQuery` no iOS 8:

-   `NSMetatadataQuery` Agora é possível listar os documentos não locais armazenados na nuvem.
-  Novas APIs foram adicionadas para acessar informações de metadados sobre os documentos baseados em nuvem. 
-  Há uma nova `NSUrl_PromisedItems` API será para acessar os atributos de arquivo dos arquivos que podem ou não ter seu conteúdo disponível localmente.
-  Use o `GetPromisedItemResourceValue` método para obter informações sobre um determinado arquivo ou use o `GetPromisedItemResourceValues` método para obter informações sobre mais de um arquivo por vez.


Dois sinalizadores de coordenação de arquivo novos foram adicionados para lidar com os metadados:

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Com os sinalizadores acima, o conteúdo do arquivo de documento não precisa estar disponível localmente para que eles possam ser usados.

O segmento de código a seguir mostra como usar `NSMetadataQuery` para consultar a existência de um arquivo específico e criar o arquivo se ele não existir:

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
            new Selector("queryDidFinishGathering:"),           NSMetadataQuery.DidFinishGatheringNotification,
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

### <a name="document-thumbnails"></a>Miniaturas de documentos

Apple parece que a melhor experiência de usuário ao listar os documentos para um aplicativo é usar as visualizações. Isso fornece o contexto de usuários finais, para que eles possam identificar rapidamente o documento que eles desejam trabalhar com.

Antes do iOS 8, mostrar as visualizações de documento necessária uma implementação personalizada. Novo no iOS 8 são atributos de sistema de arquivos que permitem que o desenvolvedor trabalhar rapidamente com miniaturas de documentos.

#### <a name="retrieving-document-thumbnails"></a>Recuperar miniaturas de documentos 

Chamando o `GetPromisedItemResourceValue` ou `GetPromisedItemResourceValues` métodos `NSUrl_PromisedItems` API, um `NSUrlThumbnailDictionary`, será retornado. A única chave atualmente neste dicionário é o `NSThumbnial1024X1024SizeKey` e sua correspondência `UIImage`.

#### <a name="saving-document-thumbnails"></a>Salvando miniaturas de documentos

A maneira mais fácil para salvar uma miniatura é usando `UIDocument`. Chamando o `GetFileAttributesToWrite` método da `UIDocument` e definir a miniatura, ele será automaticamente salvo quando o arquivo de documento está. O Daemon do iCloud verá essa alteração e propagá-los com o iCloud. No Mac OS X, as miniaturas são geradas automaticamente para o desenvolvedor pelo plug-in ser rápido.

Com os fundamentos de trabalhar com documentos do iCloud com base no local, juntamente com as modificações a API existente, estamos prontos para implementar o controlador de exibição de seletor de documento em um Xamarin iOS 8 aplicativo móvel.


## <a name="enabling-icloud-in-xamarin"></a>Habilitar iCloud no Xamarin

Antes do seletor de documento pode ser usado em um aplicativo xamarin. IOS, suporte a iCloud precisa ser habilitado em seu aplicativo e por meio da Apple. 

A seguintes etapas passo a passo o processo de provisionamento para iCloud.

1. Crie um contêiner do iCloud.
2. Crie uma ID do aplicativo que contém o serviço de aplicativo do iCloud.
3. Criar um perfil de provisionamento que inclua esse ID. App

O [trabalhando com capacidades](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) guia explica as duas primeiras etapas. Para criar um perfil de provisionamento, siga as etapas a [perfil de provisionamento](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) guia.



A seguintes etapas passo a passo o processo de configuração do seu aplicativo para o iCloud:

Faça o seguinte:

1.  Abra o projeto no Visual Studio para Mac ou Visual Studio.
2.  No **Gerenciador de soluções**, clique com botão direito no projeto e selecione opções.
3.  No, selecione a caixa de diálogo Opções **aplicativo do iOS**, certifique-se de que o **identificador de pacote** corresponde ao que foi definido no **ID do aplicativo** criado acima para o aplicativo. 
4.  Selecione **assinatura do pacote iOS**, selecione o **identidade de desenvolvedor** e o **perfil de provisionamento** criado acima.
5.  Clique o **Okey** botão para salvar as alterações e fechar a caixa de diálogo.
6.  Clique duas vezes em `Entitlements.plist` no **Gerenciador de soluções** para abri-lo no editor.

    > [!IMPORTANT]
    > No Visual Studio talvez você precise abrir o editor de direitos, clicando duas vezes nele, selecionando **abrir com...** e selecionando o Editor de lista de propriedades

7.  Verifique **habilitar iCloud** , **documentos do iCloud** , **armazenamento chave-valor** e **CloudKit** .
8.  Verifique se o **contêiner** existe para o aplicativo (conforme criado acima). Exemplo: `iCloud.com.your-company.AppName`
9.  Salve as alterações no arquivo.

Para obter mais informações sobre direitos, consulte o [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

Com a configuração acima em vigor, o aplicativo agora pode usar documentos baseados em nuvem e o novo controlador de exibição de seletor de documento.

## <a name="common-setup-code"></a>Código de configuração comum

Antes de começar com o controlador de exibição de seletor de documento, há alguns códigos de padrão de instalação necessários. Comece modificando o aplicativo `AppDelegate.cs` de arquivo e torná-lo semelhante ao seguinte:

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
> O código acima inclui o código da seção de documentos de listagem e descobrindo acima. Ela é apresentada aqui em sua totalidade, como seria em um aplicativo real. Para simplificar, este exemplo funciona com um único arquivo embutido em código (`test.txt`) apenas.

O código acima expõe vários atalhos de unidade do iCloud para torná-las mais fáceis de trabalhar no restante do aplicativo.

Em seguida, adicione o seguinte código para qualquer contêiner de modo de exibição que será usando o seletor de documento ou trabalhar com documentos baseados em nuvem ou o modo de exibição:

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

Isso adiciona um atalho para acessar o `AppDelegate` e acessar os atalhos do iCloud criados acima.

Com esse código, vamos dar uma olhada na implementação do controlador de exibição de seletor de documento em um aplicativo Xamarin iOS 8.

## <a name="using-the-document-picker-view-controller"></a>Usando o controlador de exibição de seletor de documento

Antes do iOS 8, era muito difícil acessar documentos de outro aplicativo, porque não havia nenhuma maneira de descobrir documentos fora do aplicativo de dentro do aplicativo.

### <a name="existing-behavior"></a>Comportamento de existente

 [![](document-picker-images/image31.png "Visão geral do comportamento existente")](document-picker-images/image31.png#lightbox)

Vamos dar uma olhada em como acessar um documento externo antes do iOS 8:

1.  Primeiro, o usuário teria que abrir o aplicativo que originariamente criou o documento.
1.  O documento está selecionado e o `UIDocumentInteractionController` é usado para enviar o documento para o novo aplicativo.
1.  Por fim, uma cópia do documento original é colocada no contêiner do novo aplicativo.


A partir daí, o documento está disponível para o segundo aplicativo abrir e editar.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Descoberta de documentos fora de contêiner do aplicativo

No iOS 8, um aplicativo é capaz de acessar documentos fora de seu próprio contêiner de aplicativo com facilidade:

 [![](document-picker-images/image32.png "Descoberta de documentos fora de contêiner do aplicativo")](document-picker-images/image32.png#lightbox)

Usando o novo seletor de documentos do iCloud ( `UIDocumentPickerViewController`), um aplicativo iOS diretamente pode descobrir e acessar fora de seu contêiner de aplicativo. O `UIDocumentPickerViewController` fornece um mecanismo para o usuário conceder acesso a e editar os documentos de descoberta por meio de permissões.

Um aplicativo deverá participar para que seus documentos exibidos no seletor de documentos do iCloud e estar disponível para outros aplicativos descobrir e trabalhar com eles. Para ter um aplicativo Xamarin iOS 8 compartilhar seu contêiner de aplicativo, editá-lo `Info.plist` arquivo em um editor de texto padrão e adicione as duas linhas a seguir na parte inferior do dicionário (entre o `<dict>...</dict>` marcas):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

O `UIDocumentPickerViewController` fornece uma ótima nova interface do usuário que permite que o usuário escolher documentos. Para exibir o controlador de exibição de seletor de documento em um aplicativo Xamarin iOS 8, faça o seguinte:

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
> O desenvolvedor deve chamar o `StartAccessingSecurityScopedResource` método da `NSUrl` antes de um documento externo pode ser acessado. O `StopAccessingSecurityScopedResource` método deve ser chamado para liberar o bloqueio de segurança assim que o documento foi carregado.

### <a name="sample-output"></a>Saída de Exemplo

Aqui está um exemplo de como o código acima exibirá um seletor de documento quando executado em um dispositivo iPhone:

1.  O usuário inicia o aplicativo e a interface principal é exibida:   
 
    [![](document-picker-images/image33.png "A interface principal é exibida")](document-picker-images/image33.png#lightbox)
1.  A usuário toca a **ação** botão na parte superior da tela e será solicitado a selecionar um **documento provedor** da lista de provedores disponíveis:   
 
    [![](document-picker-images/image34.png "Selecione um provedor de documento na lista de provedores disponíveis")](document-picker-images/image34.png#lightbox)
1.  O **controlador de exibição de seletor de documento** é exibido para o selecionado **documento provedor**:   
 
    [![](document-picker-images/image35.png "O controlador de exibição de seletor de documento é exibido")](document-picker-images/image35.png#lightbox)
1.  O usuário toca em um **pasta de documentos** para exibir seu conteúdo:   
 
    [![](document-picker-images/image36.png "O conteúdo da pasta de documentos")](document-picker-images/image36.png#lightbox)
1.  O usuário seleciona um **documento** e o **seletor de documento** está fechado.
1.  A interface principal é exibida novamente, o **documento** é carregado do contêiner externo e seu conteúdo exibido.


A exibição real do controlador de exibição de seletor de documento depende dos provedores de documento que o usuário instalou no dispositivo e qual modo de seletor de documento tiver sido implementar. O exemplo acima é usando o modo de abertura, os outros tipos de modo que serão discutidos em detalhes abaixo.

## <a name="managing-external-documents"></a>Gerenciando documentos externos

Conforme discutido acima, antes do iOS 8, um aplicativo só pode acessar documentos que faziam parte de seu contêiner de aplicativo. No iOS 8 um aplicativo pode acessar documentos de fontes externas:

 [![](document-picker-images/image37.png "Gerenciando a visão geral de documentos externos")](document-picker-images/image37.png#lightbox)

Quando o usuário seleciona um documento de uma fonte externa, um documento de referência é gravado para o contêiner de aplicativo que aponta para o documento original.

Para ajudar a essa nova capacidade aos aplicativos existentes para adicionar, vários recursos novos foram adicionados para o `NSMetadataQuery` API. Normalmente, um aplicativo usa o escopo de documento onipresente para listar os documentos que residem dentro de seu contêiner de aplicativo. Usando este escopo, somente os documentos dentro do contêiner de aplicativo continuará a ser exibido.

Usar o novo escopo de documento externa onipresente retornará documentos ao vivo fora do contêiner de aplicativo e retornam os metadados para eles. O `NSMetadataItemUrlKey` apontará para a URL onde o documento está realmente localizado.

Às vezes, um aplicativo não quer trabalhar com os documentos que está sendo apontados pela referência th. Em vez disso, o aplicativo deseja trabalhar diretamente com o documento de referência. Por exemplo, o aplicativo talvez queira exibir o documento na pasta do aplicativo na interface do usuário, ou para permitir que o usuário mover as referências dentro de uma pasta.

No iOS 8, um novo `NSMetadataItemUrlInLocalContainerKey` foi fornecido para acessar o documento de referência diretamente. Essa chave aponta para a referência real para o documento externa em um contêiner de aplicativo.

O `NSMetadataUbiquitousItemIsExternalDocumentKey` é usado para testar se um documento é externo ao contêiner do aplicativo. O `NSMetadataUbiquitousItemContainerDisplayNameKey` é usado para acessar o nome do contêiner que é que hospeda a cópia original de um documento externo.

### <a name="why-document-references-are-required"></a>Por que são necessárias referências de documento

O principal motivo que se iOS 8 usa referências para acessar documentos externos é segurança. Nenhum aplicativo recebe acesso a contêiner do qualquer outro aplicativo. Somente o seletor de documento pode fazer isso, porque está fora do processo em execução e tem amplo acesso de sistema.

A única maneira de obter a um documento fora do contêiner de aplicativo está usando o seletor de documento e se a URL retornada pelo seletor de tem o escopo de segurança. A URL no escopo de segurança contém informações suficientes para o documento, juntamente com os direitos no escopo necessário para conceder acesso de aplicativo para o documento selecionado.

É importante observar que, se a URL no escopo de segurança foi serializada em uma cadeia de caracteres e, em seguida, desserializada, as informações de segurança seriam perdidas e o arquivo seria inacessível da URL. O recurso de documento de referência fornece um mecanismo para voltar para os arquivos apontados por essas URLs.

Portanto, se o aplicativo adquire um `NSUrl` em um dos documentos de referência, ele já tem o escopo de segurança anexado e pode ser usado para acessar o arquivo. Por esse motivo, é altamente recomendável que o desenvolvedor usar `UIDocument` porque ele manipula todas essas informações e processos para eles.

### <a name="using-bookmarks"></a>Usando indicadores

Nem sempre é viável para enumerar os documentos de um aplicativo para obter um documento específico, por exemplo, ao fazer a restauração de estado. iOS 8 fornece um mecanismo para criar os indicadores que se destinam diretamente a um determinado documento.

O código a seguir criará um indicador de uma `UIDocument`do `FileUrl` propriedade:

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

A API existente do indicador é usada para criar um indicador em relação a um existente `NSUrl` que podem ser salvos e carregados para fornecer acesso direto a um arquivo externo. O código a seguir irá restaurar um indicador que foi criado acima:

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

## <a name="open-vs-import-mode-and-the-document-picker"></a>Abra o vs. O seletor de documento e modo de importação

O controlador de exibição de seletor de documento apresenta dois diferentes modos de operação:

1.  **Abra o modo de** – nesse modo, quando o usuário seleciona e documento externo, o seletor de documento criará um indicador no escopo de segurança no contêiner do aplicativo.   
 
    [![](document-picker-images/image37.png "Segurança no escopo do indicador no contêiner de aplicativo")](document-picker-images/image37.png#lightbox)
1.  **Modo de importação** – nesse modo, quando o usuário seleciona e documento externo, o seletor de documento não criar um indicador, mas em vez disso, copiar o arquivo para um local temporário e fornecer acesso de aplicativo para o documento neste local:   
 
    [![](document-picker-images/image38.png "O seletor de documento copiará o arquivo em um local temporário e fornecem ao aplicativo acesso ao documento neste local")](document-picker-images/image38.png#lightbox)   
 Depois que o aplicativo é encerrado por algum motivo, o local temporário é esvaziado e o arquivo removido. Se o aplicativo precisa manter o acesso ao arquivo, ele deve fazer uma cópia e colocá-lo em seu contêiner de aplicativo.


Abra o modo é útil quando o aplicativo quiser colaborar com outro aplicativo e compartilhe as alterações feitas ao documento com esse aplicativo. O modo de importação é usado quando o aplicativo deseja compartilhar suas modificações a um documento com outros aplicativos.

## <a name="making-a-document-external"></a>Tornando um documento externo

Conforme observado acima, um aplicativo iOS 8 não tem acesso aos contêineres de fora de seu próprio contêiner de aplicativo. O aplicativo pode gravar seu próprio contêiner localmente ou em um local temporário e usar um modo de documento especial para mover o documento resultante fora do contêiner de aplicativo para um usuário escolhido do local.

Para mover um documento em um local externo, faça o seguinte:

1.  Primeiro, crie um novo documento em um local temporário ou local.
1.  Criar um `NSUrl` que aponta para o novo documento.
1.  Abra um novo controlador de exibição de seletor de documento e passá-lo a `NSUrl` com o modo de `MoveToService` . 
1.  Depois que o usuário escolhe um novo local, o documento será movido de seu local atual para o novo local.
1.  Um documento de referência será gravado para o contêiner de aplicativo do aplicativo para que o arquivo ainda pode ser acessado pelo aplicativo de criação.


O código a seguir pode ser usado para mover um documento em um local externo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

O documento de referência retornada pelo processo acima é exatamente o mesmo que um criado pelo modo de abertura do seletor de documento. No entanto, há vezes em que o aplicativo talvez queira mover um documento sem manter uma referência a ele.

Para mover um documento sem gerar uma referência, use o `ExportToService` modo. Exemplo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Ao usar o `ExportToService` modo, o documento é copiado para o contêiner externo e a cópia existente é deixada no local original.

## <a name="document-provider-extensions"></a>Extensões de provedor de documento

Com o iOS 8, Apple quer o usuário final para poder acessar qualquer um dos seus documentos baseados em nuvem, não importa onde eles realmente existem. Para atingir essa meta, o iOS 8 fornece um novo mecanismo de extensão do provedor de documento.

### <a name="what-is-a-document-provider-extension"></a>O que é uma extensão de provedor de documento?

Em poucas palavras, uma extensão de provedor do documento é uma maneira para um desenvolvedor ou de terceiros, para fornecer um documento alternativa no armazenamento de aplicativos que pode ser acessado em exatamente da mesma maneira como o local de armazenamento existentes do iCloud.

O usuário pode selecionar um desses locais de armazenamento alternativo de seletor de documento e eles podem usar os exato mesmo modos de acesso (Abrir, importar, mover ou exportação) para trabalhar com arquivos nesse local.

Isso é implementado usando duas extensões diferentes:

-  **Extensão de seletor de documento** – fornece um `UIViewController` subclasse que fornece uma interface gráfica do usuário escolher um documento de um local de armazenamento alternativo. Essa subclasse será exibido como parte do controlador de exibição de seletor de documento.
-  **Fornecer extensão de arquivo** – é uma extensão sem interface do usuário que lida com a prestação, na verdade, o conteúdo de arquivos. Essas extensões são fornecidas por meio da coordenação de arquivo ( `NSFileCoordinator` ). Esse é outro caso importante onde o arquivo coordenação é necessária.


O diagrama a seguir mostra o fluxo de dados típico, ao trabalhar com extensões de provedor de documento:

 [![](document-picker-images/image39.png "Este diagrama mostra o fluxo de dados típico, ao trabalhar com extensões de provedor de documento")](document-picker-images/image39.png#lightbox)

Ocorre o seguinte processo:

1.  O aplicativo apresenta um controlador de seletor de documento para permitir que o usuário selecione um arquivo para trabalhar com.
1.  O usuário seleciona um local de arquivo alternativo e personalizado `UIViewController` extensão é chamada para exibir a interface do usuário.
1.  O usuário seleciona um arquivo nesse local e a URL é passada de volta para o seletor de documento.
1.  O seletor de documento seleciona a URL do arquivo e retorna para o aplicativo para o usuário trabalhar em.
1.  A URL é passada para o arquivo coordenador para retornar o conteúdo de arquivos para o aplicativo.
1.  O arquivo coordenador chama a extensão de provedor de arquivo personalizado para recuperar o arquivo.
1.  O conteúdo do arquivo é retornado para o coordenador de arquivo.
1.  O conteúdo do arquivo é retornado ao aplicativo.


### <a name="security-and-bookmarks"></a>Segurança e indicadores

Esta seção será dar uma olhada rápida em como segurança e o arquivo persistente acessam por meio do works indicadores com extensões de provedor do documento. Ao contrário do provedor de documento, que salva automaticamente a segurança e indicadores para o contêiner de aplicativo, iCloud extensões de provedor de documento não porque eles não fazem parte do sistema de referência de documento.

Por exemplo: em uma configuração empresarial que fornece seu próprio repositório de dados seguro em toda a empresa, os administradores não querem informações confidenciais corporativas acessados ou processado pelo iCloud público servidores. Portanto, o sistema de referência interna do documento não pode ser usado.

O sistema de indicador ainda pode ser usado e é responsabilidade da extensão do provedor de arquivo para processar corretamente um URL nos favoritos e retornar o conteúdo do documento apontado por ele.

Para fins de segurança, o iOS 8 tem uma camada de isolamento que persiste as informações sobre os quais o aplicativo tem acesso a quais identificador dentro do qual provedor de arquivo. Deve-se observar que todos os acessos de arquivo é controlado por essa camada de isolamento.

O diagrama a seguir mostra o fluxo de dados ao trabalhar com indicadores e uma extensão de provedor de documento:

 [![](document-picker-images/image40.png "Este diagrama mostra o fluxo de dados ao trabalhar com indicadores e uma extensão de provedor de documento")](document-picker-images/image40.png#lightbox)

Ocorre o seguinte processo:

1.  O aplicativo está prestes a entrar no plano de fundo e precisa para manter seu estado. Ele chama `NSUrl` para criar um indicador em um arquivo no armazenamento alternativo.
1.  `NSUrl` chama a extensão do provedor de arquivo para obter uma URL persistente para o documento. 
1.  A extensão do provedor de arquivo retorna a URL como uma cadeia de caracteres para o `NSUrl` .
1.  O `NSUrl` empacota a URL em um indicador e retorna para o aplicativo.
1.  Quando o aplicativo é ativado de estar em segundo plano e precisa restaurar o estado, ele passa o indicador a ser `NSUrl` .
1.  `NSUrl` chama a extensão do provedor de arquivo com a URL do arquivo.
1.  O provedor de extensão de arquivo acessa o arquivo e retorna o local do arquivo a ser `NSUrl` .
1.  O local do arquivo é fornecido com informações de segurança e retornado ao aplicativo.


A partir daqui, o aplicativo pode acessar o arquivo e trabalhar com eles como de costume.

### <a name="writing-files"></a>Gravando arquivos

Esta seção será dar uma olhada rápida em como gravar arquivos em um local alternativo com uma extensão do provedor de documento funciona. O aplicativo iOS usará coordenação de arquivo para salvar as informações para o disco dentro do contêiner do aplicativo. Logo após o arquivo foi gravado com êxito, a extensão do provedor de arquivo será notificada da alteração.

Neste ponto, a extensão de arquivo do provedor Inicie carregando o arquivo para o local alternativo (ou marcar o arquivo como suja e exigindo upload).

### <a name="creating-new-document-provider-extensions"></a>Criação de novas extensões de provedor de documento

Criação de novas extensões de provedor do documento está fora do escopo deste artigo introdutório. Essas informações são fornecidas aqui para mostrar que, com base nas extensões de que um usuário tiver carregado em seu dispositivo iOS, um aplicativo pode ter acesso para os locais de armazenamento de documento fora da Apple fornecidos o local do iCloud.

O desenvolvedor deve estar ciente desse fato ao usar o seletor de documento e trabalhar com documentos externos. Eles não devem presumir a esses documentos são hospedados no iCloud.

Para obter mais informações sobre como criar um provedor de armazenamento ou a extensão de seletor de documento, consulte o [Introdução às extensões de aplicativo](~/ios/platform/extensions.md) documento.

## <a name="migrating-to-icloud-drive"></a>Migrando para o iCloud Drive

No iOS 8, os usuários podem optar por continuar usando o iCloud existente sistema documentos usados no iOS 7 (e em sistemas anteriores) ou eles podem optar por migrar documentos existentes para o novo mecanismo de unidade do iCloud.

No Mac OS X Yosemite, Apple não fornece o com versões anteriores compatibilidade para todos os documentos devem ser migrados para o iCloud Drive ou eles não poderão mais ser atualizado em todos os dispositivos.

Depois que uma conta de usuário tiver sido migrada para o iCloud Drive, apenas os dispositivos usando o iCloud Drive será capazes de propagar alterações nos documentos entre esses dispositivos.

> [!IMPORTANT]
> Os desenvolvedores devem estar cientes de que os novos recursos abordados neste artigo só estarão disponíveis se a conta do usuário tiver sido migrada para o iCloud Drive. 

## <a name="summary"></a>Resumo

Este artigo abordou as alterações com o iCloud existente, as APIs necessárias para dar suporte a iCloud Drive e o novo controlador de exibição de seletor de documento. Ele abordou a coordenação de arquivo e por que é importante ao trabalhar com documentos baseados em nuvem. Ele tem abordou a configuração necessária para habilitar documentos baseados em nuvem em um aplicativo xamarin. IOS e recebe um exame introdutório trabalhar com documentos fora do contêiner de aplicativo do aplicativo usando o controlador de exibição de seletor de documento.

Além disso, este artigo abordou brevemente as extensões de provedor de documento e por que o desenvolvedor deve estar ciente ao escrever aplicativos que podem manipular documentos baseados em nuvem.

## <a name="related-links"></a>Links relacionados

- [DocPicker (amostra)](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introdução às extensões de aplicativo](~/ios/platform/extensions.md)
