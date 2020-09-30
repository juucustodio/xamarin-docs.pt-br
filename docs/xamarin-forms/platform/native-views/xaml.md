---
title: Exibições nativas em XAML
description: Exibições nativas do iOS, Android e Plataforma Universal do Windows podem ser referenciadas diretamente de Xamarin.Forms arquivos XAML. As propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e podem interagir com Xamarin.Forms exibições. Este artigo demonstra como consumir exibições nativas de Xamarin.Forms arquivos XAML.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78bd93e2f6556480ae7d2903771d7d6303dda148
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558370"
---
# <a name="native-views-in-xaml"></a>Exibições nativas em XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_Exibições nativas do iOS, Android e Plataforma Universal do Windows podem ser referenciadas diretamente de Xamarin.Forms arquivos XAML. As propriedades e os manipuladores de eventos podem ser definidos em exibições nativas e podem interagir com Xamarin.Forms exibições. Este artigo demonstra como consumir exibições nativas de Xamarin.Forms arquivos XAML._

Para inserir uma exibição nativa em um Xamarin.Forms arquivo XAML:

1. Adicione uma `xmlns` declaração de namespace no arquivo XAML para o namespace que contém a exibição nativa.
1. Crie uma instância da exibição nativa no arquivo XAML.

> [!IMPORTANT]
> O XAML compilado deve ser desabilitado para qualquer página XAML que use exibições nativas. Isso pode ser feito decorando a classe code-behind para sua página XAML com o `[XamlCompilation(XamlCompilationOptions.Skip)]` atributo. Para obter mais informações sobre a compilação XAML, consulte [compilação Xamarin.Forms XAML em ](~/xamarin-forms/xaml/xamlc.md).

Para fazer referência a uma exibição nativa de um arquivo code-behind, você deve usar um projeto de ativo compartilhado (SAP) e encapsular o código específico da plataforma com diretivas de compilação condicional. Para obter mais informações, consulte [consulte exibições nativas do código](#refer-to-native-views-from-code).

## <a name="consume-native-views"></a>Consumir exibições nativas

O exemplo de código a seguir demonstra o consumo de exibições nativas para cada plataforma para um Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

Além de especificar o `clr-namespace` e o `assembly` para um namespace de exibição nativa, um `targetPlatform` também deve ser especificado. Isso deve ser definido como `iOS` , `Android` , `UWP` , `Windows` (que é equivalente a `UWP` ), `macOS` , `GTK` , `Tizen` ou `WPF` . Em tempo de execução, o analisador XAML ignorará quaisquer prefixos de namespace XML que tenham um `targetPlatform` que não corresponda à plataforma na qual o aplicativo está sendo executado.

Cada declaração de namespace pode ser usada para fazer referência a qualquer classe ou estrutura do namespace especificado. Por exemplo, a `ios` declaração de namespace pode ser usada para fazer referência a qualquer classe ou estrutura do namespace do IOS `UIKit` . As propriedades da exibição nativa podem ser definidas por meio de XAML, mas a propriedade e os tipos de objeto devem corresponder. Por exemplo, a `UILabel.TextColor` propriedade é definida como `UIColor.Red` usando a `x:Static` extensão de marcação e o `ios` namespace.

As propriedades vinculáveis e as propriedades vinculáveis anexadas também podem ser definidas em exibições nativas usando a `Class.BindableProperty="value"` sintaxe. Cada exibição nativa é encapsulada em uma instância específica da plataforma `NativeViewWrapper` , que deriva da [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) classe. Definir uma propriedade vinculável ou uma propriedade vinculável anexada em uma exibição nativa transfere o valor da propriedade para o wrapper. Por exemplo, um layout horizontal centralizado pode ser especificado por meio `View.HorizontalOptions="Center"` da configuração na exibição nativa.

> [!NOTE]
> Observe que os estilos não podem ser usados com exibições nativas, porque os estilos só podem direcionar propriedades que são apoiadas por `BindableProperty` objetos.

Os construtores do widget do Android geralmente exigem o objeto do Android `Context` como um argumento, e isso pode ser disponibilizado por meio de uma propriedade estática na `MainActivity` classe. Portanto, ao criar um widget do Android em XAML, o `Context` objeto geralmente deve ser passado para o construtor do widget usando o `x:Arguments` atributo com uma `x:Static` extensão de marcação. Para obter mais informações, consulte [passar argumentos para exibições nativas](#pass-arguments-to-native-views).

> [!NOTE]
> Observe que a nomenclatura de uma exibição nativa com o `x:Name` não é possível em um projeto de biblioteca .net Standard ou em um projeto de ativo compartilhado (SAP). Isso irá gerar uma variável do tipo nativo, o que causará um erro de compilação. No entanto, as exibições nativas podem ser encapsuladas em `ContentView` instâncias e recuperadas no arquivo code-behind, desde que um SAP esteja sendo usado. Para obter mais informações, consulte [consulte a exibição nativa do código](#refer-to-native-views-from-code).

## <a name="native-bindings"></a>Associações nativas

A vinculação de dados é usada para sincronizar uma interface do usuário com sua fonte de dados e simplifica a maneira como um Xamarin.Forms aplicativo exibe e interage com seus dados. Desde que o objeto de origem implemente a `INotifyPropertyChanged` interface, as alterações no objeto de *origem* são automaticamente enviadas para o objeto de *destino* pela estrutura de associação, e as alterações no objeto de *destino* podem, opcionalmente, ser enviadas para o objeto de *origem* .

As propriedades de exibições nativas também podem usar a vinculação de dados. O exemplo de código a seguir demonstra a vinculação de dados usando as propriedades de exibições nativas:

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

A página contém uma [`Entry`](xref:Xamarin.Forms.Entry) cuja [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade é associada à `NativeSwitchPageViewModel.IsSwitchOn` propriedade. O [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como uma nova instância da `NativeSwitchPageViewModel` classe no arquivo code-behind, com a classe ViewModel implementando a `INotifyPropertyChanged` interface.

A página também contém um comutador nativo para cada plataforma. Cada comutador nativo usa uma [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) Associação para atualizar o valor da `NativeSwitchPageViewModel.IsSwitchOn` propriedade. Portanto, quando a opção estiver desativada, o `Entry` estiver desabilitado e quando a opção estiver ativada, o `Entry` será habilitado. As capturas de tela a seguir mostram essa funcionalidade em cada plataforma:

![Comutador nativo desabilitado para o comutador nativo ](xaml-images/native-switch-disabled.png)
 ![ habilitado](xaml-images/native-switch-enabled.png)

Associações bidirecionais são automaticamente suportadas desde que a propriedade nativa implementa `INotifyPropertyChanged` ou dá suporte à observação de valor-chave (KVO) no Ios, ou é uma `DependencyProperty` em UWP. No entanto, muitas exibições nativas não dão suporte à notificação de alteração de propriedade. Para essas exibições, você pode especificar um [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valor de propriedade como parte da expressão de associação. Essa propriedade deve ser definida como o nome de um evento no modo de exibição nativo que sinaliza quando a propriedade de destino foi alterada. Em seguida, quando o valor do comutador nativo é alterado, a `Binding` classe é notificada de que o usuário alterou o valor de opção e o `NativeSwitchPageViewModel.IsSwitchOn` valor da propriedade é atualizado.

## <a name="pass-arguments-to-native-views"></a>Passar argumentos para exibições nativas

Argumentos de construtor podem ser passados para exibições nativas usando o `x:Arguments` atributo com uma `x:Static` extensão de marcação. Além disso, os métodos de fábrica de exibição nativa ( `public static` métodos que retornam objetos ou valores do mesmo tipo que a classe ou estrutura que define os métodos) podem ser chamados especificando o nome do método usando o `x:FactoryMethod` atributo e seus argumentos usando o `x:Arguments` atributo.

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

O [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) método de fábrica é usado para definir a [`UILabel.Font`](xref:UIKit.UILabel.Font) propriedade como um novo [`UIFont`](xref:UIKit.UIFont) no Ios. O `UIFont` nome e o tamanho são especificados pelos argumentos do método que são filhos do `x:Arguments` atributo.

O [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) método de fábrica é usado para definir a [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) propriedade como um novo [`Typeface`](xref:Android.Graphics.Typeface) no Android. O `Typeface` nome e o estilo da família são especificados pelos argumentos do método que são filhos do `x:Arguments` atributo.

O [`FontFamily`](/uwp/api/Windows.UI.Xaml.Media.FontFamily) Construtor é usado para definir a [`TextBlock.FontFamily`](/uwp/api/Windows.UI.Xaml.Controls.TextBlock) propriedade para uma nova `FontFamily` no plataforma universal do Windows (UWP). O `FontFamily` nome é especificado pelo argumento do método que é filho do `x:Arguments` atributo.

> [!NOTE]
> Os argumentos devem corresponder aos tipos exigidos pelo construtor ou método de fábrica.

As capturas de tela a seguir mostram o resultado da especificação de argumentos de método e Construtor de fábrica para definir a fonte em exibições nativas diferentes:

![Configurando fontes em exibições nativas](xaml-images/passing-arguments.png)

Para obter mais informações sobre como passar argumentos em XAML, consulte [passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md).

## <a name="refer-to-native-views-from-code"></a>Consulte exibições nativas do código

Embora não seja possível nomear uma exibição nativa com o `x:Name` atributo, é possível recuperar uma instância de exibição nativa declarada em um arquivo XAML a partir de seu arquivo code-behind em um projeto de acesso compartilhado, desde que a exibição nativa seja um filho de um [`ContentView`](xref:Xamarin.Forms.ContentView) que especifique um `x:Name` valor de atributo. Em seguida, dentro das diretivas de compilação condicional no arquivo code-behind, você deve:

1. Recupere o [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) valor da propriedade e converta-o em um tipo específico da plataforma `NativeViewWrapper` .
1. Recupere a `NativeViewWrapper.NativeElement` propriedade e converta-a para o tipo de exibição nativo.

A API nativa pode ser invocada na exibição nativa para executar as operações desejadas. Essa abordagem também oferece o benefício de que várias exibições nativas XAML para diferentes plataformas podem ser filhas da mesma [`ContentView`](xref:Xamarin.Forms.ContentView) . O exemplo de código a seguir demonstra essa técnica:

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

No exemplo acima, as exibições nativas para cada plataforma são filhos de [`ContentView`](xref:Xamarin.Forms.ContentView) controles, com o `x:Name` valor de atributo que está sendo usado para recuperar o `ContentView` no code-behind:

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

A [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) propriedade é acessada para recuperar a exibição nativa encapsulada como uma instância específica da plataforma `NativeViewWrapper` . A `NativeViewWrapper.NativeElement` propriedade é então acessada para recuperar a exibição nativa como seu tipo nativo. A API da exibição nativa é invocada para executar as operações desejadas.

Os botões nativos do iOS e do Android compartilham o mesmo `OnButtonTap` manipulador de eventos, porque cada botão nativo consome um `EventHandler` delegado em resposta a um evento de toque. No entanto, o Plataforma Universal do Windows (UWP) usa um separado `RoutedEventHandler` , que por sua vez consome o `OnButtonTap` manipulador de eventos neste exemplo. Portanto, quando um botão nativo é clicado, o `OnButtonTap` manipulador de eventos é executado, o que dimensiona e gira o controle nativo contido no [`ContentView`](xref:Xamarin.Forms.ContentView) nome `contentViewTextParent` . As capturas de tela a seguir demonstram isso ocorrendo em cada plataforma:

![ContentView que contém um controle nativo](xaml-images/contentview.png)

## <a name="subclass-native-views"></a>Exibições nativas de subclasse

Muitas exibições nativas do iOS e do Android não são adequadas para instanciar em XAML porque usam métodos, em vez de propriedades, para configurar o controle. A solução para esse problema é usar exibições nativas de subclasse em wrappers que definem uma API mais amigável para XAML que usa propriedades para configurar o controle e que usa eventos independentes de plataforma. As exibições nativas encapsuladas podem ser colocadas em um projeto de ativo compartilhado (SAP) e junto com as diretivas de compilação condicional, ou colocadas em projetos específicos da plataforma e referenciadas de XAML em um projeto de biblioteca .NET Standard.

O exemplo de código a seguir demonstra uma Xamarin.Forms página que consome exibições nativas de subclasse:

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

A página contém um [`Label`](xref:Xamarin.Forms.Label) que exibe a fruta escolhida pelo usuário de um controle nativo. O é `Label` associado à `SubclassedNativeControlsPageViewModel.SelectedFruit` propriedade. O [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página é definido como uma nova instância da `SubclassedNativeControlsPageViewModel` classe no arquivo code-behind, com a classe ViewModel implementando a `INotifyPropertyChanged` interface.

A página também contém uma exibição de seletor nativo para cada plataforma. Cada exibição nativa exibe a coleção de frutas associando sua `ItemSource` Propriedade à `SubclassedNativeControlsPageViewModel.Fruits` coleção. Isso permite que o usuário escolha uma fruta, conforme mostrado nas seguintes capturas de tela:

![Exibições nativas de subclasse](xaml-images/sub-classed.png)

No iOS e Android, os seletores nativos usam métodos para configurar os controles. Portanto, esses seletores devem ser subclasses para expor propriedades para torná-los amigáveis para XAML. No Plataforma Universal do Windows (UWP), o `ComboBox` já é amigável para XAML e, portanto, não requer subclasse.

### <a name="ios"></a>iOS

A implementação do iOS subclasses a [`UIPickerView`](xref:UIKit.UIPickerView) exibição e expõe as propriedades e um evento que pode ser facilmente consumido do XAML:

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

A `MyUIPickerView` classe expõe `ItemsSource` e `SelectedItem` Propriedades e um `SelectedItemChanged` evento. Um [`UIPickerView`](xref:UIKit.UIPickerView) requer um [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) modelo de dados subjacente, que é acessado pelas `MyUIPickerView` Propriedades e pelo evento. O `UIPickerViewModel` modelo de dados é fornecido pela `PickerModel` classe:

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

A `PickerModel` classe fornece o armazenamento subjacente para a `MyUIPickerView` classe, por meio da `Items` propriedade. Sempre que o item selecionado no `MyUIPickerView` é alterado, o [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) método é executado, o qual atualiza o índice selecionado e aciona o `ItemChanged` evento. Isso garante que a `SelectedItem` propriedade sempre retornará o último item selecionado pelo usuário. Além disso, a `PickerModel` classe substitui os métodos que são usados para configurar a `MyUIPickerView` instância.

### <a name="android"></a>Android

A implementação do Android subclasse a [`Spinner`](xref:Android.Widget.Spinner) exibição e expõe as propriedades e um evento que pode ser facilmente consumido do XAML:

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

A `MySpinner` classe expõe `ItemsSource` e `SelectedObject` Propriedades e um `ItemSelected` evento. Os itens exibidos pela `MySpinner` classe são fornecidos pelo associado à [`Adapter`](xref:Android.Widget.Adapter) exibição e os itens são preenchidos no `Adapter` quando a `ItemsSource` propriedade é definida pela primeira vez. Sempre que o item selecionado na `MySpinner` classe for alterado, o `OnBindableSpinnerItemSelected` manipulador de eventos atualizará a `SelectedObject` propriedade.

## <a name="related-links"></a>Links relacionados

- [NativeSwitch (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (exemplo)](/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Formulários nativos](~/xamarin-forms/platform/native-forms.md)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)