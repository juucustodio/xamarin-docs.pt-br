---
ms.openlocfilehash: d0b00cf71a6121026dfa368592da8ce8d3be0373
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634980"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para concluir este tutorial, você precisará ter o Visual Studio 2019 (versão mais recente) com a carga de trabalho **Desenvolvimento mobile com .NET** instalada. Além disso, você precisará de um Mac emparelhado para compilar o aplicativo do tutorial no iOS. Para obter informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md). Para obter informações sobre como conectar o Visual Studio 2019 a um host de build Mac, confira [Emparelhar com o Mac para desenvolvimento no Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie o Visual Studio e crie um novo aplicativo Xamarin.Forms em branco chamado **AppLifecycleTutorial**.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **AppLifecycleTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Gerenciador de Soluções**, no projeto **AppLifecycleTutorial**, expanda **App.xaml** e clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, em **App.xaml.cs**, atualize as substituições `OnStart`, `OnSleep` e `OnResume` da seguinte maneira:

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Esse código atualiza as substituições de método do ciclo de vida do aplicativo, com instruções `Console.WriteLine` que indicam quando cada método foi invocado:

    - O método `OnStart` é invocado quando o aplicativo é iniciado.
    - O método `OnSleep` é invocado quando o aplicativo vai para o segundo plano.
    - O método `OnResume` é invocado quando o aplicativo é retomado em segundo plano.

    > [!NOTE]
    > Não há nenhum método de encerramento do aplicativo. Em circunstâncias normais, o encerramento do aplicativo ocorrerá do método `OnSleep`.

1. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo dentro de seu simulador remoto de iOS ou do Android Emulator escolhido. Quando o aplicativo é iniciado, o método `OnStart` é invocado e **OnStart** é gerado na janela de **Saída** do Visual Studio:

    ```
    [Mono] Found as 'java_interop_jnienv_get_object_array_element'.
    OnStart
    [OpenGLRenderer] HWUI GL Pipeline
    ```

    Quando o aplicativo é colocado em segundo plano (tocando no botão Página Inicial no iOS ou Android), o método `OnSleep` é invocado:

    ```
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    OnSleep
    [Mono] Image addref System.Runtime.Serialization[0x83ee19c0] -> System.Runtime.Serialization.dll[0x83f57b00]: 2
    ```

    Em seguida, quando o aplicativo é retomado em segundo plano (toque no ícone do aplicativo no iOS, toque no botão de Visão geral no Android e selecione o aplicativo AppLifecycleTutorial), o método `OnResume` é invocado:

    ```
    Thread finished: <Thread Pool> #5
    OnResume
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    ```

    > [!NOTE]
    > Esses blocos de código mostram a saída de exemplo ao executar o aplicativo no Android.

    Interrompa o aplicativo no Visual Studio.

    Para obter mais informações sobre o ciclo de vida do aplicativo do Xamarin.Forms, confira [Ciclo de vida do aplicativo do Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para concluir este tutorial, você precisará ter o Visual Studio para Mac (versão mais recente), com suporte para plataforma iOS e Android, instalado. Além disso, você também precisará do Xcode (versão mais recente). Para obter mais informações sobre como instalar a plataforma Xamarin, confira [Instalando o Xamarin](~/get-started/installation/index.md).

1. Inicie o Visual Studio para Mac e crie um novo aplicativo Xamarin.Forms em branco chamado **AppLifecycleTutorial**.

    > [!IMPORTANT]
    > Os snippets de C# e XAML neste tutorial exigem que a solução seja denominada **AppLifecycleTutorial**. Usar um nome diferente causará erros de build ao copiar o código deste tutorial para a solução.

    Para obter mais informações sobre a biblioteca .NET Standard criada, confira [Anatomia de um aplicativo Xamarin.Forms](~/get-started/first-app/index.md) em [Aprofundamento do Início Rápido do Xamarin.Forms](~/get-started/first-app/index.md).

1. No **Painel de Soluções**, no projeto **AppLifecycleTutorial**, expanda **App.xaml** e clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, em **App.xaml.cs**, atualize as substituições `OnStart`, `OnSleep` e `OnResume` da seguinte maneira:

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Esse código atualiza as substituições de método do ciclo de vida do aplicativo, com instruções `Console.WriteLine` que indicam quando cada método foi invocado:

    - O método `OnStart` é invocado quando o aplicativo é iniciado.
    - O método `OnSleep` é invocado quando o aplicativo vai para o segundo plano.
    - O método `OnResume` é invocado quando o aplicativo é retomado em segundo plano.

    > [!NOTE]
    > Não há nenhum método de encerramento do aplicativo. Em circunstâncias normais, o encerramento do aplicativo ocorrerá do método `OnSleep`.

1. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com um botão Reproduzir) para iniciar o aplicativo dentro do simulador de iOS ou do Android Emulator escolhido. Quando o aplicativo é iniciado, o método `OnStart` é invocado e **OnStart** é gerado na janela de **Saída do Aplicativo** do Visual Studio para Mac:

    ```
    2019-02-11 12:05:23.164761+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:05:23.165297+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    2019-02-11 12:05:23.685430+0000 AppLifecycleTutorial.iOS[4089:361037] OnStart
    ```

    Quando o aplicativo é colocado em segundo plano (tocando no botão Página Inicial no iOS ou Android), o método `OnSleep` é invocado:

    ```
    2019-02-11 12:06:12.394301+0000 AppLifecycleTutorial.iOS[4089:361037] OnSleep
    Thread started: <Thread Pool> #3
    Thread started: <Thread Pool> #4
    Thread started: <Thread Pool> #5
    Thread started: <Thread Pool> #6
    2019-02-11 12:06:13.056968+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:06:13.057264+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    ```

    Em seguida, quando o aplicativo é retomado em segundo plano (toque no ícone do aplicativo no iOS, toque no botão de Visão geral no Android e selecione o aplicativo AppLifecycleTutorial), o método `OnResume` é invocado:

    ```
    2019-02-11 12:07:10.321905+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4130
    2019-02-11 12:07:10.322557+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskCopyDebugDescription: AppLifecycleTuto[4130]/0#-1 LF=0
    2019-02-11 12:07:17.110938+0000 AppLifecycleTutorial.iOS[4130:365695] OnResume
    ```

    > [!NOTE]
    > Esses blocos de código mostram a saída de exemplo ao executar o aplicativo no iOS.

    Interrompa o aplicativo no Visual Studio para Mac.

    Para obter mais informações sobre o ciclo de vida do aplicativo do Xamarin.Forms, confira [Ciclo de vida do aplicativo do Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).
