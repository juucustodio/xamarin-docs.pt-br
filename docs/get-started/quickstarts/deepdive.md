---
title: Aprofundamento do Guia de Início Rápido do Xamarin.Forms
description: Este artigo examina os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms. Os tópicos abordados incluído a anatomia de um aplicativo Xamarin.Forms, arquitetura e conceitos básicos de aplicativo e a interface do usuário.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: 3936fe16ee768505c53ec119c51dcbecef7e6fbe
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "73842964"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Aprofundamento do Guia de Início Rápido do Xamarin.Forms

No [Início Rápido do Xamarin.Forms](~/get-started/index.yml), o aplicativo Notes foi criado. Este artigo analisa o que foi criado para entender os fundamentos de como os aplicativos Xamarin.Forms funcionam.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introdução ao Visual Studio

O Visual Studio organiza o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste, entre outros. O aplicativo Notes consiste em uma solução contendo quatro projetos, conforme mostrado na captura de tela a seguir:

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

Os projetos são:

- Notes – esse projeto é o projeto da biblioteca do .NET Standard que contém todos os códigos compartilhados e interfaces do usuário compartilhadas.
- Notes.Android – esse projeto contém o código específico do Android e é o ponto de entrada para o aplicativo Android.
- Notes.iOS – esse projeto contém o código específico do iOS e é o ponto de entrada para o aplicativo iOS.
- Notes.UWP – esse projeto contém código específico da UWP (Plataforma Universal do Windows) e é o ponto de entrada para o aplicativo UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um aplicativo Xamarin.Forms

A captura de tela a seguir mostra o conteúdo do projeto de biblioteca do .NET Standard Notes no Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

O projeto tem um nó **Dependências** que contém os nós **NuGet** e **SDK**:

- **NuGet** &ndash; os pacotes do NuGet Xamarin. Forms e SQLite-net-PCL que foram adicionados ao projeto.
- **SDK** &ndash; o metapacote `NETStandard.Library` que faz referência ao conjunto completo de pacotes NuGet que definem o .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

O [Visual Studio para Mac](/visualstudio/mac/) segue a prática do Visual Studio de organizar o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste, entre outros. O aplicativo Notes consiste em uma solução contendo três projetos, conforme mostrado na captura de tela a seguir:

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

Os projetos são:

- Notes – esse projeto é o projeto da biblioteca do .NET Standard que contém todos os códigos compartilhados e interfaces do usuário compartilhadas.
- Notes.Android – esse projeto contém o código específico do Android e é o ponto de entrada para aplicativos Android.
- Notes.iOS – esse projeto contém o código específico do iOS e é o ponto de entrada para aplicativos iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um aplicativo Xamarin.Forms

A captura de tela a seguir mostra o conteúdo do projeto de biblioteca do .NET Standard Notes no Visual Studio para Mac:

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

O projeto tem um nó **Dependências** que contém os nós **NuGet** e **SDK**:

- **NuGet** &ndash; os pacotes do NuGet Xamarin. Forms e SQLite-net-PCL que foram adicionados ao projeto.
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

Um aplicativo Xamarin.Forms é arquitetado da mesma forma que um aplicativo tradicional de plataforma cruzada. O código compartilhado normalmente é colocado em uma biblioteca do .NET Standard e aplicativos específicos de plataforma consomem o código compartilhado. O diagrama a seguir mostra uma visão geral dessa relação para o aplicativo Notes:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

::: zone-end

Para maximizar a reutilização de código de inicialização, os aplicativos Xamarin.Forms têm uma única classe chamada `App`, que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma, conforme mostrado no exemplo de código a seguir:

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

Esse código define a propriedade `MainPage` da classe `App` como uma instância [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) cujo conteúdo é uma instância de `NotesPage`.

Além disso, o arquivo **AssemblyInfo.cs** contém um único atributo de aplicativo, que é aplicado no nível do assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

O atributo [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) ativa o compilador XAML para que o XAML seja compilado diretamente em linguagem intermediária. Para saber mais, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilação de XAML).

## <a name="launching-the-application-on-each-platform"></a>Iniciar o aplicativo em cada plataforma

### <a name="ios"></a>iOS

Para iniciar a página Xamarin.Forms inicial no iOS, o projeto Notes.iOS define a classe `AppDelegate` que herda da classe `FormsApplicationDelegate`:

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

A substituição `FinishedLaunching` inicializa a estrutura do Xamarin.Forms ao chamar o método `Init`. Isso faz a implementação específica do iOS do Xamarin.Forms ser carregada no aplicativo antes que o controlador de exibição de raiz seja definido pela chamada para o método `LoadApplication`.

### <a name="android"></a>Android

Para iniciar a página inicial do Xamarin.Forms no Android, o projeto Notes.Android inclui código que cria um `Activity` com o atributo `MainLauncher`, com a atividade herdando da classe `FormsAppCompatActivity`:

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

A substituição `OnCreate` inicializa a estrutura do Xamarin.Forms ao chamar o método `Init`. Isso faz a implementação específica do Android do Xamarin.Forms ser carregada no aplicativo antes de o aplicativo Xamarin.Forms ser carregado.

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Em aplicativos UWP (Plataforma Universal do Windows), o método `Init` que inicializa a estrutura Xamarin.Forms é chamado da classe `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Isso faz a implementação específica de UWP do Xamarin.Forms ser carregada no aplicativo. A página do Xamarin.Forms inicial é iniciada pela classe `MainPage`:

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

O aplicativo Xamarin.Forms é carregado com o método `LoadApplication`.

> [!NOTE]
> Os aplicativos da Plataforma Universal do Windows podem ser criados com o Xamarin.Forms, mas somente usando o Visual Studio no Windows.

::: zone-end

## <a name="user-interface"></a>Interface do usuário

Há quatro grupos de controle principais usados para criar a interface do usuário de um aplicativo Xamarin.Forms:

1. **Páginas** – as páginas do Xamarin.Forms representam telas de aplicativos móveis de plataforma cruzada. O aplicativo Notes usa a classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) para exibir telas únicas. Para saber mais sobre páginas, consulte [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Páginas do Xamarin.Forms).
1. **Exibições** – as exibições do Xamarin.Forms são os controles exibidos na interface do usuário, como rótulos, botões e caixas de entrada de texto. O aplicativo Notes concluído usa as exibições [`ListView`](xref:Xamarin.Forms.ListView), [`Editor`](xref:Xamarin.Forms.Editor) e [`Button`](xref:Xamarin.Forms.Button). Para saber mais sobre exibições, consulte [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Exibições do Xamarin.Forms).
1. **Layouts** – os layouts do Xamarin.Forms são contêineres usados para compor exibições em estruturas lógicas. O aplicativo Notes usa a classe [`StackLayout`](xref:Xamarin.Forms.StackLayout) para organizar as exibições em uma pilha vertical e a classe [`Grid`](xref:Xamarin.Forms.Grid) para organizar os botões horizontalmente. Para saber mais sobre layouts, consulte [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layouts do Xamarin.Forms).
1. **Células** – as células do Xamarin.Forms são elementos especializados usados para itens em uma lista e descrevem como cada item em uma lista deve ser desenhado. O aplicativo Notes usa a [`TextCell`](xref:Xamarin.Forms.TextCell) para exibir dois itens para cada linha da lista. Para saber mais sobre células, consulte [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Células do Xamarin.Forms).

No runtime, cada controle será mapeado para seu equivalente nativo, que é o que será renderizado.

### <a name="layout"></a>Layout

O aplicativo Notes usa o [`StackLayout`](xref:Xamarin.Forms.StackLayout) para simplificar o desenvolvimento de aplicativos multiplataforma, organizando automaticamente as exibições na tela, independentemente do tamanho da tela. Os elementos filho são posicionados um após o outro, horizontalmente ou verticalmente, na ordem em que foram adicionados. A quantidade de espaço que o `StackLayout` usará dependerá de como as propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) forem definidas, mas por padrão o `StackLayout` tentará usar a tela inteira.

O código XAML a seguir mostra um exemplo de como usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) para definir o layout da `NoteEntryPage`:

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

Por padrão, o [`StackLayout`](xref:Xamarin.Forms.StackLayout) assume uma orientação vertical. No entanto, ele pode ser alterado para uma orientação horizontal definindo a propriedade [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) como o membro de enumeração [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal).

> [!NOTE]
> O tamanho das exibições pode ser definido por meio das propriedades `HeightRequest` e `WidthRequest`.

Para obter mais informações sobre a classe [`StackLayout`](xref:Xamarin.Forms.StackLayout), veja [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Respondendo à interação do usuário

Um objeto definido em XAML pode acionar um evento que é processado no arquivo code-behind. O exemplo de código a seguir mostra o método `OnSaveButtonClicked` no code-behind para a classe `NoteEntryPage`, que é executada em resposta ao disparo do evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) no botão *Salvar*.

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

O exemplo de código a seguir mostra o [`ListView`](xref:Xamarin.Forms.ListView) da `NotesPage`:

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

O layout de cada linha no [`ListView`](xref:Xamarin.Forms.ListView) é definido dentro do elemento [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) e usa a associação de dados para exibir todas as observações recuperadas pelo aplicativo. A propriedade [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) é definida como a fonte de dados em `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Esse código preenche o [`ListView`](xref:Xamarin.Forms.ListView) com todas as observações armazenadas no banco de dados.

Quando uma linha é selecionada no [`ListView`](xref:Xamarin.Forms.ListView), o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) é acionado. Um manipulador de eventos, chamado `OnListViewItemSelected`, é executado quando o evento é disparado:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

O evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) pode acessar o objeto que foi associado à célula por meio da propriedade [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem).

Para obter mais informações sobre a classe [`ListView`](xref:Xamarin.Forms.ListView), confira [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navegação

o Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo do [`Page`](xref:Xamarin.Forms.Page) sendo usado. Para instâncias [`ContentPage`](xref:Xamarin.Forms.ContentPage), a navegação pode ser hierárquica ou modal. Para obter informações sobre a navegação modal, confira [Páginas modais do Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> As classes [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) oferecem experiências de navegação alternativas. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).

Na barra de navegação hierárquica, a classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é usada para navegar por meio de uma pilha de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage), para frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](xref:Xamarin.Forms.Page). Para mover-se de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa. Para retornar à página anterior, o aplicativo removerá o item mais recente, que é a página atual da pilha de navegação, então a nova página de nível mais alto se tornará a página ativa.

A classe `NavigationPage` também adicionará uma barra de navegação à parte superior da página que exibe um título e um botão **Voltar** apropriado para a plataforma, que retornará à página anterior.

A primeira página adicionada a uma pilha de navegação é conhecida como a página *raiz* do aplicativo e o exemplo de código a seguir mostra como isso é realizado no aplicativo Notes:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Todas as instâncias do [`ContentPage`](xref:Xamarin.Forms.ContentPage) têm uma propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) que expõe métodos para modificar a pilha de páginas. Esses métodos devem ser invocados somente se o aplicativo incluir um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Para navegar até o `NoteEntryPage`, é necessário invocar o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) conforme demonstrado no exemplo de código abaixo:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

Isso faz com que o novo objeto `NoteEntryPage` seja enviado por push para a pilha de navegação, em que ele se torna a página ativa.

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela. Para retornar programaticamente à página original, o objeto `NoteEntryPage` deve invocar o método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), conforme demonstrado no exemplo de código abaixo:

```csharp
await Navigation.PopAsync();
```

Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Associação de dados

Vinculação de dados é usada para simplificar como um aplicativo Xamarin.Forms exibe e interage com seus dados. Ela estabelece uma conexão entre a interface do usuário e o aplicativo subjacente. A classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) contém a maior parte da infraestrutura para dar suporte à vinculação de dados.

A vinculação de dados conecta dois objetos, chamados de a *origem* e o *destino*. O objeto *origem* fornece os dados. O objeto *destino* consumirá (e geralmente exibirá) dados do objeto de origem. Por exemplo, um [`Editor`](xref:Xamarin.Forms.Editor) (objeto *alvo*) normalmente associará sua propriedade [`Text`](xref:Xamarin.Forms.Editor.Text) a uma propriedade `string` pública em um objeto de *origem*. O diagrama a seguir ilustra essa relação de associação:

![](deepdive-images/data-binding.png "Data Binding")

O principal benefício da vinculação de dados é que você não precisa se preocupar sobre a sincronização de dados entre suas exibições e a fonte de dados. As alterações no objeto de *origem* são enviadas automaticamente para o objeto de *destino* nos bastidores pela estrutura de associação, enquanto as alterações no objeto de destino podem ser opcionalmente enviadas de volta para o objeto de *origem*.

Estabelecer a vinculação de dados é um processo de duas etapas:

- A propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do objeto de *destino* deve ser definida como a *origem*.
- Uma associação deve ser estabelecida entre o *destino* e a *origem*. Em XAML, isso é obtido usando a extensão de marcação [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

No aplicativo Notes, o alvo de associação é o [`Editor`](xref:Xamarin.Forms.Editor), que exibe uma observação, enquanto a instância de `Note` definida como a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` é a origem da associação.

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

No método `OnNoteAddedClicked`, que é executado quando uma nova observação é adicionada ao aplicativo, o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` é definido como uma nova instância de `Note`. No método `OnListViewItemSelected`, que é executado quando uma nota existente é selecionada no [`ListView`](xref:Xamarin.Forms.ListView), o `BindingContext` da `NoteEntryPage` é definido como a instância da `Note` selecionada, que é acessada por meio da propriedade [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem).

> [!IMPORTANT]
> Embora a propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada objeto de *destino* possa ser definida individualmente, isso não é necessário. `BindingContext` é uma propriedade especial que é herdada por todos os seus filhos. Portanto, quando o `BindingContext` no [`ContentPage`](xref:Xamarin.Forms.ContentPage) é definido como uma instância de `Note`, todos os filhos do `ContentPage` têm o mesmo `BindingContext` e podem associar-se a propriedades públicas do objeto `Note`.

O [`Editor`](xref:Xamarin.Forms.Editor) na `NoteEntryPage` é associado à propriedade `Text` do objeto `Note`:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Uma associação entre a propriedade [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) e a propriedade `Text` do objeto de *origem* é estabelecida. As alterações feitas no `Editor` serão propagadas automaticamente para o objeto `Note`. De modo semelhante, quando as alterações são feitas com a propriedade `Note.Text`, o mecanismo de associação do Xamarin.Forms também atualiza o conteúdo do `Editor`. Isso é conhecido como uma *associação bidirecional*.

Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Estilo

Os aplicativos Xamarin.Forms geralmente contêm vários elementos visuais com uma aparência idêntica. Definir a aparência de cada elemento visual pode ser repetitivo e propenso a erros. Em vez disso, é possível criar estilos para definir a aparência e, em seguida, aplicá-los aos elementos visuais necessários.

A classe [`Style`](xref:Xamarin.Forms.Style) agrupa uma coleção de valores de propriedade em um objeto que pode ser aplicado a várias instâncias de elementos visuais. Os estilos são armazenados em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), seja no nível do aplicativo, no nível da página ou no nível de exibição. Escolher em que local definir um `Style` afeta o local em que ele pode ser usado:

- As instâncias [`Style`](xref:Xamarin.Forms.Style) definidas no nível do aplicativo podem ser aplicadas em todo o aplicativo.
- As instâncias [`Style`](xref:Xamarin.Forms.Style) definidas no nível da página podem ser aplicadas a ela e seus filhos.
- As instâncias [`Style`](xref:Xamarin.Forms.Style) definidas no nível da exibição podem ser aplicadas a ela e seus filhos.

> [!IMPORTANT]
> Todos os estilos usados em todo o aplicativo são armazenados no dicionário de recursos do aplicativo para evitar duplicação. No entanto, o XAML específico de uma página não deve ser incluído no dicionário de recursos do aplicativo, já que os recursos serão analisados na inicialização do aplicativo, e não quando exigido por uma página.

Cada instância de [`Style`](xref:Xamarin.Forms.Style) contém uma coleção de um ou mais objetos [`Setter`](xref:Xamarin.Forms.Setter), com cada `Setter` tendo [`Property`](xref:Xamarin.Forms.Setter.Property) e [`Value`](xref:Xamarin.Forms.Setter.Value). O `Property` é o nome da propriedade vinculável do elemento ao qual o estilo é aplicado e o `Value` é o valor aplicado à propriedade. O exemplo de código a seguir mostra um estilo de `NoteEntryPage`:

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

Esse estilo é aplicado a qualquer instância de [`Editor`](xref:Xamarin.Forms.Editor) na página.

Ao criar um [`Style`](xref:Xamarin.Forms.Style), a propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) é sempre necessária.

> [!NOTE]
> O estilo de um aplicativo Xamarin.Forms é tradicionalmente realizado com o uso de estilos XAML. No entanto, o Xamarin.Forms também é compatível com o estilo de elementos visuais usando CSS (folhas de estilos em cascata). Para obter mais informações, consulte [Aplicar estilo a aplicativos do Xamarin.Forms usando as CSS (folhas de estilos em cascata)](~/xamarin-forms/user-interface/styles/css/index.md).

Confira mais informações sobre os estilos XAML em [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos do XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

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

Esse [`Style`](xref:Xamarin.Forms.Style) define diferentes valores [`Color`](xref:Xamarin.Forms.Color) para as propriedades [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) e [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), dependendo da plataforma que está sendo usada.

Para obter mais informações sobre extensões de marcação XAML, consulte [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Para obter informações sobre a extensão de marcação de `OnPlatform`, consulte [Extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Teste e implantação

Tanto o Visual Studio para Mac quanto o Visual Studio oferecem várias opções para testar e implantar um aplicativo. Depurar aplicativos é uma parte comum do ciclo de vida de desenvolvimento de aplicativo e ajuda a diagnosticar problemas de código. Para saber mais, consulte [Definir um ponto de interrupção](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Percorrer o código](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e [Enviar as informações para a janela de log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Simuladores são um bom lugar para começar a implantar e testar um aplicativo e eles apresentam recursos úteis para testar aplicativos. No entanto, os usuários não consumirão o aplicativo final em um simulador, assim, os aplicativos devem ser testados em dispositivos reais antecipadamente e com frequência. Para saber mais sobre o provisionamento de dispositivo iOS, consulte [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md). Para saber mais sobre o provisionamento de dispositivo Android, consulte [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Próximas etapas

Este aprofundamento examinou os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms. As próximas etapas sugeridas incluem ler sobre as seguintes funcionalidades:

- Há quatro grupos de controle principais usados para criar a interface do usuário de um aplicativo Xamarin.Forms. Para obter mais informações, confira [Referência de controles](~/xamarin-forms/user-interface/controls/index.md).
- A associação de dados é uma técnica para vincular propriedades de dois objetos para que as alterações em uma propriedade sejam automaticamente refletidas na outra propriedade. Para obter mais informações, confira [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- O Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo de página sendo usado. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Estilos ajudam a reduzir as marcações repetitivas e permitem que a aparência de aplicativos seja alterada com mais facilidade. Para obter mais informações, confira [Definindo o estilo de aplicativos Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- As extensões de marcação XAML estendem a eficiência e a flexibilidade do XAML permitindo que os atributos do elemento sejam definidos de fontes que não sejam cadeias de caracteres de texto literal. Para obter mais informações, confira [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Os modelos de dados fornecem a capacidade de definir a apresentação de dados em exibições com suporte. Para saber mais, veja [Modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Cada página, layout e exibição é renderizado diferentemente em cada plataforma usando uma classe `Renderer`, que por sua vez cria um controle nativo, organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado. Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Efeitos também permitem que os controles nativos em cada plataforma sejam personalizados. Os efeitos são criados em projetos específicos da plataforma aplicando subclasses à classe [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) e consumidos anexando-os a um controle Xamarin.Forms apropriado. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).
- O código compartilhado pode acessar a funcionalidade nativa por meio da classe [`DependencyService`](xref:Xamarin.Forms.DependencyService). Para obter mais informações, consulte [Acessar recursos nativos com DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Como alternativa, veja [_Criando aplicativos móveis com Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md), um livro de Charles Petzold, é uma ótima opção para saber mais sobre o Xamarin.Forms. O livro está disponível em PDF ou em vários formatos de livro eletrônico.

## <a name="related-links"></a>Links relacionados

- [Linguagem XAML](~/xamarin-forms/xaml/index.yml)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Referência de controles](~/xamarin-forms/user-interface/controls/index.md)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Amostras do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Exemplos de Introdução](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Referência da API Xamarin.Forms](xref:Xamarin.Forms)
- [Aprendizado autodirigido gratuito (vídeo)](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
