---
title: Xamarin.Forms Aprofundamento do guia de início rápido
description: Este artigo examina os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms shell. Os tópicos abordados incluíam a anatomia de um Xamarin.Forms aplicativo de Shell, conceitos básicos de arquitetura e aplicativo e a interface do usuário.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: F65C83B7-BC9F-425F-8662-931B652A2946
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/25/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2c110093d0fea6e5d97d8e58474d84edb008b1ee
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818235"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.Forms Aprofundamento do guia de início rápido

No [ Xamarin.Forms início rápido](~/get-started/index.yml), o aplicativo Notes foi criado. Este artigo revisa o que foi criado para compreender os conceitos básicos de como Xamarin.Forms funcionam os aplicativos do Shell.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introdução ao Visual Studio

O Visual Studio organiza o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste, entre outros. O aplicativo Notes consiste em uma solução contendo três projetos, conforme mostrado na captura de tela a seguir:

![Gerenciador de Soluções do Visual Studio](deepdive-images/vs/solution.png)

Os projetos são:

- Notes – esse projeto é o projeto da biblioteca do .NET Standard que contém todos os códigos compartilhados e interfaces do usuário compartilhadas.
- Notes.Android – esse projeto contém o código específico do Android e é o ponto de entrada para o aplicativo Android.
- Notes.iOS – esse projeto contém o código específico do iOS e é o ponto de entrada para o aplicativo iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um Xamarin.Forms aplicativo

A captura de tela a seguir mostra o conteúdo do projeto de biblioteca do .NET Standard Notes no Visual Studio:

![Conteúdo do projeto do .NET Standard Phoneword](deepdive-images/vs/net-standard-project.png)

O projeto tem um nó de **dependências** que contém os nós **NuGet** e **SDK** :

- **NuGet** &ndash; os Xamarin.Forms Xamarin.Essentials pacotes NuGet,, Newtonsoft.Json e SQLite-net-PCL que foram adicionados ao projeto.
- **SDK** &ndash; o metapacote `NETStandard.Library` que faz referência ao conjunto completo de pacotes NuGet que definem o .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

O [Visual Studio para Mac](/visualstudio/mac/) segue a prática do Visual Studio de organizar o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste, entre outros. O aplicativo Notes consiste em uma solução contendo três projetos, conforme mostrado na captura de tela a seguir:

![Painel de soluções do Visual Studio para Mac](deepdive-images/vsmac/solution.png)

Os projetos são:

- Notes – esse projeto é o projeto da biblioteca do .NET Standard que contém todos os códigos compartilhados e interfaces do usuário compartilhadas.
- Notes.Android – esse projeto contém o código específico do Android e é o ponto de entrada para aplicativos Android.
- Notes.iOS – esse projeto contém o código específico do iOS e é o ponto de entrada para aplicativos iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um Xamarin.Forms aplicativo

A captura de tela a seguir mostra o conteúdo do projeto de biblioteca do .NET Standard Notes no Visual Studio para Mac:

![Conteúdo do projeto de biblioteca do .NET Standard Phoneword](deepdive-images/vsmac/net-standard-project.png)

O projeto tem um nó de **dependências** que contém os nós **NuGet** e **SDK** :

- **NuGet** &ndash; os Xamarin.Forms Xamarin.Essentials pacotes NuGet,, Newtonsoft.Json e SQLite-net-PCL que foram adicionados ao projeto.
- **SDK** &ndash; o metapacote `NETStandard.Library` que faz referência ao conjunto completo de pacotes NuGet que definem o .NET Standard.

::: zone-end

O projeto também consiste em vários arquivos:

- **Data\NoteDatabase.cs** – essa classe contém código para criar o banco de dados e ler, gravar e excluir dados do banco de dados.
- **Modelos\Note.cs** – essa classe define um modelo de `Note` cujas instâncias armazenam dados sobre cada observação no aplicativo.
- **Views\AboutPage.XAML** – a marcação XAML para a `AboutPage` classe, que define a interface do usuário para a página About.
- **Views\AboutPage.XAML.cs** – o code-behind da `AboutPage` classe, que contém a lógica de negócios que é executada quando o usuário interage com a página.
- **Views\NotesPage.XAML** – a marcação XAML para a `NotesPage` classe, que define a interface do usuário para a página mostrada quando o aplicativo é iniciado.
- **Views\NotesPage.XAML.cs** – o code-behind da `NotesPage` classe, que contém a lógica de negócios que é executada quando o usuário interage com a página.
- **Views\NoteEntryPage.XAML** – a marcação XAML da `NoteEntryPage` classe, que define a interface do usuário para a página mostrada quando o usuário insere uma nota.
- **Views\NoteEntryPage.XAML.cs** – o code-behind da `NoteEntryPage` classe, que contém a lógica de negócios que é executada quando o usuário interage com a página.
- **XAML** – a marcação XAML para a classe `App`, que define um dicionário de recursos para o aplicativo.
- **App.XAML.cs** – o code-behind para a `App` classe, que é responsável por instanciar o aplicativo de shell e para manipular eventos de ciclo de vida do aplicativo.
- **AppShell. XAML** – a marcação XAML para a `AppShell` classe, que define a hierarquia visual do aplicativo.
- **AppShell.XAML.cs** – o code-behind da `AppShell` classe, que cria uma rota para o para `NoteEntryPage` que possa ser navegada por meio de programação.
- **AssemblyInfo.cs** – esse arquivo contém um atributo de aplicativo sobre o projeto, que é aplicado no nível do assembly.

Para saber mais sobre a anatomia de um aplicativo Xamarin.iOS, consulte [Anatomia de um aplicativo Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Para saber mais sobre a anatomia de um aplicativo Xamarin.Android, consulte [Anatomia de um aplicativo Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Arquitetura e conceitos básicos de aplicativo

Um Xamarin.Forms aplicativo é arquitetado da mesma forma que um aplicativo tradicional entre plataformas. O código compartilhado normalmente é colocado em uma biblioteca do .NET Standard e aplicativos específicos de plataforma consomem o código compartilhado. O diagrama a seguir mostra uma visão geral dessa relação para o aplicativo Notes:

![Arquitetura do Notes](deepdive-images/architecture.png)

Para maximizar a reutilização do código de inicialização, Xamarin.Forms os aplicativos têm uma única classe denominada `App` responsável por instanciar o aplicativo em cada plataforma, conforme mostrado no exemplo de código a seguir:

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        // ...
    }
}
```

Esse código define a `MainPage` propriedade da `App` classe para o `AppShell` objeto. A `AppShell` classe define a hierarquia visual do aplicativo. O Shell usa essa hierarquia visual e produz a interface do usuário para ela. Para obter mais informações sobre como definir a hierarquia visual do aplicativo, consulte [hierarquia visual do aplicativo](#application-visual-hierarchy).

Além disso, o arquivo **AssemblyInfo.cs** contém um único atributo de aplicativo, que é aplicado no nível do assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

O [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo ativa o compilador XAML, para que o XAML seja compilado diretamente na linguagem intermediária. Para saber mais, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilação de XAML).

## <a name="launch-the-application-on-each-platform"></a>Iniciar o aplicativo em cada plataforma

A forma como o aplicativo é iniciado em cada plataforma é específica para a plataforma.

### <a name="ios"></a>iOS

Para iniciar a Xamarin.Forms página inicial no Ios, o projeto Notes. Ios define a `AppDelegate` classe que herda da `FormsApplicationDelegate` classe:

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

A `FinishedLaunching` substituição Inicializa a Xamarin.Forms estrutura chamando o `Init` método. Isso faz com que a implementação específica do iOS do seja Xamarin.Forms carregada no aplicativo antes que o controlador de exibição raiz seja definido pela chamada ao `LoadApplication` método.

### <a name="android"></a>Android

Para iniciar a Xamarin.Forms página inicial no Android, o projeto Notes. Android inclui um código que cria um `Activity` com o `MainLauncher` atributo, com a atividade herdada da `FormsAppCompatActivity` classe:

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

A `OnCreate` substituição Inicializa a Xamarin.Forms estrutura chamando o `Init` método. Isso faz com que a implementação específica do Android do seja Xamarin.Forms carregada no aplicativo antes que o Xamarin.Forms aplicativo seja carregado.

## <a name="application-visual-hierarchy"></a>Hierarquia visual do aplicativo

Xamarin.Forms Os aplicativos de shell definem a hierarquia visual do aplicativo em uma classe que subclasse a `Shell` classe. No aplicativo Notes, esta é a `Appshell` classe:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Notes.Views"
       x:Class="Notes.AppShell">
    <TabBar>
        <ShellContent Title="Notes"
                      Icon="icon_feed.png"
                      ContentTemplate="{DataTemplate views:NotesPage}" />
        <ShellContent Title="About"
                      Icon="icon_about.png"
                      ContentTemplate="{DataTemplate views:AboutPage}" />
    </TabBar>
</Shell>
```

Esse XAML consiste em dois objetos principais:

- `TabBar`. O `TabBar` representa a barra de guia inferior e deve ser usado quando o padrão de navegação para o aplicativo usa guias inferiores. O objeto `TabBar` é um filho do objeto `Shell`.
- `ShellContent`, que representa os `ContentPage` objetos para cada guia no `TabBar` . Cada `ShellContent` objeto é um filho do `TabBar` objeto.

Esses objetos não representam nenhuma interface do usuário, mas sim a organização da hierarquia visual do aplicativo. O Shell usará esses objetos e produzirá a interface de navegação do conteúdo para o usuário. Portanto, a `AppShell` classe define define duas páginas navegáveis a partir das guias inferiores. As páginas são criadas sob demanda, em resposta à navegação do usuário.

Para obter mais informações sobre aplicativos de Shell, consulte [ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="user-interface"></a>Interface do usuário

Há vários grupos de controle usados para criar a interface do usuário de um Xamarin.Forms aplicativo:

1. **Páginas** – as Xamarin.Forms páginas representam telas de aplicativos móveis de plataforma cruzada. O aplicativo Notes usa a [`ContentPage`](xref:Xamarin.Forms.ContentPage) classe para exibir telas únicas. Para obter mais informações sobre páginas, consulte [ Xamarin.Forms páginas](~/xamarin-forms/user-interface/controls/pages.md).
1. **Exibições** – Xamarin.Forms exibições são os controles exibidos na interface do usuário, como rótulos, botões e caixas de entrada de texto. O aplicativo notas concluídas usa [`CollectionView`](xref:Xamarin.Forms.CollectionView) as [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) exibições, e. Para obter mais informações sobre exibições, consulte [ Xamarin.Forms exibições](~/xamarin-forms/user-interface/controls/views.md).
1. **Layouts** – os Xamarin.Forms layouts são contêineres usados para compor exibições em estruturas lógicas. O aplicativo Notes usa a [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe para organizar as exibições em uma pilha vertical e a [`Grid`](xref:Xamarin.Forms.Grid) classe para organizar os botões horizontalmente. Para obter mais informações sobre layouts, consulte [ Xamarin.Forms layouts](~/xamarin-forms/user-interface/controls/layouts.md).

No runtime, cada controle será mapeado para seu equivalente nativo, que é o que será renderizado.

### <a name="layout"></a>Layout

O aplicativo Notes usa o [`StackLayout`](xref:Xamarin.Forms.StackLayout) para simplificar o desenvolvimento de aplicativos de plataforma cruzada, organizando automaticamente as exibições na tela, independentemente do tamanho da tela. Os elementos filho são posicionados um após o outro, horizontalmente ou verticalmente, na ordem em que foram adicionados. A quantidade de espaço que o `StackLayout` usará depende de como as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriedades e são definidas, mas por padrão, o `StackLayout` tentará usar a tela inteira.

O código XAML a seguir mostra um exemplo de como usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) para layout `NoteEntryPage` :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.Views.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

Por padrão, o [`StackLayout`](xref:Xamarin.Forms.StackLayout) assume uma orientação vertical. No entanto, ele pode ser alterado para uma orientação horizontal definindo a [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriedade como o [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) membro de enumeração.

> [!NOTE]
> O tamanho das exibições pode ser definido por meio das propriedades `HeightRequest` e `WidthRequest`.

Para obter mais informações sobre a [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe, consulte [ Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md).

### <a name="responding-to-user-interaction"></a>Respondendo à interação do usuário

Um objeto definido em XAML pode acionar um evento que é processado no arquivo code-behind. O exemplo de código a seguir mostra o `OnSaveButtonClicked` método no code-behind para a `NoteEntryPage` classe, que é executada em resposta ao [`Clicked`](xref:Xamarin.Forms.Button.Clicked) acionamento do evento no botão *salvar* .

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    if (!string.IsNullOrWhiteSpace(note.Text))
    {
        await App.Database.SaveNoteAsync(note);
    }
    await Shell.Current.GoToAsync("..");
}
```

O método `OnSaveButtonClicked` salva a observação no banco de dados e navega de volta para a página anterior. Para obter mais informações sobre navegação, consulte [Navigation](#navigation).

> [!NOTE]
> O arquivo code-behind de uma classe XAML pode acessar um objeto definido em XAML usando o nome atribuído a ele com o atributo `x:Name`. O valor atribuído a esse atributo tem as mesmas regras que variáveis C#, no sentido de que deve começar com uma letra ou um sublinhado e não conter espaços.

A conexão do botão Salvar para o método `OnSaveButtonClicked` ocorre na marcação XAML para a classe `NoteEntryPage`:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listas

O [`CollectionView`](xref:Xamarin.Forms.CollectionView) é responsável por exibir uma coleção de itens em uma lista. Por padrão, os itens de lista são exibidos verticalmente e cada item é exibido em uma única linha.

O exemplo de código a seguir mostra o [`CollectionView`](xref:Xamarin.Forms.CollectionView) do `NotesPage` :

```xaml
<CollectionView x:Name="collectionView"
                Margin="{StaticResource PageMargin}"
                SelectionMode="Single"
                SelectionChanged="OnSelectionChanged">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="10" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Label Text="{Binding Text}"
                       FontSize="Medium" />
                <Label Text="{Binding Date}"
                       TextColor="{StaticResource TertiaryColor}"
                       FontSize="Small" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

O layout de cada linha no [`CollectionView`](xref:Xamarin.Forms.CollectionView) é definido dentro do [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) elemento e usa a vinculação de dados para exibir todas as anotações que são recuperadas pelo aplicativo. A [`CollectionView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade é definida como a fonte de dados, em **NotesPage.XAML.cs**:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    collectionView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Esse código popula o [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todas as anotações armazenadas no banco de dados e é executado quando a página é exibida.

Quando um item é selecionado no [`CollectionView`](xref:Xamarin.Forms.CollectionView) , o evento é disparado `SelectionChanged` . Um manipulador de eventos, chamado `OnSelectionChanged`, é executado quando o evento é disparado:

```csharp
async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (e.CurrentSelection != null)
    {
        // ...
    }
}
```

O `SelectionChanged` evento pode acessar o objeto que foi associado ao item por meio da `e.CurrentSelection` propriedade.

Para obter mais informações sobre a [`CollectionView`](xref:Xamarin.Forms.CollectionView) classe, consulte [ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="navigation"></a>Navegação

A navegação é executada em um aplicativo Shell, especificando um URI para onde navegar. Os URIs de navegação têm três componentes:

- Uma *rota*, que define o caminho para o conteúdo que existe como parte da hierarquia visual do Shell.
- Uma *página*. As páginas que não existam na hierarquia visual do Shell podem ser enviadas por push para a pilha de navegação de qualquer lugar em um aplicativo Shell. Por exemplo, o `NoteEntryPage` não é definido na hierarquia visual do Shell, mas pode ser enviado por push para a pilha de navegação, conforme necessário.
- Um ou mais *parâmetros de consulta*. Parâmetros de consulta são aqueles que podem ser passados para a página de destino durante a navegação.

Um URI de navegação não precisa incluir todos os três componentes, mas quando ele faz a estrutura é://Route/Page? QueryParameters

> [!NOTE]
> As rotas podem ser definidas em elementos na hierarquia visual do Shell por meio da `Route` propriedade. No entanto, se a `Route` propriedade não estiver definida, como no aplicativo Notes, uma rota será gerada no tempo de execução.

Para obter mais informações sobre a navegação do Shell, consulte [ Xamarin.Forms navegação do Shell](~/xamarin-forms/app-fundamentals/shell/navigation.md).

### <a name="register-routes"></a>Registrar rotas

Para navegar até uma página que não existe na hierarquia visual do Shell, é necessário que ela seja registrada primeiro com o sistema de roteamento do Shell. usando o `Routing.RegisterRoute` método. No aplicativo Notes, isso ocorre no `AppShell` Construtor:

```csharp
public partial class AppShell : Shell
{
    public AppShell()
    {
        // ...
        Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
    }
}
```

Neste exemplo, uma rota chamada `NoteEntryPage` é registrada no `NoteEntryPage` tipo. Essa página pode então ser navegada para usar a navegação baseada em URI, de qualquer lugar no aplicativo.

### <a name="perform-navigation"></a>Realizar navegação

A navegação é executada pelo `GoToAsync` método, que aceita um argumento que representa a rota para a qual navegar:

```csharp
await Shell.Current.GoToAsync("NoteEntryPage");
```

Neste exemplo, o `NoteEntryPage` é navegado para.

> [!IMPORTANT]
> Uma pilha de navegação é criada quando uma página que não está na hierarquia visual do Shell é navegada para.

Ao navegar até uma página, os dados podem ser passados para a página como um parâmetro de consulta:

```csharp
async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    if (e.CurrentSelection != null)
    {
        // Navigate to the NoteEntryPage, passing the ID as a query parameter.
        Note note = (Note)e.CurrentSelection.FirstOrDefault();
        await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
    }
}
```

Este exemplo recupera o item atualmente selecionado no [`CollectionView`](xref:Xamarin.Forms.CollectionView) e navega para o `NoteEntryPage` , com o valor da Propriedade do `ID` `Note` objeto que está sendo passado como um parâmetro de consulta para a `NoteEntryPage.ItemId` propriedade.

> [!NOTE]
> Os parâmetros de consulta são codificados em URL antes da navegação.

Para receber os dados passados, a `NoteEntryPage` classe é decorada com o `QueryPropertyAttribute`

```csharp
[QueryProperty(nameof(ItemId), nameof(ItemId))]
public partial class NoteEntryPage : ContentPage
{
    public string ItemId
    {
        set
        {
            LoadNote(value);
        }
    }
    // ...
}
```

O primeiro argumento para o `QueryPropertyAttribute` especifica que a `ItemId` Propriedade receberá os dados passados, com o segundo argumento especificando a ID de parâmetro de consulta. Portanto, o `QueryPropertyAttribute` no exemplo acima Especifica que a `ItemId` Propriedade receberá os dados passados no `ItemId` parâmetro de consulta do URI na `GoToAsync` chamada do método. `ItemId`Em seguida, a propriedade chama o `LoadNote` método para recuperar a nota do dispositivo.

A navegação para trás é executada especificando ".." como o argumento para o `GoToAsync` método:

```csharp
await Shell.Current.GoToAsync("..");
```

## <a name="data-binding"></a>Vinculação de dados

A vinculação de dados é usada para simplificar a forma como um Xamarin.Forms aplicativo exibe e interage com seus dados. Ela estabelece uma conexão entre a interface do usuário e o aplicativo subjacente. A [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe contém grande parte da infraestrutura para dar suporte à vinculação de dados.

A vinculação de dados conecta dois objetos, chamados de a *origem* e o *destino*. O objeto *origem* fornece os dados. O objeto *destino* consumirá (e geralmente exibirá) dados do objeto de origem. Por exemplo, um [`Editor`](xref:Xamarin.Forms.Editor) (objeto de *destino* ) normalmente vinculará sua [`Text`](xref:Xamarin.Forms.InputView.Text) Propriedade a uma `string` propriedade pública em um objeto de *origem* . O diagrama a seguir ilustra essa relação de associação:

![Associação de dados](deepdive-images/data-binding.png)

O principal benefício da vinculação de dados é que você não precisa se preocupar sobre a sincronização de dados entre suas exibições e a fonte de dados. As alterações no objeto de *origem* são enviadas automaticamente para o objeto de *destino* nos bastidores pela estrutura de associação, enquanto as alterações no objeto de destino podem ser opcionalmente enviadas de volta para o objeto de *origem*.

Estabelecer a vinculação de dados é um processo de duas etapas:

- A [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) Propriedade do objeto de *destino* deve ser definida como a *origem*.
- Uma associação deve ser estabelecida entre o *destino* e a *origem*. Em XAML, isso é obtido usando a [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) extensão de marcação.

No aplicativo Notes, o destino de associação é o [`Editor`](xref:Xamarin.Forms.Editor) que exibe uma observação, enquanto a `Note` instância definida como [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` é a origem da associação. Inicialmente, o `BindingContext` de `NoteEntryPage` é definido quando o construtor de página é executado:

```csharp
public NoteEntryPage()
{
    // ...
    BindingContext = new Note();
}
```

Neste exemplo, a página `BindingContext` é definida como uma nova `Note` quando a `NoteEntryPage` é criada. Trata-se do cenário de adição de uma nova nota ao aplicativo.

Além disso, a página `BindingContext` também pode ser definida quando a navegação até `NoteEntryPage` ocorrer, desde que uma nota existente tenha sido selecionada no `NotesPage` :

```csharp
[QueryProperty(nameof(ItemId), nameof(ItemId))]
public partial class NoteEntryPage : ContentPage
{
    public string ItemId
    {
        set
        {
            LoadNote(value);
        }

        async void LoadNote(string itemId)
        {
            try
            {
                int id = Convert.ToInt32(itemId);
                // Retrieve the note and set it as the BindingContext of the page.
                Note note = await App.Database.GetNoteAsync(id);
                BindingContext = note;
            }
            catch (Exception)
            {
                Console.WriteLine("Failed to load note.");
            }
        }    
        // ...    
    }
}
```

Neste exemplo, quando a navegação de página ocorre, a página `BindingContext` é definida como o `Note` objeto selecionado depois de ser recuperada do banco de dados.

> [!IMPORTANT]
> Embora a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade de cada objeto de *destino* possa ser definida individualmente, isso não é necessário. `BindingContext` é uma propriedade especial que é herdada por todos os seus filhos. Portanto, quando o `BindingContext` no [`ContentPage`](xref:Xamarin.Forms.ContentPage) é definido como uma `Note` instância, todos os filhos do `ContentPage` têm o mesmo `BindingContext` e podem se associar a propriedades públicas do `Note` objeto.

[`Editor`](xref:Xamarin.Forms.Editor)Em seguida, o no é `NoteEntryPage` associado à `Text` Propriedade do `Note` objeto:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}" />
```

Uma associação entre a [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) propriedade e a `Text` Propriedade do objeto de *origem* é estabelecida. As alterações feitas no `Editor` serão propagadas automaticamente para o objeto `Note`. Da mesma forma, se forem feitas alterações na `Note.Text` propriedade, o Xamarin.Forms mecanismo de associação também atualizará o conteúdo do `Editor` . Isso é conhecido como uma *associação bidirecional*.

Para obter mais informações sobre a vinculação de dados, consulte [ Xamarin.Forms vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Estilo

Xamarin.Forms os aplicativos geralmente contêm vários elementos visuais que têm uma aparência idêntica. Definir a aparência de cada elemento visual pode ser repetitivo e propenso a erros. Em vez disso, é possível criar estilos para definir a aparência e, em seguida, aplicá-los aos elementos visuais necessários.

A [`Style`](xref:Xamarin.Forms.Style) classe agrupa uma coleção de valores de propriedade em um objeto que pode ser aplicada a várias instâncias de elementos visuais. Os estilos são armazenados em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , seja no nível do aplicativo, no nível da página ou no nível de exibição. Escolher em que local definir um `Style` afeta o local em que ele pode ser usado:

- [`Style`](xref:Xamarin.Forms.Style) as instâncias definidas no nível do aplicativo podem ser aplicadas em todo o aplicativo.
- [`Style`](xref:Xamarin.Forms.Style) as instâncias definidas no nível de página podem ser aplicadas à página e a seus filhos.
- [`Style`](xref:Xamarin.Forms.Style) as instâncias definidas no nível de exibição podem ser aplicadas à exibição e a seus filhos.

> [!IMPORTANT]
> Todos os estilos usados em todo o aplicativo são armazenados no dicionário de recursos do aplicativo para evitar duplicação. No entanto, o XAML específico de uma página não deve ser incluído no dicionário de recursos do aplicativo, uma vez que os recursos então serão analisados na inicialização do aplicativo, em vez de quando exigido por uma página. Para obter mais informações, consulte [reduzir o tamanho do dicionário de recursos do aplicativo](~/xamarin-forms/deploy-test/performance.md#reduce-the-application-resource-dictionary-size).

Cada [`Style`](xref:Xamarin.Forms.Style) instância contém uma coleção de um ou mais [`Setter`](xref:Xamarin.Forms.Setter) objetos, com cada `Setter` um com um [`Property`](xref:Xamarin.Forms.Setter.Property) e um [`Value`](xref:Xamarin.Forms.Setter.Value) . O `Property` é o nome da propriedade vinculável do elemento ao qual o estilo é aplicado e o `Value` é o valor aplicado à propriedade. O exemplo de código a seguir mostra um estilo de `NoteEntryPage`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.Views.NoteEntryPage"
             Title="Note Entry">
    <ContentPage.Resources>
        <!-- Implicit styles -->
        <Style TargetType="{x:Type Editor}">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

Esse estilo é aplicado a qualquer [`Editor`](xref:Xamarin.Forms.Editor) instância na página.

Ao criar um [`Style`](xref:Xamarin.Forms.Style) , a [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propriedade é sempre necessária.

> [!NOTE]
> O estilo de um Xamarin.Forms aplicativo é tradicionalmente realizado com o uso de estilos XAML. No entanto, o Xamarin.Forms também dá suporte a elementos visuais de estilo usando folhas de estilos em cascata (CSS). Para obter mais informações, consulte [estilizando Xamarin.Forms aplicativos usando folhas de estilos em cascata (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Para obter mais informações sobre estilos XAML, consulte [estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="test-and-deployment"></a>Teste e implantação

Tanto o Visual Studio para Mac quanto o Visual Studio oferecem várias opções para testar e implantar um aplicativo. Depurar aplicativos é uma parte comum do ciclo de vida de desenvolvimento de aplicativo e ajuda a diagnosticar problemas de código. Para saber mais, consulte [Definir um ponto de interrupção](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Percorrer o código](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e [Enviar as informações para a janela de log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Simuladores são um bom lugar para começar a implantar e testar um aplicativo e eles apresentam recursos úteis para testar aplicativos. No entanto, os usuários não consumirão o aplicativo final em um simulador, assim, os aplicativos devem ser testados em dispositivos reais antecipadamente e com frequência. Para saber mais sobre o provisionamento de dispositivo iOS, consulte [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md). Para saber mais sobre o provisionamento de dispositivo Android, consulte [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Próximas etapas

Esse aprofundamento examinou os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms shell. As próximas etapas sugeridas incluem ler sobre as seguintes funcionalidades:

- Xamarin.Forms O Shell reduz a complexidade do desenvolvimento de aplicativos móveis fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exigem. Para obter mais informações, consulte [ Xamarin.Forms shell](~/xamarin-forms/app-fundamentals/shell/index.md).
- Há vários grupos de controle usados para criar a interface do usuário de um Xamarin.Forms aplicativo. Para obter mais informações, confira [Referência de controles](~/xamarin-forms/user-interface/controls/index.md).
- A associação de dados é uma técnica para vincular propriedades de dois objetos para que as alterações em uma propriedade sejam automaticamente refletidas na outra propriedade. Para obter mais informações, confira [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms fornece várias experiências de navegação de página, dependendo do tipo de página que está sendo usado. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Estilos ajudam a reduzir as marcações repetitivas e permitem que a aparência de aplicativos seja alterada com mais facilidade. Para obter mais informações, consulte [estilizando Xamarin.Forms aplicativos](~/xamarin-forms/user-interface/styles/index.md).
- Os modelos de dados fornecem a capacidade de definir a apresentação de dados em exibições com suporte. Para saber mais, veja [Modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Efeitos também permitem que os controles nativos em cada plataforma sejam personalizados. Os efeitos são criados em projetos específicos da plataforma por meio da subclasse da [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) classe e são consumidos anexando-os a um Xamarin.Forms controle apropriado. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).
- Cada página, layout e exibição é renderizado diferentemente em cada plataforma usando uma classe `Renderer`, que por sua vez cria um controle nativo, organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado. Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- O código compartilhado pode acessar a funcionalidade nativa por meio da classe [`DependencyService`](xref:Xamarin.Forms.DependencyService). Para obter mais informações, consulte [Acessar recursos nativos com DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

## <a name="related-links"></a>Links relacionados

- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [XAML (linguagem XAML)](~/xamarin-forms/xaml/index.yml)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Referência de controles](~/xamarin-forms/user-interface/controls/index.md)
- [Exemplos de introdução](/samples/browse/?products=xamarin&term=Xamarin.Forms%2bget%2bstarted)
- [Xamarin.Forms Amostras](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms Referência de API](xref:Xamarin.Forms)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
