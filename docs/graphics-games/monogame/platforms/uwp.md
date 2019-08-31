---
title: Criando um projeto UWP para monojogo
description: O monogame pode ser usado para criar jogos e aplicativos para Plataforma Universal do Windows, direcionando vários dispositivos com uma base de código e um conjunto de conteúdo.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 367f44104b0d6049b8d65ab6e3b3f38c703cdb3f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198716"
---
# <a name="creating-a-monogame-uwp-project"></a>Criando um projeto UWP para monojogo

_O monogame pode ser usado para criar jogos e aplicativos para Plataforma Universal do Windows, direcionando vários dispositivos com uma base de código e um conjunto de conteúdo._

Este tutorial aborda a criação e o carregamento de conteúdo do UWP (monogame Plataforma Universal do Windows). Os aplicativos UWP podem ser executados em todos os dispositivos Windows 10, incluindo desktops, tablets, Windows phones e Xbox One.

Este tutorial cria um projeto vazio que exibe um plano de fundo *Azul-centáurea* (a cor do plano de fundo tradicional de aplicativos XNA).

## <a name="requirements"></a>Requisitos

O desenvolvimento de aplicativos UWP de monojogo requer:

- Sistema operacional Windows 10
- Qualquer versão do Visual Studio 2017
- Ferramentas para desenvolvedores do Windows 10
- Definindo o dispositivo para o modo de desenvolvedor
- [Monogame 3.7.1 para Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) ou mais recente

Para obter mais informações, consulte esta [página sobre como configurar o desenvolvimento de UWP do Windows 10](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

Os jogos do Xbox One podem ser desenvolvidos em um hardware do Xbox de varejo um. Um software adicional é necessário no PC de desenvolvimento e no Xbox. Para obter informações sobre como configurar um Xbox One para desenvolvimento de jogos, consulte esta página sobre como configurar [um Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index).

## <a name="creating-an-empty-template"></a>Criando um modelo vazio

Depois que todos os recursos necessários tiverem sido instalados e o modo de desenvolvedor tiver sido habilitado no computador com Windows 10, podemos criar um novo projeto de monojogo usando o Visual Studio seguindo estas etapas:

1. Selecione **arquivo** > novoprojeto >  **...**
1. Selecione a > categoriamodelos > instalados do**C#Visual** monogames: > 

    ![](uwp-images/image1.png "Categoria de monojogo")

1. Selecione a opção de **projeto universal do Windows 10 de jogos** :

    ![](uwp-images/image2.png "Selecione a opção de projeto universal do Windows 10 de jogos")

1. Insira um nome para o novo projeto e clique em **OK**.
Se o Visual Studio exibir erros depois de clicar em OK, verifique se as ferramentas do Windows 10 estão instaladas e se o dispositivo está no modo de desenvolvedor.

Depois que o Visual Studio terminar de criar o modelo, podemos executá-lo para ver o projeto vazio em execução:

![](uwp-images/image3.png "Depois que o Visual Studio terminar de criar o modelo, execute-o para ver o projeto vazio em execução")

Os números nos cantos fornecem informações de diagnóstico. Essas informações podem ser removidas com a exclusão `App.xaml.cs` do código `DEBUG` no no bloco `OnLaunched` no método:


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

Os projetos UWP podem ser implantados em qualquer dispositivo Windows 10 do mesmo projeto. Depois de configurar o computador de desenvolvimento do Windows 10 e o Xbox One, os aplicativos UWP podem ser implantados alternando o destino para o computador remoto e inserindo o endereço IP do Xbox One:

![](uwp-images/remote.png "Os aplicativos UWP podem ser implantados alternando o destino para o computador remoto e inserindo o endereço IP do Xbox")

No Xbox One, a borda branca representa a área não segura para TVs. Para obter mais informações, consulte a [seção área segura](#safe-area-on-xbox-one).

![](uwp-images/safearea.png "No Xbox One, a borda branca representa a área não segura para TVs")

### <a name="safe-area-on-xbox-one"></a>Área segura no Xbox One

Desenvolver jogos para consoles requer considerar a área segura, que é uma área no centro da tela que deve conter todos os visuais críticos (como interface do usuário ou HUD). Não há garantia de que a área fora da área de segurança esteja visível em todas as televisões, de modo que os visuais colocados nessa área possam ser parcialmente ou totalmente invisíveis em alguns monitores.

O modelo de monojogo para o Xbox um considera a área segura e a renderiza como uma borda branca. Essa área também é refletida em tempo de execução na `Window.ClientBounds` Propriedade do jogo, conforme mostrado nesta imagem da janela Watch no Visual Studio. Observe que a altura dos limites do cliente é 1016, apesar da resolução de exibição de 1920 x 1080:

![](uwp-images/clientbounds.png "Observe que a altura dos limites do cliente é 1016, apesar da resolução de exibição 1920 x 1080")

## <a name="referencing-content-in-uwp-projects"></a>Fazendo referência ao conteúdo em projetos UWP

O conteúdo em projetos de monojogo pode ser referenciado diretamente do arquivo ou por meio do [pipeline de conteúdo](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)de monojogo. Pequenos projetos de jogos podem se beneficiar da simplicidade de carregar do arquivo. Projetos maiores se beneficiarão do uso do pipeline de conteúdo para otimizar o conteúdo a fim de reduzir o tamanho e os tempos de carregamento. Ao contrário do XNA no Xbox 360, `System.IO.File` a classe está disponível em aplicativos do Xbox One UWP.

Para obter mais informações sobre como carregar conteúdo usando o pipeline de conteúdo, consulte o [Guia de pipeline de conteúdo](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

### <a name="loading-content-from-file"></a>Carregando conteúdo do arquivo

Ao contrário do iOS e do Android, os projetos UWP podem fazer referência a arquivos relativos ao executável. Jogos simples podem usar essa técnica para carregar conteúdo sem a necessidade de modificar e criar o projeto de pipeline de conteúdo.

Para carregar um `Texture2D` do arquivo:

1. Adicione um arquivo. png à pasta de conteúdo no projeto UWP. Adicionar conteúdo à pasta de conteúdo é uma convenção no XNA e no monogame.
1. Clique com o botão direito do mouse no PNG recém-adicionado e selecione Propriedades.
1. Altere a **cópia para diretório de saída** para **copiar se mais recente**.
1. Adicione o seguinte código ao método Initialize do jogo para carregar um `Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Para obter mais informações sobre como `Texture2D`usar um, consulte o [Guia de introdução ao monogame](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Resumo

Este guia aborda como criar um novo projeto UWP e considerações específicas de UWP ao carregar arquivos. Os desenvolvedores interessados na criação de jogos de UWP completos podem ler mais sobre o monogame no [guia Introdução ao monojogo](~/graphics-games/monogame/introduction/index.md).
