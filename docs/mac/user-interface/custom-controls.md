---
title: Criação de controles personalizados no xamarin. Mac
description: Este documento descreve como criar controles personalizados no xamarin. Mac. Ele mostra como criar o controle personalizado, acompanhar seu estado, desenhar sua interface, responder à entrada do usuário e usar o controle em um aplicativo.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 015c1e315b6070777542a8f8c5871c00cf336b5c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61236043"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Criação de controles personalizados no xamarin. Mac

Ao trabalhar com C# e o .NET em um aplicativo xamarin. Mac, você terá acesso ao mesmo usuário que controla um desenvolvedor que trabalha *Objective-C*, *Swift* e *Xcode*faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter seus controles de usuário (ou, opcionalmente, criá-los diretamente em código c#).

Embora o macOS fornece uma série de controles de usuário internos, pode haver vezes em que você precisa criar um controle personalizado para fornecer a funcionalidade fornecida não out-of-the-box ou para corresponder a um tema personalizado da interface do usuário (como uma interface do jogo).

[![](custom-controls-images/intro01.png "Exemplo de um controle de interface do usuário personalizado")](custom-controls-images/intro01.png#lightbox)

Neste artigo, abordaremos os fundamentos da criação de um controle de Interface de usuário personalizada reutilizável em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` comandos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introdução aos controles personalizados

Como mencionado acima, pode haver ocasiões em que você precisa para criar um reutilizável, controle de Interface de usuário personalizado para fornecer funcionalidade exclusiva para interface de usuário do seu aplicativo xamarin. Mac ou para criar um tema personalizado da interface do usuário (como uma interface do jogo).

Nessas situações, você pode facilmente herdar de `NSControl` e criar uma ferramenta personalizada que pode ser adicionada à interface de usuário do seu aplicativo por meio de código c# ou Interface Builder do Xcode. Herdando `NSControl` seu controle personalizado terá automaticamente todos os recursos padrão que tem um controle de Interface do usuário interna (como `NSButton`).

Se seu controle personalizado de Interface do usuário exibe apenas informações (como um gráfico personalizado e a ferramenta gráfica), você talvez queira herdam `NSView` em vez de `NSControl`.

Não importa qual classe base é usada, as etapas básicas para criar um controle personalizado é o mesmo.

Este artigo ajudará, cria um componente de comutador Inverter personalizado que fornece um tema de Interface de usuário exclusivo e um exemplo de criação de um controle de Interface de usuário personalizado totalmente funcional.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Criando o controle personalizado

Uma vez que o controle personalizado que estamos criando será ser responder à entrada do usuário (cliques de botão esquerdo do mouse), vamos para herdar de `NSControl`. Dessa forma, nosso controle personalizado terá automaticamente todos os recursos padrão que tem um controle de Interface do usuário interno e respondem como um controle padrão do macOS.

No Visual Studio para Mac, abra o projeto do xamarin. MAC que você deseja criar um controle de Interface do usuário personalizada para (ou crie um novo). Adicione uma nova classe e chamá-lo `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Adicionando uma nova classe")](custom-controls-images/custom01.png#lightbox)

Em seguida, edite o `NSFlipSwitch.cs` de classe e torná-lo semelhante ao seguinte:

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

A primeira coisa a observar sobre nossa classe personalizada que podemos herdando `NSControl` e usando o **registrar** comando para expor essa classe para Objective-C e do Xcode Interface Builder:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Nas seções a seguir, vamos dar uma olhada no restante do código acima em detalhes.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Controlar o estado do controle

Como o nosso controle personalizado é um comutador, precisamos de uma maneira de controlar o estado On/Off do comutador. Podemos tratar disso com o código a seguir no `NSFlipSwitch`:

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

Quando muda o estado do comutador, precisamos de uma maneira atualizada a interface do usuário. Podemos fazer isso, forçando o controle redesenhe sua interface do usuário com `NeedsDisplay = true`.

Se nosso controle necessário mais do que um único estado (por exemplo um comutador de vários estado com posições de 3) ligado/desligado, poderíamos ter usado um **Enum** para acompanhar o estado. Para nosso exemplo, um simples **bool** fará.

Também adicionamos um método auxiliar para alternar o estado da opção entre ativar e desativar:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Posteriormente, expandiremos a classe auxiliar para informar o chamador quando o estado de comutadores foi alterado.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>A Interface do controle de desenho

Vamos usar rotinas de desenho gráfico principal para desenhar a Interface do usuário do nosso controle personalizado em tempo de execução. Antes de podermos fazer isso, precisamos ativar camadas para nosso controle. Fazemos isso com o seguinte método privado:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Esse método é chamado de cada um dos construtores do controle para garantir que o controle está configurado corretamente. Por exemplo:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Em seguida, precisamos substituir o `DrawRect` método e adicione as rotinas de núcleo gráfico para desenhar o controle:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Vai, ser ajustando a representação visual do controle quando seu estado é alterado (como indo do **na** para **Off**). Sempre que as alterações de estado, podemos usar o `NeedsDisplay = true` comando para forçar o controle redesenhe para o novo estado.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Responder à entrada do usuário

Há duas maneiras básicas que podemos adicionar entrada do usuário para o nosso controle personalizado: **Substituir as rotinas de tratamento de Mouse** ou **reconhecedores de gestos**. Método que podemos usar, se basearão na funcionalidade exigida pelo nosso controle.

> [!IMPORTANT]
> Para qualquer controle personalizado que você cria, você deve usar **substituir métodos** _ou_ **reconhecedores de gestos**, mas não ambos ao mesmo tempo como eles podem entrar em conflito uns com os outros.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Manipulando a entrada do usuário com os métodos de substituição

Objetos que herdam `NSControl` (ou `NSView`) ter vários substituir métodos para manipulação de mouse ou entrada do teclado. Para nosso controle de exemplo, queremos inverter o estado da opção entre **na** e **Off** quando o usuário clica no controle com o botão esquerdo do mouse. Podemos adicionar o seguinte substituir métodos para o `NSFlipSwitch` classe para lidar com isso:

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

No código acima, podemos chamar o `FlipSwitchState` método (definido acima) para inverter a On/Off estado do comutador no `MouseDown` método. Isso também forçará o controle seja redesenhado para refletir o estado atual.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Manipulando a entrada do usuário com reconhecedores de gestos

Opcionalmente, você pode usar os reconhecedores de gestos para lidar com o usuário interagir com o controle. Remover as substituições adicionadas acima, edite o `Initialize` método e torná-lo semelhante ao seguinte:

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

Aqui, estamos criando uma nova `NSClickGestureRecognizer` e chamar nossa `FlipSwitchState` método para alterar o estado da opção quando o usuário clica nele com o botão esquerdo do mouse. O `AddGestureRecognizer (click)` método adiciona o reconhecedor de gestos ao controle.

Novamente, o método que podemos usar depende o que estamos tentando fazer com nosso controle personalizado. Se for necessário acesso de nível baixo à interação do usuário, use os métodos de substituição. Se precisarmos de funcionalidade predefinida, como cliques do mouse, use reconhecedores de gestos.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Respondendo a eventos de alteração de estado

Quando o usuário altera o estado do nosso controle personalizado, precisamos de uma maneira para responder a alterações de estado no código (tal como fazer algo quando clica em um botão personalizado). 

Para fornecer essa funcionalidade, edite o `NSFlipSwitch` de classe e adicione o seguinte código:

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

Em seguida, edite o `FlipSwitchState` método e torná-lo semelhante ao seguinte:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

Primeiro, fornecemos um `ValueChanged` eventos que podemos adicionar um manipulador no código c# para que podemos executar uma ação quando o usuário altera o estado do comutador.

Segundo, porque o nosso controle personalizado herda `NSControl`, ele tem automaticamente um **ação** que podem ser atribuídas no Interface Builder do Xcode. Chamá-la **ação** quando o estado é alterado, podemos usar o código a seguir:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Primeiro, verificamos para ver se um **ação** foi atribuído ao controle. Em seguida, chamamos o **ação** se ele tiver sido definido.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Usando o controle personalizado

Com nosso controle personalizado totalmente definido, podemos pode adicioná-lo para da interface do usuário do nosso aplicativo xamarin. Mac usando um código c# ou no Interface Builder do Xcode.

Para adicionar o controle usando o Interface Builder, primeiro fazer uma compilação limpa do projeto xamarin. Mac, clique duas vezes o `Main.storyboard` arquivo para abri-lo no construtor de Interface para edição:

[![](custom-controls-images/custom02.png "Editando o storyboard no Xcode")](custom-controls-images/custom02.png#lightbox)

Em seguida, arraste um `Custom View` com o design de Interface do usuário:

[![](custom-controls-images/custom03.png "Selecionar uma exibição personalizada da biblioteca")](custom-controls-images/custom03.png#lightbox)

Com o modo de exibição personalizado ainda selecionada, alterne para o **Inspetor de identidade** e altere o modo de exibição **classe** para `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Classe do modo de exibição de configuração")](custom-controls-images/custom04.png#lightbox)

Alterne para o **Editor assistente** e criar um **tomada** para o controle personalizado (certificando-se de associá-lo no `ViewController.h` arquivo e não o `.m` arquivo):

[![](custom-controls-images/custom05.png "Configurando uma nova saída")](custom-controls-images/custom05.png#lightbox)

Salve suas alterações, retorne ao Visual Studio para Mac e permitir que as alterações sincronizar. Editar o `ViewController.cs` do arquivo e verifique o `ViewDidLoad` método são semelhantes ao seguinte:

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

Aqui, podemos responder à `ValueChanged` evento definido acima sobre o `NSFlipSwitch` de classe e de gravação out atual **valor** quando o usuário clica no controle.

Opcionalmente, poderíamos retornar ao Interface Builder e definir um **ação** no controle:

[![](custom-controls-images/custom06.png "Configurando uma nova ação")](custom-controls-images/custom06.png#lightbox)

Novamente, editar o `ViewController.cs` arquivo e adicione o seguinte método:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Você deve usar o **evento** ou definir um **ação** no construtor de Interface, mas você não deve usar os dois métodos ao mesmo tempo ou eles podem entrar em conflito uns com os outros.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como criar um controle de Interface de usuário personalizada reutilizável em um aplicativo xamarin. Mac. Vimos como desenhar controles personalizados da interface do usuário, as duas maneiras principais para responder à entrada do usuário e de mouse e como expor o novo controle para ações no Interface Builder do Xcode.

## <a name="related-links"></a>Links relacionados

- [MacCustomControl (amostra)](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Manipulação de eventos de Mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
