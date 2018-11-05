---
title: Recursos do Lollipop
description: Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5.0 (Lollipop). Esses recursos incluem um novo estilo de interface de usuário chamado tema de Material, bem como novos recursos de suporte, como animações, sombras de modo de exibição e tonalidade desenháveis. Android 5.0 também inclui notificações aprimoradas, dois novos widgets de interface do usuário, um novo Agendador de trabalho e um punhado de novas APIs para melhorar o armazenamento, rede, conectividade e recursos de multimídia.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d79c0563d1dc9a2cfe75b702300982bb4d38553b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117857"
---
# <a name="lollipop-features"></a>Recursos do Lollipop

_Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5.0 (Lollipop). Esses recursos incluem um novo estilo de interface de usuário chamado tema de Material, bem como novos recursos de suporte, como animações, sombras de modo de exibição e tonalidade desenháveis. Android 5.0 também inclui notificações aprimoradas, dois novos widgets de interface do usuário, um novo Agendador de trabalho e um punhado de novas APIs para melhorar o armazenamento, rede, conectividade e recursos de multimídia._

## <a name="lollipop-overview"></a>Visão geral do Lollipop

Android 5.0 (Lollipop) introduz uma nova linguagem de design *Design de Material*, e com ele dar suporte a conversão de novos recursos para tornar os aplicativos mais fácil e intuitivo usar. Com o Design de Material, Android 5.0 proporciona telefones Android um facelift; Ele também fornece um novo conjunto de regras de design para tablets baseados no Android, computadores desktop, inspeções e smart TVs. Essas regras de design enfatizam a simplicidade e minimalism enquanto torna o uso de familiares atributos táteis (como realistas indicações de superfície e borda) para ajudar os usuários rapidamente e intuitivamente, entendemos a interface.

*Tema de material* é a incorporação desses princípios de design de interface do usuário no Android. Este artigo começa com abordando os recursos de suporte do tema de Material:

-   **Animações** &ndash; *comentários de toque* animações *transição da atividade* animações, *exibir transição de estado* animações e um *revelar efeito*.

-   **Exibir sombras e elevação** &ndash; modos de exibição agora têm um `elevation` propriedade; exibições com maior `elevation` valores convertidos sombras maiores no plano de fundo.

-   **Recursos de cor** &ndash; *tonalidade Desenháveis* possibilita a reutilização de ativos de imagem alterando sua cor, e *extração de cor de destaque* ajuda você a dinamicamente tema do seu aplicativo com base em cores em uma imagem.

Tema de Material de muitos recursos já estão incorporados a interface do usuário Android 5.0 experiência, enquanto outros devem ser explicitamente adicionados aos aplicativos. Por exemplo, alguns modos de exibição padrão (como botões) já incluir animações de comentários de toque, enquanto aplicativos devem habilitar a maioria das sombras do modo de exibição.

Além dos aprimoramentos da interface do usuário trazidos por meio de tema de Material, o Android 5.0 também inclui vários outros recursos novos que são abordados neste artigo:

-   **Aprimorado notificações** &ndash; notificações no Android 5.0 foram significativamente atualizadas com uma nova aparência, suporte para notificações de lockscreen e uma nova *Heads-Up* formato de apresentação da notificação.

-   **Novos widgets de interface do usuário** &ndash; nova `RecyclerView` widget torna mais fácil para aplicativos transmitir grandes conjuntos de dados e informações complexas e o novo `CardView` widget fornece um formato de apresentação simplificada cartão para exibir texto e imagens.

-   **Novas APIs** &ndash; Android 5.0 adiciona novas APIs para suporte a várias redes, melhor conectividade Bluetooth, gerenciamento de armazenamento mais fácil e um controle mais flexível de players de multimídia e dispositivos de câmera. Um novo recurso de agendamento de trabalho está disponível para executar tarefas de forma assíncrona em horários agendados. Esse recurso ajuda a melhorar a vida útil da bateria, por exemplo, agendar tarefas entrem em vigor quando o dispositivo está conectado e carregando.


## <a name="requirements"></a>Requisitos

A seguir é necessário para usar os novos recursos do Android 5.0 em aplicativos baseados em Xamarin:

-   **Xamarin. Android** &ndash; xamarin. Android 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac. 

-   **SDK do Android** &ndash; Android 5.0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; xamarin. Android exige [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior, se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1.8 também dá suporte a níveis de API anteriores ao 24, incluindo o Lollipop). A versão de 64 bits do JDK 1.8 é necessária se você estiver usando controles personalizados ou o pré-visualizador de formulários.

Você pode continuar a usar [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se você estiver desenvolvendo especificamente para o nível de API 23 ou anterior.


## <a name="setting-up-an-android-50-project"></a>Como configurar um projeto do Android 5.0

Para criar um projeto do Android 5.0, você deve instalar as últimas ferramentas e pacotes do SDK. Use as etapas a seguir para configurar um projeto xamarin. Android que tem como alvo o Android 5.0:

1. Instalar as ferramentas do xamarin. Android e ativar sua licença do Xamarin. Ver [instalação e configuração](~/android/get-started/installation/index.md) para obter mais informações sobre como instalar o xamarin. Android.

2. Se você estiver usando o Visual Studio para Mac, instale as atualizações mais recentes do Android 5.0.

3. Inicie o Gerenciador de SDK do Android (no Visual Studio para Mac, use **ferramentas &gt; abrir o Gerenciador de SDK do Android&hellip;**) e instale o Android SDK Tools 23.0.5 ou posterior:

    [![Selecionando ferramentas SDK do Android no Gerenciador de SDK do Android](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Além disso, instale os pacotes mais recentes do SDK do Android 5.0 (API 21 ou posterior):

    [![Instalando pacotes de SDK do Android 5.0 no Gerenciador de SDK do Android](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Para obter mais informações sobre como usar o Gerenciador de SDK do Android, consulte [Gerenciador de SDK](http://developer.android.com/tools/help/sdk-manager.html).

4. Crie um novo projeto do xamarin. Android. Se você for novo no desenvolvimento de Android com Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos do Android. Quando você cria um projeto do Android, certifique-se de definir as configurações de versão para Android 5.0.
   No Visual Studio para Mac, navegue até **opções de projeto &gt; construir &gt; gerais** e defina **estrutura de destino** para **Android 5.0 (Lollipop)** ou mais tarde:

    ![Definir o destino Framwework como Android 5.0 Lollipop](lollipop-images/target-framework.png)

   Sob **opções de projeto &gt; compilar &gt; aplicativo Android**, defina mínima e a versão do Android de destino **automático - use a versão do target framework**:

    ![Definindo as versões mínima e o destino do Android para automático](lollipop-images/minimum-android-version.png)

5. Configure um emulador ou um dispositivo Android para testar seu aplicativo. Se você estiver usando um emulador, consulte [configuração do Android Emulator](~/android/get-started/installation/android-emulator/index.md) para aprender a configurar um emulador do Android para uso com o Xamarin Studio ou Visual Studio. Se você estiver usando um dispositivo Android, consulte [configuração o SDK Preview](https://developer.android.com/preview/setup-sdk.html) para saber como atualizar seu dispositivo para o Android 5.0. Para configurar seu dispositivo Android para executar e depurar aplicativos xamarin. Android, consulte [configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

Observação: Se você estiver atualizando um projeto existente do Android que era destinado à visualização de L do Android, você deve atualizar o **estrutura de destino** e **Android versão** para os valores descritos acima.

## <a name="important-changes"></a>Alterações importantes

Anteriormente publicados aplicativos Android podem ser afetados por alterações no Android 5.0. Em particular, o Android 5.0 usa um novo tempo de execução e um formato de notificação de alteração de forma significativa.

### <a name="android-runtime"></a>Tempo de execução do Android

Android 5.0 usa a nova Android em tempo de execução (arte) como o tempo de execução padrão, em vez de Dalvik. ARTE implementa vários recursos novos importantes:

-   **Compilação Ahead-of-time (AOT)** &ndash; AOT pode melhorar o desempenho do aplicativo ao compilar o código do aplicativo antes que o aplicativo é iniciado pela primeira vez. Quando um aplicativo é instalado, arte gera um aplicativo compilado executável para o dispositivo de destino.

-   **Aprimorada a coleta de lixo (GC)** &ndash; aperfeiçoamentos de GC no ART também podem melhorar o desempenho do aplicativo. Coleta de lixo agora usa a pausa de um GC em vez de dois e conclusão de operações simultâneas de GC no momento mais oportuno.

-   **Depuração de aplicativo aprimorada** &ndash; arte fornece mais detalhes de diagnóstico para ajudar a analisar as exceções e relatórios de falha.

Os aplicativos existentes devem funcionar sem alteração em arte &ndash; , exceto para aplicativos que exploram técnicas exclusivas para o tempo de execução Dalvik anterior, que pode não funcionar em arte. Para obter mais informações sobre essas alterações, consulte [verificando o comportamento do aplicativo no Android em tempo de execução (arte)](http://developer.android.com/guide/practices/verifying-apps-art.html).


### <a name="notification-changes"></a>Alterações de notificação

As notificações foram alterados significativamente no Android 5.0:

-   **Sons e vibração são tratadas diferentemente** &ndash; sons de notificação e vibrações agora são tratadas pela `Notification.Builder` em vez de `Ringtone`, `MediaPlayer`, e `Vibrator`.

-   **Novo esquema de cores** &ndash; acordo com o tema de Material, notificações são processadas com texto escuro sobre planos de fundo brancos ou muito leves. Além disso, os canais alfa em ícones de notificação podem ser modificados pelo Android para coordenar com esquemas de cores do sistema. 

-   **Notificações de Lockscreen** &ndash; notificações agora podem aparecer na tela de bloqueio da dispositivo.

-   **Heads-Up** &ndash; notificações de alta prioridade agora são exibidos em uma pequena janela flutuante (notificação de alerta) quando o dispositivo seja desbloqueado, e a tela é ativada.

Na maioria dos casos, a portabilidade de funcionalidade de notificação de aplicativo existente para o Android 5.0 exige as seguintes etapas:

1.  Converter seu código para usar `Notification.Builder` (ou `NotificationsCompat.Builder`) para a criação de notificações. 

2.  Verifique se seus ativos existentes de notificação estão visíveis no novo esquema de cores do tema de Material.

3.  Decida quais visibilidade suas notificações deverão ter quando são apresentados na tela de bloqueio. Se uma notificação não é pública, o conteúdo que deve aparecer na tela de bloqueio?

4.  Definir a categoria de notificações para que eles são manipulados corretamente no Android 5.0 novos *não incomodar* modo.

Se as notificações de controles de transporte de mídia de vídeo reprodução status atual, use `RemoteControlClient`, ou chame `ActivityManager.GetRecentTasks`, consulte [importantes alterações de comportamento](http://developer.android.com/preview/api-overview.html#Behaviors) para obter mais informações sobre como atualizar suas notificações para Android 5.0.

Para obter informações sobre a criação de notificações no Android, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md). O [compatibilidade](~/android/app-fundamentals/notifications/local-notifications.md#compatibility) seção deste artigo explica como criar notificações que são compatíveis para baixo com versões anteriores do Android.


## <a name="material-theme"></a>Tema de material

O novo tema de Material 5.0 Android traz alterações abrangentes para a aparência da interface do usuário Android. Agora, os elementos visuais usam táteis superfícies que assumem os gráficos em negrito, tipografia e cores vivas de design com base em impressão. Exemplos de tema de Material são descritos nas seguintes capturas de tela:

[![Capturas de tela da tela inicial do tema de Material, a tela de aplicativos e a tela de configuração](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 parabenize a tela inicial mostrada à esquerda. A captura de tela de center é a primeira tela da lista de aplicativo e a captura de tela à direita é o **configurações** tela. Do Google [Design de Material](https://material.io/guidelines/material-design/introduction.html) especificação explica as regras de design subjacentes por trás do novo conceito de tema de Material.

Tema de material inclui três tipos internos que você pode usar em seu aplicativo: o `Theme.Material` tema escuro (padrão), o `Theme.Material.Light` tema e o `Theme.Material.Light.DarkActionBar` tema: 

[![Capturas de tela de escuro, claro e DarkActionBar temas](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Para obter mais informações sobre como usar os recursos de tema de Material em aplicativos xamarin. Android, consulte [tema de Material](~/android/user-interface/material-theme.md).


## <a name="animations"></a>Animations

Android 5.0 fornece animações de comentários de toque, animações de transição da atividade e animações de transição de estado de exibição para tornar as interfaces de aplicativo mais intuitivo de usar. Além disso, podem usar os aplicativos do Android 5.0 *revelar efeito* animações ocultar ou exibir os modos de exibição. Você pode usar *curvos movimento* configurações para definir a velocidade ou lentamente animações são renderizadas.


### <a name="touch-feedback-animations"></a>Animações de comentários de toque

Animações de comentários de toque fornecem aos usuários comentários visuais quando um modo de exibição foi tocado. Por exemplo, botões agora exibem um efeito dominó quando eles são tocados &ndash; trata a animação de comentários de toque padrão no Android 5.0. Animação de ondulação é implementada pelo novo `RippleDrawable` classe. O efeito de ondulação pode ser configurado para terminar em um dos limites da exibição ou ultrapassar os limites da exibição. Por exemplo, a sequência de capturas de tela a seguir ilustra o efeito de ondulação em um botão durante a animação de toque:

![Quadro de capturas de tela de quadro de animação de ondulação em um botão](lollipop-images/touch-animation.png)

Contato de toque inicial com o botão ocorre na primeira imagem à esquerda, enquanto a sequência restante (da esquerda para a direita) mostra como o efeito de ondulação espalha até a borda do botão. Quando a animação de ondulação termina, a exibição retorna à sua aparência original. A animação de ondulação padrão é feita em uma fração de segundo, mas o comprimento da animação pode ser personalizado para períodos maiores ou menores.

Para obter mais informações sobre animações de comentários no Android 5.0 de toque, consulte [personalizar comentários de toque](http://developer.android.com/training/material/animations.html#Touch).


### <a name="activity-transition-animations"></a>Animações de transição da atividade

Animações de transição da atividade dar aos usuários uma sensação de continuidade visual quando uma atividade faz a transição para outro. Aplicativos podem especificar três tipos de animações de transição:

-   **Insira a transição** &ndash; para quando uma atividade entra em cena.

-   **Transição de saída** &ndash; para quando uma atividade sair da cena.

-   **Compartilhado a transição do elemento** &ndash; para quando uma exibição que é comum às duas atividades altera como as transições de atividade primeiro para a próxima.

Por exemplo, a sequência de capturas de tela a seguir ilustra uma transição do elemento compartilhado:

[![Quadro por quadro capturas de tela de uma animação de transição do elemento compartilhado](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Um elemento compartilhado (uma foto de uma lagarta) é um dos vários modos de exibição na primeira atividade; Ele amplia para se tornar o único modo de exibição na atividade de segundo como as transições de atividade primeiro ao segundo.

#### <a name="enter-transition-animation-types"></a>Insira os tipos de animação de transição

As transições de enter, o Android 5.0 fornece três tipos de animações:

-   **Animação de explodir** &ndash; amplia uma exibição do centro da cena.

-   **Deslize a animação** &ndash; move um modo de exibição em uma das bordas de uma cena.

-   **Animação de esmaecimento** &ndash; atenua um modo de exibição na cena.

#### <a name="exit-transition-animation-types"></a>Tipos de animação de transição de saída

Para sair de transições, o Android 5.0 fornece três tipos de animações:

-   **Animação de explodir** &ndash; reduz um modo de exibição para o centro da cena.

-   **Deslize a animação** &ndash; move uma exibição para uma das bordas de uma cena.

-   **Animação de esmaecimento** &ndash; desaparece uma exibição de cena.

#### <a name="shared-element-transition-animation-types"></a>Tipos de animação de transição do elemento de compartilhado

Elemento compartilhado transições dão suporte a vários tipos de animações, tais como:

-   Alterando os limites de layout ou um clipe de um modo de exibição.

-   Alterar a escala e rotação de uma exibição.

-   Alterando o tipo de tamanho e escala para um modo de exibição.

Para obter mais informações sobre animações de transição de atividade no Android 5.0, consulte [transições de atividade de personalizar](http://developer.android.com/training/material/animations.html#Transitions).


### <a name="view-state-transition-animations"></a>Animações de transição de estado de exibição

Android 5.0 torna possível para animações ser executado quando o estado de um modo de exibição muda. Você pode animar as transições de estado de exibição, usando uma das seguintes técnicas:

-   Crie desenháveis que animar alterações de estado associadas a uma exibição específica. O novo `AnimatedStateListDrawable` classe permite que você crie desenháveis que exibem as animações entre alterações de estado de exibição.

-   Defina a funcionalidade de animação que é executado quando o estado de um modo de exibição muda. O novo `StateListAnimator` classe permite que você defina um animator que é executado quando o estado de um modo de exibição muda.

Para obter mais informações sobre animações de transição de estado de exibição no Android 5.0, consulte [animar alterações de estado de exibição](http://developer.android.com/training/material/animations.html#ViewState).


### <a name="reveal-effect"></a>Revelar efeito

O *revelar efeito* é um círculo de recorte radius que as alterações para revelar ou ocultar um modo de exibição. Você pode controlar esse efeito, definindo o inicial e final o raio do círculo recorte. A sequência de capturas de tela a seguir ilustra uma animação de efeito de revelação no centro da tela:

[![Quadro de capturas de tela de quadro de animação de revelação](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

A próxima sequência ilustra uma animação de efeito de revelação que ocorre do canto inferior esquerdo da tela:

[![Quadro de capturas de tela de quadro de animação de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Revelar as animações podem ser revertidas; ou seja, o círculo de recorte pode reduzir para ocultar o modo de exibição em vez de ampliar para revelar o modo de exibição.

Para obter mais informações sobre o efeito de revelação de Android 5.0 no, consulte [usar o efeito de revelar](http://developer.android.com/training/material/animations.html#Reveal).


### <a name="curved-motion"></a>Curvas de movimento

Além desses recursos de animação, o Android 5.0 também fornece novas APIs que permitem que você especifique as curvas de tempo e o movimento de animações. Android 5.0 usa essas curvas para interpolar movimento temporal e espacial durante as animações. Três curvas são definidas no Android 5.0:

-   **Fast\_horizontalmente\_linear\_na** &ndash; acelera rapidamente e continua a acelerar até o término da animação.

-   **Fast\_horizontalmente\_lento\_na** &ndash; Accelerates lentamente e rapidamente desacelerada até o final da animação.

-   **Linear\_horizontalmente\_lento\_na** &ndash; começa com uma velocidade de pico e lentamente desacelerada ao final da animação.

Você pode usar o novo `PathInterpolator` classe para especificar como a interpolação da animação ocorre. `PathInterpolator` é um interpolador que atravessa os caminhos de animação de acordo com os pontos de controle especificado e as curvas de movimento. Para obter mais informações sobre como especificar configurações de movimento de curva no Android 5.0, consulte [movimento de curvas de uso](http://developer.android.com/training/material/animations.html#CurvedMotion).


## <a name="view-shadows--elevation"></a>Sombras de modo de exibição & elevação

No Android 5.0, você pode especificar o *elevação* de uma exibição, definindo um novo `Z` propriedade. Uma maior `Z` valor faz com que o modo de exibição para uma sombra maiores em segundo plano, fazendo com que o modo de exibição aparecem em float superior acima a tela de fundo. Você pode configurar a elevação inicial de um modo de exibição por meio seu `elevation` atributo no layout.

O exemplo a seguir ilustra as sombras convertido por um vazio `TextView` controlar quando o atributo de elevação é definido como 2dp, 4dp e 6dp, respectivamente:

[![Capturas de tela de sombras de exibição maiores progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Exibir configurações de sombra podem ser estáticas (conforme mostrado acima) ou pode ser usados em animações para torná-la parece temporariamente subir acima de plano de fundo da exibição. Você pode usar o `ViewPropertyAnimator` classe para animar a elevação de um modo de exibição. A elevação de um modo de exibição é a soma de seu layout `elevation` configuração além de um `translationZ` propriedade que pode ser definida por meio de um `ViewPropertyAnimator` chamada de método.

Para obter mais informações sobre como sombras de modo de exibição no Android 5.0, consulte [definindo sombras e modos de exibição de recorte](http://developer.android.com/training/material/shadows-clipping.html).


## <a name="color-features"></a>Recursos de cor

Android 5.0 fornece dois novos recursos para gerenciamento de cores em aplicativos:

-   *Tonalidade desenháveis* permite que você altere as cores dos ativos de imagem alterando um atributo de layout.

-   *Extração de cor de destaque* possibilita que você personalize dinamicamente o tema de cores do seu aplicativo para coordenar com a paleta de cores de uma imagem exibida.


### <a name="drawable-tinting"></a>Tonalidade desenháveis

Android 5.0 layouts reconhecem um novo `tint` atributo que você pode usar para definir a cor de desenháveis sem a necessidade de criar várias versões desses ativos para exibir cores diferentes. Para usar esse recurso, você deve definir um bitmap como uma máscara de alfa e o uso de `tint` atributo para definir a cor do ativo. Isso torna possível para que você possa criar ativos de uma vez e colori-los em seu layout para corresponder ao seu tema.

No exemplo a seguir, um ativo de imagem única &ndash; um logotipo branco com um fundo transparente &ndash; é usado para criar variações de tonalidade:

![Logotipo do Xamarin em branco com o plano de fundo transparente](lollipop-images/xamarin-logo-white.png)

Esse logotipo é exibido acima de um plano de fundo azul circular, conforme mostrado nos exemplos a seguir. A imagem à esquerda é como o logotipo é exibido sem um `tint` configuração. No centro, o logotipo da imagem `tint` atributo é definido para um cinza-escuro. A imagem à direita, `tint` é definido para um cinza claro:

![Exemplos do logotipo acima com configurações diferentes de tonalidade](lollipop-images/drawable-tinting.png)

Para obter mais informações sobre a tonalidade desenháveis no Android 5.0, consulte [Desenháveis tonalidade](http://developer.android.com/training/material/drawables.html#DrawableTint).


### <a name="prominent-color-extraction"></a>Extração de cor de destaque

O Android 5.0 novo `Palette` classe permite que você extraia as cores de uma imagem para que você possa aplicá-los dinamicamente para uma paleta de cores personalizada. O `Palette` classe extrai as seis cores de uma imagem e essas cores de acordo com os níveis relativos de saturação de cor e o brilho de rótulos:

-   Vibrante

-   Escuro vibrante

-   Luz vibrante

-   Sem som

-   Mudo escuro

-   Luz mudo

Por exemplo, nas capturas de tela seguir, um aplicativo de exibição de fotos extrai as cores de destaque da imagem no monitor em usam essas cores para adaptar o esquema de cores do aplicativo para corresponder à imagem:

[![Capturas de tela de extrações de cor de tema rosa, verde e azul](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

As capturas de tela acima, a barra de ação é definida como extraído "vibrante light" cor e o plano de fundo é definido como extraído "vibrante escuro" cor. Em cada exemplo acima, uma linha de quadrados de cores pequeno é incluída para ilustrar a paleta de cores que foram extraídas da imagem.

Para obter mais informações sobre a extração de cor no Android 5.0, consulte [extraindo proeminente cores de uma imagem](http://developer.android.com/training/material/drawables.html#ColorExtract).


## <a name="new-ui-widgets"></a>Novos Widgets de interface do usuário

Android 5.0 introduz dois novos widgets de interface do usuário:

-   `RecyclerView` &ndash; Um grupo de modo de exibição que exibe uma lista de itens roláveis.

-   `CardView` &ndash; Um layout básico com cantos arredondados.

Ambos os widgets incluem suporte embutida para recursos de tema de Material; Por exemplo, `RecyclerView` usa animações para adicionar e remover os modos de exibição, e `CardView` usa exibir sombras para fazer com que cada cartão apareça flutuando acima a tela de fundo. Exemplos desses novos widgets são mostrados nas seguintes capturas de tela:

[![Capturas de tela de aplicativos criados com RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Captura de tela à esquerda é um exemplo de `RecyclerView` usada em um aplicativo de email e a captura de tela na direita é um exemplo de `CardView` conforme usado em um aplicativo de reserva de viagem.


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` é semelhante ao `ListView,` , mas ele é mais adequado para grandes conjuntos de modos de exibição ou listas de elementos que são alteradas dinamicamente. Como `ListView,` você especifica um adaptador para acessar o conjunto de dados subjacente. No entanto, diferentemente `ListView,` você usa um *Gerenciador de layout* para posicionar os itens dentro do `RecyclerView`. O Gerenciador de layout também se encarrega de reciclagem de modo de exibição; Ele gerencia a reutilização de modos de exibição do item que não são mais visíveis ao usuário.

Quando você usa um `RecyclerView` widget, você deve especificar um `LayoutManager` e um adaptador. Conforme mostrado nesta figura `LayoutManager` é o intermediário entre o adaptador e o `RecyclerView`:

![Diagrama de RecyclerView com LayoutManager, o adaptador e o conjunto de dados de suporte](lollipop-images/recyclerview-diagram.png)

As capturas de tela a seguir ilustram uma `RecyclerView` que contém 100 itens (cada item consiste em um `ImageView` e um `TextView`):

[![Capturas de tela de um aplicativo de RecyclerView rolagem por meio de imagens](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` lida com esse conjunto de dados grande com facilidade &ndash; rolagem desde o início da lista ao fim da lista neste exemplo o aplicativo leva apenas alguns segundos. `RecyclerView` também dá suporte a animações; Na verdade, as animações para adicionar e remover itens são habilitadas por padrão. Quando um item é adicionado a um `RecyclerView`, ele desaparece em conforme mostrado na sequência de capturas de tela:

[![Quadro por quadro de captura de tela de esmaecimento de item uma foto no](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Para obter mais informações sobre `RecyclerView`, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


### <a name="cardview"></a>Widgets CardView

`CardView` é uma exibição simple que simula um cartão flutuante com cantos arredondados. Porque `CardView` tem sombras de modo de exibição interno, ele fornece uma maneira fácil para que você possa adicionar profundidade visual ao seu aplicativo. As capturas de tela a seguir mostram três exemplos orientados ao texto de `CardView`:

[![Capturas de tela de exemplo de aplicativos usando RecyclerView com itens com base em widgets CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Cada um dos cartões no exemplo acima contém uma `TextView`; a cor do plano de fundo é definida por meio de `cardBackgroundColor` atributo.

Para obter mais informações sobre `CardView`, consulte [widgets CardView](~/android/user-interface/controls/card-view.md).


## <a name="enhanced-notifications"></a>Notificações aprimoradas

O sistema de notificação no Android 5.0 foi significativamente atualizado com novos recursos e um novo formato visual. As notificações estão de cara nova no Android 5.0. Por exemplo, as notificações no Android 5.0 agora usam texto escuro ao longo de um plano de fundo claro:

![Exemplo de uma notificação de Android 5.0 não expandido](lollipop-images/expanded-notification-contracted.png)

Quando um ícone grande é exibido em uma notificação (conforme mostrado no exemplo acima), o Android 5.0 apresenta o ícone pequeno como uma notificação sobre o ícone grande. 

No Android 5.0, as notificações também podem aparecer na tela de bloqueio da dispositivo.
Por exemplo, aqui está uma captura de tela de exemplo de um lockscreen com uma única notificação:

[![Captura de tela de notificação que aparece na tela de bloqueio](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Os usuários podem com um toque duplo uma notificação na tela de bloqueio para desbloquear o dispositivo e vá para o aplicativo que originou essa notificação, ou passe o dedo para ignorar a notificação. Notificações têm um novo *visibilidade* definição que determina a quantidade de conteúdo pode ser exibido na tela de bloqueio a. Os usuários podem escolher se deseja permitir que conteúdos confidenciais sejam mostrados nas notificações de lockscreen.

Android 5.0 introduz um novo formato de apresentação de notificação de alta prioridade chamado *Heads-Up*. Notificações de alerta Deslizar para baixo da parte superior da tela por alguns segundos e, em seguida, retreat volta para o tom de notificação na parte superior da tela. Notificações de alerta possibilitam que o sistema de interface do usuário para colocar informações importantes na frente do usuário sem interromper a atividade em execução no momento. O exemplo a seguir ilustra uma simple notificação Heads-Up que exibe na parte superior de um aplicativo:

[![Exemplo de uma notificação heads-up](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

Notificações de alerta são normalmente usadas para os seguintes eventos:

-   Uma nova mensagem Avançar

-   Uma chamada telefônica

-   Indicação de bateria fraca

-   Um alarme

Android 5.0 exibirá uma notificação no formato heads-up somente quando ele tem uma configuração de prioridade alta ou max.

No Android 5.0, você pode fornecer metadados de notificação para ajudar a Android, classificar e exibir as notificações mais inteligente. Android 5.0 organiza as notificações de acordo com a prioridade, visibilidade e categoria.
Categorias de notificação são usadas para filtrar quais notificações podem ser apresentadas quando o dispositivo está sendo *não incomodar* modo.

Para obter informações detalhadas sobre como criar e iniciar as notificações com os recursos mais recentes do Android 5.0, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).


## <a name="new-apis"></a>Novas APIs

Além dos novos recursos de aparência e funcionalidade descritos acima, o Android 5.0 adiciona novas APIs que estendem os recursos de multimídia existente, armazenamento e funcionalidade de conectividade/sem fio. Além disso, o Android 5.0 inclui novas APIs que oferecem suporte para um novo recurso do Agendador de trabalho.

### <a name="camera"></a>Câmera

Android 5.0 oferece várias APIs de novos recursos aprimorados de câmera. O novo `Android.Hardware.Camera2` namespace inclui a funcionalidade para acessar dispositivos de câmera individuais conectados a um dispositivo Android. Além disso, `Android.Hardware.Camera2` modela cada dispositivo de câmera como um pipeline: aceita uma solicitação de captura, captura a imagem e, em seguida, gera o resultado. Essa abordagem torna possível para aplicativos de várias solicitações de captura em um dispositivo de câmera da fila.

As seguintes APIs possibilitam a esses novos recursos:

-   `CameraManager.GetCameraIdList` &ndash; Ajuda você a acessar programaticamente os dispositivos de câmera; Você usa `CameraManager.OpenCamera` para se conectar a um dispositivo de câmera específica.

-   `CameraCaptureSession` &ndash; Captura ou transmite imagens do dispositivo de câmera. Você implementa um `CameraCaptureSession.CaptureListener` interface para lidar com novos eventos de captura de imagem.

-   `CaptureRequest` &ndash; Define parâmetros de captura.

-   `CaptureResult` &ndash; Fornece os resultados de uma operação de captura de imagem.

Para obter mais informações sobre a câmera de nova APIs no Android 5.0, consulte [mídia](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reprodução de áudio

Atualizações do Android 5.0 a `AudioTrack` classe para reprodução de áudio de melhor:

-   `ENCODING_PCM_FLOAT` &ndash; Configura `AudioTrack` para aceitar dados de áudio no formato de ponto flutuante para qualidade mais alta (graças à precisão ampliada), maior capacidade e melhor intervalo dinâmico. Além disso, o formato de ponto flutuante ajuda a evitar o recorte de áudio.

-   `ByteBuffer` &ndash; Agora você pode fornecer dados de áudio para `AudioTrack` como uma matriz de bytes.

-   `WRITE_NON_BLOCKING` &ndash; Essa opção simplifica o armazenamento em buffer e multithreading para alguns aplicativos.

Para obter mais informações sobre `AudioTrack` melhorias no Android 5.0, consulte [mídia](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controle de reprodução de mídia

Android 5.0 introduz a nova `Android.Media.MediaController` classe, que substitui `RemoteControlClient`. `Android.Media.MediaController` Fornece APIs de controle de transporte simplificada e oferece controle de thread-safe de reprodução fora do contexto de interface do usuário. As seguintes novas APIs lidar com o controle de transporte:

-   `Android.Media.Session.MediaSession` &ndash; Uma sessão de controle de mídia que lida com vários controladores. Você chama `MediaSession.GetSessionToken` para solicitar um token que seu aplicativo usa para interagir com a sessão.

-   `MediaController.TransportControls` &ndash; Identificadores de comandos, como transporte **reproduzir**, **interromper**, e **Skip**.

Além disso, você pode usar o novo `Android.App.Notification.MediaStyle` classe a ser associada a uma sessão de mídia com o conteúdo da notificação avançada (como extrair e mostrando a capa do álbum).

Para obter mais informações sobre os novos recursos de controle de reprodução de mídia no Android 5.0, consulte [mídia](http://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Armazenamento

Android 5.0 atualiza a estrutura de acesso de armazenamento para tornar mais fácil para aplicativos para trabalhar com documentos e diretórios:

-   Para selecionar uma subárvore de diretório, você pode criar e enviar um `Android.Intent.Action.OPEN_DOCUMENT_TREE` intenção. Essa tentativa faz com que o sistema exibir todas as instâncias do provedor que suportam a seleção da subárvore; em seguida, o usuário navega e seleciona um diretório.

-   Para criar e gerenciar novos documentos ou pastas em qualquer lugar dentro de uma subárvore, use a nova `CreateDocument`, `RenameDocument`, e `DeleteDocument` métodos de `DocumentsContract`.

-   Para obter os caminhos para diretórios de mídia em todos os dispositivos de armazenamento compartilhado, você chama o novo `Android.Content.Context.GetExternalMediaDirs` método.

Para obter mais informações sobre o armazenamento de novo APIs no Android 5.0, consulte [armazenamento](http://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividade e sem fio

Android 5.0 adiciona os seguintes aprimoramentos de API para conectividade e sem fio:

-   Novos *várias redes* APIs que possibilitam que os aplicativos localizar e selecionar as redes com recursos específicos antes de fazer uma conexão.

-   Funcionalidade de difusão de Bluetooth que permite que um dispositivo Android 5.0 atuar como um periférico do Bluetooth de baixa energia.

-   Aprimoramentos de NFC que tornam mais fácil de usar a funcionalidade de comunicação de curta para compartilhar dados com outros dispositivos.

Para obter mais informações sobre a conectividade APIs no Android 5.0 e sem fio, consulte [conectividade e sem fio](http://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Agendamento de trabalho

Android 5.0 introduz um novo `JobScheduler` API que pode ajudar os usuários minimizar de bateria, agendamento de determinadas tarefas para executar apenas quando o dispositivo está conectado e cobrar. Esse recurso de Agendador de trabalho também pode ser usado para agendar uma tarefa para ser executado quando as condições são mais adequadas para essa tarefa, como baixar um arquivo grande, quando o dispositivo estiver conectado por uma rede Wi-Fi em vez de uma rede limitada.

Para obter mais informações sobre o novo APIs no Android 5.0 de agendamento de trabalho, consulte [agendamento de trabalhos](http://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumo

Este artigo fornece uma visão geral dos novos recursos importantes no Android 5.0 para desenvolvedores de aplicativos xamarin. Android:

-   Tema de material

-   Animations

-   Sombras de modo de exibição e elevação

-   Extração de recursos de cor, como tonalidade desenháveis e proeminente cores

-   O novo `RecyclerView` e `CardView` widgets

-   Aprimoramentos de notificação

-   Novas APIs para a câmera, reprodução de áudio, controle de mídia, armazenamento, conectividade/sem fio e agendamento de trabalho

Se você for novo no desenvolvimento do Xamarin Android, leia [instalação e configuração](~/android/get-started/installation/index.md) para ajudá-lo a começar com o xamarin. Android.
[Hello, Android](~/android/get-started/hello-android/index.md) é uma excelente introdução para aprender a criar projetos do Android.



## <a name="related-links"></a>Links relacionados

- [L Android Developer Preview](http://developer.android.com/preview/index.html)
- [Obtenha o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Design de material](http://developer.android.com/preview/material/index.html)
- [Princípios de Design de material](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
