---
title: Usando ARKit com UrhoSharp no Xamarin. iOS
description: Este documento descreve como configurar um aplicativo ARKit no Xamarin. iOS, em seguida, examina como os quadros são renderizados, como ajustar a câmera, como detectar planos, como trabalhar com iluminação e muito mais. Ele também aborda o UrhoSharp e a gravação de código para o HoloLens.
ms.prod: xamarin
ms.assetid: 877AF974-CC2E-48A2-8E1A-0EF9ABF2C92D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/01/2017
ms.openlocfilehash: 69ddb42f6bf51ec78d9735346c44efa94fb9c418
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68508770"
---
# <a name="using-arkit-with-urhosharp-in-xamarinios"></a>Usando ARKit com UrhoSharp no Xamarin. iOS

Com a introdução do [ARKit](https://developer.apple.com/arkit/), a Apple tornou simples para os desenvolvedores a criação de aplicativos de realidade aumentada. O ARKit pode acompanhar a posição exata do seu dispositivo e detectar várias superfícies no mundo, e então é até que o desenvolvedor misture os dados provenientes do ARKit em seu código.

O [UrhoSharp](~/graphics-games/urhosharp/index.md) fornece uma API 3D abrangente e fácil de usar que você pode usar para criar aplicativos 3D.   Ambos podem ser mesclados, ARKit para fornecer as informações físicas sobre o mundo e Urho para renderizar os resultados.

Esta página explica como conectar esses dois mundos juntos para criar ótimos aplicativos de realidade aumentada.

## <a name="the-basics"></a>Noções básicas

O que queremos fazer é apresentar conteúdo 3D em cima do mundo, como visto pelo iPhone/iPad.   A ideia é misturar o conteúdo proveniente da câmera do dispositivo com o conteúdo 3D e, à medida que o usuário do dispositivo se movimenta pela sala para garantir que o objeto 3D se comporte como faz parte dessa sala, isso é feito ancorando os objetos nesse mundo.

![Figura animada em ARKit](urhosharp-images/image1.gif)

Usaremos a biblioteca Urho para carregar nossos ativos 3D e colocá-los no mundo, e usaremos o ARKit para obter o fluxo de vídeo proveniente da câmera, bem como o local do telefone do mundo.   À medida que o usuário se move com seu telefone, usaremos as alterações no local para atualizar o sistema de coordenadas que o mecanismo Urho está exibindo.

Dessa forma, quando você coloca um objeto no espaço 3D e o usuário se move, o local do objeto 3D reflete o local e o local onde ele foi colocado.

## <a name="setting-up-your-application"></a>Configurando seu aplicativo

### <a name="ios-application-launch"></a>Inicialização do aplicativo iOS

Seu aplicativo IOS precisa criar e iniciar o conteúdo 3D, você faz isso criando uma subclasse de implementação do `Urho.Application` e fornece o código de instalação substituindo o `Start` método.  É aí que sua cena é preenchida com dados, os manipuladores de eventos são configurados e assim por diante.

Apresentamos uma `Urho.ArkitApp` classe que as `Urho.Application` subclasses e em seu `Start` método realizam o trabalho pesado.   Tudo o que você precisa fazer em seu aplicativo Urho existente é alterar a classe base para ser do `Urho.ArkitApp` tipo e você tem um aplicativo que executará sua cena do Urho no mundo.

### <a name="the-arkitapp-class"></a>A classe ArkitApp

Essa classe fornece um conjunto de padrões convenientes, uma cena com alguns objetos-chave, bem como o processamento de eventos ARKit à medida que eles são entregues pelo sistema operacional.

A instalação ocorre no `Start` método virtual.   Ao substituir esse método em sua subclasse, você precisa se certificar de encadear o seu pai usando `base.Start()` em sua própria implementação.

O `Start` método configura a cena, o visor, a câmera e uma luz direcional e as superfícies como propriedades públicas:

- r `Scene` para armazenar seus objetos,
- uma direção `Light` com sombras e cujo local está disponível por meio da `LightNode` Propriedade
- um `Camera` cujos componentes são atualizados quando o ARKit entrega uma atualização para o aplicativo e
- uma `ViewPort` exibição dos resultados.

### <a name="your-code"></a>Seu código

Em seguida, você precisará subclasse da `ArkitApp` classe e substituir o `Start` método.   A primeira coisa que seu método deve fazer é encadear para o `ArkitApp.Start` chamando. `base.Start()`  Depois disso, você pode usar qualquer uma das propriedades configuradas por ArkitApp para adicionar seus objetos à cena, personalizar as luzes, as sombras ou os eventos que você deseja manipular.

O exemplo de ARKit/UrhoSharp carrega um caractere animado com texturas e reproduz a animação, com a seguinte implementação:

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

E isso é realmente tudo o que você precisa fazer neste ponto para que seu conteúdo 3D seja exibido na realidade aumentada.

O Urho usa formatos personalizados para animações e modelos 3D, portanto, você precisa exportar seus ativos para esse formato.   Você pode usar ferramentas como o [suplemento Urho3D Blender](https://github.com/reattiva/Urho3D-Blender) e o [UrhoAssetImporter](https://github.com/EgorBo/UrhoAssetImporter) que podem converter esses ativos de formatos populares, como dbx, Dae, obj, Blend, 3D-Max no formato exigido pelo Urho.

Para saber mais sobre como criar aplicativos 3D usando o Urho, visite o guia [introdução ao UrhoSharp](~/graphics-games/urhosharp/introduction.md) .

## <a name="arkitapp-in-depth"></a>ArkitApp em profundidade

> [!NOTE]
> Esta seção destina-se a desenvolvedores que desejam personalizar a experiência padrão do UrhoSharp e do ARKit ou desejam obter uma visão mais aprofundada sobre como a integração funciona.   Não é necessário ler esta seção.

A API do ARKit é bem simples, você cria e configura um objeto [ARSession](https://developer.apple.com/documentation/arkit/arsession) que, em seguida, inicia a entrega de objetos [ARFrame](https://developer.apple.com/documentation/arkit/arframe) .   Elas contêm a imagem capturada pela câmera, bem como a posição real estimada do dispositivo.

Vamos compor as imagens que estão sendo entregues pela câmera para nós com nosso conteúdo 3D e ajustar a câmera em UrhoSharp para corresponder às chances de localização e posição do dispositivo.

O diagrama a seguir mostra o que está ocorrendo `ArkitApp` na classe:

[![Diagrama de classes e telas no ArkitApp](urhosharp-images/image2.png)](urhosharp-images/image2.png#lightbox)

### <a name="rendering-the-frames"></a>Renderizando os quadros

A ideia é simples, combine o vídeo proveniente da câmera com nossos gráficos 3D para produzir a imagem combinada.     Obteremos uma série dessas imagens capturadas em sequência e misturaremos essa entrada com a cena Urho.

A maneira mais simples de fazer isso é inserir um `RenderPathCommand` no principal. `RenderPath`  Esse é um conjunto de comandos que são executados para desenhar um único quadro.  Esse comando preencherá o visor com qualquer textura que passamos para ele.    Configuramos isso no primeiro quadro que é processado, e a definição real é feita no arquivo th **ARRenderPath. xml** que é carregado neste momento.

No entanto, enfrentamos dois problemas para misturar esses dois mundos:


1. No iOS, as texturas de GPU devem ter uma resolução que seja uma potência de dois, mas os quadros que obteremos da câmera não têm resolução que sejam uma potência de dois, por exemplo: 1280x720.
2. Os quadros são codificados no formato [YUV](https://en.wikipedia.org/wiki/YUV) , representados por duas imagens-Luma e croma.

Os quadros YUV vêm em duas resoluções diferentes.  uma imagem 1280x720 que representa a luminância (basicamente uma imagem de escala cinza) e 640 x 360 muito menores para o componente crominância:

![Imagem que demonstra a combinação de componentes Y e UV](urhosharp-images/image3.png)


Para desenhar uma imagem colorida completa usando OpenGL ES, temos que escrever um sombreador pequeno que usa a luminância (componente Y) e crominância (planos UV) dos slots de textura.  Em UrhoSharp, eles têm nomes-"sDiffMap" e "sNormalMap" e os convertem em formato RGB:

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

Para renderizar a textura que não tem uma potência de duas resoluções, precisamos definir Texture2D com os seguintes parâmetros:

```chsarp
// texture for UV-plane;
cameraUVtexture = new Texture2D();
cameraUVtexture.SetNumLevels(1);
cameraUVtexture.SetSize(640, 360, Graphics.LuminanceAlphaFormat, TextureUsage.Dynamic);
cameraUVtexture.FilterMode = TextureFilterMode.Bilinear;
cameraUVtexture.SetAddressMode(TextureCoordinate.U, TextureAddressMode.Clamp);
cameraUVtexture.SetAddressMode(TextureCoordinate.V, TextureAddressMode.Clamp);
```

Portanto, podemos renderizar imagens capturadas como um plano de fundo e renderizar qualquer cena acima dela como essa Mutant assustadora.

### <a name="adjusting-the-camera"></a>Ajustando a câmera

Os `ARFrame` objetos também contêm a posição estimada do dispositivo.  Agora precisamos mover a câmera de jogo ARFrame-antes de ARKit, não era muito importante controlar a orientação do dispositivo (rolo, pitch e desvio) e renderizar um holograma fixado sobre o vídeo, mas se você mover seu dispositivo, um pouco de holograma será descompasso.

Isso acontece porque os sensores internos, como giroscópio, não conseguem controlar os movimentos, eles só podem acelerar.  O ARKit analisa cada quadro e extrai pontos de recursos para acompanhar e, portanto, é capaz de nos fornecer uma matriz de transformação precisa que contenha dados de movimentação e rotação.

Por exemplo, é assim que podemos obter a posição atual:

```csharp
var row = arCamera.Transform.Row3;
CameraNode.Position = new Vector3(row.X, row.Y, -row.Z);
```

`-row.Z` Usamos porque o ARKit usa um sistema de coordenadas à direita.


### <a name="plane-detection"></a>Detecção de plano

O ARKit é capaz de detectar planos horizontais e essa capacidade permite que você interaja com o mundo real, por exemplo, podemos posicionar o Mutant em uma tabela real ou em um andar. A maneira mais simples de fazer isso é usar o método HitTest (raycasting). Ele converte as coordenadas da tela (0,5; 0.5 é o centro) nas coordenadas do mundo real (0; 0; 0 é o local do primeiro quadro).

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

Agora, podemos inserir o Mutant em uma superfície horizontal dependendo de onde na tela do dispositivo que toquemos:

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

![Figura animada alterando planos conforme as movimentações de exibição](urhosharp-images/image4.gif)

### <a name="realistic-lighting"></a>Iluminação realista

Dependendo das condições de iluminação do mundo real, a cena virtual deve ser mais clara ou mais escura para corresponder melhor ao seu ambiente. ARFrame contém uma propriedade LightEstimate que podemos usar para ajustar a luz de ambiente de Urho, isso é feito da seguinte maneira:

```csharp
var ambientIntensity = (float) frame.LightEstimate.AmbientIntensity / 1000f;
var zone = Scene.GetComponent<Zone>();
zone.AmbientColor = Color.White * ambientIntensity;
```

### <a name="beyond-ios---hololens"></a>Além do iOS-HoloLens

O UrhoSharp [é executado em todos os principais sistemas operacionais](~/graphics-games/urhosharp/platform/index.md), para que você possa reutilizar o código existente em outro lugar.

O HoloLens é uma das plataformas mais empolgantes em que ele é executado.   Isso significa que você pode alternar facilmente entre iOS e HoloLens para criar aplicativos de realidade aumentada incríveis usando o UrhoSharp.

Você pode encontrar a fonte MutantDemo em [github.com/EgorBo/ARKitXamarinDemo](https://github.com/EgorBo/ARKitXamarinDemo).


## <a name="related-links"></a>Links relacionados

- [UrhoSharp](~/graphics-games/urhosharp/index.md)
- [ARKitXamarinDemo (com UrhoSharp) (exemplo)](https://github.com/EgorBo/ARKitXamarinDemo)
