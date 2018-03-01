---
title: Passo a passo
ms.topic: article
ms.prod: xamarin
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 7441fbe1fc686dc4fa5cb67cbfc5ae6353f32c93
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough"></a>Passo a passo

Nas etapas a seguir, um aplicativo básico é criado com fragmentos. A primeira etapa é criar um novo xamarin para o projeto Android.

## <a name="1-create-a-project"></a>1. Criar um projeto

Criar um novo projeto de xamarin chamado **FragmentSample**. O **mínimo Android** versão deve ser definida para o Android 3.1 ou posterior, conforme mostrado na imagem a seguir:

[![Definindo a versão do Android mínimo](walkthrough-images/00.png)](walkthrough-images/00.png)


## <a name="2-create-the-mainactivity"></a>2. Criar o MainActivity

Em seguida, precisamos criar o `MainActivity` classe. Essa é a atividade de inicialização para o aplicativo. Essa atividade hospedará um ou ambos os fragmentos, dependendo do tamanho da tela. `MainActivity` fará isso ao carregar o arquivo de layout que é apropriado para o dispositivo.

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

> [!NOTE]
> `Note:` Classes de subtipo do fragmento não devem ter um padrão público construtor nenhum argumento.

## <a name="3-create-the-layout-files"></a>3. Criar os arquivos de Layout

Os dois tamanhos de tela diferentes exigem dois arquivos diferentes de layout. Portanto, vamos criar uma nova pasta, **recursos/Layout-grande**e criar um novo layout chamado **activity_main.axml**. Iremos também renomear o arquivo de layout padrão como **Resources/Layout/activity_main.axml**. Após essas alterações, as pastas de layout devem ser semelhante a captura de tela a seguir:

[![Captura de tela de pastas de layout no IDE](walkthrough-images/01.png)](walkthrough-images/01.png)


Todos os dispositivos serão carregar e usar o arquivo de layout no **recursos/Layout**.
É um layout muito simple que exibe apenas uma `TitlesFragment`:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_width="fill_parent"
           android:layout_height="fill_parent" />
</LinearLayout>
```

Para dispositivos que têm uma tela grande, Android carregará o arquivo de layout no **recursos/Layout-grande**. O conteúdo do layout para tablets é o seguinte:

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
       android:orientation="horizontal"
       android:layout_width="fill_parent"
       android:layout_height="fill_parent">
 <fragment class="com.xamarin.sample.fragments.supportlib.TitlesFragment"
           android:id="@+id/titles_fragment"
           android:layout_weight="1"
           android:layout_width="0px"
           android:layout_height="match_parent" />
 <FrameLayout android:id="@+id/details"
              android:layout_weight="1"
              android:layout_width="0px"
              android:layout_height="match_parent" />
</LinearLayout>
```

O arquivo de layout para as telas maior é ligeiramente diferente. Não é apenas o `TitlesFragment` exibido neste arquivo de layout, mas um `FrameLayout` é adicionado ao lado do fragmento. Nas telas maiores, o `DetailsFragment` programaticamente é adicionado ao `MainActivity` quando o usuário seleciona uma opção. Mais tarde em diante, vamos explicar mais detalhadamente como fazer isso.

3.2 Android introduziu uma nova maneira de especificar layouts de tela. Esses qualificadores novo especificam a quantidade de espaço que precisa de seu layout, em vez do tamanho da tela. Se este aplicativo foi deve ser executado somente no Android 3.2 ou superior, criaríamos uma **Layout/recurso-sw600dp** pasta (em vez da pasta **recurso/Layout-grande**) para o arquivo de layout  **activity_main.axml**. Este arquivo de recurso seria carregado por todos os dispositivos que têm uma largura mínima de tela de 600 pixels independentes de densidade. No entanto, como esse aplicativo é definido como destino Android 3.1 ou superior, ele usa o qualificador de recurso mais antigo.

## <a name="4-create-the-titlesfragment"></a>4. Criar o TitlesFragment

`TitlesFragment` será exibir os títulos das várias opções, então vamos adicionar um novo fragmento ao projeto chamado `TitlesFragment`:

[![Adicionando um novo fragmento ao projeto TitlesFragment](walkthrough-images/02.png)](walkthrough-images/02.png)

Depois de `TitlesFragment` foi adicionado, que deve alterar a classe para que ele herda de `Android.App.ListFragment`. `ListFragment` é um tipo especializado de fragmento que inclui a funcionalidade de lista.
`TitlesFragment` também substituirão `OnActivityCreated` (outro método de ciclo de vida de fragmento) e forneça um `Adapter` que `ListFragment` será usado para preencher a lista:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
   base.OnActivityCreated(savedInstanceState);
   var adapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemChecked, Shakespeare.Titles);
   ListAdapter = adapter;
   if (savedInstanceState != null)
   {
       _currentPlayId = savedInstanceState.GetInt("current_play_id", 0);
   }
   var detailsFrame = Activity.FindViewById<View>(Resource.Id.details);
   _isDualPane = detailsFrame != null && detailsFrame.Visibility == ViewStates.Visible;
   if (_isDualPane)
   {
       ListView.ChoiceMode = (int) ChoiceMode.Single;
       ShowDetails(_currentPlayId);
   }
}
```

Como mencionado anteriormente, o nosso aplicativo tem dois layouts para `MainActivity`. O código em `OnActivityCreated` detectar a presença do `FrameLayout` e determina qual arquivo de layout foi carregado. Se o `FrameLayout` existe no layout, o `_isDualPane` sinalizador é definido como `true`. O `_isDualPane` sinalizador é usado em outro lugar na atividade, especificamente, o `ShowDetails` método. O `ShowDetails` método será abordado em mais detalhes abaixo.

`TitlesFragment` é uma lista e deve responder às seleções do usuário na lista. Para fazer isso, `TitlesFragment` substituirá o método `OnListItemClick`. Dentro de `OnListItemClick`, um novo `DetailsFragment` será criado e exibido no `FrameLayout`por meio de programação. O código relevante dentro `TitlesFragment` é:

```csharp
public override void OnListItemClick(ListView l, View v, int position, long id)
{
   ShowDetails(position);
}
private void ShowDetails(int playId)
{
   _currentPlayId = playId;
   if (_isDualPane)
   {
       // We can display everything in place with fragments.
       // Have the list highlight this item and show the data.
       ListView.SetItemChecked(playId, true);
       // Check what fragment is shown, replace if needed.
       var details = FragmentManager.FindFragmentById(Resource.Id.details) as DetailsFragment;
       if (details == null || details.ShownPlayId != playId)
       {
           // Make new fragment to show this selection.
           details = DetailsFragment.NewInstance(playId);
           // Execute a transaction, replacing any existing
           // fragment with this one inside the frame.
           var ft = FragmentManager.BeginTransaction();
           ft.Replace(Resource.Id.details, details);
           ft.SetTransition(FragmentTransaction.TransitFragmentFade);
           ft.Commit();
       }
   }
   else
   {
       // Otherwise we need to launch a new Activity to display
       // the dialog fragment with selected text.
       var intent = new Intent();
       intent.SetClass(Activity, typeof (DetailsActivity));
       intent.PutExtra("current_play_id", playId);
       StartActivity(intent);
   }
}
```

O código determina do dispositivo como formatar e exibir a cotação da ação selecionada. No caso de tablets, o `_isDualPane` sinalizador será definido como `true`, e, portanto, as aspas serão exibidas ao lado de `TitlesFragment`. Se a ação selecionada `id` não estiver sendo exibida, em seguida, um novo `DetailsFragment` é criado e, em seguida, são carregados no `FrameLayout` na atividade. Para outros dispositivos que não possuem um monitor grande &ndash; telefones, por exemplo &ndash; `isDualPane` será definida como `false` para um novo `DetailsActivity` será iniciado.

<a name="5. Create_the_DetailsActivity" />

## <a name="5-create-the-detailsactivity"></a>5. Criar o DetailsActivity

O `DetailsActivity` exibe o `DetailsFragment` para dispositivos menores. Para ver isso, primeiro, adicionaremos uma nova atividade ao projeto nomeado `DetailsActivity`. `DetailsActivity` é uma atividade muito simple. Ele criará e, em seguida, hospedar um novo `DetailsFragment` de reprodução `id` que foi enviado:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Activity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("current_play_id", 0);

       var details = DetailsFragment.NewInstance(index); // DetailsFragment.NewInstance is a factory method to create a Details Fragment
       var fragmentTransaction = FragmentManager.BeginTransaction();
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

Observe que nenhum arquivo de layout é carregado para `DetailsActivity`. Em vez disso, `DetailsFragment` é carregado no modo de exibição de raiz da atividade. Este modo de exibição de raiz tem a ID especial `Android.Resource.Id.Content`. Um novo `DetailFragment` é criado e, em seguida, adicionado a esta exibição de raiz dentro de um `FragmentTransaction` que é criado pela atividade de `FragmentManager`.

<a name="6. Create_the_DetailsFragment" />

## <a name="6-create-the-detailsfragment"></a>6. Criar o DetailsFragment

Agora, vamos adicionar outro fragmento para o aplicativo chamado `DetailsFragment`. Este fragmento exibirá uma cotação da ação selecionada. O código a seguir mostra o completo `DetailsFragment`:

```csharp
internal class DetailsFragment : Fragment
{
   public static DetailsFragment NewInstance(int playId)
   {
       var detailsFrag = new DetailsFragment {Arguments = new Bundle()};
       detailsFrag.Arguments.PutInt("current_play_id", playId);
       return detailsFrag;
   }
   public int ShownPlayId
   {
       get { return Arguments.GetInt("current_play_id", 0); }
   }
   public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
   {
       if (container == null)
       {
           // Currently in a layout without a container, so no reason to create our view.
           return null;
       }
       var scroller = new ScrollView(Activity);
       var text = new TextView(Activity);
       var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
       text.SetPadding(padding, padding, padding, padding);
       text.TextSize = 24;
       text.Text = Shakespeare.Dialogue[ShownPlayId];
       scroller.AddView(text);
       return scroller;
   }
}
```

Para que `DetailsFragment` funcione corretamente, ele deve ter o índice da ação selecionada no `TitlesFragment`. Há muitas maneiras de fornecer esse valor para `DetailsFragment`; neste exemplo, a opção `Id` é colocado em um pacote e armazenado para a propriedade de argumentos de uma instância do pacote a `DetailsFragment`. A propriedade `ShownPlayId` é fornecido por questões de conveniência &ndash; será usado por instâncias de `DetailsFragment` para recuperar esse valor de pacote.

`OnCreateView` é chamado quando o fragmento deve desenhar sua interface de usuário e deve retornar um `Android.Views.View` objeto. Na maioria dos casos, isso é um `View` inflada de um arquivo existente do layout. No caso do exemplo acima, o fragmento programaticamente criará o modo de exibição que será usado para exibição.

Parabéns! Agora você criou um aplicativo que usa os fragmentos para simplificar o desenvolvimento em fatores forma.

No [próxima seção](supporting-pre-honeycomb.md), vamos estender esse aplicativo para que ele funcione em dispositivos pré-Android 3.0.

