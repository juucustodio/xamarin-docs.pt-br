---
title: Por que meu projeto do Xamarin.Forms.Maps Android que falha com erro INESPERADO de nível superior COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250419"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Por que meu projeto do Xamarin.Forms.Maps Android que falha com erro INESPERADO de nível superior COMPILETODALVIK?

Esse erro pode ser visto no painel de erro do Visual Studio para Mac ou na janela de saída do Build do Visual Studio; em projetos do Android usando Xamarin.Forms.Maps.

Isso geralmente é resolvido aumentando o tamanho do Heap de Java para o seu projeto xamarin. Android. Siga estas etapas para aumentar o tamanho do heap:

## <a name="visual-studio"></a>Visual Studio

1. Clique com botão direito no projeto do Android e abrir as opções de projeto.
2. Vá para **opções do Android -> Avançado**
3. Na caixa de texto de tamanho do heap de Java, insira 1G.
4. Recompile o projeto.

![Captura de tela de opções de projeto do Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Android opções de Build no Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1.  Clique com botão direito no projeto do Android e abrir as opções de projeto.
2.  Vá para **Build -> Build do Android -> Avançado**
3.  Na caixa de texto de tamanho do heap de Java, insira 1G.
4.  Recompile o projeto.  

![Captura de tela do Visual Studio para opções de projeto do Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Android opções de compilação no Visual Studio para Mac")

