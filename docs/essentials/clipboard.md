---
title: 'Xamarin.Essentials: área de transferência'
description: Este documento descreve a classe Xamarin.Essentials, que permite que você copie e cole o texto para a área de transferência entre aplicativos de área de transferência.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 41b15b480fa23bd49667b68e904043e4f1a95732
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782353"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: área de transferência

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
Clipboard.SetText("Hello World");
```

Ler texto a partir de **área de transferência**:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Código-fonte da área de transferência](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentação da API da área de transferência](xref:Xamarin.Essentials.Clipboard)
