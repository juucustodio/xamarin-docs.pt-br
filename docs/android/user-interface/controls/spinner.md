---
title: Controle giratório do Xamarin. Android
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2c7f0de2347e614b8c24de32bf3f88362a212a94
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510403"
---
# <a name="xamarinandroid-spinner"></a>Controle giratório do Xamarin. Android

[`Spinner`](xref:Android.Widget.Spinner)é um widget que apresenta uma lista suspensa para a seleção de itens. Este guia explica como criar um aplicativo simples que exibe uma lista de opções em um controle giratório, seguido por modificações que exibem outros valores associados à opção selecionada.

## <a name="basic-spinner"></a>Controle giratório básico

Na primeira parte deste tutorial, você criará um widget simples de controle giratório que exibe uma lista de planetas. Quando um planeta é selecionado, uma mensagem do sistema exibe o item selecionado:

[![Capturas de tela de exemplo do aplicativo HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Inicie um novo projeto chamado **HelloSpinner**.

Abra **Resources/layout/Main. axml** e insira o seguinte XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dip"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="10dip"
        android:text="@string/planet_prompt"
    />
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:prompt="@string/planet_prompt"
    />
</LinearLayout>
```

Observe que o [`TextView`](xref:Android.Widget.TextView)atributo `android:text` do e o [`Spinner`](xref:Android.Widget.Spinner)atributo `android:prompt` do fazem referência ao mesmo recurso de cadeia de caracteres. Esse texto se comporta como um título para o widget. Quando aplicado ao [`Spinner`](xref:Android.Widget.Spinner), o texto do título será exibido na caixa de diálogo de seleção que aparece ao selecionar o widget.

Edite Resources **/Values/Strings. xml** e modifique o arquivo para ter a seguinte aparência:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloSpinner</string>
  <string name="planet_prompt">Choose a planet</string>
  <string-array name="planets_array">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
  </string-array>
</resources>
```

O segundo `<string>` elemento define a cadeia de caracteres de título [`TextView`](xref:Android.Widget.TextView) referenciada pelo e [`Spinner`](xref:Android.Widget.Spinner) no layout acima.
O `<string-array>` elemento define a lista de cadeias de caracteres que serão exibidas como a lista [`Spinner`](xref:Android.Widget.Spinner) no widget.

Agora, abra **MainActivity.cs** e adicione a `using` seguinte instrução:

```csharp
using System;
```

Em seguida, insira o seguinte código para [`OnCreate()`](xref:Android.App.Activity.OnCreate*)o método):

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    Spinner spinner = FindViewById<Spinner> (Resource.Id.spinner);

    spinner.ItemSelected += new EventHandler<AdapterView.ItemSelectedEventArgs> (spinner_ItemSelected);
    var adapter = ArrayAdapter.CreateFromResource (
            this, Resource.Array.planets_array, Android.Resource.Layout.SimpleSpinnerItem);

    adapter.SetDropDownViewResource (Android.Resource.Layout.SimpleSpinnerDropDownItem);
    spinner.Adapter = adapter;
}
```

Depois que `Main.axml` o layout é definido como a exibição de conteúdo [`Spinner`](xref:Android.Widget.Spinner) , o widget é capturado do layout [`FindViewById<>(int)`](xref:Android.App.Activity.FindViewById*)com.
Dos[`CreateFromResource()`](xref:Android.Widget.ArrayAdapter.CreateFromResource*)
em seguida, o método [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)cria um novo, que associa cada item na matriz de cadeia de caracteres à aparência [`Spinner`](xref:Android.Widget.Spinner) inicial do (que é como cada item aparecerá no controle giratório quando selecionado). A `Resource.Array.planets_array` ID referencia o `string-array` definido acima e a `Android.Resource.Layout.SimpleSpinnerItem` ID faz referência a um layout para a aparência do controle giratório padrão, definida pela plataforma.
[`SetDropDownViewResource`](xref:Android.Widget.ArrayAdapter.SetDropDownViewResource*)
é chamado para definir a aparência de cada item quando o widget é aberto. Por fim, [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) o é definido para associar todos os seus itens [`Spinner`](xref:Android.Widget.Spinner) ao definindo a [`Adapter`](xref:Android.Widget.ArrayAdapter) propriedade.

Agora, forneça um método de retorno de chamada que notifique o aplicativo quando um item tiver sido [`Spinner`](xref:Android.Widget.Spinner)selecionado no. Este método deve se parecer com o seguinte:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Quando um item é selecionado, o remetente é convertido em um [`Spinner`](xref:Android.Widget.Spinner) para que os itens possam ser acessados. Usando a `Position` propriedade `ItemEventArgs`no, você pode descobrir o texto do objeto selecionado e usá-lo para exibir um [`Toast`](xref:Android.Widget.Toast).

Executar o aplicativo; Ele deve ter a seguinte aparência:

[![Exemplo de captura de tela de Spinner com Mars selecionado como o planeta](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Controle giratório usando pares de chave/valor

Geralmente, é necessário usar `Spinner` para exibir valores de chave que estão associados a algum tipo de dados usados pelo seu aplicativo. Como `Spinner` o não funciona diretamente com pares de chave/valor, você deve armazenar o par de chave/valor separadamente, `Spinner` preencher o com valores de chave e, em seguida, usar a posição da chave selecionada no controle giratório para pesquisar o valor de dados associado. 

Nas etapas a seguir, o aplicativo **HelloSpinner** é modificado para exibir a temperatura média para o planeta selecionado:

Adicione a seguinte `using` instrução a **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Adicione a seguinte variável de instância à `MainActivity` classe.
Essa lista manterá pares de chave/valor para os planetas e suas temperaturas médias:

```csharp
private List<KeyValuePair<string, string>> planets;
```

No método, adicione o seguinte código antes que `adapter` seja declarado: `OnCreate`

```csharp
planets = new List<KeyValuePair<string, string>>
{
    new KeyValuePair<string, string>("Mercury", "167 degrees C"),
    new KeyValuePair<string, string>("Venus", "464 degrees C"),
    new KeyValuePair<string, string>("Earth", "15 degrees C"),
    new KeyValuePair<string, string>("Mars", "-65 degrees C"),
    new KeyValuePair<string, string>("Jupiter" , "-110 degrees C"),
    new KeyValuePair<string, string>("Saturn", "-140 degrees C"),
    new KeyValuePair<string, string>("Uranus", "-195 degrees C"),
    new KeyValuePair<string, string>("Neptune", "-200 degrees C")
};
```

Esse código cria um armazenamento simples para planetas e suas temperaturas médias associadas. (Em um aplicativo do mundo real, um banco de dados é normalmente usado para armazenar chaves e os seus respectivos dados associados.)

Imediatamente após o código acima, adicione as seguintes linhas para extrair as chaves e colocá-las em uma lista (em ordem):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Passe esta lista para o `ArrayAdapter` Construtor (em vez `planets_array` do recurso):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modifique `spinner_ItemSelected` para que a posição selecionada seja usada para pesquisar o valor (a temperatura) associado ao planeta selecionado:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Executar o aplicativo; o sistema deve ter a seguinte aparência:

[![Exemplo de seleção de planeta exibindo temperatura](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)

## <a name="resources"></a>Recursos

- [`Resource.Layout`](xref:Android.Resource.Layout)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`Spinner`](xref:Android.Widget.Spinner)

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de atribuição do*
[*Creative Commons 2,5*](http://creativecommons.org/licenses/by/2.5/).
