---
title: Introdução ao ContentProviders
description: O sistema operacional Android usa provedores de conteúdo para facilitar o acesso aos dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-lo.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 4105200c48e41b142fc71e3a524023790b683cdb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105981"
---
# <a name="intro-to-contentproviders"></a>Introdução ao ContentProviders

_O sistema operacional Android usa provedores de conteúdo para facilitar o acesso aos dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-lo._


## <a name="content-providers-overview"></a>Visão geral de provedores de conteúdo

Um *ContentProvider* encapsula um repositório de dados e fornece uma API para acessá-lo. O provedor existe como parte de um aplicativo Android que geralmente também fornece uma interface do usuário para exibir e gerenciar os dados. O principal benefício do uso de um provedor de conteúdo está permitindo que os outros aplicativos acessar facilmente os dados encapsulados usando um objeto de cliente do provedor (chamado de um *ContentResolver*). Juntos, um provedor de conteúdo e o resolvedor de conteúdo oferecem uma API consistente entre aplicativos para acesso a dados que é simple criar e consumir. Qualquer aplicativo pode optar por usar `ContentProviders` para gerenciar dados internamente e também para expô-lo para outros aplicativos.

Um `ContentProvider` também é necessária para seu aplicativo fornecer sugestões de pesquisa personalizada, ou se você deseja fornecer a capacidade de copiar dados complexos de seu aplicativo para colar em outros aplicativos. Este documento mostra como acessar e criar `ContentProviders` com xamarin. Android.

A estrutura desta seção é da seguinte maneira:

- **Como funciona** &ndash; uma visão geral do que o `ContentProvider` foi projetado para e como ele funciona.

- **Consumo de um provedor de conteúdo** &ndash; um exemplo de acessar a lista de contatos.

- **Usar o ContentProvider para compartilhar dados** &ndash; gravando e consumindo um `ContentProvider` no mesmo aplicativo.

`ContentProviders` e os cursores que operam em seus dados geralmente são usados para preencher os ListViews. Consulte a [guia ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre como usar essas classes.

`ContentProviders` exposto pelo Android (ou outros aplicativos) são uma maneira fácil de incluir dados de outras fontes em seu aplicativo. Eles permitem acesso e apresentar dados, como a lista de contatos, fotos ou eventos de calendário de dentro de seu aplicativo e permitir que o usuário interagir com os dados.

Personalizado `ContentProviders` são uma maneira conveniente de empacotar seus dados para uso dentro de seu próprio aplicativo ou para uso por outros aplicativos (incluindo usos especiais, como pesquisa personalizada e copiar/colar).

Os tópicos nesta seção fornecem alguns exemplos simples de consumo e a gravação de `ContentProvider` código.



## <a name="related-links"></a>Links relacionados

- [Demonstração de ContactsAdapter (amostra)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (amostra)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Guia de desenvolvedores de provedores de conteúdo](http://developer.android.com/guide/topics/providers/content-providers.html)
- [Referência de classe ContentProvider](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Referência de classe ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Referência de classe de ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Referência de classe CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Referência de classe UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Referência de classe ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
