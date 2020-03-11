---
title: Recursos do Lollipop
description: Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5,0 (pirulito). Esses recursos incluem um novo estilo de interface de usuário chamado tema material, bem como novos recursos de suporte, como animações, exibição de sombras e tonalidades desenháveis. O Android 5,0 também inclui notificações aprimoradas, dois novos widgets de interface do usuário, um novo agendador de trabalhos e uma série de novas APIs para melhorar os recursos de armazenamento, rede, conectividade e multimídia.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 297c7806ce8a880d65c38ef0e4672e41fee5acfe
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291470"
---
# <a name="lollipop-features"></a>Recursos do Lollipop

_Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5,0 (pirulito). Esses recursos incluem um novo estilo de interface de usuário chamado tema material, bem como novos recursos de suporte, como animações, exibição de sombras e tonalidades desenháveis. O Android 5,0 também inclui notificações aprimoradas, dois novos widgets de interface do usuário, um novo agendador de trabalhos e uma série de novas APIs para melhorar os recursos de armazenamento, rede, conectividade e multimídia._

## <a name="lollipop-overview"></a>Visão geral do Lollipop

O Android 5,0 (pirulito) apresenta uma nova linguagem de design, *design de material*e com ti um suporte para a conversão de novos recursos para tornar os aplicativos mais fáceis e intuitivos de usar. Com o design de material, o Android 5,0 não apenas dá aos telefones Android um cara; Ele também fornece um novo conjunto de regras de design para tablets baseados em Android, computadores desktop, inspeções e TVs inteligentes. Essas regras de design enfatizam a simplicidade e o mínimo ao usar atributos tactile familiares (como indicações de superfície e de borda realísticas) para ajudar os usuários a entenderem a interface de forma rápida e intuitiva.

O *tema do material* é o Embodiment desses princípios de design de interface do usuário no Android. Este artigo começa abordando os recursos de suporte do tema do material:

- **Animações** &ndash; animações de *comentários de toque* , animações de *transição de atividade* , animações de transição de *estado de exibição* e um *efeito de revelação*.

- **Exibir sombras e elevação** &ndash; exibições agora têm uma propriedade `elevation`;   exibições com valores `elevation` mais altos convertem sombras maiores no plano de fundo.

- Os **recursos de cores** &ndash; *tonalidades desenháveis* possibilitam que você reutilize os ativos de imagem alterando sua cor e a *extração de cores proeminentes* ajuda você a aplicar temas de forma dinâmica a seu aplicativo com base nas cores de uma imagem.

Muitos recursos de tema de material já estão incorporados à experiência de interface do usuário do Android 5,0, enquanto outros devem ser adicionados explicitamente aos aplicativos. Por exemplo, algumas exibições padrão (como botões) já incluem animações de feedback de toque, enquanto os aplicativos devem habilitar a maioria das sombras da exibição.

Além das melhorias da interface do usuário trazidas por meio do tema do material, o Android 5,0 também inclui vários outros novos recursos que são abordados neste artigo:

- **Notificações aprimoradas** &ndash; notificações no Android 5,0 foram atualizadas significativamente com uma nova aparência, suporte para notificações de tela de bloqueio e um novo formato de apresentação de notificação de *cabeça* .

- **Novos widgets de interface do usuário** &ndash; o novo widget de `RecyclerView` facilitam a transmissão de grandes conjuntos de dados e informações complexas, e o novo widget de `CardView` fornece um formato de apresentação simplificado de cartão para exibir texto e imagens.

- **Novas apis** &ndash; o Android 5,0 adiciona novas APIs para vários suporte de rede, conectividade Bluetooth aprimorada, gerenciamento de armazenamento mais fácil e controle mais flexível de players de multimídia e dispositivos de câmera. Um novo recurso de agendamento de trabalho está disponível para executar tarefas de forma assíncrona em horários agendados. Esse recurso ajuda a melhorar a vida útil da bateria, por exemplo, agendar tarefas para ocorrer quando o dispositivo estiver conectado e carregando.

## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os novos recursos do Android 5,0 em aplicativos baseados no Xamarin:

- O **xamarin. android** &ndash; Xamarin. Android 4,20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

- **SDK do Android** &ndash; Android 5,0 (API 21) ou posterior deve ser instalado por meio do gerenciador de SDK do Android.

- **Java Developer Kit** &ndash; Xamarin. Android requer o [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1,8 também oferece suporte a níveis de API anteriores a 24, incluindo pirulito). A versão de 64 bits do JDK 1,8 será necessária se você estiver usando controles personalizados ou o Visualizador de formulários.

Você pode continuar a usar o [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

## <a name="setting-up-an-android-50-project"></a>Configurando um projeto do Android 5,0

Para criar um projeto do Android 5,0, você deve instalar as ferramentas e os pacotes do SDK mais recentes. Use as etapas a seguir para configurar um projeto Xamarin. Android que tem como alvo o Android 5,0:

1. Instale as ferramentas do Xamarin. Android e ative sua licença do Xamarin. Consulte [instalação e instalação](~/android/get-started/installation/index.md) para obter mais informações sobre como instalar o Xamarin. Android.

2. Se você estiver usando o Visual Studio para Mac, instale as atualizações mais recentes do Android 5,0.

3. Inicie o Gerenciador de SDK do Android (em Visual Studio para Mac, use as **ferramentas &gt; abrir SDK do Android Manager&hellip;** ) e instale o Android SDK Tools 23.0.5 ou posterior:

    [![selecionando Ferramentas de SDK do Android no Gerenciador de SDK do Android](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Além disso, instale os pacotes mais recentes do SDK do Android 5,0 (API 21 ou posterior):

    [![Instalando pacotes do SDK do Android 5,0 no Gerenciador de SDK do Android](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Para obter mais informações sobre como usar o Gerenciador de SDK do Android, consulte [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

4. Crie um novo projeto Xamarin. Android. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Olá, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos do Android. Ao criar um projeto do Android, certifique-se de definir as configurações de versão para Android 5,0.
   Em Visual Studio para Mac, navegue até **Opções de projeto &gt; Build &gt; geral** e defina **estrutura de destino** para **Android 5,0 (pirulito)** ou posterior:

    ![Definindo o destino Framwework para Android 5,0 pirulito](lollipop-images/target-framework.png)

   Em **Opções do projeto &gt; compilar &gt; aplicativo Android**, defina a versão mínima e de destino do Android para **automático-usar a versão da estrutura de destino**:

    ![Definindo as versões mínima e de destino do Android como automática](lollipop-images/minimum-android-version.png)

5. Configure um emulador ou um dispositivo Android para testar seu aplicativo. Se você estiver usando um emulador, consulte [Android Emulator instalação](~/android/get-started/installation/android-emulator/index.md) para saber como configurar um emulador do Android para uso com o Xamarin Studio ou o Visual Studio. Se você estiver usando um dispositivo Android, consulte [Configurando o SDK de visualização](https://developer.android.com/preview/setup-sdk.html) para saber como atualizar seu dispositivo para Android 5,0. Para configurar seu dispositivo Android para executar e depurar aplicativos Xamarin. Android, consulte [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

Observação: se você estiver atualizando um projeto Android existente que estava direcionado para a visualização do Android L, você deverá atualizar a **versão do Android** e a estrutura de **destino** para os valores descritos acima.

## <a name="important-changes"></a>Alterações importantes

Os aplicativos Android publicados anteriormente poderiam ser afetados por alterações no Android 5,0. Em particular, o Android 5,0 usa um novo tempo de execução e um formato de notificação significativamente alterado.

### <a name="android-runtime"></a>Tempo de execução do Android

O Android 5,0 usa o novo tempo de execução do Android (arte) como o tempo de execução padrão em vez de Dalvik. A arte implementa vários recursos novos importantes:

- A **compilação em tempo de vida (AOT)** &ndash; AOT pode melhorar o desempenho do aplicativo compilando o código do aplicativo antes que o aplicativo seja iniciado pela primeira vez. Quando um aplicativo é instalado, a arte gera um executável de aplicativo compilado para o dispositivo de destino.

- A **coleta de lixo (GC) melhorada** &ndash; os aprimoramentos do GC em arte também podem melhorar o desempenho do aplicativo. A coleta de lixo agora usa uma pausa de GC em vez de duas, e operações de GC simultâneas são concluídas de maneira mais oportuna.

- A **melhor depuração de aplicativo** &ndash; arte fornece mais detalhes de diagnóstico para ajudar a analisar exceções e relatórios de falhas.

Os aplicativos existentes devem funcionar sem alteração sob arte &ndash;, exceto para aplicativos que exploram técnicas exclusivas para o tempo de execução Dalvik anterior, o que pode não funcionar em arte. Para obter mais informações sobre essas alterações, consulte [verificando o comportamento do aplicativo no tempo de execução do Android (arte)](https://developer.android.com/guide/practices/verifying-apps-art.html).

### <a name="notification-changes"></a>Alterações de notificação

As notificações foram alteradas significativamente no Android 5,0:

- Os **sons e a vibração são tratados de maneira diferente** &ndash; sons de notificação e vibraçãos agora são manipulados por `Notification.Builder` em vez de `Ringtone`, `MediaPlayer`e `Vibrator`.

- **Novo esquema de cores** &ndash; de acordo com o tema do material, as notificações são renderizadas com texto escuro ao longo de planos de fundo brancos ou muito leves. Além disso, os canais alfa em ícones de notificação podem ser modificados pelo Android para coordenar com esquemas de cores do sistema.

- Notificações de **tela de bloqueio** &ndash; as notificações agora podem aparecer na tela de bloqueio do dispositivo.

- O **heads-up** &ndash; as notificações de alta prioridade agora aparecem em uma pequena janela flutuante (notificação de cabeça para cima) quando o dispositivo está desbloqueado e a tela é ativada.

Na maioria dos casos, portar a funcionalidade de notificação de aplicativo existente para Android 5,0 requer as seguintes etapas:

1. Converta seu código para usar `Notification.Builder` (ou `NotificationsCompat.Builder`) para criar notificações.

2. Verifique se os ativos de notificação existentes estão visíveis no novo esquema de cores do tema do material.

3. Decida qual visibilidade suas notificações devem ter quando elas forem apresentadas na tela de bloqueio. Se uma notificação não for pública, qual conteúdo deve aparecer na tela de bloqueio?

4. Defina a categoria de suas notificações para que elas sejam manipuladas corretamente no novo Android 5,0 no modo *não incomodar* .

Se suas notificações apresentarem controles de transporte, exibir o status de reprodução de mídia, usar `RemoteControlClient`ou chamar `ActivityManager.GetRecentTasks`, consulte [alterações de comportamento importantes](https://developer.android.com/preview/api-overview.html#Behaviors) para obter mais informações sobre como atualizar suas notificações para Android 5,0.

Para obter informações sobre como criar notificações no Android, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Tema de material

O novo tema de material do Android 5,0 traz alterações de varredura para a aparência da interface do usuário do Android. Os elementos visuais agora usam superfícies tactiles que assumem os gráficos em negrito, a tipografia e as cores brilhantes do design baseado em impressão. Exemplos de tema de material são descritos nas seguintes capturas de tela:

[Capturas de tela da tela inicial do tema do material, tela de aplicativos e configuração ![](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

O Android 5,0 o sauda com a tela inicial mostrada à esquerda. A captura de tela do centro é a primeira telas da lista de aplicativos, e a captura de tela à direita é a de **configurações** . A especificação de [design de material](https://material.io/guidelines/material-design/introduction.html) do Google explica as regras de design subjacentes por trás do novo conceito de tema de material.

O tema material inclui três tipos internos que você pode usar em seu aplicativo: o `Theme.Material` tema escuro (o padrão), o tema `Theme.Material.Light` e o tema `Theme.Material.Light.DarkActionBar`:

[![capturas de tela dos temas escuro, claro e DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Para obter mais informações sobre como usar recursos de tema de material em aplicativos Xamarin. Android, consulte o [tema do material](~/android/user-interface/material-theme.md).

## <a name="animations"></a>Animations

O Android 5,0 fornece animações de feedback de toque, animações de transição de atividade e animações de transição de estado de exibição para tornar as interfaces de aplicativo mais intuitivas para uso. Além disso, os aplicativos Android 5,0 podem usar as animações de *efeito de revelar* para ocultar ou revelar exibições. Você pode usar as configurações de *movimento curvo* para configurar a renderização das animações com rapidez ou lentidão.

### <a name="touch-feedback-animations"></a>Animações de comentários de toque

As animações de comentários de toque fornecem aos usuários comentários visuais quando uma exibição é tocada. Por exemplo, agora, os botões exibem um efeito de ondulação quando são tocadas &ndash; esta é a animação de comentários de toque padrão no Android 5,0. A animação do ripple é implementada pela nova classe `RippleDrawable`. O efeito de ondulação pode ser configurado para terminar nos limites da exibição ou se estender além dos limites da exibição. Por exemplo, a seguinte sequência de capturas de tela ilustra o efeito de ondulação em um botão durante a animação de toque:

![Capturas de tela quadro a quadro da animação ondulada em um botão](lollipop-images/touch-animation.png)

O contato de toque inicial com o botão ocorre na primeira imagem à esquerda, enquanto a sequência restante (da esquerda para a direita) ilustra como o efeito de ondulação se espalha para a borda do botão. Quando a animação do Ripple termina, a exibição retorna à sua aparência original. A animação padrão do Ripple ocorre em uma fração de segundo, mas o comprimento da animação pode ser personalizado por um período de tempo maior ou menor.

Para obter mais informações sobre animações de comentários sobre toque no Android 5,0, consulte [Personalizar comentários de toque](https://developer.android.com/training/material/animations.html#Touch).

### <a name="activity-transition-animations"></a>Animações de transição de atividade

As animações de transição de atividade dão aos usuários uma noção de continuidade visual quando uma atividade faz a transição para outra. Os aplicativos podem especificar três tipos de animações de transição:

- **Insira** &ndash; de transição para quando uma atividade entrar na cena.

- **Saia da transição** &ndash; para quando uma atividade sair da cena.

- A **transição de elemento compartilhado** &ndash; para quando uma exibição comum a duas atividades é alterada à medida que a primeira atividade muda para a próxima.

Por exemplo, a seguinte sequência de capturas de tela ilustra uma transição de elemento compartilhado:

[![capturas de tela de quadro por quadro de uma animação de transição de elemento compartilhado](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Um elemento compartilhado (uma foto de um Caterpillar) é uma das várias exibições na primeira atividade; Ele se torna a única exibição na segunda atividade, uma vez que a primeira atividade passa para a segunda.

#### <a name="enter-transition-animation-types"></a>Inserir tipos de animação de transição

Para as transições de entrada, o Android 5,0 fornece três tipos de animações:

- A **animação explodir** &ndash; amplia uma exibição do centro da cena.

- A **animação de Slide** &ndash; move uma exibição de uma das bordas de uma cena.

- A **animação de esmaecimento** &ndash; esmaece uma exibição para a cena.

#### <a name="exit-transition-animation-types"></a>Sair dos tipos de animação de transição

Para transições de saída, o Android 5,0 fornece três tipos de animações:

- A **animação de detalhamento** &ndash; reduz uma exibição para o centro da cena.

- A **animação de Slide** &ndash; move um modo de exibição para uma das bordas de uma cena.

- A **animação de esmaecimento** &ndash; esmaece uma exibição da cena.

#### <a name="shared-element-transition-animation-types"></a>Tipos de animação de transição de elemento compartilhado

As transições de elemento compartilhado dão suporte a vários tipos de animações, como:

- Alterando os limites de layout ou de clipe de uma exibição.

- Alterar a escala e a rotação de uma exibição.

- Alterar o tamanho e o tipo de escala de uma exibição.

Para obter mais informações sobre animações de transição de atividade no Android 5,0, consulte [Personalizar transições de atividade](https://developer.android.com/training/material/animations.html#Transitions).

### <a name="view-state-transition-animations"></a>Exibir animações de transição de estado

O Android 5,0 torna possível que as animações sejam executadas quando o estado de uma exibição é alterado. Você pode animar as transições de estado de exibição usando uma das seguintes técnicas:

- Crie drawables que anime as alterações de estado associadas a uma exibição específica. A nova classe `AnimatedStateListDrawable` permite criar drawables que exibem animações entre as alterações de estado de exibição.

- Defina a funcionalidade de animação que é executada quando o estado de uma exibição é alterado. A nova classe `StateListAnimator` permite que você defina um Animator que é executado quando o estado de uma exibição é alterado.

Para obter mais informações sobre animações de transição de estado no Android 5,0, consulte [animar estado de exibição de alterações](https://developer.android.com/training/material/animations.html#ViewState).

### <a name="reveal-effect"></a>Revelar efeito

O *efeito revelar* é um círculo de recorte que altera o raio para revelar ou ocultar uma exibição. Você pode controlar esse efeito definindo o raio inicial e final do círculo de recorte. A sequência de capturas de tela a seguir ilustra uma animação de revelar efeito do centro do ecrã:

[![capturas de tela de quadro por quadro de revelar animação](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

A próxima sequência ilustra uma animação revelar efeito que ocorre no canto inferior esquerdo da tela:

[![capturas de tela do quadro por quadro da animação de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Revelar animações pode ser revertida; ou seja, o círculo de recorte pode ser reduzido para ocultar a exibição em vez de aumentar para revelar a exibição.

Para obter mais informações sobre o efeito de revelações do Android 5,0 no, consulte [usar o efeito revelar](https://developer.android.com/training/material/animations.html#Reveal).

### <a name="curved-motion"></a>Movimento curvo

Além desses recursos de animação, o Android 5,0 também fornece novas APIs que permitem que você especifique as curvas de tempo e movimento das animações. O Android 5,0 usa essas curvas para interpolar a movimentação temporal e espacial durante animações. Três curvas são definidas no Android 5,0:

- A **\_rápida\_\_linear no** &ndash; acelera rapidamente e continua acelerando até o final da animação.

- O **\_rápido\_o\_lento no** &ndash; acelera rapidamente e lentamente desaceleram o final da animação.

- As **\_lineares\_lentas\_no** &ndash; começam com uma velocidade de pico e são desaceleradas lentamente até o final da animação.

Você pode usar a nova classe `PathInterpolator` para especificar como ocorre a interpolação de movimento. `PathInterpolator` é um interpolador que percorre os caminhos de animação de acordo com os pontos de controle especificados e as curvas de movimento. Para obter mais informações sobre como especificar configurações de movimento curvo no Android 5,0, consulte [usar o movimento curvo](https://developer.android.com/training/material/animations.html#CurvedMotion).

## <a name="view-shadows--elevation"></a>Exibir sombras & elevação

No Android 5,0, você pode especificar a *elevação* de uma exibição definindo uma nova propriedade `Z`. Um valor maior `Z` faz com que a exibição Converta uma sombra maior em segundo plano, fazendo com que a exibição pareça flutuar para cima acima do plano de fundo. Você pode definir a elevação inicial de um modo de exibição configurando seu atributo `elevation` no layout.

O exemplo a seguir ilustra a conversão de sombras por um controle de `TextView` vazio quando seu atributo de elevação é definido como 2DP, 4DP e 6DP, respectivamente:

[![capturas de tela de sombras de exibição maiores do progessively](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

Exibir configurações de sombra pode ser estático (como mostrado acima) ou pode ser usado em animações para fazer com que uma exibição pareça ficar temporariamente acima do plano de fundo da exibição. Você pode usar a classe `ViewPropertyAnimator` para animar a elevação de uma exibição. A elevação de uma exibição é a soma de seu layout `elevation` configuração mais uma propriedade `translationZ` que pode ser definida por meio de uma chamada de método `ViewPropertyAnimator`.

Para saber mais sobre como exibir sombras no Android 5,0, confira [definição de sombras e exibições de recorte](https://developer.android.com/training/material/shadows-clipping.html).

## <a name="color-features"></a>Recursos de cores

O Android 5,0 fornece dois novos recursos para o gerenciamento de cores em aplicativos:

- A *tonalidade desenhável* permite que você altere as cores dos ativos de imagem alterando um atributo de layout.

- A *extração de cores proeminentes* torna possível personalizar dinamicamente o tema de cores do aplicativo para coordenar com a paleta de cores de uma imagem exibida.

### <a name="drawable-tinting"></a>Tonalidade de desenho

Os layouts do Android 5,0 reconhecem um novo atributo `tint` que você pode usar para definir a cor de drawables sem precisar criar várias versões desses ativos para exibir cores diferentes. Para usar esse recurso, você define um bitmap como uma máscara alfa e usa o atributo `tint` para definir a cor do ativo. Isso possibilita que você crie ativos uma vez e os colore em seu layout para corresponder ao seu tema.

No exemplo a seguir, um único ativo de imagem &ndash; um logotipo branco com um plano de fundo transparente &ndash; é usado para criar variações de tonalidade:

![Logotipo do Xamarin branco com plano de fundo transparente](lollipop-images/xamarin-logo-white.png)

Esse logotipo é exibido acima de um plano de fundo circular azul, conforme mostrado nos exemplos a seguir. A imagem à esquerda é como o logotipo aparece sem uma configuração de `tint`. Na imagem do centro, o atributo de `tint` do logotipo é definido como um cinza escuro. Na imagem à direita, `tint` está definido como cinza claro:

![Exemplos do logotipo acima com configurações de tonalidade diferentes](lollipop-images/drawable-tinting.png)

Para obter mais informações sobre tonalidades desenháveis no Android 5,0, consulte [tonalidades desenháveis](https://developer.android.com/training/material/drawables.html#DrawableTint).

### <a name="prominent-color-extraction"></a>Extração de cores proeminentes

A nova classe `Palette` do Android 5,0 permite extrair cores de uma imagem para que você possa aplicá-las dinamicamente a uma paleta de cores personalizada. A classe `Palette` extrai seis cores de uma imagem e rotula essas cores de acordo com seus níveis relativos de saturação de cor e brilho:

- Vibrante

- Escuro vibrante

- Luz vibrante

- Mudo

- Sem som escuro

- Luz sem som

Por exemplo, nas capturas de tela a seguir, um aplicativo de exibição de fotos extrai as cores proeminentes da imagem na exibição e usa essas cores para adaptar o esquema de cores do aplicativo para corresponder à imagem:

[![capturas de tela de extrações de cor de tema verde, rosa e azul](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Nas capturas de tela acima, a barra de ação é definida como a cor "luz vibrante" extraída e o plano de fundo é definido como a cor "vibrante escuro" extraída. Em cada exemplo acima, uma linha de pequenos quadrados de cor é incluída para ilustrar as cores da paleta que foram extraídas da imagem.

Para obter mais informações sobre extração de cores no Android 5,0, consulte [extraindo cores proeminentes de uma imagem](https://developer.android.com/training/material/drawables.html#ColorExtract).

## <a name="new-ui-widgets"></a>Novos widgets de interface do usuário

O Android 5,0 apresenta dois novos widgets de interface do usuário:

- `RecyclerView` &ndash; um grupo de exibição que exibe uma lista de itens roláveis.

- `CardView` &ndash; um layout básico com cantos arredondados.

Ambos os widgets incluem suporte inclusas para recursos de tema de material; por exemplo, `RecyclerView` usa animações para adicionar e remover exibições e `CardView` usa sombras de exibição para fazer com que cada cartão pareça flutuar acima do plano de fundo. Exemplos desses novos widgets são mostrados nas seguintes capturas de tela:

[![capturas de tela de aplicativos criados com o RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

A captura de tela à esquerda é um exemplo de `RecyclerView` conforme usado em um aplicativo de email, e a captura de tela à direita é um exemplo de `CardView` como usado em um aplicativo de reserva de viagem.

### <a name="recyclerview"></a>RecyclerView

`RecyclerView` é semelhante a `ListView,`, mas é mais adequada para grandes conjuntos de exibições ou listas com elementos que são alterados dinamicamente. Assim como `ListView,` você especifica um adaptador para acessar o conjunto de dados subjacente. No entanto, ao contrário `ListView,` você usa um *Gerenciador de layout* para posicionar itens dentro de `RecyclerView`. O Gerenciador de layout também cuida da reciclagem da exibição; Ele gerencia a reutilização de exibições de item que não são mais visíveis para o usuário.

Ao usar um widget de `RecyclerView`, você deve especificar um `LayoutManager` e um adaptador. Conforme mostrado nesta figura, `LayoutManager` é o intermediário entre o adaptador e o `RecyclerView`:

![Diagrama de RecyclerView com LayoutManager, adaptador e conjunto de banco de suporte](lollipop-images/recyclerview-diagram.png)

As capturas de tela a seguir ilustram uma `RecyclerView` que contém 100 itens (cada item consiste em um `ImageView` e um `TextView`):

[![capturas de tela de um aplicativo RecyclerView rolando pelas imagens](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` lida com esse grande conjunto de dados com facilidade &ndash; rolar do início da lista até o fim da lista neste aplicativo de exemplo leva apenas alguns segundos. `RecyclerView` também dá suporte a animações; na verdade, as animações para adicionar e remover itens são habilitadas por padrão. Quando um item é adicionado a um `RecyclerView`, ele desaparece como mostrado nesta sequência de capturas de tela:

[![captura de tela por quadro de um item de foto esmaecido](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Para obter mais informações sobre `RecyclerView`, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

### <a name="cardview"></a>CardView

`CardView` é uma exibição simples que simula um cartão flutuante com cantos arredondados. Como `CardView` tem sombras de exibição internas, ele fornece uma maneira fácil de adicionar profundidade visual ao seu aplicativo. As capturas de tela a seguir mostram três exemplos orientados a texto de `CardView`:

[![capturas de tela de exemplo de aplicativos usando o RecyclerView com itens baseados em CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Cada um dos cartões no exemplo acima contém um `TextView`; a cor do plano de fundo é definida por meio do atributo `cardBackgroundColor`.

Para obter mais informações sobre `CardView`, consulte [CardView](~/android/user-interface/controls/card-view.md).

## <a name="enhanced-notifications"></a>Notificações aprimoradas

O sistema de notificação no Android 5,0 foi atualizado significativamente com um novo formato visual e novos recursos. As notificações têm uma nova aparência no Android 5,0. Por exemplo, as notificações no Android 5,0 agora usam texto escuro em um plano de fundo claro:

![Exemplo de uma notificação não expandida do Android 5,0](lollipop-images/expanded-notification-contracted.png)

Quando um ícone grande é exibido em uma notificação (como mostrado no exemplo acima), o Android 5,0 apresenta o ícone pequeno como um selo sobre o ícone grande.

No Android 5,0, as notificações também podem aparecer na tela de bloqueio do dispositivo.
Por exemplo, aqui está uma captura de tela de exemplo de uma tela de bloqueio com uma única notificação:

[Captura de tela de ![de notificação que aparece no ecrã de bloqueio](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Os usuários podem tocar duas vezes em uma notificação na tela de bloqueio para desbloquear o dispositivo e ir até o aplicativo que originou essa notificação ou passar o dedo para ignorar a notificação. As notificações têm uma nova configuração de *visibilidade* que determina a quantidade de conteúdo que pode ser exibida na tela de bloqueio. Os usuários podem escolher se deseja permitir que conteúdo confidencial seja mostrado em notificações de tela de bloqueio.

O Android 5,0 apresenta um novo formato de apresentação de notificação de alta prioridade chamado *heads-up*. As notificações de cabeça para cima deslizam para baixo na parte superior da tela por alguns segundos e revertem para a sombra da notificação na parte superior da tela. As notificações de cabeçotes possibilitam que a interface do usuário do sistema Coloque informações importantes na frente de usuários sem interromper a atividade em execução no momento.
O exemplo a seguir ilustra uma notificação simples que é exibida na parte superior de um aplicativo:

[![exemplo de uma notificação de cabeça para cima](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

As notificações de cabeçotes são normalmente usadas para os seguintes eventos:

- Uma nova próxima mensagem

- Uma chamada telefônica de entrada

- Indicação de bateria fraca

- Um alarme

O Android 5,0 exibe uma notificação no formato de cabeçotes somente quando tem uma configuração de prioridade alta ou máxima.

No Android 5,0, você pode fornecer metadados de notificação para ajudar o Android a classificar e exibir notificações de forma mais inteligente. O Android 5,0 organiza as notificações de acordo com a prioridade, a visibilidade e a categoria.
As categorias de notificação são usadas para filtrar quais notificações podem ser apresentadas quando o dispositivo está no modo *não incomodar* .

Para obter informações detalhadas sobre como criar e iniciar notificações com os recursos mais recentes do Android 5,0, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="new-apis"></a>Novas APIs

Além dos novos recursos de aparência descritos acima, o Android 5,0 adiciona novas APIs que estendem os recursos de multimídia, armazenamento e funcionalidade de conectividade sem fio/rede existentes. Além disso, o Android 5,0 inclui novas APIs que dão suporte a um novo recurso do Agendador de trabalhos.

### <a name="camera"></a>Câmera

O Android 5,0 fornece várias novas APIs para recursos de câmera aprimorados. O novo namespace `Android.Hardware.Camera2` inclui a funcionalidade para acessar dispositivos individuais de câmera conectados a um dispositivo Android. Além disso, `Android.Hardware.Camera2` modela cada dispositivo de câmera como um pipeline: ele aceita uma solicitação de captura, captura a imagem e, em seguida, gera o resultado. Essa abordagem possibilita que os aplicativos enfileiram várias solicitações de captura para um dispositivo de câmera.

As APIs a seguir tornam esses novos recursos possíveis:

- `CameraManager.GetCameraIdList` &ndash; ajuda a acessar dispositivos de câmera programaticamente; Você usa `CameraManager.OpenCamera` para se conectar a um dispositivo de câmera específico.

- `CameraCaptureSession` &ndash; captura ou transmite imagens do dispositivo de câmera. Você implementa uma interface `CameraCaptureSession.CaptureListener` para lidar com novos eventos de captura de imagem.

- `CaptureRequest` &ndash; define os parâmetros de captura.

- `CaptureResult` &ndash; fornece os resultados de uma operação de captura de imagem.

Para obter mais informações sobre as novas APIs de câmera no Android 5,0, consulte [mídia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reprodução de áudio

O Android 5,0 atualiza a classe `AudioTrack` para uma reprodução de áudio melhor:

- `ENCODING_PCM_FLOAT` &ndash; configura o `AudioTrack` para aceitar dados de áudio em formato de ponto flutuante para um melhor intervalo dinâmico, maior reserva e maior qualidade (graças à maior precisão). Além disso, o formato de ponto flutuante ajuda a evitar o recorte de áudio.

- `ByteBuffer` &ndash; agora você pode fornecer dados de áudio para `AudioTrack` como uma matriz de bytes.

- `WRITE_NON_BLOCKING` &ndash; essa opção simplifica o armazenamento em buffer e multithread para alguns aplicativos.

Para obter mais informações sobre os aprimoramentos de `AudioTrack` no Android 5,0, consulte [mídia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controle de reprodução de mídia

O Android 5,0 apresenta a nova classe `Android.Media.MediaController`, que substitui `RemoteControlClient`. `Android.Media.MediaController` fornece APIs de controle de transporte simplificadas e oferece controle de reprodução de thread seguro fora do contexto da interface do usuário. As novas APIs a seguir tratam do controle de transporte:

- `Android.Media.Session.MediaSession` &ndash; uma sessão de controle de mídia que lida com vários controladores. Você chama `MediaSession.GetSessionToken` para solicitar um token que seu aplicativo usa para interagir com a sessão.

- `MediaController.TransportControls` &ndash; lida com comandos de transporte, como **reproduzir**, **parar**e **ignorar**.

Além disso, você pode usar a nova classe `Android.App.Notification.MediaStyle` para associar uma sessão de mídia com conteúdo de notificação avançado (como extração e exibição de arte do álbum).

Para obter mais informações sobre os novos recursos de controle de reprodução de mídia no Android 5,0, consulte [mídia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Armazenamento

O Android 5,0 atualiza a estrutura de acesso de armazenamento para tornar mais fácil para os aplicativos trabalhar com diretórios e documentos:

- Para selecionar uma subárvore do diretório, você pode criar e enviar uma intenção de `Android.Intent.Action.OPEN_DOCUMENT_TREE`. Essa tentativa faz com que o sistema exiba todas as instâncias de provedor que dão suporte à seleção de subárvore; em seguida, o usuário navega e seleciona um diretório.

- Para criar e gerenciar novos documentos ou diretórios em qualquer lugar em uma subárvore, você usa os métodos novos `CreateDocument`, `RenameDocument`e `DeleteDocument` de `DocumentsContract`.

- Para obter caminhos para diretórios de mídia em todos os dispositivos de armazenamento compartilhado, você chama o novo método `Android.Content.Context.GetExternalMediaDirs`.

Para obter mais informações sobre novas APIs de armazenamento no Android 5,0, consulte [armazenamento](https://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividade de & sem fio

O Android 5,0 adiciona os seguintes aprimoramentos de API para sem fio e conectividade:

- Novas APIs de *várias redes* que possibilitam que os aplicativos encontrem e selecionem redes com recursos específicos antes de estabelecer uma conexão.

- A funcionalidade de difusão Bluetooth que permite que um dispositivo Android 5,0 atue como um periférico Bluetooth de baixa energia.

- Aprimoramentos de NFC que facilitam o uso da funcionalidade de comunicações de campo Near para o compartilhamento de dados com outros dispositivos.

Para saber mais sobre as novas APIs de conectividade e sem fio no Android 5,0, consulte [sem fio e conectividade](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Agendamento de trabalho

O Android 5,0 apresenta uma nova API de `JobScheduler` que pode ajudar os usuários a minimizar a bateria drenando agendando determinadas tarefas para serem executadas somente quando o dispositivo estiver conectado e carregando. Esse recurso do Agendador de trabalhos também pode ser usado para agendar uma tarefa a ser executada quando as condições são mais adequadas para essa tarefa, como baixar um arquivo grande quando o dispositivo está conectado por uma rede Wi-Fi em vez de uma rede limitada.

Para obter mais informações sobre as novas APIs de agendamento de trabalho no Android 5,0, consulte [agendando trabalhos](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral dos novos recursos importantes no Android 5,0 para desenvolvedores de aplicativos do Xamarin. Android:

- Tema de material

- Animations

- Exibir sombras e elevação

- Recursos de cores, como tinta desenhável e extração de cores proeminentes

- Os novos `RecyclerView` e `CardView` widgets

- Aprimoramentos de notificação

- Novas APIs para câmera, reprodução de áudio, controle de mídia, armazenamento, sem fio/conectividade e agendamento de trabalho

Se você for novo no Xamarin Android Development, leia [instalação e instalação](~/android/get-started/installation/index.md) para ajudá-lo a começar a usar o Xamarin. Android.
[Olá, o Android](~/android/get-started/hello-android/index.md) é uma introdução excelente para aprender a criar projetos Android.

## <a name="related-links"></a>Links relacionados

- [Visualização do desenvolvedor do Android L](https://developer.android.com/preview/index.html)
- [Obter o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Design de material](https://developer.android.com/preview/material/index.html)
