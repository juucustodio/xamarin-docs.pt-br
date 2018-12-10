---
title: Shell do Xamarin.Forms
description: Este artigo explica como usar o Shell do Xamarin.Forms, o que reduz a complexidade dos aplicativos Xamarin.Forms fornecendo os recursos fundamentais de interface do usuário que a maioria dos aplicativos exige.
ms.prod: xamarin
ms.assetid: 1A674212-72DB-4AA4-B626-A4EC135AD1A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2018
ms.openlocfilehash: 933368f7ca1435ece4f20945b2f8e905f7584217
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899379"
---
# <a name="xamarinforms-shell"></a>Shell do Xamarin.Forms

![Visualizar](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/Microsoft/TailwindTraders-Mobile)

O Shell do Xamarin.Forms é um contêiner para aplicativos, que fornece os recursos fundamentais da interface do usuário que a maioria dos aplicativos exige, permitindo que você se concentre na carga de trabalho principal do aplicativo. Os aplicativos do Shell são fornecidos com a seguinte funcionalidade:

- Um único lugar para descrever a estrutura visual de um aplicativo.
- Uma interface do usuário de navegação comum.
- Um serviço de navegação com vinculação profunda.
- Um manipulador de pesquisa integrado.

Essa funcionalidade reduz a complexidade dos aplicativos e aumenta a produtividade do desenvolvedor. Além disso, o Shell é gravado considerando a renderização do consumo de memória e de velocidade.

> [!IMPORTANT]
> Os aplicativos iOS e Android existentes podem adotar o Shell e beneficiar-se imediatamente das melhorias de navegação, desempenho e extensibilidade.

O Shell fornece uma interface do usuário de navegação "pretensiosa", com base em guias e submenus. O nível superior da navegação em um aplicativo do Shell é um submenu:

![Submenu](shell-images/flyout.png "Submenu")

O próximo nível de navegação é a barra de guias inferior:

![Guias inferiores](shell-images/bottom-tabs-full.png "Guias inferiores")

Quando o submenu não estiver aberto, a barra de guias inferior será considerada o nível superior do painel de navegação

Em cada guia inferior, o próximo nível de navegação é a barra de guias superior, em que cada guia é uma `ContentPage`:

![Guias superiores](shell-images/top-tabs-full.png "Guias superiores")

Dentro de cada `ContentPage`, instâncias de `ContentPage` adicionais podem ser adicionadas e removidas da pilha de navegação.

## <a name="bootstrapping-a-shell-application"></a>Inicializando um aplicativo do Shell

Um aplicativo do Shell é inicializado definindo a propriedade `MainPage` da classe `App` para uma nova instância de `Shell`:

```csharp
namespace TailwindTraders.Mobile
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();

            Forms.SetFlags("Shell_Experimental");
            MainPage = new Shell();
        }
    }
}
```

A classe `Shell` é um arquivo XAML que descreve a estrutura visual do aplicativo.

> [!IMPORTANT]
> O Shell é experimental no momento e só pode ser usado com a adição de `Forms.SetFlags("Shell_Experimental");` na classe `App`, antes da criação da instância de `Shell`, ou ao projeto de plataforma, antes da invocação do método `Forms.Init`.

## <a name="shell-file-hierarchy"></a>Hierarquia de arquivos do Shell

Um arquivo do Shell consiste em três elementos hierárquicos:

- `ShellItem`. Um ou mais itens no submenu. Cada `ShellItem` é um filho de um `Shell`.
- `ShellSection`. Conteúdo agrupado, navegável pelas guias inferiores. Cada `ShellSection` é um filho de um `ShellItem`.
- `ShellContent`. As instâncias de `ContentPage` no aplicativo, que são navegáveis pelas guias superiores. Cada `ShellContent` é um filho de uma `ShellSection`.

Nenhum desses elementos representa uma interface do usuário, mas sim a organização da estrutura visual do aplicativo. O Shell usará esses elementos e produzirá a interface do usuário de navegação do conteúdo.

O XAML a seguir mostra um exemplo simples de um arquivo `Shell`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders"
       x:Class=" TailwindTraders.Shell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
</Shell>
```

> [!NOTE]
> Cada `ShellItem` também pode definir uma propriedade `FlyoutIcon` para um `ImageSource`, que será exibido à esquerda do título.

Esse XAML exibe a `HomePage`, pois esse é o primeiro item de conteúdo declarado no arquivo do Shell:

![Home page](shell-images/homepage.png "Home page")

Quando o botão de hambúrguer é pressionado, o menu suspenso é exibido:

![Submenu aberto](shell-images/flyout-initial.png "Submenu aberto")

O número de itens no submenu pode ser aumentado com a adição de mais instâncias de `ShellItem` ao arquivo do Shell. No entanto, observe que `HomePage` é criado durante a inicialização do aplicativo, e a adição de mais instâncias de `ShellItem` usando essa abordagem fará com que essas páginas também sejam criadas durante a inicialização do aplicativo. Isso pode ser evitado definindo a propriedade `ShellContent.ContentTemplate` para um `DataTemplate`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders"
       x:Class=" TailwindTraders.Shell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
    <ShellContent Title="Profile"
                  ContentTemplate="{DataTemplate local:ProfilePage}" />
</Shell>
```

Com essa abordagem, a `ProfilePage` é criada somente quando o usuário navega para ela e não na inicialização do aplicativo. Além disso, observe que para a `ProfilePage`, os objetos `ShellItem` e `ShellSection` são omitidos, e a propriedade `Title` é definida na instância de `ShellContent`. Essa abordagem concisa requer menos marcação com o Shell fornecendo os wrappers lógicos necessários (sem introduzir exibições adicionais à árvore visual).

Além disso, a aparência de cada `ShellItem` pode ser personalizada definindo a propriedade `Shell.ItemTemplate` para um `DataTemplate`:

```xaml
<Shell.ItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Title}" />
        </ContentView>
    </DataTemplate>
</Shell.ItemTemplate>          
```

Esse código simplesmente reposiciona o texto para cada `ShellItem` dentro do submenu. Observe que o Shell fornece as propriedades `Title` (e `Icon`) para o `BindingContext` do `DataTemplate`.

## <a name="flyout"></a>Submenu

O submenu é o menu raiz do aplicativo e consiste em um cabeçalho de submenu e em itens de menu:

![Submenu anotado](shell-images/flyout-annotated.png "Submenu anotado")

### <a name="flyout-behavior"></a>Comportamento do submenu

O submenu por ser acessado pelo botão de hambúrguer ou passando o dedo na lateral tela. No entanto, esse comportamento pode ser alterado definindo a propriedade `Shell.FlyoutBehavior` para um dos membros da enumeração `FlyoutBehavior`:

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

A definição da propriedade `FlyoutBehavior` para `Disabled` oculta o submenu, o que é útil quando há apenas um `ShellItem`. Outros valores válidos de `FlyoutBehavior` são `Flyout` (padrão) e `Locked`.

### <a name="flyout-header"></a>Cabeçalho do submenu

O cabeçalho do submenu é o conteúdo que é exibido, opcionalmente, na parte superior do submenu, com sua aparência definida por uma `View` que pode ser configurada por meio do valor da propriedade `Shell.FlyoutHeader`:

```xaml
<Shell.FlyoutHeader>
    <local:FlyoutHeader />
</Shell.FlyoutHeader>
```

Como alternativa, a aparência do cabeçalho do submenu pode ser definida configurando a propriedade `Shell.FlyoutHeaderTemplate` para um `DataTemplate`:

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <StackLayout HorizontalOptions="Fill"
                     VerticalOptions="Fill"
                     BackgroundColor="White"
                     Padding="16">
            <Label FontSize="Medium"
                   Text="Smart Shopping">
                <Label.Margin>
                    <Thickness Left="8" />
                </Label.Margin>
            </Label>
            <Button Image="photo"
                    Text="By taking a photo">
                <Button.Margin>
                    <Thickness Top="16" />
                </Button.Margin>
            </Button>
            <Button Image="ia"
                    Text="By using AR">
                <Button.Margin>
                    <Thickness Top="8" />
                </Button.Margin>
            </Button>
        </StackLayout>
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

Esse XAML resulta no cabeçalho de submenu a seguir:

![Cabeçalho do submenu](shell-images/flyout-header.png "Cabeçalho do submenu")

Por padrão, o cabeçalho do submenu ficará fixo no submenu enquanto o conteúdo abaixo poderá ser rolado se houver itens suficientes. No entanto, esse comportamento pode ser alterado definindo a propriedade `Shell.FlyoutHeaderBehavior` para um dos membros da enumeração `FlyoutHeaderBehavior`:

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

A definição do `FlyoutHeaderBehavior` para `CollapseOnScroll` recolhe o submenu conforme a rolagem ocorre. Os outros valores válidos de `FlyoutHeaderBehavior` são `Default`, `Fixed` e `Scroll` (rolar com os itens de menu).

## <a name="menu-items"></a>Itens de menu

O número de itens no submenu pode ser aumentado adicionando mais instâncias de `ShellItem`. No entanto, também é possível adicionar instâncias de `MenuItem` no menu suspenso. Isso permite cenários como a navegação até uma página idêntica passando dados por meio da propriedade `MenuItem.CommandParameter`.

As instâncias de `MenuItem` devem ser adicionadas à coleção `Shell.MenuItems`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Views"
       x:Class="TailwindTraders.Shell"
       FlyoutHeaderBehavior="Fixed"
       Title="Tailwind Traders"
       ...>
    <Shell.MenuItems>
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="1"
                  Text="Holiday decorations" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="2"
                  Text="Appliances" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="3"
                  Text="Bathrooms" />
        ...
    </Shell.MenuItems>
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>    
</Shell>
```

> [!NOTE]
> Cada `MenuItem` também pode definir uma propriedade `Icon` para uma `FileImageSource`, que será exibida à esquerda do texto.

Esse XAML resulta no submenu a seguir:

![Submenu completo](shell-images/flyout-full.png "Submenu completo")

Além disso, a aparência do `MenuItem` pode ser personalizada definindo a propriedade `Shell.MenuItemTemplate` para um `DataTemplate`:

```xaml
<Shell.MenuItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Text}"
                   FontAttributes="Bold" />
        </ContentView>
    </DataTemplate>
</Shell.MenuItemTemplate>
```

Isso resulta em instâncias de `MenuItem` com o texto renderizado em negrito:

![Itens de menu em negrito](shell-images/menuitems-bold.png "Itens de menu em negrito")

## <a name="tabs"></a>Tabulações

As instâncias de `ShellSection` serão renderizadas como guias inferiores, desde que haja várias instâncias de `ShellSection` em um único `ShellItem`:

```xaml
<ShellItem Title="Bottom Tab Sample"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="AR" Icon="ia.png">
        <ShellContent ContentTemplate="{DataTemplate local:ARPage}"/>
    </ShellSection>
    <ShellSection Title="Photo" Icon="photo.png">
        <ShellContent ContentTemplate="{DataTemplate local:PhotoPage}"/>
    </ShellSection>
</ShellItem>
```

Neste exemplo, as instâncias de `ShellSection` serão renderizadas como guias inferiores:

![Guias inferiores](shell-images/tabs-bottom.png "Guias inferiores")

Os itens `ShellContent` serão renderizados como guias superiores, desde que haja várias instâncias de `ShellContent` em uma única `ShellSection`:

```xaml
<ShellItem Title="Store Home"
           Shell.TitleView="Store Home"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="Browse Product">
        <ShellContent Title="Featured"
                      ContentTemplate="{DataTemplate local:FeaturedPage}" />
        <ShellContent Title="On Sale"
                      ContentTemplate="{DataTemplate local:SalePage}" />
    </ShellSection>
</ShellItem>
```

Neste exemplo, as instâncias de `ShellContent` serão renderizadas como guias superiores:

![Guias superiores](shell-images/tabs-top.png "Guias superiores")

As guias podem ser estilizadas usando estilos XAML ou fornecendo um renderizador personalizado. Por exemplo, o exemplo a seguir mostra um estilo XAML que define a cor da guia:

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.ShellTabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.ShellTabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.ShellTabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

## <a name="navigation"></a>Navegação

O Shell inclui uma experiência de navegação baseada em URI. Os URIs fornecem uma experiência de navegação aprimorada que permite a navegação para qualquer página no aplicativo, sem precisar seguir uma hierarquia de navegação definida. Além disso, eles também oferecem a capacidade de navegar para trás, sem precisar visitar todas as páginas na pilha de navegação.

Essa navegação baseada em URI é realizada com rotas, que são segmentos de URI usados para navegar dentro do aplicativo. O arquivo `Shell` deve declarar um esquema de rota, um host de rota e uma rota:

```xaml
<Shell ...
       Route="tailwindtraders"
       RouteHost="www.microsoft.com"
       RouteScheme="app">
    ...
</Shell>
```

Combinados, os valores de propriedade `RouteScheme`, `RouteHost` e `Route` formam o URI raiz de `app://www.microsoft.com/tailwindtraders`.

Cada elemento na classe `Shell` também pode definir uma propriedade de rota que pode ser usada na navegação programática.

No construtor `Shell` ou em qualquer outro local que seja executado antes da invocação de uma rota, é possível registrar explicitamente rotas adicionais para todas as páginas que não sejam representadas por um elemento do Shell (como instâncias de `MenuItem`):

```csharp
Routing.RegisterRoute("productcategory", typeof(ProductCategoryPage));
```

### <a name="implementing-navigation"></a>Implementando a navegação

Os itens de menu expõem uma propriedade `Command` que pode ser usada para implementar a navegação:

```csharp
public ICommand ProductTypeCommand { get; } = new Command<string>(NavigateToProductType);

static void NavigateToProductType(string typeId)
{
  (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"app:///tailwindtraders/productcategory?id={typeId}", true);
}
```

Para invocar a navegação, é necessário obter uma referência ao aplicativo `Shell` por meio da propriedade `MainPage` da classe `Application`. Em seguida, a navegação poderá ser invocada ao chamar o método `GoToAsync` passando um URI válido como um argumento. O método `GoToAsync` navega usando um objeto `ShellNavigationState`, que será construído usando uma `string` ou um `Uri`.

### <a name="passing-data"></a>Passando dados

Os dados podem ser passados entre as páginas por meio de parâmetros de cadeia de consulta. O Shell definirá os valores dos parâmetros de cadeia de consulta na `ContentPage` ou no ViewModel quando você adicionar atributos de propriedade de consulta à classe:

```csharp
[QueryProperty("TypeID", "id")]
public partial class ProductCategoryPage : ContentPage
{
    private string _typeId;

    public ProductCategoryPage()
    {
        InitializeComponent();

        BindingContext = new ProductCategoryViewModel();
    }

    public string TypeID
    {
        get => _typeId;
        set => MyLabel.Text = value;
    }
}
```

O atributo `QueryProperty` especifica que o `TypeID` receberá os dados passados no parâmetro de cadeia de consulta `id` do URI na chamada de método `GoToAsync`.

### <a name="intercepting-navigation"></a>Interceptando a navegação

O Shell fornece a capacidade de interceptar o roteamento de navegação antes que ele seja concluído e após sua conclusão. Isso pode ser feito registrando manipuladores de eventos para os eventos `Navigating` e `Navigated`, respectivamente:

```xaml
<Shell ...
       Navigating="OnShellNavigating">
    ...
</Shell>
```

```csharp
void OnShellNavigating(object sender, ShellNavigatingEventArgs e)
{
  if (...)
  {
    e.Cancel(); // Cancel the navigation
  }
}
```

A classe `ShellNavigatingEventArgs` fornece as seguintes propriedades:

| Propriedade | Tipo | Descrição |
|---|---|---|
| Atual | `ShellNavigationState` | O URI da página atual. |
| Origem | `ShellNavigationState` | O URI que representa onde a navegação foi originada. |
| Destino | `ShellNavigationSource`  | O URI que representa para onde a navegação se destina. |
| CanCancel  | `bool` | Um valor que indica se é possível cancelar a navegação. |
| Cancelada  | `bool` | Um valor que indica se a navegação foi cancelada. |

Além disso, a classe `ShellNavigatingEventArgs` fornece um método `Cancel`.

A classe `ShellNavigatedEventArgs` fornece as seguintes propriedades:

| Propriedade | Tipo | Descrição |
|---|---|---|
| Atual | `ShellNavigationState` | O URI da página atual. |
| Voltar| `ShellNavigationState` | O URI da página anterior. |
| Origem  | `ShellNavigationSource` | O URI que representa onde a navegação foi originada. |

Além disso, o Shell fornece um método `OnBackButtonPressed` substituível que pode ser usado para interceptar um pressionamento do botão de retorno.

## <a name="related-links"></a>Links relacionados

- [Tailwind Traders (exemplo)](https://github.com/Microsoft/TailwindTraders-Mobile)
