---
title: Introdução ao Xamarin.Forms
description: O Shell do Xamarin.Forms fornece os recursos fundamentais necessários para a maioria dos aplicativos, incluindo uma experiência de navegação comum para o usuário, um esquema de navegação baseada em URI e um manipulador de pesquisa integrado.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f6662f20485c6671edcb5a1654569cdd8498607e
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005203"
---
# <a name="xamarinforms-shell"></a>Shell do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

O Shell do Xamarin.Forms reduz a complexidade do desenvolvimento de aplicativos móveis, fornecendo os recursos fundamentais que a maioria dos aplicativos móveis exige, incluindo:

- Um único lugar para descrever a hierarquia visual do aplicativo.
- Uma experiência de navegação comum para o usuário.
- Um esquema de navegação baseada em URI que permite a navegação para qualquer página no aplicativo.
- Um manipulador de pesquisa integrado.

Além disso, o aplicativo Shell tem a vantagem de aumentar a velocidade de renderização e reduzir o consumo de memória.

> [!IMPORTANT]
> O Shell do Xamarin.Forms só está disponível no iOS e no Android. Os aplicativos iOS e Android existentes podem adotar o Shell e beneficiar-se imediatamente das melhorias de navegação, desempenho e extensibilidade.

## <a name="shell-navigation-experience"></a>Experiência de navegação do Shell

O Shell fornece uma experiência de navegação obstinada, com base em guias e submenus. O nível superior da navegação em um aplicativo do Shell é um submenu:

[![Captura de tela de um submenu do Shell no iOS e no Android](introduction-images/flyout.png "Submenu Shell")](introduction-images/flyout-large.png#lightbox "Submenu Shell")

Selecionar um item de submenu faz com que a guia inferior que representa o item seja selecionada e exibida:

[![Captura de tela das guias inferiores no Shell no iOS e no Android](introduction-images/monkeys.png "Guias inferiores do Shell")](introduction-images/monkeys-large.png#lightbox "Guias inferiores do Shell")

> [!NOTE]
> Quando o submenu está fechado, a barra de guias inferior é considerada o nível principal da navegação no aplicativo.

Cada guia exibe um [`ContentPage`](xref:Xamarin.Forms.ContentPage). No entanto, se uma guia inferior contiver mais de uma página, será possível navegar pelas páginas por meio da barra de guias superior:

[![Captura de tela das guias superiores no Shell no iOS e Android](introduction-images/cats.png "Guias superiores no Shell")](introduction-images/cats-large.png#lightbox "Guias superiores no Shell")

Dentro de cada guia, é possível navegar para objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage) adicionais:

[![Captura de tela da navegação de página no Shell no iOS e Android](introduction-images/cat-details.png "Navegação no aplicativo Shell")](introduction-images/cat-details-large.png#lightbox "Navegação no aplicativo Shell")

## <a name="subclassing-the-shell-class"></a>Criar subclasses da classe Shell

Um objeto `Shell` na subclasse descreve a hierarquia visual de um aplicativo Shell e consiste em três principais objetos hierárquicos:

- `FlyoutItem`, que representa um ou mais itens no submenu. Cada objeto `FlyoutItem` é um filho do objeto `Shell`.
- `Tab`, que representa o conteúdo agrupado, navegável pelas guias inferiores. Cada objeto `Tab` é um filho de um objeto `FlyoutItem`.
- `ShellContent`, que representa os objetos `ContentPage` em seu aplicativo. Cada objeto `ShellContent` é um filho de um objeto `Tab`. Quando mais de um objeto `ShellContent` estiver presente em um `Tab`, os objetos poderão ser navegados por meio das guias superiores.

Nenhum desses objetos representa uma interface do usuário, mas sim a organização da hierarquia visual do aplicativo. O Shell usará esses objetos e produzirá a interface de navegação do conteúdo para o usuário.

> [!NOTE]
> A classe `FlyoutItem` é um alias da classe `ShellItem`, e a classe `Tab` é um alias da classe `ShellSection`. Esses aliases são projetados para tornar a API mais amigável para utilização.

O XAML a seguir mostra um exemplo de um objeto `Shell` na subclasse:

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

Esse XAML cria e exibe a `CatsPage`, pois esse é o primeiro item do conteúdo declarado no objeto `Shell` na subclasse:

[![Captura de tela de um aplicativo Shell no iOS e Android](introduction-images/cats.png "Aplicativo Shell")](introduction-images/cats-large.png#lightbox "Aplicativo Shell")

Pressionar o ícone de hambúrguer ou passar o dedo da esquerda para a direita exibirá o menu suspenso:

[![Captura de tela de um submenu do Shell no iOS e no Android](introduction-images/flyout-reduced.png "Submenu Shell")](introduction-images/flyout-reduced-large.png#lightbox "Submenu Shell")

> [!IMPORTANT]
> Em um aplicativo do Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que é filho de um objeto `ShellContent` é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [Carregamento de páginas eficiente](tabs.md#efficient-page-loading) no guia sobre as [abas do Shell do Xamarin.Forms](tabs.md).

## <a name="bootstrapping-a-shell-application"></a>Inicializando um aplicativo do Shell

Um aplicativo Shell é inicializado com a definição da propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage) da classe `App` como um objeto `Shell` na subclasse:

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

Neste exemplo, a classe `AppShell` é um arquivo XAML que deriva da classe `Shell`, que descreve a hierarquia visual do aplicativo.

## <a name="shell-appearance"></a>Aparência do Shell

A classe `Shell` define as propriedades a seguir que controlam a aparência do aplicativo Shell:

- `BackgroundColor`, do tipo `Color`, uma propriedade anexada que define a cor de fundo no cromado Shell. A cor não preencherá por trás do conteúdo Shell.
- `DisabledColor`, do tipo `Color`, uma propriedade anexada que define a cor da tonalidade do texto e dos ícones que estão desabilitados.
- `ForegroundColor`, do tipo `Color`, uma propriedade anexada que define a cor da tonalidade do texto e dos ícones.
- `TitleColor`, do tipo `Color`, uma propriedade anexada que define a cor usada no título da página atual.
- `UnselectedColor`, do tipo `Color`, uma propriedade anexada que define a cor usada no texto não selecionado e nos ícones no cromado Shell.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

Além disso, essas propriedades podem ser definidas usando as folhas de estilo em cascata (CSS). Saiba mais em [propriedades específicas do Shell do Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="shell-content-layout"></a>Layout do conteúdo do Shell

A classe `Shell` define as propriedades a seguir que controlam o layout do aplicativo Shell:

- `NavBarIsVisible`, do tipo `boolean`, uma propriedade anexada que define se a barra de navegação deve estar visível quando uma página é apresentada. Essa propriedade deve ser definida em uma página, e seu valor padrão é `true`.
- `TabBarIsVisible`, do tipo `bool`, uma propriedade anexada que define se a barra de guia deve estar visível quando a página é apresentada. Essa propriedade deve ser definida em uma página, e seu valor padrão é `true`.
- `TitleView`, do tipo `View`, uma propriedade anexada que define o `TitleView` de uma página. Essa propriedade deve ser definida em uma página.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Propriedades específicas do Shell do Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
