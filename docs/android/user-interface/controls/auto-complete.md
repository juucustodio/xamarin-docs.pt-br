---
title: Preenchimento automático para o Xamarin. Android
ms.prod: xamarin
ms.assetid: D4C8CA49-8369-35B7-798D-B147FDC24185
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/31/2018
ms.openlocfilehash: 575235569351d0856c7fbffbf38a981ede1a35ce
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762434"
---
# <a name="auto-complete-for-xamarinandroid"></a>Preenchimento automático para o Xamarin. Android

`AutoCompleteTextView`é um elemento de exibição de texto editável que mostra sugestões de conclusão automaticamente enquanto o usuário está digitando. A lista de sugestões é exibida em um menu suspenso do qual o usuário pode escolher um item para substituir o conteúdo da caixa de edição.

![Exemplo de preenchimento automático](images/auto-complete.png)

## <a name="overview"></a>Visão geral

Para criar um widget de entrada de texto que forneça sugestões de preenchimento automático, use o[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
Widget. São recebidas sugestões de uma coleção de cadeias de caracteres associadas ao [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)widget por meio de um.

Neste tutorial, você criará um[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
o widget que fornece sugestões para um nome de país.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

O [`TextView`](xref:Android.Widget.TextView) é um rótulo que apresenta o[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
Widget.

## <a name="tutorial"></a>Tutorial

Inicie um novo projeto chamado *HelloAutoComplete*.

Crie um arquivo XML chamado `list_item.xml` e salve-o dentro da pasta de **recursos/layout** . Defina a ação de compilação deste arquivo como `AndroidResource`. Edite o arquivo para ter a seguinte aparência:

```xml
<?xml version="1.0" encoding="utf-8"?>

<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp"
    android:textColor="#000">
</TextView> 
```

Esse arquivo define um simples [`TextView`](xref:Android.Widget.TextView) que será usado para cada item que aparece na lista de sugestões.

Abra **Resources/layout/Main. axml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="5dp">
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Country" />
    <AutoCompleteTextView android:id="@+id/autocomplete_country"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"/>
</LinearLayout>
```

Abra **MainActivity.cs** e insira o código a seguir para o[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "Main" layout resource
    SetContentView (Resource.Layout.Main);

    AutoCompleteTextView textView = FindViewById<AutoCompleteTextView> (Resource.Id.autocomplete_country);
    var adapter = new ArrayAdapter<String> (this, Resource.Layout.list_item, COUNTRIES);

    textView.Adapter = adapter;
}
```

Depois que a exibição de conteúdo é definida `main.xml` para o layout, o[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
o widget é capturado do layout com [`FindViewById`](xref:Android.App.Activity.FindViewById*). Um novo [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) é inicializado para associar o `list_item.xml` layout a cada item de lista na `COUNTRIES` matriz de cadeia de caracteres (definida na próxima etapa). Por fim `SetAdapter()` , é chamado para associar [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter) o ao[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
para que a matriz de cadeia de caracteres preencha a lista de sugestões.

Dentro da `MainActivity` classe, adicione a matriz de cadeia de caracteres:

```csharp
static string[] COUNTRIES = new string[] {
  "Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
  "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda", "Argentina",
  "Armenia", "Aruba", "Australia", "Austria", "Azerbaijan",
  "Bahrain", "Bangladesh", "Barbados", "Belarus", "Belgium",
  "Belize", "Benin", "Bermuda", "Bhutan", "Bolivia",
  "Bosnia and Herzegovina", "Botswana", "Bouvet Island", "Brazil", "British Indian Ocean Territory",
  "British Virgin Islands", "Brunei", "Bulgaria", "Burkina Faso", "Burundi",
  "Cote d'Ivoire", "Cambodia", "Cameroon", "Canada", "Cape Verde",
  "Cayman Islands", "Central African Republic", "Chad", "Chile", "China",
  "Christmas Island", "Cocos (Keeling) Islands", "Colombia", "Comoros", "Congo",
  "Cook Islands", "Costa Rica", "Croatia", "Cuba", "Cyprus", "Czech Republic",
  "Democratic Republic of the Congo", "Denmark", "Djibouti", "Dominica", "Dominican Republic",
  "East Timor", "Ecuador", "Egypt", "El Salvador", "Equatorial Guinea", "Eritrea",
  "Estonia", "Ethiopia", "Faeroe Islands", "Falkland Islands", "Fiji", "Finland",
  "Former Yugoslav Republic of Macedonia", "France", "French Guiana", "French Polynesia",
  "French Southern Territories", "Gabon", "Georgia", "Germany", "Ghana", "Gibraltar",
  "Greece", "Greenland", "Grenada", "Guadeloupe", "Guam", "Guatemala", "Guinea", "Guinea-Bissau",
  "Guyana", "Haiti", "Heard Island and McDonald Islands", "Honduras", "Hong Kong", "Hungary",
  "Iceland", "India", "Indonesia", "Iran", "Iraq", "Ireland", "Israel", "Italy", "Jamaica",
  "Japan", "Jordan", "Kazakhstan", "Kenya", "Kiribati", "Kuwait", "Kyrgyzstan", "Laos",
  "Latvia", "Lebanon", "Lesotho", "Liberia", "Libya", "Liechtenstein", "Lithuania", "Luxembourg",
  "Macau", "Madagascar", "Malawi", "Malaysia", "Maldives", "Mali", "Malta", "Marshall Islands",
  "Martinique", "Mauritania", "Mauritius", "Mayotte", "Mexico", "Micronesia", "Moldova",
  "Monaco", "Mongolia", "Montserrat", "Morocco", "Mozambique", "Myanmar", "Namibia",
  "Nauru", "Nepal", "Netherlands", "Netherlands Antilles", "New Caledonia", "New Zealand",
  "Nicaragua", "Niger", "Nigeria", "Niue", "Norfolk Island", "North Korea", "Northern Marianas",
  "Norway", "Oman", "Pakistan", "Palau", "Panama", "Papua New Guinea", "Paraguay", "Peru",
  "Philippines", "Pitcairn Islands", "Poland", "Portugal", "Puerto Rico", "Qatar",
  "Reunion", "Romania", "Russia", "Rwanda", "Sqo Tome and Principe", "Saint Helena",
  "Saint Kitts and Nevis", "Saint Lucia", "Saint Pierre and Miquelon",
  "Saint Vincent and the Grenadines", "Samoa", "San Marino", "Saudi Arabia", "Senegal",
  "Seychelles", "Sierra Leone", "Singapore", "Slovakia", "Slovenia", "Solomon Islands",
  "Somalia", "South Africa", "South Georgia and the South Sandwich Islands", "South Korea",
  "Spain", "Sri Lanka", "Sudan", "Suriname", "Svalbard and Jan Mayen", "Swaziland", "Sweden",
  "Switzerland", "Syria", "Taiwan", "Tajikistan", "Tanzania", "Thailand", "The Bahamas",
  "The Gambia", "Togo", "Tokelau", "Tonga", "Trinidad and Tobago", "Tunisia", "Turkey",
  "Turkmenistan", "Turks and Caicos Islands", "Tuvalu", "Virgin Islands", "Uganda",
  "Ukraine", "United Arab Emirates", "United Kingdom",
  "United States", "United States Minor Outlying Islands", "Uruguay", "Uzbekistan",
  "Vanuatu", "Vatican City", "Venezuela", "Vietnam", "Wallis and Futuna", "Western Sahara",
  "Yemen", "Yugoslavia", "Zambia", "Zimbabwe"
};
```

Esta é a lista de sugestões que serão fornecidas em uma lista suspensa quando o usuário digitar no[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
Widget.

Execute o aplicativo. Conforme você digita, você deve ver algo assim:

[![Captura de tela de preenchimento automático listando nomes que contêm "CA"](auto-complete-images/helloautocomplete.png)](auto-complete-images/helloautocomplete.png#lightbox)

## <a name="more-information"></a>Mais informações

Observe que o uso de uma matriz de cadeia de caracteres embutida em código não é uma prática de design recomendada porque o código do aplicativo deve se concentrar no comportamento, não no conteúdo. O conteúdo do aplicativo, como cadeias de caracteres, deve ser externo do código para facilitar modificações no conteúdo e facilitar a localização do conteúdo. As cadeias de caracteres embutidas em código são usadas neste tutorial apenas para simplificar e se concentrar no[`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)
Widget. Em vez disso, seu aplicativo deve declarar essas matrizes de cadeia de caracteres em um arquivo XML. Isso pode ser feito com um `<string-array>` recurso em seu arquivo `res/values/strings.xml` de projeto. Por exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="countries_array">
        <item>Bahrain</item>
        <item>Bangladesh</item>
        <item>Barbados</item>
        <item>Belarus</item>
        <item>Belgium</item>
        <item>Belize</item>
        <item>Benin</item>
    </string-array>
</resources>
```

Para usar essas cadeias de caracteres [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)de recurso para o, substitua o original[`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
linha do construtor com o seguinte:

```csharp
string[] countries = Resources.GetStringArray (Resource.array.countries_array);
var adapter = new ArrayAdapter<String> (this, Resource.layout.list_item, countries);
```

### <a name="references"></a>Referências

- [Receita AutoCompleteTextView](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/autocomplete_text_view/add_an_autocomplete_text_input) &ndash; Projeto de exemplo do Xamarin. Android para o`AutoCompleteTextView`
- [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter)
- [`AutoCompleteTextView`](xref:Android.Widget.AutoCompleteTextView)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/). Este tutorial se baseia no [tutorial de conclusão automática do Android *](https://developer.android.com/resources/tutorials/views/hello-autocomplete.html)._
