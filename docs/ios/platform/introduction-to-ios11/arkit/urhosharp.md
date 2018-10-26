---
title: Usando o ARKit com UrhoSharp em xamarin. IOS
description: Este documento descreve como configurar um aplicativo ARKit no xamarin. IOS, em seguida, examina como os quadros são renderizados, como ajustar a câmera, como detectar planos, como trabalhar com iluminação e muito mais. Ele também discute UrhoSharp e escrever código para o HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/01/2017
ms.openlocfilehash: 435ca560eee4f8f44443816c2a4ccec195ba5395
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103888"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>Usando o ARKit com UrhoSharp em xamarin. IOS

Com a introdução da [ARKit](https://developer.apple.com/arkit/), Apple tornou fácil para os desenvolvedores criem aplicativos de realidade aumentada. ARKit pode acompanhar a posição exata do seu dispositivo e detectar várias superfícies no mundo, e ele, em seguida, é responsabilidade do desenvolvedor para mesclar os dados saídos ARKit em seu código.

[UrhoSharp](~/graphics-games/urhosharp/index.md) fornece uma API abrangente e fácil de usar de 3D que você pode usar para criar aplicativos 3D.   Ambos podem ser combinadas ARKit para fornecer as informações sobre o mundo de físico e Urho para renderizar os resultados.

Esta página explica como conectar-se esses dois mundos juntos para criar aplicativos de realidade aumentada excelente.


## <a name="the-basics"></a>Os conceitos básicos

O que queremos fazer é apresentam conteúdo 3D na parte superior do mundo, como visto pelo iPhone/iPad.   A ideia é mesclar o conteúdo vindo de câmera do dispositivo com o conteúdo 3D e como o usuário do dispositivo se move pela sala para garantir que o objeto 3D se comportar da forma é que eles parte da sala: isso é feito com ancoragem os objetos no mundo.

![Figura animada no ARKit](urhosharp-images/image1.gif)


Vamos usar a biblioteca Urho carregar nossos ativos 3D e colocá-las no mundo, e vamos usar ARKit para obter o fluxo de vídeo provenientes da câmera, bem como o local do telefone no mundo.   Quando o usuário move-se com seu telefone, usaremos as alterações no local para atualizar o sistema de coordenadas que o mecanismo Urho está exibindo.

Dessa forma, quando você colocar um objeto no espaço 3D e o usuário move, o local do objeto 3D reflete o local e o local em que ele foi colocado.

## <a name="setting-up-your-application"></a>Como configurar seu aplicativo

### <a name="ios-application-launch"></a>Inicie o aplicativo do iOS

Necessidades de seu aplicativo iOS criar e iniciar seu conteúdo 3D, você pode fazer isso criando um implementa uma subclasse do [ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) e forneça o código de instalação, substituindo o `Start` método.  Isso é onde sua cena é preenchida com dados de evento manipuladores são de instalação e assim por diante.

Apresentamos uma `Urho.ArkitApp` que pode efetuar subclasses de classes `Urho.Application` e, na sua `Start` método faz o trabalho pesado.   Tudo que você precisa fazer para seu aplicativo é de Urho existente altere a classe base para ser do tipo `Urho.ArkitApp` e você tiver um aplicativo que executará sua cena urho no mundo.

### <a name="the-arkitapp-class"></a>A classe ArkitApp

Essa classe fornece um conjunto de padrões convenientes, ambos os uma cena com alguns objetos de chave, bem como o processamento de eventos ARKit conforme elas são entregues pelo sistema operacional.

A instalação é feita na `Start` método virtual.   Quando você substitui esse método em sua subclasse, você precisará certificar-se a cadeia para seu pai usando `base.Start()` em sua própria implementação.

O `Start` método configura a cena, o visor, câmera e uma luz direcional e revela-los como propriedades públicas:

- uma [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/) para manter seus objetos,
- um direcional [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) com sombras e cuja localização está disponível por meio de `LightNode` propriedade
- uma [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) cujos componentes são atualizados quando ARKit oferece uma atualização para o aplicativo e
- uma [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/) exibindo os resultados.


### <a name="your-code"></a>Seu código

Você precisa, em seguida, a subclasse de `ArkitApp` de classe e substituir o `Start` método.   A primeira coisa que o método deve fazer é cadeia até a `ArkitApp.Start` chamando `base.Start()`.  Depois disso, você pode usar qualquer uma da configuração de propriedades por ArkitApp adicionar objetos à cena, personalizar as luzes, sombras ou eventos que você deseja manipular.

O exemplo ARKit/UrhoSharp carrega um caractere animado com texturas e reproduz a animação, com a seguinte implementação:

    ```csharp
    public class MutantDemo : ArkitApp
    {
        [Preserve]
        public MutantDemo(ApplicationOptions opts) : base(opts) { }

        Node mutantNode;

        protected override void Start()
        {
            base.Start ();

            // Mutant
            mutantNode = Scene.CreateChild();
            mutantNode.Rotation = new Quaternion(x: 0, y:15, z:0);
            mutantNode.Position = new Vector3(0, -1f, 2f); /*two meters away*/
            mutantNode.SetScale(0.5f);

            var mutant = mutantNode.CreateComponent<AnimatedModel>();
            mutant.Model = ResourceCache.GetModel("Models/Mutant.mdl");
            mutant.Material = ResourceCache.GetMaterial("Materials/mutant_M.xml");

            var animation = mutantNode.CreateComponent<AnimationController>();
            animation.Play("Animations/Mutant_HipHop1.ani", 0, true, 0.2f);
        }
    }
    ```

E isso é realmente tudo o que você precisa fazer neste momento para ter seu conteúdo 3D exibido em realidade aumentada.

Urho usa formatos personalizados para modelos 3D e animações, portanto, você precisa exportar seus ativos nesse formato.   Você pode usar ferramentas como o [Add-in Urho3D Blender](https://github.com/reattiva/Urho3D-Blender) e [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) que pode converter esses ativos de formatos populares, como DBX, DAE, OBJ, Blend, 3D e máximo para o formato exigido pelo Urho.

Para saber mais sobre como criar aplicativos 3D usando Urho, visite o [Introdução ao UrhoSharp](~/graphics-games/urhosharp/introduction.md) guia.

## <a name="arkitapp-in-depth"></a>ArkitApp em camadas

> [!NOTE]
> Esta seção destina-se a desenvolvedores que deseja personalizar a experiência de padrão de UrhoSharp e ARKit ou querem obter uma visão mais aprofundada sobre como funciona a integração.   Não é necessária para ler esta seção.

A API ARKit é muito simple, criar e configurar uma [ARSession](https://developer.apple.com/documentation/arkit/arsession) do objeto que, em seguida, começar a fornecer [ARFrame](https://developer.apple.com/documentation/arkit/arframe) objetos.   Eles contêm a imagem capturada da câmera, bem como a posição do mundo real estimada do dispositivo.

Podemos será compondo as imagens que estão sendo entregues pela câmera para nós com nosso conteúdo 3D e ajustar a câmera no UrhoSharp para corresponder as chances no dispositivo local e posição.

O diagrama a seguir mostra o que está ocorrendo `ArkitApp` classe:

[![Diagrama de classes e as telas no ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Os quadros de renderização

A ideia é simples, combinar o vídeo oriundo da câmera com nosso gráficos 3D para produzir a imagem combinada.     Podemos obterá uma série dessas imagens capturadas em sequência e podemos será combinar essa entrada com a cena Urho.

A maneira mais simples de fazer isso é inserir uma [ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/) em principal [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/).  Este é um conjunto de comandos que são executadas para desenhar um único quadro.  Esse comando preencherá o visor com qualquer textura que passamos a ela.    Podemos configurar o primeiro quadro é o processo, e a definição real é feita em th **ARRenderPath.xml** arquivo que é carregado no momento.

No entanto, existem dois problemas para combinar esses dois mundos:


1. No iOS, texturas de GPU deve ter uma resolução que seja uma potência de dois, mas os quadros que obteremos da câmera não tem a resolução que são uma potência de dois, por exemplo: 1280 x 720.
2. Os quadros são codificados em [YUV](https://en.wikipedia.org/wiki/YUV) formato, representado por duas imagens – luma e croma.

Os quadros YUV vêm em duas resoluções diferentes.  uma imagem de 1280 x 720 representando luminância (basicamente, uma imagem de escala de cinza) e muito menores 640 x 360 para o componente de crominância:

![Imagem demonstrando combinando Y e componentes UV](urhosharp-images/image3.png)


Para desenhar uma imagem completa de coloridos usando o OpenGL ES temos de escrever um pequeno sombreador que usa luminosidade (componente Y) e crominância (planos UV) de slots de textura.  No UrhoSharp tenham nomes - "sDiffMap" e "sNormalMap" e convertê-los em formato RGB:

```csharp
mat4 ycbcrToRGBTransform = mat4(
    vec4(+1.0000, +1.0000, +1.0000, +0.0000),
    vec4(+0.0000, -0.3441, +1.7720, +0.0000),
    vec4(+1.4020, -0.7141, +0.0000, +0.0000),
    vec4(-0.7010, +0.5291, -0.8860, +1.0000));

vec4 ycbcr = vec4(texture2D(sDiffMap, vTexCoord).r,
                    texture2D(sNormalMap, vTexCoord).ra, 1.0);
gl_FragColor = ycbcrToRGBTransform * ycbcr;
```

Para renderizar a textura que não tem uma potência de dois de resolução, precisamos definir Texture2D com os seguintes parâmetros:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Portanto, somos capazes de processar imagens capturadas como um plano de fundo e renderizar qualquer cena acima dele mutante parecido assustador.

### <a name="adjusting-the-camera"></a>Ajustando a câmera

O `ARFrame` objetos também contêm a posição estimada do dispositivo.  Agora, precisamos mover a câmera jogo ARFrame - antes ARKit não era um grande problema para acompanhar orientação do dispositivo (roll, tom e yaw) e render um holograma fixado na parte superior do vídeo - mas se você mover um pouco seu dispositivo - hologramas temos será sincronizado.

Isso acontece porque os sensores internos como giroscópio não são capazes de acompanhar os movimentos, eles podem somente a aceleração.  Análises de ARKit cada recurso de quadro e extrai aponta para rastrear e, portanto, é capaz de fornecer uma lista exata transformam a matriz que contém dados de movimento e a rotação.

Por exemplo, isso é como podemos obter a posição atual:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Podemos usar `-row.Z` como ARKit usa um sistema de coordenadas destro.


### <a name="plane-detection"></a>Detecção de plano

ARKit é capaz de detectar planos horizontais e essa capacidade permite que você interaja com o mundo real, por exemplo, podemos pode colocar o mutante em uma tabela real ou um andar. A maneira mais simples de fazer isso é usar o método HitTest (raycasting). Ele converte coordenadas de tela (0,5, 0,5 é o centro) para as coordenadas do mundo real (0, 0; 0 é o local do primeiro quadro).

```chsarp
protected Vector3? HitTest(float screenX = 0.5f, float screenY = 0.5f)
{
    var result = ARSession.CurrentFrame.HitTest(new CGPoint(screenX, screenY),
        ARHitTestResultType.ExistingPlaneUsingExtent)?.FirstOrDefault();
    if (result != null)
    {
        var row = result.WorldTransform.Row3;
        return new Vector3(row.X, row.Y, -row.Z);
    }
    return null;
}
```

Agora podemos pode colocar o mutante em uma superfície horizontal, dependendo de onde na tela do dispositivo, toque em:

```chsarp
void OnTouchEnd(TouchEndEventArgs e)
{
    float x = e.X / (float)Graphics.Width;
    float y = e.Y / (float)Graphics.Height;
    var pos = HitTest(x, y);
    if (pos != null)
    mutantNode.Position = pos.Value;
}
```

![Figura animada alterando planos conforme movido do modo de exibição](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Iluminação realista

Dependendo de condições de iluminação do mundo real, a cena virtual deve ser mais clara ou mais escura para melhor corresponder ao seu ambiente. ARFrame contém uma propriedade de LightEstimate que podemos usar para ajustar a luz ambiente Urho, isso é semelhante a esta:


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>Além do iOS - HoloLens

UrhoSharp [é executado em todos os principais sistemas operacionais](~/graphics-games/urhosharp/platform/index.md), portanto, você pode reutilizar seu código existente em outro lugar.

HoloLens é uma das plataformas mais interessantes que ele é executado em.   Isso significa que você pode alternar facilmente entre o iOS e HoloLens para criar aplicativos de realidade aumentada awesome usar UrhoSharp.

Você pode encontrar a fonte de MutantDemo em [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Links relacionados

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (com UrhoSharp) (amostra)](https://github.com/EgorBo/ARKitXamarinDemo)
