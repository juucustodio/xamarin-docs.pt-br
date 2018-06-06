---
title: Criando controles personalizados no Xamarin.Mac
description: Este documento descreve como criar controles personalizados em Xamarin.Mac. Ele mostra como criar o controle personalizado, acompanhar seu estado, desenhar sua interface, responder à entrada do usuário e usar o controle em um aplicativo.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e4c2b2c9ee7bae3d6489fec6b22881653ec53043
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792672"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Criando controles personalizados no Xamarin.Mac

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso ao mesmo usuário que controla um desenvolvedor trabalhando em *Objective-C*, *Swift* e *Xcode* does . Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter seus controles de usuário (ou, opcionalmente, criá-los diretamente no código do c#).

Enquanto macOS fornece uma variedade de controles internos de usuário, pode haver momentos em que você precisa criar um controle personalizado para fornecer a funcionalidade fornecida não da caixa ou para corresponder a um tema personalizado da interface do usuário (como uma interface de jogo).

[![](custom-controls-images/intro01.png "Exemplo de um controle de interface do usuário personalizado")](custom-controls-images/intro01.png#lightbox)

Neste artigo, vamos abordar os fundamentos de criação de um controle de Interface de usuário personalizada reutilizáveis em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introdução aos controles personalizados

Como mencionado acima, pode haver vezes em que você precisa criar um reutilizável, controle de Interface de usuário personalizado para fornecer funcionalidade exclusiva para a interface de usuário do seu aplicativo Xamarin.Mac ou para criar um tema personalizado da interface do usuário (como uma interface de jogo).

Nessas situações, você pode facilmente herdar `NSControl` e criar uma ferramenta personalizada que pode ser adicionada à interface de usuário do aplicativo por meio de código c# ou do construtor de Interface do Xcode. Herdando de `NSControl` o controle personalizado automaticamente terá todos os recursos padrão que tem um controle de Interface do usuário internas (como `NSButton`).

Se o controle de Interface de usuário personalizado apenas exibe informações (como um gráfico personalizado e a ferramenta gráfica), você talvez queira herdam `NSView` em vez de `NSControl`.

Não importa qual classe base é usada, as etapas básicas para criar um controle personalizado é o mesmo.

No artigo serão, crie um componente de comutador Inverter personalizado que fornece um tema de Interface de usuário exclusivo e um exemplo de criação de um controle de Interface de usuário personalizado totalmente funcional.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Criando o controle personalizado

Como o controle personalizado, estamos criando será ser respondendo a entrada do usuário (cliques de botão esquerdo do mouse), vamos herdam `NSControl`. Dessa forma, nosso controle personalizado terá automaticamente todos os recursos padrão que tem um controle de Interface de usuário interno e respondem como um controle padrão macOS.

No Visual Studio para Mac, abra o projeto Xamarin.Mac que você deseja criar um controle de Interface de usuário personalizada para (ou crie um novo). Adicionar uma nova classe e chamá-lo `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Adicionando uma nova classe")](custom-controls-images/custom01.png#lightbox)

Em seguida, edite o `NSFlipSwitch.cs` classe e torná-lo a aparência a seguir:

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

A primeira coisa a observar sobre nossa classe personalizada que herdam da `NSControl` e usando o **registrar** comando expor essa classe para Objective-C e do Xcode construtor de Interface:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Nas seções a seguir, vamos dar uma olhada no restante do código acima em detalhes.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>O estado do controle de rastreamento

Como o nosso controle personalizado é um comutador, precisamos de uma maneira de controlar o estado ativado/desativado do comutador. Podemos lidar com isso com o código a seguir no `NSFlipSwitch`:

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

Quando o estado da opção alterado, precisamos de uma maneira de atualizar a interface do usuário. Podemos fazer isso, forçando o controle redesenhar sua interface de usuário com `NeedsDisplay = true`.

Se nosso controle necessário mais que um único ativar/desativar estado (por exemplo um comutador vários estado com 3 posições), poderíamos ter usado um **Enum** para controlar o estado. Para nosso exemplo, um simples **bool** fará.

Também adicionamos um método auxiliar para trocar o estado da opção entre logon e logoff:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Posteriormente, podemos expandir esta classe auxiliar para informar o chamador quando o estado de comutadores foi alterado.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>A Interface do controle de desenho

Vamos usar rotinas de desenho gráfico principal para desenhar a Interface do usuário do nosso controle personalizado em tempo de execução. Antes de fazer isso, é preciso ativar camadas de nosso controle. Podemos fazer isso com o seguinte método particular:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Esse método é chamado de cada um dos construtores de controle para garantir que o controle está configurado corretamente. Por exemplo:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Em seguida, é preciso substituir o `DrawRect` método e adicione as rotinas de núcleo gráfico para desenhar o controle:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Vai, ser ajustando a representação visual do controle quando seu estado é alterado (como indo de **na** para **Off**). Sempre que as alterações de estado, podemos usar o `NeedsDisplay = true` comando para forçar o controle a redesenhar para o novo estado.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Responder à entrada do usuário

Há duas vias básico que podemos adicionar a entrada do usuário para nosso controle personalizado: **rotinas de manipulação de Mouse substituir** ou **reconhecedores de gestos**. Método que podemos usar, se baseará a funcionalidade exigida pelo nosso controle.

> [!IMPORTANT]
> Para qualquer controle personalizado que você criar, você deve usar **substituir métodos** _ou_ **reconhecedores de gestos**, mas não ambos ao mesmo tempo podem estar em conflito entre si.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Tratamento de entrada do usuário com métodos de substituição

Objetos que herdam de `NSControl` (ou `NSView`) tem vários substituir os métodos de manipulação de mouse ou teclado de entrada. Para o controle nosso exemplo, queremos virar o estado da opção entre **na** e **Off** quando o usuário clica no controle com o botão esquerdo do mouse. É possível adicionar o seguinte substituir métodos para o `NSFliwSwitch` classe para lidar com isso:

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

No código acima, podemos chamar o `FlipSwitchState` método (definido acima) para inverter a On/Off estado do comutador de `MouseDown` método. Isso também forçará o controle seja redesenhado para refletir o estado atual.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Tratamento de entrada do usuário com reconhecedores de gestos

Opcionalmente, você pode usar reconhecedores de gestos para lidar com o usuário interagir com o controle. Remova as substituições adicionadas acima, edite o `Initialize` método e torná-lo a aparência a seguir:

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

Aqui, estamos criando uma nova `NSClickGestureRecognizer` e chamando nosso `FlipSwitchState` método para alterar o estado do comutador quando o usuário clica nela com o botão esquerdo do mouse. O `AddGestureRecognizer (click)` método adiciona o reconhecedor de gestos para o controle.

Novamente, o método usamos depende o que estamos tentando realizar com nosso controle personalizado. Se é necessário o acesso de nível baixo à interação do usuário, use os métodos de substituição. Se houver necessidade de recursos predefinidos, como cliques do mouse, use reconhecedores de gestos.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Respondendo a eventos de alteração de estado

Quando o usuário altera o estado de nosso controle personalizado, precisamos de uma maneira para responder a alterações de estado no código (como fazer algo quando clica em um botão personalizado). 

Para fornecer essa funcionalidade, edite o `NSFlipSwitch` classe e adicione o seguinte código:

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

Em seguida, edite o `FlipSwitchState` método e torná-lo a aparência a seguir:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

Primeiro, fornecemos um `ValueChanged` eventos que podemos adicionar um manipulador no código do c# para que podemos pode executar uma ação quando o usuário altera o estado do comutador.

Segundo, como nosso controle personalizado herda de `NSControl`, ele automaticamente tem um **ação** que podem ser atribuídos no construtor de Interface do Xcode. Para chamar essa **ação** quando o estado é alterado, usamos o seguinte código:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Primeiro, verificamos se um **ação** foi atribuído ao controle. Em seguida, podemos chamar o **ação** se ele tiver sido definido.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Usando o controle personalizado

Com nosso controle personalizado totalmente definido, podemos ou pode adicioná-lo para interface do usuário da nosso aplicativo Xamarin.Mac usando o código c# ou no construtor de Interface do Xcode.

Para adicionar o controle usando o construtor de Interface, primeiro faça uma compilação limpa de projeto Xamarin.Mac, clique duas vezes o `Main.storyboard` arquivo para abri-lo no construtor de Interface para edição:

[![](custom-controls-images/custom02.png "Editando o storyboard no Xcode")](custom-controls-images/custom02.png#lightbox)

Em seguida, arraste um `Custom View` no design de Interface do usuário:

[![](custom-controls-images/custom03.png "Selecionar uma exibição personalizada da biblioteca")](custom-controls-images/custom03.png#lightbox)

Com o modo de exibição personalizado ainda selecionada, alterne para o **Inspetor de identidade** e alterar o modo de exibição **classe** para `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Classe do modo de exibição de configuração")](custom-controls-images/custom04.png#lightbox)

Alterne para o **Assistente Editor** e criar um **tomada** para o controle personalizado (certificando-se para associá-lo no `ViewControler.h` arquivo e não o `.m` arquivo):

[![](custom-controls-images/custom05.png "Configurando uma nova loja")](custom-controls-images/custom05.png#lightbox)

Salve suas alterações, retorne ao Visual Studio para Mac e permita que as alterações para sincronização. Editar o `ViewController.cs` de arquivo e verifique o `ViewDidLoad` método aparência semelhante ao seguinte:

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

Aqui, podemos responder ao `ValueChanged` evento definimos acima no `NSFlipSwitch` classe e gravar atual **valor** quando o usuário clica no controle.

Opcionalmente, poderíamos retornar para o construtor de Interface e definir um **ação** no controle:

[![](custom-controls-images/custom06.png "Configurando uma nova ação")](custom-controls-images/custom06.png#lightbox)

Novamente, editar o `ViewController.cs` de arquivo e adicione o seguinte método:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> Você deve usar o **evento** ou definir um **ação** no construtor de Interface, mas você não deve usar ambos os métodos ao mesmo tempo ou podem estar em conflito entre si.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como criar um controle de Interface de usuário personalizada reutilizáveis em um aplicativo Xamarin.Mac. Vimos como desenhar controles personalizados da interface do usuário, as duas maneiras principais para responder a entrada do usuário e de mouse e como expor o novo controle ações no construtor de Interface do Xcode.

## <a name="related-links"></a>Links relacionados

- [MacCustomControl (exemplo)](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Manipulação de eventos de Mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
