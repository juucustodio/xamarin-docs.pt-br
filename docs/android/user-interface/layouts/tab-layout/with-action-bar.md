---
title: Layouts com guias com o ActionBar
description: Este guia apresenta e explica como usar as APIs do ActionBar para criar uma interface do usuário com guias em um aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d37537f345a1532e38ab4d016cfbd5b26eae8b3a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758543"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Layouts com guias com o ActionBar

_Este guia apresenta e explica como usar as APIs do ActionBar para criar uma interface do usuário com guias em um aplicativo Xamarin. Android._

## <a name="overview"></a>Visão geral

A barra de ação é um padrão de interface do usuário do Android que é usado para fornecer uma interface de usuários consistente para os principais recursos, como guias, identidade do aplicativo, menus e pesquisa. No Android 3,0 (nível de API 11), o Google introduziu as APIs ActionBar para a plataforma Android. As APIs ActionBar introduzem temas de interface do usuário para fornecer uma aparência consistente e classes que permitem interfaces de usuário com guias. Este guia discute como adicionar guias de barra de ações a um aplicativo Xamarin. Android. Ele também aborda como usar a biblioteca de suporte do Android v7 para backport ActionBar guias para aplicativos Xamarin. Android direcionando Android 2,1 para Android 2,3. 

Observe que `Toolbar` é um componente de barra de ação mais novo e mais generalizado que você deve `ActionBar` usar`Toolbar` em vez de ( `ActionBar`foi projetado para substituir). Para obter mais informações, consulte [barra de ferramentas](~/android/user-interface/controls/tool-bar/index.md). 

## <a name="requirements"></a>Requisitos

Qualquer aplicativo Xamarin. Android voltado para a API de nível 11 (Android 3,0) ou superior tem acesso às APIs ActionBar como parte das APIs nativas do Android. 

Algumas das APIs ActionBar foram portadas de volta para o nível de API 7 (Android 2,1) e estão disponíveis por meio da [biblioteca do v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat), disponibilizada para aplicativos Xamarin. Android por meio do [Xamarin Android support library-v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) Package.

## <a name="introducing-tabs-in-the-actionbar"></a>Apresentando as guias no ActionBar

A barra de ação tenta exibir todas as suas guias simultaneamente e fazer com que todas as guias sejam iguais em tamanho com base na largura do rótulo de guia mais largo. Isso é ilustrado pela seguinte captura de tela: 

![Captura de tela de exemplo de ActionBar com todas as guias de tamanho igual mostradas](with-action-bar-images/image1.png)

Quando o ActionBar não pode exibir todas as guias, ele irá configurar as guias em uma exibição rolável horizontalmente. O usuário pode passar o dedo para a esquerda ou direita para ver as guias restantes. Esta captura de tela da Google Play mostra um exemplo disso: 

![Captura de tela de exemplo de guias em uma exibição rolável horizontalmente](with-action-bar-images/image2.png)

Cada guia na barra de ação deve ser associada a um [*fragmento*](~/android/platform/fragments/index.md). Quando o usuário selecionar uma guia, o aplicativo exibirá o fragmento associado à guia. O ActionBar não é responsável por exibir o fragmento apropriado para o usuário. Em vez disso, o ActionBar notificará um aplicativo sobre as alterações de estado em uma guia por meio de uma classe que implementa a interface ActionBar. ITabListener. Essa interface fornece três métodos de retorno de chamada que o Android invocará quando o estado da guia for alterado: 

- **OnTabSelected** -esse método é chamado quando o usuário seleciona a guia. Ele deve exibir o fragmento.

- **OnTabReselected** -esse método é chamado quando a guia já está selecionada, mas é selecionada novamente pelo usuário. Esse retorno de chamada é normalmente usado para atualizar/atualizar o fragmento exibido.

- **OnTabUnselected** -esse método é chamado quando o usuário seleciona outra guia. Esse retorno de chamada é usado para salvar o estado no fragmento exibido antes de desaparecer.

O Xamarin. Android encapsula o `ActionBar.ITabListener` com eventos `ActionBar.Tab` na classe. Os aplicativos podem atribuir manipuladores de eventos a um ou mais desses eventos. Há três eventos (um para cada método em `ActionBar.ITabListener`) que uma guia de barra de ação irá gerar: 

- TabSelected
- TabReselected
- TabUnselected

### <a name="adding-tabs-to-the-actionbar"></a>Adicionando guias ao ActionBar

O ActionBar é nativo para Android 3,0 (API nível 11) e superior e está disponível para qualquer aplicativo Xamarin. Android que se destinar a essa API como um mínimo. 

As etapas a seguir ilustram como adicionar guias ActionBar a uma atividade do Android: 

1. &ndash; `NavigationMode` `ActionBar.NavigationModeTabs` `ActionBar` No método de uma atividade *antes de inicializar qualquer widget da interface do usuário* , umaplicativodevedefinironocomomostradonestetrechodecódigo:&ndash; `OnCreate`

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Crie uma nova guia usando `ActionBar.NewTab()`.

3. Atribua manipuladores de eventos ou forneça uma `ActionBar.ITabListener` implementação personalizada que responderá aos eventos que são gerados quando o usuário interage com as guias ActionBar.

4. Adicione a guia que foi criada na etapa anterior ao `ActionBar`.

O código a seguir é um exemplo do uso dessas etapas para adicionar guias a um aplicativo que usa manipuladores de eventos para responder a alterações de Estado: 

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

#### <a name="event-handlers-vs-actionbaritablistener"></a>Manipuladores de eventos vs ActionBar. ITabListener

Os aplicativos devem usar manipuladores de `ActionBar.ITabListener` eventos e para cenários diferentes. Os manipuladores de eventos oferecem uma determinada quantidade de conveniência sintática; Eles evitam que você precise criar uma classe e implementá `ActionBar.ITabListener`-la. Essa conveniência acompanha um Xamarin de custo &ndash; . o Android realiza essa transformação para você, criando uma classe e implementando `ActionBar.ITabListener` para você. Isso é bom quando um aplicativo tem um número limitado de guias. 

Ao lidar com muitas guias ou compartilhar funcionalidades comuns entre guias ActionBar, pode ser mais eficiente em termos de memória e desempenho para criar uma classe personalizada que implementa `ActionBar.ITabListener`e compartilhar uma única instância da classe. Isso reduzirá o número de GREF que um aplicativo Xamarin. Android está usando. 

### <a name="backwards-compatibility-for-older-devices"></a>Compatibilidade com versões anteriores para dispositivos mais antigos

A [biblioteca de suporte do Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) back ports ActionBar guias para Android 2,1 (API nível 7). As guias podem ser acessadas em um aplicativo Xamarin. Android depois que esse componente tiver sido adicionado ao projeto.

Para usar o ActionBar, uma atividade deve subclasse `ActionBarActivity` e usar o tema AppCompat, conforme mostrado no seguinte trecho de código:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Uma atividade pode obter uma referência a seu ActionBar da `ActionBarActivity.SupportingActionBar` propriedade. O trecho de código a seguir ilustra um exemplo de configuração de ActionBar em uma atividade:

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

Neste guia, discutimos como criar uma interface do usuário com guias em um Xamarin. Android usando o ActionBar. Abordamos como adicionar guias ao ActionBar e como uma atividade pode interagir com os eventos de guia por meio `ActionBar.ITabListener` da interface. Também vimos como a biblioteca de suporte do Android v7 AppCompat Package reporta as guias ActionBar para versões mais antigas do Android. 

## <a name="related-links"></a>Links relacionados

- [ActionBarTabs (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-actionbartabs)
- [Toolbar](~/android/user-interface/controls/tool-bar/index.md)
- [Fragmentos](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Padrão de barra de ações](https://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Biblioteca de suporte do Xamarin. Android pacote NuGet do v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
