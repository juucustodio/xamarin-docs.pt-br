---
title: Layouts com guias com a barra de ação
description: Este guia apresenta e explica como usar as APIs ActionBar para criar uma interface com guias do usuário em um aplicativo xamarin.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 3e96ce2064391d585943f4d79453f8b4f8c6f583
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
ms.locfileid: "32020121"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Layouts com guias com a barra de ação

_Este guia apresenta e explica como usar as APIs ActionBar para criar uma interface com guias do usuário em um aplicativo xamarin._


## <a name="overview"></a>Visão geral

A barra de ação é um padrão de Android da interface do usuário que é usado para fornecer uma interface de usuário consistente para os principais recursos como pesquisa, identidade do aplicativo, menus e guias. No Android 3.0 (API nível 11), Google introduziu as APIs ActionBar para a plataforma Android. As APIs ActionBar introduzir temas de interface do usuário para fornecer uma aparência consistente e classes que permitem a interfaces de usuário com guias. Este guia descreve como adicionar guias de barra de ação para um aplicativo xamarin. Ele também discute como usar a biblioteca de suporte Android v7 para guias de barra de ação backport a aplicativos xamarin direcionamento Android 2.1 para Android 2.3. 

Observe que `Toolbar` é um componente de barra de ação mais generalizada e mais recentes que você deve usar em vez de `ActionBar` (`Toolbar` foi projetado para substituir `ActionBar`). Para obter mais informações, consulte [barra de ferramentas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Requisitos

Qualquer aplicativo xamarin que tem como alvo o nível de API 11 (3.0 Android) ou superior tem acesso às APIs do ActionBar como parte das APIs nativas Android. 

Algumas das APIs ActionBar foram adaptadas volta para o nível de API 7 (Android 2.1) e estão disponíveis por meio de [V7 AppCompat biblioteca](http://developer.android.com/tools/support-library/features.html#v7-appcompat), que será disponibilizado para aplicativos xamarin por meio do [Xamarin biblioteca de suporte do Android - V7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote.



## <a name="introducing-tabs-in-the-actionbar"></a>Guias de Introdução na barra de ação

A barra de ação tenta exibir todas suas guias simultaneamente e fazer todas as guias com base na largura do rótulo da guia mais ampla de tamanho igual. Isso é ilustrado pela captura de tela a seguir: 

![Captura de tela de exemplo de barra de ação com todos os guias de tamanho igual mostradas](with-action-bar-images/image1.png)

Quando a barra de ação não é possível exibir todas as guias, ele configurará as guias em uma exibição rolável horizontalmente. O usuário pode passar à esquerda ou direita para ver as guias restantes. Esta captura de tela do Google Play mostra um exemplo disso: 

![Captura de tela de exemplo de guias em uma exibição rolável horizontalmente](with-action-bar-images/image2.png)

Cada guia na barra de ação deve ser associado a um [ *fragmento*](~/android/platform/fragments/index.md). Quando o usuário seleciona uma guia, o aplicativo exibirá o fragmento que está associado com a guia. A barra de ação não é responsável por exibir o fragmento apropriado para o usuário. Em vez disso, a barra de ação será notificado sobre alterações de estado em uma guia através de uma classe que implementa a interface ActionBar.ITabListener uma aplicação. Essa interface fornece três métodos de retorno de chamada que Android chamará quando muda o estado da guia: 

-  **OnTabSelected** -este método é chamado quando o usuário seleciona a guia. Ele deve exibir o fragmento.

-  **OnTabReselected** -este método é chamado quando a guia já está selecionada, mas for selecionada novamente pelo usuário. Normalmente, esse retorno de chamada é usado para atualização o fragmento exibido.

-  **OnTabUnselected** -este método é chamado quando o usuário seleciona outra guia. Esse retorno de chamada é usado para salvar o estado no fragmento exibido antes de ela desaparece.

Xamarin encapsula o `ActionBar.ITabListener` com eventos no `ActionBar.Tab` classe. Aplicativos podem atribuir manipuladores de eventos para um ou mais desses eventos. Há três eventos (uma para cada método em `ActionBar.ITabListener`) que irá gerar uma guia de barra de ação: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Adicionando guias para a barra de ação

A barra de ação é nativo 3.0 Android (API nível 11) e superior e está disponível para qualquer aplicativo xamarin que tem como alvo essa API, no mínimo. 

As etapas a seguir ilustram como adicionar guias da barra de ação para uma atividade Android: 

1. No `OnCreate` método de uma atividade &ndash; *antes de iniciar qualquer widgets de interface do usuário* &ndash; um aplicativo deve definir o `NavigationMode` no `ActionBar` para `ActionBar.NavigationModeTabs` conforme mostrado neste código trecho de código:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Criar uma nova guia usando `ActionBar.NewTab()`.

3. Atribuir manipuladores de eventos ou forneça um personalizado `ActionBar.ITabListener` implementação responde aos eventos que são gerados quando o usuário interage com as guias da barra de ação.

4. Adicionar guia que foi criada na etapa anterior para o `ActionBar`.


O código a seguir é um exemplo de como usar estas etapas para adicionar guias para um aplicativo que usa os manipuladores de eventos para responder às alterações de estado: 

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


#### <a name="event-handlers-vs-actionbaritablistener"></a>Manipuladores de evento x ActionBar.ITabListener

Os aplicativos devem usar manipuladores de eventos e `ActionBar.ITabListener` para cenários diferentes. Manipuladores de eventos oferecem uma determinada quantidade de conveniência sintática; eles evitar que seja necessário criar uma classe e implementar `ActionBar.ITabListener`. Essa conveniência vêm com um custo &ndash; xamarin executa essa transformação para você, criando uma classe e implementando `ActionBar.ITabListener` para você. Isso é bom quando um aplicativo tem um número limitado de guias. 

Ao lidar com várias guias, ou a funcionalidade comum entre as guias da barra de ação de compartilhamento, pode ser mais eficiente em termos de memória e desempenho para criar uma classe personalizada que implementa `ActionBar.ITabListener`e o compartilhamento de uma única instância da classe. Isso reduzirá o número de GREF que um aplicativo xamarin está usando. 



### <a name="backwards-compatibility-for-older-devices"></a>Versões anteriores a compatibilidade de dispositivos mais antigos

O [biblioteca de suporte Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back portas guias da barra de ação para Android 2.1 (API nível 7). Guias são acessíveis em um aplicativo xamarin depois que este componente foi adicionado ao projeto.

Para usar a barra de ação, uma atividade deve subclasse `ActionBarActivity` e usar o tema de AppCompat conforme mostrado no trecho de código a seguir:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Uma atividade pode obter uma referência à sua barra de ação do `ActionBarActivity.SupportingActionBar` propriedade. O trecho de código a seguir ilustra um exemplo de configuração de barra de ação em uma atividade:

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

Este guia discutimos como criar uma interface com guias do usuário em um xamarin usando a barra de ação. Abordamos como adicionar guias para a barra de ação e como uma atividade pode interagir com eventos de guia via o `ActionBar.ITabListener` interface. Também vimos como a biblioteca de suporte Android v7 AppCompat pacote backports a barra de ação guias para versões mais antigas do Android. 


## <a name="related-links"></a>Links relacionados

- [ActionBarTabs (exemplo)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Fragmentos](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Barra de ação padrão](http://developer.android.com/design/patterns/actionbar.html)
- [Do Android v7 AppCompat](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Pacote do NuGet do suporte de xamarin biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
