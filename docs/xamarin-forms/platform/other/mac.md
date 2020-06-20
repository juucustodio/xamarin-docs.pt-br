---
title: Instalação da plataforma Mac
description: Este artigo explica como adicionar um projeto do Mac a um Xamarin.Forms projeto, que produzirá um aplicativo capaz de ser executado em MacOS Sierra e MacOS El Capitan.
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 11897d2d3b8b7ba0a62956f1dbe4d8b873352e7a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139549"
---
# <a name="mac-platform-setup"></a>Instalação da plataforma Mac

![Visualização](~/media/shared/preview.png)

Antes de começar, crie (ou use um projeto existente) Xamarin.Forms . Você só pode adicionar aplicativos Mac usando Visual Studio para Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**Adicionando um projeto do macOS ao Xamarin.Forms vídeo**

## <a name="adding-a-mac-app"></a>Adicionando um aplicativo Mac

Siga estas instruções para adicionar um aplicativo Mac que será executado em macOS Sierra e macOS El Capitan:

1. Em Visual Studio para Mac, clique com o botão direito do mouse na Xamarin.Forms solução existente e escolha **Adicionar > adicionar novo projeto...**

2. Na janela **novo projeto** , escolha **Mac > aplicativo > aplicativo Cocoa** e pressione **Avançar**.

3. Digite um **nome de aplicativo** (e, opcionalmente, escolha um nome diferente para o item de encaixe) e, em seguida, pressione **Avançar**.

4. Examine a configuração e pressione **criar**. Estas etapas são mostradas abaixo:

    ![Instruções animadas mostrando como adicionar um aplicativo Cocoa](mac-images/add-macos-proj.gif)

5. No projeto do Mac, clique com o botão direito do mouse em **pacotes > adicionar pacotes...** para adicionar o [Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet. Você também deve atualizar os outros projetos para usar a mesma versão do Xamarin.Forms pacote NuGet.

6. No projeto do Mac, clique com o botão direito do mouse em **referências** e adicione uma referência ao Xamarin.Forms projeto (projeto compartilhado ou projeto de biblioteca .net Standard).

    ![Adicionar uma referência ao Xamarin.Forms projeto de código compartilhado](mac-images/references-sml.png)

7. Atualize **Main.cs** para inicializar o `AppDelegate` :

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. Atualize `AppDelegate` para inicializar Xamarin.Forms , crie uma janela e carregue o Xamarin.Forms aplicativo (lembrando de definir um apropriado `Title` ). _Se você tiver outras dependências que precisam ser inicializadas, faça isso aqui também._

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. Clique duas vezes em **Main. Storyboard** para editar no Xcode. Selecione a **janela** e _desmarque_ a caixa de seleção **é o controlador inicial** (isso ocorre porque o código acima cria uma janela):

    [![Desmarque a caixa de seleção é o controlador inicial no Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    Você pode editar o sistema de menus no storyboard para remover itens indesejados.

10. Por fim, adicione todos os recursos locais (por exemplo, arquivos de imagem) dos projetos de plataforma existentes que são necessários.

11. O projeto do Mac agora deve executar seu Xamarin.Forms código no MacOS!

## <a name="next-steps"></a>Próximas etapas

### <a name="styling"></a>Estilo

Com as alterações recentes feitas em `OnPlatform` , agora você pode direcionar qualquer número de plataformas. Isso inclui o macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Observe que você também pode dobrar em plataformas como esta: `<On Platform="iOS, macOS" ...>` .

### <a name="window-size-and-position"></a>Tamanho e posição da janela

Você pode ajustar o tamanho inicial e o local da janela no `AppDelegate` :

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemas Conhecidos

Esta é uma visualização, portanto, você deve esperar que nem tudo está pronto para produção. Abaixo estão algumas coisas que você pode encontrar ao adicionar o macOS aos seus projetos:

### <a name="not-all-nugets-are-ready-for-macos"></a>Nem todos os NuGets estão prontos para o macOS

Você pode descobrir que algumas das bibliotecas usadas ainda não dão suporte ao macOS. Nesse caso, você precisará enviar uma solicitação ao mantenedor do projeto para adicioná-lo. Até que eles tenham suporte, talvez seja necessário procurar alternativas.

### <a name="missing-xamarinforms-features"></a>Recursos ausentes Xamarin.Forms

Nem todos os Xamarin.Forms recursos estão completos nesta visualização. Para obter mais informações, consulte [status do MacOS do suporte à plataforma](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) no [Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) repositório github.

## <a name="related-links"></a>Links relacionados

- [Xamarin.Mac](~/mac/index.yml)
