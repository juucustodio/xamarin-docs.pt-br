---
title: Por que meu projeto Xamarin.Forms.Maps Android falha com erro INESPERADO de nível superior COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Por que meu projeto Xamarin.Forms.Maps Android falha com erro INESPERADO de nível superior COMPILETODALVIK?

Esse erro pode ser visto no painel de erro do Visual Studio para Mac ou na janela de saída de compilação do Visual Studio; em projetos Android usando Xamarin.Forms.Maps.

Isso geralmente é resolvido, aumentando o tamanho do Heap do Java para o seu projeto xamarin. Siga estas etapas para aumentar o tamanho do heap:

## <a name="visual-studio"></a>Visual Studio

1. Clique com botão direito do Android & Abrir as opções de projeto.
2. Vá para **Android opções -> Avançado**
3. Na caixa de texto de tamanho do heap de Java insira 1G.
4. Recompile o projeto.

![Captura de tela das opções de projeto do Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "opções no Visual Studio de Build do Android")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1.  Clique com botão direito do Android & Abrir as opções de projeto.
2.  Vá para **Build -> compilação Android -> Avançado**
3.  Na caixa de texto de tamanho do heap de Java insira 1G.
4.  Recompile o projeto.  

![Captura de tela do Visual Studio para Mac projeto opções](maps-compiletodalvik-error-images/xsjavaheap.png "Android compilar opções no Visual Studio para Mac")

