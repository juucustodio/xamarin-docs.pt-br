---
title: Controle giratório
ms.prod: xamarin
ms.assetid: 004089E9-7C1D-2285-765A-B69143091F2A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 039c3f3a177d62a43679facba821675c6d716a91
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774337"
---
# <a name="spinner"></a>Controle giratório

[`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) é um widget que apresenta uma lista suspensa para selecionar itens. Este guia explica como criar um aplicativo simples que exibe uma lista de opções em um controle giratório, seguido por modificações que exibem os outros valores associados com a opção selecionada.

## <a name="basic-spinner"></a>Controle giratório básico

A primeira parte deste tutorial, você criará um widget do controle giratório simples que exibe uma lista dos planetas. Quando um planeta é selecionado, uma mensagem de notificação do sistema exibe o item selecionado:

[![Capturas de tela de exemplo de aplicativo HelloSpinner](spinner-images/01-example-screenshots-sml.png)](spinner-images/01-example-screenshots.png#lightbox)

Iniciar um novo projeto denominado **HelloSpinner**.

Abra **Resources/Layout/Main.axml** e insira o seguinte XML:

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

Observe que o [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/)do `android:text` atributo e o [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)do `android:prompt` atributo ambos referenciar o mesmo recurso de cadeia de caracteres. Esse texto se comporta como um título para o widget. Quando aplicado ao [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/), o texto do título aparecerá na caixa de diálogo de seleção aparece ao selecionar o widget.

Editar **Resources/Values/Strings.xml** e modifique o arquivo para se parecer com isso:

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

A segunda `<string>` elemento define a cadeia de caracteres do título referenciada pelo [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) e [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) no layout acima.
O `<string-array>` elemento define a lista de cadeias de caracteres que será exibido como a lista no [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) widget.

Agora, abra **MainActivity.cs** e adicione o seguinte `using` instrução:

```csharp
using System;
```

Em seguida, insira o seguinte código para o [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) método:

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

Após o `Main.axml` layout é definido como o modo de exibição de conteúdo, o [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) widget é capturado do layout com [ `FindViewById<>(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
O [ `CreateFromResource()` ](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.CreateFromResource/p/Android.Content.Context/System.Int32/System.Int32/) método cria um novo [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/), que associa cada item na matriz de cadeia de caracteres para a aparência inicial para o [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) (que é como cada item será exibido no controle giratório quando selecionado). O `Resource.Array.planets_array` ID referências a `string-array` definida acima e `Android.Resource.Layout.SimpleSpinnerItem` ID faz referência a um layout para a aparência do controle giratório padrão, definida pela plataforma.
[`SetDropDownViewResource`](https://developer.xamarin.com/api/member/Android.Widget.ArrayAdapter.SetDropDownViewResource/p/System.Int32/) é chamado para definir a aparência de cada item quando o widget é aberto. Por fim, o [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) é definido como associar todos os seus itens com o [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) definindo o [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter) propriedade.

Agora fornecem um método de retorno de chamada que notifys o aplicativo quando um item foi selecionado o [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/). Eis a aparência que esse método deve:

```csharp
private void spinner_ItemSelected (object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format ("The planet is {0}", spinner.GetItemAtPosition (e.Position));
    Toast.MakeText (this, toast, ToastLength.Long).Show ();
}
```

Quando um item é selecionado, o remetente é convertido em um [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) para que os itens podem ser acessados. Usando o `Position` propriedade o `ItemEventArgs`, você pode localizar o texto do objeto selecionado e usá-lo para exibir um [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/).

Executar o aplicativo. ele deve ser assim:

[![Exemplo de captura de tela de controle giratório com Mars selecionado como o planeta](spinner-images/02-basic-example-sml.png)](spinner-images/02-basic-example.png#lightbox)

## <a name="spinner-using-keyvalue-pairs"></a>Usando pares chave/valor de controle giratório

Geralmente, é necessário usar `Spinner` para exibir valores de chave que estão associados com algum tipo de dados usados pelo seu aplicativo. Porque `Spinner` não trabalhar diretamente com pares de chave/valor, você deve armazenar o par chave/valor separadamente, preencha o `Spinner` com valores de chave, em seguida, usar a posição da chave selecionada no controle giratório para pesquisar o valor de dados associados. 

Nas etapas a seguir, o **HelloSpinner** aplicativo é modificado para exibir a temperatura média para o planeta selecionado:

Adicione o seguinte `using` instrução **MainActivity.cs**:

```csharp
using System.Collections.Generic;
```

Adicione a seguinte variável de instância para o `MainActivity` classe.
Essa lista armazenará os pares chave/valor para planetas e suas temperaturas médias:

```csharp
private List<KeyValuePair<string, string>> planets;
```

No `OnCreate` método, adicione o seguinte código antes de `adapter` é declarado:

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

Esse código cria um repositório simple para planetas e seus associadas temperaturas médias. (Em um aplicativo do mundo real, um banco de dados é normalmente usado para armazenar chaves e seus dados associados.)

Imediatamente após o código acima, adicione as seguintes linhas para extrair as chaves e colocá-los em uma lista (em ordem):

```csharp
List<string> planetNames = new List<string>();
foreach (var item in planets)
    planetNames.Add (item.Key);
```

Passar essa lista para o `ArrayAdapter` construtor (em vez do `planets_array` recurso):

```csharp
var adapter = new ArrayAdapter<string>(this,
    Android.Resource.Layout.SimpleSpinnerItem, planetNames);
```

Modificar `spinner_ItemSelected` para que a posição selecionada é usada para pesquisar o valor (a temperatura) associado com o planeta selecionado:

```csharp
private void spinner_ItemSelected(object sender, AdapterView.ItemSelectedEventArgs e)
{
    Spinner spinner = (Spinner)sender;
    string toast = string.Format("The mean temperature for planet {0} is {1}",
        spinner.GetItemAtPosition(e.Position), planets[e.Position].Value);
    Toast.MakeText(this, toast, ToastLength.Long).Show();
}
```

Executar o aplicativo. o sistema deve ter esta aparência:

[![Exemplo de seleção do planeta exibindo temperatura](spinner-images/03-keyvalue-example-sml.png)](spinner-images/03-keyvalue-example.png#lightbox)
   
  

## <a name="resources"></a>Recursos

-   [`Resource.Layout`](https://developer.xamarin.com/api/type/Android.Resource+Layout/) 
-   [`ArrayAdapter`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/) 
-   [`Spinner`](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) 

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/).
