---
title: Características do Pirulito
description: Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5.0 (Lollipop). Esses recursos incluem um novo estilo de interface de usuário chamado Material Theme, bem como novos recursos de suporte, como animações, sombras de exibição e tom desenhado. O Android 5.0 também inclui notificações aprimoradas, dois novos widgets de iu de ida e volta, um novo agendador de trabalho e um punhado de novas APIs para melhorar os recursos de armazenamento, rede, conectividade e multimídia.
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 297c7806ce8a880d65c38ef0e4672e41fee5acfe
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291470"
---
# <a name="lollipop-features"></a>Características do Pirulito

_Este artigo fornece uma visão geral de alto nível dos novos recursos introduzidos no Android 5.0 (Lollipop). Esses recursos incluem um novo estilo de interface de usuário chamado Material Theme, bem como novos recursos de suporte, como animações, sombras de exibição e tom desenhado. O Android 5.0 também inclui notificações aprimoradas, dois novos widgets de iu de ida e volta, um novo agendador de trabalho e um punhado de novas APIs para melhorar os recursos de armazenamento, rede, conectividade e multimídia._

## <a name="lollipop-overview"></a>Visão geral do pirulito

O Android 5.0 (Lollipop) introduz uma nova linguagem de design, *Material Design*, e com ele um elenco de suporte de novos recursos para tornar os aplicativos mais fáceis e intuitivos de usar. Com o Material Design, o Android 5.0 não só dá aos celulares Android um facelift; ele também fornece um novo conjunto de regras de design para tablets baseados no Android, computadores desktop, relógios e smart TVs. Essas regras de design enfatizam a simplicidade e o minimalismo, ao mesmo tempo em que fazem uso de atributos táteis familiares (como pistas realistas de superfície e borda) para ajudar os usuários a entender rapidamente e intuitivamente a interface.

*Material Tema* é a personificação desses princípios de design de IU no Android. Este artigo começa cobrindo as características de suporte do Material Theme:

- **Animações** &ndash; Toque animações *de feedback,* animações de *transição de atividade,* visualize animações *de transição de estado* e um efeito *revelador*.

- **Ver sombras e elevação** &ndash; `elevation` As vistas agora têm uma propriedade;   vistas com `elevation` valores mais altos lançam sombras maiores no fundo.

- **Recursos de cor** &ndash; *A coloração* permite que você reutilize os ativos de imagem alterando sua cor, e *a extração de cores proeminenteajuda* você dinamicamente ao seu aplicativo com base em cores em uma imagem.

Muitos recursos do Material Theme já estão incorporados à experiência de Interface do Usuário do Android 5.0, enquanto outros devem ser explicitamente adicionados aos aplicativos. Por exemplo, algumas visualizações padrão (como botões) já incluem animações de feedback de toque, enquanto os aplicativos devem habilitar a maioria das sombras de visualização.

Além das melhorias na IU trazidas através do Material Theme, o Android 5.0 também inclui várias outras novidades que são abordadas neste artigo:

- **Notificações aprimoradas** &ndash; As notificações no Android 5.0 foram significativamente atualizadas com um novo visual, suporte para notificações de tela de bloqueio e um novo formato de apresentação de notificação *do Heads-up.*

- **Novos widgets de interface do reino** &ndash; O novo `RecyclerView` widget facilita que os `CardView` aplicativos transmitam grandes conjuntos de dados e informações complexas, e o novo widget fornece um formato de apresentação simplificado em forma de cartão para exibir texto e imagens.

- **Novas APIs** &ndash; O Android 5.0 adiciona novas APIs para suporte a várias redes, conectividade Bluetooth melhorada, gerenciamento de armazenamento mais fácil e controle mais flexível de jogadores multimídia e dispositivos de câmera. Um novo recurso de agendamento de trabalho está disponível para executar tarefas de forma assíncrona nos horários agendados. Esse recurso ajuda a melhorar a vida útil da bateria, por exemplo, agendando tarefas para ocorrer quando o dispositivo estiver conectado e carregando.

## <a name="requirements"></a>Requisitos

A seguir, é necessário usar os novos recursos do Android 5.0 em aplicativos baseados em Xamarin:

- **Xamarin.Android** &ndash; Xamarin.Android 4.20 ou posterior deve ser instalado e configurado com visual studio ou visual studio para Mac.

- **Android SDK** &ndash; Android 5.0 (API 21) ou posterior deve ser instalado através do Android SDK Manager.

- **Java Developer Kit** &ndash; Xamarin.Android requer [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para API nível 24 ou superior (JDK 1.8 também suporta níveis de API antes de 24, incluindo Lollipop). A versão de 64 bits do JDK 1.8 é necessária se você estiver usando controles personalizados ou o Visualizador de Formulários.

Você pode continuar a usar [o JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível 23 da API ou anterior.

## <a name="setting-up-an-android-50-project"></a>Configuração de um Projeto Android 5.0

Para criar um projeto Android 5.0, você deve instalar as ferramentas mais recentes e pacotes SDK. Use as seguintes etapas para configurar um projeto Xamarin.Android que tem como alvo o Android 5.0:

1. Instale as ferramentas Xamarin.Android e ative sua licença Xamarin. Consulte [Configuração e Instalação](~/android/get-started/installation/index.md) para obter mais informações sobre como instalar o Xamarin.Android.

2. Se você estiver usando o Visual Studio para Mac, instale as atualizações mais recentes do Android 5.0.

3. Inicie o Android SDK Manager (no Visual Studio para Mac, use **ferramentas &gt; abra o Android SDK&hellip;Manager)** e instale o Android SDK Tools 23.0.5 ou posterior:

    [![Selecionando ferramentas Android SDK no Android SDK Manager](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   Além disso, instale os pacotes Mais recentes do Android 5.0 SDK (API 21 ou posterior):

    [![Instalando pacotes Android 5.0 SDK no Android SDK Manager](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   Para obter mais informações sobre como usar o Android SDK Manager, consulte [SDK Manager](https://developer.android.com/tools/help/sdk-manager.html).

4. Crie um novo projeto Xamarin.Android. Se você é novo no desenvolvimento do Android com xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre a criação de projetos Android. Ao criar um projeto Android, certifique-se de configurar as configurações da versão para o Android 5.0.
   No Visual Studio para Mac, navegue até **project options &gt; build &gt; general** e defina a estrutura de **destino** para o Android **5.0 (Lollipop)** ou posterior:

    ![Configurando o Target Framwework para Android 5.0 Lollipop](lollipop-images/target-framework.png)

   Em **Project &gt; &gt; Options Build Android Application**, definir versão mínima e alvo do Android para automático - use a versão do framework de **destino**:

    ![Definindo as versões Mínima e Alvo do Android como Automática](lollipop-images/minimum-android-version.png)

5. Configure um emulador ou um dispositivo Android para testar seu aplicativo. Se você estiver usando um emulador, consulte [a Configuração do Emulador Android](~/android/get-started/installation/android-emulator/index.md) para aprender como configurar um emulador Android para uso com o Xamarin Studio ou o Visual Studio. Se você estiver usando um dispositivo Android, consulte [Configurando o SDK de visualização](https://developer.android.com/preview/setup-sdk.html) para saber como atualizar seu dispositivo para android 5.0. Para configurar seu dispositivo Android para executar e depurar aplicativos Xamarin.Android, consulte [Configurar dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

Nota: Se você está atualizando um projeto Android existente que tinha como alvo o Android L Preview, você deve atualizar a versão **Target Framework** e **Android** para os valores descritos acima.

## <a name="important-changes"></a>Mudanças importantes

Aplicativos para Android publicados anteriormente podem ser afetados por mudanças no Android 5.0. Em particular, o Android 5.0 usa um novo tempo de execução e um formato de notificação significativamente alterado.

### <a name="android-runtime"></a>Android Runtime

O Android 5.0 usa o novo Android Runtime (ART) como o tempo de execução padrão em vez de Dalvik. A ART implementa várias novidades principais:

- **Compilação antecipada (AOT)** &ndash; A AOT pode melhorar o desempenho do aplicativo compilando o código do aplicativo antes do aplicativo ser lançado pela primeira vez. Quando um aplicativo é instalado, o ART gera um aplicativo compilado executável para o dispositivo de destino.

- **Melhorias na coleta de lixo (GC)** &ndash; GC em ART também podem melhorar o desempenho do aplicativo. A coleta de lixo agora usa uma pausa GC em vez de duas, e as operações de GC simultâneas completam de forma mais oportuna.

- **A atualização da depuração** &ndash; de aplicativos ART fornece mais detalhes de diagnóstico para ajudar na análise de exceções e relatórios de falhas.

Os aplicativos existentes devem funcionar &ndash; sem alterações em ART, exceto para aplicativos que exploram técnicas exclusivas do tempo de execução anterior de Dalvik, que podem não funcionar sob ART. Para obter mais informações sobre essas alterações, consulte [Verificar o comportamento do aplicativo no Android Runtime (ART)](https://developer.android.com/guide/practices/verifying-apps-art.html).

### <a name="notification-changes"></a>Alterações de notificação

As notificações mudaram significativamente no Android 5.0:

- **Sons e vibrações são manuseados de forma** &ndash; `Notification.Builder` diferente `Ringtone`Os `MediaPlayer`sons `Vibrator`de notificação e as vibrações são agora manuseados por , em vez de , e .

- **Novo esquema de** &ndash; cores De acordo com o Material Theme, as notificações são renderizadas com texto escuro sobre fundos brancos ou muito claros. Além disso, os canais alfa nos ícones de notificação podem ser modificados pelo Android para coordenar com esquemas de cores do sistema.

- **Notificações** &ndash; de tela bloqueada As notificações agora podem aparecer na tela de bloqueio do dispositivo.

- **As** &ndash; notificações de alta prioridade agora aparecem em uma pequena janela flutuante (notificação de aviso prévio) quando o dispositivo é desbloqueado e a tela é ligada.

Na maioria dos casos, a portação da funcionalidade de notificação de aplicativos existente para o Android 5.0 requer as seguintes etapas:

1. Converta seu `Notification.Builder` código `NotificationsCompat.Builder`para usar (ou ) para criar notificações.

2. Verifique se seus ativos de notificação existentes são visualizados no novo esquema de cores Material Theme.

3. Decida qual a visibilidade que suas notificações devem ter quando são apresentadas na tela de bloqueio. Se uma notificação não for pública, que conteúdo deve aparecer na tela de bloqueio?

4. Defina a categoria de suas notificações para que sejam tratadas corretamente no novo Android 5.0 *Não perturbe* o modo.

Se suas notificações apresentarem controles de transporte, exibir o status de reprodução de mídia, usar `RemoteControlClient`ou ligar, `ActivityManager.GetRecentTasks`consulte [Mudanças de comportamento importantes](https://developer.android.com/preview/api-overview.html#Behaviors) para obter mais informações sobre como atualizar suas notificações para o Android 5.0.

Para obter informações sobre como criar notificações no Android, consulte [Notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="material-theme"></a>Tema de material

O novo Tema material do Android 5.0 traz mudanças radicais na aparência da UI Android. Os elementos visuais agora usam superfícies táteis que assumem os gráficos ousados, a tipografia e as cores brilhantes do design baseado em impressão. Exemplos de Tema material são retratados nas seguintes capturas de tela:

[![Capturas de tela inicial do tema do material, tela de aplicativos e tela de configuração](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

O Android 5.0 cumprimenta você com a tela inicial mostrada à esquerda. A captura de tela central é a primeira tela da lista de aplicativos, e a captura de tela à direita é a tela **Configurações.** A especificação de Design de [Materiais](https://material.io/guidelines/material-design/introduction.html) do Google explica as regras de design subjacentes por trás do novo conceito de Material Tema.

O Tema material inclui três sabores incorporados que você `Theme.Material` pode usar em seu `Theme.Material.Light` aplicativo: o `Theme.Material.Light.DarkActionBar` tema escuro (o padrão), o tema e o tema:

[![Capturas de tela dos temas Dark, Light e DarkActionBar](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

Para obter mais informações sobre como usar os recursos do Material Tema em aplicativos Xamarin.Android, consulte [Material Tema](~/android/user-interface/material-theme.md).

## <a name="animations"></a>Animations

O Android 5.0 fornece animações de feedback por toque, animações de transição de atividade e exibir animações de transição de estado para tornar as interfaces de aplicativos mais intuitivas de usar. Além disso, os aplicativos do Android 5.0 podem usar animações *de efeito revelador* para ocultar ou revelar visualizações. Você pode usar configurações *de movimento curvas* para configurar a rapidez ou lentamente das animações.

### <a name="touch-feedback-animations"></a>Animações de feedback de toque

As animações de feedback de toque fornecem aos usuários feedback visual quando uma exibição foi tocada. Por exemplo, os botões agora exibem &ndash; um efeito de ondulação quando são tocados esta é a animação padrão de feedback de toque no Android 5.0. A animação ripple é `RippleDrawable` implementada pela nova classe. O efeito de ondulação pode ser configurado para terminar nos limites da exibição ou estender-se além dos limites da exibição. Por exemplo, a seguinte seqüência de capturas de tela ilustra o efeito de ondulação em um botão durante a animação de toque:

![Capturas de tela quadro a quadro de animação de ondulação em um botão](lollipop-images/touch-animation.png)

O contato inicial do toque com o botão ocorre na primeira imagem à esquerda, enquanto a seqüência restante (da esquerda para a direita) ilustra como o efeito de ondulação se espalha para a borda do botão. Quando a animação de ondulação termina, a exibição retorna à sua aparência original. A animação de ondulação padrão ocorre em uma fração de segundo, mas o comprimento da animação pode ser personalizado por longos ou menores períodos de tempo.

Para obter mais animações sobre feedback de toque no Android 5.0, consulte [Personalizar feedback de toque](https://developer.android.com/training/material/animations.html#Touch).

### <a name="activity-transition-animations"></a>Animações de transição de atividade

As animações de transição de atividade dão aos usuários uma sensação de continuidade visual quando uma atividade passa para outra. Os aplicativos podem especificar três tipos de animações de transição:

- **Insira transição** &ndash; Para quando uma atividade entra em cena.

- **Transição de saída** &ndash; Para quando uma atividade sai da cena.

- **Transição** &ndash; de elemento compartilhado Para quando uma visão comum a duas atividades muda à medida que a primeira atividade passa para a próxima.

Por exemplo, a seguinte seqüência de capturas de tela ilustra uma transição de elemento compartilhado:

[![Capturas de tela quadro a quadro de uma animação de transição de elemento compartilhado](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

Um elemento compartilhado (uma foto de uma lagarta) é uma das várias vistas da primeira atividade; amplia-se para se tornar a única visão na segunda atividade à medida que a primeira atividade passa para a segunda.

#### <a name="enter-transition-animation-types"></a>Digite tipos de animação de transição

Para entrar em transições, o Android 5.0 fornece três tipos de animações:

- **Animação explodir** &ndash; amplia uma visão do centro da cena.

- **Animação de slides** &ndash; Move uma vista de uma das bordas de uma cena.

- **A animação** &ndash; fade desaparece uma visão da cena.

#### <a name="exit-transition-animation-types"></a>Tipos de animação de transição de saída

Para transições de saída, o Android 5.0 fornece três tipos de animações:

- **Animação explodir** &ndash; encolhe uma visão para o centro da cena.

- **Animação de slides** &ndash; Move uma vista para uma das bordas de uma cena.

- **A animação** &ndash; fade desaparece uma visão fora da cena.

#### <a name="shared-element-transition-animation-types"></a>Tipos de animação de transição de elementos compartilhados

Transições de elementos compartilhados suportam vários tipos de animações, tais como:

- Alterando os limites de layout ou clipe de uma exibição.

- Mudando a escala e a rotação de uma vista.

- Alterando o tamanho e o tipo de escala para uma exibição.

Para obter mais sobre animações de transição de atividade no Android 5.0, consulte [Personalizar transições de atividade](https://developer.android.com/training/material/animations.html#Transitions).

### <a name="view-state-transition-animations"></a>Exibir animações de transição de estado

O Android 5.0 torna possível que as animações funcionem quando o estado de uma exibição mudar. Você pode animar as transições de estado de exibição usando uma das seguintes técnicas:

- Crie desenheáveis que animam as mudanças de estado associadas a uma determinada exibição. A `AnimatedStateListDrawable` nova classe permite criar desenheáveis que exibem animações entre as alterações do estado de exibição.

- Defina a funcionalidade de animação que é executada quando o estado de uma exibição muda. A `StateListAnimator` nova classe permite definir um animador que é executado quando o estado de uma exibição muda.

Para obter mais informações sobre as animações de transição de estado de visualização no Android 5.0, consulte [Animate View State Changes](https://developer.android.com/training/material/animations.html#ViewState).

### <a name="reveal-effect"></a>Efeito revelação

O *efeito de revelação* é um círculo de recorte que muda o raio para revelar ou ocultar uma visão. Você pode controlar este efeito definindo o raio inicial e final do círculo de recorte. A seguinte seqüência de capturas de tela ilustra uma animação de efeito revelador a partir do centro da tela:

[![Capturas de tela quadro a quadro de animação reveladora](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

A seqüência seguinte ilustra uma animação de efeito revelador que ocorre a partir do canto inferior esquerdo da tela:

[![Capturas de tela quadro a quadro de animação de recorte](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

Revelar animações podem ser revertidas; ou seja, o círculo de recorte pode encolher para ocultar a vista em vez de ampliar para revelar a visão.

Para obter mais informações sobre o efeito de revelação do Android 5.0, consulte [Use the Reveal Effect](https://developer.android.com/training/material/animations.html#Reveal).

### <a name="curved-motion"></a>Movimento Curvo

Além desses recursos de animação, o Android 5.0 também fornece novas APIs que permitem especificar as curvas de tempo e movimento das animações. O Android 5.0 usa essas curvas para interpolar o movimento temporal e espacial durante as animações. Três curvas são definidas no Android 5.0:

- **Rápido\_\_lineares\_em** &ndash; Acelera rapidamente e continua a acelerar até o final da animação.

- **Rápido\_\_para\_fora lento em** &ndash; Acelera rapidamente e lentamente desacelera no final da animação.

- **Linear\_\_para\_fora lento em** &ndash; Começa com uma velocidade máxima e lentamente desacelera até o final da animação.

Você pode usar `PathInterpolator` a nova classe para especificar como a interpolação de movimento ocorre. `PathInterpolator`é um interpolador que atravessa caminhos de animação de acordo com pontos de controle especificados e curvas de movimento. Para obter mais informações sobre como especificar configurações de movimento curvo no Android 5.0, consulte [Use Curved Motion](https://developer.android.com/training/material/animations.html#CurvedMotion).

## <a name="view-shadows--elevation"></a>Ver sombras & elevação

No Android 5.0, você pode especificar a `Z` *elevação* de uma exibição definindo uma nova propriedade. Um `Z` valor maior faz com que a visão lance uma sombra maior no fundo, fazendo com que a vista pareça flutuar mais acima do fundo. Você pode definir a elevação inicial `elevation` de uma exibição configurando seu atributo no layout.

O exemplo a seguir ilustra as `TextView` sombras lançadas por um controle vazio quando seu atributo de elevação é definido como 2dp, 4dp e 6dp, respectivamente:

[![Capturas de tela de sombras de visão progessivamente maiores](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

As configurações de sombra de visualização podem ser estáticas (como mostrado acima) ou podem ser usadas em animações para fazer com que uma exibição pareça subir temporariamente acima do plano de fundo da exibição. Você pode `ViewPropertyAnimator` usar a classe para animar a elevação de uma vista. A elevação de uma vista é `elevation` a `translationZ` soma de sua configuração de layout mais uma propriedade que você pode definir através de uma `ViewPropertyAnimator` chamada de método.

Para obter mais informações sobre sombras de visualização no Android 5.0, consulte [Definindo sombras e visualizações de recorte](https://developer.android.com/training/material/shadows-clipping.html).

## <a name="color-features"></a>Características de cores

O Android 5.0 oferece dois novos recursos para o gerenciamento de cores em aplicativos:

- *O colorting desenhado* permite alterar as cores dos ativos de imagem alterando um atributo de layout.

- *A extração de cores proeminentes* permite que você personalize dinamicamente o tema de cores do seu aplicativo para coordenar com a paleta de cores de uma imagem exibida.

### <a name="drawable-tinting"></a>Desenho de desenho

Os layouts do Android `tint` 5.0 reconhecem um novo atributo que você pode usar para definir a cor dos drawables sem ter que criar várias versões desses ativos para exibir cores diferentes. Para usar esse recurso, você define um bitmap `tint` como uma máscara alfa e usa o atributo para definir a cor do ativo. Isso torna possível que você crie ativos uma vez e colora-os em seu layout para combinar com o seu tema.

No exemplo a seguir, &ndash; um único recurso de &ndash; imagem um logotipo branco com um fundo transparente é usado para criar variações de estaca:

![Logotipo Xamarin branco com fundo transparente](lollipop-images/xamarin-logo-white.png)

Este logotipo é exibido acima de um fundo circular azul, como mostrado nos exemplos a seguir. A imagem à esquerda é como `tint` o logotipo aparece sem uma configuração. Na imagem central, o `tint` atributo do logotipo é definido como um cinza escuro. Na imagem à direita, `tint` é definido como um cinza claro:

![Exemplos do logotipo acima com diferentes configurações de estat](lollipop-images/drawable-tinting.png)

Para obter mais informações sobre a tingimento de desenho no Android 5.0, consulte [Drawable Tinting](https://developer.android.com/training/material/drawables.html#DrawableTint).

### <a name="prominent-color-extraction"></a>Extração de cores proeminentes

A nova classe `Palette` Android 5.0 permite extrair cores de uma imagem para que você possa aplicá-las dinamicamente a uma paleta de cores personalizada. A `Palette` classe extrai seis cores de uma imagem e rotula essas cores de acordo com seus níveis relativos de saturação de cores e brilho:

- Vibrante

- Escuridão vibrante

- Luz vibrante

- Silenciado

- Escuro silenciado

- Luz silenciada

Por exemplo, nas capturas de tela a seguir, um aplicativo de visualização de fotos extrai as cores proeminentes da imagem em exibição e usa essas cores para adaptar o esquema de cores do aplicativo para corresponder à imagem:

[![Capturas de tela de extrações de cores temáticas verdes, rosas e azuis](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

Nas capturas de tela acima, a barra de ação é definida para a cor extraída de "luz vibrante" e o fundo é definido para a cor extraída "vibrante escuro". Em cada exemplo acima, uma linha de pequenos quadrados de cores é incluída para ilustrar as cores da paleta que foram extraídas da imagem.

Para obter mais informações sobre extração de cores no Android 5.0, consulte [Extraindo cores proeminentes de uma imagem](https://developer.android.com/training/material/drawables.html#ColorExtract).

## <a name="new-ui-widgets"></a>Novos widgets de ui

Android 5.0 apresenta dois novos widgets de ui:

- `RecyclerView`&ndash; Um grupo de exibição que exibe uma lista de itens roláveis.

- `CardView`&ndash; Um layout básico com cantos arredondados.

Ambos os widgets incluem suporte assado para recursos de tema material; por exemplo, `RecyclerView` usa animações para adicionar `CardView` e remover visualizações e usa sombras de exibição para fazer cada carta parecer flutuar acima do plano de fundo. Exemplos desses novos widgets são mostrados nas seguintes capturas de tela:

[![Capturas de tela de aplicativos construídos com o RecyclerView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

A captura de tela à `RecyclerView` esquerda é um exemplo de como usado em `CardView` um aplicativo de e-mail, e a captura de tela à direita é um exemplo de como usado em um aplicativo de reserva de viagem.

### <a name="recyclerview"></a>RecyclerView

`RecyclerView`é semelhante, `ListView,` mas é mais adequado para grandes conjuntos de visualizações ou listas com elementos que mudam dinamicamente. Como `ListView,` se você especificar um adaptador para acessar o conjunto de dados subjacente. No entanto, ao contrário de `ListView,` você usar um *gerenciador de layout* para posicionar itens dentro `RecyclerView`. O gerenciador de layout também cuida da reciclagem de visualização; gerencia a reutilização de visualizações de itens que não são mais visíveis para o usuário.

Quando você `RecyclerView` usa um widget, `LayoutManager` você deve especificar um e um adaptador. Como mostrado nesta `LayoutManager` figura, é o intermediário entre `RecyclerView`o adaptador e o:

![Diagrama do RecicladorVer com suporte ao Gerenciador de Layout, adaptador e conjunto de dados](lollipop-images/recyclerview-diagram.png)

As capturas de `RecyclerView` tela a seguir ilustram um que contém `ImageView` 100 itens (cada item consiste em um e um `TextView`):

[![Capturas de tela de um aplicativo RecyclerView percorrendo imagens](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`lida com este grande &ndash; conjunto de dados com facilidade de rolagem desde o início da lista até o fim da lista neste aplicativo de exemplo leva apenas alguns segundos. `RecyclerView`também suporta animações; na verdade, as animações para adicionar e remover itens são ativadas por padrão. Quando um item é `RecyclerView`adicionado a um , ele desaparece como mostrado nesta seqüência de capturas de tela:

[![Captura de tela quadro a quadro de um item de foto desaparecendo em](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

Para obter `RecyclerView`mais informações, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

### <a name="cardview"></a>CardView

`CardView`é uma visão simples que simula uma carta flutuante com cantos arredondados. Como `CardView` tem sombras de visão incorporadas, ele fornece uma maneira fácil de adicionar profundidade visual ao seu aplicativo. As capturas de tela a seguir `CardView`mostram três exemplos orientados ao texto de:

[![Exemplos de capturas de tela de aplicativos usando o RecyclerView com itens baseados em CardView](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

Cada uma das cartas no `TextView`exemplo acima contém um; a cor de fundo `cardBackgroundColor` é definida através do atributo.

Para obter `CardView`mais informações, consulte [CardView](~/android/user-interface/controls/card-view.md).

## <a name="enhanced-notifications"></a>Notificações aprimoradas

O sistema de notificação no Android 5.0 foi significativamente atualizado com um novo formato visual e novos recursos. As notificações têm um novo visual no Android 5.0. Por exemplo, as notificações no Android 5.0 agora usam texto escuro sobre um plano de fundo claro:

![Exemplo de uma notificação do Android 5.0 não expandida](lollipop-images/expanded-notification-contracted.png)

Quando um ícone grande é exibido em uma notificação (como mostrado no exemplo acima), o Android 5.0 apresenta o pequeno ícone como um emblema sobre o ícone grande.

No Android 5.0, as notificações também podem aparecer na tela de bloqueio do dispositivo.
Por exemplo, aqui está uma captura de tela de exemplo de uma tela de bloqueio com uma única notificação:

[![Captura de tela da notificação que aparece na tela de bloqueio](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

Os usuários podem tocar duas vezes em uma notificação na tela de bloqueio para desbloquear o dispositivo e pular para o aplicativo que originou essa notificação, ou deslizar para descartar a notificação. As notificações têm uma nova configuração *de visibilidade* que determina quanto conteúdo pode ser exibido na tela de bloqueio. Os usuários podem escolher se permitem que conteúdo sensível seja mostrado em notificações de tela de bloqueio.

O Android 5.0 introduz um novo formato de apresentação de notificação de alta prioridade chamado *Heads-up*. Notificações de cabeça para cima deslizam para baixo do topo da tela por alguns segundos e, em seguida, recuam de volta para a sombra de notificação na parte superior da tela. As notificações de aviso tornam possível que a im do sistema coloque informações importantes na frente do usuário sem interromper a atividade em execução no momento.
O exemplo a seguir ilustra uma simples notificação do Heads-up que é exibida em cima de um aplicativo:

[![Exemplo de notificação de aviso prévio](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

As notificações de aviso são normalmente usadas para os seguintes eventos:

- Uma nova mensagem seguinte

- Um telefonema de entrada

- Indicação de bateria fraca

- Um alarme

O Android 5.0 exibe uma notificação no formato Heads-up somente quando ele tem uma configuração de prioridade alta ou máxima.

No Android 5.0, você pode fornecer metadados de notificação para ajudar o Android a classificar e exibir notificações de forma mais inteligente. O Android 5.0 organiza notificações de acordo com prioridade, visibilidade e categoria.
As categorias de notificação são usadas para filtrar quais notificações podem ser apresentadas quando o dispositivo está no modo *Não perturbe.*

Para obter informações detalhadas sobre como criar e lançar notificações com os recursos mais recentes do Android 5.0, consulte [Notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).

## <a name="new-apis"></a>Novas APIs

Além dos novos recursos de visual descritos acima, o Android 5.0 adiciona novas APIs que ampliam os recursos da funcionalidade multimídia, armazenamento e conectividade existente. Além disso, o Android 5.0 inclui novas APIs que fornecem suporte para um novo recurso de agendador de trabalho.

### <a name="camera"></a>Câmera

O Android 5.0 fornece várias novas APIs para recursos aprimorados de câmera. O `Android.Hardware.Camera2` novo namespace inclui funcionalidade para acessar dispositivos de câmera individuais conectados a um dispositivo Android. Além `Android.Hardware.Camera2` disso, modela cada dispositivo de câmera como um pipeline: ele aceita uma solicitação de captura, captura a imagem e, em seguida, produz o resultado. Essa abordagem torna possível que os aplicativos enfileiram várias solicitações de captura para um dispositivo de câmera.

As apis a seguir tornam esses novos recursos possíveis:

- `CameraManager.GetCameraIdList`&ndash; Ajuda você a acessar programáticamente dispositivos de câmera; você `CameraManager.OpenCamera` usa para se conectar a um dispositivo de câmera específico.

- `CameraCaptureSession`&ndash; Captura ou transmite imagens do dispositivo da câmera. Você implementa uma `CameraCaptureSession.CaptureListener` interface para lidar com novos eventos de captura de imagem.

- `CaptureRequest`&ndash; Define parâmetros de captura.

- `CaptureResult`&ndash; Fornece os resultados de uma operação de captura de imagem.

Para saber mais sobre as novas APIs de câmera no Android 5.0, consulte [Mídia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="audio-playback"></a>Reprodução de áudio

O Android 5.0 `AudioTrack` atualiza a classe para melhor reprodução de áudio:

- `ENCODING_PCM_FLOAT`&ndash; Configura-se `AudioTrack` para aceitar dados de áudio em formato de ponto flutuante para melhor alcance dinâmico, maior espaço para a cabeça e maior qualidade (graças à maior precisão). Além disso, o formato de ponto flutuante ajuda a evitar o recorte de áudio.

- `ByteBuffer`&ndash; Agora você pode fornecer `AudioTrack` dados de áudio para como uma matriz de bytes.

- `WRITE_NON_BLOCKING`&ndash; Esta opção simplifica o buffering e o multithreading para alguns aplicativos.

Para saber `AudioTrack` mais sobre melhorias no Android 5.0, consulte [Mídia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="media-playback-control"></a>Controle de reprodução de mídia

O Android 5.0 `Android.Media.MediaController` apresenta a nova `RemoteControlClient`classe, que substitui . `Android.Media.MediaController`fornece APIs de controle de transporte simplificado e oferece controle seguro de thread de reprodução fora do contexto de Interface do UI. As novas APIs lidam com o controle de transporte:

- `Android.Media.Session.MediaSession`&ndash; Uma sessão de controle de mídia que lida com vários controladores. Você `MediaSession.GetSessionToken` liga para solicitar um token que seu aplicativo usa para interagir com a sessão.

- `MediaController.TransportControls`&ndash; Lida com comandos de transporte como **Play**, **Stop**e **Skip**.

Além disso, você `Android.App.Notification.MediaStyle` pode usar a nova classe para associar uma sessão de mídia com rico conteúdo de notificação (como extrair e mostrar arte do álbum).

Para obter mais informações sobre os novos recursos de controle de reprodução de mídia no Android 5.0, consulte [Mídia](https://developer.android.com/about/versions/android-5.0.html#Media).

### <a name="storage"></a>Armazenamento

O Android 5.0 atualiza o Storage Access Framework para facilitar o trabalho dos aplicativos com diretórios e documentos:

- Para selecionar uma subárvore de diretório, `Android.Intent.Action.OPEN_DOCUMENT_TREE` você pode construir e enviar uma intenção. Essa intenção faz com que o sistema exiba todas as instâncias do provedor que suportam a seleção de subárvores; em seguida, o usuário navega e seleciona um diretório.

- Para criar e gerenciar novos documentos ou diretórios em `CreateDocument`qualquer `RenameDocument`lugar `DeleteDocument` sob `DocumentsContract`uma subárvore, você usa os novos métodos e métodos de .

- Para obter caminhos para diretórios de mídia em todos `Android.Content.Context.GetExternalMediaDirs` os dispositivos de armazenamento compartilhado, você chama o novo método.

Para obter mais informações sobre novas APIs de armazenamento no Android 5.0, consulte [Armazenamento](https://developer.android.com/preview/api-overview.html#Storage).

### <a name="wireless--connectivity"></a>Conectividade & sem fio

O Android 5.0 adiciona os seguintes aprimoramentos de API para sem fio e conectividade:

- Novas APIs *multi-rede* que possibilitam que os aplicativos encontrem e selecionem redes com recursos específicos antes de fazer uma conexão.

- Funcionalidade de transmissão Bluetooth que permite que um dispositivo Android 5.0 atue como um periférico Bluetooth de baixa energia.

- Aprimoramentos do NFC que facilitam o uso da funcionalidade de comunicação de campo próximo para o compartilhamento de dados com outros dispositivos.

Para saber mais sobre as novas APIs sem fio e conectividade no Android 5.0, consulte [Wireless e Conectividade](https://developer.android.com/preview/api-overview.html#Wireless).

### <a name="job-scheduling"></a>Agendamento de vagas

O Android 5.0 `JobScheduler` introduz uma nova API que pode ajudar os usuários a minimizar o dreno da bateria, agendando certas tarefas para serem executadas somente quando o dispositivo estiver conectado e carregando. Este recurso de agendador de trabalho também pode ser usado para agendar uma tarefa a ser executada quando as condições são mais adequadas para essa tarefa, como baixar um arquivo grande quando o dispositivo está conectado em uma rede Wi-Fi em vez de uma rede medida.

Para obter mais informações sobre as novas APIs de agendamento de empregos no Android 5.0, consulte [Agendamento de Empregos](https://developer.android.com/preview/api-overview.html#JobScheduler).

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral de novos recursos importantes no Android 5.0 para desenvolvedores de aplicativos Xamarin.Android:

- Tema de material

- Animations

- Ver sombras e elevação

- Características de cores, como coloração desenhetível e extração de cores proeminentes

- O `RecyclerView` novo `CardView` e widgets

- Melhorias de notificação

- Novas APIs para câmera, reprodução de áudio, controle de mídia, armazenamento, sem fio/conectividade e agendamento de trabalho

Se você é novo no desenvolvimento do Android Xamarin, leia [Configuração e Instalação](~/android/get-started/installation/index.md) para ajudá-lo a começar com o Xamarin.Android.
[Olá, Android](~/android/get-started/hello-android/index.md) é uma excelente introdução para aprender a criar projetos Android.

## <a name="related-links"></a>Links relacionados

- [Pré-visualização do desenvolvedor do Android L](https://developer.android.com/preview/index.html)
- [Obter o SDK do Android](https://developer.android.com/sdk/index.html#Other)
- [Projeto de material](https://developer.android.com/preview/material/index.html)
