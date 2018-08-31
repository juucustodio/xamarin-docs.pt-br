---
title: Usando a ferramenta de Pipeline de MonoGame
description: A ferramenta de Pipeline de MonoGame é usada para criar e gerenciar projetos de conteúdo do MonoGame. Os arquivos em projetos de conteúdo são processados pela ferramenta de MonoGame Pipeline e enviados como arquivos de .xnb para uso em aplicativos CocosSharp e MonoGame.
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 347cb7e9d417f97cb6e8d78e67b1c76a378cd188
ms.sourcegitcommit: 7ffbecf4a44c204a3fce2a7fb6a3f815ac6ffa21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "34783280"
---
# <a name="using-the-monogame-pipeline-tool"></a>Usando a ferramenta de Pipeline de MonoGame

_A ferramenta de Pipeline de MonoGame é usada para criar e gerenciar projetos de conteúdo do MonoGame. Os arquivos em projetos de conteúdo são processados pela ferramenta de MonoGame Pipeline e enviados como arquivos de .xnb para uso em aplicativos CocosSharp e MonoGame._

A ferramenta de Pipeline MonoGame fornece um ambiente fácil de usar para converter arquivos de conteúdo em **.xnb** arquivos para uso em aplicativos CocosSharp e MonoGame. Para obter informações sobre pipelines de conteúdo e porque eles são úteis no desenvolvimento de jogos, consulte [esta introdução em pipelines de conteúdo](~/graphics-games/cocossharp/content-pipeline/introduction.md)

Este passo a passo abordará o seguinte:

 - Instalando a ferramenta de Pipeline de MonoGame
 - Criando um projeto de CocosSharp
 - Criando um projeto de conteúdo
 - Processamento de arquivos na ferramenta de Pipeline de MonoGame
 - Usando arquivos em tempo de execução

Este passo a passo usa um projeto de CocosSharp para demonstrar como **.xnb** arquivos podem ser carregados e usados em um aplicativo. Os usuários do MonoGame também poderão fazer referência a este passo a passo como CocosSharp e o MonoGame usam o mesmo **.xnb** arquivos de conteúdo.

O aplicativo concluído será exibido um sprite de único exibindo uma textura de um **.xnb** arquivo e um único rótulo exibindo uma fonte de sprite de uma **.xnb** arquivo:

![](walkthrough-images/image1.png "O aplicativo concluído será exibido um sprite de único exibindo uma textura de um arquivo .xnb")


## <a name="monogame-pipeline-tool-discussion"></a>Discussão da ferramenta de Pipeline MonoGame

A ferramenta de Pipeline MonoGame está disponível no Windows, OS X e Linux. Este passo a passo executará a ferramenta no Windows, mas ele pode ser seguido ao longo no Mac e Linux também. Para obter informações sobre como obter a ferramenta Configurar no máximo ou o Linux, consulte [nesta página](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/).

A ferramenta de Pipeline MonoGame é capaz de criar conteúdo para aplicativos iOS, mesmo quando executados em Windows, para que os desenvolvedores usando [Xamarin Mac Agent](~/ios/get-started/installation/windows/connecting-to-mac/index.md) poderão continuar a desenvolver no Windows.


## <a name="installing-the-monogame-pipeline-tool"></a>Instalando a ferramenta de Pipeline de MonoGame

Começaremos instalando o MonoGame, que inclui o Pipeline de conteúdo do MonoGame. Observe que o Pipeline de conteúdo MonoGame é um download separado para Mac. Todos os instaladores MonoGame podem ser encontrados na [página de Downloads de MonoGame](http://www.monogame.net/downloads/). Podemos, baixe o MonoGame para o Visual Studio, mas uma vez instalado o desenvolvedor pode usar MonoGame no Visual Studio para Mac também:

![](walkthrough-images/image2.png "Baixe o MonoGame para o Visual Studio, mas uma vez instalado o MonoGame de uso do desenvolvedor no Visual Studio para Mac muito")

Após o download, vamos executar por meio do instalador e aceite as opções de padrões. Depois que o instalador for concluído, a ferramenta de Pipeline MonoGame está instalada e pode ser encontrada na pesquisa do menu Iniciar:

![](walkthrough-images/image3.png "Depois que o instalador for concluído, a ferramenta de Pipeline MonoGame está instalada e pode ser encontrada na pesquisa do menu Iniciar")

Inicie a ferramenta de MonoGame de Pipeline:

![](walkthrough-images/image4.png "Inicie a ferramenta de Pipeline de MonoGame")

Depois que a ferramenta de Pipeline MonoGame estiver em execução, podemos começar a tornar nossos projetos de jogos e conteúdos.


## <a name="creating-an-empty-cocossharp-project"></a>Criando um projeto vazio do CocosSharp

A próxima etapa é criar um projeto de CocosSharp. É importante que podemos criar o projeto de CocosSharp primeiro para que nós podemos salvar nosso projeto de conteúdo na estrutura da pasta criada pelo projeto de CocosSharp. Para entender a estrutura de um projeto de CocosSharp, dê uma olhada a [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), que usará neste guia. No entanto, se você tiver um projeto de CocosSharp existente que você gostaria de adicionar conteúdo a, fique à vontade usar esse projeto em vez de BouncingGame.

Quando o projeto tiver sido criado, vamos executar-o para verificar que ele se baseia e que temos tudo configurado corretamente:

![](walkthrough-images/image5.png "Quando o projeto tiver sido criado, execute-o para verificar se o build e que tudo configurado corretamente")


## <a name="creating-a-content-project"></a>Criando um projeto de conteúdo

Agora que temos um projeto de jogo, podemos criar um projeto de MonoGame de Pipeline. Para fazer isso, no, selecione a ferramenta de Pipeline MonoGame **arquivo > Novo...**  e navegue até a pasta de conteúdo do seu projeto. Para o Android, a pasta está localizada em **[projeto root]\BouncingGame.Android\Assets\Content\**. Para iOS, a pasta está localizada em **[projeto root]\BouncingGame.iOS\Content\**.

Alterar o **nome do arquivo** ao **ContentProject** e clique no **salvar** botão:

![](walkthrough-images/image8.png "Altere o nome do arquivo para ContentProject e clique no botão Salvar")

Quando o projeto é criado, a ferramenta de Pipeline MonoGame exibirá informações sobre o projeto quando a raiz **ContentProject** item é selecionado:

![](walkthrough-images/image9.png "Quando o projeto é criado, a ferramenta de Pipeline MonoGame exibirá informações sobre o projeto quando o item de ContentProject raiz é selecionado")

Vamos examinar algumas das opções mais importantes para o projeto de conteúdo.


### <a name="output-folder"></a>Pasta de saída

Essa é a pasta (relativo ao projeto de conteúdo em si) em que a saída **.xnb** arquivos serão salvos. Para simplificar as coisas, vamos usar a mesma pasta para manter nossa entrada e arquivos de saída. Em outras palavras, alteraremos as **pasta de saída** ser **.\**  :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>Plataforma

Isso define a plataforma de destino para o conteúdo. Observe que isso é **Windows** por padrão, portanto, queremos alterar isso em nossa plataforma de destino que está **Android** (iOS ou se a seguir, juntamente com um projeto do iOS).

![](walkthrough-images/image11.png "Observe que esse é o Windows por padrão, portanto, altere isso para a plataforma de destino que é o Android ou iOS se seguindo junto com um projeto do iOS")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>Processamento de arquivos na ferramenta de Pipeline de MonoGame

Em seguida, estaremos adicionando conteúdo ao nosso **ContentProject**. Para este projeto, estaremos adicionando arquivos na raiz do projeto, mas projetos maiores normalmente serão organizar seu conteúdo em pastas.

Vamos adicionar dois arquivos ao nosso projeto:

 - Um **PNG** arquivo que será usado para desenhar um sprite. Esse arquivo pode [baixado aqui](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true).
 - Um **.spritefont** arquivo que será usado para desenhar texto na tela. A ferramenta de Pipeline de conteúdo oferece suporte à criação de novos arquivos de .spritefont, portanto, não há nenhum arquivo para download.


### <a name="adding-a-png-file"></a>Adicionando um arquivo. PNG

Para adicionar um **. PNG** arquivo ao projeto, podemos primeiro copiá-lo para o mesmo diretório que o projeto de pipeline, que tem o **.mgcb** extensão.

![](walkthrough-images/image12.png "Adicionar um arquivo. PNG ao projeto")

Em seguida, vamos adicionar o arquivo ao projeto do pipeline. Para fazer isso na ferramenta de MonoGame de Pipeline, selecione **Editar > Adicionar Item...** , selecione o **ball.png** do arquivo e clique em **abrir**. O arquivo agora será parte do projeto do conteúdo e, quando selecionado, exibe suas propriedades:

![](walkthrough-images/image13.png "O arquivo agora será parte do projeto do conteúdo e, quando selecionado, exibe suas propriedades")

Vamos sendo deixando todos os valores com seus padrões, pois nenhuma alteração é necessária para carregar o arquivo .xnb no CocosSharp. Podemos criar o arquivo, selecionando o **compilar > compilar** opção de menu, que iniciará uma compilação e exibir a saída sobre a compilação. Podemos verificar se a compilação funcionou corretamente verificando o **conteúdo** uma nova pasta **ball.xnb** arquivo:

![](walkthrough-images/image14.png "Verificar se a compilação funcionou corretamente, verificando a pasta de conteúdo para um novo arquivo ball.xnb")


### <a name="adding-a-spritefont-file"></a>Adicionando um arquivo .spritefont

Podemos criar arquivo .spritefont por meio da ferramenta de Pipeline MonoGame. CocosSharp requer fontes em uma **fontes** pasta e modelos de CocosSharp criar automaticamente uma pasta de fontes automaticamente. Podemos adicionar esta pasta para a ferramenta de Pipeline MonoGame selecionando **Editar > Adicionar > pasta existente...** . Navegue até a **conteúdo** pasta e selecione o **fontes** pasta e clique em **Okey**:

![](walkthrough-images/browsetofonts.png "Navegue até a pasta de conteúdo, selecione a pasta de fontes e clique Okey")

Para adicionar um novo arquivo .sprintefont, clique com botão direito na pasta de fontes e selecione **Adicionar > Novo Item...** , selecione o **descrição SpriteFont** opção, insira o nome **arial 36**e clique em **Okey**. CocosSharp requer muito específico de nomenclatura de arquivos de fonte – eles devem ser no formato [FontType]-[FontSize]. Se uma fonte não corresponder a esse formato de nomeação que ele não será carregado por CocosSharp em tempo de execução.

![](walkthrough-images/image15.png "Se uma fonte não corresponder a esse formato de nomeação que ele não será carregado por CocosSharp em tempo de execução")

O arquivo .spritefont é, na verdade, um arquivo XML que pode ser editado em qualquer editor de texto, incluindo o Visual Studio para Mac. As variáveis mais comuns editadas em um arquivo .spritefont são as `FontName` e `Size` propriedade:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

Vamos abrir o arquivo em qualquer editor de texto. Como nosso **arial 36.spritefont** nome sugere, deixaremos a `FontName` como `Arial` mas alterar o `Size` valor `36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>Usando arquivos em tempo de execução

Os arquivos .xnb agora são criados e prontas para serem usadas em nosso projeto. Estaremos adicionando os arquivos para o Visual Studio para Mac e em seguida, vamos adicionar código ao nosso `GameScene.cs` arquivo para carregar esses arquivos e exibi-los.


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>Adicionando arquivos .xnb ao Visual Studio para Mac

Primeiro vamos adicionar os arquivos ao nosso projeto. No Visual Studio para Mac, expandiremos a **BouncingGame.Android** do projeto, expanda o **ativos** pasta, clique com botão direito no **conteúdo** pasta e selecione **Adicionar > Adicionar arquivos...** Primeiro, vamos selecionar o **ball.xnb** compilamos anteriormente e clique em **abrir**. Em seguida, repita as etapas acima, mas adicione a **arial 36.xnb** arquivo. Vamos selecionar o **manter o arquivo no seu subdiretório atual** opção se o Visual Studio para Mac pergunta como adicionar o arquivo. Após a conclusão, os dois arquivos devem ser parte do nosso projeto:

![](walkthrough-images/image20.png "Após a conclusão, ambos os arquivos devem ser parte do projeto")


### <a name="adding-gamescenecs"></a>Adicionando **GameScene.cs**

Vamos criar uma classe chamada `GameScene,` que conterá nosso sprite e objetos de texto. Para fazer isso, clique com botão direito no **BouncingGame** (não BouncingGame.Android) do projeto e selecione **Adicionar > novo arquivo...** . Selecione o **gerais** categoria, selecione o **classe vazia** opção e, em seguida, insira o nome **GameScene**.

Depois de criado, vamos modificar o `GameScene.cs` arquivo para conter o seguinte código:


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

Nós não o discutirei o código acima como trabalhar com objetos visual, CocosSharp como CCSprite e CCLabelTtf é abordada a [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md).

Também precisamos adicionar código para carregar nosso recém-criado `GameScene`. Para fazer isso, vamos abrir o `GameAppDelegate.cs` arquivo (que está localizado na **BouncingGame** PCL) e modifique o `ApplicationDidFinishLaunching` método para que ele se parece com:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

Quando em execução, nosso jogo se parecerá com:

![](walkthrough-images/image1.png "Quando em execução, o jogo terá a seguinte aparência")


## <a name="summary"></a>Resumo

Este passo a passo mostrou como usar a ferramenta de MonoGame de Pipeline para criar arquivos de .xnb de um arquivo. PNG de entrada, bem como criar um novo arquivo .xnb de um arquivo .sprintefont recém-criado. Ele também mostrou como estruturar projetos CocosSharp para usar os arquivos de .xnb e como carregar esses arquivos em tempo de execução.

## <a name="related-links"></a>Links relacionados

- [Downloads de MonoGame](http://www.monogame.net/downloads/)
- [Documentação do MonoGame de Pipeline](http://www.monogame.net/documentation/?page=Pipeline)
- [Iniciar projeto BouncingGame para Android (amostra)](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [ball.PNG gráfico (amostra)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
