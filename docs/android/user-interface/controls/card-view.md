---
title: CardView
description: O widget de widgets Cardview é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em modos de exibição que se assemelhem cartões. Este guia explica como usar e personalizar os widgets CardView em aplicativos xamarin. Android enquanto mantém a compatibilidade com versões anteriores do Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: cdb75207bff3f15a54d0cdd90fa0833da9c145e6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077680"
---
# <a name="cardview"></a>CardView

_O widget de widgets Cardview é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em modos de exibição que se assemelhem cartões. Este guia explica como usar e personalizar os widgets CardView em aplicativos xamarin. Android enquanto mantém a compatibilidade com versões anteriores do Android._


## <a name="overview"></a>Visão geral

O `Cardview` widget, introduzido no Android 5.0 (Lollipop), é um componente de interface do usuário que apresenta o conteúdo de texto e imagem em modos de exibição que se assemelhem cartões. `CardView` é implementado como um `FrameLayout` widget com cantos arredondados e uma sombra. Normalmente, uma `CardView` é usado para apresentar um item de linha única em um `ListView` ou `GridView` grupo do modo de exibição. Por exemplo, a captura de tela a seguir é um exemplo de um aplicativo de reserva de viagem que implemente `CardView`-com base em cartões de destino de viagem em um rolável `ListView`:

![Aplicativo de exemplo usando um widgets CardView para cada destino de viagem](card-view-images/01-cardview-example.png)

Este guia explica como adicionar o `CardView` projeto de pacote para o xamarin. Android, como adicionar `CardView` seu layout, e como personalizar a aparência de `CardView` em seu aplicativo. Além disso, este guia fornece uma lista detalhada dos `CardView` atributos que podem ser alterados, inclusive atributos para ajudar você a usar `CardView` em versões do Android anteriores ao Android 5.0 Lollipop.

<a name="requirements" />

## <a name="requirements"></a>Requisitos

A seguir é necessária para usar o novo Android 5.0 e posteriores recursos (incluindo `CardView`) em aplicativos baseados em Xamarin:

-  **Xamarin. Android** &ndash; xamarin. Android 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-  **SDK do Android** &ndash; Android 5.0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-  **Java JDK 1.8** &ndash; JDK 1.7 pode ser usado se você é especificamente direcionando API nível 23 e versões anterior. JDK 1.8 está disponível no [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Seu aplicativo também deve incluir o `Xamarin.Android.Support.v7.CardView` pacote. Para adicionar o `Xamarin.Android.Support.v7.CardView` pacote no Visual Studio para Mac:

1. Abra o projeto, clique com botão direito **pacotes** e selecione **adicionar pacotes**.

2. No **adicionar pacotes** caixa de diálogo, pesquise por **widgets CardView**.

3. Selecione **widgets CardView v7 de biblioteca de suporte de Xamarin**, em seguida, clique em **Adicionar pacote**.

Para adicionar o `Xamarin.Android.Support.v7.CardView` pacote no Visual Studio:

1. Abra o projeto, clique com botão direito do **referências** nó (no **Gerenciador de soluções** painel) e selecione **gerenciar pacotes NuGet...** .

2. Quando o **gerenciar pacotes NuGet** caixa de diálogo for exibida, insira **widgets CardView** na caixa de pesquisa.

3. Quando **widgets CardView v7 de biblioteca de suporte de Xamarin** for exibida, clique em **instalar**.

Para saber como configurar um projeto de aplicativo do Android 5.0, consulte [configuração de backup de um Android 5.0 projeto](~/android/platform/lollipop.md).
Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: Incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


## <a name="introducing-cardview"></a>Apresentando os widgets CardView

O padrão `CardView` se parece com um cartão branco com cantos arredondados minimamente e uma pequena sombra. O exemplo a seguir **Main. axml** layout exibe uma única `CardView` widget que contém um `TextView`:

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

Se você usar esse XML para substituir o conteúdo existente de **Main. axml**, certifique-se de comentar qualquer código no **MainActivity.cs** que se refere aos recursos no XML anterior.

Este exemplo de layout cria um padrão `CardView` com uma única linha de texto conforme mostrado na seguinte captura de tela:

[![Captura de tela de widgets CardView com linha de texto e o plano de fundo branco](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

Neste exemplo, o estilo de aplicativo é definido como a luz de tema de Material (`Theme.Material.Light`) para que o `CardView` bordas e sombras são mais fácil de ver. Para obter mais informações sobre os aplicativos de temas Android 5.0, consulte [tema de Material](~/android/user-interface/material-theme.md). Na próxima seção, aprenderemos como personalizar `CardView` para um aplicativo.


## <a name="customizing-cardview"></a>Personalizando widgets CardView

Você pode modificar o basic `CardView` atributos para personalizar a aparência do `CardView` em seu aplicativo. Por exemplo, a elevação a `CardView` pode ser aumentado para converter uma sombra maior (o que torna o cartão parecem float superior acima a tela de fundo). Além disso, o raio do canto pode ser aumentado para os cantos do cartão mais arredondado.

No exemplo a seguir layout, um personalizado `CardView` é usado para criar uma simulação de uma fotografia de impressa (um "instantâneo"). Uma `ImageView` é adicionado ao `CardView` para exibir a imagem e um `TextView` é posicionada abaixo o `ImageView` para exibir o título da imagem.
No layout neste exemplo, o `CardView` tem as seguintes personalizações:

-  O `cardElevation` é aumentado para 4dp para converter uma sombra maior.
-  O `cardCornerRadius` é aumentado para 5dp para exibir os cantos arredondados mais.

Porque `CardView` é fornecido pela biblioteca de suporte do Android v7, seus atributos não estão disponíveis no `android:` namespace. Portanto, você deve definir seu próprio namespace XML e usar esse namespace como o `CardView` prefixo do atributo. O exemplo de layout abaixo, usaremos esta linha para definir um namespace chamado `cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Você pode chamar esse namespace `card_view` ou até mesmo `myapp` se você escolher (ele é acessível somente dentro do escopo deste arquivo). Tudo o que você optar por chamar esse namespace, você deve usá-lo para prefixar o `CardView` atributo que você deseja modificar. Neste exemplo de layout, o `cardview` namespace é o prefixo `cardElevation` e `cardCornerRadius`:

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

Quando este exemplo de layout é usado para exibir uma imagem em um aplicativo de exibição de fotos, o `CardView` tem a aparência de um instantâneo de foto, conforme ilustrado na captura de tela a seguir:

[![Widgets CardView com uma imagem e a legenda abaixo da imagem](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Nesta captura de tela é obtida a [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer) aplicativo de exemplo que usa um `RecyclerView` widget para apresentar uma lista de rolagem de `CardView` imagens para a exibição de fotos. Para obter mais informações sobre `RecyclerView`, consulte o [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) guia.

Observe que um `CardView` pode exibir mais de um modo de exibição filho em sua área de conteúdo. Por exemplo, a exemplo de aplicativo de exibição de fotos acima, a área de conteúdo é composta de um `ListView` que contém um `ImageView` e um `TextView`. Embora `CardView` instâncias geralmente são dispostas verticalmente, você também pode organizá-los horizontalmente (consulte [criando um estilo de exibição personalizado](~/android/user-interface/material-theme.md#customview) para uma captura de tela de exemplo).


### <a name="cardview-layout-options"></a>Opções de Layout de widgets CardView

`CardView` layouts podem ser personalizados, definindo um ou mais atributos que afetam seu preenchimento, elevação, raio do canto e cor do plano de fundo:

[![Diagrama de widgets CardView atributos](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Cada atributo também pode ser alterado dinamicamente chamando uma contraparte `CardView` método (para obter mais informações sobre `CardView` métodos, consulte o [referência de classe de widgets CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Observe que esses atributos (exceto para a cor do plano de fundo) aceitam um valor de dimensão, que é um número decimal seguido por unidade. Por exemplo, `11.5dp` Especifica pixels de 11,5 independentes de densidade.


#### <a name="padding"></a>Enchimento
`
CardView` oferece cinco atributos de preenchimento para posicionar o conteúdo dentro do cartão. Você pode defini-las em seu layout de XML ou você pode chamar métodos análogos no seu código:

[![Diagrama de widgets CardView atributos de preenchimento](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Os atributos de preenchimento são explicados a seguir:

-  `contentPadding` &ndash; Preenchimento entre os modos de exibição filho interno a `CardView` e todas as bordas do cartão.

-  `contentPaddingBottom` &ndash; Preenchimento entre os modos de exibição filho interno a `CardView` e a borda inferior do cartão.

-  `contentPaddingLeft` &ndash; Preenchimento entre os modos de exibição filho interno a `CardView` e a borda esquerda do cartão.

-  `contentPaddingRight` &ndash; Preenchimento entre os modos de exibição filho interno a `CardView` e a borda direita do cartão.

-  `contentPaddingTop` &ndash; Preenchimento entre os modos de exibição filho interno a `CardView` e a borda superior do cartão.

Atributos de preenchimento de conteúdo são relativos ao limite da área de conteúdo em vez de qualquer determinado widget localizado dentro da área de conteúdo.
Por exemplo, se `contentPadding` suficientemente aumentaram o aplicativo de exibição de fotos, o `CardView` seria cortar a imagem e o texto mostrado no cartão.



#### <a name="elevation"></a>Elevação

`CardView` oferece dois atributos de elevação para controlar sua elevação e, como resultado, o tamanho da sua sombra:

[![Diagrama de atributos de elevação de widgets CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Os atributos de elevação são explicados a seguir:

-  `cardElevation` &ndash; A elevação a `CardView` (representa o eixo Z).

-  `cardMaxElevation` &ndash; O valor máximo do `CardView`da elevação.

Valores maiores de `cardElevation` aumentar o tamanho da sombra para tornar `CardView` parecem float superior acima a tela de fundo. O `cardElevation` atributo também determina a ordem de desenho de sobreposição de modos de exibição, ou seja, o `CardView` serão desenhados sob outra exibição sobreposta com uma configuração mais alta de elevação e acima de todas as exibições de sobreposição com uma configuração mais baixa de elevação.
O `cardMaxElevation` configuração é útil para quando o seu aplicativo muda elevação dinamicamente &ndash; impede que a sombra estendendo além do limite definido com essa configuração.


#### <a name="corner-radius-and-background-color"></a>Raio do canto e a cor de plano de fundo

`CardView` oferece atributos que você pode usar para controlar seu raio de canto e sua cor de plano de fundo. Essas duas propriedades permitem que você alterar o estilo geral do `CardView`:

[![Diagrama de canto de widgets CardView radious e atributos de cor do plano de fundo](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Esses atributos são explicados a seguir:

-  `cardCornerRadius` &ndash; O raio do canto de todos os cantos do `CardView`.

-  `cardBackgroundColor` &ndash; A cor de fundo a `CardView`.

Neste diagrama `cardCornerRadius` é definido como um 10dp mais cantos arredondados e `cardBackgroundColor` é definido como `"#FFFFCC"` (amarelo claro).


## <a name="compatibility"></a>Compatibilidade

Você pode usar `CardView` em versões do Android anteriores ao Android 5.0 Lollipop. Porque `CardView` faz parte da biblioteca de suporte do Android v7, você pode usar `CardView` 2.1 do Android (API nível 7) e superior.
No entanto, você deve instalar o `Xamarin.Android.Support.v7.CardView` do pacote conforme descrito em [requisitos](#requirements), acima.

`CardView` Exibe o comportamento ligeiramente diferente em dispositivos antes de Lollipop (API nível 21):

-  `CardView` usa uma implementação de sombra programático que adiciona o preenchimento adicional.

-  `CardView` não clip-modos de exibição filho que se interseccionam com o `CardView`do cantos arredondados.

Para ajudar a gerenciar essas diferenças de compatibilidade, `CardView` fornece vários atributos adicionais que podem ser configuradas em seu layout:

-   `cardPreventCornerOverlap` &ndash; Defina esse atributo como `true` adicionar preenchimento ao seu aplicativo está sendo executado anteriormente versões do Android (API nível 20 e anterior). Essa configuração impede `CardView` conteúdo de intersecção com o `CardView`do cantos arredondados.

-   `cardUseCompatPadding` &ndash; Defina esse atributo como `true` adicionar preenchimento ao seu aplicativo está em execução em versões do Android no ou maior que o nível de API 21. Se você quiser usar `CardView` em dispositivos pré-pirulito e fazê-lo parecem iguais no pirulito (ou posterior), defina esse atributo como `true`. Quando esse atributo está habilitado, `CardView` adiciona preenchimento adicional para desenhar sombras quando ele é executado em dispositivos pré-pirulito. Isso ajuda a superar as diferenças no preenchimento que são introduzidas quando as implementações de sombra programático de pré-pirulito estão em vigor.

Para obter mais informações sobre como manter a compatibilidade com versões anteriores do Android, consulte [manter compatibilidade](https://developer.android.com/training/material/compatibility.html).


## <a name="summary"></a>Resumo

Este guia apresentou a nova `CardView` widget incluído no Android 5.0 (Lollipop). Ele demonstrou o padrão `CardView` aparência e explicou como personalizar `CardView` alterando sua elevação, o arredondamento de canto, preenchimento de conteúdo e cor do plano de fundo. Ele contém o `CardView` atributos de layout (com diagramas de referência) e explicou como usar `CardView` em dispositivos Android anteriores ao Android 5.0 Lollipop. Para obter mais informações sobre `CardView`, consulte o [referência de classe de widgets CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).


## <a name="related-links"></a>Links relacionados

- [RecyclerView (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introdução ao Lollipop](~/android/platform/lollipop.md)
- [Referência de classe de widgets CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
