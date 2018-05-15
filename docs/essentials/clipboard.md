---
title: Área de transferência Xamarin.Essentials
description: A classe de área de transferência permite que você copie e cole o texto para a área de transferência entre aplicativos.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 67a0218325918b57e5ed2618b57d52d3fe3ee820
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-clipboard"></a>Área de transferência Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **área de transferência** classe permite que você copie e cole o texto para a área de transferência entre aplicativos.

## <a name="using-clipboard"></a>Usando a área de transferência

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para verificar se o **área de transferência** tem o texto está pronto para ser colada:

```csharp
var hasText = Clipboard.HasText;
```

Para definir o texto para o **área de transferência**:

```csharp
ClipBoard.SetText("Hello World");
```

Ler texto a partir de **área de transferência**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Código-fonte da área de transferência](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentação da API da área de transferência](xref:Xamarin.Essentials.Clipboard)
