---
title: ProgressBar do Xamarin. Forms
description: O ProgressBar do Xamarin. Forms é um controle que representa visualmente o progresso como uma barra horizontal que é preenchida com base em uma propriedade float.
ms.prod: xamarin
ms.assetId: C2F85FED-797C-466B-A0FD-E73CFB79B267
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/09/2019
ms.openlocfilehash: 40f3c9a5af29d1782249775b9f3166698eb6221a
ms.sourcegitcommit: 7acff5c5a03a0351962c05beebfc347503d83fe6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73201937"
---
# <a name="xamarinforms-progressbar"></a>ProgressBar do Xamarin. Forms
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

O controle [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) Xamarin. Forms representa visualmente o progresso como uma barra horizontal que é preenchida em uma porcentagem representada por um valor de `float`. A classe de `ProgressBar` herda de [`View`](xref:Xamarin.Forms.View).

As capturas de tela a seguir mostram uma `ProgressBar` no iOS e no Android:

![Captura de tela do ProgressBar no iOS e no Android](progressbar-images/progressbars-default.png "ProgressBar no iOS e Android")

O controle `ProgressBar` define duas propriedades:

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress) é um valor de `float` que representa o andamento atual como um valor de 0 a 1. `Progress` valores menores que 0 serão clamped como 0, os valores maiores que 1 serão clamped como 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor) é um `Color` que afeta a cor da barra interna que representa o progresso atual.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que a `ProgressBar` pode ser estilizada e ser o destino de associações de dados.

O controle `ProgressBar` também define um método `ProgressTo` que anima a barra de seu valor atual para um valor especificado. Para obter mais informações, consulte [animar um ProgressBar](#animate-a-progressbar).

> [!NOTE]
> A `ProgressBar` não aceita a manipulação de usuário para que seja ignorada ao usar a tecla Tab para selecionar controles.

## <a name="create-a-progressbar"></a>Criar um ProgressBar

Um `ProgressBar` pode ser instanciado em XAML. Sua propriedade `Progress` determina a porcentagem de preenchimento da barra de cores interna. O valor padrão da propriedade de `Progress` é 0. O exemplo a seguir mostra como criar uma instância de um `ProgressBar` em XAML com a propriedade opcional `Progress` definida:

```xaml
<ProgressBar Progress="0.5" />
```

Um `ProgressBar` também pode ser criado no código:

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> Não use opções de layout horizontal irrestrito, como `Center`, `Start`ou `End` com `ProgressBar`. No UWP, o `ProgressBar` recolhe para uma barra de largura zero. Mantenha o valor de `HorizontalOptions` padrão de `Fill` e não use uma largura de `Auto` ao colocar um `ProgressBar` em um layout de `Grid`.

## <a name="progressbar-appearance-properties"></a>Propriedades de aparência de ProgressBar

A propriedade `ProgressColor` define a cor da barra interna quando a propriedade `Progress` é maior que zero. O exemplo a seguir mostra como criar uma instância de um `ProgressBar` em XAML com o conjunto de propriedades `ProgressColor`:

```xaml
<ProgressBar ProgressColor="Orange" />
```

A propriedade `ProgressColor` também pode ser definida ao criar um `ProgressBar` no código:

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

As capturas de tela a seguir mostram a `ProgressBar` com a propriedade `ProgressColor` definida como `Color.Orange` no iOS e no Android:

![Captura de tela de ProgressBar com estilo no iOS e Android](progressbar-images/progressbars-styled.png "ProgressBar com estilo no iOS e no Android")

## <a name="animate-a-progressbar"></a>Animar um ProgressBar

O método `ProgressTo` anima a `ProgressBar` de seu valor de `Progress` atual para um valor fornecido ao longo do tempo. O método aceita um `float` valor de progresso, uma duração de `uint` em milissegundos, um valor de enumeração `Easing` e retorna um `Task<bool>`. O código a seguir demonstra como animar um `ProgressBar`:

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Para obter mais informações sobre a enumeração de `Easing`, consulte [funções de atenuação no Xamarin. Forms](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Links relacionados

* [Demonstrações de ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
