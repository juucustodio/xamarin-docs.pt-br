---
title: Estendendo o exemplo de RecyclerView
description: Adicionar manipuladores de eventos de clique de item ao aplicativo de exemplo RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: 2fc2eabb26a9635b569cc6d3a51195fb554ca950
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758605"
---
# <a name="extending-the-recyclerview-example"></a>Estendendo o exemplo de RecyclerView

O aplicativo básico descrito em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) realmente não faz &ndash; muito, simplesmente rola e exibe uma lista fixa de itens de fotografia para facilitar a navegação. Em aplicativos do mundo real, os usuários esperam ser capazes de interagir com o aplicativo tocando em itens na exibição. Além disso, a fonte de dados subjacente pode alterar (ou ser alterada pelo aplicativo) e o conteúdo da exibição deve permanecer consistente com essas alterações. Nas seções a seguir, você aprenderá a lidar com eventos de clique de item e `RecyclerView` atualizar quando a fonte de dados subjacente for alterada.

### <a name="handling-item-click-events"></a>Manipulando eventos de clique de item

Quando um usuário toca um item no `RecyclerView`, um evento de clique de item é gerado para notificar o aplicativo sobre qual item foi tocado. Esse evento não é gerado pelo `RecyclerView` &ndash; em vez disso, o modo de exibição de item (que é encapsulado no espaço de exibição) detecta toques e relatórios como eventos de clique.

Para ilustrar como lidar com eventos de clique de item, as etapas a seguir explicam como o aplicativo básico de exibição de fotos é modificado para relatar qual fotografia foi tocada pelo usuário. Quando um evento de clique de item ocorre no aplicativo de exemplo, a sequência a seguir ocorre:

1. A fotografia `CardView` detecta o evento de clique do item e notifica o adaptador.

2. O adaptador encaminha o evento (com informações de posição de item) para o manipulador de clique de item da atividade.

3. O manipulador de item-clique da atividade responde ao evento de clique do item.

Primeiro, um membro do manipulador de `ItemClick` eventos chamado é adicionado `PhotoAlbumAdapter` à definição de classe:

```csharp
public event EventHandler<int> ItemClick;
```

Em seguida, um método de manipulador de eventos de clique de `MainActivity`item é adicionado a.
Este manipulador exibe brevemente um sistema de notificação que indica qual item de fotografia foi tocado:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Em seguida, uma linha de código é necessária para registrar `OnItemClick` o manipulador `PhotoAlbumAdapter`com. Um bom local para fazer isso é imediatamente após `PhotoAlbumAdapter` a criação: 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

Neste exemplo básico, o registro do manipulador ocorre no método `OnCreate` da atividade principal, mas um aplicativo de produção pode registrar o manipulador no `OnResume` e cancelar seu registro em `OnPause` &ndash; consulte [ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md) para obter mais divulgação.

`PhotoAlbumAdapter`Agora será chamado `OnItemClick` quando receber um evento de clique de item. A próxima etapa é criar um manipulador no adaptador que gera esse `ItemClick` evento. O método a seguir `OnClick`,, é adicionado imediatamente após o método `ItemCount` do adaptador:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Esse `OnClick` método é o *ouvinte* do adaptador para eventos de clique de item das exibições de item. Antes que esse ouvinte possa ser registrado com um modo de exibição de item (por meio do espaço de `PhotoViewHolder` exibição do modo de exibição de item), o construtor deve ser modificado para `OnClick` aceitar esse método como `Click` um argumento adicional e registrar com o evento de exibição de item.
Este é o construtor `PhotoViewHolder` modificado:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

O `itemView` parâmetro contém uma referência `CardView` ao que foi tocado pelo usuário. Observe que a classe base do modo de exibição tem conhecimento da posição de layout`CardView`do item () que ele representa `LayoutPosition` (por meio da propriedade), e essa posição é passada `OnClick` para o método do adaptador quando ocorre um evento de clique de item. O método do `OnCreateViewHolder` adaptador é modificado para passar o método do `OnClick` adaptador para o construtor do titular do modo de exibição:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Agora, quando você cria e executa o exemplo de aplicativo de exibição de fotos, tocar em uma foto na tela fará com que apareça uma notificação de que o relatório que a fotografia foi tocado:

[![Exemplo de notificação que aparece quando um cartão de fotos é tocado](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Este exemplo demonstra apenas uma abordagem para implementar manipuladores de eventos `RecyclerView`com. Outra abordagem que poderia ser usada aqui é inserir eventos no detentor do modo de exibição e fazer com que o adaptador assine esses eventos. Se o aplicativo de fotos de exemplo forneceu um recurso de edição de fotos, os eventos `ImageView` separados seriam `TextView` necessários para `CardView`o e o em `TextView` cada um: `EditView` os toques no iniciarão uma caixa de diálogo que permite ao usuário editar a legenda e os toques no `ImageView` iniciarão uma ferramenta de retoque de fotos que permite ao usuário cortar ou girar a foto. Dependendo das necessidades do seu aplicativo, você deve criar a melhor abordagem para lidar e responder a eventos de toque.

Para demonstrar como `RecyclerView` o pode ser atualizado quando o conjunto de dados é alterado, o aplicativo de exibição de fotos de exemplo pode ser modificado para escolher aleatoriamente uma foto na fonte de dados e trocá-la pela primeira foto. Primeiro, um botão de **seleção aleatório** é adicionado ao layout **principal. axml** do aplicativo de foto de exemplo:

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

Em seguida, o código é adicionado ao final do método da `OnCreate` atividade principal para localizar o `Random Pick` botão no layout e anexar um manipulador a ele:

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

Esse manipulador chama o método do `RandomSwap` álbum de fotos quando o botão de **seleção aleatória** é tocado. O `RandomSwap` método troca aleatoriamente uma foto pela primeira foto da fonte de dados e, em seguida, retorna o índice da foto trocada aleatoriamente. Quando você compila e executa o aplicativo de exemplo com esse código, tocar no botão de **seleção aleatório** não resulta em uma alteração de exibição `RecyclerView` porque o não está ciente da alteração na fonte de dados.

Para manter `RecyclerView` a atualização após a alteração da fonte de dados, o manipulador de cliques de **seleção aleatório** deve ser modificado `NotifyItemChanged` para chamar o método do adaptador para cada item na coleção que foi alterada (nesse caso, dois itens foram alterados: a primeira foto e a foto trocada). Isso faz `RecyclerView` com que o atualize sua exibição para que seja consistente com o novo estado da fonte de dados:

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

Agora, quando o botão de **seleção aleatória** é tocado `RecyclerView` , o atualiza a exibição para mostrar que uma foto mais para baixo na coleção foi trocada pela primeira foto da coleção:

[![Primeira captura de tela antes da permuta, segunda captura de tela após permuta](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

É claro `NotifyDataSetChanged` que poderia ter sido chamado em vez de fazer as duas `NotifyItemChanged`chamadas, mas isso forçaria `RecyclerView` a atualizar toda a coleção, embora apenas dois itens da coleção tivessem mudado. Chamar `NotifyItemChanged` é significativamente mais eficiente do que `NotifyDataSetChanged`chamar.

## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Partes e funcionalidades do RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
