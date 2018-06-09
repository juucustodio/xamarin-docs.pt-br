---
title: Adicionando um reconhecedor de gestos de gestos de toque
description: Este artigo explica como usar o gesto de tocar para detecção e toque em um aplicativo xamarin. Forms. Detecção de toque é implementada com a classe TapGestureRecognizer.
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: bbe4ca7a1080459b8aeb33640be5158b15e97715
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240659"
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de gestos de toque

_O gesto de toque é usado para detecção de toque e é implementado com a classe TapGestureRecognizer._

## <a name="overview"></a>Visão geral

Para criar um elemento de interface do usuário que pode ser clicado com o gesto de tocar, crie um [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) da instância, tratar o [ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/) eventos e adicionar o novo reconhecedor de gestos para o [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) coleta sobre o elemento de interface do usuário. O seguinte exemplo de código mostra uma `TapGestureRecognizer` anexado a um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

Por padrão a imagem responderá toques único. Definir o [ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/) propriedade aguardar um toque duplo (ou mais toques se necessário).

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

Quando [ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/) está definida acima, o manipulador de eventos será executado somente se toques de ocorrerem dentro de um determinado período (esse período não é configurável). Se toques de segundo (ou subsequentes) ocorrer dentro desse período efetivamente, elas são ignoradas e reinicia a contagem de toque em' '.

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Usando Xaml

O reconhecedor de gestos pode ser adicionado a um controle em Xaml usando propriedades anexadas. A sintaxe para adicionar um [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) a uma imagem é mostrado abaixo (nesse caso definindo um *toque duplo* eventos):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

O código para o manipulador de eventos (por exemplo) um contador é incrementado e altera a imagem de cor para preto &amp; branco.

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

Os aplicativos que usam o padrão Mvvm normalmente usam `ICommand` em vez de fiação manipuladores de eventos diretamente. O [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) pode facilmente dar suporte `ICommand` definindo a associação no código:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

ou, usando Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

O código completo para esse modelo de exibição pode ser encontrado no exemplo. Relevante `Command` detalhes de implementação são mostrados abaixo:

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

O gesto de toque é usado para detecção de toque e é implementado com o [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) classe. O número de toques pode ser especificado para reconhecer um toque duplo (ou toque triplo, ou mais toca) comportamento.


## <a name="related-links"></a>Links relacionados

- [TapGesture (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [TapGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)
