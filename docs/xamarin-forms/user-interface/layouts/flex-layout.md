---
title: O FlexLayout xamarin. Forms
description: Use FlexLayout para pilha ou uma coleção de exibições de filho de encapsulamento.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: bba5007acb54852b9427c57c26aba6358c4c5771
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
---
# <a name="the-xamarinforms-flexlayout"></a>O FlexLayout xamarin. Forms

_Use FlexLayout para pilha ou uma coleção de exibições de filho de encapsulamento._

O xamarin. Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) é novo no xamarin. Forms versão 3.0. Se baseia na folha de estilos [módulo de Layout de caixa flexível](http://www.w3.org/TR/css-flexbox-1/), geralmente conhecido como _flex layout_ ou _caixa flex_, assim chamada porque ela inclui várias opções flexíveis para organizar os filhos no layout.

`FlexLayout` é semelhante do xamarin. Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) em que ele pode organizar seus filhos horizontalmente e verticalmente em uma pilha. No entanto, o `FlexLayout` também é capaz de quebra automática de seus filhos se houver muito grande para caber em uma única linha ou coluna e também tem várias opções para a orientação, o alinhamento e adaptar para vários tamanhos de tela.

`FlexLayout` deriva [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) e herda um [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) propriedade do tipo `IList<View>`.

`FlexLayout` define seis propriedades vinculáveis públicas e cinco propriedades vinculáveis anexadas. (Se você não estiver familiarizado com as propriedades vinculáveis anexadas, consulte o artigo  **[propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)**.) Todas essas propriedades são descritas em detalhes nas seções a seguir na **[as propriedades vinculáveis aos seis](#bindable-properties)** e **[os cinco anexado propriedades vinculáveis](#attached-properties)**. No entanto, este artigo começa com uma seção de alguns **[aplicativos comuns](#common-applications)** de `FlexLayout` que mais informalmente descreve muitas dessas propriedades. No final do artigo, você verá como combinar `FlexLayout` com [folhas de estilo CSS](~/xamarin-forms/user-interface/styles/css/index.md).

<a name="common-applications" />

## <a name="common-applications"></a>Aplicativos comuns

O **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** programa de exemplo contém várias páginas que demonstrar usa alguns comuns de `FlexLayout` e permite fazer experiências com suas propriedades.

### <a name="using-flexlayout-for-a-simple-stack"></a>Usando FlexLayout para uma pilha simple

O **pilha simples** página mostra como `FlexLayout` pode substituir por um `StackLayout` , mas com marcação mais simples. Tudo neste exemplo é definido na página do XAML. O `FlexLayout` contém quatro filhos:

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

Aqui está a página em execução no iOS, Android e a plataforma Universal do Windows:

[![Página de pilha simples](flex-layout-images/SimpleStack.png "página de pilha simples")](flex-layout-images/SimpleStack-Large.png#lightbox)

Três propriedades de `FlexLayout` são mostrados no **SimpleStackPage.xaml** arquivo:

- O [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) propriedade é definida como um valor de [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection) enumeração. O padrão é `Row`. Definir a propriedade como `Column` faz com que os filhos do `FlexLayout` sejam organizados em uma única coluna de itens.

    Quando itens em uma `FlexLayout` são organizadas em uma coluna, o `FlexLayout` deve ter um vertical _eixo principal_ e um horizontal _cruzar eixo_.

- O [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) é de propriedade do tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems) e especifica como os itens são alinhados no eixo de cruz. O `Center` opção faz com que cada item centralizado horizontalmente.

    Se você estiver usando um `StackLayout` em vez de `FlexLayout` para esta tarefa, você deve centralizar todos os itens, atribuindo o `HorizontalOptions` propriedade de cada item para `Center`. O `HorizontalOptions` propriedade não funciona para os filhos de um `FlexLayout`, mas nesse único `AlignItems` propriedade realiza o mesmo objetivo. Se você precisar, você pode usar o `AlignSelf` anexado a propriedade associável para substituir o `AlignItems` propriedade para itens individuais:

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    Com essa alteração, esse `Label` é posicionado na borda esquerda do `FlexLayout` quando a ordem de leitura é esquerda para a direita.

- O [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) é de propriedade do tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)e especifica como os itens são organizados no eixo principal. O `SpaceEvenly` opção aloca todos os espaço vertical restante igualmente entre todos os itens e acima do primeiro item e abaixo do último item.

    Se você estiver usando um `StackLayout`, você precisará atribuir o `VerticalOptions` propriedade de cada item para `CenterAndExpand` para conseguir um efeito semelhante. Mas o `CenterAndExpand` opção alocará duas vezes mais espaço entre cada item que antes do primeiro item e depois do último item. Você pode simular o `CenterAndExpand` opção de `VerticalOptions` definindo o `JustifyContent` propriedade `FlexLayout` para `SpaceAround`.

Essas `FlexLayout` propriedades são discutidas mais detalhadamente na seção **[as propriedades vinculáveis aos seis](#bindable-properties)** abaixo.

### <a name="using-flexlayout-for-wrapping-items"></a>Usando FlexLayout para quebra automática de itens

O **foto encapsulamento** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** demonstra como `FlexLayout` pode encapsular seu filho para linhas e colunas adicionais. O arquivo XAML instancia o `FlexLayout` e atribui duas propriedades dele:

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

O `Direction` propriedade deste `FlexLayout` não estiver definida, portanto, tem a configuração padrão de `Row`, que significa que os filhos são organizados em linhas e o eixo principal é horizontal.

O [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) é de propriedade de um tipo de enumeração [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap). Se houver itens demais para caber em uma linha, a definição dessa propriedade faz com que os itens passe para a próxima linha.

Observe que o `FlexLayout` é um filho de um `ScrollView`. Se houver muitas linhas para caber na página, em seguida, o `ScrollView` tem um padrão `Orientation` propriedade `Vertical` e permite a rolagem vertical.

O `JustifyContent` propriedade aloca espaço restante no eixo principal (o eixo horizontal) de modo que cada item tem a mesma quantidade de espaço em branco.

O arquivo code-behind acessa uma coleção de fotos de exemplo e adiciona-os para o `Children` coleção do `FlexLayout`:

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
        int imageDimension = Device.RuntimePlatform == Device.iOS ||
                             Device.RuntimePlatform == Device.Android ? 240 : 120;

        string urlSuffix = String.Format("?width={0}&height={0}&mode=max", imageDimension);

        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("http://docs.xamarin.com/demo/stock.json");
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
                            Source = ImageSource.FromUri(new Uri(filepath + urlSuffix))
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

Aqui está o programa em execução em plataformas de três, progressivamente roladas de cima para baixo:

[![A página de quebra automática de foto](flex-layout-images/PhotoWrapping.png "foto quebra de página")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="holy-grail-layout-with-flexlayout"></a>Layout de Santa grail com FlexLayout

Há um layout padrão de design da web chamado o [ _Santa grail_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design)) porque ele é um formato de layout que é muito conveniente, mas geralmente difícil perceber com perfeição. O layout consiste em um cabeçalho na parte superior da página e rodapé na parte inferior, ambos os Estender até a largura total da página. Que ocupam o centro da página é o conteúdo principal, mas geralmente com um menu coluna à esquerda das informações de conteúdo e suplementares (às vezes chamado de um _lado_ área) à direita. [Seção 5.4.1 da especificação de Layout flexível de caixa CSS](http://www.w3.org/TR/css-flexbox-1/#order-accessibility) descreve como o layout de Santa grail pode ser realizado com uma caixa flexível.

O **Layout Santa Grail** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo mostra uma implementação simple deste layout usando um `FlexLayout` aninhado em outro. Como essa página foi projetada para um telefone no modo retrato, áreas à esquerda e à direita da área de conteúdo são apenas 50 pixels de largura:

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

Aqui, ele é executado em três plataformas:

[![A página de Layout Santa Grail](flex-layout-images/HolyGrailLayout.png "a página de Layout Santa Grail")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

As áreas de navegação e aside são renderizadas com um `BoxView` à esquerda e à direita.

O primeiro `FlexLayout` no XAML, o arquivo tem um eixo principal vertical e contém três filhos organizados em uma coluna. Esses são o cabeçalho, o corpo da página e rodapé. Aninhada `FlexLayout` tem um eixo principal horizontal com três filhos organizados em uma linha.

Três propriedades vinculáveis anexadas são demonstradas neste programa:

- O `Order` propriedade associável anexada é definida na primeira `BoxView`. Esta propriedade é um inteiro com um valor padrão de 0. Você pode usar essa propriedade para alterar a ordem de layout. Geralmente os desenvolvedores preferem o conteúdo da página seja exibido na marcação antes dos itens de navegação e separar itens. Definindo o `Order` propriedade no primeiro `BoxView` para um valor menor do que seus outros irmãos faz com que ele seja exibido como o primeiro item na linha. Da mesma forma, você pode garantir que um item é exibido por último, definindo o `Order` propriedade para um valor maior do que seus irmãos.

- O `Basis` propriedade associável anexada está definida nos dois `BoxView` itens para fornecer uma largura de 50 pixels. Essa propriedade é do tipo `FlexBasis`, uma estrutura que define uma propriedade estática do tipo `FlexBasis` chamado `Auto`, que é o padrão. Você pode usar `Basis` para especificar um tamanho de pixel ou uma porcentagem que indica a quantidade de espaço que o item ocupa no eixo principal. Ele é chamado um _base_ porque especifica um tamanho do item que é a base de layout subsequente.

- O `Grow` está definida em aninhada `Layout` e na `Label` filho que representa o conteúdo. Essa propriedade é do tipo `float` e tem um valor padrão de 0. Quando definido como um valor positivo, todo o espaço restante no eixo principal é alocado para esse item e seus irmãos com valores positivos de `Grow`. O espaço é alocado proporcionalmente para os valores, assim como a especificação de estrela em um `Grid`.

    A primeira `Grow` propriedade anexada é definida em aninhada `FlexLayout`, indicando que esse `FlexLayout` é ocupar todo o espaço vertical não utilizado em externo `FlexLayout`. A segunda `Grow` propriedade anexada é ativada a `Label` que representa o conteúdo, que indica que este conteúdo é ocupar todo o espaço horizontal não utilizado em interna `FlexLayout`.

    Também há um semelhante `Shrink` anexado a propriedade associável que podem ser usados quando o tamanho de filhos excede o tamanho do `FlexLayout` mas encapsulamento não for desejado.

### <a name="catalog-items-with-flexlayout"></a>Itens de catálogo com FlexLayout

O **itens de catálogo** página o **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo é semelhante ao [1 do exemplo na seção 1.1 da especificação de caixa de Layout flexível CSS](http://www.w3.org/TR/css-flexbox-1/#overview)exceto que ele exibe uma série rolável horizontalmente de imagens e descrições de três monkeys:

[![Página de itens de catálogo](flex-layout-images/CatalogItems.png "página de itens de catálogo")](flex-layout-images/CatalogItems-Large.png#lightbox)

Cada um de três monkeys é um `FlexLayout` contidos em um `Frame` que recebe um explícita de altura e largura, e que também é um filho de um maior `FlexLayout`. Arquivo XAML, a maioria das propriedades do `FlexLayout` filhos são especificados em estilos, todos, exceto um dos quais é um estilo implícito:

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

O estilo implícita para o `Image` inclui configurações de duas propriedades vinculáveis anexadas de `Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

O `Order` de &ndash;1 faz com que o `Image` elemento a ser exibido pela primeira vez em cada um dos aninhada `FlexLayout` modos de exibição, independentemente de sua posição dentro da coleção de filhos. O `AlignSelf` propriedade de `Center` faz com que o `Image` centralizado dentro do `FlexLayout`. Isso substitui a configuração do `AlignItems` propriedade, que tem um valor padrão de `Stretch`, o que significa que o `Label` e `Button` filhos são aumentados para a largura total do `FlexLayout`.

Dentro de cada um dos três `FlexLayout` exibições, um espaço em branco `Label` precede o `Button`, mas ele tem um `Grow` configuração de 1. Isso significa que todos o spae vertical extra é alocado em branco nesse `Label`, que efetivamente envia o `Button` até a parte inferior.

<a name="bindable-properties" />

## <a name="the-six-bindable-properties"></a>As propriedades vinculáveis aos seis

Agora que você viu alguns aplicativos comuns de `FlexLayout`, as propriedades de `FlexLayout` podem ser explorados com mais detalhes. 
`FlexLayout` Define as propriedades vinculáveis seis definida para o `FlexLayout` em si, tanto em código ou XAML, mas o `Position` propriedade não é coberta neste artigo.

Você pode testar wih cinco restantes propriedades vinculáveis usando o **experimentar** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo. Esta página permite que você adicionar ou remover o filho de um `FlexLayout` e definir combinações das cinco propriedades vinculáveis. Todos os filhos do `FlexLayout` são `Label` modos de exibição de vários tamanhos e cores com o `Text` propriedade definida como um número correspondente à sua posição no `Children` coleção.

Quando o programa é iniciado, cinco `Picker` modos de exibição exibem os valores padrão desses cinco `FlexLayout` propriedades. O `FlexLayout` na parte inferior da tela contém três filhos:

[![A página de teste: Padrão](flex-layout-images/ExperimentDefault.png "a página de teste - padrão")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

Cada uma da `Label` modos de exibição tem um plano de fundo cinza que mostra o espaço alocado para que `Label` dentro de `FlexLayout`. Plano de fundo do `FlexLayout` em si é Alice azul. Ele ocupa a área de inteiro inferior da página, exceto uma pouco margem em à esquerda e à direita.

<a name="direction" />

### <a name="the-direction-property"></a>A propriedade de direção

O [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction) é de propriedade do tipo [ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection), uma enumeração com quatro membros:

- `Column`
- `ColumnReverse` (ou "reversão de coluna" em XAML)
- `Row`, o padrão
- `RowReverse` (ou "reversão de linha" em XAML)

Em XAML, você pode especificar o valor dessa propriedade usando os nomes de membro de enumeração em letras minúsculas, maiusculas, ou maiusculas e minúsculas, ou você pode usar duas cadeias de caracteres adicionais entre parênteses são os mesmos que os indicadores CSS. (As cadeias de caracteres "coluna reversão" e "linha reversão" são definidas no [ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) classe usado pelo analisador XAML.)

Aqui está o **experimento** página mostrando (da esquerda para a direita), o `Row` direção, `Column` direção, e `ColumnReverse` direção:

[![A página de teste: Direção](flex-layout-images/ExperimentDirection.png "a página de teste - direção")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

Observe que para o `Reverse` os itens de opções, iniciam na parte direita ou inferior.

<a name="wrap" />

### <a name="the-wrap-property"></a>A propriedade de linha

O [ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap) é de propriedade do tipo [ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap), uma enumeração com três membros:

- `NoWrap`, o padrão
- `Wrap`
- `Reverse` (ou "reversão de wrap" em XAML)

Da esquerda para direita, essas telas mostram a `NoWrap`, `Wrap` e `Reverse` opções para 12 filhos:

[![A página de teste: Encapsular](flex-layout-images/ExperimentWrap.png "a página de teste - Wrap")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

Quando o `Wrap` está definida como `NoWrap` eixo principal é restrito (como esse programa) e o eixo principal não é grande ou alto o suficiente para caber todos os filhos, o `FlexLayout` tenta reduzir os itens, como a captura de tela de iOS Demonstra. Você pode controlar o shrinkness dos itens com o [ `Shrink` ](#shrink) propriedade associável anexada.

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>A propriedade JustifyContent

O [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent) é de propriedade do tipo [ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify), uma enumeração com seis membros:

- `Start` (ou "flex-start" em XAML), o padrão
- `Center`
- `End` (ou "flex-end" em XAML)
- `SpaceBetween` (ou "espaço-entre" em XAML)
- `SpaceAround` (ou "espaço alternativa" em XAML)
- `SpaceEvenly`

Esta propriedade especifica como os itens são espaçados no eixo principal, que é o eixo horizontal neste exemplo:

[![A página de teste: Justificar conteúdo](flex-layout-images/ExperimentJustifyContent.png "a página de teste - justificar conteúdo")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

Em todos os três capturas de tela de `Wrap` está definida como `Wrap`. O `Start` padrão é mostrado na captura de tela Android anterior. A captura de tela do iOS aqui mostra o `Center` opção: todos os itens são movidos para o centro. As três outras opções começando com a palavra `Space` alocar o espaço extra não ocupado pelos itens. `SpaceBetween` aloca espaço igualmente entre os itens; `SpaceAround` coloca igual espaço em torno de cada item, enquanto `SpaceEvenly` coloca igual espaço entre cada item e antes do primeiro item e após o último item na linha.

<a name="align-items" />

### <a name="the-alignitems-property"></a>A propriedade AlignItems

O [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems) é de propriedade do tipo [ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems), uma enumeração com quatro membros:

- `Stretch`, o padrão
- `Center`
- `Start` (ou "flex-start" em XAML)
- `End` (ou "flex-end" em XAML)

Esta é uma das duas propriedades (outro [ `AlignContent` ](#align-content)) que indica como os filhos são alinhados no eixo de cruz. Em cada linha, os filhos são ampliados (conforme mostrado na captura de tela anterior) ou alinhados no início, centro ou final de cada item, conforme mostrado nas capturas de tela de três seguintes:

[![A página de teste: Alinhar itens](flex-layout-images/ExperimentAlignItems.png "a página de teste - alinhar itens")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

A captura de tela do iOS, a parte superior de todos os filhos é alinhada. Nas capturas de tela Android, os itens são centralizados verticalmente com base no filho mais alto. A captura de tela de UWP, a parte inferior de todos os itens é alinhados.

Para qualquer item individual, o `AlignItems` configuração pode ser substituída com o [ `AlignSelf` ](#align-self) propriedade associável anexada.

<a name="align-content" />

### <a name="the-aligncontent-property"></a>A propriedade AlignContent

O [ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent) é de propriedade do tipo [ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent), uma enumeração com sete membros:

- `Stretch`, o padrão
- `Center`
- `Start` (ou "flex-start" em XAML)
- `End` (ou "flex-end" em XAML)
- `SpaceBetween` (ou "espaço-entre" em XAML)
- `SpaceAround` (ou "espaço alternativa" em XAML)
- `SpaceEvenly`

Como `AlignItems`, o `AlignContent` propriedade também alinha filhos no eixo transversal, mas afeta as linhas ou colunas inteiras:

[![A página de teste: Alinhar conteúdo](flex-layout-images/ExperimentAlignContent.png "a página de teste - alinhar conteúdo")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

Em screnshot iOS, ambas as linhas estão na parte superior; na captura de tela Android está no Centro; e a captura de tela de UWP está na parte inferior. As linhas também podem ser espaçadas de várias maneiras:

[![A página de teste: Alinhar conteúdo 2](flex-layout-images/ExperimentAlignContent2.png "a página de teste - alinhar conteúdo 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

O `AlignContent` não tem nenhum efeito quando há apenas uma linha ou coluna.

<a name="attached-properties" />

## <a name="the-five-attached-bindable-properties"></a>Os cinco anexado propriedades vinculáveis

`FlexLayout` define cinco propriedades vinculáveis anexadas. Essas propriedades são definidas nos filhos a `FlexLayout` e só pertencem a criança específica.

<a name="align-self" />

### <a name="the-alignself-property"></a>A propriedade AlignSelf

O [ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty) propriedade associável anexada é do tipo [ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent), uma enumeração com cinco membros:

- `Auto`, o padrão
- `Stretch`
- `Center`
- `Start` (ou "flex-start" em XAML)
- `End` (ou "flex-end" em XAML)

Para qualquer filho individual a `FlexLayout`, essa propriedade definir substituições a [ `AlignItems` ](#align-items) propriedade definida no `FlexLayout` em si. A configuração padrão de `Auto` significa usar o `AlignItems` configuração.

Para uma `Label` elemento chamado `label` (ou exemplo), você pode definir o `AlignSelf` propriedade em código como este:

```csharp
FlexAlign.SetAlignSelf(label, FlexAlignSelf.Center);
```

Observe que não há nenhuma referência para o `FlexLayout` pai do `Label`. Em XAML, você deve definir a propriedade como este:

```xaml
<Label ... FlexAlign.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>A propriedade de ordem

O [ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty) é de propriedade do tipo `int`. O valor padrão é 0.

O `Order` propriedade permite que você altere a ordem que os filhos do `FlexLayout` são organizadas. Normalmente, os filhos de um `FlexLayout` são organizados é a mesma ordem em que aparecem no `Children` coleção. Você pode substituir essa ordem, definindo o `Order` propriedade associável anexa um valor inteiro diferente de zero em um ou mais filhos. O `FlexLayout` organiza seus filhos com base na configuração do `Order` propriedade em cada filho, mas filhos com o mesmo `Order` configuração são organizados na ordem em que aparecem no `Children` coleção.

### <a name="the-basis-property"></a>A propriedade de base

O [ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty) propriedade associável anexada indica a quantidade de espaço alocado para um filho do `FlexLayout` no eixo principal. Especificado o tamanho, o `Basis` propriedade é o tamanho ao longo do eixo principal do pai `FlexLayout`. Em outras palavras, `Basis` indica a largura de um filho quando os filhos são organizados em linhas ou da altura quando os filhos são organizados em colunas.

O `Basis` é de propriedade do tipo [ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis), uma estrutura. O tamanho pode ser especificado em unidades independentes de dispositivo ou como uma porcentagem do tamanho do `FlexLayout`. O valor padrão de `Basis` é a propriedade estática `FlexBasis.Auto`, que significa que o filho do solicitada largura ou altura é usada.

No código, você pode definir o `Basis` propriedade para um `Label` chamado `label` para 40 unidades independentes de dispositivo como este:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

O segundo argumento para o `FlexBasis` chamado construtor `isRelative` e indica se o tamanho relativo (`true`) ou absoluto (`false`). O argumento tem um valor padrão de `false`, portanto, você também pode usar o código a seguir:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

Uma conversão implícita de `float` para `FlexBasis` for definida, portanto você pode simplificar ainda mais:

```csharp
FlexLayout.SetBasis(label, 40);
```

Você pode definir o tamanho de 25% do `FlexLayout` pai como este:

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

Este valor fracionário deve estar no intervalo de 0 a 1.

Em XAML, você pode usar um número para um tamanho em unidades independentes de dispositivo:

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

Ou você pode especificar uma porcentagem do intervalo de 0 a 100%:

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

O **base experiências** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** amostra permite que você experimente o `Basis` propriedade. A página exibe uma coluna de quebra automática de cinco `Label` elementos com alternando as cores de primeiro plano e plano de fundo. Dois `Slider` elementos permitem especificar `Basis` valores para o segundo e o quarto `Label`:

[![Página de teste de base](flex-layout-images/BasisExperiment.png "página de teste de base")](flex-layout-images/BasisExperiment-Large.png#lightbox)

A captura de tela de iOS à esquerda mostra os dois `Label` elementos sendo fornecidos alturas em unidades independentes de dispositivo. Mostra a tela Android dada alturas menos da metade da altura total do `FlexLayout`. Se o `Basis` for definida em 100%, o filho é a altura do `FlexLayout`e passe para a próxima coluna e ocupa toda a altura da coluna, como mostra a captura de tela UWP: ele aparece como se os cinco filhos são organizados em uma linha , mas na verdade são dispostos em cinco colunas.

### <a name="the-grow-property"></a>A propriedade de crescimento

O [ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty) propriedade associável anexada é do tipo `int`. O valor padrão é 0 e o valor deve ser maior ou igual a 0.

O `Grow` propriedade desempenha um papel quando quando o `Wrap` está definida como `NoWrap` e a linha de filhos tem uma largura total menor que a largura do `FlexLayout`, ou a coluna de filhos tem uma altura menor que o `FlexLayout`. O `Grow` propriedade indica como distribuir o espaço entre os filhos.

No **crescer experimento** página cinco `Label` elementos de cores alternadas são organizados em uma coluna e dois `Slider` elementos que você possa ajustar a `Grow` propriedade de segundo e quarto `Label`. A captura de tela do iOS na extremidade esquerda mostra o padrão `Grow` propriedades de 0:

[![A página de teste de expansão](flex-layout-images/GrowExperiment.png "a página de teste de expansão")](flex-layout-images/GrowExperiment-Large.png#lightbox)

Se qualquer um filho é fornecido um positivo `Grow` valor, e a criança ocupa todo o espaço restante, como mostra a captura de tela de Android. Este espaço também pode ser alocado entre dois ou mais filhos. A captura de tela de UWP, o `Grow` propriedade da segunda `Label` é definido como 0,5, enquanto o `Grow` propriedade do quarto `Label` é 1.5, que dá a quarta `Label` três vezes mais o espaço restante como o segundo `Label`.

Como a exibição filha usa esse espaço depende do tipo específico de filho. Para uma `Label`, o texto pode ser posicionado dentro do espaço total do `Label` usando as propriedades `HorizontalTextAlignment` e `VerticalTextAlignment`.

<a name="shrink" />

### <a name="the-shrink-property"></a>A propriedade de redução

O [ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) propriedade associável anexada é do tipo `int`. O valor padrão é 1 e o valor deve ser maior ou igual a 0.

O `Shrink` propriedade desempenha um papel quando o `Wrap` está definida como `NoWrap` e a largura da agregação de uma linha de filhos é maior que a largura do `FlexLayout`, ou maior que a altura da agregação de uma única coluna de filhos a altura do `FlexLayout`. Normalmente o `FlexLayout` exibirá esses filhos por constricting seus tamanhos. O `Shrink` propriedade pode indicar quais filhos recebem a prioridade em que está sendo exibido em seus tamanhos completos.

O **reduzir experimento** página cria um `FlexLayout` com uma única linha de cinco `Label` filhos que exigem mais espaço do que o `FlexLayout` largura. A captura de tela de iOS à esquerda mostra todos os `Label` elementos com valores padrão de 1:

[![A redução experimentar página](flex-layout-images/ShrinkExperiment.png "a redução experiências de página")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

A captura de tela Android, o `Shrink` valor para a segunda `Label` é definido como 0, e que `Label` é exibido em sua largura inteira. Além disso, o quarto `Label` recebe um `Shrink` valor maior do que um, e ele pode ser reduzido. Mostra a captura de tela UWP `Label` elementos receberem um `Shrink` valor de 0 para permitir que eles a ser exibida em seu tamanho total, se for possível.

Você pode definir ambos os `Grow` e `Shrink` valores para acomodar situações em que os tamanhos de agregação filho, às vezes, podem ser menor ou às vezes maior que o tamanho do `FlexLayout`.

## <a name="css-styling-with-flexlayout"></a>Estilo CSS com FlexLayout

Você pode usar o [estilos de CSS](~/xamarin-forms/user-interface/styles/css/index.md) recurso introduzido com o xamarin. Forms 3.0 na conexão com `FlexLayout`. O **itens de catálogo de CSS** página do **[FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)** exemplo duplica o layout do **itens de catálogo** página, mas com uma CSS folha de estilos para muitos dos estilos:

[![Página de itens do catálogo de CSS](flex-layout-images/CssCatalogItems.png "página de itens do catálogo de CSS")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

O original **CatalogItemsPage.xaml** arquivo tem cinco `Style` definições no seu `Resources` seção com 15 `Setter` objetos. No **CssCatalogItemsPage.xaml** arquivo, que reduziu a dois `Style` definições com apenas quatro `Setter` objetos. Esses estilos complementam a folha de estilos CSS para propriedades que o recurso de estilo CSS xamarin. Forms atualmente não é possível lidar com:

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

Referenciado na primeira linha de folha de estilos CSS de `Resources` seção:

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

Observe também que dois elementos em cada um dos três itens incluem `StyleClass` configurações:

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

Elas fazem referência a seletores no **CatalogItemsStyles.css** folha de estilos:

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

Vários `FlexLayout` propriedades vinculáveis anexadas são referenciadas aqui. No `label.empty` seletor, você verá o `flex-grow` atributo, que estilos vazio `Label` para fornecer um espaço em branco acima do `Button`. O `image` seletor contém um `order` atributo e um `align-self` atributo, que correspondem aos `FlexLayout` propriedades vinculáveis anexadas.

Você viu que você pode definir propriedades diretamente no `FlexLayout` e você pode definir as propriedades vinculáveis anexadas nos filhos de um `FlexLayout`. Ou, você pode definir essas propriedades indiretamente usando estilos tradicionais baseados em XAML ou os estilos CSS. O importante é conhecido e entender essas propriedades. Eles são o que torna o `FlexLayout` realmente flexível. 

## <a name="flexlayout-with-xamarinuniversity"></a>FlexLayout com Xamarin.University

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin. Forms 3.0 Flex Layout, pelo [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Links relacionados

- [FlexLayoutDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/)
