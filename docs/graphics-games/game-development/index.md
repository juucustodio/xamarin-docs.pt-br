---
title: Introdução ao desenvolvimento de jogos com o Xamarin
description: Este documento fornece uma visão geral de alto nível do desenvolvimento de jogos com o Xamarin, descrevendo como os jogos são feitos e uma amostra de tecnologias disponíveis para uso com Xamarin. iOS e Xamarin. Android.
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 5992e8df3080bb35fd123483e5ffb5e64f268b1a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291732"
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introdução ao desenvolvimento de jogos com o Xamarin

Desenvolver jogos pode ser muito empolgante, especialmente considerando como é fácil publicar seu trabalho em plataformas móveis. Este artigo discute os conceitos e as tecnologias relacionados ao desenvolvimento de jogos que ajudarão você a criar jogos, seja a meta de criar um jogo AAA de alta qualidade ou apenas para programar para diversão.

Este artigo aborda os seguintes tópicos:

- **Conceitos de programação de jogo versus não jogo** – exploraremos alguns conceitos que são exclusivos do desenvolvimento de jogos ou que são compartilhados com outros tipos de desenvolvimento, mas que merecem ênfase aqui devido à sua importância.
- **Equipe de desenvolvimento de jogos** – esta seção analisa as várias funções em uma equipe de desenvolvedores de jogos.
- **Criando uma ideia de jogo** – esta seção pode ajudá-lo a criar uma nova ideia de jogo – a primeira etapa para fazer um novo jogo.
- **Tecnologia de desenvolvimento de jogos** – aqui, listaremos algumas das tecnologias de plataforma cruzada disponíveis que podem melhorar sua produtividade como um desenvolvedor de jogos.

## <a name="game-vs-non-game-programming-concepts"></a>Conceitos de programação de jogo versus não jogo

Os programadores que se movimentam para o desenvolvimento de jogos são frequentemente confrontáveis com novos conceitos e padrões de desenvolvimento. Esta seção apresenta uma exibição de alto nível de alguns desses conceitos.

### <a name="the-game-loop"></a>O loop de jogo

Um jogo típico requer movimento constante ou alteração para estar acontecendo na tela em resposta à interação do usuário e à lógica de jogos automática. Isso é obtido por meio do que normalmente é chamado de *loop de jogo*. Um loop de jogo é um tipo de instrução de looping (como um loop while) que é executado em uma frequência muito alta, como 30 ou 60 *quadros por segundo*.

Veja a seguir um diagrama de um loop de jogo simples:

![](images/image1.png "This is a diagram of a simple game loop")

As tecnologias que discutimos abaixo abstrairão o loop while real, mas apesar dessa abstração, o conceito de atualizações de cada quadro estará presente.

O desempenho do código pode ter prioridade até mesmo com os jogos mais simples. Por exemplo: uma função que leva 10 milissegundos para ser executada pode ter um impacto significativo no desempenho de um jogo, especialmente se ele for chamado mais de uma vez por quadro. Se o jogo estiver sendo executado em 30 quadros por segundo, isso significa que cada quadro deve ser executado em menos de 33 milissegundos. Por outro lado, essa função pode nem mesmo ser perceptível se for executada apenas em resposta a um clique de botão em um aplicativo que não seja de jogo.

Os tipos comuns de lógica que podem ser executadas a cada quadro incluem:

- **Lendo a entrada** – o jogo pode precisar verificar se o usuário interagiu com o jogo verificando o hardware de entrada, como a tela sensível ao toque, o teclado, o mouse ou o controlador de jogo.
- **Movimentação** – os objetos que se movem de um local para outro normalmente movem um valor muito pequeno a cada quadro para fornecer a ilusão de movimento fluido.
- **Colisão** – muitos jogos exigem o teste frequente de se vários objetos estão sobrepostos ou interseccionando. Abordaremos a colisão com mais detalhes em uma seção posterior neste artigo. A movimentação e a colisão podem ser tratadas por um sistema de simulação de física dedicada.
- **Verificando condições específicas do jogo** – o estado de um jogo pode ser controlado por determinadas condições, como se o jogador ganhou pontos suficientes ou se o tempo alocado foi esgotado.
- **Comportamento de ia** – lógica de cada quadro que pode ser usada para controlar o comportamento de objetos que não são controlados pelo jogador, como a patrulha de um inimigo ou a movimentação de drivers do adversário em um Racetrack.
- **Renderização** – a maioria dos jogos atualizará o que é exibido na tela a cada quadro. Isso pode ser feito em resposta a alterações que têm impacto na reprodução do jogo (como um caractere passando por um nível) ou simplesmente para fornecer um polonês Visual (como a neve ou ícones animados).

Tenha em mente que muitas das atividades listadas acima podem alterar o estado de todo o aplicativo, enquanto muitos aplicativos que não são jogos tendem a alterar o estado em resposta aos eventos que estão sendo gerados.

### <a name="content-loading-and-unloading"></a>Carregamento e descarregamento de conteúdo

O carregamento e o descarregamento manual do conteúdo (ou descartar) podem ser necessários dependendo de qual tecnologia você está usando em desenvolvimento. O carregamento e o descarregamento manual de ativos podem ser necessários por vários motivos:

- Os ativos podem levar muito tempo para serem carregados em relação ao comprimento de um único quadro. Alguns ativos podem até mesmo levar alguns segundos para serem carregados, o que interromperia severamente a experiência se estiver carregado em meados de jogo. Se o tempo de carregamento for especialmente longo (por exemplo, mais de um segundo ou dois), talvez você queira mostrar uma tela de carregamento animada ou uma barra de progresso.
- Os ativos podem consumir muita RAM, exigindo o gerenciamento ativo do que é carregado para se ajustar no que é fornecido pelas plataformas de destino do jogo.
- Talvez os jogos precisem exibir mais ativos do que podem se ajustar na RAM. Os jogos "Open World" geralmente incluem grandes ambientes que os jogadores podem navegar diretamente – ou seja, sem nenhuma tela de carregamento. Nesse caso, talvez seja necessário criar um sistema personalizado para transmitir conteúdo e gerenciar o uso de memória.

Os formatos de arquivo personalizados podem precisar de processamento no tempo de carregamento, exigindo código de carregamento personalizado.

### <a name="math"></a>Math

Muitos jogos exigem matemática mais avançada do que aplicativos que não são do jogo. É claro que o nível de matemática depende da complexidade do jogo. Em jogos 3D gerais, é necessário mais matemática do que o 2D. Felizmente, você sempre pode começar a usar jogos simples e aprender como usar. O desenvolvimento de jogos pode ser uma ótima maneira de aprender a matemática!

Se você estiver familiarizado com o plano cartesiano, que está usando coordenadas X e Y para posicionar objetos, então você sabe o suficiente para começar a usar o desenvolvimento de jogos. O seguinte mostra um plano cartesiano com Y positivo apontando para cima:

![](images/image2.png "This shows a Cartesian plane with positive Y pointing upward")

> [!IMPORTANT]
> Alguns mecanismos/APIs usam um sistema de coordenadas em que o aumento do valor Y de um objeto vai movê-lo para baixo, enquanto outros sistemas usam um sistema de coordenadas onde Y positivo está ativo. Tenha isso em mente se estiver mudando entre sistemas.
As funções trigonométricas (como seno e cosseno) são comumente usadas em jogos 2D que implementam qualquer forma de rotação.

Se você estiver planejando fazer um jogo 3D, provavelmente precisará estar familiarizado com os conceitos de algebra linear (para rotação e movimento no espaço 3D), bem como alguns cálculo (para implementar a aceleração).

### <a name="content-pipelines"></a>Pipelines de conteúdo

O termo *pipeline de conteúdo* refere-se ao processo que um arquivo leva para obter de seu formato quando criado (como um arquivo de imagem. png) para seu formato final quando usado em um jogo. O formato final depende de qual tipo de conteúdo está sendo usado, bem como qual tecnologia está sendo usada para apresentar o conteúdo.

Alguns pipelines de conteúdo podem ser muito rápidos e não exigem esforço manual. Por exemplo, a maioria dos mecanismos de jogo e APIs pode carregar o formato de arquivo. png em seu formato não processado. Por outro lado, formatos mais complicados (como modelos 3D) talvez precisem ser processados em um formato diferente antes de serem carregados, e esse processamento pode levar algum tempo dependendo do tamanho e da complexidade do ativo.

## <a name="game-development-teams"></a>Equipes de desenvolvimento de jogos

O desenvolvimento de jogos apresenta novas funções e títulos para indivíduos envolvidos no processo. A maioria dos desenvolvedores de jogos não é capaz de atender ao amplo conjunto de habilidades necessárias para lançar um jogo completo, para que existam várias disciplinas. Tenha em mente que essa não é uma lista completa de áreas de desenvolvimento – apenas algumas das mais comuns.

- **Programador** – a maioria das pessoas que estão lendo este artigo se enquadram nessa categoria. A função de um programador no desenvolvimento de jogos é semelhante à função de um programador em um aplicativo que não é do jogo. As responsabilidades incluem a escrita de lógica para controlar o fluxo de um jogo, o desenvolvimento de sistemas para tarefas comuns no contexto de um determinado projeto, a adição e a exibição de conteúdo, e, é claro, a correção de bugs.
- **artista 2D** – artistas 2D são responsáveis por criar *ativos 2D*. Isso inclui arquivos de imagem para a GUI, as partículas, os ambientes e os caracteres do jogo. Se o jogo que você está desenvolvendo for 3D, os artistas 2D poderão não ser responsáveis por ambientes e caracteres. Você pode encontrar uma arte gratuita para seu jogo em [http://opengameart.org/](http://opengameart.org/) .
- **artistas** 3D – os artistas 3D são responsáveis por criar *ativos 3D*. Isso inclui modelos 3D para ambientes, caracteres e props (mobília, plantas e outros objetos inanimados). Algumas equipes diferenciam entre artistas 3D e 3D Animators, dependendo do tamanho da equipe. Você pode encontrar uma arte 3D gratuita para seu jogo em [http://opengameart.org/](http://opengameart.org/) .
- **Game designer** – os designers de jogos são responsáveis por definir como o jogo é tocado. Isso pode incluir decisões de alto nível, como a configuração do jogo, o objetivo geral do jogo e como um jogador avança pelo jogo. Os designers de jogos também podem estar envolvidos em decisões muito detalhadas, como o mapeamento de entrada para ações, a definição de coeficientes para movimentação ou nivelamento e o layout de nível de design. Tenha em mente que o termo *Designer* pode se referir a um designer de jogos ou um designer visual, dependendo do contexto.
- **Designer de som** – os designers de som são responsáveis pelos ativos de áudio de um jogo. Algumas equipes podem diferenciar os indivíduos responsáveis pela criação de efeitos de som e compodores, enquanto equipes menores podem ter um único indivíduo responsável por todo o áudio.

## <a name="creating-a-game-idea"></a>Criando uma ideia do jogo

A criação de um jogo pode parecer fácil – depois que todo o único requisito é "fazer algo divertido". Infelizmente, muitos desenvolvedores se encontram em uma perda quando chega o momento de criar uma ideia a partir da qual iniciar o desenvolvimento.

A disciplina do design do jogo não é explicada com facilidade e requer a prática para melhorar da mesma forma que a arte ou a programação, mas esta seção pode ajudá-lo a iniciar o caminho.

Novos desenvolvedores devem começar pequenos. Pode ser difícil resistir à tentação de refazer um jogo de vídeo grande e moderno, mas jogos menores podem ser um ambiente de aprendizado melhor e o progresso mais rápido faz uma experiência mais ágil.

Muitos jogos, tanto para fins de aprendizado quanto para jogos comerciais, começam como uma melhoria ou modificação em um jogo existente. Uma maneira de gerar ideias é examinar outros jogos para inspiração. Por exemplo, você pode considerar um jogo que você gosta e tentar identificar quais características sobre a reprodução do jogo o tornam divertido. Pode ser a exploração, a principalidade da mecânica do jogo ou o andamento de uma história. Não se esqueça de considerar os jogos "retrô" também ao procurar novas ideias.

Outra técnica para gerar novas ideias é considerar um gênero específico, como jogos de quebra-cabeça, jogos de estratégia ou plataformas. Um gênero familiar ao desenvolvedor pode fornecer um bom ponto de partida.

A recriação de jogos existentes também é uma experiência educacional, embora isso possa limitar a viabilidade comercial do produto concluído. O processo de criação de um jogo, mesmo um clone preciso, fornece uma experiência de educação valiosa.

## <a name="game-development-technology"></a>Tecnologia de desenvolvimento de jogos

Os desenvolvedores que usam o Xamarin. Android e o Xamarin. iOS têm uma ampla gama de tecnologias disponíveis para auxiliar no desenvolvimento de jogos. Esta seção discutirá algumas das soluções de plataforma cruzada mais populares.

### <a name="monogame"></a>MonoGame

O monogames é uma versão de plataforma cruzada de software livre da API XNA da Microsoft. O monogame pode ser usado para fazer jogos para iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One e switch.

O monogames não é tecnicamente um mecanismo de jogo, mas sim uma API de desenvolvimento de jogos. Isso significa que trabalhar com monogames requer o gerenciamento direto de objetos de jogo, o desenho manual de objetos e a implementação de objetos comuns, como câmeras e *grafos de cena* (a hierarquia pai filho entre objetos de jogos).

O monogame não oferece um ambiente de desenvolvimento Visual padrão, por isso trabalhar com monojogo requer conhecimento de programação.

Exemplos notáveis de jogos que usam o monogame incluem:

FEZ:

![](images/image7.png "FEZ")

Bastiões

![](images/image8.jpg "Bastion")

Para começar a trabalhar com monojogo, acesse nossos [guias de monojogo](~/graphics-games/monogame/index.md).

### <a name="urhosharp"></a>UrhoSharp

O UrhoSharp é um mecanismo 3D e 2D de alto nível de plataforma cruzada que pode ser usado para criar cenas 3D e 2D animadas para seus aplicativos usando geometrias, materiais, luzes e câmeras.

![](images/urhosharp.gif "UrhoSharp is a cross-platform high-level 3D and 2D engine that can be used to create animated 3D and 2D scenes")

Confira os [guias do UrhoSharp](~/graphics-games/urhosharp/index.md) para começar.

### <a name="additional-technology"></a>Tecnologia adicional

As tecnologias realçadas acima são apenas uma amostra das tecnologias disponíveis. Outras tecnologias notáveis incluem:

- **Kit de Sprite** – o Xamarin fornece suporte para a estrutura de jogos do kit de Sprite da Apple, que fornece acesso a todas as funcionalidades da API nativa. Como o sprite kit é a tecnologia criada pela Apple, ele fornece uma integração profunda com o restante do ecossistema do iOS. É claro que o sprite kit não é uma plataforma cruzada, portanto não pode ser usado no Android. Para obter mais informações sobre como usar o kit Sprite, consulte esta postagem: [https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit de cena** – o Xamarin também fornece suporte para a estrutura de kit de cena da Apple, que simplifica a implementação de gráficos 3D em aplicativos Ios. O Scene Kit também é uma tecnologia fornecida pela Apple, portanto, ela tem considerações específicas de plataforma e integração mencionadas acima para o kit de Sprite. Para obter mais informações sobre o kit de cena, consulte esta postagem: [https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (que significa o Open Tool Kit) fornece acesso OpenGL de baixo nível a hardware do iOS, da Apple e do Mac. Para obter mais informações sobre o OpenTK, consulte a página principal em: [https://opentk.net/](https://opentk.net/)

## <a name="related-links"></a>Links relacionados

- [Guias de monojogo](~/graphics-games/monogame/index.md)
- [Guias de UrhoSharp](~/graphics-games/urhosharp/index.md)
