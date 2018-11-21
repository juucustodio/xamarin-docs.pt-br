---
title: Classes Base do renderizador e controles nativos
description: Todos os controles xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de controle nativo que implementam cada página do xamarin. Forms, layout, exibição e célula e o renderizador.
ms.prod: xamarin
ms.assetid: A8909AE3-ED0E-4D24-BF96-B49E732E3B93
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 56df2f7e6b83ddd4a5780506471cbd32a3aced40
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171944"
---
# <a name="renderer-base-classes-and-native-controls"></a>Classes Base do renderizador e controles nativos

_Todos os controles xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de controle nativo que implementam cada página do xamarin. Forms, layout, exibição e célula e o renderizador._

Com exceção do `MapRenderer` os renderizadores específicos da plataforma de classe, podem ser encontrados nos seguintes namespaces:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Android (AppCompat)** – Xamarin.Forms.Platform.Android.AppCompat
- **Plataforma universal do Windows (UWP)** – Xamarin.Forms.Platform.UWP

O `MapRenderer` classe pode ser encontrada nos seguintes namespaces:

- **iOS** – Xamarin.Forms.Maps.iOS
- **Android** – Xamarin.Forms.Maps.Android
- **Plataforma universal do Windows (UWP)** – Xamarin.Forms.Maps.UWP

## <a name="pages"></a>Pages (Páginas)

A tabela a seguir lista as classes de controle nativo que implementam cada xamarin. Forms e o renderizador [página](~/xamarin-forms/user-interface/controls/pages.md) tipo:

|Página|Renderizador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ContentPage`](xref:Xamarin.Forms.ContentPage)|[PageRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/contentpage.md)|UIViewController|ViewGroup||FrameworkElement|
|[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)|PhoneMasterDetailRenderer (iOS – telefone), TabletMasterDetailPageRenderer (iOS – Tablet), MasterDetailRenderer (Android), MasterDetailPageRenderer (AppCompat Android), MasterDetailPageRenderer (UWP)|UIViewController (telefone), UISplitViewController (Tablet)|DrawerLayout (v4)|DrawerLayout (v4)|FrameworkElement (controle personalizado)|
|[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)|NavigationRenderer (iOS e Android), NavigationPageRenderer (AppCompat Android), NavigationPageRenderer (UWP)|UIToolbar|ViewGroup|ViewGroup|FrameworkElement (controle personalizado)|
|[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)|TabbedRenderer (iOS e Android), TabbedPageRenderer (AppCompat Android), TabbedPageRenderer (UWP)|UIView|ViewPager|ViewPager|FrameworkElement (dinâmica)|
|[`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)|PageRenderer|UIViewController|ViewGroup||FrameworkElement|
|[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)|CarouselPageRenderer|UIScrollView|ViewPager|ViewPager|FrameworkElement (FlipView)|

## <a name="layouts"></a>Layouts

A tabela a seguir lista as classes de controle nativo que implementam cada xamarin. Forms e o renderizador [Layout](~/xamarin-forms/user-interface/controls/layouts.md) tipo:

|Layout|Renderizador|iOS|Android|UWP|
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

A tabela a seguir lista as classes de controle nativo que implementam cada xamarin. Forms e o renderizador [exibição](~/xamarin-forms/user-interface/controls/views.md) tipo:

|Exibições|Renderizador|iOS|Android|Android (AppCompat)|UWP|
|--- |--- |--- |--- |--- |--- |
|[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)|ActivityIndicatorRenderer|UIActivityIndicator|ProgressBar||ProgressBar|
|[`BoxView`](xref:Xamarin.Forms.BoxView)|BoxRenderer (iOS e Android), BoxViewRenderer (UWP)|UIView|ViewGroup||Retângulo|
|[`Button`](xref:Xamarin.Forms.Button)|ButtonRenderer|UIButton|Botão|AppCompatButton|Botão|
|[`DatePicker`](xref:Xamarin.Forms.DatePicker)|DatePickerRenderer|UITextField|EditText||DatePicker|
|[`Editor`](xref:Xamarin.Forms.Editor)|EditorRenderer|UITextView|EditText||TextBox|
|[`Entry`](xref:Xamarin.Forms.Entry)|[EntryRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/entry.md)|UITextField|EditText||TextBox|
|[`Image`](xref:Xamarin.Forms.Image)|ImageRenderer|UIImageView|ImageView||Image|
|`ImageButton`|ImageButtonRenderer|UIButton||AppCompatImageButton|Botão|
|[`Label`](xref:Xamarin.Forms.Label)|LabelRenderer|UILabel|TextView||TextBlock|
|[`ListView`](xref:Xamarin.Forms.ListView)|[ListViewRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)|UITableView|ListView||ListView|
|[`Map`](xref:Xamarin.Forms.Maps.Map)|[MapRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)|MKMapView|MapView||MapControl|
|[`Picker`](xref:Xamarin.Forms.Picker)|PickerRenderer|UITextField|EditText|EditText|ComboBox|
|[`ProgressBar`](xref:Xamarin.Forms.ProgressBar)|ProgressBarRenderer|UIProgressView|ProgressBar||ProgressBar|
|[`SearchBar`](xref:Xamarin.Forms.SearchBar)|SearchBarRenderer|UISearchBar|SearchView||AutoSuggestBox|
|[`Slider`](xref:Xamarin.Forms.Slider)|SliderRenderer|UISlider|SeekBar||Controle deslizante|
|[`Stepper`](xref:Xamarin.Forms.Stepper)|StepperRenderer|UIStepper|LinearLayout||Controle|
|[`Switch`](xref:Xamarin.Forms.Switch)|SwitchRenderer|UISwitch|Alternar|SwitchCompat|ToggleSwitch|
|[`TableView`](xref:Xamarin.Forms.TableView)|TableViewRenderer|UITableView|ListView||ListView|
|[`TimePicker`](xref:Xamarin.Forms.TimePicker)|TimePickerRenderer|UITextField|EditText||TimePicker|
|[`WebView`](xref:Xamarin.Forms.WebView)|WebViewRenderer|UIWebView|WebView||WebView|

## <a name="cells"></a>Células

A tabela a seguir lista as classes de controle nativo que implementam cada xamarin. Forms e o renderizador [célula](~/xamarin-forms/user-interface/controls/cells.md) tipo:

|Células|Renderizador|iOS|Android|UWP|
|--- |--- |--- |--- |--- |
|[`EntryCell`](xref:Xamarin.Forms.EntryCell)|EntryCellRenderer|UITableViewCell com um UITextField|LinearLayout com um TextView e EditText|DataTemplate com uma caixa de texto|
|[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)|SwitchCellRenderer|UITableViewCell com um UISwitch|Alternar|DataTemplate com uma grade que contém um TextBlock e ToggleSwitch|
|[`TextCell`](xref:Xamarin.Forms.TextCell)|TextCellRenderer|UITableViewCell|LinearLayout com dois TextViews|DataTemplate com um StackPanel que contém dois TextBlocks|
|[`ImageCell`](xref:Xamarin.Forms.ImageCell)|ImageCellRenderer|UITableViewCell com um UIImage|LinearLayout com dois TextViews e um ImageView|DataTemplate com uma grade que contém uma imagem e dois TextBlocks|
|[`ViewCell`](xref:Xamarin.Forms.ViewCell)|[ViewCellRenderer](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)|UITableViewCell|Exibir|DataTemplate com um ContentPresenter|

## <a name="summary"></a>Resumo

Este artigo listou o renderizador e classes de controle nativo que implementam cada página do xamarin. Forms, layout, exibição e célula. Todos os controles xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo.

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados (vídeo do Xamarin University)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
