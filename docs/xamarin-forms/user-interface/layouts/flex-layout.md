---
title: O xamarin. Forms FlexLayout
description: Use FlexLayout para empilhamento ou encapsulando uma coleção de exibições filho.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 70e99e77cbb0bc32e920be2c8165e22007af478d
ms.sourcegitcommit: 864f47c4f79fa588b65ff7f721367311ff2e8f8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "64347212"
---
# <a name="the-xamarinforms-flexlayout"></a>O xamarin. Forms FlexLayout

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)

_Use FlexLayout para empilhamento ou encapsulando uma coleção de exibições filho._

O xamarin. Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) há de novo no xamarin. Forms versão 3.0. Ele se baseia na folha de estilos [módulo de Layout de caixa flexível](http://www.w3.org/TR/css-flexbox-1/), comumente conhecido como _flex layout_ ou _flex caixa_, assim chamada porque ele inclui muitas opções flexíveis para organizar filhos no layout.

`FlexLayout` é semelhante ao xamarin. Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) em que ele pode organizar seus filhos horizontalmente e verticalmente em uma pilha. No entanto, o `FlexLayout` também é capaz de quebra automática de seus filhos se houver muitas para caber em uma única linha ou coluna, e também tem muitas opções para a orientação, alinhamento e adaptar-se a vários tamanhos de tela.

`FlexLayout` deriva [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) e herda um [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) propriedade do tipo `IList<View>`.

`FlexLayout` define seis propriedades vinculáveis públicas e cinco propriedades vinculáveis anexadas que afetam o tamanho, orientação e o alinhamento dos elementos filho. (Se você não estiver familiarizado com as propriedades vinculáveis anexadas, consulte o artigo  **[propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)**.) Essas propriedades são descritas detalhadamente nas seções a seguir **[as propriedades vinculáveis detalhadamente](#bindable-properties)** e  **[as propriedades anexadas associáveis detalhadamente](#attached-properties)**. No entanto, este artigo começa com uma seção em algumas **[cenários comuns de uso](#common-scenarios)** de `FlexLayout` que descreve muitas dessas propriedades de modo mais informal. Até o final do artigo, você verá como combinar `FlexLayout` com [folhas de estilos CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>Cenários de uso comuns

O **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** programa de exemplo contém várias páginas que demonstram alguns usos comuns do `FlexLayout` e permite fazer experiências com suas propriedades.

### <a name="using-flexlayout-for-a-simple-stack"></a>Usando FlexLayout para uma pilha simple

O **simples pilha** página mostra como `FlexLayout` pode substituir por um `StackLayout` , mas com a marcação mais simples. Tudo o que neste exemplo é definido na página XAML. O `FlexLayout` contém quatro filhos:

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

Aqui está a página em execução no iOS, Android e plataforma Universal do Windows:

[![Página de pilha simples](flex-layout-images/SimpleStack.png "simples página de pilha")](flex-layout-images/SimpleStack-Large.png#lightbox)

Três propriedades do `FlexLayout` são mostrados na **SimpleStackPage.xaml** arquivo:

- O [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) estiver definida como um valor de [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection) enumeração. O padrão é `Row`. Definindo a propriedade como `Column` faz com que os filhos do `FlexLayout` sejam organizados em uma única coluna de itens.

    Quando itens de um `FlexLayout` são organizados em uma coluna, o `FlexLayout` é considerado como tendo uma vertical _eixo principal_ e um horizontal _cruzar eixo_.

- O [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) propriedade é do tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems) e especifica como os itens são alinhados no eixo transversal. O `Center` opção faz com que cada item centralizado horizontalmente.

    Se você estivesse usando um `StackLayout` em vez de uma `FlexLayout` para essa tarefa, você teria centralizar todos os itens por meio da atribuição a `HorizontalOptions` propriedade de cada item para `Center`. O `HorizontalOptions` propriedade não funciona para os filhos de um `FlexLayout`, mas a única `AlignItems` propriedade realiza o mesmo objetivo. Se você precisar, você pode usar o `AlignSelf` anexados a propriedade associável para substituir o `AlignItems` propriedade para itens individuais:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Com essa alteração, essa `Label` é posicionado na borda esquerda do `FlexLayout` quando a ordem de leitura é esquerda para a direita.

- O [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriedade é do tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)e especifica como os itens são organizados no eixo principal. O `SpaceEvenly` opção aloca todos os espaço vertical abandonado igualmente entre todos os itens e acima do primeiro item e abaixo do último item.

    Se você estivesse usando um `StackLayout`, você precisará atribuir a `VerticalOptions` propriedade de cada item para `CenterAndExpand` para conseguir um efeito semelhante. Mas o `CenterAndExpand` opção alocará duas vezes mais espaço entre cada item que antes do primeiro item e depois do último item. Você pode imitar a `CenterAndExpand` opção de `VerticalOptions` definindo o `JustifyContent` propriedade do `FlexLayout` para `SpaceAround`.

Eles `FlexLayout` propriedades são discutidas mais detalhadamente na seção **[as propriedades vinculáveis detalhadamente](#bindable-properties)** abaixo.

### <a name="using-flexlayout-for-wrapping-items"></a>Usando FlexLayout para quebra automática de itens

O **foto encapsulamento** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo demonstra como `FlexLayout` pode encapsular a seus filhos para linhas ou colunas adicionais. O arquivo XAML instancia o `FlexLayout` e atribui as duas propriedades:

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

O `Direction` propriedade deste `FlexLayout` não for definido, para que ele tenha a configuração padrão de `Row`, que significa que os filhos são organizados em linhas e o eixo principal é horizontal.

O [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) é de propriedade de um tipo de enumeração [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap). Se houver itens demais para caber em uma linha, a configuração dessa propriedade faz com que os itens quebre para a próxima linha.

Observe que o `FlexLayout` é um filho de um `ScrollView`. Se houver muitas linhas para caber na página, em seguida, a `ScrollView` tem um padrão `Orientation` propriedade de `Vertical` e permite a rolagem vertical.

O `JustifyContent` propriedade aloca espaço remanescente no eixo principal (o eixo horizontal), de modo que cada item fica entre a mesma quantidade de espaço em branco.

O arquivo code-behind acessa uma coleção de fotos de exemplo e adicioná-los para o `Children` coleção do `FlexLayout`:

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

Aqui está o programa em execução, progressivamente rolado de cima para baixo:

[![A página de quebra automática de foto](flex-layout-images/PhotoWrapping.png "a página de quebra automática de foto")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>Layout de página com FlexLayout

Há um layout padrão do design da web chamada a [ _Santo graal_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) porque ele é um formato de layout que é algo extremamente desejável, mas muitas vezes é difícil perceber com perfeição. O layout consiste em um cabeçalho na parte superior da página e rodapé na parte inferior, ambos os Estender até a largura total da página. Ocupar o centro da página é o conteúdo principal, mas geralmente com um menu em colunas à esquerda das informações de conteúdo e suplementares (às vezes chamado de um _separar_ área) à direita. [Seção 5.4.1 da especificação de Layout de caixa flexível CSS](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) descreve como o layout de Santo graal pode ser realizado com uma caixa flexível.

O **Layout Santo Graal** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo mostra uma implementação simples desse layout usando um `FlexLayout` aninhado em outro. Como essa página é projetada para um telefone no modo retrato, as áreas para a esquerda e direita da área de conteúdo são apenas 50 pixels de largura:

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

Aqui ele está em execução:

[![A página de Layout de Santo Graal](flex-layout-images/HolyGrailLayout.png "a página de Layout de busca Cálice sagrado")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

As áreas de navegação e aside são renderizadas com um `BoxView` à esquerda e direita.

A primeira `FlexLayout` o XAML arquivo tem um eixo principal vertical e contém três filhos organizados em uma coluna. Esses são o cabeçalho, o corpo da página e rodapé. Aninhada `FlexLayout` tem um eixo horizontal de principal com três filhos organizados em uma linha.

Três propriedades vinculáveis anexadas são demonstradas neste programa:

- O `Order` anexados a propriedade associável é definida no primeiro `BoxView`. Esta propriedade é um inteiro com um valor padrão de 0. Você pode usar essa propriedade para alterar a ordem de layout. Geralmente os desenvolvedores preferem o conteúdo da página apareça na marcação antes dos itens de navegação e separar itens. Definindo o `Order` propriedade na primeira `BoxView` para um valor menor do que seus outros irmãos faz com que ele seja exibido como o primeiro item na linha. Da mesma forma, você pode garantir que um item é exibido por último, definindo o `Order` propriedade para um valor maior do que seus irmãos.

- O `Basis` anexados a propriedade associável é definida nos dois `BoxView` itens para fornecer a eles uma largura de 50 pixels. Essa propriedade é do tipo `FlexBasis`, uma estrutura que define uma propriedade estática do tipo `FlexBasis` denominado `Auto`, que é o padrão. Você pode usar `Basis` para especificar um tamanho de pixel ou uma porcentagem que indica quanto espaço o item ocupa no eixo principal. Ele é chamado uma _base_ porque especifica um tamanho do item que é a base de layout subsequente.

- O `Grow` propriedade é definida em aninhada `Layout` e, no `Label` filho que representa o conteúdo. Essa propriedade é do tipo `float` e tem um valor padrão de 0. Quando definido como um valor positivo, todo o espaço restante no eixo principal é alocado para esse item e irmãos com valores positivos de `Grow`. O espaço é alocado proporcionalmente para os valores, um pouco semelhante a especificação de estrela em um `Grid`.

    A primeira `Grow` propriedade anexada é definida em aninhada `FlexLayout`, indicando que esse `FlexLayout` é ocupar todo o espaço vertical não utilizado dentro externo `FlexLayout`. A segunda `Grow` propriedade anexada é definida em de `Label` que representa o conteúdo, que indica que esse conteúdo é ocupar todo o espaço horizontal não utilizado dentro interno `FlexLayout`.

    Há também um semelhante `Shrink` anexados a propriedade associável que você pode usar quando o tamanho dos filhos excede o tamanho do `FlexLayout` mas encapsulamento não for desejado.

### <a name="catalog-items-with-flexlayout"></a>Itens de catálogo com FlexLayout

O **itens de catálogo** página na **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo é semelhante ao [1 do exemplo na seção 1.1 da especificação CSS Flex Layout caixa](http://www.w3.org/TR/css-flexbox-1/#overview), exceto pelo fato de que ele exibe uma série rolável horizontalmente de imagens e descrições dos três Monkeys (Macacos):

[![Página de itens de catálogo](flex-layout-images/CatalogItems.png "página de itens de catálogo")](flex-layout-images/CatalogItems-Large.png#lightbox)

Cada um de Monkeys três (Macacos) é um `FlexLayout` contido em um `Frame` que é determinado explícita de altura e largura e que também é um filho de uma maior `FlexLayout`. Nesse arquivo XAML, a maioria das propriedades do `FlexLayout` filhos são especificados em estilos, todos, exceto um dos quais é um estilo implícito:

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

O estilo implícito para o `Image` inclui as configurações de duas propriedades vinculáveis anexadas de `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

O `Order` configuração do &ndash;1 faz com que o `Image` elemento a ser exibido pela primeira vez em cada uma das aninhada `FlexLayout` modos de exibição, independentemente de sua posição dentro da coleção de filhos. O `AlignSelf` propriedade de `Center` faz com que o `Image` centralizado dentro de `FlexLayout`. Isso substitui a configuração do `AlignItems` propriedade, que tem um valor padrão de `Stretch`, o que significa que o `Label` e `Button` filhos são ampliados para a largura total do `FlexLayout`.

Dentro de cada um dos três `FlexLayout` modos de exibição, um espaço em branco `Label` precede a `Button`, mas tem um `Grow` configuração de 1. Isso significa que todo o espaço vertical extra é alocado para isso em branco `Label`, que efetivamente envia a `Button` na parte inferior.

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>As propriedades vinculáveis em detalhes

Agora que você já viu alguns aplicativos comuns `FlexLayout`, as propriedades de `FlexLayout` podem ser explorados mais detalhadamente.
`FlexLayout` define seis propriedades vinculáveis definida para o `FlexLayout` em si, em código ou XAML, a orientação do controle e o alinhamento. (Uma dessas propriedades, [ `Position` ](xref:Xamarin.Forms.FlexLayout.Position), não é abordado neste artigo.)

Você pode fazer experiências com cinco restantes propriedades vinculáveis usando o **experiências** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo. Esta página permite que você adicione ou remova os filhos de um `FlexLayout` e definir combinações das cinco propriedades associáveis. Todos os filhos do `FlexLayout` estão `Label` modos de exibição de várias cores e tamanhos, com o `Text` propriedade definida como um número correspondente à sua posição no `Children` coleção.

Quando o programa for iniciado, cinco `Picker` modos de exibição exibem os valores padrão desses cinco `FlexLayout` propriedades. O `FlexLayout` na parte inferior da tela contém três filhos:

[![A página de teste: Padrão](flex-layout-images/ExperimentDefault.png "padrão a página de teste:")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Cada um dos `Label` modos de exibição tem um plano de fundo cinza que mostra o espaço alocado para que `Label` dentro de `FlexLayout`. Plano de fundo do `FlexLayout` em si é azul-bebê. Ele ocupa a área inteira inferior da página, exceto para uma pequena margem à esquerda e direita.

<a name="direction" />

### <a name="the-direction-property"></a>A propriedade de direção

O [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) propriedade é do tipo [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection), uma enumeração com quatro membros:

- `Column`
- `ColumnReverse` (ou "reversão de coluna" em XAML)
- `Row`, o padrão
- `RowReverse` (ou "reversão de linha" em XAML)

No XAML, você pode especificar o valor dessa propriedade usando os nomes de membro de enumeração em letras minúsculas, letras maiusculas, ou maiusculas e minúsculas, ou você pode usar duas cadeias de caracteres adicionais entre parênteses são os mesmos que os indicadores CSS. (As cadeias de caracteres "coluna inverso" e "linha reversão" são definidas na [ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) classe usada pelo analisador XAML.)

Aqui está o **experimento** página mostrando (da esquerda para a direita), o `Row` direção, `Column` direção, e `ColumnReverse` direção:

[![A página de teste: Direção](flex-layout-images/ExperimentDirection.png "na página de teste - direção")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Observe que para o `Reverse` opções, os itens iniciam na parte direita ou inferior.

<a name="wrap" />

### <a name="the-wrap-property"></a>A propriedade de quebra automática de linha

O [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) propriedade é do tipo [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap), uma enumeração com três membros:

- `NoWrap`, o padrão
- `Wrap`
- `Reverse` (ou "reversão de quebra automática de linha" em XAML)

Da esquerda para direita, essas telas mostram a `NoWrap`, `Wrap` e `Reverse` opções para os filhos de 12:

[![A página de teste: Encapsular](flex-layout-images/ExperimentWrap.png "encapsular a página de teste:")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Quando o `Wrap` estiver definida como `NoWrap` eixo principal é restrito (como esse programa) e o eixo principal não está altas ou largas o suficiente para se ajustar todos os filhos, o `FlexLayout` faz com que os itens de menor, como a captura de tela do iOS Demonstra. Você pode controlar o shrinkness dos itens com o [ `Shrink` ](#shrink) propriedade associável anexada.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>A propriedade JustifyContent

O [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriedade é do tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify), uma enumeração com seis membros:

- `Start` (ou "flex-start" em XAML), o padrão
- `Center`
- `End` (ou "flex-end" em XAML)
- `SpaceBetween` (ou "espaço-entre" em XAML)
- `SpaceAround` (ou "espaço-around" em XAML)
- `SpaceEvenly`

Esta propriedade especifica como os itens são espaçados no eixo principal, que é o eixo horizontal neste exemplo:

[![A página de teste: Conteúdo de justificar](flex-layout-images/ExperimentJustifyContent.png "conteúdo de justificar a página de teste:")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

Em todos os três capturas de tela, o `Wrap` estiver definida como `Wrap`. O `Start` padrão é mostrado na captura de tela Android anterior. Mostra a captura de tela do iOS aqui o `Center` opção: todos os itens são movidos para o centro. As três outras opções começando com a palavra `Space` alocar o espaço extra não ocupado pelos itens. `SpaceBetween` aloca o espaço igualmente entre os itens; `SpaceAround` puts igual espaço ao redor de cada item, enquanto `SpaceEvenly` puts igual espaço entre cada item e antes do primeiro item e depois do último item na linha.

<a name="align-items" />

### <a name="the-alignitems-property"></a>A propriedade AlignItems

O [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) propriedade é do tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems), uma enumeração com quatro membros:

- `Stretch`, o padrão
- `Center`
- `Start` (ou "flex-start" em XAML)
- `End` (ou "flex-end" em XAML)

Essa é uma das duas propriedades (o outro sendo [ `AlignContent` ](#align-content)) que indica como os filhos são alinhados no eixo transversal. Em cada linha, os filhos são alongados (conforme mostrado na captura de tela anterior) ou alinhados no início, centro ou final de cada item, conforme mostrado nas capturas de três tela seguintes:

[![A página de teste: Alinhar itens](flex-layout-images/ExperimentAlignItems.png "na página de teste - alinhar itens")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

A captura de tela do iOS, as partes superiores de todos os filhos são alinhadas. No Android capturas de tela, os itens são centralizados verticalmente com base no filho mais alto. A captura de tela de UWP, a parte inferior de todos os itens é alinhados.

Para qualquer item individual, o `AlignItems` configuração pode ser substituída com o [ `AlignSelf` ](#align-self) propriedade associável anexada.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>A propriedade AlignContent

O [ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent) propriedade é do tipo [ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent), uma enumeração com sete membros:

- `Stretch`, o padrão
- `Center`
- `Start` (ou "flex-start" em XAML)
- `End` (ou "flex-end" em XAML)
- `SpaceBetween` (ou "espaço-entre" em XAML)
- `SpaceAround` (ou "espaço-around" em XAML)
- `SpaceEvenly`

Como o `AlignItems`, o `AlignContent` propriedade também alinha filhos no eixo transversal, mas afeta linhas ou colunas inteiras:

[![A página de teste: Alinhar o conteúdo](flex-layout-images/ExperimentAlignContent.png "alinhar o conteúdo a página de teste:")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

A captura de tela do iOS, ambas as linhas estão na parte superior; na captura de tela Android está no Centro de; e a captura de tela de UWP que eles estão na parte inferior. As linhas também podem ser espaçadas de várias maneiras:

[![A página de teste:  Alinhar conteúdo 2](flex-layout-images/ExperimentAlignContent2.png "na página de teste - alinhar conteúdo 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

O `AlignContent` não tem nenhum efeito quando há apenas uma linha ou coluna.

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>As propriedades anexadas associáveis em detalhes

`FlexLayout` define cinco propriedades vinculáveis anexadas. Essas propriedades são definidas nos filhos do `FlexLayout` e só pertencem a esse filho específico.

<a name="align-self" />

### <a name="the-alignself-property"></a>A propriedade AlignSelf

O [ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) anexados a propriedade associável é do tipo [ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent), uma enumeração com cinco membros:

- `Auto`, o padrão
- `Stretch`
- `Center`
- `Start` (ou "flex-start" em XAML)
- `End` (ou "flex-end" em XAML)

Para qualquer filho individual do `FlexLayout`, essa propriedade substituirá o [ `AlignItems` ](#align-items) propriedade definida no `FlexLayout` em si. A configuração padrão de `Auto` significa usar o `AlignItems` configuração.

Para um `Label` elemento denominado `label` (ou exemplo), você pode definir o `AlignSelf` propriedade no código como este:

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

Observe que não há nenhuma referência para o `FlexLayout` pai do `Label`. No XAML, você definir a propriedade como este:

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>A propriedade de ordem

O [ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty) propriedade é do tipo `int`. O valor padrão é 0.

O `Order` propriedade permite que você altere a ordem que os filhos do `FlexLayout` são organizados. Normalmente, os filhos de um `FlexLayout` são organizados é a mesma ordem que aparecem no `Children` coleção. Você pode substituir essa ordem, definindo o `Order` propriedade associável anexada a um valor inteiro diferente de zero em uma ou mais filhos. O `FlexLayout` , em seguida, organiza seus filhos com base na configuração do `Order` propriedade em cada filho, mas filhos com o mesmo `Order` configuração são organizados na ordem em que aparecem no `Children` coleção.

### <a name="the-basis-property"></a>A propriedade de base

O [ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty) anexados a propriedade associável indica a quantidade de espaço é alocado para um filho do `FlexLayout` no eixo principal. O tamanho especificado o `Basis` propriedade é o tamanho ao longo do eixo principal do pai `FlexLayout`. Portanto, `Basis` indica a largura de um filho quando os filhos são organizados em linhas ou a altura, quando os filhos são organizados em colunas.

O `Basis` propriedade é do tipo [ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis), uma estrutura. O tamanho pode ser especificado em qualquer um dos unidades independentes de dispositivo ou como uma porcentagem do tamanho do `FlexLayout`. O valor padrão de `Basis` é a propriedade estática `FlexBasis.Auto`, o que significa que o filho solicitado pelo largura ou altura é usada.

No código, você pode definir as `Basis` propriedade para um `Label` denominado `label` para 40 unidades independentes de dispositivo como este:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

O segundo argumento para o `FlexBasis` construtor é chamado `isRelative` e indica se o tamanho é relativo (`true`) ou absoluto (`false`). O argumento tem um valor padrão de `false`, portanto, você também pode usar o código a seguir:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Uma conversão implícita da `float` para `FlexBasis` for definida, portanto, você pode simplificar ainda mais:

```csharp
FlexLayout.SetBasis(label, 40);
```

Você pode definir o tamanho para 25% do `FlexLayout` pai como este:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Esse valor fracionário deve estar no intervalo de 0 a 1.

No XAML, você pode usar um número para um tamanho em unidades independentes de dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Ou você pode especificar uma porcentagem do intervalo de 0 a 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

O **base experiências** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** amostra permite que você experimentar o `Basis` propriedade. A página exibe uma coluna encapsulada de cinco `Label` elementos com alternando as cores de primeiro plano e plano de fundo. Duas `Slider` elementos permitem que você especifique `Basis` valores para o segundo e quarto `Label`:

[![Página de teste de base](flex-layout-images/BasisExperiment.png "página de teste de base")](flex-layout-images/BasisExperiment-Large.png#lightbox)

A captura de tela do iOS à esquerda mostra os dois `Label` elementos que estão sendo fornecidos alturas em unidades independentes de dispositivo. A tela Android mostra-las sendo dada alturas menos da metade da altura total do `FlexLayout`. Se o `Basis` é definida em 100%, em seguida, o filho é a altura do `FlexLayout`e passe para a próxima coluna e ocupa toda a altura da coluna, como demonstra a captura de tela UWP: Ele aparece como se os filhos de cinco são organizados em uma linha, mas, na verdade, são dispostos em cinco colunas.

### <a name="the-grow-property"></a>A propriedade de crescimento

O [ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty) anexados a propriedade associável é do tipo `int`. O valor padrão é 0 e o valor deve ser maior que ou igual a 0.

O `Grow` propriedade desempenha um papel quando o `Wrap` estiver definida como `NoWrap` e a linha de filhos tem uma largura total menor que a largura do `FlexLayout`, ou a coluna de filhos tem uma altura menor que o `FlexLayout`. O `Grow` propriedade indica como distribuir o espaço entre os filhos.

No **experimento crescer** página cinco `Label` elementos de cores alternadas são organizados em uma coluna e dois `Slider` elementos permitem que você ajuste a `Grow` propriedade do segundo e quarto `Label`. A captura de tela do iOS na extremidade esquerda mostra o padrão `Grow` propriedades 0:

[![A página de teste de crescimento](flex-layout-images/GrowExperiment.png "a página de teste a crescer")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Se qualquer um filho é fornecido um positivo `Grow` de valor, em seguida, esse filho ocupa todo o espaço restante, como demonstra a captura de tela do Android. Esse espaço também pode ser alocado entre dois ou mais filhos. A captura de tela de UWP, o `Grow` propriedade da segunda `Label` é definido como 0,5, enquanto o `Grow` propriedade do quarto `Label` é 1.5, que fornece a quarta `Label` três vezes mais do espaço de sobra como o segundo `Label`.

Como o modo de exibição filho usa esse espaço depende do tipo específico de filho. Para um `Label`, o texto pode ser posicionado dentro do espaço total do `Label` usando as propriedades `HorizontalTextAlignment` e `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>A propriedade de redução

O [ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) anexados a propriedade associável é do tipo `int`. O valor padrão é 1, e o valor deve ser maior que ou igual a 0.

O `Shrink` propriedade desempenha um papel quando o `Wrap` estiver definida como `NoWrap` e a largura de agregação de uma linha de filhos é maior que a largura do `FlexLayout`, ou a altura de agregação de uma única coluna de filhos é maior que o altura do `FlexLayout`. Normalmente o `FlexLayout` exibirá esses filhos por constricting seus tamanhos. O `Shrink` propriedade pode indicar quais filhas recebem prioridade em que estão sendo exibidos com seus tamanhos completos.

O **reduzir experimento** página cria uma `FlexLayout` com uma única linha de cinco `Label` filhos que exigem mais espaço do que o `FlexLayout` largura. A captura de tela do iOS à esquerda mostra todos os `Label` elementos com os valores padrão de 1:

[![Página de teste a redução](flex-layout-images/ShrinkExperiment.png "página de experimentar a redução")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

Na captura de tela Android, o `Shrink` valor para a segunda `Label` é definido como 0 e que `Label` é exibido na sua largura completa. Além disso, o quarto `Label` recebe um `Shrink` valor maior do que um e, em seguida, ele foi reduzido. Mostra a captura de tela UWP `Label` elementos que estão sendo fornecidos um `Shrink` valor de 0 para permitir que sejam exibidos em seu tamanho completo, se isso for possível.

Você pode definir ambos os `Grow` e `Shrink` valores para adaptar a situações em que os tamanhos de agregação filho, às vezes, podem ser menor que ou às vezes maior que o tamanho do `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Estilos da CSS com FlexLayout

Você pode usar o [estilos da CSS foram](~/xamarin-forms/user-interface/styles/css/index.md) recurso introduzido com o xamarin. Forms 3.0 na conexão com `FlexLayout`. O **itens de catálogo de CSS** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo duplica o layout do **itens de catálogo** página, mas com CSS folha de estilos para muitos dos estilos:

[![Página de itens do catálogo de CSS](flex-layout-images/CssCatalogItems.png "página de itens do catálogo de CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

O original **CatalogItemsPage.xaml** arquivo tem cinco `Style` definições em seu `Resources` seção com 15 `Setter` objetos. No **CssCatalogItemsPage.xaml** arquivo, que foi reduzido para dois `Style` definições com apenas quatro `Setter` objetos. Esses estilos complementam a folha de estilos CSS para propriedades que o recurso de estilo CSS do xamarin. Forms atualmente não dá suporte:

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

A folha de estilos CSS é referenciada na primeira linha do `Resources` seção:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Observe também que dois elementos em cada um dos três itens a incluem `StyleClass` configurações:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Elas fazem referência a seletores de **CatalogItemsStyles.css** folha de estilos:

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

Vários `FlexLayout` propriedades vinculáveis anexadas são referenciadas aqui. No `label.empty` seletor, você verá o `flex-grow` atributo, que define o estilo vazio `Label` para fornecer um espaço em branco acima a `Button`. O `image` seletor contém um `order` atributo e um `align-self` atributo, que correspondem aos `FlexLayout` associáveis propriedades anexadas.

Você já viu que você pode definir propriedades diretamente na `FlexLayout` e você pode definir propriedades vinculáveis anexadas nos filhos de um `FlexLayout`. Ou, você pode definir essas propriedades indiretamente usando estilos tradicionais baseado em XAML ou estilos CSS. O importante é saber e compreender essas propriedades. Essas propriedades são o que torna o `FlexLayout` realmente flexível.

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout com Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin. Forms 3.0 Flex Layout, por [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Links relacionados

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
