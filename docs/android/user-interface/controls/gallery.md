---
title: Controle da galeria do Android
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 93eb7f98da6f3fe06f288eae5823f7173e58585f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029238"
---
# <a name="xamarinandroid-gallery-control"></a>Controle da galeria do Xamarin. Android

[`Gallery`](xref:Android.Widget.Gallery) é um widget de layout usado para exibir itens em uma lista de rolagem horizontal e posiciona a seleção atual no centro da exibição.

> [!IMPORTANT]
> Este widget foi preterido no Android 4,1 (nível de API 16). 

Neste tutorial, você criará uma galeria de fotos e, em seguida, exibirá uma mensagem do sistema sempre que um item da Galeria for selecionado.

Depois que o layout de `Main.axml` é definido para o modo de exibição de conteúdo, o `Gallery` é capturado do layout com [`FindViewById`](xref:Android.App.Activity.FindViewById*).
O [`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
em seguida, a propriedade é usada para definir um adaptador personalizado (`ImageAdapter`) como a origem de todos os itens a serem exibidos no DALLERY. O `ImageAdapter` é criado na próxima etapa.

Para fazer algo quando um item na galeria é clicado, um delegado anônimo é inscrito no [`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
. Ele mostra um [`Toast`](xref:Android.Widget.Toast)
que exibe a posição do índice (com base em zero) do item selecionado (em um cenário do mundo real, a posição pode ser usada para obter a imagem de tamanho completo de alguma outra tarefa).

Primeiro, há algumas variáveis de membro, incluindo uma matriz de IDs que fazem referência às imagens salvas no diretório de recursos desenháveis (**recursos/empates**).

Em seguida, está o construtor de classe, em que o [`Context`](xref:Android.Content.Context)
para um `ImageAdapter` instância é definida e salva em um campo local.
Em seguida, isso implementa alguns métodos necessários herdados do [`BaseAdapter`](xref:Android.Widget.BaseAdapter).
O construtor e o [`Count`](xref:Android.Widget.BaseAdapter.Count)
a propriedade é auto-explicativa. Normalmente, [`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
deve retornar o objeto real na posição especificada no adaptador, mas é ignorado para este exemplo. Da mesma forma, [`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
deve retornar a ID de linha do item, mas não é necessário aqui.

O método faz o trabalho para aplicar uma imagem a um [`ImageView`](xref:Android.Widget.ImageView)
que será inserido no [`Gallery`](xref:Android.Widget.Gallery)
Nesse método, o membro [`Context`](xref:Android.Content.Context)
é usado para criar um novo [`ImageView`](xref:Android.Widget.ImageView).
O [`ImageView`](xref:Android.Widget.ImageView)
é preparado aplicando uma imagem da matriz local de recursos de desenho, definindo o [`Gallery.LayoutParams`](xref:Android.Widget.Gallery.LayoutParams)
altura e largura da imagem, definindo a escala como se ajusta à [`ImageView`](xref:Android.Widget.ImageView)
dimensões e, finalmente, definir o plano de fundo para usar o atributo estilizado adquirido no construtor.

Consulte [`ImageView.ScaleType`](xref:Android.Widget.ImageView.ScaleType) para ver outras opções de dimensionamento de imagem.

## <a name="walkthrough"></a>Passo a passo

Inicie um novo projeto chamado *HelloGallery*.

[![captura de tela do novo projeto do Android na caixa de diálogo nova solução](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Encontre algumas fotos que você gostaria de usar ou [Baixe essas imagens de exemplo](https://developer.android.com/shareables/sample_images.zip).
Adicione os arquivos de imagem ao diretório de **recursos/empates** do projeto. Na janela **Propriedades** , defina a ação de compilação para cada para **AndroidResource**.

Abra **Resources/layout/Main. axml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Abra `MainActivity.cs` e insira o código a seguir para o [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Crie uma nova classe chamada `ImageAdapter` que as subclasses [`BaseAdapter`](xref:Android.Widget.BaseAdapter):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

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
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

Execute o aplicativo. Ele deve ser semelhante à captura de tela abaixo:

![Captura de tela de HelloGallery exibindo imagens de exemplo](gallery-images/hellogallery3.png)

## <a name="references"></a>Referências

- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)
- [`Gallery`](xref:Android.Widget.Gallery)
- [`ImageView`](xref:Android.Widget.ImageView)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/)._
