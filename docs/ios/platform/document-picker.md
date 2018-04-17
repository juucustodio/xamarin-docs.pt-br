---
title: Seletor de documento
description: O controlador de exibição de documento seletor concede aos usuários acesso aos arquivos fora da área de segurança do aplicativo. É um mecanismo simples para compartilhamento de documentos entre aplicativos. Ele também permite que os fluxos de trabalho mais complexos, porque os usuários possam editar um único documento com vários aplicativos. Este artigo fornece uma introdução ao usar o seletor de documento em um aplicativo xamarin e as alterações nos documentos iCloud necessárias para dar suporte a ele.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: d9b98611c7d269e590ce6fe2ce0270ef71dacf1e
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="document-picker"></a>Seletor de documento

_O controlador de exibição de documento seletor concede aos usuários acesso aos arquivos fora da área de segurança do aplicativo. É um mecanismo simples para compartilhamento de documentos entre aplicativos. Ele também permite que os fluxos de trabalho mais complexos, porque os usuários possam editar um único documento com vários aplicativos. Este artigo fornece uma introdução ao usar o seletor de documento em um aplicativo xamarin e as alterações nos documentos iCloud necessárias para dar suporte a ele._

O seletor de documento permite que documentos sejam compartilhados entre aplicativos. Esses documentos podem ser armazenados no iCloud ou no diretório do aplicativo um diferente. Documentos compartilhados por meio do conjunto de [documento provedor extensões](~/ios/platform/extensions.md) o usuário instalou em seu dispositivo. 

Devido a dificuldade de manter documentos sincronizados em todos os aplicativos e a nuvem, eles apresentam uma determinada quantidade de complexidade necessária.

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para concluir as etapas apresentadas neste artigo:

-  **Xcode 7 e iOS 8 ou mais recente** – da Apple Xcode 7 e iOS 8 ou mais recente APIs precisa ser instalado e configurado no computador do desenvolvedor.
-  **O Visual Studio ou o Visual Studio para Mac** – a versão mais recente do Visual Studio para Mac deve ser instalada.
-  **Dispositivo iOS** – um dispositivo iOS com iOS 8 ou superior.

## <a name="changes-to-icloud"></a>Alterações no icloud

Para implementar os novos recursos do seletor de documento, as seguintes alterações foram feitas para iCloud da Apple serviço:

-  O Daemon do iCloud foi completamente reescrito usando CloudKit.
-  O iCloud existente recursos foram renomeados iCloud unidade.
-  Foi adicionado suporte para o sistema operacional Microsoft Windows com o iCloud.
-  Uma pasta iCloud foi adicionada no localizador de sistema operacional Mac.
-  dispositivos iOS podem acessar o conteúdo da pasta iCloud do Mac OS.

> [!IMPORTANT]
> Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com a norma de proteção de dados geral (GDPR) da União Europeia.

## <a name="what-is-a-document"></a>O que é um documento?

Ao fazer referência a um documento no iCloud, ele é uma entidade única, autônoma e deve ser percebido como tal pelo usuário. Um usuário poderá modificar o documento ou compartilhá-lo com outros usuários (usando email, por exemplo).

Há vários tipos de arquivos que o usuário imediatamente reconhecerão como documentos, como páginas, números ou apresentação de arquivos. No entanto, iCloud não é limitado a esse conceito. Por exemplo, o estado do jogo (como uma correspondência de xadrez) pode tratado como um documento e armazenado no iCloud. Esse arquivo pode ser transmitidos entre dispositivos do usuário e permiti-las pegar um jogo de onde parou em um dispositivo diferente.

## <a name="dealing-with-documents"></a>Lidando com documentos

Antes de mergulhar o código necessário para usar o seletor de documento com o Xamarin, este artigo vai cobrem as práticas recomendadas para trabalhar com documentos iCloud e várias das modificações feitas para APIs existentes necessários para dar suporte ao seletor de documento.

### <a name="using-file-coordination"></a>Usando a coordenação de arquivo

Como um arquivo pode ser modificado em vários locais, a coordenação deve ser usada para evitar a perda de dados.

 [![](document-picker-images/image1.png "Usando a coordenação de arquivo")](document-picker-images/image1.png#lightbox)

Vamos dar uma olhada na ilustração acima:

1.  Um dispositivo iOS usando a coordenação de arquivo cria um novo documento e salva-o para o pasta no iCloud.
2.  iCloud salva o arquivo modificado na nuvem para distribuição para cada dispositivo.
3.  Um Mac anexado vê o arquivo modificado no pasta iCloud e usa a coordenação de arquivo para copiar as alterações para o arquivo.
4.  Um dispositivo usando o arquivo coordenação não faz uma alteração no arquivo e salva-o para o pasta no iCloud. Instantaneamente, essas alterações são replicadas para outros dispositivos.

Suponha que o original dispositivo iOS ou Mac estava editando o arquivo, agora as suas alterações sejam perdidas e substituídas com a versão do arquivo do dispositivo não coordenado. Para evitar a perda de dados, a coordenação de arquivo é fundamental ao trabalhar com documentos baseados em nuvem.

### <a name="using-uidocument"></a>Usando UIDocument

 `UIDocument` torna as coisas simples (ou `NSDocument` em macOS) ao fazer todo o trabalho pesado para o desenvolvedor. Ele fornece criados no arquivo coordenação com filas de plano de fundo para evitar o bloqueio de interface de usuário do aplicativo.

 `UIDocument` expõe várias APIs de alto nível que facilitam o esforço de desenvolvimento de um aplicativo Xamarin para qualquer finalidade o desenvolvedor requer.

O código a seguir cria uma subclasse de `UIDocument` para implementar um documento baseado em texto genérico que pode ser usado para armazenar e recuperar o texto do iCloud:

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

O `GenericTextDocument` classe apresentado a seguir será usada neste artigo ao trabalhar com o seletor de documento e documentos externos em um aplicativo xamarin 8.

## <a name="asynchronous-file-coordination"></a>Coordenação de arquivo assíncrono

iOS 8 fornece vários novos recursos de coordenação de arquivo assíncrona por meio das APIs de coordenação de arquivo novo. Antes de iOS 8, todas as APIs de coordenação de arquivo existentes foram totalmente síncronas. Isso significa que o desenvolvedor era responsável por implementar seu próprio plano de fundo enfileiramento de mensagens para impedir que o arquivo coordenação bloqueando a interface de usuário do aplicativo.

O novo `NSFileAccessIntent` classe contém uma URL apontando para o arquivo e várias opções para controlar o tipo de coordenação necessário. O código a seguir demonstra a movimentação de um arquivo de um local para outro usando propósitos:

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

## <a name="discovering-and-listing-documents"></a>Descobrindo e Listar documentos

É a maneira de descobrir e a lista de documentos usando existente `NSMetadataQuery` APIs. Esta seção aborda os novos recursos adicionados ao `NSMetadataQuery` que facilitam o trabalho com documentos até mesmo de antes.

### <a name="existing-behavior"></a>Comportamento de existente

Antes do iOS 8, `NSMetadataQuery` foi lenta para alterações de arquivo local de retirada, como: exclui, cria e renomeia.

 [![](document-picker-images/image2.png "Visão geral de alterações de arquivo local NSMetadataQuery")](document-picker-images/image2.png#lightbox)

No diagrama acima:

1.  Para arquivos que já existem no contêiner do aplicativo, `NSMetadataQuery` tem existente `NSMetadata` registros criado previamente e em spool para que fiquem disponíveis imediatamente para o aplicativo.
1.  O aplicativo cria um novo arquivo no contêiner do aplicativo.
1.  Há um atraso antes de `NSMetadataQuery` considera a modificação para o contêiner do aplicativo e cria necessários `NSMetadata` registro.


Devido o atraso na criação do `NSMetadata` registro, o aplicativo precisava ter duas fontes de dados abertas: uma para alterações de arquivo local e para a nuvem com base em alterações.

### <a name="stitching"></a>Junção

No iOS 8, `NSMetadataQuery` é mais fácil usar diretamente com um novo recurso chamado de junção:

 [![](document-picker-images/image3.png "NSMetadataQuery com um novo recurso chamado junção")](document-picker-images/image3.png#lightbox)

Usando a junção no diagrama acima:

1.  Como antes, para arquivos que já existem no contêiner do aplicativo, `NSMetadataQuery` tem existente `NSMetadata` registros criado previamente e colocados no spool.
1.  O aplicativo cria um novo arquivo no contêiner do aplicativo usando a coordenação de arquivo.
1.  Um gancho no contêiner do aplicativo vê a modificação e chama `NSMetadataQuery` criar necessários `NSMetadata` registro.
1.  O `NSMetadata` registro é criado diretamente após o arquivo e ficam disponível para o aplicativo.


Usando a junção o aplicativo não tem mais para abrir uma fonte de dados para monitorar o local e as alterações de arquivo baseada em nuvem. Agora que o aplicativo pode depender `NSMetadataQuery` diretamente.

> [!IMPORTANT]
> Junção só funciona se o aplicativo está usando a coordenação de arquivo como apresentado na seção acima. Se a coordenação de arquivo não está sendo usada, as APIs padrão o comportamento de iOS 8 pre existente.




### <a name="new-ios-8-metadata-features"></a>Novos recursos de metadados do iOS 8

Os seguintes novos recursos foram adicionados ao `NSMetadataQuery` no iOS 8:

-   `NSMetatadataQuery` Agora é possível listar local não documentos armazenados na nuvem.
-  Foram adicionadas novas APIs para acessar as informações de metadados nos documentos baseados em nuvem. 
-  Há uma nova `NSUrl_PromisedItems` API para acessar os atributos de arquivo dos arquivos que podem ou não ter seu conteúdo disponível localmente.
-  Use o `GetPromisedItemResourceValue` método para obter informações sobre um determinado arquivo ou use o `GetPromisedItemResourceValues` método para obter informações em mais de um arquivo por vez.


Dois sinalizadores de coordenação de arquivo novos foram adicionados para lidar com metadados:

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Com os sinalizadores acima, o conteúdo do arquivo de documento não precisa estar disponível localmente para utilizá-los.

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

Apple acredita que a melhor experiência de usuário ao listar documentos para um aplicativo é usar visualizações. Isso fornece o contexto de usuários finais, para que podem identificar rapidamente o documento que deseja trabalhar.

Antes do iOS 8, mostrar as visualizações de documento necessária uma implementação personalizada. Novo para o iOS 8 são atributos de sistema de arquivos que permitem que o desenvolvedor trabalhar rapidamente com miniaturas de documentos.

#### <a name="retrieving-document-thumbnails"></a>Recuperando miniaturas de documentos 

Chamando o `GetPromisedItemResourceValue` ou `GetPromisedItemResourceValues` métodos, `NSUrl_PromisedItems` API, um `NSUrlThumbnailDictionary`, será retornado. É a única chave atualmente neste dicionário o `NSThumbnial1024X1024SizeKey` e sua correspondência `UIImage`.

#### <a name="saving-document-thumbnails"></a>Salvando miniaturas de documentos

É a maneira mais fácil para salvar uma miniatura usando `UIDocument`. Chamando o `GetFileAttributesToWrite` método o `UIDocument` e definir a miniatura, ela será automaticamente salva quando o arquivo de documento é. O Daemon do iCloud visualizará essa alteração e propagá-lo com o iCloud. No Mac OS X, miniaturas são geradas automaticamente para o desenvolvedor, o plug-in rápido pesquisar.

Com os fundamentos de trabalhar com documentos iCloud com base no local, juntamente com as modificações API existente, você está pronto para implementar o controlador de exibição de seletor de documento em um Xamarin iOS 8 aplicativo móvel.


## <a name="enabling-icloud-in-xamarin"></a>Habilitando o iCloud em Xamarin

Antes do seletor de documento pode ser usado em um aplicativo xamarin, suporte iCloud precisa ser habilitado em seu aplicativo e por meio da Apple. 

A seguintes etapas passo a passo o processo de provisionamento para iCloud.

1. Crie um contêiner no iCloud.
2. Crie uma ID de aplicativo que contém o serviço de aplicativo no iCloud.
3. Criar um perfil de provisionamento que inclui esse ID. App

O [trabalhando com recursos](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) guia aborda as duas primeiras etapas. Para criar um perfil de provisionamento, siga as etapas a [perfil de provisionamento](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile) guia.



A seguintes etapas passo a passo o processo de configuração do seu aplicativo para o iCloud:

Faça o seguinte:

1.  Abra o projeto no Visual Studio para Mac ou o Visual Studio.
2.  No **Solution Explorer**, clique com o botão direito e selecione opções.
3.  No, selecione a caixa de diálogo Opções **iOS aplicativo**, certifique-se de que o **identificador de pacote** corresponde ao que foi definido no **ID do aplicativo** criado acima para o aplicativo. 
4.  Selecione **iOS de assinatura de pacote**, selecione o **desenvolvedor identidade** e **perfil de provisionamento de** criado acima.
5.  Clique o **Okey** botão para salvar as alterações e fechar a caixa de diálogo.
6.  Clique duas vezes em `Entitlements.plist` no **Solution Explorer** para abri-lo no editor.

    > [!IMPORTANT]
    > No Visual Studio pode precisar abrir o editor de direitos clicando nele, selecionando **abrir com...** e selecionando o Editor de lista de propriedades

7.  Verificar **habilitar iCloud** , **iCloud documentos** , **armazenamento de chave-valor** e **CloudKit** .
8.  Verifique se o **contêiner** existe para o aplicativo (como criado acima). Exemplo: `iCloud.com.your-company.AppName`
9.  Salve as alterações no arquivo.

Para obter mais informações sobre direitos, consulte o [trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md) guia.

Com a configuração acima em vigor, o aplicativo agora pode usar documentos baseados em nuvem e o novo controlador de exibição de seletor de documento.

## <a name="common-setup-code"></a>Código de instalação comum

Antes de começar com o controlador de exibição de seletor de documento, há um código de instalação padrão necessário. Iniciar, modificando o aplicativo `AppDelegate.cs` de arquivo e torná-lo a aparência a seguir:

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
                    // Yes, inform caller and save location the the Application Container
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
> O código acima inclui o código da seção de detecção e Listar documentos acima. Ele é apresentado aqui em sua totalidade, como seria em um aplicativo real. Para simplificar, este exemplo funciona com um único arquivo embutido (`test.txt`) apenas.

O código acima expõe vários iCloud unidade atalhos para torná-las mais fáceis de trabalhar com o restante do aplicativo.

Em seguida, adicione o seguinte código para qualquer contêiner de exibição que será usando o seletor de documento ou trabalhar com documentos baseados em nuvem ou o modo de exibição:

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

Isso adiciona um atalho para obter o `AppDelegate` e acessar os atalhos do iCloud criados acima.

Com esse código, vamos implementar o controlador de exibição de seletor de documento em um aplicativo do iOS 8 Xamarin.

## <a name="using-the-document-picker-view-controller"></a>Usando o controlador de exibição de seletor de documento

Antes do iOS 8, era muito difícil acessar documentos de outro aplicativo, porque havia uma maneira de descobrir documentos fora do aplicativo de dentro do aplicativo.

### <a name="existing-behavior"></a>Comportamento de existente

 [![](document-picker-images/image31.png "Visão geral sobre o comportamento existente")](document-picker-images/image31.png#lightbox)

Vamos dar uma olhada acessar um documento externo antes de iOS 8:

1.  Primeiro, o usuário teria que abrir o aplicativo que criou o documento.
1.  O documento está selecionado e o `UIDocumentInteractionController` é usado para enviar o documento para o novo aplicativo.
1.  Por fim, uma cópia do documento original é colocada no contêiner do novo aplicativo.


A partir daí, o documento está disponível para o segundo aplicativo abrir e editar.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Descoberta de documentos fora de contêiner do aplicativo

No iOS 8, um aplicativo é capaz de acessar documentos fora de seu próprio aplicativo de contêiner com facilidade:

 [![](document-picker-images/image32.png "Descoberta de documentos fora de contêiner do aplicativo")](document-picker-images/image32.png#lightbox)

Usando o iCloud novo seletor de documento ( `UIDocumentPickerViewController`), um aplicativo iOS diretamente pode descobrir e acessar fora de seu contêiner do aplicativo. O `UIDocumentPickerViewController` fornece um mecanismo para o usuário ao conceder acesso a e editar os documentos de descoberta por meio de permissões.

Um aplicativo deve participar para ter seus documentos aparecerão no seletor de documento no iCloud e estará disponível para outros aplicativos descobrir e trabalhar com eles. Para compartilhar seu contêiner de aplicativo, editá-lo um aplicativo do iOS 8 Xamarin `Info.plist` do arquivo em um editor de texto padrão e adicione as duas linhas seguintes à parte inferior do dicionário (entre o `<dict>...</dict>` marcas):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

O `UIDocumentPickerViewController` fornece uma ótima nova interface do usuário que permite que o usuário escolha documentos. Para exibir o controlador de exibição de seletor de documento em um aplicativo do iOS 8 Xamarin, faça o seguinte:

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
> O desenvolvedor deve chamar o `StartAccessingSecurityScopedResource` método o `NSUrl` antes de um documento externo pode ser acessado. O `StopAccessingSecurityScopedResource` método deve ser chamado para liberar o bloqueio de segurança assim que o documento foi carregado.

### <a name="sample-output"></a>Saída de Exemplo

Aqui está um exemplo de como o código acima exibiria um seletor de documento, quando executado em um dispositivo iPhone:

1.  O usuário inicia o aplicativo e a interface principal será exibida:   
 
    [![](document-picker-images/image33.png "A interface principal é exibida")](document-picker-images/image33.png#lightbox)
1.  Toques de usuário a **ação** botão na parte superior da tela e será solicitado a selecionar um **documento provedor** da lista de provedores disponíveis:   
 
    [![](document-picker-images/image34.png "Selecione um provedor de documento da lista de provedores disponíveis")](document-picker-images/image34.png#lightbox)
1.  O **documento seletor de exibição controlador** é exibido selecionado **documento provedor**:   
 
    [![](document-picker-images/image35.png "O controlador de exibição de seletor de documento é exibido")](document-picker-images/image35.png#lightbox)
1.  O usuário toca em um **pasta de documentos** para exibir seu conteúdo:   
 
    [![](document-picker-images/image36.png "O conteúdo da pasta do documento")](document-picker-images/image36.png#lightbox)
1.  O usuário seleciona um **documento** e **documento seletor** está fechado.
1.  A interface principal é exibida novamente, o **documento** é carregado do contêiner externo e seu conteúdo exibido.


A exibição atual do controlador de exibição de seletor de documento depende dos provedores de documento que o usuário instalou no dispositivo e qual modo de seletor de documento foi implementar. O exemplo acima é usando o modo de abertura, os outros tipos de modo serão discutidos em detalhes abaixo.

## <a name="managing-external-documents"></a>Gerenciando documentos externos

Como discutido acima, antes de iOS 8, um aplicativo pode acessar somente documentos que faziam parte de seu contêiner de aplicativo. No iOS 8, um aplicativo pode acessar documentos de fontes externas:

 [![](document-picker-images/image37.png "Gerenciando documentos externos-visão geral")](document-picker-images/image37.png#lightbox)

Quando o usuário seleciona um documento de uma fonte externa, um documento de referência é gravado para o contêiner do aplicativo que aponta para o documento original.

Para ajudar a adicionar essa nova capacidade em aplicativos existentes, vários novos recursos foram adicionados para o `NSMetadataQuery` API. Normalmente, um aplicativo usa o escopo de documento onipresente para listar os documentos que residem dentro de seu contêiner do aplicativo. Usando este escopo, somente os documentos dentro do contêiner do aplicativo continuará a ser exibido.

Usar o novo escopo de documento externo onipresente retornará documentos que resida fora do contêiner do aplicativo e retorna os metadados para eles. O `NSMetadataItemUrlKey` apontará para a URL onde o documento está realmente localizado.

Às vezes, um aplicativo não deseja trabalhar com os documentos que está sendo apontados pela referência de th. Em vez disso, o aplicativo deseja trabalhar diretamente com o documento de referência. Por exemplo, o aplicativo pode querer para exibir o documento na pasta do aplicativo na interface de usuário ou para permitir que o usuário mover as referências dentro de uma pasta.

No iOS 8, um novo `NSMetadataItemUrlInLocalContainerKey` foi fornecido para acessar o documento de referência diretamente. Essa chave aponta para a referência real para o documento externo em um contêiner do aplicativo.

O `NSMetadataUbiquitousItemIsExternalDocumentKey` é usado para testar se um documento é externo ao contêiner de um aplicativo ou não. O `NSMetadataUbiquitousItemContainerDisplayNameKey` é usada para acessar o nome do contêiner que está hospedando a cópia original de um documento externo.

### <a name="why-document-references-are-required"></a>Por que são necessárias referências de documento

O principal motivo que iOS 8 usa referências para acessar documentos externos é de segurança. Nenhum aplicativo receber acesso a contêiner do qualquer outro aplicativo. O seletor de documento pode fazer isso, porque está fora do processo em execução e tem o amplo acesso de sistema.

É a única maneira de obter um documento fora o contêiner do aplicativo usando o seletor de documento, e se a URL retornada pelo seletor de tem o escopo de segurança. A URL de escopo de segurança contém informações suficientes para o documento junto com os direitos de escopo necessários para conceder um acesso de aplicativo para o documento selecionado.

É importante observar que, se a URL de escopo de segurança foi serializada em uma cadeia de caracteres e, em seguida, desserializada, as informações de segurança que seriam perdidas e o arquivo seria inacessível da URL. O recurso de referência do documento fornece um mecanismo para obter os arquivos apontados por essas URLs.

Portanto, se o aplicativo adquire um `NSUrl` em um dos documentos de referência, ele já tem o escopo de segurança anexado e pode ser usado para acessar o arquivo. Por esse motivo, é altamente recomendável que o desenvolvedor usar `UIDocument` porque trata todas essas informações e processos para eles.

### <a name="using-bookmarks"></a>Usando indicadores

Nem sempre é possível enumerar os documentos de um aplicativo para voltar a um documento específico, por exemplo, ao fazer a restauração de estado. iOS 8 fornece um mecanismo para criar indicadores que se destinam diretamente a um determinado documento.

O código a seguir criará um indicador de um `UIDocument`do `FileUrl` propriedade:

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

A API de indicador existente é usada para criar um indicador em relação a um existente `NSUrl` que podem ser salvos e carregados para fornecer acesso direto a um arquivo externo. O código a seguir irá restaurar um indicador que foi criado acima:

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

## <a name="open-vs-import-mode-and-the-document-picker"></a>Abra o vs. O seletor de documento e o modo de importação

O controlador de exibição de seletor de documento apresenta dois modos diferentes de operação:

1.  **Abrir modo** – neste modo, quando o usuário seleciona e documento externo, o seletor de documento criará um indicador de escopo de segurança no contêiner do aplicativo.   
 
    [![](document-picker-images/image37.png "Segurança no escopo do indicador no contêiner do aplicativo")](document-picker-images/image37.png#lightbox)
1.  **Modo de importação** – neste modo, quando o usuário seleciona e documento externo, o seletor de documento não criar um indicador, mas em vez disso, copie o arquivo em um local temporário e fornecem ao aplicativo acesso ao documento neste local:   
 
    [![](document-picker-images/image38.png "O seletor de documento será copiá-lo em um local temporário e fornecem ao aplicativo acesso ao documento neste local")](document-picker-images/image38.png#lightbox)   
 Após o encerramento do aplicativo por algum motivo, o local temporário é esvaziado e o arquivo removido. Se o aplicativo deve manter o acesso ao arquivo, ele deve fazer uma cópia e colocá-lo em seu contêiner do aplicativo.


O modo de abertura é útil quando o aplicativo deseja colaborar com outro aplicativo e compartilhar as alterações feitas no documento com esse aplicativo. O modo de importação é usado quando o aplicativo não deseja compartilhar suas modificações em um documento com outros aplicativos.

## <a name="making-a-document-external"></a>Tornando um documento externo

Como observado anteriormente, um aplicativo iOS 8 não tem acesso aos contêineres de fora de seu próprio contêiner do aplicativo. O aplicativo pode gravar seu próprio contêiner localmente ou em um local temporário e usar um modo de documento especial para mover o documento resultante fora o contêiner do aplicativo para um usuário escolhido local.

Para mover um documento em um local externo, faça o seguinte:

1.  Primeiro, crie um novo documento em um local temporário ou local.
1.  Criar um `NSUrl` que aponta para o novo documento.
1.  Abra um novo controlador de exibição de seletor de documento e passá-lo a `NSUrl` com o modo de `MoveToService` . 
1.  Depois que o usuário escolhe um novo local, o documento será movido de seu local atual para o novo local.
1.  Um documento de referência será gravado para o contêiner de aplicativo do aplicativo para que o arquivo ainda pode ser acessado pelo aplicativo de criação.


O código a seguir pode ser usado para mover um documento em um local externo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

O documento de referência retornada pelo processo de acima é exatamente o mesmo que um criado pelo modo do seletor de documento aberto. No entanto, há vezes em que o aplicativo pode querer mover um documento sem manter uma referência a ele.

Para mover um documento sem gerar uma referência, use o `ExportToService` modo. Exemplo: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Ao usar o `ExportToService` modo, o documento será copiado para o contêiner externo e a cópia existente é deixada no local original.

## <a name="document-provider-extensions"></a>Extensões de provedor do documento

Com o iOS 8, Apple quer que o usuário final para poder acessar qualquer um dos seus documentos baseados em nuvem, independentemente de onde eles realmente existem. Para alcançar essa meta, o iOS 8 fornece um novo mecanismo de extensão do provedor de documento.

### <a name="what-is-a-document-provider-extension"></a>O que é uma extensão do provedor de documento?

Em poucas palavras, uma extensão do provedor de documento é uma maneira para um desenvolvedor ou de terceiros, para fornecer a um armazenamento de documentos alternativo de aplicativo que pode ser acessado em exatamente da mesma maneira como o local de armazenamento no iCloud existente.

O usuário pode selecionar um desses locais de armazenamento alternativo do seletor de documento e usarem os exata mesmo modos de acesso (Abrir, importar, mover ou exportação) para trabalhar com arquivos nesse local.

Isso é implementado usando duas extensões diferentes:

-  **Extensão de seletor de documento** – fornece um `UIViewController` subclasse que fornece uma interface gráfica do usuário escolher um documento de um local de armazenamento alternativo. Essa subclasse será exibido como parte do documento seletor de exibição de controlador.
-  **Fornecer extensão de arquivo** – é uma extensão sem interface do usuário que lida com fornecendo o conteúdo de arquivos. Essas extensões são fornecidas através de coordenação de arquivo ( `NSFileCoordinator` ). Este é outro caso importante em que a coordenação de arquivo é necessária.


O diagrama a seguir mostra o fluxo de dados comuns ao trabalhar com extensões de provedor de documento:

 [![](document-picker-images/image39.png "Este diagrama mostra o fluxo de dados comuns ao trabalhar com extensões de provedor do documento")](document-picker-images/image39.png#lightbox)

O seguinte processo ocorre:

1.  O aplicativo apresenta um controlador de seletor de documento para permitir que o usuário selecione um arquivo para trabalhar com.
1.  O usuário seleciona um local de arquivo alternativo e personalizado `UIViewController` extensão é chamada para exibir a interface do usuário.
1.  O usuário seleciona um arquivo nesse local e a URL é passada de volta para o seletor de documento.
1.  O seletor de documento seleciona da URL do arquivo e retorna-a para o aplicativo para o usuário trabalhar em.
1.  A URL é passada para o coordenador de arquivo para retornar o conteúdo de arquivos para o aplicativo.
1.  O coordenador de arquivos chama o provedor de extensão personalizada para recuperar o arquivo.
1.  O conteúdo do arquivo é retornado ao coordenador de arquivo.
1.  O conteúdo do arquivo é retornado para o aplicativo.


### <a name="security-and-bookmarks"></a>Segurança e indicadores

Esta seção terão uma visão geral de como a segurança e o arquivo persistente acessam com funciona indicadores com extensões de provedor do documento. Ao contrário do iCloud provedor de documento, que salva automaticamente a segurança e indicadores para o contêiner do aplicativo, extensões de provedor de documento não porque eles não fazem parte do sistema de referência do documento.

Por exemplo: em uma configuração empresarial que fornece seu próprio repositório de dados seguro em toda a empresa, os administradores não querem informações confidenciais corporativas acessados ou processado pelo iCloud público servidores. Portanto, o sistema de referência interna do documento não pode ser usado.

O sistema de indicador ainda pode ser usado e é responsabilidade do provedor de extensão do arquivo para processar corretamente um URL indicado e retornar o conteúdo do documento apontada por ele.

Para fins de segurança, o iOS 8 tem uma camada de isolamento que mantém as informações sobre os quais o aplicativo tem acesso a quais identificador dentro do qual provedor de arquivo. Observe que todo o acesso de arquivo é controlado por essa camada de isolamento.

O diagrama a seguir mostra o fluxo de dados quando estiver trabalhando com indicadores e uma extensão de provedor de documento:

 [![](document-picker-images/image40.png "Este diagrama mostra o fluxo de dados quando estiver trabalhando com indicadores e uma extensão de provedor do documento")](document-picker-images/image40.png#lightbox)

O seguinte processo ocorre:

1.  O aplicativo está prestes a entrar no plano de fundo e precisa persistir o estado. Ele chama `NSUrl` para criar um indicador em um arquivo no armazenamento alternativo.
1.  `NSUrl` chama a extensão de provedor para obter uma URL persistente para o documento. 
1.  A extensão do provedor de arquivo retorna a URL como uma cadeia de caracteres para o `NSUrl` .
1.  O `NSUrl` agrupa a URL em um indicador e o retorna ao aplicativo.
1.  Quando o aplicativo é ativado após sendo em segundo plano e precisa restaurar o estado, ele passa o indicador para `NSUrl` .
1.  `NSUrl` chama a extensão do provedor de arquivo com a URL do arquivo.
1.  O provedor de extensão de arquivo acessa o arquivo e retorna o local do arquivo para `NSUrl` .
1.  O local do arquivo é fornecido com informações de segurança e retornado ao aplicativo.


Aqui, o aplicativo pode acessar o arquivo e trabalhar com ela como normal.

### <a name="writing-files"></a>Gravando arquivos

Esta seção será dar uma olhada rápida como gravar arquivos em um local alternativo com uma extensão do provedor de documento funciona. O aplicativo iOS usará a coordenação de arquivo para salvar informações de disco dentro do contêiner do aplicativo. Logo após o arquivo foi gravado com êxito, a extensão do provedor de arquivo será notificada da alteração.

Neste ponto, a extensão do provedor de arquivo pode iniciar o carregamento do arquivo para o local alternativo (ou marcar o arquivo como carregamento sujo e exigindo).

### <a name="creating-new-document-provider-extensions"></a>Criando novas extensões de provedor de documento

Criar novas extensões de provedor do documento está fora do escopo deste artigo introdutório. Essas informações são fornecidas aqui para mostrar que, com base nas extensões de que um usuário tenha carregado em seu dispositivo iOS, um aplicativo possa ter acesso a locais de armazenamento de documento fora o Apple fornecido iCloud local.

O desenvolvedor deve estar ciente desse fato ao usar o seletor de documento e trabalhar com documentos externos. Eles não devem presumir que esses documentos são hospedados no iCloud.

Para obter mais informações sobre como criar um provedor de armazenamento ou uma extensão de seletor de documento, consulte o [Introdução a extensões de aplicativo](~/ios/platform/extensions.md) documento.

## <a name="migrating-to-icloud-drive"></a>Migrando para o iCloud unidade

No iOS 8, os usuários podem escolher continuar usando o iCloud existente documentos sistema usado em iOS 7 (e em sistemas anteriores), ou eles podem optar por migrar documentos existentes para o novo mecanismo de unidade no iCloud.

No Mac OS X Yosemite, Apple não fornece as versões anteriores compatibilidade para todos os documentos devem ser migrados com o iCloud unidade ou eles não serão mais atualizadas em todos os dispositivos.

Depois que uma conta de usuário tiver sido migrada para iCloud unidade, somente dispositivos usando o iCloud unidade poderá propagar alterações para documentos entre os dispositivos.

> [!IMPORTANT]
> Os desenvolvedores devem estar cientes de que os novos recursos abordados neste artigo só estarão disponíveis se a conta do usuário foi migrada para iCloud unidade. 

## <a name="summary"></a>Resumo

Este artigo abordou as alterações com o iCloud existente APIs necessárias para dar suporte a iCloud unidade e o novo controlador de exibição de seletor de documento. Ela abordou coordenação de arquivo e por que é importante ao trabalhar com documentos baseados em nuvem. Ela tem coberto a configuração necessária para habilitar documentos baseados em nuvem em um aplicativo xamarin e recebe uma olhada introdutória trabalhando com documentos fora recipiente do aplicativo de aplicativo usando o controlador de exibição de seletor de documento.

Além disso, este artigo brevemente coberto extensões de provedor de documento e por que o desenvolvedor deve estar ciente ao escrever aplicativos que podem manipular documentos baseados em nuvem.

## <a name="related-links"></a>Links relacionados

- [DocPicker (exemplo)](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [Introdução ao iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introdução a extensões de aplicativo](~/ios/platform/extensions.md)
