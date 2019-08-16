---
title: Por que meu Xamarin. Forms. Maps projeto do Android falha com COMPILETODALVIK erro de nível superior inesperado?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: efa21d3547167996e1a7dcc533caa00e0b1262e6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529040"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Por que meu Xamarin. Forms. Maps projeto do Android falha com COMPILETODALVIK erro de nível superior inesperado?

Esse erro pode ser visto no painel de erro de Visual Studio para Mac ou na janela de saída da compilação do Visual Studio; em projetos do Android usando Xamarin. Forms. Maps.

Isso é normalmente resolvido aumentando o tamanho do heap do Java para seu projeto Xamarin. Android. Siga estas etapas para aumentar o tamanho do heap:

## <a name="visual-studio"></a>Visual Studio

1. Clique com o botão direito do mouse no projeto do Android & abra as opções do projeto.
2. Vá para **Opções do Android-> avançado**
3. Na caixa de texto tamanho do heap do Java, digite 1G.
4. Recompile o projeto.

![Captura de tela das opções de projeto do Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Opções de Build do Android no Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do Android & abra as opções do projeto.
2. Vá para **compilação-> Android Build-> avançado**
3. Na caixa de texto tamanho do heap do Java, digite 1G.
4. Recompile o projeto.  

![Captura de tela das opções de projeto de Visual Studio para Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Opções de Build do Android no Visual Studio para Mac")

