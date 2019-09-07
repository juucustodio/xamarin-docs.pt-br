---
title: Usar UrhoSharp em xamarin. Forms
description: Este artigo explica como UrhoSharp pode ser usado para adicionar elementos gráficos 3D a um aplicativo xamarin. Forms para visualização avançada.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: 7045bd4d3343d0c11c6cd52fa02cdc005175b8a7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772929"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Usar UrhoSharp em xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>O que é UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) é um poderoso mecanismo 3D para desenvolvedores de Xamarin e .NET. O [introduction](~/graphics-games/urhosharp/introduction.md) explica mais sobre a biblioteca de UrhoSharp, e [estas notas](~/graphics-games/urhosharp/using.md) descrevem como programar cenas e ações.

UrhoSharp pode ser usado para renderizar elementos gráficos em aplicativos xamarin. Forms.
Isso [amostra](https://github.com/xamarin/urho-samples/tree/master/FormsSample) demonstra como UrhoSharp poderia ser usada para construir um gráfico 3D interativo:

![](urhosharp-images/ios-animation.gif "Gráfico 3D interativos de UrhoSharp no iOS")
![](urhosharp-images/android-animation.gif "gráfico 3D interativos de UrhoSharp no Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Adicionando os pacotes do Nuget do UrhoSharp

Antes de usar UrhoSharp, os desenvolvedores precisam adicionar o pacote Nuget de UrhoSharp à sua solução. Este guia pressupõe que um projeto xamarin. Forms com um iOS, Android e o .NET Standard projeto de biblioteca. Todo o código será gravado no projeto da biblioteca .NET Standard; mas, o UrhoSharp Nuget devem ser adicionado a projetos do Android e iOS muito.

O pacote do UrhoSharp.Forms Nuget contém todos os objetos necessários para criar objetos de UrhoSharp. O pacote do nuget UrhoSharp.Forms inclui o `UrhoSurface` classe, que é usada para hospedar o UrhoSharp em xamarin. Forms.
Para começar, clique com botão direito no **pacotes** pasta no projeto de biblioteca .NET Standard e selecione **adicionar pacotes...** . Insira o termo de pesquisa **UrhoSharp.Forms**, selecione **UrhoSharp para xamarin. Forms**, em seguida, clique em **Adicionar pacote**.

[![](urhosharp-images/add-package-sml.png "Adicionar caixa de diálogo pacotes")](urhosharp-images/add-package.png#lightbox "Adicionar caixa de diálogo de pacotes")

O pacote do UrhoSharp.Forms NuGet será adicionado ao projeto:

![](urhosharp-images/packages.png "Pasta de pacotes")

Repita as etapas acima para projetos específicos da plataforma (por exemplo, iOS e Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Passo a passo: Adicionando UrhoSharp a um aplicativo Xamarin. Forms

Estas etapas descrevem o código no exemplo do xamarin. Forms UrhoSharp:

1. [Criar uma página de formulários do Xamarin](#1)
2. [Adicionar o UrhoSurface](#2)
3. [Criar um aplicativo Urho](#3)
4. [Adicione a classe de gráficos para o UrhoSurface](#4)
5. [Interagindo com UrhoSharp](#5)

Observe que o exemplo usa C# recursos de 6 e podem não ser compilados em versões anteriores do Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. Criar uma página de formulários do Xamarin

O código a seguir mostra uma página do xamarin. Forms `UrhoPage` antes que qualquer código relacionado ao Urho foi adicionado:

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

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2. Adicionar o UrhoSurface

UrhoSharp pode ser hospedado em um `ContentPage` como outros controles do xamarin. Forms.
O trecho de código abaixo mostra um `UrhoSurface` adicionado à página xamarin. Forms:

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

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3. Criar um aplicativo Urho

Consulte o `Charts` classe para a implementação dos gráficos 3D Urho usados neste exemplo. A estrutura de tópicos de código básico é mostrada abaixo – Observe que a classe implementa `Urho.Application` que é diferente para o `Xamarin.Forms.Application` que é implementado na classe **App.cs**.

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

O [UrhoSharp documentação](~/graphics-games/urhosharp/index.md) contém mais informações sobre como criar ações e cenas 3D.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Adicione a classe de gráficos para o UrhoSurface

Use o `UrhoSurface.Show<T>` método genérico para adicionar o aplicativo Urho para a página do xamarin. Forms. O trecho de código a seguir mostra o código adicional necessário para criar o `Charts` classe:

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

Observação: os `Show<T>` método é assíncrono e deve ser chamado com o `await` palavra-chave.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. Interagindo com UrhoSharp

O exemplo permite que as barras do gráfico selecionado e modificados. O `Charts` classe expõe o `Bars` e `SelectedBar` para permitir essa interação.

Cada "bar" tem um manipulador de eventos de seleção adicionado após o `Charts` tenha sido processado classe iterando sobre a exposta `Bars` coleção:

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

O manipulador de eventos usa o valor do xamarin. Forms `Slider` controle para ajustar a altura da barra de ferramentas fornecida:

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

Por fim, conectar os dois `Slider` controles de modo que quando seu valor é alterado, a tela de UrhoSharp é afetada. O controle deslizante primeiro gira a imagem do gráfico 3D e o segundo controle deslizante ajusta a altura da barra de ferramentas selecionada.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

As animações na [superior da página](#what-is-urhosharp) mostram o exemplo em execução.

## <a name="summary"></a>Resumo

Esta página mostra como UrhoSharp pode ser usado para adicionar a visualização de dados 3D ao xamarin. Forms. Leia as [UrhoSharp documentação](~/graphics-games/urhosharp/index.md) para obter mais informações sobre como construir cenas Urho que podem ser incluídas em aplicativos xamarin. Forms usando o método mostrado acima.

## <a name="related-links"></a>Links relacionados

- [Gráficos de exemplo (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
