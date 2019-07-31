---
title: Personalizar a aparência de uma ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 9307e440a780d60a8301c58d70ee882fbbdebab1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646395"
---
# <a name="customizing-a-listviews-appearance-with-xamarinandroid"></a>Personalizando a aparência de um ListView com o Xamarin. Android

A aparência de um ListView é ditada pelo layout das linhas que estão sendo exibidas. Para alterar a aparência de um `ListView`, use um layout de linha diferente.


## <a name="built-in-row-views"></a>Exibições de linha internas

Há doze exibições internas que podem ser referenciadas usando **Android. Resource. layout**:

- **TestListItem** &ndash; Linha única de texto com formatação mínima.

- **SimpleListItem1** &ndash; Linha de texto única.

- **SimpleListItem2** &ndash; Duas linhas de texto.

- **SimpleSelectableListItem** &ndash; Linha única de texto que dá suporte à seleção de um único ou de vários itens (adicionado no nível de API 11).

- **SimpleListItemActivated1** &ndash; Semelhante a SimpleListItem1, mas a cor do plano de fundo indica quando uma linha é selecionada (adicionada no nível da API 11).

- **SimpleListItemActivated2** &ndash; Semelhante a SimpleListItem2, mas a cor do plano de fundo indica quando uma linha é selecionada (adicionada no nível da API 11).

- **SimpleListItemChecked** &ndash; Exibe marcas de seleção para indicar a seleção.

- **SimpleListItemMultipleChoice** &ndash; Exibe as caixas de seleção para indicar a seleção de várias opções.

- **SimpleListItemSingleChoice** &ndash; Exibe botões de opção para indicar a seleção mutuamente exclusiva.

- **TwoLineListItem** &ndash; Duas linhas de texto.

- **ActivityListItem** &ndash; Linha única de texto com uma imagem.

- **SimpleExpandableListItem** &ndash; Agrupa linhas por categorias e cada grupo pode ser expandido ou recolhido.

Cada exibição de linha interna tem um estilo interno associado a ela. Estas capturas de tela mostram como cada exibição aparece:

[![Capturas de tela de TestListItem, SimpleSelectableListItem, SimpleListitem1 e SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Capturas de tela de SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked e SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Capturas de tela de SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem e SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

O arquivo de exemplo **BuiltInViews/HomeScreenAdapter. cs** (na solução **BuiltInViews** ) contém o código para produzir as telas de item de lista não expansível. A exibição é definida no `GetView` método como este:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

As propriedades da exibição podem então ser definidas `Text1`referenciando os identificadores `Text2` de controle padrão e `Icon` em `Android.Resource.Id` (não defina as propriedades que a exibição não contém ou uma exceção será lançada):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

O arquivo de exemplo **BuiltInExpandableViews/ExpandableScreenAdapter. cs** (na solução **BuiltInViews** ) contém o código para produzir a tela SimpleExpandableListItem. A exibição de grupo é definida no `GetGroupView` método como este:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

A exibição filho é definida no `GetChildView` método como este:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

As propriedades da exibição de grupo e do modo de exibição filho podem ser definidas referenciando os `Text1` identificadores padrão e `Text2` de controle, conforme mostrado acima. A captura de tela SimpleExpandableListItem (mostrada acima) fornece um exemplo de uma SimpleExpandableListItem1 (exibição de grupo de uma linha) e uma exibição filho de duas linhas (SimpleExpandableListItem2). Como alternativa, a exibição de grupo pode ser configurada para duas linhas (SimpleExpandableListItem2) e a exibição filho pode ser configurada para uma linha (SimpleExpandableListItem1), ou a exibição de grupo e a exibição filho podem ter o mesmo número de linhas. 



## <a name="accessories"></a>Acessórios

As linhas podem ter acessórios adicionados à direita da exibição para indicar o estado de seleção:

- **SimpleListItemChecked** &ndash; Cria uma lista de seleção única com uma verificação como o indicador.

- **SimpleListItemSingleChoice** &ndash; Cria listas de tipos de botão de opção em que apenas uma opção é possível.

- **SimpleListItemMultipleChoice** &ndash; Cria listas de tipos de caixas de seleção em que várias opções são possíveis.

Os acessórios mencionados anteriormente são ilustrados nas telas a seguir, em sua respectiva ordem:

[![Capturas de tela de SimpleListItemChecked, SimpleListItemSingleChoice e SimpleListItemMultipleChoice com acessórios](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Para exibir um desses acessórios, passe a ID de recurso de layout necessária para o adaptador e defina manualmente o estado de seleção para as linhas necessárias. Esta linha de código mostra como criar e atribuir um `Adapter` usando um desses layouts:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

A `ListView` si só dá suporte a modos de seleção diferentes, independentemente do acessório que está sendo exibido. Para evitar confusão, use `Single` o modo de `SingleChoice` seleção com acessórios `Checked` e `Multiple` o modo ou `MultipleChoice` com o estilo. O modo de seleção é controlado pela `ChoiceMode` propriedade `ListView`do.


### <a name="handling-api-level"></a>Manipulando nível de API

As versões anteriores do Xamarin. Android implementaram enumerações como propriedades de inteiros. A versão mais recente introduziu os tipos de enumeração .NET apropriados, o que torna muito mais fácil descobrir as possíveis opções.

Dependendo de qual nível de API você está direcionando `ChoiceMode` , é um inteiro ou uma enumeração. O arquivo de exemplo **AccessoryViews/homescreen. cs** tem um bloco comentado se você deseja direcionar para a API do Gingerbread:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>Selecionando itens programaticamente

Definir manualmente quais itens são ' selecionados ' é feito com o `SetItemChecked` método (ele pode ser chamado várias vezes para seleção múltipla):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

O código também precisa detectar seleções únicas de forma diferente de várias seleções. Para determinar qual linha foi selecionada no `Single` modo, use a `CheckedItemPosition` Propriedade Integer:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Para determinar quais linhas foram selecionadas no `Multiple` modo, é necessário executar um loop `CheckedItemPositions` `SparseBooleanArray`no. Uma matriz esparsa é como um dicionário que contém apenas entradas em que o valor foi alterado, portanto, você deve percorrer toda a matriz procurando por `true` valores para saber o que foi selecionado na lista, conforme ilustrado no seguinte trecho de código:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>Criando layouts de linha personalizados

As quatro exibições internas de linha são muito simples. Para exibir layouts mais complexos (como uma lista de emails ou tweets ou informações de contato), é necessário um modo de exibição personalizado. Exibições personalizadas geralmente são declaradas como arquivos AXML no diretório de **recursos/layout** e, em seguida, carregadas usando sua ID de recurso por um adaptador personalizado. O modo de exibição pode conter qualquer número de classes de exibição (como textviews, ImageViews e outros controles) com cores, fontes e layout personalizados.

Este exemplo é diferente dos exemplos anteriores de várias maneiras:

-  Herda de `Activity` , não `ListActivity` . Você pode personalizar linhas para qualquer `ListView` , no entanto, outros controles também podem ser `Activity` incluídos em um layout (como um título, botões ou outros elementos da interface do usuário). Este exemplo adiciona um título acima do `ListView` a ilustrar.

-  Requer um arquivo de layout AXML para a tela; nos exemplos anteriores, o `ListActivity` não requer um arquivo de layout. Este AXML contém uma `ListView` declaração de controle.

-  Requer um arquivo de layout AXML para renderizar cada linha. Esse arquivo AXML contém os controles Text e Image com configurações de fonte e cor personalizadas.

-  Usa um arquivo XML seletor personalizado opcional para definir a aparência da linha quando ela é selecionada.

-  A `Adapter` implementação retorna um layout personalizado `GetView` da substituição.

-  `ItemClick`deve ser declarado de forma diferente (um manipulador de eventos `ListView.ItemClick` é anexado a em `OnListItemClick` vez `ListActivity`de uma substituição em).


Essas alterações são detalhadas abaixo, começando com a criação da exibição da atividade e da exibição de linha personalizada e, em seguida, a cobertura das modificações ao adaptador e à atividade para renderizá-las.


### <a name="adding-a-listview-to-an-activity-layout"></a>Adicionando um ListView a um layout de atividade

Como `HomeScreen` não `ListActivity` é mais herdado dele, ele não tem uma exibição padrão, portanto, um arquivo de layout AXML deve ser criado para a exibição do homescreen. Para este exemplo, o modo de exibição terá um título (usando `TextView`um) e `ListView` um para exibir dados. O layout é definido no arquivo **Resources/layout/homescreen. axml** que é mostrado aqui:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

O benefício de usar um `Activity` com um layout personalizado (em vez de `ListActivity`um) está na capacidade de adicionar controles adicionais à tela, como o título `TextView` neste exemplo.


### <a name="creating-a-custom-row-layout"></a>Criando um layout de linha personalizado

Outro arquivo de layout AXML é necessário para conter o layout personalizado para cada linha que será exibida na exibição de lista. Neste exemplo, a linha terá um plano de fundo verde, texto marrom e imagem alinhada à direita. A marcação XML do Android para declarar esse layout é descrita em Resources **/layout/CustomView. axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

Embora um layout de linha personalizado possa conter muitos controles diferentes, o desempenho da rolagem pode ser afetado por designs complexos e por meio de imagens (especialmente se eles precisarem ser carregados pela rede). Consulte o artigo do Google para obter mais informações sobre como resolver problemas de desempenho de rolagem.


### <a name="referencing-a-custom-row-view"></a>Fazendo referência a uma exibição de linha personalizada

A implementação do exemplo de adaptador personalizado está em `HomeScreenAdapter.cs`. O método de chave `GetView` é onde ele carrega o AXML personalizado usando a ID `Resource.Layout.CustomView`de recurso e, em seguida, define as propriedades em cada um dos controles na exibição antes de retorná-lo. A classe completa do adaptador é mostrada:

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```


### <a name="referencing-the-custom-listview-in-the-activity"></a>Referenciando ListView personalizado na atividade

Como a `HomeScreen` classe agora é herdada de `ListView` `Activity`, um campo é declarado na classe para manter uma referência ao controle declarado no AXML:

```csharp
ListView listView;
```

Em seguida, a classe deve carregar o AXML de layout personalizado da `SetContentView` atividade usando o método. Em seguida, ele pode `ListView` localizar o controle no layout e, em seguida, cria e atribui o adaptador e atribui o manipulador de cliques. O código para o método OnCreate é mostrado aqui:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Por fim `ItemClick` , o manipulador deve ser definido; nesse caso, ele apenas `Toast` exibe uma mensagem:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

A tela resultante é parecida com esta:

[![Captura de tela da CustomRowView resultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Personalizando a cor do seletor de linha

Quando uma linha é tocada, ela deve ser realçada para comentários do usuário. Quando uma exibição personalizada especifica como cor da tela de fundo como **CustomView. axml** , ela também substitui o realce da seleção. Essa linha de código em **CustomView. axml** define o plano de fundo como verde claro, mas também significa que não há nenhum indicador visual quando a linha é tocada:

```xml
android:background="#FFDAFF7F"
```

Para reabilitar o comportamento de realce e também personalizar a cor que é usada, defina o atributo background como um seletor personalizado em vez disso. O seletor irá declarar a cor do plano de fundo padrão, bem como a cor de realce. Os recursos de arquivo **/drawáveis/CustomSelector. xml** contêm a seguinte declaração:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

Para fazer referência ao seletor personalizado, altere o atributo background em **CustomView. axml** para:

```xml
android:background="@drawable/CustomSelector"
```

Uma linha selecionada e a mensagem `Toast` correspondente agora têm esta aparência:

[![Uma linha selecionada em laranja, com mensagem do sistema exibindo o nome da linha selecionada](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Impedindo a cintilação em layouts personalizados

O Android tenta melhorar o desempenho da `ListView` rolagem por meio do cache de informações de layout. Se você tiver listas de rolagem longas de dados, também deverá definir `android:cacheColorHint` a propriedade `ListView` na declaração na definição de AXML da atividade (para o mesmo valor de cor que o plano de fundo de seu layout de linha personalizado). A falha ao incluir essa dica pode resultar em uma ' cintilação ', pois o usuário rola através de uma lista com cores de plano de fundo de linha personalizadas.



## <a name="related-links"></a>Links relacionados

- [BuiltInViews (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [AccessoryViews (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [CustomRowView (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
