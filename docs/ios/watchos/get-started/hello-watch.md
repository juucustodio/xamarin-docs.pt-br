---
title: "Olá, inspecionar"
description: "Introdução ao Xamarin e watchOS"
ms.topic: article
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/14/2016
ms.openlocfilehash: 732fd9808ded4bf97e99e7ab0e6ab63b989452d1
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="hello-watch"></a>Olá, inspecionar

Depois de ter criado uma solução seguindo as etapas em [instalação e configuração](~/ios/watchos/get-started/installation.md), você terá 3 projetos:

- O aplicativo pai do iOS que é usado para instalação ou outras tarefas administrativas no dispositivo. (Com outros tipos de extensões do iOS, isso é conhecido como o aplicativo "Contêiner".) Com aplicativos de observação, será possível para os usuários a começar a executar o aplicativo Watch sem **nunca** executando o aplicativo pai;
- A extensão de observar que contém o código de programa para o aplicativo watch; e
- O aplicativo de inspeção, que contém os recursos de storyboard e imagem são renderizados no relógio.

Verifique se o [referências estão corretas](~/ios/watchos/get-started/project-references.md): o aplicativo pai tem uma referência para a extensão e a extensão tem uma referência para o aplicativo de inspeção.

Confirmar que seu pacote identificadores siga o \*.watchkitextension \*.watchkitapp convenção tem e que o arquivo de info. plist da extensão possui **ID do pacote WKApp** valor definido para o identificador de pacote de seu aplicativo de inspeção.

Você deve ser capaz de executar seu aplicativo de inspeção agora, mas porque o arquivo de storyboard dentro de seu aplicativo de inspeção estiver em branco, não será capaz de dizer.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](hello-watch-images/projectstructure.png "O Gerenciador de soluções")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-projectstructure.png "O Gerenciador de soluções")

-----

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
Clique duas vezes em Interface.storyboard em seu aplicativo de inspeção para iniciar o Designer do Xamarin iOS (se você estiver usando um Mac também pode clicar e **abrir com > Xcode Interface Builder**)


1.  Verifique se o **caixa de ferramentas** e **propriedades** preenche é visíveis,
1.  Clique para selecionar o controlador de Interface
1.  Definir o identificador e o título do controlador de Interface para **interfaceController** e **Hi inspeção**,
1.  Verifique se o **classe** é definido como **InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "Definir o identificador e o título do controlador de Interface de interfaceController e inspecionar Hi")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Clique duas vezes em Interface.storyboard em seu aplicativo de inspeção para editar com o Xamarin iOS Designer no Visual Studio:

1.  Abra o painel de propriedades;
1.  Alterar a classe para **InterfaceController**;
1.  Clique no controlador de Interface; e
1.  Definir o identificador e o título do controlador de Interface para **interfaceController** e **Hi inspecionar**.

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "Definir o identificador e o título do controlador de Interface de interfaceController e inspecionar Hi")

-----


Crie sua interface do usuário:

1. Do **caixa de ferramentas** teclado,
1. Arraste e solte um **botão** e um **rótulo** em cena, e
1. Defina o texto e os atributos dos controles, conforme mostrado:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](hello-watch-images/draganddrop.png "Definir o texto e os atributos dos controles, conforme mostrado")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-draganddrop.png "Definir o texto e os atributos dos controles, conforme mostrado")

-----

1. Definir o **nome** no **propriedades** teclado para cada controle. Este exemplo usamos `myButton` e `myLabel`.

1. Selecione o botão no storyboard e vá para o **propriedades** do preenchimento **eventos** lista, em seguida,

1. Criar um novo **ação** digitando `OnButtonPress` e pressionando **Enter**.
  A ação será exibido na lista, e um método parcial será criado automaticamente no c#.

![](hello-watch-images/buttonaction.png "A ação OnButtonPress adicionado a um botão")

Depois de salvar o storyboard a **InterfaceController.designer.cs** é atualizada com os nomes de controle e as ações. Se você abrir esse arquivo depois que ele foi atualizado, você pode ver como o `RegisterAttribute` corresponde para o controlador e como os controles de interface do usuário correspondem ao c# variáveis de instância marcadas com o `OutletAttribute` , como ações são mapeados para os métodos parciais marcados com o `ActionAttribute`:

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

Esse código deve ser relativamente transparente: a variável de instância `clickCount` é incrementado toda vez que a função `OnButtonPress` é chamado. O texto da `myLabel` é alterado para refletir essa contagem; `myLabel`, obviamente, é o nome de uma das saídas de que você criou no XCode. O `partial` função é a implementação da função associada com o nome da ação que você especificou.

Se já não é o projeto de inicialização

1. Clique com botão direito no seu projeto de extensão de inspeção e escolha **definir como projeto de inicialização**,

1. Definir o destino de implantação para uma imagem de simulador compatível com inspeção Kit (como iPhone 6 iOS 8.2)

1. Pressione a **depurar** botão para disparar uma inicialização de compilação e do simulador.

    [![](hello-watch-images/readytodebug-sml.png "Os elementos de interface do Visual Studio")](hello-watch-images/readytodebug.png#lightbox)

Quando o simulador é iniciado, pressione o botão para incrementar o rótulo.
Parabéns, você terá um aplicativo de Observação!

![](hello-watch-images/running.png "O aplicativo em execução no simulador")


## <a name="related-links"></a>Links relacionados

- [Guia de Introdução (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [Instalação e configuração](~/ios/watchos/get-started/installation.md)
- [Vídeo do primeiro aplicativo Watch](http://blog.xamarin.com/your-first-watch-kit-app/)
