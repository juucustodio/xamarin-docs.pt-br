---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2017
ms.openlocfilehash: 1cd332894deac1e1fb076f647bb0120bda2306da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763599"
---
# <a name="textureview"></a>TextureView

O `TextureView` classe é uma exibição que usa a renderização 2D acelerada por hardware para habilitar um vídeo ou OpenGL fluxo de conteúdo a ser exibido. Por exemplo, a captura de tela a seguir mostra o `TextureView` exibindo um feed de câmera do dispositivo:

[![Captura de tela de exemplo de uma imagem ao vivo da câmera do dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

Ao contrário de `SurfaceView` classe, que também pode ser usado para exibir o conteúdo de vídeo ou de OpenGL, o TextureView não será renderizado em uma janela separada.
Portanto, `TextureView` é capaz de dar suporte a transformações de exibição como qualquer outra exibição. Por exemplo, girando uma `TextureView` pode ser feito simplesmente definindo seu `Rotation` propriedade, a transparência, definindo seu `Alpha` propriedade e assim por diante.

Portanto, com o `TextureView` agora podemos fazer coisas como exibir uma transmissão ao vivo da câmera e transformá-los, conforme mostrado no código a seguir:

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

O código acima cria um `TextureView` instância na atividade de `OnCreate` método e define a atividade como o `TextureView`do `SurfaceTextureListener`. Para ser o `SurfaceTextureListener`, a atividade implementa o `TextureView.ISurfaceTextureListener` interface. O sistema chamará o `OnSurfaceTextAvailable` método quando o `SurfaceTexture` está pronto para uso. Nesse método, faremos o `SurfaceTexture` que é transmitido e defina-a textura de visualização da câmera. Em seguida, estamos livres para executar operações com base em modo de exibição normais, como configuração de `Rotation` e `Alpha`, conforme mostrado no exemplo acima. O aplicativo resultante, em execução em um dispositivo, é mostrado abaixo:

[![Exemplo de aplicativo em execução em um dispositivo, exibindo uma imagem](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Para usar o `TextureView`, aceleração de hardware deve ser ativada, o que será por padrão a partir de 14 de nível de API. Também, uma vez que este exemplo usa a câmera, tanto o `android.permission.CAMERA` permissão e o `android.hardware.camera` recurso deve ser definido na **AndroidManifest.xml**.



## <a name="related-links"></a>Links relacionados

- [TextureViewDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [Introdução ao sabor de sorvete Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
