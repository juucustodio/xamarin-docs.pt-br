---
title: Adicionar um reconhecedor de gestos Tap
description: Este artigo explica como usar o gesto de toque para detecção de toque em um Xamarin.Forms aplicativo. A detecção de toque é implementada com a classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d0b9b5a568a94208d5e177c88d2e73edb54b735c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375181"
---
# <a name="add-a-tap-gesture-recognizer"></a>Adicionar um reconhecedor de gestos Tap

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_O gesto de toque é usado para detecção de toque e é implementado com a classe TapGestureRecognizer._

Para tornar um elemento de interface do usuário clicável com o gesto de toque, crie uma [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) instância, manipule o [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) evento e adicione o novo reconhecedor de gesto à [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) coleção no elemento de interface do usuário. O exemplo de código a seguir mostra um `TapGestureRecognizer` anexado a um [`Image`](xref:Xamarin.Forms.Image) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Por padrão, a imagem responderá a toques único. Defina a [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) propriedade como aguardar um toque duplo (ou mais toques, se necessário).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) é definido acima de um, o manipulador de eventos só será executado se os toques ocorrerem dentro de um período de tempo definido (esse período não é configurável). Se o segundo toque (ou os toques posteriores) não ocorrerem dentro desse período, eles serão ignorados e a “contagem de toques” será reiniciada.

## <a name="using-xaml"></a>Usando XAML

Um reconhecedor de gestos pode ser adicionado a um controle em XAML usando propriedades anexadas. A sintaxe para adicionar um [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a uma imagem é mostrada abaixo (neste caso, definindo um evento de *toque duplo* ):

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

Aplicativos que usam o padrão MVVM (Model-View-ViewModel) normalmente usam `ICommand` em vez de conectar manipuladores de eventos diretamente. O [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) pode facilmente dar suporte ao `ICommand` definindo a associação no código:

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

## <a name="related-links"></a>Links Relacionados

- [TapGesture (amostra)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)