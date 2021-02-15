---
title: Diretório de imagens padrão no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do Windows que define o diretório no projeto do qual os ativos de imagem serão carregados.
ms.prod: xamarin
ms.assetid: 537A032B-74DD-4D43-864E-7D7113286D0D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4dd8655d4cd84f7d4214801e55677706eb67efa7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374674"
---
# <a name="default-image-directory-on-windows"></a>Diretório de imagens padrão no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa Plataforma Universal do Windows específica à plataforma define o diretório no projeto do qual os ativos de imagem serão carregados. Ele é consumido em XAML definindo o `Application.ImageDirectory` para um `string` que representa o diretório do projeto que contém os ativos de imagem:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
             ...
             windows:Application.ImageDirectory="Assets">
    ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
Application.Current.On<Windows>().SetImageDirectory("Assets");
```

O `Application.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O `Application.SetImageDirectory` método, no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para especificar o diretório do projeto do qual as imagens serão carregadas. Além disso, o `GetImageDirectory` método pode ser usado para retornar um `string` que representa o diretório do projeto que contém os ativos de imagem do aplicativo.

O resultado é que todas as imagens usadas em um aplicativo serão carregadas do diretório do projeto especificado.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)