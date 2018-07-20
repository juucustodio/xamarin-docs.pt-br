---
title: Resumo do capítulo 9. Chamadas à API específicas da plataforma
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 9. Chamadas à API específicas da plataforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: e7b2eea22758155db7d79fa26f3376e16cf16a45
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39157010"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumo do capítulo 9. Chamadas à API específicas da plataforma

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Às vezes, é necessário executar algum código que varia por plataforma. Este capítulo explora as técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pré-processamento no projeto ativo compartilhado

Um projeto de ativo compartilhado do xamarin. Forms pode executar um código diferente para cada plataforma usando as diretivas de pré-processador c# `#if`, `#elif`, e `endif`. Isso é demonstrado em [ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Tripla captura de tela da variável formatado parágrafo](images/ch09fg01-small.png "modelo de dispositivo e o sistema operacional")](images/ch09fg01-large.png#lightbox "modelo do dispositivo e o sistema operacional")

No entanto, o código resultante pode ser desagradável e difícil de ler.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classes de paralelas no projeto ativo compartilhado

Uma abordagem mais estruturada para a execução de código específico da plataforma no SAP é demonstrada na [ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) exemplo. Cada um dos projetos de plataforma contém uma classe nomeada de forma idêntica e métodos, mas implementado para a plataforma específica. O SAP, em seguida, simplesmente instancia a classe e chama o método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e a biblioteca de classes portátil

> [!NOTE] 
> Bibliotecas de classes portáteis foram substituídas por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET.

Uma biblioteca normalmente não é possível acessar classes em projetos de aplicativos. Essa restrição parece impedir que a técnica mostrada **PlatInfoSap2** sejam usados em uma biblioteca. No entanto, o xamarin. Forms contém uma classe chamada [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) que usa a reflexão do .NET para acessar as classes públicas no projeto de aplicativo da biblioteca.

A biblioteca deve definir um `interface` com os membros que ele precisa usar em cada plataforma. Em seguida, cada uma das plataformas contém uma implementação dessa interface. A classe que implementa a interface deve ser identificada com uma [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) no nível de assembly.

A biblioteca, em seguida, usa o genérico [ `Get` ](xref:Xamarin.Forms.DependencyService.Get*) método `DependencyService` para obter uma instância da classe de plataforma que implementa a interface.

Isso é demonstrado na [ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) exemplo.

## <a name="platform-specific-sound-generation"></a>Geração de som específica da plataforma

O [ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) exemplo adiciona o alarme sonoro para o **MonkeyTap** programa acessando recursos de geração de som em cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 9 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Exemplos do capítulo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
