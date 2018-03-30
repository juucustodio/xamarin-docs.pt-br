---
title: "Criando um projeto de CocosSharp de várias plataformas"
description: "Este passo a passo mostra como criar uma nova solução CocosSharp várias plataformas. O resultado deste passo a passo é um Visual Studio para solução de Mac que inclui três projetos: um projeto de biblioteca de classes portátil, um projeto específico do Android e um projeto específico do iOS. O projeto resultante exibirá uma tela em branco vazia quando executado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 37C97693-B0A8-4064-97B6-A6FAB5BA4FB7
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 2906035ce9bd44d111b89ccfe7443896775315b7
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-multi-platform-cocossharp-project"></a>Criando um projeto de CocosSharp de várias plataformas

_Este passo a passo mostra como criar uma nova solução CocosSharp várias plataformas. O resultado deste passo a passo é um Visual Studio para solução de Mac que inclui três projetos: um projeto de biblioteca de classes portátil, um projeto específico do Android e um projeto específico do iOS. O projeto resultante exibirá uma tela em branco vazia quando executado._

Permite que o mecanismo de jogos 2D CocosSharp código e conteúdo a ser compartilhada entre várias plataformas. Este passo a passo mostra como criar um projeto que pode dar suporte a iOS e Android development. Especificamente, este passo a passo abordará os seguintes tópicos:

 - Instalando CocosSharp
 - Criar uma nova solução
 - Método `LoadGame`

# <a name="installing-cocossharp"></a>Instalando CocosSharp

Primeiro, vamos adicionar CocosSharp para o Visual Studio para Mac. Se executando em um Mac, selecione **Visual Studio para Mac** > **Gerenciador de suplementos...**  . Se em execução no Windows, selecione **ferramentas** > **Gerenciador de suplementos...**  . Clique o **galeria** guia, expanda o **CocosSharp item**, selecione **modelos de projeto CocosSharp**e, finalmente, clique em **instalar...**  .

![Suplemento CocosSharp](part1-images/xamarinstudioaddinsmac.png "")

# <a name="creating-a-new-solution"></a>Criando uma nova solução

Agora que CocosSharp está instalado, vamos criar uma solução. No Visual Studio para Mac, selecione **arquivo** > **novo** > **solução...** . Selecione o **aplicativo** opção sob o **multiplataforma** seção, selecione **projeto vazio CocosSharp**e, em seguida, clique em **próximo**:

![](part1-images/image1.png "Selecione a opção de aplicativo na seção de plataforma cruzada, selecione o projeto vazio CocosSharp e, em seguida, clique em Avançar")

Digite o nome **BouncingGame** para o **nome do projeto**, em seguida, clique em **criar**:

![](part1-images/image2.png "Digite o nome BouncingGame para o nome do projeto e, em seguida, clique em criar")

Depois que o projeto foi criado e o Visual Studio para Mac, podemos compile e execute-o para exibir um plano de fundo cinza: 

![](part1-images/image3.png "Depois que o projeto tiver sido criado e o Visual Studio para Mac, compile e execute-o para exibir um plano de fundo cinza")


# <a name="loadgame-method"></a>Método LoadGame

O projeto de CocosSharp padrão inclui classes específicos para iOS e Android para configurar um `CCGameView`, que é usada para iniciar CocosSharp. O `CCGameView` instância é criada de uma maneira específica da plataforma: cria o projeto iOS o `CCGameView` no `Main.storyboard` de arquivo, enquanto o Android cria o `CCGameView` no `Main.axml` arquivo. Cada plataforma usa a instância de CCGameView em um `LoadGame` método que executa algumas configurações básicas. Mesmo que nós não modificar este código, vamos dar uma olhada em alguns detalhes importantes:

 - O código define o `gameView.DesignResolution` a 1024 por 768. Isso padroniza o posicionamento em todos os dispositivos, independentemente da taxa de proporção, resolução física ou orientação do dispositivo atual. 
 - O código adiciona alguns caminhos de pesquisa. Caminhos de pesquisa também permitem que o conteúdo a ser carregado sem prefixos de diretório. Por exemplo, desde o `"Sounds"` caminho é adicionado como um caminho de pesquisa e, em seguida, um arquivo no diretório `"Content/Sounds/mysound.xnb"` simplesmente pôde ser carregado como `"mysound.xnb"`. Caminhos de pesquisa são semelhantes às `using` instruções em código – eles podem reduzir o código, mas elas também podem introduzir ambiguidade.
 - O código constrói um `GameLayer` instância. `GameLayer` é um `CCLayer`-herança de classe onde adicionaremos todos nossa lógica de jogo. Jogos maior podem exigir várias `CCLayer` instâncias, ou até mesmo várias `CCScene` instâncias (que pode conter vários `CCLayer` instâncias), mas veremos uma única `CCLayer` do jogo.

#  <a name="summary"></a>Resumo

Este passo a passo abordou como criar um projeto de CocosSharp de plataforma cruzada usando o Visual Studio para Mac. O resultado é uma tela vazia que pode ser usada como o ponto de partida de qualquer projeto de jogo.