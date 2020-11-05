---
title: Xamarin.Forms ProgressBar
description: O Xamarin.Forms ProgressBar é um controle que representa visualmente o progresso como uma barra horizontal que é preenchida com base em uma propriedade float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 824851c1406dfefb5f276be069f92040d03a5c98
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370514"
---
# <a name="no-locxamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

O Xamarin.Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) controle representa visualmente o progresso como uma barra horizontal que é preenchida em um percentual representado por um `float` valor. A `ProgressBar` classe é herdada de [`View`](xref:Xamarin.Forms.View) .

As capturas de tela a seguir mostram um `ProgressBar` no Ios e no Android:

![Captura de tela do ProgressBar no iOS e no Android](progressbar-images/progressbars-default.png "ProgressBar no iOS e Android")

O `ProgressBar` controle define duas propriedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) é um `float` valor que representa o andamento atual como um valor de 0 a 1. `Progress` valores menores que 0 serão clamped como 0, os valores maiores que 1 serão clamped como 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) é um `Color` que afeta a cor da barra interna que representa o andamento atual.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que o `ProgressBar` pode ser estilizado e ser o destino de associações de dados.

O `ProgressBar` controle também define um `ProgressTo` método que anima a barra de seu valor atual para um valor especificado. Para obter mais informações, consulte [animar um ProgressBar](#animate-a-progressbar).

> [!NOTE]
> O `ProgressBar` não aceita a manipulação de usuário para que seja ignorado ao usar a tecla Tab para selecionar controles.

## <a name="create-a-progressbar"></a>Criar um ProgressBar

Um `ProgressBar` pode ser instanciado em XAML. Sua `Progress` propriedade determina a porcentagem de preenchimento da barra de cores interna. O `Progress` valor da propriedade padrão é 0. O exemplo a seguir mostra como criar uma instância de `ProgressBar` em XAML com o `Progress` conjunto de propriedades opcional:

```xaml
<ProgressBar Progress="0.5" />
```

Um `ProgressBar` também pode ser criado no código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Não use opções de layout horizontal irrestrito, como `Center` , `Start` ou `End` com `ProgressBar` . No UWP, o `ProgressBar` recolhido para uma barra de largura zero. Mantenha o `HorizontalOptions` valor padrão de `Fill` e não use uma largura de `Auto` quando colocar um `ProgressBar` em um `Grid` layout.

## <a name="progressbar-appearance-properties"></a>Propriedades de aparência de ProgressBar

A `ProgressColor` propriedade define a cor da barra interna quando a `Progress` propriedade é maior que zero. O exemplo a seguir mostra como criar uma instância de `ProgressBar` em XAML com o `ProgressColor` conjunto de propriedades:

```xaml
<ProgressBar ProgressColor="Orange" />
```

A `ProgressColor` propriedade também pode ser definida ao criar um `ProgressBar` no código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

As capturas de tela a seguir mostram o `ProgressBar` com a `ProgressColor` propriedade definida como `Color.Orange` em Ios e Android:

![Captura de tela de ProgressBar com estilo no iOS e Android](progressbar-images/progressbars-styled.png "ProgressBar com estilo no iOS e no Android")

## <a name="animate-a-progressbar"></a>Animar um ProgressBar

O `ProgressTo` método anima o `ProgressBar` de seu valor atual `Progress` para um valor fornecido ao longo do tempo. O método aceita um `float` valor de progresso, uma `uint` duração em milissegundos, um `Easing` valor de enumeração e retorna um `Task<bool>` . O código a seguir demonstra como animar um `ProgressBar` :

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obter mais informações sobre a `Easing` enumeração, consulte [facilitando Xamarin.Forms funções no ](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Links relacionados

* [Demonstrações de ProgressBar](/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)