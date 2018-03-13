---
title: Splash Screen
description: "Um aplicativo do Android leva algum tempo para iniciar, especialmente quando o aplicativo é iniciado pela primeira vez em um dispositivo. Uma tela inicial pode exibir o início até o progresso para o usuário ou para indicar a identidade visual."
ms.topic: article
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 9f88899d390f7f268f1b2f435617dc952f9eb205
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="splash-screen"></a>Splash Screen

_Um aplicativo do Android leva algum tempo para iniciar, especialmente quando o aplicativo é iniciado pela primeira vez em um dispositivo. Uma tela inicial pode exibir o início até o progresso para o usuário ou para indicar a identidade visual._


## <a name="overview"></a>Visão geral

Um aplicativo do Android leva algum tempo para iniciar, especialmente durante a primeira vez que o aplicativo é executado em um dispositivo (às vezes, isso é conhecido como um _inicialização a frio_). Pode exibir a tela inicial do progresso para o usuário de inicialização, ou ele pode exibir informações de identidade visual para identificar e promover o aplicativo.

Este guia descreve uma técnica para implementar uma tela inicial em um aplicativo do Android. Ele aborda as etapas a seguir:

1.  Criando um recurso drawable para a tela inicial.

2.  Definindo um novo tema que exibirá o recurso drawable.

3.  Adicionando uma nova atividade para o aplicativo que será usado como a tela inicial definida pelo tema criado na etapa anterior.

[![Tela de inicial do exemplo Xamarin logotipo seguida de tela do aplicativo](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>Requisitos

Este guia pressupõe que o aplicativo tem como alvo o nível de API do Android 15 (Android 4.0.3) ou superior. O aplicativo também deve ter o **Xamarin.Android.Support.v4** e **Xamarin.Android.Support.v7.AppCompat** pacotes do NuGet adicionados ao projeto.

Todos os códigos e XML neste guia podem ser encontrados no [SplashScreen](https://developer.xamarin.com/samples/monodroid/SplashScreen) projeto de exemplo para este guia.


## <a name="implementing-a-splash-screen"></a>Implementando uma tela inicial

A maneira mais rápida para processar e exibir a tela inicial é criar um tema personalizado e aplicá-la a uma atividade que exibe a tela inicial. Quando a atividade é processada, ele carrega o tema e aplica-se o recurso drawable (referenciado pelo tema) para o plano de fundo da atividade. Essa abordagem evita a necessidade de criar um arquivo de layout.

A tela inicial é implementada como uma atividade que exibe a marca drawable, executa qualquer inicializações e quaisquer tarefas é iniciada. Depois que o aplicativo foi inicializado, a tela inicial atividade inicia a atividade principal e remove próprio da pilha voltar do aplicativo.


### <a name="creating-a-drawable-for-the-splash-screen"></a>Criando um Drawable para a tela inicial

A tela exibirá um XML drawable no plano de fundo da tela inicial da atividade. É necessário usar uma imagem de bitmap (como JPG ou PNG) para a imagem para exibir.

Neste guia, usamos um [lista camada](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) para centralizar a imagem da tela inicial do aplicativo. O trecho a seguir está um exemplo de um `drawable` de recursos usando um `layer-list`:

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

Isso `layer-list` será centralizar a imagem da tela inicial **splash.png** em um plano de fundo especificado pelo `@color/splash_background` recursos.
Colocar esse arquivo no **recursos/drawable** pasta (por exemplo, **Resources/drawable/splash_screen.xml**).

Depois que a tela inicial do drawable tiver sido criada, a próxima etapa é criar um tema para a tela inicial.


### <a name="implementing-a-theme"></a>Implementando um tema

Para criar um tema personalizado para a atividade de tela inicial, editar (ou adicionar) o arquivo **values/styles.xml** e criar um novo `style` elemento para a tela inicial. Um exemplo de **values/style.xml** arquivo é mostrado abaixo com um `style` chamado **MyTheme.Splash**:

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

**MyTheme.Splash** é muito espartana &ndash; declara o plano de fundo da janela, explicitamente remove a barra de título da janela e declara que se trata de tela inteira. Se você quiser criar uma tela inicial que emula a interface do usuário do seu aplicativo antes que a atividade inflação primeiro layout, você pode usar `windowContentOverlay` em vez de `windowBackground` em sua definição de estilo. Nesse caso, você também deve modificar o **splash_screen.xml** drawable para que ele exiba uma emulação de sua interface do usuário.


### <a name="create-a-splash-activity"></a>Criar uma atividade inicial

Agora precisamos de uma nova atividade para o Android iniciar nossa imagem inicial e executa as tarefas de inicialização. O código a seguir é um exemplo de uma implementação de tela inicial completa:

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
Não é necessário carregar um layout em `OnCreate` como o tema declara drawable como plano de fundo.

É importante definir o `NoHistory=true` para que a atividade é removida da pilha voltar do atributo. Para impedir que o botão Voltar cancelar o processo de inicialização, você também pode substituir `OnBackPressed` e não fazer nada:

```csharp
public override void OnBackPressed() { }
```

O trabalho de inicialização é executado de forma assíncrona em `OnResume`. Isso é necessário para que o trabalho de inicialização não lento ou atrasar a aparência da tela inicial. Quando o trabalho foi concluído, `SplashActivity` iniciará `MainActivity` e o usuário pode começar a interagir com o aplicativo.

Essa nova `SplashActivity` é definido como a atividade de iniciador para o aplicativo, definindo o `MainLauncher` atributo `true`. Porque `SplashActivity` é agora a atividade de iniciador, você deve editar `MainActivity.cs`e remover o `MainLauncher` de atributo de `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```


## <a name="summary"></a>Resumo

Este guia discutido uma maneira de implementar uma tela inicial em um aplicativo xamarin; ou seja, aplicando um tema personalizado para a atividade de inicialização.


## <a name="related-links"></a>Links relacionados

- [Tela inicial (exemplo)](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [lista de camadas Drawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ Padrões de Design material - telas de inicialização](https://www.google.com/design/spec/patterns/launch-screens.html)
