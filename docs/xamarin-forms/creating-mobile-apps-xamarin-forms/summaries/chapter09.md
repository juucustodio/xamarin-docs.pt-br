---
title: Resumo do capítulo 9. Chamadas à API específicas da plataforma
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 9. Chamadas à API específicas da plataforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334456"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumo do capítulo 9. Chamadas à API específicas da plataforma

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Às vezes, é necessário executar algum código que varia por plataforma. Este capítulo explora as técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pré-processamento no projeto ativo compartilhado

Um projeto de ativos do Xamarin. Forms compartilhado pode executar código diferente para cada plataforma C# usando as diretivas de pré-processador `#if`, `#elif`e `endif`. Isso é demonstrado em [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Captura de tela tripla de parágrafo formatado por variável](images/ch09fg01-small.png "Modelo de dispositivo e sistema operacional")](images/ch09fg01-large.png#lightbox "Modelo de dispositivo e sistema operacional")

No entanto, o código resultante pode ser desagradável e difícil de ler.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classes de paralelas no projeto ativo compartilhado

Uma abordagem mais estruturada para executar o código específico da plataforma no SAP é demonstrada no exemplo de [**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) . Cada um dos projetos de plataforma contém uma classe nomeada de forma idêntica e métodos, mas implementado para a plataforma específica. O SAP, em seguida, simplesmente instancia a classe e chama o método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e a biblioteca de classes portátil

> [!NOTE] 
> Bibliotecas de classes portáteis foram substituídas por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET.

Uma biblioteca normalmente não é possível acessar classes em projetos de aplicativos. Essa restrição parece impedir que a técnica mostrada em **PlatInfoSap2** seja usada em uma biblioteca. No entanto, o Xamarin. Forms contém uma classe chamada [`DependencyService`](xref:Xamarin.Forms.DependencyService) que usa a reflexão .net para acessar classes públicas no projeto de aplicativo da biblioteca do.

A biblioteca deve definir um `interface` com os membros que ele precisa usar em cada plataforma. Em seguida, cada uma das plataformas contém uma implementação dessa interface. A classe que implementa a interface deve ser identificada com um [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) no nível do assembly.

Em seguida, a biblioteca usa o método de [`Get`](xref:Xamarin.Forms.DependencyService.Get*) genérico de `DependencyService` para obter uma instância da classe de plataforma que implementa a interface.

Isso é demonstrado no exemplo de [**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) .

## <a name="platform-specific-sound-generation"></a>Geração de som específica da plataforma

O exemplo [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) adiciona sinais sonoros ao programa **MonkeyTap** acessando instalações de geração de som em cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Capítulo 9 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Exemplos do capítulo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
