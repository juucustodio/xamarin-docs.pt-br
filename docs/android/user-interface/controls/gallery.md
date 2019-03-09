---
title: Galeria
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f9b73428531deeacc7bdea271cdc0c2872038e99
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666953"
---
# <a name="gallery"></a>Galeria

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) é um widget de layout usado para exibir itens em uma lista de rolagem horizontais e posiciona a seleção atual no centro da exibição.

> [!IMPORTANT]
> Este widget foi preterido no Android 4.1 (API nível 16). 

Neste tutorial, você criará uma galeria de fotos e, em seguida, exibir uma mensagem de notificação do sistema sempre que um item da Galeria está selecionado.

Após o `Main.axml` layout é definido para a exibição de conteúdo, o `Gallery` for capturada do layout com [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
O [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
propriedade, em seguida, é usada para definir um adaptador personalizado ( `ImageAdapter`) como a fonte para todos os itens a serem exibidos no dallery. O `ImageAdapter` é criado na próxima etapa.

Para fazer algo quando um item na Galeria é clicado, um delegado anônimo está inscrito para o [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
. Ele mostra um [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
que exibe a posição de índice (baseado em zero) do item theselected (em um cenário do mundo real, a posição pode ser usada para obter a imagem completa de tamanhos para alguma outra tarefa).

Primeiro, existem algumas variáveis de membro, incluindo uma matriz de IDs que referenciam as imagens salvas no diretório de recursos desenháveis (**recursos/drawable**).

Em seguida, é o construtor da classe, em que o [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
para um `ImageAdapter` instância é definida e salvo em um campo local.
Em seguida, isso implementa alguns métodos necessários herdados de [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).
O construtor e o [`Count`](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)
propriedade são auto-explicativos. Normally, [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)
deve retornar o objeto real na posição especificada no adaptador, mas é ignorado para este exemplo. Likewise, [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)
deve retornar a id da linha do item, mas não é necessária aqui.

O método faz o trabalho para aplicar uma imagem a um [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
que será inserido o [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
Nesse método, o membro [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
é usado para criar um novo [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).
O [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
está preparado com a aplicação de uma imagem da matriz local de recursos desenháveis, definindo o [`Gallery.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)
altura e largura da imagem, definir a escala de acordo com o [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
as dimensões e, em seguida, definir, por fim, o plano de fundo para usar o atributo estilizáveis adquirido no construtor.

Ver [ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/) para outra opções de dimensionamento de imagem.

## <a name="walkthrough"></a>Passo a passo

Iniciar um novo projeto chamado *HelloGallery*.

[![Captura de tela do novo projeto Android na caixa de diálogo nova solução](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Encontrar algumas fotos que você deseja usar, ou [baixar essas imagens de exemplo](https://developer.android.com/shareables/sample_images.zip).
Adicione os arquivos de imagem para o projeto **recursos/Drawable** directory. No **propriedades** janela, defina a Build Action de cada um para **AndroidResource**.

Abra **Resources/Layout/Main.axml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Abra `MainActivity.cs` e insira o seguinte código para o [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

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

Criar uma nova classe chamada `ImageAdapter` que pode efetuar subclasses [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

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

Execute o aplicativo. Ele deve ser semelhante a captura de tela abaixo:

![Captura de tela de HelloGallery exibindo imagens de exemplo](gallery-images/hellogallery3.png)



## <a name="references"></a>Referências

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*Partes desta página são modificações com base no trabalho criado e compartilhado por Android Open Source Project e usadas de acordo com os termos descritos na*
[*2.5 atribuição de licença da Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).


