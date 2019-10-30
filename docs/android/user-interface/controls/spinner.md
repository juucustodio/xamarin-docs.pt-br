---
title: Controle giratório do Xamarin. Android
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: ba4a83eb997b879e8a2398f9857e2fd80221f8ef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029159"
---
# <a name="xamarinandroid-spinner"></a>Controle giratório do Xamarin. Android

[`Spinner`](xref:Android.Widget.Spinner) é um widget que apresenta uma lista suspensa para a seleção de itens. Este guia explica como criar um aplicativo simples que exibe uma lista de opções em um controle giratório, seguido por modificações que exibem outros valores associados à opção selecionada.

## <a name="basic-spinner"></a>Controle giratório básico

Na primeira parte deste tutorial, você criará um widget simples de controle giratório que exibe uma lista de planetas. Quando um planeta é selecionado, uma mensagem do sistema exibe o item selecionado:

[![capturas de tela de exemplo do aplicativo HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

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

Observe que o atributo `android:text` do [`TextView`](xref:Android.Widget.TextView)e o atributo `android:prompt` do [`Spinner`](xref:Android.Widget.Spinner)referenciam o mesmo recurso de cadeia de caracteres. Esse texto se comporta como um título para o widget. Quando aplicado à [`Spinner`](xref:Android.Widget.Spinner), o texto do título será exibido na caixa de diálogo de seleção que aparece ao selecionar o widget.

Edite **Resources/Values/Strings. xml** e modifique o arquivo para ter a seguinte aparência:

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

O segundo elemento `<string>` define a cadeia de caracteres de título referenciada pelo [`TextView`](xref:Android.Widget.TextView) e [`Spinner`](xref:Android.Widget.Spinner) no layout acima.
O elemento `<string-array>` define a lista de cadeias de caracteres que serão exibidas como a lista no widget [`Spinner`](xref:Android.Widget.Spinner) .

Agora, abra **MainActivity.cs** e adicione a seguinte instrução de `using`:

```csharp
using System;
```

Em seguida, insira o seguinte código para o método [`OnCreate()`](xref:Android.App.Activity.OnCreate*)):

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

Depois que o layout de `Main.axml` é definido como a exibição de conteúdo, o widget de [`Spinner`](xref:Android.Widget.Spinner) é capturado do layout com [`FindViewById<>(int)`](xref:Android.App.Activity.FindViewById*).
O [`CreateFromResource()`](xref:Android.Widget.ArrayAdapter.CreateFromResource*)
em seguida, o método cria um novo [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter), que associa cada item na matriz de cadeia de caracteres à aparência inicial do [`Spinner`](xref:Android.Widget.Spinner) (que é como cada item aparecerá no controle giratório quando selecionado). A ID de `Resource.Array.planets_array` referencia a `string-array` definida acima e a ID de `Android.Resource.Layout.SimpleSpinnerItem` referencia um layout para a aparência do controle giratório padrão, definida pela plataforma.
[`SetDropDownViewResource`](xref:Android.Widget.ArrayAdapter.SetDropDownViewResource*)
é chamado para definir a aparência de cada item quando o widget é aberto. Por fim, a [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) é definida para associar todos os seus itens com a [`Spinner`](xref:Android.Widget.Spinner) , definindo a propriedade [`Adapter`](xref:Android.Widget.ArrayAdapter) .

Agora, forneça um método de retorno de chamada que notifique o aplicativo quando um item tiver sido selecionado no [`Spinner`](xref:Android.Widget.Spinner). Este método deve se parecer com o seguinte:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Quando um item é selecionado, o remetente é convertido em um [`Spinner`](xref:Android.Widget.Spinner) para que os itens possam ser acessados. Usando a propriedade `Position` na `ItemEventArgs`, você pode descobrir o texto do objeto selecionado e usá-lo para exibir uma [`Toast`](xref:Android.Widget.Toast).

Executar o aplicativo; Ele deve ter a seguinte aparência:

[![exemplo de captura de tela do controle giratório com Mars selecionado como o planeta](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Controle giratório usando pares de chave/valor

Geralmente, é necessário usar `Spinner` para exibir valores de chave que estão associados a algum tipo de dados usados pelo seu aplicativo. Como `Spinner` não funciona diretamente com pares de chave/valor, você deve armazenar o par de chave/valor separadamente, preencher o `Spinner` com valores de chave e, em seguida, usar a posição da chave selecionada no controle giratório para pesquisar o valor de dados associado. 

Nas etapas a seguir, o aplicativo **HelloSpinner** é modificado para exibir a temperatura média para o planeta selecionado:

Adicione a seguinte instrução `using` a **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Adicione a seguinte variável de instância à classe `MainActivity`.
Essa lista manterá pares de chave/valor para os planetas e suas temperaturas médias:

```csharp
private List<KeyValuePair<string, string>> planets;
```

No método `OnCreate`, adicione o código a seguir antes que `adapter` seja declarado:

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

Passe esta lista para o Construtor `ArrayAdapter` (em vez do recurso `planets_array`):

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

[![exemplo de seleção do planeta exibindo a temperatura](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)

## <a name="resources"></a>Recursos

- [`Resource.Layout`](xref:Android.Resource.Layout)
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`Spinner`](xref:Android.Widget.Spinner)

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na licença de* [*atribuição
Creative Commons 2,5*](https://creativecommons.org/licenses/by/2.5/).
