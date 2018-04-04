---
title: Resumo do capítulo 9. Chamadas à API específica de plataforma
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a988c207185fa2305631be67bdd628de089d247e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumo do capítulo 9. Chamadas à API específica de plataforma

Às vezes, é necessário executar algum código que varia por plataforma. Este capítulo explora as técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>O projeto de ativo compartilhado de pré-processamento

Um projeto de ativo compartilhado xamarin. Forms podem executar código diferente para cada plataforma usando as diretivas de pré-processador C# `#if`, `#elif`, e `endif`. Isso é demonstrado nos [ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Tripla captura de tela da variável formatado parágrafo](images/ch09fg01-small.png "modelo do dispositivo e o sistema operacional")](images/ch09fg01-large.png#lightbox "modelo do dispositivo e o sistema operacional")

No entanto, o código resultante pode ser feio e difíceis de ler.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classes paralelas no projeto de ativo compartilhado

Uma abordagem mais estruturada para a execução de código específico da plataforma no SAP é demonstrada no [ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) exemplo. Cada um dos projetos plataforma contém uma classe com o mesmo nome e métodos, mas implementado para a plataforma específica. O SAP, em seguida, simplesmente cria uma instância da classe e chama o método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e a biblioteca de classes portátil

Uma biblioteca normalmente não é possível acessar classes nos projetos de aplicativo. Essa restrição parece evitar a técnica mostrada no **PlatInfoSap2** sejam usadas em um PCL. No entanto, xamarin. Forms contém uma classe denominada [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) que usa reflexão do .NET para acessar as classes públicas no projeto de aplicativo do PCL.

O PCL deve definir um `interface` com os membros necessários em cada plataforma. Em seguida, cada uma das plataformas contém uma implementação da interface. A classe que implementa a interface deve ser identificada com um [DependencyAttribute](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyAttribute/) em nível de assembly.

O PCL usa genérica [ `Get` ](https://developer.xamarin.com/api/member/Xamarin.Forms.DependencyService.Get{T}/p/Xamarin.Forms.DependencyFetchTarget/) método `DependencyService` para obter uma instância da classe de plataforma que implementa a interface.

Isso é demonstrado no [ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) exemplo.

## <a name="platform-specific-sound-generation"></a>Geração de som específica de plataforma

O [ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) exemplo adiciona avisos sonoros para o **MonkeyTap** programa acessando recursos de geração de som em cada plataforma.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 9 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Exemplos de capítulo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
