---
title: Usando ARKit com UrhoSharp
ms.topic: article
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/01/2016
ms.openlocfilehash: 4c6928af364870541581b784b7c4e9e7717eb2db
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="using-arkit-with-urhosharp"></a>Usando ARKit com UrhoSharp

Com a introdução de [ARKit](https://developer.apple.com/arkit/), Apple tornou simples para os desenvolvedores criarem aplicativos realidade aumentada. ARKit pode controlar a posição exata do seu dispositivo e detectar várias superfícies no mundo, e, em seguida, é responsabilidade do desenvolvedor para combinar os dados provenientes de ARKit em seu código.

[UrhoSharp](~/graphics-games/urhosharp/index.md) fornece uma API 3D abrangente e fácil de usar que você pode usar para criar aplicativos 3D.   Ambos podem ser mesclados juntos, ARKit para fornecer as informações físicas sobre do mundo e Urho para processar os resultados.

Esta página explica como conectar-se esses dois mundos juntos para criar aplicativos de grande realidade aumentada.


## <a name="the-basics"></a>Noções básicas

O que queremos fazer é apresentar conteúdo 3D na parte superior do mundo, conforme visto pelo iPhone.   A ideia é combinar o conteúdo proveniente da câmera do celular com o conteúdo 3D e como o usuário do telefone se move pela sala para garantir que o objeto 3D se comportam pois eles faz parte da sala - isso é feito através de ancoragem os objetos no mundo.

![Figura animada na ARKit](urhosharp-images/image1.gif)


Vamos usar a biblioteca de Urho para carregar os ativos 3D e colocá-los no mundo e usaremos ARKit para obter o fluxo de vídeo provenientes da câmera, bem como o local do telefone no mundo.   Como o usuário se desloca com seu telefone, usaremos as alterações no local para atualizar o sistema de coordenadas que o mecanismo de Urho está exibindo.

Dessa forma, quando você colocar um objeto no espaço 3D e o usuário move, o local do objeto 3D reflete o local e o local onde ele foi colocado.

## <a name="setting-up-your-application"></a>Configurar seu aplicativo

### <a name="ios-application-launch"></a>Inicie o aplicativo do iOS

Seu aplicativo iOS precisa para criar e iniciar o seu conteúdo 3D, você pode fazer isso criando uma implementação de uma subclasse do [ `Urho.Application` ](https://developer.xamarin.com/api/type/Urho.Application/) e forneça o código de instalação, substituindo o `Start` método.  Isso é onde a cena é preenchida com dados de evento manipuladores são de instalação e assim por diante.

Apresentamos uma `Urho.ArkitApp` que as subclasses de classe `Urho.Application` e, na sua `Start` método faz o trabalho pesado.   Você precisa fazer sua Urho existente aplicativo é alterar a classe base para ser do tipo `Urho.ArkitApp` e você tiver um aplicativo que será executado o urho cena no mundo.

### <a name="the-arkitapp-class"></a>A classe ArkitApp

Essa classe fornece um conjunto de padrões convenientes, ambos os uma cena com alguns objetos de chave, bem como o processamento de eventos de ARKit conforme elas são fornecidas pelo sistema operacional.

A instalação é feita na `Start` método virtual.   Quando você substituir esse método na sua subclasse, você precisa verificar se a cadeia de seu pai usando `base.Start()` em sua própria implementação.

O `Start` método configura a cena, visor, câmera e uma luz direcional e superfícies como propriedades públicas:

- um [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene/) para manter seus objetos
- um direcional [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) com sombras e cuja localização está disponível por meio de `LightNode` propriedade
- um [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/) cujos componentes são atualizados quando ARKit oferece uma atualização para o aplicativo e
- um [ `ViewPort` ](https://developer.xamarin.com/api/type/Urho.Viewport/) exibindo os resultados.


### <a name="your-code"></a>Seu código

Você terá de subclasse de `ArkitApp` classe e substituir o `Start` método.   A primeira coisa que o método deve fazer é cadeia até o `ArkitApp.Start` chamando `base.Start()`.  Depois disso, você pode usar qualquer uma da configuração de propriedades por ArkitApp para adicionar objetos à cena, personalizar as luzes, sombras ou eventos que você deseja manipular.

O exemplo ARKit/UrhoSharp carrega um caractere animado com texturas e reproduz a animação, com a implementação a seguir:

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

E isso é tudo o que você precisa fazer agora para seu conteúdo 3D exibido em realidade aumentada.

Urho usa formatos personalizados para modelos 3D e animações, portanto você precisa exportar seus ativos para esse formato.   Você pode usar ferramentas como o [suplemento Urho3D Blender](https://github.com/reattiva/Urho3D-Blender) e [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) que pode converter esses ativos de formatos populares como DBX, DAE, OBJ, mesclagem, 3D máxima para o formato exigido pelo Urho.

Para saber mais sobre como criar aplicativos 3D usando Urho, visite o [Introdução ao UrhoSharp](~/graphics-games/urhosharp/introduction.md) guia.

## <a name="arkitapp-in-depth"></a>ArkitApp em camadas

> [!NOTE]
> Esta seção destina-se a desenvolvedores que quiser personalizar a experiência de padrão de UrhoSharp e ARKit ou obter uma visão mais profunda sobre como funciona a integração.   Não é necessária para ler esta seção.

A API ARKit é muito simple, criar e configurar um [ARSession](https://developer.apple.com/documentation/arkit/arsession) do objeto que e começar a entrega de [ARFrame](https://developer.apple.com/documentation/arkit/arframe) objetos.   Eles contêm imagem capturada por câmera, bem como a posição do mundo real estimada do dispositivo.

Vamos ser compor as imagens que estão sendo entregues pela câmera para nós com nosso conteúdo 3D e ajustar a câmera no UrhoSharp para corresponder as chances no local do dispositivo e posição.

O diagrama a seguir mostra o que está acontecendo `ArkitApp` classe:

[ ![Diagrama de classes e telas de ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png)

### <a name="rendering-the-frames"></a>Os quadros de renderização

A ideia é simple, combinar o vídeo saindo da câmera com nosso gráficos 3D para produzir a imagem combinada.     Obterá uma série dessas imagens capturadas em sequência, e nós serão misturados essa entrada com o cenário de Urho.

A maneira mais simples de fazer isso é inserir um [ `RenderPathCommand` ](https://developer.xamarin.com/api/type/Urho.RenderPathCommand/) em principal [ `RenderPath` ](https://developer.xamarin.com/api/type/Urho.RenderPath/).  Este é um conjunto de comandos que são executadas para desenhar um quadro simples.  Este comando preencherá o visor com qualquer textura que passamos a ele.    Fizemos isso no primeiro quadro que é o processo e a definição real é feita em th **ARRenderPath.xml** arquivo carregado neste momento.

No entanto, existem dois problemas para misturam esses dois mundos:


1. No iOS, texturas de GPU deve ter uma resolução que seja uma potência de dois, mas os quadros que obteremos da câmera não possuem resolução são a uma potência de dois, por exemplo: 1280x720.
2. Os quadros são codificados em [YUV](https://en.wikipedia.org/wiki/YUV) formato, representado por duas imagens - luma e croma.

Os quadros YUV vêm em duas resoluções diferentes.  uma imagem de 1280x720 que representa a luminosidade (basicamente uma imagem em escala de cinza) e a quantidade 640x360 menor para o componente chrominance:

![Imagem demonstrando combinando Y e componentes UV](urhosharp-images/image3.png)


Para desenhar uma imagem colorida completa usando OpenGL ES é necessário gravar um sombreador pequeno que usa luminosidade (componente Y) e chrominance (planos UV) de slots de textura.  Em UrhoSharp tenham nomes - "sDiffMap" e "sNormalMap" e convertê-los em formato RGB:

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

Para renderizar a textura que não tenha uma potência de dois resolução, precisamos definir Texture2D com os seguintes parâmetros:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Assim, somos capazes de renderizar imagens capturadas como um plano de fundo e renderizar qualquer cena acima dele mutante assustador como esse.

### <a name="adjusting-the-camera"></a>Ajustando a câmera

O `ARFrame` objetos também contêm a posição do dispositivo estimado.  Estamos Agora precisamos mover câmera jogo ARFrame - antes de ARKit não era um grande problema para acompanhar orientação do dispositivo (roll, tom e desvio) e processar um holograma fixado na parte superior do vídeo -, mas se você mover o dispositivo um pouco - hologramas será sincronizado.

Isso acontece porque sensores internos como giroscópio não são capazes de acompanhar movimentos, eles podem somente a aceleração.  Análises de ARKit cada recurso de quadro e extrai aponta para rastrear e, portanto, é capaz de envie-em um precisa transformam matriz que contém dados de movimentação e a rotação.

Por exemplo, isso é como podemos obter posição atual:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

Podemos usar `-row.Z` como ARKit usa um sistema de coordenadas direita.


### <a name="plane-detection"></a>Detecção de plano

ARKit é capaz de detectar planos horizontais e essa capacidade permite que você interaja com o mundo real, por exemplo, podemos pode colocar o mutante em uma tabela real ou floor. A maneira mais simples de fazer isso é usar o método HitTest (raycasting). Ele converte coordenadas de tela (0,5; 0,5 é o centro) para as coordenadas do mundo real (0; 0; 0 é o local do primeiro quadro).

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

![Figura animada alterando planos como move de exibição](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Iluminação realista

Dependendo das condições de iluminação do mundo real, a cena virtual deve ser mais clara ou escura para que corresponda melhor o seu ambiente. ARFrame contém uma propriedade LightEstimate que podemos usar para ajustar a luz ambiente Urho, isso é feito como este:


    var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
    var zone = Scene.GetComponent<Zone>();
    zone.AmbientColor = Color.White * ambientIntensity;


### <a name="beyond-ios---hololens"></a>Além do iOS - HoloLens

UrhoSharp [é executado em todos os principais sistemas operacionais](~/graphics-games/urhosharp/platform/index.md), portanto, você pode reutilizar o código existente em outro lugar.

HoloLens é uma das plataformas mais interessantes que ele é executado.   Isso significa que você pode alternar facilmente entre HoloLens para criar aplicativos de realidade aumentada awesome usando UrhoSharp e iOS.

Você pode encontrar a fonte MutantDemo em [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Links relacionados

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (com UrhoSharp) (amostra)](https://github.com/EgorBo/ARKitXamarinDemo)
