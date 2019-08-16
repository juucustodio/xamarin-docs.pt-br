---
title: Populando um ListView do Xamarin. Android com dados
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: e92aada7be8a296baeaa9eebfb18fe906b5c3b63
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522547"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>Populando um ListView do Xamarin. Android com dados

Para adicionar linhas a uma `ListView` , você precisa adicioná-la ao seu layout e implementar `IListAdapter` um com métodos que `ListView` as chamadas se preencham. O Android inclui classes internas `ListActivity` e `ArrayAdapter` que você pode usar sem definir nenhum código ou XML de layout personalizado. A `ListActivity` classe cria automaticamente um `ListView` e expõe uma `ListAdapter` propriedade para fornecer as exibições de linha a serem exibidas por meio de um adaptador.

Os adaptadores internos usam uma ID de recurso de exibição como um parâmetro que é usado para cada linha. Você pode usar recursos internos como os do `Android.Resource.Layout` , para que não seja necessário escrever o seu próprio.

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Usando a cadeia de&lt;caracteres ListActivity e ArrayAdapter&gt;

O exemplo **basictable/homescreen. cs** demonstra como usar essas classes para exibir um `ListView` em apenas algumas linhas de código:

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>Manipulando cliques de linha

Geralmente, `ListView` um também permitirá que o usuário toque em uma linha para executar alguma ação (como reproduzir uma música ou chamar um contato ou mostrar outra tela). Para responder aos toques do usuário, é necessário que haja mais um método implementado `ListActivity` da &ndash; `OnListItemClick` &ndash; seguinte maneira:

[![Captura de tela de um SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Agora, o usuário pode tocar em uma linha `Toast` e um alerta será exibido:

[![Captura de tela de notificação do sistema que aparece quando uma linha é retocada](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implementando um ListAdapter

`ArrayAdapter<string>`é ótimo devido à sua simplicidade, mas é extremamente limitado. No entanto, muitas vezes você tem uma coleção de entidades comerciais, em vez de apenas cadeias de caracteres que você deseja associar.
Por exemplo, se seus dados consistem em uma coleção de classes Employee, talvez você queira que a lista exiba apenas os nomes de cada funcionário. Para personalizar o comportamento de um `ListView` para controlar quais dados são exibidos, você deve implementar uma subclasse de `BaseAdapter` substituindo os quatro itens a seguir:

- **Contagem** de &ndash; Para informar ao controle quantas linhas estão nos dados.

- **GetView** &ndash; Para retornar uma exibição para cada linha, preenchida com dados.
    Esse método tem um parâmetro para que `ListView` o passe em uma linha existente não usada para reutilização.

- Getitemid &ndash; Retornar um identificador de linha (normalmente o número da linha, embora possa ser qualquer valor longo que você desejar).

- Este indexador &ndash; **[int]** retorna os dados associados a um número de linha específico.

O código de exemplo em **BasicTableAdapter/HomeScreenAdapter. cs** demonstra como subclasse `BaseAdapter`:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```


### <a name="using-a-custom-adapter"></a>Usando um adaptador personalizado

O uso do adaptador personalizado é semelhante ao interno `ArrayAdapter`, passando um `context` e o `string[]` de valores a serem exibidos:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Como este exemplo usa o mesmo layout de linha`SimpleListItem1`(), o aplicativo resultante parecerá idêntico ao exemplo anterior.


### <a name="row-view-re-use"></a>Reutilização de exibição de linha

Neste exemplo, há apenas seis itens. Como a tela pode se ajustar a oito, nenhuma reutilização de linha é necessária. No entanto, ao exibir centenas ou milhares de linhas, seria um desperdício de memória para criar centenas ou milhares de `View` objetos quando apenas oito couberem na tela por vez. Para evitar essa situação, quando uma linha desaparece da tela, sua exibição é colocada em uma fila para reutilização. À medida que o usuário rola, `ListView` as `GetView` chamadas para solicitar novas exibições &ndash; para exibir, se disponíveis, passam por uma exibição `convertView` não usada no parâmetro. Se esse valor for nulo, seu código deverá criar uma nova instância de exibição, caso contrário, você poderá redefinir as propriedades desse objeto e usá-lo novamente.

O `GetView` método deve seguir esse padrão para reutilizar exibições de linha:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Implementações de adaptador personalizado *sempre* devem reutilizar `convertView` o objeto antes de criar novos modos de exibição para garantir que eles não fiquem sem memória ao exibir listas longas.

Algumas implementações de adaptador (como `CursorAdapter`, por exemplo, `GetView` ) não têm um método, em vez `NewView` de `BindView` exigirem dois métodos diferentes e que impõem a reutilização `GetView` de linha separando as responsabilidades de em duas maneiras. Há um `CursorAdapter` exemplo mais adiante no documento.


## <a name="enabling-fast-scrolling"></a>Habilitando a rolagem rápida

A rolagem rápida ajuda o usuário a rolar por listas longas fornecendo um "identificador" adicional que atua como uma barra de rolagem para acessar diretamente uma parte da lista. Esta captura de tela mostra o identificador de rolagem rápida:

[![Captura de tela de rolagem rápida com uma alça de rolagem](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Fazer com que o identificador de rolagem rápida apareça seja tão simples quanto `FastScrollEnabled` definir a `true`Propriedade como:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Adicionando um índice de seção

Um índice de seção fornece comentários adicionais para os usuários quando eles estão rolando rapidamente por uma longa &ndash; lista, mostra qual ' seção ' eles rolaram. Para fazer com que o índice da seção apareça, a subclasse do adaptador `ISectionIndexer` deve implementar a interface para fornecer o texto do índice, dependendo das linhas que estão sendo exibidas:

[![Captura de tela de H que aparece acima da seção que começa com H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Para implementar `ISectionIndexer` , você precisa adicionar três métodos a um adaptador:

- **Getseções** &ndash; Fornece a lista completa de títulos de índice de seção que podem ser exibidos. Esse método requer uma matriz de objetos Java para que o código precise criar um `Java.Lang.Object[]` de uma coleção .net. Em nosso exemplo, ele retorna uma lista dos caracteres iniciais da lista como `Java.Lang.String` .

- **GetPositionForSection** &ndash; Retorna a primeira posição de linha para um determinado índice de seção.

- **GetSectionForPosition** &ndash; Retorna o índice de seção a ser exibido para uma determinada linha.


O arquivo `SectionIndex/HomeScreenAdapter.cs` de exemplo implementa esses métodos e alguns códigos adicionais no construtor. O construtor cria o índice da seção fazendo um loop em cada linha e extraindo o primeiro caractere do título (os itens já devem estar classificados para que isso funcione).

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

Com as estruturas de dados criadas, `ISectionIndexer` os métodos são muito simples:

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

Seus títulos de índice de seção não precisam mapear 1:1 para suas seções reais. É por isso que `GetPositionForSection` o método existe.
`GetPositionForSection`oferece a oportunidade de mapear quaisquer índices que estejam em sua lista de índice para quaisquer seções no modo de exibição de lista. Por exemplo, você pode ter um "z" no índice, mas talvez não tenha uma seção de tabela para cada letra, portanto, em vez do mapeamento "z" para 26, ele pode ser mapeado para 25 ou 24, ou qualquer índice de seção "z" deve mapear para.



## <a name="related-links"></a>Links relacionados

- [BasicTableAndroid (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
