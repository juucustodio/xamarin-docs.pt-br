---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bb77e9fafe39bf76a7d4290dba0bc658cd15094f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140030"
---
# <a name="entry-input-method-editor-options-on-android"></a>Opções do editor de método de entrada de entrada no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma Android define as opções do IME (editor de método de entrada) para o teclado soft de um [`Entry`](xref:Xamarin.Forms.Entry) . Isso inclui a configuração do botão de ação do usuário no canto inferior do teclado soft e as interações com o `Entry` . Ele é consumido em XAML definindo a [`Entry.ImeOptions`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) Propriedade anexada como um valor da [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeração:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

O `Entry.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `Entry.SetImeOptions` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. entry. SetImeOptions ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Entry}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. ImeFlags)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir a opção de ação do método de entrada para o teclado soft para o [`Entry`](xref:Xamarin.Forms.Entry) , com a [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeração que fornece os seguintes valores:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default)– indica que nenhuma chave de ação específica é necessária e que o controle subjacente produzirá sua própria, se puder. Isso será `Next` ou `Done` .
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None)– indica que nenhuma chave de ação será disponibilizada.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go)– indica que a chave de ação executará uma operação de "ir", levando o usuário para o destino do texto digitado.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search)– indica que a chave de ação executa uma operação de "pesquisa", levando o usuário para os resultados da pesquisa do texto digitado.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send)– indica que a chave de ação executará uma operação de "envio", fornecendo o texto para seu destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next)– indica que a chave de ação executará uma operação de "Avançar", levando o usuário para o próximo campo que aceitará o texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done)– indica que a chave de ação executará uma operação "concluída", fechando o teclado virtual.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous)– indica que a chave de ação executará uma operação "anterior", levando o usuário para o campo anterior que aceitará o texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction)– a máscara para selecionar opções de ação.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning)– indica que o verificador ortográfico não irá aprender com o usuário nem sugerir correções com base no que o usuário digitou anteriormente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen)– indica que a interface do usuário não deve ficar em tela inteira.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi)– indica que nenhuma interface do usuário será mostrada para texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction)– indica que nenhuma interface do usuário será exibida para ações personalizadas.

O resultado é que um [`ImeFlags`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valor especificado é aplicado ao teclado soft para o [`Entry`](xref:Xamarin.Forms.Entry) , que define as opções do editor de método de entrada:

[![Entrada específica da plataforma do editor de método de entrada](entry-ime-options-images/entry-imeoptions.png "Entrada específica da plataforma do editor de método de entrada")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "Entrada específica da plataforma do editor de método de entrada")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
