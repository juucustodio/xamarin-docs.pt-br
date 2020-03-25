---
title: Exibições nativas em XAML
description: Exibições nativas do iOS, Android e plataforma Universal do Windows podem ser referenciadas diretamente de arquivos XAML de xamarin. Forms. Propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e eles podem interagir com exibições do xamarin. Forms. Este artigo demonstra como consumir exibições nativas de arquivos XAML de xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
ms.openlocfilehash: 6d3954f44cd769ed02535eb260b9952e81e67c98
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247620"
---
# <a name="native-views-in-xaml"></a>Exibições nativas em XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Exibições nativas do iOS, Android e Plataforma Universal do Windows podem ser referenciadas diretamente de arquivos XAML Xamarin. Forms. As propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e podem interagir com exibições do Xamarin. Forms. Este artigo demonstra como consumir exibições nativas de arquivos XAML do Xamarin. Forms._

Este artigo discute os seguintes tópicos:

- [Consumindo exibições nativas](#consuming) – o processo para consumir uma exibição nativa do XAML.
- [Usando associações nativas](#native_bindings) – vinculação de dados de e para propriedades de exibições nativas.
- [Passando argumentos para exibições nativas](#passing_arguments) – passando argumentos para construtores de exibição nativa e chamando métodos de fábrica nativos de exibição.
- [Referindo-se a exibições nativas do código](#native_view_code) – recuperando instâncias de exibição nativa declaradas em um arquivo XAML, de seu arquivo code-behind.
- [Subclasse de exibições nativas](#subclassing) – subclasse de exibições nativas para definir uma API amigável para XAML.  

<a name="overview" />

## <a name="overview"></a>Visão geral

Para inserir uma exibição nativa em um arquivo XAML de xamarin. Forms:

1. Adicione uma declaração de namespace `xmlns` no arquivo XAML para o namespace que contém a exibição nativa.
1. Crie uma instância do modo nativo no arquivo XAML.

> [!IMPORTANT]
> O XAML compilado deve ser desabilitado para qualquer página XAML que use exibições nativas. Isso pode ser feito decorando a classe code-behind para sua página XAML com o atributo `[XamlCompilation(XamlCompilationOptions.Skip)]`. Para obter mais informações sobre a compilação XAML, consulte [compilação XAML no Xamarin. Forms](~/xamarin-forms/xaml/xamlc.md).

Para fazer referência a um modo de exibição nativo de um arquivo code-behind, você deve usar um projeto de ativo compartilhado (SAP) e encapsule o código específico da plataforma com diretivas de compilação condicional. Para obter mais informações, consulte [referindo-se a exibições nativas do código](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Consumindo exibições nativas

O exemplo de código a seguir demonstra o consumo de exibições nativas para cada plataforma em um [`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin. Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Além de especificar o `clr-namespace` e `assembly` para um namespace de exibição nativa, um `targetPlatform` também deve ser especificado. Isso deve ser definido como `iOS`, `Android`, `UWP``Windows` (que é equivalente a `UWP`), `macOS`, `GTK`, `Tizen`ou `WPF`. Em tempo de execução, o analisador XAML ignorará todos os prefixos de namespace XML que têm um `targetPlatform` que não corresponde à plataforma na qual o aplicativo está sendo executado.

Cada declaração de namespace pode ser usada para fazer referência a qualquer classe ou estrutura de namespace especificado. Por exemplo, a declaração de namespace `ios` pode ser usada para fazer referência a qualquer classe ou estrutura do namespace do iOS `UIKit`. Propriedades do modo nativo podem ser definidas por meio de XAML, mas os tipos de objeto e propriedade devem coincidir. Por exemplo, a propriedade `UILabel.TextColor` é definida como `UIColor.Red` usando a extensão de marcação `x:Static` e o namespace `ios`.

As propriedades vinculáveis e as propriedades vinculáveis anexadas também podem ser definidas em exibições nativas usando a sintaxe `Class.BindableProperty="value"`. Cada exibição nativa é encapsulada em uma instância de `NativeViewWrapper` específica da plataforma, que deriva da classe [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) . Definir uma propriedade associável ou a propriedade associável anexada em um modo de exibição nativo transfere o valor da propriedade para o wrapper. Por exemplo, um layout horizontal centralizado pode ser especificado definindo `View.HorizontalOptions="Center"` na exibição nativa.

> [!NOTE]
> Observe que os estilos não podem ser usados com exibições nativas, porque os estilos só podem direcionar Propriedades com suporte de objetos `BindableProperty`.

Os construtores de widget do Android geralmente exigem o objeto de `Context` do Android como um argumento, e isso pode ser disponibilizado por meio de uma propriedade estática na classe `MainActivity`. Portanto, ao criar um widget do Android em XAML, o objeto de `Context` geralmente deve ser passado para o construtor do widget usando o atributo `x:Arguments` com uma extensão de marcação `x:Static`. Para obter mais informações, consulte [passando argumentos para exibições nativas](#passing_arguments).

> [!NOTE]
> Observe que nomear uma exibição nativa com `x:Name` não é possível em um projeto de biblioteca .NET Standard ou em um projeto de ativo compartilhado (SAP). Isso vai gerar uma variável do tipo nativo, o que causará um erro de compilação. No entanto, exibições nativas podem ser encapsuladas em instâncias de `ContentView` e recuperadas no arquivo code-behind, desde que um SAP esteja sendo usado. Para obter mais informações, consulte [referindo-se a uma exibição nativa do código](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Associações nativas

Vinculação de dados é usada para sincronizar uma interface do usuário com sua fonte de dados e simplifica como um aplicativo xamarin. Forms exibe e interage com seus dados. Desde que o objeto de origem implemente a interface `INotifyPropertyChanged`, as alterações no objeto de *origem* são enviadas automaticamente para o objeto de *destino* pela estrutura de associação, e as alterações no objeto de *destino* podem, opcionalmente, ser enviadas por push ao objeto de *origem* .

Propriedades de exibições nativas também podem usar a vinculação de dados. O exemplo de código a seguir demonstra a associação de dados usando propriedades de exibições nativas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

A página contém um [`Entry`](xref:Xamarin.Forms.Entry) cuja propriedade [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) é associada à propriedade `NativeSwitchPageViewModel.IsSwitchOn`. O [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como uma nova instância da classe `NativeSwitchPageViewModel` no arquivo code-behind, com a classe ViewModel implementando a interface `INotifyPropertyChanged`.

A página também contém um comutador nativo para cada plataforma. Cada comutador nativo usa uma associação de [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) para atualizar o valor da propriedade `NativeSwitchPageViewModel.IsSwitchOn`. Portanto, quando a opção estiver desativada, a `Entry` estiver desabilitada e, quando a opção estiver ativada, a `Entry` será habilitada. As capturas de tela a seguir mostram a essa funcionalidade em cada plataforma:

![](xaml-images/native-switch-disabled.png "Native Switch Disabled")
![](xaml-images/native-switch-enabled.png "Native Switch Enabled")

Associações bidirecionais são automaticamente suportadas, desde que a propriedade nativa implemente `INotifyPropertyChanged`ou que dê suporte à observação de valor-chave (KVO) no iOS ou seja uma `DependencyProperty` no UWP. No entanto, muitas exibições nativas não oferecem suporte a notificação de alteração de propriedade. Para essas exibições, você pode especificar um valor de propriedade [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) como parte da expressão de associação. Essa propriedade deve ser definida para o nome de um evento no modo de exibição nativo que sinaliza quando a propriedade de destino foi alterado. Em seguida, quando o valor do comutador nativo é alterado, a classe `Binding` é notificada de que o usuário alterou o valor de opção e o valor da propriedade `NativeSwitchPageViewModel.IsSwitchOn` é atualizado.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passando argumentos para exibições nativas

Argumentos de construtor podem ser passados para exibições nativas usando o atributo `x:Arguments` com uma extensão de marcação `x:Static`. Além disso, os métodos de fábrica de exibição nativa (`public static` métodos que retornam objetos ou valores do mesmo tipo que a classe ou estrutura que define os métodos) podem ser chamados especificando o nome do método usando o atributo `x:FactoryMethod` e seus argumentos usando o atributo `x:Arguments`.

O exemplo de código a seguir demonstra as duas técnicas:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

O método de fábrica [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) é usado para definir a propriedade [`UILabel.Font`](xref:UIKit.UILabel.Font) como uma nova [`UIFont`](xref:UIKit.UIFont) no Ios. O nome de `UIFont` e o tamanho são especificados pelos argumentos do método que são filhos do atributo `x:Arguments`.

O método de fábrica [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) é usado para definir a propriedade [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) como uma nova [`Typeface`](xref:Android.Graphics.Typeface) no Android. O nome e o estilo da família de `Typeface` são especificados pelos argumentos do método que são filhos do atributo `x:Arguments`.

O construtor [`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) é usado para definir a propriedade [`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) como uma nova `FontFamily` no plataforma universal do Windows (UWP). O nome do `FontFamily` é especificado pelo argumento do método que é filho do atributo `x:Arguments`.

> [!NOTE]
> Argumentos devem corresponder aos tipos exigidos pelo método de construtor ou de fábrica.

As capturas de tela a seguir mostram o resultado da especificação de argumentos de método e construtor de fábrica para definir a fonte em diferentes exibições nativas:

![](xaml-images/passing-arguments.png "Setting Fonts on Native Views")

Para obter mais informações sobre como passar argumentos em XAML, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Referindo-se a exibições nativas do código

Embora não seja possível nomear uma exibição nativa com o atributo `x:Name`, é possível recuperar uma instância de exibição nativa declarada em um arquivo XAML a partir de seu arquivo code-behind em um projeto de acesso compartilhado, desde que a exibição nativa seja um filho de um [`ContentView`](xref:Xamarin.Forms.ContentView) que especifique um valor de atributo `x:Name`. Em seguida, dentro de diretivas de compilação condicional no arquivo code-behind você deve:

1. Recupere o valor da propriedade [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) e converta-o em um tipo de `NativeViewWrapper` específico da plataforma.
1. Recupere a propriedade `NativeViewWrapper.NativeElement` e converta-a para o tipo de exibição nativo.

A API nativa, em seguida, pode ser invocada no modo de nativo para executar as operações desejadas. Essa abordagem também oferece o benefício de que várias exibições nativas XAML para diferentes plataformas podem ser filhos da mesma [`ContentView`](xref:Xamarin.Forms.ContentView). O exemplo de código a seguir demonstra essa técnica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

No exemplo acima, as exibições nativas para cada plataforma são filhos de [`ContentView`](xref:Xamarin.Forms.ContentView) controles, com o valor de atributo `x:Name` usado para recuperar o `ContentView` no code-behind:

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

A propriedade [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) é acessada para recuperar a exibição nativa encapsulada como uma instância de `NativeViewWrapper` específica da plataforma. A propriedade `NativeViewWrapper.NativeElement` é então acessada para recuperar a exibição nativa como seu tipo nativo. API nativa do modo de exibição, em seguida, é chamado para realizar as operações desejadas.

Os botões nativos do iOS e do Android compartilham o mesmo manipulador de eventos `OnButtonTap`, porque cada botão nativo consome um delegado `EventHandler` em resposta a um evento de toque. No entanto, o Plataforma Universal do Windows (UWP) usa um `RoutedEventHandler`separado que, por sua vez, consome o manipulador de eventos `OnButtonTap` neste exemplo. Portanto, quando um botão nativo é clicado, o manipulador de eventos `OnButtonTap` é executado, o que dimensiona e gira o controle nativo contido no [`ContentView`](xref:Xamarin.Forms.ContentView) nomeado `contentViewTextParent`. As capturas de tela a seguir demonstram essa que ocorrem em cada plataforma:

![](xaml-images/contentview.png "ContentView Containing a Native Control")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Criando subclasses exibições nativas

Muitos iOS e Android exibições nativas não são adequadas para criar uma instância em XAML, porque eles usam métodos, em vez de propriedades, para configurar o controle. Exibições nativas de subclasse nos wrappers que definem uma API de XAML compatível com mais do que usa as propriedades para o controle de instalação, e que usa eventos independente de plataforma é a solução para esse problema. Os modos de exibição encapsulados nativos podem ser colocados em um projeto de ativo compartilhado (SAP) e cercados com diretivas de compilação condicional, ou colocados em projetos específicos da plataforma e referenciados de XAML em um projeto de biblioteca .NET Standard.

O exemplo de código a seguir demonstra uma página de xamarin. Forms que consome uma subclasse exibições nativas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

A página contém um [`Label`](xref:Xamarin.Forms.Label) que exibe as frutas escolhidas pelo usuário a partir de um controle nativo. O `Label` é associado à propriedade `SubclassedNativeControlsPageViewModel.SelectedFruit`. O [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como uma nova instância da classe `SubclassedNativeControlsPageViewModel` no arquivo code-behind, com a classe ViewModel implementando a interface `INotifyPropertyChanged`.

A página também contém um modo de exibição de seletor de nativo para cada plataforma. Cada exibição nativa exibe a coleção de frutas associando sua propriedade `ItemSource` à coleção `SubclassedNativeControlsPageViewModel.Fruits`. Isso permite que o usuário escolha uma frutas, conforme mostrado nas capturas de tela seguir:

![](xaml-images/sub-classed.png "Subclassed Native Views")

No iOS e Android os seletores nativos usam métodos para configurar os controles. Portanto, esses seletores devem ser uma subclasse para expor as propriedades para torná-las amigável a XAML. No Plataforma Universal do Windows (UWP), o `ComboBox` já é amigável para XAML e, portanto, não requer subclasse.

### <a name="ios"></a>iOS

A implementação do iOS subclasses do [`UIPickerView`](xref:UIKit.UIPickerView) exibição e expõe as propriedades e um evento que pode ser facilmente consumido do XAML:

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

A classe `MyUIPickerView` expõe as propriedades `ItemsSource` e `SelectedItem` e um evento de `SelectedItemChanged`. Um [`UIPickerView`](xref:UIKit.UIPickerView) requer um modelo de dados de [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) subjacente, que é acessado pelo evento e pelas propriedades do `MyUIPickerView`. O modelo de dados `UIPickerViewModel` é fornecido pela classe `PickerModel`:

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

A classe `PickerModel` fornece o armazenamento subjacente para a classe `MyUIPickerView`, por meio da propriedade `Items`. Sempre que o item selecionado no `MyUIPickerView` for alterado, o método [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) será executado, o que atualizará o índice selecionado e acionará o evento `ItemChanged`. Isso garante que a propriedade `SelectedItem` sempre retornará o último item selecionado pelo usuário. Além disso, a classe `PickerModel` substitui os métodos usados para configurar a instância de `MyUIPickerView`.

### <a name="android"></a>Android

A implementação do Android subclasses do [`Spinner`](xref:Android.Widget.Spinner) exibição e expõe as propriedades e um evento que pode ser facilmente consumido do XAML:

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

A classe `MySpinner` expõe as propriedades `ItemsSource` e `SelectedObject` e um evento de `ItemSelected`. Os itens exibidos pela classe `MySpinner` são fornecidos pelo [`Adapter`](xref:Android.Widget.Adapter) associado à exibição e os itens são populados no `Adapter` quando a propriedade `ItemsSource` é definida pela primeira vez. Sempre que o item selecionado na classe `MySpinner` for alterado, o manipulador de eventos `OnBindableSpinnerItemSelected` atualizará a propriedade `SelectedObject`.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir exibições nativas de arquivos XAML de xamarin. Forms. Propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e eles podem interagir com exibições do xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [NativeSwitch (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formulários nativos](~/xamarin-forms/platform/native-forms.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
