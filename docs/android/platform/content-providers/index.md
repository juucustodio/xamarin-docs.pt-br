---
title: Introdução ao ContentProviders
description: O sistema operacional Android usa provedores de conteúdo para facilitar o acesso a dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-la.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027551"
---
# <a name="intro-to-contentproviders"></a>Introdução ao ContentProviders

_O sistema operacional Android usa provedores de conteúdo para facilitar o acesso a dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-la._

## <a name="content-providers-overview"></a>Visão geral dos provedores de conteúdo

Um *ContentProvider* encapsula um repositório de dados e fornece uma API para acessá-lo. O provedor existe como parte de um aplicativo Android que geralmente também fornece uma interface do usuário para exibir/gerenciar os dados. O principal benefício de usar um provedor de conteúdo é permitir que outros aplicativos acessem facilmente os dados encapsulados usando um objeto de cliente de provedor (chamado de *ContentResolver retornem*). Juntos, um provedor de conteúdo e um resolvedor de conteúdo oferecem uma API entre aplicativos consistente para acesso a dados que é simples de criar e consumir. Qualquer aplicativo pode optar por usar `ContentProviders` para gerenciar dados internamente e também para expô-los a outros aplicativos.

Um `ContentProvider` também é necessário para que seu aplicativo forneça sugestões de pesquisa personalizadas, ou se você quiser fornecer a capacidade de copiar dados complexos de seu aplicativo para colar em outros aplicativos. Este documento mostra como acessar e criar `ContentProviders` com o Xamarin. Android.

A estrutura desta seção é a seguinte:

- **Como funciona** &ndash; uma visão geral do que o `ContentProvider` foi projetado e como ele funciona.

- **Consumindo um provedor de conteúdo** &ndash; um exemplo de acesso à lista de contatos.

- **Usar o ContentProvider para compartilhar dados** &ndash; gravar e consumir um `ContentProvider` no mesmo aplicativo.

`ContentProviders` e os cursores que operam em seus dados são frequentemente usados para preencher ListViews. Consulte o [Guia de ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre como usar essas classes.

`ContentProviders` expostas pelo Android (ou outros aplicativos) são uma maneira fácil de incluir dados de outras fontes em seu aplicativo. Eles permitem que você acesse e apresente dados como a lista de contatos, fotos ou eventos de calendário de dentro de seu aplicativo e permite que o usuário interaja com esses dados.

Os `ContentProviders` personalizados são uma maneira conveniente de empacotar seus dados para uso dentro de seu próprio aplicativo ou para uso por outros aplicativos (incluindo usos especiais, como pesquisa personalizada e copiar/colar).

Os tópicos desta seção fornecem alguns exemplos simples de consumo e gravação de `ContentProvider` código.

## <a name="related-links"></a>Links relacionados

- [Demonstração do ContactsAdapter (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guia de desenvolvedores de provedores de conteúdo](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Referência de classe ContentProvider](xref:Android.Content.ContentProvider)
- [Referência de classe ContentResolver retornem](xref:Android.Content.ContentResolver)
- [Referência de classe ListView](xref:Android.Widget.ListView)
- [Referência de classe CursorAdapter](xref:Android.Widget.CursorAdapter)
- [Referência de classe UriMatcher](xref:Android.Content.UriMatcher)
- [Android. Provider](xref:Android.Provider)
- [Referência de classe ContactsContract](xref:Android.Provider.ContactsContract)
