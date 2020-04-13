---
title: Passo a passo de fragmentos – Parte 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 4d9ef88f39914f8fa5e578577ee9f6977c2bc88e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020262"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Fragmentos passo &ndash; a passo da paisagem

O [Passo a &ndash; Passo 1 fragmentos](./walkthrough.md) mostrou como criar e usar fragmentos em um aplicativo para Android que tem como alvo as telas menores de um telefone. O próximo passo neste passo a passo é modificar o aplicativo &ndash; para aproveitar o espaço horizontal extra no tablet `TitlesFragment`haverá `PlayQuoteFragment` uma atividade que sempre será a lista de jogadas (a ) e será adicionada dinamicamente à Atividade em resposta a uma seleção feita pelo usuário:

[![Aplicativo rodando em tablet](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Os telefones que estão sendo executados no modo paisagem também se beneficiarão desse aprimoramento:

[![Aplicativo rodando em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Atualizando o aplicativo para lidar com a orientação paisagística

As seguintes modificações serão construídas sobre o trabalho que foi feito no [Passo a Passo dos Fragmentos - Telefone](./walkthrough.md)

1. Crie um layout alternativo `TitlesFragment` para `PlayQuoteFragment`exibir o e .
1. Atualize `TitlesFragment` para detectar se o dispositivo está exibindo ambos os fragmentos simultaneamente e altere o comportamento de acordo.
1. Atualize `PlayQuoteActivity` para fechar quando o dispositivo estiver no modo paisagem.

## <a name="1-create-an-alternate-layout"></a>1. Crie um layout alternativo

Quando a Atividade Principal é criada em um dispositivo Android, o Android decidirá qual layout carregar com base na orientação do dispositivo. Por padrão, o Android fornecerá o arquivo de layout **Resources/layout/activity_main.axml.** Para dispositivos que carregam no modo paisagem, o Android fornecerá o arquivo de layout **Resources/layout resources/layout activity_main.axml.** O guia sobre [recursos android](/xamarin/android/app-fundamentals/resources-in-android) contém mais detalhes sobre como o Android decide quais arquivos de recursos carregar para um aplicativo.

Crie um layout alternativo que visa a orientação **paisagística** seguindo as etapas descritas no guia [Layouts Alternativos.](/xamarin/android/user-interface/android-designer/alternative-layout-views) Isso deve adicionar um novo arquivo de recurso de layout ao projeto, **Recursos/layout/activity_main.axml**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Layout alternativo no Solution Explorer](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Layout alternativo no Solution Pad](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Depois de criar o layout alternativo, edite a fonte do arquivo **Resources/layout-land/activity_main.axml** para que ele corresponda a este XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

A visão raiz da atividade é `two_fragments_layout` dada ao ID `fragment` de `FrameLayout`recurso e tem duas subvisualizações, a e a . Enquanto `fragment` o está maquinado estiver carregado estáticamente, o atua `FrameLayout` como um `PlayQuoteFragment`"espaço reservado" que será substituído em tempo de execução pelo . Cada vez que uma nova `TitlesFragment`peça `playquote_container` é selecionada no , `PlayQuoteFragment`o será atualizado com uma nova instância do .

Cada uma das sub-visualizações ocupará toda a altura de seus pais. A largura de cada subviera é controlada pelos `android:layout_weight` atributos. `android:layout_width` Neste exemplo, cada subvimento ocupará 50% da largura fornecida pelo pai. Consulte [o documento do Google no LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) para obter detalhes sobre o Peso do _Layout_.

## <a name="2-changes-to-titlesfragment"></a>2. Alterações em TitlesFragment

Uma vez criado o layout alternativo, é `TitlesFragment`necessário atualizar . Quando o aplicativo estiver exibindo os dois fragmentos em uma atividade, então `TitlesFragment` deve carregar o `PlayQuoteFragment` na atividade pai. Caso contrário, `TitlesFragment` deve `PlayQuoteActivity` lançar `PlayQuoteFragment`o que hospedar o . Uma bandeira booleana ajudará a `TitlesFragment` determinar qual comportamento deve usar. Esta bandeira será inicializada `OnActivityCreated` no método.

Primeiro, adicione uma variável de `TitlesFragment` instância no topo da classe:

```csharp
bool showingTwoFragments;
```

Em seguida, adicione o seguinte `OnActivityCreated` trecho de código para inicializar a variável: 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

Se o dispositivo estiver em execução no modo paisagem, `FrameLayout` então o ID `playquote_container` de recurso será visível na tela, então `showingTwoFragments` será inicializado para `true`. Se o dispositivo estiver em `playquote_container` execução no modo retrato, `showingTwoFragments` então `false`não estará na tela, assim será .

O `ShowPlayQuote` método precisará alterar a forma &ndash; como exibe uma citação em um fragmento ou lançar uma nova atividade.  Atualize `ShowPlayQuote` o método para carregar um fragmento ao mostrar dois fragmentos, caso contrário, ele deve iniciar uma Atividade:

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Se o usuário selecionou uma reprodução diferente da que está `PlayQuoteFragment`sendo exibida `PlayQuoteFragment` atualmente, então um novo `playquote_container` será criado `FragmentTransaction`e substituirá o conteúdo do dentro do contexto de um .

### <a name="complete-code-for-titlesfragment"></a>Código completo para TitlesFragment

Após concluir todas as `TitlesFragment`alterações anteriores para , a classe completa deve corresponder a este código:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }

        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3. Alterações em PlayQuoteActivity

Há um detalhe final para `PlayQuoteActivity` cuidar: não é necessário quando o dispositivo está em modo paisagístico. Se o dispositivo estiver `PlayQuoteActivity` no modo paisagem, não deve ser visível. Atualize `OnCreate` o `PlayQuoteActivity` método para que ele se feche sozinho. Este código é a `PlayQuoteActivity.OnCreate`versão final de:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

Esta modificação adiciona uma verificação para a orientação do dispositivo. Se estiver no modo `PlayQuoteActivity` paisagem, então se fechará.

## <a name="4-run-the-application"></a>4. Execute o aplicativo

Uma vez que essas alterações estejam concluídas, execute o aplicativo, gire o dispositivo para o modo paisagem (se necessário) e selecione uma reprodução. A citação deve ser exibida na mesma tela da lista de reproduzidos:

[![Aplicativo rodando em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Aplicativo rodando em um tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
