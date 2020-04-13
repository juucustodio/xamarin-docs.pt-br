---
title: Xamarin.Android Fragmentos Passo a Passo - Parte 1
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 043ad02f9ca9148910364ac82917551ee58d72ba
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027399"
---
# <a name="fragments-walkthrough-ndash-phone"></a>Fragmentos passo &ndash; a passo telefone

Esta é a primeira parte de um passo a passo que criará um aplicativo Xamarin.Android que tem como alvo um dispositivo Android em orientação retrato. Este passo a passo discutirá como criar fragmentos no Xamarin.Android e como adicioná-los a uma amostra.

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

As seguintes classes serão criadas para este aplicativo:

1. `PlayQuoteFragment`&nbsp; Este fragmento mostrará uma citação de uma peça de William Shakespeare. Será hospedado por `PlayQuoteActivity`.
1. `Shakespeare`&nbsp; Esta classe terá duas matrizes codificadas como propriedades.
1. `TitlesFragment`&nbsp; Este fragmento exibirá uma lista de títulos de peças que foram escritas por William Shakespeare. Será hospedado por `MainActivity`.
1. `PlayQuoteActivity`iniciará `PlayQuoteActivity` a resposta ao usuário selecionando uma reprodução em `TitlesFragment`. &nbsp; `TitlesFragment`

## <a name="1-create-the-android-project"></a>1. Crie o projeto Android

Crie um novo projeto Xamarin.Android chamado **FragmentSample**.
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Criar um novo projeto Xamarin.Android](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Criando um novo projeto Xamarin.Android](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

Recomenda-se selecionar **Desenvolvimento Moderno** para este passo a passo.

Depois de criar o projeto, renomeie o layout do **arquivo/Main.axml** para **layout/activity_main.axml**.

-----

## <a name="2-add-the-data"></a>2. Adicione os dados

Os dados deste aplicativo serão armazenados em dois arrays de string `Shakespeare`codificados com código rígido que são propriedades de um nome de classe:

* `Shakespeare.Titles`&nbsp; Esta matriz terá uma lista de peças de William Shakespeare. Esta é a fonte `TitlesFragment`de dados para o .
* `Shakespeare.Dialogue`&nbsp; Esta matriz terá uma lista de citações de `Shakespeare.Titles`uma das jogadas contidas em . Esta é a fonte `PlayQuoteFragment`de dados para o .

Adicione uma nova classe C# ao projeto **FragmentSample** e nomeie-o **Shakespeare.cs**. Dentro deste arquivo, crie uma `Shakespeare` nova classe C# chamada com o seguinte conteúdo

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3. Crie o PlayQuoteFragment

O `PlayQuoteFragment` é um fragmento do Android que exibirá uma citação para uma peça de Shakespeare que foi selecionada pelo usuário anteriormente no aplicativo, este fragmento não usará um arquivo de layout do Android; em vez disso, criará dinamicamente sua interface de usuário. Adicionar uma `Fragment` nova `PlayQuoteFragment` classe nomeada ao projeto:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Adicione uma nova classe C#](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Adicione uma nova classe C#](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

Em seguida, altere o código para que o fragmento se assemelhe a este trecho:

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

É um padrão comum em aplicativos Android para fornecer um método de fábrica que irá instanciar um fragmento. Isso garante que o fragmento seja criado com os parâmetros necessários para o bom funcionamento. Neste passo a passo, espera-se `PlayQuoteFragment.NewInstance` que o aplicativo use o método para criar um novo fragmento cada vez que uma citação é selecionada. O `NewInstance` método levará um &ndash; único parâmetro para exibir o índice da cotação.

O `OnCreateView` método será invocado pelo Android na hora de renderizar o fragmento na tela. Ele retornará `View` um objeto Android que é o fragmento. Este fragmento não usa um arquivo de layout para criar uma exibição. Em vez disso, ele criará programáticamente a exibição instanciando um **TextView** para segurar a cotação e exibirá esse widget em um **ScrollView**.

> [!NOTE]
> As subclasses de fragmentos devem ter um construtor padrão público que não tenha parâmetros.

## <a name="4-create-the-playquoteactivity"></a>4. Crie o PlayQuoteActivity

Os fragmentos devem ser hospedados dentro de uma atividade, `PlayQuoteFragment`de modo que este aplicativo requer uma atividade que hospedará o . A Atividade adicionará dinamicamente o fragmento ao seu layout em tempo de execução. Adicione uma nova atividade ao `PlayQuoteActivity`aplicativo e nomeie-o:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Adicionar atividade android ao projeto](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Adicionar atividade android ao projeto](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

Editar o `PlayQuoteActivity`código em:

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

Quando `PlayQuoteActivity` for criado, ele instanciará um novo `PlayQuoteFragment` e carregará esse `FragmentTransaction`fragmento em sua visão raiz no contexto de um . Observe que essa atividade não carrega um arquivo de layout do Android para sua interface de usuário. Em vez `PlayQuoteFragment` disso, um novo é adicionado à visão raiz do aplicativo. O identificador `Android.Resource.Id.Content` de recursos é usado para se referir à exibição raiz de uma Atividade sem conhecer seu identificador específico.

## <a name="5-create-titlesfragment"></a>5. Criar títulosFragment

O `TitlesFragment` testamento subclasse um fragmento `ListFragment` especializado conhecido como um que `ListView` encapsula a lógica para exibir um em um fragmento. A `ListFragment` expõe `ListAdapter` uma propriedade (usada pelo responsável pela exibição de `ListView` seu conteúdo) e um manipulador de eventos nomeado `OnListItemClick` que permite que o fragmento responda a cliques em uma linha exibida pelo `ListView`.

Para começar, adicione um novo fragmento ao projeto e nomeie-o **TitlesFragment**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Adicionar fragmento de Android ao projeto](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Adicionar fragmento de Android ao projeto](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

Editar o código dentro do fragmento:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
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
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Quando a Atividade for criada, `OnActivityCreated` o Android invocará o método do fragmento; é aqui que o adaptador de lista para o `ListView` é criado.  O `ShowQuoteFromPlay` método iniciará uma `PlayQuoteActivity` instância do para exibir a citação para a reprodução selecionada.

## <a name="display-titlesfragment-in-mainactivity"></a>Exibir títulosFragment em MainActivity

O passo final `TitlesFragment` é `MainActivity`exibir dentro . A Atividade não carrega dinamicamente o fragmento. Em vez disso, o fragmento será carregado estáticamente declarando-o `fragment` no arquivo de layout da atividade usando um elemento. O fragmento a carregar é `android:name` identificado definindo o atributo para a classe do fragmento (incluindo o namespace do tipo). Por exemplo, para `TitlesFragment`usar `android:name` o , `FragmentSample.TitlesFragment`então seria definido como .

Edite o arquivo de layout **activity_main.axml**, substituindo o XML existente pelo seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> O `class` atributo é `android:name`um substituto válido para . Não há orientação formal sobre qual forma é preferível, existem `class` muitos `android:name`exemplos de bases de código que serão utilizados intercambiavelmente com .

Não são necessárias alterações de código para o MainActivity. O código nessa classe deve ser muito semelhante a este trecho:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>Executar o aplicativo

Agora que o código está completo, execute o aplicativo em um dispositivo para vê-lo em ação.

[![Capturas de tela do aplicativo em execução em um telefone.](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[A parte 2 deste passo a passo](./walkthrough-landscape.md) otimizará este aplicativo para dispositivos em execução no modo paisagem.
