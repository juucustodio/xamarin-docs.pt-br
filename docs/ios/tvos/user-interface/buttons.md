---
title: Trabalhando com botões de tvOS no Xamarin
description: Este documento descreve como trabalhar com botões em um aplicativo tvOS criado com o Xamarin. Ele discute como trabalhar com botões em código e em storyboards, e ele examina como Estilizar um botão.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/07/2017
ms.openlocfilehash: 6d8fc1daaced24dccead78c4f9d0e5d0959b3755
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116336"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Trabalhando com botões de tvOS no Xamarin

Usar uma instância da `UIButton` classe para criar um botão focalizável selecionável em uma janela de tvOS. Quando o usuário seleciona um botão, ele envia uma mensagem de ação para o objeto de destino permitir responder seu aplicativo tvos o usuário de entrada do.

[![](buttons-images/buttons01.png "Botões de exemplo")](buttons-images/buttons01.png#lightbox)

Para obter mais informações sobre como trabalhar com o foco e navegar com o Siri remoto, consulte nosso [trabalhando com navegação e fogo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

<a name="About-Buttons" />

## <a name="about-buttons"></a>Sobre botões

No tvOS, os botões são usados para ações específicas de aplicativo e podem conter um título, um ícone ou ambos. Quando o usuário navega de Interface do usuário do aplicativo usando o [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), foco muda para o botão determinado, tornando a alterar as cores de plano de fundo e texto. Uma sombra também é aplicada ao botão de adição de um efeito 3D, facilitando aparecer subir o restante da Interface do usuário.

[![](buttons-images/buttons01.png "Botões de exemplo")](buttons-images/buttons01.png#lightbox)

Apple tem as sugestões a seguir para trabalhar com botões:

- **Use um título ou um ícone** – enquanto o ambos os um ícone e um título pode ser incluído em um botão, o espaço é limitado até não tentar combinar dois.
- **Claramente marca botões destrutiva** - se no botão executa um destrutiva ação (como excluir um arquivo), claramente marcá-lo como tal, usando o texto e/ou ícone. Ações destrutivas sempre devem apresentar um [alerta](~/ios/tvos/user-interface/alerts.md) perguntando ao usuário para restringir a ação.
- **Não Use volta botões** -botão de Menu no controle Siri remoto é usado para retornar à tela anterior. A única exceção a essa regra é para compras no aplicativo ou ações destrutivas onde uma **Cancelar** botão deve ser exibido.

Para obter mais informações sobre como trabalhar com o foco e a navegação, consulte nosso [trabalhando com navegação e fogo](~/ios/tvos/app-fundamentals/navigation-focus.md) documentação.

<a name="Button-Icons" />

### <a name="button-icons"></a>Ícones de botão

A Apple sugere que você use imagens simples e altamente reconhecíveis para os ícones de botão. Ícones excessivamente complexas são difíceis de reconhecer em uma tela de TV na sala em um sofá, portanto, tente usar a representação mais simples possível transmitir a ideia. Sempre que possível, use o padrão, bem conhecida imagens para ícones (como uma lupa para pesquisa).

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botão

Apple tem as seguintes sugestões ao criar os títulos de seus botões:

- **Mostrar texto descritivo abaixo ícones botões** - onde possível, insira o texto descritivo, desmarque abaixo do ícone botões apenas obter ainda mais a finalidade do botão entre.
- **Use verbos ou frases de verbo para o título** -informar claramente colocar para a ação que terá quando o usuário clica no botão.
- **Usar a capitalização de estilo do título** – com exceção dos artigos, associações ou preposições (quatro letras ou menos), todas as palavras do título do botão devem estar em letras maiusculas.
- **Use uma breve, a ponto título** -usar argumentação mais curta possível para descrever a ação do botão.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Botões e Storyboards

A maneira mais fácil trabalhar com botões em um aplicativo xamarin. tvos é adicioná-los à interface de usuário do aplicativo usando o Designer do Xamarin para iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **botão** da **biblioteca** e solte-o no modo de exibição: 

    [![](buttons-images/storyboard01.png "Um botão")](buttons-images/storyboard01.png#lightbox)
1. No **Gerenciador de propriedades**, você pode ajustar várias propriedades do botão como seus **título** e **texto cor**: 

    [![](buttons-images/storyboard02.png "Propriedades do botão")](buttons-images/storyboard02.png#lightbox)
1. Em seguida, alterne para o **guia eventos** wire-up e uma **evento** do **botão** e chamá-lo `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "A guia eventos")](buttons-images/storyboard03.png#lightbox)
1. Você será levado automaticamente para o `ViewController.cs` modo de exibição em que você pode colocar a nova ação em seu código usando o **backup** e **para baixo** teclas de direção: 

    [![](buttons-images/storyboard04.png "Colocar uma nova ação no código")](buttons-images/storyboard04.png#lightbox)
1. Pressione a **Enter** para selecionar o local: 

    [![](buttons-images/storyboard05.png "O editor de código")](buttons-images/storyboard05.png#lightbox)
1. Salve as alterações a todos os arquivos.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **botão** da **biblioteca** e solte-o no modo de exibição: 

    [![](buttons-images/storyboard01vs.png "Um botão")](buttons-images/storyboard01vs.png#lightbox)
1. No **Gerenciador de propriedades**, você pode ajustar várias propriedades do botão como seus **título** e **texto cor**: 

    [![](buttons-images/storyboard02vs.png "O Gerenciador de propriedades")](buttons-images/storyboard02vs.png#lightbox)
1. Em seguida, alterne para o **guia eventos** wire-up e uma **evento** do **botão** e chamá-lo `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "A guia eventos")](buttons-images/storyboard03vs.png#lightbox)
1. Salve as alterações a todos os arquivos.



Editar seu controlador de exibição (exemplo `ViewController.cs`) de arquivo e adicione o seguinte código para lidar com o botão que está sendo selecionado:


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

Desde que um botão `Enabled` é de propriedade `true` e ele não é coberto por outro controle ou modo de exibição, ela pode se tornar o item em foco usando o Siri remoto. Se o usuário seleciona o botão e clicar na superfície de toque, o `ButtonPressed` ação definida acima seria executada.

> [!IMPORTANT]
> Embora seja possível atribuir ações, como `TouchUpInside` para um `UIButton` no iOS Designer durante a criação de um **manipulador de eventos**, ele nunca será chamado como Apple TV não tem um toque de tela ou dar suporte a eventos de toque. Você sempre deve usar o padrão **tipo de ação** durante a criação **ações** para elementos de interface de usuário do tvOS.




Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Botões e o código

Opcionalmente, uma `UIButton` podem ser criados no C# de código e adicionado à exibição do aplicativo tvOS. Por exemplo:

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

Quando você cria um novo `UIButton` no código, você deve especificar seu `UIButtonType` como um dos seguintes:

- **Sistema** -isso é o tipo de botão apresentado pelo tvOS padrão e é o tipo que você usará com mais frequência.
- **DetailDisclosure** -apresenta um tipo de "recusar" do botão usado para ocultar ou mostrar informações detalhadas.
- **InfoDark** -um escuro detalhados de informações do botão exibido "i" em um círculo.
- **InfoLight** -uma luz detalhados de informações do botão exibido "i" em um círculo.
- **AddContact** -exibir o botão como um botão Adicionar contato.
- **Personalizado** -permite que você personalize várias características do botão.

Em seguida, defina o tamanho na tela e o local do botão. Exemplo:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Em seguida, defina o título do botão. `UIButtons` são diferentes do que a maioria `UIKit` controles em que eles têm um estado para que você não pode simplesmente alterar o título, você precisa alterá-la para um determinado `UIControlState`. Por exemplo:

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

Por fim, você deve adicionar o botão para o modo de exibição para exibi-lo:

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Embora seja possível atribuir ações, como `TouchUpInside` para um `UIButton`, ele nunca será chamado como Apple TV não tem um toque de tela ou dar suporte a eventos de toque. Você sempre deve usar eventos, como **AllEvents** ou **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Definir o estilo de um botão

tvOS fornece várias propriedades de um `UIButton` que pode ser usado para fornecer seu título e estilize-a com coisas como a cor do plano de fundo e imagens.

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botão

Como vimos anteriormente, `UIButtons` são diferentes do que a maioria `UIKit` controles que têm um estado para que você não pode simplesmente alterar o título, você precisa alterá-la para um determinado `UIControlState`. Por exemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Você pode definir a cor do título para o botão usando o `SetTitleColor` método. Por exemplo:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

E você pode ajustar o título de sombra usando o `SetTitleShadowColor`. Por exemplo:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Você pode definir a sombra do título para alterar de *Engraved* à *em relevo* quando o botão está realçado usando o seguinte código:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Além disso, você pode usar o texto atribuído como o título do botão. Por exemplo:

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Imagens de botão

Um `UIButton` pode ter uma imagem anexada a ele e pode usar uma imagem como seu plano de fundo.

Para definir a imagem de plano de fundo de um botão para um determinado `UIControlState`, use o seguinte código:

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Defina as `AdjustsImageWhenHiglighted` propriedade para `true` para desenhar a imagem mais clara quando o botão é realçado (esse é o padrão). Defina as `AdjustsImageWhenDisabled` propriedade para `true` para desenhar a imagem mais escura, quando o botão está desabilitado (novamente, esse é o padrão).

Para definir a imagem exibida no botão, use o seguinte código:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Use o `TintColor` propriedade para definir um tom de cor é aplicado para o título e a imagem do botão. Para botões do `Custom` tipo, essa propriedade não tem nenhum efeito, você deve implementar o `TintColor` comportamento por conta própria.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo cobriu criando e trabalhando com botões dentro de um aplicativo xamarin. tvos. Ele mostrou como trabalhar com os botões no iOS Designer e como criar botões em C# código. Por fim, ele mostrou como modificar o título de um botão e alterar sua aparência e estilo.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
