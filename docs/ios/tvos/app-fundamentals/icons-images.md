---
title: Trabalhando com ícones e imagens do tvOS no Xamarin
description: Este documento descreve como trabalhar com ícones e imagens em um aplicativo tvOS criado com o Xamarin. Ele aborda imagens de inicialização, imagens em camadas, o ícone do aplicativo e muito mais.
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: f3a0525a42a59edfe08e5b478bf6af801008ef05
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277848"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>Trabalhando com ícones e imagens do tvOS no Xamarin

A criação de ícones e imagens de cativante é uma parte essencial do desenvolvimento de uma experiência de usuário de imersão para seus aplicativos da Apple TV. Este guia abordará as etapas necessárias para criar e incluir os ativos gráficos necessários para seus aplicativos Xamarin. tvOS:

- [Imagem de inicialização](#Launch-Image) -uma imagem de inicialização é exibida quando seu aplicativo é iniciado pela primeira vez e é substituída pela primeira tela do aplicativo após a conclusão da inicialização.
- [Imagens em camadas](#Layered-Images) – específicas para a Apple TV, as novas imagens em camadas da Apple funcionam com o efeito da Parallax para criar um efeito 3D para os itens selecionados. Há várias maneiras de [criar imagens em camadas](#Creating-Layered-Images).
- [Ícone do aplicativo](#App-Icons) – ícones são necessários para não apenas a tela inicial da Apple TV, mas para a loja de aplicativos. Eles devem ser fornecidos como uma imagem em camadas.
- [Imagem de prateleira superior](#Top-Shelf-Image) – se seu aplicativo for colocado na linha superior da tela inicial, será necessário ter uma imagem de prateleira superior para destacar os recursos do aplicativo. Opcionalmente, você pode fornecer [conteúdo de prateleira superior dinâmico](#Dynamic-Top-Shelf-Content) para realçar o conteúdo em seu aplicativo.
- [Game Center imagens](#Game-Center-Images) – se seu aplicativo for um jogo e usar Game Center, várias imagens adicionais serão necessárias.
- [Definindo imagens do projeto xamarin. tvOS](#Setting-Xamarin.tvOS-Project-Images) -aborda as etapas necessárias para definir a imagem de inicialização e o ícone do aplicativo para seu aplicativo Xamarin. tvOS.

> [!IMPORTANT]
> Todas as imagens na Apple TV estão na resolução 1x (`@1x`) e você _só_ deve usar imagens desse tamanho. Incluindo gráficos maiores, de resolução mais alta, não só Reserve tempo para baixar e usar mais memória e armazenamento, mas eles precisam ser dinamicamente redimensionados em tempo de execução e afetarão negativamente o desempenho do desenho.

<a name="Launch-Image" />

## <a name="launch-image"></a>Imagem de inicialização

A imagem de inicialização é a primeira coisa que é exibida quando seu aplicativo Xamarin. tvOS é inicialmente iniciado na Apple TV e, dessa forma, cada aplicativo tvOS deve fornecer uma imagem de inicialização. 

A imagem de inicialização é exibida rapidamente e dá a impressão de que seu aplicativo é rápido e responsivo. A Apple TV substituirá a imagem de inicialização pela primeira tela do seu aplicativo, logo após.

As imagens de inicialização não são uma oportunidade para anúncios ou expressões artísticas, elas só existem para dar a impressão de que seu aplicativo é iniciado rapidamente e está pronto para uso.

|Tamanho da imagem de inicialização|Observações|
|---|---|
|1920x1080px|Somente arquivos. png não em camadas|

A Apple faz as seguintes sugestões para criar a imagem de inicialização do seu aplicativo:

- **Quase idêntico à primeira tela** – sua tela de início deve ser o mais próximo possível da primeira tela do aplicativo. A inclusão de gráficos ou elementos diferentes pode resultar em "Flash" irritante quando a primeira tela for exibida.
- **Evite usar** imagens de inicialização de texto estática e, como tal, não será localizada antes de ser exibida.
- **Inicialização minimizar** -como os usuários da Apple TV frequentemente alternam aplicativos, você não deve chamar a atenção para o processo de inicialização do aplicativo.
- **Sem anúncios ou identidade visual** -sua imagem de inicialização não deve ser usada como uma tela About ou incluir qualquer identidade visual, a menos que seja estática parte da primeira tela do seu aplicativo. Os anúncios são estritamente proibidos.

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>Definindo a imagem de inicialização

Para definir a imagem de inicialização para seu projeto tvOS, faça o seguinte:

1. Na **Gerenciador de soluções**, clique `Assets.xcassets` duas vezes para abri-lo para edição: 

    [![](icons-images-images/asset01.png "O arquivo assets. xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor de ativos**, clique no `LaunchImages` ativo: 

    [![](icons-images-images/asset02.png "O ativo LaunchImages")](icons-images-images/asset02.png#lightbox)
3. Clique na entrada **1x Apple TV** e selecione a imagem de inicialização ou, opcionalmente, arraste uma nova imagem no do sistema de arquivos: 

    [![](icons-images-images/asset03.png "Selecionar uma imagem de inicialização")](icons-images-images/asset03.png#lightbox)
4. Salve as alterações.

<a name="Layered-Images" />

## <a name="layered-images"></a>Imagens em camadas

Novidade na Apple TV, as imagens em camadas funcionam com o efeito de da Parallax para produzir um efeito 3D que ajuda a manter o usuário no mentalmente de sofá conectado ao conteúdo da tela pela sala.

As imagens em camadas contêm de duas (2) a cinco (5) camadas separadas que são combinadas para formar uma imagem completa. Com exceção da camada de plano de fundo, cada camada usa sua ordem Z junto com a transparência para criar uma ilusão de profundidade. Quando o usuário interage com uma imagem em camadas, as camadas mais altas ordenadas em Z são dimensionadas e sobrepostas para criar esse efeito.

[![](icons-images-images/layered01.png "Diagrama ordenado Z de imagens em camadas")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> As imagens em camadas são necessárias para os ícones do seu aplicativo e são opcionais para outros [itens com foco](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) (como a imagem de prateleira superior). No entanto, a Apple sugere o uso de imagens em camadas para qualquer imagem que possa ficar em foco em seu aplicativo.




A Apple faz as seguintes sugestões para projetar suas imagens em camadas:

- **Tornar a camada de plano de fundo opaca** -sua camada de plano de fundo (camada 1) **deve** ser opaca ou você receberá um erro ao tentar usar a imagem em camadas na Apple TV. Todas as outras camadas podem conter vários níveis de transparência para melhorar o efeito 3D.
- **Isolar elementos de primeiro plano, meio e segundo plano** – coloque elementos proeminentes (como caracteres de jogo) em primeiro plano, use o meio para elementos secundários ou sombras. Por fim, inclua um plano de fundo neutro para fornecer um estágio para as camadas superiores.
- **Mantenha o texto em primeiro plano** -a menos que você queira que o texto seja obscurecido por níveis mais altos, geralmente ele deve estar na camada superior.
- **Usar camadas simples** – o efeito da Parallax foi projetado para ser sutil, portanto, mantenha suas camadas mínimas para evitar efeitos dissonantes e irreais.
- **Incluir uma zona segura** -como as camadas superiores podem ser cortadas durante um efeito de da Parallax, você precisa criar uma borda de zona segura em cada camada. Se você colocar seu conteúdo muito perto da borda das camadas, ele poderá ser cortado. As camadas superiores apresentarão mais dimensionamento e corte do que as camadas inferiores. Consulte a seção [dimensionando camadas de imagem](#Sizing-Image-Layers) abaixo.
- **Visualização com frequência** as imagens em camadas devem ser visualizadas com frequência para garantir que o efeito 3D desejado ocorra e que nenhum conteúdo nas camadas individuais esteja sendo cortado. Você deve visualizar suas imagens em camadas no hardware real da Apple TV para garantir que elas sejam processadas conforme o esperado.

Sempre que possível, você sempre deve usar os `UIKit` controles internos para exibir suas imagens em camadas, pois eles obterão automaticamente o efeito de da Parallax quando entrarem em foco.

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>Dimensionamento de camadas de imagem

É importante lembrar-se de incluir uma borda de _zona segura_ em cada camada que comporá a imagem em camadas. Como as camadas individuais podem ser dimensionadas e cortadas durante o efeito da Parallax, o conteúdo das camadas pode ser cortado se estiver muito próximo da borda da camada:

[![](icons-images-images/layered02.png "borda de 35 pixels")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>Criando imagens em camadas

o tvOS funciona com imagens em camadas nos seguintes formatos:

- **Arquivos de carros** – este é um formato de catálogo de ativos proprietário criado pela Apple. Você não cria arquivos de carro diretamente, eles são criados no momento da compilação a partir de qualquer arquivo LSR e incluídos em seu pacote de aplicativo.
- **Imagens LSR** -este é um formato de imagem proprietário criado pela Apple. Use o [plug-in Adobe Photoshop do exportador da Parallax](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip) ou o [previsor da Parallax](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para criar e visualizar imagens em camadas no formato LSR.
- **Assets. xcassets** -de duas (2) a cinco (5) `.png` imagens formatadas padrão incluídas em um catálogo de ativos que serão compiladas em uma imagem em camadas formatada por carro ou LSR em tempo de compilação.
- **Arquivos de LCR** – esse é um formato de arquivo proprietário criado pela Apple. Os arquivos de LCR destinam-se a serem usados como conteúdo adicional baixado de um dos seus servidores de conteúdo. O arquivo de LCR nunca deve ser incluído no seu pacote de aplicativo. Os arquivos de LCR são gerados a partir de arquivos LSR `layerutil` ou Photoshop usando a ferramenta de linha de comando incluída no Xcode.

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>O da Parallax de visualização

A Apple criou o [da Parallax](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg) para visualizar e criou imagens em camadas necessárias para ícones de aplicativos e itens de foco opcionais. O visualizador mostra todas as camadas que formam a imagem em camadas concluída:

[![](icons-images-images/layered03.png "O da Parallax de visualização")](icons-images-images/layered03.png#lightbox)

Ao visualizar uma imagem em camadas, você pode usar o mouse para girar a imagem e visualizar o efeito de da Parallax. Use os **+** botões (mais) **-** e (menos) para adicionar e remover camadas.

Ao criar uma nova imagem em camadas, ela pode ser exportada no formato LSR e incluída no pacote do aplicativo.

Para obter mais informações sobre como criar e visualizar imagens em camadas, consulte a seção [criando arte da Parallax](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1) da Apple do [Guia de programação do aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/).

<a name="App-Icons" />

## <a name="app-icons"></a>Ícones de aplicativo

Seu aplicativo Xamarin. tvOS exigirá não apenas um ícone de aplicativo para a tela inicial da Apple TV, mas também um ícone para a loja de aplicativos. O ícone do aplicativo é a sua primeira alteração para fazer uma ótima impressão sobre o usuário potencial e deve comunicar a finalidade do aplicativo em um relance.

[![](icons-images-images/icon01.png "O ícone do aplicativo")](icons-images-images/icon01.png#lightbox)

Cada aplicativo deve fornecer uma versão pequena e uma grande de seu ícone de aplicativo. O ícone pequeno será usado na tela inicial da Apple TV quando o aplicativo for instalado. A versão grande é usada pela loja de aplicativos. O ícone do aplicativo grande deve imitar a aparência da versão do ícone pequeno.

|Ícone pequeno||Ícone grande||
|---|---|---|---|
|Tamanho real|400x240px|Size|1280x768px|
|Tamanho da zona segura|370x222px|||
|Tamanho não focalizado|300x180px|||
|Tamanho focado|370x222px|||

> [!IMPORTANT]
> Os ícones do aplicativo devem ser fornecidos como **imagens em camadas**. Consulte a seção [imagem em camadas](#Layered-Images) acima para obter mais detalhes.




A Apple fornece as seguintes sugestões para criar os ícones do aplicativo:

- **Forneça um único ponto de foco** – projete seu ícone com um único ponto de foco colocado diretamente no centro do ícone.
- **Use um plano de fundo simples** – Mantenha seu plano de fundo de ícone simples para que as camadas superiores se destaquem. Considere usar uma cor simples ou um gradiente sutil.
- **Limitar a quantidade de texto** – como o nome do aplicativo aparecerá abaixo do ícone quando for selecionado pelo usuário, você só deverá incluir texto quando for essencial para o design do ícone.
- **Não use capturas de tela** – capturas de tela são muito complexas para um ícone e não permitem que o usuário veja a finalidade de seu aplicativo em um relance.
- **Manter os ícones quadrados** – o tvOS aplica automaticamente uma máscara que arredonda sutilmente os cantos dos ícones. Não inclua esse arredondamento por conta própria.
- **Separe suas camadas cuidadosamente** – o texto deve estar na camada superior, os itens secundários no meio e um plano de fundo neutro que permite que as camadas superiores se.
- **Use gradientes e sombras com cuidado** – gradientes e sombras podem conflitar com o efeito de da Parallax para que eles devam ser usados com cuidado. Os estilos de gradiente de cima para baixo mais simples funcionam melhor. As sombras normalmente funcionam melhor como tonalidades nítidas e rígidas.
- **Variar a transparência da camada** – use níveis variados de transparência nos níveis superiores do ícone do aplicativo para aumentar o efeito 3D. A camada de plano de fundo deve ser opaca ou resultará em um erro.

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>Definindo os ícones do aplicativo

Para definir os ícones de aplicativo necessários para seu projeto tvOS, faça o seguinte:

1. Na **Gerenciador de soluções**, clique `Assets.xcassets` duas vezes para abri-lo para edição: 

    [![](icons-images-images/asset01.png "Os ativos. xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. No **Editor de ativos**, expanda `App Icon & Top Shelf Image` o ativo: 

    [![](icons-images-images/asset04.png "Expandir o ativo de imagem de prateleira superior")](icons-images-images/asset04.png#lightbox)
3. Em seguida, expanda o `App Icon - Small` ativo: 

    [![](icons-images-images/asset05.png "Expandir o ícone do aplicativo-ativo pequeno")](icons-images-images/asset05.png#lightbox)
4. Em seguida, `Back` expanda o ativo e `Contents` clique na entrada: 

    [![](icons-images-images/asset06.png "Em seguida, expanda o ativo de back")](icons-images-images/asset06.png#lightbox)
5. Clique na **entrada 1x Apple TV** e selecione um arquivo de imagem.
6. Repita as etapas acima para os `Front` ativos `Middle` e.
7. Em seguida, repita as mesmas etapas para `App Icon - Large` definir o ativo.
8. Salve as alterações.

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>Imagem da prateleira superior

Se o usuário tiver colocado seu aplicativo Xamarin. tvOS na linha superior da tela inicial da Apple TV, uma imagem de prateleira grande será exibida quando seu aplicativo for selecionado pelo usuário. Essa imagem deve destacar os recursos do seu aplicativo ou fornecer links diretos para seu conteúdo.

[![](icons-images-images/topshelf01.png "Exemplo de imagem de prateleira superior")](icons-images-images/topshelf01.png#lightbox)

A imagem da prateleira superior pode ser fornecida como um único arquivo `.png` estático `.lsr` ou (consulte [criação de imagens em camadas](#Creating-Layered-Images)) ou pode ser criada dinamicamente em tempo de execução como uma única linha de itens com foco (consulte o [conteúdo da prateleira superior dinâmico](#Dynamic-Top-Shelf-Content) abaixo).

|Tamanho da imagem de prateleira superior|Observações|
|---|---|
|1920x720px|Arquivo estático. png ou em camadas. LSR|

A Apple fornece as seguintes sugestões para criar suas imagens de prateleira principais:

- **Usar imagens estáticas avançadas** – se seu aplicativo não fornecer um conteúdo dinâmico, sua imagem de prateleira superior não terá foco. Use essa imagem para destacar os recursos do aplicativo ou sua marca.
- **Vincular ao conteúdo do aplicativo** – os layouts de prateleira superior dinâmicos fornecem um link rápido para o conteúdo que o usuário encontra mais importante em seu aplicativo. Use essa área para fornecer um link rápido para iniciar seu aplicativo e ir imediatamente para o conteúdo fornecido.
- **Mostrar o conteúdo mais recente – o** conteúdo de prateleira superior pode desenhar um usuário em seu aplicativo e fazer com que eles desejam usá-lo mais. Use-o como uma área para demonstrar o conteúdo mais alto classificado ou mais recente.
- **Conteúdo personalizado** – os usuários colocam seus aplicativos mais usados ou favoritos na linha superior da tela inicial. Use a prateleira superior para exibir o conteúdo em que ele estaria mais interessado.
- **Anúncios não permitidos** – os anúncios são estritamente proibidos de serem exibidos na prateleira superior. Você pode mostrar o conteúdo mais recente do compráveis, mas nenhuma informação de preço deve ser exibida.

### <a name="setting-the-top-shelf-image"></a>Configurando a imagem da prateleira superior

Para definir a imagem de prateleira superior necessária para seu projeto tvOS, faça o seguinte:

1. Na **Gerenciador de soluções**, clique `Assets.xcassets` duas vezes para abri-lo para edição: 

    [![](icons-images-images/asset01.png "O arquivo assets. xcassets")](icons-images-images/asset01.png#lightbox)
2. No **Editor de ativos**, expanda `App Icon & Top Shelf Image` o ativo: 

    [![](icons-images-images/asset04.png "Expandir o ativo de imagem de prateleira superior")](icons-images-images/asset04.png#lightbox)
3. Clique no `Top Shelf Image` ativo: 

    [![](icons-images-images/asset07.png "O ativo da imagem da prateleira superior")](icons-images-images/asset07.png#lightbox)
4. Clique na **entrada 1x Apple TV** e selecione um arquivo de imagem.
5. Salve as alterações.

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>Conteúdo de prateleira superior dinâmico

Em vez de exibir uma imagem de prateleira superior estática, a prateleira superior pode conter uma linha dinâmica de [itens com foco](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) ou um conjunto dinâmico de faixas de rolagem. Ambos os estilos dinâmicos permitem que você realce o conteúdo fornecido pelo seu aplicativo ou salte para seus recursos mais usados.

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>Linha de conteúdo em seções

Esse tipo de conteúdo de prateleira mais dinâmico apresenta uma única linha de rolagem, itens com foco, opcionalmente divididos em seções. Normalmente, ele é usado para realçar o conteúdo de um aplicativo novo, favorito ou exibido recentemente.

O conteúdo é apresentado como uma lista de conteúdo única e de rolagem horizontal com um rótulo que aparece sob a parte atual do conteúdo selecionado (que atualmente tem foco). Se o usuário selecionar uma determinada parte do conteúdo, seu aplicativo será iniciado e deverá ser levado diretamente para esse conteúdo.

Os seguintes tamanhos de conteúdo serão necessários:

||Cartaz (2:3)|Quadrado (1:1)|HDTV (16:9)|
|---|---|---|---|
|Tamanho real|404x608px|608x608px|908x512px|
|Tamanho da zona segura|380x570px|570x570px|852x479px|
|Tamanho não focalizado|333x500px|500x500px|782x440px|
|Tamanho focado|380x570px|570x570px|852x479px|

A Apple fornece as seguintes sugestões para a linha de conteúdo em seções:

- **Concluir a linha** – você deve fornecer conteúdo suficiente para abranger a largura total da tela.
- **Dimensionamento de imagens mistas** – a linha de conteúdo seção foi projetada para conter uma combinação de tamanhos de imagem (da lista fornecida acima). No entanto, se você encontrar tamanhos de imagem misturados, lembre-se de que o dimensionamento adicional será aplicado para normalizar a exibição do conteúdo.

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>Rolagem de faixas de inserção

Opcionalmente, seu aplicativo Xamarin. tvOS pode apresentar seu conteúdo na prateleira superior como uma coleção de faixas de rolagem e looping automaticamente que quase preencha a tela. Esse estilo é normalmente usado para apresentar conteúdo sofisticado e novo, como novos programas de TV.

Além da rolagem automática, o usuário pode assumir o controle das faixas e rolar em qualquer direção usando o Siri remoto. Fazer um pequeno gesto circular no Siri remoto quando uma faixa está em foco ativará o efeito de da Parallax para essa faixa.

**Imagem de faixa (extra de largura)**

|   |   |
|---|---|
|Tamanho real|1940x624px|
|Tamanho da zona segura|1740x620px|
|Tamanho não focalizado|1740x560px|
|Tamanho focado|1740x620px|

A rolagem de faixas de inserção pode ser fornecida como `.png` um `.lsr` arquivo estático ou em camadas.

A Apple fornece as seguintes sugestões para as faixas de inserção de rolagem:

- **Valor do conteúdo** -você deve fornecer um mínimo de três (3) faixas para que a rolagem fique natural. Você deve incluir no máximo oito (8) faixas ou tornar a navegação difícil para o usuário final.
- **Texto de conteúdo** -se sua faixa exigir que o texto seja incluído na imagem do banner. Se você estiver usando imagens em camadas, o texto deverá estar na camada superior.

Consulte a referência da [estrutura TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) da Apple para obter mais informações sobre como adicionar uma extensão de prateleira superior ao seu aplicativo para fornecer conteúdo de prateleira superior dinâmico.

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>Game Center imagens

Se seu aplicativo Xamarin. tvOS for um jogo e você tiver incluído Game Center suporte, vários outros ativos de imagem serão necessários:

- **Ícones de realização** -uma imagem opaca é necessária para cada realização que será cortada automaticamente em um círculo. As realizações são itens sem foco.
- **Arte do painel** -é possível fornecer uma imagem opcional que será exibida na parte superior do painel do aplicativo dentro do Game Center. Essas imagens não são de foco.
- **Arte placar** -você deve fornecer entre uma (1) a três (3) 16:9 imagens de taxa de proporção para cada placar ao qual seu aplicativo dá suporte. Eles podem ser `.png` `.lsr` arquivos estáticos ou em camadas. A arte placar é focada.

||Ícones de realização|Arte do painel|Arte placar|
|---|---|---|---|
|Tamanho visível|200x200px|923x150px|N/D|
|Tamanho real|320x320px|N/D|659x371px|
|Tamanho da zona segura|N/D|N/D|618x348px|
|Tamanho não focalizado|N/D|N/D|548x309px|
|Tamanho focado|N/D|N/D|618x348px|

Para obter mais informações sobre como trabalhar com Game Center, consulte o [Guia de programação do Game Center](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)da Apple.

<a name="Working-with-Images" />

## <a name="working-with-images"></a>Trabalhando com imagens

Como o tvOS 9 é um subconjunto do iOS 9, as mesmas técnicas usadas para incluir e exibir imagens em um aplicativo Xamarin. iOS também funcionam para um aplicativo Xamarin. tvOS. Consulte a documentação [exibindo uma imagem](~/ios/app-fundamentals/images-icons/displaying-an-image.md) para obter mais informações.

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>Definindo imagens do projeto Xamarin. tvOS

Conforme mencionado acima, todos os aplicativos tvOS exigem uma [imagem de inicialização](#Launch-Image)e o ícone do [aplicativo](#App-Icons). Esta seção aborda a seleção do ícone do aplicativo e da imagem de inicialização para seu projeto do aplicativo Xamarin. tvOS depois que eles tiverem sido definidos em um catálogo de ativos.

Faça o seguinte:

1. Na **Gerenciador de soluções**, clique duas vezes no `Info.plist` para abri-lo para edição: 

    [![](icons-images-images/info01.png "O arquivo info. plist")](icons-images-images/info01.png#lightbox)
2. No **Editor info. plist**, selecione o catálogo ativos (configurado acima na seção [definir os ícones do aplicativo](#Setting-the-App-Icons) ) para os **ícones do aplicativo**: 

    [![](icons-images-images/info02.png "O editor info. plist")](icons-images-images/info02.png#lightbox)
3. Em seguida, selecione o catálogo de ativos (configurado acima na seção [definindo a imagem de inicialização](#Setting-the-Launch-Image) ) para as **imagens de inicialização**.
4. Salve as alterações.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou todos os tipos de imagem e tamanhos usados em um aplicativo Xamarin. tvOS. Primeiro, ele abordou imagens de inicialização, imagens em camadas, ícones de aplicativos, imagens mais importantes e imagens de Game Center. Em seguida, ele abordou o trabalho com imagens em seu aplicativo Xamarin. tvOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
