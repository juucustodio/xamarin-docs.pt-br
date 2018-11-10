---
title: 'Xamarin.Essentials: Área de transferência'
description: Este documento descreve a classe Clipboard no Xamarin.Essentials, que permite copiar e colar o texto para a área de transferência do sistema entre aplicativos.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8dd238da678dfb5773801137d313b286590aa463
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675530"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Área de transferência

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Clipboard** permite que você copie e cole o texto para a área de transferência do sistema entre aplicativos.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Como usar Clipboard

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Para verificar se a **Área de Transferência** tem algum texto pronto para ser colado:

```csharp
var hasText = Clipboard.HasText;
```

Para definir o texto na **Área de Transferência**:

```csharp
Clipboard.SetText("Hello World");
```

Para ler o texto da **Área de Transferência**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Código-fonte de Área de Transferência](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentação de API de Área de Transferência](xref:Xamarin.Essentials.Clipboard)
