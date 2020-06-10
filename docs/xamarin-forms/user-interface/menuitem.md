---
Título: " Xamarin.Forms MENUITEM" Descrição: "a classe MenuItem é usada para criar itens de menu para menus como menus de contexto de item ListView e menus flutuantes de aplicativo Shell".
MS. Prod: xamarin MS. AssetID: 62655C21-6053-466D-A7F4-DE2BE36538F5 MS. Technology: xamarin-Forms autor: profexorgeek MS. Author: jusjohns MS. Date: 08/01/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-menuitem"></a>Xamarin.FormsMenuItem

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

A Xamarin.Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe define itens de menu para menus como menus de `ListView` contexto de item e menus de submenu de aplicativo Shell.

As capturas de tela a seguir mostram `MenuItem` objetos em um `ListView` menu de contexto no Ios e no Android:

[!["MenuItems no iOS e no Android"](menuitem-images/menuitem-demo-cropped.png "MenuItems no iOS e no Android")](menuitem-images/menuitem-demo-full.png#lightbox "MenuItems em imagem completa do iOS e Android")

A `MenuItem` classe define as seguintes propriedades:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)é um `ICommand` que permite a vinculação de ações do usuário, como toques ou cliques de dedos, a comandos definidos em um ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)é um `object` que especifica o parâmetro que deve ser passado para o `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)é um `ImageSource` valor que define o ícone de exibição.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)é um `bool` valor que indica se o `MenuItem` Remove seu elemento de interface do usuário associado da lista.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)é um `bool` valor que indica se este objeto responde à entrada do usuário.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)é um `string` valor que especifica o texto de exibição.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos para que a `MenuItem` instância possa ser o destino de associações de dados.

## <a name="create-a-menuitem"></a>Criar um MenuItem

`MenuItem`os objetos podem ser usados em um menu de contexto nos itens de um `ListView` objeto. O padrão mais comum é criar `MenuItem` objetos dentro de uma `ViewCell` instância, que é usada como o `DataTemplate` objeto para os `ListView` s `ItemTemplate` . Quando o `ListView` objeto for preenchido, ele criará cada item usando o `DataTemplate` , expondo as `MenuItem` opções quando o menu de contexto for ativado para um item.

O exemplo a seguir mostra `MenuItem` a instanciação dentro do contexto de um `ListView` objeto:

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Um `MenuItem` também pode ser criado no código:

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>Definir comportamento MenuItem com eventos

A `MenuItem` classe expõe um `Clicked` evento. Um manipulador de eventos pode ser anexado a esse evento para reagir a toques ou cliques na `MenuItem` instância do XAML:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

Um manipulador de eventos também pode ser anexado no código:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

Os exemplos anteriores referenciaram um `OnItemClicked` manipulador de eventos. O código a seguir mostra uma implementação de exemplo:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>Definir comportamento MenuItem com MVVM

A `MenuItem` classe oferece suporte ao padrão Model-View-ViewModel (MVVM) por meio [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) de objetos e da `ICommand` interface. O XAML a seguir mostra as `MenuItem` instâncias associadas a comandos definidos em um ViewModel:

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

No exemplo anterior, dois `MenuItem` objetos são definidos com suas `Command` Propriedades e `CommandParameter` associadas a comandos no ViewModel. O ViewModel contém os comandos referenciados no XAML:

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

O aplicativo de exemplo inclui uma `DataService` classe usada para obter uma lista de itens para preencher os `ListView` objetos. Um ViewModel é instanciado, com itens da `DataService` classe e definido como o `BindingContext` no code-behind:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Ícones MenuItem

> [!WARNING]
> `MenuItem`os objetos exibem apenas os ícones no Android. Em outras plataformas, somente o texto especificado pela `Text` propriedade será exibido.

 Os ícones são especificados usando a `IconImageSource` propriedade. Se um ícone for especificado, o texto especificado pela `Text` propriedade não será exibido. A captura de tela a seguir mostra um `MenuItem` com um ícone no Android:

!["Captura de tela do ícone MenuItem no Android"](menuitem-images/menuitem-android-icon.png "Captura de tela do ícone MenuItem no Android")

Para obter mais informações sobre como usar imagens no Xamarin.Forms , consulte [imagens em Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>Habilitar ou desabilitar um MenuItem em tempo de execução

Para habilitar a desabilitação de um `MenuItem` em tempo de execução, associe sua `Command` Propriedade a uma `ICommand` implementação e verifique se um `canExecute` delegado habilita e desabilita o `ICommand` conforme apropriado.

> [!IMPORTANT]
> Não associe a `IsEnabled` Propriedade a outra propriedade ao usar a `Command` propriedade para habilitar ou desabilitar o `MenuItem` .

O exemplo a seguir mostra uma `MenuItem` cuja `Command` propriedade está associada a um `ICommand` nome `MyCommand` :

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

A `ICommand` implementação requer um `canExecute` delegado que retorne o valor de uma `bool` propriedade para habilitar e desabilitar `MenuItem` :

```csharp
public class MyViewModel : INotifyPropertyChanged
{
    bool isMenuItemEnabled = false;
    public bool IsMenuItemEnabled
    {
        get { return isMenuItemEnabled; }
        set
        {
            isMenuItemEnabled = value;
            MyCommand.ChangeCanExecute();
        }
    }

    public Command MyCommand { get; private set; }

    public ToolbarItemViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsToolbarItemEnabled);
    }
}
```

Neste exemplo, o `MenuItem` será desabilitado até que a `IsMenuItemEnabled` propriedade seja definida. Quando isso ocorre, o `Command.ChangeCanExecute` método é chamado, o que faz com que o `canExecute` delegado `MyCommand` seja reavaliado.

## <a name="cross-platform-context-menu-behavior"></a>Comportamento do menu de contexto de plataforma cruzada

Os menus de contexto são acessados e exibidos de forma diferente em cada plataforma.

No Android, o menu de contexto é ativado por uma prensa de longa duração em um item de lista. O menu de contexto substitui o título e a área da barra de navegação e `MenuItem` as opções são exibidas como botões horizontais.

!["Captura de tela do menu de contexto no Android"](menuitem-images/menuitem-android-icon.png "Captura de tela do menu de contexto no Android")

No iOS, o menu de contexto é ativado passando o dedo em um item de lista. O menu de contexto é exibido no item de lista e `MenuItems` são exibidos como botões horizontais.

!["Captura de tela do menu de contexto no iOS"](menuitem-images/menuitem-ios-contextmenu.png "Captura de tela do menu de contexto no iOS")

No UWP, o menu de contexto é ativado clicando com o botão direito do mouse em um item de lista. O menu de contexto é exibido próximo ao cursor como uma lista vertical.

!["Captura de tela do menu de contexto em UWP"](menuitem-images/menuitem-uwp.png "Captura de tela do menu de contexto no UWP")

## <a name="related-links"></a>Links relacionados

* [Demonstrações MenuItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Imagens emXamarin.Forms](~/xamarin-forms/user-interface/images.md)
