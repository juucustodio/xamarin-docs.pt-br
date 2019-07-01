---
title: 'Xamarin.Essentials: Área de Transferência'
description: Este documento descreve a classe Clipboard no Xamarin.Essentials, que permite copiar e colar o texto para a área de transferência do sistema entre aplicativos.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: c186f5c61bd2fa3df305be92a03135e57e302d02
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352124"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Área de Transferência

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
await Clipboard.SetTextAsync("Hello World");
```

Para ler o texto da **Área de Transferência**:

```csharp
var text = await Clipboard.GetTextAsync();
```

> [!TIP]
> O acesso à Área de Transferência deve ser feito no thread de interface do usuário principal. Confira a API do [MainThread](~/essentials/main-thread.md) para ver como invocar métodos no thread de interface do usuário principal.

## <a name="api"></a>API

- [Código-fonte de Área de Transferência](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Documentação de API de Área de Transferência](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
