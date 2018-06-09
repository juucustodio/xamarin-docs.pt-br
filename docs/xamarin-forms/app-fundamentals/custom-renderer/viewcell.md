---
title: Personalizando um ViewCell
description: Um ViewCell xamarin. Forms é uma célula que pode ser adicionada a um ListView ou modo de tabela que contém uma exibição definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um ViewCell que está hospedado dentro de um controle ListView do xamarin. Forms.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 2011049180aa47b7be68486d4f30bd356e2ba813
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241797"
---
# <a name="customizing-a-viewcell"></a>Personalizando um ViewCell

_Um ViewCell xamarin. Forms é uma célula que pode ser adicionada a um ListView ou modo de tabela que contém uma exibição definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um ViewCell que está hospedado dentro de um controle ListView do xamarin. Forms. Isso impede que os cálculos de layout xamarin. Forms de ser chamado várias vezes durante a rolagem de ListView._

Cada célula xamarin. Forms tem um processador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) é processado por um aplicativo xamarin. Forms, no iOS a `ViewCellRenderer` classe é instanciada, que por sua vez instancia um nativo `UITableViewCell` controle. Na plataforma Android, o `ViewCellRenderer` classe instancia um nativo `View` controle. Sobre o Windows UWP (plataforma Universal), o `ViewCellRenderer` classe instancia um nativo `DataTemplate`. Para obter mais informações sobre o processador e classes de controle nativo que mapeiam xamarin. Forms controles, consulte [Classes de Base de processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) e os controles nativo correspondentes que implementação-la:

![](viewcell-images/viewcell-classes.png "Relação entre o controle ViewCell e implementar controles nativos")

O processo de renderização pode ser tomado aproveitar para implementar personalizações específicas da plataforma, criando um renderizador personalizado para um [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_Custom_Cell) uma célula personalizada xamarin. Forms.
1. [Consumir](#Consuming_the_Custom_Cell) na célula personalizada de xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) renderizador personalizado para a célula em cada plataforma.

Cada item agora será discutida por sua vez, para implementar um `NativeCell` processador que tira proveito de um layout específico de plataforma para cada célula hospedado dentro de um xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controle. Isso impede que os cálculos de layout xamarin. Forms de ser chamado repetidamente durante `ListView` rolagem.

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>Criando a célula personalizada

Um controle de célula personalizado pode ser criado, subclassificação o [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) classe, conforme mostrado no exemplo de código a seguir:

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```
O `NativeCell` classe é criada no projeto da biblioteca .NET padrão e define a API para a célula personalizada. A célula personalizada expõe `Name`, `Category`, e `ImageFilename` propriedades que podem ser exibidas por meio de associação de dados. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>Consumindo a célula personalizada

O `NativeCell` célula personalizado pode ser referenciada em Xaml no projeto da biblioteca .NET padrão declarando um namespace para seu local e usando o prefixo de namespace no elemento de célula personalizado. O seguinte exemplo de código mostra como o `NativeCell` célula personalizada pode ser consumida por uma página XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <StackLayout>
              <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
              <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                  <ListView.ItemTemplate>
                      <DataTemplate>
                          <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                      </DataTemplate>
                  </ListView.ItemTemplate>
              </ListView>
          </StackLayout>
      </ContentPage.Content>
</ContentPage>
```

O `local` o prefixo do namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem coincidir com os detalhes do controle personalizado. Depois que o namespace for declarado, o prefixo é usado para fazer referência à célula personalizada.

O seguinte exemplo de código mostra como o `NativeCell` célula personalizada pode ser consumida por uma página c#:

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
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

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Um xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controle é usado para exibir uma lista de dados, que são preenchidas por meio de [ `ItemSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemsSource/) propriedade. O [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estratégia de cache tenta minimizar a `ListView` espaço de memória e execução acelerar Reciclando células da lista. Para obter mais informações, consulte [estratégia de cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

Cada linha na lista contém três itens de dados – um nome, uma categoria e um nome de arquivo de imagem. O layout de cada linha na lista é definido pelo `DataTemplate` que é referenciado por meio de [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemTemplate/) propriedade associável. O `DataTemplate` define que cada linha de dados na lista será um `NativeCell` que exibe sua `Name`, `Category`, e `ImageFilename` propriedades por meio de associação de dados. Para obter mais informações sobre o `ListView` , consulte [ListView](~/xamarin-forms/user-interface/listview/index.md).

Agora é possível adicionar um renderizador personalizado para cada projeto de aplicativo para personalizar o layout de plataforma específica para cada célula.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é o seguinte:

1. Criar uma subclasse do `ViewCellRenderer` classe que renderiza a célula personalizada.
1. Substitua o método específico de plataforma que renderiza a célula personalizada e grave a lógica para personalizá-lo.
1. Adicionar um `ExportRenderer` de atributo para a classe de renderizador personalizado para especificar que será usada para renderizar a célula personalizada xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com xamarin. Forms.

> [!NOTE]
> Para a maioria dos elementos de xamarin. Forms, é opcional fornecer um renderizador personalizado em cada projeto da plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão para a classe base do controle. No entanto, renderizadores personalizados são necessários em cada projeto de plataforma ao renderizar um [ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) elemento.

O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com as relações entre eles:

![](viewcell-images/solution-structure.png "Responsabilidades de projeto do renderizador NativeCell personalizado")

O `NativeCell` célula personalizado é processada por classes de processador específico da plataforma, que derivam de `ViewCellRenderer` classe para cada plataforma. Isso resulta em cada `NativeCell` célula personalizado está sendo processada com o layout de plataforma específica, conforme mostrado nas capturas de tela seguir:

![](viewcell-images/screenshots.png "NativeCell em cada plataforma")

O `ViewCellRenderer` classe expõe métodos específicos de plataforma para a renderização de célula personalizada. Este é o `GetCell` método na plataforma iOS, o `GetCellCore` método na plataforma Android e o `GetTemplate` método UWP.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo da célula xamarin. Forms está sendo processado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica ao assembly inteiro.

As seções a seguir discutem a implementação de cada classe de renderizador personalizado específico da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma iOS:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

O `GetCell` método é chamado para criar cada célula a ser exibido. Cada célula é um `NativeiOSCell` instância, que define o layout da célula e seus dados. A operação do `GetCell` método depende de [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estratégia de cache:

- Quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estratégia de cache é [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/), o `GetCell` método será chamado para cada célula. Um `NativeiOSCell` instância será criada para cada `NativeCell` instância que é inicialmente exibida na tela. Como o usuário rola o `ListView`, `NativeiOSCell` instâncias serão reutilizadas. Para obter mais informações sobre a reutilização de célula iOS, consulte [reutilizar célula](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Esse código de renderizador personalizado irá realizar algumas célula reutilização, mesmo quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é definida para manter as células.

  Os dados exibidos por cada `NativeiOSCell` instância, se recentemente criados ou usados novamente, será atualizada com os dados de cada `NativeCell` instância pelo `UpdateCell` método.

  > [!NOTE]
  > O `OnNativeCellPropertyChanged` método nunca será chamado quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estratégia de cache é definida para reter as células.

- Quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estratégia de cache é [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/), o `GetCell` método será chamado para cada célula que inicialmente é exibida na tela. Um `NativeiOSCell` instância será criada para cada `NativeCell` instância que é inicialmente exibida na tela. Os dados exibidos por cada `NativeiOSCell` instância será atualizada com os dados do `NativeCell` instância pelo `UpdateCell` método. No entanto, o `GetCell` método não invocado como o usuário o percorre o `ListView`. Em vez disso, o `NativeiOSCell` instâncias serão reutilizadas. `PropertyChanged` eventos serão gerados no `NativeCell` instância quando seus dados são alterados e o `OnNativeCellPropertyChanged` manipulador de eventos atualizará os dados em cada reutilizadas `NativeiOSCell` instância.

O seguinte exemplo de código mostra o `OnNativeCellPropertyChanged` método que tem invocado quando uma `PropertyChanged` é gerado:

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Essas atualizações de método os dados que está sendo exibidos pelo reutilizadas `NativeiOSCell` instâncias. É feita uma verificação para a propriedade que é alterada, como o método pode ser chamado várias vezes.

O `NativeiOSCell` classe define o layout de cada célula e é mostrado no exemplo de código a seguir:

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Essa classe define os controles usados para renderizar o conteúdo da célula e seu layout. A classe implementa o [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) interface, que é necessária quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) usa o [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estratégia de cache. Essa interface Especifica que a classe deve implementar o [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) propriedade, que deve retornar os dados de célula personalizado para células reciclados.

O `NativeiOSCell` construtor inicializa a aparência do `HeadingLabel`, `SubheadingLabel`, e `CellImageView` propriedades. Essas propriedades são usadas para exibir os dados armazenados no `NativeCell` instância, com o `UpdateCell` método ser chamado para definir o valor de cada propriedade. Além disso, quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) usa o [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) cache estratégia, os dados exibidos pelo `HeadingLabel`, `SubheadingLabel`, e `CellImageView` propriedades podem ser atualizado pelo `OnNativeCellPropertyChanged` método no renderizador personalizado.

Layout da célula é executada pelo `LayoutSubviews` substituir, que define as coordenadas de `HeadingLabel`, `SubheadingLabel`, e `CellImageView` dentro da célula.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

O `GetCellCore` método é chamado para criar cada célula a ser exibido. Cada célula é um `NativeAndroidCell` instância, que define o layout da célula e seus dados. A operação do `GetCellCore` método depende de [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estratégia de cache:

- Quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estratégia de cache é [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/), o `GetCellCore` método será chamado para cada célula. Um `NativeAndroidCell` será criado para cada `NativeCell` instância que é inicialmente exibida na tela. Como o usuário rola o `ListView`, `NativeAndroidCell` instâncias serão reutilizadas. Para obter mais informações sobre a reutilização de célula Android, consulte [reutilização do modo de exibição linha](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Observe que esse código de renderizador personalizado irá realizar algumas célula reutilizar mesmo quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é definida para manter as células.

  Os dados exibidos por cada `NativeAndroidCell` instância, se recentemente criados ou usados novamente, será atualizada com os dados de cada `NativeCell` instância pelo `UpdateCell` método.

  > [!NOTE]
  > Observe que, embora o `OnNativeCellPropertyChanged` método será chamado quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é definida para manter as células, não será atualizada a `NativeAndroidCell` valores de propriedade.

- Quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estratégia de cache é [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/), o `GetCellCore` método será chamado para cada célula que inicialmente é exibida na tela. Um `NativeAndroidCell` instância será criada para cada `NativeCell` instância que é inicialmente exibida na tela. Os dados exibidos por cada `NativeAndroidCell` instância será atualizada com os dados do `NativeCell` instância pelo `UpdateCell` método. No entanto, o `GetCellCore` método não invocado como o usuário o percorre o `ListView`. Em vez disso, o `NativeAndroidCell` instâncias serão reutilizadas.  `PropertyChanged` eventos serão gerados no `NativeCell` instância quando seus dados são alterados e o `OnNativeCellPropertyChanged` manipulador de eventos atualizará os dados em cada reutilizadas `NativeAndroidCell` instância.

O seguinte exemplo de código mostra o `OnNativeCellPropertyChanged` método que tem invocado quando uma `PropertyChanged` é gerado:

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Essas atualizações de método os dados que está sendo exibidos pelo reutilizadas `NativeAndroidCell` instâncias. É feita uma verificação para a propriedade que é alterada, como o método pode ser chamado várias vezes.

O `NativeAndroidCell` classe define o layout de cada célula e é mostrado no exemplo de código a seguir:

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

Essa classe define os controles usados para renderizar o conteúdo da célula e seu layout. A classe implementa o [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) interface, que é necessária quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) usa o [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) estratégia de cache. Essa interface Especifica que a classe deve implementar o [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) propriedade, que deve retornar os dados de célula personalizado para células reciclados.

O `NativeAndroidCell` inflação do construtor de `NativeAndroidCell` layout e inicializa o `HeadingTextView`, `SubheadingTextView`, e `ImageView` propriedades para os controles no layout inflated. Essas propriedades são usadas para exibir os dados armazenados no `NativeCell` instância, com o `UpdateCell` método ser chamado para definir o valor de cada propriedade. Além disso, quando o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) usa o [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) cache estratégia, os dados exibidos pelo `HeadingTextView`, `SubheadingTextView`, e `ImageView` propriedades podem ser atualizado pelo `OnNativeCellPropertyChanged` método no renderizador personalizado.

O exemplo de código a seguir mostra a definição de layout para o `NativeAndroidCell.axml` arquivo de layout:

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
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
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

Esse layout Especifica que dois `TextView` controles e um `ImageView` controle são usados para exibir o conteúdo da célula. Os dois `TextView` controles são orientados verticalmente dentro de um `LinearLayout` controle, com todos os controles que está sendo contidos em um `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado em UWP

O exemplo de código a seguir mostra o renderizador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

O `GetTemplate` método é chamado para retornar a célula a ser renderizado para cada linha de dados na lista. Ele cria um `DataTemplate` para cada `NativeCell` instância que será exibida na tela, com o `DataTemplate` definindo a aparência e o conteúdo da célula.

O `DataTemplate` é armazenado no dicionário de recursos de nível de aplicativo e é mostrado no exemplo de código a seguir:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
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

O `DataTemplate` Especifica os controles usados para exibir o conteúdo da célula e seu layout e aparência. Dois `TextBlock` controles e um `Image` controle são usados para exibir o conteúdo da célula por meio de associação de dados. Além disso, uma instância do `ConcatImageExtensionConverter` é usado para concatenar a `.jpg` extensão para cada nome de arquivo de imagem do arquivo. Isso garante que o `Image` controle pode carregar e processar a imagem quando for `Source` está definida.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) que está hospedado dentro de um xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controle. Isso impede que os cálculos de layout xamarin. Forms de ser chamado repetidamente durante `ListView` rolagem.


## <a name="related-links"></a>Links relacionados

- [Desempenho de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
