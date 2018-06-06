---
title: Introdução ao OpenTK em Xamarin.Mac
description: Este artigo fornece uma introdução ao trabalhar com OpenTK em um aplicativo Xamarin.Mac. Ele abrange a criar e manter uma janela de jogo, renderização de um objeto simples e exibir o objeto para o usuário.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 448b8bdba8ccedbb732a73a265d0ce76bb589190
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792600"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introdução ao OpenTK em Xamarin.Mac

OpenTK (Abra o Toolkit) é uma avançada de baixo nível c# biblioteca que facilita o trabalho com OpenGL, OpenCL e OpenAL. OpenTK pode ser usado para jogos, aplicativos científicos ou outros projetos que exigem gráficos 3D, a funcionalidade de áudio ou computacional. Este artigo fornece uma breve introdução ao uso de OpenTK em um aplicativo Xamarin.Mac.

[![](opentk-images/intro01.png "Executar um aplicativo de exemplo")](opentk-images/intro01.png#lightbox)

Neste artigo, vamos abordar os fundamentos de OpenTK em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Sobre OpenTK

Como mencionado acima, OpenTK (Abra o Toolkit) é uma avançada de baixo nível c# biblioteca que facilita o trabalho com OpenGL, OpenCL e OpenAL. Usar OpenTK em um aplicativo Xamarin.Mac fornece os seguintes recursos:

- **Desenvolvimento rápido** -OpenTK fornece tipos de dados de alta segurança e documentação embutida para melhorar o fluxo de trabalho de codificação e detectar erros, mais fácil e mais cedo.
- **Integração fácil** -OpenTK foi projetado para integrar facilmente aplicativos .NET.
- **Licença permissiva** -OpenTK é distribuído sob as licenças do MIT/X11 e é totalmente gratuita.
- **Avançada, associações de tipo seguro** -OpenTK oferece suporte às versões mais recentes do OpenGL, OpenGL | ES, OpenAL e OpenCL com carregamento automático de extensão, documentação de erro de verificação e embutido.
- **Opções flexíveis de GUI** -OpenTK fornece nativo, a janela de jogo de alto desempenho projetado especificamente para jogos e Xamarin.Mac.
- **Totalmente gerenciado, código compatível com CLS** -OpenTK oferece suporte a versões de 32 bits e 64 bits do macOS com nenhuma bibliotecas não gerenciadas.
- **Kit de ferramentas de matemática 3D** OpenTK fornece `Vector`, `Matrix`, `Quaternion` e `Bezier` estruturas por meio de seu kit de ferramentas de matemática 3D.

OpenTK pode ser usado para jogos, aplicativos científicos ou outros projetos que exigem gráficos 3D, a funcionalidade de áudio ou computacional.

Para obter mais informações, consulte [o Kit de ferramentas abrir](http://www.opentk.com) site.

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Guia de início rápido OpenTK

Como uma rápida introdução às usando OpenTK em um aplicativo Xamarin.Mac, vamos criar um aplicativo simples que abre uma exibição de jogo, renderiza um triângulo simples na exibição e attachs o modo de jogo do aplicativo Mac principal janela para exibir o triângulo para o usuário.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Iniciando um novo projeto

Inicie o Visual Studio para Mac e criar uma nova solução Xamarin.Mac. Selecione **Mac** > **aplicativo** > **geral** > **Cocoa aplicativo**:

[![](opentk-images/sample01.png "Adicionando um novo aplicativo Cocoa")](opentk-images/sample01.png#lightbox)

Digite `MacOpenTK` para o **nome do projeto**:

[![](opentk-images/sample02.png "Definir o nome do projeto")](opentk-images/sample02.png#lightbox)

Clique o **criar** botão para criar o novo projeto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Incluindo OpenTK

Antes de usar TK aberto em um aplicativo de Xamarin.Mac, você precisa incluir uma referência ao assembly OpenTK. No **Solution Explorer**, com o botão direito do **referências** pasta e selecione **Editar referências...** .

Marque pelo `OpenTK` e clique no **Okey** botão:

[![](opentk-images/sample03.png "Editar as referências do projeto")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Usando OpenTK

Com o novo projeto criado, clique duas vezes o `MainWindow.cs` arquivo o **Solution Explorer** para abri-lo para edição. Verifique o `MainWindow` classe aparência semelhante ao seguinte:

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

Vamos falar sobre este código em detalhes abaixo.

<a name="Required_APIs" />

### <a name="required-apis"></a>APIs necessárias

Várias referências são necessários para usar OpenTK em uma classe Xamarin.Mac. A seguir foram incluídas no início da definição de `using` instruções:

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

### <a name="adding-the-game-view"></a>Adicionar o modo de jogo

Em seguida, precisamos criar um modo de exibição de jogo para conter todos os nossos interação com OpenTK e exibir os resultados. Usamos o seguinte código:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Aqui, fizemos o modo de jogo do mesmo tamanho que nossa janela principal do Mac e substituído a exibição de conteúdo da janela com o novo `MonoMacGameView`. Porque substituímos o conteúdo da janela existente, nosso deu a exibição será redimensionada automaticamente quando o Windows principal é redimensionado.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Respondendo a eventos

Há vários eventos padrão que cada modo de jogo deve responder. Esta seção aborda os principais eventos.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>O evento de carregamento

O `Load` evento é o local para carregar recursos de disco, como imagens, texturas ou música. Para nosso simples, o aplicativo de teste, não estamos usando o `Load` evento, mas ter incluído para referência:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>O evento de redimensionamento

O `Resize` evento deve ser chamado sempre que o modo de jogo é redimensionado. Para nosso aplicativo de exemplo, estamos fazendo o visor GL do mesmo tamanho que nossa visão de jogo (que é ser automaticamente redimensionado pela janela principal do Mac) com o código a seguir:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>O evento UpdateFrame

O `UpdateFrame` eventos é usado para manipular a entrada do usuário, atualizar posições de objeto, a execução física ou cálculos AI. Para nosso simples, o aplicativo de teste, não estamos usando o `UpdateFrame` evento, mas ter incluído para referência: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> A implementação de Xamarin.Mac de OpenTK não inclui o `Input API`, portanto, você precisará usar a Apple, desde que o suportam a APIs para adicionar o teclado e Mouse. Opcionalmente, você pode criar uma instância personalizada do `MonoMacGameView` e substituir o `KeyDown` e `KeyUp` métodos.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>O evento RenderFrame

O `RenderFrame` evento contém o código que é usado para renderizar (empate) dos elementos gráficos. Para nosso exemplo de aplicativo, estamos enchem o modo de jogo com um triângulo simple: 

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

Normalmente, o código de renderização será sendo com uma chamada para `GL.Clear` para remover os elementos existentes antes de desenho novos elementos.

> [!IMPORTANT]
> Para obter a versão Xamarin.Mac OpenTK **não** chamar o `SwapBuffers` método de sua `MonoMacGameView` instância no final do seu código de renderização. Isso fará com que o modo de jogo para strobe rapidamente, em vez de exibir o modo de exibição renderizado.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Executando o modo de jogo

Com todos os eventos necessários definir e o modo de jogo anexado à janela de Mac principal do nosso aplicativo, são lidos para executar o modo de jogo e exibir os elementos gráficos. Use o código a seguir:

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Passamos na taxa de quadros desejado que queremos que a exibição de jogo para atualizar em, em nosso exemplo, escolhemos `60` quadros por segundo (a mesma taxa de atualização como TV normal).

Vamos executar o nosso aplicativo e ver a saída:

[![](opentk-images/intro01.png "Um exemplo da saída de aplicativos")](opentk-images/intro01.png#lightbox)

Se podemos redimensionar nossa janela, o modo de jogo também serão residem e o triângulo será redimensionado e atualizado em tempo real.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>Onde Avançar?

Com os conceitos básicos de como trabalhar com OpenTk em um aplicativo de Xamarin.mac feito, aqui estão algumas sugestões do que testar Avançar:

- Tente alterar a cor do triângulo e a cor de plano de fundo da exibição de jogo no `Load` e `RenderFrame` eventos.
- Alterar o triângulo cor quando o usuário pressiona uma tecla no `UpdateFrame` e `RenderFrame` eventos ou fazer seu próprios personalizado `MonoMacGameView` classe e substituir o `KeyUp` e `KeyDown` métodos.
- Tornar o triângulo mover pela tela usando as chaves com suporte a `UpdateFrame` eventos. Dica: use o `Matrix4.CreateTranslation` método para criar uma matriz de conversão e a chamada a `GL.LoadMatrix` método carregá-lo no `RenderFrame` evento.
- Use um `for` loop para processar vários triângulos no `RenderFrame` evento.
- Gire a câmera para fornecer uma exibição diferente do triângulo no espaço 3D. Dica: use o `Matrix4.CreateTranslation` método para criar uma matriz de conversão e a chamada a `GL.LoadMatrix` método carregá-lo. Você também pode usar o `Vector2`, `Vector3`, `Vector4` e `Matrix4` classes para manipulação de câmera.

Para obter mais exemplos, consulte o [OpenTK exemplos GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples) repositório. Ele contém uma lista oficial de exemplos de como usar OpenTK. Você precisará adaptar esses exemplos de uso com a versão de Xamarin.Mac do OpenTK.

Para obter um exemplo mais complexo de Xamarin.Mac de uma implementação de OpenTK, consulte nosso [MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/) exemplo.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão geral de trabalhar com OpenTK em um aplicativo Xamarin.Mac. Vimos como criar uma janela de jogo, como anexar a janela de jogo para uma janela de Mac e como processar uma forma simple na janela do jogo.

## <a name="related-links"></a>Links relacionados

- [MacOpenTK (exemplo)](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView (exemplo)](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [O Kit de ferramentas aberta](http://www.opentk.com)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
