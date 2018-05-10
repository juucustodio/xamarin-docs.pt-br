---
title: Área de transferência Xamarin.Essentials
description: A classe de área de transferência permite que você copie e cole o texto para a área de transferência entre aplicativos.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 44ba8090851b65327682b3d290d58fb23bb8aae4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
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

- [Código-fonte da área de transferência](https://github.com/xamarin/Essentials/tree/master/Essentials/Clipboard)
- [Documentação da API da área de transferência](xref:Xamarin.Essentials.Clipboard)
