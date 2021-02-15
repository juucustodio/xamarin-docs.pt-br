---
title: CardView
description: O widget Cardview é um componente de interface do usuário que apresenta conteúdo de texto e imagem em exibições semelhantes a cartões. Este guia explica como usar e personalizar o CardView em aplicativos Xamarin. Android e, ao mesmo tempo, manter a compatibilidade com versões anteriores do Android.
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d378b48741b6640f342d51e6eaa16aaf1a2f74a5
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457452"
---
# <a name="xamarinandroid-cardview"></a>CardView Xamarin. Android

_O widget Cardview é um componente de interface do usuário que apresenta conteúdo de texto e imagem em exibições semelhantes a cartões. Este guia explica como usar e personalizar o CardView em aplicativos Xamarin. Android e, ao mesmo tempo, manter a compatibilidade com versões anteriores do Android._

## <a name="overview"></a>Visão geral

O `Cardview` widget, introduzido no Android 5,0 (pirulito), é um componente de interface do usuário que apresenta conteúdo de texto e imagem em exibições que se assemelham a cartões. `CardView` é implementado como um `FrameLayout` Widget com cantos arredondados e uma sombra. Normalmente, um `CardView` é usado para apresentar um único item de linha em um `ListView` grupo de ou de `GridView` exibição. Por exemplo, a captura de tela a seguir é um exemplo de um aplicativo de reserva de viagem que implementa `CardView` cartões de destino de viagem baseados em uma rolagem `ListView` :

![Aplicativo de exemplo usando um CardView para cada destino de viagem](card-view-images/01-cardview-example.png)

Este guia explica como adicionar o `CardView` pacote ao seu projeto do Xamarin. Android, como adicionar `CardView` ao seu layout e como personalizar a aparência do `CardView` em seu aplicativo. Além disso, este guia fornece uma lista detalhada de `CardView` atributos que você pode alterar, incluindo atributos para ajudá-lo a usar `CardView` versões do Android anteriores ao Android 5,0 pirulito.

<a name="requirements"></a>

## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os novos recursos do Android 5,0 e posteriores (incluindo `CardView` ) em aplicativos baseados no Xamarin:

- **Xamarin. Android** &ndash; xamarin. Android 4,20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

- **SDK do Android** &ndash; O Android 5,0 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

- **Java JDK 1,8** &ndash; O JDK 1,7 pode ser usado se você estiver especificamente direcionado para a API de nível 23 e anterior. O JDK 1,8 está disponível na [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Seu aplicativo também deve incluir o `Xamarin.Android.Support.v7.CardView` pacote. Para adicionar o `Xamarin.Android.Support.v7.CardView` pacote no Visual Studio para Mac:

1. Abra seu projeto, clique com o botão direito do mouse em **pacotes** e selecione **adicionar pacotes**.

2. Na caixa de diálogo **adicionar pacotes** , procure **CardView**.

3. Selecione **biblioteca de suporte do Xamarin v7 CardView**e clique em **Adicionar pacote**.

Para adicionar o `Xamarin.Android.Support.v7.CardView` pacote no Visual Studio:

1. Abra seu projeto, clique com o botão direito do mouse no nó **referências** (no painel **Gerenciador de soluções** ) e selecione **gerenciar pacotes NuGet...**.

2. Quando a caixa de diálogo **gerenciar pacotes do NuGet** for exibida, insira **CardView** no quadro de pesquisa.

3. Quando a **biblioteca de suporte do Xamarin v7 CardView** for exibida, clique em **instalar**.

Para saber como configurar um projeto de aplicativo do Android 5,0, consulte [Configurando um projeto do android 5,0](~/android/platform/lollipop.md).
Para obter mais informações sobre a instalação de pacotes do NuGet, consulte [Walkthrough: incluindo um NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough).

## <a name="introducing-cardview"></a>Apresentando o CardView

O padrão é `CardView` semelhante a um cartão branco com cantos arredondados mínimos e uma pequena sombra. O layout **Main. axml** de exemplo a seguir exibe um único `CardView` widget que contém um `TextView` :

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

Se você usar esse XML para substituir o conteúdo existente de **Main. axml**, lembre-se de comentar qualquer código em **MainActivity.cs** que se refere aos recursos no XML anterior.

Este exemplo de layout cria um padrão `CardView` com uma única linha de texto, conforme mostrado na captura de tela a seguir:

[![Captura de tela de CardView com plano de fundo branco e linha de texto](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

Neste exemplo, o estilo do aplicativo é definido como o tema do material claro ( `Theme.Material.Light` ) para que as `CardView` sombras e bordas sejam mais fáceis de ver. Para obter mais informações sobre os aplicativos do Android 5,0, consulte [material Theme](~/android/user-interface/material-theme.md). Na próxima seção, aprenderemos a personalizar o `CardView` para um aplicativo.

## <a name="customizing-cardview"></a>Personalizando o CardView

Você pode modificar os `CardView` atributos básicos para personalizar a aparência do `CardView` em seu aplicativo. Por exemplo, a elevação do `CardView` pode ser aumentada para converter uma sombra maior (o que faz com que o cartão pareça flutuar mais acima do plano de fundo). Além disso, o raio do canto pode ser aumentado para tornar os cantos do cartão mais arredondados.

No próximo exemplo de layout, um personalizado `CardView` é usado para criar uma simulação de uma fotografia de impressão (um "instantâneo"). Um `ImageView` é adicionado ao `CardView` para exibir a imagem e um `TextView` está posicionado abaixo do `ImageView` para exibir o título da imagem.
Neste exemplo de layout, o `CardView` tem as seguintes personalizações:

- O `cardElevation` é aumentado para 4DP para converter uma sombra maior.
- O `cardCornerRadius` é aumentado para 5dp para fazer com que os cantos sejam mais arredondados.

Como `CardView` o é fornecido pela biblioteca de suporte do Android v7, seus atributos não estão disponíveis no `android:` namespace. Portanto, você deve definir seu próprio namespace XML e usar esse namespace como o `CardView` prefixo do atributo. No exemplo de layout abaixo, usaremos essa linha para definir um namespace chamado `cardview` :

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

Você pode chamar esse namespace `card_view` ou mesmo `myapp` se escolher (ele só pode ser acessado dentro do escopo deste arquivo). Seja qual for a sua escolha para chamar esse namespace, você deve usá-lo para prefixar o `CardView` atributo que deseja modificar. Neste exemplo de layout, o `cardview` namespace é o prefixo para `cardElevation` e `cardCornerRadius` :

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

Quando este exemplo de layout é usado para exibir uma imagem em um aplicativo de visualização de fotos, o `CardView` tem a aparência de um instantâneo de foto, conforme descrito na captura de tela a seguir:

[![CardView com uma imagem e legenda abaixo da imagem](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

Essa captura de tela é obtida do aplicativo de exemplo [RecyclerViewer](/samples/xamarin/monodroid-samples/android50-recyclerviewer) , que usa um `RecyclerView` widget para apresentar uma lista de rolagem de `CardView` imagens para exibir fotos. Para obter mais informações sobre `RecyclerView` o, consulte o guia do [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md) .

Observe que um `CardView` pode exibir mais de uma exibição filho em sua área de conteúdo. Por exemplo, no exemplo de aplicativo de exibição de fotos acima, a área de conteúdo é composta de um `ListView` que contém um `ImageView` e um `TextView` . Embora `CardView` as instâncias geralmente sejam organizadas verticalmente, você também pode organizá-las horizontalmente (consulte [criando um estilo de exibição personalizado](~/android/user-interface/material-theme.md#customview) para um exemplo de captura de tela).

### <a name="cardview-layout-options"></a>Opções de layout CardView

`CardView` os layouts podem ser personalizados definindo um ou mais atributos que afetam seu preenchimento, elevação, raio de canto e cor de plano de fundo:

[![Diagrama de atributos CardView](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

Cada atributo também pode ser alterado dinamicamente chamando-se um `CardView` método correspondente (para obter mais informações sobre `CardView` métodos, consulte a [referência de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)).
Observe que esses atributos (exceto a cor do plano de fundo) aceitam um valor de dimensão, que é um número decimal seguido pela unidade. Por exemplo, `11.5dp` especifica 11,5 pixels independentes de densidade.

#### <a name="padding"></a>Preenchimento

`CardView` oferece cinco atributos de preenchimento para posicionar o conteúdo dentro do cartão. Você pode defini-los no seu XML de layout ou pode chamar métodos análogos em seu código:

[![Diagrama de atributos de preenchimento de CardView](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

Os atributos de preenchimento são explicados da seguinte maneira:

- `contentPadding`&ndash;Preenchimento interno entre as exibições filho do `CardView` e todas as bordas do cartão.

- `contentPaddingBottom`&ndash;Preenchimento interno entre as exibições filho do `CardView` e a borda inferior do cartão.

- `contentPaddingLeft`&ndash;Preenchimento interno entre as exibições filho do `CardView` e a borda esquerda do cartão.

- `contentPaddingRight`&ndash;Preenchimento interno entre as exibições filho do `CardView` e a borda direita do cartão.

- `contentPaddingTop`&ndash;Preenchimento interno entre as exibições filho do `CardView` e a borda superior do cartão.

Os atributos de preenchimento de conteúdo são relativos ao limite da área de conteúdo, e não a um determinado widget localizado na área de conteúdo.
Por exemplo, se `contentPadding` fosse suficientemente aumentado no aplicativo de visualização de fotos, o `CardView` cortaria a imagem e o texto mostrado no cartão.

#### <a name="elevation"></a>Elevação

`CardView` oferece dois atributos de elevação para controlar sua elevação e, como resultado, o tamanho de sua sombra:

[![Diagrama de atributos de elevação de CardView](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

Os atributos de elevação são explicados da seguinte maneira:

- `cardElevation`&ndash;A elevação do `CardView` (representa seu eixo Z).

- `cardMaxElevation`&ndash;O valor máximo da `CardView` elevação do.

Valores maiores de `cardElevation` aumentar o tamanho da sombra para que `CardView` pareçam flutuar para cima acima do plano de fundo. O `cardElevation` atributo também determina a ordem de desenho de exibições sobrepostas; ou seja, `CardView` será desenhado em outra exibição sobreposta com uma configuração de elevação mais alta e acima de quaisquer exibições sobrepostas com uma configuração de elevação mais baixa.
A `cardMaxElevation` configuração é útil para quando seu aplicativo altera a elevação dinamicamente &ndash; e impede que a sombra ultrapasse o limite definido com essa configuração.

#### <a name="corner-radius-and-background-color"></a>Raio do canto e cor do plano de fundo

`CardView` oferece atributos que você pode usar para controlar seu raio de canto e sua cor de plano de fundo. Essas duas propriedades permitem alterar o estilo geral do `CardView` :

[![Diagrama dos atributos de cor do plano de fundo e da opção do canto do CardView](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

Esses atributos são explicados da seguinte maneira:

- `cardCornerRadius`&ndash;O raio do canto de todos os cantos do `CardView` .

- `cardBackgroundColor`&ndash;A cor do plano de fundo do `CardView` .

Neste diagrama, `cardCornerRadius` é definido como um 10DP mais arredondado e `cardBackgroundColor` é definido como `"#FFFFCC"` (amarelo claro).

## <a name="compatibility"></a>Compatibilidade

Você pode usar `CardView` versões do Android anteriores ao android 5,0 pirulito. Como `CardView` o faz parte da biblioteca de suporte do Android v7, você pode usar o `CardView` com o Android 2,1 (API nível 7) e superior.
No entanto, você deve instalar o `Xamarin.Android.Support.v7.CardView` pacote conforme descrito em [requisitos](#requirements), acima.

`CardView` exibe um comportamento ligeiramente diferente nos dispositivos antes da pirulito (API nível 21):

- `CardView` usa uma implementação de sombra programática que adiciona um preenchimento adicional.

- `CardView` Não corta exibições filhas que interseccionam com os  `CardView` cantos arredondados.

Para ajudar no gerenciamento dessas diferenças de compatibilidade, o `CardView` fornece vários atributos adicionais que podem ser configurados em seu layout:

- `cardPreventCornerOverlap`&ndash;Defina esse atributo como `true` para adicionar o preenchimento quando seu aplicativo estiver em execução em versões anteriores do Android (API nível 20 e anterior). Essa configuração impede que `CardView` o conteúdo se cruze com os `CardView` cantos arredondados.

- `cardUseCompatPadding`&ndash;Defina esse atributo como `true` para adicionar o preenchimento quando seu aplicativo estiver sendo executado em versões do Android em ou superior à API nível 21. Se você quiser usar `CardView` em dispositivos de pré-autenticação e ele parecer o mesmo na pirulito (ou posterior), defina esse atributo como `true` . Quando esse atributo é habilitado, o `CardView` adiciona um preenchimento adicional para desenhar sombras quando ele é executado em dispositivos de pré-autenticação. Isso ajuda a superar as diferenças no preenchimento que são introduzidas quando implementações de sombra programática de pré-autenticação em vigor.

Para obter mais informações sobre como manter a compatibilidade com versões anteriores do Android, consulte [mantendo a compatibilidade](https://developer.android.com/training/material/compatibility.html).

## <a name="summary"></a>Resumo

Este guia introduziu o novo `CardView` widget incluído no Android 5,0 (pirulito). Ele demonstrou a `CardView` aparência padrão e explicou como personalizar `CardView` alterando sua elevação, arredondamento de canto, preenchimento de conteúdo e cor de plano de fundo. Ele listou os `CardView` atributos de layout (com diagramas de referência) e explicou como usar `CardView` dispositivos Android anteriores ao Android 5,0 pirulito. Para obter mais informações sobre `CardView` o, consulte a [referência de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html).

## <a name="related-links"></a>Links Relacionados

- [RecyclerView (exemplo)](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introdução à pirulito](~/android/platform/lollipop.md)
- [Referência de classe CardView](https://developer.android.com/reference/android/support/v7/widget/CardView.html)