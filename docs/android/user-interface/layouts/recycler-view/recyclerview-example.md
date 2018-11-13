---
title: Um exemplo de RecyclerView básico
description: Um aplicativo de exemplo que demonstra como usar RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/30/2018
ms.openlocfilehash: d71c4f0f3221d06c22876329a5933273d8d6f92d
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526761"
---
# <a name="a-basic-recyclerview-example"></a>Um exemplo de RecyclerView básico

Para entender como `RecyclerView` funciona em um aplicativo típico, este tópico explora as [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicativo de exemplo, um exemplo de código simples que usa `RecyclerView` para exibir uma grande coleção de fotos: 

[![Duas capturas de tela de um aplicativo de RecyclerView que usa CardViews para exibir fotos](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** usa [widgets CardView](~/android/user-interface/controls/card-view.md) implementar cada item fotografia a `RecyclerView` layout. Por causa da `RecyclerView`do vantagens de desempenho, esse aplicativo de exemplo é capaz de rolar rapidamente por meio de uma grande coleção de fotos tranquilamente e sem atrasos notáveis.


### <a name="an-example-data-source"></a>Uma fonte de dados de exemplo

Neste aplicativo de exemplo, uma fonte de dados de "álbum de fotos" (representado pela `PhotoAlbum` classe) fornece `RecyclerView` com o conteúdo do item.
`PhotoAlbum` é uma coleção de fotos com legendas; Quando você instanciá-la, você obtém uma coleção prontas para uso de 32 fotos:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Cada instância de foto na `PhotoAlbum` expõe propriedades que permitem que você leia sua ID de recurso de imagem `PhotoID`e sua cadeia de caracteres de legenda, `Caption`. A coleção de fotos é organizada de modo que cada foto pode ser acessada por um indexador. Por exemplo, as seguintes linhas de código acessam a ID de recurso de imagem e a legenda para a foto décima na coleção:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` também fornece um `RandomSwap` método que você pode chamar para trocar a foto primeiro na coleção com uma foto escolhidos aleatoriamente em outro lugar na coleção:

```csharp
mPhotoAlbum.RandomSwap ();
```

Porque os detalhes de implementação `PhotoAlbum` não são relevantes para compreensão `RecyclerView`, o `PhotoAlbum` código-fonte não é apresentado aqui. O código-fonte `PhotoAlbum` está disponível em [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) no [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) aplicativo de exemplo.


### <a name="layout-and-initialization"></a>Layout e inicialização

O arquivo de layout **Main. axml**, consiste em uma única `RecyclerView` dentro de um `LinearLayout`:

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

Observe que você deve usar o nome totalmente qualificado **android.support.v7.widget.RecyclerView** porque `RecyclerView` é empacotado em uma biblioteca de suporte. O `OnCreate` método de `MainActivity` inicializa esse layout, instancia o adaptador e prepara a fonte de dados subjacente:

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

Esse código faz o seguinte:

1. Cria uma instância de `PhotoAlbum` fonte de dados.

2. Passa a fonte de dados do álbum de fotos para o construtor do adaptador, `PhotoAlbumAdapter` (que é definido neste documento). 
   Observe que ele é considerado uma prática recomendada para passar a fonte de dados como um parâmetro para o construtor do adaptador. 

3. Obtém o `RecyclerView` do layout.

4. Conecta-se o adaptador para o `RecyclerView` instância chamando o `RecyclerView` `SetAdapter` método conforme mostrado acima.

### <a name="layout-manager"></a>Gerenciador de layout

Cada item na `RecyclerView` é composto por um `CardView` que contém uma imagem de foto e uma legenda de foto (os detalhes são abordados na [titular de modo de exibição](#view-holder) seção abaixo). Predefinido `LinearLayoutManager` é usado para dispor cada `CardView` em uma organização de rolagem vertical:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Esse código reside em que a atividade principal `OnCreate` método. O construtor para o Gerenciador de layout requer um *contexto*, portanto, o `MainActivity` é passado usando `this` como mostrado acima.

Em vez de usar predefinida `LinearLayoutManager`, você pode conectar um Gerenciador de layout personalizado que exibe dois `CardView` itens lado a lado, Implementando um efeito de animação de folheio de página para percorrer a coleção de fotos. Mais adiante neste guia, você verá um exemplo de como modificar o layout, trocando em um Gerenciador de layout diferente.

<a name="view-holder" />

### <a name="view-holder"></a>Proprietário da exibição

A classe de proprietário da exibição é chamada `PhotoViewHolder`. Cada `PhotoViewHolder` instância contém referências para o `ImageView` e `TextView` de um item de linha associada, o que está disposto em uma `CardView` conforme diagramadas aqui:

[![Diagrama de widgets CardView contendo um ImageView e TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` deriva `RecyclerView.ViewHolder` e contém propriedades para armazenar referências para o `ImageView` e `TextView` mostrado no layout acima.
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
Neste exemplo de código, o `PhotoViewHolder` recebe uma referência para o modo de exibição do item pai (o `CardView`) que `PhotoViewHolder` encapsula. Observe que você sempre encaminha pai exibição de item para o construtor base. O `PhotoViewHolder` chamadas de construtor `FindViewById` no modo de exibição de item pai para localizar cada uma das suas referências de exibição filho, `ImageView` e `TextView`, armazenando os resultados no `Image` e `Caption` propriedades, respectivamente. O adaptador posteriormente recupera exibir referências dessas propriedades quando ele atualiza este `CardView`do modos de exibição filho com novos dados.

Para obter mais informações sobre `RecyclerView.ViewHolder`, consulte o [referência de classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Adaptador

O adaptador carrega cada `RecyclerView` linha com os dados de uma fotografia específica. Para uma determinada fotografia na posição de linha *P*, por exemplo, o adaptador localiza os dados associados na posição *P* dentro da fonte de dados e copia esses dados para a linha de item na posição *P* no `RecyclerView` coleção. O adaptador usa o detentor de modo de exibição para pesquisar as referências para o `ImageView` e `TextView` nessa posição, portanto, não precisa chamar repetidamente `FindViewById` para os modos de exibição que o usuário rola por meio da coleção de fotografia e reutiliza os modos de exibição.

Na **RecyclerViewer**, uma classe de adaptador deriva `RecyclerView.Adapter` para criar `PhotoAlbumAdapter`:

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

-   **`OnCreateViewHolder`** &ndash; Instancia o detentor de arquivo e o modo de exibição de layout do item.

-   **`OnBindViewHolder`** &ndash; Carrega os dados na posição especificada para os modos de exibição cujas referências são armazenadas no proprietário de determinado modo de exibição.

-   **`ItemCount`** &ndash; Retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto ele está posicionando itens dentro de `RecyclerView`. A implementação desses métodos é examinada nas seções a seguir.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

As chamadas de Gerenciador de layout `OnCreateViewHolder` quando o `RecyclerView` precisa de um novo proprietário de exibição para representar um item. `OnCreateViewHolder` descompacta o modo de exibição de item de arquivo de layout do modo de exibição e encapsula o modo de exibição em uma nova `PhotoViewHolder` instância. O `PhotoViewHolder` construtor localiza e armazena as referências a exibições filho no layout, conforme descrito anteriormente [titular de exibição](#view-holder).

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

Esse layout representa um item de linha única no `RecyclerView`. O `OnBindViewHolder` método (descrito abaixo) copia dados da fonte de dados para o `ImageView` e `TextView` desse layout.
`OnCreateViewHolder` infla esse layout para um local de dada foto na `RecyclerView` e cria uma nova `PhotoViewHolder` instância (que localiza e armazena em cache as referências para o `ImageView` e `TextView` exibições filho associado ao `CardView` layout):

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

A instância de titular de modo de exibição resultante, `vh`, é retornado ao chamador (o Gerenciador de layout).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando o Gerenciador de layout está pronto para exibir uma exibição específica na `RecyclerView`da área visível da tela, ele chama o adaptador `OnBindViewHolder` método para preencher o item na posição de linha especificada com o conteúdo da fonte de dados. `OnBindViewHolder` Obtém as informações de foto para a posição da linha especificada (recurso de imagem da foto e a cadeia de caracteres para a legenda da foto) e copia esses dados para os modos de exibição associados. Modos de exibição são localizados por meio de referências armazenadas no objeto de proprietário de exibição (que é passado por meio de `holder` parâmetro):

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

O objeto de proprietário passados em modo de exibição deve primeiro ser convertido no tipo de proprietário de exibição derivadas (nesse caso, `PhotoViewHolder`) antes de ser usada.
O adaptador carrega o recurso de imagem para a exibição referenciada por do titular do modo de exibição `Image` propriedade e ele copia o texto da legenda para a exibição referenciada por do titular do modo de exibição `Caption` propriedade. Isso *associa* o modo de exibição associado com seus dados.

Observe que `OnBindViewHolder` é o código que lida diretamente com a estrutura dos dados. Nesse caso, `OnBindViewHolder` entende como mapear o `RecyclerView` posição ao seu item de dados associados na fonte de dados de item. O mapeamento é direto nesse caso porque a posição pode ser usada como um índice de matriz no álbum de fotos; No entanto, as fontes de dados mais complexas podem exigir código extra para estabelecer tal mapeamento.


#### <a name="itemcount"></a>ItemCount

O `ItemCount` método retorna o número de itens na coleção de dados. No aplicativo de Visualizador de fotos do exemplo, a contagem de itens é o número de fotos do álbum de fotos:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Para obter mais informações sobre `RecyclerView.Adapter`, consulte o [referência de classe RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Juntando as peças

Resultante `RecyclerView` consiste em implementação para o aplicativo de fotos de exemplo `MainActivity` código que cria a fonte de dados, o Gerenciador de layout e o adaptador. `MainActivity` cria o `mRecyclerView` instância, cria uma instância de fonte de dados e o adaptador e plug-ins no Gerenciador de layout e do adaptador:

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

`PhotoViewHolder` Localiza e armazena em cache as referências de modo de exibição:

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

`PhotoAlbumAdapter` implementa as substituições de método necessários três:

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

[![Duas capturas de tela do aplicativo com os cartões de foto de rolagem vertical de exibição de fotos](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Se não estão sendo desenhadas sombras (como visto na captura de tela acima), edite **androidmanifest** e adicione a seguinte configuração de atributo para o `<application>` elemento:

```xml
android:hardwareAccelerated="true"
```

Esse aplicativo básico somente oferece suporte à navegação do álbum de fotografias. Ele não responder a eventos de toque de item, nem processa alterações nos dados subjacentes. Essa funcionalidade é adicionada no [estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).




### <a name="changing-the-layoutmanager"></a>Alterando o LayoutManager

Por causa da `RecyclerView`da flexibilidade, é fácil modificar o aplicativo para usar um Gerenciador de layout diferente. No exemplo a seguir, ele é modificado para exibir o álbum de fotos com um layout de grade que rola horizontalmente em vez de com um layout linear vertical. Para fazer isso, a instanciação do Gerenciador de layout é modificada para usar o `GridLayoutManager` da seguinte maneira:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Essa alteração de código substitui o vertical `LinearLayoutManager` com um `GridLayoutManager` que apresenta uma grade composta de duas linhas a rolar na direção horizontal. Quando você compila e executa o aplicativo novamente, você verá que as fotografias são exibidas em uma grade e que a rolagem é horizontal, em vez de vertical:

[![Captura de tela de exemplo de aplicativo com fotos rolar horizontalmente em uma grade](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Alterando apenas uma linha de código, é possível modificar o aplicativo de exibição de fotos para usar um layout diferente com um comportamento diferente.
Observe que o código do adaptador nem o XML de layout tinha que ser modificado para alterar o estilo de layout. 

No próximo tópico [estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), esse aplicativo de exemplo básico é estendido para lidar com eventos de clique de item e atualizar `RecyclerView` quando as alterações da fonte de dados subjacentes.



## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView partes e funcionalidade](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
