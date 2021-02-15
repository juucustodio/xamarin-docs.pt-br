---
title: RecyclerView
description: RecyclerView é um grupo de exibição para exibir coleções; Ele foi projetado para ser uma substituição mais flexível para grupos de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar o RecyclerView em aplicativos Xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: a972f6686103dc9b3a1317d15985cbf3dc78bd82
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457050"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView é um grupo de exibição para exibir coleções; Ele foi projetado para ser uma substituição mais flexível para grupos de exibição mais antigos, como ListView e GridView.  Este guia explica como usar e personalizar o RecyclerView em aplicativos Xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

Muitos aplicativos precisam exibir coleções do mesmo tipo (como mensagens, contatos, imagens ou músicas); Geralmente, essa coleção é muito grande para caber na tela, portanto, a coleção é apresentada em uma pequena janela que pode rolar suavemente por todos os itens na coleção.
`RecyclerView` é um widget do Android que exibe uma coleção de itens em uma lista ou em uma grade, permitindo que o usuário percorra a coleção. Veja a seguir uma captura de tela de um exemplo de aplicativo que usa `RecyclerView` o para exibir o conteúdo da caixa de entrada de email em uma lista de rolagem vertical:

[![Aplicativo de exemplo usando RecyclerView para listar mensagens da caixa de entrada](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` oferece dois recursos atraentes:

- Ele tem uma arquitetura flexível que permite que você modifique seu comportamento conectando seus componentes preferenciais.

- Ele é eficiente com grandes coleções porque reutiliza exibições de item e requer o uso de *contentores de exibição* para armazenar em cache referências de exibição.

Este guia explica como usar `RecyclerView` o em aplicativos xamarin. Android; ele explica como adicionar o `RecyclerView` pacote ao seu projeto Xamarin. Android e descreve como o `RecyclerView` funciona em um aplicativo típico. Exemplos de código reais são fornecidos para mostrar como integrar `RecyclerView` em seu aplicativo, como implementar o clique de item-View e como atualizar `RecyclerView` quando seus dados subjacentes são alterados. Este guia pressupõe que você esteja familiarizado com o desenvolvimento do Xamarin. Android.

### <a name="requirements"></a>Requisitos

Embora `RecyclerView` o esteja sempre associado à pirulito 5,0 do Android, ele é oferecido como uma biblioteca de suporte &ndash; `RecyclerView` funciona com aplicativos direcionados à API do nível 7 (Android 2,1) e posterior. O seguinte é necessário para usar `RecyclerView` em aplicativos baseados no Xamarin:

- **Xamarin. Android** &ndash; xamarin. Android 4,20 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

- Seu projeto de aplicativo deve incluir o pacote **Xamarin. Android. support. v7. RecyclerView** . Para obter mais informações sobre a instalação de pacotes do NuGet, consulte [Walkthrough: incluindo um NuGet em seu projeto](/visualstudio/mac/nuget-walkthrough).

### <a name="overview"></a>Visão geral

`RecyclerView` pode ser pensado como uma substituição para os `ListView` `GridView` widgets e no Android. Como seus predecessores, o `RecyclerView` é projetado para exibir um grande conjunto de dados em uma pequena janela, mas `RecyclerView` oferece mais opções de layout e é melhor otimizado para exibir grandes coleções. Se você estiver familiarizado com o `ListView` , há várias diferenças importantes entre o `ListView` e o `RecyclerView` :

- `RecyclerView` é um pouco mais complexo de usar: você precisa escrever mais código para usar `RecyclerView` em comparação com `ListView` .

- `RecyclerView` não fornece um adaptador predefinido; Você deve implementar o código do adaptador que acessa sua fonte de dados. No entanto, o Android inclui vários adaptadores predefinidos que funcionam com o `ListView` e o `GridView` .

- `RecyclerView` não oferece um evento de clique de item quando um usuário toca em um item; em vez disso, eventos de clique de item são manipulados por classes auxiliares. Por outro lado, `ListView` o oferece um evento de clique de item.

- `RecyclerView` melhora o desempenho por meio da reciclagem de exibições e pela imposição do padrão de suporte de exibição, o que elimina pesquisas desnecessárias de recursos de layout. O uso do padrão de detentor de exibição é opcional no `ListView` .

- `RecyclerView` o é baseado em um design modular que facilita a personalização. Por exemplo, você pode conectar uma política de layout diferente sem alterações significativas de código em seu aplicativo.
    Por outro lado, `ListView` é relativamente monolítico na estrutura.

- `RecyclerView` inclui animações internas para adicionar e remover itens. `ListView` as animações exigem um esforço adicional na parte do desenvolvedor do aplicativo.

### <a name="sections"></a>Seções

#### <a name="recyclerview-parts-and-functionality"></a>[Partes e funcionalidades do RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Este tópico explica como o `Adapter` , o `LayoutManager` e o `ViewHolder` trabalham juntos como classes auxiliares para dar suporte ao `RecyclerView` .
Ele fornece uma visão geral de alto nível de cada uma dessas classes auxiliares e explica como usá-las em seu aplicativo.

#### <a name="a-basic-recyclerview-example"></a>[Um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Este tópico baseia-se nas informações fornecidas em [partes e funcionalidades do RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) fornecendo exemplos de código reais de como os vários `RecyclerView` elementos são implementados para criar um aplicativo de navegação de fotos do mundo real.

#### <a name="extending-the-recyclerview-example"></a>[Estendendo o exemplo de RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Este tópico adiciona código adicional ao aplicativo de exemplo apresentado em [um exemplo de RecyclerView básico](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) para demonstrar como tratar eventos de clique de item e atualizar `RecyclerView` quando a fonte de dados subjacente é alterada.

### <a name="summary"></a>Resumo

Este guia introduziu o `RecyclerView` widget do Android; ele explicou como adicionar a `RecyclerView` biblioteca de suporte a projetos Xamarin. Android, como `RecyclerView` o recicla exibições, como ela impõe o padrão de detentor de exibição para eficiência e como as várias classes auxiliares que compõem `RecyclerView` colaboram para exibir coleções. Ele forneceu um código de exemplo para demonstrar como `RecyclerView` é integrado a um aplicativo, explicado como ajustar a `RecyclerView` política de layout ao conectar-se a diferentes gerentes de layout e descreveu como lidar com eventos de clique de item e notificar `RecyclerView` as alterações da fonte de dados.

Para obter mais informações sobre `RecyclerView` o, consulte a [referência de classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).

## <a name="related-links"></a>Links Relacionados

- [RecyclerViewer (exemplo)](/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introdução à pirulito](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)