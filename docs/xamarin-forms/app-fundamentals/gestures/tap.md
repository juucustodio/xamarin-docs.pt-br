---
title: Adicionando um reconhecedor de gestos de toque
description: Este artigo explica como usar o gesto de toque para detecção de toque em um aplicativo do Xamarin.Forms. A detecção de toque é implementada com a classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 95f25dbce55e2b960f604b6e304ffb6e8ed775e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771332"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de toque

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_O gesto de toque é usado para detecção de toque e é implementado com a classe TapGestureRecognizer._

Para tornar um elemento de interface de usuário [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) clicável [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) com o gesto de toque, crie uma instância, manuseie o evento e adicione o novo reconhecimento de gestos à [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) coleção no elemento interface do usuário. O exemplo de `TapGestureRecognizer` código a [`Image`](xref:Xamarin.Forms.Image) seguir mostra um anexo a um elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Por padrão, a imagem responderá a toques único. Defina [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) a propriedade para esperar por um toque duplo (ou mais toques, se necessário).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) estiver definido acima de um, o manipulador de eventos só será executado se os toques ocorrerem dentro de um período definido de tempo (este período não é configurável). Se o segundo toque (ou os toques posteriores) não ocorrerem dentro desse período, eles serão ignorados e a “contagem de toques” será reiniciada.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Usando XAML

Um reconhecedor de gestos pode ser adicionado a um controle em XAML usando propriedades anexadas. A sintaxe [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) para adicionar a a uma imagem é mostrada abaixo (neste caso definindo um evento *de toque duplo):*

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

O código para o manipulador de eventos (no exemplo) incrementa um contador e altera a imagem de colorida para preto &amp; branca.

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>Usando ICommand

Aplicativos que usam o padrão MVVM (Model-View-ViewModel) normalmente usam `ICommand` em vez de conectar manipuladores de eventos diretamente. O [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) pode `ICommand` suportar facilmente qualquer um definindo a vinculação em código:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

ou usando XAML:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

O código completo para esse modelo de exibição pode ser encontrado na amostra. Os detalhes relevantes da implementação de `Command` são mostrados abaixo:

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="related-links"></a>Links relacionados

- [TapGesture (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
