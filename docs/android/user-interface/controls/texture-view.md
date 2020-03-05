---
title: TextureView Xamarin. Android
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 2ffa544789e0d605a241c8e038c790650a7fc6a3
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292458"
---
# <a name="xamarinandroid-textureview"></a>TextureView Xamarin. Android

A classe `TextureView` é uma exibição que usa renderização 2D acelerada por hardware para permitir que um fluxo de conteúdo de vídeo ou OpenGL seja exibido. Por exemplo, a captura de tela a seguir mostra a `TextureView` exibir um feed ao vivo da câmera do dispositivo:

[![captura de tela de exemplo de uma imagem ao vivo da câmera do dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

Ao contrário da classe `SurfaceView`, que também pode ser usada para exibir o conteúdo de vídeo ou OpenGL, o TextureView não é renderizado em uma janela separada.
Portanto, `TextureView` é capaz de dar suporte a transformações de exibição como qualquer outra exibição. Por exemplo, girar um `TextureView` pode ser realizado simplesmente definindo sua propriedade `Rotation`, sua transparência definindo sua propriedade `Alpha` e assim por diante.

Portanto, com o `TextureView` agora podemos fazer coisas como exibir um fluxo ao vivo da câmera e transformá-la, conforme mostrado no código a seguir:

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;

        SetContentView (_textureView);
    }

    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }

        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

O código acima cria uma instância de `TextureView` no método `OnCreate` da atividade e define a atividade como a `SurfaceTextureListener`do `TextureView`. Para ser o `SurfaceTextureListener`, a atividade implementa a interface `TextureView.ISurfaceTextureListener`. O sistema chamará o método `OnSurfaceTextAvailable` quando o `SurfaceTexture` estiver pronto para uso. Nesse método, pegamos o `SurfaceTexture` que é passado e o definimos para a textura de visualização da câmera. Em seguida, temos a liberdade de executar operações normais baseadas em modo de exibição, como definir o `Rotation` e `Alpha`, como no exemplo acima. O aplicativo resultante, em execução em um dispositivo, é mostrado abaixo:

[![exemplo do aplicativo em execução em um dispositivo, exibindo uma imagem](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Para usar o `TextureView`, a aceleração de hardware deve ser habilitada, que será por padrão a partir do nível de API 14. Além disso, como este exemplo usa a câmera, a permissão `android.permission.CAMERA` e o recurso `android.hardware.camera` devem ser definidos no **AndroidManifest. xml**.

## <a name="related-links"></a>Links relacionados

- [TextureViewDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)/)
