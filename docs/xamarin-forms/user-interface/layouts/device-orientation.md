---
title: "Orientação do dispositivo"
description: "Entenda como dispor aplicativos uma aparência excelentes na orientações retrato e paisagem."
ms.topic: article
ms.prod: xamarin
ms.assetid: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/09/2015
ms.openlocfilehash: cb17c224fc6102d9e0dc25853c2222734299647a
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="device-orientation"></a>Orientação do dispositivo

É importante considerar como seu aplicativo será usado e como a orientação de paisagem pode ser incorporada para melhorar a experiência do usuário. Layouts individuais podem ser criados para acomodar várias orientações e melhor usa o espaço disponível. No nível do aplicativo, rotação pode ser desabilitada ou habilitada.

Este artigo guiará você durante a criação de aplicativos que aproveitam os recursos de orientação do dispositivo e tem as seguintes seções:

- **[Controlando a orientação](#Controlling_Orientation)**  &ndash; entender como controlar a orientação no nível do aplicativo em cada plataforma.
- **[Reagir a alterações na orientação](#Reacting_to_Changes_in_Orientation)**  &ndash; aprender a ser notificado sobre e reagir a, as alterações na orientação.
- **[Layout dinâmico](#Responsive_Layout)**  &ndash; aprender a criar layouts automaticamente funcionam em orientações paisagem e retrato.

<a name="Controlling_Orientation" />

## <a name="controlling-orientation"></a>Controlando a orientação

Ao usar xamarin. Forms, o método com suporte de controlar a orientação do dispositivo é usar as configurações para cada projeto.

> [!NOTE]
> A partir do xamarin. Forms 1.5.0 há um bug que impede personalizado com base no renderizador tenta controlar orientação falha. Consulte [esta discussão](https://forums.xamarin.com/discussion/46653/forcing-landscape-for-a-single-page-in-ios#latest)esta discussão nos fóruns do Xamarin para obter mais informações.

### <a name="ios"></a>iOS

No iOS, a orientação do dispositivo está configurada para aplicativos que usam o **Info. plist** arquivo. Este arquivo incluirá as configurações de orientação para iPhone e iPod, bem como configurações para iPad, se o aplicativo inclui-lo como um destino. A seguir estão as instruções específicas para seu IDE. Use as opções de IDE na parte superior deste documento para selecionar quais instruções que você gostaria de ver:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio, abra o projeto iOS e abra **Info. plist**. O arquivo será aberto em um painel de configuração, começando com a guia de informações de implantação do iPhone:

![iPhone informações de implantação no Visual Studio](device-orientation-images/orientation-vs-iphone.png)

Para configurar a orientação de iPad, selecione o **iPad informações de implantação** guia na parte superior esquerda do painel, em seguida, selecione das orientações disponíveis:

![Orientações de dispositivos com suporte no Visual Studio](device-orientation-images/orientation-vs-ipad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

No Visual Studio para Mac, abra o projeto iOS e abra **Info. plist**. Sob o **aplicativo** guia seções estarão disponíveis para definir a orientação:

![iPhone informações de implantação no Visual Studio para Mac](device-orientation-images/orientation-xam-ui.png)

Se você preferir editar os valores usando uma interface de editor de valor de chave, selecione o **fonte**> guia na parte inferior da tela:

![Suporte de orientações do dispositivo no Visual Studio para Mac](device-orientation-images/orientation-xam-source.png)

-----


### <a name="android"></a>Android

Para controlar a orientação no Android, abra **MainActivity.cs** e defina a orientação usando o atributo decorar o `MainActivity` classe:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

Xamarin dá suporte a várias opções para especificar a orientação:

- **Paisagem** &ndash; força a orientação de aplicativo para ser paisagem, independentemente dos dados do sensor.
- **Retrato** &ndash; força a orientação de aplicativo para ser retrato, independentemente dos dados do sensor.
- **Usuário** &ndash; faz com que o aplicativo ser exibidas usando a orientação preferida do usuário.
- **Por trás de** &ndash; faz com que a orientação do aplicativo ser o mesmo que a orientação do [atividade](https://developer.xamarin.com/api/type/Android.App.Activity/) por trás dele.
- **Sensor de** &ndash; faz com que a orientação do aplicativo seja determinado pelo sensor, mesmo se o usuário tiver desabilitado a rotação automática.
- **SensorLandscape** &ndash; faz com que o aplicativo para usar a orientação de paisagem durante o uso de dados para alterar a direção que a tela está enfrentando (de forma que a tela não é vista como cabeça para baixo).
- **SensorPortrait** &ndash; faz com que o aplicativo para usar a orientação retrato durante o uso de dados para alterar a direção que a tela está enfrentando (de forma que a tela não é vista como cabeça para baixo).
- **ReverseLandscape** &ndash; faz com que o aplicativo para usar a orientação de paisagem, voltados para a direção oposta de normal para aparecer "cabeça para baixo."
- **ReversePortrait** &ndash; faz com que o aplicativo para usar a orientação retrato, voltados para a direção oposta de normal para aparecer "cabeça para baixo."
- **FullSensor** &ndash; faz com que o aplicativo dependem de dados de sensor para selecionar a orientação correta (sem o possível 4).
- **FullUser** &ndash; faz com que o aplicativo para usar as preferências do usuário orientação. Se a rotação automática estiver habilitada, todas as 4 orientações podem ser usadas.
- **UserLandscape** &ndash;  _\[não tem suporte\]_  faz com que o aplicativo para usar a orientação de paisagem, a menos que o usuário tenha a rotação automática habilitada, caso em que ele usará o sensor para determinar a orientação. Esta opção interromperá a compilação.
- **UserPortrait** &ndash;  _\[não tem suporte\]_  faz com que o aplicativo para usar a orientação retrato, a menos que o usuário tenha a rotação automática habilitada, caso em que ele usará o sensor para determinar a orientação. Esta opção interromperá a compilação.
- **Bloqueado** &ndash;  _\[não tem suporte\]_  faz com que o aplicativo para usar a orientação da tela física que ele estiver na inicialização, sem responder a alterações no dispositivo do orientação. Esta opção interromperá a compilação.

Observe que as APIs do Android native fornecem muito controle sobre como a orientação é gerenciada, incluindo opções contradigam explicitamente o usuário expresso preferências.

### <a name="windows-phone"></a>Windows Phone

No Windows Phone RT, as orientações com suporte são definidas no <span class="UIItem">Package. appxmanifest</span> arquivo. Abrir o manifesto revelará um painel de configuração em que as orientações com suporte podem ser selecionadas:

![](device-orientation-images/vs-winrt-config.png "Editor do Visual Package. appxmanifest")

No Windows Phone 8 (Silverlight), as orientações com suporte são definidas no código no <span class="UIItem">MainPage.xaml.cs</span> arquivo. No modelo de projeto padrão, o valor já está definido com a seguinte linha de código:

```csharp
SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;
```

Para especificar as opções de orientação na Windows Phone, substituí-lo com o código para habilitar as orientações que você deseja:

```csharp
SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;
SupportedOrientations = SupportedPageOrientation.Portrait; // portrait only
SupportedOrientations = SupportedPageOrientation.Landscape; // landscape only
```

Observe que Windows Phone dá suporte paisagem exibições em ambos (como visto de retrato) orientações da esquerda para a direita e da direita para esquerda. Não é possível especificar o que é usado.

<a name="Reacting_to_Changes_in_Orientation" />

## <a name="reacting-to-changes-in-orientation"></a>Reagir a alterações na orientação

Xamarin. Forms não oferece quaisquer eventos nativo para notificar seu aplicativo de alterações de orientação em código compartilhado. No entanto, o `SizeChanged` eventos do `Page` é acionado quando a largura ou altura do `Page` alterações. Quando a largura do `Page` é maior que a altura, o dispositivo estiver no modo paisagem. Para obter mais informações, consulte [exibir uma imagem com base na orientação da tela](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/).

> [!NOTE]
> Há um pacote NuGet livre existente para receber notificações de alterações de orientação em código compartilhado. Consulte o [repositório GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) para obter mais informações.

Como alternativa, é possível substituir o [ `OnSizeAllocated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnSizeAllocated(System.Double,System.Double)/) método em um `Page`, inserir qualquer layout altere a lógica existe. O `OnSizeAllocated` método é chamado sempre que uma `Page` é alocado um novo tamanho, o que acontece whenver o dispositivo for girado. Observe que a implementação de base de `OnSizeAllocated` realiza funções de layout importante, portanto, é importante chamar a implementação base na substituição:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

Falha ao executar essa etapa resulta em uma página não está funcionando.

Observe que o `OnSizeAllocated` método pode ser chamado várias vezes quando um dispositivo for girado. Alterando o layout de cada vez é desperdício de recursos e pode levar a cintilação. Considere o uso de uma variável de instância dentro de sua página para controlar se é a orientação de paisagem ou retrato e redesenhar apenas quando há uma alteração:

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

Depois que uma alteração na orientação do dispositivo foi detectada, convém adicionar ou remover exibições adicionais de/para a interface do usuário para reagir a alterações no espaço disponível. Por exemplo, considere o cálculo interno em cada plataforma em Retrato:

![](device-orientation-images/calculator-portrait.png "Aplicativo de calculadora em Retrato")

e paisagem:

![](device-orientation-images/calculator-landscape.png "Aplicativo de calculadora em Paisagem")

Observe que os aplicativos aproveitem o espaço disponível, adicionando mais funcionalidade em paisagem.

<a name="Responsive_Layout" />

## <a name="responsive-layout"></a>Layout dinâmico

É possível para interfaces de design usando os layouts internos para que eles normalmente a transição quando o dispositivo for girado. Durante a criação de interfaces que continuarão a ser atraente ao responder a alterações na orientação, considere as seguintes regras gerais:

- **Preste atenção às taxas de** &ndash; alterações na orientação podem causar problemas quando determinadas suposições são feitas em relação a taxas. Por exemplo, uma exibição que tem espaço suficiente em 1/3 do espaço vertical de uma tela em Retrato não caber em 1/3 do espaço vertical em paisagem.
- **Tenha cuidado com valores absolutos** &ndash; valores absolutos (pixel) que fazem sentido em Retrato não podem fazer sentido em paisagem. Quando valores absolutos são necessários, use layouts aninhados para isolar seu impacto. Por exemplo, seria razoável usar valores absolutos em um `TableView` `ItemTemplate` quando o modelo de item tem uma altura uniforme garantida.

As regras acima também se aplicam quando implementar interfaces por vários tamanhos de telas e são geralmente consideradas práticas recomendadas. O restante deste guia explicará exemplos específicos de layouts responsivos usando cada um dos layouts de primário em xamarin. Forms.

> [!NOTE]
> Para maior clareza, as seguintes seções demonstram como implementar responsivos layouts usando apenas um tipo de `Layout` por vez. Na prática, geralmente é mais simples de misturar `Layout`s para obter um layout desejado usando a mais simples ou mais intuitiva `Layout` para cada componente.

### <a name="stacklayout"></a>StackLayout

Considere o aplicativo a seguir, exibido em Retrato:

![](device-orientation-images/photo-stack-portrait.png "Aplicativo de fotos em Retrato")

e paisagem:

![](device-orientation-images/photo-stack-landscape.png "Aplicativo de fotos em Paisagem")

Isso é feito com o XAML a seguir:

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

Alguns c# é usado para alterar a orientação do `outerStack` com base na orientação do dispositivo:

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

e paisagem:

![](device-orientation-images/photo-abs-landscape.png "Aplicativo de fotos em Paisagem")

Isso é feito com o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.AbsoluteLayoutPageXaml"
Title="AbsoluteLayout - XAML" BackgroundImage="deer.jpg">
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

- Por causa da forma que a página foi disposta, não é necessário para o código de procedimento apresentar capacidade de resposta.
- O `ScrollView` está sendo usado para permitir que o rótulo fique visível, mesmo quando a altura da tela é menor que a soma das alturas fixas dos botões e a imagem.


### <a name="relativelayout"></a>RelativeLayout

Considere o aplicativo a seguir, exibido em Retrato:

![](device-orientation-images/photo-rel-portrait.png "Aplicativo de fotos em Retrato")

e paisagem:

![](device-orientation-images/photo-rel-landscape.png "Aplicativo de fotos em Paisagem")

Isso é feito com o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="ResponsiveLayout.RelativeLayoutPageXaml"
Title="RelativeLayout - XAML"
BackgroundImage="deer.jpg">
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

- Por causa da forma que a página foi disposta, não é necessário para o código de procedimento apresentar capacidade de resposta.
- O `ScrollView` está sendo usado para permitir que o rótulo fique visível, mesmo quando a altura da tela é menor que a soma das alturas fixas dos botões e a imagem.

### <a name="grid"></a>Grade

Considere o aplicativo a seguir, exibido em Retrato:

![](device-orientation-images/photo-grid-portrait.png "Aplicativo de fotos em Retrato")

e paisagem:

![](device-orientation-images/photo-grid-landscape.png "Aplicativo de fotos em Paisagem")

Isso é feito com o XAML a seguir:

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

Junto com o seguinte código de procedimento para manipular as alterações de rotação:

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

- Por causa da forma que a página tem sido estabelecida, há um método para alterar o posicionamento de grade dos controles.


## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemplo de BusinessTumble (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
- [Layout dinâmico (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ResponsiveLayout)
- [Exibir uma imagem com base na orientação da tela](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/controls/screen-orientation/)
