---
title: ProgressBar do Xamarin. Forms
description: O ProgressBar do Xamarin. Forms é um controle que representa visualmente o progresso como uma barra horizontal que é preenchida com base em uma propriedade float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 78c5f38428e20a2e0c6a15d0964f8fd505a8d082
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739408"
---
# <a name="xamarinforms-progressbar"></a>ProgressBar do Xamarin. Forms
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

O Xamarin. Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) é um controle que representa visualmente o progresso como uma barra horizontal que é preenchida para um percentual `float` representado por um valor. A `ProgressBar` classe é herdada de [`View`](xref:Xamarin.Forms.View).

A captura de tela a `ProgressBar` seguir mostra um no Ios e Android:

![Captura de tela do ProgressBar no Ios e no Android](progressbar-images/progressbars-default.png "ProgressBar no Ios e Android")

O `ProgressBar` controle define duas propriedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)é um `float` valor que representa o andamento atual como um valor de 0 a 1. `Progress`valores menores que 0 serão clamped como 0, os valores maiores que 1 serão clamped como 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)é um `Color` que afeta a cor da barra interna que representa o andamento atual.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que `ProgressBar` o pode ser estilizado e ser o destino de associações de dados.

O `ProgressBar` controle também define um `ProgressTo` método que anima a barra de seu valor atual para um valor especificado. Para obter mais informações, consulte [animar um ProgressBar](#animate-a-progressbar).

> [!NOTE]
> O `ProgressBar` não aceita a manipulação de usuário para que seja ignorado ao usar a tecla Tab para selecionar controles.

## <a name="create-a-progressbar"></a>Criar um ProgressBar

Um `ProgressBar` pode ser instanciado em XAML. Sua `Progress` propriedade pode ser definida para determinar a porcentagem de preenchimento da barra de cores interna. Se a `Progress` propriedade não for definida, o padrão será 0. O exemplo a seguir mostra como criar uma `ProgressBar` instância de em XAML com `Progress` o conjunto de propriedades opcional:

```xaml
<ProgressBar Progress="0.5" />
```

Um `ProgressBar` também pode ser criado no código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Não use opções de layout horizontal irrestrito, `Center`como, `Start`ou `End` com `ProgressBar`. No UWP, o `ProgressBar` recolhido para uma barra de largura zero. Mantenha o valor `HorizontalOptions` padrão de `Fill` e não use uma largura de `Auto` quando colocar um `ProgressBar` em um `Grid` layout.

## <a name="progressbar-appearance-properties"></a>Propriedades de aparência de ProgressBar

A `ProgressColor` propriedade pode ser definida para definir a cor da barra interna quando `Progress` a propriedade for maior que zero. O exemplo a seguir mostra como criar uma `ProgressBar` instância de em XAML `ProgressColor` com o conjunto de propriedades:

```xaml
<ProgressBar OnColor="Orange" />
```

A `ProgressColor` Propriedade também pode ser definida ao criar um `ProgressBar` no código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

A captura de tela a `ProgressBar` seguir mostra `ProgressColor` o com a `Color.Orange` propriedade definida como em Ios e Android:

![Captura de tela de ProgressBar com estilo no Ios e Android](progressbar-images/progressbars-styled.png "ProgressBar com estilo no Ios e no Android")

## <a name="animate-a-progressbar"></a>Animar um ProgressBar

O `ProgressTo` método anima o `ProgressBar` de seu valor atual `Progress` para um valor fornecido ao longo do tempo. O método aceita um `float` valor de progresso, `uint` uma duração em milissegundos, `Easing` um valor de enumeração e `Task<bool>`retorna um. O código a seguir demonstra como animar `ProgressBar`um:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obter mais informações sobre `Easing` a enumeração, consulte [facilitando funções no Xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Links relacionados

* [Demonstrações de ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
