---
title: Recursos de pirulito
description: "Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5.0 (pirulito). Esses recursos incluem um novo estilo de interface de usuário chamado tema de Material, bem como novos recursos de suporte, como animações, sombras de exibição e tonalidade drawable. Android 5.0 também inclui notificações aprimoradas, dois novos widgets de interface do usuário, um novo Agendador de trabalho e uma série de novas APIs para melhorar o armazenamento, rede, conectividade e recursos de multimídia."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 24d85d7be580f8db8621d91ebbb27c0b7881b4eb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="lollipop-features"></a>Recursos de pirulito

_Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5.0 (pirulito). Esses recursos incluem um novo estilo de interface de usuário chamado tema de Material, bem como novos recursos de suporte, como animações, sombras de exibição e tonalidade drawable. Android 5.0 também inclui notificações aprimoradas, dois novos widgets de interface do usuário, um novo Agendador de trabalho e uma série de novas APIs para melhorar o armazenamento, rede, conectividade e recursos de multimídia._

## <a name="lollipop-overview"></a>Visão geral de pirulito

Android 5.0 (pirulito) introduz uma nova linguagem de design, *Material de Design*, e com ele suporte a uma conversão de novos recursos para tornar os aplicativos mais fácil e intuitivo para usar. Design de Material, Android 5.0 não oferece apenas telefones Android um facelift; Ele também fornece um novo conjunto de regras de design para tablets baseados no Android, computadores desktop, inspeciona e TVs inteligentes. Estas regras de design enfatizam simplicidade e minimalism ao fazer uso de atributos táteis familiares (como realistas indicações de superfície e borda) para ajudar os usuários rapidamente e entender intuitivamente a interface.

*Tema material* é a incorporação desses princípios de design de interface do usuário no Android. Este artigo começa que abrangem recursos de suporte do tema Material:

-   **Animações** &ndash; *Touch comentários* animações, *transição de atividade* animações, *exibir transição de estado* animações e um *revelar efeito*.

-   **Exibir sombras e elevação** &ndash; exibições agora têm um `elevation` propriedade; exibições com maior `elevation` valores convertidos sombras maior no plano de fundo.

-   **Recursos de cores** &ndash; *tonalidade Drawable* possibilita a reutilização de ativos de imagem alterando sua cor e *extração de cor de destaque* ajuda a dinamicamente tema do seu aplicativo com base em cores em uma imagem.

Tema de Material muitos recursos já são criados na interface do usuário do Android 5.0 experiência, enquanto outros usuários devem ser adicionados explicitamente para aplicativos. Por exemplo, alguns modos de exibição padrão (como botões) já incluir animações de comentários de toque, enquanto os aplicativos devem habilitar a maioria das sombras de exibição.

Além dos aprimoramentos de interface do usuário provocados por meio do tema de Material, Android 5.0 também inclui vários outros recursos novos que são abordados neste artigo:

-   **Avançado notificações** &ndash; notificações no Android 5.0 foram significativamente atualizadas com uma nova aparência, suporte para notificações de bloqueio e um novo *Heads-Up* formato de apresentação de notificação.

-   **Novos widgets de interface do usuário** &ndash; novo `RecyclerView` widget torna mais fácil para aplicativos transmitir grandes conjuntos de dados e informações complexas e o novo `CardView` widget fornece um formato de apresentação do cartão como simplificada para exibir texto e imagens.

-   **Novas APIs** &ndash; Android 5.0 adiciona novas APIs para suporte a várias redes, melhor conectividade Bluetooth, gerenciamento de armazenamento mais fácil e controle mais flexível de players de multimídia e dispositivos de câmera. Um novo recurso de agendamento de trabalho está disponível para executar tarefas de forma assíncrona em horários agendados. Esse recurso ajuda a melhorar a vida útil da bateria, por exemplo, agendando tarefas entrem em vigor quando o dispositivo está conectado e carregamento.


## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para usar os novos recursos do Android 5.0 em aplicativos baseados em Xamarin:

-   **Xamarin** &ndash; xamarin 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac. 

-   **SDK do Android** &ndash; Android 5.0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; xamarin requer [1.8 JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1.8 também dá suporte a API níveis anteriores a 24, incluindo pirulito). A versão de 64 bits do JDK 1.8 é necessária se você estiver usando controles personalizados ou o Visualizador de formulários.

Você pode continuar a usar [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se você estiver desenvolvendo especificamente para API nível 23 ou anterior.


<a name="settingup" />

## <a name="setting-up-an-android-50-project"></a>Configurando um projeto do Android 5.0

Para criar um projeto Android 5.0, você deve instalar as ferramentas mais recentes e os pacotes do SDK. Use as etapas a seguir para configurar um projeto xamarin que tem como destino Android 5.0:

1. Instalar as ferramentas xamarin e ativar a licença do Xamarin. Consulte [instalação e configuração](~/android/get-started/installation/index.md) para obter mais informações sobre como instalar o xamarin.

2. Se você estiver usando o Visual Studio para Mac, instale as atualizações mais recentes do Android 5.0.

3. Inicie o Gerenciador de SDK do Android (no Visual Studio para Mac, use **ferramentas &gt; abrir o Gerenciador de SDK do Android&hellip;**) e instale as ferramentas do SDK do Android 23.0.5 ou posterior:

    [![Selecionando Ferramentas do SDK do Android no Gerenciador de SDK do Android](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png)

   Além disso, instale os pacotes mais recentes do SDK do Android 5.0 (API 21 ou posterior):

    [![Instalando pacotes de SDK do Android 5.0 no Gerenciador de SDK do Android](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png)

   Para obter mais informações sobre como usar o Gerenciador de SDK do Android, consulte [Manager SDK](http://developer.android.com/tools/help/sdk-manager.html).

4. Crie um novo projeto de xamarin. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos Android. Quando você cria um projeto Android, certifique-se de definir as configurações de versão para o Android 5.0.
   No Visual Studio para Mac, navegue até **opções de projeto &gt; criar &gt; geral** e defina **framework de destino** para **Android 5.0 (pirulito)** ou mais tarde:

    ![Definir o destino Framwework como Android pirulito 5.0](lollipop-images/target-framework.png)

   Em **opções de projeto &gt; criar &gt; aplicativo Android**, definir a duração mínima e direcionar a versão do Android para **automático - versão do framework de destino use**:

    ![Definindo as versões de mínimo e o destino Android como automático](lollipop-images/minimum-android-version.png)

5. Configure um emulador ou um dispositivo Android para testar seu aplicativo. Se você estiver usando um emulador, consulte [configuração do emulador Android](~/android/get-started/installation/android-emulator/index.md) para saber como configurar um emulador Android para uso com o Xamarin Studio ou Visual Studio. Se você estiver usando um dispositivo Android, consulte [configuração o SDK visualização](https://developer.android.com/preview/setup-sdk.html) para saber como atualizar o dispositivo para o Android 5.0. Para configurar seu dispositivo Android para executar e depurar aplicativos xamarin, consulte [definir o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

Observação: Se você estiver atualizando um projeto Android existente que foi direcionamento a visualização L Android, você deve atualizar o **Framework de destino** e **versão Android** para os valores descritos acima.


<a name="changes" />

## <a name="important-changes"></a>Alterações importantes

Anteriormente aplicativos Android publicados podem ser afetados por alterações no Android 5.0. Em particular, o Android 5.0 usa um novo tempo de execução e um formato de notificação de alteração significativamente.

<a name="runtime" />

### <a name="android-runtime"></a>Tempo de execução do Android

Android 5.0 usa a nova Android em tempo de execução (arte) como o tempo de execução padrão em vez de Dalvik. ARTE implementa vários novos recursos principais:

-   **Compilação antecipada de tempo (AOT)** &ndash; AOT pode melhorar o desempenho do aplicativo ao compilar o código do aplicativo antes que o aplicativo é iniciado pela primeira vez. Quando um aplicativo é instalado, a arte gera um aplicativo compilado executável para o dispositivo de destino.

-   **Melhor a coleta de lixo (GC)** &ndash; aperfeiçoamentos de GC no arte também podem melhorar o desempenho do aplicativo. Coleta de lixo agora usa uma pausa de GC em vez de dois, e operações simultâneas de GC concluída de maneira mais rápida.

-   **Melhor depuração de aplicativo** &ndash; arte fornece mais detalhes de diagnóstico para ajudar na análise de exceções e relatórios de falhas.

Aplicativos existentes devem funcionar sem alteração em arte &ndash; , exceto aplicativos exploram técnicas exclusivas para o tempo de execução Dalvik anterior, que podem não funcionar em arte. Para obter mais informações sobre essas alterações, consulte [verificando o comportamento do aplicativo sobre o tempo de execução Android (arte)](http://developer.android.com/guide/practices/verifying-apps-art.html).

<a name="notifchanges" />

### <a name="notification-changes"></a>Notificação de alterações

As notificações foram alterados significativamente no Android 5.0:

-   **Sons e vibração são tratadas diferentemente** &ndash; sons de notificação e vibrações agora são manipuladas pelo `Notification.Builder` em vez de `Ringtone`, `MediaPlayer`, e `Vibrator`.

-   **Novo esquema de cores** &ndash; acordo com tema de Material, notificações são processadas com texto escuro sobre planos de fundo brancos ou muito leves. Além disso, os canais alfa em ícones de notificação podem ser modificados pelo Android para coordenar com esquemas de cores do sistema. 

-   **Notificações de bloqueio** &ndash; notificações podem ser exibidas agora no bloqueio do dispositivo.

-   **Heads-Up** &ndash; as notificações de alta prioridade agora aparecem em uma janela flutuante pequena (notificação Heads-Up) quando o dispositivo está desbloqueado e a tela é ativada.

Na maioria dos casos, a portabilidade de funcionalidade de notificação existente do aplicativo para Android 5.0 exige as seguintes etapas:

1.  Converter seu código para usar `Notification.Builder` (ou `NotificationsCompat.Builder`) para a criação de notificações. 

2.  Verifique se seus ativos de notificação estão visíveis no novo esquema de cores do tema de Material.

3.  Decida quais as notificações devem ter quando eles são apresentados no bloqueio de visibilidade. Se uma notificação não é pública, o conteúdo deve aparecem no bloqueio?

4.  Defina a categoria de suas notificações para que eles são tratados corretamente no Android 5.0 novo *não interfira forma* modo.

Se as notificações de controles de transporte, exibição mídia reprodução status atual, use `RemoteControlClient`, ou chame `ActivityManager.GetRecentTasks`, consulte [importantes alterações de comportamento](http://developer.android.com/preview/api-overview.html#Behaviors) para obter mais informações sobre como atualizar as notificações para Android 5.0.

Para obter informações sobre como criar notificações no Android, consulte [notificações Local](~/android/app-fundamentals/notifications/local-notifications.md). O [compatibilidade](~/android/app-fundamentals/notifications/local-notifications.md#compatibility) deste artigo explica como criar notificações que são descendentes compatíveis com versões anteriores do Android.

<a name="materialtheme" />

## <a name="material-theme"></a>Tema material

O novo tema de Material 5.0 Android traz alterações de varredura para a aparência da interface do usuário do Android. Elementos visuais agora usam táteis superfícies que assumir os gráficos em negrito, tipografia e cores claras do design com base na impressão. Exemplos de Material tema são descritos nas capturas de tela seguir:

[![Capturas de tela da tela inicial do tema de Material, a tela de aplicativos e a tela de configuração](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png)

Android 5.0 você recebe com a tela inicial mostrada à esquerda. A captura de tela de center é a primeira tela de lista de aplicativos e a captura de tela da direita é a **configurações** tela. Do Google [Material de Design](https://material.io/guidelines/material-design/introduction.html) especificação explica as regras de design subjacentes por trás o novo conceito de tema do Material.

Tema material inclui três tipos internos que você pode usar em seu aplicativo: o `Theme.Material` tema escuro (padrão), o `Theme.Material.Light` tema e o `Theme.Material.Light.DarkActionBar` tema: 

[![Temas de capturas de tela de escuro, claro e DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png)

Para obter mais informações sobre como usar os recursos de tema Material em aplicativos xamarin, consulte [Material tema](~/android/user-interface/material-theme.md).

<a name="animations" />

## <a name="animations"></a>Animations

Android 5.0 fornece toque comentários animações, animações de transição de atividade e animações de transição de estado de exibição para tornar as interfaces de aplicativo mais intuitivo de usar. Além disso, podem usar os aplicativos do Android 5.0 *revelar efeito* animações ocultar ou exibir os modos de exibição. Você pode usar *movimento em curva* configurações para definir a rapidez ou lenta animações são renderizadas.

<a name="touchanim" />

### <a name="touch-feedback-animations"></a>Toque animações de comentários

Toque comentários animações fornecem aos usuários comentários visuais quando um modo de exibição foi alterado. Por exemplo, botões agora exibem um efeito de ondulação quando eles são tocados &ndash; trata a animação de comentários de toque padrão no Android 5.0. Animação de ondulação é implementada pelo novo `RippleDrawable` classe. O efeito de ondulação pode ser configurado para terminar com os limites do modo de exibição ou ultrapassar os limites do modo de exibição. Por exemplo, a sequência de capturas de tela a seguir ilustra o efeito de ondulação em um botão durante a animação de toque:

![Quadro de capturas de tela de quadro de animação de ondulação em um botão](lollipop-images/touch-animation.png)

Toque inicial de contato com o botão ocorre na primeira imagem à esquerda, enquanto a sequência restante (da esquerda para a direita) ilustra como o efeito de ondulação espalha para a borda do botão. Quando a animação de ondulação termina, a exibição retorna à sua aparência original. A animação de ondulação padrão é feita em uma fração de segundo, mas o comprimento da animação pode ser personalizado por períodos maiores ou menores.

Para obter mais informações sobre touch animações de comentários no Android 5.0, consulte [personalizar comentários Touch](http://developer.android.com/training/material/animations.html#Touch).

<a name="activityanim" />

### <a name="activity-transition-animations"></a>Animações de transição de atividade

Animações de transição de atividade dar aos usuários uma noção da continuidade visual quando uma atividade faz a transição para outro. Aplicativos podem especificar três tipos de animações de transição:

-   **Insira a transição** &ndash; para quando uma atividade entra em cena.

-   **Transição de saída** &ndash; para quando uma atividade sai da cena.

-   **Compartilhado transição elemento** &ndash; para quando uma exibição que é comum para duas atividades muda conforme as transições da atividade primeiro para o próximo.

Por exemplo, a sequência de capturas de tela a seguir ilustra uma transição de elemento compartilhado:

[![Quadro de capturas de tela de quadro de animação de transição um elemento compartilhado](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png)

Um elemento compartilhado (uma foto de uma lagarta) é um dos vários modos de exibição a primeira atividade; Ele amplia para se tornar o único modo de exibição na atividade de segundo como as transições da atividade primeiro para o segundo.

#### <a name="enter-transition-animation-types"></a>Inserir tipos de animação de transição

Para enter transições, Android 5.0 fornece três tipos de animações:

-   **Detalhar animação** &ndash; aumenta um modo de exibição do centro da cena.

-   **Deslize animação** &ndash; move uma exibição em uma das bordas de uma cena.

-   **Animação de esmaecimento** &ndash; surge uma exibição em cena.

#### <a name="exit-transition-animation-types"></a>Tipos de animação de transição de saída

Transições de saída, o Android 5.0 fornece três tipos de animações:

-   **Detalhar animação** &ndash; reduz um modo de exibição para o centro da cena.

-   **Deslize animação** &ndash; move uma exibição de para uma das bordas de uma cena.

-   **Animação de esmaecimento** &ndash; surge uma exibição fora da cena.

#### <a name="shared-element-transition-animation-types"></a>Tipos de animação de transição de elemento compartilhadas

Elemento compartilhado transições oferecem suporte a vários tipos de animação, como:

-   Alterar os limites de layout ou de clipe de um modo de exibição.

-   Alterar a escala e rotação de uma exibição.

-   Alterando o tipo de tamanho e a escala para um modo de exibição.

Para obter mais informações sobre animações de transição de atividade no Android 5.0, consulte [personalizar as transições da atividade](http://developer.android.com/training/material/animations.html#Transitions).

<a name="viewstate" />

### <a name="view-state-transition-animations"></a>Animações de transição de estado de exibição

Android 5.0 possibilita animações ser executado quando muda o estado de um modo de exibição. É possível animar transições de estado de exibição usando uma das seguintes técnicas:

-   Crie drawables que animar alterações de estado associadas a uma determinada exibição. O novo `AnimatedStateListDrawable` classe permite que você crie drawables que exibem animações entre alterações de estado de exibição.

-   Defina a funcionalidade de animação que é executado quando muda o estado de um modo de exibição. O novo `StateListAnimator` classe permite que você defina um animator que é executado quando muda o estado de um modo de exibição.

Para obter mais informações sobre animações de transição de estado de exibição no Android 5.0, consulte [animar alterações de estado de exibição](http://developer.android.com/training/material/animations.html#ViewState).

<a name="reveal" />

### <a name="reveal-effect"></a>Revelar efeito

O *revelar efeito* é um círculo de recorte radius que alterações para mostrar ou ocultar um modo de exibição. Você pode controlar esse efeito, definindo o raio do círculo recorte inicial e final. A sequência de capturas de tela a seguir ilustra uma animação de efeito de revelação do centro da tela:

[![Quadro de capturas de tela de quadro de animação de revelação](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png)

A sequência seguinte ilustra uma animação de efeito revelar que ocorre no canto inferior esquerdo da tela:

[![Quadro de capturas de tela de quadro de animação de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png)

Revelar animações podem ser revertidas; ou seja, o círculo de recorte pode reduzir para ocultar o modo de exibição em vez de ampliar para revelar o modo de exibição.

Para obter mais informações sobre o efeito de revelação 5.0 Android no, consulte [usar o efeito revelar](http://developer.android.com/training/material/animations.html#Reveal).

<a name="curvedmotion" />

### <a name="curved-motion"></a>Curva de movimento

Além desses recursos de animação, Android 5.0 também fornece novas APIs que permitem que você especifique as curvas tempo e o movimento de animações. Android 5.0 usa essas curvas para interpolar movimento temporal e espacial durante animações. Três curvas são definidas no Android 5.0:

-   **Fast\_out\_linear\_na** &ndash; aceleram rapidamente e continua a acelerar até o término da animação.

-   **Fast\_out\_lenta\_na** &ndash; Accelerates rapidamente e lenta será desacelerado em direção ao final da animação.

-   **Linear\_out\_lenta\_na** &ndash; começa com uma velocidade de pico e lenta será desacelerado ao final da animação.

Você pode usar o novo `PathInterpolator` classe para especificar como a interpolação de movimento ocorre. `PathInterpolator` é um interpolador que atravessa os caminhos de animação de acordo com os pontos de controle específicos e curvas de movimento. Para obter mais informações sobre como especificar configurações de movimento de curva no Android 5.0, consulte [movimento curva Use](http://developer.android.com/training/material/animations.html#CurvedMotion).

<a name="viewshadows" />

## <a name="view-shadows--elevation"></a>Exibição sombras & elevação

No Android 5.0, você pode especificar o *elevação* de uma exibição, definindo um novo `Z` propriedade. Maior `Z` valor faz com que o modo de exibição converter uma sombra maior no plano de fundo, fazendo com que o modo de exibição aparecem em float superior acima do plano de fundo. Você pode definir a elevação inicial de uma exibição, configurando seus `elevation` atributo no layout.

O exemplo a seguir ilustra as sombras convertido pela vazio `TextView` controlar quando o atributo de elevação é definido como 2dp, 4dp e 6dp, respectivamente:

[![Capturas de tela de sombras de exibição maior progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png)

Exibir configurações de sombra podem ser estáticas (conforme mostrado acima) ou pode ser usadas em animações para exibir um modo de exibição subir temporariamente plano de fundo da exibição. Você pode usar o `ViewPropertyAnimator` classe para animar a elevação de um modo de exibição. A elevação de um modo de exibição é a soma de seu layout `elevation` configuração mais um `translationZ` propriedade que pode ser definida por meio de um `ViewPropertyAnimator` chamada de método.

Para obter mais informações sobre as sombras de exibição no Android 5.0, consulte [definindo sombras e exibições de recorte](http://developer.android.com/training/material/shadows-clipping.html).

<a name="colorfeatures" />

## <a name="color-features"></a>Recursos de cor

Android 5.0 fornece dois novos recursos para gerenciamento de cores em aplicativos:

-   *Tonalidade drawable* permite que você altere as cores dos ativos de imagem alterando um atributo de layout.

-   *Extração de cor de destaque* possibilita personalizar dinamicamente o tema de cores do aplicativo para coordenar com a paleta de cores de uma imagem exibida.

<a name="tinting" />

### <a name="drawable-tinting"></a>Tonalidade drawable

Layouts de 5.0 Android reconhecem um novo `tint` atributo que você pode usar para definir a cor de drawables sem a necessidade de criar várias versões desses ativos para exibir cores diferentes. Para usar esse recurso, você deve definir um bitmap como uma máscara alfa e use o `tint` atributo para definir a cor do ativo. Isso possibilita que você criar ativos de uma vez e cor-los do layout para coincidir com seu tema.

No exemplo a seguir, um ativo de imagem única &ndash; um logotipo branco com um plano de fundo transparente &ndash; é usada para criar variações de tom:

![Logotipo do Xamarin branco com o plano de fundo transparente](lollipop-images/xamarin-logo-white.png)

Esse logotipo é exibido acima de um plano de fundo azul circular, conforme mostrado nos exemplos a seguir. A imagem à esquerda é como o logotipo aparece sem um `tint` configuração. No centro, o logotipo da imagem `tint` atributo é definido como um cinza escuro. A imagem à direita, `tint` é definido como um cinza claro:

![Exemplos do logotipo acima com configurações diferentes de tonalidade](lollipop-images/drawable-tinting.png)

Para obter mais informações sobre um tonalizar drawable no Android 5.0, consulte [tonalidade Drawable](http://developer.android.com/training/material/drawables.html#DrawableTint).

<a name="colorextract" />

### <a name="prominent-color-extraction"></a>Extração de cor de destaque

O novo Android 5.0 `Palette` classe permite extrair as cores de uma imagem para que você possa aplicá-los dinamicamente para uma paleta de cores personalizadas. O `Palette` classe extrai seis cores de uma imagem e rótulos essas cores de acordo com os níveis relativos de saturação de cor e o brilho:

-   Vibrant

-   Vibrante escuro

-   Luz vibrante

-   Sem som

-   Mudo escuro

-   Mudo luz

Por exemplo, nas capturas de tela seguir, um aplicativo de exibição de fotos extrai as cores de destaque de imagem em tela em usa essas cores para adaptar o esquema de cores do aplicativo para corresponder a imagem:

[![Capturas de tela de extrações de cor de tema azul, verde e rosa](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png)

Nas capturas de tela acima, a barra de ação é definida como extraído "vibrante light" cor e o plano de fundo é definido como extraído "vibrante escuro" cor. Em cada exemplo acima, uma linha dos quadrados de cor pequeno é incluída para ilustrar a paleta de cores que foram extraídas da imagem.

Para obter mais informações sobre a extração de cor no Android 5.0, consulte [extraindo proeminentes cores de uma imagem](http://developer.android.com/training/material/drawables.html#ColorExtract).

<a name="newuiwidgets" />

## <a name="new-ui-widgets"></a>Novos Widgets de interface do usuário

Android 5.0 introduz dois novos widgets de interface do usuário:

-   `RecyclerView` &ndash; Um grupo de modo de exibição que exibe uma lista de itens roláveis.

-   `CardView` &ndash; Um layout básico com cantos arredondados.

Ambos os widgets incluem baked no suporte para recursos de tema Material; Por exemplo, `RecyclerView` usa animações para adicionar e remover os modos de exibição, e `CardView` usa exibir sombras para exibir cada cartão de flutuar acima do plano de fundo. Exemplos desses novos widgets são mostrados nas capturas de tela seguir:

[![Capturas de tela de aplicativos compilados com RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png)

A captura de tela da esquerda é um exemplo de `RecyclerView` conforme usado em um aplicativo de email e a captura de tela na direita é um exemplo de `CardView` conforme usado em um aplicativo de reserva de viagem.

<a name="recyclerview" />

### <a name="recyclerview"></a>RecyclerView

`RecyclerView` é semelhante ao `ListView,` , mas ele é mais adequado para grandes conjuntos de modos de exibição ou listas com elementos que são alterados dinamicamente. Como `ListView,` você especificar um adaptador para acessar o conjunto de dados subjacente. No entanto, diferentemente `ListView,` você usar um *Gerenciador de layout* posicionar itens em `RecyclerView`. O Gerenciador de layout também cuida da exibição reciclagem; Ele gerencia a reutilização dos modos de exibição do item que não são visíveis para o usuário.

Quando você usa um `RecyclerView` widget, você deve especificar um `LayoutManager` e um adaptador. Conforme mostrado nesta figura, `LayoutManager` é o intermediário entre o adaptador e o `RecyclerView`:

![Diagrama de RecyclerView com LayoutManager, o adaptador e o conjunto de dados de suporte](lollipop-images/recyclerview-diagram.png)

As capturas de tela a seguir ilustram uma `RecyclerView` que contém 100 itens (cada item consiste em um `ImageView` e um `TextView`):

[![Capturas de tela de um aplicativo RecyclerView rolando imagens](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png)

`RecyclerView` lida com esse conjunto de dados grande com facilidade &ndash; rolagem desde o início da lista até o fim da lista neste exemplo o aplicativo leva apenas alguns segundos. `RecyclerView` também dá suporte a animações; Na verdade, animações para adicionar e remover itens estão habilitadas por padrão. Quando um item é adicionado a um `RecyclerView`, ele desaparece em como mostra esta sequência de capturas de tela:

[![Quadros por captura de tela de quadro de esmaecimento de item uma foto em](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png)

Para obter mais informações sobre `RecyclerView`, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

<a name="cardview" />

### <a name="cardview"></a>CardView

`CardView` é uma exibição simple que simula um cartão flutuante com cantos arredondados. Porque `CardView` tem sombras exibição interna, ele fornece uma maneira fácil para adicionar profundidade visual ao seu aplicativo. As capturas de tela a seguir mostram três exemplos de texto de `CardView`:

[![Capturas de tela de exemplo de aplicativos que usam RecyclerView com itens com base em CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png)

Cada uma das placas no exemplo acima contém uma `TextView`; a cor de plano de fundo é definida por meio de `cardBackgroundColor` atributo.

Para obter mais informações sobre `CardView`, consulte [CardView](~/android/user-interface/controls/card-view.md).

<a name="enhanced" />

## <a name="enhanced-notifications"></a>Notificações aprimoradas

O sistema de notificação no Android 5.0 foi significativamente atualizado com novos recursos e um novo formato visual. Notificações têm uma nova aparência no Android 5.0. Por exemplo, as notificações no Android 5.0 agora usam texto escuro sobre um plano de fundo claro:

![Exemplo de uma notificação de Android 5.0 não expandido](lollipop-images/expanded-notification-contracted.png)

Quando um ícone grande é exibido em uma notificação (conforme mostrado no exemplo acima), o Android 5.0 apresenta o ícone pequeno como uma notificação sobre o ícone grande. 

No Android 5.0, as notificações também podem aparecer no bloqueio do dispositivo.
Por exemplo, aqui está uma captura de tela de exemplo de um bloqueio com uma única notificação:

[![Captura de tela de notificação que aparece na tela de bloqueio](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png)

Os usuários podem com um toque duplo uma notificação de bloqueio para desbloquear o dispositivo e ir para o aplicativo que originou a notificação, ou passe o dedo para ignorar a notificação. Notificações têm um novo *visibilidade* configuração determina a quantidade de conteúdo pode ser exibido no bloqueio. Os usuários podem optar por permitir que conteúdo confidencial a ser mostrado em notificações de bloqueio.

Android 5.0 introduz um novo formato de apresentação de notificação de alta prioridade chamado *Heads-Up*. Notificações de Heads-Up deslize para baixo da parte superior da tela por alguns segundos e, em seguida, retreat volta para o formato de notificação na parte superior da tela. Notificações de Heads-Up possibilitam que o sistema de interface do usuário para colocar informações importantes na frente do usuário sem interromper a atividade em execução no momento. O exemplo a seguir ilustra uma simple notificação Heads-Up que exibe na parte superior de um aplicativo:

[![Exemplo de uma notificação heads-up](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png)

Notificações de Heads-Up geralmente são usadas para os seguintes eventos:

-   Uma nova mensagem Avançar

-   Uma chamada telefônica

-   Indicação de bateria fraca

-   Um alarme

Android 5.0 exibe uma notificação no formato heads-up somente quando ela tem uma configuração de prioridade alta ou max.

No Android 5.0, você pode fornecer os metadados de notificação para ajudar a classificar e exibir as notificações mais inteligente Android. Android 5.0 organiza as notificações de acordo com a prioridade, visibilidade e categoria.
Categorias de notificação são usadas para filtrar as notificações podem ser apresentadas quando o dispositivo estiver em *não interfira forma* modo.

Para obter informações detalhadas sobre como criar e iniciar as notificações com os recursos mais recentes do Android 5.0, consulte [notificações Local](~/android/app-fundamentals/notifications/local-notifications.md).

<a name="newapis" />

## <a name="new-apis"></a>Novas APIs

Além dos novos recursos de aparência e funcionalidade descritos acima, o Android 5.0 adiciona novas APIs que estendem os recursos de multimídia existente, o armazenamento e a funcionalidade sem fio/conectividade. Além disso, o Android 5.0 inclui novas APIs que oferecem suporte para um novo recurso do Agendador de trabalho.

### <a name="camera"></a>Câmera

Android 5.0 oferece várias APIs de novos recursos aprimorados de câmera. O novo `Android.Hardware.Camera2` namespace inclui a funcionalidade de acesso a dispositivos de câmera individuais conectado a um dispositivo Android. Além disso, `Android.Hardware.Camera2` modelos de cada dispositivo de câmera como um pipeline: aceita uma solicitação de captura, captura a imagem e, em seguida, gera o resultado. Essa abordagem permite que aplicativos para a fila de várias solicitações de captura em um dispositivo de câmera.

As seguintes APIs possibilitam a esses novos recursos:

-   `CameraManager.GetCameraIdList` &ndash; Ajuda você a acessar programaticamente os dispositivos de câmera; Você usa `CameraManager.OpenCamera` para se conectar a um dispositivo de câmera específico.

-   `CameraCaptureSession` &ndash; Captura ou fluxo de imagens do dispositivo de câmera. Implementar um `CameraCaptureSession.CaptureListener` interface para lidar com os novos eventos de captura de imagem.

-   `CaptureRequest` &ndash; Define parâmetros de captura.

-   `CaptureResult` &ndash; Fornece os resultados de uma operação de captura de imagem.

Para obter mais informações sobre a câmera novas APIs no Android 5.0, consulte [mídia](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reprodução de áudio

5.0 atualizações Android a `AudioTrack` classe para reprodução de áudio melhor:

-   `ENCODING_PCM_FLOAT` &ndash; Configura `AudioTrack` para aceitar dados de áudio no formato de ponto flutuante de alta qualidade (graças precisão ampliada), maior capacidade e melhor intervalo dinâmico. Além disso, o formato de ponto flutuante ajuda a evitar o recorte de áudio.

-   `ByteBuffer` &ndash; Agora você pode fornecer dados de áudio para `AudioTrack` como uma matriz de bytes.

-   `WRITE_NON_BLOCKING` &ndash; Esta opção simplifica o armazenamento em buffer e multithread para alguns aplicativos.

Para obter mais informações sobre `AudioTrack` melhorias no Android 5.0, consulte [mídia](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controle de reprodução de mídia

Android 5.0 introduz os novos `Android.Media.MediaController` de classe que substitui o `RemoteControlClient`. `Android.Media.MediaController` Fornece APIs de controle de transporte simplificada e oferece controle de thread-safe de reprodução fora do contexto de interface do usuário. As seguintes novas APIs lidar com controle de transporte:

-   `Android.Media.Session.MediaSession` &ndash; Uma sessão de controle de mídia que lida com vários controladores. Você chamar `MediaSession.GetSessionToken` para solicitar um token que seu aplicativo usa para interagir com a sessão.

-   `MediaController.TransportControls` &ndash; Identificadores de comandos, como transporte **reproduzir**, **parar**, e **ignorar**.

Além disso, você pode usar o novo `Android.App.Notification.MediaStyle` classe para associar uma sessão com o conteúdo de notificação avançados (como extrair e mostrar as capas).

Para obter mais informações sobre os novos recursos de controle de reprodução de mídia no Android 5.0, consulte [mídia](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Armazenamento

Android 5.0 atualiza a estrutura de acesso de armazenamento para tornar mais fácil para aplicativos para trabalhar com documentos e pastas:

-   Para selecionar uma subárvore de diretório, você pode criar e enviar um `Android.Intent.Action.OPEN_DOCUMENT_TREE` intenção. Essa tentativa faz com que o sistema exibir todas as instâncias do provedor que suportam a seleção de subárvore; o usuário, em seguida, procura e selecionar um diretório.

-   Para criar e gerenciar novos documentos ou pastas em qualquer lugar em uma subárvore, use o novo `CreateDocument`, `RenameDocument`, e `DeleteDocument` métodos de `DocumentsContract`.

-   Para obter os caminhos para diretórios de mídia em todos os dispositivos de armazenamento compartilhado, chame o novo `Android.Content.Context.GetExternalMediaDirs` método.

Para obter mais informações sobre o armazenamento de novas APIs no Android 5.0, consulte [armazenamento](http://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividade e sem fio

Android 5.0 adiciona os seguintes aprimoramentos de API para conectividade e sem fio:

-   Novo *rede vários* APIs que tornam possível para aplicativos localizar e selecionar redes com recursos específicos antes de fazer uma conexão.

-   Funcionalidade de difusão de Bluetooth que permite que um dispositivo Android 5.0 atuar como um periférico Bluetooth de baixo consumo de energia.

-   Aprimoramentos de NFC que tornam mais fácil de usar a funcionalidade de comunicação de curta distância para compartilhar dados com outros dispositivos.

Para obter mais informações sobre a conectividade APIs no Android 5.0 e sem fio, consulte [conectividade e sem fio](http://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Agendamento de trabalhos

Android 5.0 introduz um novo `JobScheduler` API que pode ajudar os usuários minimizar bateria pelo agendamento certas tarefas de executar somente quando o dispositivo está conectado e carregamento. Esse recurso do Agendador de trabalho também pode ser usado para agendar uma tarefa para ser executada quando as condições são mais adequadas para essa tarefa, como baixar um arquivo grande quando o dispositivo estiver conectado por uma rede Wi-Fi em vez de uma rede limitada.

Para obter mais informações sobre o novo trabalho de agendamento APIs no Android 5.0, consulte [agendar trabalhos](http://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumo

Este artigo fornece uma visão geral dos novos recursos importantes no Android 5.0 para desenvolvedores de aplicativo xamarin:

-   Tema material

-   Animations

-   Sombras de exibição e de elevação

-   Recursos de cor, como tonalidade drawable e proeminentes extração de cores

-   O novo `RecyclerView` e `CardView` widgets

-   Aprimoramentos de notificação

-   Novas APIs de câmera, reprodução de áudio, controle de mídia, armazenamento, sem fio/conectividade e agendamento de trabalho

Se você for novo no desenvolvimento do Xamarin Android, leia [instalação e configuração](~/android/get-started/installation/index.md) para ajudá-lo a começar com xamarin.
[Olá, Android](~/android/get-started/hello-android/index.md) é uma introdução excelente para aprender a criar projetos Android.



## <a name="related-links"></a>Links relacionados

- [Visualização do desenvolvedor Android L](http://developer.android.com/preview/index.html)
- [Obter o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Design de material](http://developer.android.com/preview/material/index.html)
- [Noções básicas de criação de material](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
