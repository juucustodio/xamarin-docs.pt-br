---
title: Xamarin.Forms ProgressBar
description: A barra de progresso do xamarin. Forms é um controle que representa visualmente o progresso como uma barra horizontal que é preenchida com base em uma propriedade de float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 2e2917077575ebc78dbdda8ae55aa230a39a7ba1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837006"
---
# <a name="xamarinforms-progressbar"></a>Xamarin.Forms ProgressBar
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)

O xamarin. Forms [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) é um controle que representa visualmente o progresso como uma barra horizontal que é preenchida para um percentual representado por um `float` valor. O `ProgressBar` herda [ `View` ](xref:Xamarin.Forms.View).

A captura de tela a seguir mostra um `ProgressBar` no iOS e Android:

![Captura de tela da barra de progresso no iOS e Android](progressbar-images/progressbars-default.png "ProgressBar no iOS e Android")

O `ProgressBar` controle define duas propriedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) é um `float` valor que representa o progresso atual como um valor de 0 a 1. `Progress` valores menores que 0 será ser fixado como 0, valores maiores que 1 será ser fixado como 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) é um `Color` que afeta o interior que representa o progresso atual de cor da barra.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que o `ProgressBar` podem ser estilizados e ser o destino de vinculações de dados.

O `ProgressBar` controle também define um `ProgressTo` método que anima a barra de seu valor atual para um valor especificado. Para obter mais informações, consulte [animar uma ProgressBar](#animate-a-progressbar).

> [!NOTE]
> O `ProgressBar` não aceita a manipulação do usuário para que ela é ignorada ao usar a tecla Tab para selecionar os controles.

## <a name="create-a-progressbar"></a>Criar uma barra de progresso

Um `ProgressBar` pode ser instanciada em XAML. Seu `Progress` propriedade pode ser definida para determinar a porcentagem de preenchimento da barra de ferramentas interna, colorida. Se o `Progress` não está definida, o padrão é 0. O exemplo a seguir mostra como instanciar uma `ProgressBar` no XAML com opcional `Progress` conjunto de propriedades:

```xaml
<ProgressBar Progress="0.5" />
```

Um `ProgressBar` também podem ser criados no código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Não use opções de layout horizontal irrestrito, como `Center`, `Start`, ou `End` com `ProgressBar`. Na UWP, o `ProgressBar` se recolhe para uma barra de largura zero. Mantenha o padrão `HorizontalOptions` valor de `Fill` e não use uma largura de `Auto` quando colocar uma `ProgressBar` em um `Grid` layout.

## <a name="progressbar-appearance-properties"></a>Propriedades de aparência da barra de progresso

O `ProgressColor` propriedade pode ser definida para definir a barra de ferramentas interna de cor quando o `Progress` propriedade é maior que zero. O exemplo a seguir mostra como instanciar uma `ProgressBar` no XAML com o `ProgressColor` conjunto de propriedades:

```xaml
<ProgressBar OnColor="Orange" />
```

O `ProgressColor` propriedade também pode ser definida durante a criação de um `ProgressBar` no código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

A captura de tela a seguir mostra a `ProgressBar` com o `ProgressColor` propriedade definida como `Color.Orange` no iOS e Android:

![Captura de tela da barra de progresso com estilo definido no iOS e Android](progressbar-images/progressbars-styled.png "estilizado ProgressBar no iOS e Android")

## <a name="animate-a-progressbar"></a>Animar uma ProgressBar

O `ProgressTo` método anima a `ProgressBar` partir de seu `Progress` valor para um valor fornecido ao longo do tempo. O método aceita uma `float` progride do valor, uma `uint` duração em milissegundos, um `Easing` valor de enumeração e retorna um `Task<bool>`. O código a seguir demonstra como animar um `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obter mais informações sobre o `Easing` enumeração, consulte [funções de Easing no xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Links relacionados

* [Demonstrações do ProgressBar](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ProgressBarDemos)
