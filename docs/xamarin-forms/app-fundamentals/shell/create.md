---
Título: "criar um Xamarin.Forms aplicativo de Shell" Descrição: "o processo para criar um Xamarin.Forms aplicativo de Shell é criar um arquivo XAML que crie uma subclasse da classe Shell, defina a propriedade MainPage da classe App do aplicativo para o objeto de Shell de subclasse e, em seguida, descreva a hierarquia visual do aplicativo na classe de Shell de subclasse."
MS. Prod: xamarin MS. AssetID: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 05/24/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="create-a-xamarinforms-shell-application"></a>Criar um Xamarin.Forms aplicativo de Shell

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

O processo para criar um Xamarin.Forms aplicativo de Shell é o seguinte:

1. Crie um novo Xamarin.Forms aplicativo ou carregue um aplicativo existente que você deseja converter em um aplicativo de Shell.
1. Adicione um arquivo XAML ao projeto de código compartilhado, que seja uma subclasse da classe `Shell`. Confira mais informações em [Criar subclasse da classe Shell](#subclass-the-shell-class).
1. Defina a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade da classe do aplicativo `App` para o objeto de subclasse `Shell` . Confira mais informações em [Inicializar o aplicativo do Shell](#bootstrap-the-shell-application).
1. Descreva a hierarquia visual do aplicativo na classe `Shell` na subclasse. Confira mais informações em [Descrever a hierarquia visual do aplicativo](#describe-the-visual-hierarchy-of-the-application).

## <a name="subclass-the-shell-class"></a>Criar subclasse da classe Shell

A primeira etapa na criação de um Xamarin.Forms aplicativo de Shell é adicionar um arquivo XAML ao projeto de código compartilhado que cria uma `Shell` subclasse da classe. Este arquivo pode ter qualquer nome, mas é recomendado usar **AppShell**. O exemplo de código a seguir mostra um arquivo **AppShell.xaml** recém-criado:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell">

</Shell>
```

O exemplo a seguir mostra o arquivo code-behind, **AppShell.xaml.cs**:

```csharp
using Xamarin.Forms;

namespace Xaminals
{
    public partial class AppShell : Shell
    {
        public AppShell()
        {
            InitializeComponent();
        }
    }
}
```

## <a name="bootstrap-the-shell-application"></a>Inicializar um aplicativo do Shell

Depois de criar o arquivo XAML que cria uma subclasse do `Shell` objeto, a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade da `App` classe deve ser definida como o objeto de subclasse `Shell` :

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

Neste exemplo, a classe `AppShell` é um arquivo XAML que deriva da classe `Shell`.

> [!NOTE]
> Durante a criação de um aplicativo do Shell em branco, executá-lo resultará na exibição de um `InvalidOperationException`.

## <a name="describe-the-visual-hierarchy-of-the-application"></a>Descrever a hierarquia visual do aplicativo

A etapa final na criação de um Xamarin.Forms aplicativo de Shell é descrever a hierarquia visual do aplicativo, na classe de subclasse `Shell` . Uma classe `Shell` na subclasse consiste em três principais objetos hierárquicos:

- `FlyoutItem` ou `TabBar` Um `FlyoutItem` representa um ou mais itens no submenu e deve ser usado quando o padrão de navegação para o aplicativo inclui um submenu. Um `TabBar` representa a barra de guias inferior e deve ser usado quando o padrão de navegação para o aplicativo começa com as guias inferiores. Cada objeto `FlyoutItem` ou objeto `TabBar` é um filho do objeto `Shell`.
- `Tab`, que representa o conteúdo agrupado, navegável pelas guias inferiores. Cada objeto `Tab` é um filho de um objeto `FlyoutItem` ou de um objeto `TabBar`.
- `ShellContent`, que representa os objetos `ContentPage` em seu aplicativo. Cada objeto `ShellContent` é um filho de um objeto `Tab`. Quando mais de um objeto `ShellContent` estiver presente em um `Tab`, os objetos poderão ser navegados por meio das guias superiores.

Nenhum desses objetos representa uma interface do usuário, mas sim a organização da hierarquia visual do aplicativo. O Shell usará esses objetos e produzirá a interface de navegação do conteúdo para o usuário.

O XAML a seguir mostra um exemplo de uma classe `Shell` na subclasse:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

Esse XAML cria e exibe a `CatsPage`, pois esse é o primeiro item do conteúdo declarado na classe `Shell` na subclasse:

[![Captura de tela de um aplicativo de Shell, no iOS e no Android](create-images/cats.png "Aplicativo de Shell")](create-images/cats-large.png#lightbox "Aplicativo de Shell")

Pressionar o ícone de hambúrguer ou passar o dedo da esquerda para a direita exibirá o menu suspenso:

[![Captura de tela de um submenu do Shell no iOS e no Android](create-images/flyout-reduced.png "Submenu do Shell")](create-images/flyout-reduced-large.png#lightbox "Submenu do Shell")

> [!IMPORTANT]
> Em um aplicativo de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) um filho de um `ShellContent` objeto é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Para obter mais informações, consulte [carregamento de página eficiente](tabs.md#efficient-page-loading) no guia de [ Xamarin.Forms guias do Shell](tabs.md) .

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
