---
title: Trabalhar com controladores de barra de guia no Xamarin do tvOS
description: Este documento descreve como trabalhar com controladores de barra de guia em um aplicativo tvOS criados com o Xamarin. Ele fornece um alto nível sobre a exibição das barras de guia e discute os itens da barra de guia, integração de storyboard e itens da barra de guia.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: a0efc30fd9814e4da858c4e3e4e99990eccf102e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119810"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Trabalhar com controladores de barra de guia no Xamarin do tvOS

Para muitos tipos de aplicativos do tvOS, navegação principal é apresentada como uma barra de guias, em execução na parte superior da tela. O usuário passa o dedo left e right em toda a lista de categorias possíveis e a área de conteúdo abaixo as alterações para refletir a seleção do usuário.

[![](tab-bars-images/tab01.png "Exemplo de barra de guia")](tab-bars-images/tab01.png#lightbox)

A barra de guia é translúcida por padrão e sempre aparece na parte superior da tela. Quando estiver em foco, uma barra de guias abordará os principais 140 pixels da tela, mas será rapidamente deslizam quando o foco muda para a área de conteúdo abaixo.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barras de guia no tvOS

O `UITabViewController` funciona de maneira semelhante e serve a uma finalidade similar tvos como faz no iOS, com as seguintes diferenças principais:

- Ao contrário de barra de guia no iOS, que aparece na parte inferior da tela, barras de guia no tvOS ocupam os 140 pixels superior da tela e são translúcidas por padrão.
- Quando o foco deixar a barra de guia para a área de conteúdo abaixo, a barra de guia será rapidamente deslize da parte superior da tela e oculta. O usuário pode tocar no botão de Menu de uma vez ou passe o dedo para cima na [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para mostrar a barra de guia novamente.
- Deslizar para baixo no Siri remoto será move o foco para a área de conteúdo abaixo da barra de guia para a primeira [focalizável Item](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) no conteúdo que está sendo mostrado. Novamente, isso ocultará a barra de guia depois que o foco muda.
- Clicar para selecionar uma categoria exibida na barra de guia será alternado para que conteúdo e o foco da categoria serão alternadas para o primeiro Item focalizável nessa exibição.
- O número de categorias exibido na barra de guia deve ser corrigido e todas as categorias devem ser acessíveis em todos os momentos, uma determinada categoria nunca deve ser desabilitada.
- Barras de guia não dão suporte a personalização do tvos. Além disso, eles não mostrarem o **mais** categoria (como o iOS) se não houver mais categorias que pode caber na barra de guias.

Apple tem as sugestões a seguir para trabalhar com barras de guia:

- **Use as barras de guia para organizar conteúdo logicamente** -Use a barra de guia para organizar logicamente o conteúdo que seu aplicativo tvOS funciona. Por exemplo, em destaque, gráficos da parte superior, comprado e pesquisa.
- **Adicionar notificações para informar os usuários do novo conteúdo** -opcionalmente, você pode exibir uma notificação (uma elipse com um número em branco ou o ponto de exclamação vermelha) para informar ao usuário o novo conteúdo em uma categoria.
- **Use notificações com moderação** - não sobrecarregar a barra de guia com notificações e apenas exibi-los em que eles fornecem informações críticas para o usuário.
- **Limitar o número de categorias** - para reduzir a complexidade e mantê-lo aplicativo gerenciável, não sobrecarregar sua barra de guias com categorias e certifique-se de que todas as categorias são visíveis e não está cheia. Títulos curtos e simples funcionam melhor.
- **Não desabilite a uma categoria** -todas as guias (categorias) deve ser sempre visível e habilitado em todos os momentos. Se uma determinada guia não tem nenhum conteúdo, fornece uma explicação para o usuário por que. Por exemplo, na guia de compras será vazia se o usuário não tenha feito nenhuma compras.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Itens de barra de guias

Cada categoria (guia) na barra de guia é representada por um Item da barra de guia (`UITabBarItem`). Apple tem as sugestões a seguir para trabalhar com itens da barra de guia:

- **Usar texto com base em guias** -enquanto o Item da barra de guia a é capaz de ser representado como um ícone, Apple sugere o uso somente como um título conciso é mais fácil de interpretar que um ícone de texto.
- **Use nomes significativos de curto ou verbos** -um Item da barra de guia claramente devem retransmitir o conteúdo que ele contém e funciona melhor quando ele é um substantivo simple (por exemplo, fotos, filmes ou músicas) ou verbos (como pesquisa ou reprodução).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barras de guia e Storyboards

A maneira mais fácil trabalhar com barras de guia em um aplicativo xamarin. tvos é adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)
    
1. Inicie um novo aplicativo xamarin. tvos e selecione **tvOS** > **App** > **aplicativo com guias**: 

    [![](tab-bars-images/tab02.png "Selecione o aplicativo com guias")](tab-bars-images/tab02.png#lightbox)
1. Siga todos os prompts para criar uma nova solução xamarin. tvos.
1. No **painel de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Para alterar o **ícone** ou **título** para uma determinada categoria, selecione o **Item da barra de guia** para o **controlador de exibição** no  **Estrutura de tópicos de documento**:

    [![](tab-bars-images/tab03a.png "A barra de guia de Item para o controlador de exibição na estrutura de tópicos do documento")](tab-bars-images/tab03a.png#lightbox)
1. Em seguida, defina as propriedades necessárias **guia Widget** da **Gerenciador de propriedades**: 

    [![](tab-bars-images/tab03.png "Na guia de Widget")](tab-bars-images/tab03.png#lightbox)
1. Para adicionar uma nova categoria (guia), soltar uma **controlador de exibição** para a superfície de design: 

    [![](tab-bars-images/tab04.png "Um controlador de exibição")](tab-bars-images/tab04.png#lightbox)
1. CTRL + clique e arraste a **controlador de exibição da guia** para a nova **controlador de exibição**.
1. Na janela pop-up, selecione **exibir controladores** para adicionar o novo modo de exibição como uma guia (categoria): 

    [![](tab-bars-images/tab05.png "Selecione a guia")](tab-bars-images/tab05.png#lightbox)
1. Crie o layout da interface do usuário para cada área de conteúdo Caterogies como normal, com a adição de elementos de interface do usuário no Designer do iOS.
1. Expor todos os eventos necessários para trabalhar com os controles de interface do usuário em C# código.
1. Nomeie todos os controles da interface do usuário que você deseja expor no C# código.
1. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. Inicie um novo aplicativo xamarin. tvos e selecione **tvOS** > **App** > **aplicativo com guias**: 

    [![](tab-bars-images/tab02vs.png "Selecione o aplicativo com guias")](tab-bars-images/tab02vs.png#lightbox)
1. Siga todos os prompts para criar uma nova solução xamarin. tvos.
1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Para alterar o **ícone** ou **título** para uma determinada categoria, selecione o **Item da barra de guia** para o **controlador de exibição** no  **Estrutura de tópicos de documento**:

    [![](tab-bars-images/tab03avs.png "O controlador de exibição na estrutura de tópicos do documento")](tab-bars-images/tab03avs.png#lightbox)
1. Em seguida, defina as propriedades necessárias **guia Widget** da **Gerenciador de propriedades**: 

    [![](tab-bars-images/tab03vs.png "Na guia de Widget")](tab-bars-images/tab03vs.png#lightbox)
1. Para adicionar uma nova categoria (guia), arraste uma **controlador de exibição** da **caixa de ferramentas** e solte-o para a superfície de design: 

    [![](tab-bars-images/tab04vs.png "Um controlador de exibição")](tab-bars-images/tab04vs.png#lightbox)
1. CTRL + clique e arraste a **controlador de exibição da guia** para a nova **controlador de exibição**.
1. Na janela pop-up, selecione **exibir controladores** para adicionar o novo modo de exibição como uma guia (categoria): 

    [![](tab-bars-images/tab05vs.png "Selecione a guia")](tab-bars-images/tab05vs.png#lightbox)
1. Crie o layout da interface do usuário para cada área de conteúdo Caterogies como normal, com a adição de elementos de interface do usuário no Designer do iOS.
1. Expor todos os eventos necessários para trabalhar com os controles de interface do usuário em C# código.
1. Nomeie todos os controles da interface do usuário que você deseja expor no C# código.
1. Salve as alterações.
    
-----

> [!IMPORTANT]
> Embora seja possível atribuir a eventos, como `TouchUpInside` a um elemento de interface do usuário (como um `UIButton`) no Designer do iOS, ele nunca será chamado como Apple TV não tem um toque de tela ou dar suporte a eventos de toque. Você sempre deve usar o `Primary Action ` eventos durante a criação de manipuladores de eventos para tvOS elementos da interface do usuário.

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Trabalhando com barras de guia

Use o `Items` propriedade do `UITabBar` para acessar a coleção de `UITabBarItems` contém como uma matriz indexada zero (0). O `SelectedItem` propriedade retornará a guia selecionada no momento (categoria) como um `UITabBarItem`.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Trabalhando com itens de barra de guias

Para exibir uma notificação em uma guia determinada (uma elipse vermelha com texto em branco), use o seguinte código:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Que produziria os seguintes resultados quando executado:

[![](tab-bars-images/tab06.png "Um Item da barra de guia com notificação")](tab-bars-images/tab06.png#lightbox)

Use o `Title` propriedade do `UITabBarItem` para alterar o título e o `Image` propriedade para alterar o ícone.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a projetar e trabalhar com o controlador de barra de guia dentro de um aplicativo xamarin. tvos.




## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
