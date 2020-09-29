---
title: Introdução ao ContentProviders
description: O sistema operacional Android usa provedores de conteúdo para facilitar o acesso a dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-la.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 00da8b668ea26aa9146ff0c30d07a60cefe6695a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454007"
---
# <a name="intro-to-contentproviders"></a>Introdução ao ContentProviders

_O sistema operacional Android usa provedores de conteúdo para facilitar o acesso a dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-la._

## <a name="content-providers-overview"></a>Visão geral dos provedores de conteúdo

Um *ContentProvider* encapsula um repositório de dados e fornece uma API para acessá-lo. O provedor existe como parte de um aplicativo Android que geralmente também fornece uma interface do usuário para exibir/gerenciar os dados. O principal benefício de usar um provedor de conteúdo é permitir que outros aplicativos acessem facilmente os dados encapsulados usando um objeto de cliente de provedor (chamado de *ContentResolver retornem*). Juntos, um provedor de conteúdo e um resolvedor de conteúdo oferecem uma API entre aplicativos consistente para acesso a dados que é simples de criar e consumir. Qualquer aplicativo pode optar por usar o `ContentProviders` para gerenciar dados internamente e também para expô-los a outros aplicativos.

Um `ContentProvider` também é necessário para que seu aplicativo forneça sugestões de pesquisa personalizadas, ou se você quiser fornecer a capacidade de copiar dados complexos de seu aplicativo para colar em outros aplicativos. Este documento mostra como acessar e compilar `ContentProviders` com o Xamarin. Android.

A estrutura desta seção é a seguinte:

- **Como funciona** &ndash; Uma visão geral do que o `ContentProvider` foi projetado para o e como ele funciona.

- **Consumindo um provedor** &ndash; de conteúdo Um exemplo de acesso à lista de contatos.

- **Usando o ContentProvider para compartilhar dados** &ndash; Gravando e consumindo um `ContentProvider` no mesmo aplicativo.

`ContentProviders` e os cursores que operam em seus dados são frequentemente usados para preencher ListViews. Consulte o [Guia de ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre como usar essas classes.

`ContentProviders` exposto pelo Android (ou outros aplicativos) são uma maneira fácil de incluir dados de outras fontes em seu aplicativo. Eles permitem que você acesse e apresente dados como a lista de contatos, fotos ou eventos de calendário de dentro de seu aplicativo e permite que o usuário interaja com esses dados.

Personalizado `ContentProviders` são uma maneira conveniente de empacotar seus dados para uso dentro de seu próprio aplicativo ou para uso por outros aplicativos (incluindo usos especiais, como pesquisa personalizada e copiar/colar).

Os tópicos nesta seção fornecem alguns exemplos simples de consumo e gravação de `ContentProvider` código.

## <a name="related-links"></a>Links Relacionados

- [Demonstração do ContactsAdapter (exemplo)](/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (exemplo)](/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guia de desenvolvedores de provedores de conteúdo](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Referência de classe ContentProvider](xref:Android.Content.ContentProvider)
- [Referência de classe ContentResolver retornem](xref:Android.Content.ContentResolver)
- [Referência de classe ListView](xref:Android.Widget.ListView)
- [Referência de classe CursorAdapter](xref:Android.Widget.CursorAdapter)
- [Referência de classe UriMatcher](xref:Android.Content.UriMatcher)
- [Android. Provider](xref:Android.Provider)
- [Referência de classe ContactsContract](xref:Android.Provider.ContactsContract)