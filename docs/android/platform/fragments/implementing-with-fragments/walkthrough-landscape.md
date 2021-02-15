---
title: Passo a passo de fragmentos – Parte 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: c0d4b0d1a31be43d16fb69eba18c07815631f496
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453928"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Paisagem de orientação do fragmento &ndash;

A [ &ndash; etapa 1 da explicação de fragmentos](./walkthrough.md) demonstrou como criar e usar fragmentos em um aplicativo Android que tem como alvo as telas menores em um telefone. A próxima etapa neste passo a passos é modificar o aplicativo para tirar proveito do espaço horizontal extra no Tablet. haverá &ndash;  uma atividade que sempre será a lista de reproduções (o `TitlesFragment` ) e  `PlayQuoteFragment` será adicionada dinamicamente à atividade em resposta a uma seleção feita pelo usuário:

[![Aplicativo em execução no Tablet](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Os telefones que estão em execução no modo paisagem também se beneficiarão desse aprimoramento:

[![Aplicativo em execução em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Atualizando o aplicativo para manipular a orientação paisagem

As seguintes modificações se basearão no trabalho que foi feito nos [fragmentos Walkthrough – Phone](./walkthrough.md)

1. Crie um layout alternativo para exibir o `TitlesFragment` e o `PlayQuoteFragment` .
1. Atualizar `TitlesFragment` para detectar se o dispositivo está exibindo ambos os fragmentos simultaneamente e alterar o comportamento de acordo.
1. Atualize `PlayQuoteActivity` para fechar quando o dispositivo estiver no modo paisagem.

## <a name="1-create-an-alternate-layout"></a>1. criar um layout alternativo

Quando a atividade principal é criada em um dispositivo Android, o Android decide qual layout será carregado com base na orientação do dispositivo. Por padrão, o Android fornecerá o arquivo de layout de **recursos/layout/activity_main. axml** . Para dispositivos que carregam no modo paisagem, o Android fornecerá o arquivo de layout **Resources/layout-Land/activity_main. axml** . O guia sobre os [recursos do Android](../../../app-fundamentals/resources-in-android/index.md) contém mais detalhes sobre como o Android decide quais arquivos de recursos carregar para um aplicativo.

Crie um layout alternativo que direcione a orientação **paisagem** seguindo as etapas descritas no guia de [layouts alternativos](../../../user-interface/android-designer/alternative-layout-views.md) . Isso deve adicionar um novo arquivo de recurso de layout ao projeto, **recursos/layout/activity_main. axml**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Layout alternativo no Gerenciador de Soluções](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Layout alternativo no Painel de Soluções](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Depois de criar o layout alternativo, edite a origem do arquivo **Resources/layout-Land/activity_main. axml** para que ele corresponda a este XML:

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

A exibição raiz da atividade recebe a ID do recurso `two_fragments_layout` e tem duas subexibições, a `fragment` e a `FrameLayout` . Enquanto o `fragment` é carregado estaticamente, o `FrameLayout` age como um "espaço reservado" que será substituído em tempo de execução pelo `PlayQuoteFragment` . Cada vez que uma nova reprodução for selecionada no `TitlesFragment` , o `playquote_container` será atualizado com uma nova instância do `PlayQuoteFragment` .

Cada uma das subexibições ocupará a altura total de seu pai. A largura de cada subexibição é controlada pelos `android:layout_weight` atributos e `android:layout_width` . Neste exemplo, cada subexibição ocupará 50% da largura fornecida pelo pai. Consulte [o documento do Google no LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) para obter detalhes sobre o _peso do layout_.

## <a name="2-changes-to-titlesfragment"></a>2. alterações em TitlesFragment

Depois que o layout alternativo tiver sido criado, será necessário atualizá-lo `TitlesFragment` . Quando o aplicativo estiver exibindo os dois fragmentos em uma atividade, o `TitlesFragment` deverá carregar o `PlayQuoteFragment` na atividade pai. Caso contrário, `TitlesFragment` o deve iniciar o `PlayQuoteActivity` que hospeda o `PlayQuoteFragment` . Um sinalizador booliano ajudará a `TitlesFragment` determinar qual comportamento deve ser usado. Esse sinalizador será inicializado no `OnActivityCreated` método.

Primeiro, adicione uma variável de instância na parte superior da `TitlesFragment` classe:

```csharp
bool showingTwoFragments;
```

Em seguida, adicione o seguinte trecho de código ao  `OnActivityCreated` para inicializar a variável: 

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

Se o dispositivo estiver sendo executado no modo paisagem, o `FrameLayout` com a ID do recurso `playquote_container` ficará visível na tela, portanto, `showingTwoFragments` será inicializado como `true` . Se o dispositivo estiver sendo executado no modo retrato, ele `playquote_container` não estará na tela, portanto, será `showingTwoFragments` `false` .

O `ShowPlayQuote` método precisará alterar como ele exibe uma aspa &ndash; em um fragmento ou iniciar uma nova atividade.  Atualize o `ShowPlayQuote` método para carregar um fragmento ao mostrar dois fragmentos, caso contrário, ele deve iniciar uma atividade:

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

Se o usuário tiver selecionado uma reprodução diferente da que está sendo exibida no momento `PlayQuoteFragment` , um novo `PlayQuoteFragment` será criado e substituirá o conteúdo do `playquote_container` dentro do contexto de um `FragmentTransaction` .

### <a name="complete-code-for-titlesfragment"></a>Código completo para TitlesFragment

Depois de concluir todas as alterações anteriores para `TitlesFragment` , a classe completa deve corresponder a este código:

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

## <a name="3-changes-to-playquoteactivity"></a>3. alterações em PlayQuoteActivity

Há um detalhe final a ser resolvido: `PlayQuoteActivity` não é necessário quando o dispositivo está no modo paisagem. Se o dispositivo estiver no modo paisagem, o `PlayQuoteActivity` não deverá estar visível. Atualize o `OnCreate` método do `PlayQuoteActivity` para que ele se feche. Esse código é a versão final do `PlayQuoteActivity.OnCreate` :

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

Essa modificação adiciona uma verificação para a orientação do dispositivo. Se estiver no modo paisagem, o `PlayQuoteActivity` mesmo será fechado.

## <a name="4-run-the-application"></a>4. executar o aplicativo

Depois que essas alterações forem concluídas, execute o aplicativo, gire o dispositivo para o modo paisagem (se necessário) e, em seguida, selecione uma reprodução. A cotação deve ser exibida na mesma tela que a lista de reproduções:

[![Aplicativo em execução em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Aplicativo em execução em um tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)