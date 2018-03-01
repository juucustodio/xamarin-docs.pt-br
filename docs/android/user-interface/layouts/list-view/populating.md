---
title: Preenchendo uma ListView com dados
ms.topic: article
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 74d8533d0a757a307d88125701a482dfefd5eec2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="populating-a-listview-with-data"></a>Preenchendo uma ListView com dados

<a name="overview" />

## <a name="overview"></a>Visão geral

Para adicionar linhas a uma `ListView` necessário adicioná-lo ao seu layout e implementar um `IListAdapter` com métodos que o `ListView` chamadas para preencher a mesmo. Android inclui interno `ListActivity` e `ArrayAdapter` classes que podem ser usados sem definir qualquer layout personalizado XML ou código. O `ListActivity` classe cria automaticamente um `ListView` e expõe um `ListAdapter` propriedade para fornecer os modos de exibição de linha para exibir por meio de um adaptador.

Os adaptadores internos usam uma ID de recurso do modo de exibição como um parâmetro que é usado para cada linha. Você pode usar recursos internos, como aqueles em `Android.Resource.Layout` para que você não precisa escrever suas próprias.

<a name="Using_ListActivity_and_ArrayAdapterString" />

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Usando ListActivity e ArrayAdapter&lt;cadeia de caracteres&gt;

O exemplo **BasicTable/HomeScreen.cs** demonstra como usar essas classes para exibir um `ListView` em apenas algumas linhas de código:

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

<a name="Handling_Row_Clicks" />

### <a name="handling-row-clicks"></a>Tratamento de linha clica

Geralmente um `ListView` também permitirá que o usuário tocar em uma linha para executar alguma ação (como executar uma música, chamar um contato ou mostrando outra tela). Para responder a ajustes de usuário deve ser um método mais implementado no `ListActivity` &ndash; `OnListItemClick` &ndash; esta aparência:

[![Captura de tela de um SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Agora o usuário pode tocar em uma linha e um `Toast` alerta será exibido:

[![Captura de tela de notificação do sistema que é exibida quando uma linha é alterada](populating-images/basictable2.png)](populating-images/basictable2.png)

<a name="Implementing_a_ListAdapter" />

## <a name="implementing-a-listadapter"></a>Implementando um ListAdapter

`ArrayAdapter<string>` é ótimo devido à sua simplicidade, mas é extremamente limitada. No entanto, muitas vezes você tem uma coleção de entidades de negócios, em vez de apenas cadeias de caracteres que você deseja vincular.
Por exemplo, se seus dados consistem em uma coleção de classes do funcionário, talvez seja a lista para exibir apenas os nomes de cada funcionário. Para personalizar o comportamento de um `ListView` para controlar quais dados são exibidos deve implementar uma subclasse de `BaseAdapter` substituindo quatro itens a seguir:

-   **Contagem de** &ndash; para informar o controle quantas linhas estão nos dados.

-   **GetView** &ndash; para retornar uma exibição para cada linha preenchida com dados.
    Esse método tem um parâmetro para o `ListView` para passar em uma linha existente, não utilizada para reutilização.

-   **GetItemId** &ndash; retornar um identificador de linha (normalmente a linha número, embora ele pode ser qualquer valor longo que desejar).

-   **Este [int]** indexador &ndash; para retornar os dados associados a um número de linha específico.

O código de exemplo **BasicTableAdapter/HomeScreenAdapter.cs** demonstra como subclasse `BaseAdapter`:

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

<a name="Using_a_Custom_Adapter" />

### <a name="using-a-custom-adapter"></a>Usando um adaptador padrão

Usar o adaptador personalizado é semelhante a interna `ArrayAdapter`, passando um `context` e `string[]` de valores a serem exibidos:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Como este exemplo usa o mesmo layout de linha (`SimpleListItem1`) o aplicativo resultante terá a mesma aparência ao exemplo anterior.

<a name="Row_View_Re-Use" />

### <a name="row-view-re-use"></a>Reutilização do modo de exibição linha

Neste exemplo, há apenas seis itens. Desde que a tela pode ser ajustadas oito, nenhuma linha reutilizar necessária. Ao exibir centenas ou milhares de linhas, no entanto, seria uma perda de memória para criar centenas ou milhares de `View` objetos quando apenas oito cabem na tela de cada vez. Para evitar essa situação, quando uma linha desaparece da tela de que seu modo de exibição é colocado em uma fila para reutilização. Conforme o usuário rola, o `ListView` chamadas `GetView` para solicitar novos modos de exibição para exibir &ndash; se disponível passa uma exibição não utilizada no `convertView` parâmetro. Se esse valor for nulo, seu código deve criar uma nova instância de exibição, caso contrário, você pode definir novamente as propriedades desse objeto e usá-lo novamente.

O `GetView` método deve seguir esse padrão para usar novamente as exibições de linha:

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

Implementações de adaptador personalizado devem *sempre* usar novamente o `convertView` objeto antes de criar novos modos de exibição para garantir que não são executados sem memória ao exibir listas longas.

Algumas implementações de adaptador (como o `CursorAdapter`) não tem um `GetView` método, em vez disso, eles exigem dois métodos diferentes `NewView` e `BindView` que impõe a reutilização de linha, separando as responsabilidades de `GetView` em duas métodos. Há um `CursorAdapter` exemplo posteriormente neste documento.

<a name="Enabling_Fast_Scrolling" />

## <a name="enabling-fast-scrolling"></a>Habilitar a rolagem rápida

Rolagem rápida ajuda o usuário rolar por listas longas, fornecendo um adicional 'Identificador' que atua como uma barra de rolagem para acessar diretamente uma parte da lista. Esta captura de tela mostra o identificador de rolagem rápida:

[![Captura de tela de rolagem fast com um identificador de rolagem](populating-images/fastscroll.png)](populating-images/fastscroll.png)

Fazendo com que o identificador de rolagem rápido apareça é tão simple quanto a configuração de `FastScrollEnabled` propriedade `true`:

```csharp
ListView.FastScrollEnabled = true;
```

<a name="Adding_a_Section_Index" />

### <a name="adding-a-section-index"></a>Adicionar um índice de seção

Um índice de seção fornece comentários adicionais para os usuários quando eles estiverem fast-rolar por uma longa lista &ndash; mostra quais eles percorreu 'seção'. Para fazer com que o índice de seção apareça a subclasse adaptador deve implementar o `ISectionIndexer` interface para fornecer o texto de índice, dependendo das linhas que está sendo exibido:

[![Captura de tela de H que aparece acima da seção que começa com H](populating-images/sectionindex.png)](populating-images/sectionindex.png)

Para implementar `ISectionIndexer` você precisa adicionar três métodos para um adaptador:

-   **GetSections** &ndash; fornece a lista completa de seção títulos de índice que podem ser exibidos. Esse método requer uma matriz de objetos Java para o código precisa para criar um `Java.Lang.Object[]` de uma coleção de .NET. Em nosso exemplo retorna uma lista dos caracteres inicias na lista como `Java.Lang.String` .

-   **GetPositionForSection** &ndash; retorna a primeira posição de linha para um índice de seção especificado.

-   **GetSectionForPosition** &ndash; retorna o índice de seção a ser exibido para uma determinada linha.


O exemplo `SectionIndex/HomeScreenAdapter.cs` arquivo implementa os métodos e algum código adicional no construtor. O construtor cria o índice de seção loop através de cada linha e extrair o primeiro caractere do título (os itens já devem ser classificados para este trabalho).

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

Com as estruturas de dados criadas, o `ISectionIndexer` métodos são muito simples:

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

Os títulos de índice de seção não é necessário mapear 1:1 para as seções reais. É por isso que o `GetPositionForSection` método existe.
`GetPositionForSection` Fornece uma oportunidade para mapear quaisquer índices estão em sua lista de índice para qualquer seções estão no modo de exibição de lista. Por exemplo, você pode ter um "z" no índice, mas você não pode ter uma seção de tabela para todas as letras, portanto, em vez de mapeamento de "z" 26, ele pode ser mapeada para 25 ou 24 ou qualquer índice de seção "z" deve ser mapeado para.



## <a name="related-links"></a>Links relacionados

- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (exemplo)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll (exemplo)](https://developer.xamarin.com/samples/FastScroll/)
