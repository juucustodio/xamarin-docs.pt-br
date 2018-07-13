---
title: Personalizando um ListView
description: Um ListView do xamarin. Forms é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula os controles de lista específico da plataforma e layouts de célula nativo, permitindo mais controle sobre o desempenho do controle de lista nativo.
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: b3b73d542faebdb8ab85c989d7812368f4f3ffac
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997473"
---
# <a name="customizing-a-listview"></a>Personalizando um ListView

_Um ListView do xamarin. Forms é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula os controles de lista específico da plataforma e layouts de célula nativo, permitindo mais controle sobre o desempenho do controle de lista nativo._

Cada modo de exibição do xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Quando um [ `ListView` ](xref:Xamarin.Forms.ListView) é processado por um aplicativo xamarin. Forms, no iOS o `ListViewRenderer` classe é instanciada, que por sua vez cria uma instância de um nativo `UITableView` controle. Na plataforma Android, o `ListViewRenderer` classe instancia um nativo `ListView` controle. Na Universal Windows Platform (UWP), o `ListViewRenderer` classe instancia um nativo `ListView` controle. Para obter mais informações sobre as classes de controle nativo que mapeiam controles xamarin. Forms e o renderizador, consulte [Classes de Base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `ListView` ](xref:Xamarin.Forms.ListView) controle e os controles nativos correspondentes que implementação-lo:

![](listview-images/listview-classes.png "Relação entre o controle ListView e implementar controles nativos")

O processo de renderização pode ser aproveitado para implementar personalizações específicas de plataforma, criando um renderizador personalizado para um [ `ListView` ](xref:Xamarin.Forms.ListView) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_ListView_Control) um controle personalizado do xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Control) o controle personalizado do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o controle em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `NativeListView` renderizador que tira proveito dos controles de lista específico da plataforma e layouts de célula nativo. Esse cenário é útil ao portar um aplicativo nativo existente que contém a lista e o código de célula pode ser reutilizado. Além disso, ele permite a personalização detalhada dos recursos de controle de lista que podem afetar o desempenho, como a virtualização de dados.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Criando o controle ListView personalizado

Um personalizado [ `ListView` ](xref:Xamarin.Forms.ListView) controle pode ser criado, a criação de subclasses de `ListView` de classe, conforme mostrado no exemplo de código a seguir:

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

O `NativeListView` é criada no projeto da biblioteca .NET Standard e define a API para o controle personalizado. Esse controle expõe um `Items` propriedade que é usada para popular o `ListView` com dados e que pode ser associada a dados para fins de exibição. Ele também expõe um `ItemSelected` evento será disparado sempre que um item é selecionado em um controle de lista nativo de plataforma específica. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O `NativeListView` controle personalizado pode ser referenciado em Xaml no projeto da biblioteca .NET Standard declarando um namespace para seu local e usando o prefixo de namespace no controle. O seguinte exemplo de código mostra como o `NativeListView` controle personalizado pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*" />
            </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
            <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
          </Grid>
      </ContentPage.Content>
</ContentPage>
```

O `local` prefixo de namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem corresponder aos detalhes de controle personalizado. Depois que o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o `NativeListView` controle personalizado pode ser consumido por uma página do c#:

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

O `NativeListView` controle personalizado usa renderizadores personalizados específicos da plataforma para exibir uma lista de dados, que são populados por meio de `Items` propriedade. Cada linha na lista contém três itens de dados – um nome, uma categoria e um nome de arquivo de imagem. O layout de cada linha na lista é definido pelo renderizador personalizado específico da plataforma.

> [!NOTE]
> Porque o `NativeListView` controle personalizado será renderizado usando controles de lista específico da plataforma que incluem a capacidade de rolagem, o controle personalizado não deve ser colocado em controles de layout rolável, como o [ `ScrollView` ](xref:Xamarin.Forms.ScrollView).

Agora pode ser adicionado a um renderizador personalizado para cada projeto de aplicativo para criar layouts de célula nativo e controles de lista específico da plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é da seguinte maneira:

1. Criar uma subclasse do `ListViewRenderer` classe que renderiza o controle personalizado.
1. Substituir o `OnElementChanged` método que processa a lógica personalizada de controle e gravar para personalizá-lo. Esse método é chamado quando o xamarin. Forms correspondente [ `ListView` ](xref:Xamarin.Forms.ListView) é criado.
1. Adicionar um `ExportRenderer` atributo à classe de renderizador personalizado para especificar que será usada para renderizar o controle personalizado do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com o xamarin. Forms.

> [!NOTE]
> É opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, o renderizador padrão para a classe de base da célula será usado.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](listview-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado NativeListView")

O `NativeListView` controle personalizado é renderizado por classes de renderizador específica da plataforma, que derivam de `ListViewRenderer` classe para cada plataforma. Isso resulta em cada `NativeListView` controle personalizado que está sendo renderizado com controles de lista específico da plataforma e layouts de célula nativo, conforme mostrado nas capturas de tela seguir:

![](listview-images/screenshots.png "NativeListView em cada plataforma")

O `ListViewRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o controle personalizado do xamarin. Forms é criado para renderizar o controle nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro, que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento do xamarin. Forms que o renderizador *foi* associada e o elemento do xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de exemplo, o `OldElement` propriedade será `null` e o `NewElement` propriedade conterá uma referência para o `NativeListView` instância.

Uma versão de substituição a `OnElementChanged` método em cada classe de renderizador específica da plataforma é o lugar para realizar a personalização de controle nativo. Uma referência tipada para o controle nativo que está sendo usado na plataforma pode ser acessada por meio de `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade.

Tome cuidado ao assinar manipuladores de eventos no `OnElementChanged` método, conforme demonstrado no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

O controle nativo deve ser configurado somente e manipuladores de eventos assinado quando o renderizador personalizado é anexado a um novo elemento xamarin. Forms. Da mesma forma, os manipuladores de evento inscritos devem ser cancelados somente quando o elemento o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado que não sofra perdas de memória.

Uma versão de substituição a `OnElementPropertyChanged` método em cada classe de renderizador específica da plataforma é o lugar para responder a alterações de propriedade associável sobre o controle personalizado do xamarin. Forms. Uma verificação para a propriedade que é alterada sempre deve ser feita conforme essa substituição pode ser chamada várias vezes.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle personalizado xamarin. Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a implementação de cada classe de renderizador personalizado específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma iOS:

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

O `UITableView` controle é configurado com a criação de uma instância da `NativeiOSListViewSource` de classe, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Essa classe fornece dados para o `UITableView` controle por meio da substituição a `RowsInSection` e `GetCell` métodos da `UITableViewSource` classe e por expor um `Items` propriedade que contém a lista de dados a serem exibidos. A classe também fornece um `RowSelected` substituição do método que invoca a `ItemSelected` eventos fornecidos pelo `NativeListView` controle personalizado. Para obter mais informações sobre o método de substituem, consulte [subclassificação UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). O `GetCell` método retorna um `UITableCellView` que é preenchido com dados para cada linha na lista e é mostrado no exemplo de código a seguir:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

Esse método cria um `NativeiOSListViewCell` instância para cada linha de dados que serão exibidos na tela. O `NativeiOSCell` instância define o layout de cada célula e os dados da célula. Quando uma célula desaparece da tela devido a rolagem, a célula será disponibilizada para reutilização. Isso evita o desperdício de memória, garantindo que existem apenas `NativeiOSCell` instâncias para os dados que está sendo exibidos na tela, em vez de todos os dados na lista. Para obter mais informações sobre a reutilização de célula, consulte [reutilização de célula](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). O `GetCell` método também lê a `ImageFilename` propriedade de cada linha de dados, fornecidos se ele existe e lê a imagem e armazena-o como um `UIImage` instância antes de atualizar o `NativeiOSListViewCell` de instância com os dados (nome, categoria e imagem) para a linha.

O `NativeiOSListViewCell` classe define o layout para cada célula e é mostrado no exemplo de código a seguir:

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Essa classe define os controles usados para renderizar o conteúdo da célula e o seu layout. O `NativeiOSListViewCell` construtor cria instâncias de `UILabel` e `UIImageView` controla e inicializa sua aparência. Esses controles são usados para exibir dados de cada linha, com o `UpdateCell` método que está sendo usado para definir esses dados em de `UILabel` e `UIImageView` instâncias. O local dessas instâncias é definido por substituído `LayoutSubviews` método, especificando as coordenadas dentro da célula.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Respondendo a uma alteração de propriedade no controle personalizado

Se o `NativeListView.Items` propriedade alterado devido a itens adicionados ou removidos da lista, o renderizador personalizado precisa responder exibindo as alterações. Isso pode ser feito por meio da substituição de `OnElementPropertyChanged` método, que é mostrado no exemplo de código a seguir:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

O método cria uma nova instância dos `NativeiOSListViewSource` classe que fornece dados para o `UITableView` controlar, contanto que o associável `NativeListView.Items` propriedade foi alterada.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

Nativo `ListView` controle é configurado, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Essa configuração envolve a criação de uma instância das `NativeAndroidListViewAdapter` classe que fornece dados para o nativo `ListView` controlar e registrar um manipulador de eventos para processar o `ItemClick` eventos. Por sua vez, esse manipulador invocará o `ItemSelected` eventos fornecidos pelo `NativeListView` controle personalizado. O `ItemClick` evento é cancelado a assinatura de se o elemento de xamarin. Forms o renderizador está anexado for alterado.

O `NativeAndroidListViewAdapter` deriva a `BaseAdapter` classe e expõe um `Items` propriedade que contém a lista de dados a serem exibidos, bem como substituir o `Count`, `GetView`, `GetItemId`, e `this[int]` métodos. Para obter mais informações sobre essas substituições de método, consulte [implementando um ListAdapter](~/android/user-interface/layouts/list-view/populating.md). O `GetView` método retorna uma exibição para cada linha, preenchida com dados e é mostrado no exemplo de código a seguir:

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

O `GetView` método é chamado para retornar a célula a ser renderizado, como um `View`, para cada linha de dados na lista. Ele cria uma `View` instância para cada linha de dados que serão exibidos na tela, com a aparência do `View` instância que está sendo definida em um arquivo de layout. Quando uma célula desaparece da tela devido a rolagem, a célula será disponibilizada para reutilização. Isso evita o desperdício de memória, garantindo que existem apenas `View` instâncias para os dados que está sendo exibidos na tela, em vez de todos os dados na lista. Para obter mais informações sobre a reutilização do modo de exibição, consulte [reutilização de exibição linha](~/android/user-interface/layouts/list-view/populating.md).

O `GetView` método também preenche a `View` instância com dados, incluindo a leitura dos dados de imagem do nome do arquivo especificado no `ImageFilename` propriedade.

O layout de cada exibido de célula por nativo `ListView` é definido em de `NativeAndroidListViewCell.axml` arquivo de layout, que seja inflacionado pelo `LayoutInflater.Inflate` método. O exemplo de código a seguir mostra a definição de layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

Esse layout Especifica que dois `TextView` controles e um `ImageView` controle são usados para exibir o conteúdo da célula. Os dois `TextView` controles são orientados verticalmente dentro de uma `LinearLayout` controle, com todos os controles que está sendo contidos em um `RelativeLayout`.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Respondendo a uma alteração de propriedade no controle personalizado

Se o `NativeListView.Items` propriedade alterado devido a itens adicionados ou removidos da lista, o renderizador personalizado precisa responder exibindo as alterações. Isso pode ser feito por meio da substituição de `OnElementPropertyChanged` método, que é mostrado no exemplo de código a seguir:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

O método cria uma nova instância dos `NativeAndroidListViewAdapter` classe que fornece dados para o nativo `ListView` controlar, contanto que o associável `NativeListView.Items` propriedade foi alterada.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O exemplo de código a seguir mostra o renderizador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeUWPListViewRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

Nativo `ListView` controle é configurado, desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms. Essa configuração envolve a configuração como nativo `ListView` controle responderá aos itens que estão sendo selecionados, preencher os dados exibidos pelo controle, definir a aparência e o conteúdo de cada célula e registrar um manipulador de eventos para processar os `SelectionChanged` eventos. Por sua vez, esse manipulador invocará o `ItemSelected` eventos fornecidos pelo `NativeListView` controle personalizado. O `SelectionChanged` evento é cancelado a assinatura de se o elemento de xamarin. Forms o renderizador está anexado for alterado.

A aparência e o conteúdo de cada nativos `ListView` célula são definidos por um `DataTemplate` denominado `ListViewItemTemplate`. Isso `DataTemplate` são armazenados no dicionário de recursos de nível de aplicativo e é mostrado no exemplo de código a seguir:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

O `DataTemplate` Especifica os controles usados para exibir o conteúdo da célula e o layout e a aparência. Duas `TextBlock` controles e um `Image` controle são usados para exibir o conteúdo da célula por meio da vinculação de dados. Além disso, uma instância das `ConcatImageExtensionConverter` é usado para concatenar a `.jpg` extensão para cada nome de arquivo de imagem do arquivo. Isso garante que o `Image` controle pode carregar e processar a imagem quando ele estiver `Source` propriedade está definida.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Respondendo a uma alteração de propriedade no controle personalizado

Se o `NativeListView.Items` propriedade alterado devido a itens adicionados ou removidos da lista, o renderizador personalizado precisa responder exibindo as alterações. Isso pode ser feito por meio da substituição de `OnElementPropertyChanged` método, que é mostrado no exemplo de código a seguir:

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

O método preenche novamente nativo `ListView` controle com os dados alterados, contanto que o associável `NativeListView.Items` propriedade foi alterada.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado que encapsula os controles de lista específico da plataforma e layouts de célula nativo, permitindo mais controle sobre o desempenho do controle de lista nativo.


## <a name="related-links"></a>Links relacionados

- [CustomRendererListView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/listview/)
