---
title: Configuração de plataforma do Mac
description: Este artigo explica como adicionar um projeto do Mac para um projeto xamarin. Forms, que produzirá um aplicativo capaz de executar no macOS Sierra e macOS El Capitan.
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: 3a488b3a9f729da5d4bee8c1262190b15c2e9240
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902645"
---
# <a name="mac-platform-setup"></a>Configuração de plataforma do Mac

![Visualizar](~/media/shared/preview.png)

Antes de começar, crie (ou usar uma existente) projeto xamarin. Forms. Você só pode adicionar aplicativos do Mac usando o Visual Studio para Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**A adição de um projeto do macOS para xamarin. Forms, por [Xamarin University](https://university.xamarin.com/)**

## <a name="adding-a-mac-app"></a>Adicionar um aplicativo do Mac

Siga estas instruções para adicionar um aplicativo do Mac que será executado no macOS Sierra e macOS El Capitan:

1. No Visual Studio para Mac, clique com botão direito na solução do xamarin. Forms existente e escolha **Adicionar > Adicionar novo projeto...**

2. No **novo projeto** janela escolher **Mac > aplicativo > aplicativo Cocoa** e pressione **próxima**.

3. Tipo de um **nome do aplicativo** (e, opcionalmente, escolha um nome diferente para o Item do Dock), em seguida, pressione **próxima**.

4. Examine a configuração e pressione **criar**. Essas etapas são mostradas no abaixo:

    ![Instruções animadas mostrando como adicionar um aplicativo Cocoa](mac-images/add-macos-proj.gif)

5. No projeto do Mac, clique duas vezes em **pacotes > Adicionar pacotes...**  para adicionar o [xamarin. Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet. Você também deve atualizar os outros projetos para usar a mesma versão do pacote NuGet do xamarin. Forms.

6. No projeto do Mac, clique duas vezes em **referências** e adicione uma referência ao projeto xamarin. Forms (projeto de biblioteca de projeto compartilhado ou .NET Standard).

    ![Adicione uma referência ao projeto de código compartilhado do xamarin. Forms](mac-images/references-sml.png)

7. Atualização **Main.cs** para inicializar o `AppDelegate`:

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

8. Atualização `AppDelegate` para inicializar o xamarin. Forms, criar uma janela e carregar o aplicativo xamarin. Forms (Lembre-se de definir um apropriado `Title`). _Se você tiver outras dependências que precisam ser inicializados, fazer isso aqui também._

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

9. Clique duas vezes em **Main. Storyboard** para editar no Xcode. Selecione o **janela** e _desmarque_ o **controlador inicial é** caixa de seleção (Isso ocorre porque o código anterior cria uma janela):

    [![Desmarque a caixa de seleção de controlador inicial é no Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    Você pode editar o sistema de menus no storyboard para remover os itens indesejados.

10. Por fim, adicione qualquer recurso local (por exemplo. arquivos de imagem) de projetos de plataforma existentes que são necessários.

11. Projeto do Mac agora deve executar seu código do xamarin. Forms em macOS!

## <a name="next-steps"></a>Próximas etapas

### <a name="styling"></a>Estilo

Com as alterações recentes feitas `OnPlatform` agora você pode direcionar qualquer número de plataformas. Isso inclui o macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Observe que você também pode dobrar em plataformas como este: `<On Platform="iOS, macOS" ...>`.

### <a name="window-size-and-position"></a>Posição e tamanho da janela

Você pode ajustar o tamanho inicial e o local da janela no `AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemas Conhecidos

Esta é uma visualização, portanto, você deve esperar que nem tudo está pronto para produção. Abaixo estão algumas coisas que você pode encontrar conforme você adiciona o macOS para seus projetos:

### <a name="not-all-nugets-are-ready-for-macos"></a>Nem todos os NuGets estão prontos para o macOS

Você pode achar que algumas das bibliotecas que você usa não ainda dão suporte a macOS. Nesse caso, você precisará enviar uma solicitação para o mantenedor do projeto para adicioná-lo. Até que tenham suporte, você precisa procurar alternativas.

### <a name="missing-xamarinforms-features"></a>Recursos ausentes do xamarin. Forms

Nem todos os recursos do xamarin. Forms forem concluídos nesta visualização. Para obter mais informações, consulte [suporte de plataforma macOS Status](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) na [xamarin. Forms](https://github.com/xamarin/Xamarin.Forms) repositório do GitHub.

## <a name="related-links"></a>Links relacionados

- [Xamarin.Mac](~/mac/index.yml)
