---
title: CardView
description: O widget Cardview é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em exibições que lembram cartões. Este guia explica como usar e personalizar aplicativos xamarin CardView enquanto mantém a compatibilidade com versões anteriores do Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 21e2a2e8ef04936664344cb4fb758bc2af3b4d05
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="cardview"></a>CardView

_O widget Cardview é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em exibições que lembram cartões. Este guia explica como usar e personalizar aplicativos xamarin CardView enquanto mantém a compatibilidade com versões anteriores do Android._


## <a name="overview"></a>Visão geral

O `Cardview` widget, introduzido no Android 5.0 (pirulito) é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em exibições que lembram cartões. `CardView` é implementado como um `FrameLayout` widget com cantos arredondados e uma sombra. Normalmente, um `CardView` é usado para apresentar um item de linha em uma `ListView` ou `GridView` grupo do modo de exibição. Por exemplo, a captura de tela a seguir está um exemplo de um aplicativo de reserva de viagem implementa `CardView`-com base em cartões de destino de viagem em um rolável `ListView`:

![Exemplo de aplicativo usando um CardView para cada destino de viagem](card-view-images/01-cardview-example.png)

Este guia explica como adicionar a `CardView` pacote para o xamarin do projeto, como adicionar `CardView` o layout e como personalizar a aparência de `CardView` em seu aplicativo. Além disso, este guia fornece uma lista detalhada de `CardView` atributos que você pode alterar, inclusive atributos para ajudá-lo a usar `CardView` em versões anteriores ao Android 5.0 de pirulito do Android.

<a name="requirements" />

## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar o novo Android 5.0 e recursos posteriores (incluindo `CardView`) em aplicativos baseados em Xamarin:

-  **Xamarin** &ndash; xamarin 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-  **SDK do Android** &ndash; Android 5.0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 pode ser usado se você é especificamente direcionar API nível 23 e versões anterior. 1.8 JDK está disponível em [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Seu aplicativo também deve incluir o `Xamarin.Android.Support.v7.CardView` pacote. Para adicionar o `Xamarin.Android.Support.v7.CardView` pacote no Visual Studio para Mac:

1. Abra seu projeto, clique no **pacotes** e selecione **adicionar pacotes**.

2. No **adicionar pacotes** caixa de diálogo, procure **CardView**.

3. Selecione **biblioteca de suporte de Xamarin v7 CardView**, em seguida, clique em **Adicionar pacote**.

Para adicionar o `Xamarin.Android.Support.v7.CardView` pacote no Visual Studio:

1. Abra seu projeto, clique com botão direito do **referências** nó (no **Solution Explorer** painel) e selecione **gerenciar pacotes NuGet...** .

2. Quando o **gerenciar pacotes NuGet** caixa de diálogo é exibida, digite **CardView** na caixa de pesquisa.

3. Quando **biblioteca de suporte de Xamarin v7 CardView** for exibida, clique em **instalar**.

Para saber como configurar um projeto de aplicativo do Android 5.0, consulte [configuração de backup de um Android 5.0 projeto](~/android/platform/lollipop.md).
Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Introdução ao CardView

O padrão `CardView` se assemelha a um cartão branco com cantos arredondados minimamente e uma pequena sombra. O exemplo a seguir **Main.axml** layout exibe um único `CardView` widget que contém um `TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Se você usar esse XML para substituir o conteúdo existente de **Main.axml**, certifique-se de comentar qualquer código de **MainActivity.cs** que refere-se aos recursos no XML anterior.

Este exemplo de layout cria um padrão `CardView` com uma única linha de texto, como mostrado na captura de tela a seguir:

[![Captura de tela de CardView com linha de texto e plano de fundo branco](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

Neste exemplo, o estilo de aplicativo é definido como o Material de tema claro (`Theme.Material.Light`) para que o `CardView` bordas e sombras são mais fáceis de ver. Para obter mais informações sobre aplicativos de temas Android 5.0, consulte [Material tema](~/android/user-interface/material-theme.md). Na próxima seção, podemos aprenderá a personalizar `CardView` para um aplicativo.


## <a name="customizing-cardview"></a>Personalizando CardView

Você pode modificar o basic `CardView` atributos para personalizar a aparência do `CardView` em seu aplicativo. Por exemplo, a elevação do `CardView` pode ser aumentado para converter uma sombra maior (o que torna o cartão parece float superior acima do plano de fundo). Além disso, o raio de canto pode ser aumentado para tornar os cantos do cartão mais arredondados.

No exemplo a seguir layout, um personalizado `CardView` é usado para criar uma simulação de uma fotografia de impressão (um "instantâneo"). Um `ImageView` é adicionada para o `CardView` para exibir a imagem e um `TextView` é posicionada abaixo o `ImageView` para exibir o título da imagem.
No layout de exemplo, o `CardView` tem as seguintes personalizações:

-  O `cardElevation` é aumentado para 4dp para converter uma sombra maior.
-  O `cardCornerRadius` é aumentado para 5dp para exibir os cantos arredondados mais.

Porque `CardView` é fornecida pela biblioteca de suporte do Android v7, seus atributos não estão disponíveis no `android:` namespace. Portanto, você deve definir seu próprio namespace XML e usar esse namespace como o `CardView` prefixo do atributo. O exemplo de layout abaixo, usaremos esta linha para definir um namespace chamado `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Você pode chamar esse namespace `card_view` ou até mesmo `myapp` se você escolher (é acessível somente dentro do escopo deste arquivo). Tudo o que você escolher chamar esse namespace, você deve usá-lo para prefixar o `CardView` atributo que você deseja modificar. Neste exemplo de layout, o `cardview` o prefixo do namespace é `cardElevation` e `cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

Quando este exemplo de layout é usado para exibir uma imagem em um aplicativo de exibição de foto, o `CardView` tem a aparência de um instantâneo de fotos, conforme ilustrado na captura de tela a seguir:

[![CardView com uma imagem e a legenda abaixo da imagem](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Esta captura de tela é obtida a [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) amostra de aplicativo, que usa um `RecyclerView` widget para apresentar uma lista de rolagem de `CardView` imagens para exibir fotos. Para obter mais informações sobre `RecyclerView`, consulte o [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) guia.

Observe que um `CardView` pode exibir mais de uma exibição de filho em sua área de conteúdo. Por exemplo, a exemplo de aplicativo de exibição de fotos acima, a área de conteúdo é composta de uma `ListView` que contém um `ImageView` e um `TextView`. Embora `CardView` instâncias geralmente são organizadas verticalmente, você também pode organizá-los horizontalmente (consulte [criando um estilo de exibição personalizado](~/android/user-interface/material-theme.md#customview) para uma captura de tela de exemplo).


### <a name="cardview-layout-options"></a>Opções de Layout de CardView

`CardView` layouts de podem ser personalizados, definindo um ou mais atributos que afetam seu preenchimento, elevação, raio de canto e cor de plano de fundo:

[![Diagrama de atributos de CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Cada atributo também pode ser alterado dinamicamente ao chamar um equivalente `CardView` método (para obter mais informações sobre `CardView` métodos, consulte o [referência de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Observe que esses atributos (exceto para a cor de plano de fundo) aceitam um valor de dimensão, que é um número decimal seguido de unidade. Por exemplo, `11.5dp` Especifica 11.5 pixels de densidade independentes.


#### <a name="padding"></a>Enchimento
`
CardView` oferece cinco atributos de preenchimento para posicionar o conteúdo dentro do cartão. Você pode defini-las no layout do XML, ou você pode chamar métodos análogos no seu código:

[![Diagrama de CardView atributos de preenchimento](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Os atributos de preenchimento são explicados a seguir:

-  `contentPadding` &ndash; Interna preenchimento entre os modos de exibição filho a `CardView` e todas as bordas do cartão.

-  `contentPaddingBottom` &ndash; Interna preenchimento entre os modos de exibição filho a `CardView` e a borda inferior do cartão.

-  `contentPaddingLeft` &ndash; Interna preenchimento entre os modos de exibição filho a `CardView` e a borda esquerda do cartão.

-  `contentPaddingRight` &ndash; Interna preenchimento entre os modos de exibição filho a `CardView` e a borda direita do cartão.

-  `contentPaddingTop` &ndash; Interna preenchimento entre os modos de exibição filho a `CardView` e a borda superior do cartão.

Atributos de preenchimento de conteúdo são relativas ao limite da área de conteúdo em vez de qualquer dado widget localizado dentro da área de conteúdo.
Por exemplo, se `contentPadding` suficientemente aumentaram no aplicativo de exibição de foto, o `CardView` seria cortar a imagem e o texto mostrado no cartão.



#### <a name="elevation"></a>Elevação

`CardView` oferece dois atributos de elevação para controlar sua elevação e, como resultado, o tamanho da sua sombra:

[![Diagrama de atributos de elevação CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Os atributos de elevação são explicados a seguir:

-  `cardElevation` &ndash; A elevação do `CardView` (representa o eixo Z).

-  `cardMaxElevation` &ndash; O valor máximo de `CardView`da elevação.

Valores maiores de `cardElevation` aumentar o tamanho da sombra para tornar `CardView` parece float superior acima do plano de fundo. O `cardElevation` atributo também determina a ordem de desenho de sobreposição de exibições; ou seja, o `CardView` será desenhado em outra exibição sobreposta com uma configuração de elevação superior e acima de qualquer exibição sobrepostas com uma configuração inferior de elevação.
O `cardMaxElevation` configuração é útil para quando seu aplicativo altera elevação dinamicamente &ndash; impede que a sombra estender além dos limites que definem com essa configuração.


#### <a name="corner-radius-and-background-color"></a>Raio de canto e a cor do plano de fundo

`CardView` oferece atributos que você pode usar para controlar o raio de canto e sua cor de plano de fundo. Essas duas propriedades permitem que você alterar o estilo geral do `CardView`:

[![Diagrama de canto de CardView radious e atributos de cor do plano de fundo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Esses atributos são explicados a seguir:

-  `cardCornerRadius` &ndash; O raio de canto de todos os cantos do `CardView`.

-  `cardBackgroundColor` &ndash; A cor de fundo a `CardView`.

Neste diagrama, `cardCornerRadius` é definido como um 10dp mais arredondado e `cardBackgroundColor` é definido como `"#FFFFCC"` (amarelo-claro).


## <a name="compatibility"></a>Compatibilidade

Você pode usar `CardView` em versões anteriores ao Android 5.0 de pirulito do Android. Porque `CardView` faz parte da biblioteca de suporte do Android v7, você pode usar `CardView` com Android 2.1 (API nível 7) e superior.
No entanto, você deve instalar o `Xamarin.Android.Support.v7.CardView` pacote conforme descrito em [requisitos](#requirements)acima.

`CardView` Exibe um comportamento um pouco diferente em dispositivos antes de pirulito (API nível 21):

-  `CardView` usa uma implementação de sombra programático que adiciona o preenchimento adicional.

-  `CardView` Não recorte exibições filho que se cruzam com o `CardView`do cantos arredondados.

Para ajudar a gerenciar essas diferenças de compatibilidade, `CardView` fornece vários atributos adicionais que você pode configurar seu layout:

-   `cardPreventCornerOverlap` &ndash; Defina este atributo como `true` para adicionar preenchimento quando seu aplicativo é executado em versões anteriores Android (API nível 20 e anterior). Essa configuração impede `CardView` conteúdo de intersecção com o `CardView`do cantos arredondados.

-   `cardUseCompatPadding` &ndash; Defina este atributo como `true` para adicionar preenchimento quando seu aplicativo estiver sendo executado em versões do Android no ou maior que o nível de API 21. Se você quiser usar `CardView` em dispositivos de pirulito antes que se parecem pirulito (ou posterior), defina este atributo como `true`. Quando esse atributo está habilitado, `CardView` adiciona o preenchimento adicional para desenhar sombras quando ele é executado em dispositivos de pré pirulito. Isso ajuda a superar as diferenças no preenchimento que são apresentadas ao implementações de sombra programático pré-pirulito estão em vigor.

Para obter mais informações sobre como manter a compatibilidade com versões anteriores do Android, consulte [manter compatibilidade](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Resumo

Este guia introduziu o novo `CardView` widget incluído no Android 5.0 (pirulito). Ele demonstrado padrão `CardView` aparência e explicou como personalizar `CardView` alterando sua elevação, o arredondamento de canto, preenchimento de conteúdo e cor do plano de fundo. Ele contém o `CardView` atributos de layout (com diagramas de referência) e explicou como usar `CardView` em dispositivos Android anteriores pirulito do Android 5.0. Para obter mais informações sobre `CardView`, consulte o [referência de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Links relacionados

- [RecyclerView (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introdução ao pirulito](~/android/platform/lollipop.md)
- [Referência de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
