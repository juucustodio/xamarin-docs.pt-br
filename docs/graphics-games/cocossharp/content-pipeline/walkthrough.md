---
title: Usando a ferramenta de Pipeline de MonoGame
description: "A ferramenta MonoGame Pipeline é usada para criar e gerenciar projetos de conteúdo MonoGame. Os arquivos em projetos de conteúdo são processados pela ferramenta Monogame Pipeline e gerados como arquivos .xnb para uso em aplicativos CocosSharp e MonoGame."
ms.topic: article
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: df3692777eaa0791385c9ef3d114fbc8a9ab752e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="using-the-monogame-pipeline-tool"></a>Usando a ferramenta de Pipeline de MonoGame

_A ferramenta MonoGame Pipeline é usada para criar e gerenciar projetos de conteúdo MonoGame. Os arquivos em projetos de conteúdo são processados pela ferramenta Monogame Pipeline e gerados como arquivos .xnb para uso em aplicativos CocosSharp e MonoGame._

A ferramenta de MonoGame Pipeline fornece um ambiente fácil de usar para converter arquivos de conteúdo em **.xnb** arquivos para uso em aplicativos CocosSharp e MonoGame. Para obter informações sobre pipelines de conteúdo e porque eles são úteis no desenvolvimento de jogos, consulte [esta introdução em Pipelines de conteúdo](~/graphics-games/cocossharp/content-pipeline/introduction.md)

Este passo a passo aborda o seguinte:

 - Instalando a ferramenta MonoGame Pipeline
 - Criando um projeto de CocosSharp
 - Criando um projeto de conteúdo
 - Processamento de arquivos na ferramenta de Pipeline de MonoGame
 - Usando arquivos em tempo de execução

Este passo a passo usa um projeto CocosSharp para demonstrar como **.xnb** arquivos podem ser carregados e usados em um aplicativo. Os usuários do MonoGame também poderá fazer referência a este passo a passo que CocosSharp e MonoGame usam o mesmo **.xnb** arquivos de conteúdo.

O aplicativo concluído será exibido em uma única entidade de gráfica exibindo uma textura de um **.xnb** de arquivo e um rótulo único exibindo uma fonte de entidade gráfica de um **.xnb** arquivo:

![](walkthrough-images/image1.png "O aplicativo concluído será exibido em uma única entidade de gráfica exibindo uma textura de um arquivo .xnb")


# <a name="monogame-pipeline-platform-discussion"></a>Pipeline de MonoGame discussão de plataforma

A ferramenta de Pipeline de MonoGame está disponível no Windows, OS X e Linux. Este passo a passo executará a ferramenta no Windows, mas ele pode ser seguido ao longo de Mac e Linux também. Para obter informações sobre como obter a ferramenta Configurar no máximo ou o Linux, consulte [essa página](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/).

A ferramenta MonoGame Pipeline é capaz de criar conteúdo para aplicativos iOS mesmo quando executados no Windows, para que os desenvolvedores usando [Xamarin Mac Agent](~/ios/get-started/installation/windows/connecting-to-mac/index.md) poderão continuar a desenvolver no Windows.


# <a name="installing-the-monogame-pipeline-tool"></a>Instalando a ferramenta MonoGame Pipeline

Começaremos instalando MonoGame, que inclui o Pipeline de conteúdo MonoGame. Observe que o Pipeline de conteúdo MonoGame é um download separado para Mac. Todos os instaladores de MonoGame podem ser encontradas no [página Downloads MonoGame](http://www.monogame.net/downloads/). Podemos irá baixar MonoGame para Visual Studio, mas depois de instalar o desenvolvedor pode usar MonoGame no Visual Studio para Mac muito:

![](walkthrough-images/image2.png "Baixar MonoGame para Visual Studio, mas depois de instalar o uso de desenvolvedor MonoGame no Visual Studio para Mac muito")

Após o download, vamos percorrer o instalador e aceite as opções padrão. Depois que o instalador termina, a ferramenta MonoGame Pipeline está instalada e pode ser encontrada na pesquisa do menu Iniciar:

![](walkthrough-images/image3.png "Depois que o instalador termina, a ferramenta MonoGame Pipeline está instalada e pode ser encontrada na pesquisa do menu Iniciar")

Inicie a ferramenta MonoGame Pipeline:

![](walkthrough-images/image4.png "Inicie a ferramenta de Pipeline de MonoGame")

Depois que estiver executando a ferramenta MonoGame Pipeline, podemos começar tornar nossos projetos do jogos e conteúdos.


# <a name="creating-an-empty-cocossharp-project"></a>Criando um projeto vazio do CocosSharp

A próxima etapa é criar um projeto CocosSharp. É importante que criaremos o projeto CocosSharp primeiro para que poderemos salvar nosso projeto de conteúdo na estrutura de pastas criada pelo projeto CocosSharp. Para obter informações sobre como criar um novo projeto, consulte [guia BouncingGame](~/graphics-games/cocossharp/first-game/part1.md). Para este guia, estamos criando um projeto chamado BouncingGame, mas qualquer projeto CocosSharp existente funcionará bem. Se você tiver um projeto CocosSharp existente que você gostaria de adicionar conteúdo, fique à vontade para usar o projeto em vez do projeto BouncingGame.

Quando o projeto tiver sido criado, vamos executar-o para verificar que ele cria e que temos tudo configurado corretamente:

![](walkthrough-images/image5.png "Quando o projeto tiver sido criado, execute-o para verificar que ele cria e que tudo configurado corretamente")


# <a name="creating-a-content-project"></a>Criando um projeto de conteúdo

Agora que temos um projeto de jogo, podemos criar um projeto MonoGame Pipeline. Para fazer isso, no, selecione Pipeline de MonoGame ferramenta **arquivo > Novo...**  e navegue até a pasta de conteúdo do projeto. Para o Android, a pasta está localizada em **[projeto root]\BouncingGame.Android\Assets\Content\**. Para iOS, a pasta está localizada em **[projeto root]\BouncingGame.iOS\Content\**.

Alterar o **nome de arquivo** para **ContentProject** e clique no **salvar** botão:

![](walkthrough-images/image8.png "Altere o nome do arquivo para ContentProject e clique no botão Salvar")

Quando o projeto é criado, a ferramenta de MonoGame Pipeline exibirá informações sobre o projeto quando raiz **ContentProject** item é selecionado:

![](walkthrough-images/image9.png "Quando o projeto é criado, a ferramenta MonoGame Pipeline exibirá informações sobre o projeto quando o item de ContentProject raiz é selecionado")

Vamos examinar algumas das opções mais importantes para o projeto de conteúdo.


## <a name="output-folder"></a>Pasta de Saída

Essa é a pasta (relativo ao projeto de conteúdo em si) onde a saída **.xnb** arquivos serão salvos. Para manter a simplicidade, usaremos a mesma pasta para manter nossos entrada e arquivos de saída. Em outras palavras, alteraremos a **pasta de saída** ser **.\**  :

![](walkthrough-images/image10.png "")


## <a name="platform"></a>Plataforma

Isso define a plataforma de destino para o conteúdo. Observe que isso é **Windows** por padrão, então queremos alterar isso para nossa plataforma de destino que é **Android** (ou iOS se seguir junto com um projeto do iOS).

![](walkthrough-images/image11.png "Observe que esse é o Windows por padrão, portanto, alterar isso para a plataforma de destino que é Android ou iOS se seguir junto com um projeto do iOS")


# <a name="processing-files-in-the-monogame-pipelinetool"></a>Processamento de arquivos no MonoGame PipelineTool

Em seguida, incluiremos conteúdo para nosso **ContentProject**. Para este projeto, incluiremos arquivos para a raiz do projeto, mas projetos maiores normalmente organizará seu conteúdo em pastas.

Vamos adicionar dois arquivos ao nosso projeto:

 - Um **. PNG** arquivo que será usado para desenhar uma entidade gráfica. Esse arquivo pode [baixado em](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true).
 - Um **.spritefont** arquivo que será usado para desenhar o texto na tela. A ferramenta ContentPipeline dá suporte à criação de novos arquivos de .spritefont, portanto, não há nenhum arquivo a ser baixado.


## <a name="adding-a-png-file"></a>Adicionando um arquivo. PNG

Para adicionar um **. PNG** arquivo ao projeto, primeiro copiaremos-lo no mesmo diretório do projeto de pipeline, que tem o **.mgcb** extensão.

![](walkthrough-images/image12.png "Adicionar um arquivo. PNG ao projeto")

Em seguida, adicionaremos o arquivo para o projeto de pipeline. Para fazer isso na ferramenta de Pipeline de MonoGame, selecione **Editar > Adicionar Item...** , selecione o **ball.png** de arquivo e clique em **abrir**. O arquivo agora será parte do projeto de conteúdo e, quando selecionada, exibirá suas propriedades:

![](walkthrough-images/image13.png "O arquivo agora será parte do projeto de conteúdo e, quando selecionada, exibirá suas propriedades")

Vamos enviar uma mensagem que está sendo deixar todos os valores com suas configurações padrão, nenhuma alteração é necessária para carregar o arquivo .xnb em CocosSharp. É possível criar o arquivo, selecionando o **Construir > compilar** opção de menu, que irá iniciar uma compilação e exibir saída sobre a compilação. Podemos verificar se a compilação funcionou corretamente verificando o **conteúdo** pasta para um novo **ball.xnb** arquivo:

![](walkthrough-images/image14.png "Verificar se a compilação funcionou corretamente verificando a pasta de conteúdo para um novo arquivo ball.xnb")


## <a name="adding-a-spritefont-file"></a>Adicionar um arquivo .spritefont

Podemos criar arquivo .spritefont por meio da ferramenta MonoGame Pipeline. CocosSharp requer fontes em uma **fontes** pasta e modelos de CocosSharp criar automaticamente uma pasta de fontes automaticamente. Podemos adicionar esta pasta para a ferramenta de MonoGame Pipeline selecionando **Editar > Adicionar > pasta existente...** . Navegue até o **conteúdo** pasta e selecione o **fontes** pasta e clique em **Okey**:

![](walkthrough-images/browsetofonts.png "Navegue até a pasta de conteúdo e selecione a pasta de fontes, clique em Okey")

Para adicionar um novo arquivo de .sprintefont, com o botão direito na pasta fontes e selecione **Adicionar > Novo Item...** , selecione o **SpriteFont descrição** opção, digite o nome **arial 36**e clique em **Okey**. CocosSharp requer muito específica de nomenclatura de arquivos de fonte – eles devem estar no formato [FontType]-[FontSize]. Se uma fonte não coincide com este formato de nomenclatura que ele não será carregado por CocosSharp em tempo de execução.

![](walkthrough-images/image15.png "Se uma fonte não coincide com este formato de nomenclatura que ele não será carregado por CocosSharp em tempo de execução")

O arquivo .spritefont é realmente um arquivo XML que pode ser editado em qualquer editor de texto, incluindo o Visual Studio para Mac. As variáveis mais comuns editadas em um arquivo de .spritefont são o `FontName` e `Size` propriedade:


```csharp
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

Abrirá o arquivo em qualquer editor de texto. Como nossa **arial 36.spritefont** nome sugere, vamos deixar o `FontName` como `Arial` , mas alterar o `Size` valor `36`:


```csharp
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
# <a name="using-files-at-runtime"></a>Usando arquivos em tempo de execução

Os arquivos .xnb agora são criados e pronto para ser usado em nosso projeto. Incluiremos os arquivos para o Visual Studio para Mac e em seguida, vamos adicionar código para nosso `GameScene.cs` arquivo para carregar esses arquivos e exibi-los.


## <a name="adding-xnb-files-to-visual-studio-for-mac"></a>Adicionando arquivos .xnb para o Visual Studio para Mac

Primeiro, adicionaremos os arquivos ao nosso projeto. No Visual Studio para Mac, podemos expandir o **BouncingGame.Android** de projeto, expanda o **ativos** com o botão direito na pasta de **conteúdo** pasta e selecione **Adicionar > Adicionar arquivos...** Primeiro, selecionaremos a **ball.xnb** é criado anteriormente e clique em **abrir**. Em seguida, repita as etapas acima, mas adicionar o **arial 36.xnb** arquivo. Selecionaremos a **manter o arquivo no seu atual subdiretório** opção se o Visual Studio para Mac pergunta como adicionar o arquivo. Uma vez concluído, os dois arquivos devem fazer parte do projeto:

![](walkthrough-images/image20.png "Uma vez concluído, os dois arquivos devem fazer parte do projeto")


## <a name="adding-gamescenecs"></a>Adicionando GameScene.cs

Vamos criar uma classe chamada `GameScene,` que conterá nosso entidade gráfica e objetos de texto. Para fazer isso, clique duas vezes no **BouncingGame** (não BouncingGame.Android) do projeto e selecione **Adicionar > novo arquivo...** . Selecione o **geral** categoria, selecione o **classe vazia** opção e, em seguida, digite o nome **GameScene**.

Depois de criado, modificaremos a `GameScene.cs` arquivo para conter o código a seguir:


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

Não é possível abordar o código acima como trabalhar com objetos visual, CocosSharp como CCSprite e CCLabelTtf é abordado no [guia de Introdução CocosSharp](~/graphics-games/cocossharp/first-game/index.md).

Também precisamos adicionar o código para carregar o nosso recém-criado `GameScene`. Para fazer isso, abrirá o `GameAppDelegate.cs` arquivo (que está localizado no **BouncingGame** PCL) e modificar o `ApplicationDidFinishLaunching` método para que ele se parece com:


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

![](walkthrough-images/image1.png "Quando em execução, o jogo se parecerá com")


# <a name="summary"></a>Resumo

Este passo a passo mostrou como usar a ferramenta MonoGame Pipeline para criar arquivos de .xnb de um arquivo. PNG de entrada, bem como criar um novo arquivo de .xnb de um arquivo .sprintefont recém-criado. Ele também mostrou como estruturar CocosSharp projetos para usar arquivos de .xnb e carregar esses arquivos em tempo de execução.

## <a name="related-links"></a>Links relacionados

- [Downloads de MonoGame](http://www.monogame.net/downloads/)
- [Documentação do Pipeline de MonoGame](http://www.monogame.net/documentation/?page=Pipeline)
- [Início do projeto BouncingGame para Android (exemplo)](https://developer.xamarin.com/samples/BouncingGameCompleteAndroid/)
- [ball.PNG gráfico (exemplo)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)
