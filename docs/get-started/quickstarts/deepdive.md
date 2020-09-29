---
title: Xamarin.Forms Aprofundamento do guia de início rápido
description: Este artigo examina os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms . Os tópicos abordados incluíam a anatomia de um Xamarin.Forms aplicativo, conceitos básicos de aplicativos e de arquitetura e a interface do usuário.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8bfa78ef334829ab228d691368ec44ac71d415ad
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433508"
---
# <a name="no-locxamarinforms-quickstart-deep-dive"></a>Xamarin.Forms Aprofundamento do guia de início rápido

No [ Xamarin.Forms início rápido](~/get-started/index.yml), o aplicativo Notes foi criado. Este artigo revisa o que foi criado para compreender os conceitos básicos de como Xamarin.Forms os aplicativos funcionam.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introdução ao Visual Studio

O Visual Studio organiza o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste, entre outros. O aplicativo Notes consiste em uma solução contendo quatro projetos, conforme mostrado na captura de tela a seguir:

![Gerenciador de Soluções do Visual Studio](deepdive-images/vs/solution.png)

Os projetos são:

- Notes – esse projeto é o projeto da biblioteca do .NET Standard que contém todos os códigos compartilhados e interfaces do usuário compartilhadas.
- Notes.Android – esse projeto contém o código específico do Android e é o ponto de entrada para o aplicativo Android.
- Notes.iOS – esse projeto contém o código específico do iOS e é o ponto de entrada para o aplicativo iOS.
- Notes.UWP – esse projeto contém código específico da UWP (Plataforma Universal do Windows) e é o ponto de entrada para o aplicativo UWP.

## <a name="anatomy-of-a-no-locxamarinforms-application"></a>Anatomia de um Xamarin.Forms aplicativo

A captura de tela a seguir mostra o conteúdo do projeto de biblioteca do .NET Standard Notes no Visual Studio:

![Conteúdo do projeto do .NET Standard Phoneword](deepdive-images/vs/net-standard-project.png)

O projeto tem um nó de **dependências** que contém os nós **NuGet** e **SDK** :

- **NuGet** &ndash; os Xamarin.Forms pacotes NuGet do SQLite-net-PCL que foram adicionados ao projeto.
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

## <a name="anatomy-of-a-no-locxamarinforms-application"></a>Anatomia de um Xamarin.Forms aplicativo

A captura de tela a seguir mostra o conteúdo do projeto de biblioteca do .NET Standard Notes no Visual Studio para Mac:

![Conteúdo do projeto de biblioteca do .NET Standard Phoneword](deepdive-images/vsmac/net-standard-project.png)

O projeto tem um nó de **dependências** que contém os nós **NuGet** e **SDK** :

- **NuGet** &ndash; os Xamarin.Forms pacotes NuGet do SQLite-net-PCL que foram adicionados ao projeto.
- **SDK** &ndash; o metapacote `NETStandard.Library` que faz referência ao conjunto completo de pacotes NuGet que definem o .NET Standard.

::: zone-end

O projeto também consiste em vários arquivos:

- **Data\NoteDatabase.cs** – essa classe contém código para criar o banco de dados e ler, gravar e excluir dados do banco de dados.
- **Modelos\Note.cs** – essa classe define um modelo de `Note` cujas instâncias armazenam dados sobre cada observação no aplicativo.
- **XAML** – a marcação XAML para a classe `App`, que define um dicionário de recursos para o aplicativo.
- **App.XAML.CS** – o code-behind para a classe `App`, que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma e por processar eventos de ciclo de vida do aplicativo.
- **AssemblyInfo.cs** – esse arquivo contém um atributo de aplicativo sobre o projeto, que é aplicado no nível do assembly.
- **NotesPage.xaml** – a marcação XAML para a classe `NotesPage`, que define a interface do usuário para a página exibida quando o aplicativo é iniciado.
- **NotesPage.xaml.cs** – o code-behind para a classe `NotesPage`, que contém a lógica de negócios executada quando o usuário interage com a página.
- **NoteEntryPage.xaml** – a marcação XAML para a classe `NoteEntryPage`, que define a interface do usuário para a página exibida quando o usuário insere uma observação.
- **NoteEntryPage.xaml.cs** – o code-behind para a classe `NoteEntryPage`, que contém a lógica de negócios executada quando o usuário interage com a página.

Para saber mais sobre a anatomia de um aplicativo Xamarin.iOS, consulte [Anatomia de um aplicativo Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Para saber mais sobre a anatomia de um aplicativo Xamarin.Android, consulte [Anatomia de um aplicativo Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Arquitetura e conceitos básicos de aplicativo

Um Xamarin.Forms aplicativo é arquitetado da mesma forma que um aplicativo tradicional entre plataformas. O código compartilhado normalmente é colocado em uma biblioteca do .NET Standard e aplicativos específicos de plataforma consomem o código compartilhado. O diagrama a seguir mostra uma visão geral dessa relação para o aplicativo Notes:

::: zone pivot="windows"

![Arquitetura do Notes](deepdive-images/vs/architecture.png)

::: zone-end
::: zone pivot="macos"

![Arquitetura do Notes](deepdive-images/vsmac/architecture.png)

::: zone-end

Para maximizar a reutilização do código de inicialização, os Xamarin.Forms aplicativos têm uma única classe denominada `App` responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma, conforme mostrado no exemplo de código a seguir:

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

Esse código define a `MainPage` propriedade da `App` classe para uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância cujo conteúdo é uma `NotesPage` instância.

Além disso, o arquivo **AssemblyInfo.cs** contém um único atributo de aplicativo, que é aplicado no nível do assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

O [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) atributo ativa o compilador XAML, para que o XAML seja compilado diretamente na linguagem intermediária. Para saber mais, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilação de XAML).

## <a name="launching-the-application-on-each-platform"></a>Iniciar o aplicativo em cada plataforma

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

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Em aplicativos Plataforma Universal do Windows (UWP), o `Init` método que inicializa a Xamarin.Forms estrutura é invocado da `App` classe:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Isso faz com que a implementação específica de UWP do seja Xamarin.Forms carregada no aplicativo. A Xamarin.Forms página inicial é iniciada pela `MainPage` classe:

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

O Xamarin.Forms aplicativo é carregado com o `LoadApplication` método.

> [!NOTE]
> Plataforma Universal do Windows aplicativos podem ser compilados com o Xamarin.Forms , mas apenas usando o Visual Studio no Windows.

::: zone-end

## <a name="user-interface"></a>Interface do usuário

Há quatro grupos de controle principais usados para criar a interface do usuário de um Xamarin.Forms aplicativo:

1. **Páginas** – as Xamarin.Forms páginas representam telas de aplicativos móveis de plataforma cruzada. O aplicativo Notes usa a [`ContentPage`](xref:Xamarin.Forms.ContentPage) classe para exibir telas únicas. Para obter mais informações sobre páginas, consulte [ Xamarin.Forms páginas](~/xamarin-forms/user-interface/controls/pages.md).
1. **Exibições** – Xamarin.Forms exibições são os controles exibidos na interface do usuário, como rótulos, botões e caixas de entrada de texto. O aplicativo notas concluídas usa [`ListView`](xref:Xamarin.Forms.ListView) as [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) exibições, e. Para obter mais informações sobre exibições, consulte [ Xamarin.Forms exibições](~/xamarin-forms/user-interface/controls/views.md).
1. **Layouts** – os Xamarin.Forms layouts são contêineres usados para compor exibições em estruturas lógicas. O aplicativo Notes usa a [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe para organizar as exibições em uma pilha vertical e a [`Grid`](xref:Xamarin.Forms.Grid) classe para organizar os botões horizontalmente. Para obter mais informações sobre layouts, consulte [ Xamarin.Forms layouts](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Células** – Xamarin.Forms as células são elementos especializados usados para itens em uma lista e descrevem como cada item em uma lista deve ser desenhado. O aplicativo Notes usa o [`TextCell`](xref:Xamarin.Forms.TextCell) para exibir dois itens para cada linha na lista. Para obter mais informações sobre células, consulte [ Xamarin.Forms células](~/xamarin-forms/user-interface/controls/cells.md).

No runtime, cada controle será mapeado para seu equivalente nativo, que é o que será renderizado.

### <a name="layout"></a>Layout

O aplicativo Notes usa o [`StackLayout`](xref:Xamarin.Forms.StackLayout) para simplificar o desenvolvimento de aplicativos de plataforma cruzada, organizando automaticamente as exibições na tela, independentemente do tamanho da tela. Os elementos filho são posicionados um após o outro, horizontalmente ou verticalmente, na ordem em que foram adicionados. A quantidade de espaço que o `StackLayout` usará depende de como as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriedades e são definidas, mas por padrão, o `StackLayout` tentará usar a tela inteira.

O código XAML a seguir mostra um exemplo de como usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) para layout `NoteEntryPage` :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
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

Para obter mais informações sobre a [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe, consulte [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md).

### <a name="responding-to-user-interaction"></a>Respondendo à interação do usuário

Um objeto definido em XAML pode acionar um evento que é processado no arquivo code-behind. O exemplo de código a seguir mostra o `OnSaveButtonClicked` método no code-behind para a `NoteEntryPage` classe, que é executada em resposta ao [`Clicked`](xref:Xamarin.Forms.Button.Clicked) acionamento do evento no botão *salvar* .

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

O método `OnSaveButtonClicked` salva a observação no banco de dados e navega de volta para a página anterior.

> [!NOTE]
> O arquivo code-behind de uma classe XAML pode acessar um objeto definido em XAML usando o nome atribuído a ele com o atributo `x:Name`. O valor atribuído a esse atributo tem as mesmas regras que variáveis C#, no sentido de que deve começar com uma letra ou um sublinhado e não conter espaços.

A conexão do botão Salvar para o método `OnSaveButtonClicked` ocorre na marcação XAML para a classe `NoteEntryPage`:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listas

O [`ListView`](xref:Xamarin.Forms.ListView) é responsável por exibir uma coleção de itens verticalmente em uma lista. Cada item no `ListView` estará contido em uma única célula.

O exemplo de código a seguir mostra o [`ListView`](xref:Xamarin.Forms.ListView) do `NotesPage` :

```xaml
<ListView x:Name="listView"
          Margin="{StaticResource PageMargin}"
          ItemSelected="OnListViewItemSelected">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Text}"
                      Detail="{Binding Date}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

O layout de cada linha no [`ListView`](xref:Xamarin.Forms.ListView) é definido dentro do [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) elemento e usa a vinculação de dados para exibir todas as anotações que são recuperadas pelo aplicativo. A [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propriedade é definida como a fonte de dados, em `NotesPage.xaml.cs` :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Esse código popula o [`ListView`](xref:Xamarin.Forms.ListView) com todas as anotações armazenadas no banco de dados.

Quando uma linha é selecionada no [`ListView`](xref:Xamarin.Forms.ListView) , o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento é acionado. Um manipulador de eventos, chamado `OnListViewItemSelected`, é executado quando o evento é disparado:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

O [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento pode acessar o objeto que foi associado à célula por meio da [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriedade.

Para obter mais informações sobre a [`ListView`](xref:Xamarin.Forms.ListView) classe, consulte [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navegação

Xamarin.Forms fornece várias experiências de navegação de página diferentes, dependendo do [`Page`](xref:Xamarin.Forms.Page) tipo que está sendo usado. Para a [`ContentPage`](xref:Xamarin.Forms.ContentPage) navegação de instâncias podem ser hierárquicas ou modais. Para obter informações sobre a navegação modal, consulte [ Xamarin.Forms páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> As [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) classes e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) fornecem experiências de navegação alternativas. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).

Na navegação hierárquica, a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe é usada para navegar por uma pilha de [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos, para frente e para trás, conforme desejado. A classe implementa a navegação como uma pilha UEPS (último a entrar, primeiro a sair) de [`Page`](xref:Xamarin.Forms.Page) objetos. Para mover-se de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa. Para retornar à página anterior, o aplicativo removerá o item mais recente, que é a página atual da pilha de navegação, então a nova página de nível mais alto se tornará a página ativa.

A classe `NavigationPage` também adicionará uma barra de navegação à parte superior da página que exibe um título e um botão **Voltar** apropriado para a plataforma, que retornará à página anterior.

A primeira página adicionada a uma pilha de navegação é conhecida como a página *raiz* do aplicativo e o exemplo de código a seguir mostra como isso é realizado no aplicativo Notes:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Todas as [`ContentPage`](xref:Xamarin.Forms.ContentPage) instâncias têm uma [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade que expõe métodos para modificar a pilha de páginas. Esses métodos só devem ser invocados se o aplicativo incluir um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Para navegar até o `NoteEntryPage` , é necessário invocar o [ `PushAsync` ] (xref: Xamarin.Forms . NavigationPage. PushAsync ( Xamarin.Forms . Page)), conforme demonstrado no exemplo de código abaixo:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

Isso faz com que o novo objeto `NoteEntryPage` seja enviado por push para a pilha de navegação, em que ele se torna a página ativa.

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela. Para retornar de forma programática a página original, o `NoteEntryPage` objeto deve invocar o [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) método, conforme demonstrado no exemplo de código abaixo:

```csharp
await Navigation.PopAsync();
```

Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Associação de dados

A vinculação de dados é usada para simplificar a forma como um Xamarin.Forms aplicativo exibe e interage com seus dados. Ela estabelece uma conexão entre a interface do usuário e o aplicativo subjacente. A [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe contém grande parte da infraestrutura para dar suporte à vinculação de dados.

A vinculação de dados conecta dois objetos, chamados de a *origem* e o *destino*. O objeto *origem* fornece os dados. O objeto *destino* consumirá (e geralmente exibirá) dados do objeto de origem. Por exemplo, um [`Editor`](xref:Xamarin.Forms.Editor) (objeto de*destino* ) normalmente vinculará sua [`Text`](xref:Xamarin.Forms.InputView.Text) Propriedade a uma `string` propriedade pública em um objeto de *origem* . O diagrama a seguir ilustra essa relação de associação:

![Associação de dados](deepdive-images/data-binding.png)

O principal benefício da vinculação de dados é que você não precisa se preocupar sobre a sincronização de dados entre suas exibições e a fonte de dados. As alterações no objeto de *origem* são enviadas automaticamente para o objeto de *destino* nos bastidores pela estrutura de associação, enquanto as alterações no objeto de destino podem ser opcionalmente enviadas de volta para o objeto de *origem*.

Estabelecer a vinculação de dados é um processo de duas etapas:

- A [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) Propriedade do objeto de *destino* deve ser definida como a *origem*.
- Uma associação deve ser estabelecida entre o *destino* e a *origem*. Em XAML, isso é obtido usando a [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) extensão de marcação.

No aplicativo Notes, o destino de associação é o [`Editor`](xref:Xamarin.Forms.Editor) que exibe uma observação, enquanto a `Note` instância definida como [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` é a origem da associação.

O `BindingContext` de `NoteEntryPage` é definido durante a navegação da página, conforme mostrado no exemplo de código a seguir:

```csharp
async void OnNoteAddedClicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new NoteEntryPage
    {
        BindingContext = new Note()
    });
}

async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        await Navigation.PushAsync(new NoteEntryPage
        {
            BindingContext = e.SelectedItem as Note
        });
    }
}
```

No `OnNoteAddedClicked` método, que é executado quando uma nova nota é adicionada ao aplicativo, o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` é definido como uma nova `Note` instância. No `OnListViewItemSelected` método, que é executado quando uma nota existente é selecionada no [`ListView`](xref:Xamarin.Forms.ListView) , o `BindingContext` de `NoteEntryPage` é definido como a `Note` instância selecionada, que é acessada por meio da [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriedade.

> [!IMPORTANT]
> Embora a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade de cada objeto de *destino* possa ser definida individualmente, isso não é necessário. `BindingContext` é uma propriedade especial que é herdada por todos os seus filhos. Portanto, quando o `BindingContext` no [`ContentPage`](xref:Xamarin.Forms.ContentPage) é definido como uma `Note` instância, todos os filhos do `ContentPage` têm o mesmo `BindingContext` e podem se associar a propriedades públicas do `Note` objeto.

[`Editor`](xref:Xamarin.Forms.Editor)Em seguida, o no é `NoteEntryPage` associado à `Text` Propriedade do `Note` objeto:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
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
> Todos os estilos usados em todo o aplicativo são armazenados no dicionário de recursos do aplicativo para evitar duplicação. No entanto, o XAML específico de uma página não deve ser incluído no dicionário de recursos do aplicativo, uma vez que os recursos então serão analisados na inicialização do aplicativo, em vez de quando exigido por uma página.

Cada [`Style`](xref:Xamarin.Forms.Style) instância contém uma coleção de um ou mais [`Setter`](xref:Xamarin.Forms.Setter) objetos, com cada `Setter` um com um [`Property`](xref:Xamarin.Forms.Setter.Property) e um [`Value`](xref:Xamarin.Forms.Setter.Value) . O `Property` é o nome da propriedade vinculável do elemento ao qual o estilo é aplicado e o `Value` é o valor aplicado à propriedade. O exemplo de código a seguir mostra um estilo de `NoteEntryPage`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
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

### <a name="providing-platform-specific-styles"></a>Como fornecendo estilos específicos da plataforma

A extensão de marcação `OnPlatform` permite que você personalize a aparência da interface do usuário por plataforma:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

Isso [`Style`](xref:Xamarin.Forms.Style) define [`Color`](xref:Xamarin.Forms.Color) valores diferentes para as [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) Propriedades e de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , dependendo da plataforma que está sendo usada.

Para obter mais informações sobre extensões de marcação XAML, consulte [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Para obter informações sobre a extensão de marcação de `OnPlatform`, consulte [Extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Teste e implantação

Tanto o Visual Studio para Mac quanto o Visual Studio oferecem várias opções para testar e implantar um aplicativo. Depurar aplicativos é uma parte comum do ciclo de vida de desenvolvimento de aplicativo e ajuda a diagnosticar problemas de código. Para saber mais, consulte [Definir um ponto de interrupção](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Percorrer o código](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e [Enviar as informações para a janela de log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Simuladores são um bom lugar para começar a implantar e testar um aplicativo e eles apresentam recursos úteis para testar aplicativos. No entanto, os usuários não consumirão o aplicativo final em um simulador, assim, os aplicativos devem ser testados em dispositivos reais antecipadamente e com frequência. Para saber mais sobre o provisionamento de dispositivo iOS, consulte [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md). Para saber mais sobre o provisionamento de dispositivo Android, consulte [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Próximas etapas

Esse aprofundamento examinou os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms . As próximas etapas sugeridas incluem ler sobre as seguintes funcionalidades:

- Há quatro grupos de controle principais usados para criar a interface do usuário de um Xamarin.Forms aplicativo. Para obter mais informações, confira [Referência de controles](~/xamarin-forms/user-interface/controls/index.md).
- A associação de dados é uma técnica para vincular propriedades de dois objetos para que as alterações em uma propriedade sejam automaticamente refletidas na outra propriedade. Para obter mais informações, confira [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms fornece várias experiências de navegação de página diferentes, dependendo do tipo de página que está sendo usado. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Estilos ajudam a reduzir as marcações repetitivas e permitem que a aparência de aplicativos seja alterada com mais facilidade. Para obter mais informações, consulte [estilizando Xamarin.Forms aplicativos](~/xamarin-forms/user-interface/styles/index.md).
- As extensões de marcação XAML estendem a eficiência e a flexibilidade do XAML permitindo que os atributos do elemento sejam definidos de fontes que não sejam cadeias de caracteres de texto literal. Para obter mais informações, confira [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Os modelos de dados fornecem a capacidade de definir a apresentação de dados em exibições com suporte. Para saber mais, veja [Modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Cada página, layout e exibição é renderizado diferentemente em cada plataforma usando uma classe `Renderer`, que por sua vez cria um controle nativo, organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado. Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Efeitos também permitem que os controles nativos em cada plataforma sejam personalizados. Os efeitos são criados em projetos específicos da plataforma por meio da subclasse da [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) classe e são consumidos anexando-os a um Xamarin.Forms controle apropriado. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).
- O código compartilhado pode acessar a funcionalidade nativa por meio da [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe. Para obter mais informações, consulte [Acessar recursos nativos com DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Como alternativa, a [_criação de aplicativos Xamarin.Forms móveis com _](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)o, um livro de Charles Petzold, é um bom local para saber mais sobre o Xamarin.Forms . O livro está disponível em PDF ou em vários formatos de livro eletrônico.

## <a name="related-links"></a>Links relacionados

- [Linguagem de marcação de aplicativo extensível (XAML)](~/xamarin-forms/xaml/index.yml)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Referência de Controles](~/xamarin-forms/user-interface/controls/index.md)
- [Extensões de marcação do XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms Amostras](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Exemplos de Introdução](/samples/browse/?products=xamarin&term=Xamarin.Forms%2bget%2bstarted)
- [Xamarin.Forms Referência de API](xref:Xamarin.Forms)
- [Aprendizado autodirigido gratuito (vídeo)](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]