---
title: RecyclerView
description: RecyclerView é um grupo de modo de exibição para exibir coleções; ele é projetado para ser uma substituição mais flexível para grupos de modo de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar RecyclerView em aplicativos xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: 1332a7ef5b8e5bb2f1178582bcf058123f1e177c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110141"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView é um grupo de modo de exibição para exibir coleções; ele é projetado para ser uma substituição mais flexível para grupos de modo de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar RecyclerView em aplicativos xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

Muitos aplicativos precisam exibir coleções do mesmo tipo (como mensagens, contatos, imagens ou músicas); Geralmente, essa coleção é muito grande para caber na tela, portanto, a coleção é apresentada em uma pequena janela que pode rolar suavemente por todos os itens na coleção.
`RecyclerView` é um Android widget que exibe uma coleção de itens em uma lista ou uma grade, permitindo que o usuário role por meio da coleção. A seguir está uma captura de tela de um aplicativo de exemplo que usa `RecyclerView` para exibir o conteúdo da caixa de entrada de email em uma lista de rolagem vertical:

[![Aplicativo de exemplo usando RecyclerView para mensagens de entrada de lista](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` oferece dois recursos atraentes:

-  Ele tem uma arquitetura flexível que permite modificar seu comportamento conectando-se em seus componentes preferenciais.

-  É eficiente com grandes coleções porque reutiliza os modos de exibição do item e requer o uso de *exibir detentores* para referências de modo de exibição do cache.

Este guia explica como usar `RecyclerView` em aplicativos xamarin. Android; ele explica como adicionar a `RecyclerView` pacote ao seu projeto xamarin. Android e ele descreve como `RecyclerView` funções em um aplicativo típico. Exemplos de códigos reais são fornecidos para mostrar como integrar `RecyclerView` em seu aplicativo, como implementar a exibição de item, clique em e como atualizar `RecyclerView` quando seus dados subjacentes forem alterados. Este guia pressupõe que você esteja familiarizado com o desenvolvimento do xamarin. Android.


### <a name="requirements"></a>Requisitos

Embora `RecyclerView` é geralmente associado ao Android 5.0 Lollipop, é oferecido como uma biblioteca de suporte &ndash; `RecyclerView` funciona com aplicativos que nível de API de destino (Android 2.1) de 7 e posterior. A seguir é necessária para usar `RecyclerView` em aplicativos baseados em Xamarin:

-  **Xamarin. Android** &ndash; xamarin. Android 4.20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-  Seu projeto de aplicativo deve incluir a **Xamarin.Android.Support.v7.RecyclerView** pacote. Para obter mais informações sobre como instalar pacotes do NuGet, consulte [instruções passo a passo: incluindo o NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Visão geral

`RecyclerView` pode ser pensada como uma substituição para o `ListView` e `GridView` widgets no Android. Como seu antecessores `RecyclerView` é projetado para exibir um grande conjunto de dados em uma janela pequena, mas `RecyclerView` oferece mais opções de layout e é otimizada para exibir coleções grandes. Se você estiver familiarizado com o `ListView`, há várias diferenças importantes entre `ListView` e `RecyclerView`:

-   `RecyclerView` é um pouco mais complexa para usar: você precisa escrever mais código para usar `RecyclerView` em comparação com `ListView`.

-   `RecyclerView` não fornece um adaptador predefinido; Você deve implementar o código do adaptador que acessa sua fonte de dados. No entanto, o Android inclui vários adaptadores predefinidos que funcionam com `ListView` e `GridView`.

-   `RecyclerView` não oferece um evento de clique do item quando um usuário toca um item; em vez disso, os eventos de clique de item são manipulados pelo classes auxiliares. Por outro lado, `ListView` oferece um evento de clique no item.

-   `RecyclerView` melhora o desempenho por modos de exibição de reciclagem e aplicando o padrão de titular de modo de exibição, o que elimina as pesquisas de recursos de layout desnecessário. Uso do titular do modo de exibição padrão é opcional em `ListView`.

-   `RecyclerView` se baseia em um design modular que torna mais fácil de personalizar. Por exemplo, você pode conectar uma diretiva de layout diferente sem alterações significativas do código ao seu aplicativo.
    Por outro lado, `ListView` é relativamente monolítico na estrutura.

-   `RecyclerView` inclui animações internas para o item Adicionar e remover. `ListView` animações exigirá algum esforço adicional por parte do desenvolvedor do aplicativo.


### <a name="sections"></a>Seções

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[RecyclerView partes e funcionalidade](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Este tópico explica como o `Adapter`, `LayoutManager`, e `ViewHolder` funcionam juntos como classes auxiliares para dar suporte a `RecyclerView`.
Ele fornece uma visão geral de cada uma dessas classes auxiliares e explica como usá-los em seu aplicativo.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Este tópico se baseia nas informações fornecidas em [RecyclerView partes e funcionalidade](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) , fornecendo exemplos de código real de como os vários `RecyclerView` elementos são implementados para criar um aplicativo de navegação de foto do mundo real.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Este tópico adiciona código adicional para o aplicativo de exemplo apresentado [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para demonstrar como manipular eventos de clique de item e atualizar `RecyclerView` quando as alterações da fonte de dados subjacentes.


### <a name="summary"></a>Resumo

Este guia apresentou a Android `RecyclerView` widget; ele explicou como adicionar a `RecyclerView` dar suporte à biblioteca para projetos do xamarin. Android, como `RecyclerView` recicla modos de exibição, como ele aplica o padrão de titular de modo de exibição para a eficiência e como os vários classes auxiliares que compõem `RecyclerView` colaboram para exibir coleções. Ele fornecido o código de exemplo para demonstrar como `RecyclerView` é integrado em um aplicativo, ele explicou como personalizar `RecyclerView`da diretiva de layout, dispondo os gerenciadores de layout diferente e ele descreveu como lidar com o item eventos de clique e notificar `RecyclerView`das alterações de fonte de dados.

Para obter mais informações sobre `RecyclerView`, consulte o [referência de classe de RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introdução ao Lollipop](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
