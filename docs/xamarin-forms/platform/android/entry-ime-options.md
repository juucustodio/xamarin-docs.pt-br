---
title: Opções do Editor de método de entrada de entrada no Android
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o Android específicos da plataforma que define o método de entrada de opções do editor para o teclado virtual para uma entrada.
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 930614b2479d0edcdba74ed3f98a169491252c63
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208911"
---
# <a name="entry-input-method-editor-options-on-android"></a>Opções do Editor de método de entrada de entrada no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma Android define o método de entrada opções de IME (editor) para o teclado virtual para um [ `Entry` ](xref:Xamarin.Forms.Entry). Isso inclui a definição no botão de ação do usuário no canto inferior do teclado virtual e as interações com o `Entry`. Ele é consumido em XAML, definindo o [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propriedade anexada a um valor da [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeração:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

O `Entry.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir a opção de ação do método de entrada para o teclado virtual para o [ `Entry` ](xref:Xamarin.Forms.Entry), com o [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) fornecendo os seguintes valores de enumeração:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) – indica que nenhuma chave de ação específica é necessária e que o controle subjacente produzirá o seu próprio se possível. Isso será `Next` ou `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) – indica que nenhuma chave de ação será disponibilizado.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – indica que a chave de ação executará uma operação de "Ir", direcionando o usuário para o destino do texto digitado.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – indica que a chave de ação executa uma operação de "Pesquisar", direcionando o usuário para os resultados da pesquisa para o texto que tiver digitado.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – indica que a chave de ação executará uma operação de "Enviar", fornecendo o texto para seu destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – indica que a chave de ação executará uma operação de "next", direcionando o usuário para o próximo campo que aceita texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – indica que a chave de ação executará uma operação de "done", fechando o teclado virtual.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – indica que a chave de ação executará uma operação "anterior", direcionando o usuário para o campo anterior que aceita texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – a máscara para selecionar opções de ação.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – indica que o verificador ortográfico não saiba do usuário, nem sugerir correções com base no qual o usuário tiver digitado anteriormente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – indica que a interface do usuário não deve estar em tela inteira.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) – indica que nenhuma interface do usuário será mostrada para texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) – indica que nenhuma interface do usuário será exibido para ações personalizadas.

O resultado é que a especificada [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valor é aplicado para o teclado virtual para o [ `Entry` ](xref:Xamarin.Forms.Entry), que define o método de entrada, as opções do editor:

[![Método editor específico da plataforma de entrada de entrada](entry-ime-options-images/entry-imeoptions.png "específicos de plataforma de editor de método de entrada de entrada")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "específicos de plataforma de editor de método de entrada de entrada")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
