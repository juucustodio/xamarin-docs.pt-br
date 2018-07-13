---
title: Adicionando um reconhecedor de gestos de gestos de toque
description: Este artigo explica como usar o gesto de tocar para detecção e toque em um aplicativo xamarin. Forms. Detecção de toque é implementada com a classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: e602ae1f140640d9a895b65d78feab3d0a3b7861
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994848"
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de gestos de toque

_O gesto de toque é usado para detecção de toque e é implementado com a classe TapGestureRecognizer._

## <a name="overview"></a>Visão geral

Para criar um elemento de interface do usuário que pode ser clicado com o gesto de tocar, crie uma [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) da instância, lidar com o [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) eventos e adicione o reconhecedor de gestos de novo para o [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) coleta sobre o elemento de interface do usuário. O seguinte exemplo de código mostra uma `TapGestureRecognizer` anexados a uma [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Por padrão, a imagem responderá aos toques único. Defina as [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) propriedade aguardar um toque duplo (ou mais toques, se necessário).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) é definido acima, o manipulador de eventos será executado somente se os toques ocorrerem em um período definido de tempo (esse período não é configurável). Se os toques de segundo (ou posteriores) não ocorram dentro desse período efetivamente, eles serão ignorados e reinicia a contagem de toque' '.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Usando Xaml

Um reconhecedor de gestos pode ser adicionado a um controle em Xaml usando as propriedades anexadas. A sintaxe para adicionar um [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) a uma imagem é mostrada abaixo (nesse caso, definindo uma *toque duplo* eventos):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

O código para o manipulador de eventos (no exemplo) incrementa um contador e altera a imagem de cor para preto &amp; branca.

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

Aplicativos que usam o padrão Mvvm normalmente usam `ICommand` em vez de conectar manipuladores de eventos diretamente. O [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) pode facilmente dar suporte `ICommand` definindo a associação no código:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

ou usando Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

O código completo para esse modelo de exibição pode ser encontrado na amostra. O relevantes `Command` detalhes de implementação são mostrados abaixo:

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

## <a name="summary"></a>Resumo

O gesto de toque é usado para detecção de toque e é implementado com o [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) classe. O número de toques pode ser especificado para reconhecer um toque duplo (ou toque triplo, ou mais toca) comportamento.


## <a name="related-links"></a>Links relacionados

- [TapGesture (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
