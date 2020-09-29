---
title: Introdução ao OpenTK no Xamarin. Mac
description: Este artigo fornece uma introdução ao trabalho com o OpenTK em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de uma janela de jogo, a renderização de um objeto simples e a exibição do objeto para o usuário.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7f600593d7a3bb180ef8daca6639dbbea4bf07aa
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430070"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introdução ao OpenTK no Xamarin. Mac

O OpenTK (The Open Toolkit) é uma biblioteca C# avançada e de baixo nível que facilita o trabalho com OpenGL, OpenCL e OpenAL. O OpenTK pode ser usado para jogos, aplicativos científicos ou outros projetos que exigem gráficos 3D, áudio ou funcionalidade computacional. Este artigo fornece uma breve introdução ao uso do OpenTK em um aplicativo Xamarin. Mac.

[![Uma execução de aplicativo de exemplo](opentk-images/intro01.png)](opentk-images/intro01.png#lightbox)

Neste artigo, abordaremos os conceitos básicos do OpenTK em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` comandos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

<a name="About_OpenTK"></a>

## <a name="about-opentk"></a>Sobre o OpenTK

Conforme mencionado acima, OpenTK (The Open Toolkit) é uma biblioteca C# avançada e de baixo nível que facilita o trabalho com OpenGL, OpenCL e OpenAL. O uso de OpenTK em um aplicativo Xamarin. Mac fornece os seguintes recursos:

- **Desenvolvimento rápido** – o OpenTK fornece tipos de dados fortes e documentação embutida para aprimorar o fluxo de trabalho de codificação e detectar erros com mais facilidade e antecedência.
- **Fácil integração** -o OpenTK foi projetado para integrar facilmente com aplicativos .net.
- **Licença permissiva** – o OpenTK é distribuído sob as licenças do MIT/X11 e é totalmente gratuito.
- **Associações avançadas de tipo seguro** -o OpenTK dá suporte às versões mais recentes do OpenGL, OpenGL | ES, OpenAL e OpenCL com carregamento automático de extensão, verificação de erros e documentação embutida.
- **Opções de GUI flexíveis** -o OpenTK fornece a janela de jogos nativa e de alto desempenho projetada especificamente para jogos e Xamarin. Mac.
- **Código totalmente gerenciado, compatível com CLS** -OpenTK dá suporte a versões de 32 bits e 64 bits do MacOS sem bibliotecas não gerenciadas.
- **Kit de ferramentas de matemática 3D** OpenTK fornece `Vector` `Matrix` estruturas, `Quaternion` e `Bezier` structs por meio de seu kit de ferramentas de matemática 3D.

O OpenTK pode ser usado para jogos, aplicativos científicos ou outros projetos que exigem gráficos 3D, áudio ou funcionalidade computacional.

Para obter mais informações, consulte [o site do kit de ferramentas aberto](https://opentk.net) .

<a name="OpenTK_Quickstart"></a>

## <a name="opentk-quickstart"></a>Início rápido do OpenTK

Como uma rápida introdução ao uso do OpenTK em um aplicativo Xamarin. Mac, vamos criar um aplicativo simples que abre uma exibição de jogo, renderiza um triângulo simples nessa exibição e anexa a exibição do jogo à janela principal do aplicativo Mac para exibir o triângulo para o usuário.

<a name="Starting_a_New_Project"></a>

### <a name="starting-a-new-project"></a>Iniciando um novo projeto

Inicie o Visual Studio para Mac e crie uma nova solução Xamarin. Mac. Selecione aplicativo **Mac**  >  **App**  >  **geral**  >  **aplicativo Cocoa**:

[![Adicionando um novo aplicativo Cocoa](opentk-images/sample01.png)](opentk-images/sample01.png#lightbox)

Insira `MacOpenTK` para o **nome do projeto**:

[![Definindo o nome do projeto](opentk-images/sample02.png)](opentk-images/sample02.png#lightbox)

Clique no botão **criar** para criar o novo projeto.

<a name="Including_OpenTK"></a>

### <a name="including-opentk"></a>Incluindo OpenTK

Antes de poder usar o Open TK em um aplicativo Xamarin. Mac, você precisa incluir uma referência ao assembly OpenTK. Na **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **referências** e selecione **Editar referências..**..

Faça um check-in `OpenTK` e clique no botão **OK** :

[![Editando as referências do projeto](opentk-images/sample03.png)](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK"></a>

### <a name="using-opentk"></a>Usando OpenTK

Com o novo projeto criado, clique duas vezes no `MainWindow.cs` arquivo no **Gerenciador de soluções** para abri-lo para edição. Faça com que a `MainWindow` classe fique parecida com a seguinte:

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

Vamos examinar esse código em detalhes abaixo.

<a name="Required_APIs"></a>

### <a name="required-apis"></a>APIs necessárias

Várias referências são necessárias para usar o OpenTK em uma classe Xamarin. Mac. No início da definição, incluímos as seguintes `using` instruções:

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

<a name="Adding_the_Game_View"></a>

### <a name="adding-the-game-view"></a>Adicionando a exibição do jogo

Em seguida, precisamos criar uma exibição de jogo para conter toda a nossa interação com o OpenTK e exibir os resultados. Usamos o seguinte código:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Aqui, fizemos a exibição do jogo do mesmo tamanho que nossa janela principal do Mac e substituímos a exibição de conteúdo da janela pela nova `MonoMacGameView` . Como substituímos o conteúdo da janela existente, nosso modo de exibição fornecido será redimensionado automaticamente quando as janelas principais forem redimensionadas.

<a name="Responding_to_Events"></a>

### <a name="responding-to-events"></a>Respondendo a eventos

Há vários eventos padrão aos quais cada exibição do jogo deve responder. Nesta seção, serão abordados os principais eventos necessários.

<a name="The_Load_Event"></a>

### <a name="the-load-event"></a>O evento de carregamento

O `Load` evento é o local para carregar recursos do disco, como imagens, texturas ou músicas. Para nosso aplicativo de teste simples, não estamos usando o `Load` evento, mas o incluímos para referência:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event"></a>

### <a name="the-resize-event"></a>O evento de redimensionamento

O `Resize` evento deve ser chamado toda vez que a exibição do jogo é redimensionada. Para nosso aplicativo de exemplo, estamos tornando o visor GL o mesmo tamanho que o nosso modo de exibição de jogo (que é redimensionado automaticamente pela janela principal do Mac) com o seguinte código:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event"></a>

### <a name="the-updateframe-event"></a>O evento UpdateFrame

O `UpdateFrame` evento é usado para lidar com a entrada do usuário, atualizar posições de objeto, executar cálculos de física ou de ia. Para nosso aplicativo de teste simples, não estamos usando o `UpdateFrame` evento, mas o incluímos para referência:

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> A implementação do Xamarin. Mac de OpenTK não inclui o `Input API` , portanto, será necessário usar as APIs fornecidas pela Apple para adicionar suporte a teclado e mouse. Opcionalmente, você pode criar uma instância personalizada do `MonoMacGameView` e substituir os `KeyDown` `KeyUp` métodos e.

<a name="The_RenderFrame_Event"></a>

### <a name="the-renderframe-event"></a>O evento RenderFrame

O `RenderFrame` evento contém o código usado para renderizar (desenhar) seus elementos gráficos. Para nosso aplicativo de exemplo, estamos preenchendo a exibição do jogo com um triângulo simples:

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

Normalmente, o código de renderização estará com uma chamada para `GL.Clear` para remover qualquer elemento existente antes de desenhar os novos elementos.

> [!IMPORTANT]
> Para a versão Xamarin. Mac do OpenTK, **não** chame o `SwapBuffers` método de sua `MonoMacGameView` instância no final do seu código de renderização. Isso fará com que a exibição do jogo seja estroboscópico rapidamente em vez de exibir o modo de exibição renderizado.

<a name="Running_the_Game_View"></a>

### <a name="running-the-game-view"></a>Executando a exibição do jogo

Com todos os eventos necessários definem e a exibição do jogo anexada à janela principal do Mac do nosso aplicativo, somos lidos para executar a exibição do jogo e exibir nossos elementos gráficos. Use o seguinte código:

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Passamos a taxa de quadros desejada que queremos que a exibição do jogo atualize em, para nosso exemplo, escolhemos `60` quadros por segundo (a mesma taxa de atualização que a TV normal).

Vamos executar nosso aplicativo e ver a saída:

[![Um exemplo de saída de aplicativos](opentk-images/intro01.png)](opentk-images/intro01.png#lightbox)

Se redimensionarmos nossa janela, a exibição do jogo também residirá e o triângulo será redimensionado e atualizado em tempo real também.

<a name="Where_to_Next"></a>

### <a name="where-to-next"></a>Para onde avançar?

Com os fundamentos de trabalhar com OpenTk em um aplicativo Xamarin. Mac concluído, aqui estão algumas sugestões do que testar em seguida:

- Tente alterar a cor do triângulo e a cor da tela de fundo da exibição do jogo `Load` nos `RenderFrame` eventos e.
- Faça com que o triângulo altere a cor quando o usuário pressionar uma tecla nos `UpdateFrame` `RenderFrame` eventos e ou criar sua própria `MonoMacGameView` classe personalizada e substituir os `KeyUp` `KeyDown` métodos e.
- Faça com que o triângulo se mova pela tela usando as teclas de reconhecimento no `UpdateFrame` evento. Dica: Use o `Matrix4.CreateTranslation` método para criar uma matriz de conversão e chamar o `GL.LoadMatrix` método para carregá-lo no `RenderFrame` evento.
- Use um `for` loop para renderizar vários triângulos no `RenderFrame` evento.
- Gire a câmera para dar uma exibição diferente do triângulo no espaço 3D. Dica: Use o `Matrix4.CreateTranslation` método para criar uma matriz de conversão e chamar o `GL.LoadMatrix` método para carregá-lo. Você também pode usar as `Vector2` `Vector3` classes, `Vector4` e `Matrix4` para manipulações de câmera.

Para obter mais exemplos, consulte o repositório [GitHub de exemplos de OpenTK](https://github.com/opentk/opentk/tree/master/Source/Examples) . Ele contém uma lista oficial de exemplos de como usar OpenTK. Você precisará adaptar esses exemplos para uso com a versão Xamarin. Mac do OpenTK.

Para obter um exemplo mais complexo do Xamarin. Mac de uma implementação de OpenTK, consulte nosso exemplo de [MonoMacGameView](/samples/xamarin/mac-samples/monomacgamewindow) .

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo deu uma olhada rápida no trabalho com o OpenTK em um aplicativo Xamarin. Mac. Vimos como criar uma janela de jogo, como anexar a janela do jogo a uma janela do Mac e como renderizar uma forma simples na janela do jogo.

## <a name="related-links"></a>Links Relacionados

- [MacOpenTK (exemplo)](/samples/xamarin/mac-samples/macopentk)
- [MonoMacGameView (exemplo)](/samples/xamarin/mac-samples/monomacgamewindow)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [O kit de ferramentas aberto](https://opentk.net)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)