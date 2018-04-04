---
title: Instruções passo a passo - salvar o estado de atividade
description: Nós abordamos a teoria por trás de salvar o estado no guia de ciclo de vida da atividade; Agora, vamos examinar um exemplo.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: e282eeb8732bd5294da4ec4e3fe337e81107c8f3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---saving-the-activity-state"></a>Instruções passo a passo - salvar o estado de atividade

_Nós abordamos a teoria por trás de salvar o estado no guia de ciclo de vida da atividade; Agora, vamos examinar um exemplo._

## <a name="activity-state-walkthrough"></a>Instruções passo a passo do estado da atividade

Vamos abrir o **ActivityLifecycle_Start** projeto (no [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) exemplo), compilá-lo e executá-lo. Este é um projeto muito simple que tem duas atividades para demonstrar o ciclo de vida da atividade e como os vários métodos de ciclo de vida são chamados. Quando você inicia o aplicativo, a tela de `MainActivity` é exibida: 

[![Tela de atividade A](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Transições de estado de exibição

Cada método neste exemplo grava na janela de saída do aplicativo de IDE para indicar o estado de atividade. (Para abrir a janela de saída no Visual Studio, digite **CTRL-ALT-DEL**; para abrir a janela de saída no Visual Studio para Mac, clique em **exibição > preenche > saída do aplicativo**.)

Quando o aplicativo é iniciado pela primeira vez, a janela Saída exibe as alterações de estado de *atividade A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Quando clicarmos o **Iniciar atividade B** botão, podemos ver *atividade A* pausar e parar ao *atividade B* passa pelas suas alterações de estado: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Como resultado, *atividade B* é iniciado e exibido no lugar de *atividade A*: 

[![Tela de atividade B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Quando clicarmos o **novamente** botão, *atividade B* é destruído e *atividade A* é retomado: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Adicionar um contador de clique

Em seguida, vamos alterar o aplicativo para que tenhamos um botão que conta e exibe o número de vezes que ele for clicado. Primeiro, vamos adicionar uma `_counter` variável de instância para `MainActivity`:

```csharp
int _counter = 0;
```

Em seguida, vamos editar o **Resource/layout/Main.axml** layout de arquivo e adicionar um novo `clickButton` que exibe o número de vezes que o usuário clicou no botão. Resultante **Main.axml** deve ser semelhante à seguinte: 

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

Vamos adicionar o seguinte código ao final do [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método `MainActivity` &ndash; esse código identificadores de eventos de clique no `clickButton`:

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

Quando criamos e executar o aplicativo novamente, um novo botão que incrementa e exibe o valor de `_counter` cada, clique em:

[![Adicionar a contagem de toque](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Mas quando podemos gira o dispositivo para o modo de paisagem, essa contagem é perdida:

[![Girando para paisagem define a contagem de volta para zero](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Examinar a saída do aplicativo, podemos ver que *atividade A* foi pausado, interrompido, destruído, recriado, reiniciado e retomada durante a rotação de retrato para modo paisagem: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Porque *atividade A* é destruído e recriado novamente quando o dispositivo for girado, seu estado de instância é perdido. Em seguida, adicionaremos o código para salvar e restaurar o estado da instância.

### <a name="adding-code-to-preserve-instance-state"></a>Adicionando código para preservar estado de instância

Vamos adicionar um método para `MainActivity` para salvar o estado da instância. Antes de *atividade A* é destruído, o Android chama automaticamente [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) e passa um [pacote](https://developer.xamarin.com/api/type/Android.OS.Bundle/) que podemos usar para armazenar o estado da instância. Vamos usá-lo para salvar nosso contagem de clique como um valor inteiro:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Quando *atividade A* é recriada e retomada, Android passa esse `Bundle` no nosso `OnCreate` método. Vamos adicionar código para `OnCreate` para restaurar o `_counter` valor passado na `Bundle`. Adicione o seguinte código antes da linha onde `clickbutton` está definido: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Compilar e executar o aplicativo novamente, e clique no botão segundo algumas vezes. Quando é gira o dispositivo para o modo de paisagem, a contagem é preservada!

[![Rotação de tela mostra a contagem de quatro preservados](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


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

Antes do [OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) método foi chamado, nosso novo `OnSaveInstanceState` método foi chamado para salvar o `_counter` valor em um `Bundle`. Android passado isso `Bundle` para nós quando chamado nosso `OnCreate` método e vamos conseguiu usado para restaurar o `_counter` valor onde paramos.


## <a name="summary"></a>Resumo

Nesta explicação passo a passo, usamos nosso conhecimento do ciclo de vida de atividade para preservar os dados de estado. 



## <a name="related-links"></a>Links relacionados

- [ActivityLifecycle (exemplo)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Atividade do Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
