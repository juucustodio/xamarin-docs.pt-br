---
title: Usando UrhoSharp emXamarin.Forms
description: Este artigo explica como o UrhoSharp pode ser usado para adicionar gráficos 3D a um Xamarin.Forms aplicativo para visualização avançada.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fd1893a91d9d8e5d2c2581a9f3f9b5ef8ee59f1f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937664"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Usando UrhoSharp emXamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>O que é o UrhoSharp?

O [UrhoSharp](~/graphics-games/urhosharp/index.md) é um poderoso mecanismo 3D para desenvolvedores do Xamarin e do .net. A [introdução](~/graphics-games/urhosharp/introduction.md) explica mais sobre a biblioteca UrhoSharp, e [essas notas](~/graphics-games/urhosharp/using.md) descrevem como programar cenas e ações.

UrhoSharp pode ser usado para renderizar gráficos em Xamarin.Forms aplicativos.
Este [exemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample) demonstra como UrhoSharp poderia ser usado para construir um gráfico 3D interativo:

![Gráfico interativo do UrhoSharp 3D no ](urhosharp-images/ios-animation.gif)
 ![ gráfico interativo do IOS UrhoSharp 3D no Android](urhosharp-images/android-animation.gif)

## <a name="adding-the-urhosharp-nuget-packages"></a>Adicionando os pacotes NuGet do UrhoSharp

Antes de usar o UrhoSharp, os desenvolvedores precisam adicionar o pacote NuGet do UrhoSharp à sua solução. Este guia pressupõe um Xamarin.Forms projeto com um projeto de biblioteca do IOS, Android e .net Standard. Todo o código será escrito no projeto de biblioteca de .NET Standard; Mas o NuGet UrhoSharp também deve ser adicionado aos projetos iOS e Android.

O pacote NuGet UrhoSharp. Forms contém todos os objetos necessários para criar objetos UrhoSharp. O pacote NuGet UrhoSharp. Forms inclui a `UrhoSurface` classe, que é usada para hospedar o UrhoSharp no Xamarin.Forms .
Para começar, clique com o botão direito do mouse na pasta **pacotes** no projeto de biblioteca .net Standard e selecione **adicionar pacotes...**. Insira o termo de pesquisa **UrhoSharp. Forms**, selecione **UrhoSharp Xamarin.Forms para **e clique em **Adicionar pacote**.

[![Caixa de diálogo adicionar pacotes](urhosharp-images/add-package-sml.png)](urhosharp-images/add-package.png#lightbox "Caixa de diálogo adicionar pacotes")

O pacote NuGet UrhoSharp. Forms será adicionado ao projeto:

![Pasta de pacotes](urhosharp-images/packages.png)

Repita as etapas acima para projetos específicos da plataforma (como iOS e Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Walkthrough: adicionando UrhoSharp a um Xamarin.Forms aplicativo

Estas etapas descrevem o código no Xamarin.Forms exemplo de UrhoSharp:

1. [Criar uma página de formulários do Xamarin](#1-create-a-xamarin-forms-page)
2. [Adicionar o UrhoSurface](#2-add-the-urhosurface)
3. [Criar um aplicativo Urho](#3-build-an-urho-application)
4. [Adicionar a classe Charts ao UrhoSurface](#4-add-the-charts-class-to-the-urhosurface)
5. [Interagindo com UrhoSharp](#5-interacting-with-urhosharp)

Observe que o exemplo usa recursos C# 6 e não pode compilar em versões mais antigas do Visual Studio.

### <a name="1-create-a-xamarin-forms-page"></a>1. criar uma página de formulários do Xamarin

O código a seguir mostra uma Xamarin.Forms página `UrhoPage` antes de qualquer código relacionado a Urho ter sido adicionado:

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

### <a name="2-add-the-urhosurface"></a>2. Adicionar o UrhoSurface

UrhoSharp pode ser hospedado em um `ContentPage` como outros Xamarin.Forms controles.
O trecho de código abaixo mostra um `UrhoSurface` adicionado à Xamarin.Forms página:

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

### <a name="3-build-an-urho-application"></a>3. criar um aplicativo Urho

Consulte a `Charts` classe para a implementação dos gráficos 3D Urho usados neste exemplo. A estrutura de tópicos básica de código é mostrada abaixo – Observe que a classe implementa `Urho.Application` que é diferente da `Xamarin.Forms.Application` classe implementada no **app.cs**.

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

A [documentação do UrhoSharp](~/graphics-games/urhosharp/index.md) contém mais informações sobre como criar cenas e ações 3D.

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. adicionar a classe Charts ao UrhoSurface

Use o `UrhoSurface.Show<T>` método genérico para adicionar o aplicativo Urho à Xamarin.Forms página. O trecho de código a seguir mostra o código adicional necessário para criar a `Charts` classe:

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

Observação: o `Show<T>` método é assíncrono e deve ser chamado com a `await` palavra-chave.

### <a name="5-interacting-with-urhosharp"></a>5. interagindo com UrhoSharp

O exemplo permite que as barras do gráfico sejam selecionadas e modificadas. A `Charts` classe expõe o `Bars` e `SelectedBar` para habilitar essa interação.

Cada "bar" tem um manipulador de eventos de seleção adicionado após a `Charts` classe ter sido renderizada, Iterando sobre a `Bars` coleção exposta:

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

O manipulador de eventos usa o valor do Xamarin.Forms `Slider` controle para ajustar a altura da barra fornecida:

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

Por fim, conecte os dois `Slider` controles para que, quando seu valor for alterado, a tela UrhoSharp seja afetada. O primeiro controle deslizante gira a imagem do gráfico 3D e o segundo controle deslizante ajusta a altura da barra selecionada.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

As animações na [parte superior da página](#what-is-urhosharp) mostram o exemplo em execução.

## <a name="summary"></a>Resumo

Esta página mostra como UrhoSharp pode ser usado para adicionar visualização de dados 3D ao Xamarin.Forms . Leia a [documentação do UrhoSharp](~/graphics-games/urhosharp/index.md) para obter mais informações sobre como criar cenas do Urho que podem ser incluídas em Xamarin.Forms aplicativos usando o método mostrado acima.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de gráficos (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
