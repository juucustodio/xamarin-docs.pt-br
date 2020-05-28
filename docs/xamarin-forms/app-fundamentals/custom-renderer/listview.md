---
title: ''
description: Um Xamarin.Forms ListView é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula os controles de lista e layouts de célula nativa específicos a uma plataforma, permitindo mais controle sobre o desempenho do controle de lista nativo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3403948c2853289610a73bb36073f09c0c86137d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135298"
---
# <a name="customizing-a-listview"></a>Personalizando uma ListView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)

_Um Xamarin.Forms ListView é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula controles de lista específicos da plataforma e layouts de células nativas, permitindo mais controle sobre o desempenho do controle de lista nativa._

Cada Xamarin.Forms exibição tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`ListView`](xref:Xamarin.Forms.ListView) é renderizado por um Xamarin.Forms aplicativo, no Ios, a `ListViewRenderer` classe é instanciada, o que, por sua vez, instancia um `UITableView` controle nativo. Na plataforma Android, a classe `ListViewRenderer` cria uma instância de um controle `ListView` nativo. Na UWP (Plataforma Universal do Windows), a classe `ListViewRenderer` cria uma instância de um controle `ListView` nativo. Para obter mais informações sobre o renderizador e as classes de controle nativo que Xamarin.Forms controlam o mapa para o, consulte [classes base do processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra a relação entre o [`ListView`](xref:Xamarin.Forms.ListView) controle e os controles nativos correspondentes que o implementam:

![](listview-images/listview-classes.png "Relationship Between the ListView Control and the Implementing Native Controls")

O processo de renderização pode ser aproveitado para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`ListView`](xref:Xamarin.Forms.ListView) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Crie](#Creating_the_Custom_ListView_Control) um Xamarin.Forms controle personalizado.
1. [Consuma](#Consuming_the_Custom_Control) o controle personalizado do Xamarin.Forms .
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o controle em cada plataforma.

Agora, cada item será abordado separadamente, a fim de implementar um renderizador de `NativeListView` que tira proveito dos layouts de célula nativos e dos controles de lista específicos da plataforma. Esse cenário é útil ao portar um aplicativo nativo existente que contém código de lista e de célula pode ser reutilizado. Além disso, ele permite a personalização detalhada de recursos de controle de lista que podem afetar o desempenho, como a virtualização de dados.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Criando o controle ListView personalizado

Um [`ListView`](xref:Xamarin.Forms.ListView) controle personalizado pode ser criado por meio da subclasse da `ListView` classe, conforme mostrado no exemplo de código a seguir:

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

A `NativeListView` é criada no projeto da biblioteca .NET Standard e define a API para o controle personalizado. Esse controle expõe uma propriedade `Items` que é usada para popular o `ListView` com os dados e que pode ser associada a dados para fins de exibição. Ele também expõe um evento `ItemSelected` que será disparado sempre que um item for selecionado em um controle de lista nativo específico da plataforma. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Consumindo o controle personalizado

O controle personalizado `NativeListView` pode ser referenciado em XAML no projeto da biblioteca .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no controle. O exemplo de código a seguir mostra como o controle personalizado `NativeListView` pode ser consumido por uma página XAML:

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

O prefixo do namespace `local` pode ser qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o controle personalizado `NativeListView` pode ser consumido por um página em C#:

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

O controle personalizado `NativeListView` usa renderizadores personalizados específicos da plataforma para exibir uma lista de dados, que são populados por meio da propriedade `Items`. Cada linha na lista contém três itens de dados – um nome, uma categoria e um nome de arquivo de imagem. O layout de cada linha na lista é definido pelo renderizador personalizado específico da plataforma.

> [!NOTE]
> Como o `NativeListView` controle personalizado será renderizado usando controles de lista específicos da plataforma que incluem a capacidade de rolagem, o controle personalizado não deve ser hospedado em controles de layout rolável, como o [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

Agora, é possível adicionar um renderizador personalizado a cada projeto de aplicativo para criar layouts de célula nativa e controles de lista específicos da plataforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é a seguinte:

1. Crie uma subclasse da classe `ListViewRenderer` que renderiza o controle personalizado.
1. Substitua o método `OnElementChanged` que renderiza o controle personalizado e escreva a lógica para personalizá-lo. Esse método é chamado quando o correspondente Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) é criado.
1. Adicione um `ExportRenderer` atributo à classe de processador personalizado para especificar que ele será usado para renderizar o Xamarin.Forms controle personalizado. Esse atributo é usado para registrar o renderizador personalizado com Xamarin.Forms .

> [!NOTE]
> O fornecimento de um renderizador personalizado em cada projeto da plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base da célula.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](listview-images/solution-structure.png "NativeListView Custom Renderer Project Responsibilities")

O controle personalizado `NativeListView` é renderizado por classes de renderizador específicas da plataforma, que derivam da classe `ListViewRenderer` para cada plataforma. Isso faz com que cada controle personalizado `NativeListView` seja renderizado com controles de lista específicos da plataforma e layouts de célula nativos, conforme mostrado nas capturas de tela seguir:

![](listview-images/screenshots.png "NativeListView on each Platform")

A `ListViewRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o Xamarin.Forms controle personalizado é criado para renderizar o controle nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o Xamarin.Forms elemento ao qual o renderizador *foi* anexado e o Xamarin.Forms elemento ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `NativeListView`.

Uma versão de substituição do método `OnElementChanged`, em cada classe de renderizador específica da plataforma, é o lugar para realização da personalização do controle nativo. Uma referência tipada ao controle nativo que está sendo usado na plataforma pode ser acessada por meio da propriedade `Control`. Além disso, uma referência ao Xamarin.Forms controle que está sendo processado pode ser obtida por meio da `Element` propriedade.

É necessário ter cuidado ao assinar manipuladores de eventos no método `OnElementChanged`, conforme demonstrado no seguinte exemplo de código:

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

O controle nativo só deve ser configurado e os manipuladores de eventos assinados quando o renderizador personalizado é anexado a um novo Xamarin.Forms elemento. De forma semelhante, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado que não sofre perdas de memória.

Uma versão substituída do `OnElementPropertyChanged` método, em cada classe de processador específica da plataforma, é o local para responder às alterações de propriedade vinculáveis no Xamarin.Forms controle personalizado. Uma verificação da propriedade alterada sempre deve ser feita, pois essa substituição pode ser chamada várias vezes.

Cada classe de processador personalizado é decorada com um `ExportRenderer` atributo que registra o renderizador com Xamarin.Forms . O atributo usa dois parâmetros – o nome do tipo do Xamarin.Forms controle personalizado que está sendo renderizado e o nome do tipo do renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação de cada classe de renderizador personalizado específica da plataforma.

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

O `UITableView` controle é configurado pela criação de uma instância da `NativeiOSListViewSource` classe, desde que o renderizador personalizado seja anexado a um novo Xamarin.Forms elemento. Essa classe fornece dados ao controle `UITableView` substituindo os métodos `RowsInSection` e `GetCell` da classe `UITableViewSource` e expondo uma propriedade `Items` que contém a lista de dados a serem exibidos. A classe também fornece uma substituição do método `RowSelected` que invoca o evento `ItemSelected` fornecido pelo controle personalizado `NativeListView`. Para obter mais informações sobre as substituições de método, confira [Subclassificação de UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). O método `GetCell` retorna um `UITableCellView` que é preenchido com os dados para cada linha na lista e é mostrado no exemplo de código a seguir:

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

Esse método cria uma instância de `NativeiOSListViewCell` para cada linha de dados que será exibida na tela. A instância de `NativeiOSCell` define o layout de cada célula e os dados da célula. Quando uma célula desaparecer da tela devido à rolagem, ela será disponibilizada para reutilização. Isso evita o desperdício de memória garantindo que haja apenas instâncias de `NativeiOSCell` para os dados que estão sendo exibidos na tela, em vez de todos os dados da lista. Para obter mais informações sobre a reutilização de células, confira [Reutilização de células](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). O método `GetCell` também lê a propriedade `ImageFilename` de cada linha de dados, desde que elas existam, e lê a imagem e a armazena como uma instância de `UIImage` antes de atualizar a instância de `NativeiOSListViewCell` com os dados (nome, categoria e imagem) da linha.

A classe `NativeiOSListViewCell` define o layout para cada célula e é mostrada no exemplo de código a seguir:

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

Essa classe define os controles usados para renderizar o conteúdo da célula e seu layout. O construtor `NativeiOSListViewCell` cria instâncias dos controles `UILabel` e `UIImageView` e inicializa sua aparência. Esses controles são usados para exibir dados de cada linha, com o método `UpdateCell` sendo usado para definir esses dados na instâncias de `UILabel` e `UIImageView`. A localização dessas instâncias é definida pelo método `LayoutSubviews` substituído especificando suas coordenadas dentro da célula.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Respondendo a uma alteração de propriedade no controle personalizado

Se a propriedade `NativeListView.Items` for alterada devido a itens serem adicionados ou removidos da lista, o renderizador personalizado precisará responder exibindo as alterações. Isso pode ser feito substituindo o método `OnElementPropertyChanged`, que é mostrado no exemplo de código a seguir:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

O método cria uma nova instância da classe `NativeiOSListViewSource` que fornece dados para o controle `UITableView`, desde que a propriedade `NativeListView.Items` vinculável tenha sido alterada.

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

O `ListView` controle nativo é configurado desde que o renderizador personalizado esteja anexado a um novo Xamarin.Forms elemento. Essa configuração envolve a criação de uma instância da classe `NativeAndroidListViewAdapter` que fornece dados para o controle `ListView` nativo e o registro de um manipulador de eventos para processar o evento `ItemClick`. Por sua vez, esse manipulador invocará o evento `ItemSelected` fornecido pelo controle personalizado `NativeListView`. O `ItemClick` cancelamento da assinatura do evento se o Xamarin.Forms elemento ao qual o processador está anexado for alterado.

O `NativeAndroidListViewAdapter` deriva da classe `BaseAdapter` e expõe uma propriedade `Items` que contém a lista de dados a serem exibidos, além de substituir os métodos `Count`, `GetView`, `GetItemId` e `this[int]`. Para obter mais informações sobre essas substituições de método, confira [Implementando um ListAdapter](~/android/user-interface/layouts/list-view/populating.md). O método `GetView` retorna uma exibição para cada linha, preenchida com os dados, e é mostrado no exemplo de código a seguir:

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

O método `GetView` é chamado para retornar a célula a ser renderizada, como um `View`, para cada linha de dados na lista. Ele cria uma instância de `View` para cada linha de dados que será exibida na tela, com a aparência da instância de `View` definida em um arquivo de layout. Quando uma célula desaparecer da tela devido à rolagem, ela será disponibilizada para reutilização. Isso evita o desperdício de memória garantindo que haja apenas instâncias de `View` para os dados que estão sendo exibidos na tela, em vez de todos os dados da lista. Para obter mais informações sobre a reutilização da exibição, confira [Reutilização da exibição de linha](~/android/user-interface/layouts/list-view/populating.md).

O método `GetView` também preenche a instância de `View` com os dados, incluindo a leitura dos dados de imagem do nome de arquivo especificado na propriedade `ImageFilename`.

O layout de cada célula exibida pelo `ListView` nativo é definido no arquivo de layout `NativeAndroidListViewCell.axml`, que é inflado pelo método `LayoutInflater.Inflate`. O exemplo de código a seguir mostra a definição do layout:

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

Esse layout especifica que dois controles `TextView` e um controle `ImageView` sejam usados para exibir o conteúdo da célula. Os dois controles `TextView` têm orientação vertical dentro de um controle `LinearLayout`, com todos os controles contidos em um `RelativeLayout`.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Respondendo a uma alteração de propriedade no controle personalizado

Se a propriedade `NativeListView.Items` for alterada devido a itens serem adicionados ou removidos da lista, o renderizador personalizado precisará responder exibindo as alterações. Isso pode ser feito substituindo o método `OnElementPropertyChanged`, que é mostrado no exemplo de código a seguir:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

O método cria uma nova instância da classe `NativeAndroidListViewAdapter` que fornece dados para o controle `ListView` nativo, desde que a propriedade `NativeListView.Items` vinculável tenha sido alterada.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O seguinte exemplo de código mostra o renderizador personalizado para o UWP:

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

O `ListView` controle nativo é configurado desde que o renderizador personalizado esteja anexado a um novo Xamarin.Forms elemento. Essa configuração envolve definir como o controle `ListView` nativo responderá à seleção de itens, ao preenchimento dos dados exibidos pelo controle, à definição da aparência e do conteúdo de cada célula e ao registro de um manipulador de eventos para processar o evento `SelectionChanged`. Por sua vez, esse manipulador invocará o evento `ItemSelected` fornecido pelo controle personalizado `NativeListView`. O `SelectionChanged` cancelamento da assinatura do evento se o Xamarin.Forms elemento ao qual o processador está anexado for alterado.

A aparência e o conteúdo de cada célula `ListView` nativa são definidos por um `DataTemplate` denominado `ListViewItemTemplate`. Esse `DataTemplate` é armazenado no dicionário de recursos de nível de aplicativo e é mostrado no exemplo de código a seguir:

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

O `DataTemplate` especifica os controles usados para exibir o conteúdo da célula, bem como seu layout e aparência. Dois controles `TextBlock` e um controle `Image` são usados para exibir o conteúdo da célula por meio da associação de dados. Além disso, uma instância do `ConcatImageExtensionConverter` é usada para concatenar a extensão de arquivo `.jpg` a cada nome de arquivo de imagem. Isso garante que o controle `Image` possa carregar e renderizar a imagem quando sua propriedade `Source` estiver definida.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Respondendo a uma alteração de propriedade no controle personalizado

Se a propriedade `NativeListView.Items` for alterada devido a itens serem adicionados ou removidos da lista, o renderizador personalizado precisará responder exibindo as alterações. Isso pode ser feito substituindo o método `OnElementPropertyChanged`, que é mostrado no exemplo de código a seguir:

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

O método preenche novamente o controle `ListView` nativo com os dados alterados, desde que a propriedade `NativeListView.Items` vinculável tenha sido alterada.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado que encapsula os controles de lista e layouts de célula nativa específicos a uma plataforma, permitindo mais controle sobre o desempenho do controle de lista nativo.

## <a name="related-links"></a>Links relacionados

- [CustomRendererListView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)
