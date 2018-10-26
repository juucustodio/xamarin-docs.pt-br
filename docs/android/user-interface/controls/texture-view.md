---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: f43147d98599d36aa136e4ddc2975205e0e69e26
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122836"
---
# <a name="textureview"></a>TextureView

O `TextureView` classe é um modo de exibição que usa renderização 2D acelerada por hardware para habilitar um vídeo ou um fluxo de conteúdo do OpenGL a ser exibido. Por exemplo, a captura de tela a seguir mostra o `TextureView` exibindo uma transmissão ao vivo da câmera do dispositivo:

[![Captura de tela de exemplo de uma imagem ao vivo da câmera do dispositivo](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

Ao contrário de `SurfaceView` classe, que também pode ser usado para exibir o OpenGL ou conteúdo de vídeo, o TextureView não é renderizado em uma janela separada.
Portanto, `TextureView` é capaz de dar suporte a transformações de modo de exibição, como qualquer outra exibição. Por exemplo, girando uma `TextureView` pode ser feito simplesmente definindo seu `Rotation` propriedade, a transparência, definindo seu `Alpha` propriedade e assim por diante.

Portanto, com o `TextureView` agora podemos fazer coisas como exibir uma transmissão ao vivo da câmera e transformá-la, conforme mostrado no código a seguir:

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

O código acima cria uma `TextureView` instância na atividade de `OnCreate` método e define a atividade como o `TextureView`do `SurfaceTextureListener`. Para ser o `SurfaceTextureListener`, a atividade implementa o `TextureView.ISurfaceTextureListener` interface. O sistema irá chamar o `OnSurfaceTextAvailable` método quando o `SurfaceTexture` está pronto para uso. Nesse método, podemos levar a `SurfaceTexture` que é passado e defini-lo como textura de visualização da câmera. Em seguida, estamos livres para executar operações normais com base no modo de exibição, como a configuração de `Rotation` e `Alpha`, conforme mostrado no exemplo acima. O aplicativo resultante, em execução em um dispositivo, é mostrado abaixo:

[![Exemplo de como o aplicativo em execução em um dispositivo, exibindo uma imagem](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

Para usar o `TextureView`, aceleração de hardware deve ser habilitada, que será por padrão a partir de 14 de nível de API. Também, uma vez que este exemplo usa a câmera, tanto a `android.permission.CAMERA` permissão e o `android.hardware.camera` recurso deve ser definido na **androidmanifest. XML**.



## <a name="related-links"></a>Links relacionados

- [TextureViewDemo (amostra)](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [Apresentando o Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
