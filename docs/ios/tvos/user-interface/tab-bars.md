---
title: Trabalhando com o controlador de barra de guias
description: Este artigo aborda criando e trabalhando com o controlador de barra de guia dentro de um aplicativo Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: fc9765b3f6a77f47fdce32dbc3805dd7bd70a08a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-tab-bar-controller"></a>Trabalhando com o controlador de barra de guias

_Este artigo aborda criando e trabalhando com o controlador de barra de guia dentro de um aplicativo Xamarin.tvOS._

Para muitos tipos de aplicativos tvOS, navegação principal é apresentada como uma barra de guias em execução na parte superior da tela. O usuário obtém esquerda e direita entre a lista de categorias possíveis e a área de conteúdo abaixo as alterações para refletir a seleção do usuário.

[![](tab-bars-images/tab01.png "Barra de guias de exemplo")](tab-bars-images/tab01.png#lightbox)

Barra de guias é transparente por padrão e sempre aparece na parte superior da tela. Em foco, uma barra de guia aborda os principais 140 pixels da tela, mas rapidamente slide imediatamente quando o foco muda para a área de conteúdo abaixo.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barras de guia no tvOS

O `UITabViewController` funciona de maneira semelhante e serve a uma finalidade semelhante em tvOS como faz no iOS, com as seguintes diferenças importantes:

- Ao contrário da barra de guias que aparece na parte inferior da tela, barras de guia no tvOS ocupam superiores 140 pixels da tela e são transparente por padrão.
- Quando o foco deixa a barra de guias para a área de conteúdo abaixo, a barra de guias serão rapidamente slide da parte superior da tela e oculta. O usuário pode tocar no botão de Menu de uma vez ou passa o dedo para cima no [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para mostrar a barra de guias novamente.
- Passar o dedo para baixo no controle remoto Siri Mover foco para a área de conteúdo abaixo da barra de guia para a primeira [Item Focusable é](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) no conteúdo que está sendo mostrado. Novamente, isso ocultará a barra de guias quando o foco muda.
- Clique para selecionar uma categoria exibida na barra de guias alternará ao conteúdo e o foco da categoria serão transferidas para o primeiro Item de controle no modo de exibição.
- O número de categorias exibido na barra de guia deve ser corrigido e todas as categorias devem ser acessíveis em todos os momentos, uma determinada categoria nunca deve ser desabilitada.
- Guia barras não dão suporte a personalização em tvOS. Além disso, eles não mostrarem o **mais** categoria (como iOS) se houver mais de categorias que pode ser ajustadas na barra de guias.

Apple tem as seguintes sugestões para trabalhar com barras de guia:

- **Use as barras de guia para organizar conteúdo logicamente** -Use a barra de guia para organizar o conteúdo que seu aplicativo tvOS funciona com de forma lógica. Por exemplo, em destaque, gráficos de parte superior, comprado e pesquisa.
- **Adicionar notificações para informar os usuários do novo conteúdo** -opcionalmente, você pode exibir uma notificação (uma elipse com um número branco ou um ponto de exclamação vermelha) para informar o usuário de novo conteúdo em uma categoria.
- **Use credenciais com moderação** - não sobrecarregar a barra de guia com notificações e apenas exibi-los em que eles fornecem informações essenciais para o usuário.
- **Limitar o número de categorias** – para reduzir a complexidade e mantê-lo aplicativo gerenciável, não sobrecarregar a barra de guias com categorias e certifique-se de que todas as categorias são visíveis e não está cheio. Títulos curtos e simples funcionam melhor.
- **Não desabilite uma categoria** -todas as guias (categorias) deve ser sempre visível e habilitado em todos os momentos. Se uma guia não tem nenhum conteúdo, fornece uma explicação do usuário por que. Por exemplo, a guia de compras será vazia se o usuário não tiver feito nenhum compras.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Itens da barra de guias

Cada categoria (guia) na barra de guias é representada por um Item de barra de guia (`UITabBarItem`). Apple tem as seguintes sugestões para trabalhar com itens da barra de guia:

- **Use as guias com base em texto** -enquanto a guia Item da barra de é capaz de ser representado como um ícone, Apple sugere o uso de texto só porque um título conciso é mais fácil de interpretar que um ícone.
- **Use nomes significativos de curto ou verbos** -um Item da barra de guia deve retransmissão claramente o conteúdo que ele contém e funciona melhor quando é um substantivo simple (como fotos, filmes ou músicas) ou verbos (como pesquisa ou Play).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barras de guia e Storyboards

É a maneira mais fácil trabalhar com a guia barras em um aplicativo de Xamarin.tvOS para adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. Iniciar um novo aplicativo Xamarin.tvOS e selecione **tvOS** > **aplicativo** > **aplicativo com guias**: 

    [![](tab-bars-images/tab02.png "Selecione o aplicativo com guias")](tab-bars-images/tab02.png#lightbox)
1. Siga todas as solicitações para criar uma nova solução Xamarin.tvOS.
1. No **solução preenchimento**, clique duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Para alterar o **ícone** ou **título** para uma determinada categoria, selecione o **Item da barra de guia** para o **View Controller** no  **Estrutura de tópicos de documento**:

    [![](tab-bars-images/tab03a.png "Item de barra de guias para o controlador de exibição na estrutura de tópicos do documento")](tab-bars-images/tab03a.png#lightbox)
1. Em seguida, defina as propriedades necessárias **Widget guia** do **propriedades Explorer**: 

    [![](tab-bars-images/tab03.png "A guia de Widget")](tab-bars-images/tab03.png#lightbox)
1. Para adicionar uma nova categoria (guia), descartar um **View Controller** para a superfície de design: 

    [![](tab-bars-images/tab04.png "Um controlador de exibição")](tab-bars-images/tab04.png#lightbox)
1. CTRL + clique e arraste do **guia View Controller** para o novo **View Controller**.
1. Na janela pop-up, selecione **exibir controladores** para adicionar o novo modo de exibição como uma guia (categoria): 

    [![](tab-bars-images/tab05.png "Selecione a guia")](tab-bars-images/tab05.png#lightbox)
1. Crie o layout da interface do usuário para cada área de conteúdo Caterogies como normal, pela adição de elementos de interface do usuário no Designer de iOS.
1. Expor quaisquer eventos necessários para trabalhar com os controles de interface do usuário em código c#.
1. Nome os controles de interface do usuário que você deseja expor no código c#.
1. Salve as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. Iniciar um novo aplicativo Xamarin.tvOS e selecione **tvOS** > **aplicativo** > **aplicativo com guias**: 

    [![](tab-bars-images/tab02vs.png "Selecione o aplicativo com guias")](tab-bars-images/tab02vs.png#lightbox)
1. Siga todas as solicitações para criar uma nova solução Xamarin.tvOS.
1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Para alterar o **ícone** ou **título** para uma determinada categoria, selecione o **Item da barra de guia** para o **View Controller** no  **Estrutura de tópicos de documento**:

    [![](tab-bars-images/tab03avs.png "O controlador de exibição na estrutura de tópicos do documento")](tab-bars-images/tab03avs.png#lightbox)
1. Em seguida, defina as propriedades necessárias **Widget guia** do **propriedades Explorer**: 

    [![](tab-bars-images/tab03vs.png "A guia de Widget")](tab-bars-images/tab03vs.png#lightbox)
1. Para adicionar uma nova categoria (guia), arraste um **View Controller** do **caixa de ferramentas** e solte-o para a superfície de design: 

    [![](tab-bars-images/tab04vs.png "Um controlador de exibição")](tab-bars-images/tab04vs.png#lightbox)
1. CTRL + clique e arraste do **guia View Controller** para o novo **View Controller**.
1. Na janela pop-up, selecione **exibir controladores** para adicionar o novo modo de exibição como uma guia (categoria): 

    [![](tab-bars-images/tab05vs.png "Selecione a guia")](tab-bars-images/tab05vs.png#lightbox)
1. Crie o layout da interface do usuário para cada área de conteúdo Caterogies como normal, com a adição de elementos de interface do usuário no Designer do iOS.
1. Expor quaisquer eventos necessários para trabalhar com os controles de interface do usuário em código c#.
1. Nome os controles de interface do usuário que você deseja expor no código c#.
1. Salve as alterações.
    
-----

> [!IMPORTANT]
> **Observação:** enquanto é possível atribuir eventos como `TouchUpInside` para um elemento de interface do usuário (como um `UIButton`) no Designer de iOS, ele nunca será chamado porque Apple TV não tem um toque de tela ou oferecer suporte a eventos de toque. Você sempre deve usar o `Primary Action ` eventos durante a criação de manipuladores de eventos para tvOS elementos da interface do usuário.

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Trabalhando com barras de guia

Use o `Items` propriedade o `UITabBar` para acessar a coleção de `UITabBarItems` contém como uma matriz indexada zero (0). O `SelectedItem` propriedade retornará a guia selecionada no momento (categoria) como um `UITabBarItem`.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Trabalhando com itens da barra de guias

Para exibir uma notificação em uma guia (uma elipse vermelha com texto em branco), use o seguinte código:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Que pode produzir os seguintes resultados quando executar:

[![](tab-bars-images/tab06.png "Um Item de barra de guia com notificação")](tab-bars-images/tab06.png#lightbox)

Use o `Title` propriedade o `UITabBarItem` para alterar o título e o `Image` propriedade para alterar o ícone.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com o controlador de barra de guia dentro de um aplicativo Xamarin.tvOS.




## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
