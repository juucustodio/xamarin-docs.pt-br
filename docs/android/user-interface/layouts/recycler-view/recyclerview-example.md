---
title: Um exemplo de RecyclerView básico
description: Um aplicativo de exemplo que demonstra como usar o RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: 7266d13136dbfc858bc3c882cacb802d83c92f52
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028833"
---
# <a name="a-basic-recyclerview-example"></a>Um exemplo de RecyclerView básico

Para entender como `RecyclerView` funciona em um aplicativo típico, este tópico explora o aplicativo de exemplo [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , um exemplo de código simples que usa `RecyclerView` para exibir uma grande coleção de fotos: 

[![duas capturas de tela de um aplicativo RecyclerView que usa CardViews para exibir fotos](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** usa [CardView](~/android/user-interface/controls/card-view.md) para implementar cada item de fotografia no layout de `RecyclerView`. Devido às vantagens de desempenho do `RecyclerView`, este aplicativo de exemplo é capaz de rolar rapidamente por uma grande coleção de fotos sem atrasos.

### <a name="an-example-data-source"></a>Uma fonte de dados de exemplo

Neste aplicativo de exemplo, uma fonte de dados de "álbum de fotos" (representada pela classe `PhotoAlbum`) fornece `RecyclerView` com conteúdo de item.
`PhotoAlbum` é uma coleção de fotos com legendas; ao instanciar, você obtém uma coleção pronta de fotos de 32:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Cada instância de foto no `PhotoAlbum` expõe propriedades que permitem que você leia sua ID de recurso de imagem, `PhotoID`e sua cadeia de caracteres de legenda `Caption`. A coleção de fotos é organizada de forma que cada foto possa ser acessada por um indexador. Por exemplo, as linhas de código a seguir acessam a ID de recurso de imagem e a legenda para a décima foto na coleção:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` também fornece um método `RandomSwap` que você pode chamar para alternar a primeira foto da coleção com uma foto escolhida aleatoriamente em outro lugar na coleção:

```csharp
mPhotoAlbum.RandomSwap ();
```

Como os detalhes de implementação de `PhotoAlbum` não são relevantes para entender `RecyclerView`, o código-fonte `PhotoAlbum` não é apresentado aqui. O código-fonte para `PhotoAlbum` está disponível em [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) no aplicativo de exemplo [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) .

### <a name="layout-and-initialization"></a>Layout e inicialização

O arquivo de layout, **Main. axml**, consiste em um único `RecyclerView` dentro de um `LinearLayout`:

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

Observe que você deve usar o nome totalmente qualificado **Android. support. v7. widget. RecyclerView** porque `RecyclerView` é empacotado em uma biblioteca de suporte. O método `OnCreate` de `MainActivity` inicializa esse layout, cria uma instância do adaptador e prepara a fonte de dados subjacente:

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

1. Cria uma instância da fonte de dados `PhotoAlbum`.

2. Passa a fonte de dados do álbum de fotos para o construtor do adaptador, `PhotoAlbumAdapter` (que é definido mais adiante neste guia). 
   Observe que é considerada uma prática recomendada passar a fonte de dados como um parâmetro para o construtor do adaptador. 

3. Obtém o `RecyclerView` do layout.

4. Conecta o adaptador à instância de `RecyclerView` chamando o método `RecyclerView` `SetAdapter`, conforme mostrado acima.

### <a name="layout-manager"></a>Gerenciador de layout

Cada item na `RecyclerView` é composto de um `CardView` que contém uma imagem de foto e legenda de foto (os detalhes são abordados na seção [Exibir titulares](#view-holder) abaixo). O `LinearLayoutManager` predefinido é usado para dispor cada `CardView` em uma organização de rolagem vertical:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Esse código reside no método de `OnCreate` da atividade principal. O construtor para o Gerenciador de layout requer um *contexto*, portanto, o `MainActivity` é passado usando `this` como visto acima.

Em vez de usar o `LinearLayoutManager`predefinido, você pode conectar um Gerenciador de layout personalizado que exibe dois itens de `CardView` lado a lado, implementando um efeito de animação de folheio de página para percorrer a coleção de fotos. Posteriormente neste guia, você verá um exemplo de como modificar o layout alternando em um Gerenciador de layout diferente.

<a name="view-holder" />

### <a name="view-holder"></a>Exibir detentor

A classe do detentor do modo de exibição é chamada `PhotoViewHolder`. Cada instância de `PhotoViewHolder` contém referências à `ImageView` e à `TextView` de um item de linha associado, que é apresentado em um `CardView` como diagrama aqui:

[![diagrama de CardView contendo um ImageView e TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` deriva de `RecyclerView.ViewHolder` e contém propriedades para armazenar referências para o `ImageView` e `TextView` mostrados no layout acima.
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

Neste exemplo de código, o Construtor `PhotoViewHolder` é passado uma referência para a exibição de item pai (a `CardView`) que `PhotoViewHolder` encapsula. Observe que você sempre encaminha a exibição de item pai para o construtor base. O construtor de `PhotoViewHolder` chama `FindViewById` na exibição de item pai para localizar cada uma de suas referências de exibição filho, `ImageView` e `TextView`, armazenando os resultados nas propriedades `Image` e `Caption`, respectivamente. Posteriormente, o adaptador recupera as referências de exibição dessas propriedades quando ele atualiza as exibições filho de `CardView`com novos dados.

Para obter mais informações sobre `RecyclerView.ViewHolder`, consulte a [referência de classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

### <a name="adapter"></a>Adaptador

O adaptador carrega cada linha de `RecyclerView` com dados para uma fotografia específica. Para uma determinada fotografia na posição da linha *p*, por exemplo, o adaptador localiza os dados associados na posição *p* na fonte de dados e copia esses dados para o item de linha na posição *p* na coleção de `RecyclerView`. O adaptador usa o detentor da exibição para pesquisar as referências para o `ImageView` e `TextView` nessa posição para que ele não precise chamar repetidamente `FindViewById` para essas exibições à medida que o usuário rola pela coleção de fotografia e reutiliza exibições.

No **RecyclerViewer**, uma classe de adaptador é derivada de `RecyclerView.Adapter` para criar `PhotoAlbumAdapter`:

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

O membro `mPhotoAlbum` contém a fonte de dados (o álbum de fotos) que é passada para o Construtor; o construtor copia o álbum de fotos nessa variável de membro. Os seguintes métodos de `RecyclerView.Adapter` necessários são implementados:

- **`OnCreateViewHolder`** &ndash; instancia o arquivo de layout de item e o detentor da exibição.

- **`OnBindViewHolder`** &ndash; carrega os dados na posição especificada nas exibições cujas referências são armazenadas no espaço de exibição fornecido.

- **`ItemCount`** &ndash; retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto está posicionando itens dentro do `RecyclerView`. A implementação desses métodos é examinada nas seções a seguir.

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

O Gerenciador de layout chama `OnCreateViewHolder` quando o `RecyclerView` precisa de um novo portador de exibição para representar um item. `OnCreateViewHolder` replana a exibição de item do arquivo de layout da exibição e encapsula a exibição em uma nova instância de `PhotoViewHolder`. O construtor de `PhotoViewHolder` localiza e armazena referências a exibições filhas no layout, conforme descrito anteriormente no [detentor da exibição](#view-holder).

Cada item de linha é representado por um `CardView` que contém um `ImageView` (para a foto) e um `TextView` (para a legenda). Esse layout reside no arquivo **PhotoCardView. axml**:

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

Esse layout representa um único item de linha no `RecyclerView`. O método `OnBindViewHolder` (descrito abaixo) copia dados da fonte de dados para o `ImageView` e `TextView` desse layout.
`OnCreateViewHolder` replana esse layout para um determinado local de foto na `RecyclerView` e instancia uma nova instância de `PhotoViewHolder` (que localiza e armazena em cache as referências às exibições filho `ImageView` e `TextView` no layout `CardView` associado) :

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

A instância do detentor de exibição resultante, `vh`, é retornada ao chamador (o Gerenciador de layout).

#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando o Gerenciador de layout está pronto para exibir uma exibição específica na área de tela visível do `RecyclerView`, ele chama o método `OnBindViewHolder` do adaptador para preencher o item na posição da linha especificada com conteúdo da fonte de dados. `OnBindViewHolder` Obtém as informações de foto para a posição de linha especificada (o recurso de imagem da foto e a cadeia de caracteres da legenda da foto) e copia esses dados para as exibições associadas. As exibições são localizadas por meio de referências armazenadas no objeto de espaço para exibição (que é passado por meio do parâmetro `holder`):

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

O objeto de suporte de exibição passado deve primeiro ser convertido no tipo de suporte de exibição derivada (nesse caso, `PhotoViewHolder`) antes de ser usado.
O adaptador carrega o recurso de imagem na exibição referenciada pela propriedade `Image` do detentor da exibição e copia o texto da legenda na exibição referenciada pela propriedade `Caption` do detentor da exibição. Isso *associa* a exibição associada a seus dados.

Observe que `OnBindViewHolder` é o código que lida diretamente com a estrutura dos dados. Nesse caso, `OnBindViewHolder` entende como mapear a posição do item de `RecyclerView` para seu item de dados associado na fonte de dados. O mapeamento é simples nesse caso porque a posição pode ser usada como um índice de matriz no álbum de fotos; no entanto, fontes de dados mais complexas podem exigir código extra para estabelecer um mapeamento desse tipo.

#### <a name="itemcount"></a>ItemCount

O método `ItemCount` retorna o número de itens na coleta de dados. No aplicativo visualizador de fotos de exemplo, a contagem de itens é o número de fotos no álbum de fotos:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Para obter mais informações sobre `RecyclerView.Adapter`, consulte a [referência de classe RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

### <a name="putting-it-all-together"></a>Juntando tudo

A implementação `RecyclerView` resultante para o aplicativo de foto de exemplo consiste em `MainActivity` código que cria a fonte de dados, o Gerenciador de layout e o adaptador. `MainActivity` cria a instância de `mRecyclerView`, instancia a fonte de dados e o adaptador e conecta o Gerenciador de layout e o adaptador:

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

`PhotoViewHolder` localiza e armazena em cache as referências de exibição:

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

`PhotoAlbumAdapter` implementa as três substituições de método necessárias:

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

Quando esse código é compilado e executado, ele cria o aplicativo básico de visualização de fotos, conforme mostrado nas seguintes capturas de tela:

[![duas capturas de tela do aplicativo de visualização de fotos com a rolagem vertical dos cartões de fotos](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Se as sombras não estiverem sendo desenhadas (como visto na captura de tela acima), edite **Properties/AndroidManifest. xml** e adicione a seguinte configuração de atributo ao elemento `<application>`:

```xml
android:hardwareAccelerated="true"
```

Esse aplicativo básico só dá suporte à navegação do álbum de fotos. Ele não responde a eventos de toque de item, nem trata as alterações nos dados subjacentes. Essa funcionalidade é adicionada na [extensão do exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).

### <a name="changing-the-layoutmanager"></a>Alterando o LayoutManager

Devido à flexibilidade do `RecyclerView`, é fácil modificar o aplicativo para usar um Gerenciador de layout diferente. No exemplo a seguir, ele é modificado para exibir o álbum de fotos com um layout de grade que rola horizontalmente em vez de com um layout linear vertical. Para fazer isso, a instanciação do Gerenciador de layout é modificada para usar o `GridLayoutManager` da seguinte maneira:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Essa alteração de código substitui a `LinearLayoutManager` vertical por uma `GridLayoutManager` que apresenta uma grade composta por duas linhas que rolam na direção horizontal. Ao compilar e executar o aplicativo novamente, você verá que as fotografias são exibidas em uma grade e que a rolagem é horizontal em vez de vertical:

[![captura de tela de exemplo do aplicativo com fotos com rolagem horizontal em uma grade](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Ao alterar apenas uma linha de código, é possível modificar o aplicativo de exibição de fotos para usar um layout diferente com comportamento diferente.
Observe que nem o código do adaptador nem o XML de layout precisavam ser modificados para alterar o estilo do layout. 

No próximo tópico, [estendendo o exemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), esse aplicativo de exemplo básico é estendido para manipular eventos de clique de item e atualizar `RecyclerView` quando a fonte de dados subjacente é alterada.

## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Partes e funcionalidades do RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
