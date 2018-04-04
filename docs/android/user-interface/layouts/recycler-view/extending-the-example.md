---
title: Estendendo o exemplo RecyclerView
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 83147261a2d5458272f7e2bc105154da4308f4b0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="extending-the-recyclerview-example"></a>Estendendo o exemplo RecyclerView


O aplicativo básico descrito em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) , na verdade, não faz muito &ndash; simplesmente rola e exibe uma lista fixa de itens de fotografia para facilitar a navegação. Em aplicativos do mundo real, os usuários esperam ser capaz de interagir com o aplicativo ao tocar em itens na exibição. Além disso, a fonte de dados pode alterar (ou ser alterado pelo aplicativo) e o conteúdo da exibição deve permanecer consistente com essas alterações. As seções a seguir, você aprenderá como tratar eventos de clique de item e atualizar `RecyclerView` quando as alterações da fonte de dados subjacentes.


### <a name="handling-item-click-events"></a>Manipulação de eventos de clique de Item

Quando um usuário toca em um item de `RecyclerView`, será gerado um evento de clique de item para notificar o aplicativo do qual item foi alterado. Esse evento não é gerado por `RecyclerView` &ndash; em vez disso, o modo de exibição de item (o que é encapsulado no proprietário de exibição) detecta ajustes e reporta esses ajustes como eventos de clique.

Para ilustrar como tratar eventos de clique de item, as etapas a seguir explicam como o aplicativo de exibição de fotos básico é modificado para relatórios que fotografia tinha sido tocada pelo usuário. Quando ocorre um evento de clique do item no aplicativo de exemplo, ocorre a seguinte sequência:

1.  A fotografia `CardView` detecta o evento de clique de item e notifica o adaptador.

2.  O adaptador encaminha o evento (com informações de posição de item) para o manipulador de item da atividade.

3.  Manipulador de item da atividade responde ao evento de clique do item.

Primeiro, um membro do manipulador de eventos chamado `ItemClick` é adicionada para o `PhotoAlbumAdapter` definição da classe:

```csharp
public event EventHandler<int> ItemClick;
```

Em seguida, um método de manipulador de eventos de clique do item é adicionado ao `MainActivity`.
Este manipulador exibirá rapidamente uma notificação do sistema que indica qual item de fotografia foi alterado:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Em seguida, uma linha de código é necessário para registrar o `OnItemClick` manipulador com `PhotoAlbumAdapter`. Um bom lugar para fazer isso é imediatamente após `PhotoAlbumAdapter` é criado (em que a atividade principal `OnCreate` método):

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

`PhotoAlbumAdapter` Agora chamará `OnItemClick` quando ele recebe um evento de clique do item. A próxima etapa é criar um manipulador de adaptador que emite isso `ItemClick` eventos. O método a seguir, `OnClick`, é adicionada imediatamente após o adaptador `ItemCount` método:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Isso `OnClick` método é o adaptador *ouvinte* para eventos de clique de item de modos de exibição do item. Antes desse ouvinte pode ser registrado com um modo de exibição de item (por meio de proprietário de exibição do modo de exibição de item), o `PhotoViewHolder` construtor deve ser modificado para aceitar este método como um argumento adicional e registrar `OnClick` com o modo de exibição do item `Click` eventos.
Aqui está o `PhotoViewHolder` construtor:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

O `itemView` parâmetro contém uma referência para o `CardView` que foi alterado pelo usuário. Observe que a classe base do proprietário de exibição sabe a posição de layout do item (`CardView`) que representa (por meio de `LayoutPosition` propriedade), e essa posição é passada para o adaptador `OnClick` método quando ocorre um evento de clique do item. O adaptador `OnCreateViewHolder` método será modificado para passar o adaptador `OnClick` método de construtor do titular exibição:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Agora quando você compilar e executa o aplicativo de exibição de fotos de exemplo, ao tocar em uma foto na exibição fará com que uma notificação do sistema sejam exibidos que informa quais fotografia foi alterada:

[![Sistema de exemplo que aparece quando uma cartão de foto é tocado](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Este exemplo demonstra apenas uma abordagem para implementar manipuladores de eventos com `RecyclerView`. Outra abordagem que pode ser usada aqui é colocar os eventos no suporte de exibição e fazer com que o adaptador de assinar esses eventos. Se o aplicativo de fotos de exemplo fornecido um recurso de edição de fotos, eventos separados seria necessários para o `ImageView` e `TextView` dentro de cada `CardView`: toca `TextView` inicia um `EditView` caixa de diálogo que permite que o usuário edite a legenda e ajustes no `ImageView` inicia uma ferramenta de retoque de fotos que permite ao usuário cortar ou girar a foto. Dependendo das necessidades do seu aplicativo, você deve projetar a melhor abordagem para manipular e responder a eventos de toque.

Para demonstrar como `RecyclerView` podem ser atualizados quando as alterações do conjunto de dados, o aplicativo de exibição de fotos de exemplo podem ser modificadas para escolher uma foto na fonte de dados e troque-o com a primeira foto aleatoriamente. Primeiro, um **escolha aleatório** botão é adicionado para o aplicativo de fotos de exemplo **Main.axml** layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Em seguida, o código é adicionado ao final da atividade principal `OnCreate` método para localizar o `Random Pick` botão no layout e anexar um manipulador a ela:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

Este manipulador chama o álbum de fotos `RandomSwap` método quando o **escolha aleatório** botão é tocado. O `RandomSwap` método aleatoriamente alterna uma foto com a foto primeiro na fonte de dados, em seguida, retorna o índice da foto trocado aleatoriamente. Quando você compila e executa o aplicativo de exemplo com esse código, tocar a **escolha aleatório** botão não resulta em uma alteração de exibição porque o `RecyclerView` não está ciente de que a alteração para a fonte de dados.

Para manter `RecyclerView` atualizada depois que a fonte de dados alterações, o **escolha aleatório** clique manipulador deve ser modificado para chamar o adaptador `NotifyItemChanged` método para cada item na coleção que foi alterada (nesse caso, dois itens têm alterado: a foto a primeira e a foto trocada). Isso faz com que `RecyclerView` para atualizar sua exibição para que seja consistente com o novo estado da fonte de dados:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

Agora, quando o **escolha aleatório** botão é tocado, `RecyclerView` atualiza a exibição para mostrar que uma foto ainda mais para baixo na coleção foram trocada com a foto primeiro na coleção:

[![Captura de tela de primeira antes da troca, captura de tela de segundo após a troca](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Obviamente, `NotifyDataSetChanged` pode ter sido chamado em vez de fazer as duas chamadas para `NotifyItemChanged`, mas fazer assim forçaria `RecyclerView` atualizar toda a coleção, mesmo que apenas dois itens da coleção foi alterado. Chamando `NotifyItemChanged` é significativamente mais eficiente do que chamar `NotifyDataSetChanged`.


## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funcionalidade e partes de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
