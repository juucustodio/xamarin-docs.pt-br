---
title: Trabalhando com ícones e imagens no Xamarin do tvOS
description: Este documento descreve como trabalhar com ícones e imagens em um aplicativo tvOS criados com o Xamarin. Ele aborda as imagens de inicialização, imagens em camadas, o ícone do aplicativo e muito mais.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: dab1b0f7bf7aabb4dfcfbfdcb5e202baa48e664d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865162"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Trabalhando com ícones e imagens no Xamarin do tvOS

Criando cativante ícones e imagens são uma parte crítica do desenvolvimento de uma experiência de usuário envolventes para seus aplicativos da Apple TV. Este guia aborda as etapas necessárias para criar e incluir os ativos gráficos necessários para seus aplicativos xamarin. tvos:

- [Imagem de inicialização](#Launch-Image) -uma imagem de inicialização é exibido quando seu aplicativo é iniciado pela primeira vez e é substituído pela primeira tela do aplicativo após a conclusão da inicialização.
- [Em camadas imagens](#Layered-Images) – específicos para a Apple TV, novos trabalhos de imagens em camadas da Apple com o efeito da Parallax para criar um efeito 3D para itens selecionados. Há várias maneiras [criar imagens em camadas](#Creating-Layered-Images).
- [Ícone do aplicativo](#App-Icons) -ícones são necessários para a tela não somente o Apple TV Home, mas não para a App Store. Elas devem ser fornecidas como uma imagem em camadas.
- [Imagem da prateleira de principais](#Top-Shelf-Image) -se seu aplicativo é colocado na linha superior da tela inicial, ele precisará de uma imagem da prateleira superior para destacar os recursos do seu aplicativo. Opcionalmente, você pode fornecer [conteúdo dinâmico de prateleira superior](#Dynamic-Top-Shelf-Content) para realçar o conteúdo em seu aplicativo.
- [Centro de imagens de jogos](#Game-Center-Images) -se seu aplicativo é um jogo e usa o Game Center, várias imagens adicionais será necessárias.
- [Definindo imagens de projeto do xamarin. tvos](#Setting-Xamarin.tvOS-Project-Images) -aborda as etapas necessárias para definir o ícone do aplicativo e imagens de inicialização para seu aplicativo xamarin. tvos.

> [!IMPORTANT]
> Todas as imagens na Apple TV estão com a resolução de 1 x (`@1x`) e você deverá _somente_ usar imagens desse tamanho. Maior, incluindo gráficos de alta resolução não só levam tempo para baixar e usar mais memória e armazenamento, mas precisa ser redimensionados dinamicamente em tempo de execução e afetará negativamente o desempenho do desenho.

<a name="Launch-Image" />

## <a name="launch-image"></a>Imagem de inicialização

A imagem de inicialização é a primeira coisa que é exibida quando seu aplicativo tvos inicialmente é iniciado na Apple TV e, assim, cada aplicativo tvOS deve fornecer uma imagem de inicialização. 

A imagem de inicialização aparece rapidamente e dá a impressão de que seu aplicativo seja rápido e responsivo. A Apple TV substituirá a imagem de inicialização com a primeira tela do seu aplicativo em breve lá após.

Imagens de inicialização não são uma oportunidade para anúncios ou expressão artístico, elas existem somente para dar a impressão de que seu aplicativo é iniciado rapidamente e está pronto para usar.

|Inicie o tamanho da imagem|Observações|
|---|---|
|1920x1080px|Somente os arquivos em camadas não PNG|

A Apple deixa as sugestões a seguir para a criação de imagens de inicialização do aplicativo:

- **Quase idêntico à primeira tela** -sua tela de inicialização deve ser o mais próximo a primeira tela do aplicativo possível. Incluindo o elemento ou elementos gráficos diferentes pode resultar em um irritantes "flash" quando a primeira tela exibida.
- **Evite usar texto** -imagens de inicialização são estáticos e como tal, não serão localizadas antes de serem exibidos.
- **Minimizará inicialização** -usuários de Apple TV porque frequentemente mudam de aplicativos, você não deve chamar a atenção para o processo de inicialização do aplicativo.
- **Sem anúncios ou a identidade visual** -Your imagens de inicialização não deve ser usado como uma tela sobre ou incluir qualquer identidade visual, a menos que ele faz parte estática da primeira tela do seu aplicativo. Anúncios são estritamente proibidos.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Definir a imagem de inicialização

Para definir as imagens de inicialização para seu projeto de tvOS, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes em `Assets.xcassets` para abri-lo para edição: 

    [![](icons-images-images/asset01.png "O arquivo do xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor de ativos**, clique no `LaunchImages` ativo: 

    [![](icons-images-images/asset02.png "O ativo LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Clique no **1X Apple TV** entrada e selecione a imagem de inicialização ou, opcionalmente, arraste uma nova imagem do sistema de arquivos: 

    [![](icons-images-images/asset03.png "Selecione uma imagem de inicialização")](icons-images-images/asset03.png#lightbox)
4. Salve as alterações.

<a name="Layered-Images" />

## <a name="layered-images"></a>Imagens em camadas

Novo para a Apple TV, o trabalho de imagens em camadas com o efeito da Parallax para produzir um efeito 3D que ajuda a manter o usuário, no sofá mentalmente conectado ao conteúdo na tela dentro de uma sala.

Contenham imagens em camadas de dois (2) a cinco (5) separe as camadas que são combinadas para formar uma imagem completa. Com exceção da camada de plano de fundo, cada camada usa sua ordem Z, juntamente com a transparência para criar a ilusão de profundidade. Quando o usuário interage com uma imagem em camadas, camadas superiores ordenados de Z são dimensionadas e sobrepostas para criar esse efeito.

[![](icons-images-images/layered01.png "Diagrama de ordenados de Z de imagens em camadas")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Imagens em camadas são necessárias para os ícones do aplicativo e são opcionais para outros [focalizável itens](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (por exemplo, a imagem da prateleira superior). No entanto, a Apple sugere o uso de imagens em camadas para qualquer imagem que pode receber o foco em seu aplicativo.




A Apple deixa as sugestões a seguir para a criação de suas imagens em camadas:

- **Certifique-se a camada de plano de fundo opaco** -a camada de plano de fundo (camada 1) **deve** ser opaco, ou você obterá um erro ao tentar usar a imagem em camadas na Apple TV. Todas as outras camadas podem conter vários níveis de transparência para aprimorar o efeito em 3D.
- **Isolar o primeiro plano, intermediária e elementos de plano de fundo** -colocar elementos proeminentes (como caracteres de jogos) em primeiro plano, use o meio para elementos secundários ou sombras. Finalmente, inclua um plano de fundo neutro para fornecer um estágio para suas camadas superiores.
- **Manter o texto em primeiro plano** -, a menos que você deseja que seu texto ser obscurecido por níveis mais altos, geralmente ele deve ser na camada mais alta.
- **Use a disposição em camadas simples** -o efeito da Parallax foi projetado para ser sutil então manter suas camadas ao mínimo para evitar efeitos irreais brusca.
- **Incluir uma zona segura** -como camadas superiores podem ser cortadas durante um efeito Parallax, você precisará criar uma borda de zona de segurança em cada camada. Se você receber o seu conteúdo muito próximos à borda de camadas, ele pode se tornar cortado. Camadas superiores terão mais de escala e de corte que as camadas inferiores. Consulte a [camadas de imagem do dimensionamento](#Sizing-Image-Layers) seção abaixo.
- **Visualizar geralmente** -imagens em camadas deve ser visualizado com frequência para garantir que o efeito desejado de 3D ocorre e que nenhuma do conteúdo nas camadas individuais que está sendo cortada. Você deve visualizar suas imagens em camadas em hardwares reais da Apple TV, para garantir que eles sejam renderizados conforme o esperado.

Sempre que possível, você deve usar sempre o interno `UIKit` controles para exibir suas imagens em camadas, pois eles receberão automaticamente o efeito da Parallax quando eles entrarem no foco.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Camadas de imagem de dimensionamento

É importante lembrar-se de incluir um _Safe zona_ borda em cada camada que será compor sua imagem em camadas. Porque as camadas individuais podem ser dimensionadas e cortadas durante o efeito da Parallax, o conteúdo das camadas pode ser cortado se ele estiver muito próximo à borda da camada:

[![](icons-images-images/layered02.png "borda de 35 pixels")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Criação de imagens colocadas em camadas

tvOS funciona com as imagens em camadas nos seguintes formatos:

- **Arquivos de CARRO** -isso é um formato proprietário do catálogo de ativos criado pela Apple. Você não cria arquivos de CARRO diretamente, eles são criados em tempo de compilação de todos os arquivos LSR e incluídos em seu pacote de aplicativos.
- **Imagens LSR** -este é um formato de imagem proprietárias criado pela Apple. Use o [Parallax exportador Adobe Photoshop plug-in](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) ou o [Parallax pré-visualizador](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para criar e visualizar as imagens em camadas no formato LSR.
- **Xcassets** – de dois (2) a cinco (5) padrão `.png` imagens formatadas incluídas em um catálogo de ativos que serão compilados em um CARRO ou LSR formatado em camadas de imagem no tempo de compilação.
- **Arquivos de LCR** -isso é um formato de arquivo criado pela Apple. Arquivos LCR destinam-se a ser usado como conteúdo adicional baixado de um dos seus servidores de conteúdo. Arquivo LCR nunca deve ser incluído em seu pacote de aplicativos. Arquivos LCR são gerados de arquivos do Photoshop ou de LSR usando o `layerutil` ferramenta de linha de comando incluída no Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>O pré-visualizador Parallax

Apple criada o [Parallax pré-visualizador](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para visualização e imagens criadas em camadas, necessários para os ícones de aplicativos e itens focalizável opcionais. O pré-visualizador mostra todas as camadas que formam a imagem completa em camadas:

[![](icons-images-images/layered03.png "O pré-visualizador Parallax")](icons-images-images/layered03.png#lightbox)

Enquanto estiver visualizando uma imagem em camadas, você pode usar o mouse para girar a imagem e visualizar o efeito da Parallax. Use o **+** (adição) e **-** (botões Adicionar e remover as camadas menos).

Ao criar uma nova imagem em camadas, podem ser exportado no formato LSR e incluído no pacote do aplicativo.

Para obter mais informações sobre como criar e visualizar imagens em camadas, consulte da Apple [criação de arte da Parallax](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) seção o [guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Ícones do aplicativo

Seu aplicativo tvos exige não apenas um ícone de aplicativo para a tela inicial para Apple TV, mas também um ícone para a App Store. O ícone do aplicativo é o seu primeiro alterar para tornar uma boa impressão em seu usuário possíveis e preciso comunicar-se a finalidade do seu aplicativo em um relance.

[![](icons-images-images/icon01.png "O ícone do aplicativo")](icons-images-images/icon01.png#lightbox)

Cada aplicativo deve fornecer uma pequena e uma versão grande do seu ícone de aplicativo. Quando o aplicativo é instalado, o ícone pequeno será usado na tela de início para Apple TV. A versão grande é usada pelo Store do aplicativo. O ícone grande do aplicativo deve simular a aparência da versão do ícone pequeno.

|Ícone pequeno||Ícone grande||
|---|---|---|---|
|Tamanho real|400x240px|Tamanho|1280x768px|
|Tamanho da zona de segurança|370x222px|||
|Tamanho sem foco|300x180px|||
|Tamanho de foco|370x222px|||

> [!IMPORTANT]
> Os ícones de aplicativo deve ser fornecidos como **imagens em camadas**. Consulte a [em camadas de imagem](#Layered-Images) seção acima para obter mais detalhes.




A Apple fornece as seguintes sugestões para a criação de seus ícones do aplicativo:

- **Fornecer um ponto único de foco** – Design seu ícone com um ponto único de foco são colocados diretamente no centro do ícone.
- **Usar um plano de fundo simples** – mantenha seu plano de fundo do ícone simples, de modo que as camadas superiores se destacam. Considere o uso de uma cor simples ou o gradiente sutil.
- **Limitar a quantidade de texto** – desde que o nome do aplicativo será exibida abaixo do ícone quando ele é selecionado pelo usuário, você somente deve incluir o texto quando ele é essencial para o design do ícone.
- **Não use capturas de tela** – capturas de tela são complexas demais para um ícone e não permitir que o usuário veja a finalidade do seu aplicativo em um relance.
- **Quadrado de ícones de Keep** – tvOS aplica automaticamente uma máscara que sutilmente Arredonda os cantos de seus ícones. Não inclua esse arredondado.
- **Separar cuidadosamente seu camadas** – texto deve estar no canto superior a maioria de camada, itens secundárias no meio e uma tela de fundo neutra que permite que suas camadas superiores desenvolvido para.
- **Usar gradientes e sombras cuidadosamente** – gradientes e sombras podem estar em conflito com o efeito da Parallax para que eles devem ser usados com cuidado. Simple de cima para baixo, estilos de gradação de claro para escuro funcionam melhor. Sombras normalmente funcionam melhor como arestas, agudos tons.
- **Variar a transparência da camada** – Use vários níveis de transparência em níveis superiores de seu ícone do aplicativo para aumentar o efeito em 3D. A camada de plano de fundo deve ser opaca ou resultará em um erro.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Definir os ícones de aplicativo

Para definir os ícones de aplicativo necessários para o projeto de tvOS, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes em `Assets.xcassets` para abri-lo para edição: 

    [![](icons-images-images/asset01.png "A fileg xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor de ativos**, expanda o `App Icon & Top Shelf Image` ativo: 

    [![](icons-images-images/asset04.png "Expanda o ativo de imagem da prateleira superior")](icons-images-images/asset04.png#lightbox)
3. Em seguida, expanda o `App Icon - Small` ativo: 

    [![](icons-images-images/asset05.png "Expanda o ícone do aplicativo - ativo pequeno")](icons-images-images/asset05.png#lightbox)
4. Em seguida, expanda o `Back` ativo e clique no `Contents` entrada: 

    [![](icons-images-images/asset06.png "Em seguida, expanda o ativo Back")](icons-images-images/asset06.png#lightbox)
5. Clique no **1X a entrada de Apple TV** e selecione um arquivo de imagem.
6. Repita as etapas acima para o `Front` e `Middle` ativos.
7. Em seguida, repita as mesmas etapas para definir o `App Icon - Large` ativo.
8. Salve as alterações.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Imagem da prateleira superior

Se o usuário tiver colocado o seu aplicativo tvos na linha superior na tela de início para Apple TV, uma imagem da prateleira superior grande será exibida quando seu aplicativo é selecionado pelo usuário. Essa imagem deve realçam os recursos do seu aplicativo ou fornecem links diretos para o seu conteúdo.

[![](icons-images-images/topshelf01.png "Exemplo de imagem da prateleira superior")](icons-images-images/topshelf01.png#lightbox)

A imagem da prateleira superior pode ser fornecida como um único estático `.png` ou `.lsr` arquivo (consulte [criando imagens em camadas](#Creating-Layered-Images)) ou ele pode ser criado dinamicamente em tempo de execução como uma única linha de itens focalizável (consulte [ Conteúdo dinâmico prateleira superior](#Dynamic-Top-Shelf-Content) abaixo).

|Tamanho de imagem da prateleira superior|Observações|
|---|---|
|1920x720px|PNG estático ou arquivo em camadas .lsr|

A Apple fornece as seguintes sugestões para a criação de suas imagens de prateleira superior:

- **Use imagens estáticas de Rich** – se seu aplicativo não fornecer um conteúdo dinâmico, sua imagem da prateleira superior será não focalizável. Use essa imagem para realçar os recursos do aplicativo ou sua marca.
- **Link para o conteúdo do aplicativo** – Layouts de prateleira superior dinâmicos fornecem um link rápido para o conteúdo que o usuário encontra mais importante em seu aplicativo. Use esta área para fornecer um link rápido para iniciar seu aplicativo e saltar imediatamente para o conteúdo em questão.
- **Apresentar o conteúdo mais recente** – conteúdo de Rich Top Shelf pode desenhar um usuário em seu aplicativo e torná-los para usá-lo mais. Use isso como uma área para apresentar o conteúdo mais recente ou classificado mais alta.
- **Personalizadas conteúdo** – suas mais usados de local de usuários ou aplicativos favoritos na linha superior da tela inicial. Use prateleira superior para exibir o conteúdo que seria mais interessados.
- **Anúncios não permitido** – anúncios são estritamente proibidos seja exibida na prateleira superior. Você pode mostrar o conteúdo mais recente disponível para compra, mas nenhuma informação de preço deve ser exibida.

### <a name="setting-the-top-shelf-image"></a>Definir a imagem da prateleira superior

Para definir a imagem da prateleira superior necessários para o projeto de tvOS, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes em `Assets.xcassets` para abri-lo para edição: 

    [![](icons-images-images/asset01.png "O arquivo do xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor de ativos**, expanda o `App Icon & Top Shelf Image` ativo: 

    [![](icons-images-images/asset04.png "Expanda o ativo de imagem da prateleira superior")](icons-images-images/asset04.png#lightbox)
3. Clique no `Top Shelf Image` ativo: 

    [![](icons-images-images/asset07.png "O ativo de imagem da prateleira superior")](icons-images-images/asset07.png#lightbox)
4. Clique no **1X a entrada de Apple TV** e selecione um arquivo de imagem.
5. Salve as alterações.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Conteúdo dinâmico prateleira superior

Em vez de exibir uma imagem estática de prateleira superior, prateleira superior pode conter uma linha dinâmica dos [focalizável itens](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) ou um conjunto dinâmico de faixas de rolagem. Ambos esses estilos dinâmica permitem que você realce o conteúdo fornecido pelo seu aplicativo ou um salto para seus recursos mais usados.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Linha dividida em seções de conteúdo

Esse tipo de conteúdo dinâmico prateleira superior apresenta uma única linha de rolagem, itens focalizável, opcionalmente, dividido em seções. Ele normalmente é usado para realçar novo, Favoritos ou exibidos recentemente o conteúdo do aplicativo.

O conteúdo é apresentado como uma única lista rolagem horizontal do conteúdo com um rótulo que aparece sob o item atual de conteúdo selecionado (que tem o foco no momento). Se o usuário seleciona uma determinada parte do conteúdo, seu aplicativo será iniciado e eles devem ser levados diretamente em que o conteúdo.

Os seguintes tamanhos de conteúdo serão necessários:

||Cartaz (2:3)|Quadrado (1:1)|HDTV (16:9)|
|---|---|---|---|
|Tamanho real|404x608px|608x608px|908x512px|
|Tamanho da zona de segurança|380x570px|570x570px|852x479px|
|Tamanho sem foco|333x500px|500x500px|782x440px|
|Tamanho de foco|380x570px|570x570px|852x479px|

A Apple fornece as seguintes sugestões para a linha divididas em seções de conteúdo:

- **Completar a linha** – você deve fornecer conteúdo suficiente para abranger a largura total da tela.
- **Dimensionando imagens misto** – linha The divididas em seções Content foi projetada para conter uma mistura de tamanhos de imagem (na lista fornecida acima). Se você mesclar os tamanhos de imagem, no entanto, lembre-se de que a escala adicional será aplicada para normalizar a exibição de conteúdo.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Rolagem faixas de baixo-relevo

Opcionalmente, seu aplicativo tvos pode apresentar seu conteúdo na prateleira superior como um rolagem automaticamente e a coleção de faixas que quase preencher a tela de loop. Esse estilo normalmente é usado para exibir o conteúdo de rich, novo, como novos programas de TV.

Além de rolagem automática, o usuário pode assumir o controle das faixas de e rolar em qualquer direção usando Siri remoto. Fazer um pequeno, gesto circular no Siri remoto quando uma faixa está em foco ativará o efeito da Parallax para essa faixa.

**Imagem da faixa (Wide Extra)**

|   |   |
|---|---|
|Tamanho real|1940x624px|
|Tamanho da zona de segurança|1740x620px|
|Tamanho sem foco|1740x560px|
|Tamanho de foco|1740x620px|

Baixo-relevo faixas de rolagem pode a ser fornecida como estático `.png` ou em camadas `.lsr` arquivo.

A Apple fornece as seguintes sugestões para faixas de baixo-relevo rolagem:

- **Quantidade de conteúdo** -você deve fornecer um mínimo de faixas de três (3) para a rolagem para naturais. Você deve incluir faixas de não mais do que oito (8) ou ele dificultar navegação para o usuário final.
- **Texto de conteúdo** - se a sua faixa requer o texto em deve ser incluído na imagem do banner. Se você estiver usando imagens em camadas, o texto deve ser da camada superior.

Consulte da Apple [referência de estrutura TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obter mais informações sobre como adicionar uma extensão da prateleira superior ao seu aplicativo para fornecer conteúdo dinâmico da prateleira superior.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Imagens do Game Center

Se seu aplicativo tvos é um jogo e foi incluído suporte Game Center, vários ativos de imagem mais serão necessários:

- **Ícones de medalha** -uma imagem opaca é necessária para cada conquista será cortada automaticamente em um círculo. Realizações são itens não focalizável.
- **Arte do painel** -uma imagem opcional pode ser fornecido que será exibido na parte superior do painel de controle de seu aplicativo no Game Center. Essas imagens são não focalizável.
- **Arte de placar de líderes** -você deve fornecer entre um (1) a três (3) imagens de taxa de proporção de 16: 9 para cada placar de líderes que dá suporte a seu aplicativo. Elas podem ser estáticos `.png` ou em camadas `.lsr` arquivos. A arte de placar de líderes é focalizável.

||Ícones de medalha|Arte de painel|Arte de placar de líderes|
|---|---|---|---|
|Tamanho visível|200x200px|923x150px|N/D|
|Tamanho real|320x320px|N/D|659x371px|
|Tamanho da zona de segurança|N/D|N/D|618x348px|
|Tamanho sem foco|N/D|N/D|548x309px|
|Tamanho de foco|N/D|N/D|618x348px|

Para obter mais informações sobre como trabalhar com o Game Center, consulte da Apple [guia de programação do Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html).

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Trabalhando com imagens

Como tvOS 9 é um subconjunto do iOS 9, as mesmas técnicas usadas para incluir e exibir imagens em um aplicativo xamarin. IOS, também funcionam para um aplicativo xamarin. tvos. Consulte nosso [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documentação para obter mais informações.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Definindo imagens de projeto do xamarin. tvos

Como mencionado acima, todos os aplicativos de tvOS exigem uma [imagens de inicialização](#Launch-Image), e [ícone do aplicativo](#App-Icons). Esta seção aborda selecionando o ícone do aplicativo e imagens de inicialização para seu projeto de aplicativo tvos, depois que elas foram definidas em um catálogo de ativos.

Faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o `Info.plist` para abri-lo para edição: 

    [![](icons-images-images/info01.png "O arquivo Info. plist")](icons-images-images/info01.png#lightbox)
2. No **Editor de info. plist**, selecione o catálogo de ativos (configurado acima na [definindo os ícones de aplicativo](#Setting-the-App-Icons) seção) para o **ícones do aplicativo**: 

    [![](icons-images-images/info02.png "O Editor de info. plist")](icons-images-images/info02.png#lightbox)
3. Em seguida, selecione o catálogo de ativos (configurado acima na [definindo a imagem de inicialização](#Setting-the-Launch-Image) seção) para o **imagens de inicialização**.
4. Salve as alterações.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou todos os tipos de imagem e tamanhos usados em um aplicativo xamarin. tvos. Primeiro, ele abordou a imagens de inicialização, imagens em camadas, os ícones de aplicativos, imagens de prateleira superior e imagens do Game Center. Em seguida, ele abordou a trabalhar com imagens em seu aplicativo xamarin. tvos.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
