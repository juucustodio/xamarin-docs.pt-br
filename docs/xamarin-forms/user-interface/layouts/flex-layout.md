---
title: O Xamarin.Forms FlexLayout
description: Use FlexLayout para empilhar ou encapsular uma coleção de exibições filho.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c7705304a6eda662095c1e2a57633bf74c90c6de
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370137"
---
# <a name="the-no-locxamarinforms-flexlayout"></a>O Xamarin.Forms FlexLayout

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_Use FlexLayout para empilhar ou encapsular uma coleção de exibições filho._

O Xamarin.Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) é novo na Xamarin.Forms versão 3,0. Ele se baseia no módulo de [layout de caixa flexível](https://www.w3.org/TR/css-flexbox-1/)CSS, normalmente conhecido como _layout flexível_ ou _caixa flexível_ , portanto chamado porque inclui muitas opções flexíveis para organizar os filhos no layout.

`FlexLayout` é semelhante ao Xamarin.Forms [`StackLayout`](~/xamarin-forms/user-interface/layouts/stacklayout.md) em que ele pode organizar seus filhos horizontal e verticalmente em uma pilha. No entanto, o `FlexLayout` também é capaz de encapsular seus filhos se houver muitos para caber em uma única linha ou coluna, além de ter muitas opções de orientação, alinhamento e adaptação a vários tamanhos de tela.

`FlexLayout` deriva de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) e herda uma [`Children`](xref:Xamarin.Forms.Layout`1.Children) Propriedade do tipo `IList<View>` .

`FlexLayout` define seis propriedades públicas vinculáveis e cinco propriedades vinculáveis anexadas que afetam o tamanho, a orientação e o alinhamento de seus elementos filho. (Se você não estiver familiarizado com as propriedades vinculáveis anexadas, consulte o artigo **[Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)**.) Essas propriedades são descritas detalhadamente nas seções abaixo sobre **[as propriedades vinculáveis em detalhes](#the-bindable-properties-in-detail)** e **[as propriedades vinculáveis anexadas em detalhes](#the-attached-bindable-properties-in-detail)**. No entanto, este artigo começa com uma seção sobre alguns **[cenários de uso comuns](#common-usage-scenarios)** do `FlexLayout` que descreve muitas dessas propriedades de forma mais INFORMS. Em direção ao final do artigo, você verá como combinar `FlexLayout` com folhas de [estilo CSS](~/xamarin-forms/user-interface/styles/css/index.md).

## <a name="common-usage-scenarios"></a>Cenários de uso comuns

O programa de exemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** contém várias páginas que demonstram alguns usos comuns do `FlexLayout` e permitem que você experimente suas propriedades.

### <a name="using-flexlayout-for-a-simple-stack"></a>Usando o FlexLayout para uma pilha simples

A página **pilha simples** mostra como o `FlexLayout` pode substituir por um `StackLayout` , mas com marcação mais simples. Tudo neste exemplo é definido na página XAML. O `FlexLayout` contém quatro filhos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

Aqui está a página em execução no iOS, no Android e no Plataforma Universal do Windows:

[![A página pilha simples](flex-layout-images/SimpleStack.png "A página pilha simples")](flex-layout-images/SimpleStack-Large.png#lightbox)

Três propriedades de `FlexLayout` são mostradas no arquivo **SimpleStackPage. XAML** :

- A [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propriedade é definida como um valor da [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) enumeração. O padrão é `Row`. Definir a propriedade como `Column` faz com que os filhos do `FlexLayout` sejam organizados em uma única coluna de itens.

    Quando os itens em um `FlexLayout` são organizados em uma coluna, o `FlexLayout` é considerado um eixo vertical _principal_ e um _eixo cruzado_ horizontal.

- A [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propriedade é do tipo [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) e especifica como os itens são alinhados no eixo cruzado. A `Center` opção faz com que cada item seja centralizado horizontalmente.

    Se você estava usando um `StackLayout` em vez de um `FlexLayout` para essa tarefa, você centralizaria todos os itens atribuindo a `HorizontalOptions` propriedade de cada item ao `Center` . A `HorizontalOptions` propriedade não funciona para filhos de um `FlexLayout` , mas a `AlignItems` propriedade única realiza a mesma meta. Se necessário, você pode usar a `AlignSelf` propriedade vinculável anexada para substituir a `AlignItems` propriedade para itens individuais:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Com essa alteração, essa `Label` será posicionada na borda esquerda do `FlexLayout` quando a ordem de leitura for da esquerda para a direita.

- A [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriedade é do tipo [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) e especifica como os itens são organizados no eixo principal. A `SpaceEvenly` opção aloca todos os espaços verticais restantes igualmente entre todos os itens e acima do primeiro item e abaixo do último item.

    Se você estivesse usando um `StackLayout` , precisaria atribuir a `VerticalOptions` propriedade de cada item ao `CenterAndExpand` para obter um efeito semelhante. Mas a `CenterAndExpand` opção alocaria duas vezes mais espaço entre cada item do que antes do primeiro item e depois do último item. Você pode imitar a `CenterAndExpand` opção de `VerticalOptions` definindo a `JustifyContent` propriedade de `FlexLayout` como `SpaceAround` .

Essas `FlexLayout` Propriedades são discutidas em mais detalhes na seção **[as propriedades vinculáveis em detalhes](#the-bindable-properties-in-detail)** abaixo.

### <a name="using-flexlayout-for-wrapping-items"></a>Usando FlexLayout para encapsular itens

A página de **disposição de fotos** do exemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** demonstra como o `FlexLayout` pode encapsular seus filhos em linhas ou colunas adicionais. O arquivo XAML instancia o `FlexLayout` e atribui duas propriedades a ele:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

A `Direction` Propriedade disso `FlexLayout` não é definida, portanto, ela tem a configuração padrão de `Row` , o que significa que os filhos são organizados em linhas e o eixo principal é horizontal.

A [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) propriedade é de um tipo de enumeração [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) . Se houver muitos itens para caber em uma linha, essa configuração de propriedade fará com que os itens sejam quebrados para a próxima linha.

Observe que o `FlexLayout` é um filho de um `ScrollView` . Se houver muitas linhas para caber na página, o `ScrollView` terá uma `Orientation` propriedade padrão de `Vertical` e permitirá a rolagem vertical.

A `JustifyContent` Propriedade aloca espaço sobra no eixo principal (o eixo horizontal) para que cada item seja circundado pela mesma quantidade de espaço em branco.

O arquivo code-behind acessa uma coleção de fotos de exemplo e as adiciona à `Children` coleção de `FlexLayout` :

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

Aqui está o programa em execução, rola progressivamente de cima para baixo:

[![A página de disposição da foto](flex-layout-images/PhotoWrapping.png "A página de disposição da foto")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Layout de página com FlexLayout

Há um layout padrão no design da Web chamado de o [_Grail Santa_](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) , pois é um formato de layout muito desejável, mas, muitas vezes, difícil de entender com perfeição. O layout consiste em um cabeçalho na parte superior da página e um rodapé na parte inferior, que se estendem para a largura total da página. Ocupar o centro da página é o conteúdo principal, mas geralmente com um menu de coluna à esquerda do conteúdo e informações complementares (às vezes chamado de área de _separar_ ) à direita. A [seção 5.4.1 da especificação de layout de caixa flexível do CSS](https://www.w3.org/TR/css-flexbox-1/#order-accessibility) descreve como o layout do Grail Santa pode ser percebido com uma caixa flexível.

A página de **layout do Grail Santa** do exemplo de **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** mostra uma implementação simples desse layout usando um `FlexLayout` aninhado em outro. Como essa página foi projetada para um telefone no modo retrato, as áreas à esquerda e à direita da área de conteúdo têm apenas 50 pixels de largura:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

Aqui está executando:

[![A página de layout do Grail Santa](flex-layout-images/HolyGrailLayout.png "A página de layout do Grail Santa")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

A navegação e as áreas de lado são renderizadas com um `BoxView` à esquerda e à direita.

O primeiro `FlexLayout` no arquivo XAML tem um eixo vertical principal e contém três filhos organizados em uma coluna. Esses são o cabeçalho, o corpo da página e o rodapé. O aninhado `FlexLayout` tem um eixo principal horizontal com três filhos organizados em uma linha.

Três propriedades vinculáveis anexadas são demonstradas neste programa:

- A `Order` propriedade vinculável anexada é definida no primeiro `BoxView` . Essa propriedade é um número inteiro com um valor padrão de 0. Você pode usar essa propriedade para alterar a ordem de layout. Geralmente, os desenvolvedores preferem que o conteúdo da página apareça na marcação antes dos itens de navegação e dos itens colados. Definir a `Order` propriedade no primeiro `BoxView` como um valor menor que seus irmãos faz com que ela apareça como o primeiro item na linha. Da mesma forma, você pode garantir que um item seja exibido por último definindo a `Order` propriedade com um valor maior do que seus irmãos.

- A `Basis` propriedade vinculável anexada é definida nos dois `BoxView` itens para dar a eles uma largura de 50 pixels. Essa propriedade é do tipo `FlexBasis` , uma estrutura que define uma propriedade estática do tipo `FlexBasis` chamado `Auto` , que é o padrão. Você pode usar `Basis` para especificar um tamanho de pixel ou uma porcentagem que indica a quantidade de espaço que o item ocupa no eixo principal. Ele é chamado de _base_ porque especifica um tamanho de item que é a base de todo o layout subsequente.

- A `Grow` propriedade é definida no aninhado `Layout` e no `Label` filho que representa o conteúdo. Essa propriedade é do tipo `float` e tem um valor padrão de 0. Quando definido como um valor positivo, todo o espaço restante ao longo do eixo principal é alocado para esse item e para irmãos com valores positivos de `Grow` . O espaço é alocado proporcionalmente aos valores, um pouco parecido com a especificação de estrela em um `Grid` .

    A primeira `Grow` Propriedade anexada é definida no aninhado `FlexLayout` , indicando que isso `FlexLayout` é para ocupar todo o espaço vertical não utilizado dentro do exterior `FlexLayout` . A segunda `Grow` Propriedade anexada é definida na `Label` representação do conteúdo, indicando que esse conteúdo é para ocupar todo o espaço horizontal não utilizado dentro do interior `FlexLayout` .

    Também há uma `Shrink` propriedade vinculável semelhante que você pode usar quando o tamanho dos filhos exceder o tamanho do `FlexLayout` , mas o encapsulamento não é desejado.

### <a name="catalog-items-with-flexlayout"></a>Itens de catálogo com FlexLayout

A página de **itens de catálogo** no exemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** é semelhante ao [exemplo 1 na seção 1,1 da especificação da caixa de layout flexível do CSS](https://www.w3.org//TR/css-flexbox-1/#overview) , exceto pelo fato de que ela exibe uma série de imagens e descrições horizontalmente roláveis de três Monkeys:

[![A página de itens do catálogo](flex-layout-images/CatalogItems.png "A página de itens do catálogo")](flex-layout-images/CatalogItems-Large.png#lightbox)

Cada um dos três Monkeys é um `FlexLayout` contido em um `Frame` que recebe uma altura e largura explícitas, e que também é um filho de maior `FlexLayout` . Nesse arquivo XAML, a maioria das propriedades dos `FlexLayout` filhos é especificada em estilos, tudo, exceto um, que é um estilo implícito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

O estilo implícito para o `Image` inclui configurações de duas propriedades vinculáveis anexadas de `Flexlayout` :

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

A `Order` configuração de &ndash; 1 faz com que o `Image` elemento seja exibido primeiro em cada uma das `FlexLayout` exibições aninhadas, independentemente de sua posição dentro da coleção de filhos. A `AlignSelf` propriedade de `Center` faz com que a `Image` seja centralizada dentro do `FlexLayout` . Isso substitui a configuração da `AlignItems` propriedade, que tem um valor padrão de `Stretch` , o que significa que `Label` os `Button` filhos e são ampliados para a largura total do `FlexLayout` .

Em cada uma das três `FlexLayout` exibições, um espaço em branco `Label` precede o `Button` , mas tem uma `Grow` configuração de 1. Isso significa que todo o espaço vertical extra é alocado para este em branco `Label` , o que efetivamente envia o `Button` para o fim.

## <a name="the-bindable-properties-in-detail"></a>As propriedades vinculáveis em detalhes

Agora que você viu alguns aplicativos comuns do `FlexLayout` , as propriedades do `FlexLayout` podem ser exploradas mais detalhadamente.
`FlexLayout` define seis propriedades vinculáveis que você define no `FlexLayout` próprio, no código ou no XAML, para controlar a orientação e o alinhamento. (Uma dessas propriedades, [`Position`](xref:Xamarin.Forms.FlexLayout.Position) , não é abordada neste artigo.)

Você pode experimentar as cinco propriedades vinculáveis restantes usando a página **experimento** do exemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** . Esta página permite adicionar ou remover filhos de um `FlexLayout` e definir combinações das cinco propriedades vinculáveis. Todos os filhos de `FlexLayout` são `Label` exibições de várias cores e tamanhos, com a `Text` propriedade definida como um número correspondente à sua posição na `Children` coleção.

Quando o programa é iniciado, cinco `Picker` exibições exibem os valores padrão dessas cinco `FlexLayout` Propriedades. O em `FlexLayout` direção à parte inferior da tela contém três filhos:

[![A página experimento: padrão](flex-layout-images/ExperimentDefault.png "A página de experimento-padrão")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Cada uma das `Label` exibições tem um plano de fundo cinza que mostra o espaço alocado para aquela `Label` dentro do `FlexLayout` . O plano de fundo do `FlexLayout` próprio é azul-bebê. Ele ocupa toda a área inferior da página, com exceção de uma pequena margem à esquerda e à direita.

### <a name="the-direction-property"></a>A propriedade Direction

A [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propriedade é do tipo [`FlexDirection`](xref:Xamarin.Forms.FlexDirection) , uma enumeração com quatro membros:

- `Column`
- `ColumnReverse` (ou "Column-Reverse" em XAML)
- `Row`, o padrão
- `RowReverse` (ou "Row-Reverse" em XAML)

Em XAML, você pode especificar o valor dessa propriedade usando os nomes de membro de enumeração em letras minúsculas, maiúsculas ou letras mistas, ou pode usar duas cadeias de caracteres adicionais mostradas entre parênteses que são iguais aos indicadores de CSS. (As cadeias de caracteres "Column-Reverse" e "Row-Reverse" são definidas na [`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter) classe usada pelo analisador XAML.)

Aqui está a página de **experimento** mostrando (da esquerda para a direita), a direção `Row` , a `Column` direção e a `ColumnReverse` direção:

[![A página de experimento: direção](flex-layout-images/ExperimentDirection.png "A direção da página do experimento")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Observe que, para as `Reverse` Opções, os itens começam na parte direita ou inferior.

### <a name="the-wrap-property"></a>A propriedade Wrap

A [`Wrap`](xref:Xamarin.Forms.FlexLayout.Wrap) propriedade é do tipo [`FlexWrap`](xref:Xamarin.Forms.FlexWrap) , uma enumeração com três membros:

- `NoWrap`, o padrão
- `Wrap`
- `Reverse` (ou "wrap-REVERT" em XAML)

Da esquerda para a direita, essas telas mostram `NoWrap` as `Wrap` `Reverse` Opções e para 12 filhos:

[![A página experimento: Wrap](flex-layout-images/ExperimentWrap.png "A página do experimento-encapsulamento")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Quando a `Wrap` propriedade é definida como `NoWrap` e o eixo principal é restrito (como neste programa) e o eixo principal não é largo ou alto o suficiente para se ajustar a todos os filhos, as `FlexLayout` tentativas de tornar os itens menores, como demonstra a captura de tela do Ios. Você pode controlar a redução dos itens com a [`Shrink`](#the-shrink-property) propriedade vinculável anexada.

### <a name="the-justifycontent-property"></a>A propriedade JustifyContent

A [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriedade é do tipo [`FlexJustify`](xref:Xamarin.Forms.FlexJustify) , uma enumeração com seis membros:

- `Start` (ou "Flex-Start" em XAML), o padrão
- `Center`
- `End` (ou "Flex-end" em XAML)
- `SpaceBetween` (ou "espaço entre" em XAML)
- `SpaceAround` (ou "espaçamento" em XAML)
- `SpaceEvenly`

Essa propriedade especifica como os itens são espaçados no eixo principal, que é o eixo horizontal neste exemplo:

[![A página de experimento: justificar conteúdo](flex-layout-images/ExperimentJustifyContent.png "A página de teste – justificar conteúdo")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

Em todas as três capturas de tela, a `Wrap` propriedade é definida como `Wrap` . O `Start` padrão é mostrado na captura de tela anterior do Android. A captura de tela do iOS aqui mostra a `Center` opção: todos os itens são movidos para o centro. As três outras opções que começam com a palavra `Space` alocam o espaço extra não ocupado pelos itens. `SpaceBetween` aloca o espaço igualmente entre os itens; `SpaceAround` coloca o espaço igual em cada item, enquanto coloca o mesmo `SpaceEvenly` espaço entre cada item e antes do primeiro item e depois do último item na linha.

### <a name="the-alignitems-property"></a>A propriedade AlignItems

A [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propriedade é do tipo [`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) , uma enumeração com quatro membros:

- `Stretch`, o padrão
- `Center`
- `Start` (ou "Flex-Start" em XAML)
- `End` (ou "Flex-end" em XAML)

Essa é uma das duas propriedades (a outra é [`AlignContent`](#the-aligncontent-property) ) que indica como os filhos são alinhados no eixo cruzado. Dentro de cada linha, os filhos são ampliados (conforme mostrado na captura de tela anterior) ou alinhados no início, no centro ou no final de cada item, conforme mostrado nas três capturas de tela a seguir:

[![A página de experimento: alinhar itens](flex-layout-images/ExperimentAlignItems.png "A página do experimento – alinhar itens")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

Na captura de tela do iOS, a parte superior de todos os filhos está alinhada. Nas capturas de tela do Android, os itens são verticalmente centralizados com base no filho mais alto. Na captura de tela da UWP, as partes inferiores de todos os itens são alinhadas.

Para qualquer item individual, a `AlignItems` configuração pode ser substituída pela [`AlignSelf`](#the-alignself-property) propriedade vinculável anexada.

### <a name="the-aligncontent-property"></a>A propriedade AlignContent

A [`AlignContent`](xref:Xamarin.Forms.FlexLayout.AlignContent) propriedade é do tipo [`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent) , uma enumeração com sete membros:

- `Stretch`, o padrão
- `Center`
- `Start` (ou "Flex-Start" em XAML)
- `End` (ou "Flex-end" em XAML)
- `SpaceBetween` (ou "espaço entre" em XAML)
- `SpaceAround` (ou "espaçamento" em XAML)
- `SpaceEvenly`

Como `AlignItems` , a `AlignContent` propriedade também alinha os filhos no eixo cruzado, mas afeta linhas ou colunas inteiras:

[![A página de experimento: alinhar conteúdo](flex-layout-images/ExperimentAlignContent.png "A página do experimento-alinhar conteúdo")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

Na captura de tela do iOS, ambas as linhas estão na parte superior; na captura de tela do Android, eles estão no centro; e na captura de tela da UWP, eles estão na parte inferior. As linhas também podem ser espaçadas de várias maneiras:

[![A página do experimento: alinhar o conteúdo 2](flex-layout-images/ExperimentAlignContent2.png "A página do experimento-alinhar o conteúdo 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

O `AlignContent` não tem efeito quando há apenas uma linha ou coluna.

## <a name="the-attached-bindable-properties-in-detail"></a>As propriedades vinculáveis anexadas em detalhes

`FlexLayout` define cinco propriedades vinculáveis anexadas. Essas propriedades são definidas em filhos do `FlexLayout` e pertencem somente a esse filho específico.

### <a name="the-alignself-property"></a>A propriedade AlignSelf

A [`AlignSelf`](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) propriedade vinculável anexada é do tipo [`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent) , uma enumeração com cinco membros:

- `Auto`, o padrão
- `Stretch`
- `Center`
- `Start` (ou "Flex-Start" em XAML)
- `End` (ou "Flex-end" em XAML)

Para qualquer filho individual do `FlexLayout` , essa configuração de propriedade substitui a [`AlignItems`](#the-alignitems-property) propriedade definida na `FlexLayout` própria. A configuração padrão de `Auto` significa usar a `AlignItems` configuração.

Para um `Label` elemento chamado `label` (ou exemplo), você pode definir a `AlignSelf` propriedade em um código como este:

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Observe que não há nenhuma referência ao `FlexLayout` pai do `Label` . Em XAML, você define a propriedade como esta:

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>A propriedade Order

A [`Order`](xref:Xamarin.Forms.FlexLayout.OrderProperty) propriedade é do tipo `int` . O valor padrão é 0.

A `Order` propriedade permite que você altere a ordem em que os filhos `FlexLayout` são organizados. Normalmente, os filhos de um `FlexLayout` são organizados é a mesma ordem em que aparecem na `Children` coleção. Você pode substituir essa ordem definindo a `Order` propriedade vinculável anexada a um valor inteiro diferente de zero em um ou mais filhos. `FlexLayout`Em seguida, o organiza seus filhos com base na configuração da `Order` propriedade em cada filho, mas os filhos com a mesma `Order` configuração são organizados na ordem em que aparecem na `Children` coleção.

### <a name="the-basis-property"></a>A propriedade base

A [`Basis`](xref:Xamarin.Forms.FlexLayout.BasisProperty) propriedade vinculável anexada indica a quantidade de espaço alocada a um filho do `FlexLayout` no eixo principal. O tamanho especificado pela `Basis` propriedade é o tamanho ao longo do eixo principal do pai `FlexLayout` . Portanto, `Basis` indica a largura de um filho quando os filhos são organizados em linhas ou a altura quando os filhos são organizados em colunas.

A `Basis` propriedade é do tipo [`FlexBasis`](xref:Xamarin.Forms.FlexBasis) , uma estrutura. O tamanho pode ser especificado em unidades independentes de dispositivo ou como uma porcentagem do tamanho do `FlexLayout` . O valor padrão da `Basis` propriedade é a propriedade estática `FlexBasis.Auto` , o que significa que a largura ou a altura solicitada do filho é usada.

No código, você pode definir a `Basis` propriedade de um `Label` nome `label` para as unidades independentes do dispositivo 40 como esta:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

O segundo argumento para o `FlexBasis` Construtor é nomeado `isRelative` e indica se o tamanho é relativo ( `true` ) ou absoluto ( `false` ). O argumento tem um valor padrão de `false` , portanto, você também pode usar o seguinte código:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Uma conversão implícita de `float` para `FlexBasis` é definida, para que você possa simplificar ainda mais a ti:

```csharp
FlexLayout.SetBasis(label, 40);
```

Você pode definir o tamanho como 25% do `FlexLayout` pai como este:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Esse valor fracionário deve estar no intervalo de 0 a 1.

Em XAML, você pode usar um número para um tamanho em unidades independentes de dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Ou você pode especificar uma porcentagem no intervalo de 0% a 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

A página **experimento base** do exemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** permite que você experimente a `Basis` propriedade. A página exibe uma coluna encapsulada de cinco `Label` elementos com cores de plano de fundo alternadas e de primeiro plano. Dois `Slider` elementos permitem que você especifique `Basis` valores para o segundo e o quarto `Label` :

[![A página experimento base](flex-layout-images/BasisExperiment.png "A página experimento base")](flex-layout-images/BasisExperiment-Large.png#lightbox)

A captura de tela do iOS à esquerda mostra os dois `Label` elementos que recebem alturas em unidades independentes de dispositivo. A tela do Android mostra que elas recebem alturas que são uma fração da altura total do `FlexLayout` . Se o `Basis` for definido em 100%, o filho será a altura do `FlexLayout` e será quebrado para a próxima coluna e ocupará toda a altura dessa coluna, como demonstra a captura de tela do UWP: ela aparece como se os cinco filhos fossem organizados em uma linha, mas eles são realmente organizados em cinco colunas.

### <a name="the-grow-property"></a>A propriedade Grow

A [`Grow`](xref:Xamarin.Forms.FlexLayout.GrowProperty) propriedade vinculável anexada é do tipo `int` . O valor padrão é 0 e o valor deve ser maior ou igual a 0.

A `Grow` Propriedade desempenha uma função quando a `Wrap` propriedade é definida como `NoWrap` e a linha de filhos tem uma largura total menor que a largura do `FlexLayout` ou a coluna de filhos tem uma altura menor do que o `FlexLayout` . A `Grow` propriedade indica como distribuir o espaço restante entre os filhos.

Na página de **experimento de crescimento** , cinco `Label` elementos de cores alternadas são organizados em uma coluna, e dois `Slider` elementos permitem ajustar a `Grow` propriedade da segunda e quarta `Label` . A captura de tela do iOS na extrema esquerda mostra as `Grow` propriedades padrão de 0:

[![A página de experimento de crescimento](flex-layout-images/GrowExperiment.png "A página de experimento de crescimento")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Se um filho receber um `Grow` valor positivo, esse filho ocupará todo o espaço restante, como demonstra a captura de tela do Android. Esse espaço também pode ser alocado entre dois ou mais filhos. Na captura de tela UWP, a `Grow` Propriedade do segundo `Label` é definida como 0,5, enquanto a `Grow` propriedade da quarta `Label` é 1,5, que fornece a quarta `Label` três vezes o espaço restante como o segundo `Label` .

Como a exibição filho usa esse espaço depende do tipo específico de filho. Para um `Label` , o texto pode ser posicionado dentro do espaço total do `Label` usando as propriedades `HorizontalTextAlignment` e `VerticalTextAlignment` .

### <a name="the-shrink-property"></a>A propriedade Shrink

A [`Shrink`](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) propriedade vinculável anexada é do tipo `int` . O valor padrão é 1 e o valor deve ser maior ou igual a 0.

A `Shrink` Propriedade desempenha uma função quando a `Wrap` propriedade é definida como `NoWrap` e a largura agregada de uma linha de filhos é maior que a largura do `FlexLayout` , ou a altura agregada de uma única coluna de filhos é maior do que a altura do `FlexLayout` . Normalmente `FlexLayout` , o exibirá esses filhos restringindo seus tamanhos. A `Shrink` propriedade pode indicar quais filhos recebem prioridade quando são exibidos em seus tamanhos completos.

A página **experimento de redução** cria um `FlexLayout` com uma única linha de cinco `Label` filhos que exigem mais espaço do que a `FlexLayout` largura. A captura de tela do iOS à esquerda mostra todos os `Label` elementos com valores padrão de 1:

[![A página de teste de redução](flex-layout-images/ShrinkExperiment.png "A página de teste de redução")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Na captura de tela do Android, o `Shrink` valor da segunda `Label` é definido como 0 e `Label` é exibido em sua largura total. Além disso, o quarto `Label` é dado um `Shrink` valor maior que um e reduzido. A captura de tela do UWP mostra os dois `Label` elementos que recebem um `Shrink` valor de 0 para permitir que eles sejam exibidos em seu tamanho total, se isso for possível.

Você pode definir os `Grow` valores e `Shrink` para acomodar situações em que os tamanhos filho agregados às vezes podem ser menores ou, às vezes, maiores que o tamanho do `FlexLayout` .

## <a name="css-styling-with-flexlayout"></a>Estilo de CSS com FlexLayout

Você pode usar o recurso de [estilo de CSS](~/xamarin-forms/user-interface/styles/css/index.md) introduzido com Xamarin.Forms 3,0 em conexão com o `FlexLayout` . A página de **itens do catálogo CSS** do exemplo **[FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** duplica o layout da página de **itens do catálogo** , mas com uma folha de estilos CSS para muitos dos estilos:

[![A página de itens do catálogo CSS](flex-layout-images/CssCatalogItems.png "A página de itens do catálogo CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

O arquivo **CatalogItemsPage. XAML** original tem cinco `Style` definições em sua `Resources` seção com 15 `Setter` objetos. No arquivo **CssCatalogItemsPage. XAML** , que foi reduzido para duas `Style` definições com apenas quatro `Setter` objetos. Esses estilos complementam a folha de estilos CSS para propriedades que o Xamarin.Forms recurso de estilo de CSS atualmente não suporta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

A folha de estilos CSS é referenciada na primeira linha da `Resources` seção:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Observe também que dois elementos em cada um dos três itens incluem `StyleClass` configurações:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Eles se referem aos seletores na folha de estilos **CatalogItemsStyles. css** :

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

Várias `FlexLayout` propriedades vinculáveis anexadas são referenciadas aqui. No `label.empty` seletor, você verá o `flex-grow` atributo, que cria um estilo vazio `Label` para fornecer algum espaço em branco acima do `Button` . O `image` seletor contém um `order` atributo e um `align-self` atributo, os quais correspondem às `FlexLayout` propriedades vinculáveis anexadas.

Você viu que pode definir as propriedades diretamente no `FlexLayout` e pode definir propriedades vinculáveis anexadas nos filhos de um `FlexLayout` . Ou, você pode definir essas propriedades indiretamente usando estilos tradicionais baseados em XAML ou estilos CSS. O que é importante é saber e entender essas propriedades. Essas propriedades são o que torna o `FlexLayout` verdadeiramente flexível.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout com Xamarin. University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin.Forms vídeo de layout flexível 3,0**

## <a name="related-links"></a>Links relacionados

- [FlexLayoutDemos](/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)