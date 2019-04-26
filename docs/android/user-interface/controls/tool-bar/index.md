---
title: Barra de ferramentas
description: 'A barra de ferramentas é um componente de barra de ação que fornece mais flexibilidade do que a barra de ação padrão: ele pode ser colocado em qualquer lugar no aplicativo, seu tamanho pode ser alterado e ele pode usar um esquema de cores é diferente do tema do aplicativo. Além disso, cada tela do aplicativo pode ter várias barras de ferramentas.'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 2c9de4058fdaee53671e65f49ad95c3af5e127d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082817"
---
# <a name="toolbar"></a>Barra de ferramentas

_A barra de ferramentas é um componente de barra de ação que fornece mais flexibilidade do que a barra de ação padrão: ele pode ser colocado em qualquer lugar no aplicativo, seu tamanho pode ser alterado e ele pode usar um esquema de cores é diferente do tema do aplicativo. Além disso, cada tela do aplicativo pode ter várias barras de ferramentas._

 
## <a name="overview"></a>Visão geral

Um elemento de design importante de qualquer atividade Android é um *barra de ação*. A barra de ação é o componente de interface do usuário que é usado para navegação, pesquisa, menus e identidade visual em um aplicativo Android. Em versões Android anteriores ao Android 5.0 Lollipop, a barra de ação (também conhecido como o *barra de aplicativo*) era o componente recomendado para fornecer essa funcionalidade. 

O `Toolbar` widget (introduzida no Android 5.0 Lollipop) pode ser pensada como uma generalização do que a interface da barra de ação &ndash; destina-se para substituir a barra de ação. O `Toolbar` pode ser usado em qualquer lugar em um layout do aplicativo e é muito mais personalizável do que uma barra de ação. Captura de tela a seguir ilustra o personalizado `Toolbar` exemplo criado neste guia: 

[![Captura de tela de exemplo de uma barra de ferramentas com editar, salvar e itens de menu de estouro](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Há algumas diferenças importantes entre o `Toolbar` e a barra de ação: 

-   Um `Toolbar` pode ser colocado em qualquer lugar na interface do usuário.

-   Várias barras de ferramentas podem ser exibidas na mesma tela.

-   Se forem usados fragmentos, cada fragmento pode ter seu próprio `Toolbar`. 

-   Um `Toolbar` pode ser configurada para abranger apenas uma parcial largura da tela. 

-   Porque o `Toolbar` não está associado ao esquema de cores de decoração de janela de atividade, ele pode ter um esquema de cores visualmente distintos. 

-   Ao contrário da barra de ação, o `Toolbar` não inclui um ícone à esquerda. Seus menus à direita usam menos espaço. 

-   O `Toolbar` altura é ajustável. 

-   Outros modos de exibição podem ser incluídos dentro de `Toolbar`. 

Um `Toolbar` pode conter um ou mais dos seguintes elementos: 

-   Botão de navegação

-   Uma imagem de logotipo com marca

-   Título e subtítulo

-   Modos de exibição personalizados

-   Menu de ação

-   Menu de estouro

Do Google [diretrizes de Design de Material](https://material.google.com/) recomenda aproveitando esses elementos para os aplicativos uma aparência distinta (em vez de depender exclusivamente por um ícone do aplicativo e o título). 

Este guia aborda o mais usado `Toolbar` cenários:

-   Substituindo a barra de ação padrão de uma atividade com um `Toolbar`. 

-   Adicionar uma segunda `Toolbar` a uma atividade.

-   Usando o **biblioteca de suporte ao Android v7 AppCompat** biblioteca (conhecido como *AppCompat* no restante deste guia) para implantar `Toolbar` em versões anteriores do Android. 

 
 
## <a name="requirements"></a>Requisitos

`Toolbar` está disponível no Android 5.0 Lollipop (API 21) e posterior. Quando destinada ao Android versões anteriores ao Android 5.0, use o [dar suporte a biblioteca do Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), que fornece compatível `Toolbar` dar suporte a um pacote do NuGet. 
[Barra de ferramentas compatibilidade](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) explica como usar essa biblioteca. 




## <a name="related-links"></a>Links relacionados

- [Barra de ferramentas de pirulito (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra de ferramentas AppCompat (amostra)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
