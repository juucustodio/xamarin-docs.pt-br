---
title: Populando um ListView do Xamarin. Android com dados
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: 7ec5537345536884e2dc3da02ab54a3ca00f760e
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607965"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>Populando um ListView do Xamarin. Android com dados

Para adicionar linhas a um `ListView` você precisa adicioná-lo ao seu layout e implementar uma `IListAdapter` com métodos que o `ListView` chama para preencher a si mesmo. O Android inclui classes internas de `ListActivity` e `ArrayAdapter` que você pode usar sem definir qualquer código ou XML de layout personalizado. A classe `ListActivity` cria automaticamente uma `ListView` e expõe uma propriedade `ListAdapter` para fornecer as exibições de linha a serem exibidas por meio de um adaptador.

Os adaptadores internos usam uma ID de recurso de exibição como um parâmetro que é usado para cada linha. Você pode usar recursos internos como aqueles em `Android.Resource.Layout` para que você não precise escrever os seus próprios.

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Usando ListActivity e ArrayAdapter&lt;cadeia de caracteres&gt;

O exemplo **basictable/homescreen. cs** demonstra como usar essas classes para exibir uma `ListView` em apenas algumas linhas de código:

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
}
```

### <a name="handling-row-clicks"></a>Manipulando cliques de linha

Geralmente, um `ListView` também permitirá que o usuário toque em uma linha para executar alguma ação (como reproduzir uma música ou chamar um contato ou mostrar outra tela). Para responder aos toques do usuário, é necessário que haja mais um método implementado no `ListActivity` &ndash; `OnListItemClick` &ndash; assim:

[![Captura de tela de de um SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Agora, o usuário pode tocar em uma linha e um `Toast` alerta será exibido:

[![captura de tela do sistema de notificação que aparece quando uma linha é tocada](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)

## <a name="implementing-a-listadapter"></a>Implementando um ListAdapter

`ArrayAdapter<string>` é ótimo devido à sua simplicidade, mas é extremamente limitado. No entanto, muitas vezes você tem uma coleção de entidades comerciais, em vez de apenas cadeias de caracteres que você deseja associar.
Por exemplo, se seus dados consistem em uma coleção de classes Employee, talvez você queira que a lista exiba apenas os nomes de cada funcionário. Para personalizar o comportamento de um `ListView` para controlar quais dados são exibidos, você deve implementar uma subclasse de `BaseAdapter` substituindo os quatro itens a seguir:

- **Count** &ndash; para dizer ao controle quantas linhas estão nos dados.

- **GetView** &ndash; retornar uma exibição para cada linha, preenchida com dados.
    Esse método tem um parâmetro para a `ListView` passar uma linha existente não usada para reutilização.

- **Getitemid** &ndash; retornar um identificador de linha (normalmente o número da linha, embora possa ser qualquer valor longo que você desejar).

- Este &ndash; do indexador **[int]** para retornar os dados associados a um número de linha específico.

O código de exemplo em **BasicTableAdapter/HomeScreenAdapter. cs** demonstra como `BaseAdapter`de subclasse:

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

O uso do adaptador personalizado é semelhante ao `ArrayAdapter`interno, passando um `context` e a `string[]` de valores a serem exibidos:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Como este exemplo usa o mesmo layout de linha (`SimpleListItem1`), o aplicativo resultante parecerá idêntico ao exemplo anterior.

### <a name="row-view-re-use"></a>Reutilização de exibição de linha

Neste exemplo, há apenas seis itens. Como a tela pode se ajustar a oito, nenhuma reutilização de linha é necessária. No entanto, ao exibir centenas ou milhares de linhas, seria um desperdício de memória para criar centenas ou milhares de objetos de `View` quando apenas oito couberem na tela por vez. Para evitar essa situação, quando uma linha desaparece da tela, sua exibição é colocada em uma fila para reutilização. À medida que o usuário rola, o `ListView` chama `GetView` para solicitar novas exibições para exibir &ndash;, se disponível, ele passa uma exibição não usada no parâmetro `convertView`. Se esse valor for nulo, seu código deverá criar uma nova instância de exibição, caso contrário, você poderá redefinir as propriedades desse objeto e usá-lo novamente.

O método `GetView` deve seguir esse padrão para reutilizar exibições de linha:

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

Implementações de adaptador personalizado *sempre* devem reutilizar o objeto `convertView` antes de criar novos modos de exibição para garantir que eles não fiquem sem memória ao exibir listas longas.

Algumas implementações de adaptador (como o `CursorAdapter`) não têm um método `GetView`, em vez de exigirem dois métodos diferentes `NewView` e `BindView` que impõem a reutilização de linha separando as responsabilidades de `GetView` em dois métodos. Há um exemplo `CursorAdapter` mais adiante no documento.

## <a name="enabling-fast-scrolling"></a>Habilitando a rolagem rápida

A rolagem rápida ajuda o usuário a rolar por listas longas fornecendo um "identificador" adicional que atua como uma barra de rolagem para acessar diretamente uma parte da lista. Esta captura de tela mostra o identificador de rolagem rápida:

[![Captura de tela de de rolagem rápida com uma alça de rolagem](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Fazer com que o identificador de rolagem rápida apareça seja tão simples quanto definir a propriedade `FastScrollEnabled` como `true`:

```csharp
ListView.FastScrollEnabled = true;
```

### <a name="adding-a-section-index"></a>Adicionando um índice de seção

Um índice de seção fornece comentários adicionais para os usuários quando eles estão rolando rapidamente por uma longa lista &ndash; mostra qual ' seção ' eles rolaram. Para fazer com que o índice da seção apareça, a subclasse do adaptador deve implementar a interface `ISectionIndexer` para fornecer o texto do índice dependendo das linhas que estão sendo exibidas:

[![captura de tela de H que aparece acima da seção que começa com H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Para implementar `ISectionIndexer` você precisa adicionar três métodos a um adaptador:

- **Getseções** &ndash; fornece a lista completa de títulos de índice de seção que podem ser exibidos. Esse método requer uma matriz de objetos Java para que o código precise criar uma `Java.Lang.Object[]` de uma coleção .NET. Em nosso exemplo, ele retorna uma lista dos caracteres iniciais na lista como `Java.Lang.String`.

- **GetPositionForSection** &ndash; retorna a primeira posição de linha para um determinado índice de seção.

- **GetSectionForPosition** &ndash; retorna o índice de seção a ser exibido para uma determinada linha.

O arquivo de exemplo `SectionIndex/HomeScreenAdapter.cs` implementa esses métodos e alguns códigos adicionais no construtor. O construtor cria o índice da seção fazendo um loop em cada linha e extraindo o primeiro caractere do título (os itens já devem estar classificados para que isso funcione).

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

Com as estruturas de dados criadas, os métodos de `ISectionIndexer` são muito simples:

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

Seus títulos de índice de seção não precisam mapear 1:1 para suas seções reais. É por isso que o método `GetPositionForSection` existe.
`GetPositionForSection` oferece a oportunidade de mapear quaisquer índices que estejam em sua lista de índice para quaisquer seções no modo de exibição de lista. Por exemplo, você pode ter um "z" no índice, mas talvez não tenha uma seção de tabela para cada letra, portanto, em vez do mapeamento "z" para 26, ele pode ser mapeado para 25 ou 24, ou qualquer índice de seção "z" deve mapear para.

## <a name="related-links"></a>Links relacionados

- [BasicTableAndroid (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
