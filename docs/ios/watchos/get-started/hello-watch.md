---
title: Olá, watchOS – passo a passo
description: Este documento fornece um passo a passo da criação de um aplicativo do watchOS simples usando o Xamarin. Ele descreve como trabalhar no Visual Studio e o Visual Studio para Mac, trabalhar com storyboards e responder a eventos no código.
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/14/2016
ms.openlocfilehash: de04a2d7f42ec36c464c75ced73bf8f8029ec1da
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672606"
---
# <a name="hello-watchos--walkthrough"></a>Olá, watchOS – passo a passo

Depois de criar uma solução seguindo as etapas em [configuração e instalação](~/ios/watchos/get-started/installation.md), você terá 3 projetos:

- O aplicativo pai do iOS que é usado para a instalação ou outras tarefas administrativas no dispositivo. (Com outros tipos de extensões do iOS, isso é conhecido como o aplicativo de "Contêiner".) Com aplicativos do Watch, será possível que os usuários iniciar a execução do aplicativo de inspeção sem **nunca** executando o aplicativo pai;
- A extensão de inspeção, que contém o código de programa para o aplicativo watch; e
- O aplicativo de inspeção, que contém os recursos de storyboard e imagem são renderizados no relógio.

Verifique se sua [referências estão corretas](~/ios/watchos/get-started/project-references.md): que o aplicativo pai tem uma referência para a extensão, e que a extensão tem uma referência para o aplicativo Watch.

Confirme que os seus identificadores de pacote siga a \*.watchkitextension \*.watchkitapp convenção e se o arquivo de info. plist da sua extensão tem ele tem **ID do pacote de WKApp** valor definido como o identificador de pacote do o aplicativo de inspeção.

Você deve ser capaz de executar o aplicativo de inspeção agora, mas porque o arquivo de storyboard dentro de seu aplicativo de inspeção estiver em branco, você não conseguiria dizer.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "O Gerenciador de soluções")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "O Gerenciador de soluções")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
Clique duas vezes no Interface.storyboard em seu aplicativo de inspeção para iniciar o Designer do iOS Xamarin (se você estiver usando um Mac pode também clicar duas vezes e **abrir com > Interface Builder do Xcode**)


1.  Verifique se o **caixa de ferramentas** e **propriedades** painéis estiverem visíveis,
1.  Clique para selecionar o controlador de Interface
1.  Definir o identificador e o título do controlador de Interface para o **interfaceController** e **Hi inspeção**,
1.  Verifique se o **classe** é definido como **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Definir o identificador e o título do controlador de Interface de interfaceController e inspeção de Olá")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Clique duas vezes no Interface.storyboard em seu aplicativo de inspeção para editar com o Xamarin iOS Designer no Visual Studio:

1.  Abra o painel de propriedades;
1.  Altere a classe para **InterfaceController**;
1.  Clique no controlador de Interface; e
1.  Definir o identificador e o título do controlador de Interface para o **interfaceController** e **Hi inspeção**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Definir o identificador e o título do controlador de Interface de interfaceController e inspeção de Olá")

-----


Crie sua interface do usuário:

1. Dos **caixa de ferramentas** teclado,
1. Arraste e solte uma **botão** e uma **rótulo** até a cena, e
1. Defina o texto e os atributos dos controles conforme mostrado:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "Definir o texto e os atributos dos controles conforme mostrado")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "Definir o texto e os atributos dos controles conforme mostrado")

-----

1. Defina a **nome** na **propriedades** pad para cada controle. Neste exemplo usamos `myButton` e `myLabel`.

1. Selecione o botão no storyboard e vá para o **propriedades** do painel **eventos** listar, em seguida,

1. Criar um novo **ação** digitando `OnButtonPress` e pressionando **Enter**.
  A ação será exibida na lista, e um método parcial será criado automaticamente no C#.

![](hello-watch-images/buttonaction.png "A ação de OnButtonPress adicionada a um botão")

Depois de salvar o storyboard, o **InterfaceController.designer.cs** é atualizada com os nomes de controle e as ações... Se você abrir esse arquivo depois que ele tiver sido atualizado, você pode ver como o `RegisterAttribute` corresponde ao controlador e como os controles de interface do usuário correspondem ao C# variáveis de instância marcados com o `OutletAttribute` e como as ações são mapeadas para métodos parciais marcados com o `ActionAttribute`:

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

Esse código deve ser bastante transparente: a variável de instância `clickCount` é incrementado toda vez que a função `OnButtonPress` é chamado. O texto do `myLabel` é alterado para refletir essa contagem; `myLabel`, é claro, o nome de um dos meios de comunicação que você criou no XCode. O `partial` função é a implementação da função associada com o nome da ação que você especificou.

Se já não é o projeto de inicialização

1. Clique com botão direito no seu projeto de extensão de inspeção e escolha **definir como projeto de inicialização**,

1. Definir o destino de implantação para uma imagem de simulador compatível com inspeção Kit (como iPhone 6 iOS 8.2)

1. Pressione a **depurar** botão para disparar uma inicialização de compilação e simulador.

    [![](hello-watch-images/readytodebug-sml.png "Os elementos de interface do Visual Studio")](hello-watch-images/readytodebug.png#lightbox)

Ao inicia o simulador, pressione o botão para incrementar o rótulo.
Parabéns, você terá um aplicativo de inspeção!

![](hello-watch-images/running.png "O aplicativo em execução no simulador")


## <a name="related-links"></a>Links relacionados

- [Guia de Introdução (amostra)](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [Instalação e configuração](~/ios/watchos/get-started/installation.md)
- [Primeiro vídeo de aplicativo de inspeção](https://blog.xamarin.com/your-first-watch-kit-app/)
