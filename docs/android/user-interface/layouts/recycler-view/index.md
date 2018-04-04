---
title: RecyclerView
description: RecyclerView é um grupo de exibição para exibir coleções; ele é projetado para ser uma substituição mais flexível para grupos de modo de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar aplicativos xamarin RecyclerView.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/03/2018
ms.openlocfilehash: 187339244d53c154cc22672a3d2ceba7e0a75bcf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView é um grupo de exibição para exibir coleções; ele é projetado para ser uma substituição mais flexível para grupos de modo de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar aplicativos xamarin RecyclerView._

## <a name="recyclerview"></a>RecyclerView

Muitos aplicativos precisam exibir coleções do mesmo tipo (como mensagens, contatos, imagens ou músicas); Geralmente, essa coleção é muito grande para caber na tela, para que a coleção é apresentada em uma janela pequena que pode rolar suavemente através de todos os itens na coleção.
`RecyclerView` é um Android widget que exibe uma coleção de itens em uma lista ou uma grade, permitindo que o usuário percorrer a coleção. A seguir está uma captura de tela de um aplicativo de exemplo que usa `RecyclerView` para exibir o conteúdo da caixa de entrada de email em uma lista de rolagem vertical:

[![Exemplo de aplicativo usando RecyclerView para mensagens de entrada da lista](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` oferece dois recursos interessantes:

-  Ele tem uma arquitetura flexível que permite modificar seu comportamento conectando seus componentes preferenciais.

-  É eficiente com grandes coleções porque ele reutiliza os modos de exibição de item e requer o uso de *exibir detentores* para exibir referências de cache.

Este guia explica como usar `RecyclerView` em aplicativos xamarin; ele explica como adicionar a `RecyclerView` pacote para seu projeto de xamarin e descreve como `RecyclerView` funções em um aplicativo típico. Exemplos de código reais são fornecidos para mostrar a você como integrar `RecyclerView` em seu aplicativo, como implementar a exibição de item, clique em e como atualizar `RecyclerView` quando seus dados subjacentes forem alterados. Este guia pressupõe que você esteja familiarizado com o desenvolvimento do xamarin.


### <a name="requirements"></a>Requisitos

Embora `RecyclerView` é geralmente associado pirulito do Android 5.0, é oferecido como uma biblioteca de suporte &ndash; `RecyclerView` funciona com aplicativos que nível de API de destino 7 (Android 2.1) e posterior. O seguinte é necessário para usar `RecyclerView` em aplicativos baseados em Xamarin:

-  **Xamarin** &ndash; xamarin 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-  Seu projeto de aplicativo deve incluir o **Xamarin.Android.Support.v7.RecyclerView** pacote. Para obter mais informações sobre como instalar pacotes do NuGet, consulte [passo a passo: incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Visão geral

`RecyclerView` pode ser pensada como uma substituição para o `ListView` e `GridView` widgets no Android. Como seu antecessores, `RecyclerView` é projetado para exibir um grande conjunto de dados em uma janela pequena, mas `RecyclerView` oferece mais opções de layout e é otimizada para exibir coleções grandes. Se você estiver familiarizado com `ListView`, há várias diferenças importantes entre `ListView` e `RecyclerView`:

-   `RecyclerView` é um pouco mais complexo a ser usado: você precisa escrever código mais usar `RecyclerView` em comparação com `ListView`.

-   `RecyclerView` não fornece um adaptador predefinido; Você deve implementar o código do adaptador que acessa sua fonte de dados. No entanto, o Android inclui vários adaptadores predefinidos que funcionam com `ListView` e `GridView`.

-   `RecyclerView` não tem um evento de clique do item quando um usuário toca em um item; em vez disso, os eventos de clique de item são manipulados pelo classes auxiliares. Por outro lado, `ListView` oferece um evento de clique do item.

-   `RecyclerView` melhora o desempenho por modos de exibição de reciclagem e aplicando o padrão de modo reservado, o que elimina as pesquisas de recursos de layout desnecessário. Uso do proprietário da exibição padrão é opcional em `ListView`.

-   `RecyclerView` baseia-se em um design modular que torna mais fácil personalizar. Por exemplo, você pode conectar uma diretiva de layout diferente sem alterações significativas do código ao seu aplicativo.
    Por outro lado, `ListView` é relativamente monolítico na estrutura.

-   `RecyclerView` inclui animações incorporadas para o item Adicionar e remover. `ListView` animações exigem algum esforço adicional por parte do desenvolvedor do aplicativo.


### <a name="sections"></a>Seções

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Funcionalidade e partes de RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Este tópico explica como o `Adapter`, `LayoutManager`, e `ViewHolder` trabalham em conjunto como classes auxiliares para dar suporte a `RecyclerView`.
Ele fornece uma visão geral de cada uma dessas classes auxiliares e explica como usá-los em seu aplicativo.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Este tópico se baseia nas informações fornecidas na [RecyclerView partes e funcionalidade](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) fornecendo exemplos de código real de como os vários `RecyclerView` elementos são implementados para compilar um aplicativo de navegação de foto do mundo real.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Estendendo o exemplo RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Este tópico adiciona código adicional para o aplicativo de exemplo apresentado em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para demonstrar como tratar eventos de clique de item e atualizar `RecyclerView` quando as alterações da fonte de dados subjacentes.


### <a name="summary"></a>Resumo

Este guia introduziu o Android `RecyclerView` widget; ele explicado como adicionar o `RecyclerView` suporte a biblioteca de projetos de xamarin, como `RecyclerView` recicla modos de exibição, como ele aplica o padrão de modo reservado para eficiência e os vários classes auxiliares que compõem `RecyclerView` colaborar para exibir coleções. Ele fornecido o código de exemplo para demonstrar como `RecyclerView` é integrado em um aplicativo, ele explicado como adaptar o `RecyclerView`da diretiva de layout conectando gerenciadores de layout diferente e ele descrito como lidar com o item de eventos de clique e notificar `RecyclerView`das alterações de fonte de dados.

Para obter mais informações sobre `RecyclerView`, consulte o [referência de classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introdução ao pirulito](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
