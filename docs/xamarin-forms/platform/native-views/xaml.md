---
title: "Modos de exibição nativo em XAML"
description: "Exibições nativo do iOS, Android e a plataforma Universal do Windows podem ser referenciadas diretamente arquivos XAML xamarin. Forms. Propriedades e manipuladores de eventos podem ser definidos em exibições nativo, e eles podem interagir com exibições xamarin. Forms. Este artigo demonstra como utilizar exibições nativo arquivos XAML xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: fc44b2a6080832d11c610661244172ad4a6a0716
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="native-views-in-xaml"></a>Modos de exibição nativo em XAML

_Exibições nativo do iOS, Android e a plataforma Universal do Windows podem ser referenciadas diretamente arquivos XAML xamarin. Forms. Propriedades e manipuladores de eventos podem ser definidos em exibições nativo, e eles podem interagir com exibições xamarin. Forms. Este artigo demonstra como utilizar exibições nativo arquivos XAML xamarin. Forms._

Este artigo aborda os seguintes tópicos:

- [Consumindo exibições nativo](#consuming) – o processo para o consumo de um modo nativo do XAML.
- [Usando associações nativo](#native_bindings) – para e de propriedades de exibições nativo de associação de dados.
- [Argumentos de passagem para exibições nativo](#passing_arguments) – passar argumentos para construtores de modo nativo e chamar métodos de fábrica de modo nativo.
- [Referindo-se a modos de exibição nativos de código](#native_view_code) – recuperação de instâncias de modo nativo declarado em um arquivo XAML, de seu arquivo code-behind.
- [Subclasses de exibições nativo](#subclassing) – subclasses exibições nativo para definir uma API amigável XAML.  

<a name="overview" />

## <a name="overview"></a>Visão geral

Para inserir uma exibição nativo em um arquivo XAML xamarin. Forms:

1. Adicionar um `xmlns` declaração de namespace no arquivo XAML para o namespace que contém o modo nativo.
1. Crie uma instância do modo nativo no arquivo XAML.

> [!NOTE]
> XAMLC deve ser desativada para as páginas XAML que usam exibições nativo.

Para fazer referência a um modo de exibição nativo de um arquivo code-behind, você deve usar um projeto de ativo compartilhado (SAP) e encapsular o código específico da plataforma com as diretivas de compilação condicional. Para obter mais informações, consulte [referindo-se a modos de exibição nativo do código](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Consumindo exibições nativo

O exemplo de código a seguir demonstra o consumo nativo modos de exibição para cada plataforma de um xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static formsandroid:Forms.Context}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Assim como especificar o `clr-namespace` e `assembly` para um namespace de modo nativo, um `targetPlatform` também deve ser especificado. Isso deve ser definido como um dos valores do [ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/) enumeração e geralmente será definido como `iOS`, `Android`, ou `Windows`. Em tempo de execução, o analisador XAML irá ignorar qualquer prefixos de namespace XML que têm um `targetPlatform` que não corresponde a plataforma na qual o aplicativo está em execução.

Cada declaração de namespace pode ser usada para fazer referência a qualquer classe ou estrutura do namespace especificado. Por exemplo, o `ios` declaração de namespace pode ser usada para fazer referência a qualquer classe ou estrutura do iOS `UIKit` namespace. Propriedades do modo nativo podem ser definidas por meio de XAML, mas os tipos de objeto e propriedade devem coincidir. Por exemplo, o `UILabel.TextColor` está definida como `UIColor.Red` usando o `x:Static` extensão de marcação e o `ios` namespace.

Propriedades vinculáveis e propriedades vinculáveis anexadas podem também ser definidas em exibições nativo usando o `Class.BindableProperty="value"` sintaxe. Cada modo nativo é encapsulado em uma determinada plataforma `NativeViewWrapper` instância, que deriva de [ `Xamarin.Forms.View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe. Definir uma propriedade associável ou a propriedade associável anexada em uma exibição nativo transfere o valor da propriedade para o wrapper. Por exemplo, um layout horizontal centralizado pode ser especificado definindo `View.HorizontalOptions="Center"` no modo de exibição nativo.

> [!NOTE]
> Observe que os estilos não podem ser usados com modos de exibição nativo porque estilos somente podem atingir propriedades que são feitas por `BindableProperty` objetos.

Construtores de widget Android geralmente exigem o Android `Context` objeto como um argumento e isso está disponível por meio de `Xamarin.Forms.Platform.Android.Forms.Context` objeto. Portanto, ao criar um widget Android em XAML, o `Context` objeto geralmente deve ser passado para o construtor do widget usando o `x:Arguments` atributo com um `x:Static` extensão de marcação. Para obter mais informações, consulte [passar argumentos para exibições nativo](#passing_arguments).

> [!NOTE]
> Observe que uma exibição nativo com a nomenclatura `x:Name` não é possível em um projeto de biblioteca de classe portátil (PCL) ou um projeto de ativo compartilhado (SAP). Isso irá gerar uma variável do tipo nativo, o que causará um erro de compilação. No entanto, os modos nativo podem ser ajustados em `ContentView` instâncias e recuperados no arquivo code-behind, desde que um SAP está sendo usado. Para obter mais informações, consulte [referindo-se a uma exibição nativa de código](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Associações nativo

Associação de dados é usada para sincronizar uma interface de usuário com a fonte de dados e simplifica como um aplicativo xamarin. Forms exibe e interage com seus dados. Desde que o objeto de origem implementa o `INotifyPropertyChanged` interface, as alterações no *fonte* objeto são enviadas automaticamente para o *destino* objeto pela estrutura de associação e alterações de *destino* objeto opcionalmente pode ser enviado para o *fonte* objeto.

Propriedades de exibições nativo também podem usar a associação de dados. O exemplo de código a seguir demonstra a associação de dados usando propriedades de exibições nativo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
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
        <androidWidget:Switch x:Arguments="{x:Static formsAndroid:Forms.Context}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

A página contém um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) cujo [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) propriedade associa ao `NativeSwitchPageViewModel.IsSwitchOn` propriedade. O [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) da página é definido como uma nova instância do `NativeSwitchPageViewModel` classe no arquivo code-behind, com a implementação de classe ViewModel o `INotifyPropertyChanged` interface.

A página também contém um comutador nativo para cada plataforma. Cada comutador nativo usa um [ `TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) associação para atualizar o valor da `NativeSwitchPageViewModel.IsSwitchOn` propriedade. Portanto, quando a opção estiver desativada, o `Entry` estiver desabilitada, e quando a opção está ativada, o `Entry` está habilitado. As capturas de tela a seguir mostram essa funcionalidade em cada plataforma:

![](xaml-images/native-switch-disabled.png "Comutador nativo desabilitado")
![](xaml-images/native-switch-enabled.png "comutador nativo habilitado")

Associações bidirecionais automaticamente têm suporte contanto que implementa a propriedade nativo `INotifyPropertyChanged`, dá suporte à chave-valor observando (KVO) no iOS, ou é um `DependencyProperty` em UWP. No entanto, vários modos de exibição nativos não dão suporte a notificação de alteração de propriedade. Esses modos de exibição, você pode especificar um [ `UpdateSourceEventName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.UpdateSourceEventName/) valor da propriedade como parte da expressão de associação. Essa propriedade deve ser definida para o nome de um evento no modo de exibição nativo que indica quando a propriedade de destino foi alterado. Em seguida, quando o valor da opção nativo muda, o `Binding` classe será notificada de que o usuário alterou o valor da opção e o `NativeSwitchPageViewModel.IsSwitchOn` o valor da propriedade é atualizado.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passando argumentos para exibições nativo

Argumentos de construtor podem ser passados para exibições nativo usando o `x:Arguments` atributo com um `x:Static` extensão de marcação. Além disso, os métodos de fábrica de modo nativo (`public static` métodos que retornam valores do mesmo tipo da classe ou estrutura que define os métodos ou objetos) pode ser chamado, especificando o método nome usando o `x:FactoryMethod` atributos e seus argumentos usando o `x:Arguments` atributo.

O exemplo de código a seguir demonstra ambas as técnicas:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
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
        <androidWidget:TextView x:Arguments="{x:Static formsAndroid:Forms.Context}"
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

O [ `UIFont.FromName` ](https://developer.xamarin.com/api/member/UIKit.UIFont.FromName/) método de fábrica é usado para definir o [ `UILabel.Font` ](https://developer.xamarin.com/api/property/UIKit.UILabel.Font/) propriedade para uma nova [ `UIFont` ](https://developer.xamarin.com/api/type/UIKit.UIFont/) no iOS. O `UIFont` nome e tamanho são especificados pelos argumentos de método que são filhos do `x:Arguments` atributo.

O [ `Typeface.Create` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/p/System.String/Android.Graphics.TypefaceStyle/) método de fábrica é usado para definir o [ `TextView.Typeface` ](https://developer.xamarin.com/api/property/Android.Widget.TextView.Typeface/) propriedade para uma nova [ `Typeface` ](https://developer.xamarin.com/api/type/Android.Graphics.Typeface/) no Android. O `Typeface` nome de família e estilo são especificados pelos argumentos de método que são filhos do `x:Arguments` atributo.

O [ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) construtor é usado para definir o [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) propriedade para um novo `FontFamily` no Windows UWP (plataforma Universal). O `FontFamily` nome é especificado pelo argumento de método é um filho de `x:Arguments` atributo.

> [!NOTE]
> **Observação**: argumentos devem corresponder aos tipos exigidos pelo método de construtor ou fábrica.

As capturas de tela a seguir mostram o resultado de especificar argumentos de método e o construtor de fábrica para definir a fonte em diferentes modos de exibição nativo:

![](xaml-images/passing-arguments.png "Definição das fontes em exibições nativo")

Para obter mais informações sobre os argumentos de passagem em XAML, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Referindo-se a modos de exibição nativos de código

Embora não seja possível nomear um modo nativo com o `x:Name` atributo, é possível recuperar uma instância de modo nativo declarada em um arquivo XAML de seu arquivo de code-behind em um projeto de acesso compartilhado, desde que o modo nativo é um filho de um [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) que especifica um `x:Name` valor do atributo. Em seguida, dentro de diretivas de compilação condicional no arquivo code-behind, você deve:

1. Recuperar o [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propriedade de valor e converta-a para uma determinada plataforma `NativeViewWrapper` tipo.
1. Recuperar o `NativeViewWrapper.NativeElement` propriedade e converta-a para o tipo de modo nativo.

A API nativa pode ser invocada no modo de nativo para executar as operações desejadas. Essa abordagem também oferece o benefício que várias exibições XAML nativo para diferentes plataformas podem ser filhos do mesmo [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/). O exemplo de código a seguir demonstra essa técnica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static formsAndroid:Forms.Context}"
                Text="Text in a TextView" />
            <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static formsAndroid:Forms.Context}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>

```

No exemplo acima, as exibições de nativo para cada plataforma são filhos do [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) controles, com o `x:Name` valor de atributo que está sendo usado para recuperar o `ContentView` no code-behind:

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

O [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propriedade é acessada para recuperar o modo de exibição nativo encapsulado como uma plataforma específica `NativeViewWrapper` instância. O `NativeViewWrapper.NativeElement` propriedade é acessada para recuperar o modo nativo como seu tipo nativo. API do modo nativo, é chamado para executar as operações desejadas.

O iOS e Android botões nativos compartilham o mesmo `OnButtonTap` manipulador de eventos, porque cada botão nativo consome um `EventHandler` delegar em resposta a um evento de toque. No entanto, o Windows UWP (plataforma Universal) usa um separado `RoutedEventHandler`, que por sua vez consome o `OnButtonTap` manipulador de eventos neste exemplo. Portanto, quando um botão nativo é clicado, o `OnButtonTap` manipulador de eventos é executado, que pode ser expandido e gira controle nativo contido o [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) chamado `contentViewTextParent`. As capturas de tela a seguir demonstram esse ocorrendo em cada plataforma:

![](xaml-images/contentview.png "ContentView que contém um controle nativo")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Subclasses de exibições nativo

Muitos iOS e Android exibições nativo não são adequadas para criar uma instância em XAML, porque eles usam métodos, em vez de propriedades, para configurar o controle. A solução para esse problema é nativo modos de exibição de subclasse de wrappers que definem uma API amigável XAML mais que usa propriedades para o controle de instalação, e que usa eventos independente de plataforma. Os modos de exibição nativo encapsulados podem ser colocados em um projeto de ativo compartilhado (SAP) e colocados entre as diretivas de compilação condicional, ou colocados em projetos específicos de plataforma e referenciados do XAML em um projeto de biblioteca de classe portátil (PCL).

O exemplo de código a seguir demonstra uma página xamarin. Forms que consome uma subclasse exibições nativo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
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
        <androidLocal:MySpinner x:Arguments="{x:Static formsAndroid:Forms.Context}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

A página contém um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) que exibe as frutas escolhidas pelo usuário a partir de um controle nativo. O `Label` associa ao `SubclassedNativeControlsPageViewModel.SelectedFruit` propriedade. O [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) da página é definido como uma nova instância do `SubclassedNativeControlsPageViewModel` classe no arquivo code-behind, com a implementação de classe ViewModel o `INotifyPropertyChanged` interface.

A página também contém uma exibição de seletor de nativo para cada plataforma. Cada exibição nativo exibe a coleção de frutas associando seu `ItemSource` propriedade para o `SubclassedNativeControlsPageViewModel.Fruits` coleção. Isso permite que o usuário escolha uma frutas, conforme mostrado nas capturas de tela seguir:

![](xaml-images/sub-classed.png "Modos de exibição subclasses nativo")

No iOS e Android os selecionadores nativo usam métodos para configurar os controles. Portanto, essas seleções devem ser uma subclasse para expor as propriedades para torná-las XAML amigável. Sobre o Windows UWP (plataforma Universal), o `ComboBox` já é compatível com XAML e, portanto, não precisa subclassificação.

### <a name="ios"></a>iOS

As subclasses de implementação do iOS a [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) exibição e expõe propriedades e um evento que pode ser facilmente consumido do XAML:

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

O `MyUIPickerView` classe `ItemsSource` e `SelectedItem` propriedades e um `SelectedItemChanged` eventos. Um [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) requer uma subjacente [ `UIPickerViewModel` ](https://developer.xamarin.com/api/type/UIKit.UIPickerViewModel/) modelo de dados, que pode é acessado pelo `MyUIPickerView` propriedades e eventos. O `UIPickerViewModel` modelo de dados é fornecido pelo `PickerModel` classe:

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

O `PickerModel` classe fornece o armazenamento subjacente para o `MyUIPickerView` classe, por meio de `Items` propriedade. Sempre que o item selecionado no `MyUIPickerView` alterações, o [ `Selected` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.Selected/) método é executado, o que atualiza o índice selecionado e aciona o `ItemChanged` evento. Isso garante que o `SelectedItem` propriedade sempre retornará o último item escolhido pelo usuário. Além disso, o `PickerModel` classe substituições de métodos que são usados para configurar o `MyUIPickerView` instância.

### <a name="android"></a>Android

As subclasses de implementação Android a [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) exibição e expõe propriedades e um evento que pode ser facilmente consumido do XAML:

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

O `MySpinner` classe `ItemsSource` e `SelectedObject` propriedades e um `ItemSelected` eventos. Itens exibidos pelo `MySpinner` classe são fornecidos pelo [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.Adapter/) associado com o modo de exibição e itens são preenchidos no `Adapter` quando o `ItemsSource` propriedade é definida pela primeira vez. Sempre que o item selecionado no `MySpinner` classe alterações, o `OnBindableSpinnerItemSelected` atualizações do manipulador de eventos de `SelectedObject` propriedade.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir nativo modos de exibição de arquivos XAML xamarin. Forms. Propriedades e manipuladores de eventos podem ser definidos em exibições nativo, e eles podem interagir com exibições xamarin. Forms.


## <a name="related-links"></a>Links relacionados

- [NativeSwitch (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeSwitch/)
- [Forms2Native (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Forms2Native/)
- [NativeViewInsideContentView (sample)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeViewInsideContentView/)
- [SubclassedNativeControls (sample)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/SubclassedNativeControls/)
- [Formulários nativo](~/xamarin-forms/platform/native-forms.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
