---
title: Criar um projeto UWP MonoGame
description: MonoGame pode ser usado para criar jogos e aplicativos para a plataforma Universal do Windows, o direcionamento de que vários dispositivos com uma base de código e um conjunto de conteúdo.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 9f39580d282defed354f3b9e5cbe4eb1cdec4796
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161061"
---
# <a name="creating-a-monogame-uwp-project"></a>Criar um projeto UWP MonoGame

_MonoGame pode ser usado para criar jogos e aplicativos para a plataforma Universal do Windows, o direcionamento de que vários dispositivos com uma base de código e um conjunto de conteúdo._

Este passo a passo aborda a criação do projeto de MonoGame Universal Windows Platform (UWP) e o carregamento de conteúdo. Aplicativos UWP podem ser executados em todos os dispositivos Windows 10, incluindo desktops, tablets, telefones do Windows e Xbox One.

Este passo a passo cria um projeto vazio que exibe uma *cornflower azul* em segundo plano (a cor de fundo tradicional de aplicativos XNA).

## <a name="requirements"></a>Requisitos

Desenvolvimento de aplicativos UWP MonoGame requer:

- Sistema operacional Windows 10
- Qualquer versão do Visual Studio 2017
- Ferramentas de desenvolvedor do Windows 10
- Dispositivo de configuração para o modo de desenvolvedor
- [MonoGame 3.7.1 para Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) ou mais recente

Para obter mais informações, consulte este [página sobre a configuração para o desenvolvimento do Windows 10 UWP](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

Jogos Xbox One podem ser desenvolvidos no hardware de varejo Xbox One. Software adicional é necessária no desenvolvimento PC e Xbox One. Para obter informações sobre como configurar um Xbox One para desenvolvimento de jogos, consulte esta página no [Configurando um Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Criando um modelo vazio

Depois que todos os recursos necessários foram instalados e o modo de desenvolvedor foi habilitado no computador do Windows 10, podemos criar um novo projeto de MonoGame usando o Visual Studio seguindo estas etapas:

1. Selecione **arquivo** > **nova** > **projeto...**
1. Selecione o **instalados** > **modelos** > **Visual C#**   >  **MonoGame** categoria:

    ![](uwp-images/image1.png "Categoria de MonoGame")

1. Selecione o **MonoGame Windows 10 Universal projeto** opção:

    ![](uwp-images/image2.png "Selecione a opção de projeto Universal do MonoGame Windows 10")

1. Insira um nome para o novo projeto e clique em **Okey**.
Se o Visual Studio exibe todos os erros depois de clicar em Okey, verifique se que as ferramentas do Windows 10 estão instaladas e que o dispositivo está no modo de desenvolvedor.

Depois que o Visual Studio terminar de criar o modelo, podemos pode executá-lo para ver o projeto vazio em execução:

![](uwp-images/image3.png "Depois que o Visual Studio terminar de criar o modelo, execute-o para ver o projeto vazio em execução")

Os números nos cantos fornecem informações de diagnóstico. Essas informações podem ser removidas, excluindo o código na `App.xaml.cs` no `DEBUG` bloquear o `OnLaunched` método:


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

Projetos da UWP podem implantar em qualquer dispositivo Windows 10 do mesmo projeto. Depois de configurar a máquina de desenvolvimento do Windows 10 e o Xbox One, aplicativos UWP podem ser implantados, alternando o destino para o computador remoto e inserindo o endereço IP do Xbox One:

![](uwp-images/remote.png "Aplicativos UWP podem ser implantados, alternando o destino para o computador remoto e inserir o endereço IP do Xbox aqueles")

No Xbox One, a borda branca representa a área de não-safe para TVs. Para obter mais informações, consulte o [seção área segura](#safe-area-on-xbox-one).

![](uwp-images/safearea.png "No Xbox One, a borda branca representa a área de não-safe para TVs")

### <a name="safe-area-on-xbox-one"></a>Área segura no Xbox One

Desenvolvimento de jogos para consoles requer considerando a área de segurança, que é uma área no centro da tela que deve conter todos os visuais críticos (como a interface do usuário ou HUD). A área de fora da área de segurança não é garantida para ser visíveis em todos os televisões, portanto visuais colocados nessa área podem ser totalmente ou parcialmente invisíveis alguns monitores.

O modelo de MonoGame para Xbox One considera a área de segurança e renderiza-o como uma borda branca. Essa área também é refletida em tempo de execução do jogo `Window.ClientBounds` propriedade conforme mostrado nesta imagem da janela Inspeção no Visual Studio. Observe que a altura dos limites do cliente é 1016, apesar de resolução de 1920 x 1080 vídeo:

![](uwp-images/clientbounds.png "Observe que a altura dos limites do cliente é 1016, apesar de resolução de tela de 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Referenciar conteúdo em projetos UWP

Conteúdo em projetos de MonoGame pode ser referenciado diretamente do arquivo ou por meio de [Pipeline de conteúdo MonoGame](~/graphics-games/cocossharp/content-pipeline/index.md). Pequenos projetos de jogos podem se beneficiar da simplicidade de carregamento de arquivo. Projetos maiores serão beneficiadas por usarem o pipeline de conteúdo para otimizar o conteúdo para reduzir o tamanho e tempos de carregamento. Ao contrário do XNA no Xbox 360, o `System.IO.File` classe está disponível em aplicativos UWP do Xbox One.

Para obter mais informações sobre como carregar conteúdo usando o pipeline de conteúdo, consulte o [guia do Pipeline de conteúdo](~/graphics-games/cocossharp/content-pipeline/index.md).

### <a name="loading-content-from-file"></a>Carregando conteúdo do arquivo

Ao contrário do iOS e Android, os projetos UWP podem fazer referência a arquivos em relação ao executável. Jogos simples podem usar esse conteúdo de carga técnica sem a necessidade de modificar e compilar o projeto do pipeline de conteúdo.

Para carregar um `Texture2D` do arquivo:

1. Adicione um arquivo. png para a pasta Content no projeto UWP. Adicionar conteúdo à pasta de conteúdo é uma convenção no XNA e MonoGame.
1. Clique com botão direito no PNG recém-adicionado e selecione Propriedades.
1. Alterar o **Copy to Output Directory** à **Copy if Newer**.
1. Adicione o seguinte código ao método de inicialização do seu jogo para carregar um `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Para obter mais informações sobre como usar um `Texture2D`, consulte o [Introdução ao guia de MonoGame](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Resumo

Este guia aborda como criar um novo projeto UWP e considerações específicas do UWP ao carregamento de arquivos. Os desenvolvedores que estejam interessados em criar jogos UWP completos podem ler mais sobre o MonoGame na [Introdução ao guia de MonoGame](~/graphics-games/monogame/introduction/index.md).
