---
title: Trabalhando com botões tvOS no Xamarin
description: Este documento descreve como trabalhar com botões em um aplicativo tvOS criado com o Xamarin. Ele aborda como trabalhar com botões no código e em storyboards e examina como estilizar um botão.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/07/2017
ms.openlocfilehash: 07d48a38751680f5e7227682d0639b77b3a3627b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436152"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Trabalhando com botões tvOS no Xamarin

Use uma instância da `UIButton` classe para criar um botão de foco e selecionável em uma janela tvOS. Quando o usuário seleciona um botão, ele envia uma mensagem de ação para o objeto de destino, permitindo que seu aplicativo Xamarin. tvOS responda à entrada do usuário.

[![Botões de exemplo](buttons-images/buttons01.png)](buttons-images/buttons01.png#lightbox)

Para saber mais sobre como trabalhar com o foco e navegar com o Siri remoto, Confira nossa documentação sobre [como trabalhar com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) e [controladores remotos e Bluetooth do Siri](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="About-Buttons"></a>

## <a name="about-buttons"></a>Sobre os botões

No tvOS, os botões são usados para ações específicas do aplicativo e podem conter um título, um ícone ou ambos. À medida que o usuário navega pela interface do usuário do aplicativo usando o [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), o foco muda para o botão fornecido, fazendo com que ele altere o texto e as cores do plano de fundo. Uma sombra também é aplicada ao botão adicionando um efeito 3D, o que parece aumentar acima do restante da interface do usuário.

[![Botões de exemplo](buttons-images/buttons01.png)](buttons-images/buttons01.png#lightbox)

A Apple tem as seguintes sugestões para trabalhar com botões:

- **Use um título ou um ícone** , enquanto que um ícone e um título podem ser incluídos em um botão, o espaço é limitado, portanto, tente não combinar ambos.
- **Marque claramente os botões destrutivos** – se o botão executar uma ação destrutiva (como excluir um arquivo), marque-o claramente como usando o texto e/ou o ícone. As ações destrutivas sempre devem apresentar um [alerta](~/ios/tvos/user-interface/alerts.md) solicitando que o usuário confinasse a ação.
- **Não use os botões voltar** – o botão de menu no Siri remoto é usado para retornar à tela anterior. A única exceção a essa regra é para compras no aplicativo ou ações destrutivas em que um botão de **cancelamento** deve ser exibido.

Para obter mais informações sobre como trabalhar com foco e navegação, consulte nossa documentação [trabalhando com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) .

<a name="Button-Icons"></a>

### <a name="button-icons"></a>Ícones de botão

A Apple sugere que você use imagens simples e altamente reconhecíveis para seus ícones de botão. Ícones muito complexos são difíceis de reconhecer em uma tela de TV na sala em um sofá, portanto, tente usar a representação mais simples possível para obter a ideia. Sempre que possível, use imagens padrão e bem conhecidas para ícones (como uma lupa para pesquisa).

<a name="Button-Titles"></a>

### <a name="button-titles"></a>Títulos de botão

A Apple tem as seguintes sugestões ao criar os títulos para seus botões:

- **Mostrar texto descritivo abaixo dos botões ícones** – sempre que possível, coloque um texto descritivo claro abaixo dos botões ícone para obter mais informações sobre o objetivo do botão.
- **Use verbos ou frases de verbo para o título** – declare claramente a ação que ocorrerá quando o usuário clicar no botão.
- **Use a capitalização no estilo de título** -com a exceção de artigos, conjunçãos ou preposições (quatro letras ou menos), todas as palavras do título do botão devem ser colocadas em maiúsculas.
- **Use um título curto e ponto-a** -passo, use o argumentação mais curto possível para descrever a ação do botão.

<a name="Buttons-and-Storyboards"></a>

## <a name="buttons-and-storyboards"></a>Botões e storyboards

A maneira mais fácil de trabalhar com botões em um aplicativo Xamarin. tvOS é adicioná-los à interface do usuário do aplicativo usando o Xamarin Designer para iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Arraste um **botão** da **biblioteca** e solte-o na exibição: 

    [![Botão A](buttons-images/storyboard01.png)](buttons-images/storyboard01.png#lightbox)
1. No **Gerenciador de propriedades**, você pode ajustar várias propriedades do botão, como seu **título** e a **cor do texto**: 

    [![Propriedades do botão](buttons-images/storyboard02.png)](buttons-images/storyboard02.png#lightbox)
1. Em seguida, alterne para a **guia eventos** e conecte um **evento** do **botão** e chame-o `ButtonPressed` : 

    [![A guia eventos](buttons-images/storyboard03.png)](buttons-images/storyboard03.png#lightbox)
1. Você será alternado automaticamente para a `ViewController.cs` exibição em que você pode posicionar a nova ação em seu código usando as teclas de seta para **cima** e **para baixo** : 

    [![Colocando uma nova ação no código](buttons-images/storyboard04.png)](buttons-images/storyboard04.png#lightbox)
1. Pressione **Enter** para selecionar o local: 

    [![O editor de código](buttons-images/storyboard05.png)](buttons-images/storyboard05.png#lightbox)
1. Salve as alterações em todos os arquivos.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Arraste um **botão** da **biblioteca** e solte-o na exibição: 

    [![Botão A](buttons-images/storyboard01vs.png)](buttons-images/storyboard01vs.png#lightbox)
1. No **Gerenciador de propriedades**, você pode ajustar várias propriedades do botão, como seu **título** e a **cor do texto**: 

    [![O Gerenciador de propriedades](buttons-images/storyboard02vs.png)](buttons-images/storyboard02vs.png#lightbox)
1. Em seguida, alterne para a **guia eventos** e conecte um **evento** do **botão** e chame-o `ButtonPressed` : 

    [![A guia eventos](buttons-images/storyboard03vs.png)](buttons-images/storyboard03vs.png#lightbox)
1. Salve as alterações em todos os arquivos.

Edite o arquivo do controlador de exibição (exemplo `ViewController.cs` ) e adicione o seguinte código para manipular o botão que está sendo selecionado:

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

Desde que a propriedade de um botão `Enabled` seja `true` e não seja coberta por outro controle ou exibição, ela pode se tornar o item em foco usando o Siri remoto. Se o usuário selecionar o botão e clicar na superfície de toque, a `ButtonPressed` ação definida acima será executada.

> [!IMPORTANT]
> Embora seja possível atribuir ações como `TouchUpInside` a um `UIButton` no designer do Ios ao criar um **manipulador de eventos**, ele nunca será chamado porque a Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar o **tipo de ação** padrão ao criar **ações** para elementos da interface do usuário do tvOS.

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code"></a>

## <a name="buttons-and-code"></a>Botões e código

Opcionalmente, um `UIButton` pode ser criado em código C# e adicionado à exibição do aplicativo tvOS. Por exemplo:

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

Quando você cria um novo `UIButton` no código, você especifica seu `UIButtonType` como um dos seguintes:

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

Em seguida, defina o título para o botão. `UIButtons` são diferentes da maioria dos `UIKit` controles em que têm um estado, de modo que você não pode simplesmente alterar o título, você precisa alterá-lo para um determinado `UIControlState` . Por exemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Em seguida, use o `AllEvents` evento para ver quando o usuário clicou no botão. Exemplo:

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
> Embora seja possível atribuir ações como `TouchUpInside` a a `UIButton` , ela nunca será chamada porque a Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar eventos como os próprios **eventos** ou **PrimaryActionTriggered**.

<a name="Styling-a-Button"></a>

## <a name="styling-a-button"></a>Estilizando um botão

o tvOS fornece várias propriedades de um `UIButton` que podem ser usadas para fornecer seu título e estilizar a ti com coisas como cores de plano de fundo e imagens.

<a name="Button-Titles"></a>

### <a name="button-titles"></a>Títulos de botão

Como vimos acima, `UIButtons` são diferentes da maioria dos `UIKit` controles, pois eles têm um estado para que você não possa simplesmente alterar o título, você precisa alterá-lo para um determinado `UIControlState` . Por exemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Você pode definir a cor do título para o botão usando o `SetTitleColor` método. Por exemplo:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

E você pode ajustar a sombra do título usando o `SetTitleShadowColor` . Por exemplo:

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

Um `UIButton` pode ter uma imagem anexada e pode usar uma imagem como seu plano de fundo.

Para definir a imagem de plano de fundo de um botão para um determinado `UIControlState` , use o seguinte código:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Defina a `AdjustsImageWhenHiglighted` propriedade como `true` para desenhar a imagem mais clara quando o botão for realçado (esse é o padrão). Defina a `AdjustsImageWhenDisabled` propriedade como `true` para desenhar a imagem mais escura quando o botão estiver desabilitado (novamente, esse é o padrão).

Para definir a imagem exibida no botão, use o seguinte código:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Use a `TintColor` propriedade para definir uma tonalidade de cor que é aplicada ao título e à imagem do botão. Para botões do `Custom` tipo, essa propriedade não tem efeito, você mesmo deve implementar o `TintColor` comportamento.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com botões dentro de um aplicativo Xamarin. tvOS. Ele mostrou como trabalhar com botões no designer do iOS e como criar botões em código C#. Por fim, ele mostrou como modificar o título de um botão e alterar seu estilo e sua aparência.

## <a name="related-links"></a>Links Relacionados

- [Exemplos do tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)