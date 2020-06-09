---
Título: "orientação do dispositivo" Descrição: "Este artigo explica como fazer layout de Xamarin.Forms aplicativos que parecem ótimos em orientações retrato e paisagem".
MS. Prod: xamarin MS. AssetID: 11A1D327-2DF3-4F3B-810D-6C95B71D27B2 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/24/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="device-orientation"></a>Orientação do dispositivo

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)

É importante considerar como seu aplicativo será usado e como a orientação paisagem pode ser incorporada para melhorar a experiência do usuário. Layouts individuais podem ser criados para acomodar várias orientações e usar melhor o espaço disponível. No nível do aplicativo, a rotação pode ser desabilitada ou habilitada.

## <a name="controlling-orientation"></a>Controlando a orientação

Ao usar o Xamarin.Forms , o método com suporte para controlar a orientação do dispositivo é usar as configurações para cada projeto individual.

### <a name="ios"></a>iOS

No iOS, a orientação do dispositivo é configurada para aplicativos que usam o arquivo **info. plist** . Use as opções do IDE na parte superior deste documento para selecionar quais instruções você gostaria de ver:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

No Visual Studio, abra o projeto do iOS e abra **info. plist**. O arquivo será aberto em um painel de configuração, começando com a guia informações de implantação do iPhone:

![Informações de implantação do iPhone no Visual Studio](device-orientation-images/orientation-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, abra o projeto do iOS e abra o **info. plist**. Na guia **aplicativo** , as seções estarão disponíveis para definir a orientação:

![Informações de implantação do iPhone no Visual Studio para Mac](device-orientation-images/orientation-vsmac.png)

Se você preferir editar os valores usando uma interface de editor de chave-valor, selecione a guia **fonte**> na parte inferior da tela:

![Orientações de dispositivo com suporte no Visual Studio para Mac](device-orientation-images/orientation-source-vsmac.png)

-----

### <a name="android"></a>Android

Para controlar a orientação no Android, abra **MainActivity.cs** e defina a orientação usando o atributo decoração da `MainActivity` classe:

```csharp
namespace MyRotatingApp.Droid
{
    [Activity (Label = "MyRotatingApp.Droid", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, ScreenOrientation = ScreenOrientation.Landscape)] //This is what controls orientation
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate (Bundle bundle)
...
```

O Xamarin. Android dá suporte a várias opções para especificar a orientação:

- **Paisagem** &ndash; força a orientação do aplicativo a ser paisagem, independentemente dos dados do sensor.
- **Retrato** &ndash; força a orientação do aplicativo a ser retrato, independentemente dos dados do sensor.
- **Usuário** &ndash; do faz com que o aplicativo seja apresentado usando a orientação preferencial do usuário.
- **Por trás** &ndash; faz com que a orientação do aplicativo seja igual à orientação da [atividade](xref:Android.App.Activity) por trás dela.
- **Sensor** &ndash; faz com que a orientação do aplicativo seja determinada pelo sensor, mesmo se o usuário tiver desabilitado a rotação automática.
- **SensorLandscape** &ndash; faz com que o aplicativo use a orientação paisagem ao usar dados de sensor para alterar a direção com que a tela está voltada (para que a tela não seja vista como de cabeça para baixo).
- **SensorPortrait** &ndash; faz com que o aplicativo use a orientação retrato ao usar dados de sensor para alterar a direção com que a tela está voltada (para que a tela não seja vista como de cabeça para baixo).
- **ReverseLandscape** &ndash; faz com que o aplicativo use a orientação paisagem, voltada para a direção oposta, de forma a aparecer "de cabeça para baixo".
- **ReversePortrait** &ndash; faz com que o aplicativo use orientação retrato, voltado para a direção oposta, para que pareça "de cabeça para baixo".
- **FullSensor** &ndash; faz com que o aplicativo confie nos dados do sensor para selecionar a orientação correta (fora dos possíveis 4).
- **FullUser** &ndash; faz com que o aplicativo use as preferências de orientação do usuário. Se a rotação automática estiver habilitada, todas as quatro orientações poderão ser usadas.
- **Userlandscape** &ndash; _ \[ Sem suporte \] _ faz com que o aplicativo use a orientação paisagem, a menos que o usuário tenha a rotação automática habilitada; nesse caso, ele usará o sensor para determinar a orientação. Esta opção interromperá a compilação.
- **Userretrato** &ndash; _ \[ Sem suporte \] _ faz com que o aplicativo use a orientação retrato, a menos que o usuário tenha a rotação automática habilitada; nesse caso, ele usará o sensor para determinar a orientação. Esta opção interromperá a compilação.
- **Bloqueado** &ndash; Sem _ \[ suporte \] _ faz com que o aplicativo use a orientação da tela, o que estiver na inicialização, não respondendo às alterações na orientação física do dispositivo. Esta opção interromperá a compilação.

Observe que as APIs nativas do Android fornecem muito controle sobre como a orientação é gerenciada, incluindo opções que se contradizem explicitamente às preferências expressas do usuário.

### <a name="universal-windows-platform"></a>Plataforma universal do Windows

No Plataforma Universal do Windows (UWP), as orientações com suporte são definidas no arquivo **Package. appxmanifest** . Abrir o manifesto revelará um painel de configuração onde as orientações com suporte podem ser selecionadas.

## <a name="reacting-to-changes-in-orientation"></a>Reagindo a alterações na orientação

Xamarin.Formsnão oferece nenhum evento nativo para notificar seu aplicativo sobre alterações de orientação no código compartilhado. No entanto, [Xamarin.Essentials](~/essentials/index.md) contém uma `DeviceDisplay` classe [] que fornece notificações de alterações de orientação.

Para detectar orientações sem Xamarin.Essentials , monitore o `SizeChanged` evento do `Page` , que é acionado quando a largura ou a altura das `Page` alterações. Quando a largura do `Page` for maior que a altura, o dispositivo estará no modo paisagem. Para obter mais informações, consulte [exibir uma imagem com base na orientação da tela](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation).

Como alternativa, é possível substituir o [`OnSizeAllocated`](xref:Xamarin.Forms.Page.OnSizeAllocated*) método em um `Page` , inserindo qualquer lógica de alteração de layout lá. O `OnSizeAllocated` método é chamado sempre que um `Page` é alocado um novo tamanho, o que acontece sempre que o dispositivo é girado. Observe que a implementação base de `OnSizeAllocated` executa funções de layout importantes, portanto, é importante chamar a implementação base na substituição:

```csharp
protected override void OnSizeAllocated(double width, double height)
{
    base.OnSizeAllocated(width, height); //must be called
}
```

A falha ao executar essa etapa resultará em uma página que não está funcionando.

Observe que o `OnSizeAllocated` método pode ser chamado muitas vezes quando um dispositivo é girado. A alteração do layout a cada vez é um desperdício de recursos e pode levar à cintilação. Considere usar uma variável de instância em sua página para controlar se a orientação está em paisagem ou retrato e redesenhar apenas quando houver uma alteração:

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

Depois que uma alteração na orientação do dispositivo for detectada, talvez você queira adicionar ou remover exibições adicionais de/para sua interface do usuário para reagir à alteração no espaço disponível. Por exemplo, considere a calculadora interna em cada plataforma em retrato:

![](device-orientation-images/calculator-portrait.png "Calculator Application in Portrait")

e paisagem:

![](device-orientation-images/calculator-landscape.png "Calculator Application in Landscape")

Observe que os aplicativos tiram proveito do espaço disponível adicionando mais funcionalidade em paisagem.

## <a name="responsive-layout"></a>Layout responsivo

É possível criar interfaces usando os layouts internos para que eles se migrem normalmente quando o dispositivo for girado. Ao criar interfaces que continuarão a ser atraentes ao responder a alterações na orientação, considere as seguintes regras gerais:

- **Preste atenção às proporções** &ndash; as alterações na orientação podem causar problemas quando determinadas pressuposições são feitas com relação às proporções. Por exemplo, uma exibição que teria muito espaço em 1/3 do espaço vertical de uma tela em retrato pode não caber em 1/3 do espaço vertical em paisagem.
- **Tenha cuidado com valores** &ndash; absolutos valores absolutos (pixel) que fazem sentido em retrato podem não fazer sentido em paisagem. Quando valores absolutos são necessários, use layouts aninhados para isolar seu impacto. Por exemplo, seria razoável usar valores absolutos em um `TableView` `ItemTemplate` quando o modelo de item tiver uma altura uniforme garantida.

As regras acima também se aplicam ao implementar interfaces para vários tamanhos de tela e, em geral, são consideradas práticas recomendadas. O restante deste guia explicará exemplos específicos de layouts responsivos usando cada um dos layouts primários no Xamarin.Forms .

> [!NOTE]
> Para maior clareza, as seções a seguir demonstram como implementar layouts responsivos usando apenas um tipo de `Layout` cada vez. Na prática, geralmente é mais simples misturar `Layout` os s para obter um layout desejado usando o mais simples ou mais intuitivo `Layout` para cada componente.

### <a name="stacklayout"></a>StackLayout

Considere o seguinte aplicativo, exibido em retrato:

![](device-orientation-images/photo-stack-portrait.png "Photo Application in Portrait")

e paisagem:

![](device-orientation-images/photo-stack-landscape.png "Photo Application in Landscape")

Isso é feito com o seguinte XAML:

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

Alguns C# são usados para alterar a orientação de `outerStack` com base na orientação do dispositivo:

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

- `outerStack`é ajustado para apresentar a imagem e os controles como uma pilha horizontal ou vertical, dependendo da orientação, para aproveitar melhor o espaço disponível.

### <a name="absolutelayout"></a>AbsoluteLayout

Considere o seguinte aplicativo, exibido em retrato:

![](device-orientation-images/photo-abs-portrait.png "Photo Application in Portrait")

e paisagem:

![](device-orientation-images/photo-abs-landscape.png "Photo Application in Landscape")

Isso é feito com o seguinte XAML:

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

- Devido à maneira como a página foi disposta, não há necessidade de código de procedimento para introduzir a capacidade de resposta.
- O `ScrollView` está sendo usado para permitir que o rótulo fique visível mesmo quando a altura da tela for menor que a soma das alturas fixas dos botões e da imagem.

### <a name="relativelayout"></a>RelativeLayout

Considere o seguinte aplicativo, exibido em retrato:

![](device-orientation-images/photo-rel-portrait.png "Photo Application in Portrait")

e paisagem:

![](device-orientation-images/photo-rel-landscape.png "Photo Application in Landscape")

Isso é feito com o seguinte XAML:

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

- Devido à maneira como a página foi disposta, não há necessidade de código de procedimento para introduzir a capacidade de resposta.
- O `ScrollView` está sendo usado para permitir que o rótulo fique visível mesmo quando a altura da tela for menor que a soma das alturas fixas dos botões e da imagem.

### <a name="grid"></a>Grade

Considere o seguinte aplicativo, exibido em retrato:

![](device-orientation-images/photo-grid-portrait.png "Photo Application in Portrait")

e paisagem:

![](device-orientation-images/photo-grid-landscape.png "Photo Application in Landscape")

Isso é feito com o seguinte XAML:

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

Juntamente com o seguinte código de procedimento para lidar com as alterações de rotação:

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

- Devido à maneira como a página foi disposta, há um método para alterar o posicionamento da grade dos controles.

## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
- [Layout responsivo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-responsivelayout)
- [Exibir uma imagem com base na orientação da tela](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/screen-orientation)
