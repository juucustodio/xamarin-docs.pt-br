---
title: PhotoKit no Xamarin. iOS
description: Este documento descreve o PhotoKit, discutindo seus objetos de modelo, como consultar dados de modelo e salvar alterações na biblioteca de fotos.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: def34efd1fd48cc0e7dd802a6d3e843be1e156a4
ms.sourcegitcommit: 5ddb107b0a56bef8a16fce5bc6846f9673b3b22e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75558801"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit no Xamarin. iOS

[![baixar](~/media/shared/download.png) de exemplo baixar um exemplo de código](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-samplephotoapp/)

PhotoKit é uma estrutura que permite que os aplicativos consultem a biblioteca de imagens do sistema e criem interfaces de usuário personalizadas para exibir e modificar seu conteúdo. Ele inclui várias classes que representam ativos de imagem e vídeo, bem como coleções de ativos, como álbuns e pastas.

## <a name="permissions"></a>Permissões

Antes que seu aplicativo possa acessar a biblioteca de fotos, será apresentada ao usuário uma caixa de diálogo de permissões. Você deve fornecer um texto explicativo no arquivo **info. plist** para explicar como seu aplicativo usa a biblioteca de fotos, por exemplo:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Applies filters to photos and updates the original image</string>
```

## <a name="model-objects"></a>Objetos de modelo

PhotoKit representa esses ativos em que ele chama objetos de modelo. Os objetos de modelo que representam as fotos e os vídeos são do tipo `PHAsset`. Uma `PHAsset` contém metadados como o tipo de mídia do ativo e sua data de criação.
Da mesma forma, as classes `PHAssetCollection` e `PHCollectionList` contêm metadados sobre coleções de ativos e listas de coleções, respectivamente. As coleções de ativos são grupos de ativos, como todas as fotos e vídeos de um determinado ano. Da mesma forma, as listas de coleta são grupos de coleções de ativos, como fotos e vídeos agrupados por ano.

## <a name="querying-model-data"></a>Consultando dados de modelo

O PhotoKit facilita a consulta de dados de modelo por meio de uma variedade de métodos de busca. Por exemplo, para recuperar todas as imagens, você chamaria `PHAsset.Fetch`, passando o tipo de mídia `PHAssetMediaType.Image`.

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

Em seguida, a instância de `PHFetchResult` conteria todas as instâncias de `PHAsset` que representam imagens. Para obter as próprias imagens, use o `PHImageManager` (ou a versão de cache `PHCachingImageManager`) para fazer uma solicitação para a imagem chamando `RequestImageForAsset`. Por exemplo, o código a seguir recupera uma imagem para cada ativo em uma `PHFetchResult` a ser exibida em uma célula de exibição de coleção:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
    imageMgr.RequestImageForAsset (
        (PHAsset)fetchResults [(uint)indexPath.Item],
        thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (),
        (img, info) => {
            imageCell.ImageView.Image = img;
        }
    );
    return imageCell;
}
```

Isso resulta em uma grade de imagens, conforme mostrado abaixo:

![](photokit-images/image4.png "The running app displaying a grid of images")

## <a name="saving-changes-to-the-photo-library"></a>Salvando alterações na biblioteca de fotos

É assim que lida com a consulta e a leitura de dados. Você também pode gravar as alterações de volta na biblioteca. Como vários aplicativos interessados são capazes de interagir com a biblioteca de fotos do sistema, você pode registrar um observador para ser notificado sobre as alterações usando uma `PhotoLibraryObserver`. Em seguida, quando as alterações chegam, seu aplicativo pode ser atualizado de acordo. Por exemplo, aqui está uma implementação simples para recarregar a exibição de coleção acima:

```csharp
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
```

Para realmente escrever alterações de seu aplicativo, você cria uma solicitação de alteração. Cada uma das classes de modelo tem uma classe de solicitação de alteração associada. Por exemplo, para alterar um `PHAsset`, você cria uma `PHAssetChangeRequest`. As etapas para executar alterações que são gravadas na biblioteca de fotos e enviadas aos observadores como a acima são:

1. Execute a operação de edição.
2. Salve os dados da imagem filtrada em uma instância do `PHContentEditingOutput`.
3. Faça uma solicitação de alteração para publicar as alterações da saída de edição.

Aqui está um exemplo que grava uma alteração em uma imagem que aplica um filtro de Noir de imagem principal:

```csharp
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
```

Quando o usuário seleciona o botão, o filtro é aplicado:

![Dois exemplos, mostrando a foto antes e depois que o filtro é aplicado](photokit-images/image5.png)

E, graças ao `PHPhotoLibraryChangeObserver`, a alteração é refletida na exibição de coleção quando o usuário navega de volta:

![Exibição de coleção de fotos mostrando a foto modificada](photokit-images/image6.png)
