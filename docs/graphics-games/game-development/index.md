---
title: Introdução ao desenvolvimento de jogos com o Xamarin
description: 'Este documento fornece uma visão geral do desenvolvimento de jogos com o Xamarin, que descreve como os jogos são feitos e uma amostragem das tecnologias disponíveis para uso com xamarin. IOS e xamarin. Android.'
ms.prod: xamarin
ms.assetid: 0E3CDCD2-FBE4-49F5-A70E-8A7B937BAF1D
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
---

# <a name="introduction-to-game-development-with-xamarin"></a>Introdução ao desenvolvimento de jogos com o Xamarin

Desenvolvimento de jogos pode ser muito interessante, especialmente considerando como ele pode ser fácil publicar seu trabalho em plataformas móveis. Este artigo aborda os conceitos e tecnologias relacionadas ao desenvolvimento de jogos que ajudará você a criam jogos, se seu objetivo é criar uma alta qualidade AAA, jogo ou apenas para o programa por diversão.

Este artigo aborda os seguintes tópicos:

- **Jogo versus os conceitos de programação não é de jogo** – vamos explorar alguns conceitos que são exclusivas para desenvolvimento de jogos, ou são compartilhados com outros tipos de desenvolvimento, mas merecem ênfase devido à sua importância.
- **Equipe de desenvolvimento de jogos** – esta seção aborda as várias funções em uma equipe de desenvolvedores de jogos.
- **Criando uma ideia de jogo** – nesta seção pode ajudá-lo a criar uma nova ideia de jogo – a primeira etapa na criação de um novo jogo.
- **Tecnologia de desenvolvimento de jogos** – aqui vai listamos alguns das tecnologias de plataforma cruzada disponíveis que podem aumentar sua produtividade como desenvolvedor de jogos.


## <a name="game-vs-non-game-programming-concepts"></a>Jogos vs. Conceitos de programação não é de jogo

Mover para o desenvolvimento de jogos de programadores muitas vezes se confrontam com novos conceitos e padrões de desenvolvimento. Esta seção apresenta uma visão geral de alguns desses conceitos.


### <a name="the-game-loop"></a>O Loop do jogo

Um jogo típico requer constante movimento ou alteração na tela em resposta à interação do usuário e a lógica do jogo automática está ocorrendo. Isso é obtido com o que é normalmente conhecido como um *loop do jogo*. Um loop do jogo é algum tipo de instrução (por exemplo, um loop while) que é executado em uma alta frequência, como 30 ou 60 de looping *quadros por segundo*.

Este é um diagrama de um loop de jogo simple:

![](images/image1.png "Esse é um diagrama de um loop de jogo simple")

As tecnologias que discutiremos abaixo simplificarão o loop while real, mas, apesar dessa abstração o conceito de atualizações de cada quadro estará presente.

Desempenho do código pode levar a prioridade até mesmo da maneira mais simples de jogos. Por exemplo: uma função que leva de 10 milissegundos para executar pode ter um impacto significativo no desempenho de um jogo – especialmente se ele é chamado mais de uma vez por quadro. Se o jogo estiver em execução em 30 quadros por segundo, em seguida, isso significa que cada quadro deve executar em 33 em milissegundos. Por outro lado, essa função pode nem será notada se ela é executada apenas em resposta a um clique de botão em um aplicativo não é de jogo.

Tipos comuns de lógica que pode ser executado cada quadro incluem:

- **Entrada de leitura** – o jogo, talvez seja necessário verificar se o usuário interagiu com o jogo, verificando o hardware de entrada, como a tela sensível ao toque, teclado, mouse ou controlador de jogo.
- **Movimentação** – quais movimentação de um único lugar para outro geralmente moverá muito pequeno de objetos amount cada quadro para dar a ilusão de movimentos suaves.
- **Colisão** – muitos jogos exigem teste frequentes se vários objetos são sobrepostos ou interseção. Abordaremos colisão com mais profundidade em uma seção mais adiante neste artigo. Movimentação e a colisão podem ser manipuladas por um sistema de simulação de física dedicada.
- **Verificação de condições específicas de jogo** – o estado de um jogo pode ser controlado por determinadas condições, como se o jogador alcançou pontos suficientes ou se o tempo alocado está vazio.
- **Comportamento de AI** – lógica de cada quadro que pode ser usada para controlar o comportamento dos objetos que não são controladas pelo player, como o patrolling de um inimigo ou a movimentação dos drivers adversária em torno de um com tamanho ajustável.
- **Renderização** – a maioria dos jogos atualizará o que é exibido na tela de cada quadro. Isso pode ser feito em resposta às alterações que ter um impacto no jogo (como um caractere de movimento através de um nível) ou simplesmente para melhorar a estética visual (como queda de neve ou ícones animados).

Tenha em mente que muitas das atividades listadas acima podem alterar o estado de todo o aplicativo, enquanto que muitos aplicativos não é de jogo tendem a alterar o estado em resposta a eventos que está sendo gerado.


### <a name="content-loading-and-unloading"></a>Carregar conteúdo e descarregar

Conteúdo manualmente Carregando e descarregando (ou descarte) pode ser necessários, dependendo de qual tecnologia você está usando no desenvolvimento. Manualmente, carregamento e descarregamento de ativos podem ser necessários por uma série de motivos:

 - Ativos podem levar muito tempo para carregar relativo ao tamanho de um único quadro. Alguns ativos ainda podem levar segundos para carregar, que gravemente pode atrapalhar a experiência se carregado mid jogo. Se o tempo de carregamento é especialmente longo (por exemplo, mais de um segundo ou dois) você talvez queira mostrar uma imagem ao carregar a barra de progresso ou de tela.
 - Ativos podem consumir muita RAM, que exigem gerenciamento ativo do que é carregado para se ajustarem no que é fornecido por plataformas de destino do jogo.
 - Jogos talvez seja necessário exibir mais ativos que pode caber na RAM. "Abrir o mundo" jogos geralmente incluem grandes ambientes que os jogadores podem navegar pelo perfeitamente – isso é com nenhuma tela de carregamento. Nesse caso, você talvez precise criar um sistema personalizado para o conteúdo de streaming em e gerenciar o uso de memória.

Formatos de arquivo personalizado, talvez seja necessário processamento em tempo de carregamento, exigir que o código de carregamento de personalizado.


### <a name="math"></a>Matemática

Muitos jogos exigem cálculos mais avançados que os aplicativos não é de jogo. É claro, o nível de matemática depende da complexidade do jogo. Em geral, jogos 3D exigem mais matemática que 2D. Felizmente, você sempre pode começar a usar com jogos simples e desde o início. Desenvolvimento de jogos pode ser uma ótima maneira de aprender matemática!

Se você estiver familiarizado com o plano cartesiano – o que está usando coordenadas X e Y para posicionar objetos – em seguida, você sabe o suficiente para começar o desenvolvimento de jogos. O exemplo a seguir mostra um plano cartesiano com positivo Y apontando para cima:

![](images/image2.png "Isso mostra um plano cartesiano com positivo Y apontando para cima")

> [!IMPORTANT]
> Alguns mecanismos/APIs usam um sistema de coordenadas onde aumentar o valor de Y de um objeto irá movê-lo para baixo, enquanto outros sistemas usam um sistema de coordenadas onde Y positivo está ativo. Tenha isso em mente, se você estiver movendo entre sistemas.
Funções trigonométricas (como o seno e cosseno) normalmente são usadas em jogos 2D que implementam qualquer forma de rotação.



Se você estiver planejando fazer um jogo 3D, em seguida, você provavelmente precisará estar familiarizado com conceitos de álgebra Linear (para rotação e movimentação no espaço 3D), bem como alguns cálculo (para a implementação de aceleração).


### <a name="content-pipelines"></a>Pipelines de conteúdo

O termo *pipeline de conteúdo* refere-se ao processo de um arquivo necessário para obter do seu formato quando criado (por exemplo, um arquivo de imagem. png) em seu formato final quando usado em um jogo. O formato final depende em qual tipo de conteúdo está sendo usado, bem como qual tecnologia está sendo usada para apresentar o conteúdo.

Alguns pipelines de conteúdo pode ser muito rápidas e não exigir nenhum esforço manual. Por exemplo, a maioria das APIs e mecanismos de jogos pode carregar o formato de arquivo. PNG em seu formato não processado. Por outro lado, mais complicados formatos (como modelos 3D) talvez precise ser processado em um formato diferente antes que está sendo carregado, e esse processamento pode demorar algum tempo dependendo do tamanho e da complexidade do ativo.


## <a name="game-development-teams"></a>Equipes de desenvolvimento de jogos

Desenvolvimento de jogos apresenta novas funções e títulos para as pessoas envolvidas no processo. A maioria dos desenvolvedores de jogos não são capazes de satisfazer o amplo conjunto de habilidades necessárias para liberar um jogo completo, portanto, um número de disciplinas existe. Tenha em mente que isso não é uma lista completa das áreas de desenvolvimento – apenas algumas das mais comuns.

- **Programador** – a maioria das pessoas lendo este artigo se enquadram nessa categoria. A função de um programador no desenvolvimento de jogos é semelhante à função de um programador em um aplicativo não é de jogo. As responsabilidades incluem a criação de lógica para controlar o fluxo de um jogo, desenvolvimento de sistemas para tarefas comuns no contexto de um determinado projeto, adicionando e exibindo o conteúdo e – obviamente – correção de bugs.
- **Artista 2D** – 2D artistas são responsáveis pela criação *ativos 2D*. Isso inclui arquivos de imagem para GUI do jogo, partículas, ambientes e caracteres. Se o jogo que você está desenvolvendo for 3D, então artistas 2D podem não ser responsáveis por ambientes e caracteres. Você pode encontrar livre arte para o seu jogo no [ http://opengameart.org/ ](http://opengameart.org/) .
- **3D artistas** – artistas 3D são responsáveis pela criação *ativos 3D*. Eles incluem modelos 3D para ambientes, caracteres e objetos (móveis, plantas e outros objetos inanimados). Algumas equipes de diferenciam entre artistas 3D e animadores 3D, dependendo do tamanho da equipe. Você pode encontrar livre arte 3D para o seu jogo no [ http://opengameart.org/ ](http://opengameart.org/) .
- **Designer de jogos** – designers de jogos são responsáveis por definir como o jogo. Isso pode incluir as decisões de alto nível, como a configuração do jogo, o objetivo geral do jogo e como um jogador progride através do jogo. Designers de jogo também podem ser envolvidos nas decisões muito detalhadas, como a entrada de mapeamento para ações, definindo os coeficientes para movimentação ou nível-no-break e criação de layout de nível. Tenha em mente que o termo *designer* podem se referir a um jogo designer ou um designer visual, dependendo do contexto.
- **Designer de som** – designers de som são responsáveis por ativos de áudio de um jogo. Algumas equipes podem diferenciar entre os indivíduos responsáveis pela criação de efeitos de som e compositores, enquanto as equipes menores podem ter um único indivíduo responsável por todo o áudio.


## <a name="creating-a-game-idea"></a>Criando uma ideia de jogo

Projetar um jogo pode aparecer ser fácil de fazer – afinal o único requisito é "tornar algo divertido". Infelizmente, muitos desenvolvedores se veem em uma perda quando chega o momento de criar uma ideia do qual iniciar o desenvolvimento.

A disciplina de projeto de jogo não é explicada com facilidade e requer prática para melhorar assim como a arte ou faz programação, mas esta seção pode ajudá-lo a iniciar o caminho.

Novos desenvolvedores devem começar pequeno. Pode ser difícil resistir à tentação de recriar um jogo de vídeo grande e moderno, mas jogos menores podem ser um melhor ambiente de aprendizagem e faz com que o progresso mais rápido para uma experiência mais gratificante.

Começarem a muitos jogos, ambos com a finalidade de jogos de aprendizado, bem como comerciais, como um aperfeiçoamento ou modificação de um jogo existente. Uma maneira para gerar ideias é examinar outros jogos inspiração. Por exemplo, você pode considerar um jogo de sua preferência pessoal e tente para identificar quais características a jogabilidade torna divertido. É possível exploração, dominar mecânica do jogo ou está em andamento por meio de uma história. Não se esqueça de considerar também jogos "Retrô" ao pesquisar novas ideias.

Outra técnica para a geração de novas ideias é considerar um gênero específico, como jogos de quebra-cabeça, jogos de estratégia ou jogos eletrônicos de plataformas. Um gênero familiar para o desenvolvedor pode fornecer um bom ponto de partida.

Refazendo a jogos existentes também é uma experiência educacional, embora isso pode limitar a viabilidade de comercial do produto acabado. O processo de criação de um jogo, mesmo um que é um clone preciso, fornece uma experiência de educacional valiosa.


## <a name="game-development-technology"></a>Tecnologia de desenvolvimento de jogos

Os desenvolvedores que usam o xamarin. Android e xamarin. IOS têm uma ampla variedade de tecnologias disponíveis a eles para ajudar no desenvolvimento de jogos. Esta seção aborda algumas das mais populares soluções de plataforma cruzada.


### <a name="cocossharp"></a>CocosSharp

CocosSharp é uma versão de software livre, plataforma cruzada do mecanismo de jogos 2D Cocos. O mecanismo fornece acesso ao Android, iOS, Mac OS X, área de trabalho do Windows, Windows RT e Windows Phone.

CocosSharp se concentra em um programador simple API para desenvolvimento de jogos 2D. O crescimento em jogos em dispositivos móveis tenha ajudado a reignite a popularidade do desenvolvimento de jogos 2D, tornando a tecnologia viável CocosSharp para hobby e comerciais semelhantes de projetos. Ele é fornecido como arquivos de código ou. dll de origem (que podem ser obtidos por meio do NuGet), mas ele não oferece um editor visual; Portanto, qualquer interação com o mecanismo de CocosSharp requer conhecimento de programação.

Para começar com CocosSharp, Confira nossos [CocosSharp guias](~/graphics-games/cocossharp/index.md).

O jogo Ovelha Ninjas é criado com CocosSharp e pode ser um bom ponto de partida se você estiver procurando um jogo já em execução para várias plataformas:

![](images/image3.png "O jogo Ninjas Ovelha foi criado com CocosSharp")

Você pode baixá-lo e obter mais informações o [página do AngryNinjas Github](https://github.com/xamarin/AngryNinjas).


### <a name="monogame"></a>MonoGame

MonoGame é uma software livre, plataforma de versão da API do XNA da Microsoft. MonoGame pode ser usado para criar jogos para iOS, Android, Mac OS X, Linux, Windows, Windows RT, PS4, PSVita, Xbox One e comutador.

Ao contrário de CocosSharp, MonoGame é tecnicamente não um mecanismo de jogo, mas em vez disso, um jogo de desenvolvimento de API. Isso significa que o trabalho com MonoGame requer diretamente gerenciando objetos do jogo, manualmente objetos de desenho e implementar objetos comuns, como câmeras e *gráficos da cena* (a hierarquia pai-filho entre objetos do jogo). Para ajudar a compreender a diferença, considere que CocosSharp se baseia no MonoGame. MonoGame generaliza algumas das tecnologias específicas da plataforma, como gráficos, renderização e áudio, enquanto CocosSharp fornece código para organizar e implementar a lógica do jogo.

MonoGame não oferece um ambiente de desenvolvimento visual padrão, portanto, trabalhar com MonoGame requer conhecimento de programação.

Exemplos importantes de jogos usando o MonoGame incluem:

FEZ:

![](images/image7.png "FEZ")

Bastiões:

![](images/image8.jpg "Bastiões")

Para começar a trabalhar com o MonoGame, vá até nossa [MonoGame guias](~/graphics-games/monogame/index.md).


### <a name="urhosharp"></a>UrhoSharp

UrhoSharp é um mecanismo 3D e 2D de alto nível de plataforma cruzada que pode ser usado para criar animadas cenas 3D e 2D para seus aplicativos usando geometrias, material, luzes e câmeras.

![](images/urhosharp.gif "UrhoSharp é um mecanismo 3D e 2D de alto nível de plataforma cruzada que pode ser usado para criar animadas cenas 3D e 2D")

Confira a [UrhoSharp guias](~/graphics-games/urhosharp/index.md) para começar a usar.

### <a name="additional-technology"></a>Tecnologia adicional

As tecnologias realçadas acima é apenas uma amostra das tecnologias disponíveis. Outras tecnologias importantes incluem:

- **Sprite Kit** – Xamarin fornece suporte para a estrutura da Apple Sprite Kit jogo, que fornece acesso a toda a funcionalidade da API nativa. Como o Sprite Kit é uma tecnologia criada pela Apple, ele fornece uma integração profunda com o resto do ecossistema do iOS. É claro, Sprite Kit não é plataforma cruzada para que ele não pode ser usado no Android. Para obter mais informações sobre como usar o Sprite Kit, consulte esta postagem:  [https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/](https://blog.xamarin.com/make-games-with-xamarin.ios-and-sprite-kit/)
- **Kit de cena** – Xamarin também oferece suporte para estrutura Scene Kit da Apple, que simplifica a implementação de elementos gráficos 3D em aplicativos do iOS. Scene Kit também é fornecida pela Apple, para que ele tenha a integração e considerações específicas da plataforma mencionadas acima para Sprite Kit de tecnologia. Para obter mais informações sobre o Kit de cena, consulte esta postagem: [https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/](https://blog.xamarin.com/3d-in-ios-8-with-scene-kit/)
- **OpenTK –** OpenTK (que representa o Kit de ferramentas aberta) fornece acesso de baixo nível OpenGL para Mac, iOS e Apple hardware. Para obter mais informações sobre OpenTK, consulte a página principal em:  [http://www.opentk.com/](http://www.opentk.com/)

## <a name="related-links"></a>Links relacionados

- [Guias de CocosSharp](~/graphics-games/cocossharp/index.md)
- [Guias de MonoGame](~/graphics-games/monogame/index.md)
- [Guias de UrhoSharp](~/graphics-games/urhosharp/index.md)
