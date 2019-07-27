---
title: Classes de fragmento especializadas
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 67fb12fd66d4d218f6e8bd40e21499e6c07e9e15
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510595"
---
# <a name="specialized-fragment-classes"></a>Classes de fragmento especializadas

A API de fragmentos fornece outras subclasses que encapsulam algumas das funcionalidades mais comuns encontradas em aplicativos. Essas subclasses são:

-   **ListFragment** &ndash; Esse fragmento é usado para exibir uma lista de itens associados a uma fonte de fontes, como uma matriz ou um cursor.

-   **DialogFragment** &ndash; Esse fragmento é usado como um wrapper em uma caixa de diálogo. O fragmento exibirá a caixa de diálogo sobre sua atividade.

-   **PreferenceFragment** &ndash; Esse fragmento é usado para mostrar objetos de preferência como listas.



## <a name="the-listfragment"></a>O ListFragment

O `ListFragment` é muito semelhante em conceito e funcionalidade para o `ListActivity`; é um wrapper que hospeda um `ListView` em um fragmento. A imagem abaixo mostra uma `ListFragment` execução em um Tablet e um telefone:

[![Capturas de tela de ListFragment em um Tablet e em um telefone](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>Ligando dados com o ListAdapter

A `ListFragment` classe já fornece um layout padrão, portanto, não é necessário substituir `OnCreateView` para `ListFragment`exibir o conteúdo do. O `ListView` é associado a dados usando uma `ListAdapter` implementação. O exemplo a seguir mostra como isso pode ser feito usando uma matriz simples de cadeias de caracteres:

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

Ao definir o `ListAdapter`, é importante usar a `ListFragment.ListAdapter` Propriedade, e não a `ListView.ListAdapter` propriedade. Usar `ListView.ListAdapter` fará com que um código de inicialização importante seja ignorado.



### <a name="responding-to-user-selection"></a>Respondendo à seleção de usuário

Para responder às seleções do usuário, um aplicativo deve substituir o `OnListItemClick` método. O exemplo a seguir mostra uma dessas possibilidades:

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

No código acima, quando o usuário seleciona um item no `ListFragment`, um novo fragmento é exibido na atividade de hospedagem, mostrando mais detalhes sobre o item que foi selecionado.



## <a name="dialogfragment"></a>DialogFragment

O *DialogFragment* é um fragmento usado para exibir um objeto de caixa de diálogo dentro de um fragmento que flutuará na parte superior da janela da atividade. Destina-se a substituir as APIs de caixa de diálogo gerenciadas (a partir do Android 3,0). A captura de tela a seguir mostra um `DialogFragment`exemplo de:

[![Captura de tela do DialogFragment exibindo adicionar novo veículo](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Um `DialogFragment` garante que o estado entre o fragmento e a caixa de diálogo permaneça consistente. Todas as interações e o controle do objeto da caixa de `DialogFragment` diálogo devem ocorrer por meio da API e não são feitos com chamadas diretas no objeto da caixa de diálogo. A `DialogFragment` API fornece cada instância com um `Show()` método que é usado para exibir um fragmento. Há duas maneiras de se livrar de um fragmento:

-  `DialogFragment.Dismiss()` Chame`DialogFragment` na instância. 

-  Exibir outro `DialogFragment`.

Para criar um `DialogFragment`, uma classe herda de `Android.App.DialogFragment,` e, em seguida, substitui um dos dois métodos a seguir:

- **OnCreateView** &ndash; Isso cria e retorna uma exibição.

- **OnCreateDialog** &ndash; Isso cria uma caixa de diálogo personalizada. Normalmente, ele é usado para mostrar um *AlertDialog*. Ao substituir esse método, não é necessário substituir `OnCreateView` .



### <a name="a-simple-dialogfragment"></a>Um DialogFragment simples

A captura de tela a seguir `DialogFragment` mostra uma simples `TextView` que tem `Button`um e dois s:

[![Exemplo de DialogFragment com um TextView e dois botões](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

O `TextView` exibirá o número de vezes que o usuário clicou `DialogFragment`em um botão no, enquanto clicar no botão outro fechará o fragmento. O código para `DialogFragment` é:

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

Como todos os fragmentos, `DialogFragment` um é exibido no contexto de um `FragmentTransaction`.

O `Show()` método em um `DialogFragment` usa um `FragmentTransaction` e um `string` como uma entrada. A caixa de diálogo será adicionada à atividade e `FragmentTransaction` confirmada.

O código a seguir demonstra uma possível maneira como uma atividade pode `Show()` usar o método para `DialogFragment`mostrar um:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>Ignorando um fragmento

Chamar `Dismiss()` em uma instância de a `DialogFragment` faz com que um fragmento seja removido da atividade e confirma essa transação.
Os métodos de ciclo de vida padrão do fragmento envolvidos na destruição de um fragmento serão chamados.


### <a name="alert-dialog"></a>Caixa de diálogo alerta

Em vez de `OnCreateView`substituir, `DialogFragment` um pode substituir `OnCreateDialog`. Isso permite que um aplicativo crie um [AlertDialog](xref:Android.App.AlertDialog) que é gerenciado por um fragmento. O código a seguir é um exemplo que usa `AlertDialog.Builder` o para criar `Dialog`um:

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



## <a name="preferencefragment"></a>PreferenceFragment

Para ajudar a gerenciar as preferências, a API de `PreferenceFragment` fragmentos fornece a subclasse. O `PreferenceFragment` é semelhante ao [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; , ele mostrará uma hierarquia de preferências para o usuário em um fragmento. À medida que o usuário interage com as preferências, elas serão salvas automaticamente em [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
Em aplicativos Android 3,0 ou superior, use o `PreferenceFragment` para lidar com as preferências em aplicativos. A imagem a seguir mostra um exemplo de `PreferenceFragment`:

[![Exemplo de PreferencesFragment com as preferências embutidas, de diálogo e de inicialização](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>Criar um fragmento de preferência a partir de um recurso

O fragmento de preferência pode ser alterado de um arquivo de recurso XML usando o método [PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) . Um local lógico para chamar esse método no ciclo de vida do fragmento estaria no `OnCreate` método.

A `PreferenceFragment` figura acima foi criada carregando um recurso do XML. O arquivo de recurso é:

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

O código para o fragmento de preferência é o seguinte:

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

Outra técnica para criar um `PreferenceFragment` envolve a consulta de atividades. Cada atividade pode usar o atributo de [preferência de chave\_\_de metadados](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) que apontará para um arquivo de recurso XML. No Xamarin. Android, isso é feito com a adornação de uma atividade `MetaDataAttribute`com o e a especificação do arquivo de recurso a ser usado. A `PreferenceFragment` classe fornece o método [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) que pode ser usado para consultar uma atividade para localizar esse recurso XML e inflar uma hierarquia de preferência para ele.

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

O Android examinará a classe `MyActivityWithPreference`. A classe deve ser adornada com o `MetaDataAttribute,` conforme mostrado no seguinte trecho de código:

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

O `MetaDataAttribute` declara um arquivo de recurso XML que o `PreferenceFragment` usará para aumentar a hierarquia de preferência. Se o `MetatDataAttribute` não for fornecido, uma exceção será lançada em tempo de execução. Quando esse código é executado, `PreferenceFragment` o aparece como na seguinte captura de tela:

[![Captura de tela do aplicativo de exemplo com PreferenceFragment exibido](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
