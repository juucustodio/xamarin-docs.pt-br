---
title: Orientação do dispositivo
description: Este artigo explica como aplicativos do xamarin. Forms de layout que se parecem muito bem em orientações retrato e paisagem.
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: d988be9163c0a6e12735425a139c03d9641fa95c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772758"
---
# <a name="device-orientation"></a>Orientação do dispositivo

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

É importante considerar como seu aplicativo será usado e como orientação paisagem pode ser incorporada para melhorar a experiência do usuário. Layouts individuais podem ser projetados para acomodar várias orientações e melhor usa o espaço disponível. No nível do aplicativo, rotação pode ser desabilitada ou habilitada.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Controlando a orientação

Ao usar o xamarin. Forms, o método com suporte de controlar a orientação do dispositivo é usar as configurações para cada projeto individual.

### <a name="ios"></a>iOS

No iOS, a orientação do dispositivo está configurada para aplicativos que usam o **Info. plist** arquivo. Esse arquivo incluirá as configurações de orientação para iPhone e iPod, bem como configurações para iPad, se o aplicativo inclui-lo como um destino. A seguir estão as instruções específicas para seu IDE. Use as opções de IDE na parte superior deste documento para selecionar quais instruções que você gostaria de ver:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No Visual Studio, abra o projeto do iOS e abra **Info. plist**. O arquivo será aberto em um painel de configuração, começando com o guia de informações de implantação do iPhone:

![Informações de implantação no Visual Studio do iPhone](device-orientation-images/orientation-vs-iphone.png)

Para configurar a orientação do iPad, selecione a **informações de implantação do iPad** guia na parte superior esquerda do painel, em seguida, selecione das orientações disponíveis:

![Orientações de dispositivo com suporte no Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, abra o projeto do iOS e abra **Info. plist**. Sob o **aplicativo** guia, seções estarão disponíveis para definir a orientação:

![iPhone informações de implantação no Visual Studio para Mac](device-orientation-images/orientation-xam-ui.png)

Se você preferir editar os valores usando uma interface do editor de chave-valor, selecione o **origem**> guia na parte inferior da tela:

![Orientações de dispositivo com suporte no Visual Studio para Mac](device-orientation-images/orientation-xam-source.png)

-----

### <a name="android"></a>Android

Para controlar a orientação no Android, abra **MainActivity.cs** e defina a orientação usando o atributo decorar o `MainActivity` classe:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin. Android dá suporte a várias opções para especificar a orientação:

- **Paisagem** &ndash; força a orientação do aplicativo para ser paisagem, independentemente dos dados do sensor.
- **Retrato** &ndash; força a orientação do aplicativo para ser retrato, independentemente dos dados do sensor.
- **Usuário** &ndash; faz com que o aplicativo a ser apresentada usando a orientação de preferência do usuário.
- **Por trás** &ndash; faz com que a orientação do aplicativo ser o mesmo que a orientação do [atividade](xref:Android.App.Activity) por trás dele.
- **Sensor** &ndash; faz com que a orientação do aplicativo seja determinado pelo sensor, mesmo se o usuário tiver desabilitado a rotação automática.
- **SensorLandscape** &ndash; faz com que o aplicativo para usar a orientação de paisagem durante o uso de dados de sensor para alterar a direção em que a tela é voltado para a (de modo que a tela não é vista como cabeça para baixo).
- **SensorPortrait** &ndash; faz com que o aplicativo para usar a orientação de retrato durante o uso de dados de sensor para alterar a direção em que a tela é voltado para a (de modo que a tela não é vista como cabeça para baixo).
- **ReverseLandscape** &ndash; faz com que o aplicativo para usar a orientação paisagem, voltado para a direção oposta usual para aparecer "cabeça para baixo."
- **ReversePortrait** &ndash; faz com que o aplicativo para usar a orientação retrato, voltado para a direção oposta usual para aparecer "cabeça para baixo."
- **FullSensor** &ndash; faz com que o aplicativo se baseiam nos dados de sensor para selecionar a orientação correta (fora de 4 a possíveis).
- **FullUser** &ndash; faz com que o aplicativo para usar as preferências do usuário orientação. Se a rotação automática estiver habilitada, todas as 4 orientações podem ser usadas.
- **UserLandscape** &ndash; _\[Nepodporuje\]_ faz com que o aplicativo para usar a orientação paisagem, a menos que o usuário tenha a rotação automática habilitada, caso em que ele usará o sensor para determinar a orientação. Esta opção interromperá a compilação.
- **UserPortrait** &ndash; _\[Nepodporuje\]_ faz com que o aplicativo para usar a orientação retrato, a menos que o usuário tenha a rotação automática habilitada, caso em que ele usará o sensor para determinar a orientação. Esta opção interromperá a compilação.
- **Bloqueado** &ndash; _\[não tem suporte\]_ faz com que o aplicativo para usar a orientação da tela física que quer que seja no lançamento, sem responder a alterações no dispositivo do orientação. Esta opção interromperá a compilação.

Observe que as APIs do Android nativos fornecem muito controle sobre como a orientação é gerenciada, incluindo as opções que contradizem explicitamente o usuário expresso preferências.

### <a name="universal-windows-platform"></a>Plataforma universal do Windows

Na Universal Windows Platform (UWP), as orientações com suporte são definidas **Package. appxmanifest** arquivo. Abrir o manifesto irá revelar um painel de configuração onde as orientações com suporte podem ser selecionadas.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Reagir a alterações na orientação

Xamarin. Forms não oferece quaisquer eventos nativos para notificar seu aplicativo de alterações de orientação no código compartilhado. No entanto, o `SizeChanged` eventos do `Page` é acionada quando a largura ou altura do `Page` alterações. Quando a largura do `Page` é maior que a altura, o dispositivo estiver no modo paisagem. Para obter mais informações, consulte [exibir uma imagem com base na orientação da tela](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

> [!NOTE]
> Há um pacote de NuGet existente e gratuito para receber notificações de alterações de orientação no código compartilhado. Consulte a [repositório GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) para obter mais informações.

Como alternativa, é possível substituir a [ `OnSizeAllocated` ](xref:Xamarin.Forms.Page.OnSizeAllocated*) método em um `Page`, inserindo qualquer layout alterar lógica nele. O `OnSizeAllocated` método é chamado sempre que um `Page` é alocado um novo tamanho, o que ocorre sempre que o dispositivo seja girado. Observe que a implementação base de `OnSizeAllocated` executa funções importantes de layout, portanto, é importante chamar a implementação base na substituição:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Falha ao executar essa etapa resulta em uma página não está funcionando.

Observe que o `OnSizeAllocated` método pode ser chamado várias vezes quando um dispositivo é girado. Alterando o layout de cada vez que é um desperdício de recursos e pode levar a cintilação. Considere o uso de uma variável de instância dentro de sua página para controlar se a orientação é em paisagem ou retrato e redesenhar somente quando há uma alteração:

```csharp
private double width = 0;
private double height = 0;

protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
    if (this.width != width || this.height != height)
    {
        this.width = width;
        this.height = height;
        //reconfigure layout
    }
}
```

Depois que tiver sido detectada uma alteração na orientação do dispositivo, você talvez queira adicionar ou remover exibições adicionais de/para sua interface do usuário para reagir a alterações no espaço disponível. Por exemplo, considere a Calculadora interna em cada plataforma em Retrato:

![](device-orientation-images/calculator-portrait.png "Aplicativo de calculadora em Retrato")

e o cenário:

![](device-orientation-images/calculator-landscape.png "Aplicativo de calculadora em Paisagem")

Observe que os aplicativos aproveitam o espaço disponível com a adição de mais funcionalidades em paisagem.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Layout dinâmico

É possível para interfaces de design usando os layouts internos para que eles normalmente a transição quando o dispositivo é girado. Durante a criação de interfaces que continuarão a ser atraente ao responder a alterações na orientação, considere as seguintes regras gerais:

- **Preste atenção às taxas** &ndash; alterações na orientação podem causar problemas quando determinadas suposições são feitas com relação a taxas. Por exemplo, uma exibição que teria bastante espaço em 1/3, o espaço vertical de uma tela em Retrato não caber em 1/3 do espaço vertical em paisagem.
- **Tenha cuidado com os valores absolutos** &ndash; valores absolutos (pixel) que fazem sentido em Retrato talvez não faça sentido em paisagem. Quando valores absolutos forem necessárias, use layouts aninhados para isolar seu impacto. Por exemplo, seria razoável usar valores absolutos em um `TableView` `ItemTemplate` quando o modelo de item tem uma altura uniforme garantida.

As regras acima também se aplicam ao implementar interfaces para vários tamanhos de tela e são geralmente considerados práticas recomendadas. O restante deste guia explicará exemplos específicos de layouts responsivos usando cada um dos layouts primários no xamarin. Forms.

> [!NOTE]
> Para maior clareza, as seções a seguir demonstram como implementar layouts responsivos usando apenas um tipo de `Layout` por vez. Na prática, geralmente é mais simples de misturar `Layout`s para obter um layout desejado usando o mais simples ou mais intuitiva `Layout` para cada componente.

### <a name="stacklayout"></a>StackLayout

Considere o aplicativo a seguir, exibido em Retrato:

![](device-orientation-images/photo-stack-portrait.png "Aplicativo de fotos em Retrato")

e o cenário:

![](device-orientation-images/photo-stack-landscape.png "Aplicativo de fotos em Paisagem")

Que pode ser feito com o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.StackLayoutPageXaml"
Title="Stack Photo Editor - XAML">
    <ContentPage.Content>
        <StackLayout Spacing="10" Padding="5" Orientation="Vertical"
        x:Name="outerStack"> <!-- can change orientation to make responsive -->
            <ScrollView>
                <StackLayout Spacing="5" HorizontalOptions="FillAndExpand"
                    WidthRequest="1000">
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Name: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer.jpg"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Date: " WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="07/05/2015"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Label Text="Tags:" WidthRequest="75"
                            HorizontalOptions="Start" />
                        <Entry Text="deer, tiger"
                            HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                    <StackLayout Orientation="Horizontal">
                        <Button Text="Save" HorizontalOptions="FillAndExpand" />
                    </StackLayout>
                </StackLayout>
            </ScrollView>
            <Image  Source="deer.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Alguns C# é usado para alterar a orientação do `outerStack` com base na orientação do dispositivo:

```csharp
protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            outerStack.Orientation = StackOrientation.Horizontal;
        } else {
            outerStack.Orientation = StackOrientation.Vertical;
        }
    }
}
```

Observe o seguinte:

- `outerStack` é ajustado para apresentar os controles e a imagem como uma pilha horizontal ou vertical, dependendo da orientação, para aproveitar melhor o espaço disponível.

### <a name="absolutelayout"></a>AbsoluteLayout

Considere o aplicativo a seguir, exibido em Retrato:

![](device-orientation-images/photo-abs-portrait.png "Aplicativo de fotos em Retrato")

e o cenário:

![](device-orientation-images/photo-abs-landscape.png "Aplicativo de fotos em Paisagem")

Que pode ser feito com o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <AbsoluteLayout>
            <ScrollView AbsoluteLayout.LayoutBounds="0,0,1,1"
                AbsoluteLayout.LayoutFlags="PositionProportional,SizeProportional">
                <AbsoluteLayout>
                    <Image Source="deer.jpg"
                        AbsoluteLayout.LayoutBounds=".5,0,300,300"
                        AbsoluteLayout.LayoutFlags="PositionProportional" />
                    <BoxView Color="#CC1A7019" AbsoluteLayout.LayoutBounds=".5
                        300,.7,50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" />
                    <Label Text="deer.jpg" AbsoluteLayout.LayoutBounds = ".5
                        310,1, 50" AbsoluteLayout.LayoutFlags="XProportional
                        WidthProportional" HorizontalTextAlignment="Center" TextColor="White" />
                </AbsoluteLayout>
            </ScrollView>
            <Button Text="Previous" AbsoluteLayout.LayoutBounds="0,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional"
                BackgroundColor="White" TextColor="Green" BorderRadius="0" />
            <Button Text="Next" AbsoluteLayout.LayoutBounds="1,1,.5,60"
                AbsoluteLayout.LayoutFlags="PositionProportional
                    WidthProportional" BackgroundColor="White"
                    TextColor="Green" BorderRadius="0" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

Observe o seguinte:

- Por causa da maneira que a página foi disposta, não é necessário para o código procedural introduzir a capacidade de resposta.
- O `ScrollView` está sendo usado para permitir que o rótulo fique visível, mesmo quando a altura da tela é menor que a soma das alturas dos botões e a imagem fixas.

### <a name="relativelayout"></a>RelativeLayout

Considere o aplicativo a seguir, exibido em Retrato:

![](device-orientation-images/photo-rel-portrait.png "Aplicativo de fotos em Retrato")

e o cenário:

![](device-orientation-images/photo-rel-landscape.png "Aplicativo de fotos em Paisagem")

Que pode ser feito com o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImageSource="deer.jpg">
    <ContentPage.Content>
        <RelativeLayout x:Name="outerLayout">
            <BoxView BackgroundColor="#AA1A7019"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}" />
            <ScrollView
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=1}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=0,Constant=0}">
                <RelativeLayout>
                    <Image Source="deer.jpg" x:Name="imageDeer"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.8}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=.1}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=10}" />
                    <Label Text="deer.jpg" HorizontalTextAlignment="Center"
                        RelativeLayout.WidthConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=1}"
                        RelativeLayout.HeightConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Height,Factor=0,Constant=75}"
                        RelativeLayout.XConstraint="{ConstraintExpression
                            Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                        RelativeLayout.YConstraint="{ConstraintExpression
                            Type=RelativeToView,ElementName=imageDeer,Property=Height,Factor=1,Constant=20}" />
                </RelativeLayout>

            </ScrollView>

            <Button Text="Previous" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=0}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                 />
            <Button Text="Next" BackgroundColor="White" TextColor="Green" BorderRadius="0"
                RelativeLayout.XConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                RelativeLayout.YConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Height,Factor=1,Constant=-60}"
                RelativeLayout.HeightConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=0,Constant=60}"
                RelativeLayout.WidthConstraint="{ConstraintExpression
                    Type=RelativeToParent,Property=Width,Factor=.5}"
                />
        </RelativeLayout>
    </ContentPage.Content>
</ContentPage>

```

Observe o seguinte:

- Por causa da maneira que a página foi disposta, não é necessário para o código procedural introduzir a capacidade de resposta.
- O `ScrollView` está sendo usado para permitir que o rótulo fique visível, mesmo quando a altura da tela é menor que a soma das alturas dos botões e a imagem fixas.

### <a name="grid"></a>Grade

Considere o aplicativo a seguir, exibido em Retrato:

![](device-orientation-images/photo-grid-portrait.png "Aplicativo de fotos em Retrato")

e o cenário:

![](device-orientation-images/photo-grid-landscape.png "Aplicativo de fotos em Paisagem")

Que pode ser feito com o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.GridPageXaml"
Title="Grid - XAML">
    <ContentPage.Content>
        <Grid x:Name="outerGrid">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="60" />
            </Grid.RowDefinitions>
            <Grid x:Name="innerGrid" Grid.Row="0" Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="*" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="deer.jpg" Grid.Row="0" Grid.Column="0" HeightRequest="300" WidthRequest="300" />
                <Grid x:Name="controlsGrid" Grid.Row="0" Grid.Column="1" >
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="*" />
                    </Grid.ColumnDefinitions>
                    <Label Text="Name:" Grid.Row="0" Grid.Column="0" />
                    <Label Text="Date:" Grid.Row="1" Grid.Column="0" />
                    <Label Text="Tags:" Grid.Row="2" Grid.Column="0" />
                    <Entry Grid.Row="0" Grid.Column="1" />
                    <Entry Grid.Row="1" Grid.Column="1" />
                    <Entry Grid.Row="2" Grid.Column="1" />
                </Grid>
            </Grid>
            <Grid x:Name="buttonsGrid" Grid.Row="1">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Previous" Grid.Column="0" />
                <Button Text="Save" Grid.Column="1" />
                <Button Text="Next" Grid.Column="2" />
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Juntamente com o seguinte código procedural para manipular as alterações de rotação:

```csharp
private double width;
private double height;

protected override void OnSizeAllocated (double width, double height){
    base.OnSizeAllocated (width, height);
    if (width != this.width || height != this.height) {
        this.width = width;
        this.height = height;
        if (width > height) {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.RowDefinitions.Add (new RowDefinition{ Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 1, 0);
        } else {
            innerGrid.RowDefinitions.Clear();
            innerGrid.ColumnDefinitions.Clear ();
            innerGrid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (1, GridUnitType.Star) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Auto) });
            innerGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
            innerGrid.Children.Remove (controlsGrid);
            innerGrid.Children.Add (controlsGrid, 0, 1);
        }
    }
}
```

Observe o seguinte:

- Por causa da maneira que a página foi disposta, há um método para alterar o posicionamento de grade dos controles.

## <a name="related-links"></a>Links relacionados

- [Layout (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [Layout dinâmico (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [Exibir uma imagem com base na orientação da tela](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
