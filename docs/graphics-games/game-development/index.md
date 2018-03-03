---
title: "Introdução ao desenvolvimento de jogos com o Xamarin"
description: "A natureza do desenvolvimento de jogos pode diferir consideravelmente o desenvolvimento de outros tipos de aplicativos. Este artigo é uma introdução ao desenvolvimento de jogos com tecnologias que podem ser usadas com xamarin e xamarin. Ele fornece uma discussão detalhada sobre como jogos são feitos e uma amostra de tecnologias disponíveis para uso com o xamarin e xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 9d1ce2da87d6f169efb5431f734695f6876cf3f0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-game-development-with-xamarin"></a>Introdução ao desenvolvimento de jogos com o Xamarin

_A natureza do desenvolvimento de jogos pode diferir consideravelmente o desenvolvimento de outros tipos de aplicativos. Este artigo é uma introdução ao desenvolvimento de jogos com tecnologias que podem ser usadas com xamarin e xamarin. Ele fornece uma discussão detalhada sobre como jogos são feitos e uma amostra de tecnologias disponíveis para uso com o xamarin e xamarin._

Desenvolvimento de jogos pode ser muito interessante, especialmente considerando como é fácil pode ser para publicar seu trabalho em plataformas móveis. Este artigo aborda os conceitos e tecnologias relacionadas ao desenvolvimento de jogos que ajudará você a criam jogos, se seu objetivo é criar um AAA de alta qualidade jogos ou apenas para o programa para diversão.

Este artigo aborda os seguintes tópicos:

- **Jogo versus conceitos de programação de jogo não** – vamos explorar alguns conceitos que são exclusivos para o desenvolvimento de jogos, ou são compartilhados com outros tipos de desenvolvimento, mas merecem ênfase devido à sua importância.
- **Equipe de desenvolvimento de jogos** – esta seção examina as várias funções em uma equipe de desenvolvedores de jogos.
- **Criando uma ideia de jogo** – esta seção pode ajudar a criar uma nova ideia jogo – a primeira etapa para fazer um novo jogo.
- **Tecnologia de desenvolvimento de jogos** – aqui será listado alguns as tecnologias de plataforma cruzada disponíveis que podem aumentar sua produtividade como um desenvolvedor de jogo.


# <a name="game-vs-non-game-programming-concepts"></a>Jogos vs. Conceitos de programação não é de jogo

Os programadores movendo para desenvolvimento de jogos geralmente são enfrentam novos conceitos e padrões de desenvolvimento. Esta seção apresenta uma visão geral de alguns desses conceitos.


## <a name="the-game-loop"></a>O Loop de jogo

Um jogo típico requer constante movimentação ou alteração está ocorrendo na tela em resposta à interação do usuário e a lógica de jogo automática. Isso é feito por meio do que é normalmente conhecido como um *loop de jogo*. Um loop de jogo é algum tipo de instrução (como um loop while) que é executado em uma alta frequência, como 30 ou 60 de loop *quadros por segundo*.

Este é um diagrama de um loop de jogo simple:

![](images/image1.png "Este é um diagrama de um loop de jogo simple")

As tecnologias que discutiremos abaixo simplificarão o loop while real, mas Apesar dessa abstração o conceito de atualizações de cada quadro estará presente.

Desempenho de código pode levar a prioridade no mesmo o mais simples de jogos. Por exemplo: uma função que leva 10 milissegundos para executar pode ter um impacto significativo sobre o desempenho de um jogo – especialmente se for chamado mais de uma vez por quadro. Se estiver executando o jogo em 30 quadros por segundo, que significa que cada quadro deve executar em 33 em milissegundos. Por outro lado, uma função não pode até mesmo ser perceptível se só será executado em resposta a um clique de botão em um aplicativo não é de jogo.

Tipos comuns de lógica que pode ser executada a cada quadro incluem:

- **Entrada de leitura** – o jogo talvez seja necessário verificar se o usuário interagiu com o jogo, verificando o hardware de entrada, como a tela sensível ao toque, teclado, mouse ou controlador de jogo.
- **Movimentação de** – objetos que mover de um local para outro geralmente moverá um pequeno valor cada quadro para dar a ilusão de interrupções.
- **Colisão** – muitos jogos requerem o teste frequentes se vários objetos são sobrepostos ou interseção. Abordaremos colisão com mais profundidade em uma seção mais adiante neste artigo. Movimentação e a colisão podem ser manipuladas por um sistema simulação de física dedicada.
- **Verificação de condições específicas do jogo** – o estado do jogo pode ser controlado por determinadas condições, como se o player obteve pontos suficientes ou se o tempo alocado está vazio.
- **Comportamento do AI** – lógica em cada quadro que pode ser usada para controlar o comportamento de objetos que não são controladas pelo player, como o patrolling de um inimigo ou a movimentação de drivers adversário em torno de um com tamanho ajustável.
- **Renderização** – a maioria dos jogos atualizará o que é exibido na tela de todos os quadros. Isso pode ser feito em resposta a alterações que têm impacto em jogo (como um caractere de movimentação em um nível) ou simplesmente para fornecer polonês visual (como neve queda ou ícones animados).

Tenha em mente que muitas das atividades listadas acima podem alterar o estado do aplicativo inteiro, enquanto que muitos aplicativos não-jogo tendem a alterar o estado em resposta a eventos que está sendo gerado.


## <a name="content-loading-and-unloading"></a>Conteúdo Carregando e descarregando

Conteúdo manualmente Carregando e descarregando (ou descartando) pode ser necessárias dependendo de qual tecnologia você estiver usando em desenvolvimento. Manualmente, carregando e descarregando de ativos podem ser necessários para alguns dos motivos:

 - Ativos podem levar muito tempo para carregar relativo ao tamanho de um quadro simples. Alguns ativos podem levar até mesmo segundos para carregar, que lhe pode atrapalhar a experiência se carregado mid jogos. Se o tempo de carregamento é especialmente longo (por exemplo, mais de um ou dois segundos), será necessário mostrar uma imagem ao carregar a barra de progresso ou de tela.
 - Ativos podem consumir uma grande quantidade de RAM, que exigem o gerenciamento ativo do que é carregado para se ajustarem que é fornecido por plataformas de destino do jogo.
 - Jogos talvez seja necessário exibir mais ativos que pode caber na memória RAM. Jogos "Abrir World" geralmente incluem ambientes de grandes porte que players podem navegar pelo perfeitamente – que é com nenhuma tela de carregamento. Nesse caso, você precisará criar um sistema personalizado para conteúdo de streaming em e gerenciamento de uso de memória.

Formatos de arquivo personalizado podem ser necessário processamento no tempo de carregamento, a necessidade de código personalizado de carregamento.


## <a name="math"></a>Matemática

Muitos jogos exigem matemática mais avançada que os aplicativos não é de jogo. Naturalmente, o nível de matemática depende da complexidade do jogo. Em geral, jogos 3D exigem mais matemática que 2D. Felizmente, você sempre pode começar a usar com jogos simples e desde o início. Desenvolvimento de jogos pode ser uma ótima maneira de aprender matemática.

Se você estiver familiarizado com o plano cartesiano – que está usando coordenadas X e Y para posicionar objetos – em seguida, você sabe o suficiente para começar o desenvolvimento de jogos. O exemplo a seguir mostra um plano cartesiano com positivo Y apontando para cima:

![](images/image2.png "Isso mostra que um plano cartesiano com positivo Y apontando para cima")

> [!IMPORTANT]
> Alguns mecanismos/APIs usam um sistema de coordenadas onde aumentando o valor de Y de um objeto será movê-lo, enquanto outros sistemas usam um sistema de coordenadas onde Y positivo está ativo. Lembre-se se você estiver movendo entre sistemas.
Funções trigonométricas (como o seno e o cosseno) são usadas em jogos 2D que implementam qualquer forma de rotação.



Se você estiver planejando fazer um jogo 3D, em seguida, você provavelmente precisará estar familiarizado com conceitos de álgebra Linear (para rotação e movimentação em espaço 3D), bem como alguns cálculo (para implementar aceleração).


## <a name="content-pipelines"></a>Pipelines de conteúdo

O termo *pipeline conteúdo* refere-se ao processo que usa um arquivo para obter seu formato quando criado (por exemplo, um arquivo de imagem. png) em seu formato final quando usada em um jogo. O formato final depende em qual tipo de conteúdo está sendo usado, bem como qual tecnologia está sendo usada para apresentar o conteúdo.

Algum conteúdos pipelines podem ser muito rápido e sem esforço manual. Por exemplo, a maioria dos mecanismos de jogos e APIs pode carregar o formato de arquivo. PNG em seu formato não processado. Por outro lado, talvez seja necessário mais complicados formatos (como modelos 3D) para serem processados em um formato diferente antes de carregar e esse processamento pode demorar algum tempo dependendo do tamanho e da complexidade do ativo.


# <a name="game-development-teams"></a>Equipes de desenvolvimento de jogos

Desenvolvimento de jogos apresenta novas funções e títulos para as pessoas envolvidas no processo. A maioria dos desenvolvedores de jogos não são capazes de satisfazer o conjunto de habilidades necessárias para liberar um jogo completo, para que um número de disciplinas existe. Tenha em mente que isso não é uma lista completa das áreas de desenvolvimento – apenas algumas das mais comuns.

- **Programador** – a maioria das pessoas lendo este artigo se enquadram nessa categoria. A função de um programador no desenvolvimento de jogos é semelhante à função do programador em um aplicativo não é de jogo. Responsabilidades incluem a criação de lógica para controlar o fluxo de um jogo, o desenvolvimento de sistemas para tarefas comuns no contexto de um determinado projeto, adicionando e exibindo o conteúdo e – obviamente – corrigindo os bugs.
- **Artista 2D** – artistas 2D são responsáveis por criar *ativos 2D*. Isso inclui arquivos de imagem para a interface gráfica do usuário do jogo, partículas, ambientes e caracteres. Se o jogo que você está desenvolvendo 3D, artistas 2D não estejam responsáveis para ambientes e caracteres. Você pode encontrar livre arte para seu jogo no [http://opengameart.org/](http://opengameart.org/) .
- **Artistas 3D** – artistas 3D são responsáveis por criar *ativos 3D*. Isso inclui modelos 3D para propriedades (móveis, plantas e outros objetos inanimados), caracteres e ambientes. Algumas equipes diferenciam artistas 3D e 3D animadores dependendo do tamanho da equipe. Você pode encontrar livre arte 3D para seu jogo no [http://opengameart.org/](http://opengameart.org/) .
- **Designer de jogos** – designers de jogos serão responsáveis por definir como o jogo. Isso pode incluir as decisões de alto nível, como a configuração de jogo, a meta geral do jogo e como um player avança através do jogo. Designers de jogo também podem ser envolvidos em decisões muito detalhadas, como a entrada de mapeamento para ações, definindo os coeficientes para movimentação ou nível-no-break e criação de layout de nível. Lembre-se de que o termo *designer* podem se referir a um designer de jogo ou um designer visual dependendo do contexto.
- **Designer de som** – som designers são responsáveis por recursos de áudio do jogo. Algumas equipes podem diferenciar entre os indivíduos responsáveis pela criação de efeitos de som e compositores, enquanto a equipes menores podem ter um indivíduo responsável por todo o áudio.


# <a name="creating-a-game-idea"></a>Criando uma ideia de jogo

Criar um jogo pode aparecer ser fácil de fazer – afinal o único requisito é "fazer algo divertido." Infelizmente, muitos desenvolvedores veem confuso quando chegar a hora de criar uma ideia do qual iniciar o desenvolvimento.

A disciplina de projeto de jogo não é explicada facilmente e requer prática para melhorar assim como a arte ou faz de programação, mas esta seção pode ajudá-lo a iniciar o caminho.

Novos desenvolvedores devem começar. Pode ser difícil resistir a tentativa para recriar um videogame grande e moderno, mas menores jogos podem ser um ambiente de aprendizado melhor e torna o progresso mais rápido para uma experiência mais compensadora.

Começam a muitos jogos, ambos com a finalidade de jogos de aprendizado, bem como comerciais, como um aperfeiçoamento ou modificação de um jogo existente. É uma maneira de gerar ideias examinar outros jogos para inspirei. Por exemplo, você pode considerar um jogo que você deseja que pessoal e tentar identificar quais características sobre o jogo torná-lo divertido. Pode ser exploração, o domínio de mecanismos do jogo ou em andamento por meio de um texto. Não se esqueça de considerar também jogos "Retrô" durante a pesquisa de novas ideias.

Outra técnica para a geração de novas ideias é considerar um gênero específico, como quebra-cabeça jogos, jogos de estratégia ou platformers. Um gênero familiar para o desenvolvedor pode fornecer um bom ponto de partida.

Refazendo a jogos existentes também é uma experiência educacional, embora isso pode limitar a viabilidade comercial do produto acabado. O processo de criação de um jogo, até mesmo um que seja um clone preciso, fornece uma experiência de ensino valiosa.


# <a name="game-development-technology"></a>Tecnologia de desenvolvimento de jogos

Os desenvolvedores usando xamarin e xamarin têm uma ampla variedade de tecnologias disponíveis para ajudar no desenvolvimento de jogos. Esta seção aborda algumas das soluções mais populares de plataforma cruzada.


## <a name="cocossharp"></a>CocosSharp

CocosSharp é uma versão de software livre, multiplataforma do mecanismo Cocos de jogos 2D. O mecanismo fornece acesso para o Android, iOS, Mac OS X, área de trabalho do Windows, Windows RT e Windows Phone.

CocosSharp enfoca programador API simple para o desenvolvimento de jogos 2D. O crescimento em jogos em dispositivos móveis ajudou a reignite a popularidade do desenvolvimento de jogos 2D fazer CocosSharp a tecnologia viável para hobbies e projetos comerciais da mesma forma. Ele é fornecido como arquivos de código ou. dll de origem (que podem ser obtidos através do NuGet), mas não tem um editor visual; Portanto, qualquer interação com o mecanismo CocosSharp requer conhecimento de programação.

Para começar a usar CocosSharp, Confira nosso [CocosSharp guias](~/graphics-games/cocossharp/index.md).

O jogo irritado Ninjas é criada com CocosSharp e pode ser um bom ponto de partida se você estiver procurando um jogo já em execução para várias plataformas:

![](images/image3.png "O jogo irritado Ninjas foi criado com CocosSharp")

Você pode baixá-lo e obter mais informações o [AngryNinjas Github página](https://github.com/xamarin/AngryNinjas).


## <a name="monogame"></a>MonoGame

MonoGame é uma código-fonte aberto, entre as versões de plataforma da API do XNA da Microsoft. MonoGame pode ser usado para criar jogos para iOS, Android, Mac OS X, Linux, Windows, Windows RT e Windows Phone.

Ao contrário de CocosSharp, MonoGame é tecnicamente não um mecanismo de jogo, mas em vez disso, um desenvolvimento de jogos API. Isso significa que o trabalho com MonoGame requer diretamente gerenciando objetos de jogos manualmente objetos de desenho e implementar objetos comuns como câmeras e *gráficos de cena* (a hierarquia pai-filho entre os objetos de jogos). Para ajudar a compreender a diferença, considere que CocosSharp se baseia no MonoGame. MonoGame generaliza alguns da tecnologia de plataforma específica, como gráficos, renderização e áudio, enquanto CocosSharp fornece código para organizar e implementar a lógica de jogo.

MonoGame não oferece um ambiente de desenvolvimento visual padrão para que trabalhar com MonoGame requer conhecimento de programação.

Os exemplos importantes de jogos usando MonoGame incluem:

FEZ:

![](images/image7.png "FEZ")

Bastiões:

![](images/image8.jpg "Bastiões")

Para começar a trabalhar com MonoGame, vá direto para nosso [MonoGame guias](~/graphics-games/monogame/index.md).


## <a name="urhosharp"></a>UrhoSharp

UrhoSharp é um mecanismo 3D e 2D de alto nível de plataforma cruzada que pode ser usado para criar animadas cenas 2D e 3D para seus aplicativos usando geometrias, materiais, luzes e câmeras.

![](images/urhosharp.gif "UrhoSharp é um mecanismo 3D e 2D de alto nível de plataforma cruzada que pode ser usado para criar cenas 3D e 2D animadas")

Check-out de [UrhoSharp guias](~/graphics-games/urhosharp/index.md) para começar.

## <a name="additional-technology"></a>Tecnologia adicional

As tecnologias realçadas acima é apenas um exemplo das tecnologias disponíveis. Outras tecnologias importantes incluem:

- **Kit de entidade gráfica** – Xamarin fornece suporte para entidade gráfica Kit jogo framework da Apple, que fornece acesso a toda a funcionalidade da API nativa. Como o Kit de entidade gráfica é criada pela Apple de tecnologia, ele fornece profunda integração com o restante do ecossistema do iOS. Obviamente, entidade gráfica Kit não é entre plataformas e ela não pode ser usada no Android. Para obter mais informações sobre como usar o Kit de entidade gráfica, consulte esta postagem: [http://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](http://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit de cena** – Xamarin também oferece suporte para a estrutura de cena Kit da Apple, que simplifica a implementação de elementos gráficos 3D em aplicativos do iOS. Kit de cena também é fornecida pela Apple, para que ele tenha a integração e considerações específico da plataforma mencionadas acima para entidade gráfica Kit de tecnologia. Para obter mais informações sobre o Kit de cena, consulte esta postagem: [http://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](http://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (que representa o Kit de ferramentas de Open) fornece acesso de baixo nível OpenGL para Mac, iOS e Apple hardware. Para obter mais informações sobre OpenTK, consulte a página principal em: [http://www.opentk.com/](http://www.opentk.com/)


# <a name="summary"></a>Resumo

Este artigo aborda os principais conceitos de desenvolvimento de jogos e fornece informações sobre como criar seu primeiro jogo. Depois de concluir este artigo, as próximas etapas são para escolher a tecnologia e começar a trabalhar através de nossa série de tutoriais vinculados nas seções acima apropriadas.

## <a name="related-links"></a>Links relacionados

- [Guias de CocosSharp](~/graphics-games/cocossharp/index.md)
- [Guias de MonoGame](~/graphics-games/monogame/index.md)
- [Guias de UrhoSharp](~/graphics-games/urhosharp/index.md)
