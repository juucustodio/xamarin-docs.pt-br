---
title: Criar um Aplicativo do Shell do Xamarin.Forms
description: O processo para a criação de um aplicativo Shell do Xamarin.Forms é criar um arquivo XAML que seja uma subclasse da classe do Shell, definir a propriedade MainPage da classe App do aplicativo para o objeto Shell na subclasse e descrever a hierarquia visual do aplicativo na classe Shell na subclasse.
ms.prod: xamarin
ms.assetid: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
ms.openlocfilehash: eec20ff6ceb4aee7e8fde59992576899690616c3
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739302"
---
# <a name="create-a-xamarinforms-shell-application"></a>Criar um Aplicativo do Shell do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

O processo para criar um aplicativo do Shell do Xamarin.Forms é o seguinte:

1. Crie um novo aplicativo do Xamarin.Forms ou carregue um aplicativo existente que você deseja converter em um aplicativo do Shell.
1. Adicione um arquivo XAML ao projeto de código compartilhado, que seja uma subclasse da classe `Shell`. Confira mais informações em [Criar subclasse da classe Shell](#subclass-the-shell-class).
1. Defina a propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage) da classe `App` do aplicativo para o objeto `Shell` na subclasse. Confira mais informações em [Inicializar o aplicativo do Shell](#bootstrap-the-shell-application).
1. Descreva a hierarquia visual do aplicativo na classe `Shell` na subclasse. Confira mais informações em [Descrever a hierarquia visual do aplicativo](#describe-the-visual-hierarchy-of-the-application).

## <a name="subclass-the-shell-class"></a>Criar subclasse da classe Shell

A primeira etapa na criação de um aplicativo do Shell do Xamarin.Forms é adicionar um arquivo XAML ao projeto de código compartilhado que pode criar subclasses da classe `Shell`. Este arquivo pode ter qualquer nome, mas é recomendado usar **AppShell**. O exemplo de código a seguir mostra um arquivo **AppShell.xaml** recém-criado:

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

Depois de criar o arquivo XAML que cria a subclasse do objeto `Shell`, a propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage) da classe `App` deve ser definida para o objeto `Shell` na subclasse:

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

A etapa final na criação de um aplicativo do Shell do Xamarin.Forms é descrever a hierarquia visual do aplicativo, na classe `Shell` na subclasse. Uma classe `Shell` na subclasse consiste em três principais objetos hierárquicos:

- `FlyoutItem` ou `TabBar`. Um `FlyoutItem` representa um ou mais itens no submenu e deve ser usado quando o padrão de navegação para o aplicativo inclui um submenu. Um `TabBar` representa a barra de guias inferior e deve ser usado quando o padrão de navegação para o aplicativo começa com as guias inferiores. Cada objeto `FlyoutItem` ou objeto `TabBar` é um filho do objeto `Shell`.
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

[![Captura de tela de um aplicativo Shell no iOS e Android](create-images/cats.png "Aplicativo Shell")](create-images/cats-large.png#lightbox "Aplicativo Shell")

Pressionar o ícone de hambúrguer ou passar o dedo da esquerda para a direita exibirá o menu suspenso:

[![Captura de tela de um submenu do Shell no iOS e no Android](create-images/flyout-reduced.png "Submenu Shell")](create-images/flyout-reduced-large.png#lightbox "Submenu Shell")

> [!IMPORTANT]
> Em um aplicativo do Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que é filho de um objeto `ShellContent` é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [Carregamento de páginas eficiente](tabs.md#efficient-page-loading) no guia sobre as [abas do Shell do Xamarin.Forms](tabs.md).

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
