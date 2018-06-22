---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 82c82de912fd253d45e6343e2dd1c50e389c6371
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766293"
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) é um [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que exibe itens em uma grade bidimensional, rolável. Os itens de grade são inseridos automaticamente para o layout usando um [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).

Neste tutorial, você criará uma grade de miniaturas de imagem. Quando um item é selecionado, será exibida uma mensagem de notificação do sistema a posição da imagem.

Iniciar um novo projeto denominado **HelloGridView**.

Localizar algumas fotos que você deseja usar, ou [baixar essas imagens de amostra](http://developer.android.com/shareables/sample_images.zip). Adicionar arquivos de imagem para o projeto **recursos/Drawable** directory. No **propriedades** janela, defina a ação de compilação de cada **AndroidResource**.

Abra o **Resources/Layout/Main.axml** de arquivo e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

Isso [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) preencher a tela inteira. Os atributos são bastante auto-explicativo. Para obter mais informações sobre atributos válidos, consulte o [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) referência.

Abra `HelloGridView.cs` e insira o seguinte código para o [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Após o **Main.axml** layout está definido para o modo de exibição de conteúdo, o [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) é capturado do layout com [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). O [ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/) propriedade é usada para definir um adaptador personalizado (`ImageAdapter`) como a fonte para todos os itens a serem exibidos na grade. O `ImageAdapter` é criado na próxima etapa.

Para fazer algo quando um item na grade é clicado, um delegado anônimo está assinado para o [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) eventos.
Ele mostra uma [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) que exibe a posição de índice (baseado em zero) do item selecionado (em um cenário do mundo real, a posição pode ser usada para obter a imagem completa de tamanho para outra tarefa). Observe que as classes de ouvinte de estilo do Java podem ser usadas em vez de eventos .NET.

Criar uma nova classe chamada `ImageAdapter` que as subclasses [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

    public override Java.Lang.Object GetItem (int position)
    {
        return null;
    }

    public override long GetItemId (int position)
    {
        return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

Primeiro, isso implementa alguns métodos necessários herdados de [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/). O construtor e o [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) propriedade são autoexplicativas. Normalmente, [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/) deve retornar o objeto real na posição especificada no adaptador, mas é ignorada para este exemplo. Da mesma forma, [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/) deve retornar a id do item de linha, mas ela não é necessária aqui.

É o primeiro método necessário [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/).
Esse método cria um novo [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) para cada imagem adicionada para o `ImageAdapter`. Quando isso é chamado, um [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) for passado, que normalmente é um objeto reciclado (pelo menos depois que isso é chamado uma vez), portanto, há uma verificação para ver se o objeto é nulo. Se ele *é* nulo, uma [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) é instanciada e configurado com as propriedades desejadas para a apresentação de imagem:

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) Define a altura e largura do modo de exibição&mdash;Isso garante que, independentemente do tamanho do drawable, cada imagem é redimensionada e cortada para caber dessas dimensões, conforme apropriado.

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) declara que imagens devem ser cortadas em direção ao centro (se necessário).

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) Define o preenchimento de todos os lados. (Observe que, se as imagens têm taxas de proporção diferentes, em seguida, menos o preenchimento fará com que para cortar mais da imagem se ele não corresponder às dimensões fornecidas para o ImageView).

Se o [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) passado para [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) é *não* nulo, então o local [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) é inicializada com a reciclagem [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) objeto.

No final do [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) método, o `position` inteiro passado para o método é usado para selecionar uma imagem do `thumbIds` matriz, que é definido como o recurso de imagem para o [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

Tudo o que resta é definir o `thumbIds` matriz de recursos drawable.

Execute o aplicativo. O layout de grade deve ter esta aparência:

[![Captura de tela de exemplo de GridView exibindo 15 imagens](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Faça experiências com os comportamentos do [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) e [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) elementos ajustando suas propriedades. Por exemplo, em vez de usar [ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) tente usar [ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/).


## <a name="references"></a>Referências

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/).
