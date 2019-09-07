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
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771332"
---
# <a name="adding-a-tap-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de toque

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_O gesto de toque é usado para detecção de toque e é implementado com a classe TapGestureRecognizer._

Para que seja possível clicar em um elemento da interface do usuário com um gesto de toque, crie uma instância de [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer), manipule o evento [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) e adicione o novo reconhecedor de gestos à coleção [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) no elemento de interface do usuário. O exemplo de código a seguir mostra um `TapGestureRecognizer` anexado a um elemento [`Image`](xref:Xamarin.Forms.Image):

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Por padrão, a imagem responderá a toques único. Configure a propriedade [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) para aguardar um toque duplo (ou mais toques se necessário).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) é configurado como mais de um, o manipulador de eventos é executado somente quando os toques ocorrem em um período definido (esse período não é configurável). Se o segundo toque (ou os toques posteriores) não ocorrerem dentro desse período, eles serão ignorados e a “contagem de toques” será reiniciada.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Usando XAML

Um reconhecedor de gestos pode ser adicionado a um controle em XAML usando propriedades anexadas. A sintaxe para adicionar um [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a uma imagem é mostrada abaixo (nesse caso, definindo um evento de *toque duplo*):

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

Aplicativos que usam o padrão MVVM (Model-View-ViewModel) normalmente usam `ICommand` em vez de conectar manipuladores de eventos diretamente. O [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) pode facilmente dar suporte a `ICommand` definindo a associação no código:

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
