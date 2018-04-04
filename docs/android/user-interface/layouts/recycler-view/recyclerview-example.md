---
title: Um exemplo de RecyclerView básico
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d5be838dcb5530ece76c3701d8fce10403622e8d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="a-basic-recyclerview-example"></a>Um exemplo de RecyclerView básico


Para entender como `RecyclerView` funciona em um aplicativo típico, este tópico explora o [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicativo de exemplo, um exemplo de código simples que usa `RecyclerView` para exibir uma grande coleção de fotos: 

[![Duas capturas de tela de um aplicativo de RecyclerView que usa CardViews para exibir fotos](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** usa [CardView](~/android/user-interface/controls/card-view.md) para implementar cada item fotografia de `RecyclerView` layout. Porque `RecyclerView`do vantagens de desempenho, este aplicativo de exemplo é capaz de percorrer uma grande coleção de fotos rapidamente sem problemas e sem atrasos notáveis.


### <a name="an-example-data-source"></a>Uma fonte de dados de exemplo

Neste aplicativo de exemplo, uma fonte de dados de "álbum" (representado pelo `PhotoAlbum` classe) fornece `RecyclerView` com conteúdo do item.
`PhotoAlbum` é uma coleção de fotos com legendas; Quando você instanciá-la, você obtém um conjunto pronto de 32 fotos:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Cada instância de foto em `PhotoAlbum` expõe propriedades que permitem que você leia o sua ID de recurso de imagem, `PhotoID`e sua cadeia de caracteres de legenda, `Caption`. A coleção de fotos é organizada de modo que cada foto pode ser acessada por um indexador. Por exemplo, as seguintes linhas de código acessam a ID de recurso de imagem e a legenda para a foto décima na coleção:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` também fornece um `RandomSwap` método que você pode chamar para trocar a foto primeiro na coleção com uma foto escolhido aleatoriamente em outro lugar na coleção:

```csharp
mPhotoAlbum.RandomSwap ();
```

Como os detalhes da implementação `PhotoAlbum` não são relevantes para compreensão `RecyclerView`, o `PhotoAlbum` código-fonte não é apresentado aqui. O código-fonte para `PhotoAlbum` está disponível em [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) no [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicativo de exemplo.


### <a name="layout-and-initialization"></a>Layout e inicialização

O arquivo de layout, **Main.axml**, consiste em um único `RecyclerView` dentro de um `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Observe que você deve usar o nome totalmente qualificado **android.support.v7.widget.RecyclerView** porque `RecyclerView` é empacotado em uma biblioteca de suporte. O `OnCreate` método `MainActivity` inicializa este layout, instancia o adaptador e prepara a fonte de dados:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

Este código faz o seguinte:

1. Instancia o `PhotoAlbum` fonte de dados.

2. Passa a fonte de dados do álbum de fotos para o construtor do adaptador, `PhotoAlbumAdapter` (que é definido mais adiante neste guia). 
   Observe que ele é considerado uma prática recomendada para passar a fonte de dados como um parâmetro para o construtor do adaptador. 

3. Obtém o `RecyclerView` do layout.

4. Conecta-se o adaptador para o `RecyclerView` instância chamando o `RecyclerView` `SetAdapter` método como mostrado acima.

### <a name="layout-manager"></a>Gerenciador de layout

Cada item no `RecyclerView` é composto de um `CardView` que contém uma foto e uma legenda de foto (detalhes são abordados a [proprietário de exibição](#view-holder) seção abaixo). Predefinida `LinearLayoutManager` é usada para formatar cada `CardView` em uma organização de rolagem vertical:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Esse código reside na atividade principal `OnCreate` método. O construtor para o Gerenciador de layout requer um *contexto*, portanto, o `MainActivity` é passado usando `this` como mostrado acima.

Em vez de usar o a predefind `LinearLayoutManager`, você pode conectar um Gerenciador de layout personalizado que exibe dois `CardView` itens lado a lado, Implementando um efeito de animação folheio de página para percorrer a coleção de fotos. Posteriormente neste guia, você verá um exemplo de como modificar o layout, trocando em um Gerenciador de layout diferente.

<a name="view-holder" />

### <a name="view-holder"></a>Proprietário de exibição

A classe de proprietário de exibição é chamada `PhotoViewHolder`. Cada `PhotoViewHolder` instância contém referências para o `ImageView` e `TextView` de um item de linha associada, que é apresentado em um `CardView` como diagramado aqui:

[![Diagrama de CardView contendo um ImageView e TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` deriva `RecyclerView.ViewHolder` e contém propriedades para armazenar as referências para o `ImageView` e `TextView` mostrado no layout acima.
`PhotoViewHolder` consiste em duas propriedades e um construtor:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```
Neste exemplo de código, o `PhotoViewHolder` recebe uma referência para o modo de exibição do item pai (o `CardView`) que `PhotoViewHolder` encapsula. Observe que você sempre encaminhar pai exibição de item para o construtor base. O `PhotoViewHolder` chamadas de construtor `FindViewById` na exibição de item pai para localizar cada uma de suas referências de exibição filho, `ImageView` e `TextView`, armazenando os resultados no `Image` e `Caption` propriedades, respectivamente. O adaptador posteriormente recupera exibir referências dessas propriedades quando ela atualiza esse `CardView`do exibições filho com novos dados.

Para obter mais informações sobre `RecyclerView.ViewHolder`, consulte o [referência de classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Adaptador

O adaptador carrega cada `RecyclerView` linha com os dados de uma fotografia específica. Para uma determinada fotografia na posição de linha *P*, por exemplo, o adaptador localiza os dados associados na posição *P* dentro da fonte de dados e copia esses dados para a linha do item na posição *P* no `RecyclerView` coleção. O adaptador usa o proprietário de exibição para pesquisar as referências para o `ImageView` e `TextView` naquela posição, portanto, não precisa chamar repetidamente `FindViewById` para as exibições que o usuário rola através da coleção de fotografia e reutiliza os modos de exibição.

Em **RecyclerViewer**, uma classe de adaptador é derivada do `RecyclerView.Adapter` criar `PhotoAlbumAdapter`:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

O `mPhotoAlbum` membro contém a fonte de dados (o álbum de fotos) que é passada para o construtor; o construtor copia o álbum de fotos para essa variável de membro. O seguinte necessário `RecyclerView.Adapter` métodos são implementados:

-   **`OnCreateViewHolder`** &ndash; Instancia o proprietário de arquivo e o modo de exibição de layout do item.

-   **`OnBindViewHolder`** &ndash; Carrega os dados na posição especificada para os modos de exibição cujas referências são armazenadas no proprietário de exibição fornecido.

-   **`ItemCount`** &ndash; Retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto o posicionamento de itens dentro de `RecyclerView`. A implementação desses métodos é examinada nas seções a seguir.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

As chamadas de Gerenciador de layout `OnCreateViewHolder` quando o `RecyclerView` precisa de um novo proprietário de exibição para representar um item. `OnCreateViewHolder` inflação o modo de exibição de item de arquivo de layout do modo de exibição e ajusta a exibição em uma nova `PhotoViewHolder` instância. O `PhotoViewHolder` construtor localiza e armazena as referências a exibições filho no layout, conforme descrito anteriormente na [exibição proprietário](#view-holder).

Cada item de linha é representado por um `CardView` que contém um `ImageView` (para a foto) e um `TextView` (para a legenda). Esse layout reside no arquivo **PhotoCardView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

Esse layout representa um item de linha no `RecyclerView`. O `OnBindViewHolder` método (descrito abaixo) copia os dados da fonte de dados para o `ImageView` e `TextView` este layout.
`OnCreateViewHolder` inflação este layout para um local de determinada foto no `RecyclerView` e cria um novo `PhotoViewHolder` instância (que localiza e armazena em cache as referências para o `ImageView` e `TextView` exibições filho associada `CardView` layout):

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

A instância de proprietário de exibição resultante, `vh`, é retornado ao chamador (o Gerenciador de layout).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando o Gerenciador de layout está pronto para exibir uma determinada exibição o `RecyclerView`da área visível da tela, ele chama o adaptador `OnBindViewHolder` método para preencher o item na posição de linha especificado com o conteúdo da fonte de dados. `OnBindViewHolder` Obtém informações da imagem para a posição de linha especificada (recurso de imagem da foto e a cadeia de caracteres para a legenda da foto) e copia esses dados para os modos de exibição associados. Modos de exibição são localizados por meio de referências armazenadas no objeto de proprietário de exibição (que é passado por meio de `holder` parâmetro):

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

O objeto de portador passado em modo de exibição deve primeiro ser convertido no tipo de proprietário de exibição derivadas (nesse caso, `PhotoViewHolder`) antes de ser usada.
O adaptador carrega o recurso de imagem para a exibição referenciada do proprietário da exibição `Image` propriedade e copia o texto da legenda para a exibição referenciada do proprietário da exibição `Caption` propriedade. Isso *associa* o modo de exibição associado com seus dados.

Observe que `OnBindViewHolder` é o código que trabalha diretamente com a estrutura dos dados. Nesse caso, `OnBindViewHolder` entende como mapear o `RecyclerView` posição para o item de dados associados na fonte de dados do item. O mapeamento é simples neste caso porque a posição pode ser usada como um índice de matriz no álbum de fotos; No entanto, as fontes de dados mais complexas podem exigir código extra para estabelecer um mapeamento desse tipo.


#### <a name="itemcount"></a>ItemCount

O `ItemCount` método retorna o número de itens na coleção de dados. No aplicativo de Visualizador de fotos do exemplo, a contagem de itens é o número de fotos no álbum de fotos:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Para obter mais informações sobre `RecyclerView.Adapter`, consulte o [referência de classe RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Juntando tudo

Resultante `RecyclerView` consiste de implementação para o exemplo de aplicativo de fotos `MainActivity` código que cria a fonte de dados, o Gerenciador de layout e o adaptador. `MainActivity` cria o `mRecyclerView` instância, cria a fonte de dados e o adaptador e conecta-se no Gerenciador de layout e do adaptador:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder` Localiza e armazena em cache as referências de exibição:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter` implementa as substituições de método necessário três:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

Quando esse código é compilado e executado, ele cria a foto básica exibindo o aplicativo conforme mostrado nas capturas de tela seguir:

[![Duas capturas de tela do aplicativo com cartões de foto de rolagem vertical de exibição de fotos](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Este aplicativo básico somente oferece suporte à navegação de álbum de fotos. Ele não responde a eventos de toque de item nem processa as alterações nos dados subjacentes. Essa funcionalidade é adicionada no [estender o exemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).


### <a name="changing-the-layoutmanager"></a>Alterando o LayoutManager

Porque `RecyclerView`da flexibilidade, é fácil modificar o aplicativo para usar um Gerenciador de layout diferente. No exemplo a seguir, ele é modificado para exibir o álbum de fotos com um layout de grade rola horizontalmente em vez de com um layout linear vertical. Para fazer isso, a instanciação do Gerenciador de layout é modificada para usar o `GridLayoutManager` da seguinte maneira:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Essa alteração de código substitui vertical `LinearLayoutManager` com um `GridLayoutManager` que apresenta uma grade composta de duas linhas rolagem na direção horizontal. Quando você compila e executa o aplicativo novamente, você verá que as fotografias são exibidas em uma grade e que a rolagem é horizontal em vez de vertical:

[![Captura de tela de exemplo de aplicativo com fotos rolar horizontalmente em uma grade](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Alterando apenas uma linha de código, é possível modificar o aplicativo de exibição de foto para usar um layout diferente com comportamento diferente.
Observe que o código do adaptador, nem o layout do XML tinha que ser modificados para alterar o estilo de layout. 

No próximo tópico, [estender o exemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), este aplicativo de exemplo básico é estendido para lidar com eventos de clique de item e atualizar `RecyclerView` quando as alterações da fonte de dados subjacentes.



## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funcionalidade e partes de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estendendo o exemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
