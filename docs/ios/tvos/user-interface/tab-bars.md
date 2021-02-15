---
title: Trabalhando com controladores de barra de guias tvOS no Xamarin
description: Este documento descreve como trabalhar com controladores de barra de guias em um aplicativo tvOS criado com o Xamarin. Ele fornece um alto nível de exibição de barras de guias e discute itens da barra de guias, integração do storyboard e itens da barra de guias.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 813a4de1966aa53e9c1447804deda8f55e5672f3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430284"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Trabalhando com controladores de barra de guias tvOS no Xamarin

Para muitos tipos de aplicativos tvOS, a navegação principal é apresentada como uma barra de guias em execução na parte superior da tela. O usuário passa o dedo para a esquerda e para a direita na lista de categorias possíveis e a área de conteúdo abaixo das alterações para refletir a seleção do usuário.

[![Barra de guias de exemplo](tab-bars-images/tab01.png)](tab-bars-images/tab01.png#lightbox)

A barra de guias é translúcida por padrão e sempre aparece na parte superior da tela. Quando estiver em foco, uma barra de guias cobrirá os primeiros 140 pixels da tela, mas será rapidamente deslizar quando o foco mudar para a área de conteúdo abaixo.

<a name="Tab-Bars-in-tvOS"></a>

## <a name="tab-bars-in-tvos"></a>Barras de guias no tvOS

O `UITabViewController` funciona de maneira semelhante e serve a uma finalidade semelhante no tvOS como faz no Ios, com as seguintes diferenças principais:

- Ao contrário da barra de guias no iOS que aparece na parte inferior da tela, as barras de guias no tvOS ocupam os primeiros 140 pixels da tela e são translúcidas por padrão.
- Quando o foco deixa a barra de guias para a área de conteúdo abaixo, a barra de guias deslizará rapidamente para a parte superior da tela e ficará oculta. O usuário pode tocar no botão de menu uma vez ou passar o dedo para cima no [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para mostrar a barra de guias novamente.
- Passar o dedo para baixo no Siri remoto moverá o foco para a área de conteúdo abaixo da barra de guias para o primeiro [Item focado](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) no conteúdo que está sendo mostrado. Novamente, isso ocultará a barra de guias quando o foco for deslocado.
- Clicar para selecionar uma categoria exibida na barra de guias alternará para o conteúdo e o foco dessa categoria será alternado para o primeiro item focado nessa exibição.
- O número de categorias exibidas na barra de guias deve ser fixo e todas as categorias devem estar acessíveis o tempo todo, uma determinada categoria nunca deve ser desabilitada.
- As barras de guias não dão suporte à personalização em tvOS. Além disso, eles não mostrarão a categoria **mais** (como Ios) se houver mais categorias do que podem caber na barra de guias.

A Apple tem as seguintes sugestões para trabalhar com barras de tabulação:

- **Use as barras de guias para organizar o conteúdo logicamente** -use a barra de guias para organizar logicamente o conteúdo com o qual seu aplicativo tvOS funciona. Por exemplo, em destaque, gráficos superiores, comprados e pesquisados.
- **Adicionar notificações para informar os usuários do novo conteúdo** -você pode, opcionalmente, exibir uma notificação (uma elipse vermelha com um número branco ou ponto de exclamação) para informar o usuário sobre o novo conteúdo em uma categoria.
- **Use as notificações com moderação** -não obstruir a barra de guias com notificações e exibi-las somente onde elas fornecem informações críticas ao usuário.
- **Limitar o número de categorias** – para reduzir a complexidade e manter seu aplicativo gerenciável, não sobrecarregar sua barra de guias com categorias e garantir que todas as categorias fiquem visíveis e não lotadas. Os títulos simples e curtos funcionam melhor.
- **Não desabilitar uma categoria** – todas as guias (categorias) sempre devem estar visíveis e habilitadas em todos os momentos. Se uma determinada guia não tiver conteúdo, forneça uma explicação para o usuário. Por exemplo, a guia compras estará vazia se o usuário não tiver feito nenhuma compra.

<a name="Tab-Bar-Items"></a>

## <a name="tab-bar-items"></a>Itens da barra de guias

Cada categoria (guia) na barra de guias é representada por um item da barra de guias ( `UITabBarItem` ). A Apple tem as seguintes sugestões para trabalhar com itens de barra de guias:

- **Usar guias baseadas em texto** -embora o item da barra de guias possa ser representado como um ícone, a Apple sugere usar texto somente porque um título conciso é mais fácil de interpretar do que um ícone.
- **Usar substantivos ou verbos pequenos e significativos** -um item da barra de guias deve retransmitir claramente o conteúdo que ele contém e funciona melhor quando é um substantivo simples (como fotos, filmes ou música) ou verbos (como pesquisa ou reprodução).

<a name="Tab-Bars-and-Storyboards"></a>

## <a name="tab-bars-and-storyboards"></a>Barras de tabulação e storyboards

A maneira mais fácil de trabalhar com barras de guias em um aplicativo Xamarin. tvOS é adicioná-las à interface do usuário do aplicativo usando o designer do iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie um novo aplicativo Xamarin. tvOS e selecione **tvOS**  >  **App**  >  **aplicativo com guias**do aplicativo tvOS: 

    [![Selecionar aplicativo com guias](tab-bars-images/tab02.png)](tab-bars-images/tab02.png#lightbox)
1. Siga todos os prompts para criar uma nova solução Xamarin. tvOS.
1. No **painel de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Para alterar o **ícone** ou o **título** de uma determinada categoria, selecione o **item de barra de guias** para o controlador de **exibição** no **contorno do documento**:

    [![O item da barra de guias do controlador de exibição na estrutura de tópicos do documento](tab-bars-images/tab03a.png)](tab-bars-images/tab03a.png#lightbox)
1. Em seguida, defina as propriedades necessárias na **Guia Widget** do **Gerenciador de propriedades**: 

    [![A guia do widget](tab-bars-images/tab03.png)](tab-bars-images/tab03.png#lightbox)
1. Para adicionar uma nova categoria (guia), descarte um **controlador de exibição** na superfície de design: 

    [![Um controlador de exibição](tab-bars-images/tab04.png)](tab-bars-images/tab04.png#lightbox)
1. Clique no botão de controle e arraste-o do **controlador de exibição de guia** para o novo controlador de **exibição**.
1. No pop-up, selecione **Exibir controladores** para adicionar a nova exibição como uma guia (categoria): 

    [![Selecionar guia](tab-bars-images/tab05.png)](tab-bars-images/tab05.png#lightbox)
1. Projete o layout da interface do usuário para cada área de conteúdo do Caterogies como normal, adicionando elementos da interface do usuário no designer do iOS.
1. Expor todos os eventos necessários para trabalhar com os controles de interface do usuário no código C#.
1. Nomeie os controles de interface do usuário que você deseja expor no código C#.
1. Salve as alterações.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie um novo aplicativo Xamarin. tvOS e selecione **tvOS**  >  **App**  >  **aplicativo com guias**do aplicativo tvOS: 

    [![Selecionar aplicativo com guias](tab-bars-images/tab02vs.png)](tab-bars-images/tab02vs.png#lightbox)
1. Siga todos os prompts para criar uma nova solução Xamarin. tvOS.
1. No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Para alterar o **ícone** ou o **título** de uma determinada categoria, selecione o **item de barra de guias** para o controlador de **exibição** no **contorno do documento**:

    [![O controlador de exibição na estrutura de tópicos do documento](tab-bars-images/tab03avs.png)](tab-bars-images/tab03avs.png#lightbox)
1. Em seguida, defina as propriedades necessárias na **Guia Widget** do **Gerenciador de propriedades**: 

    [![A guia do widget](tab-bars-images/tab03vs.png)](tab-bars-images/tab03vs.png#lightbox)
1. Para adicionar uma nova categoria (guia), arraste um **controlador de exibição** da **caixa de ferramentas** e solte-o na superfície de design: 

    [![Um controlador de exibição](tab-bars-images/tab04vs.png)](tab-bars-images/tab04vs.png#lightbox)
1. Clique no botão de controle e arraste-o do **controlador de exibição de guia** para o novo controlador de **exibição**.
1. No pop-up, selecione **Exibir controladores** para adicionar a nova exibição como uma guia (categoria): 

    [![Selecionar guia](tab-bars-images/tab05vs.png)](tab-bars-images/tab05vs.png#lightbox)
1. Projete o layout da interface do usuário para cada área de conteúdo do Caterogies como normal, adicionando elementos da interface do usuário no designer do iOS.
1. Expor todos os eventos necessários para trabalhar com os controles de interface do usuário no código C#.
1. Nomeie os controles de interface do usuário que você deseja expor no código C#.
1. Salve as alterações.

-----

> [!IMPORTANT]
> Embora seja possível atribuir eventos como, por exemplo `TouchUpInside` , um elemento de interface do usuário (como um `UIButton` ) no designer do IOS, ele nunca será chamado porque o Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar o `Primary Action` evento ao criar manipuladores de eventos para elementos da interface do usuário do tvOS.

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars"></a>

## <a name="working-with-tab-bars"></a>Trabalhando com barras de tabulação

Use a `Items` Propriedade do `UITabBar` para acessar a coleção `UITabBarItems` que ela contém como uma matriz indexada zero (0). A `SelectedItem` propriedade retornará a guia selecionada no momento (categoria) como um `UITabBarItem` .

<a name="Working-with-Tab-Bar-Items"></a>

## <a name="working-with-tab-bar-items"></a>Trabalhando com itens da barra de guias

Para exibir uma notificação em uma determinada guia (uma elipse vermelha com texto em branco), use o seguinte código:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

O que produziria os seguintes resultados quando executado:

[![Um item da barra de guias com uma notificação](tab-bars-images/tab06.png)](tab-bars-images/tab06.png#lightbox)

Use a `Title` Propriedade do `UITabBarItem` para alterar o título e a `Image` propriedade para alterar o ícone.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com o controlador da barra de guias dentro de um aplicativo Xamarin. tvOS.

## <a name="related-links"></a>Links Relacionados

- [Exemplos do tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)