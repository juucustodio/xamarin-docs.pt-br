---
title: Exibições nativas em XAML
description: Exibições nativas do iOS, Android e plataforma Universal do Windows podem ser referenciadas diretamente de arquivos XAML de xamarin. Forms. Propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e eles podem interagir com exibições do xamarin. Forms. Este artigo demonstra como consumir exibições nativas de arquivos XAML de xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: 3c4fa085c9fdf17cdc256d9710c23911bb60d584
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770640"
---
# <a name="native-views-in-xaml"></a>Exibições nativas em XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Exibições nativas do iOS, Android e Plataforma Universal do Windows podem ser referenciadas diretamente de arquivos XAML Xamarin. Forms. As propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e podem interagir com exibições do Xamarin. Forms. Este artigo demonstra como consumir exibições nativas de arquivos XAML do Xamarin. Forms._

Este artigo aborda os seguintes tópicos:

- [Consumindo exibições nativas](#consuming) – o processo para o consumo de um modo de exibição nativo de XAML.
- [Usando associações nativas](#native_bindings) – e para as propriedades de exibições nativas de vinculação de dados.
- [Passando argumentos para exibições nativas](#passing_arguments) – passando argumentos para construtores de modo nativo e chamar métodos de fábrica de modo nativo.
- [Referindo-se a exibições nativas do código](#native_view_code) – recuperar instâncias de modo nativo declarado em um arquivo XAML, de seu arquivo code-behind.
- [Exibições nativas da subclasse](#subclassing) – subclasses exibições nativas para definir uma API amigável a XAML.  

<a name="overview" />

## <a name="overview"></a>Visão geral

Para inserir uma exibição nativa em um arquivo XAML de xamarin. Forms:

1. Adicionar um `xmlns` declaração de namespace no arquivo XAML para o namespace que contém o modo nativo.
1. Crie uma instância do modo nativo no arquivo XAML.

> [!IMPORTANT]
> O XAML compilado deve ser desabilitado para qualquer página XAML que use exibições nativas. Isso pode ser feito decorando a classe code-behind para sua página XAML com o atributo `[XamlCompilation(XamlCompilationOptions.Skip)]`. Para obter mais informações sobre a compilação XAML, consulte [compilação XAML no Xamarin. Forms](~/xamarin-forms/xaml/xamlc.md).

Para fazer referência a um modo de exibição nativo de um arquivo code-behind, você deve usar um projeto de ativo compartilhado (SAP) e encapsule o código específico da plataforma com diretivas de compilação condicional. Para obter mais informações, consulte [referindo-se a exibições nativas do código](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Consumindo exibições nativas

O exemplo de código a seguir demonstra o consumo de exibições nativas para cada plataforma para um xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage):

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

Bem como especificar o `clr-namespace` e `assembly` para um namespace de modo nativo, um `targetPlatform` também deve ser especificado. Isso deve ser definido como um dos valores da [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) enumeração e normalmente será definido como `iOS`, `Android`, ou `Windows`. Em tempo de execução, o analisador XAML ignorará quaisquer prefixos de namespace XML que têm um `targetPlatform` que não coincida com a plataforma na qual o aplicativo está em execução.

Cada declaração de namespace pode ser usada para fazer referência a qualquer classe ou estrutura de namespace especificado. Por exemplo, o `ios` declaração de namespace pode ser usada para fazer referência a qualquer classe ou estrutura do iOS `UIKit` namespace. Propriedades do modo nativo podem ser definidas por meio de XAML, mas os tipos de objeto e propriedade devem coincidir. Por exemplo, o `UILabel.TextColor` estiver definida como `UIColor.Red` usando o `x:Static` extensão de marcação e o `ios` namespace.

Propriedades vinculáveis e propriedades vinculáveis anexadas podem também ser definidas em exibições nativas usando o `Class.BindableProperty="value"` sintaxe. Cada exibição nativa é encapsulada em uma plataforma específica `NativeViewWrapper` instância, que deriva de [ `Xamarin.Forms.View` ](xref:Xamarin.Forms.View) classe. Definir uma propriedade associável ou a propriedade associável anexada em um modo de exibição nativo transfere o valor da propriedade para o wrapper. Por exemplo, um layout horizontal centralizado pode ser especificado definindo `View.HorizontalOptions="Center"` na exibição nativa.

> [!NOTE]
> Observe que os estilos não podem ser usados com exibições nativas, porque os estilos somente podem direcionar as propriedades que têm o respaldo `BindableProperty` objetos.

Construtores de widget Android geralmente requerem o Android `Context` objeto como um argumento e isso podem ser disponibilizado por meio de uma propriedade estática no `MainActivity` classe. Portanto, ao criar um widget de Android em XAML, o `Context` objeto geralmente deve ser passado ao construtor do widget usando o `x:Arguments` atributo com um `x:Static` extensão de marcação. Para obter mais informações, consulte [passando argumentos para exibições nativas](#passing_arguments).

> [!NOTE]
> Observe que uma exibição nativa com a nomenclatura `x:Name` não é possível em um projeto de biblioteca .NET Standard ou um projeto de ativo compartilhado (SAP). Isso vai gerar uma variável do tipo nativo, o que causará um erro de compilação. No entanto, podem ser ajustadas exibições nativas em `ContentView` instâncias e recuperados no arquivo code-behind, desde que um SAP está sendo usado. Para obter mais informações, consulte [referindo-se a uma exibição nativo do código](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Associações nativas

Vinculação de dados é usada para sincronizar uma interface do usuário com sua fonte de dados e simplifica como um aplicativo xamarin. Forms exibe e interage com seus dados. Desde que o objeto de origem implementa o `INotifyPropertyChanged` interface, as alterações na *código-fonte* objeto são enviadas automaticamente para o *destino* objeto pela estrutura de associação e as alterações no *alvo* objeto opcionalmente pode ser enviado para o *origem* objeto.

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

A página contém um [ `Entry` ](xref:Xamarin.Forms.Entry) cujo [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade associa ao `NativeSwitchPageViewModel.IsSwitchOn` propriedade. O [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como uma nova instância dos `NativeSwitchPageViewModel` classe no arquivo code-behind, com a classe ViewModel Implementando o `INotifyPropertyChanged` interface.

A página também contém um comutador nativo para cada plataforma. Cada comutador nativo usa um [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay) associação para atualizar o valor da `NativeSwitchPageViewModel.IsSwitchOn` propriedade. Portanto, quando estiver desativada, o `Entry` estiver desabilitada, e quando a opção estiver ativada, o `Entry` está habilitado. As capturas de tela a seguir mostram a essa funcionalidade em cada plataforma:

![](xaml-images/native-switch-disabled.png "Desabilitado nativas de Switch")
![](xaml-images/native-switch-enabled.png "nativas de Switch habilitada")

Vinculações bidirecionais automaticamente têm suporte contanto que implementa a propriedade nativa `INotifyPropertyChanged`, ou oferece suporte a observar de chave-valor (KVO) no iOS, ou é um `DependencyProperty` na UWP. No entanto, muitas exibições nativas não oferecem suporte a notificação de alteração de propriedade. Para esses modos de exibição, você pode especificar uma [ `UpdateSourceEventName` ](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valor da propriedade como parte da expressão de associação. Essa propriedade deve ser definida para o nome de um evento no modo de exibição nativo que sinaliza quando a propriedade de destino foi alterado. Em seguida, quando o valor da opção nativo muda, o `Binding` classe é notificado de que o usuário alterou o valor da opção e o `NativeSwitchPageViewModel.IsSwitchOn` valor da propriedade é atualizado.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passando argumentos para exibições nativas

Argumentos de construtor podem ser passados para exibições nativas usando o `x:Arguments` do atributo com um `x:Static` extensão de marcação. Além disso, os métodos de fábrica de modo nativo (`public static` métodos que retornam objetos ou valores do mesmo tipo que a classe ou estrutura que define os métodos) pode ser chamado, especificando o método nome usando o `x:FactoryMethod` atributo e seus argumentos usando o `x:Arguments` atributo.

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

O [ `UIFont.FromName` ](xref:UIKit.UIFont.FromName*) método de fábrica é usado para definir a [ `UILabel.Font` ](xref:UIKit.UILabel.Font) propriedade para um novo [ `UIFont` ](xref:UIKit.UIFont) no iOS. O `UIFont` nome e tamanho são especificados pelos argumentos de método que são filhos do `x:Arguments` atributo.

O [ `Typeface.Create` ](xref:Android.Graphics.Typeface.Create*) método de fábrica é usado para definir a [ `TextView.Typeface` ](xref:Android.Widget.TextView.Typeface) propriedade para um novo [ `Typeface` ](xref:Android.Graphics.Typeface) no Android. O `Typeface` nome da família e estilo são especificados pelos argumentos de método que são filhos do `x:Arguments` atributo.

O [ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) construtor é usado para definir a [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) propriedade para um novo `FontFamily` no Universal Windows Platform (UWP). O `FontFamily` nome é especificado pelo argumento do método que é um filho do `x:Arguments` atributo.

> [!NOTE]
> Argumentos devem corresponder aos tipos exigidos pelo método de construtor ou de fábrica.

As capturas de tela a seguir mostram o resultado da especificação de argumentos de método e construtor de fábrica para definir a fonte em diferentes exibições nativas:

![](xaml-images/passing-arguments.png "Definindo fontes em exibições nativas")

Para obter mais informações sobre como passar argumentos em XAML, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Referindo-se a exibições nativas do código

Embora não seja possível nomear uma exibição nativa com o `x:Name` atributo, é possível recuperar uma instância de modo nativo, declarada em um arquivo XAML de seu arquivo code-behind em um projeto de acesso compartilhado, desde que o modo nativo é um filho de um [ `ContentView` ](xref:Xamarin.Forms.ContentView) que especifica um `x:Name` valor do atributo. Em seguida, dentro de diretivas de compilação condicional no arquivo code-behind você deve:

1. Recuperar o [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propriedade de valor e convertê-lo para um específico da plataforma `NativeViewWrapper` tipo.
1. Recuperar o `NativeViewWrapper.NativeElement` propriedade e convertê-lo para o tipo de modo nativo.

A API nativa, em seguida, pode ser invocada no modo de nativo para executar as operações desejadas. Essa abordagem também oferece o benefício que várias exibições nativas do XAML para diferentes plataformas podem ser filhos dos mesmos [ `ContentView` ](xref:Xamarin.Forms.ContentView). O exemplo de código a seguir demonstra essa técnica:

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

No exemplo acima, as exibições de nativas para cada plataforma são filhos de [ `ContentView` ](xref:Xamarin.Forms.ContentView) controles, com o `x:Name` valor de atributo que está sendo usado para recuperar o `ContentView` no code-behind:

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

O [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) propriedade é acessada para recuperar o modo de exibição nativo encapsulado como um específico da plataforma `NativeViewWrapper` instância. O `NativeViewWrapper.NativeElement` propriedade é acessada, em seguida, para recuperar o modo nativo como seu tipo nativo. API nativa do modo de exibição, em seguida, é chamado para realizar as operações desejadas.

O iOS e Android botões nativo compartilham a mesma `OnButtonTap` manipulador de eventos, porque cada botão nativo consome um `EventHandler` delegar em resposta a um evento de toque. No entanto, a Universal Windows Platform (UWP) usa um separado `RoutedEventHandler`, que por sua vez consome o `OnButtonTap` manipulador de eventos neste exemplo. Portanto, quando um botão nativo é clicado, o `OnButtonTap` manipulador de eventos é executado, que se dimensiona e gira o controle nativo contido a [ `ContentView` ](xref:Xamarin.Forms.ContentView) chamado `contentViewTextParent`. As capturas de tela a seguir demonstram essa que ocorrem em cada plataforma:

![](xaml-images/contentview.png "ContentView que contém um controle nativo")

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

A página contém um [ `Label` ](xref:Xamarin.Forms.Label) que exibe as frutas escolhidas pelo usuário de um controle nativo. O `Label` está associado a `SubclassedNativeControlsPageViewModel.SelectedFruit` propriedade. O [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como uma nova instância dos `SubclassedNativeControlsPageViewModel` classe no arquivo code-behind, com a classe ViewModel Implementando o `INotifyPropertyChanged` interface.

A página também contém um modo de exibição de seletor de nativo para cada plataforma. Cada exibição nativa exibe a coleção de frutas, associando sua `ItemSource` propriedade para o `SubclassedNativeControlsPageViewModel.Fruits` coleção. Isso permite que o usuário escolha uma frutas, conforme mostrado nas capturas de tela seguir:

![](xaml-images/sub-classed.png "Exibições nativas de subclasse")

No iOS e Android os seletores nativos usam métodos para configurar os controles. Portanto, esses seletores devem ser uma subclasse para expor as propriedades para torná-las amigável a XAML. Na Universal Windows Platform (UWP), o `ComboBox` já é compatível com XAML e, portanto, não exige a criação de subclasses.

### <a name="ios"></a>iOS

As subclasses de implementação do iOS a [ `UIPickerView` ](xref:UIKit.UIPickerView) exibição e expõe propriedades e um evento que pode ser facilmente consumido de XAML:

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

O `MyUIPickerView` classe expõe `ItemsSource` e `SelectedItem` propriedades e um `SelectedItemChanged` eventos. Um [ `UIPickerView` ](xref:UIKit.UIPickerView) requer uma subjacente [ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel) modelo de dados, que pode é acessado pelo `MyUIPickerView` propriedades e eventos. O `UIPickerViewModel` modelo de dados é fornecido pelo `PickerModel` classe:

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

O `PickerModel` classe fornece o armazenamento subjacente para o `MyUIPickerView` classe, por meio de `Items` propriedade. Sempre que o item selecionado na `MyUIPickerView` alterações, o [ `Selected` ](xref:UIKit.UIPickerViewModel.Selected*) método é executado, que atualiza o índice selecionado e aciona o `ItemChanged` eventos. Isso garante que o `SelectedItem` propriedade sempre retornará o último item escolhido pelo usuário. Além disso, o `PickerModel` classe substitui os métodos que são usados para configurar o `MyUIPickerView` instância.

### <a name="android"></a>Android

As subclasses de implementação do Android a [ `Spinner` ](xref:Android.Widget.Spinner) exibição e expõe propriedades e um evento que pode ser facilmente consumido de XAML:

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

O `MySpinner` classe expõe `ItemsSource` e `SelectedObject` propriedades e um `ItemSelected` eventos. Os itens exibidos pelo `MySpinner` classe são fornecidas pelo [ `Adapter` ](xref:Android.Widget.Adapter) associado com o modo de exibição e itens serão populadas na `Adapter` quando o `ItemsSource` propriedade é definida pela primeira vez. Sempre que o item selecionado na `MySpinner` classe alterações, o `OnBindableSpinnerItemSelected` atualizações do manipulador de eventos a `SelectedObject` propriedade.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir exibições nativas de arquivos XAML de xamarin. Forms. Propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e eles podem interagir com exibições do xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [NativeSwitch (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formulários nativos](~/xamarin-forms/platform/native-forms.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
