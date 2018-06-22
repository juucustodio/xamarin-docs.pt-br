---
title: Fragmentos de instruções passo a passo - parte 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 58291388d375a4fd9273c8e0cd46db3799966766
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798908"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Instruções passo a passo de fragmentos &ndash; paisagem

O [passo a passo de fragmentos &ndash; parte 1](./walkthrough.md) demonstraram como criar e usar os fragmentos em um aplicativo do Android que tem como alvo as telas menores em um telefone. A próxima etapa neste passo a passo é modificar o aplicativo para aproveitar o espaço extra horizontal em tablet &ndash; haverá uma atividade que sempre será a lista de opções (o `TitlesFragment`) e `PlayQuoteFragment` serão adicionadas dinamicamente para a atividade em resposta a uma seleção feita pelo usuário:

[![Aplicativo em execução no tablet](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Telefones que estão em execução no modo paisagem também se beneficiarão com esse aprimoramento:

[![Aplicativo em execução em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Atualizando o aplicativo para lidar com a orientação de paisagem

As seguintes modificações se baseará o trabalho foi realizado no [fragmentos passo a passo - Phone](./walkthrough.md)

1. Criar um layout alternativo para exibir tanto os `TitlesFragment` e `PlayQuoteFragment`.
1. Atualização `TitlesFragment` para detectar se o dispositivo está exibindo simultaneamente ambos os fragmentos e alterar o comportamento.
1. Atualização `PlayQuoteActivity` fechar quando o dispositivo estiver no modo paisagem.

## <a name="1-create-an-alternate-layout"></a>1. Criar um layout alternativo

Quando a atividade principal é criada em um dispositivo Android, o Android decidirá qual layout de carga com base na orientação do dispositivo. Por padrão, o Android fornecerá o **Resources/layout/activity_main.axml** arquivo de layout. Para dispositivos que carregam no modo paisagem Android fornecerá o **Resources/layout-land/activity_main.axml** arquivo de layout. O guia [recursos Android](/xamarin/android/app-fundamentals/resources-in-android) contém mais detalhes sobre como o Android decide quais recursos de arquivos para carregar para um aplicativo.

Criar um layout alternativo que tem como destino **paisagem** orientação seguindo as etapas descritas no [Layouts alternativos](/xamarin/android/user-interface/android-designer/alternative-layout-views) guia. Isso deve adicionar um novo arquivo de recursos de layout para o projeto, **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Layout alternativo no Gerenciador de soluções](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Layout alternativo no teclado de solução](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Depois de criar o layout alternativo, edite a fonte do arquivo **Resources/layout-land/activity_main.axml** para que ele corresponda a esse XML:

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

O modo de exibição de raiz da atividade é fornecido a ID de recurso `two_fragments_layout` e tem dois modos de exibição sub, um `fragment` e um `FrameLayout`. Enquanto o `fragment` é estaticamente carregado, o `FrameLayout` atua como um "espaço reservado" que será substituído em tempo de execução, o `PlayQuoteFragment`. Cada vez que uma nova opção está selecionada no `TitlesFragment`, o `playquote_container` será atualizada com uma nova instância do `PlayQuoteFragment`.

Cada um dos modos de exibição sub ocupará a altura total do seu pai. A largura de cada modo de exibição secundário é controlada pelo `android:layout_weight` e `android:layout_width` atributos. Neste exemplo, cada subexibição ocupará 50% da largura fornecem pelo pai. Consulte [documento do Google no LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) para obter detalhes sobre _Layout peso_.

## <a name="2-changes-to-titlesfragment"></a>2. Alterações TitlesFragment

Quando o layout alternativo tiver sido criado, é necessário atualizar `TitlesFragment`. Quando o aplicativo está exibindo os dois fragmentos em uma atividade, em seguida, `TitlesFragment` deve carregar o `PlayQuoteFragment` na atividade pai. Caso contrário, `TitlesFragment` deve iniciar o `PlayQuoteActivity` host a `PlayQuoteFragment`. Um sinalizador booleano ajudará `TitlesFragment` determinar qual deve usar o comportamento. Este sinalizador será inicializado no `OnActivityCreated` método.

Primeiro, adicione uma variável de instância no topo do `TitlesFragment` classe:

```csharp
bool showingTwoFragments;
```

Em seguida, adicione o seguinte trecho de código para `OnActivityCreated` para inicializar a variável: 

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

Se o dispositivo estiver em execução no modo paisagem, em seguida, o `FrameLayout` com a ID de recurso `playquote_container` estará visível na tela, para `showingTwoFragments` serão inicializadas com `true`. Se o dispositivo está em execução no modo retrato, em seguida, `playquote_container` não estará na tela, portanto `showingTwoFragments` será `false`.

O `ShowPlayQuote` método precisará alterar como ele exibe uma cotação &ndash; em um fragmento ou iniciar uma nova atividade.  Atualização de `ShowPlayQuote` método para carregar um fragmento quando mostrando dois fragmentos, caso contrário, ele deve iniciar uma atividade:

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

Se o usuário selecionou uma opção diferente daquele que está sendo exibido atualmente no `PlayQuoteFragment`, em seguida, um novo `PlayQuoteFragment` é criado e será substituído pelo conteúdo do `playquote_container` dentro do contexto de um `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Código completo para TitlesFragment

Depois de concluir todas as alterações anteriores para `TitlesFragment`, a classe completa deve corresponder a este código:

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

## <a name="3-changes-to-playquoteactivity"></a>3. Alterações PlayQuoteActivity

Há um detalhe final para cuidar da: `PlayQuoteActivity` não é necessário quando o dispositivo estiver no modo paisagem. Se o dispositivo estiver no modo paisagem a `PlayQuoteActivity` não deve ser visível. Atualização de `OnCreate` método `PlayQuoteActivity` para que ele se fechará. Esse código é a versão final do `PlayQuoteActivity.OnCreate`:

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

Essa modificação adiciona uma verificação para a orientação do dispositivo. Se ele estiver no modo paisagem, em seguida, `PlayQuoteActivity` fechará a mesmo.

## <a name="4-run-the-application"></a>4. Executar o aplicativo

Depois que essas alterações forem concluídas, execute o aplicativo, gira o dispositivo para paisagem modo (se necessário) e, em seguida, selecione uma opção. As aspas devem ser exibidas na mesma tela como a lista de ações:

[![Aplicativo em execução em um telefone Android no modo paisagem](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Aplicativo em execução em um tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
