---
title: Layouts com guias com a ActionBar
description: Este guia apresenta e explica como usar as APIs ActionBar para criar uma interface do usuário com guias em um aplicativo xamarin. Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 01ff4426dfb4764b7d9ca22703aa2b1667a90454
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528540"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Layouts com guias com a ActionBar

_Este guia apresenta e explica como usar as APIs ActionBar para criar uma interface do usuário com guias em um aplicativo xamarin. Android._


## <a name="overview"></a>Visão geral

A barra de ação é um padrão de interface do usuário Android que é usado para fornecer uma interface do usuário consistente para os principais recursos, como guias, identidade do aplicativo, menus e pesquisa. No Android 3.0 (API nível 11), o Google apresentou as APIs ActionBar para a plataforma Android. As APIs ActionBar introduzir temas de interface do usuário para fornecer uma aparência consistente e classes que permitem a interfaces do usuário com guias. Este guia aborda como adicionar guias da barra de ação a um aplicativo xamarin. Android. Ele também aborda como usar a biblioteca de suporte ao Android v7 a backport ActionBar guias para aplicativos xamarin. Android visando o Android 2.1 para Android 2.3. 

Observe que `Toolbar` é um componente de barra de ação mais recente e mais generalizada que deve ser usado em vez de `ActionBar` (`Toolbar` foi projetado para substituir `ActionBar`). Para obter mais informações, consulte [barra de ferramentas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Requisitos

Qualquer aplicativo do xamarin. Android que tem como alvo o nível de API 11 (Android 3.0) ou superior tem acesso às APIs ActionBar como parte das APIs do Android nativo. 

Algumas das APIs ActionBar foram portadas volta para o nível de API 7 (Android 2.1) e estão disponíveis por meio de [V7 AppCompat biblioteca](http://developer.android.com/tools/support-library/features.html#v7-appcompat), que é disponibilizado para aplicativos xamarin. Android por meio do [biblioteca de suporte ao Android Xamarin - V7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote.



## <a name="introducing-tabs-in-the-actionbar"></a>Apresentando os guias a ActionBar

Barra de ação tenta exibir todas as suas guias simultaneamente e fazer todas as guias igual em tamanho com base na largura do rótulo da guia mais ampla. Isso é ilustrado pela captura de tela a seguir: 

![Captura de tela do exemplo da barra de ação com todas as guias de tamanho igual mostrado](with-action-bar-images/image1.png)

Quando a barra de ação não é possível exibir todas as guias, ele configurará as guias em uma exibição rolável horizontalmente. O usuário talvez passe o dedo para esquerda ou direita para ver as guias restantes. Esta captura de tela do Google Play mostra um exemplo disso: 

![Captura de tela de exemplo das guias em uma exibição rolável horizontalmente](with-action-bar-images/image2.png)

Cada guia na barra de ação deve ser associado com um [ *fragmento*](~/android/platform/fragments/index.md). Quando o usuário seleciona uma guia, o aplicativo exibirá o fragmento que está associado com a guia. A barra de ação não é responsável por exibir o fragmento apropriado para o usuário. Em vez disso, a ActionBar notificará um aplicativo sobre alterações de estado em uma guia por meio de uma classe que implementa a interface ActionBar.ITabListener. Essa interface fornece três métodos de retorno de chamada que Android será invocada quando muda o estado de guia: 

-  **OnTabSelected** -esse método é chamado quando o usuário seleciona a guia. O fragmento deve ser exibida.

-  **OnTabReselected** -esse método é chamado quando a guia já está selecionada, mas for selecionada novamente pelo usuário. Esse retorno de chamada normalmente é usado para atualização do fragmento exibido.

-  **OnTabUnselected** -esse método é chamado quando o usuário seleciona outra guia. Esse retorno de chamada é usado para salvar o estado no fragmento exibido antes de desaparecer.

Xamarin. Android encapsula o `ActionBar.ITabListener` com os eventos no `ActionBar.Tab` classe. Aplicativos podem atribuir manipuladores de eventos a um ou mais desses eventos. Há três eventos (uma para cada método em `ActionBar.ITabListener`) que gerará uma guia de barra de ação: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Adicionando guias à ActionBar

A barra de ação é nativa para Android 3.0 (API nível 11) e superior e está disponível para qualquer aplicativo xamarin. Android que se destina essa API, no mínimo. 

As etapas a seguir ilustram como adicionar guias de barra de ação para uma atividade do Android: 

1. No `OnCreate` método de uma atividade &ndash; *antes de inicializar quaisquer widgets de interface do usuário* &ndash; um aplicativo deve definir o `NavigationMode` sobre o `ActionBar` para `ActionBar.NavigationModeTabs` conforme mostrado neste código trecho de código:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Criar uma nova guia usando `ActionBar.NewTab()`.

3. Atribuir manipuladores de eventos ou fornecer um personalizado `ActionBar.ITabListener` implementação que irá responder aos eventos que são gerados quando o usuário interage com as guias da barra de ação.

4. A guia que foi criada na etapa anterior para adicionar o `ActionBar`.


O código a seguir é um exemplo de como usar estas etapas para adicionar guias a um aplicativo que usa os manipuladores de eventos para responder a alterações de estado: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>Manipuladores de evento vs ActionBar.ITabListener

Os aplicativos devem usar manipuladores de eventos e `ActionBar.ITabListener` para cenários diferentes. Manipuladores de eventos oferecem uma determinada quantidade de conveniência sintática; eles poupar você da necessidade de criar uma classe e implementar `ActionBar.ITabListener`. Esta conveniência vêm com um custo &ndash; xamarin. Android executa essa transformação para você, criando uma classe e implementando `ActionBar.ITabListener` para você. Isso é bom quando um aplicativo tem um número limitado de guias. 

Ao lidar com várias guias, ou a funcionalidade comum entre as guias da barra de ação de compartilhamento, pode ser mais eficiente em termos de memória e desempenho para criar uma classe personalizada que implementa `ActionBar.ITabListener`e compartilhar uma única instância da classe. Isso reduzirá o número de GREF que um aplicativo xamarin. Android está usando. 



### <a name="backwards-compatibility-for-older-devices"></a>Com versões anteriores a compatibilidade de dispositivos mais antigos

O [biblioteca de suporte ao Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back portas ActionBar guias para Android 2.1 (API nível 7). As guias são acessíveis em um aplicativo xamarin. Android depois que este componente foi adicionado ao projeto.

Para usar a barra de ação, uma atividade deve subclasse `ActionBarActivity` e usar o tema AppCompat, conforme mostrado no trecho de código a seguir:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Uma atividade pode obter uma referência para sua barra de ação do `ActionBarActivity.SupportingActionBar` propriedade. O trecho de código a seguir ilustra um exemplo de como configurar a barra de ação em uma atividade:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```


## <a name="summary"></a>Resumo

Neste guia, discutimos como criar uma interface do usuário com guias em um xamarin. Android usando a barra de ação. Abordamos como adicionar guias para a barra de ação e como uma atividade pode interagir com eventos de guia por meio de `ActionBar.ITabListener` interface. Também vimos como o backports do suporte à biblioteca do Android v7 AppCompat pacote a ActionBar guias para versões mais antigas do Android. 


## <a name="related-links"></a>Links relacionados

- [ActionBarTabs (amostra)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Fragmentos](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Barra de ação padrão](http://developer.android.com/design/patterns/actionbar.html)
- [AppCompat do Android v7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Pacote do NuGet do xamarin. Android suporte biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
