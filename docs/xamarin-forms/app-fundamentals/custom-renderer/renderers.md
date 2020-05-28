---
title: ''
description: Cada Xamarin.Forms controle tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de processador e controle nativo que implementam cada Xamarin.Forms página, layout, exibição e célula.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a56f05f8ff4eb8ece43a9f4f38a669cfdc85c4be
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135181"
---
# <a name="renderer-base-classes-and-native-controls"></a>Classes base de renderizador e controles nativos

_Cada Xamarin.Forms controle tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de processador e controle nativo que implementam cada Xamarin.Forms página, layout, exibição e célula._

Com exceção da classe `MapRenderer`, os renderizadores específicos da plataforma podem ser encontrados nos seguintes namespaces:

- **Ios** – Xamarin.Forms . Platform. iOS
- **Android** – Xamarin.Forms . Platform. Android
- **Android (AppCompat)** – Xamarin.Forms . Platform. Android. AppCompat
- **Plataforma universal do Windows (UWP)** – Xamarin.Forms . Platform. UWP

A classe `MapRenderer` pode ser encontrada nos seguintes namespaces:

- **Ios** – Xamarin.Forms . Maps. iOS
- **Android** – Xamarin.Forms . Maps. Android
- **Plataforma universal do Windows (UWP)** – Xamarin.Forms . Maps. UWP

> [!NOTE]
> Para obter informações sobre como criar renderizadores personalizados para aplicativos de Shell, consulte [ Xamarin.Forms renderizadores personalizados do Shell](~/xamarin-forms/app-fundamentals/shell/customrenderers.md).

## <a name="pages"></a>Pages (Páginas)

A tabela a seguir lista as classes de processador e de controle nativo que implementam cada tipo de Xamarin.Forms [página](~/xamarin-forms/user-interface/controls/pages.md) :

|Página|Renderizador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – Telefone), TabletMasterDetailPageRenderer (iOS – Tablet), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (Telefone), UISplitViewController (Tablet)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (Controle Personalizado)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS e Android), NavigationPageRenderer (Android AppCompat), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (Controle Personalizado)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS e Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivô)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Layouts

A tabela a seguir lista as classes de processador e de controle nativo que implementam cada tipo de Xamarin.Forms [layout](~/xamarin-forms/user-interface/controls/layouts.md) :

|Layout|Renderizador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Visualizar|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Visualizar|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Visualizar|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|Borda|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Visualizar|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Visualizar|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Visualizar|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Visualizar|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Visualizar|FrameworkElement|

## <a name="views"></a>Exibições

A tabela a seguir lista as classes de processador e de controle nativo que implementam cada tipo de Xamarin.Forms [exibição](~/xamarin-forms/user-interface/controls/views.md) :

|Exibições|Renderizador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS e Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Retângulo|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Botão|AppCompatButton|Botão|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarouselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|`CheckBox`|CheckBoxRenderer|UIButton||AppCompatCheckBox|CheckBox|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Imagem|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Botão|
|`IndicatorView`|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)|MKMapView|MapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|MediaElementRenderer|UIView||VideoView|MediaElement|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|`RadioButton`|RadioButtonRenderer|UIButton||AppCompatRadioButton|RadioButton|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`RefreshView`](xref:Xamarin.Forms.RefreshView)|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Controle deslizante|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Control|
|`SwipeView`|SwipeViewRenderer|UIView||Visualizar|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Alternar|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS), WebViewRenderer (Android e UWP)|WkWebView|WebView||WebView|

> [!NOTE]
> O `Expander` controle é implementado usando um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , com animação. Portanto, ele não tem nenhum renderizador de plataforma.

## <a name="cells"></a>Células

A tabela a seguir lista as classes de processador e de controle nativo que implementam cada tipo de Xamarin.Forms [célula](~/xamarin-forms/user-interface/controls/cells.md) :

|Células|Renderizador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell com um UITextField|LinearLayout com uma TextView e um EditText|DataTemplate com uma TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell com um UISwitch|Alternar|DataTemplate com uma Grid contendo um TextBlock e um ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout com duas TextViews|DataTemplate com um StackPanel contendo dois TextBlocks|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell com uma UIImage|LinearLayout com duas TextViews e uma ImageView|DataTemplate com uma Grade contendo uma Imagem e dois TextBlocks|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Visualizar|DataTemplate com um ContentPresenter|

## <a name="summary"></a>Resumo

Este artigo lista o renderizador e as classes de controle nativo que implementam cada Xamarin.Forms página, layout, exibição e célula. Cada Xamarin.Forms controle tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo.
