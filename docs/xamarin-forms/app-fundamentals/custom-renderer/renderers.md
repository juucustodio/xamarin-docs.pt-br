---
title: Classes base de renderizador e controles nativos
description: Cada um dos controles do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de renderizador e controle nativo que implementam cada página, layout, exibição e célula do Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2019
ms.openlocfilehash: ead643357b3ef88f3099c6ee03cda694a8ea1971
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489239"
---
# <a name="renderer-base-classes-and-native-controls"></a>Classes base de renderizador e controles nativos

_Todo controle Xamarin. Forms tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de processador e controle nativo que implementam cada página do Xamarin. Forms, layout, exibição e célula._

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

## <a name="pages"></a>Páginas

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

|{1&gt;{2&gt;Layout&lt;2}&lt;1}|Renderizador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)|ViewRenderer|UIView|Exibir|FrameworkElement|
|[`ContentView`](xref:Xamarin.Forms.ContentView)|ViewRenderer|UIView|Exibir|FrameworkElement|
|[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)|ViewRenderer|UIView|Exibir|FrameworkElement|
|[`Frame`](xref:Xamarin.Forms.Frame)|FrameRenderer|UIView|ViewGroup|Borda|
|[`ScrollView`](xref:Xamarin.Forms.ScrollView)|ScrollViewRenderer|UIScrollView|ScrollView|ScrollViewer|
|[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)|ViewRenderer|UIView|Exibir|FrameworkElement|
|[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)|ViewRenderer|UIView|Exibir|FrameworkElement|
|[`Grid`](xref:Xamarin.Forms.Grid)|ViewRenderer|UIView|Exibir|FrameworkElement|
|[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)|ViewRenderer|UIView|Exibir|FrameworkElement|
|[`StackLayout`](xref:Xamarin.Forms.StackLayout)|ViewRenderer|UIView|Exibir|FrameworkElement|

## <a name="views"></a>Exibições

A seguinte tabela lista as classes de renderizador e controle nativo que implementam cada tipo [View](~/xamarin-forms/user-interface/controls/views.md) do Xamarin.Forms:

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
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Image|
|[`ImageButton`](xref:Xamarin.Forms.ImageButton)|ImageButtonRenderer|UIButton||AppCompatImageButton|Botão|
|`IndicatorView`|IndicatorViewRenderer|UIPageControl||LinearLayout||
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|`RefreshView`|RefreshViewRenderer|UIView||SwipeRefreshLayout|RefreshContainer|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Controle Deslizante|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Controle|
|`SwipeView`|SwipeViewRenderer|UIView||Exibir|SwipeControl|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Alternar|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WkWebViewRenderer (iOS), WebViewRenderer (Android e UWP)|WkWebView|WebView||WebView|

## <a name="cells"></a>Células

A seguinte tabela lista as classes de renderizador e controle nativo que implementam cada tipo [Cell](~/xamarin-forms/user-interface/controls/cells.md) do Xamarin.Forms:

|Células|Renderizador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell com um UITextField|LinearLayout com uma TextView e um EditText|DataTemplate com uma TextBox|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell com um UISwitch|Alternar|DataTemplate com uma Grid contendo um TextBlock e um ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout com duas TextViews|DataTemplate com um StackPanel contendo dois TextBlocks|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell com uma UIImage|LinearLayout com duas TextViews e uma ImageView|DataTemplate com uma Grade contendo uma Imagem e dois TextBlocks|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Exibir|DataTemplate com um ContentPresenter|

## <a name="summary"></a>Resumo

Este artigo listou as classes de renderizador e controle nativo que implementam cada página, layout, exibição e célula do Xamarin.Forms. Cada um dos controles do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo.
