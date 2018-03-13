---
title: Desempenho do Xamarin.Forms
description: "Há muitas técnicas para aumentar o desempenho de aplicativos Xamarin.Forms. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo. Esse artigo descreve e aborda essas técnicas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: a750167cb9e6bde3a4a9abe11c5386497f9a12ae
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinforms-performance"></a>Desempenho do Xamarin.Forms

_Há muitas técnicas para aumentar o desempenho de aplicativos Xamarin.Forms. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo. Esse artigo descreve e aborda essas técnicas._

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evoluir 2016: como otimizar o desempenho do aplicativo com o Xamarin.Forms**

## <a name="overview"></a>Visão geral

O baixo desempenho de aplicativo se apresenta de várias maneiras. Ele pode fazer com que o aplicativo pareça não responder, deixar a rolagem lenta ou reduzir a vida útil da bateria. No entanto, a otimização do desempenho engloba mais do que apenas a implementação de um código eficiente. A experiência do usuário quanto ao desempenho do aplicativo também deve ser considerada. Por exemplo, garantir que as operações sejam executadas sem impedir o usuário de realizar outras atividades pode ajudar a melhorar a experiência do usuário.

Há várias técnicas para aumentar o desempenho, bem como o desempenho percebido, de um aplicativo Xamarin.Forms. Entre elas estão:

- [Habilitar o compilador de XAML](#xamlc)
- [Escolher o layout correto](#correctlayout)
- [Habilitar a Compactação de Layout](#layoutcompression)
- [Usar os Renderizadores Rápidos](#fastrenderers)
- [Reduzir associações desnecessárias](#databinding)
- [Otimizar o desempenho do layout](#optimizelayout)
- [Otimizar o desempenho da ListView](#optimizelistview)
- [Otimizar os recursos de imagem](#optimizeimages)
- [Reduzir o tamanho da árvore visual](#visualtree)
- [Reduzir o tamanho do dicionário de recursos do aplicativo](#resourcedictionary)
- [Uso do padrão de renderizador personalizado](#rendererpattern)

> [!NOTE]
>  Antes de ler esse artigo, você deve primeiro ler [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md), que discute técnicas que não são específicas a uma plataforma para melhorar o uso de memória e o desempenho de aplicativos criados usando a plataforma Xamarin.

<a name="xamlc" />

## <a name="enable-the-xaml-compiler"></a>Habilitar o compilador de XAML

Opcionalmente, XAML pode ser compilado direto na IL (linguagem intermediária) com o compilador XAML (XAMLC). XAMLC oferece vários benefícios:

- Executa verificação de tempo de compilação de XAML, notificando o usuário de quaisquer erros.
- Elimina parte da carga e do tempo de instanciação para elementos XAML.
- Ajuda a reduzir o tamanho do arquivo do assembly final não incluindo mais arquivos .XAML.

O XAMLC é desabilitado por padrão para garantir a compatibilidade com versões anteriores. No entanto, pode ser habilitado nos níveis de classe e do assembly. Para saber mais, consulte [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).

<a name="correctlayout" />

## <a name="choose-the-correct-layout"></a>Escolher o layout correto

Um layout que é capaz de exibir vários filhos, mas que tem apenas um único filho, é um desperdício. Por exemplo, o seguinte exemplo de código mostra um [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) com um único filho:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <StackLayout>
            <Image Source="waterfront.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Isso é um desperdício e o elemento [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) deve ser removido, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <Image Source="waterfront.jpg" />
    </ContentPage.Content>
</ContentPage>
```

Além disso, não tente reproduzir a aparência de um layout específico usando combinações de outros layouts, pois isso resulta na execução de cálculos de layout desnecessários. Por exemplo, não tente reproduzir um layout [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) usando uma combinação de instâncias [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). O código a seguir mostra um exemplo dessa má prática:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" />
                <Entry Placeholder="Enter your name" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Age:" />
                <Entry Placeholder="Enter your age" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Occupation:" />
                <Entry Placeholder="Enter your occupation" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Address:" />
                <Entry Placeholder="Enter your address" />
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Isso é um desperdício porque cálculos de layout desnecessário são executados. Em vez disso, o layout desejado pode ser melhor obtido usando um [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/), conforme mostra o exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="100" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
            </Grid.RowDefinitions>
            <Label Text="Name:" />
            <Entry Grid.Column="1" Placeholder="Enter your name" />
            <Label Grid.Row="1" Text="Age:" />
            <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
            <Label Grid.Row="2" Text="Occupation:" />
            <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
            <Label Grid.Row="3" Text="Address:" />
            <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

<a name="layoutcompression" />

## <a name="enable-layout-compression"></a>Habilitar a Compactação de Layout

A compactação de layout remove os layouts especificados da árvore visual, em uma tentativa de melhorar o desempenho de renderização da página. O benefício de desempenho que isso oferece varia dependendo da complexidade de uma página, da versão do sistema operacional que está sendo usado e do dispositivo no qual o aplicativo está sendo executado. No entanto, os maiores ganhos de desempenho serão observados em versões mais antigas. Para obter mais informações, confira [Layout de Compactação](~/xamarin-forms/user-interface/layouts/layout-compression.md).

<a name="fastrenderers" />

## <a name="use-fast-renderers"></a>Usar os Renderizadores Rápidos

Os renderizadores rápidos reduzem os custos de renderização e inflação dos controles Xamarin.Forms no Android nivelando a hierarquia resultante de controle nativo. Isso aprimora o desempenho criando menos objetos, o que resulta em uma árvore visual menos complexa e em menos uso de memória. Para obter mais informações, veja [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md).

<a name="databinding" />

## <a name="reduce-unnecessary-bindings"></a>Reduzir associações desnecessárias

Não use associações para conteúdo que pode ser facilmente definido estaticamente. Não há nenhuma vantagem em associar dados que não precisam ser associados, pois associações não são econômicas. Por exemplo, a configuração `Button.Text = "Accept"` tem menos sobrecarga do que a associação [`Button.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/) para uma propriedade ViewModel `string` com valor “Aceitar”.

<a name="optimizelayout" />

## <a name="optimize-layout-performance"></a>Otimizar o desempenho do layout

O Xamarin.Forms 2 introduziu um mecanismo de layout otimizado que afeta as atualizações de layout. Para obter o melhor desempenho possível do layout, siga estas diretrizes:

- Reduzir a profundidade das hierarquias de layout especificando os valores de propriedade [`Margin`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/), permitindo a criação de layouts com menos exibições de encapsulamento. Para saber mais, consulte [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Ao usar um [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/), tente garantir que o menor número de linhas e colunas possível seja definido para o tamanho [`Auto`](https://developer.xamarin.com/api/property/Xamarin.Forms.GridLength.Auto/). Cada linha ou coluna dimensionada automaticamente fará o mecanismo de layout realizar cálculos de layout adicionais. Em vez disso, use linhas e colunas de tamanho fixo, se possível. Como alternativa, defina linhas e colunas para ocupar uma quantidade proporcional de espaço com o valor de enumeração [`GridUnitType.Star`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Star/), desde que a árvore pai siga essas diretrizes de layout.
- Não defina as propriedades [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) e [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) de um layout, a menos que necessário. Os valores padrão de [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) e [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/) permitem a melhor otimização de layout. Alterar essas propriedades tem um custo e consome memória, mesmo ao configurá-las como os valores padrão.
- Evite usar um [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) sempre que possível. Isso resultará em a CPU precisar realizar significativamente mais trabalho.
- Ao usar um [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/), evite usar a propriedade [`AbsoluteLayout.AutoSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/) sempre que possível.
- Ao usar um [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), garanta que apenas um filho seja definido como [`LayoutOptions.Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/). Essa propriedade garante que o filho especificado ocupe o maior espaço que o `StackLayout` pode dar a ele e é um desperdício executar esses cálculos mais de uma vez.
- Não chame nenhum dos métodos da classe [`Layout`](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), uma vez que eles resultam na execução de cálculos de layout de alto custo. Em vez disso, é provável que o comportamento de layout desejado possa ser obtido configurando as propriedades [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/). Como alternativa, subclasse da classe [`Layout<View>`](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) para obter o comportamento de layout desejado.
- Não atualize nenhuma instância [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) com mais frequência do que o necessário, pois a alteração do tamanho do rótulo pode resultar no recálculo de todo o layout de tela.
- Não defina a propriedade [`Label.VerticalTextAlignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/), a menos que necessário.
- Defina o [`LineBreakMode`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/) de quaisquer instâncias [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) como [`NoWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/) sempre que possível.

<a name="optimizelistview" />

## <a name="optimize-listview-performance"></a>Otimizar o desempenho da ListView

Ao usar um controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), há várias experiências de usuário que devem ser otimizadas:

- **Inicialização** – o intervalo de tempo que começa quando o controle é criado e termina quando itens são mostrados na tela.
- **Rolagem** – a capacidade de rolar pela lista e garantir que a interface do usuário não fique atrasada com relação a gestos de toque.
- **Interação** para adicionar, excluir e selecionar itens.

O controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) requer um aplicativo para fornecer dados e modelos de célula. Como isso é feito terá um grande impacto sobre o desempenho do controle. Para obter mais informações, consulte [Desempenho da ListView](~/xamarin-forms/user-interface/listview/performance.md).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Otimizar os recursos de imagem

Exibir recursos de imagem pode aumentar significativamente o volume de memória do aplicativo. Portanto, eles só devem ser criados quando necessário e devem ser liberados assim que o aplicativo não exigi-los mais. Por exemplo, se um aplicativo estiver exibindo uma imagem lendo seus dados de um fluxo, certifique-se de que esse fluxo seja criado somente quando necessário e liberado quando não for mais necessário. Isso pode ser feito criando o fluxo quando a página é criada ou quando o evento [`Page.Appearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) é acionado e, em seguida, descartando o fluxo quando o evento [`Page.Disappearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Disappearing/) é acionado.

Ao fazer o download de uma imagem para exibição com o método [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/), armazene em cache a imagem baixada garantindo que a propriedade [`UriImageSource.CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) esteja definida como `true`. Para saber mais, consulte [Trabalhando com imagens](~/xamarin-forms/user-interface/images.md).

Para saber mais, consulte [Otimizar recursos de imagem](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

<a name="visualtree" />

## <a name="reduce-the-visual-tree-size"></a>Reduzir o tamanho da árvore visual

Reduzir o número de elementos em uma página tornará a renderização da página mais rápida. Há duas técnicas principais para realizar essa tarefa. A primeira é ocultar elementos que não estão visíveis. A propriedade [`IsVisible`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) de cada elemento determina se o elemento deve fazer parte da árvore visual ou não. Portanto, se um elemento não estiver visível porque está oculto atrás de outros elementos, remova o elemento ou defina sua propriedade `IsVisible` como `false`.

A segunda técnica é remover elementos desnecessários. Por exemplo, o código a seguir mostra um layout da página que exibe uma série de elementos [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

```xaml
<ContentPage.Content>
    <StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Hello" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Welcome to the App!" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Downloading Data..." />
        </StackLayout>
    </StackLayout>
</ContentPage.Content>
```

O mesmo layout da página poderá ser mantido com uma contagem de elementos reduzida, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage.Content>
  <StackLayout Padding="20,20,0,0" Spacing="25">
    <Label Text="Hello" />
    <Label Text="Welcome to the App!" />
    <Label Text="Downloading Data..." />
  </StackLayout>
</ContentPage.Content>
```

<a name="resourcedictionary" />

## <a name="reduce-the-application-resource-dictionary-size"></a>Reduzir o tamanho do dicionário de recursos do aplicativo

Todos os recursos usados em todo o aplicativo devem ser armazenados no dicionário de recursos do aplicativo para evitar duplicação. Isso ajudará a reduzir a quantidade de XAML que precisa ser analisada em todo o aplicativo. O seguinte exemplo de código mostra o recurso `HeadingLabelStyle`, que é usado em todo o aplicativo e então é definido no dicionário de recursos do aplicativo:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
     </Application.Resources>
</Application>
```

No entanto, XAML específico de uma página não deve ser incluído no dicionário de recursos do aplicativo, uma vez que os recursos então serão analisados na inicialização do aplicativo, em vez de quando exigido por uma página. Se um recurso for usado por uma página que não seja a página de inicialização, ele deverá ser colocado no dicionário de recursos para essa página, ajudando, assim, a reduzir o XAML analisado quando o aplicativo é iniciado. O seguinte exemplo de código mostra o recurso `HeadingLabelStyle`, que está em apenas uma única página e então é definido no dicionário de recursos da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
     <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
     </ContentPage.Resources>
    <ContentPage.Content>
      ...
    </ContentPage.Content>
</ContentPage>

```

Para saber mais sobre os recursos de aplicativo, consulte [`Working with Styles`](~/xamarin-forms/user-interface/styles/index.md).

<a name="rendererpattern" />

## <a name="use-the-custom-renderer-pattern"></a>Uso do padrão de renderizador personalizado

A maioria das classes de renderizador expõe o método `OnElementChanged`, que é chamado quando um controle personalizado do Xamarin.Forms é criado para renderizar o controle nativo correspondente. Então, classes de renderizador personalizadas em cada classe de renderizador específica da plataforma substituem esse método para instanciar e personalizar o controle nativo. O método `SetNativeControl` é usado para instanciar o controle nativo e esse método também atribuirá a referência de controle à propriedade `Control`.

No entanto, em algumas circunstâncias, o método `OnElementChanged` pode ser chamado várias vezes. Portanto, para evitar perdas de memória, que podem ter um impacto no desempenho, é necessário ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. O controle deve ser configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado de desempenho eficiente que não sofra perdas de memória.

Para obter mais informações sobre renderizadores personalizados, consulte [Como personalizar controles em cada plataforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="summary"></a>Resumo

Esse artigo descreveu e discutiu técnicas para aumentar o desempenho dos aplicativos criados Xamarin.Forms. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.


## <a name="related-links"></a>Links relacionados

- [Desempenho de plataforma cruzada](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Desempenho de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Renderizadores Rápidos](~/xamarin-forms/internals/fast-renderers.md)
- [Compactação de Layout](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Exemplo de redimensionador de imagem do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/XamFormsImageResize/)
- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilation/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
