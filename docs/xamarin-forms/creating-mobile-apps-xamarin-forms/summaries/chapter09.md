---
title: Resumo do capítulo 9. Chamadas de API específicas à plataforma
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 9. Chamadas de API específicas à plataforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8feb636057f1e11c7df90236dee44697203d51c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136845"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumo do capítulo 9. Chamadas de API específicas à plataforma

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

Às vezes, é necessário executar algum código que varie de acordo com a plataforma. Este capítulo explora as técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pré-processando no projeto de ativo compartilhado

Um Xamarin.Forms projeto de ativo compartilhado pode executar código diferente para cada plataforma usando as diretivas de pré-processador do C# `#if` , `#elif` e `endif` . Isso é demonstrado em [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Captura de tela tripla de parágrafo formatado por variável](images/ch09fg01-small.png "Modelo de dispositivo e sistema operacional")](images/ch09fg01-large.png#lightbox "Modelo de dispositivo e sistema operacional")

No entanto, o código resultante pode ser ruim e difícil de ler.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classes paralelas no projeto de ativo compartilhado

Uma abordagem mais estruturada para executar o código específico da plataforma no SAP é demonstrada no exemplo de [**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) . Cada um dos projetos de plataforma contém uma classe nomeada e métodos idênticos, mas implementados para essa plataforma específica. O SAP, em seguida, simplesmente instancia a classe e chama o método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e a biblioteca de classes portátil

> [!NOTE] 
> As bibliotecas de classes portáteis foram substituídas por bibliotecas de .NET Standard. Todo o código de exemplo do livro foi convertido para usar bibliotecas do .NET Standard.

Normalmente, uma biblioteca não pode acessar classes em projetos de aplicativo. Essa restrição parece impedir que a técnica mostrada em **PlatInfoSap2** seja usada em uma biblioteca. No entanto, Xamarin.Forms contém uma classe chamada [`DependencyService`](xref:Xamarin.Forms.DependencyService) que usa a reflexão .net para acessar classes públicas no projeto de aplicativo da biblioteca.

A biblioteca deve definir um `interface` com os membros que ele precisa usar em cada plataforma. Em seguida, cada uma das plataformas contém uma implementação dessa interface. A classe que implementa a interface deve ser identificada com um [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) no nível do assembly.

Em seguida, a biblioteca usa o [`Get`](xref:Xamarin.Forms.DependencyService.Get*) método genérico de `DependencyService` para obter uma instância da classe de plataforma que implementa a interface.

Isso é demonstrado no exemplo de [**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) .

## <a name="platform-specific-sound-generation"></a>Geração de som específica da plataforma

O exemplo [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) adiciona sinais sonoros ao programa **MonkeyTap** acessando instalações de geração de som em cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Capítulo 9 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Exemplos do capítulo 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
