---
title: Barra de ferramentas
description: "A barra de ferramentas é um componente de barra de ação que fornece mais flexibilidade do que a barra de ação padrão: ele pode ser colocado em qualquer lugar no aplicativo, seu tamanho pode ser alterado e ele pode usar um esquema de cores diferente do tema do aplicativo. Além disso, cada tela do aplicativo pode ter várias barras de ferramentas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: cf2211a572d45b7c29018d00f36cb8408484483f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="toolbar"></a>Barra de ferramentas

_A barra de ferramentas é um componente de barra de ação que fornece mais flexibilidade do que a barra de ação padrão: ele pode ser colocado em qualquer lugar no aplicativo, seu tamanho pode ser alterado e ele pode usar um esquema de cores diferente do tema do aplicativo. Além disso, cada tela do aplicativo pode ter várias barras de ferramentas._


<a name="overview" />
 
## <a name="overview"></a>Visão geral

Um elemento de design importante de qualquer atividade Android é um *barra de ação*. A barra de ação é o componente de interface do usuário que é usada para navegação, pesquisa, menus e identidade visual em um aplicativo do Android. No Android versões antes de pirulito do Android 5.0, a barra de ação (também conhecido como o *barra de aplicativos*) foi o componente recomendado para fornecer essa funcionalidade. 

O `Toolbar` widget (introduzido no Android 5.0 de pirulito) pode ser pensada como uma generalização da interface da barra de ação &ndash; destina-se para substituir a barra de ação. O `Toolbar` pode ser usado em qualquer lugar no layout de um aplicativo, e é muito mais personalizável do que uma barra de ação. Captura de tela a seguir ilustra o personalizado `Toolbar` exemplo criado neste guia: 

[![Captura de tela de exemplo de uma barra de ferramentas com editar, salvar e itens de menu de estouro](images/01-toolbar-sml.png)](images/01-toolbar.png)

Há algumas diferenças importantes entre o `Toolbar` e a barra de ação: 

-   Um `Toolbar` pode ser colocado em qualquer lugar na interface do usuário.

-   Várias barras de ferramentas podem ser exibidas na tela do mesmo.

-   Se os fragmentos são usados, cada fragmento pode ter seu próprio `Toolbar`. 

-   Um `Toolbar` pode ser configurada para abranger apenas uma parcial largura da tela. 

-   Porque o `Toolbar` não está associado ao esquema de cores da casa de janela da atividade, ele pode ter um esquema de cores distintas visualmente. 

-   Ao contrário da barra de ação, o `Toolbar` não tem um ícone à esquerda. Seus menus à direita usam menos espaço. 

-   O `Toolbar` altura é ajustada. 

-   Outros modos de exibição podem ser incluídos dentro de `Toolbar`. 

Um `Toolbar` pode conter um ou mais dos seguintes elementos: 

-   Botão de navegação

-   Uma imagem de logotipo de marca

-   Título e subtítulo

-   Modos de exibição personalizados

-   Menu Ação

-   Menu de estouro

Do Google [diretrizes de Design de Material](https://material.google.com/) recomenda tirar proveito desses elementos para fornecer aplicativos uma aparência distinta (em vez de depender exclusivamente por um ícone de aplicativo e um título). 

Este guia aborda o comumente usada `Toolbar` cenários:

-   Substituir a barra de ação padrão de uma atividade com um `Toolbar`. 

-   Adicionar um segundo `Toolbar` a uma atividade.

-   Usando o **biblioteca de suporte Android v7 AppCompat** biblioteca (conhecido como *AppCompat* no restante deste guia) para implantar `Toolbar` em versões anteriores do Android. 

 
<a name="requirements" />
 
## <a name="requirements"></a>Requisitos

`Toolbar` está disponível no Android 5.0 pirulito (API 21) e posterior. Ao direcionamento Android versões anteriores ao Android 5.0, use o [biblioteca de suporte Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), que fornece compatível com versões anteriores `Toolbar` suporte em um pacote do NuGet. 
[Compatibilidade de barra de ferramentas](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) explica como usar essa biblioteca. 




## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas de AppCompat (exemplo)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
