---
title: Estrutura de destino para Xamarin. Mac
description: Este artigo aborda as estruturas de destino (bibliotecas de classes base) disponíveis para o Xamarin. Mac e as implicações de usá-las em seu projeto Xamarin. Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: a612c2c23ceff13ea1d602465573514547628e55
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769801"
---
# <a name="target-framework-for-xamarinmac"></a>Estrutura de destino para Xamarin. Mac

_Este artigo aborda as estruturas de destino (bibliotecas de classes base) disponíveis para o Xamarin. Mac e as implicações de usá-las em seu projeto Xamarin. Mac._

![Opções de estrutura de destino para Xamarin. Mac](target-framework-images/select-target.png "Opções de estrutura de destino para Xamarin. Mac")

## <a name="background"></a>Informações preliminares

Cada programa ou biblioteca .NET depende da funcionalidade fornecida pela BCL (base Class Library). Essa BCL inclui assemblies como mscorlib, System, System .net. http e System. XML que fornecem a funcionalidade comum incorporada em todas as linguagens .NET.

Ao longo dos anos, desenvolveu várias versões diferentes dessa BCL, otimizadas para diferentes casos de uso. A BCL de "área de trabalho" inclui um conjunto mais rico de bibliotecas que podem ser muito pesadas para outros casos de uso, enquanto a mobilidade se concentra em garantir que as APIs sejam seguras para vinculação, o que remove o código não utilizado para reduzir a superfície do aplicativo.

Uma das repercussões mais importantes dessas diferentes estruturas de destino é que todos os assemblies em um determinado programa *devem* ter como destino ASSEMBLIES de BCL compatíveis. Se esse não for o caso, você poderia ter dois assemblies vinculados a diferentes versões do **System. dll** desconhecendo a assinatura de um determinado tipo. Uma biblioteca compartilhada pode ter como destino [.net Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), que é o subconjunto comum das estruturas de destino ou uma estrutura de destino específica.

Há três opções de estrutura de destino disponíveis para o Xamarin. Mac, cada uma com vantagens e desvantagens diferentes:

- **Moderno** (chamado de Mobile na documentação mais antiga) – um subconjunto muito semelhante ao que capacita o Xamarin. iOS, altamente ajustado para desempenho e tamanho. Essa estrutura de destino é segura para o vinculador, portanto, esses projetos podem ter sua superfície final reduzida drasticamente com a remoção de código não utilizado.

- **Completo** (chamado de XM 4,5 na documentação mais antiga) – um subconjunto muito semelhante à BCL de "área de trabalho", com algumas pequenas remoções. Como a estrutura de destino é quase idêntica ao Net45 (e posterior), ela pode facilmente consumir muitas nugets que não fornecem netstandard2 ou compilações específicas do Xamarin. Mac. No entanto, devido ao uso de System. Configuration, ele é incompatível com a vinculação.

- **Sem suporte** (chamado de sistema na documentação mais antiga) – em vez de vincular a uma BCL fornecida pelo Xamarin. Mac, use o mono instalado do sistema atual. Isso fornece o conjunto completo de assemblies, incluindo algum problema conhecido (System. Drawing, por exemplo). Essa opção existe apenas com um "último recurso" e é altamente recomendável esgotar outras opções antes de usá-la. Como o nome indica, o uso não é suportado por canais de suporte oficial.

## <a name="setting-the-target-framework"></a>Configurando a estrutura de destino

Para alterar para o tipo de estrutura de destino de um projeto Xamarin. Mac, faça o seguinte:

1. Abra o projeto Xamarin.Mac no Visual Studio para Mac.
2. No **Gerenciador de Soluções**, clique duas vezes no arquivo do projeto para abrir a caixa de diálogo **Opções de Projeto**.
3. Na guia **geral** , selecione o tipo de **estrutura de destino** que atenda às necessidades do seu aplicativo:

    [![Usando a janela Opções do projeto para escolher uma estrutura de destino](target-framework-images/select-target-full.png "Usando a janela Opções do projeto para escolher uma estrutura de destino")](target-framework-images/select-target-full-large.png#lightbox)

4. Clique no botão **OK** para salvar suas alterações.

Você deve **limpar** e, em seguida, **Recompilar** seu projeto Xamarin. Mac depois de alternar o tipo de estrutura de destino.

## <a name="summary"></a>Resumo

Este artigo abordou brevemente os diferentes tipos de estruturas de destino (bibliotecas de classes base) disponíveis para um aplicativo Xamarin. Mac e quando cada tipo de estrutura deve ser usado.

## <a name="related-links"></a>Links relacionados

- [compartilhamento de código iOS e Mac](~/cross-platform/macios/index.md)
- [API Unificada](~/cross-platform/macios/unified/index.md)
- [Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)
- [Assemblies](~/cross-platform/internals/available-assemblies.md)
- [Atualizando aplicativos Mac existentes](~/cross-platform/macios/unified/updating-mac-apps.md)
