---
title: Personalizando a aparência de um ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: d1b6c663be5745455f332afc11c185869579fde3
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732899"
---
# <a name="customizing-a-listviews-appearance"></a>Personalizando a aparência de um ListView


## <a name="overview"></a>Visão geral

A aparência de um ListView é determinada pelo layout das linhas que está sendo exibido. Para alterar a aparência de um `ListView`, use um layout de linha diferente.


## <a name="built-in-row-views"></a>Modos de exibição de linhas internas

Há doze exibições internas que podem ser referenciadas usando **Android.Resource.Layout**:

- **TestListItem** &ndash; com uma linha de texto com formatação mínima.

- **SimpleListItem1** &ndash; com uma linha de texto.

- **SimpleListItem2** &ndash; duas linhas de texto.

- **SimpleSelectableListItem** &ndash; com uma linha de texto que dá suporte à seleção de item único ou vários (adicionada no nível de API 11).

- **SimpleListItemActivated1** &ndash; semelhante a SimpleListItem1, mas a cor de plano de fundo indica quando uma linha é selecionada (adicionado no nível de API 11).

- **SimpleListItemActivated2** &ndash; semelhante a SimpleListItem2, mas a cor de plano de fundo indica quando uma linha é selecionada (adicionado no nível de API 11).

- **SimpleListItemChecked** &ndash; exibe marcas de seleção para indicar a seleção.

- **SimpleListItemMultipleChoice** &ndash; exibe as caixas de seleção para indicar a seleção de várias opções.

- **SimpleListItemSingleChoice** &ndash; exibe botões para indicar seleção mutuamente exclusivas.

- **TwoLineListItem** &ndash; duas linhas de texto.

- **ActivityListItem** &ndash; com uma linha de texto com uma imagem.

- **SimpleExpandableListItem** &ndash; agrupa linhas por categorias e cada grupo podem ser expandidas ou recolhidas.

Cada modo de exibição de linha interna tem um estilo de interno associado a ele. Essas capturas de tela mostram como cada modo de exibição é exibido:

[![Capturas de tela de TestListItem, SimpleSelectableListItem, SimpleListitem1 e SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Capturas de tela de SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked e SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Capturas de tela de SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem e SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

O **BuiltInViews/HomeScreenAdapter.cs** arquivo de exemplo (no **BuiltInViews** solução) contém o código para gerar as telas de item de lista não é expansível. O modo de exibição é definido `GetView` método assim:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

As propriedades da exibição, em seguida, podem ser definidas por referência os identificadores de controle padrão `Text1`, `Text2` e `Icon` em `Android.Resource.Id` (não definido para propriedades que não tem o modo de exibição ou uma exceção será lançada):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

O **BuiltInExpandableViews/ExpandableScreenAdapter.cs** arquivo de exemplo (no **BuiltInViews** solução) contém o código para produzir a tela de SimpleExpandableListItem. O modo de exibição de grupo é definido no `GetGroupView` método assim:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

A exibição filha é definida `GetChildView` método assim:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

As propriedades para a exibição de grupo e o filho podem ser definidas referenciando o padrão `Text1` e `Text2` identificadores de controle, como mostrado acima. A captura de tela de SimpleExpandableListItem (mostrada acima) fornece um exemplo de uma exibição de grupo de uma linha (SimpleExpandableListItem1) e um modo de exibição de duas linhas filho (SimpleExpandableListItem2). Como alternativa, o modo de exibição de grupo pode ser configurado para duas linhas (SimpleExpandableListItem2) e a exibição filha pode ser configurada para uma linha (SimpleExpandableListItem1), ou ambos grupo exibição e exibição filho pode ter o mesmo número de linhas. 



## <a name="accessories"></a>Acessórios

As linhas podem ter Acessórios adicionados à direita da exibição para indicar o estado de seleção:

- **SimpleListItemChecked** &ndash; cria uma lista de seleção única com uma verificação de como o indicador.

- **SimpleListItemSingleChoice** &ndash; cria listas de tipo de botão de rádio, onde é possível somente uma opção.

- **SimpleListItemMultipleChoice** &ndash; cria listas de tipo de caixa de seleção em que várias opções são possíveis.

Acessórios mencionados acima são ilustrados nas telas a seguir, em sua respectiva ordem:

[![Capturas de tela de SimpleListItemChecked, SimpleListItemSingleChoice e SimpleListItemMultipleChoice com Acessórios](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Para exibir uma passagem esses Acessórios a ID de recurso necessária de layout para o adaptador, em seguida, define manualmente o estado de seleção para as linhas necessárias. Esta linha de código mostra como criar e atribuir um `Adapter` usando um desses layouts:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

O `ListView` se dá suporte aos modos de seleção diferente, independentemente do acessório que está sendo exibido. Para evitar confusão, use `Single` modo de seleção com `SingleChoice` Acessórios e `Checked` ou `Multiple` modo com o `MultipleChoice` estilo. O modo de seleção é controlado pelo `ChoiceMode` propriedade o `ListView`.


### <a name="handling-api-level"></a>Tratamento de nível de API

Versões anteriores do xamarin implementado enumerações como propriedades de inteiro. A versão mais recente introduziu adequados tipos de enumeração do .NET que torna muito mais fácil de descobrir as possíveis opções.

Dependendo de qual nível de API estiver direcionando, `ChoiceMode` é um inteiro ou uma enumeração. O arquivo de exemplo **AccessoryViews/HomeScreen.cs** tem um bloco comentada se você deseja direcionar a API de biscoito:

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

Definir manualmente os itens que são 'selecionado' é feito com o `SetItemChecked` método (ele pode ser chamado várias vezes para seleção múltipla):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

O código também precisa detectar único seleções de maneira diferente de várias seleções. Para determinar qual linha tiver sido selecionada no `Single` uso de modo a `CheckedItemPosition` propriedade de inteiro:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Para determinar quais linhas foram selecionadas `Multiple` você precisa executar um loop através de modo a `CheckedItemPositions` `SparseBooleanArray`. Uma matriz esparsa é como um dicionário que contém apenas entradas onde o valor foi alterado, portanto devem atravessar a matriz inteira procurando `true` valores saber o que foi selecionado na lista conforme ilustrado no seguinte trecho de código:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>A criação de Layouts de linha personalizada

Os quatro modos internos de linha são muito simples. Para exibir os layouts mais complexos (como uma lista de emails, ou tweets ou informações de contato) é necessário um modo de exibição personalizado. Modos de exibição personalizados geralmente são declarados como arquivos AXML o **recursos/Layout** diretório e, depois, carregados usando o Id de um adaptador personalizado de recursos. O modo de exibição pode conter qualquer número de classes de exibição (como TextViews, ImageViews e outros controles) com o layout, fontes e cores personalizadas.

Este exemplo é diferente dos exemplos anteriores de várias maneiras:

-  Herda de `Activity` , não `ListActivity` . Você pode personalizar linhas para qualquer `ListView` , no entanto, outros controles também podem ser incluídos em um `Activity` layout (por exemplo, um título, botões ou outros elementos de interface do usuário). Este exemplo adiciona um título acima de `ListView` para ilustrar.

-  Requer um arquivo de layout AXML da tela; nos exemplos anteriores a `ListActivity` não requer um arquivo de layout. Este AXML contém um `ListView` declaração de controle.

-  Requer um arquivo de layout AXML para processar cada linha. Este arquivo AXML contém os controles de texto e imagem com configurações de cor e fonte personalizada.

-  Usa um arquivo XML do seletor personalizados opcionais para definir a aparência da linha quando ele é selecionado.

-  O `Adapter` implementação retorna um layout personalizado do `GetView` substituir.

-  `ItemClick` deve ser declarado de forma diferente (um manipulador de eventos está anexado a `ListView.ItemClick` em vez de uma substituição `OnListItemClick` em `ListActivity`).


Essas alterações são detalhadas abaixo, começando com a criação da atividade e a exibição de linha personalizado e, em seguida, que abrangem as modificações para o adaptador e a atividade para renderizá-los.


### <a name="adding-a-listview-to-an-activity-layout"></a>Adicionando uma ListView em um Layout de atividade

Porque `HomeScreen` não herda de `ListActivity` não tem um modo de exibição padrão, para um arquivo AXML layout deve ser criado para o modo de exibição do HomeScreen. Neste exemplo, a exibição terá um título (usando um `TextView`) e um `ListView` para exibir dados. O layout é definido no **Resources/Layout/HomeScreen.axml** arquivo que é mostrado aqui:

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

A vantagem de usar um `Activity` com um layout personalizado (em vez de um `ListActivity`) está na capacidade de adicionar controles adicionais para a tela, como o título `TextView` neste exemplo.


### <a name="creating-a-custom-row-layout"></a>Criar um Layout de linha personalizada

Outro arquivo de layout AXML é necessário para conter o layout personalizado para cada linha que será exibido na exibição de lista. Neste exemplo de linha terá um plano de fundo verde, marrom texto e imagem alinhado à direita. A marcação XML Android para declarar este layout é descrita em **Resources/Layout/CustomView.axml**:

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

Enquanto um layout de linha personalizado pode conter vários controles diferentes, rolar desempenho pode ser afetado por designs complexos e usando imagens (especialmente se eles têm a ser carregado pela rede). Consulte o artigo do Google para obter mais informações sobre como solucionar problemas de desempenho de rolagem.


### <a name="referencing-a-custom-row-view"></a>Fazendo referência a um modo de exibição de linha personalizada

A implementação do exemplo adaptador personalizado está em `HomeScreenAdapter.cs`. O método principal é `GetView` que carrega o AXML personalizado usando a ID de recurso `Resource.Layout.CustomView`e, em seguida, define propriedades em cada um dos controles no modo de exibição antes de retorná-lo. A classe de adaptador completa é mostrada:

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


### <a name="referencing-the-custom-listview-in-the-activity"></a>Referência de ListView personalizado na atividade

Porque o `HomeScreen` classe agora herda de `Activity`, um `ListView` campo é declarado na classe para manter uma referência ao controle declarado no AXML:

```csharp
ListView listView;
```

A classe, em seguida, carregue o layout personalizado da atividade AXML usando o `SetContentView` método. Em seguida, ele pode encontrar o `ListView` controle no layout, em seguida, cria e atribui o adaptador e atribui o manipulador de cliques. O código para o método OnCreate é mostrado aqui:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Por fim o `ItemClick` manipulador deve ser definido; nesse caso ele apenas exibe um `Toast` mensagem:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

A tela resultante tem esta aparência:

[![Captura de tela da CustomRowView resultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Personalizando a cor do seletor de linha

Quando uma linha é tocada deve ser realçado para comentários de usuário. Quando uma exibição personalizada especifica como a cor de plano de fundo como **CustomView.axml** , ele também substitui o realce de seleção. Esta linha de código em **CustomView.axml** define o plano de fundo verde-claro, mas também significa que não há nenhum indicador visual a linha é alterada:

```xml
android:background="#FFDAFF7F"
```

Para habilitar novamente o comportamento de realce e também para personalizar a cor que é usada, defina o atributo de plano de fundo a um seletor personalizado. O seletor irá declarar a cor de plano de fundo padrão, bem como a cor de realce. O arquivo **Resources/Drawable/CustomSelector.xml** contém a declaração a seguir:

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

Para fazer referência o seletor personalizado, altere o atributo de plano de fundo no **CustomView.axml** para:

```xml
android:background="@drawable/CustomSelector"
```

Uma linha selecionada e correspondente `Toast` mensagem agora esta aparência:

[![Uma linha selecionada em laranja, com a mensagem de notificação do sistema exibindo o nome da linha selecionada](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Impedindo a cintilação em Layouts personalizados

Android tenta melhorar o desempenho de `ListView` rolagem armazenando informações de layout. Se você tiver a rolagem longa lista de dados também deve definir o o `android:cacheColorHint` propriedade o `ListView` declaração na definição de AXML da atividade (para o mesmo valor de cor de plano de fundo do seu layout de linha personalizado). Não inclua essa dica pode resultar em 'cintilação' como o usuário rolar por meio de uma lista com cores de plano de fundo de linha personalizado.



## <a name="related-links"></a>Links relacionados

- [BuiltInViews (exemplo)](https://developer.xamarin.com/samples/BuiltInViews/)
- [AccessoryViews (exemplo)](https://developer.xamarin.com/samples/AccessoryViews/)
- [CustomRowView (exemplo)](https://developer.xamarin.com/samples/CustomRowView/)
