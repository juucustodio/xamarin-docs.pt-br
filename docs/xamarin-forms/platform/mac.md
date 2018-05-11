---
title: Instalação da plataforma de Mac
description: Xamarin. Forms agora tem suporte para a plataforma Mac visualização
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
ms.openlocfilehash: 0985fb533969a331ceee62f490bae600de9f189c
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="mac-platform-setup"></a>Instalação da plataforma de Mac

![Visualizar](~/media/shared/preview.png)

Antes de começar, crie (ou usar uma existente) projeto xamarin. Forms.
Você só pode adicionar aplicativos Mac usando o Visual Studio para Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**A adição de um projeto macOS para xamarin. Forms, pelo [University Xamarin](https://university.xamarin.com/)**

## <a name="adding-a-mac-app"></a>Adicionar um aplicativo do Mac

Siga estas instruções para adicionar um aplicativo do Mac que serão executados no macOS Serra e Mac OS X El Capitan:

1. No Visual Studio para Mac, clique com botão direito na solução xamarin. Forms existente e escolha **Adicionar > Adicionar novo projeto...**

2. No **novo projeto** janela escolher **Mac > aplicativo > aplicativo Cocoa** e pressione **próximo**.

3. Tipo de um **nome do aplicativo** (e, opcionalmente, escolha um nome diferente para o Item de encaixe), em seguida, pressione **próximo**.

4. Examine a configuração e pressione **criar**. Essas etapas são mostradas na abaixo:

  ![Instruções animadas mostrando como adicionar um aplicativo Cocoa](mac-images/add-macos-proj.gif)

5. No projeto de Mac, clique duas vezes em **pacotes > Adicionar pacotes de...**  para adicionar o [Xamarin.Forms/2.3.5.235-pre2](https://www.nuget.org/packages/Xamarin.Forms/2.3.5.235-pre2) NuGet. Você também deve atualizar os outros projetos nesta versão.

6. No projeto de Mac, clique duas vezes em **referências** e adicione uma referência ao projeto xamarin. Forms (projeto de biblioteca de projeto compartilhado ou padrão do .NET).

  ![Adicione uma referência ao projeto de código compartilhado xamarin. Forms](mac-images/references-sml.png)

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

9. Clique duas vezes em **Main.storyboard** Editar no Xcode. Selecione o **janela** e _desmarque_ o **controlador inicial é** caixa de seleção (Isso ocorre porque o código acima cria uma janela):

  [![Desmarque a caixa de seleção de controlador inicial estiver no Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

  Você pode editar o sistema de menu no storyboard para remover os itens indesejados.

10. Finalmente, adicione qualquer recurso local (por exemplo. arquivos de imagem) dos projetos existentes de plataforma que são necessários.

11. O projeto de Mac agora deve executar seu código xamarin. Forms no macOS!

## <a name="next-steps"></a>Próximas etapas

### <a name="styling"></a>Estilo

Com as alterações recentes feitas `OnPlatform` agora você pode direcionar qualquer número de plataformas. Isso inclui macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Observe que você também pode dobrar em plataformas assim: `<On Platform="iOS, macOS" ...>`.

### <a name="window-size-and-position"></a>Posição e tamanho da janela

Você pode ajustar o tamanho inicial e o local da janela de `AppDelegate`:

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemas Conhecidos

Esta é uma visualização, portanto você deve esperar que nem tudo é preparado para produção. Abaixo estão algumas coisas que você pode encontrar ao adicionar macOS seus projetos:

### <a name="not-all-nugets-are-ready-for-macos"></a>Nem todos os NuGets estão prontos para macOS

Pacotes devem direcionar "xamarinmac20" para funcionar em um projeto macOS. Você pode achar que algumas das bibliotecas de que você usar ainda não suportam macOS.

Nesse caso, você precisará enviar uma solicitação para o mantenedor do projeto para adicioná-lo. Até que tenham suporte, talvez seja necessário procurar alternativas.

### <a name="missing-xamarinforms-features"></a>Falta de recursos do xamarin. Forms

Nem todos os recursos do xamarin. Forms forem concluídos nesta visualização; Aqui está uma lista de algumas das funcionalidades que ainda não foi implementada:

* Rodapé
* Imagem – aspecto
* Suporte de ListView – ScrollTo, UnevenRows, atualizar, SeparatorColor, SeparatorVisibility
* MasterDetailPage – BackgroundColor
* Navegação – InsertPageBefore
* OpenGLRenderer
* Seletor – implementação Bindable/observável
* TabbedPage – BarBackgroundColor, BarTextColor
* Modo de tabela – UnevenRows
* ViewCell – IsEnabled, ForceUpdateSize
* WebView – a maioria dos WebNavigationEvents


## <a name="related-links"></a>Links relacionados

- [Xamarin.Mac](~/mac/index.yml)
