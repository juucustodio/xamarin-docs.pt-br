---
title: Personalizando um ViewCell
description: Um ViewCell do Xamarin.Forms é uma célula que pode ser adicionada a um ListView ou a um TableView, que contém uma exibição definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um ViewCell hospedado dentro de um controle ListView do Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 90215be6356722729b441513bf749150c77755ca
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924644"
---
# <a name="customizing-a-viewcell"></a>Personalizando um ViewCell

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/ViewCell/)

_Um ViewCell do Xamarin.Forms é uma célula que pode ser adicionada a um ListView ou a um TableView, que contém uma exibição definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um ViewCell hospedado dentro de um controle ListView do Xamarin.Forms. Isso impede que os cálculos de layout do Xamarin.Forms sejam chamados repetidamente durante a rolagem de ListView._

Cada uma das células do Xamarin.Forms tem um renderizador que a acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`ViewCell`](xref:Xamarin.Forms.ViewCell) é renderizado por um aplicativo Xamarin.Forms, no iOS é criada uma instância da classe `ViewCellRenderer`, o que por sua vez cria uma instância de um controle `UITableViewCell` nativo. Na plataforma Android, a classe `ViewCellRenderer` cria uma instância de um controle `View` nativo. Na UWP (Plataforma Universal do Windows), a classe `ViewCellRenderer` cria uma instância de um `DataTemplate` nativo. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O seguinte diagrama ilustra a relação entre o [`ViewCell`](xref:Xamarin.Forms.ViewCell) e os controles nativos correspondentes que o implementam:

![](viewcell-images/viewcell-classes.png "Relação entre o controle ViewCell e a implementação de controles nativos")

E possível aproveitar o processo de renderização para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`ViewCell`](xref:Xamarin.Forms.ViewCell) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_Custom_Cell) uma célula personalizada do Xamarin.Forms.
1. [Consumir](#Consuming_the_Custom_Cell) a célula personalizada do Xamarin.Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para a célula em cada plataforma.

Agora, cada item será abordado separadamente, a fim de implementar um renderizador de `NativeCell` que tira proveito de um layout específico da plataforma para cada célula hospedada dentro de um controle [`ListView`](xref:Xamarin.Forms.ListView) do Xamarin.Forms. Isso impede que os cálculos de layout do Xamarin.Forms sejam chamados repetidamente durante a rolagem de `ListView`.

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>Criando a célula personalizada

É possível criar um controle de célula personalizado criando subclasses da classe [`ViewCell`](xref:Xamarin.Forms.ViewCell), conforme mostrado no exemplo de código a seguir:

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
A classe `NativeCell` é criada no projeto da biblioteca .NET Standard e define a API para a célula personalizada. A célula personalizada expõe as propriedades `Name`, `Category` e `ImageFilename`, que podem ser exibidas por meio da associação de dados. Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>Consumindo a célula personalizada

A célula personalizada `NativeCell` pode ser referenciada em XAML no projeto da biblioteca .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace na célula personalizada. O exemplo de código a seguir mostra como a célula personalizada `NativeCell` pode ser consumida por uma página XAML:

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

O prefixo do namespace `local` pode ter qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar a célula personalizada.

O exemplo de código a seguir mostra como a célula personalizada `NativeCell` pode ser consumida por uma página C#:

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

Um controle [`ListView`](xref:Xamarin.Forms.ListView) do Xamarin.Forms é usado para exibir uma lista de dados, que é preenchida por meio da propriedade [`ItemSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource). A estratégia de cache de [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) tenta minimizar o volume de memória de `ListView` e a velocidade de execução reciclando células da lista. Para obter mais informações, confira [Estratégia de Cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

Cada linha na lista contém três itens de dados – um nome, uma categoria e um nome de arquivo de imagem. O layout de cada linha da lista é definido pelo `DataTemplate`, que é referenciado por meio da propriedade associável [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate). O `DataTemplate` define que cada linha de dados na lista será um `NativeCell` que exibe suas propriedades `Name`, `Category` e `ImageFilename` por meio da associação de dados. Para obter mais informações sobre o controle `ListView`, confira [ListView](~/xamarin-forms/user-interface/listview/index.md).

Agora, um renderizador personalizado pode ser adicionado a cada projeto de aplicativo para personalizar o layout específico à plataforma para cada célula.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é o seguinte:

1. Criar uma subclasse da classe `ViewCellRenderer` que renderiza a célula personalizada.
1. Substitua o método específico à plataforma que renderiza a célula personalizada e escreva a lógica para personalizá-la.
1. Adicione um atributo `ExportRenderer` à classe de renderizador personalizado para especificar que ele será usado para renderizar a célula personalizada do Xamarin.Forms. Esse atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> Para a maioria dos elementos do Xamarin.Forms, o fornecimento de um renderizador personalizado em cada projeto de plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [ViewCell](xref:Xamarin.Forms.ViewCell).

O seguinte diagrama ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](viewcell-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado de NativeCell")

A célula personalizada `NativeCell` é renderizada por classes de renderizador específicas da plataforma, que derivam da classe `ViewCellRenderer` de cada plataforma. Isso faz com que cada célula personalizada `NativeCell` seja renderizada com o layout específico da plataforma, conforme mostrado nas capturas de tela seguir:

![](viewcell-images/screenshots.png "NativeCell em cada plataforma")

A classe `ViewCellRenderer` expõe métodos específicos da plataforma para renderização da célula personalizada. Trata-se do método `GetCell` na plataforma iOS, do método `GetCellCore` na plataforma Android e do método `GetTemplate` na UWP.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo aceita dois parâmetros – o nome do tipo da célula do Xamarin.Forms que está sendo renderizada e o nome do tipo de renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a implementação de cada classe de renderizador personalizado específica da plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma iOS:

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

O método `GetCell` é chamado para compilar cada célula a ser exibida. Cada célula é uma instância de `NativeiOSCell`, que define o layout da célula e seus dados. A operação do método `GetCell` depende da estratégia de cache de [`ListView`](xref:Xamarin.Forms.ListView):

- Quando a estratégia de cache de [`ListView`](xref:Xamarin.Forms.ListView) é [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), o método `GetCell` é invocado para cada célula. Uma instância de `NativeiOSCell` é criada para cada instância de `NativeCell` exibida inicialmente na tela. Conforme o usuário rola pelo `ListView`, instâncias de `NativeiOSCell` são reutilizadas. Para obter mais informações sobre a reutilização de células do iOS, confira [Reutilização de células](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Esse código de renderizador personalizado reutilizará algumas células mesmo quando o [`ListView`](xref:Xamarin.Forms.ListView) estiver definido para reter células.

  Os dados exibidos por cada instância de `NativeiOSCell`, quer ela tenha sido criada recentemente ou reutilizada, serão atualizados com os dados de cada instância de `NativeCell` pelo método `UpdateCell`.

  > [!NOTE]
  > O método `OnNativeCellPropertyChanged` nunca será invocado quando a estratégia de cache de [`ListView`](xref:Xamarin.Forms.ListView) estiver definida para reter as células.

- Quando a estratégia de cache de [`ListView`](xref:Xamarin.Forms.ListView) é [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), o método `GetCell` é invocado para cada célula exibida inicialmente na tela. Uma instância de `NativeiOSCell` é criada para cada instância de `NativeCell` exibida inicialmente na tela. Os dados exibidos por cada instância de `NativeiOSCell` serão atualizados com os dados da instância de `NativeCell` pelo método `UpdateCell`. No entanto, o método `GetCell` não será invocado conforme o usuário percorrer o `ListView`. Em vez disso, as instâncias de `NativeiOSCell` serão reutilizadas. Eventos `PropertyChanged` serão gerados na instância de `NativeCell` quando seus dados forem alterados e o manipulador de eventos `OnNativeCellPropertyChanged` atualizará os dados em cada instância de `NativeiOSCell` reutilizada.

O exemplo de código a seguir mostra o método `OnNativeCellPropertyChanged` invocado quando um evento `PropertyChanged` é gerado:

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

Esse método atualiza os dados exibidos pelas instâncias de `NativeiOSCell` reutilizadas. É feita uma verificação da propriedade alterada, pois o método pode ser chamado várias vezes.

A classe `NativeiOSCell` define o layout para cada célula e é mostrada no exemplo de código a seguir:

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

Essa classe define os controles usados para renderizar o conteúdo da célula e seu layout. A classe implementa a interface [`INativeElementView`](xref:Xamarin.Forms.INativeElementView), o que é necessário quando o [`ListView`](xref:Xamarin.Forms.ListView) usa a estratégia de cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement). Essa interface especifica que a classe deve implementar a propriedade [`Element`](xref:Xamarin.Forms.INativeElementView.Element), que deve retornar dados da célula personalizada para células recicladas.

O construtor `NativeiOSCell` inicializa a aparência das propriedades `HeadingLabel`, `SubheadingLabel` e `CellImageView`. Essas propriedades são usadas para exibir os dados armazenados na instância de `NativeCell`, com o método `UpdateCell` sendo chamado para definir o valor de cada propriedade. Além disso, quando o [`ListView`](xref:Xamarin.Forms.ListView) usa a estratégia de cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), os dados exibidos pelas propriedades `HeadingLabel`, `SubheadingLabel` e `CellImageView` podem ser atualizados pelo método `OnNativeCellPropertyChanged` no renderizador personalizado.

O layout da célula é executado pela substituição `LayoutSubviews`, que define as coordenadas de `HeadingLabel`, `SubheadingLabel` e `CellImageView` dentro da célula.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma Android:

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

O método `GetCellCore` é chamado para compilar cada célula a ser exibida. Cada célula é uma instância de `NativeAndroidCell`, que define o layout da célula e seus dados. A operação do método `GetCellCore` depende da estratégia de cache de [`ListView`](xref:Xamarin.Forms.ListView):

- Quando a estratégia de cache de [`ListView`](xref:Xamarin.Forms.ListView) é [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), o método `GetCellCore` é invocado para cada célula. Um `NativeAndroidCell` é criado para cada instância de `NativeCell` exibida inicialmente na tela. Conforme o usuário rola pelo `ListView`, instâncias de `NativeAndroidCell` são reutilizadas. Para obter mais informações sobre a reutilização de células no Android, confira [Reutilização da exibição de linha](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Observe que esse código de renderizador personalizado reutilizará algumas células mesmo quando o [`ListView`](xref:Xamarin.Forms.ListView) estiver definido para reter células.

  Os dados exibidos por cada instância de `NativeAndroidCell`, quer ela tenha sido criada recentemente ou reutilizada, serão atualizados com os dados de cada instância de `NativeCell` pelo método `UpdateCell`.

  > [!NOTE]
  > Observe que, embora o método `OnNativeCellPropertyChanged` seja invocado quando [`ListView`](xref:Xamarin.Forms.ListView) é configurado para reter as células, ele não atualizará o valores da propriedade `NativeAndroidCell`.

- Quando a estratégia de cache de [`ListView`](xref:Xamarin.Forms.ListView) é [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), o método `GetCellCore` é invocado para cada célula exibida inicialmente na tela. Uma instância de `NativeAndroidCell` é criada para cada instância de `NativeCell` exibida inicialmente na tela. Os dados exibidos por cada instância de `NativeAndroidCell` serão atualizados com os dados da instância de `NativeCell` pelo método `UpdateCell`. No entanto, o método `GetCellCore` não será invocado conforme o usuário percorrer o `ListView`. Em vez disso, as instâncias de `NativeAndroidCell` serão reutilizadas.  Eventos `PropertyChanged` serão gerados na instância de `NativeCell` quando seus dados forem alterados e o manipulador de eventos `OnNativeCellPropertyChanged` atualizará os dados em cada instância de `NativeAndroidCell` reutilizada.

O exemplo de código a seguir mostra o método `OnNativeCellPropertyChanged` invocado quando um evento `PropertyChanged` é gerado:

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

Esse método atualiza os dados exibidos pelas instâncias de `NativeAndroidCell` reutilizadas. É feita uma verificação da propriedade alterada, pois o método pode ser chamado várias vezes.

A classe `NativeAndroidCell` define o layout para cada célula e é mostrada no exemplo de código a seguir:

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

Essa classe define os controles usados para renderizar o conteúdo da célula e seu layout. A classe implementa a interface [`INativeElementView`](xref:Xamarin.Forms.INativeElementView), o que é necessário quando o [`ListView`](xref:Xamarin.Forms.ListView) usa a estratégia de cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement). Essa interface especifica que a classe deve implementar a propriedade [`Element`](xref:Xamarin.Forms.INativeElementView.Element), que deve retornar dados da célula personalizada para células recicladas.

O construtor `NativeAndroidCell` infla o layout `NativeAndroidCell` e inicializa as propriedades `HeadingTextView`, `SubheadingTextView` e `ImageView` para os controles no layout inflado. Essas propriedades são usadas para exibir os dados armazenados na instância de `NativeCell`, com o método `UpdateCell` sendo chamado para definir o valor de cada propriedade. Além disso, quando o [`ListView`](xref:Xamarin.Forms.ListView) usa a estratégia de cache [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), os dados exibidos pelas propriedades `HeadingTextView`, `SubheadingTextView` e `ImageView` podem ser atualizados pelo método `OnNativeCellPropertyChanged` no renderizador personalizado.

O exemplo de código a seguir mostra a definição do layout para o arquivo de layout `NativeAndroidCell.axml`:

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

Esse layout especifica que dois controles `TextView` e um controle `ImageView` sejam usados para exibir o conteúdo da célula. Os dois controles `TextView` têm orientação vertical dentro de um controle `LinearLayout`, com todos os controles contidos em um `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O seguinte exemplo de código mostra o renderizador personalizado para o UWP:

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

O método `GetTemplate` é chamado para retornar a célula a ser renderizada para cada linha de dados na lista. Ele cria um `DataTemplate` para cada instância de `NativeCell` que será exibida na tela, com o `DataTemplate` definindo a aparência e o conteúdo da célula.

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

O `DataTemplate` especifica os controles usados para exibir o conteúdo da célula, bem como seu layout e aparência. Dois controles `TextBlock` e um controle `Image` são usados para exibir o conteúdo da célula por meio da associação de dados. Além disso, uma instância do `ConcatImageExtensionConverter` é usada para concatenar a extensão de arquivo `.jpg` a cada nome de arquivo de imagem. Isso garante que o controle `Image` possa carregar e renderizar a imagem quando sua propriedade `Source` estiver definida.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um [`ViewCell`](xref:Xamarin.Forms.ViewCell) hospedado dentro de um controle [`ListView`](xref:Xamarin.Forms.ListView) do Xamarin.Forms. Isso impede que os cálculos de layout do Xamarin.Forms sejam chamados repetidamente durante a rolagem de `ListView`.


## <a name="related-links"></a>Links relacionados

- [Desempenho de ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (amostra)](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/ViewCell/)
