---
title: PhotoKit em xamarin
description: Este documento descreve PhotoKit, discutir seus objetos de modelo, como dados de modelo de consulta e salvar as alterações para a biblioteca de fotos.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 4aeeec5b96e24c654407ad672930c0cb78592450
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787891"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit em xamarin

PhotoKit é uma nova estrutura que permite que aplicativos consultar a biblioteca de imagens do sistema e criar interfaces do usuário personalizadas para exibir e modificar seu conteúdo. Ele inclui um número de classes que representam os ativos de vídeo e imagem, bem como coleções de ativos como álbuns e pastas.

## <a name="model-objects"></a>Objetos de modelo

PhotoKit representa esses ativos em que ele chama objetos de modelo. Os objetos de modelo que representam as fotos e vídeos em si são do tipo `PHAsset`. Um `PHAsset` contém metadados, como o tipo de mídia do ativo e sua data de criação.
Da mesma forma, o `PHAssetCollection` e `PHCollectionList` classes contêm metadados sobre coleções de ativo e listas de coleção respectivamente. Coleções de ativos são grupos de ativos, como todas as fotos e vídeos para um determinado ano. Da mesma forma, as listas de coleção são grupos de coleções de ativo, como fotos e vídeos, agrupados por ano.

## <a name="querying-model-data"></a>Consultar dados do modelo

PhotoKit facilita para os dados de modelo de consulta por meio de uma variedade de métodos de busca. Por exemplo, para recuperar todas as imagens, você poderia chamar `PFAsset.Fetch`, passando o `PHAssetMediaType.Image` tipo de mídia.

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

O `PHFetchResult` instância poderia conter todas as `PFAsset` instâncias representando imagens. Para obter as próprias imagens, use o `PHImageManager` (ou a versão do cache, `PHCachingImageManager`) para fazer uma solicitação para a imagem chamando `RequestImageForAsset`. Por exemplo, o código a seguir recupera uma imagem para cada ativo em um `PHFetchResult` para exibir em uma célula de exibição de coleção:


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

Isso resulta em uma grade de imagens, conforme mostrado abaixo:

![](photokit-images/image4.png "O aplicativo em execução, exibindo uma grade de imagens")
 
## <a name="saving-changes-to-the-photo-library"></a>Salvar alterações para a biblioteca de fotos

Isso é como lidar com a consulta e leitura de dados. Você também pode escrever as alterações de volta na biblioteca. Como vários aplicativos de interessados são capazes de interagir com a biblioteca de fotos do sistema, você pode registrar um observador para ser notificado sobre alterações usando um PhotoLibraryObserver. Em seguida, quando as alterações chegam, seu aplicativo pode atualizar adequadamente. Por exemplo, aqui está uma implementação simples para recarregar o modo de exibição de coleção acima:

    class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
    {
        readonly PhotosViewController controller;
        public PhotoLibraryObserver (PhotosViewController controller)
        
        {
            this.controller = controller;
        }
    
        public override void PhotoLibraryDidChange (PHChange changeInstance)
        {
            DispatchQueue.MainQueue.DispatchAsync (() => {
            var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
            controller.fetchResults = changes.FetchResultAfterChanges;
            controller.CollectionView.ReloadData ();
            });
        }
    }
    
Para realmente gravar alterações do seu aplicativo, você deve criar uma solicitação de alteração. Cada uma das classes de modelo tem uma classe de solicitação de alteração associada. Por exemplo, para alterar um PHAsset, você cria um PHAssetChangeRequest. As etapas para executar as alterações que são gravadas de volta para a biblioteca de fotos e enviadas à observadores como a mostrada acima são:

-   Execute a operação de edição.
-   Salve os dados de imagem filtrada em uma instância de PHContentEditingOutput.
-   Fazer uma solicitação de alteração para publicá-lo de alterações a saída de edição.

Aqui está um exemplo que grava uma alteração em uma imagem que aplica um filtro de noir imagem principal:

    void ApplyNoirFilter (object sender, EventArgs e)
    {
            
            Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {
            
        // perform the editing operation, which applies a noir filter in this case
            var image = CIImage.FromUrl (input.FullSizeImageUrl);
            image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
            var noir = new CIPhotoEffectNoir {
                Image = image
            };
            var ciContext = CIContext.FromOptions (null);
            var output = noir.OutputImage;
            var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
            imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
            var editingOutput = new PHContentEditingOutput(input);
            var adjustmentData = new PHAdjustmentData();
            var data = uiImage.AsJPEG();
            NSError error;
            data.Save(editingOutput.RenderedContentUrl, false, out error);
            editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
            PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
                PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
                request.ContentEditingOutput = editingOutput;
          },
          (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
      });
    }
    
Quando o usuário seleciona o botão, o filtro é aplicado:

![](photokit-images/image5.png "Um exemplo de aplicação do filtro")
 
E graças PHPhotoLibraryChangeObserver, a alteração será refletida na exibição de coleção quando o utilizador navega de volta:

![](photokit-images/image6.png "A alteração será refletida na exibição de coleção quando o utilizador navega de volta")
