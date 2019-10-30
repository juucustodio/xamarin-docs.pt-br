---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 6409a4cb65186cade454253e1afdd38a66f3357f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028986"
---
# <a name="xamarinandroid-gridview"></a>O Xamarin. Android GridView

[`GridView`](xref:Android.Widget.GridView) é um [`ViewGroup`](xref:Android.Views.ViewGroup)
Isso exibe os itens de uma grade rolável bidimensional. Os itens de grade são inseridos automaticamente no layout usando uma [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter).

Neste tutorial, você criará uma grade de miniaturas de imagem. Quando um item é selecionado, uma mensagem do sistema exibirá a posição da imagem.

Inicie um novo projeto chamado **HelloGridView**.

Encontre algumas fotos que você gostaria de usar ou [Baixe essas imagens de exemplo](https://developer.android.com/shareables/sample_images.zip). Adicione os arquivos de imagem ao diretório de **recursos/empates** do projeto. Na janela **Propriedades** , defina a ação de compilação para cada para **AndroidResource**.

Abra o arquivo **Resources/layout/Main. axml** e insira o seguinte:

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

Esta [`GridView`](xref:Android.Widget.GridView) preencherá a tela inteira. Os atributos são, na verdade, auto-explicativos. Para obter mais informações sobre atributos válidos, consulte a referência de [`GridView`](xref:Android.Widget.GridView) .

Abra `HelloGridView.cs` e insira o código a seguir para o [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

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

Depois que o layout **Main. axml** é definido para o modo de exibição de conteúdo, o [`GridView`](xref:Android.Widget.GridView) é capturado do layout com [`FindViewById`](xref:Android.App.Activity.FindViewById*). O [`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
em seguida, a propriedade é usada para definir um adaptador personalizado (`ImageAdapter`) como a origem de todos os itens a serem exibidos na grade. O `ImageAdapter` é criado na próxima etapa.

Para fazer algo quando um item na grade é clicado, um delegado anônimo é assinado para o evento [`ItemClick`](xref:Android.Widget.AdapterView.ItemClick) .
Ele mostra um [`Toast`](xref:Android.Widget.Toast) que exibe a posição do índice (baseado em zero) do item selecionado (em um cenário do mundo real, a posição pode ser usada para obter a imagem de tamanho completo de alguma outra tarefa). Observe que as classes de ouvinte em estilo Java podem ser usadas em vez de eventos .NET.

Crie uma nova classe chamada `ImageAdapter` que as subclasses [`BaseAdapter`](xref:Android.Widget.BaseAdapter):

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

Primeiro, isso implementa alguns métodos necessários herdados do [`BaseAdapter`](xref:Android.Widget.BaseAdapter). O construtor e a propriedade [`Count`](xref:Android.Widget.BaseAdapter.Count) são auto-explicativos. Normalmente, [`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
deve retornar o objeto real na posição especificada no adaptador, mas é ignorado para este exemplo. Da mesma forma, [`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
deve retornar a ID de linha do item, mas não é necessário aqui.

O primeiro método necessário é [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*).
Esse método cria um novo [`View`](xref:Android.Views.View)
para cada imagem adicionada ao `ImageAdapter`. Quando isso é chamado, um [`View`](xref:Android.Views.View)
é passado, que normalmente é um objeto reciclado (pelo menos depois que isso é chamado uma vez), portanto, há uma verificação para ver se o objeto é nulo. Se *for* NULL, um [`ImageView`](xref:Android.Widget.ImageView)
é instanciado e configurado com as propriedades desejadas para a apresentação da imagem:

- [`LayoutParams`](xref:Android.Views.View.LayoutParameters) define a altura e a largura da exibição&mdash;isso garante que, não importa o tamanho do empate, cada imagem seja redimensionada e cortada para caber nessas dimensões, conforme apropriado.

- [`SetScaleType()`](xref:Android.Widget.ImageView.SetScaleType*) declara que as imagens devem ser cortadas em direção ao centro (se necessário).

- [`SetPadding(int, int, int, int)`](xref:Android.Views.View.SetPadding*) define o preenchimento de todos os lados. (Observe que, se as imagens tiverem proporções de aspecto diferentes, menos preenchimento causará mais corte da imagem se não corresponder às dimensões atribuídas ao ImageView.)

Se o [`View`](xref:Android.Views.View) passado para [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*) *não* for nulo, o [`ImageView`](xref:Android.Widget.ImageView) local
é inicializado com o objeto de [`View`](xref:Android.Views.View) reciclado.

No final da [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*)
, o número inteiro `position` passado para o método é usado para selecionar uma imagem da matriz de `thumbIds`, que é definida como o recurso de imagem para o [`ImageView`](xref:Android.Widget.ImageView).

Tudo o que resta é definir a `thumbIds` matriz de recursos de desenho.

Execute o aplicativo. O layout da grade deve ser semelhante ao seguinte:

[![captura de tela de exemplo de GridView exibindo 15 imagens](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Tente experimentar os comportamentos do [`GridView`](xref:Android.Widget.GridView) e [`ImageView`](xref:Android.Widget.ImageView)
elementos ajustando suas propriedades. Por exemplo, em vez de usar [`LayoutParams`](xref:Android.Views.View.LayoutParameters) tente usar [`SetAdjustViewBounds()`](xref:Android.Widget.ImageView.SetAdjustViewBounds*).

## <a name="references"></a>Referências

- [`GridView`](xref:Android.Widget.GridView)
- [`ImageView`](xref:Android.Widget.ImageView)
- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/)._
