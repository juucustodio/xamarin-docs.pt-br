---
title: Xamarin.Forms Button
description: O botão responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fba8dcb344f79c21815a58cff0d8132967381cca
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556731"
---
# <a name="no-locxamarinforms-button"></a>Xamarin.Forms Button

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_O botão responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica._

O [`Button`](xref:Xamarin.Forms.Button) é o controle interativo mais fundamental em todos os Xamarin.Forms . O `Button` geralmente exibe uma cadeia de caracteres de texto curta indicando um comando, mas também pode exibir uma imagem de bitmap ou uma combinação de texto e imagem. O usuário pressiona o `Button` com um dedo ou clica com um mouse para iniciar esse comando.

A maioria dos tópicos discutidos abaixo corresponde às páginas no exemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) .

## <a name="handling-button-clicks"></a>Manipulando cliques de botão

`Button` define um [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento que é disparado quando o usuário toca no `Button` com um ponteiro de dedo ou mouse. O evento é acionado quando o botão do dedo ou do mouse é liberado da superfície do `Button` . O `Button` deve ter sua [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade definida como para `true` que ele responda a toques.

A página de **clique do botão básico** no exemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) demonstra como instanciar um `Button` em XAML e manipular seu `Clicked` evento. O arquivo **BasicButtonClickPage. XAML** contém um `StackLayout` com um `Label` e um `Button` :

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

O `Button` tende a ocupar todo o espaço permitido para ele. Por exemplo, se você não definir a `HorizontalOptions` propriedade de `Button` como algo diferente de `Fill` , o `Button` ocupará a largura total de seu pai.

Por padrão, o `Button` é retangular, mas você pode dar a ele cantos arredondados usando a [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) propriedade, conforme descrito abaixo na aparência do [**botão**](#button-appearance)de seção.

A [`Text`](xref:Xamarin.Forms.Button.Text) propriedade especifica o texto que aparece no `Button` . O [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento é definido como um manipulador de eventos chamado `OnButtonClicked` . Esse manipulador está localizado no arquivo code-behind, **BasicButtonClickPage.XAML.cs**:

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

Quando o `Button` é tocado, o método `OnButtonClicked` é executado. O `sender` argumento é o `Button` objeto responsável por esse evento. Você pode usar isso para acessar o `Button` objeto ou para distinguir entre vários `Button` objetos que compartilham o mesmo `Clicked` evento.

Esse `Clicked` manipulador específico chama uma função de animação que gira os `Label` 360 graus em 1000 milissegundos. Aqui está o programa em execução em dispositivos iOS e Android e como um aplicativo Plataforma Universal do Windows (UWP) na área de trabalho do Windows 10:

[![Clique no botão básico](button-images/BasicButtonClick.png "Clique no botão básico")](button-images/BasicButtonClick-Large.png#lightbox "Clique no botão básico")

Observe que o `OnButtonClicked` método inclui o `async` modificador porque `await` é usado dentro do manipulador de eventos. Um `Clicked` manipulador de eventos requer o `async` Modificador somente se o corpo do manipulador usar `await` .

Cada plataforma renderiza a `Button` própria maneira específica. Na seção [**aparência do botão**](#button-appearance) , você verá como definir cores e tornar a `Button` borda visível para aparências mais personalizadas. `Button` implementa a [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) interface, de modo que ela inclui [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) [`FontSize`](xref:Xamarin.Forms.Button.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) as propriedades, e.

## <a name="creating-a-button-in-code"></a>Criando um botão no código

É comum instanciar um `Button` em XAML, mas você também pode criar um `Button` no código. Isso pode ser conveniente quando o aplicativo precisa criar vários botões com base em dados que são enumeráveis com um `foreach` loop.

A página de **clique do botão código** demonstra como criar uma página que é funcionalmente equivalente à página de **clique do botão básico** , mas inteiramente em C#:

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

Tudo é feito no construtor da classe. Como o `Clicked` manipulador é apenas uma instrução longa, ele pode ser anexado ao evento de forma muito simples:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

É claro que você também pode definir o manipulador de eventos como um método separado (assim como o `OnButtonClick` método no **clique do botão básico**) e anexar esse método ao evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Desabilitando o botão

Às vezes, um aplicativo está em um estado específico em que um determinado `Button` clique não é uma operação válida. Nesses casos, o `Button` deve ser desabilitado definindo sua `IsEnabled` propriedade como `false` . O exemplo clássico é um `Entry` controle para um nome de arquivo acompanhado por um File-Open `Button` : o `Button` deve ser habilitado somente se algum texto tiver sido digitado no `Entry` .
Você pode usar um `DataTrigger` para essa tarefa, conforme mostrado no artigo [**gatilhos de dados**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) .

## <a name="using-the-command-interface"></a>Usando a interface de comando

É possível que um aplicativo responda a `Button` toques sem manipular o `Clicked` evento. O `Button` implementa um mecanismo de notificação alternativo chamado de _comando_ ou interface de _comando_ . Isso consiste em duas propriedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) do tipo [`ICommand`](xref:System.Windows.Input.ICommand) , uma interface definida no [`System.Windows.Input`](xref:System.Windows.Input) namespace.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) Propriedade do tipo [`Object`](xref:System.Object) .

Essa abordagem é particularmente adequada em relação à vinculação de dados e, particularmente, ao implementar a arquitetura MVVM (Model-View-ViewModel). Esses tópicos são discutidos na [vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)de artigos, [de associações de dados para MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)e [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

Em um aplicativo MVVM, o ViewModel define as propriedades do tipo `ICommand` que são então conectadas aos `Button` elementos XAML com associações de dados. Xamarin.Forms também define [`Command`](xref:Xamarin.Forms.Command) e [`Command<T>`](xref:Xamarin.Forms.Command`1) classes que implementam a `ICommand` interface e auxiliam o ViewModel na definição de propriedades do tipo `ICommand` .

O comando é descrito mais detalhadamente no artigo [**interface de comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , mas a página de **comando do botão básico** no exemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) mostra a abordagem básica.

A `CommandDemoViewModel` classe é um ViewModel muito simples que define uma propriedade do tipo `double` chamado e `Number` duas propriedades do tipo `ICommand` chamado `MultiplyBy2Command` e `DivideBy2Command` :

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

As duas `ICommand` Propriedades são inicializadas no construtor da classe com dois objetos do tipo `Command` . Os `Command` construtores incluem uma função pequena (chamada de `execute` argumento do Construtor) que dobra ou reduz a `Number` propriedade.

O arquivo **BasicButtonCommand. XAML** define seu `BindingContext` como uma instância do `CommandDemoViewModel` . O `Label` elemento e dois `Button` elementos contêm associações para as três propriedades no `CommandDemoViewModel` :

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

Como os dois `Button` elementos são tocadas, os comandos são executados e o valor das alterações de número:

[![Comando de botão básico](button-images/BasicButtonCommand.png "Comando de botão básico")](button-images/BasicButtonCommand-Large.png#lightbox)

A vantagem dessa abordagem sobre `Clicked` os manipuladores é que toda a lógica envolvendo a funcionalidade desta página está localizada no ViewModel em vez do arquivo code-behind, obtendo uma separação melhor da interface do usuário da lógica de negócios.

Também é possível que os `Command` objetos controlem a habilitação e a desabilitação dos `Button` elementos. Por exemplo, suponha que você deseja limitar o intervalo de valores numéricos entre<sup>2 e</sup> 2<sup> &ndash; 10</sup>. Você pode adicionar outra função ao Construtor (chamado de `canExecute` argumento) que retorna `true` se o `Button` deve ser habilitado. Aqui está a modificação para o `CommandDemoViewModel` Construtor:

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

As chamadas para o `ChangeCanExecute` método de `Command` são necessárias para que o `Command` método possa chamar o `canExecute` método e determinar se o `Button` deve ser desabilitado ou não. Com essa alteração de código, à medida que o número atinge o limite, o `Button` é desabilitado:

[![Botão básico-modificado por comando](button-images/BasicButtonCommandModified.png "Botão básico-modificado por comando")](button-images/BasicButtonCommandModified-Large.png#lightbox)

É possível que dois ou mais `Button` elementos sejam associados à mesma `ICommand` propriedade. Os `Button` elementos podem ser diferenciados usando a [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propriedade de `Button` . Nesse caso, você desejará usar a classe genérica [`Command<T>`](xref:Xamarin.Forms.Command`1) . `CommandParameter`Em seguida, o objeto é passado como um argumento para os `execute` `canExecute` métodos e. Essa técnica é mostrada em detalhes na seção [**básica de comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) do artigo [**interface de comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) .

O exemplo [**ButtonDemos**](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) também usa essa técnica em sua `MainPage` classe. O arquivo **MainPage. XAML** contém um `Button` para cada página do exemplo:

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

Cada `Button` um tem sua `Command` propriedade associada a uma propriedade chamada `NavigateCommand` , e `CommandParameter` é definido como um [`Type`](xref:System.Type) objeto correspondente a uma das classes de página no projeto.

Essa `NavigateCommand` propriedade é do tipo `ICommand` e é definida no arquivo code-behind:

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

O construtor inicializa a `NavigateCommand` propriedade para um `Command<Type>` objeto porque `Type` é o tipo do `CommandParameter` objeto definido no arquivo XAML. Isso significa que o `execute` método tem um argumento do tipo `Type` que corresponde a esse `CommandParameter` objeto. A função instancia a página e, em seguida, navega até ela.

Observe que o Construtor conclui definindo seu `BindingContext` próprio. Isso é necessário para que as propriedades no arquivo XAML se associem à `NavigateCommand` propriedade.

## <a name="pressing-and-releasing-the-button"></a>Pressionar e liberar o botão

Além do `Clicked` evento, `Button` também define [`Pressed`](xref:Xamarin.Forms.Button.Pressed) e [`Released`](xref:Xamarin.Forms.Button.Released) eventos. O `Pressed` evento ocorre quando um dedo pressiona em um `Button` ou um botão do mouse é pressionado com o ponteiro posicionado sobre o `Button` . O `Released` evento ocorre quando o botão do dedo ou do mouse é liberado. Em geral, um `Clicked` evento também é disparado ao mesmo tempo que o `Released` evento, mas se o ponteiro do dedo ou do mouse deslizar para longe da superfície do `Button` antes de ser liberado, o `Clicked` evento poderá não ocorrer.

Os `Pressed` `Released` eventos e geralmente não são usados, mas podem ser usados para fins especiais, como demonstrado na página do **botão de pressionar e liberar** . O arquivo XAML contém um `Label` e um `Button` com manipuladores anexados para `Pressed` os `Released` eventos e:

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

O arquivo code-behind anima o `Label` quando um `Pressed` evento ocorre, mas suspende a rotação quando `Released` ocorre um evento:

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

O resultado é que a `Label` única gira enquanto um dedo está em contato com o `Button` e para quando o dedo é liberado:

[![Botão de pressionar e liberar](button-images/PressAndReleaseButton.png "Botão de pressionar e liberar")](button-images/PressAndReleaseButton-Large.png)

Esse tipo de comportamento tem aplicativos para jogos: um dedo mantido em um `Button` pode fazer com que um objeto na tela seja movido em uma direção específica.

## <a name="button-appearance"></a>Aparência do botão

O `Button` herda ou define várias propriedades que afetam sua aparência:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) é a cor do `Button` texto
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) é a cor do plano de fundo para esse texto
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) é a cor de uma área ao redor da `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) é a família de fontes usada para o texto
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) é o tamanho do texto
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indica se o texto está em itálico ou negrito
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) é a largura da borda
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) é o raio de canto do `Button`
- [`CharacterSpacing`](xref:Xamarin.Forms.Button.CharacterSpacing) é o espaçamento entre os caracteres do `Button` texto.
- `TextTransform` determina a capitalização do `Button` texto.

> [!NOTE]
> A `Button` classe também tem [`Margin`](xref:Xamarin.Forms.View.Margin) e [`Padding`](xref:Xamarin.Forms.Button.Padding) Propriedades que controlam o comportamento de layout do `Button` . Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Os efeitos de seis dessas propriedades (excluindo `FontFamily` e `FontAttributes` ) são demonstrados na página **aparência do botão** . Outra propriedade, [`Image`](xref:Xamarin.Forms.Button.ImageSource) , é discutida na seção [**usando bitmaps com o botão**](#using-bitmaps-with-buttons).

Todas as exibições e associações de dados na página **aparência do botão** são definidas no arquivo XAML:

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

O `Button` na parte superior da página tem suas três `Color` propriedades associadas aos `Picker` elementos na parte inferior da página. Os itens nos `Picker` elementos são cores da `NamedColor` classe incluída no projeto. Três `Slider` elementos contêm associações bidirecionais às `FontSize` `BorderWidth` Propriedades, e `CornerRadius` do `Button` .

Este programa permite que você experimente combinações de todas essas propriedades:

[![Aparência do botão](button-images/ButtonAppearance.png "Aparência do botão")](button-images/ButtonAppearance-Large.png)

Para ver a `Button` borda, você precisará definir um `BorderColor` para algo diferente de `Default` e o `BorderWidth` para um valor positivo.

No iOS, você observará que as larguras de bordas grandes são invasos no interior do `Button` e interferem na exibição do texto. Se você optar por usar uma borda com um iOS `Button` , provavelmente desejará começar e terminar a `Text` propriedade com espaços para manter sua visibilidade.

Em UWP, selecionar um `CornerRadius` que exceda metade da altura do `Button` gera uma exceção.

## <a name="button-visual-states"></a>Estados visuais de botão

[`Button`](xref:Xamarin.Forms.Button) tem um `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração Visual para o `Button` quando pressionado pelo usuário, desde que ele esteja habilitado.

O exemplo de XAML a seguir mostra como definir um estado visual para o `Pressed` estado:

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

O `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando o [`Button`](xref:Xamarin.Forms.Button) for pressionado, sua [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade será alterada de seu valor padrão de 1 para 0,8. O `Normal` `VisualState` especifica que quando o `Button` estiver em um estado normal, sua `Scale` propriedade será definida como 1. Portanto, o efeito geral é que quando o `Button` é pressionado, ele é redimensionado para ser ligeiramente menor e quando o `Button` é liberado, ele é redimensionado para seu tamanho padrão.

Para obter mais informações sobre os Estados visuais, consulte [o Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Criando um botão de alternância

É possível criar `Button` uma subclasse para que ela funcione como uma opção de ativação: toque no botão uma vez para alternar o botão e toque nele novamente para desativá-lo.

A classe a seguir `ToggleButton` deriva de `Button` e define um novo evento chamado `Toggled` e uma propriedade booliana chamada `IsToggled` . Essas são as mesmas duas propriedades definidas pelo Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) :

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

O `ToggleButton` Construtor anexa um manipulador ao evento para `Clicked` que ele possa alterar o valor da `IsToggled` propriedade. O `OnIsToggledChanged` método aciona o `Toggled` evento.

A última linha do `OnIsToggledChanged` método chama o método estático `VisualStateManager.GoToState` com as duas cadeias de texto "ToggledOn" e "ToggledOff". Você pode ler sobre esse método e como seu aplicativo pode responder a estados visuais no artigo [**o Xamarin.Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md).

Como `ToggleButton` faz a chamada para `VisualStateManager.GoToState` , a classe em si não precisa incluir recursos adicionais para alterar a aparência do botão com base em seu `IsToggled` estado. Essa é a responsabilidade do XAML que hospeda o `ToggleButton` .

A página de **demonstração do botão de alternância** contém duas instâncias do `ToggleButton` , incluindo marcação do Gerenciador de estado visual que define o `Text` , `BackgroundColor` e `TextColor` do botão com base no estado visual:

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

Os `Toggled` manipuladores de eventos estão no arquivo code-behind. Eles são responsáveis por definir a `FontAttributes` Propriedade do `Label` com base no estado dos botões:

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

Aqui está o programa em execução no iOS, no Android e no UWP:

[![Demonstração do botão de alternância](button-images/ToggleButtonDemo.png "Demonstração do botão de alternância")](button-images/ToggleButtonDemo-Large.png#lightbox)

## <a name="using-bitmaps-with-buttons"></a>Usando bitmaps com botões

A `Button` classe define uma [`ImageSource`](xref:Xamarin.Forms.Button.Image) propriedade que permite que você exiba uma imagem de bitmap no `Button` , seja sozinho ou em combinação com texto. Você também pode especificar como o texto e a imagem são organizados.

A `ImageSource` propriedade é do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , o que significa que os bitmaps podem ser carregados de um arquivo, recurso inserido, URI ou fluxo.

> [!NOTE]
> Embora um `Button` possa carregar um GIF animado, ele só exibirá o primeiro quadro do GIF.

Cada plataforma suportada pelo Xamarin.Forms permite que as imagens sejam armazenadas em vários tamanhos para diferentes resoluções de pixel dos vários dispositivos em que o aplicativo pode ser executado. Esses vários bitmaps são nomeados ou armazenados de forma que o sistema operacional possa escolher a melhor correspondência para a resolução de vídeo do dispositivo.

Para um bitmap em um `Button` , o melhor tamanho geralmente é entre 32 e 64 unidades independentes de dispositivo, dependendo de quão grande você deseja que seja. As imagens usadas neste exemplo baseiam-se em um tamanho de 48 unidades independentes de dispositivo.

No projeto do iOS, a pasta de **recursos** contém três tamanhos dessa imagem:

- Um bitmap quadrado de 48 pixels armazenado como **/Resources/MonkeyFace.png**
- Um bitmap quadrado de 96 pixels armazenado como **/Resource/MonkeyFace@2x.png**
- Um bitmap quadrado de 144 pixels armazenado como **/Resource/MonkeyFace@3x.png**

Todos os três bitmaps receberam uma **ação de compilação** de **BundleResource**.

Para o projeto do Android, todos os bitmaps têm o mesmo nome, mas eles são armazenados em subpastas diferentes da pasta de **recursos** :

- Um bitmap quadrado de 72 pixels armazenado como **/Resources/drawable-hdpi/MonkeyFace.png**
- Um bitmap quadrado de 96 pixels armazenado como **/Resources/drawable-xhdpi/MonkeyFace.png**
- Um bitmap quadrado de 144 pixels armazenado como **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Um bitmap quadrado de 192 pixels armazenado como **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Elas receberam uma **ação de compilação** de **AndroidResource**.

No projeto UWP, os bitmaps podem ser armazenados em qualquer lugar no projeto, mas geralmente são armazenados em uma pasta personalizada ou na pasta de **ativos** existentes. O projeto UWP contém estes bitmaps:

- Um bitmap quadrado de 48 pixels armazenado como **/Assets/MonkeyFace.scale-100.png**
- Um bitmap quadrado de 96 pixels armazenado como **/Assets/MonkeyFace.scale-200.png**
- Um bitmap quadrado de 192 pixels armazenado como **/Assets/MonkeyFace.scale-400.png**

Todos eles receberam uma **ação de compilação** de **conteúdo**.

Você pode especificar como as `Text` `ImageSource` Propriedades e são organizadas no `Button` usando a [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) propriedade de `Button` . Essa propriedade é do tipo [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout) , que é uma classe incorporada no `Button` . O [constructor] (xref: Xamarin.Forms . Button. ButtonContentLayout .% 23ctor ( Xamarin.Forms . Button. ButtonContentLayout. ImagePosition, System. Double)) tem dois argumentos:

- Um membro da [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumeração: `Left` ,, `Top` `Right` ou `Bottom` que indica como o bitmap é exibido em relação ao texto.
- Um `double` valor para o espaçamento entre o bitmap e o texto.

Os padrões são `Left` e 10 unidades. Duas propriedades somente leitura do `ButtonContentLayout` nomeadas [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) e [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fornecem os valores dessas propriedades.

No código, você pode criar um `Button` e definir a `ContentLayout` propriedade como esta:

```csharp
Button button = new Button
{
    Text = "button text",
    ImageSource = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

Em XAML, você precisa especificar apenas o membro de enumeração ou o espaçamento, ou ambos em qualquer ordem separada por vírgulas:

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

A página de **demonstração do botão de imagem** usa `OnPlatform` para especificar nomes de arquivo diferentes para os arquivos de bitmap Ios, Android e UWP. Se você quiser usar o mesmo nome de arquivo para cada plataforma e evitar o uso do `OnPlatform` , você precisará armazenar os bitmaps UWP no diretório raiz do projeto.

O primeiro `Button` na página de **demonstração do botão de imagem** define a `Image` propriedade, mas não a `Text` Propriedade:

```xaml
<Button>
    <Button.ImageSource>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.ImageSource>
</Button>
```

Se os bitmaps UWP estiverem armazenados no diretório raiz do projeto, essa marcação poderá ser consideravelmente simplificada:

```xaml
<Button ImageSource="MonkeyFace.png" />
```

Para evitar muita marcação redundante no arquivo **ImageButtonDemo. XAML** , um implícito `Style` também é definido para definir a `ImageSource` propriedade. Isso `Style` é aplicado automaticamente a cinco outros `Button` elementos. Este é o arquivo XAML completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="ImageSource">
                    <OnPlatform x:TypeArguments="ImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.ImageSource>
                <OnPlatform x:TypeArguments="ImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.ImageSource>
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

Os quatro `Button` elementos finais fazem uso da `ContentLayout` propriedade para especificar uma posição e um espaçamento do texto e do bitmap:

[![Demonstração do botão de imagem](button-images/ImageButtonDemo.png "Demonstração do botão de imagem")](button-images/ImageButtonDemo-Large.png#lightbox)

Agora você viu as várias maneiras como você pode manipular `Button` eventos e alterar a `Button` aparência.

## <a name="related-links"></a>Links relacionados

- [Exemplo de ButtonDemos](/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [API do Botão](xref:Xamarin.Forms.Button)