---
title: Criar um projeto UWP MonoGame
description: MonoGame pode ser usado para criar aplicativos e jogos para plataforma Universal do Windows, direcionamento de que vários dispositivos com uma base de código e um conjunto de conteúdo.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: f539ede3bb90f216463a55cca30554a5f50e2386
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922672"
---
# <a name="creating-a-monogame-uwp-project"></a>Criar um projeto UWP MonoGame

_MonoGame pode ser usado para criar aplicativos e jogos para plataforma Universal do Windows, direcionamento de que vários dispositivos com uma base de código e um conjunto de conteúdo._

Este passo a passo aborda a criação do projeto MonoGame Windows UWP (plataforma Universal) e o carregamento de conteúdo. Aplicativos UWP podem executar em todos os dispositivos Windows 10, incluindo desktops, tablets, Windows Phone e Xbox One.

Este passo a passo cria um projeto vazio que exibe um *espinheiro azul* em segundo plano (a cor de fundo tradicional de aplicativos do XNA).

## <a name="requirements"></a>Requisitos

Desenvolvimento de aplicativos UWP MonoGame requer:

- Sistema operacional Windows 10
- Qualquer versão do Visual Studio 2015
- Ferramentas de desenvolvedor do Windows 10
- Dispositivo de configuração para o modo de desenvolvedor
- [MonoGame 3.5 para Visual Studio](http://www.monogame.net/2016/03/17/monogame-3-5/) ou mais recente

Para obter mais informações, consulte [página sobre a configuração para o desenvolvimento de UWP do Windows 10](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

Jogos do Xbox One podem ser desenvolvidos em hardware de varejo Xbox One. Software adicional é necessária para o computador de desenvolvimento e Xbox One. Para obter informações sobre como configurar um Xbox One para desenvolvimento de jogos, consulte esta página no [Configurando um Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Criando um modelo vazio

Depois que todos os recursos necessários foram instalados e o modo de desenvolvedor foi habilitado no computador do Windows 10, podemos criar um novo projeto de MonoGame usando o Visual Studio seguindo estas etapas:

1. Selecione **arquivo** > **novo** > **projeto...**
1. Selecione o **instalado** > **modelos** > **Visual C#** > **MonoGame** categoria: 

    ![](uwp-images/image1.png "Categoria de MonoGame")

1. Selecione o **MonoGame Windows 10 Universal projeto** opção: 

    ![](uwp-images/image2.png "Selecione a opção de projeto Universal do MonoGame Windows 10")

1. Insira um nome para o novo projeto e clique em **Okey**.
Se depois de clicar em Okey, o Visual Studio exibe os erros, verifique se as ferramentas do Windows 10 estão instaladas e que o dispositivo estiver no modo de desenvolvedor.

Depois que o Visual Studio termina de criar o modelo, podemos pode executá-lo para ver o projeto vazio em execução:

![](uwp-images/image3.png "Depois que o Visual Studio termina de criar o modelo, execute-o para ver o projeto vazio em execução")

Os números nos cantos fornecem informações de diagnóstico. Essas informações podem ser removidas se o código em `App.xaml.cs` no `DEBUG` bloco no `OnLaunched` método:


```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>Em execução no Xbox One

Os projetos UWP podem implantar em qualquer dispositivo Windows 10 do mesmo projeto. Depois de configurar a máquina de desenvolvimento do Windows 10 e Xbox One, aplicativos UWP podem ser implantados, alternando o destino ao computador remoto e Inserir endereço IP do Xbox um:

![](uwp-images/remote.png "Aplicativos UWP podem ser implantados, alternando o destino ao computador remoto e inserir o endereço IP do Xbox aqueles")

No Xbox One, a borda branca representa a área de não segura para TVs de tela. Para obter mais informações, consulte o [seção área segura](#Safe_Area_on_Xbox_One).

![](uwp-images/safearea.png "No Xbox One, a borda branca representa a área de não segura para TVs de tela")

### <a name="safe-area-on-xbox-one"></a>Área segura Xbox One

Desenvolvimento de jogos para consoles requer considerar a área segura, que é uma área no centro da tela que deve conter todos os visuais críticos (como a interface do usuário ou HUD). A área fora da área de segurança não é garantida para ser visível em todos os televisores, portanto, visuais colocados nessa área podem ser parcialmente ou totalmente visíveis em alguns monitores.

O modelo de MonoGame para Xbox One considera a área segura e renderiza-o como uma borda branca. Essa área também é refletida no tempo de execução do jogo `Window.ClientBounds` propriedade conforme mostrado nesta imagem da janela de inspeção de variáveis no Visual Studio. Observe que a altura dos limites do cliente é 1016, apesar da resolução 1920 x 1080:

![](uwp-images/clientbounds.png "Observe que a altura dos limites do cliente é 1016, apesar da resolução 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Conteúdo de referência em projetos UWP

Conteúdo em projetos de MonoGame pode ser referenciado diretamente do arquivo ou por meio de [Pipeline conteúdo MonoGame](~/graphics-games/cocossharp/content-pipeline/index.md). Projetos de jogos pequenos podem se beneficiar a simplicidade de carregamento de arquivo. Projetos maiores serão beneficiadas por usarem o pipeline de conteúdo para otimizar o conteúdo para reduzir o tamanho e tempos de carregamento. Ao contrário do XNA no Xbox 360, o `System.IO.File` classe está disponível em aplicativos UWP um Xbox.

Para obter mais informações sobre como carregar o conteúdo usando o pipeline de conteúdo, consulte o [conteúdo Pipeline guia](~/graphics-games/cocossharp/content-pipeline/index.md). 

### <a name="loading-content-from-file"></a>Carregar o conteúdo do arquivo

Ao contrário de iOS e Android, os projetos UWP podem fazer referência a arquivos em relação ao executável. Jogos simples podem usar esse conteúdo de carga técnica sem a necessidade de modificar e compilar o projeto do pipeline de conteúdo.

Para carregar um `Texture2D` do arquivo:

1. Adicione um arquivo. png para a pasta de conteúdo do projeto UWP. Adicionar conteúdo à pasta de conteúdo é uma convenção em XNA e MonoGame.
1. Clique com botão direito no PNG recém-adicionado e selecione Propriedades.
1. Alterar o **copiar para diretório de saída** para **Copy if Newer**.
1. Adicione o seguinte código ao método de inicialização do jogo para carregar um `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Para obter mais informações sobre como usar um `Texture2D`, consulte o [Introdução ao guia de MonoGame](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Resumo

Este guia aborda como criar um novo projeto UWP e considerações específicas do UWP ao carregar arquivos. Os desenvolvedores que queiram criar jogos UWP completos podem ler mais sobre MonoGame no [Introdução ao guia de MonoGame](~/graphics-games/monogame/introduction/index.md).
