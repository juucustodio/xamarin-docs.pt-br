---
title: Trabalhando com botões tvOS no Xamarin
description: Este documento descreve como trabalhar com botões em um aplicativo tvOS criado com o Xamarin. Ele aborda como trabalhar com botões no código e em storyboards e examina como estilizar um botão.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/07/2017
ms.openlocfilehash: 559944e5ae168fd3b45c4d25a86705c5032b2e04
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030484"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Trabalhando com botões tvOS no Xamarin

Use uma instância da classe `UIButton` para criar um botão com foco e selecionável em uma janela tvOS. Quando o usuário seleciona um botão, ele envia uma mensagem de ação para o objeto de destino, permitindo que seu aplicativo Xamarin. tvOS responda à entrada do usuário.

[![](buttons-images/buttons01.png "Example buttons")](buttons-images/buttons01.png#lightbox)

Para saber mais sobre como trabalhar com o foco e navegar com o Siri remoto, Confira nossa documentação sobre [como trabalhar com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) e [controladores remotos e Bluetooth do Siri](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="About-Buttons" />

## <a name="about-buttons"></a>Sobre os botões

No tvOS, os botões são usados para ações específicas do aplicativo e podem conter um título, um ícone ou ambos. À medida que o usuário navega pela interface do usuário do aplicativo usando o [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), o foco muda para o botão fornecido, fazendo com que ele altere o texto e as cores do plano de fundo. Uma sombra também é aplicada ao botão adicionando um efeito 3D, o que parece aumentar acima do restante da interface do usuário.

[![](buttons-images/buttons01.png "Example buttons")](buttons-images/buttons01.png#lightbox)

A Apple tem as seguintes sugestões para trabalhar com botões:

- **Use um título ou um ícone** , enquanto que um ícone e um título podem ser incluídos em um botão, o espaço é limitado, portanto, tente não combinar ambos.
- **Marque claramente os botões destrutivos** – se o botão executar uma ação destrutiva (como excluir um arquivo), marque-o claramente como usando o texto e/ou o ícone. As ações destrutivas sempre devem apresentar um [alerta](~/ios/tvos/user-interface/alerts.md) solicitando que o usuário confinasse a ação.
- **Não use os botões voltar** – o botão de menu no Siri remoto é usado para retornar à tela anterior. A única exceção a essa regra é para compras no aplicativo ou ações destrutivas em que um botão de **cancelamento** deve ser exibido.

Para obter mais informações sobre como trabalhar com foco e navegação, consulte nossa documentação [trabalhando com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) .

<a name="Button-Icons" />

### <a name="button-icons"></a>Ícones de botão

A Apple sugere que você use imagens simples e altamente reconhecíveis para seus ícones de botão. Ícones muito complexos são difíceis de reconhecer em uma tela de TV na sala em um sofá, portanto, tente usar a representação mais simples possível para obter a ideia. Sempre que possível, use imagens padrão e bem conhecidas para ícones (como uma lupa para pesquisa).

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botão

A Apple tem as seguintes sugestões ao criar os títulos para seus botões:

- **Mostrar texto descritivo abaixo dos botões ícones** – sempre que possível, coloque um texto descritivo claro abaixo dos botões ícone para obter mais informações sobre o objetivo do botão.
- **Use verbos ou frases de verbo para o título** – declare claramente a ação que ocorrerá quando o usuário clicar no botão.
- **Use a capitalização no estilo de título** -com a exceção de artigos, conjunçãos ou preposições (quatro letras ou menos), todas as palavras do título do botão devem ser colocadas em maiúsculas.
- **Use um título curto e ponto-a** -passo, use o argumentação mais curto possível para descrever a ação do botão.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Botões e storyboards

A maneira mais fácil de trabalhar com botões em um aplicativo Xamarin. tvOS é adicioná-los à interface do usuário do aplicativo usando o Xamarin Designer para iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **Gerenciador de soluções**, clique duas vezes no arquivo de `Main.storyboard` e abra-o para edição.
1. Arraste um **botão** da **biblioteca** e solte-o na exibição: 

    [![](buttons-images/storyboard01.png "A button")](buttons-images/storyboard01.png#lightbox)
1. No **Gerenciador de propriedades**, você pode ajustar várias propriedades do botão, como seu **título** e a **cor do texto**: 

    [![](buttons-images/storyboard02.png "Button properties")](buttons-images/storyboard02.png#lightbox)
1. Em seguida, alterne para a **guia eventos** e conecte um **evento** do **botão** e chame-o `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "The Events Tab")](buttons-images/storyboard03.png#lightbox)
1. Você será alternado automaticamente para a exibição `ViewController.cs`, onde poderá posicionar a nova ação em seu código usando as teclas de seta para **cima** e **para baixo** : 

    [![](buttons-images/storyboard04.png "Placing a new Action in code")](buttons-images/storyboard04.png#lightbox)
1. Pressione **Enter** para selecionar o local: 

    [![](buttons-images/storyboard05.png "The code editor")](buttons-images/storyboard05.png#lightbox)
1. Salve as alterações em todos os arquivos.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes no arquivo de `Main.storyboard` e abra-o para edição.
1. Arraste um **botão** da **biblioteca** e solte-o na exibição: 

    [![](buttons-images/storyboard01vs.png "A button")](buttons-images/storyboard01vs.png#lightbox)
1. No **Gerenciador de propriedades**, você pode ajustar várias propriedades do botão, como seu **título** e a **cor do texto**: 

    [![](buttons-images/storyboard02vs.png "The Properties Explorer")](buttons-images/storyboard02vs.png#lightbox)
1. Em seguida, alterne para a **guia eventos** e conecte um **evento** do **botão** e chame-o `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "The Events Tab")](buttons-images/storyboard03vs.png#lightbox)
1. Salve as alterações em todos os arquivos.

Edite o arquivo do controlador de exibição (exemplo `ViewController.cs`) e adicione o seguinte código para manipular o botão que está sendo selecionado:

```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

Desde que a propriedade `Enabled` de um botão seja `true` e não seja coberta por outro controle ou exibição, ela poderá se tornar o item em foco usando o Siri remoto. Se o usuário selecionar o botão e clicar na superfície de toque, a ação `ButtonPressed` definida acima será executada.

> [!IMPORTANT]
> Embora seja possível atribuir ações como `TouchUpInside` a um `UIButton` no designer do iOS ao criar um **manipulador de eventos**, ele nunca será chamado porque a Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar o **tipo de ação** padrão ao criar **ações** para elementos da interface do usuário do tvOS.

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Botões e código

Opcionalmente, uma `UIButton` pode ser criada no C# código e adicionada à exibição do aplicativo tvOS. Por exemplo:

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

Ao criar um novo `UIButton` no código, você especifica seu `UIButtonType` como um dos seguintes:

- **Sistema** -esse é o tipo padrão de botão apresentado por tvOS e é o tipo que você usará com mais frequência.
- **DetailDisclosure** -apresenta um tipo de botão "desligar" usado para ocultar ou mostrar informações detalhadas.
- **InfoDark** -um botão de informações detalhados escuros exibiu um "i" em um círculo.
- **InfoLight** -um botão de informações detalhadas claros exibiu um "i" em um círculo.
- **AddContact** – exiba o botão como um botão Adicionar contato.
- **Personalizado** – permite que você personalize várias características do botão.

Em seguida, defina o tamanho da tela e o local do botão. Exemplo:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Em seguida, defina o título para o botão. `UIButtons` são diferentes do que a maioria dos controles de `UIKit`, pois eles têm um estado para que você não possa simplesmente alterar o título, você precisa alterá-lo para um determinado `UIControlState`. Por exemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Em seguida, use o evento `AllEvents` para ver quando o usuário clicou no botão. Exemplo:

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Por fim, você adiciona o botão à exibição para exibi-lo:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Embora seja possível atribuir ações como `TouchUpInside` a um `UIButton`, ela nunca será chamada porque a Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar eventos como os próprios **eventos** ou **PrimaryActionTriggered**.

<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Estilizando um botão

o tvOS fornece várias propriedades de uma `UIButton` que podem ser usadas para fornecer seu título e estilizar o estilo com coisas como cores de plano de fundo e imagens.

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botão

Como vimos acima, `UIButtons` são diferentes do que a maioria dos controles de `UIKit`, pois eles têm um estado para que você não possa simplesmente alterar o título, você precisa alterá-lo para um determinado `UIControlState`. Por exemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Você pode definir a cor do título para o botão usando o método `SetTitleColor`. Por exemplo:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

E você pode ajustar a sombra do título usando o `SetTitleShadowColor`. Por exemplo:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Você pode definir a sombra do título para mudar de *baixo para baixo* para *relevo* quando o botão for realçado usando o seguinte código:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Além disso, você pode usar texto atribuído como o título do botão. Por exemplo:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Imagens de botão

Uma `UIButton` pode ter uma imagem anexada a ela e pode usar uma imagem como seu plano de fundo.

Para definir a imagem de plano de fundo de um botão para um determinado `UIControlState`, use o seguinte código:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Defina a propriedade `AdjustsImageWhenHiglighted` como `true` para desenhar a imagem mais clara quando o botão for realçado (esse é o padrão). Defina a propriedade `AdjustsImageWhenDisabled` como `true` para desenhar a imagem mais escura quando o botão estiver desabilitado (novamente, esse é o padrão).

Para definir a imagem exibida no botão, use o seguinte código:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Use a propriedade `TintColor` para definir uma tonalidade de cor que é aplicada ao título e à imagem do botão. Para botões do tipo de `Custom`, essa propriedade não tem efeito, você deve implementar o comportamento de `TintColor` você mesmo.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com botões dentro de um aplicativo Xamarin. tvOS. Ele mostrou como trabalhar com botões no designer do iOS e como criar botões no C# código. Por fim, ele mostrou como modificar o título de um botão e alterar seu estilo e sua aparência.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
