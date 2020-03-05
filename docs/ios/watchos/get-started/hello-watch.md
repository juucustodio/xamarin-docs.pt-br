---
title: Olá, watchOS – Walkthrough
description: Este documento fornece uma explicação da criação de um aplicativo watchOS simples usando o Xamarin. Ele descreve como trabalhar no Visual Studio e Visual Studio para Mac, trabalhar com storyboards e responder a eventos no código.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/14/2016
ms.openlocfilehash: 2d8b48892a5a1106b03778ac30eca4b18f049f4d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291865"
---
# <a name="hello-watchos--walkthrough"></a>Olá, watchOS – Walkthrough

Depois de criar uma solução seguindo as etapas em [instalação e instalação](~/ios/watchos/get-started/installation.md), você terá três projetos:

- O aplicativo pai do iOS que é usado para instalação ou outras tarefas administrativas no dispositivo. (Com outros tipos de extensões do iOS, isso é geralmente chamado de aplicativo "contêiner".) Com os aplicativos Watch, será possível que os usuários comecem a executar o aplicativo Watch sem **nunca** executar o aplicativo pai;
- A extensão Watch que contém o código do programa para o aplicativo Watch; e
- O aplicativo Watch, que contém os recursos de storyboard e imagem que são renderizados na inspeção.

Verifique se suas [referências estão corretas](~/ios/watchos/get-started/project-references.md): se o aplicativo pai tem uma referência à extensão e se a extensão tem uma referência para o aplicativo Watch.

Confirme que seus identificadores de pacote seguem a Convenção de \*. watchkitextension \*. watchkitapp e que o arquivo info. plist de sua extensão tem o valor de **ID de pacote WKApp** definido como o identificador de pacote do seu aplicativo Watch.

Você deve ser capaz de executar seu aplicativo Watch agora, mas como o arquivo de storyboard em seu aplicativo Watch está em branco, você não conseguiria dizer.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "The Solution Explorer")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "The Solution Explorer")

-----

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Clique duas vezes na interface. Storyboard em seu aplicativo Watch para iniciar o Xamarin iOS designer (se você estiver em um Mac, você também pode clicar com o botão direito do mouse e **abrir com > Xcode Interface Builder**)

1. Verifique se a **caixa de ferramentas** e os painéis de **Propriedades** estão visíveis,
1. Clique para selecionar o controlador de interface,
1. Defina o identificador e o título do controlador de interface como **interfaceController** e **Hi Watch**,
1. Verifique se a **classe** está definida como **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Set the Identifier and Title of the Interface Controller to interfaceController and Hi Watch")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Clique duas vezes na interface. Storyboard em seu aplicativo Watch para editar com o Xamarin iOS designer no Visual Studio:

1. Abra o painel Propriedades;
1. Altere a classe para **InterfaceController**;
1. Clique no controlador de interface; e
1. Defina o identificador e o título do controlador de interface como **interfaceController** e **Hi Watch**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Set the Identifier and Title of the Interface Controller to interfaceController and Hi Watch")

-----

Crie sua interface do usuário:

1. No painel **caixa de ferramentas** ,
1. Arraste e solte um **botão** e um **rótulo** para a cena e
1. Defina o texto e os atributos dos controles, conforme mostrado:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "Set the text and attributes of the controls as shown")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "Set the text and attributes of the controls as shown")

-----

1. Defina o **nome** no painel de **Propriedades** para cada controle. Neste exemplo, usamos `myButton` e `myLabel`.

1. Selecione o botão no storyboard e vá para a lista de **eventos** do painel de **Propriedades** e, em seguida,

1. Crie uma nova **ação** digitando `OnButtonPress` e pressionando **Enter**.
  A ação aparecerá na lista e um método parcial será criado automaticamente no C#.

![](hello-watch-images/buttonaction.png "The OnButtonPress Action added to a button")

Depois de salvar o storyboard, o **InterfaceController.designer.cs** é atualizado com os nomes de controle e ações. Se você abrir esse arquivo após ele ter sido atualizado, poderá ver como o `RegisterAttribute` corresponde ao controlador e como os controles da interface do C# usuário correspondem às variáveis de instância marcadas com o `OutletAttribute` e como as ações são mapeadas para os métodos parciais marcados com o `ActionAttribute`:

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

Agora, abra **InterfaceController.cs** (*não* InterfaceController.designer.cs) e adicione o seguinte código:

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

Esse código deve ser razoavelmente transparente: a variável de instância `clickCount` é incrementada toda vez que a função `OnButtonPress` é chamada. O texto de `myLabel` é alterado para refletir essa contagem; `myLabel`, é claro, é o nome de uma das saídas que você criou no XCode. A função `partial` é a implementação da função associada ao nome da ação especificada.

Se ele ainda não for o projeto de inicialização,

1. Clique com o botão direito do mouse no projeto de extensão de inspeção e escolha **definir como projeto de inicialização**,

1. Definir o destino de implantação para uma imagem de simulador compatível com o kit de inspeção (como iPhone 6 iOS 8,2),

1. Pressione o botão **depurar** para disparar uma compilação e o lançamento do simulador.

    [![](hello-watch-images/readytodebug-sml.png "The Visual Studio interface elements")](hello-watch-images/readytodebug.png#lightbox)

Quando o simulador for iniciado, pressione o botão para incrementar o rótulo.
Parabéns, você tem um aplicativo de inspeção!

![](hello-watch-images/running.png "The app running in the Simulator")

## <a name="related-links"></a>Links relacionados

- [Instalação e configuração](~/ios/watchos/get-started/installation.md)
- [Primeiro vídeo do aplicativo Watch](https://blog.xamarin.com/your-first-watch-kit-app/)
