---
title: Trabalhando com tvOS ícones e imagens no Xamarin
description: Este documento descreve como trabalhar com imagens em um aplicativo de tvOS criados com o Xamarin e ícones. Ele aborda as imagens de inicialização, imagens em camadas, o ícone do aplicativo e muito mais.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 59cbc53acf3ab7da12826b9d3cffb821631a0500
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788791"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Trabalhando com tvOS ícones e imagens no Xamarin

Criando cativante ícones e imagens são uma parte crítica do desenvolvimento de uma experiência de usuário envolventes para seus aplicativos da Apple TV. Este guia aborda as etapas necessárias para criar e incluir os ativos de gráficos necessários para seus aplicativos Xamarin.tvOS:

- [Imagem de inicialização](#Launch-Image) -uma imagem de inicialização é exibido quando seu aplicativo é iniciado pela primeira vez e é substituído pela primeira de tela do aplicativo após a conclusão da inicialização.
- [Em camadas imagens](#Layered-Images) - específicas para Apple TV, novos trabalhos de imagens em camadas da Apple com o efeito da Parallax para criar um efeito 3D para itens selecionados. Há várias maneiras de [criar imagens em camadas](#Creating-Layered-Images).
- [Ícone do aplicativo](#App-Icons) -ícones são necessários para a tela não apenas o Apple TV Home, mas para a loja de aplicativos. Elas devem ser fornecidas como uma imagem em camadas.
- [Principais prateleira imagem](#Top-Shelf-Image) -se seu aplicativo é colocado na linha superior da tela inicial, será necessário uma imagem de prateleira superior para destacar os recursos do aplicativo. Opcionalmente, você pode fornecer [conteúdo dinâmico de prateleira superior](#Dynamic-Top-Shelf-Content) para realçar o conteúdo em seu aplicativo.
- [Imagens do centro do jogo](#Game-Center-Images) -se o aplicativo é um jogo e usa Game Center, várias imagens adicionais será necessárias.
- [Definindo imagens de projeto Xamarin.tvOS](#Setting-Xamarin.tvOS-Project-Images) -abrange as etapas necessárias para configurar a imagem de inicialização e o ícone do aplicativo para seu aplicativo Xamarin.tvOS.

> [!IMPORTANT]
> Todas as imagens na Apple TV estão na resolução 1 x (`@1x`) e você deve _somente_ usar imagens desse tamanho. Incluindo maior, gráficos de resolução mais alta não só levam tempo para baixar e usar mais memória e armazenamento, mas precisa ser redimensionada dinamicamente em tempo de execução e afetará negativamente o desempenho do desenho.

<a name="Launch-Image" />

## <a name="launch-image"></a>Imagem de inicialização

A imagem de inicialização é a primeira coisa que é exibida quando seu aplicativo Xamarin.tvOS inicialmente é iniciado no Apple TV, e como tal, todos os aplicativos de tvOS devem fornecer uma imagem de inicialização. 

A imagem de inicialização é exibido rapidamente e fornece a impressão de que seu aplicativo é rápido e capacidade de resposta. A Apple TV substituirá a imagem de inicialização com a primeira tela do aplicativo em breve existe depois.

Imagens de inicialização não são uma oportunidade para anúncios ou expressão artístico, eles existem somente para dar a impressão de que seu aplicativo é iniciado rapidamente e está pronto para usar.

|Tamanho da imagem de inicialização|Observações|
|---|---|
|1920x1080px|Somente arquivos. PNG em camadas não|

Apple torna as sugestões a seguir para criar a imagem de inicialização do aplicativo:

- **Quase idêntico à primeira tela** -sua tela de início deve ser o mais próximo possível primeira tela do seu aplicativo. Incluindo gráficos diferentes ou elemento pode resultar em um irritantes "flash" quando a primeira tela exibida.
- **Evite usar texto** -imagens de inicialização são estáticos e como tal, não serão localizados antes de ser exibido.
- **Minimizará inicialização** -usuários porque Apple TV frequentemente alternam aplicativos, você não deve chamar a atenção para o processo de inicialização do aplicativo.
- **Não há anúncios ou marca** -sua imagem de inicialização não deve ser usado como uma tela sobre ou incluir qualquer identidade visual, a menos que é parte estática do primeira tela do seu aplicativo. Anúncios são estritamente proibidos.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Definir a imagem de inicialização

Para definir a imagem de inicialização para o seu projeto tvOS, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes em `Assets.xcassets` para abri-lo para edição: 

    [![](icons-images-images/asset01.png "O arquivo Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor ativo**, clique no `LaunchImages` ativo: 

    [![](icons-images-images/asset02.png "O ativo LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Clique no **1 x Apple TV** entrada e selecione a imagem de inicialização ou, opcionalmente, arraste uma nova imagem do sistema de arquivos: 

    [![](icons-images-images/asset03.png "Selecione uma imagem de inicialização")](icons-images-images/asset03.png#lightbox)
4. Salve as alterações.

<a name="Layered-Images" />

## <a name="layered-images"></a>Imagens em camadas

Novo para Apple TV, o trabalho de imagens em camadas com o efeito da Parallax para produzir um efeito 3D que ajuda a manter o usuário no sofá mentalmente conectado ao conteúdo na tela na sala.

Contém imagens em camadas de dois (2) a cinco (5) separar camadas que são combinadas para formar uma imagem completa. Com exceção da camada de plano de fundo, cada camada usa sua ordem Z junto com transparência para criar uma ilusão de profundidade. Quando o usuário interage com uma imagem em camadas, as camadas superiores ordenados Z são dimensionadas e sobrepostas para criar esse efeito.

[![](icons-images-images/layered01.png "Diagrama de ordenados Z de imagens em camadas")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> Imagens em camadas são necessárias para os ícones do aplicativo e opcionais para outros [itens Focusable é](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (como a imagem de prateleira superior). No entanto, a Apple sugere usando imagens em camadas para qualquer imagem que pode receber o foco em seu aplicativo.




Apple torna as sugestões a seguir para criar as imagens em camadas:

- **Verifique a camada de plano de fundo opaco** -a camada de plano de fundo (camada 1) **deve** ser opacas ou você obterá um erro quando você tenta usar a imagem em camadas no Apple TV. Todas as outras camadas podem conter vários níveis de transparência para aprimorar o efeito 3D.
- **Isolar o primeiro plano, intermediária e elementos de plano de fundo** -colocar elementos importantes (como caracteres de jogos) em primeiro plano, use o meio de elementos secundários ou sombras. Finalmente, inclua um plano de fundo neutro para fornecer um estágio para as camadas superiores.
- **Manter o texto em primeiro plano** -a menos que você deseja que o texto a ser encobertas por níveis mais altos, geralmente deve ser na camada superior.
- **Dispondo em camadas simples de usar** -o efeito da Parallax foi projetado para ser sutis para manter as camadas mínima para evitar efeitos gritante, irreais.
- **Incluir uma zona de segurança** -como camadas superiores podem ser cortadas durante um efeito da Parallax, você precisa criar uma borda da zona de segurança em cada camada. Se você receber o conteúdo muito próximos da borda de camadas, ele pode se tornar cortado. Camadas superiores passarão por mais de escala e de corte de camadas inferiores. Consulte o [camadas de imagem de dimensionamento](#Sizing-Image-Layers) seção abaixo.
- **Visualizar geralmente** -imagens em camadas deve ser visualizado com frequência para garantir que ocorre o efeito 3D desejado e que nenhum conteúdo nas camadas individuais está sendo cortado. Você deve visualizar as imagens em camadas em hardware de Apple TV real para certificar-se de que eles sejam renderizados como esperado.

Sempre que possível, você sempre deve usar o interno `UIKit` controles para exibir as imagens em camadas, como elas automaticamente obterão o efeito da Parallax quando eles entram em foco.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Camadas de imagem de dimensionamento

É importante lembrar-se de incluir um _zona de segurança_ borda em cada camada que será compõem sua imagem em camadas. Como camadas individuais podem ser dimensionadas e cortadas durante o efeito da Parallax, o conteúdo das camadas pode ser cortado se ele for muito próximos à borda da camada:

[![](icons-images-images/layered02.png "borda de pixel 35")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Criando imagens de camadas

tvOS funciona com imagens em camadas nos seguintes formatos:

- **Arquivos de CARRO** -este é um formato proprietário do catálogo de ativos criado pela Apple. Você não criar arquivos de CARRO diretamente, elas são criadas em tempo de compilação de todos os arquivos LSR e incluídas no pacote de aplicativo.
- **Imagens LSR** -este é um formato de imagem proprietárias criado pela Apple. Use o [da Parallax exportador Adobe Photoshop plug-in](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) ou [da Parallax visualizador](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para criar e visualizar as imagens em camadas no formato LSR.
- **Assets.xcassets** - de dois (2) a cinco (5) padrão `.png` formatadas imagens incluídas em um catálogo de ativos que serão compilados em um CARRO ou LSR formatado em camadas de imagem em tempo de compilação.
- **Arquivos de LCR** -este é um formato de arquivo criado pela Apple. Arquivos LCR destinam-se a ser usado como conteúdo adicional baixado de um dos seus servidores de conteúdo. Arquivo LCR nunca deve ser incluído no pacote de aplicativo. Arquivos LCR gerados a partir de arquivos LSR ou Photoshop usando o `layerutil` ferramenta de linha de comando incluída no Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>O Visualizador da Parallax

Apple criado o [da Parallax visualizador](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para visualização e imagens de em camadas criado necessários para os ícones de aplicativo e itens Focusable é opcional. O visualizador mostra todas as camadas que formam a imagem completa em camadas:

[![](icons-images-images/layered03.png "O Visualizador da Parallax")](icons-images-images/layered03.png#lightbox)

Enquanto visualiza uma imagem em camadas, você pode usar o mouse para girar a imagem e visualizar o efeito da Parallax. Use o **+** (mais) e **-** (botões para adicionar e remover camadas menos).

Ao criar uma nova imagem em camadas, podem ser exportado no formato LSR e incluído no pacote do aplicativo.

Para obter mais informações sobre como criar e visualizar imagens em camadas, consulte da Apple [criação da Parallax arte](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) seção o [guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Ícones de aplicativo

Seu aplicativo Xamarin.tvOS exigirá não só um ícone do aplicativo para a tela inicial para Apple TV, mas também um ícone para a loja de aplicativos. O ícone do aplicativo é o seu primeiro alterar para tornar uma boa impressão potenciais do usuário e deve se comunicar a finalidade do seu aplicativo em um relance.

[![](icons-images-images/icon01.png "O ícone do aplicativo")](icons-images-images/icon01.png#lightbox)

Cada aplicativo deve fornecer uma pequena e uma versão grande do seu ícone de aplicativo. O ícone pequeno será usado na tela de início para Apple TV, quando o aplicativo é instalado. A versão grande é usada pela loja de aplicativos. O ícone de aplicativo grande deve simular a aparência da versão do ícone pequeno.

|Ícone pequeno||Ícone grande||
|---|---|---|---|
|Tamanho real|400x240px|Tamanho|1280x768px|
|Tamanho da zona de segurança|370x222px|||
|Tamanho de foco|300x180px|||
|Tamanho foco|370x222px|||

> [!IMPORTANT]
> Os ícones de aplicativo deve ser fornecidos como **imagens em camadas**. Consulte o [em camadas de imagem](#Layered-Images) seção acima para obter mais detalhes.




Apple fornece as seguintes sugestões para criar os ícones de aplicativo:

- **Fornecer um ponto único de foco** – Design seu ícone com um ponto único de foco colocada diretamente no centro do ícone.
- **Usar um plano de fundo simples** – mantenha seu plano de fundo do ícone simples para que as camadas superiores destacarem. Considere usar uma cor simple ou um gradiente sutil.
- **Limitar a quantidade de texto** – desde que o nome do aplicativo será exibida abaixo do ícone quando ele é selecionado pelo usuário, você deve incluir apenas texto quando ele é essencial para o design do ícone.
- **Não use capturas de tela** – capturas de tela são muito complexas para um ícone e não permitir que o usuário veja a finalidade do seu aplicativo em um relance.
- **Manter ícones quadrado** – tvOS aplica automaticamente uma máscara que sutilmente Arredonda os cantos de seus ícones. Não inclua esse arredondamento.
- **Separe o camadas cuidadosamente** – texto deve estar no canto superior camada maioria, itens secundários no meio e um plano de fundo neutro que permite que as camadas superiores ser exibido.
- **Use gradientes e sombras cuidadosamente** – sombras e gradientes podem conflitar com o efeito da Parallax para que eles devem ser usados com cuidado. Simple de cima para baixo, estilos de gradação de claro para escuro funcionam melhor. Sombras normalmente funcionam melhor com tons de curva, arestas.
- **Variar a transparência da camada** – Use vários níveis de transparência em níveis superiores do seu ícone de aplicativo para aumentar o efeito 3D. A camada de plano de fundo deve ser opaca ou resultará em erro.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Definindo os ícones de aplicativo

Para definir os ícones de aplicativo necessária para o seu projeto tvOS, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes em `Assets.xcassets` para abri-lo para edição: 

    [![](icons-images-images/asset01.png "O fileg Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor ativo**, expanda o `App Icon & Top Shelf Image` ativo: 

    [![](icons-images-images/asset04.png "Expanda o ativo de imagem de prateleira superior")](icons-images-images/asset04.png#lightbox)
3. Em seguida, expanda o `App Icon - Small` ativo: 

    [![](icons-images-images/asset05.png "Expanda o ícone do aplicativo - ativo pequeno")](icons-images-images/asset05.png#lightbox)
4. Em seguida, expanda o `Back` ativo e clique no `Contents` entrada: 

    [![](icons-images-images/asset06.png "Em seguida, expanda o ativo novamente")](icons-images-images/asset06.png#lightbox)
5. Clique no **1 x entrada Apple TV** e selecione um arquivo de imagem.
6. Repita as etapas acima para o `Front` e `Middle` ativos.
7. Em seguida, repita as mesmas etapas para definir o `App Icon - Large` ativo.
4. Salve as alterações.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Imagem de prateleira superior

Se o usuário tiver inserido o seu aplicativo Xamarin.tvOS na linha superior na tela de início para Apple TV, uma imagem grande de prateleira superior será exibida quando seu aplicativo é selecionado pelo usuário. Essa imagem deve realçar os recursos do seu aplicativo ou forneça links diretos para o seu conteúdo.

[![](icons-images-images/topshelf01.png "Exemplo de imagem prateleira superior")](icons-images-images/topshelf01.png#lightbox)

A imagem de prateleira superior pode ser fornecida como um único static `.png` ou `.lsr` arquivo (consulte [criar imagens em camadas](#Creating-Layered-Images)) ou pode ser dinamicamente criado em tempo de execução como uma única linha de itens de controle (consulte [ Conteúdo dinâmico prateleira superior](#Dynamic-Top-Shelf-Content) abaixo).

|Tamanho da imagem prateleira superior|Observações|
|---|---|
|1920x720px|PNG estático ou arquivo .lsr em camadas|

Apple fornece as seguintes sugestões para a criação de imagens prateleira superior:

- **Usar imagens estáticas Rich** – se seu aplicativo não fornecer um conteúdo dinâmico, sua imagem de prateleira superior será sem controle. Use esta imagem para realçar os recursos de aplicativo ou a marca.
- **Link para o conteúdo do aplicativo** – Layouts de prateleira superior dinâmicos fornecem um link para o conteúdo que o usuário encontra mais importante em seu aplicativo. Use esta área para fornecer um link para iniciar seu aplicativo e imediatamente ir diretamente para o conteúdo em questão.
- **Exibir o conteúdo mais recente** – conteúdo Rich superior prateleira pode desenhar um usuário em seu aplicativo e torná-los para usá-lo mais. Use isso como uma área para exibir o conteúdo mais recente ou classificado mais alta.
- **Personalizado conteúdo** – mais usadas de local de usuários ou aplicativos favoritos na linha superior da tela inicial. Use prateleira superior para exibir o conteúdo seria mais interessante.
- **Anúncios não permitido** – anúncios são estritamente proibidos seja exibido em prateleira superior. Você pode mostrar o conteúdo mais recente pode ser adquirido, mas nenhuma informação de preço deve ser exibida.

### <a name="setting-the-top-shelf-image"></a>Definir a imagem de prateleira superior

Para definir a imagem de prateleira principais necessários para seu projeto tvOS, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes em `Assets.xcassets` para abri-lo para edição: 

    [![](icons-images-images/asset01.png "O arquivo Assets.xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor ativo**, expanda o `App Icon & Top Shelf Image` ativo: 

    [![](icons-images-images/asset04.png "Expanda o ativo de imagem de prateleira superior")](icons-images-images/asset04.png#lightbox)
3. Clique no `Top Shelf Image` ativo: 

    [![](icons-images-images/asset07.png "O ativo de imagem de prateleira superior")](icons-images-images/asset07.png#lightbox)
5. Clique no **1 x entrada Apple TV** e selecione um arquivo de imagem.
6. Salve as alterações.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Conteúdo dinâmico prateleira superior

Em vez de exibir uma imagem estática de prateleira superior, prateleira superior pode conter uma linha dinâmica de [itens Focusable é](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) ou um conjunto dinâmico de faixas de rolagem. Ambos esses estilos dinâmica permitem que você realce o conteúdo fornecido pelo seu aplicativo ou um salto para seus recursos mais usados.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Linha dividida em seções de conteúdo

Esse tipo de conteúdo dinâmico superior prateleira apresenta uma única linha de rolagem, itens de controle, opcionalmente, dividido em seções. Ele é normalmente usado para o novo, realce favorito ou exibidos recentemente o conteúdo do aplicativo.

O conteúdo é apresentado como uma única lista rolagem horizontal do conteúdo com um rótulo que aparecem sob a peça atual do conteúdo selecionado (que tem o foco no momento). Se o usuário seleciona uma determinada parte do conteúdo, o aplicativo será iniciado e eles devem ser levados diretamente em que o conteúdo.

Os seguintes tamanhos de conteúdo serão necessários:

||Cartaz (2:3)|Quadrado (1:1)|HDTV (16:9)|
|---|---|---|---|
|Tamanho real|404x608px|608x608px|908x512px|
|Tamanho da zona de segurança|380x570px|570x570px|852x479px|
|Tamanho de foco|333x500px|500x500px|782x440px|
|Tamanho foco|380x570px|570x570px|852x479px|

Apple fornece as seguintes sugestões para a linha divididas em seções de conteúdo:

- **Completar a linha** – você deve fornecer conteúdo suficiente para abranger a largura total da tela.
- **Dimensionando imagens misto** – linha o divididas em seções conteúdo foi projetada para conter uma combinação de tamanhos de imagem (na lista fornecida acima). Se você mesclar os tamanhos de imagem, no entanto, lembre-se de que o dimensionamento adicionais será aplicado para normalizar a exibição de conteúdo.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Faixas de inserção de rolagem

Opcionalmente, seu aplicativo Xamarin.tvOS pode apresentar seu conteúdo prateleira superior como rolagem automaticamente e coleção de faixas que quase preencher a tela de loop. Esse estilo normalmente é usado para apresentar conteúdo avançado, nova como novos programas de TV.

Além de rolagem automática, o usuário pode assumir o controle das faixas de e rolagem em qualquer direção usando Siri remoto. Fazer um pequeno, circular gesto remoto o Siri quando uma faixa esteja em foco ativará o efeito da Parallax para essa faixa.

**Imagem da faixa (Wide Extra)**

|   |   |
|---|---|
|Tamanho real|1940x624px|
|Tamanho da zona de segurança|1740x620px|
|Tamanho de foco|1740x560px|
|Tamanho foco|1740x620px|

Faixas de inserção de rolagem pode a ser fornecida como estático `.png` ou em camadas `.lsr` arquivo.

Apple fornece as seguintes sugestões para faixas de inserção de rolagem:

- **Quantidade de conteúdo** -você deve fornecer um mínimo de três (3) faixas para a rolagem para achar natural. Você deve incluir as faixas não mais do que oito (8) ou ele dificultar navegação para o usuário final.
- **Conteúdo de texto** - se a sua faixa requer texto deve ser incluído na imagem a faixa. Se você estiver usando imagens em camadas, o texto deve ser da camada superior.

Consulte da Apple [TVServices Framework referência](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) para obter mais informações sobre como adicionar uma extensão de prateleira superior ao seu aplicativo para fornecer conteúdo dinâmico de validade da parte superior.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Imagens de game Center

Se seu aplicativo Xamarin.tvOS é um jogo e incluiu suporte Game Center, vários ativos de imagem mais serão necessários:

- **Ícones de realização** -uma imagem opaca é necessária para cada realização que será cortada automaticamente em um círculo. Realizações são itens sem controle.
- **Arte final do painel** -uma imagem opcional pode ser fornecido, que será exibido na parte superior do painel de controle do aplicativo no Game Center. Essas imagens são não Focusable é.
- **Placar de líderes de arte** -você deve fornecer entre um (1) para três (3) imagens de taxa de proporção de 16:9 para cada placar de líderes de que seu aplicativo aceita. Eles podem ser estáticas `.png` ou em camadas `.lsr` arquivos. A arte placar de líderes é Focusable é.

||Ícones de realização|Arte final do painel de controle|Placar de líderes de arte|
|---|---|---|---|
|Tamanho visível|200x200px|923x150px|N/D|
|Tamanho real|320x320px|N/D|659x371px|
|Tamanho da zona de segurança|N/D|N/D|618x348px|
|Tamanho de foco|N/D|N/D|548x309px|
|Tamanho foco|N/D|N/D|618x348px|

Para obter mais informações sobre como trabalhar com Game Center, consulte da Apple [guia de programação do Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html).

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Trabalhando com imagens

Como tvOS 9 é um subconjunto do iOS 9, as mesmas técnicas usadas para incluir e exibir imagens em um aplicativo xamarin, também funcionam para um aplicativo Xamarin.tvOS. Confira nosso [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) documentação para obter mais informações.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Definindo imagens Xamarin.tvOS projeto

Como mencionado acima, todos os aplicativos de tvOS exigem um [imagem de inicialização](#Launch-Image), e [ícone do aplicativo](#App-Icons). Esta seção aborda selecionando a imagem de inicialização e o ícone do aplicativo para o seu projeto de aplicativo Xamarin.tvOS depois que elas foram definidas em um catálogo de ativos.

Faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o `Info.plist` para abri-lo para edição: 

    [![](icons-images-images/info01.png "O arquivo Info. plist")](icons-images-images/info01.png#lightbox)
2. No **Info. plist Editor**, selecione o catálogo de ativos (configurado acima no [definindo os ícones de aplicativo](#Setting-the-App-Icons) seção) para o **ícones de aplicativo**: 

    [![](icons-images-images/info02.png "O Editor de info. plist")](icons-images-images/info02.png#lightbox)
3. Em seguida, selecione o catálogo de ativos (configurado acima no [definindo a imagem de inicialização](#Setting-the-Launch-Image) seção) para o **imagens de inicialização**.
4. Salve as alterações.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou todos os tipos de imagem e tamanhos usados em um aplicativo Xamarin.tvOS. Primeiro, coberto imagens de inicialização, imagens em camadas, os ícones de aplicativo, superior prateleira imagens e imagens do Game Center. Em seguida, ele coberto trabalhar com imagens em seu aplicativo Xamarin.tvOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
