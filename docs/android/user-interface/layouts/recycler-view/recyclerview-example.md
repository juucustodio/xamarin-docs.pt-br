---
title: Um exemplo de RecyclerView básico
description: Um aplicativo de exemplo que demonstra como usar o RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/30/2018
ms.openlocfilehash: 82423e48f844cde60bcd5e85c5bbe3cb8bc856b8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522458"
---
# <a name="a-basic-recyclerview-example"></a>Um exemplo de RecyclerView básico

Para entender como `RecyclerView` o funciona em um aplicativo típico, este tópico explora o aplicativo de exemplo [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , um exemplo de código simples `RecyclerView` que usa para exibir uma grande coleção de fotos: 

[![Duas capturas de tela de um aplicativo RecyclerView que usa CardViews para exibir fotos](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** usa [CardView](~/android/user-interface/controls/card-view.md) para implementar cada `RecyclerView` item de fotografia no layout. Por causa `RecyclerView`das vantagens de desempenho, este aplicativo de exemplo é capaz de rolar rapidamente por uma grande coleção de fotos de forma tranqüila e sem atrasos perceptíveis.


### <a name="an-example-data-source"></a>Uma fonte de dados de exemplo

Neste aplicativo de exemplo, uma fonte de dados de "álbum de fotos" ( `PhotoAlbum` representada pela classe `RecyclerView` ) fornece conteúdo de item.
`PhotoAlbum`é uma coleção de fotos com legendas; ao instanciar, você obtém uma coleção pronta de fotos de 32:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Cada instância de foto `PhotoAlbum` no expõe propriedades que permitem que você leia sua ID de recurso `PhotoID`de imagem, e sua cadeia `Caption`de caracteres de legenda,. A coleção de fotos é organizada de forma que cada foto possa ser acessada por um indexador. Por exemplo, as linhas de código a seguir acessam a ID de recurso de imagem e a legenda para a décima foto na coleção:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum`também fornece um `RandomSwap` método que você pode chamar para alternar a primeira foto da coleção com uma foto escolhida aleatoriamente em outro lugar na coleção:

```csharp
mPhotoAlbum.RandomSwap ();
```

Como os detalhes de implementação `PhotoAlbum` do não são relevantes para `RecyclerView`a compreensão `PhotoAlbum` , o código-fonte não é apresentado aqui. O código-fonte `PhotoAlbum` para está disponível em [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) no aplicativo de exemplo [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) .


### <a name="layout-and-initialization"></a>Layout e inicialização

O arquivo de layout, **Main. axml**, consiste em um `RecyclerView` único dentro `LinearLayout`de um:

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

Observe que você deve usar o nome totalmente qualificado **Android. support. v7. widget. RecyclerView** porque `RecyclerView` o é empacotado em uma biblioteca de suporte. O `OnCreate` método de `MainActivity` inicializa esse layout, cria uma instância do adaptador e prepara a fonte de dados subjacente:

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

1. Cria uma instância `PhotoAlbum` da fonte de dados.

2. Passa a fonte de dados do álbum de fotos para o construtor do `PhotoAlbumAdapter` adaptador, (que é definido mais adiante neste guia). 
   Observe que é considerada uma prática recomendada passar a fonte de dados como um parâmetro para o construtor do adaptador. 

3. Obtém o `RecyclerView` do layout.

4. Conecta o adaptador `RecyclerView` à instância chamando o `RecyclerView` `SetAdapter` método, conforme mostrado acima.

### <a name="layout-manager"></a>Gerenciador de layout

Cada item no `RecyclerView` é composto de um `CardView` que contém uma imagem de foto e legenda de foto (os detalhes são abordados na seção [Exibir titulares](#view-holder) abaixo). O predefinido `LinearLayoutManager` é usado para dispor cada `CardView` um em uma organização de rolagem vertical:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Esse código reside no método da `OnCreate` atividade principal. O construtor para o Gerenciador de layout requer um *contexto*, portanto `MainActivity` , o é `this` passado usando como visto acima.

Em vez de usar o `LinearLayoutManager`predefinido, você pode conectar um Gerenciador de layout personalizado que `CardView` exibe dois itens lado a lado, implementando um efeito de animação de folheio de página para percorrer a coleção de fotos. Posteriormente neste guia, você verá um exemplo de como modificar o layout alternando em um Gerenciador de layout diferente.

<a name="view-holder" />

### <a name="view-holder"></a>Exibir detentor

A classe do detentor do modo `PhotoViewHolder`de exibição é chamada. Cada `PhotoViewHolder` instância mantém referências `ImageView` ao e `TextView` de um item de linha associado, que é apresentado em um `CardView` como diagramado aqui:

[![Diagrama de CardView contendo ImageView e TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder`deriva de `RecyclerView.ViewHolder` e contém propriedades para armazenar referências `ImageView` para e `TextView` mostradas no layout acima.
`PhotoViewHolder`consiste em duas propriedades e um construtor:

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
Neste exemplo de código, o `PhotoViewHolder` Construtor recebe uma referência para a exibição de item pai (o `CardView`) que `PhotoViewHolder` encapsula. Observe que você sempre encaminha a exibição de item pai para o construtor base. O `PhotoViewHolder` construtor chama `FindViewById` a exibição de item pai para localizar cada uma de `ImageView` suas referências de exibição filho `TextView`e, em seguida, armazenar `Image` os `Caption` resultados nas propriedades e, respectivamente. Posteriormente, o adaptador recupera as referências de exibição dessas propriedades quando atualiza `CardView`as exibições filhas com novos dados.

Para obter mais informações `RecyclerView.ViewHolder`sobre, consulte a [referência de classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Adaptador

O adaptador carrega cada `RecyclerView` linha com dados para uma determinada fotografia. Para uma determinada fotografia na posição da linha *p*, por exemplo, o adaptador localiza os dados associados na posição *p* na fonte de dados e copia esses dados para o item de linha na posição *p* na `RecyclerView` coleção. O adaptador usa o detentor da exibição para pesquisar as referências para `ImageView` o `TextView` e nessa posição para que ele não precise chamar `FindViewById` repetidamente para essas exibições à medida que o usuário rola pela coleção de fotografia e reutiliza exibições.

No **RecyclerViewer**, uma classe de adaptador é derivada `RecyclerView.Adapter` de para `PhotoAlbumAdapter`criar:

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

O `mPhotoAlbum` membro contém a fonte de dados (o álbum de fotos) que é passada para o Construtor; o construtor copia o álbum de fotos nessa variável de membro. Os seguintes métodos `RecyclerView.Adapter` necessários são implementados:

- **`OnCreateViewHolder`** &ndash; Instancia o arquivo de layout de item e o detentor da exibição.

- **`OnBindViewHolder`** &ndash; Carrega os dados na posição especificada nas exibições cujas referências são armazenadas no espaço de exibição fornecido.

- **`ItemCount`** &ndash; Retorna o número de itens na fonte de dados.

O Gerenciador de layout chama esses métodos enquanto está posicionando itens dentro `RecyclerView`do. A implementação desses métodos é examinada nas seções a seguir.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

O Gerenciador de layout `OnCreateViewHolder` chama quando `RecyclerView` o precisa de um novo proprietário de exibição para representar um item. `OnCreateViewHolder`inplana a exibição de item do arquivo de layout da exibição e encapsula a exibição em uma nova `PhotoViewHolder` instância. O `PhotoViewHolder` Construtor localiza e armazena referências a exibições filhas no layout, conforme descrito anteriormente no [detentor da exibição](#view-holder).

Cada item de linha é representado por `CardView` um que contém `ImageView` um (para a foto) e `TextView` um (para a legenda). Esse layout reside no arquivo **PhotoCardView. axml**:

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

Esse layout representa um único item de linha no `RecyclerView`. O `OnBindViewHolder` método (descrito abaixo) copia dados da fonte de dados para o `ImageView` e `TextView` deste layout.
`OnCreateViewHolder`replana esse layout para `RecyclerView` um determinado local de foto no e instancia uma nova `PhotoViewHolder` instância (que localiza e armazena em cache as `ImageView` referências às exibições filho `TextView` e no layout associado `CardView` ):

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

A instância do detentor de exibição `vh`resultante,, é retornada ao chamador (o Gerenciador de layout).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando o Gerenciador de layout está pronto para exibir uma exibição específica na `RecyclerView`área de tela visível do, ele chama o método `OnBindViewHolder` do adaptador para preencher o item na posição de linha especificada com o conteúdo da fonte de dados. `OnBindViewHolder`Obtém as informações de foto para a posição de linha especificada (o recurso de imagem da foto e a cadeia de caracteres da legenda da foto) e copia esses dados para as exibições associadas. As exibições estão localizadas por meio de referências armazenadas no objeto de espaço para exibição ( `holder` que é passado por meio do parâmetro):

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
O adaptador carrega o recurso de imagem na exibição referenciada pela propriedade do detentor `Image` do modo de exibição e copia o texto da legenda na exibição referenciada pela propriedade do `Caption` titular do modo de exibição. Isso *associa* a exibição associada a seus dados.

Observe que `OnBindViewHolder` é o código que lida diretamente com a estrutura dos dados. Nesse caso, `OnBindViewHolder` o entende como mapear a `RecyclerView` posição do item para seu item de dados associado na fonte de dados. O mapeamento é simples nesse caso porque a posição pode ser usada como um índice de matriz no álbum de fotos; no entanto, fontes de dados mais complexas podem exigir código extra para estabelecer um mapeamento desse tipo.


#### <a name="itemcount"></a>ItemCount

O `ItemCount` método retorna o número de itens na coleção de dados. No aplicativo visualizador de fotos de exemplo, a contagem de itens é o número de fotos no álbum de fotos:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Para obter mais informações `RecyclerView.Adapter`sobre o, consulte a [referência de classe RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Juntando tudo

A implementação `RecyclerView` resultante do aplicativo de foto de exemplo consiste `MainActivity` em um código que cria a fonte de dados, o Gerenciador de layout e o adaptador. `MainActivity`cria a `mRecyclerView` instância, instancia a fonte de dados e o adaptador e conecta o Gerenciador de layout e o adaptador:

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

`PhotoViewHolder`localiza e armazena em cache as referências da exibição:

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

`PhotoAlbumAdapter`implementa as três substituições de método necessárias:

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

[![Duas capturas de tela do aplicativo de visualização de fotos com a rolagem vertical de cartões de fotos](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Se as sombras não estiverem sendo desenhadas (como visto na captura de tela acima), edite **Properties/AndroidManifest. xml** e adicione a `<application>` seguinte configuração de atributo ao elemento:

```xml
android:hardwareAccelerated="true"
```

Esse aplicativo básico só dá suporte à navegação do álbum de fotos. Ele não responde a eventos de toque de item, nem trata as alterações nos dados subjacentes. Essa funcionalidade é adicionada na [extensão do exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).




### <a name="changing-the-layoutmanager"></a>Alterando o LayoutManager

Por causa `RecyclerView`da flexibilidade, é fácil modificar o aplicativo para usar um Gerenciador de layout diferente. No exemplo a seguir, ele é modificado para exibir o álbum de fotos com um layout de grade que rola horizontalmente em vez de com um layout linear vertical. Para fazer isso, a instanciação do Gerenciador de layout é modificada `GridLayoutManager` para usar o da seguinte maneira:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Essa alteração de código substitui a `LinearLayoutManager` vertical por `GridLayoutManager` uma que apresenta uma grade composta por duas linhas que rolam na direção horizontal. Ao compilar e executar o aplicativo novamente, você verá que as fotografias são exibidas em uma grade e que a rolagem é horizontal em vez de vertical:

[![Captura de tela de exemplo do aplicativo com fotos de rolagem horizontal em uma grade](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Ao alterar apenas uma linha de código, é possível modificar o aplicativo de exibição de fotos para usar um layout diferente com comportamento diferente.
Observe que nem o código do adaptador nem o XML de layout precisavam ser modificados para alterar o estilo do layout. 

No próximo tópico, estendendo [o exemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), esse aplicativo de exemplo básico é estendido para manipular eventos de clique `RecyclerView` de item e atualizar quando a fonte de dados subjacente é alterada.



## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Partes e funcionalidades do RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
