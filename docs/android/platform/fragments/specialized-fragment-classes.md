---
title: Classes de fragmento especializadas
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 75d95d630415cdaa4c0c1ed3b8ddebb32b8e3c4d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947967"
---
# <a name="specialized-fragment-classes"></a>Classes de fragmento especializadas

A API de fragmentos fornece outras subclasses que encapsulam algumas das funcionalidades mais comuns encontradas em aplicativos. Essas subclasses são:

-   **ListFragment** &ndash; este fragmento é usado para exibir uma lista de itens associados a uma fonte de dados como uma matriz ou um cursor.

-   **DialogFragment** &ndash; este fragmento é usado como um wrapper em torno de uma caixa de diálogo. O fragmento exibirá a caixa de diálogo sobre sua atividade.

-   **PreferenceFragment** &ndash; este fragmento é usado para mostrar objetos de preferência como listas.



## <a name="the-listfragment"></a>O ListFragment

O `ListFragment` é muito semelhante em conceito e a funcionalidade para o `ListActivity`; é um wrapper que hospeda um `ListView` em um fragmento. A imagem abaixo mostra um `ListFragment` em execução em um tablet e um telefone:

[![Capturas de tela de ListFragment em um tablet e em um telefone](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>Associação de dados com o ListAdapter

O `ListFragment` classe já fornece um layout padrão, portanto, não é necessário substituir `OnCreateView` para exibir o conteúdo do `ListFragment`. O `ListView` está associado a dados usando um `ListAdapter` implementação. O exemplo a seguir mostra como isso pode ser feito usando uma matriz de cadeias de caracteres:

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

Ao definir a `ListAdapter`, é importante usar o `ListFragment.ListAdapter` propriedade e não o `ListView.ListAdapter` propriedade. Usando `ListView.ListAdapter` fará com que o código de inicialização importante deve ser ignorada.



### <a name="responding-to-user-selection"></a>Respondendo a seleção do usuário

Para responder às seleções do usuário, um aplicativo deve substituir o `OnListItemClick` método. O exemplo a seguir mostra uma tal possibilidade:

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

O *DialogFragment* é um fragmento que é usado para exibir um objeto de caixa de diálogo dentro de um fragmento que flutua sobre a janela de atividade. Ele é usado para substituir a caixa de diálogo gerenciada APIs (começando em Android 3.0). Captura de tela a seguir mostra um exemplo de um `DialogFragment`:

[![Captura de tela de DialogFragment exibindo adicionar novo veículo EditBox](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Um `DialogFragment` garante que o estado entre o fragmento e a caixa de diálogo permaneça consistente. Todas as interações e controle do objeto de caixa de diálogo devem ocorrer por meio de `DialogFragment` API e não ser feito com chamadas diretas no objeto de caixa de diálogo. O `DialogFragment` API fornece a cada instância com um `Show()` método que é usado para exibir um fragmento. Há duas maneiras de se livrar-se de um fragmento:

-  Chame `DialogFragment.Dismiss()` sobre o `DialogFragment` instância. 

-  Exibir outro `DialogFragment`.

Para criar uma `DialogFragment`, uma classe herda de `Android.App.DialogFragment,` e, em seguida, substitui um dos dois métodos a seguir:

- **OnCreateView** &ndash; isso cria e retorna uma exibição.

- **OnCreateDialog** &ndash; isso cria uma caixa de diálogo personalizada. Ele é normalmente usado para mostrar uma *AlertDialog*. Ao substituir esse método, não é necessário substituir `OnCreateView` .



### <a name="a-simple-dialogfragment"></a>Um Simple DialogFragment

Captura de tela a seguir mostra um simples `DialogFragment` que tem um `TextView` e duas `Button`s:

[![DialogFragment de exemplo com um TextView e dois botões](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

O `TextView` exibirá o número de vezes que o usuário tenha clicado em um botão no `DialogFragment`, enquanto clicar no botão outro fechará o fragmento. O código para `DialogFragment` é:

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


### <a name="dismissing-a-fragment"></a>Ignorando um fragmento

Chamando `Dismiss()` em uma instância de um `DialogFragment` faz com que um fragmento a ser removido da atividade e confirma a transação.
Os métodos de ciclo de vida padrão do fragmento que estão envolvidos com a destruição de um fragmento serão chamados.


### <a name="alert-dialog"></a>Caixa de diálogo alerta

Em vez de substituir `OnCreateView`, um `DialogFragment` em vez disso, podem substituir `OnCreateDialog`. Isso permite que um aplicativo criar uma [AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/) que é gerenciado por um fragmento. O código a seguir está um exemplo que usa o `AlertDialog.Builder` para criar um `Dialog`:

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

Para ajudar a gerenciar Preferências, fornece a API de fragmentos a `PreferenceFragment` subclasse. O `PreferenceFragment` é semelhante de [PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/) &ndash; mostrará uma hierarquia de preferências para o usuário em um fragmento. Conforme o usuário interage com as preferências, eles serão salvos automaticamente no [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
No Android 3.0 ou superiores aplicativos, use o `PreferenceFragment` para lidar com as preferências em aplicativos. A imagem a seguir mostra um exemplo de um `PreferenceFragment`:

[![Exemplo PreferencesFragment com inline, caixa de diálogo e as preferências de inicialização](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>Criar um fragmento de preferência de um recurso

A preferência de fragmento pode ser aumentado de um arquivo de recurso XML usando o [PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/) método. Seria um momento lógico para chamar esse método no ciclo de vida do fragmento no `OnCreate` método.

O `PreferenceFragment` mostrada acima foi criado por carregar um recurso do XML. O arquivo de recurso é:

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



### <a name="querying-activities-to-create-a-preference-fragment"></a>Consultar atividades para criar um fragmento de preferência

Outra técnica para criar um `PreferenceFragment` envolver as atividades de consulta. Cada atividade pode usar o [METADADOS\_chave\_preferência](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/) atributo apontará para um arquivo de recurso XML. No xamarin. Android, isso é feito pelo adorno de uma atividade com o `MetaDataAttribute`e, em seguida, especificando o arquivo de recurso a ser usado. O `PreferenceFragment` classe fornece o método [AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/)) que pode ser usado para consultar uma atividade para localizar esse recurso XML e inflação de uma hierarquia de preferência para ele.

Um exemplo desse processo é fornecido no seguinte trecho de código, que usa `AddPreferencesFromIntent` para criar um `PreferenceFragment`:

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

O `MetaDataAttribute` declara um arquivo de recurso XML que o `PreferenceFragment` usará para aumentar a hierarquia de preferência. Se o `MetatDataAttribute` não for fornecido, em seguida, uma exceção será lançada em tempo de execução. Quando esse código é executado, o `PreferenceFragment` aparece como na seguinte captura de tela:

[![Captura de tela do aplicativo de exemplo com PreferenceFragment exibido](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
