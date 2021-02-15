---
title: Barra de ferramentas
description: 'A barra de ferramentas é um componente da barra de ação que fornece mais flexibilidade do que a barra de ação padrão: ela pode ser colocada em qualquer lugar no aplicativo, seu tamanho pode ser alterado e pode usar um esquema de cores diferente do tema do aplicativo. Além disso, cada tela de aplicativo pode ter várias barras de ferramentas.'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 85d649ec464b725b5e0b438c650e0c9f32d23a27
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457674"
---
# <a name="toolbar"></a>Barra de ferramentas

_A barra de ferramentas é um componente da barra de ação que fornece mais flexibilidade do que a barra de ação padrão: ela pode ser colocada em qualquer lugar no aplicativo, seu tamanho pode ser alterado e pode usar um esquema de cores diferente do tema do aplicativo. Além disso, cada tela de aplicativo pode ter várias barras de ferramentas._

## <a name="overview"></a>Visão geral

Um elemento-chave de design de qualquer atividade do Android é uma *barra de ação*. A barra de ação é o componente da interface do usuário que é usado para navegação, pesquisa, menus e identidade visual em um aplicativo Android. Em versões do Android antes do Android 5,0 pirulito, a barra de ação (também conhecida como a *barra de aplicativos*) era o componente recomendado para fornecer essa funcionalidade. 

O `Toolbar` widget (introduzido no Android 5,0 pirulito) pode ser considerado uma generalização da interface da barra de ações &ndash; para substituir a barra de ação. O `Toolbar` pode ser usado em qualquer lugar em um layout de aplicativo e é muito mais personalizável do que uma barra de ação. A captura de tela a seguir ilustra o `Toolbar` exemplo personalizado criado neste guia: 

[![Captura de tela de exemplo de uma barra de ferramentas com itens de menu Editar, salvar e estouro](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Há algumas diferenças importantes entre o `Toolbar` e a barra de ação: 

- Um `Toolbar` pode ser colocado em qualquer lugar na interface do usuário.

- Várias barras de ferramentas podem ser exibidas na mesma tela.

- Se forem usados fragmentos, cada fragmento poderá ter seu próprio `Toolbar` . 

- Um `Toolbar` pode ser configurado para abranger apenas uma largura parcial da tela. 

- Como o `Toolbar` não está associado ao esquema de cores da janela da atividade décor, ele pode ter um esquema de cores visualmente distinto. 

- Ao contrário da barra de ação, o não `Toolbar` inclui um ícone à esquerda. Seus menus à direita usam menos espaço. 

- A `Toolbar` altura é ajustável. 

- Outras exibições podem ser incluídas dentro do `Toolbar` . 

Um `Toolbar` pode conter um ou mais dos seguintes elementos: 

- Botão de navegação

- Uma imagem de logotipo com marca

- Título e subtítulo

- Modos de exibição personalizados

- Menu Ação

- Menu de estouro

As [diretrizes de design de material](https://material.google.com/) do Google recomendam aproveitar esses elementos para dar aos aplicativos uma aparência distinta (em vez de depender exclusivamente de um ícone e título de aplicativo). 

Este guia aborda os cenários mais comumente usados `Toolbar` :

- Substituindo a barra de ação padrão de uma atividade por um `Toolbar` . 

- Adicionando um segundo `Toolbar` a uma atividade.

- Usando a biblioteca de suporte do Android da biblioteca **v7 AppCompat** (conhecida como *AppCompat* no restante deste guia) para implantar   `Toolbar` em versões anteriores do Android. 

## <a name="requirements"></a>Requisitos

`Toolbar` está disponível no Android 5,0 pirulito (API 21) e posterior. Ao direcionar as versões do Android anteriores ao Android 5,0, use a [biblioteca de suporte do Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), que fornece suporte compatível com versões posteriores `Toolbar` em um pacote NuGet. 
[Compatibilidade de barra de ferramentas](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) explica como usar essa biblioteca. 

## <a name="related-links"></a>Links Relacionados

- [Barra de ferramentas pirulito (exemplo)](/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra de ferramentas AppCompat (exemplo)](/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)