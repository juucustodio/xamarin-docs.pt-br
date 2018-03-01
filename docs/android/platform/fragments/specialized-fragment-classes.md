---
title: Classes de fragmento especializado
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: e7b4349ee2664a94ef6dff3c6a58d5f8f97682a1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="specialized-fragment-classes"></a>Classes de fragmento especializado

A API de fragmentos fornece outros subclasses que encapsulam algumas das funcionalidades mais comuns encontradas em aplicativos. Dessas subclasses são:

-   **ListFragment** &ndash; este fragmento é usado para exibir uma lista de itens associados a uma fonte de dados como uma matriz ou um cursor.

-   **DialogFragment** &ndash; este fragmento é usado como um wrapper em torno de uma caixa de diálogo. O fragmento exibirá a caixa de diálogo sobre sua atividade.

-   **PreferenceFragment** &ndash; este fragmento é usado para mostrar objetos de preferência como listas.


<a name="The_ListFragment" />

## <a name="the-listfragment"></a>O ListFragment

O `ListFragment` é muito semelhante ao conceito e a funcionalidade para o `ListActivity`; é um wrapper que hospeda um `ListView` em um fragmento. A imagem abaixo mostra um `ListFragment` em execução em um tablet e um telefone:

[![Capturas de tela de ListFragment em um tablet e em um telefone](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png)

<a name="Binding_Data_With_The_ListAdapter" />

### <a name="binding-data-with-the-listadapter"></a>Associação de dados com o ListAdapter

O `ListFragment` classe já fornece um layout padrão, portanto, não é necessário substituir `OnCreateView` para exibir o conteúdo do `ListFragment`. O `ListView` está associada aos dados usando um `ListAdapter` implementação. O exemplo a seguir mostra como isso pode ser feito usando uma matriz de cadeias de caracteres:

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

Ao definir o `ListAdapter`, é importante usar o `ListFragment.ListAdapter` propriedade e não o `ListView.ListAdapter` propriedade. Usando `ListView.ListAdapter` fará com que o código de inicialização importante deve ser ignorada.

<a name="Responding_to_User_Selection" />


### <a name="responding-to-user-selection"></a>Respondendo a seleção do usuário

Para responder às seleções do usuário, um aplicativo deve substituir o `OnListItemClick` método. O exemplo a seguir mostra uma possibilidade de tal:

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

No código acima, quando o usuário seleciona um item no `ListFragment`, um novo fragmento é exibido na atividade de hospedagem, mostrando mais detalhes sobre o item selecionado.

<a name="DialogFragment" />


## <a name="dialogfragment"></a>DialogFragment

O *DialogFragment* é um fragmento que é usado para exibir um objeto de caixa de diálogo dentro de um fragmento que flutua na parte superior da janela da atividade. É destinada a substituir o diálogo gerenciado APIs (começando no Android 3.0). Captura de tela a seguir mostra um exemplo de uma `DialogFragment`:

[![Captura de tela de DialogFragment exibindo adicionar novo veículo EditBox](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png)

Um `DialogFragment` garante que o estado entre o fragmento e a caixa de diálogo permaneça consistente. Todas as interações e controle do objeto de caixa de diálogo devem acontecer por meio de `DialogFragment` API e não com chamadas diretas no objeto de caixa de diálogo. O `DialogFragment` API fornece cada instância com um `Show()` método que é usado para exibir um fragmento. Há duas maneiras para eliminar um fragmento:

-  Chamar `DialogFragment.Dismiss()` no `DialogFragment` instância. 

-  Exibir outro `DialogFragment`.

Para criar um `DialogFragment`, uma classe herda de `Android.App.DialogFragment,` e, em seguida, substitui um dos dois métodos a seguir:

- **OnCreateView** &ndash; isso cria e retorna uma exibição.

- **OnCreateDialog** &ndash; isso cria uma caixa de diálogo personalizada. Ela é normalmente usada para mostrar um *AlertDialog*. Ao substituir esse método, não é necessário substituir `OnCreateView` .


<a name="A_Simple_DialogFragment" />

### <a name="a-simple-dialogfragment"></a>Um Simple DialogFragment

Captura de tela a seguir mostra um simples `DialogFragment` que tem um `TextView` e dois `Button`s:

[![DialogFragment de exemplo com um TextView e dois botões](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png)

O `TextView` exibirá o número de vezes que o usuário clicou em um botão no `DialogFragment`, ao clicar no botão outros fechará o fragmento. O código para `DialogFragment` é:

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

<a name="Displaying_a_Fragment" />

### <a name="displaying-a-fragment"></a>Exibindo um fragmento

Como todos os fragmentos, um `DialogFragment` é exibido no contexto de um `FragmentTransaction`.

O `Show()` método em um `DialogFragment` leva um `FragmentTransaction` e um `string` como entrada. A caixa de diálogo será adicionada à atividade e o `FragmentTransaction` confirmada.

O código a seguir demonstra uma maneira possível de uma atividade pode usar o `Show()` método para mostrar um `DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

<a name="Dismissing_a_Fragment" />

### <a name="dismissing-a-fragment"></a>Ignorar um fragmento

Chamando `Dismiss()` em uma instância de um `DialogFragment` faz com que um fragmento a ser removido da atividade e confirma a transação.
Os métodos de ciclo de vida padrão do fragmento que estão envolvidos com a destruição de um fragmento serão chamados.

<a name="Alert_Dialog" />

### <a name="alert-dialog"></a>Caixa de diálogo alerta

Em vez de substituir `OnCreateView`, um `DialogFragment` em vez disso, pode substituir `OnCreateDialog`. Isso permite que um aplicativo criar um [AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/) que é gerenciado por um fragmento. O código a seguir está um exemplo que usa o `AlertDialog.Builder` para criar um `Dialog`:

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

 <a name="PreferenceFragment" />


## <a name="preferencefragment"></a>PreferenceFragment

Para ajudar a gerenciar Preferências, fornece a API de fragmentos de `PreferenceFragment` subclasse. O `PreferenceFragment` é semelhante de [PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/
) &ndash; mostrará uma hierarquia de preferências para o usuário em um fragmento. Como o usuário interage com as preferências, eles serão automaticamente salvas [SharedPreferences](http://developer.android.com/reference/android/content/SharedPreferences.html).
No Android 3.0 ou mais aplicativos, use o `PreferenceFragment` para lidar com as preferências em aplicativos. A figura a seguir mostra um exemplo de uma `PreferenceFragment`:

[![Exemplo PreferencesFragment com embutido, caixa de diálogo e preferências de inicialização](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png)

<a name="Create_A_Preference_Fragment_from_a_Resource" />

### <a name="create-a-preference-fragment-from-a-resource"></a>Criar um fragmento de preferência de um recurso

A preferência de fragmento pode ser aumentado de um arquivo de recurso XML usando o [PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/) método. Um local lógico para chamar esse método no ciclo de vida do fragmento estaria no `OnCreate` método.

O `PreferenceFragment` mostrada acima foi criada por um recurso no carregamento do XML. O arquivo de recurso é:

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

O código para a preferência de fragmento é da seguinte maneira:

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

 <a name="Querying_Activities_to_Create_a_Preference_Fragment" />


### <a name="querying-activities-to-create-a-preference-fragment"></a>Atividades de consulta para criar um fragmento de preferência

Outra técnica para a criação de um `PreferenceFragment` envolver consulta de atividades. Cada atividade pode usar o [METADADOS\_chave\_preferência](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/) atributo apontará para um arquivo de recurso XML. Xamarin, isso é feito pelo adorno de uma atividade com o `MetaDataAttribute`e, em seguida, especificando o arquivo de recurso a ser usado. O `PreferenceFragment` classe fornece o método [AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/)) que pode ser usado para consultar uma atividade para localizar o recurso XML e aumentar a uma hierarquia de preferência para ele.

Um exemplo desse processo é fornecido no trecho de código a seguir, que usa `AddPreferencesFromIntent` para criar um `PreferenceFragment`:

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

Android examinará a classe `MyActivityWithPreference`. A classe deve ser adornada com o `MetaDataAttribute,` conforme mostrado no trecho de código a seguir:

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

O `MetaDataAttribute` declara um arquivo de recurso XML que o `PreferenceFragment` será usado para aumentar a hierarquia de preferência. Se o `MetatDataAttribute` não for fornecido, em seguida, uma exceção será lançada em tempo de execução. Quando esse código é executado, o `PreferenceFragment` aparece como a captura de tela a seguir:

[![Captura de tela do aplicativo de exemplo com PreferenceFragment exibido](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)
