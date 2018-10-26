---
title: Preenchendo um ListView com dados
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 57c69223a01074ed15714026b7e9ec4e995808e0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103173"
---
# <a name="populating-a-listview-with-data"></a>Preenchendo um ListView com dados


## <a name="overview"></a>Visão geral

Para adicionar linhas a um `ListView` você precisa adicioná-lo ao seu layout e implementar uma `IListAdapter` com métodos que o `ListView` chamadas para preencher a mesmo. Android inclui internos `ListActivity` e `ArrayAdapter` classes que você pode usar sem definir qualquer layout personalizado XML ou código. O `ListActivity` classe cria automaticamente um `ListView` e expõe um `ListAdapter` propriedade para fornecer os modos de exibição de linha para exibir por meio de um adaptador.

Os adaptadores internos usam uma ID de recurso do modo de exibição como um parâmetro que é usado para cada linha. Você pode usar recursos internos, como aqueles em `Android.Resource.Layout` para que você não precisa gravar sua própria.


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


### <a name="handling-row-clicks"></a>Tratamento de linha clica

Normalmente, um `ListView` também permitirá que o usuário tocar em uma linha para executar alguma ação (como tocar uma música, ou chamar um contato ou que mostra outra tela). Para responder a toques de usuário deve ser um método mais implementado de `ListActivity` &ndash; `OnListItemClick` &ndash; semelhante a esta:

[![Captura de tela de um SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Agora o usuário pode tocar em uma linha e um `Toast` alerta será exibido:

[![Captura de tela de notificação do sistema que é exibida quando uma linha é atingida](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implementando um ListAdapter

`ArrayAdapter<string>` é ótimo devido à sua simplicidade, mas é extremamente limitado. No entanto, muitas vezes, você tem uma coleção de entidades de negócios, em vez de apenas cadeias de caracteres que você deseja associar.
Por exemplo, se seus dados consistem em uma coleção de classes do funcionário, você poderá a lista para exibir apenas os nomes de cada funcionário. Para personalizar o comportamento de um `ListView` para controlar quais dados são exibidos é necessário implementar uma subclasse de `BaseAdapter` substituindo os quatro itens a seguir:

-   **Contagem** &ndash; para informar o controle quantas linhas estão nos dados.

-   **GetView** &ndash; para retornar uma exibição para cada linha, preenchido com dados.
    Esse método tem um parâmetro para o `ListView` para passar em uma linha existente e não utilizada para reutilização.

-   **GetItemId** &ndash; retornar um identificador de linha (normalmente a linha de número, embora possa ser qualquer valor longo que desejar).

-   **Este [int]** indexador &ndash; para retornar os dados associados com um número de linha específico.

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


### <a name="using-a-custom-adapter"></a>Usando um adaptador personalizado

Usar o adaptador personalizado é semelhante ao internos `ArrayAdapter`, passando uma `context` e o `string[]` dos valores a serem exibidos:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Como este exemplo usa o mesmo layout de linha (`SimpleListItem1`) o aplicativo resultante parecerá idêntico ao exemplo anterior.


### <a name="row-view-re-use"></a>Reutilização de exibição linha

Neste exemplo, há apenas seis itens. Uma vez que a tela pode conter de oito, nenhuma linha usar novamente necessários. Ao exibir centenas ou milhares de linhas, no entanto, seria um desperdício de memória criar centenas ou milhares de `View` objetos quando apenas oito cabem na tela por vez. Para evitar essa situação, quando uma linha desaparecer da tela de que seu modo de exibição é colocado em uma fila para reutilização. Conforme o usuário rola, o `ListView` chamadas `GetView` para solicitar novos modos de exibição para exibir &ndash; se disponível ele passa um modo de exibição não utilizado no `convertView` parâmetro. Se esse valor for nulo, em seguida, seu código deve criar uma nova instância do modo de exibição, caso contrário, você pode novamente, defina as propriedades desse objeto e usá-lo novamente.

O `GetView` método deve seguir esse padrão para reutilizar modos de exibição de linha:

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

Implementações de adaptador personalizado devem *sempre* usar novamente o `convertView` objeto antes de criar novos modos de exibição para garantir que eles não ficar sem memória ao exibir listas longas.

Algumas implementações de adaptador (como o `CursorAdapter`) não tem um `GetView` método, em vez disso, eles exigem dois métodos diferentes `NewView` e `BindView` que impõem a reutilização de linha, separando as responsabilidades de `GetView` em duas métodos. Há um `CursorAdapter` exemplo posteriormente neste documento.


## <a name="enabling-fast-scrolling"></a>Habilitar a rolagem rápida

Rolagem rápida ajuda o usuário role por listas longas, fornecendo um adicional 'Identificador' que atua como uma barra de rolagem para acessar diretamente uma parte da lista. Esta captura de tela mostra o identificador de rolagem rápida:

[![Captura de tela de rolagem rápida com um identificador de rolagem](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Fazendo com que o identificador de rolagem rápido apareçam é tão simple quanto a configuração do `FastScrollEnabled` propriedade para `true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Adição de um índice de seção

Um índice de seção fornece comentários adicionais para os usuários quando eles são a rolagem rápida por meio de uma longa lista &ndash; mostra quais eles percorreu 'seção'. Para fazer com que o índice de seção apareça a subclasse adaptador deve implementar o `ISectionIndexer` interface para fornecer o texto de índice, dependendo das linhas que estão sendo exibidas:

[![Captura de tela de H que aparecem acima da seção que começa com H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Para implementar `ISectionIndexer` você precisa adicionar três métodos a um adaptador:

-   **GetSections** &ndash; fornece a lista completa de seção títulos de índice que poderiam ser exibidos. Esse método requer uma matriz de objetos Java para que o código precisa criar um `Java.Lang.Object[]` de uma coleção do .NET. Em nosso exemplo retorna uma lista dos caracteres iniciais na lista como `Java.Lang.String` .

-   **GetPositionForSection** &ndash; retorna a primeira posição de linha para um índice de seção determinado.

-   **GetSectionForPosition** &ndash; retorna o índice da seção a ser exibido para uma determinada linha.


O exemplo `SectionIndex/HomeScreenAdapter.cs` arquivo implementa os métodos e algum código adicional no construtor. O construtor cria o índice de seção fazendo um loop por meio de todas as linhas e extrair o primeiro caractere do título (os itens já devem ser classificados para que isso funcione).

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

Os títulos de seção índice não precisam mapear 1:1 para suas seções reais. É por isso que o `GetPositionForSection` existe um método.
`GetPositionForSection` Fornece uma oportunidade para mapear qualquer índices estão em sua lista de índice para qualquer seções estão no modo de exibição de lista. Por exemplo, você pode ter um "z" em seu índice, mas você não pode ter uma seção de tabela para cada letra, portanto, em vez de mapeamento "z" 26, ele pode mapear para 25 ou 24 ou por qualquer índice de seção "z" deve ser mapeado para.



## <a name="related-links"></a>Links relacionados

- [BasicTableAndroid (amostra)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (amostra)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll (amostra)](https://developer.xamarin.com/samples/FastScroll/)
