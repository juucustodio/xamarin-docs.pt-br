---
title: Botão xamarin. Forms
description: O botão responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/01/2018
ms.openlocfilehash: 095736e77b2f502261f9b85ab73c45dce74309b9
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34734108"
---
# <a name="xamarinforms-button"></a>Botão xamarin. Forms

_O botão responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica._ 

O [ `Button` ](xref:Xamarin.Forms.Button) é o controle interativo mais importante em todos os xamarin. Forms. O `Button` geralmente exibe uma cadeia de caracteres de texto curto indicando um comando, mas ele também pode exibe uma imagem de bitmap, ou uma combinação de texto e uma imagem. O usuário pressiona o `Button` com um dedo ou clicar nele com o mouse para iniciar esse comando.

A maioria dos tópicos abordados abaixo corresponde às páginas de [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo.

## <a name="handling-button-clicks"></a>Cliques de botão de manuseio

`Button` define uma [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento é acionado quando o usuário toca o `Button` com um ponteiro de mouse ou o dedo. O evento é acionado quando o botão dedo ou o mouse é liberado da superfície do `Button`. O `Button` deve ter seu [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade definida como `true` para responder às toques. 

O **básica de clique de botão** página o [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo demonstra como criar uma instância de um `Button` em XAML e o identificador de seu `Clicked` eventos. O **BasicButtonClickPage.xaml** arquivo contém um `StackLayout` tanto com um `Label` e um `Button`:

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

O `Button` tende a ocupar todo o espaço permitido para ele. Por exemplo, se você não definir a `HorizontalOptions` propriedade `Button` para algo diferente de `Fill`, o `Button` ocupará a largura total do seu pai.

Por padrão, o `Button` é retangular, mas você pode atribuir os cantos arredondado de it usando o [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) propriedade, conforme descrito na seção a seguir [ **botão aparência** ](#button-appearance).

O [ `Text` ](xref:Xamarin.Forms.Button.Text) propriedade especifica o texto que aparece no `Button`. O [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) conjunto de eventos para um manipulador de eventos chamado `OnButtonClicked`. Este manipulador está localizado no arquivo code-behind, **BasicButtonClickPage.xaml.cs**:

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

Quando o `Button` é tocado, o `OnButtonClicked` método é executado. O `sender` argumento é o `Button` objeto responsável por esse evento. Você pode usar isso para acessar o `Button` objeto, ou para distinguir entre vários `Button` objetos que compartilham a mesma `Clicked` evento.

Específico `Clicked` manipulador chama uma função de animação gira o `Label` 360 graus em 1000 milissegundos. Aqui está o programa em execução em dispositivos iOS e Android e como um aplicativo do Windows UWP (plataforma Universal) na área de trabalho do Windows 10:

[![Clique do botão básico](button-images/BasicButtonClick.png "clique do botão básico")](button-images/BasicButtonClick-Large.png#lightbox "clique do botão básico")

Observe que o `OnButtonClicked` método inclui o `async` modificador porque `await` é usado no manipulador de eventos. Um `Clicked` requer o manipulador de eventos de `async` modificador somente se o corpo do manipulador usa `await`.

Cada plataforma processa o `Button` de sua própria maneira específica. No [ **botão aparência** ](#button-appearance) seção, você verá como definir cores e fazer a `Button` borda visível para aparências mais personalizadas. `Button` implementa o [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) interface, para que ela inclua [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), e [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)propriedades.

## <a name="creating-a-button-in-code"></a>Criar um botão no código

É comum para instanciar um `Button` em XAML, mas você também pode criar um `Button` no código. Isso pode ser conveniente quando seu aplicativo precisa para criar vários botões com base nos dados que é enumeráveis com um `foreach` loop.

O **clique de botão de código** página demonstra como criar uma página que é funcionalmente equivalente ao **básica de clique de botão** página mas inteiramente no c#:

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

Tudo o que é feito no construtor da classe. Porque o `Clicked` manipulador é somente uma instrução longo, ele poderá ser anexado ao evento muito simples:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Naturalmente, você também pode definir o manipulador de eventos como um método separado (como o `OnButtonClick` método **básica de clique de botão**) e anexar esse método para o evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Desabilitação do botão

Às vezes, um aplicativo estiver em um estado específico em que um determinado `Button` não é uma operação válida. Nesses casos, o `Button` deve ser desativado, definindo seu `IsEnabled` propriedade `false`. O exemplo clássico é um `Entry` controle para um nome de arquivo acompanhado de um arquivo-abrir `Button`: O `Button` deve ser habilitada apenas se algum texto foi digitado para o `Entry`. Você pode usar um `DataTrigger` para esta tarefa, como mostra o [ **gatilhos de dados** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) artigo.

## <a name="using-the-command-interface"></a>Usando a interface de comando

É possível que um aplicativo responder a `Button` toques sem tratamento de `Clicked` eventos. O `Button` implementa um mecanismo de notificação alternativo chamado o _comando_ ou _ordenar_ interface. Isso consiste em duas propriedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) tipo [ `ICommand` ](xref:System.Windows.Input.ICommand), uma interface definida a [ `System.Windows.Input` ](xref:System.Windows.Input) namespace.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) propriedade do tipo [ `Object` ](xref:System.Object).

Essa abordagem é particularmente adequada em conjunto com a associação de dados e particularmente ao implementar a arquitetura de Model-View-ViewModel (MVVM). Esses tópicos são discutidos nos artigos [associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md), [de associações de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), e [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

Em um aplicativo MVVM, ViewModel define propriedades de tipo `ICommand` que estão conectados, em seguida, ao XAML `Button` elementos com associações de dados. Também define xamarin. Forms [ `Command` ]((xref:Xamarin.Forms.Command`1)) e [ `Command<T>` ](xref:Xamarin.Forms.Command`1) as classes que implementam o `ICommand` interface e ajudar ViewModel definindo propriedades do tipo `ICommand`.

Ordenar é descrita mais detalhadamente no artigo [ **a Interface de comando** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , mas o **básica de botão de comando** página o [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo mostra a abordagem básica.

O `CommandDemoViewModel` classe é um ViewModel muito simple que define uma propriedade de tipo `double` chamado `Number`e duas propriedades do tipo `ICommand` chamado `MultiplyBy2Command` e `DivideBy2Command`:

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

Os dois `ICommand` propriedades são inicializadas no construtor da classe com dois objetos do tipo `Command`. O `Command` construtores incluem um pouco função (chamado de `execute` argumento do construtor de) que dobra ou metades o `Number` propriedade.

O **BasicButtonCommand.xaml** arquivo define seu `BindingContext` para uma instância de `CommandDemoViewModel`. O `Label` elemento e dois `Button` elementos contenham associações para as três propriedades em `CommandDemoViewModel`:

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

[![Botão básico comando](button-images/BasicButtonCommand.png "básica de botão comando")](button-images/BasicButtonCommand-Large.png#lightbox)

A vantagem dessa abordagem sobre `Clicked` manipuladores é que toda a lógica que envolvem a funcionalidade desta página está localizada em ViewModel em vez do arquivo code-behind, para alcançar uma separação melhor da interface do usuário da lógica de negócios.

Também é possível que o `Command` objetos para controlar a habilitação e desabilitação do `Button` elementos. Por exemplo, suponha que você queira limitar o intervalo de valores numéricos entre 2<sup>10</sup> e 2<sup>&ndash;10</sup>. Você pode adicionar outra função para o construtor (chamado de `canExecute` argumento) que retorna `true` se o `Button` deve ser habilitado. Aqui está a modificação para o `CommandDemoViewModel` construtor:

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

As chamadas para o `ChangeCanExecute` método de `Command` são necessários para que o `Command` método pode chamar o `canExecute` método e determinar se o `Button` devem ser desabilitados ou não. Com essa alteração de código, como o número de atingir o limite, o `Button` está desabilitada:

[![Botão básico Command - modificado](button-images/BasicButtonCommandModified.png "comando botão básico - modificado")](button-images/BasicButtonCommandModified-Large.png#lightbox)

É possível que dois ou mais `Button` elementos a ser associado ao mesmo `ICommand` propriedade. O `Button` elementos podem ser distinguidos usando o [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) propriedade `Button`. Nesse caso, você desejará usar o genérico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe. O `CommandParameter` objeto, em seguida, é passado como um argumento para o `execute` e `canExecute` métodos. Essa técnica é mostrada em detalhes no [ **ordenar básico** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) seção o [ **comando Interface** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) artigo.

O [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) exemplo também usa essa técnica em seu `MainPage` classe. O **MainPage. XAML** arquivo contém um `Button` para cada página do exemplo:

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

Cada `Button` tem seu `Command` propriedade vinculada a uma propriedade chamada `NavigateCommand`e o `CommandParameter` é definido como um [ `Type` ](xref:System.Type) objeto correspondente a uma das classes de página no projeto.

Que `NavigateCommand` é de propriedade do tipo `ICommand` e é definido no arquivo code-behind:

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

O construtor inicializa o `NavigateCommand` propriedade para um `Command<Type>` porque `Type` é o tipo do `CommandParameter` objeto definido no arquivo XAML. Isso significa que o `execute` método tem um argumento de tipo `Type` que corresponde a este `CommandParameter` objeto. A função cria a página e, em seguida, navega para ele.

Observe que o construtor conclui definindo seu `BindingContext` a mesmo. Isso é necessário para as propriedades no arquivo XAML para ligar para o `NavigateCommand` propriedade.

## <a name="pressing-and-releasing-the-button"></a>Pressionar e liberar o botão

Além de `Clicked` evento, `Button` também define [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) e [ `Released` ](xref:Xamarin.Forms.Button.Released) eventos. O `Pressed` evento ocorre quando um dedo pressiona em um `Button`, ou um botão do mouse é pressionado com o ponteiro sobre o `Button`. O `Released` evento ocorre quando o botão dedo ou o mouse é liberado. Em geral, um `Clicked` evento também é acionado ao mesmo tempo como o `Released` evento, mas se o ponteiro do mouse ou o dedo slides longe a superfície do `Button` antes de ser liberada, o `Clicked` evento não pode ocorrer.

O `Pressed` e `Released` eventos não são usados com frequência, mas eles podem ser usados para finalidades especiais, como demonstrado no **no botão de versão e pressione** página. O arquivo XAML contém um `Label` e um `Button` com os manipuladores anexados para o `Pressed` e `Released` eventos:

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

O resultado é que o `Label` gira apenas enquanto um dedo está em contato com o `Button`e é interrompido quando o dedo é liberado:

[![Pressione e solte o botão](button-images/PressAndReleaseButton.png "pressione e solte o botão")](button-images/PressAndReleaseButton-Large.png)

Esse tipo de comportamento tem aplicativos para jogos: um dedo mantido em um `Button` pode tornar um objeto de tela em Mover em uma direção específica. 

<a name="button-appearance" />

## <a name="button-appearance"></a>Aparência de botão

O `Button` herda ou define várias propriedades que afetam sua aparência:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) é a cor do `Button` texto
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) é a cor do plano de fundo para que o texto
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) é a cor de uma área ao redor do `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) a família de fonte para o texto é usada
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) é o tamanho do texto
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) Indica se o texto está em negrito ou itálico
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) é a largura da borda 
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) Arredonda os cantos

Os efeitos de seis dessas propriedades (exceto `FontFamily` e `FontAttributes`) são demonstradas no **aparência botão** página. Outra propriedade, [ `Image` ](xref:Xamarin.Forms.Button.Image), é discutida na seção [ **com bitmaps de botão**](#image-button).

Todas as associações de dados e exibições no **aparência botão** página são definidos no arquivo XAML:

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

O `Button` na parte superior da página tem seus três `Color` propriedades vinculadas a `Picker` elementos na parte inferior da página. Os itens a `Picker` elementos são cores do `NamedColor` classe incluído no projeto. Três `Slider` elementos contenham associações bidirecionais para o `FontSize`, `BorderWidth`, e `CornerRadius` propriedades da `Button`.

Este programa permite fazer experiências com combinações de todas essas propriedades:

[![Botão aparência](button-images/ButtonAppearance.png "botão aparência")](button-images/ButtonAppearance-Large.png)

Para ver o `Button` borda, você precisará definir um `BorderColor` para algo diferente de `Default`e o `BorderWidth` para um valor positivo.

No iOS, você observará que as larguras de borda grande interferir no interior do `Button` e interferir com a exibição do texto. Se você optar por usar uma borda com um iOS `Button`, você provavelmente desejará começar e terminar a `Text` propriedade espaços para manter sua visibilidade.

Em UWP, selecionando um `CornerRadius` que excede a metade da altura do `Button` gera uma exceção.

## <a name="creating-a-toggle-button"></a>Criar um botão de alternância

É possível criar subclasse `Button` para que ele funciona como um comutador-off: Toque no botão uma vez para o botão de alternância em e disponibilizá-lo novamente para ativá-lo desativado.

O seguinte `ToggleButton` classe derivada de `Button` e define um novo evento chamado `Toggled` e uma propriedade booleana chamada `IsToggled`. Essas são as propriedades de dois mesmo definidas pelo xamarin. Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

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

O `ToggleButton` construtor anexa um manipulador para o `Clicked` evento para que ele possa alterar o valor da `IsToggled` propriedade. O `OnIsToggledChanged` método dispara o `Toggled` evento. 

A última linha do `OnIsToggledChanged` chamadas de método estático `VisualStateManager.GoToState` método com o texto de duas cadeias de caracteres "ToggledOn" e "ToggledOff". Você pode ler sobre este método e como seu aplicativo pode responder a estados visuais no artigo [ **o Gerenciador de estado Visual xamarin. Forms**](~/xamarin-forms/user-interface/visual-state-manager.md). 

Porque `ToggleButton` faz a chamada para `VisualStateManager.GoToState`, a classe em si não precisa incluir quaisquer recursos adicionais para alterar a aparência do botão com base em seu `IsToggled` estado. Isto é responsabilidade do XAML que hospeda o `ToggleButton`. 

O **demonstração de botão de alternância** página contém duas instâncias do `ToggleButton`, inclusive a marcação de Gerenciador de estado Visual que define o `Text`, `BackgroundColor`, e `TextColor` do botão de acordo com o estado visual: 

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

O `Toggled` manipuladores de eventos são no arquivo code-behind. Eles são responsáveis pela configuração de `FontAttributes` propriedade do `Label` com base no estado dos botões:

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

Aqui está o programa em execução no iOS, Android e o UWP:

[![Alternar o botão demonstração](button-images/ToggleButtonDemo.png "alternar demonstração de botão")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>Usar bitmaps com botões

O `Button` classe define um [ `Image` ](xref:Xamarin.Forms.Button.Image) propriedade que permite a você exibir uma imagem de bitmap no `Button`, sozinho ou em combinação com o texto. Você também pode especificar como o texto e imagem são organizadas.

O `Image` é de propriedade do tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), que significa que os bitmaps devem ser armazenados como recursos nos projetos de plataforma individual e não no projeto de biblioteca do .NET padrão. 

Cada plataforma com suporte pelo xamarin. Forms permite que as imagens sejam armazenados em vários tamanhos para resoluções de pixel diferente do que o aplicativo pode ser executado em vários dispositivos. Essas várias bitmaps são nomeados ou armazenadas de forma que o sistema operacional pode escolher a melhor correspondência para o dispositivo de vídeo resolução de vídeo. 

Para um bitmap em um `Button`, geralmente é o melhor tamanho entre unidades independentes de dispositivo de 32 e 64, dependendo do tamanho desejado para ser. As imagens usadas neste exemplo baseiam-se em um tamanho de unidades de 48 independente de dispositivo.

No projeto do iOS, o **recursos** pasta contém três tamanhos da imagem:

- Um bitmap de quadrado 48 pixels armazenado como **/Resources/MonkeyFace.png**
- Um bitmap de quadrado de 96 pixels armazenado como **/Resource/MonkeyFace@2x.png**
- Um bitmap de quadrado de pixel 144 armazenado como **/Resource/MonkeyFace@3x.png**

Todos os três bitmaps recebeu um **ação de compilação** de **BundleResource**.

Para o projeto Android, os bitmaps todos têm o mesmo nome, mas eles são armazenados nas subpastas diferentes de **recursos** pasta:

- Um bitmap de 72 pixel quadrado armazenado como **/Resources/drawable-hdpi/MonkeyFace.png**
- Um bitmap de quadrado de 96 pixels armazenado como **/Resources/drawable-xhdpi/MonkeyFace.png**
- Um bitmap de quadrado de pixel 144 armazenado como **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Um bitmap de 192 pixel quadrado armazenado como **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Eles receberam uma **ação de compilação** de **AndroidResource**.

No projeto UWP, bitmaps podem ser armazenados em qualquer lugar no projeto, mas eles geralmente são armazenados em uma pasta personalizada ou **ativos** pasta existente. O projeto UWP contém esses bitmaps:

- Um bitmap de quadrado 48 pixels armazenado como **/Assets/MonkeyFace.scale-100.png**
- Um bitmap de quadrado de 96 pixels armazenado como **/Assets/MonkeyFace.scale-200.png**
- Um bitmap de 192 pixel quadrado armazenado como **/Assets/MonkeyFace.scale-400.png**

Eles foram fornecidos um **ação de compilação** de **conteúdo**.

Você pode especificar como o `Text` e `Image` propriedades são organizadas no `Button` usando o [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) propriedade `Button`. Essa propriedade é do tipo [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), que é uma classe incorporada em `Button`. O [construtor](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) tem dois argumentos:

- Membro de [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumeração: `Left`, `Top`, `Right`, ou `Bottom` que indica como o bitmap é exibido em relação ao texto.
- Um `double` valor para o espaçamento entre o bitmap e o texto.

Os padrões são `Left` e 10 unidades. Duas propriedades somente leitura do `ButtonContentLayout` chamado [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) e [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fornecem os valores dessas propriedades.

No código, você pode criar um `Button` e defina o `ContentLayout` propriedade assim:

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

Em XAML, você precisa especificar o membro de enumeração ou o espaçamento ou em qualquer ordem separados por vírgulas:

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

O **imagem do botão demonstração** página usa `OnPlatform` para especificar nomes de arquivo diferentes para o iOS, Android e UWP arquivos de bitmap. Se você quiser usar o mesmo nome de arquivo para todas as plataformas de três e evite o uso de `OnPlatform`, você precisa para armazenar os bitmaps UWP no diretório raiz do projeto.

A primeira `Button` no **demonstração do botão de imagem** página define o `Image` propriedade, mas não o `Text` propriedade:

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

Se os bitmaps UWP são armazenados no diretório raiz do projeto, essa marcação pode ser simplificada consideravelmente:

```xaml
<Button Image="MonkeyFace.png" />
```

Para evitar muita marcações repetitivas o **ImageButtonDemo.xaml** arquivo implícita `Style` também é definido para configurar o `Image` propriedade. Isso `Style` é aplicada automaticamente a outros cinco `Button` elementos. Aqui está o arquivo XAML completo:

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

Os quatro final `Button` elementos fazem uso do `ContentLayout` propriedade para especificar uma posição e espaçamento do texto e bitmap:

[![Imagem do botão demonstração](button-images/ImageButtonDemo.png "botão demonstração da imagem")](button-images/ImageButtonDemo-Large.png#lightbox)

Você viu as várias maneiras que você pode manipular `Button` eventos e altere o `Button` aparência.

## <a name="related-links"></a>Links relacionados

- [Exemplo de ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Botão de API](xref:Xamarin.Forms.Button)
