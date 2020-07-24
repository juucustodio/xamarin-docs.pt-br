---
title: Criando controles personalizados no Xamarin. Mac
description: Este documento descreve como criar controles personalizados no Xamarin. Mac. Ele mostra como criar o controle personalizado, acompanhar seu estado, desenhar sua interface, responder à entrada do usuário e usar o controle em um aplicativo.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 8ed83ee8f0bded6258b695f7a6383cda1929f542
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997079"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Criando controles personalizados no Xamarin. Mac

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso aos mesmos controles de usuário que um desenvolvedor trabalhando em *Objective-C*, *Swift* e *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter seus controles de usuário (ou, opcionalmente, criá-los diretamente no código C#).

Embora o macOS forneça uma infinidade de controles de usuário internos, pode haver ocasiões em que você precise criar um controle personalizado para fornecer funcionalidade não fornecida pronta para uso ou para corresponder a um tema personalizado da interface do usuário (como uma interface de jogo).

[![Exemplo de um controle de interface do usuário personalizado](custom-controls-images/intro01.png)](custom-controls-images/intro01.png#lightbox)

Neste artigo, abordaremos as noções básicas da criação de um controle de interface do usuário personalizada reutilizável em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` comandos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

<a name="Introduction-to-Outline-Views"></a>

## <a name="introduction-to-custom-controls"></a>Introdução aos controles personalizados

Conforme mencionado acima, pode haver ocasiões em que você precisa criar um controle de interface do usuário reutilizável e personalizado para fornecer uma funcionalidade exclusiva para a interface do usuário do aplicativo Xamarin. Mac ou para criar um tema de interface de usuário personalizado (como uma interface de jogo).

Nessas situações, você pode herdar facilmente de `NSControl` e criar uma ferramenta personalizada que pode ser adicionada à interface do usuário do seu aplicativo por meio de código C# ou por meio do Interface Builder do Xcode. Herdar de `NSControl` seu controle personalizado terá automaticamente todos os recursos padrão que um controle de interface do usuário interno tem (como `NSButton` ).

Se o controle de interface do usuário personalizado apenas exibir informações (como uma ferramenta gráfica e gráfico personalizados), talvez você queira herdar de `NSView` em vez de `NSControl` .

Não importa qual classe base é usada, as etapas básicas para criar um controle personalizado são as mesmas.

Neste artigo, você criará um componente de comutador de flip personalizado que fornece um tema de interface de usuário exclusivo e um exemplo de criação de um controle de interface de usuário personalizado totalmente funcional.

<a name="Building-the-Custom-Control"></a>

## <a name="building-the-custom-control"></a>Criando o controle personalizado

Como o controle personalizado que estamos criando estará respondendo à entrada do usuário (cliques do botão esquerdo do mouse), vamos herdar de `NSControl` . Dessa forma, nosso controle personalizado terá automaticamente todos os recursos padrão que um controle de interface do usuário interno tem e responde como um controle padrão do macOS.

No Visual Studio para Mac, abra o projeto Xamarin. Mac para o qual você deseja criar um controle de interface de usuário personalizado (ou criar um novo). Adicione uma nova classe e chame-a `NSFlipSwitch` :

[![Adicionando uma nova classe](custom-controls-images/custom01.png)](custom-controls-images/custom01.png#lightbox)

Em seguida, edite a `NSFlipSwitch.cs` classe e faça com que ela fique parecida com a seguinte:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

A primeira coisa a ser observada sobre nossa classe personalizada no que estamos herdando `NSControl` e usando o comando **Register** para expor essa classe para o Objective-C e o Interface Builder do Xcode:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Nas seções a seguir, vamos dar uma olhada no restante do código acima em detalhes.

<a name="Tracking-the-Controls-State"></a>

### <a name="tracking-the-controls-state"></a>Controlando o estado do controle

Como nosso controle personalizado é um comutador, precisamos de uma maneira de acompanhar o estado ligado/desligado do comutador. Tratamos disso com o seguinte código em `NSFlipSwitch` :

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

Quando o estado do comutador muda, precisamos de uma maneira de atualizar a interface do usuário. Fazemos isso forçando o controle a redesenhar sua interface do usuário `NeedsDisplay = true` .

Se o nosso controle exigisse mais que um único estado ligado/desligado (por exemplo, um comutador de vários Estados com 3 posições), poderíamos ter usado uma **Enumeração** para acompanhar o estado. Para nosso exemplo, um **bool** simples fará.

Também adicionamos um método auxiliar para trocar o estado do comutador entre ativar e desativar:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Posteriormente, expandiremos essa classe auxiliar para informar ao chamador quando o estado switches for alterado.

<a name="Drawing-the-Controls-Interface"></a>

### <a name="drawing-the-controls-interface"></a>Desenhando a interface do controle

Vamos usar as rotinas de desenho gráfico principal para desenhar a interface do usuário do controle personalizado em tempo de execução. Antes que possamos fazer isso, precisamos ativar as camadas para nosso controle. Fazemos isso com o seguinte método particular:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Esse método é chamado de cada um dos construtores do controle para garantir que o controle esteja configurado corretamente. Por exemplo:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Em seguida, precisamos substituir o `DrawRect` método e adicionar as principais rotinas gráficas para desenhar o controle:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Ajustaremos a representação visual do controle quando seu estado for alterado (como, por exemplo, de **ativado** para **desativado**). Sempre que o estado é alterado, podemos usar o `NeedsDisplay = true` comando para forçar o controle a redesenhar para o novo estado.

<a name="Responding-to-User-Input"></a>

### <a name="responding-to-user-input"></a>Respondendo à entrada do usuário

Há duas maneiras básicas de adicionar a entrada do usuário ao nosso controle personalizado: **substituir rotinas de manipulação do mouse** ou **reconhecedores de gesto**. Qual método que usamos, será baseado na funcionalidade exigida por nosso controle.

> [!IMPORTANT]
> Para qualquer controle personalizado que você criar, você deve usar os **métodos de substituição** _ou_ os **reconhecedores de gesto**, mas não ambos ao mesmo tempo que eles podem entrar em conflito entre si.

<a name="Summary"></a>

#### <a name="handling-user-input-with-override-methods"></a>Manipulando a entrada do usuário com métodos de substituição

Objetos que herdam de `NSControl` (ou `NSView` ) têm vários métodos de substituição para manipular a entrada do mouse ou do teclado. Para nosso controle de exemplo, queremos inverter o estado da opção entre **on** e **off** quando o usuário clica no controle com o botão esquerdo do mouse. Podemos adicionar os seguintes métodos de substituição à `NSFlipSwitch` classe para lidar com isso:

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

No código acima, chamamos o `FlipSwitchState` método (definido acima) para virar o estado ligado/desligado da opção no `MouseDown` método. Isso também forçará o controle a ser redesenhado para refletir o estado atual.

<a name="Handling-User-Input-with-Gesture-Recognizers"></a>

#### <a name="handling-user-input-with-gesture-recognizers"></a>Manipulando a entrada do usuário com reconhecedores de gesto

Opcionalmente, você pode usar reconhecedores de gesto para manipular o usuário interagindo com o controle. Remova as substituições adicionadas acima, edite o `Initialize` método e faça com que ele se pareça com o seguinte:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

Aqui, estamos criando um novo `NSClickGestureRecognizer` e chamando nosso `FlipSwitchState` método para alterar o estado do comutador quando o usuário clica nele com o botão esquerdo do mouse. O `AddGestureRecognizer (click)` método adiciona o reconhecedor de gestos ao controle.

Novamente, o método que usamos depende do que estamos tentando realizar com nosso controle personalizado. Se precisar de acesso de baixo nível à interação do usuário, use os métodos de substituição. Se precisar de funcionalidades predefinidas, como cliques do mouse, use reconhecedores de gesto.

<a name="Responding-to-State-Change-Events"></a>

### <a name="responding-to-state-change-events"></a>Respondendo a eventos de alteração de estado

Quando o usuário altera o estado de nosso controle personalizado, precisamos de uma maneira de responder à alteração de estado no código (como fazer algo quando clica em um botão personalizado).

Para fornecer essa funcionalidade, edite a `NSFlipSwitch` classe e adicione o seguinte código:

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null)
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

Em seguida, edite o `FlipSwitchState` método e faça parecer com o seguinte:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

Primeiro, fornecemos um `ValueChanged` evento que podemos adicionar um manipulador no código C# para que possamos executar uma ação quando o usuário alterar o estado do comutador.

Segundo, como o nosso controle personalizado é herdado `NSControl` , ele automaticamente tem uma **ação** que pode ser atribuída no interface Builder do Xcode. Para chamar essa **ação** quando o estado for alterado, usamos o seguinte código:

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Primeiro, verificamos se uma **ação** foi atribuída ao controle. Em seguida, chamamos a **ação** se ela tiver sido definida.

<a name="Using-the-Custom-Control"></a>

## <a name="using-the-custom-control"></a>Usando o controle personalizado

Com o nosso controle personalizado totalmente definido, podemos adicioná-lo à interface do usuário do aplicativo Xamarin. Mac usando código C# ou no Interface Builder do Xcode.

Para adicionar o controle usando Interface Builder, primeiro faça uma compilação limpa do projeto Xamarin. Mac e clique duas vezes no `Main.storyboard` arquivo para abri-lo em interface Builder para edição:

[![Editando o storyboard no Xcode](custom-controls-images/custom02.png)](custom-controls-images/custom02.png#lightbox)

Em seguida, arraste um `Custom View` para o design da interface do usuário:

[![Selecionando uma exibição personalizada da biblioteca](custom-controls-images/custom03.png)](custom-controls-images/custom03.png#lightbox)

Com a exibição personalizada ainda selecionada, alterne para o **Inspetor de identidade** e altere a **classe** da exibição para `NSFlipSwitch` :

[![Definindo a classe da exibição](custom-controls-images/custom04.png)](custom-controls-images/custom04.png#lightbox)

Alterne para o **Editor do assistente** e crie uma **tomada** para o controle personalizado (assegurando associá-lo ao `ViewController.h` arquivo e não ao `.m` arquivo):

[![Configurando uma nova tomada](custom-controls-images/custom05.png)](custom-controls-images/custom05.png#lightbox)

Salve suas alterações, retorne ao Visual Studio para Mac e permita que as alterações sejam sincronizadas. Edite o `ViewController.cs` arquivo e faça com que o `ViewDidLoad` método se pareça com o seguinte:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
```

Aqui, respondemos ao `ValueChanged` evento que definimos acima na `NSFlipSwitch` classe e escrevemos o **valor** atual quando o usuário clica no controle.

Opcionalmente, poderíamos retornar para Interface Builder e definir uma **ação** no controle:

[![Configurando uma nova ação](custom-controls-images/custom06.png)](custom-controls-images/custom06.png#lightbox)

Novamente, edite o `ViewController.cs` arquivo e adicione o seguinte método:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Você deve usar o **evento** ou definir uma **ação** no interface Builder, mas não deve usar ambos os métodos ao mesmo tempo ou pode entrar em conflito entre si.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo fez uma visão detalhada da criação de um controle de interface do usuário personalizado reutilizável em um aplicativo Xamarin. Mac. Vimos como desenhar a interface do usuário de controles personalizados, as duas maneiras principais de responder à entrada do mouse e do usuário e como expor o novo controle a ações no Interface Builder do Xcode.

## <a name="related-links"></a>Links Relacionados

- [MacCustomControl (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Manipulando eventos do mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
