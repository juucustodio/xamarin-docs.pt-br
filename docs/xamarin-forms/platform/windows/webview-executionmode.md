---
title: Modo de execução do WebView no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do Windows que define o thread no qual uma WebView hospeda seu conteúdo.
ms.prod: xamarin
ms.assetid: 4D3C4112-0FF6-47F8-BC22-579D92032807
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 314851c4697e95ef8662710c986d0b175249fad6
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940683"
---
# <a name="webview-execution-mode-on-windows"></a>Modo de execução do WebView no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma define o thread no qual um [`WebView`](xref:Xamarin.Forms.WebView) hospeda seu conteúdo. Ele é consumido em XAML definindo a `WebView.ExecutionMode` propriedade vinculável como um `WebViewExecutionMode` valor de enumeração:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.ExecutionMode="SeparateThread" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

WebView webView = new Xamarin.Forms.WebView();
webView.On<Windows>().SetExecutionMode(WebViewExecutionMode.SeparateThread);
```

O `WebView.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O `WebView.SetExecutionMode` método, no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para definir o thread no qual um `WebView` hospeda seu conteúdo, com a `WebViewExecutionMode` Enumeração fornecendo três valores possíveis:

- `SameThread` indica que o conteúdo está hospedado no thread da interface do usuário. Esse é o valor padrão para o `WebView` no Windows.
- `SeparateThread` indica que o conteúdo está hospedado em um thread em segundo plano.
- `SeparateProcess` indica que o conteúdo está hospedado em um processo separado fora do processo do aplicativo. Não há um processo separado por instância do WebView e, portanto, todas as instâncias do WebView de um aplicativo compartilham o mesmo processo separado.

Além disso, o `GetExecutionMode` método pode ser usado para retornar o atual `WebViewExecutionMode` para o `WebView` .

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
