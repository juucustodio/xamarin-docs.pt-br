---
title: "Trabalhando com botões"
description: "Este artigo aborda criando e trabalhando com botões dentro de um aplicativo Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/07/2017
ms.openlocfilehash: 4b2a470d7fe2a1f9d4b8df40836c934547adf614
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-buttons"></a>Trabalhando com botões

_Este artigo aborda criando e trabalhando com botões dentro de um aplicativo Xamarin.tvOS._


Usar uma instância de `UIButton` classe para criar um botão Focusable é selecionável em uma janela tvOS. Quando o usuário seleciona um botão, ele envia uma mensagem de ação para o objeto de destino permitir responder seu aplicativo Xamarin.tvOS o usuário de entrada do.

[![](buttons-images/buttons01.png "Botões de exemplo")](buttons-images/buttons01.png#lightbox)

Para obter mais informações sobre como trabalhar com foco e navegando com Siri remota, consulte nosso [trabalhando com foco e navegação](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

<a name="About-Buttons" />

## <a name="about-buttons"></a>Sobre botões

Em tvOS, botões são usados para ações específicas para o aplicativo em podem conter um título, um ícone ou ambos. Quando o usuário navega usando de Interface do usuário do aplicativo de [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), foco muda para o botão determinado, facilitando a alterar as cores de plano de fundo e texto. Uma sombra também é aplicada para o botão Adicionar um efeito 3D, facilitando aparecer subir o restante da Interface do usuário.

[![](buttons-images/buttons01.png "Botões de exemplo")](buttons-images/buttons01.png#lightbox)

Apple tem as seguintes sugestões para trabalhar com botões:

- **Use um título ou um ícone** - enquanto ambos um ícone e um título pode ser incluído em um botão, o espaço é limitado tente não combinar as duas opções.
- **Claramente marca botões destrutiva** - se no botão executa um destrutiva ação (como excluir um arquivo), claramente marcá-lo como tal usando texto e/ou ícone. Ações destrutivas sempre devem apresentar um [alerta](~/ios/tvos/user-interface/alerts.md) pedindo que o usuário para restringir a ação.
- **Não Use volta botões** -botão de Menu no controle remoto Siri é usado para retornar à tela anterior. A única exceção a essa regra é para compras no aplicativo ou ações destrutivas onde um **Cancelar** botão deve ser exibido.

Para obter mais informações sobre como trabalhar com foco e navegação, consulte nosso [trabalhando com foco e navegação](~/ios/tvos/app-fundamentals/navigation-focus.md) documentação.

<a name="Button-Icons" />

### <a name="button-icons"></a>Ícones de botão

Apple sugere que você use imagens simples e altamente reconhecíveis para os ícones de botão. Ícones excessivamente complexas são difíceis de reconhecer em uma tela de TV na sala em um sofá, tente usar a representação mais simples possível transmitir a ideia. Sempre que possível, use padrão, saber bem de imagens para ícones (como uma lupa para pesquisa).

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botão

Apple tem as sugestões a seguir ao criar os títulos para os botões:

- **Mostrar texto descritivo abaixo ícones botões** - onde possível, insira o texto descritivo, desmarque abaixo ícone botões somente para obter mais finalidade do botão em.
- **Use verbos ou frases de verbo para o título** -estado claramente colocar para a ação que terá quando o usuário clica no botão.
- **Usar a capitalização de estilo do título** - com a exceção de artigos, conjunções ou preposições (quatro letras ou menos), todas as palavras do título do botão devem estar em letras maiusculas.
- **Use uma breve, o título para o ponto** -usar a menor argumentação possíveis para descrever a ação do botão.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Botões e Storyboards

É a maneira mais fácil para trabalhar com os botões em um aplicativo de Xamarin.tvOS para adicioná-los à interface de usuário do aplicativo usando o Designer de Xamarin para iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **botão** do **biblioteca** e solte-o no modo de exibição: 

    [![](buttons-images/storyboard01.png "Um botão")](buttons-images/storyboard01.png#lightbox)
1. No **propriedades Explorer**, você pode ajustar várias propriedades do botão como seu **título** e **cor do texto**: 

    [![](buttons-images/storyboard02.png "Propriedades de botão")](buttons-images/storyboard02.png#lightbox)
1. Em seguida, alterne para o **guia eventos** e transmissão até um **evento** do **botão** e chamá-lo `ButtonPressed`: 

    [![](buttons-images/storyboard03.png "Na guia eventos")](buttons-images/storyboard03.png#lightbox)
1. Você será levado automaticamente para o `ViewController.cs` exibição onde você pode colocar a nova ação no seu código usando o **backup** e **para baixo** teclas de seta: 

    [![](buttons-images/storyboard04.png "Colocando uma nova ação no código")](buttons-images/storyboard04.png#lightbox)
1. Pressione a **Enter** para selecionar o local: 

    [![](buttons-images/storyboard05.png "O editor de códigos")](buttons-images/storyboard05.png#lightbox)
1. Salve as alterações para todos os arquivos.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **botão** do **biblioteca** e solte-o no modo de exibição: 

    [![](buttons-images/storyboard01vs.png "Um botão")](buttons-images/storyboard01vs.png#lightbox)
1. No **propriedades Explorer**, você pode ajustar várias propriedades do botão como seu **título** e **cor do texto**: 

    [![](buttons-images/storyboard02vs.png "O Gerenciador de propriedades")](buttons-images/storyboard02vs.png#lightbox)
1. Em seguida, alterne para o **guia eventos** e transmissão até um **evento** do **botão** e chamá-lo `ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "Na guia eventos")](buttons-images/storyboard03vs.png#lightbox)
1. Salve as alterações para todos os arquivos.



Editar seu controlador de exibição (exemplo `ViewController.cs`) e adicione o seguinte código para lidar com o botão selecionado:


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

Desde que um botão `Enabled` é de propriedade `true` e ele não é coberto por outro controle ou exibição, pode ser feita o item de em foco usando Siri remoto. Se o usuário seleciona o botão e clicar em superfície de toque, o `ButtonPressed` definida acima a ação deve ser executada.

> [!IMPORTANT]
> **Observação:** enquanto é possível atribuir ações como `TouchUpInside` para um `UIButton` no iOS Designer durante a criação de um **manipulador de eventos**, ele nunca será chamado porque Apple TV não tiver uma tela sensível ao toque ou suporte eventos de toque. Você sempre deve usar o padrão **tipo de ação** ao criar **ações** para tvOS elementos de interface do usuário.




Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Botões e código

Opcionalmente, um `UIButton` podem ser criados em código c# e adicionadas à exibição do aplicativo tvOS. Por exemplo:

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

- **Sistema** -este é o tipo de botão apresentado pelo tvOS padrão e é o tipo que você usará com mais frequência.
- **DetailDisclosure** -apresenta um tipo de "Desativar" de botão usada para ocultar ou mostrar informações detalhadas.
- **InfoDark** -um escuro detalhadas informações botão exibido um "i" em um círculo.
- **InfoLight** -clara detalhadas informações botão exibido um "i" em um círculo.
- **AddContact** -exibir o botão como um botão Adicionar contato.
- **Personalizar** -permite que você personalize várias características do botão.

Em seguida, defina o tamanho na tela e o local do botão. Exemplo:

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Em seguida, defina o título do botão. `UIButtons` são diferentes do que a maioria `UIKit` controles em que eles têm um estado para que você não pode simplesmente altere o título, você precisa alterá-la para um determinado `UIControlState`. Por exemplo:

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
> **Observação:** enquanto é possível atribuir ações como `TouchUpInside` para um `UIButton`, ele nunca será chamado porque Apple TV não tem um toque de tela ou oferecer suporte a eventos de toque. Você sempre deve usar eventos, como **AllEvents** ou **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Um botão de estilo

tvOS fornece várias propriedades de um `UIButton` que pode ser usado para fornecer seu título e definir o estilo com coisas como cor de plano de fundo e imagens.

<a name="Button-Titles" />

### <a name="button-titles"></a>Títulos de botão

Como vimos acima, `UIButtons` são diferentes do que a maioria `UIKit` controles em que eles têm um estado para que você não pode simplesmente altere o título, você precisa alterá-la para um determinado `UIControlState`. Por exemplo:

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Você pode definir a cor do título do botão usando o `SetTitleColor` método. Por exemplo:

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

E você pode ajustar o título de sombra usando o `SetTitleShadowColor`. Por exemplo:

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Você pode definir a sombra do título para alterar de *Engraved* para *em relevo* quando o botão é realçado usando o seguinte código:

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

Além disso, você pode usar o texto atribuído como título do botão. Por exemplo:

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

Definir o `AdjustsImageWhenHiglighted` propriedade `true` para desenhar a imagem mais clara quando o botão é realçado (esse é o padrão). Definir o `AdjustsImageWhenDisabled` propriedade `true` para desenhar a imagem mais escura quando o botão está desabilitado (novamente, esse é o padrão).

Para definir a imagem exibida no botão, use o seguinte código:

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Use o `TintColor` propriedade para definir um tom de cor que é aplicado para o título e a imagem do botão. Botões do `Custom` tipo, essa propriedade não tem nenhum efeito, você deve implementar o `TintColor` comportamento por conta própria.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com botões dentro de um aplicativo Xamarin.tvOS. Ele mostrou como trabalhar com os botões no Designer de iOS e como criar botões no código c#. Finalmente, ele mostrou como modificar o título do botão e alterar sua aparência e o estilo.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
