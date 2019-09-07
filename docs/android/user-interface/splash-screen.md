---
title: Splash Screen
description: Um aplicativo Android leva algum tempo para ser iniciado, especialmente quando o aplicativo é iniciado pela primeira vez em um dispositivo. Uma tela inicial pode exibir o progresso de inicialização para o usuário ou para indicar a identidade visual.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: b05ab7ee835a97f13af618332baec7a5ebf404ec
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764095"
---
# <a name="splash-screen"></a>Splash Screen

_Um aplicativo Android leva algum tempo para ser iniciado, especialmente quando o aplicativo é iniciado pela primeira vez em um dispositivo. Uma tela inicial pode exibir o progresso de inicialização para o usuário ou para indicar a identidade visual._

## <a name="overview"></a>Visão geral

Um aplicativo Android leva algum tempo para ser inicializado, especialmente durante a primeira vez em que o aplicativo é executado em um dispositivo (às vezes, isso é chamado de _inicialização a frio_). A tela inicial pode exibir o progresso de inicialização para o usuário ou pode exibir informações de identidade visual para identificar e promover o aplicativo.

Este guia aborda uma técnica para implementar uma tela inicial em um aplicativo Android. Ele aborda as seguintes etapas:

1. Criar um recurso que poderia ser desenhado para a tela inicial.

2. Definir um novo tema que exibirá o recurso que poderá ser desenhado.

3. Adicionar uma nova atividade ao aplicativo que será usada como a tela inicial definida pelo tema criado na etapa anterior.

[![Tela inicial do logotipo do Xamarin de exemplo seguida pela tela do aplicativo](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)

## <a name="requirements"></a>Requisitos

Este guia pressupõe que o aplicativo tem como alvo o nível 15 (Android 4.0.3) ou superior da API do Android. O aplicativo também deve ter os pacotes **xamarin. Android. support. v4** e **xamarin. Android. support. v7. AppCompat** NuGet adicionados ao projeto.

Todo o código e o XML neste guia podem ser encontrados no projeto de exemplo de [tela](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen) inicial deste guia.

## <a name="implementing-a-splash-screen"></a>Implementando uma tela inicial

A maneira mais rápida de renderizar e exibir a tela inicial é criar um tema personalizado e aplicá-lo a uma atividade que exibe a tela inicial. Quando a atividade é renderizada, ela carrega o tema e aplica o recurso desenhável (referenciado pelo tema) ao plano de fundo da atividade. Essa abordagem evita a necessidade de criar um arquivo de layout.

A tela inicial é implementada como uma atividade que exibe o empate com marca, executa todas as inicializações e inicia todas as tarefas. Depois que o aplicativo for inicializado, a atividade da tela inicial iniciará a atividade principal e se removerá da pilha voltar do aplicativo.

### <a name="creating-a-drawable-for-the-splash-screen"></a>Criando um empate para a tela inicial

A tela inicial exibirá um XML que poderá ser desenhado no plano de fundo da atividade da tela inicial. É necessário usar uma imagem de bitmap (como um PNG ou JPG) para a imagem a ser exibida.

Neste guia, usamos uma lista de [camadas](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) para centralizar a imagem da tela inicial no aplicativo. O trecho a seguir é um exemplo de `drawable` um recurso usando `layer-list`um:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

Isso `layer-list` centralizará a imagem de abertura **Splash. png** em `@color/splash_background` segundo plano especificado pelo recurso. Coloque esse arquivo XML na pasta **recursos/empates** (por exemplo, **resourceable/splash_screen. xml**).

Depois que a tela inicial for criada, a próxima etapa será criar um tema para a tela inicial.

### <a name="implementing-a-theme"></a>Implementando um tema

Para criar um tema personalizado para a atividade da tela inicial, edite (ou adicione) o arquivo **Values/Styles. xml** e crie um novo `style` elemento para a tela inicial. Um arquivo **Values/Style. xml** de exemplo é mostrado abaixo `style` com um chamado **mythemes. Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
  </style>
</resources>
```

**MyTheme. Splash** é muito companhias Spartan &ndash; ele declara o plano de fundo da janela, remove explicitamente a barra de título da janela e declara que ela é de tela inteira. Se você quiser criar uma tela inicial que emula a interface do usuário do seu aplicativo antes que a atividade inplana o primeiro layout, você pode usar `windowContentOverlay` em vez `windowBackground` de em sua definição de estilo. Nesse caso, você também deve modificar o **splash_screen. xml** Drawable para que ele exiba uma emulação de sua interface do usuário.

### <a name="create-a-splash-activity"></a>Criar uma atividade de splash

Agora precisamos de uma nova atividade para que o Android seja iniciado, que tem nossa imagem de splash e executa qualquer tarefa de inicialização. O código a seguir é um exemplo de uma implementação de tela inicial completa:

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity`o usa explicitamente o tema que foi criado na seção anterior, substituindo o tema padrão do aplicativo.
Não é necessário carregar um layout `OnCreate` como o tema declara um empate como plano de fundo.

É importante definir o `NoHistory=true` atributo para que a atividade seja removida da pilha voltar. Para impedir que o botão voltar cancele o processo de inicialização, você também pode substituí `OnBackPressed` -lo e fazer com que ele não faça nada:

```csharp
public override void OnBackPressed() { }
```

O trabalho de inicialização é executado de `OnResume`forma assíncrona no. Isso é necessário para que o trabalho de inicialização não fique lento ou atrase a aparência da tela de inicialização. Quando o trabalho for concluído, `SplashActivity` o será `MainActivity` iniciado e o usuário poderá começar a interagir com o aplicativo.

Esse novo `SplashActivity` é definido como a atividade do iniciador para o aplicativo, `MainLauncher` definindo o `true`atributo como. Como `SplashActivity` agora é a atividade do iniciador, você `MainActivity.cs`deve editar e remover `MainLauncher` o atributo `MainActivity`de:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Modo paisagem

A tela inicial implementada nas etapas anteriores será exibida corretamente no modo retrato e paisagem. No entanto, em alguns casos, é necessário ter telas de abertura separadas para os modos retrato e paisagem (por exemplo, se a imagem de abertura estiver em tela inteira).

Para adicionar uma tela inicial para o modo paisagem, use as seguintes etapas:

1. Na pasta **recursos/empates** , adicione a versão de paisagem da imagem de tela inicial que você deseja usar. Neste exemplo, **splash_logo_land. png** é a versão de paisagem do logotipo que foi usada nos exemplos acima (ela usa letra branca em vez de azul).

2. Na pasta **recursos/empates** , crie uma versão de paisagem do `layer-list` draw que foi definido anteriormente (por exemplo, **splash_screen_land. xml**). Nesse arquivo, defina o caminho do bitmap para a versão de paisagem da imagem da tela inicial. No exemplo a seguir, **splash_screen_land. xml** usa **splash_logo_land. png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3. Crie a pasta **Resources/Values-Land** se ela ainda não existir.

4. Adicione os arquivos **Colors. xml** e **Style. xml** a **Values-Land** (eles podem ser copiados e modificados dos arquivos **valores/Colors. xml** e **Values/Style. xml** existentes).

5. Modifique **Values-Land/Style. xml** para que ele use a versão de paisagem do drawed for `windowBackground`. Neste exemplo, **splash_screen_land. xml** é usado:

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6. Modifique **Values-Land/Colors. xml** para configurar as cores que você deseja usar para a versão de paisagem da tela inicial. Neste exemplo, a cor da tela de fundo inicial é alterada para azul para o modo paisagem:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7. Compile e execute o aplicativo novamente. Gire o dispositivo para o modo paisagem enquanto a tela inicial ainda é exibida. A tela inicial muda para a versão de paisagem:

    [![Rotação da tela inicial para o modo paisagem](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)

Observe que o uso de uma tela inicial em modo paisagem nem sempre fornece uma experiência contínua. Por padrão, o Android inicia o aplicativo no modo retrato e faz a transição para o modo paisagem mesmo que o dispositivo já esteja no modo paisagem. Como resultado, se o aplicativo for iniciado enquanto o dispositivo estiver no modo paisagem, o dispositivo apresentará brevemente a tela de abertura retrato e, em seguida, anima a rotação de retrato para a tela inicial paisagem. Infelizmente, essa transição inicial de retrato para paisagem ocorre mesmo quando `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` é especificado nos sinalizadores da atividade de abertura. A melhor maneira de contornar essa limitação é criar uma única imagem de tela inicial que seja renderizada corretamente nos modos retrato e paisagem.

## <a name="summary"></a>Resumo

Este guia abordou uma maneira de implementar uma tela inicial em um aplicativo Xamarin. Android; a saber, aplicando um tema personalizado à atividade de inicialização.

## <a name="related-links"></a>Links relacionados

- [Tela inicial (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)
- [lista de camadas desenhável](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [Padrões de design de material – telas de inicialização](https://material.io/design/communication/launch-screen.html#usage)
