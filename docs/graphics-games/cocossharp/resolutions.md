---
title: Manipulando várias resoluções CocosSharp
description: Este guia mostra como trabalhar com CocosSharp para desenvolver jogos exibem corretamente em dispositivos de resoluções diferentes.
ms.topic: article
ms.prod: xamarin
ms.assetid: 859ABF98-2646-431A-A4A8-3E7E48DA5A43
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 772b0d6408a5ba438c5eb0be04a9b549e29b40f9
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="handling-multiple-resolutions-in-cocossharp"></a>Manipulando várias resoluções CocosSharp

_Este guia mostra como trabalhar com CocosSharp para desenvolver jogos exibem corretamente em dispositivos de resoluções diferentes._

CocosSharp fornece métodos para padronização dimensões de objeto em seu jogo, independentemente do número físico de pixels na tela do dispositivo. Tradicionalmente, jogos desenvolvidos para PCs e consoles de jogos podem ter como alvo uma única solução. Jogos modernos – e especialmente jogos para dispositivos móveis – devem ser criados para acomodar uma ampla variedade de dispositivos. Este guia mostra como padronizar CocosSharp jogos, independentemente das características de resolução da tela física.

É o comportamento de resolução padrão de CocosSharp corresponder pixels físicos com coordenadas do jogo. A tabela a seguir mostra como vários dispositivos geraria uma entidade gráfica ambiente de plano de fundo com largura e altura de 368 x 240. A primeira linha é tecnicamente não um dispositivo real, mas em vez disso, a renderização esperada da entidade gráfica, independentemente de resolução do dispositivo:


| **Dispositivo** | **Resolução de vídeo** | **Captura de tela de exemplo** |
|--- | --- |--- |
|Exibição desejada|368 x 240 (com barras pretas para taxa de proporção)| ![368 x 240 (com barras pretas para taxa de proporção)](resolutions-images/image1.png) |
|iPhone 4s|960x640| ![iPhone 4s 960x640](resolutions-images/image2.png) |
|iPhone 6 Plus|1920x1080| ![iPhone 6 Plus 1920 x 1080](resolutions-images/image3.png) |

Este documento aborda como usar CocosSharp para corrigir o problema mostrado na tabela acima. Ou seja, abordaremos como fazer com que qualquer dispositivo renderizar como mostrado na primeira linha – independentemente de resolução da tela.


## <a name="working-with-setdesignresolutionsize"></a>Trabalhando com SetDesignResolutionSize

O `CCScene` classe normalmente é usada como o contêiner raiz para todos os objetos visuais, mas ele também fornece um método estático chamado `SetDesignResolutionSize` para especificar o tamanho padrão para todas as cenas. Em outras palavras `SetDesignResolutionSize` método permite que os desenvolvedores a desenvolver jogos que serão exibido corretamente em uma variedade de soluções de hardware. Os modelos de projeto CocosSharp usam este método para definir o tamanho padrão do projeto como 1024 x 768, conforme é mostrado no código a seguir:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```

Você pode alterar o `desiredWidth` e `desiredHeight` variáveis acima para modificar a exibição para corresponder à resolução desejada do jogo. Por exemplo, o código acima pode ser modificado como a seguir para exibir o plano de fundo 368 x 240 como tela inteira:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");
    CCSize windowSize = mainWindow.WindowSizeInPixels;
    float desiredWidth = 368.0f;
    float desiredHeight = 240.0f;
    
    // This will set the world bounds to(0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDesignResolutionSize (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    ...
```


## <a name="ccsceneresolutionpolicy"></a>CCSceneResolutionPolicy

`SetDesignResolutionSize` permite especificar como a janela do jogo ajusta a resolução desejada. As seções a seguir demonstram como uma imagem de 500 x 500 é exibida com diferentes `CCSceneResolutonPolicy` valores passados para o `SetDesignResolutionSize` método. Os valores a seguir são fornecidos pelo `CCSceneResolutionPolicy` enum:

 - `ShowAll` – Exibe a resolução solicitada inteira, mantendo a taxa de proporção.
 - `ExactFit` – Exibe a resolução solicitada inteira, mas não manter a taxa de proporção.
 - `FixedWidth` – Exibe a largura solicitada inteira, mantendo a taxa de proporção.
 - `FixedHeight` – Exibe a altura solicitada inteira, mantendo a taxa de proporção.
 - `NoBorder` – Exibe a altura inteira ou largura inteira, mantendo a taxa de proporção. Se a taxa de proporção do dispositivo for maior que a taxa de proporção da resolução desejada, toda a largura é mostrada. Se a taxa de proporção do dispositivo for menor que a taxa de proporção da resolução desejada, toda a altura é mostrada.
 - `Custom` – A exibição depende do `Viewport` propriedade `CCScene`.

Todas as capturas de tela são produzidas no iPhone 4s resolução (960x640) na orientação paisagem e usam essa imagem:

![](resolutions-images/image4.png "Todas as capturas de tela são produzidas no iPhone 4s resolução 960x640 na orientação paisagem e usam essa imagem")


### <a name="ccsceneresolutionpolicyshowall"></a>CCSceneResolutionPolicy.ShowAll

`ShowAll` Especifica que a resolução do jogo inteira será visível na tela, mas pode exibir *aplicação do formato Letterbox* (barras pretas) para ajustar a taxas de proporção diferentes. Essa política geralmente é usada como ela garante que toda a exibição de jogo será exibida na tela sem qualquer distorção.

Exemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Aplicação do formato Letterbox é visível para a esquerda e direita da imagem para levar em conta a taxa de proporção física sendo maior que a resolução desejada:

![](resolutions-images/image5.png "Aplicação do formato Letterbox é visível para a esquerda e direita da imagem para levar em conta a taxa de proporção física sendo maior que a resolução desejada")


### <a name="ccsceneresolutionpolicyexactfit"></a>CCSceneResolutionPolicy.ExactFit

`ExactFit` Especifica que a resolução do jogo inteira será visível na tela com nenhuma aplicação do formato Letterbox. A área visível pode ser distorcida (taxa de proporção não pode ser mantida) de acordo com a taxa de proporção de hardware.

Exemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ExactFit);
```

Nenhuma aplicação do formato Letterbox estiver visível, mas uma vez que a resolução do dispositivo é retangular o modo de jogo é distorcido:

![](resolutions-images/image6.png "Nenhuma aplicação do formato Letterbox é visível, mas uma vez que a resolução do dispositivo é retangular o modo de jogo é distorcida.")


### <a name="ccsceneresolutionpolicyfixedwidth"></a>CCSceneResolutionPolicy.FixedWidth

`FixedWidth` Especifica que a largura da exibição corresponderá o valor de largura passado para `SetDesignResolutionSize`, mas a altura de exibição está sujeito à taxa de proporção do dispositivo físico. O valor da altura passado para `SetDesignResolutionSize` é ignorado, pois ele será calculado em tempo de execução com base na taxa de proporção do dispositivo físico. Isso significa que a altura calculada pode ser menor do que a altura desejada (o que resulta em partes da exibição jogo sendo fora da tela), ou da altura calculada pode ser maior que a altura desejada (o que resulta em mais o modo de jogo que está sendo exibido). Como isso pode resultar em mais do jogo que está sendo exibido, em seguida, ele pode aparecer como se a aplicação do formato Letterbox ocorreu; No entanto, o espaço extra não serão necessariamente preto se qualquer objeto visual aparece nele. 

Exemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedWidth);
```

IPhone 4s tem uma taxa de proporção de 3:2, portanto, a altura calculada é aproximadamente 333 unidades:

![](resolutions-images/image7.png "IPhone 4s tem uma taxa de proporção de 3:2, portanto, a altura calculada é aproximadamente 333 unidades")


### <a name="ccsceneresolutionpolicyfixedheight"></a>CCSceneResolutionPolicy.FixedHeight

Conceitualmente, `FixedHeight` se comporta da mesma forma que `FixedWidth` – o jogo obedecerão passado para o valor da altura `SetDesignResolutionSize,` mas calculará a largura em tempo de execução com base na resolução física. Como mencionado acima, isso significa que a largura exibida ser menor ou maior que a largura desejada, resultando em parte do jogo que está sendo desativado tela ou mais do jogo que está sendo exibido, respectivamente.

Exemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

Desde que a captura de tela a seguir foi criada com o aplicativo em execução no modo paisagem, a largura calculada é maior que 500 – especificamente 750. Essa política mantém o valor de 0 X alinhado à esquerda, para que a resolução extra é visível no lado direito da tela.

![](resolutions-images/image8.png "Essa política mantém alinhado à esquerda, o valor de X de 0 para a resolução extra é visível no lado direito da tela")


### <a name="ccsceneresolutionpolicynoborder"></a>CCSceneResolutionPolicy.NoBorder

`NoBorder` tenta exibir o aplicativo com nenhuma aplicação do formato letterbox, mantendo a taxa de proporção original (sem distorção). Se a taxa de proporção da resolução solicitado corresponde a taxa de proporção do dispositivo físico, sem recorte ocorrerá. Se não corresponderem, taxas de proporção de recorte, em seguida, irá ocorrer.

Exemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.FixedHeight);
```

A captura de tela a seguir exibe as partes superior e inferior da exibição recortado, enquanto todos os 500 pixels de largura de exibição são exibidos:

![](resolutions-images/image9.png "Esta captura de tela exibe as partes superior e inferior da tela recortado, enquanto todos os 500 pixels de largura de exibição são exibidos")


### <a name="ccsceneresolutionpolicycustom"></a>CCSceneResolutionPolicy.Custom

`Custom` permite que cada `CCScene` para especificar seu próprio visor personalizado em relação a resolução especificada no `SetDesignResolutionSize`.

Nos bastidores definem seus `Viewport` propriedade criando um `CCViewport`, que por sua vez é construído com uma `CCRect`. Para obter mais informações sobre `CCViewport` e `CCRect` consulte o [documentação da API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/). No contexto de criação de um `CCViewport` o `CCRect` especificam parâmetros do construtor (em ordem):

 - x – o valor de deslocamento horizontal do visor, onde cada unidade representa uma largura de tela inteira. Por exemplo, usando um valor de .5f resulta na cena deslocada para a direita pela metade da largura da tela.
 - y – o valor para o deslocamento vertical do visor, onde cada unidade representa uma altura da tela inteira. Por exemplo, usando um valor de .5f resultados na cena deslocada para baixo pela metade da altura da tela.
 - largura – parte horizontal cena deve ocupar. Por exemplo, usando um valor de 1 / 3.0 f resulta na cena horizontalmente que ocupam um terço da tela.
 - altura – parte vertical cena deve ocupar. Por exemplo, usando um valor de 1 / 3.0 f resulta na cena verticalmente que ocupam um terço da tela.

Exemplo de código:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.Custom);
...
float horizontalDisplayPortion = 2 / 3.0f;
float verticalDisplayPortion = 1 / 2.0f;
float displayOffsetInScreenWidths = 0;
float displayOffsetInScreenHeights = .5f;
var rectangle = new CCRect (
    displayOffsetInScreenWidths, 
    displayOffsetInScreenHeights, 
    horizontalDisplayPortion, 
    verticalDisplayPortion);
scene.Viewport = new CCViewport (rectangle);
```

O código acima resulta no seguinte:

![](resolutions-images/image10.png "O código acima resulta na captura de tela")


## <a name="defaulttexeltocontentsizeratio"></a>DefaultTexelToContentSizeRatio

O `DefaultTexelToContentSizeRatio` simplifica o uso de texturas de resolução mais alta em dispositivos com telas de resolução mais altas. Especificamente, essa propriedade permite jogos usar ativos de resolução mais alta sem a necessidade de alterar o tamanho ou o posicionamento dos elementos visuais. 

Por exemplo, um jogo pode incluir um ativo de arte para um caractere de jogo que é 200 pixels de altura e a tela do jogo (conforme especificado por `SetDesignResolutionSize`) pode ser 400 pixels de altura. Nesse caso, o caractere ocupará metade da tela. No entanto, se um ativo de 400 pixels de altura foram usados para o caractere para dispositivos de alta resolução, o caractere seria ocupa toda a altura da exibição. Se a intenção é manter o caractere do mesmo tamanho para tirar proveito dos dispositivos de resolução mais altos, um código adicional é necessário para manter a entidade gráfica caractere na metade da altura da tela.

O exemplo simples apresentado acima representa um problema comum no desenvolvimento de jogos – adicionando ativos de tamanho maior sem a necessidade de desenvolvedor para executar o redimensionamento de cada elemento visual de acordo com a resolução do dispositivo. `DefaultTexelToContentSizeRatio` uma propriedade global é usada para redimensionar os elementos visuais. Esse valor redimensiona todos os elementos visuais de um determinado tipo, o valor atribuído.

Essa propriedade está presente nas seguintes classes: 

 - `CCApplication`
 - `CCSprite`
 - `CCLabelTtf`
 - `CCLabelBMFont`
 - `CCTMXLayer`

O CocosSharp do Visual Studio para o conjunto de modelos de Mac `CCSprite.DefaultTexelToContentSizeRatio` acordo com a largura do dispositivo como um exemplo de como ele pode ser usado. O código a seguir está contido no `CCApplicationDelegate.ApplicationDidFinishLaunching`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
           
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```


### <a name="defaulttexeltocontentsizeratio-example"></a>Exemplo de DefaultTexelToContentSizeRatio

Para ver como `DefaultTexelToContentSizeRatio` afeta o tamanho do visual de elementos, considere o código apresentado acima:


```csharp
CCScene.SetDesignResolutionSize (500.0f, 500.0f, CCSceneResolutionPolicy.ShowAll);
```

Que resultará na imagem a seguir usando uma textura de 500 x 500:

![](resolutions-images/image5.png "Que resultará nesta imagem usando uma textura de 500 x 500")

IPad Retina é executado em uma resolução física de 2048 x 1536. Isso significa que o jogo conforme exibido acima seria Alongar 500 pixels em pixels físicos 1536. Jogos podem aproveitar essa resolução extra criando que são normalmente conhecido como *hd* ativos – ativos que são projetados para funcionar em telas de resolução mais altas. Por exemplo, o jogo pode usar uma versão de hd desse Textura dimensionada 1000 x 1000. No entanto, usando a maior imagem resultaria no `CCSprite` ter uma largura e altura de unidades de 1000. Como o nosso jogo sempre exibirá 500 unidades (devido ao `SetDesignResolutioSize` chamar), nossa entidade gráfica de 1000 x 1000 será muito grande (exibe apenas parte inferior esquerda dele):

![](resolutions-images/image11.png "Desde que o jogo sempre exibirá 500 unidades devido a chamada SetDesignResolutioSize, a entidade gráfica 1000 x 1000 seria muito grande que exibe apenas parte inferior esquerda dele")

Podemos definir `CCSprite.DefaultTexelToContentSizeRatio` para levar em conta a textura de 1000 x 1000 sendo duas vezes como largura e altura como a textura de 500 x 500 original:


```csharp
CCSprite.DefaultTexelToContentSizeRatio = 2;
```

Agora quando executamos o jogo a textura de 1000 x 1000 será totalmente visível:

![](resolutions-images/image12.png "Agora quando executamos o jogo a textura de 1000 x 1000 será totalmente visível")


### <a name="defaulttexeltocontentsizeratio-details"></a>Detalhes de DefaultTexelToContentSizeRatio

O `DefaultTexelToContentSizeRatio` é de propriedade `static,` que significa que todos os objetos no aplicativo compartilha o mesmo valor. A abordagem típica para jogos com feitas para diferentes resoluções de ativos é contém um conjunto completo de ativos para cada categoria de resolução. Por padrão o CocosSharp do Visual Studio para modelos de Mac fornecer **ld** e **hd** pastas de ativos, que pode ser útil para dar suporte a dois conjuntos de texturas de jogos. Uma pasta de conteúdo de exemplo com conteúdo pode se parecer com:

![](resolutions-images/image13.png "Uma pasta de conteúdo de exemplo com conteúdo pode se parecer com")

Observe que o modelo padrão também inclui um código para adicionar condicionalmente para o aplicativo `ContentSearchPaths`:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }
    ...           
}
```

Isso significa que o aplicativo irá procurar por arquivos nos caminhos de acordo com se ele está sendo executado no modo de resolução regular ou de alta resolução. Por exemplo, se o **hd** e **ld** pastas contêm um arquivo chamado **background.png** , em seguida, executará o código a seguir e selecione o arquivo correto para a resolução:


```csharp
backgroundSprite  = new CCSprite ("background");
```


## <a name="summary"></a>Resumo

Este artigo aborda como criar jogos que exibem corretamente, independentemente da resolução do dispositivo. Ele mostra exemplos de como usar diferentes `CCSceneResolutionPolicy` valores para redimensionar o jogo de acordo com a resolução do dispositivo. Ele também fornece um exemplo de como `DefaultTexelToContentSizeRatio` pode ser usado para acomodar vários conjuntos de conteúdo sem a necessidade de elementos visuais para ser redimensionado individualmente.

## <a name="related-links"></a>Links relacionados

- [Introdução de CocosSharp](~/graphics-games/cocossharp/index.md)
- [Documentação da API do CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
