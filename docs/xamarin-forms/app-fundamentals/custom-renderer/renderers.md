---
title: Classes base de renderizador e controles nativos
description: Cada um dos controles do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de renderizador e controle nativo que implementam cada página, layout, exibição e célula do Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2019
ms.openlocfilehash: f4c2663e4e4e298975fc1e230750ec562547b0da
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77636023"
---
# <a name="renderer-base-classes-and-native-controls"></a>Classes base de renderizador e controles nativos

_Cada controle Xamarin.Forms tem um renderizador que acompanha cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de controle renderizador e nativa que implementam cada página, layout, exibição e célula do renderizador._

Com exceção da classe `MapRenderer`, os renderizadores específicos da plataforma podem ser encontrados nos seguintes namespaces:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **UWP (Plataforma Universal do Windows)** – Xamarin.Forms.Platform.UWP

A classe `MapRenderer` pode ser encontrada nos seguintes namespaces:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **UWP (Plataforma Universal do Windows)** – Xamarin.Forms.Maps.UWP

> [!NOTE]
> Saiba mais sobre como criar renderizadores personalizados para aplicativos do Shell em [Renderizadores personalizados do Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/customrenderers.md).

## <a name="pages"></a>Pages (Páginas)

A seguinte tabela lista as classes de renderizador e controle nativo que implementam cada tipo [Page](~/xamarin-forms/user-interface/controls/pages.md) do Xamarin.Forms:

|Página|Renderizador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – Telefone), TabletMasterDetailPageRenderer (iOS – Tablet), MasterDetailRenderer (Android), MasterDetailPageRenderer (Android AppCompat), MasterDetailPageRenderer (UWP)|UIViewController (Telefone), UISplitViewController (Tablet)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (Controle Personalizado)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS e Android), NavigationPageRenderer (Android AppCompat), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (Controle Personalizado)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS e Android), TabbedPageRenderer (Android AppCompat), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (Pivô)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Layouts

A seguinte tabela lista as classes de renderizador e controle nativo que implementam cada tipo [Layout](~/xamarin-forms/user-interface/controls/layouts.md) do Xamarin.Forms:

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

A seguinte tabela lista as classes de renderizador e controle nativo que implementam cada tipo [View](~/xamarin-forms/user-interface/controls/views.md) do Xamarin.Forms:

|Exibições|Renderizador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS e Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Retângulo|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Botão|AppCompatButton|Botão|
|[`CarouselView`](xref:Xamarin.Forms.CarouselView)|CarrosselViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|`CheckBox`|CheckBoxRenderer|UIButton||AppCompatCheckBox|CheckBox|
|[`CollectionView`](xref:Xamarin.Forms.CollectionView)|CollectionViewRenderer|UICollectionView||RecyclerView|ListViewBase|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Imagem|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Botão|
|`IndicatorView`|IndicadorVerRenderizador|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`MediaElement`](xref:Xamarin.Forms.MediaElement)|MediaElementRenderer|UIView||VídeoVer|MediaElement|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|`RefreshView`|RefreshViewRenderer|UIView||SwipeRefreshLayout|Recipiente de atualização|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Controle deslizante|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Control|
|`SwipeView`|SwipeViewRenderer|UIView||Visualizar|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Opção|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS), WebViewRenderer (Android e UWP)|WkWebView|Exibição da Web||Exibição da Web|

## <a name="cells"></a>Células

A seguinte tabela lista as classes de renderizador e controle nativo que implementam cada tipo [Cell](~/xamarin-forms/user-interface/controls/cells.md) do Xamarin.Forms:

|Células|Renderizador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell com um UITextField|LinearLayout com uma TextView e um EditText|DataTemplate com uma TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell com um UISwitch|Opção|DataTemplate com uma Grid contendo um TextBlock e um ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout com duas TextViews|DataTemplate com um StackPanel contendo dois TextBlocks|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell com uma UIImage|LinearLayout com duas TextViews e uma ImageView|DataTemplate com uma Grade contendo uma Imagem e dois TextBlocks|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Visualizar|DataTemplate com um ContentPresenter|

## <a name="summary"></a>Resumo

Este artigo listou as classes de renderizador e controle nativo que implementam cada página, layout, exibição e célula do Xamarin.Forms. Cada um dos controles do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo.
