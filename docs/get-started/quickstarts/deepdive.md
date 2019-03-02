---
title: Aprofundamento de início rápido do xamarin. Forms
description: Este artigo examina os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms. Os tópicos abordados incluído a anatomia de um aplicativo Xamarin.Forms, arquitetura e conceitos básicos de aplicativo e a interface do usuário.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: 8e466c80468551f8262cfe49556d9527a147de4c
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197544"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Aprofundamento de início rápido do xamarin. Forms

No [início rápido do xamarin. Forms](~/get-started/index.yml), o aplicativo de anotações foi criado. Este artigo analisa o que foi criado para entender os fundamentos de como os aplicativos Xamarin.Forms funcionam.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introdução ao Visual Studio

O Visual Studio organiza o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste, entre outros. O aplicativo de anotações consiste em uma solução contendo quatro projetos, conforme mostrado na seguinte captura de tela:

![](deepdive-images/vs/solution.png "Gerenciador de soluções do Visual Studio")

Os projetos são:

- Notas – esse projeto é o projeto de biblioteca .NET Standard que contém todos os códigos compartilhados e a interface do usuário compartilhada.
- Notes.Android – esse projeto contém código específico do Android e é o ponto de entrada para o aplicativo Android.
- Notes.iOS – esse projeto contém código específico do iOS e é o ponto de entrada para o aplicativo iOS.
- Notes.UWP – esse projeto contém o código específico da plataforma Universal do Windows (UWP) e é o ponto de entrada para o aplicativo UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um aplicativo xamarin. Forms

Captura de tela a seguir mostra o conteúdo do projeto de biblioteca .NET Standard de notas no Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Conteúdo do projeto do .NET Standard Phoneword")

O projeto tem um nó **Dependências** que contém os nós **NuGet** e **SDK**:

- **NuGet** &ndash; o xamarin. Forms e pacotes do NuGet sqlite-net-pcl que foram adicionados ao projeto.
- **SDK** &ndash; o metapacote `NETStandard.Library` que faz referência ao conjunto completo de pacotes NuGet que definem o .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

O [Visual Studio para Mac](/visualstudio/mac/) segue a prática do Visual Studio de organizar o código em *Soluções* e *Projetos*. Uma solução é um contêiner que pode conter um ou mais projetos. Um projeto pode ser um aplicativo, uma biblioteca com suporte, um aplicativo de teste, entre outros. O aplicativo de anotações consiste em uma solução contendo três projetos, conforme mostrado na seguinte captura de tela:

![](deepdive-images/vsmac/solution.png "Painel de soluções do Visual Studio para Mac")

Os projetos são:

- Notas – esse projeto é o projeto de biblioteca .NET Standard que contém todos os códigos compartilhados e a interface do usuário compartilhada.
- Notes.Android – esse projeto contém código específico do Android e é o ponto de entrada para aplicativos Android.
- Notes.iOS – esse projeto contém código específico do iOS e é o ponto de entrada para aplicativos iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia de um aplicativo xamarin. Forms

Captura de tela a seguir mostra o conteúdo do projeto de biblioteca .NET Standard de notas no Visual Studio para Mac:

![](deepdive-images/vsmac/net-standard-project.png "Conteúdo do projeto de biblioteca do .NET Standard Phoneword")

O projeto tem um nó **Dependências** que contém os nós **NuGet** e **SDK**:

- **NuGet** &ndash; o xamarin. Forms e pacotes do NuGet sqlite-net-pcl que foram adicionados ao projeto.
- **SDK** &ndash; o metapacote `NETStandard.Library` que faz referência ao conjunto completo de pacotes NuGet que definem o .NET Standard.

::: zone-end

O projeto também consiste em vários arquivos:

- **Data\NoteDatabase.CS** – essa classe contém o código para criar o banco de dados, ler dados a partir dele, gravação de dados e exclua dados dele.
- **Models\Note.CS** – essa classe define um `Note` modelo cujas instâncias armazenam dados sobre cada Observação no aplicativo.
- **XAML** – a marcação XAML para a classe `App`, que define um dicionário de recursos para o aplicativo.
- **App.XAML.CS** – o code-behind para a classe `App`, que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma e por processar eventos de ciclo de vida do aplicativo.
- **NotesPage.xaml** – a marcação XAML o para o `NotesPage` classe, que define a interface do usuário para a página exibida quando o aplicativo é iniciado.
- **NotesPage.xaml.cs** – o code-behind para o `NotesPage` classe, que contém a lógica de negócios que é executada quando o usuário interage com a página.
- **NoteEntryPage.xaml** – a marcação XAML o para o `NoteEntryPage` classe, que define a interface do usuário para a página exibida quando o usuário insere uma observação.
- **NoteEntryPage.xaml.cs** – o code-behind para o `NoteEntryPage` classe, que contém a lógica de negócios que é executada quando o usuário interage com a página.

Para saber mais sobre a anatomia de um aplicativo Xamarin.iOS, consulte [Anatomia de um aplicativo Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Para saber mais sobre a anatomia de um aplicativo Xamarin.Android, consulte [Anatomia de um aplicativo Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Conceitos básicos de arquitetura e aplicativo

Um aplicativo Xamarin.Forms é arquitetado da mesma forma que um aplicativo tradicional de plataforma cruzada. O código compartilhado normalmente é colocado em uma biblioteca do .NET Standard e aplicativos específicos de plataforma consomem o código compartilhado. O diagrama a seguir mostra uma visão geral dessa relação para o aplicativo de anotações:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Arquitetura de anotações")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Arquitetura de anotações")

::: zone-end

Para maximizar a reutilização de código de inicialização, os aplicativos Xamarin.Forms têm uma única classe chamada `App`, que é responsável por instanciar a primeira página que será exibida pelo aplicativo em cada plataforma, conforme mostrado no exemplo de código a seguir:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
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

Esse código define a `MainPage` propriedade do `App` de classe para um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instância cujo conteúdo é um `NotesPage` instância. Além disso, o atributo [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) ativa o compilador XAML para que o XAML seja compilado diretamente em linguagem intermediária. Para saber mais, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilação de XAML).

## <a name="launching-the-application-on-each-platform"></a>Iniciar o aplicativo em cada plataforma

### <a name="ios"></a>iOS

Para iniciar a página inicial do xamarin. Forms no iOS, o projeto de Notes.iOS define o `AppDelegate` classe que herda o `FormsApplicationDelegate` classe:

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

Para iniciar a página inicial do xamarin. Forms no Android, o projeto Notes.Android inclui código que cria um `Activity` com o `MainLauncher` atributo, com a atividade herdando a `FormsAppCompatActivity` classe:

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

Isso faz a implementação específica de UWP do Xamarin.Forms ser carregada no aplicativo. A página inicial do xamarin. Forms é iniciada quando o `MainPage` classe:

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
> Aplicativos da plataforma Windows universal podem ser criados com o xamarin. Forms, mas somente usando o Visual Studio no Windows.

::: zone-end

## <a name="user-interface"></a>Interface do usuário

Há quatro grupos de controle principal usados para criar a interface do usuário de um aplicativo xamarin. Forms:

1. **Páginas** – as páginas do Xamarin.Forms representam telas de aplicativos móveis de plataforma cruzada. O aplicativo de anotações usa o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) classe para exibir telas únicas. Para saber mais sobre páginas, consulte [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Páginas do Xamarin.Forms).
1. **Exibições** – as exibições do Xamarin.Forms são os controles exibidos na interface do usuário, como rótulos, botões e caixas de entrada de texto. O aplicativo de anotações concluído usa o [ `ListView` ](xref:Xamarin.Forms.ListView), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Button` ](xref:Xamarin.Forms.Button) modos de exibição. Para saber mais sobre exibições, consulte [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Exibições do Xamarin.Forms).
1. **Layouts** – os layouts do Xamarin.Forms são contêineres usados para compor exibições em estruturas lógicas. O aplicativo de anotações usa o [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) classe para organizar os modos de exibição em uma pilha vertical e o [ `Grid` ](xref:Xamarin.Forms.Grid) classe para organizar botões horizontalmente. Para saber mais sobre layouts, consulte [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layouts do Xamarin.Forms).
1. **Células** – as células do Xamarin.Forms são elementos especializados usados para itens em uma lista e descrevem como cada item em uma lista deve ser desenhado. O aplicativo de anotações usa o [ `TextCell` ](xref:Xamarin.Forms.TextCell) para exibir dois itens para cada linha na lista. Para saber mais sobre células, consulte [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Células do Xamarin.Forms).

No tempo de execução, cada controle será mapeado para seu equivalente nativo, que é o que será renderizado.

### <a name="layout"></a>Layout

O aplicativo de anotações usa o [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) para simplificar o desenvolvimento de aplicativos de plataforma cruzada organizando automaticamente os modos de exibição na tela, independentemente do tamanho da tela. Os elementos filho são posicionados um após o outro, horizontalmente ou verticalmente, na ordem em que foram adicionados. A quantidade de espaço que o `StackLayout` usará dependerá de como as propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) forem definidas, mas por padrão o `StackLayout` tentará usar a tela inteira.

O código XAML a seguir mostra um exemplo do uso de um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) ao layout de `NoteEntryPage`:

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

Por padrão o [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) assume uma orientação vertical. No entanto, ela pode ser alterada para uma orientação horizontal, definindo a [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propriedade para o [ `StackOrientation.Horizontal` ](xref:Xamarin.Forms.StackOrientation.Horizontal) membro de enumeração.

> [!NOTE]
> O tamanho dos modos de exibição pode ser definido por meio de `HeightRequest` e `WidthRequest` propriedades.

Para obter mais informações sobre a classe [`StackLayout`](xref:Xamarin.Forms.StackLayout), veja [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Respondendo à interação do usuário

Um objeto definido em XAML pode acionar um evento que é processado no arquivo code-behind. O seguinte exemplo de código mostra a `OnSaveButtonClicked` método no code-behind para o `NoteEntryPage` classe, que é executado em resposta ao [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) disparo de eventos no *salvar* botão .

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

O `OnSaveButtonClicked` método salva a anotação no banco de dados e navega de volta para a página anterior.

> [!NOTE]
> O arquivo code-behind de uma classe XAML pode acessar um objeto definido em XAML usando o nome atribuído a ele com o atributo `x:Name`. O valor atribuído a esse atributo tem as mesmas regras que variáveis C#, no sentido de que deve começar com uma letra ou um sublinhado e não conter espaços.

A fiação do salvamento botão para o `OnSaveButtonClicked` método ocorre na marcação XAML para o `NoteEntryPage` classe:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Listas

O [ `ListView` ](xref:Xamarin.Forms.ListView) é responsável por exibir uma coleção de itens verticalmente em uma lista. Cada item no `ListView` estará contido em uma única célula.

O seguinte exemplo de código mostra a [ `ListView` ](xref:Xamarin.Forms.ListView) da `NotesPage`:

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

O layout de cada linha na [ `ListView` ](xref:Xamarin.Forms.ListView) é definido dentro de [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) elemento e usa dados de associação para exibir todas as anotações são recuperadas pelo aplicativo. O [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) estiver definida como a fonte de dados em `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Esse código preenche a [ `ListView` ](xref:Xamarin.Forms.ListView) com as observações armazenadas no banco de dados.

Quando uma linha está selecionada na [ `ListView` ](xref:Xamarin.Forms.ListView), o [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento é acionado. Um manipulador de eventos, chamado `OnListViewItemSelected`, é executado quando o evento é acionado:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

O [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento pode acessar o objeto que foi associado à célula por meio de [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriedade.

Para obter mais informações sobre o [ `ListView` ](xref:Xamarin.Forms.ListView) da classe, consulte [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navegação

o Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo do [`Page`](xref:Xamarin.Forms.Page) sendo usado. Para [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) navegação de instâncias pode ser modal ou hierárquica. Para obter informações sobre navegação modal, consulte [páginas modais do xamarin. Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> As classes [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) oferecem experiências de navegação alternativas. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).

No painel de Navegação hierárquica, o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe é usada para navegar por meio de uma pilha de [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) objetos, frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](xref:Xamarin.Forms.Page). Para mover-se de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa. Para retornar à página anterior, o aplicativo removerá o item mais recente, que é a página atual da pilha de navegação, então a nova página de nível mais alto se tornará a página ativa.

A classe `NavigationPage` também adicionará uma barra de navegação à parte superior da página que exibe um título e um botão **Voltar** apropriado para a plataforma, que retornará à página anterior.

A primeira página adicionada a uma pilha de navegação é conhecida como o *raiz* página do aplicativo e o exemplo de código a seguir mostra como isso é feito no aplicativo de anotações:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Todas as instâncias do [`ContentPage`](xref:Xamarin.Forms.ContentPage) têm uma propriedade [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) que expõe métodos para modificar a pilha de páginas. Esses métodos devem ser invocados somente se o aplicativo incluir um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Para navegar até o `NoteEntryPage`, é necessário invocar o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) conforme demonstrado no exemplo de código abaixo:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

Isso faz com que o novo `NoteEntryPage` objeto a ser enviado para a pilha de navegação, em que ele se torna a página ativa.

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela. Para retornar programaticamente à página original, o objeto `NoteEntryPage` deve invocar o método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), conforme demonstrado no exemplo de código abaixo:

```csharp
await Navigation.PopAsync();
```

Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Associação de dados

Vinculação de dados é usada para simplificar como um aplicativo Xamarin.Forms exibe e interage com seus dados. Ela estabelece uma conexão entre a interface do usuário e o aplicativo subjacente. A classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) contém a maior parte da infraestrutura para dar suporte à vinculação de dados.

A vinculação de dados conecta dois objetos, chamados de a *origem* e o *destino*. O objeto *origem* fornece os dados. O objeto *destino* consumirá (e geralmente exibirá) dados do objeto de origem. Por exemplo, um [ `Editor` ](xref:Xamarin.Forms.Editor) (*destino* objeto) normalmente associará sua [ `Text` ](xref:Xamarin.Forms.Editor.Text) propriedade para um público `string` propriedade em um *código-fonte* objeto. O diagrama a seguir ilustra essa relação de associação:

![](deepdive-images/data-binding.png "Associação de dados")

O principal benefício da vinculação de dados é que você não precisa se preocupar sobre a sincronização de dados entre suas exibições e a fonte de dados. As alterações no objeto de *origem* são enviadas automaticamente para o objeto de *destino* nos bastidores pela estrutura de associação, enquanto as alterações no objeto de destino podem ser opcionalmente enviadas de volta para o objeto de *origem*.

Estabelecimento de dados de associação é um processo em duas etapas:

- A propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do objeto de *destino* deve ser definida como a *origem*.
- Uma associação deve ser estabelecida entre o *destino* e a *origem*. Em XAML, isso é obtido usando a extensão de marcação [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

No aplicativo de anotações, o destino da associação é o [ `Editor` ](xref:Xamarin.Forms.Editor) que exibe uma observação, enquanto o `Note` instância definida como o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de `NoteEntryPage` é a associação código-fonte.

O `BindingContext` do `NoteEntryPage` é definido durante a navegação de página, conforme mostrado no exemplo de código a seguir:

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

No `OnNoteAddedClicked` método, que é executado quando uma nova nota é adicionada ao aplicativo, o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) dos `NoteEntryPage` é definido como um novo `Note` instância. No `OnListViewItemSelected` método, que é executado quando uma anotação existente é selecionada na [ `ListView` ](xref:Xamarin.Forms.ListView), o `BindingContext` do `NoteEntryPage` é definido como selecionado `Note` instância, que pode é acessada por meio do [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriedade.

> [!IMPORTANT]
> Embora a propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada objeto de *destino* possa ser definida individualmente, isso não é necessário. `BindingContext` é uma propriedade especial que é herdada por todos os seus filhos. Portanto, quando o `BindingContext` no [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) é definido como um `Note` instância, todos os filhos do `ContentPage` têm o mesmo `BindingContext`e pode associar a propriedades públicas da `Note`objeto.

O [ `Editor` ](xref:Xamarin.Forms.Editor) na `NoteEntryPage` , em seguida, associa ao `Text` propriedade do `Note` objeto:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Uma associação entre a propriedade [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) e a propriedade `Text` do objeto de *origem* é estabelecida. As alterações feitas de `Editor` serão propagadas automaticamente para o `Note` objeto. Da mesma forma, se forem feitas alterações para o `Note.Text` propriedade, o mecanismo de associação do xamarin. Forms também atualizará o conteúdo do `Editor`. Isso é conhecido como uma *associação bidirecional*.

Para obter mais informações sobre associação de dados, consulte [vinculação de dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Estilo

Aplicativos xamarin. Forms geralmente contêm vários elementos visuais que têm uma aparência idêntica. Definir a aparência de cada elemento visual pode ser repetitiva e sujeito a erros. Em vez disso, podem ser criados estilos que definem a aparência e, em seguida, aplicada aos elementos visuais necessários.

O [ `Style` ](xref:Xamarin.Forms.Style) classe agrupa uma coleção de valores de propriedade em um único objeto, em seguida, pode ser aplicado a várias instâncias do elemento visual. Estilos são armazenados em uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), no nível do aplicativo, o nível de página ou o nível de exibição. Escolhendo onde definir um `Style` impactos em que ele pode ser usado:

- [`Style`](xref:Xamarin.Forms.Style) instâncias definidas no nível do aplicativo podem ser aplicadas em todo o aplicativo.
- [`Style`](xref:Xamarin.Forms.Style) instâncias definidas no nível de página podem ser aplicadas para a página e seus filhos.
- [`Style`](xref:Xamarin.Forms.Style) instâncias definidas no nível de exibição podem ser aplicadas para o modo de exibição e seus filhos.

> [!IMPORTANT]
> Todos os estilos que são usados em todo o aplicativo são armazenados no dicionário de recursos do aplicativo para evitar a duplicação. No entanto, o XAML que é específico para uma página não deve ser incluído no dicionário de recursos do aplicativo, como os recursos então serão analisados na inicialização do aplicativo em vez de quando exigido por uma página.

Cada [ `Style` ](xref:Xamarin.Forms.Style) instância contém uma coleção de um ou mais [ `Setter` ](xref:Xamarin.Forms.Setter) objetos, com cada `Setter` tendo um [ `Property` ](xref:Xamarin.Forms.Setter.Property) e uma [`Value`](xref:Xamarin.Forms.Setter.Value). O `Property` é o nome da propriedade associável do elemento de estilo é aplicado, e o `Value` é o valor que é aplicado à propriedade. O exemplo de código a seguir mostra um estilo de `NoteEntryPage`:

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

Esse estilo é aplicado a qualquer [ `Editor` ](xref:Xamarin.Forms.Editor) instâncias na página.

Ao criar uma [ `Style` ](xref:Xamarin.Forms.Style), o [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriedade sempre é necessária.

> [!NOTE]
> Definir o estilo de um aplicativo xamarin. Forms tradicionalmente é realizado usando estilos XAML. No entanto, o xamarin. Forms também dá suporte a elementos visuais de estilo usando folhas de estilo em cascata (CSS). Para obter mais informações, consulte [aplicativos de estilo de xamarin. Forms usando folhas de estilo em cascata (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Para obter mais informações sobre estilos XAML, consulte [estilos aplicativos xamarin. Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>Fornecer estilos específicos de plataforma

O `OnPlatform` extensões de marcação permitem que você personalize a aparência da interface do usuário em uma base por plataforma:

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

Isso [ `Style` ](xref:Xamarin.Forms.Style) define diferentes [ `Color` ](xref:Xamarin.Forms.Color) valores para o [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) e [ `BarTextColor` ](xref:Xamarin.Forms.NavigationPage.BarTextColor) Propriedades de [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), dependendo da plataforma que está sendo usada.

Para obter mais informações sobre extensões de marcação XAML, consulte [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md). Para obter informações sobre o `OnPlatform` extensão de marcação, consulte [extensão de marcação OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Teste e implantação

Tanto o Visual Studio para Mac quanto o Visual Studio oferecem várias opções para testar e implantar um aplicativo. Depurar aplicativos é uma parte comum do ciclo de vida de desenvolvimento de aplicativo e ajuda a diagnosticar problemas de código. Para saber mais, consulte [Definir um ponto de interrupção](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [Percorrer o código](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e [Enviar as informações para a janela de log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

Simuladores são um bom lugar para começar a implantar e testar um aplicativo e eles apresentam recursos úteis para testar aplicativos. No entanto, os usuários não consumirão o aplicativo final em um simulador, assim, os aplicativos devem ser testados em dispositivos reais antecipadamente e com frequência. Para saber mais sobre o provisionamento de dispositivo iOS, consulte [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md). Para saber mais sobre o provisionamento de dispositivo Android, consulte [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

## <a name="next-steps"></a>Próximas etapas

Essa informação aprofundada examinou os conceitos básicos do desenvolvimento de aplicativos usando o xamarin. Forms. As próximas etapas sugeridas incluem ler sobre as seguintes funcionalidades:

- Há quatro grupos de controle principais usados para criar a interface do usuário de um aplicativo Xamarin.Forms. Para obter mais informações, consulte [referência de controles](~/xamarin-forms/user-interface/controls/index.md).
- Associação de dados é uma técnica para vincular as propriedades de dois objetos para que as alterações em uma propriedade são refletidas automaticamente na outra propriedade. Para obter mais informações, consulte [associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin. Forms fornece uma série de experiências de navegação de página diferente, dependendo do tipo de página que está sendo usado. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Estilos de ajudam a reduzir as marcações repetitivas e permitir uma aparência de aplicativos a ser alterado com mais facilidade. Para obter mais informações, consulte [estilos aplicativos do xamarin. Forms](~/xamarin-forms/user-interface/styles/index.md).
- Extensões de marcação XAML estendem o poder e flexibilidade do XAML, permitindo que os atributos do elemento a ser definido de fontes diferentes cadeias de caracteres de texto literal. Para obter mais informações, consulte [extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- Modelos de dados fornecem a capacidade de definir a apresentação dos dados em exibições com suporte. Para saber mais, veja [Modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- Cada página, layout e exibição é renderizado diferentemente em cada plataforma usando uma `Renderer` classe que por sua vez cria um controle nativo, organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado. Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Efeitos também permitem que os controles nativos em cada plataforma sejam personalizados. Os efeitos são criados em projetos específicos da plataforma Subclassificando a [ `PlatformEffect` ](xref:Xamarin.Forms.PlatformEffect`2) de classe e consumidos anexando-os a um controle xamarin. Forms apropriado. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).
- O código compartilhado pode acessar a funcionalidade nativa por meio da classe [`DependencyService`](xref:Xamarin.Forms.DependencyService). Para obter mais informações, consulte [Acessar recursos nativos com DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Como alternativa, veja [_Criando aplicativos móveis com Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md), um livro de Charles Petzold, é uma ótima opção para saber mais sobre o Xamarin.Forms. O livro está disponível em PDF ou em vários formatos de livro eletrônico.

## <a name="related-links"></a>Links relacionados

- [Linguagem de marcação de aplicativo extensível (XAML)](~/xamarin-forms/xaml/index.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Referência de controles](~/xamarin-forms/user-interface/controls/index.md)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Exemplos de Introdução](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/)
- [Referência da API Xamarin.Forms](xref:Xamarin.Forms)
- [Aprendizado autodirigido gratuito (vídeo)](https://university.xamarin.com/self-guided/)
