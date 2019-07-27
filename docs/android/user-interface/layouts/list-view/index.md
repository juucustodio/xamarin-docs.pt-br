---
title: Usando ListView no Xamarin. Android
description: ListView é um componente de interface do usuário importante dos aplicativos Android; Ele é usado em todos os lugares de listas curtas de opções de menu para listas longas de contatos ou favoritos da Internet. Ele fornece uma maneira simples de apresentar uma lista de rolagem de linhas que podem ser formatadas com um estilo interno ou personalizados extensivamente.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 653b6e3d41f9acb4ee3e9ee3626e72220687ccb8
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510084"
---
# <a name="xamarinandroid-listview"></a>ListView do Xamarin. Android

_ListView é um componente de interface do usuário importante dos aplicativos Android; Ele é usado em todos os lugares de listas curtas de opções de menu para listas longas de contatos ou favoritos da Internet. Ele fornece uma maneira simples de apresentar uma lista de rolagem de linhas que podem ser formatadas com um estilo interno ou personalizados extensivamente._

## <a name="overview"></a>Visão geral

Os modos de exibição de lista e os adaptadores estão incluídos nos blocos de construção mais fundamentais dos aplicativos Android. A `ListView` classe fornece uma maneira flexível de apresentar dados, seja um menu curto ou uma lista de rolagem longa. Ele fornece recursos de usabilidade como rolagem rápida, índices e seleção única ou múltipla para ajudá-lo a criar interfaces de usuário amigáveis para dispositivos móveis para seus aplicativos. A instância `ListView` requer um *Adaptador* para fornecer a ela dados contidos nas exibições de linha.

Este guia explica como implementar `ListView` e as várias `Adapter` classes no Xamarin. Android. Ele também demonstra como personalizar a aparência de um `ListView`e aborda a importância de reutilização de linha para reduzir o consumo de memória. Também há alguma discussão sobre como o ciclo de vida da `ListView` atividade `Adapter` afeta e usa. Se você estiver trabalhando em aplicativos de plataforma cruzada com o Xamarin. Ios `ListView` , o controle será estruturalmente semelhante ao `UITableView` Ios (e o `Adapter` `UITableViewSource`Android é semelhante ao).

Primeiro, um breve tutorial apresenta o `ListView` com um exemplo de código básico. Em seguida, são fornecidos links para tópicos mais avançados para ajudá- `ListView` lo a usar os aplicativos do mundo real.

> [!NOTE]
> O `RecyclerView` widget é uma versão mais avançada e flexível do `ListView`. Como `RecyclerView` o foi projetado para ser o `ListView` sucessor ( `GridView`e), recomendamos que você `RecyclerView` use em `ListView` vez de criar um novo desenvolvimento de aplicativos. Para obter mais informações, consulte [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

## <a name="listview-tutorial"></a>Tutorial de ListView

[`ListView`](xref:Android.Widget.ListView)é um[`ViewGroup`](xref:Android.Views.ViewGroup)
Isso cria uma lista de itens roláveis. Os itens de lista são inseridos automaticamente na lista usando [`IListAdapter`](xref:Android.Widget.IListAdapter)um.

Neste tutorial, você criará uma lista rolável de nomes de países que são lidos de uma matriz de cadeia de caracteres. Quando um item de lista é selecionado, uma mensagem do sistema exibe a posição do item na lista.

Inicie um novo projeto chamado **HelloListView**.

Crie um arquivo XML chamado **list_item. xml** e salve-o dentro do **recurso/layout/** pasta. Insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Esse arquivo define o layout para cada item que será colocado no [`ListView`](xref:Android.Widget.ListView).

Abra `MainActivity.cs` e modifique a classe a ser [`ListActivity`](xref:Android.App.ListActivity) estendida ( [`Activity`](xref:Android.App.Activity)em vez de):

```csharp
public class MainActivity : ListActivity
{
```

Insira o seguinte código para o [`OnCreate()`](xref:Android.App.Activity.OnCreate*)método):

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

Observe que isso não carrega um arquivo de layout para a atividade (que você geralmente faz com [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*))).
Em vez disso, defina a[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
a propriedade adiciona automaticamente um[`ListView`](xref:Android.Widget.ListView)
para preencher a tela inteira do [`ListActivity`](xref:Android.App.ListActivity).
Esse método usa um [`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1), que gerencia a matriz de itens de lista que serão colocados [`ListView`](xref:Android.Widget.ListView)no.
Dos[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
o construtor usa o [`Context`](xref:Android.Content.Context)aplicativo, a descrição de layout para cada item de lista (criado na etapa anterior) e `T[]` um ou[`Java.Util.IList<T>`](xref:Java.Util.IList)
matriz de objetos a serem inseridos no[`ListView`](xref:Android.Widget.ListView)
(definido em seguida).

Dos[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
a propriedade ativa a filtragem de texto [`ListView`](xref:Android.Widget.ListView)para o, de modo que, quando o usuário começar a digitar, a lista será filtrada.

Dos[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
o evento pode ser usado para assinar manipuladores para cliques. Quando um item no[`ListView`](xref:Android.Widget.ListView)
é clicado, o manipulador é chamado e um[`Toast`](xref:Android.Widget.Toast)
a mensagem é exibida, usando o texto do item clicado.

Você pode usar os designs de item de lista fornecidos pela plataforma em vez de definir seu próprio [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)arquivo de layout para o.
Por exemplo, tente usar `Android.Resource.Layout.SimpleListItem1` em vez `Resource.Layout.list_item`de.

Adicione a seguinte `using` instrução:

```csharp
using System;
```
Em seguida, adicione a seguinte matriz de cadeia de caracteres `MainActivity`como um membro de:

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

Essa é a matriz de cadeias de caracteres que será colocada [`ListView`](xref:Android.Widget.ListView)no.

Execute o aplicativo. Você pode rolar a lista ou digitar para filtrá-la e clicar em um item para ver uma mensagem. Você deve ver algo parecido com isso:

[![Captura de tela de exemplo de ListView com nomes de país](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Observe que o uso de uma matriz de cadeia de caracteres embutida em código não é a melhor prática de design. Um é usado neste tutorial para simplificar, para demonstrar o[`ListView`](xref:Android.Widget.ListView)
Widget. A prática recomendada é fazer referência a uma matriz de cadeia de caracteres definida por um recurso externo, `string-array` como com um recurso no arquivo Resources/ **Values/Strings. xml** do projeto. Por exemplo:

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

Para usar essas cadeias de caracteres [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1)de recurso para o, substitua o original[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
linha com o seguinte:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

Execute o aplicativo. Você deve ver algo parecido com isso:

[![Captura de tela de exemplo de ListView com lista de nomes menor](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>Indo mais com ListView

Os tópicos restantes (vinculados abaixo) têm uma visão abrangente de como trabalhar `ListView` com a classe e os diferentes tipos de tipos de adaptadores que você pode usar com ele. A estrutura é a seguinte:

-   **Aparência visual** &ndash; Partes`ListView` do controle e como elas funcionam.

-   **Classes** do Visão geral das classes usadas para exibir um `ListView`. &ndash;

-   **Exibindo dados em um ListView** Como exibir uma lista simples de dados; como implementar `ListView's` recursos de usabilidade; como usar layouts de linha internos diferentes e como os adaptadores salvam a memória reutilizando exibições de linha. &ndash;

-   **Aparência personalizada** Alterar o estilo `ListView` do com layouts, fontes e cores personalizados. &ndash;

-   **Usando o SQLite** Como exibir dados de um banco de dados SQLite com `CursorAdapter`um. &ndash;

-   **Ciclo de vida da atividade** Considerações de design ao `ListView` implementar atividades, incluindo onde, no ciclo de vida, você deve preencher seus dados e quando liberar recursos. &ndash;

A discussão (dividida em seis partes) começa com uma visão geral da `ListView` própria classe antes de introduzir exemplos progressivamente mais complexos de como usá-la.

-   [Partes e funcionalidade de ListView](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Populando um ListView com dados](~/android/user-interface/layouts/list-view/populating.md)
-   [Personalizar a aparência de uma ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Usar CursorAdapters](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Usar um ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView e o ciclo de vida da atividade](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Resumo

Esse conjunto de tópicos apresentou `ListView` e forneceu alguns exemplos de como usar os recursos internos `ListActivity`do. Ele abordou implementações `ListView` personalizadas de que permitia layouts coloridos e uso de um banco de dados SQLite, além de ser brevemente abordada na relevância `ListView` do ciclo de vida da atividade em sua implementação.


## <a name="related-links"></a>Links relacionados

- [AccessoryViews (exemplo)](https://developer.xamarin.com/samples/monodroid/AccessoryViews/)
- [BasicTableAndroid (exemplo)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (exemplo)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [BuiltInViews (exemplo)](https://developer.xamarin.com/samples/monodroid/BuiltInViews/)
- [CustomRowView (exemplo)](https://developer.xamarin.com/samples/monodroid/CustomRowView/)
- [FastScroll (exemplo)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
- [SectionIndex (exemplo)](https://developer.xamarin.com/samples/monodroid/SectionIndex/)
- [SimpleCursorTableAdapter (exemplo)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (exemplo)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
- [Tutorial do ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Trabalhando com tabelas e células (no Xamarin. iOS)](~/ios/user-interface/controls/tables/index.md)
- [Referência de classe ListView](xref:Android.Widget.ListView)
- [Referência de classe ListActivity](xref:Android.App.ListActivity)
- [Referência de classe BaseAdapter](xref:Android.Widget.BaseAdapter)
- [Referência de classe ArrayAdapter](xref:Android.Widget.ArrayAdapter)
- [Referência de classe CursorAdapter](xref:Android.Widget.CursorAdapter)
