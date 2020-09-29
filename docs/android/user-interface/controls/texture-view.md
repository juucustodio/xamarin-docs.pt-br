---
title: TextureView Xamarin. Android
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 3085131e251a787965c91216106becb6c954da85
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457699"
---
# <a name="xamarinandroid-textureview"></a>TextureView Xamarin. Android

A `TextureView` classe é uma exibição que usa renderização 2D acelerada por hardware para permitir que um fluxo de conteúdo de vídeo ou OpenGL seja exibido. Por exemplo, a captura de tela a seguir mostra a `TextureView` exibição de um feed ao vivo da câmera do dispositivo:

[![Captura de tela de exemplo de uma imagem ao vivo da câmera do dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

Ao contrário da `SurfaceView` classe, que também pode ser usada para exibir o conteúdo de vídeo ou OpenGL, o TextureView não é renderizado em uma janela separada.
Portanto, o `TextureView` é capaz de dar suporte a transformações de exibição como qualquer outra exibição. Por exemplo, girar um `TextureView` pode ser realizado simplesmente definindo sua `Rotation` propriedade, sua transparência definindo sua `Alpha` propriedade e assim por diante.

Portanto, com o, `TextureView` agora podemos fazer coisas como exibir um fluxo ao vivo da câmera e transformá-la, conforme mostrado no código a seguir:

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

O código acima cria uma `TextureView` instância no método da atividade `OnCreate` e define a atividade como a `TextureView` `SurfaceTextureListener` . Para ser o `SurfaceTextureListener` , a atividade implementa a `TextureView.ISurfaceTextureListener` interface. O sistema chamará o `OnSurfaceTextAvailable` método quando o `SurfaceTexture` estiver pronto para uso. Nesse método, pegamos o `SurfaceTexture` que é passado e o definimos para a textura de visualização da câmera. Em seguida, temos a liberdade de executar operações normais baseadas em modo de exibição, como a definição de `Rotation` e `Alpha` , como no exemplo acima. O aplicativo resultante, em execução em um dispositivo, é mostrado abaixo:

[![Exemplo do aplicativo em execução em um dispositivo, exibindo uma imagem](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Para usar o `TextureView` , a aceleração de hardware deve ser habilitada, que será por padrão a partir do nível de API 14. Além disso, como este exemplo usa a câmera, a `android.permission.CAMERA` permissão e o `android.hardware.camera` recurso devem ser definidos no **AndroidManifest.xml**.

## <a name="related-links"></a>Links Relacionados

- [TextureViewDemo (exemplo)](/samples/xamarin/monodroid-samples/textureviewdemo)/)