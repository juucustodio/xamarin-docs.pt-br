---
title: 'Passo a passo: salvando o estado da atividade'
description: Abordamos a teoria por trás de salvar o estado no guia do ciclo de vida de atividade; Agora, vamos examinar um exemplo.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c8f92e55648dff469227cc3bad981ad5f6e6d0ac
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122121"
---
# <a name="walkthrough---saving-the-activity-state"></a>Passo a passo: salvando o estado da atividade

_Abordamos a teoria por trás de salvar o estado no guia do ciclo de vida de atividade; Agora, vamos examinar um exemplo._

## <a name="activity-state-walkthrough"></a>Instruções passo a passo do estado da atividade

Vamos abrir o **ActivityLifecycle_Start** projeto (na [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) exemplo), compilá-lo e executá-lo. Esse é um projeto muito simple que tem duas atividades para demonstrar o ciclo de vida de atividade e como os vários métodos de ciclo de vida são chamados. Quando você inicia o aplicativo, a tela de `MainActivity` é exibida: 

[![Tela de atividade](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Transições de estado de exibição

Cada método neste exemplo grava na janela de saída do aplicativo de IDE para indicar o estado de atividade. (Para abrir a janela de saída no Visual Studio, digite **CTRL-ALT-DEL**; para abrir a janela de saída no Visual Studio para Mac, clique em **Exibir > Pads > saída do aplicativo**.)

Quando o aplicativo é iniciado pela primeira vez, a janela Saída exibe as alterações de estado de *atividade A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Quando clicamos na **Iniciar atividade B** botão, podemos ver *atividade* pausar e parar durante *atividade B* percorre as alterações de estado: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Como resultado, *atividade B* é iniciado e exibido no lugar de *atividade A*: 

[![Tela atividade B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Quando clicamos na **volta** botão *atividade B* é destruído e *atividade A* é retomada: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Adicionar um contador de clique

Em seguida, vamos alterar o aplicativo para que tenhamos um botão que conta e exibe o número de vezes que ele é clicado. Primeiro, vamos adicionar um `_counter` variável de instância para `MainActivity`:

```csharp
int _counter = 0;
```

Em seguida, vamos editar o **Resource/layout/Main.axml** layout de arquivo e adicione um novo `clickButton` que exibe o número de vezes que o usuário clicou no botão. Resultante **Main. axml** deve ser semelhante à seguinte: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

Vamos adicionar o seguinte código ao final da [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método na `MainActivity` &ndash; esse código manipula eventos de clique a `clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

Quando criamos e execute o aplicativo novamente, um novo botão aparecerá que incrementa e exibe o valor de `_counter` em cada clique:

[![Adicionar contagem de toque](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Mas quando estamos gira o dispositivo para modo paisagem, essa contagem é perdida:

[![Girar para paisagem define a contagem como zero](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Examinar a saída do aplicativo, podemos ver que *atividade A* foi pausada, interrompido, destruídos, recriado, reiniciado e retomada durante a rotação de retrato para modo paisagem: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Porque *atividade A* é destruída e recriada novamente quando o dispositivo for girado, o estado da instância será perdido. Em seguida, adicionaremos o código para salvar e restaurar o estado da instância.

### <a name="adding-code-to-preserve-instance-state"></a>Adicionar código para preservar estado de instância

Vamos adicionar um método para `MainActivity` para salvar o estado da instância. Antes de *atividade de um* é destruído, o Android chama automaticamente [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) e passa em um [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que podemos usar para armazenar o estado da instância. Vamos usá-lo para salvar nosso contagem de cliques como um valor inteiro:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Quando *atividade A* é recriado e retomada, Android passa isso `Bundle` volta no nosso `OnCreate` método. Vamos adicionar código para `OnCreate` para restaurar o `_counter` o valor do passado `Bundle`. Adicione o seguinte código antes da linha onde `clickbutton` é definido: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Compilar e executar o aplicativo novamente, clique o segundo botão algumas vezes. Quando estamos gira o dispositivo para modo paisagem, a contagem é preservada!

[![Rotação de tela mostra a contagem de quatro preservadas](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


Vamos dar uma olhada na janela de saída para ver o que aconteceu:
    
```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
``` 

Antes do [OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) método foi chamado, nosso novo `OnSaveInstanceState` método foi chamado para salvar as `_counter` valor em um `Bundle`. Android passado isso `Bundle` diga quando chamado nossa `OnCreate` método e podemos conseguir usou para restaurar o `_counter` valor de onde paramos.


## <a name="summary"></a>Resumo

Essa explicação passo a passo, usamos nosso conhecimento do ciclo de vida de atividade para preservar os dados de estado. 



## <a name="related-links"></a>Links relacionados

- [ActivityLifecycle (amostra)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Atividade do Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
