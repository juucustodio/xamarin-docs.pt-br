---
title: Introdução a Provedores de Conteúdo
description: O sistema operacional Android usa provedores de conteúdo para facilitar o acesso a dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo introduz a classe ContentProvider e fornece dois exemplos de como usá-lo.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027551"
---
# <a name="intro-to-contentproviders"></a>Introdução a Provedores de Conteúdo

_O sistema operacional Android usa provedores de conteúdo para facilitar o acesso a dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo introduz a classe ContentProvider e fornece dois exemplos de como usá-lo._

## <a name="content-providers-overview"></a>Visão geral dos provedores de conteúdo

Um *ContentProvider* encapsula um repositório de dados e fornece uma API para acessá-lo. O provedor existe como parte de um aplicativo para Android que geralmente também fornece uma interface do usuário para exibir/gerenciar os dados. O principal benefício de usar um provedor de conteúdo é permitir que outros aplicativos acessem facilmente os dados encapsulados usando um objeto cliente do provedor (chamado *de ContentResolver*). Juntos, um provedor de conteúdo e um resolver conteúdo oferecem uma API inter-aplicativo consistente para acesso a dados que é simples de construir e consumir. Qualquer aplicativo pode `ContentProviders` optar por usar para gerenciar dados internamente e também para expô-los a outros aplicativos.

A `ContentProvider` também é necessário para que seu aplicativo forneça sugestões de pesquisa personalizadas, ou se você quiser fornecer a capacidade de copiar dados complexos do seu aplicativo para colar em outros aplicativos. Este documento mostra como `ContentProviders` acessar e construir com Xamarin.Android.

A estrutura desta seção é a seguinte:

- **Como funciona** &ndash; Uma visão geral `ContentProvider` do que o projetado para e como ele funciona.

- **Consumir um provedor de** &ndash; conteúdo Um exemplo acessando a lista Contatos.

- **Usando o ContentProvider para compartilhar** &ndash; `ContentProvider` dados Escrevendo e consumindo um no mesmo aplicativo.

`ContentProviders`e os cursores que operam em seus dados são frequentemente usados para preencher ListViews. Consulte o [guia ListViews and Adapters](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre como usar essas classes.

`ContentProviders`expostos pelo Android (ou outros aplicativos) são uma maneira fácil de incluir dados de outras fontes em seu aplicativo. Eles permitem que você acesse e apresente dados, como a lista contatos, fotos ou eventos de calendário de dentro do seu aplicativo, e permite que o usuário interaja com esses dados.

O `ContentProviders` costume é uma maneira conveniente de empacotar seus dados para uso dentro do seu próprio aplicativo, ou para uso por outros aplicativos (incluindo usos especiais como pesquisa personalizada e cópia/colar).

Os tópicos desta seção fornecem alguns `ContentProvider` exemplos simples de código de consumo e escrita.

## <a name="related-links"></a>Links relacionados

- [Demonstração do Adaptador de Contatos (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guia de Desenvolvedores de Provedores de Conteúdo](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Referência de classe de provedor de conteúdo](xref:Android.Content.ContentProvider)
- [Referência de classe ContentResolver](xref:Android.Content.ContentResolver)
- [ListVer Referência de classe](xref:Android.Widget.ListView)
- [Referência de classe cursorAdapter](xref:Android.Widget.CursorAdapter)
- [Referência da classe UriMatcher](xref:Android.Content.UriMatcher)
- [Android.Provider](xref:Android.Provider)
- [Referência de classe de contrato de contatos](xref:Android.Provider.ContactsContract)
