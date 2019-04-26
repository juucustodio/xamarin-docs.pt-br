---
title: Botão de xamarin. Forms
description: O botão responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 3b23a1a07741cd048ea034b2b39b5f9cde902dc6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020055"
---
# <a name="xamarinforms-button"></a>Botão de xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)

_O botão responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica._

O [ `Button` ](xref:Xamarin.Forms.Button) é o controle interativo mais fundamental em todas as do xamarin. Forms. O `Button` normalmente exibe uma cadeia de caracteres de texto curto indicando um comando, mas também pode exibe uma imagem de bitmap, ou uma combinação de texto e uma imagem. O usuário pressiona o `Button` com um dedo ou clica nele com o mouse para iniciar esse comando.

A maioria dos tópicos discutidos a seguir corresponde às páginas na [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo.

## <a name="handling-button-clicks"></a>Botão de manuseio de cliques

`Button` define uma [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento que é acionado quando o usuário toca o `Button` com um ponteiro de mouse ou o dedo. O evento é acionado quando o botão de dedo ou o mouse é liberado da superfície do `Button`. O `Button` deve ter seu [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade definida como `true` para que ele responda aos toques.

O **básica de clique do botão** página no [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo demonstra como criar uma instância de um `Button` em XAML e o identificador de seu `Clicked` eventos. O **BasicButtonClickPage.xaml** arquivo contém uma `StackLayout` tanto com um `Label` e um `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

O `Button` tende a ocupar todo o espaço permitido para ele. Por exemplo, se você não definir a `HorizontalOptions` propriedade de `Button` para algo diferente de `Fill`, o `Button` ocupa a largura total do seu pai.

Por padrão, o `Button` é retangular, mas você pode atribuir os cantos arredondado de it, usando o [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) propriedade, conforme descrito abaixo na seção [ **botão aparência** ](#button-appearance).

O [ `Text` ](xref:Xamarin.Forms.Button.Text) propriedade especifica o texto que aparece no `Button`. O [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento é definido como um manipulador de eventos chamado `OnButtonClicked`. Esse manipulador está localizado no arquivo code-behind, **BasicButtonClickPage.xaml.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

Quando o `Button` é tocado, o `OnButtonClicked` método é executado. O `sender` argumento é o `Button` objeto responsável por esse evento. Você pode usar isso para acessar o `Button` objeto, ou para distinguir entre vários `Button` objetos que compartilham o mesmo `Clicked` eventos.

Essa determinada `Clicked` manipulador chama uma função de animação que gira o `Label` 360 graus em 1000 milissegundos. Aqui está o programa em execução em dispositivos iOS e Android e como um aplicativo da plataforma Universal do Windows (UWP) na área de trabalho do Windows 10:

[![Clique de botão básica](button-images/BasicButtonClick.png "clique de botão básicas")](button-images/BasicButtonClick-Large.png#lightbox "clique de botão básico")

Observe que o `OnButtonClicked` método inclui o `async` modificador porque `await` é usado dentro do manipulador de eventos. Um `Clicked` manipulador de eventos exige as `async` modificador somente se o corpo do manipulador usa `await`.

Cada plataforma renderiza o `Button` em sua própria maneira específica. No [ **botão aparência** ](#button-appearance) seção, você verá como definir cores e fazer o `Button` borda visível para aparências mais personalizadas. `Button` implementa o [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) interface, portanto, ela inclui [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), e [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)propriedades.

## <a name="creating-a-button-in-code"></a>Criando um botão no código

É comum para criar uma instância de um `Button` no XAML, mas você também pode criar um `Button` no código. Isso pode ser conveniente quando seu aplicativo precisa para criar vários botões com base nos dados que é enumeráveis com um `foreach` loop.

O **código de clique de botão** página demonstra como criar uma página que é funcionalmente equivalente à **básica de clique de botão** página, mas inteiramente no C#:

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

Tudo o que é feito no construtor da classe. Porque o `Clicked` manipulador é apenas uma instrução longa, ele pode ser anexado ao evento muito simples:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

É claro, você também pode definir o manipulador de eventos como um método separado (assim como o `OnButtonClick` método no **básico de clique do botão**) e anexar esse método para o evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Desabilitação do botão

Às vezes, um aplicativo está em um estado específico em que um determinado `Button` clique não é uma operação válida. Nesses casos, o `Button` deve ser desabilitado definindo seu `IsEnabled` propriedade `false`. O exemplo clássico é um `Entry` controle de um nome de arquivo acompanhado por uma abertura de arquivo `Button`: O `Button` deve ser habilitada apenas se algum texto foi digitado para o `Entry`.
Você pode usar um `DataTrigger` para essa tarefa, conforme mostrado na [ **gatilhos de dados** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) artigo.

## <a name="using-the-command-interface"></a>Usando a interface de comando

É possível que um aplicativo para responder às `Button` toques sem tratamento a `Clicked` eventos. O `Button` implementa um mecanismo de notificação alternativo chamado a _comando_ ou _comandando_ interface. Isso consiste em duas propriedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) do tipo [ `ICommand` ](xref:System.Windows.Input.ICommand), uma interface definida a [ `System.Windows.Input` ](xref:System.Windows.Input) namespace.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propriedade do tipo [ `Object` ](xref:System.Object).

Essa abordagem é especialmente adequada em conexão com a vinculação de dados e especialmente quando a implementação da arquitetura do Model-View-ViewModel (MVVM). Esses tópicos são discutidos nos artigos [associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md), [de associações de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), e [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

Em um aplicativo MVVM, o ViewModel define propriedades do tipo `ICommand` conectados, em seguida, o XAML `Button` elementos com associações de dados. Xamarin. Forms também define [ `Command` ]((xref:Xamarin.Forms.Command)) e [ `Command<T>` ](xref:Xamarin.Forms.Command`1) as classes que implementam o `ICommand` de interface e auxiliar o ViewModel na definição de propriedades de tipo `ICommand`.

Comandos é descrito mais detalhadamente no artigo [ **a Interface de comando** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , mas o **básica de botão de comando** página o [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo mostra a abordagem básica.

O `CommandDemoViewModel` classe é um ViewModel muito simple que define uma propriedade do tipo `double` denominado `Number`e duas propriedades do tipo `ICommand` denominada `MultiplyBy2Command` e `DivideBy2Command`:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

Os dois `ICommand` as propriedades são inicializadas no construtor da classe com dois objetos do tipo `Command`. O `Command` construtores incluem uma pequena função (chamado de `execute` argumento do construtor) que dobra ou reduz pela metade o `Number` propriedade.

O **BasicButtonCommand.xaml** arquivo define seu `BindingContext` a uma instância do `CommandDemoViewModel`. O `Label` elemento e dois `Button` elementos contenham associações para as três propriedades em `CommandDemoViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

Como os dois `Button` tocados elementos, os comandos são executados, e o número de alterações de valor:

[![Comando básico Button](button-images/BasicButtonCommand.png "básica do botão comando")](button-images/BasicButtonCommand-Large.png#lightbox)

A vantagem dessa abordagem em relação `Clicked` manipuladores é que toda a lógica que envolvem a funcionalidade dessa página está localizada no ViewModel em vez de um arquivo code-behind, obtendo uma melhor separação da interface do usuário da lógica de negócios.

Também é possível que o `Command` objetos para controlar a habilitação e desabilitação do `Button` elementos. Por exemplo, suponha que você deseja limitar o intervalo de valores numéricos entre 2<sup>10</sup> e 2<sup>&ndash;10</sup>. Você pode adicionar outra função para o construtor (chamado de `canExecute` argumento) que retorna `true` se o `Button` deve ser habilitado. Aqui está a modificação para o `CommandDemoViewModel` construtor:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

As chamadas para o `ChangeCanExecute` método de `Command` são necessárias para que o `Command` método pode chamar o `canExecute` método e determinar se o `Button` deve ser desabilitado ou não. Com essa alteração de código, como o número de atingir o limite, o `Button` está desabilitado:

[![Comando do botão básico - modificado](button-images/BasicButtonCommandModified.png "comando botão básico - modificado")](button-images/BasicButtonCommandModified-Large.png#lightbox)

É possível que duas ou mais `Button` elementos a ser associado ao mesmo `ICommand` propriedade. O `Button` elementos podem ser diferenciados com o [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) propriedade do `Button`. Nesse caso, você desejará usar o genérico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe. O `CommandParameter` objeto é então passado como um argumento para o `execute` e `canExecute` métodos. Essa técnica é mostrada em detalhes na [ **comandos básicos** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) seção os [ **Interface de comando** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) artigo.

O [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo também usa essa técnica em seu `MainPage` classe. O **MainPage. XAML** arquivo contém um `Button` para cada página de exemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Cada `Button` tem sua `Command` propriedade associada a uma propriedade denominada `NavigateCommand`e o `CommandParameter` é definido como um [ `Type` ](xref:System.Type) objeto correspondente a uma das classes de página no projeto.

Que `NavigateCommand` propriedade é do tipo `ICommand` e é definido no arquivo code-behind:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

O construtor inicializa o `NavigateCommand` propriedade para um `Command<Type>` porque `Type` é o tipo do `CommandParameter` objeto definido no arquivo XAML. Isso significa que o `execute` método tem um argumento do tipo `Type` que corresponde a este `CommandParameter` objeto. A função cria uma instância de página e, em seguida, navega até ela.

Observe que o construtor é concluído, definindo seu `BindingContext` a mesmo. Isso é necessário para as propriedades no arquivo XAML para associar o `NavigateCommand` propriedade.

## <a name="pressing-and-releasing-the-button"></a>Pressionando e soltando o botão

Além de `Clicked` evento `Button` também define [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) e [ `Released` ](xref:Xamarin.Forms.Button.Released) eventos. O `Pressed` evento ocorre quando um dedo pressiona em um `Button`, ou um botão do mouse é pressionado com o ponteiro posicionado sobre o `Button`. O `Released` evento ocorre quando o botão de dedo ou o mouse é liberado. Em geral, uma `Clicked` evento também é acionado ao mesmo tempo que o `Released` evento, mas se o ponteiro do mouse ou o dedo desliza para fora a superfície do `Button` antes de serem liberadas, o `Clicked` não pode ocorrer um evento.

O `Pressed` e `Released` eventos não são usados com frequência, mas eles podem ser usados para finalidades especiais, conforme demonstrado na **pressione e solte o botão** página. O arquivo XAML contém um `Label` e uma `Button` com os manipuladores anexados para o `Pressed` e `Released` eventos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

O arquivo code-behind anima a `Label` quando um `Pressed` evento ocorre, mas suspende a rotação quando um `Released` evento ocorre:

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

O resultado é que o `Label` apenas gira enquanto é de um dedo em contato com o `Button`e é interrompido quando o dedo é liberado:

[![Pressione e solte o botão](button-images/PressAndReleaseButton.png "pressione e solte o botão")](button-images/PressAndReleaseButton-Large.png)

Esse tipo de comportamento tem aplicativos de jogos: Um dedo mantido um `Button` pode fazer um objeto de tela em Mover em uma direção específica.

<a name="button-appearance" />

## <a name="button-appearance"></a>Aparência do botão

O `Button` herda ou define várias propriedades que afetam sua aparência:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) é a cor do `Button` texto
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) é a cor do plano de fundo para que o texto
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) é a cor de uma área ao redor do `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) a família de fontes para o texto é usada
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) é o tamanho do texto
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) Indica se o texto em negrito ou itálico
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) é a largura da borda
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) é o raio do canto das `Button`

> [!NOTE]
> O `Button` classe também tem [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Button.Padding) propriedades que controlam o comportamento de layout a `Button`. Para obter mais informações, consulte [margem e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Os efeitos de seis dessas propriedades (exceto `FontFamily` e `FontAttributes`) são demonstradas a **aparência botão** página. Outra propriedade, [ `Image` ](xref:Xamarin.Forms.Button.Image), é discutida na seção [ **usar bitmaps com o botão**](#image-button).

Todas as associações de dados e modos de exibição na **aparência botão** página são definidos no arquivo XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

O `Button` na parte superior da página tem suas três `Color` propriedades associadas a `Picker` elementos na parte inferior da página. Os itens a `Picker` elementos são as cores do `NamedColor` incluído no projeto de classe. Três `Slider` elementos contêm as vinculações bidirecionais para o `FontSize`, `BorderWidth`, e `CornerRadius` propriedades do `Button`.

Esse programa permite que você experimente combinações de todas essas propriedades:

[![Botão de aparência](button-images/ButtonAppearance.png "aparência de botão")](button-images/ButtonAppearance-Large.png)

Para ver os `Button` borda, você precisará definir um `BorderColor` para algo diferente de `Default`e o `BorderWidth` como um valor positivo.

No iOS, você observará que as larguras de borda grande atrapalham a para o interior do `Button` e interferir com a exibição do texto. Se você optar por usar um border com um iOS `Button`, você provavelmente vai querer começar e terminar o `Text` propriedade com espaços para manter sua visibilidade.

Na UWP, selecionando uma `CornerRadius` que excede a metade da altura do `Button` gera uma exceção.

## <a name="button-visual-states"></a>Estados visuais do botão

[`Button`](xref:Xamarin.Forms.Button) tem um `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual para o `Button` quando pressionado pelo usuário, desde que ele está habilitado.

O exemplo XAML a seguir mostra como definir um estado visual para o `Pressed` estado:

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Button>
```

O `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Especifica que, quando o [ `Button` ](xref:Xamarin.Forms.Button) for pressionado, seu [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade será alterada de seu valor padrão de 1 para 0,8. O `Normal` `VisualState` Especifica que, quando o `Button` está em um estado normal, seu `Scale` propriedade será definida como 1. Portanto, o efeito geral é que quando o `Button` é pressionado, ele será escalada novamente para ser um pouco menores e quando o `Button` é lançado, ele será escalada novamente ao seu tamanho padrão.

Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Criando um botão de alternância

É possível que a subclasse `Button` para que ele funcione como um comutador ligado / desligado: Toque no botão de uma vez para o botão de alternância no e toque novamente para ativá-lo.

O seguinte `ToggleButton` classe deriva `Button` e define um novo evento chamado `Toggled` e uma propriedade booleana chamada `IsToggled`. Essas são as mesmas propriedades de dois definidas pelo xamarin. Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

O `ToggleButton` construtor anexa um manipulador para o `Clicked` evento para que ele possa alterar o valor da `IsToggled` propriedade. O `OnIsToggledChanged` método dispara o `Toggled` eventos.

A última linha do `OnIsToggledChanged` chamadas de método estático `VisualStateManager.GoToState` método com o texto de duas cadeias de caracteres "ToggledOn" e "ToggledOff". Você pode ler sobre esse método e como seu aplicativo pode responder a estados visuais no artigo [ **o Gerenciador de estado Visual xamarin. Forms**](~/xamarin-forms/user-interface/visual-state-manager.md).

Porque `ToggleButton` faz a chamada para `VisualStateManager.GoToState`, a classe em si não precisa incluir quaisquer instalações adicionais para alterar a aparência do botão com base em seu `IsToggled` estado. Ou seja, a responsabilidade do XAML que hospeda o `ToggleButton`.

O **demonstração do botão de alternância** página contém duas instâncias do `ToggleButton`, inclusive a marcação de Gerenciador de estado Visual que define os `Text`, `BackgroundColor`, e `TextColor` do botão de acordo com o estado visual:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

O `Toggled` manipuladores de eventos estão no arquivo code-behind. Eles são responsáveis por configuração o `FontAttributes` propriedade do `Label` com base no estado dos botões:

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

Aqui está o programa em execução no iOS, Android e UWP:

[![Ativar/desativar Button Demo](button-images/ToggleButtonDemo.png "alternar Button Demo")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>Usar bitmaps com botões

O `Button` classe define um [ `Image` ](xref:Xamarin.Forms.Button.Image) que permite que você exiba uma imagem de bitmap na propriedade de `Button`, sozinho ou em combinação com o texto. Você também pode especificar como o texto e imagem são organizados.

O `Image` propriedade é do tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), que significa que os bitmaps devem ser armazenados como recursos nos projetos de plataforma individual e não no projeto da biblioteca .NET Standard.

Cada plataforma suportada pelo xamarin. Forms permite que as imagens sejam armazenados em vários tamanhos para resoluções de pixel diferente do que o aplicativo pode ser executado em vários dispositivos. Esses bitmaps de vários são nomeados ou armazenadas de tal forma que o sistema operacional pode escolher a melhor correspondência para o dispositivo de vídeo do resolução de vídeo.

Para um bitmap em um `Button`, geralmente é o melhor tamanho entre unidades independentes de dispositivo de 32 e 64, dependendo de quão grande você deseja que ele fique. As imagens usadas neste exemplo baseiam-se um tamanho de 48 unidades independentes de dispositivo.

No projeto do iOS, o **recursos** pasta contém três tamanhos desta imagem:

- Um bitmap de 48 pixels quadrado armazenado como **/Resources/MonkeyFace.png**
- Um bitmap de 96 pixels quadrado armazenado como **/Resource/MonkeyFace@2x.png**
- Um bitmap de 144 pixels quadrado armazenado como **/Resource/MonkeyFace@3x.png**

Todos os três bitmaps recebeu uma **ação de compilação** dos **BundleResource**.

Para o projeto Android, os bitmaps todos têm o mesmo nome, mas eles são armazenados em subpastas diferentes do **recursos** pasta:

- Um bitmap de 72 pixels quadrado armazenado como **/Resources/drawable-hdpi/MonkeyFace.png**
- Um bitmap de 96 pixels quadrado armazenado como **/Resources/drawable-xhdpi/MonkeyFace.png**
- Um bitmap de 144 pixels quadrado armazenado como **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Um bitmap de 192 pixels quadrado armazenado como **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Eles receberam um **ação de compilação** dos **AndroidResource**.

No projeto UWP, os bitmaps podem ser armazenados em qualquer lugar no projeto, mas eles geralmente são armazenados em uma pasta personalizada ou o **ativos** pasta existente. O projeto UWP contém esses bitmaps:

- Um bitmap de 48 pixels quadrado armazenado como **/Assets/MonkeyFace.scale-100.png**
- Um bitmap de 96 pixels quadrado armazenado como **/Assets/MonkeyFace.scale-200.png**
- Um bitmap de 192 pixels quadrado armazenado como **/Assets/MonkeyFace.scale-400.png**

Eles foram fornecidos um **ação de compilação** dos **conteúdo**.

Você pode especificar como o `Text` e `Image` propriedades forem organizadas na `Button` usando o [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) propriedade `Button`. Essa propriedade é do tipo [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), que é uma classe incorporada no `Button`. O [construtor](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) tem dois argumentos:

- Um membro do [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumeração: `Left`, `Top`, `Right`, ou `Bottom` indicando como o bitmap aparece em relação ao texto.
- Um `double` valor para o espaçamento entre o bitmap e o texto.

Os padrões são `Left` e 10 unidades. Duas propriedades somente leitura do `ButtonContentLayout` nomeado [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) e [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) forneça os valores dessas propriedades.

No código, você pode criar uma `Button` e defina o `ContentLayout` propriedade como este:

```csharp
Button button = new Button
{
    Text = "button text",
    Image = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

No XAML, você precisa especificar somente o membro de enumeração ou o espaçamento ou ambos em qualquer ordem separados por vírgulas:

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

O **imagem Button Demo** página usa `OnPlatform` para especificar nomes de arquivo diferente para o iOS, Android e UWP arquivos de bitmap. Se você quiser usar o mesmo nome de arquivo para cada plataforma e evite o uso de `OnPlatform`, você precisa para armazenar os bitmaps UWP no diretório raiz do projeto.

A primeira `Button` no **imagem Button Demo** página conjuntos a `Image` propriedade, mas não o `Text` propriedade:

```xaml
<Button>
    <Button.Image>
        <OnPlatform x:TypeArguments="FileImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.Image>
</Button>
```

Se os bitmaps UWP são armazenados no diretório raiz do projeto, essa marcação pode ser consideravelmente simplificada:

```xaml
<Button Image="MonkeyFace.png" />
```

Para evitar muita marcação repetitivas na **ImageButtonDemo.xaml** arquivo implícito `Style` também é definido para configurar o `Image` propriedade. Isso `Style` é aplicada automaticamente a outros cinco `Button` elementos. Aqui está o arquivo XAML completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="Image">
                    <OnPlatform x:TypeArguments="FileImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.Image>
                <OnPlatform x:TypeArguments="FileImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.Image>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

Os quatro final `Button` elementos fazem uso do `ContentLayout` propriedade para especificar uma posição e o espaçamento entre o texto e o bitmap:

[![Demonstração do botão de imagem](button-images/ImageButtonDemo.png "Button Demo de imagem")](button-images/ImageButtonDemo-Large.png#lightbox)

Agora você já viu as várias maneiras que você pode manipular `Button` eventos e altere o `Button` aparência.

## <a name="related-links"></a>Links relacionados

- [Exemplo de ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Botão de API](xref:Xamarin.Forms.Button)
