---
title: RecyclerView
description: RecyclerView é um grupo de exibição para exibir coleções; Ele foi projetado para ser uma substituição mais flexível para grupos de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar o RecyclerView em aplicativos Xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: bce89be8bec512ac70ca40015521c7d56f3460d3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028808"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView é um grupo de exibição para exibir coleções; Ele foi projetado para ser uma substituição mais flexível para grupos de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar o RecyclerView em aplicativos Xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

Muitos aplicativos precisam exibir coleções do mesmo tipo (como mensagens, contatos, imagens ou músicas); Geralmente, essa coleção é muito grande para caber na tela, portanto, a coleção é apresentada em uma pequena janela que pode rolar suavemente por todos os itens na coleção.
`RecyclerView` é um widget do Android que exibe uma coleção de itens em uma lista ou em uma grade, permitindo que o usuário percorra a coleção. Veja a seguir uma captura de tela de um aplicativo de exemplo que usa `RecyclerView` para exibir o conteúdo da caixa de entrada de email em uma lista de rolagem vertical:

[![aplicativo de exemplo usando RecyclerView para listar mensagens da caixa de entrada](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

o `RecyclerView` oferece dois recursos atraentes:

- Ele tem uma arquitetura flexível que permite que você modifique seu comportamento conectando seus componentes preferenciais.

- Ele é eficiente com grandes coleções porque reutiliza exibições de item e requer o uso de *contentores de exibição* para armazenar em cache referências de exibição.

Este guia explica como usar `RecyclerView` em aplicativos Xamarin. Android; Ele explica como adicionar o pacote de `RecyclerView` ao seu projeto Xamarin. Android e descreve como o `RecyclerView` funciona em um aplicativo típico. Exemplos de código reais são fornecidos para mostrar como integrar `RecyclerView` em seu aplicativo, como implementar o clique de item-View e como atualizar `RecyclerView` quando seus dados subjacentes forem alterados. Este guia pressupõe que você esteja familiarizado com o desenvolvimento do Xamarin. Android.

### <a name="requirements"></a>Requisitos

Embora o `RecyclerView` geralmente esteja associado à pirulito 5,0 do Android, ele é oferecido como uma biblioteca de suporte &ndash; `RecyclerView` funciona com aplicativos direcionados ao nível 7 (Android 2,1) e posteriores à API. O seguinte é necessário para usar `RecyclerView` em aplicativos baseados no Xamarin:

- O **xamarin. android** &ndash; Xamarin. Android 4,20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

- Seu projeto de aplicativo deve incluir o pacote **Xamarin. Android. support. v7. RecyclerView** . Para obter mais informações sobre a instalação de pacotes do NuGet, consulte [Walkthrough: incluindo um NuGet em seu projeto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

### <a name="overview"></a>Visão Geral

`RecyclerView` pode ser pensado como uma substituição para os widgets de `ListView` e `GridView` no Android. Como seus predecessores, `RecyclerView` é projetado para exibir um grande conjunto de dados em uma pequena janela, mas `RecyclerView` oferece mais opções de layout e é melhor otimizado para exibir grandes coleções. Se você estiver familiarizado com `ListView`, há várias diferenças importantes entre `ListView` e `RecyclerView`:

- `RecyclerView` é um pouco mais complexo de usar: você precisa escrever mais código para usar `RecyclerView` em comparação com `ListView`.

- `RecyclerView` não fornece um adaptador predefinido; Você deve implementar o código do adaptador que acessa sua fonte de dados. No entanto, o Android inclui vários adaptadores predefinidos que funcionam com `ListView` e `GridView`.

- `RecyclerView` não oferece um evento de clique de item quando um usuário toca em um item; em vez disso, eventos de clique de item são manipulados por classes auxiliares. Por outro lado, `ListView` oferece um evento de clique de item.

- `RecyclerView` aprimora o desempenho por meio da reciclagem de exibições e pela imposição do padrão de suporte de exibição, o que elimina pesquisas desnecessárias de recursos de layout. O uso do padrão de detentor de exibição é opcional em `ListView`.

- o `RecyclerView` é baseado em um design modular que facilita a personalização. Por exemplo, você pode conectar uma política de layout diferente sem alterações significativas de código em seu aplicativo.
    Por outro lado, `ListView` é relativamente monolítico na estrutura.

- `RecyclerView` inclui animações internas para adicionar e remover itens. `ListView` animações exigem algum esforço adicional por parte do desenvolvedor do aplicativo.

### <a name="sections"></a>Seções

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Partes e funcionalidades do RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Este tópico explica como as `Adapter`, `LayoutManager`e `ViewHolder` trabalham em conjunto como classes auxiliares para dar suporte ao `RecyclerView`.
Ele fornece uma visão geral de alto nível de cada uma dessas classes auxiliares e explica como usá-las em seu aplicativo.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Este tópico baseia-se nas informações fornecidas em [partes e funcionalidades do RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) fornecendo exemplos de código reais de como os vários elementos de `RecyclerView` são implementados para criar um aplicativo de navegação de fotos do mundo real.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Este tópico adiciona código adicional ao aplicativo de exemplo apresentado em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para demonstrar como tratar eventos de clique de item e atualizar `RecyclerView` quando a fonte de dados subjacente é alterada.

### <a name="summary"></a>Resumo

Este guia introduziu o widget de `RecyclerView` do Android; Ele explicou como adicionar a biblioteca de suporte do `RecyclerView` a projetos Xamarin. Android, como o `RecyclerView` recicla exibições, como ela impõe o padrão de suporte de exibição para eficiência e como as várias classes auxiliares que compõem `RecyclerView` colaboram para exibir coleções. Ele forneceu um código de exemplo para demonstrar como o `RecyclerView` é integrado a um aplicativo, explicado como personalizar a política de layout do `RecyclerView`conectando-se a diferentes gerentes de layout e descreveu como lidar com eventos de clique de item e notificar `RecyclerView` de dados alterações de origem.

Para obter mais informações sobre `RecyclerView`, consulte a [referência de classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).

## <a name="related-links"></a>Links relacionados

- [RecyclerViewer (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introdução à pirulito](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
