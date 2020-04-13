---
title: Resumo do Capítulo 9. Chamadas de API específicas à plataforma
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 9. Chamadas de API específicas à plataforma'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334456"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Resumo do Capítulo 9. Chamadas de API específicas à plataforma

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Às vezes é necessário executar algum código que varia de acordo com a plataforma. Este capítulo explora as técnicas.

## <a name="preprocessing-in-the-shared-asset-project"></a>Pré-processamento no Projeto de Ativos Compartilhados

Um Projeto de Ativos Compartilhados Xamarin.Forms pode executar códigos diferentes para cada plataforma usando as diretivas `#if`de pré-processador C# , `#elif`e `endif`. Isso é demonstrado no [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Captura de tela tripla de parágrafo formatado variável](images/ch09fg01-small.png "Modelo do dispositivo e sistema operacional")](images/ch09fg01-large.png#lightbox "Modelo do dispositivo e sistema operacional")

No entanto, o código resultante pode ser feio e difícil de ler.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Aulas paralelas no Projeto De Ativos Compartilhados

Uma abordagem mais estruturada para a execução de código específico da plataforma no SAP é demonstrada na amostra [**PlatInfoSap2.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) Cada um dos projetos da plataforma contém uma classe e métodos nomeados de forma idêntica, mas implementados para essa plataforma em particular. O SAP então simplesmente instancia a classe e chama o método.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService e a Biblioteca de Classes Portáteis

> [!NOTE] 
> Bibliotecas de classe portáteis foram substituídas por bibliotecas padrão .NET. Todo o código de amostra do livro foi convertido para usar bibliotecas padrão .NET.

Uma biblioteca normalmente não pode acessar classes em projetos de aplicativos. Essa restrição parece impedir que a técnica mostrada no **PlatInfoSap2** seja usada em uma biblioteca. No entanto, o Xamarin.Forms contém uma classe chamada [`DependencyService`](xref:Xamarin.Forms.DependencyService) .NET reflection para acessar classes públicas no projeto de aplicação da biblioteca.

A biblioteca deve `interface` definir um com os membros que precisa usar em cada plataforma. Em seguida, cada uma das plataformas contém uma implementação dessa interface. A classe que implementa a interface deve ser identificada com um [Atributo de Dependência](xref:Xamarin.Forms.DependencyAttribute) no nível de montagem.

Em seguida, a [`Get`](xref:Xamarin.Forms.DependencyService.Get*) biblioteca `DependencyService` usa o método genérico de obter uma instância da classe de plataforma que implementa a interface.

Isso é demonstrado na amostra [**DisplayPlatformInfo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)

## <a name="platform-specific-sound-generation"></a>Geração de som específica da plataforma

A amostra [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) adiciona bipes ao programa **MonkeyTap** acessando instalações de geração de som em cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Capítulo 9 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Capítulo 9 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Serviço de dependência](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
