---
title: Usando o ListView no xamarin. Android
description: ListView é um componente importante da interface do usuário dos aplicativos do Android; ele é usado em qualquer lugar do curto listas de opções de menu para listas longas de contatos ou Favoritos do internet. Ele fornece uma maneira simples para apresentar uma lista de rolagem de linhas que pode ser formatado com um estilo interno ou amplamente personalizada.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: a30256722647bbea482970d0c4a751954810d99e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61170780"
---
# <a name="listview"></a>ListView

_ListView é um componente importante da interface do usuário dos aplicativos do Android; ele é usado em qualquer lugar do curto listas de opções de menu para listas longas de contatos ou Favoritos do internet. Ele fornece uma maneira simples para apresentar uma lista de rolagem de linhas que pode ser formatado com um estilo interno ou amplamente personalizada._


## <a name="overview"></a>Visão geral

Modos de exibição de lista e adaptadores estão incluídos nos blocos de construção mais fundamentais dos aplicativos do Android. O `ListView` classe fornece uma maneira flexível para apresentar dados, seja ele um menu de curto ou uma lista de rolagem de tempo. Ele fornece recursos de usabilidade, como índices de rolagem, rápidos e uma ou várias seleções para ajudá-lo a criar interfaces do usuário de dispositivos móveis para seus aplicativos. A instância `ListView` requer um *Adaptador* para fornecer a ela dados contidos nas exibições de linha.

Este guia explica como implementar `ListView` e os vários `Adapter` classes no xamarin. Android. Ele também demonstra como personalizar a aparência de um `ListView`, e ele discute a importância da linha usar novamente para reduzir o consumo de memória. Também há alguma discussão de como o ciclo de vida de atividade afeta `ListView` e `Adapter` usar. Se você estiver trabalhando em aplicativos de plataforma cruzada com xamarin. IOS, o `ListView` controle é estruturalmente similar do iOS `UITableView` (e o Android `Adapter` é semelhante ao `UITableViewSource`).

Primeiro, um breve tutorial apresenta o `ListView` com um exemplo de código básico. Em seguida, links para tópicos mais avançados são fornecidos para ajudar você a usar `ListView` em aplicativos do mundo real.


> [!NOTE]
> O `RecyclerView` widget é uma versão mais avançada e flexível de `ListView`. Porque `RecyclerView` foi projetado para ser o sucessor `ListView` (e `GridView`), é recomendável que você use `RecyclerView` em vez de `ListView` para desenvolver novos aplicativos. Para obter mais informações, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).



## <a name="listview-tutorial"></a>Tutorial de ListView

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) is a [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
Isso cria uma lista de itens roláveis. Os itens da lista são inseridos automaticamente à lista usando um [ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/).

Neste tutorial, você criará uma lista rolável de nomes de país que são lidos a partir de uma matriz de cadeia de caracteres. Quando um item de lista é selecionado, será exibida uma mensagem de notificação do sistema a posição do item na lista.


Iniciar um novo projeto chamado **HelloListView**.

Criar um arquivo XML denominado **list_item.xml** e salve-o dentro de **recursos/Layout/** pasta. Insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Esse arquivo define o layout para cada item que será colocada na [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Abra `MainActivity.cs` e modifique a classe para estender [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (em vez de [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

Insira o seguinte código para o [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

Observe que isso não carregar um arquivo de layout para a atividade (que geralmente faz com [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))).
Em vez disso, definindo o [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
propriedade adiciona automaticamente um [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
para preencher a tela inteira do [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/).
Esse método usa um [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), que gerencia a matriz de itens de lista que será alocada com o [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).
O [`ArrayAdapter<T>`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)
construtor usa o aplicativo [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/), a descrição de layout para cada item de lista (criado na etapa anterior) e um `T[]` ou [`Java.Util.IList<T>`](https://developer.xamarin.com/api/type/Java.Util.IList/)
matriz de objetos a serem inseridos no [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
(definido em seguida).

O [`TextFilterEnabled`](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)
ativa a propriedade texto filtragem para o [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/), de modo que quando o usuário começa a digitar, a lista será filtrada.

O [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
evento pode ser usado para assinar os manipuladores de cliques. Quando um item no [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
é clicado, o manipulador é chamado e uma [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
será exibida a mensagem, usando o texto do item clicado.

Você pode usar os designs de item de lista fornecidas pela plataforma em vez de definir seu próprio arquivo de layout para o [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).
Por exemplo, tente usar `Android.Resource.Layout.SimpleListItem1` em vez de `Resource.Layout.list_item`.

Adicione o seguinte `using` instrução:

```csharp
using System;
```
Em seguida, adicione a seguinte matriz de cadeia de caracteres como um membro de `MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

Isso é a matriz de cadeias de caracteres que será alocada com o [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Execute o aplicativo. Você pode rolar a lista ou digite para filtrar a ele e clique em um item para ver uma mensagem. Você deve ver algo parecido com isso:

[![Captura de tela do exemplo de ListView com nomes de país](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Observe que usando uma matriz de cadeia de caracteres embutido em código não é a melhor prática de design. Uma é usada neste tutorial para simplificar, para demonstrar o [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
widget. A prática recomendada é fazer referência a uma matriz de cadeia de caracteres definida por um recurso externo, como com um `string-array` recursos em seu projeto **Resources/Values/Strings.xml** arquivo. Por exemplo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
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

Para usar essas cadeias de caracteres de recurso para o [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), substituir o original [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
linha com o seguinte:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
Execute o aplicativo. Você deve ver algo parecido com isso:

[![Captura de tela do exemplo de ListView com menor lista de nomes](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>Vá mais longe com ListView

Os tópicos restantes (vinculados abaixo) dê uma olhada abrangente trabalhando com o `ListView` classe e os diferentes tipos de tipos de adaptadores que você pode usar com ele. A estrutura é a seguinte:

-   **Aparência visual** &ndash; partes do `ListView` controle e como eles funcionam.

-   **As classes** &ndash; visão geral das classes usadas para exibir um `ListView`.

-   **Exibindo dados em um ListView** &ndash; como exibir uma lista simple de dados, como implementar `ListView's` recursos de usabilidade; como usar layouts de linha interna diferente; e adaptadores de economizar memória, novamente usando modos de exibição de linha.

-   **Aparência personalizada** &ndash; alterando o estilo do `ListView` com layouts personalizados, fontes e cores.

-   **Usando o SQLite** &ndash; como exibir dados de um banco de dados SQLite com um `CursorAdapter`.

-   **Ciclo de vida de atividade** &ndash; considerações de Design ao implementar `ListView` atividades, incluindo onde, no ciclo de vida, você deve preencher seus dados e quando liberar recursos.

A discussão (dividida em seis partes) começa com uma visão geral do `ListView` própria classe antes de introduzir progressivamente mais complexos exemplos de como usá-lo.

-   [Partes e funcionalidade de ListView](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Preenchendo um ListView com dados](~/android/user-interface/layouts/list-view/populating.md)
-   [Personalizar a aparência de uma ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Usar CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Usar um ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView e o ciclo de vida da atividade](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Resumo

Este conjunto de tópicos introduzido `ListView` e fornecidos alguns exemplos de como usar os recursos internos do `ListActivity`. Ele discutiu implementações personalizadas dos `ListView` que é permitido para layouts coloridos e usando um banco de dados SQLite e ele tocadas brevemente sobre a relevância do ciclo de vida de atividade seu `ListView` implementação.


## <a name="related-links"></a>Links relacionados

- [AccessoryViews (amostra)](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid (amostra)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (amostra)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews (amostra)](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView (amostra)](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll (amostra)](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex (amostra)](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter (amostra)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (amostra)](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [Tutorial de ciclo de vida de atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Trabalhar com tabelas e células (no xamarin. IOS)](~/ios/user-interface/controls/tables/index.md)
- [Referência de classe de ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Referência de classe ListActivity](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [Referência de classe BaseAdapter](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [Referência de classe ArrayAdapter](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [Referência de classe CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
