---
title: "Introdução ao ContentProviders"
description: "O sistema operacional Android usa provedores de conteúdo para facilitar o acesso aos dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-lo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 53408220f1bbd3b0aa97e0c54bd8f4c09847b448
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="intro-to-contentproviders"></a>Introdução ao ContentProviders

_O sistema operacional Android usa provedores de conteúdo para facilitar o acesso aos dados compartilhados, como arquivos de mídia, contatos e informações de calendário. Este artigo apresenta a classe ContentProvider e fornece dois exemplos de como usá-lo._


## <a name="content-providers-overview"></a>Visão geral dos provedores de conteúdo

Um *ContentProvider* encapsula um repositório de dados e fornece uma API para acessá-lo. O provedor existe como parte de um aplicativo do Android que geralmente também fornece uma interface de usuário para exibir e gerenciar os dados. O principal benefício de usar um provedor de conteúdo é habilitar outros aplicativos para acessar facilmente os dados encapsulados usando um objeto de cliente do provedor (chamado um *ContentResolver*). Juntos, um provedor de conteúdo e o resolvedor de conteúdo oferecem uma API consistente entre aplicativos para acesso a dados que é mais simple de criar e consumir. Qualquer aplicativo pode optar por usar `ContentProviders` para gerenciar dados internamente e também para expô-lo para outros aplicativos.

Um `ContentProvider` também é necessário para seu aplicativo para fornecer sugestões de pesquisa personalizada, ou se você deseja fornecer a capacidade de copiar dados complexos de seu aplicativo para colar em outros aplicativos. Este documento mostra como acessar e criar `ContentProviders` com xamarin.

A estrutura desta seção é da seguinte maneira:

- **Como ele funciona** &ndash; uma visão geral do que o `ContentProvider` foi projetado para e como ele funciona.

- **Consumindo um provedor de conteúdo** &ndash; um exemplo de acessar a lista de contatos.

- **Usando ContentProvider para compartilhar dados** &ndash; escrita e consumir um `ContentProvider` no mesmo aplicativo.

`ContentProviders` e os cursores que operam em seus dados geralmente são usados para popular ListViews. Consulte o [guia ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre como usar essas classes.

`ContentProviders` expostos pelo Android (ou outros aplicativos) são uma maneira fácil de incluir dados de outras fontes em seu aplicativo. Eles permitem acesso e apresentar dados como a lista de contatos, fotos ou eventos de calendário de dentro de seu aplicativo e permitir que o usuário interagir com esses dados.

Personalizado `ContentProviders` são uma maneira conveniente para empacotar seus dados para uso dentro de seu próprio aplicativo ou para uso por outros aplicativos (incluindo usos especiais, como pesquisa personalizada e copiar/colar).

Os tópicos nesta seção fornecem alguns exemplos simples de consumo e gravando `ContentProvider` código.



## <a name="related-links"></a>Links relacionados

- [Demonstração de ContactsAdapter (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Guia de desenvolvedores de provedores de conteúdo](http://developer.android.com/guide/topics/providers/content-providers.html)
- [Referência de classe ContentProvider](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Referência de classe ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Referência de classe de ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Referência de classe CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Referência de classe UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Referência de classe ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
