---
title: Classes de fragmento especializadas
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027308"
---
# <a name="specialized-fragment-classes"></a>Classes de fragmento especializadas

A API Fragmentos fornece outras subclasses que encapsulam algumas das funcionalidades mais comuns encontradas em aplicativos. Estas subclasses são:

- **Fragmento de** &ndash; lista Este fragmento é usado para exibir uma lista de itens vinculados a uma fonte de dados, como um array ou um cursor.

- **DiálogoFragmento** &ndash; Este Fragmento é usado como um invólucro em torno de uma caixa de diálogo. O Fragmento exibirá a caixa de diálogo no topo de sua atividade.

- **PreferenceFragment** &ndash; Este fragmento é usado para mostrar objetos de preferência como listas.

## <a name="the-listfragment"></a>O Fragmento de Lista

O `ListFragment` é muito semelhante em conceito `ListActivity`e funcionalidade ao; é um invólucro `ListView` que hospeda um em um Fragmento. A imagem abaixo `ListFragment` mostra uma execução em um tablet e um telefone:

[![Capturas de tela de ListFragment em um tablet e em um telefone](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>Dados de vinculação com o adaptador de lista

A `ListFragment` classe já fornece um layout padrão, portanto `OnCreateView` não é necessário `ListFragment`substituir para exibir o conteúdo do . O `ListView` é obrigado a `ListAdapter` dados usando uma implementação. O exemplo a seguir mostra como isso pode ser feito usando uma matriz simples de strings:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

Ao definir `ListAdapter`o , é `ListFragment.ListAdapter` importante usar o `ListView.ListAdapter` imóvel, e não o imóvel. O `ListView.ListAdapter` uso fará com que um código de inicialização importante seja ignorado.

### <a name="responding-to-user-selection"></a>Respondendo à seleção do usuário

Para responder às seleções de usuários, um aplicativo deve substituir o `OnListItemClick` método. O exemplo a seguir mostra uma dessas possibilidades:

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

No código acima, quando o usuário seleciona `ListFragment`um item no , um novo Fragmento é exibido na atividade de hospedagem, mostrando mais detalhes sobre o item selecionado.

## <a name="dialogfragment"></a>DialogFragment

O *'''''''''''''''''''''''''''''* Activity'. Destina-se a substituir as APIs de diálogo gerenciadas (a partir do Android 3.0). A captura de tela `DialogFragment`a seguir mostra um exemplo de:

[![Captura de tela do DiálogoFragmentado exibindo adicionar nova caixa de edição do veículo](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

A `DialogFragment` garante que o estado entre o Fragmento e o diálogo permaneça consistente. Todas as interações e o controle `DialogFragment` do objeto de diálogo devem acontecer através da API, e não devem ser feitas com chamadas diretas no objeto de diálogo. A `DialogFragment` API fornece cada `Show()` instância com um método que é usado para exibir um Fragmento. Há duas maneiras de se livrar de um Fragmento:

- Ligue `DialogFragment.Dismiss()` para `DialogFragment` o caso. 

- Exibir `DialogFragment`outro .

Para criar `DialogFragment`um , uma `Android.App.DialogFragment,` classe herda e, em seguida, substitui um dos dois métodos a seguir:

- **OnCreateView** &ndash; Isso cria e retorna uma exibição.

- **OnCreateDialog** &ndash; Isso cria uma caixa de diálogo personalizada. É normalmente usado para mostrar um *AlertDialog*. Ao substituir este método, não é `OnCreateView` necessário substituir .

### <a name="a-simple-dialogfragment"></a>Um simples fragmento de diálogo

A captura de `DialogFragment` tela a `TextView` seguir `Button`mostra um simples que tem a e dois s:

[![Exemplo De DiálogoFragmento com uma TextView e dois botões](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

O `TextView` exibirá o número de vezes que o `DialogFragment`usuário clicou em um botão no , enquanto clicar no outro botão fechará o Fragmento. O código `DialogFragment` para é:

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```

### <a name="displaying-a-fragment"></a>Exibindo um fragmento

Como todos os `DialogFragment` Fragmentos, a é `FragmentTransaction`exibido no contexto de um .

O `Show()` método `DialogFragment` em `FragmentTransaction` a `string` leva a e a como uma entrada. O diálogo será adicionado à Atividade `FragmentTransaction` e ao comprometido.

O código a seguir demonstra uma `Show()` maneira possível `DialogFragment`de uma Atividade usar o método para mostrar:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>Dispensando um Fragmento

Chamar `Dismiss()` uma instância `DialogFragment` de um faz com que um Fragmento seja removido da Atividade e comprometa essa transação.
Os métodos padrão do ciclo de vida do Fragmento que estão envolvidos com a destruição de um Fragmento serão chamados.

### <a name="alert-dialog"></a>Diálogo de alerta

Em vez `OnCreateView`de `DialogFragment` substituir , `OnCreateDialog`uma pode, em vez disso, substituir . Isso permite que um aplicativo crie um [AlertDialog](xref:Android.App.AlertDialog) gerenciado por um Fragmento. O código a seguir é `AlertDialog.Builder` um `Dialog`exemplo que usa o para criar um:

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```

## <a name="preferencefragment"></a>Fragmento de preferência

Para ajudar a gerenciar preferências, a `PreferenceFragment` API Fragments fornece a subclasse. O `PreferenceFragment` é semelhante ao [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; que mostrará uma hierarquia de preferências ao usuário em um Fragmento. À medida que o usuário interage com as preferências, eles serão automaticamente salvos [em Preferências Compartilhadas](https://developer.android.com/reference/android/content/SharedPreferences.html).
Em aplicativos Android 3.0 ou `PreferenceFragment` superior, use o para lidar com preferências em aplicativos. A imagem a seguir `PreferenceFragment`mostra um exemplo de:

[![Preferências de exemploFragmente com preferências inline, de diálogo e de lançamento](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>Criar um fragmento de preferência a partir de um recurso

O fragmento de preferência pode ser inflado de um arquivo de recurso XML usando o método [PreferenceFragment.AddPreferencesFromResource.](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) Um lugar lógico para chamar esse método no ciclo `OnCreate` de vida do Fragmento estaria no método.

A `PreferenceFragment` foto acima foi criada carregando um recurso do XML. O arquivo de recursos é:

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

O código para o Fragmento de preferência é o seguinte:

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```

### <a name="querying-activities-to-create-a-preference-fragment"></a>Consultando atividades para criar um fragmento de preferência

Outra técnica para `PreferenceFragment` criar um envolve consultar atividades. Cada atividade pode usar o atributo [METADATA\_KEY\_PREFERENCE](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) que apontará para um arquivo de recurso XML. No Xamarin.Android, isso é feito adornando uma atividade com o `MetaDataAttribute`, e, em seguida, especificando o arquivo de recurso para usar. A `PreferenceFragment` classe fornece o método [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) que pode ser usado para consultar uma atividade para encontrar esse recurso XML e inflar uma hierarquia de preferência para ele.

Um exemplo desse processo é fornecido no seguinte trecho `AddPreferencesFromIntent` de código, que usa para criar um `PreferenceFragment`:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android vai olhar `MyActivityWithPreference`para a classe . A classe deve ser `MetaDataAttribute,` adornada com o que for mostrado no seguinte trecho de código:

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

O `MetaDataAttribute` declara um arquivo de `PreferenceFragment` recurso XML que o usará para inflar a hierarquia de preferências. Se `MetatDataAttribute` o não for fornecido, então uma exceção será lançada no tempo de execução. Quando este código `PreferenceFragment` é executado, o é reaparece como na captura de tela a seguir:

[![Captura de tela do aplicativo exemplo com PreferenceFragment exibido](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
