---
title: Introdução ao OpenTK no xamarin. Mac
description: Este artigo fornece uma introdução ao trabalhar com OpenTK em um aplicativo xamarin. Mac. Ele aborda a criação e manter uma janela do jogo, renderização de um objeto simples e exibindo o objeto para o usuário.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 835b8cd0f2e689c4d7d4cace1d846543863b7393
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032633"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introdução ao OpenTK no xamarin. Mac

OpenTK (o Toolkit aberto) é uma avançada e de baixo nível biblioteca c# que facilita o trabalho com o OpenGL, OpenCL e OpenAL. OpenTK pode ser usado para jogos, aplicativos científicos ou outros projetos que exigem gráficos 3D, a funcionalidade de computação ou de áudio. Este artigo fornece uma breve introdução ao uso OpenTK em um aplicativo xamarin. Mac.

[![](opentk-images/intro01.png "Um execução do aplicativo de exemplo")](opentk-images/intro01.png#lightbox)

Neste artigo, abordaremos os fundamentos de OpenTK em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` comandos usado para transmissão-up suas classes de c# a objetos de Objective-C e elementos de interface do usuário.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Sobre OpenTK

Como mencionado acima, OpenTK (o Toolkit aberto) é uma avançada e de baixo nível c# biblioteca que facilita o trabalho com o OpenGL, OpenCL e OpenAL. Usar OpenTK em um aplicativo xamarin. Mac oferece os seguintes recursos:

- **Desenvolvimento rápido** -OpenTK fornece tipos de dados de alta segurança e a documentação embutida para melhorar o fluxo de trabalho de codificação e capturar erros de maneira mais fácil e mais cedo.
- **Fácil integração** -OpenTK foi projetado para integrar facilmente com os aplicativos .NET.
- **Licença permissiva** -OpenTK é distribuído sob as licenças do MIT/X11 e é totalmente gratuito.
- **Rich, associações de tipo seguro** -OpenTK suporta as versões mais recentes do OpenGL, OpenGL | ES, OpenAL e OpenCL com o carregamento automático de extensão, documentação de erro de verificação e embutido.
- **Opções flexíveis de GUI** -OpenTK fornece nativo, a janela de jogo de alto desempenho projetado especificamente para jogos e xamarin. Mac.
- **Totalmente gerenciado, um código compatível com CLS** -OpenTK dá suporte a versões de 32 bits e 64 bits do macOS com nenhum bibliotecas não gerenciadas.
- **Kit de ferramentas de matemática 3D** OpenTK fornece `Vector`, `Matrix`, `Quaternion` e `Bezier` estruturas por meio do seu kit de ferramentas de matemática 3D.

OpenTK pode ser usado para jogos, aplicativos científicos ou outros projetos que exigem gráficos 3D, a funcionalidade de computação ou de áudio.

Para obter mais informações, consulte [o Kit de ferramentas abertas](http://www.opentk.com) site.

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Guia de início rápido OpenTK

Como uma rápida introdução ao uso OpenTK em um aplicativo xamarin. Mac, vamos criar um aplicativo simples que abre um modo de exibição do jogo, renderiza um triângulo simples nessa exibição e attachs o modo de exibição de jogo para a Mac janela do aplicativo principal para exibir o triângulo para o usuário.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Iniciar um novo projeto

Inicie o Visual Studio para Mac e criar uma nova solução xamarin. Mac. Selecione **Mac** > **App** > **geral** > **aplicativo Cocoa**:

[![](opentk-images/sample01.png "Adicionando um novo aplicativo Cocoa")](opentk-images/sample01.png#lightbox)

Insira `MacOpenTK` para o **nome do projeto**:

[![](opentk-images/sample02.png "Configurando o nome do projeto")](opentk-images/sample02.png#lightbox)

Clique o **criar** botão para criar o novo projeto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Incluindo OpenTK

Antes de usar TK aberto em um aplicativo xamarin. Mac, você precisa incluir uma referência ao assembly OpenTK. No **Gerenciador de soluções**, clique com botão direito do **referências** pasta e selecione **Editar referências...** .

Colocar uma marca de seleção por `OpenTK` e clique em de **Okey** botão:

[![](opentk-images/sample03.png "Editando as referências do projeto")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Usando OpenTK

Com o novo projeto criado, clique duas vezes o `MainWindow.cs` arquivo o **Gerenciador de soluções** para abri-lo para edição. Verifique o `MainWindow` classe são semelhantes ao seguinte:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

Vamos falar sobre esse código em detalhes abaixo.

<a name="Required_APIs" />

### <a name="required-apis"></a>APIs necessárias

Várias referências devem usar OpenTK em uma classe de xamarin. Mac. No início da definição do que incluímos o seguinte `using` instruções:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
Esse conjunto mínimo será necessário para qualquer classe usando OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Adicionar exibição de jogo

Em seguida, precisamos criar uma exibição de jogo para conter todos nossa interação com OpenTK e exibir os resultados. Usamos o código a seguir:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Aqui que fizemos a exibição de jogo do mesmo tamanho que nossa janela principal do Mac e substituído a exibição de conteúdo da janela com o novo `MonoMacGameView`. Como substituímos o conteúdo da janela existente, nossa exibição deu será redimensionada automaticamente quando o Windows principal é redimensionado.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Respondendo a eventos

Há vários eventos padrão que cada modo de exibição de jogo deve responder a. Esta seção abordará os principais eventos necessários.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>O evento de carregamento

O `Load` evento é o lugar para carregar recursos de disco, como imagens, como texturas ou música. Para nosso simples e testar o aplicativo, não estamos usando o `Load` evento, mas ter incluído para referência:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>O evento de redimensionamento

O `Resize` evento deve ser chamado sempre que o modo de exibição do jogo é redimensionado. Para nosso aplicativo de exemplo, estamos fazendo o visor GL do mesmo tamanho que nossa exibição do jogo (que é ser automaticamente redimensionado, a janela principal do Mac) com o código a seguir:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>O evento UpdateFrame

O `UpdateFrame` evento é usado para manipular a entrada do usuário, atualize as posições de objeto, a execução física ou cálculos de inteligência Artificial. Para nosso simples e testar o aplicativo, não estamos usando o `UpdateFrame` evento, mas ter incluído para referência: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> A implementação do xamarin. MAC de OpenTK não inclui o `Input API`, portanto, você precisará usar o Apple desde que o suportam a APIs para adicionar o teclado e Mouse. Opcionalmente, você pode criar uma instância personalizada do `MonoMacGameView` e substitua o `KeyDown` e `KeyUp` métodos.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>O evento RenderFrame

O `RenderFrame` evento contém o código que é usado para renderizar (empate) seus elementos gráficos. Para nosso aplicativo de exemplo, podemos enchem o modo de exibição de jogo com um triângulo simple: 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

Normalmente, o código de renderização será sendo com uma chamada para `GL.Clear` para remover os elementos existentes antes de desenhado novos elementos.

> [!IMPORTANT]
> Para a versão do xamarin. MAC do OpenTK **não tiver** chamar a `SwapBuffers` método de sua `MonoMacGameView` instância no final do seu código de renderização. Isso fará com que o modo de exibição de jogo para strobe rapidamente, em vez de exibir o modo de exibição renderizado.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Executando o modo de exibição de jogo

Com todos os eventos necessários, definir e o modo de exibição do jogo é anexado à janela de Mac Main de nosso aplicativo, lemos para executar o modo de exibição do jogo e exibir nossos elementos gráficos. Use o código a seguir:

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Passamos na taxa de quadros desejada que queremos usar o modo de exibição de jogo para atualizar em, para nosso exemplo, poderíamos ter escolhido `60` quadros por segundo (a mesma taxa de atualização de TV normal).

Vamos executar nosso aplicativo e ver a saída:

[![](opentk-images/intro01.png "Um exemplo da saída de aplicativos")](opentk-images/intro01.png#lightbox)

Se redimensionamos nossa janela, o modo de exibição do jogo também será residem e o triângulo será redimensionado e também atualizado em tempo real.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>Onde Avançar?

Com os fundamentos de trabalhar com OpenTk em um aplicativo xamarin. Mac feito, aqui estão algumas sugestões sobre o que testar o próximo:

- Tente alterar a cor do triângulo e a cor de fundo da exibição no jogo a `Load` e `RenderFrame` eventos.
- Alterar o triângulo de cor quando o usuário pressiona uma tecla na `UpdateFrame` e `RenderFrame` eventos ou fazer seu próprio custom `MonoMacGameView` de classe e substituir o `KeyUp` e `KeyDown` métodos.
- Verifique o triângulo se movem pela tela usando as chaves com suporte no `UpdateFrame` eventos. Dica: use o `Matrix4.CreateTranslation` método para criar uma matriz de translação e a chamada a `GL.LoadMatrix` método carregá-lo no `RenderFrame` eventos.
- Use uma `for` loop para processar vários triângulos no `RenderFrame` eventos.
- Gire a câmera para fornecer uma exibição diferente do triângulo no espaço 3D. Dica: use o `Matrix4.CreateTranslation` método para criar uma matriz de translação e a chamada a `GL.LoadMatrix` método carregá-lo. Você também pode usar o `Vector2`, `Vector3`, `Vector4` e `Matrix4` classes para manipulações de câmera.

Para obter mais exemplos, consulte o [GitHub de exemplos de OpenTK](https://github.com/opentk/opentk/tree/master/Source/Examples) repositório. Ele contém uma lista oficial de exemplos de como usar OpenTK. Você precisará adaptar esses exemplos para usar com a versão do xamarin. MAC do OpenTK.

Para obter um exemplo mais complexo do xamarin. MAC de uma implementação de OpenTK, consulte nosso [MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/) exemplo.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão geral de trabalhar com OpenTK em um aplicativo xamarin. Mac. Vimos como criar uma janela do jogo, como anexar a janela de jogo para uma janela de Mac e como renderizar uma forma simple na janela do jogo.

## <a name="related-links"></a>Links relacionados

- [MacOpenTK (amostra)](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView (amostra)](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [O Kit de ferramentas aberta](http://www.opentk.com)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
