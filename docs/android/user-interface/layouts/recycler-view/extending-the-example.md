---
title: Estendendo o exemplo de RecyclerView
description: Adicionando manipuladores de eventos de clique de item para o aplicativo de exemplo de RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: eca0f58a470228ce8e6331defe88c1ef727cef57
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035981"
---
# <a name="extending-the-recyclerview-example"></a>Estendendo o exemplo de RecyclerView


O aplicativo básico descrito em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) , na verdade, não faz muita coisa &ndash; simplesmente rola e exibe uma lista fixa de itens de fotografia para facilitar a navegação. Em aplicativos do mundo real, os usuários esperam ser capaz de interagir com o aplicativo tocando em itens na exibição. Além disso, a fonte de dados subjacente pode alterar (ou ser alterada pelo aplicativo) e o conteúdo da exibição deve permanecer consistente com essas alterações. Nas seções a seguir, você aprenderá como lidar com eventos de clique de item e atualizar `RecyclerView` quando as alterações da fonte de dados subjacentes.


### <a name="handling-item-click-events"></a>Manipulação de eventos de clique de Item

Quando um usuário toca em um item a `RecyclerView`, um evento de clique de item é gerado para notificar o aplicativo no qual o item foi tocado. Esse evento não é gerado por `RecyclerView` &ndash; em vez disso, o modo de exibição de item (que é encapsulado em titular da exibição) detecta toques e reporta esses toques como eventos de clique.

Para ilustrar como manipular eventos de clique de item, as etapas a seguir explicam como o aplicativo de exibição de fotos básico é modificado para relatório quais fotografia tinha sido tocada pelo usuário. Quando ocorre um evento de clique do item no aplicativo de exemplo, ocorre a seguinte sequência:

1.  A fotografia `CardView` detecta o evento de clique no item e notifica o adaptador.

2.  O adaptador de encaminha o evento (com informações de posição do item) para o manipulador de clique de item de atividade.

3.  Manipulador de clique de item de atividade responde ao evento de clique do item.

Chamado pela primeira vez, de um membro do manipulador de eventos `ItemClick` é adicionado para o `PhotoAlbumAdapter` definição de classe:

```csharp
public event EventHandler<int> ItemClick;
```

Em seguida, um método de manipulador de eventos de clique do item é adicionado à `MainActivity`.
Esse manipulador exibirá rapidamente uma notificação do sistema que indica qual item de fotografia foi tocado:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Em seguida, uma linha de código é necessário para registrar o `OnItemClick` manipulador com `PhotoAlbumAdapter`. Um bom lugar para fazer isso é imediatamente após `PhotoAlbumAdapter` é criado: 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

Neste exemplo básico, o registro do manipulador é feita na atividade principal `OnCreate` método, mas um aplicativo de produção pode registrar o manipulador no `OnResume` e cancelar o registro no `OnPause` &ndash; consulte [ciclo de vida de atividade ](~/android/app-fundamentals/activity-lifecycle/index.md) para obter mais informações.

`PhotoAlbumAdapter` agora irá chamar `OnItemClick` quando ele recebe um evento de clique no item. A próxima etapa é criar um manipulador no adaptador que emite isso `ItemClick` eventos. O seguinte método, `OnClick`, é adicionada logo após o adaptador `ItemCount` método:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Isso `OnClick` método é o adaptador *ouvinte* para eventos de clique de item de modos de exibição do item. Antes que este ouvinte pode ser registrado com uma exibição de item (por meio do titular do modo de exibição do modo de exibição de item), o `PhotoViewHolder` construtor deve ser modificada para aceitar esse método como um argumento adicional e registre `OnClick` com o modo de exibição do item `Click` eventos.
Aqui está o modificado `PhotoViewHolder` construtor:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

O `itemView` parâmetro contém uma referência para o `CardView` que foi tocado pelo usuário. Observe que a classe base do modo de exibição titular sabe a posição de layout do item (`CardView`) que ele representa (por meio de `LayoutPosition` propriedade), e essa posição é passada para o adaptador `OnClick` método quando um evento de clique no item ocorre. O adaptador `OnCreateViewHolder` método é modificado para passar o adaptador `OnClick` método ao construtor do titular exibição:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Agora quando você compila e executa o aplicativo de exibição de fotos de exemplo, tocando em uma foto na exibição fará com que uma notificação do sistema apareçam que relata qual fotografia foi tocada:

[![Toast de exemplo que aparece quando uma cartão de foto é tocado](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Este exemplo demonstra apenas uma abordagem para a implementação de manipuladores de eventos com `RecyclerView`. Outra abordagem que pode ser usada aqui é colocar eventos no proprietário da exibição e têm o adaptador de assinar esses eventos. Se o aplicativo de fotos de exemplo fornecido a um recurso de edição de fotos, eventos separados seria necessários para o `ImageView` e o `TextView` dentro de cada `CardView`: aborda o `TextView` iniciaria uma `EditView` caixa de diálogo que permite que o usuário edite a legenda e toca no `ImageView` iniciaria uma ferramenta Retocar fotos que permite ao usuário recortar ou gire a foto. Dependendo das necessidades do seu aplicativo, você deve projetar a melhor abordagem para manipular e respondendo a eventos de toque.

Para demonstrar como `RecyclerView` pode ser atualizada quando as alterações do conjunto de dados, o aplicativo de exibição de fotos de exemplo podem ser modificadas para escolher uma foto na fonte de dados aleatoriamente e trocá-lo com a foto primeiro. Primeiro, uma **escolher aleatório** botão é adicionado para o aplicativo de fotos de exemplo **Main. axml** layout:

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

Em seguida, o código é adicionado ao final da atividade principal `OnCreate` método para localizar o `Random Pick` botão no layout e anexar um manipulador para ele:

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

Esse manipulador chama o álbum de fotos `RandomSwap` método quando o **escolha aleatória** botão é tocado. O `RandomSwap` método aleatoriamente alterna uma foto com a primeira foto na fonte de dados, em seguida, retorna o índice da foto trocado aleatoriamente. Quando você compila e executar o aplicativo de exemplo com esse código, tocando o **escolher aleatório** botão não resulta em uma alteração de exibição porque o `RecyclerView` não está ciente da mudança para a fonte de dados.

Para manter `RecyclerView` atualizado depois que a fonte de dados alterações, o **escolha aleatória** clique manipulador deve ser modificado para chamar o adaptador `NotifyItemChanged` método para cada item na coleção que foi alterada (nesse caso, dois itens têm alterado: a foto de primeira e a foto trocada). Isso faz com que `RecyclerView` para atualizar sua exibição para que ele seja consistente com o novo estado da fonte de dados:

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

Agora, quando o **escolher aleatório** botão é tocado, `RecyclerView` atualiza a exibição para mostrar que uma foto ainda mais para baixo na coleção foi trocada com a primeira foto na coleção:

[![Primeira captura de tela antes da troca, a segunda captura de tela após a troca](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

É claro `NotifyDataSetChanged` pode ter sido chamado em vez de fazer as duas chamadas para `NotifyItemChanged`, mas fazer então forçaria `RecyclerView` atualizar toda a coleção, mesmo que apenas dois itens da coleção foi alterado. Chamando `NotifyItemChanged` é significativamente mais eficiente do que chamar `NotifyDataSetChanged`.


## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView partes e funcionalidade](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
