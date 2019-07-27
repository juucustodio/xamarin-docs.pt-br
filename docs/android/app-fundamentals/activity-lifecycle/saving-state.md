---
title: 'Passo a passo: salvar o estado da atividade'
description: Abordamos a teoria por trás do estado de salvamento no guia do ciclo de vida da atividade; Agora, vamos examinar um exemplo.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 7e2d6c1bb462a8eee5debc139aee9f71ae8ac6a8
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68508780"
---
# <a name="walkthrough---saving-the-activity-state"></a>Passo a passo: salvar o estado da atividade

_Abordamos a teoria por trás do estado de salvamento no guia do ciclo de vida da atividade; Agora, vamos examinar um exemplo._

## <a name="activity-state-walkthrough"></a>Instruções do estado da atividade

Vamos abrir o projeto **ActivityLifecycle_Start** (no exemplo de [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) ), compilá-lo e executá-lo. Esse é um projeto muito simples que tem duas atividades para demonstrar o ciclo de vida da atividade e como os vários métodos de ciclo de vida são chamados. Quando você inicia o aplicativo, a tela de `MainActivity` é exibida:

[![Atividade de uma tela](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Exibindo transições de estado

Cada método neste exemplo grava na janela de saída do aplicativo IDE para indicar o estado da atividade. (Para abrir a janela saída no Visual Studio, digite **Ctrl-Alt-O**; para abrir a janela saída no Visual Studio para Mac, clique em **exibir > pads > saída do aplicativo**.)

Quando o aplicativo é iniciado pela primeira vez, a janela saída exibe as alterações de estado da *atividade A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Quando clicamos no botão **iniciar atividade B** , vemos a *atividade* em pausa e parada enquanto a *atividade B* passa pelas alterações de Estado: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Como resultado, a *atividade B* é iniciada e exibida no lugar da *atividade a*: 

[![Tela atividade B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Quando clicamos no botão **voltar** , a *atividade B* é destruída e *A atividade A* é retomada: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Adicionando um contador de clique

Em seguida, vamos alterar o aplicativo para que tenhamos um botão que conta e exiba o número de vezes que ele é clicado. Primeiro, vamos adicionar uma `_counter` variável de instância a: `MainActivity`

```csharp
int _counter = 0;
```

Em seguida, vamos editar o arquivo de layout **Resource/layout/Main. axml** e adicionar um `clickButton` novo que exibe o número de vezes que o usuário clicou no botão. O **Main. axml** resultante deve ser semelhante ao seguinte: 

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

Vamos adicionar o código a seguir ao final do método [OnCreate](xref:Android.App.Activity.OnCreate*) `MainActivity` neste &ndash; código para manipular eventos de clique do `clickButton`:

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

Quando criamos e executamos o aplicativo novamente, um novo botão é exibido que incrementa e exibe o valor `_counter` de cada clique:

[![Adicionar contagem de toque](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Mas quando giramos o dispositivo para o modo paisagem, essa contagem é perdida:

[![A rotação para paisagem define a contagem de volta para zero](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Examinando a saída do aplicativo, vemos que *a atividade a* foi pausada, interrompida, destruída, recriada, reiniciada e retomada durante a rotação do modo de retrato para paisagem: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Como a *atividade a* é destruída e recriada quando o dispositivo é girado, seu estado de instância é perdido. Em seguida, adicionaremos o código para salvar e restaurar o estado da instância.

### <a name="adding-code-to-preserve-instance-state"></a>Adicionando código para preservar o estado da instância

Vamos adicionar um método para `MainActivity` salvar o estado da instância. Antes que *A atividade A* seja destruída, o Android chama o [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) automaticamente e passa um [pacote](xref:Android.OS.Bundle) que podemos usar para armazenar nosso estado de instância. Vamos usá-lo para salvar nossa contagem de clique como um valor inteiro:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Quando *a atividade A* é recriada e retomada, o `Bundle` Android passa isso `OnCreate` de volta para o nosso método. Vamos adicionar o código ao `OnCreate` para restaurar o `_counter` valor `Bundle`do passado. Adicione o seguinte código logo antes da linha em `clickbutton` que está definido: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Crie e execute o aplicativo novamente e, em seguida, clique no segundo botão algumas vezes. Quando giramos o dispositivo para o modo paisagem, a contagem é preservada!

[![Girar a tela mostra a contagem de quatro preservados](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)

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

Antes de o método [OnStop](xref:Android.App.Activity.OnStop) ser chamado, nosso `OnSaveInstanceState` novo método foi chamado para salvar `_counter` o valor em `Bundle`um. O Android passou `Bundle` isso para nós quando ele chamou o `OnCreate` nosso método, e conseguimos usá-lo para restaurar o `_counter` valor em que parou.

## <a name="summary"></a>Resumo

Neste Walkthough, usamos nosso conhecimento do ciclo de vida da atividade para preservar os dados de estado.

## <a name="related-links"></a>Links relacionados

- [ActivityLifecycle (exemplo)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Atividade do Android](xref:Android.App.Activity)
