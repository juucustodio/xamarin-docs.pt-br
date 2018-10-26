---
title: Splash Screen
description: Um aplicativo Android leva algum tempo para serem inicializadas, especialmente quando o aplicativo é iniciado pela primeira vez em um dispositivo. Uma tela inicial poderá exibir o início até o progresso ao usuário ou para indicar a identidade visual.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: f21eca052ef13fd0e3d6efa261e1ff70b3b14372
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120704"
---
# <a name="splash-screen"></a>Splash Screen

_Um aplicativo Android leva algum tempo para serem inicializadas, especialmente quando o aplicativo é iniciado pela primeira vez em um dispositivo. Uma tela inicial poderá exibir o início até o progresso ao usuário ou para indicar a identidade visual._


## <a name="overview"></a>Visão geral

Um aplicativo Android leva algum tempo para ser inicializado, especialmente durante a primeira vez que o aplicativo é executado em um dispositivo (às vezes, isso é conhecido como um _inicialização a frio_). A tela inicial poderá exibir andamento para o usuário de inicialização, ou ele pode exibir informações de identidade visual para identificar e promova o aplicativo.

Este guia aborda uma técnica para implementar uma tela inicial em um aplicativo do Android. Ele aborda as etapas a seguir:

1.  Criando um recurso desenhável para a tela inicial.

2.  Definindo um novo tema que exibirá o recurso desenhável.

3.  Adicionando uma nova atividade para o aplicativo que será usado como a tela inicial definida pelo tema criado na etapa anterior.

[![Exemplo Xamarin tela de abertura seguida de tela do aplicativo](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>Requisitos

Este guia pressupõe que o aplicativo se destina a nível de API do Android (Android 4.0.3) de 15 ou superior. O aplicativo também deve ter o **Xamarin.Android.Support.v4** e **Xamarin.Android.Support.v7.AppCompat** pacotes NuGet adicionados ao projeto.

Todos os códigos e o XML neste guia podem ser encontrados na [SplashScreen](https://developer.xamarin.com/samples/monodroid/SplashScreen) projeto de exemplo para este guia.


## <a name="implementing-a-splash-screen"></a>Implementando uma tela inicial

A maneira mais rápida para processar e exibir a tela inicial é criar um tema personalizado e aplicá-la a uma atividade que exibe a tela inicial. Quando a atividade é processada, ele carrega o tema e aplica-se o recurso drawable (referenciado pelo tema) para o plano de fundo da atividade. Essa abordagem evita a necessidade de criar um arquivo de layout.

A tela inicial é implementada como uma atividade que exibe a marca desenháveis, executa qualquer inicializações e quaisquer tarefas é iniciada. Depois que o aplicativo tem a ser inicializado, a tela inicial atividade começa a atividade principal e remove a próprio da pilha voltar de aplicativo.


### <a name="creating-a-drawable-for-the-splash-screen"></a>Criando um Desenháveis para a tela inicial

A tela inicial exibirá um XML desenhável no plano de fundo da tela inicial da atividade. É necessário usar uma imagem de bitmap (como um PNG ou JPG) para a imagem para exibir.

Neste guia, usamos uma [camada lista](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) para centralizar a imagem da tela inicial do aplicativo. O trecho a seguir está um exemplo de uma `drawable` de recursos usando um `layer-list`:

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

Isso `layer-list` serão centralizar a imagem de abertura **splash.png** no plano de fundo especificado pelo `@color/splash_background` recursos. Coloque esse arquivo XML na **recursos/drawable** pasta (por exemplo, **Resources/drawable/splash_screen.xml**).

Depois que a tela inicial do desenhável tiver sido criada, a próxima etapa é criar um tema para a tela inicial.


### <a name="implementing-a-theme"></a>Implementando um tema

Para criar um tema personalizado para a atividade de tela inicial, editar (ou adicione) o arquivo **values/styles.xml** e crie um novo `style` elemento para a tela inicial. Uma amostra **values/style.xml** arquivo é mostrado abaixo, com um `style` denominada **MyTheme.Splash**:

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

**MyTheme.Splash** é muito spartan &ndash; ele declara o plano de fundo da janela, explicitamente remove a barra de título da janela e declara que se trata de tela inteira. Se você quiser criar uma tela inicial que emula a interface do usuário do seu aplicativo antes que a atividade infla primeiro layout, você pode usar `windowContentOverlay` em vez de `windowBackground` em sua definição de estilo. Nesse caso, você também deve modificar a **splash_screen.xml** desenháveis para que ele exiba uma emulação de sua interface do usuário.


### <a name="create-a-splash-activity"></a>Criar uma atividade inicial

Agora precisamos de uma nova atividade para o Android iniciar nossa imagem inicial e executa as tarefas de inicialização. O código a seguir é um exemplo de uma implementação de tela de abertura completa:

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

`SplashActivity` usa explicitamente o tema que foi criado na seção anterior, substituindo o tema padrão do aplicativo.
Não é necessário para carregar um layout em `OnCreate` conforme o tema declara desenháveis como plano de fundo.

É importante definir o `NoHistory=true` de atributo para que a atividade é removida da pilha voltar. Para impedir que o botão Voltar o cancelamento do processo de inicialização, você também pode substituir `OnBackPressed` que ele faça nada:

```csharp
public override void OnBackPressed() { }
```

O trabalho de inicialização é executado de forma assíncrona em `OnResume`. Isso é necessário para que o trabalho de inicialização não reduzir a velocidade ou atrasar a aparência da tela inicial. Quando o trabalho for concluída, `SplashActivity` iniciará `MainActivity` e o usuário pode começar a interagir com o aplicativo.

Essa nova `SplashActivity` é definido como a atividade de iniciador para o aplicativo, definindo o `MainLauncher` atributo `true`. Porque `SplashActivity` é agora a atividade de inicializador, você deve editar `MainActivity.cs`e remova os `MainLauncher` de atributos de `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Modo de paisagem

A tela inicial implementada nas etapas anteriores serão exibidos corretamente no modo retrato e paisagem. No entanto, em alguns casos é necessário ter as telas de abertura separado para os modos retrato e paisagem (por exemplo, se a imagem inicial é a tela inteira).

Para adicionar uma tela inicial para o modo paisagem, use as seguintes etapas:

1. No **recursos/drawable** pasta, adicione a versão de paisagem da imagem da tela inicial que deseja usar. Neste exemplo, **splash_logo_land.png** é a versão de paisagem do logotipo que foi usada nos exemplos acima (utiliza mortas branca em vez de azul).

2. No **recursos/drawable** pasta, crie uma versão de paisagem do `layer-list` desenháveis que foi definido anteriormente (por exemplo, **splash_screen_land.xml**). Nesse arquivo, defina o caminho de bitmap para a versão de paisagem da imagem da tela inicial. No exemplo a seguir **splash_screen_land.xml** usa **splash_logo_land.png**:

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

3.  Criar o **valores/recursos-land** pasta se ela ainda não exista.

4.  Adicione os arquivos **Colors** e **style.xml** para **valores land** (eles podem ser copiados e modificados de existente **values/colors.xml**e **values/style.xml** arquivos).

5.  Modifique **valores-Terra/style.xml** para que ele use a versão de paisagem do desenháveis para `windowBackground`. Neste exemplo, **splash_screen_land.xml** é usado:

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

6.  Modifique **valores-Terra/Colors** para configurar as cores que você deseja usar para a versão de paisagem da tela inicial. Neste exemplo, a cor do plano de fundo de inicial é alterada para azul para o modo paisagem:

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

7.  Compilar e executar o aplicativo novamente. Gira o dispositivo para modo paisagem, enquanto a tela inicial ainda é exibida. A tela inicial muda para a versão do cenário:

    [![Rotação da tela inicial para o modo de paisagem](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


Observe que o uso de uma tela de abertura do modo de paisagem nem sempre oferece uma experiência perfeita. Por padrão, o Android iniciará o aplicativo no modo retrato e faz a transição para o modo paisagem, mesmo se o dispositivo já está no modo paisagem. Como resultado, se o aplicativo for iniciado enquanto o dispositivo está no modo paisagem, o dispositivo apresenta brevemente a tela inicial de retrato e, em seguida, anima a rotação de retrato para a tela inicial do cenário. Infelizmente, essa transição de retrato para paisagem inicial ocorre mesmo quando `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` é especificado nos sinalizadores da atividade inicial. A melhor maneira de contornar essa limitação é criar uma imagem de tela de abertura único que renderiza corretamente em modos retrato e paisagem.


## <a name="summary"></a>Resumo

Este guia discutiu a uma maneira de implementar uma tela inicial em um aplicativo xamarin. Android; ou seja, aplicando um tema personalizado para a atividade de inicialização.


## <a name="related-links"></a>Links relacionados

- [Tela inicial (amostra)](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [lista de camada Drawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ Padrões de Design de material - telas de inicialização](https://material.io/design/communication/launch-screen.html#usage)
