---
title: "Instruções passo a passo - Criando uma interface do usuário com guias com TabHost"
description: "Este artigo traz orientações durante a criação de uma interface de usuário com guias em xamarin usando a API TabHost."
ms.topic: article
ms.prod: xamarin
ms.assetid: AD6E2173-974E-477C-940F-0CAB5E53326D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2dd397e824ce7735be4421c3f258852de3f77ecb
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="walkthrough---creating-a-tabbed-ui-with-tabhost"></a>Instruções passo a passo - Criando uma interface do usuário com guias com TabHost

_Este artigo traz orientações durante a criação de uma interface de usuário com guias em xamarin usando a API TabHost._

> [!NOTE]
> `TabHost` é uma API antiga que foi substituída pelo Google. Os desenvolvedores são encorajados a criar aplicativos com guias usando o [barra de ação](~/android/user-interface/controls/action-bar.md). O `ActionBar` está disponível em todos os versão do Android. Ele foi introduzido no Android 3.0 (API nível 11) e volta foi movido para o Android 2.2 (API nível 8) e no Android 2.3 (API nível 10) no [V7 AppCompat biblioteca](http://developer.android.com/tools/support-library/features.html#v7-appcompat), que está disponível para xamarin por meio de [Xamarin Biblioteca de suporte do Android - V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote.

Este artigo traz orientações durante a criação de uma interface de usuário com guias em xamarin usando o `TabHost` API. Esta é uma API mais antigo que está disponível em todas as versões do Android. Este exemplo criará um aplicativo com estas três guias com a lógica para cada guia sendo encapsulada em uma atividade.
Captura de tela a seguir é um exemplo de aplicativo que vamos criar:

![Captura de tela de exemplo do aplicativo em várias guias](creating-a-tabbed-ui-images/image02.png)


## <a name="creating-the-application"></a>Criando o aplicativo

Baixe e descompacte [TabHostWalkthrough](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/).
Este projeto serve como o ponto de partida para nosso aplicativo e contém algumas imagens. Se você examinar este projeto, você verá que já criou os recursos drawable para os ícones de guia.

Primeiro vamos atualizar o arquivo de layout **Resources/Layout/Main.axml** que hospedam as guias. Editar **Resources/Layout/Main.axml** e insira o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TabHost xmlns:android="http://schemas.android.com/apk/res/android"
         android:id="@android:id/tabhost"
         android:layout_width="fill_parent"
         android:layout_height="fill_parent">
    <LinearLayout
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:padding="5dp">
        <TabWidget
                android:id="@android:id/tabs"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content" />
        <FrameLayout
                android:id="@android:id/tabcontent"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:padding="5dp" />
    </LinearLayout>
</TabHost>
```

Captura de tela a seguir mostra o layout no Designer de Xamarin:

[![Captura de tela do layout TabHost no Designer de Xamarin](creating-a-tabbed-ui-images/image04-sml.png)](creating-a-tabbed-ui-images/image04.png#lightbox)

O TabHost deve ter dois modos de exibição do filho dentro dela: uma `TabWidget` e um `FrameLayout`. A posição de `TabWidget` e `FrameLayout` verticalmente dentro de `TabHost`, um `LinearLayout` é usado. O FrameLayout é onde fica o conteúdo para cada guia, que está vazio porque o `TabHost` incorporará automaticamente cada atividade em tempo de execução. Há várias regras que devem ser observadas quando se trata de criar o layout de interfaces de usuário com guias:

-   O `TabHost` devem ter a id `@android:id/tabhost`.

-   O `TabWidget` devem ter a id `@android:id/tabs`.

-   O `FrameLayout` devem ter a id `@android:id/tabcontent`.

-   `TabHost` requer que todas as atividades que ele gerencia herdar de `TabActivity`. Portanto, é importante para a subclasse `TabActivity` aqui &ndash; uma atividade regular não funcionará.

Edite o arquivo **MainActivity.cs** para que a classe `MainActivity` subclasses `TabActivity` conforme mostrado no trecho de código a seguir:

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/ic_launcher")]
public class MainActivity : TabActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);
    }
}
```

Criar quatro classes separadas de atividade em seu projeto: `MyScheduleActivity`, `SessionsActivity`, `SpeakersActivity`, e `WhatsOnActivity`. Cada atividade fará a interface de usuário de uma guia. Agora, essas atividades serão um stub que exibe um `TextView` com uma mensagem simples. Editar o código em cada atividade para conter o seguinte `OnCreate` implementação:

```csharp
[Activity]
public class MyScheduleActivity : Activity
{
    protected override void OnCreate (Bundle savedInstanceState)
    {
        base.OnCreate (savedInstanceState);
        TextView textview = new TextView (this);
        textview.Text = "This is the My Schedule tab";
        SetContentView (textview);
    }
}
```

Observe que o código acima não usa um arquivo de layout. Ele cria apenas uma `TextView` com algum texto e define que `TextView` como o modo de exibição de conteúdo. Duplicar isso para cada uma das três atividades restantes.

Em seguida, atribuirá os ícones para cada guia. Cada guia requer dois ícones &ndash; um para o estado selecionado e outro para o estado desmarcado. Um exemplo de como esses dois ícones diferentes pode ser visto nas duas imagens a seguir (os ícones necessários para este aplicativo já foram adicionados ao projeto de exemplo):

![Captura de tela de ícones de estado desmarcado e estado selecionado](creating-a-tabbed-ui-images/tab-icons.png)


Podemos atribuirá os recursos drawable às guias ícone definindo um *listadeestadoDrawable*. Drawables lista de estado são um recurso drawable especiais que é definidos em XML e permitem que você especifique imagens diferentes que são específicas para o estado do item. Neste exemplo, há uma imagem que é usada quando uma guia é selecionada e outra que é usado quando não estiver selecionada na guia. Para economizar tempo, os drawables de lista de estado necessárias foram adicionadas ao projeto. A lista a seguir mostra os arquivos e o XML contém:

-   `ic_tab_my_schedule.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

-   `ic_tab_sessions.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_sessions_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_sessions_unselected"/>
    </selector>
    ```

-   `ic_tab_speakers.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_speakers_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_speakers_unselected"/>
    </selector>
    ```

-   `ic_tab_whats_on.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android">
        <item android:drawable="@drawable/ic_tab_whats_on_selected"
              android:state_selected="true"/>
        <item android:drawable="@drawable/ic_tab_whats_on_unselected"/>
    </selector>
    ```

Guias são adicionadas para o `TabHost` programaticamente, que é uma tarefa muito repetitiva. Para ajudar com isso, adicione o seguinte método à classe `MainActivity`:

```csharp
private void CreateTab(Type activityType, string tag, string label, int drawableId )
{
    var intent = new Intent(this, activityType);
    intent.AddFlags(ActivityFlags.NewTask);

    var spec = TabHost.NewTabSpec(tag);
    var drawableIcon = Resources.GetDrawable(drawableId);
    spec.SetIndicator(label, drawableIcon);
    spec.SetContent(intent);

    TabHost.AddTab(spec);
}
```

Cada guia no `TabHost` é representado por uma instância das do `TabHost.TabSpec` classe. Essa instância contém os metadados necessários para processar a guia, especificamente:

-   **Texto e o ícone** &ndash; a ser exibida no `TabWidget`.

-   **Guia conteúdo** &ndash; isso pode ser um `Activity` ou um `View` e é exibido quando a guia é selecionada.

-   **Marca exclusivo** &ndash; cada guia deve ter uma marca exclusivo atribuída a ele.

É necessário adicionar um `TabHost.TabSpec` instância para cada guia em nosso aplicativo. Permite fazer isso na próxima etapa.

O método Update `OnCreate` em `MainActivity` para que ele se parece com o código a seguir:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateTab(typeof(WhatsOnActivity), "whats_on", "What's On", Resource.Drawable.ic_tab_whats_on);
    CreateTab(typeof(SpeakersActivity), "speakers", "Speakers", Resource.Drawable.ic_tab_speakers);
    CreateTab(typeof(SessionsActivity), "sessions", "Sessions", Resource.Drawable.ic_tab_sessions);
    CreateTab(typeof(MyScheduleActivity), "my_schedule", "My Schedule", Resource.Drawable.ic_tab_my_schedule);
}
```

Execute o aplicativo. Seu aplicativo deve ser semelhante a captura de tela mostrada no início deste passo a passo.

É só isso! Você criou um aplicativo com guias que concede ao usuário navegar de uma maneira fácil para partes diferentes de um aplicativo.



## <a name="summary"></a>Resumo

Este capítulo discutido layouts com guias e guiado você pelo processo de criação de um aplicativo com guias. Passo a passo demonstrou como usar um `TabActivity` para aumentar a um layout de arquivo que hospeda um `TabHost` e um `TabWidget`. O `TabHost` foi populada com uma coleção de `TabHost.TabSpec` objetos que pode ser usados pelo `TabHost` em tempo de execução para instanciar as atividades que devem ser usadas em cada guia.



## <a name="related-links"></a>Links relacionados

- [TabHostWalkthrough (exemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/TabHostWalkthrough/)
- [TabHost](https://developer.xamarin.com/api/type/Android.Widget.TabHost/)
- [TabWidget](https://developer.xamarin.com/api/type/Android.Widget.TabWidget/)
- [TabActivity](https://developer.xamarin.com/api/type/Android.App.TabActivity/)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacote do NuGet do suporte do Android biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat v7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
