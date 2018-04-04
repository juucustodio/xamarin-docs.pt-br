---
title: Estrutura de Destino
description: Este artigo aborda a estrutura de destino (bibliotecas de classes Base) disponível para Xamarin.Mac e as implicações de usá-los em seu projeto Xamarin.Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 053cdd2dbfc7741257e6630e5b11b77b1055428e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="target-framework"></a>Estrutura de Destino

_Este artigo aborda a estrutura de destino (bibliotecas de classes Base) disponível para Xamarin.Mac e as implicações de usá-los em seu projeto Xamarin.Mac._

![Opções de estrutura para Xamarin.Mac de destino](target-framework-images/select-target.png "opções Xamarin.Mac do framework de destino")

## <a name="background"></a>Informações preliminares

Cada programa .NET ou biblioteca depende da funcionalidade fornecida pela classe de biblioteca BCL (Base). Este BCL inclui assemblies como mscorlib, sistema, System e System. XML que fornecem a funcionalidade comum incorporada em todas as linguagens .NET.

Ao longo dos anos, têm desenvolvido várias versões diferentes deste BCL, otimizado para diferentes casos de uso. A BCL "área de trabalho" inclui um valioso conjunto de bibliotecas que pode ser muito alta gramatura para outros casos de uso, enquanto móvel se concentra em assegurar que APIs são seguros para vinculação, que remove o código não utilizado para reduzir a superfície do aplicativo.

Um as mais importante repercussões dessas estruturas de destino diferente, é que todos os assemblies em um determinado programa *deve* assemblies BCL compatíveis de destino. Se esta não for o caso, você pode ter dois assemblies vinculados em relação a versões diferentes do **System.dll** disagreeing sobre a assinatura de um determinado tipo. Uma biblioteca compartilhada pode qualquer destino [.NET padrão 2](https://blog.xamarin.com/share-code-net-standard-2-0/), que é o subconjunto comum de estrutura de destino ou uma estrutura de destino específico.

Há três opções de estrutura de destino para Xamarin.Mac, cada uma com seus prós e contras e diferentes vantagens:

- **Modernos** (chamado Mobile na documentação mais antiga) – um subconjunto muito semelhante a que potências xamarin, altamente ajustadas para desempenho e tamanho. Essa estrutura de destino é vinculador seguro, portanto esses projetos podem ter sua superfície final drasticamente reduzido, removendo o código não utilizado.

- **Completo** (chamado XM 4.5 na documentação mais antiga) – um subconjunto muito semelhante para a BCL "área de trabalho", com poucas remoções pequenas. Como a estrutura de destino é quase idêntica ao net45 (e posterior), facilmente possa consumir muitos nugets que não fornecem um netstandard2 ou cria Xamarin.Mac específico. No entanto, devido ao uso de System. Configuration é incompatível com a vinculação.

- **Não há suporte para** (chamado de sistema na documentação mais antiga) – em vez disso, de vincular a um BCL fornecido pelo Xamarin.Mac, use o mono atual do sistema instalado. Isso fornece o conjunto mais completo de assemblies, inclusive alguns conhecido problemáticos (System. Drawing por exemplo). Essa opção só existe tem "último recurso" e é altamente recomendável esgotar outras opções antes de usá-lo. Como o nome implica, uso é suportado pelo canais de suporte oficial.

## <a name="setting-the-target-framework"></a>Definir a estrutura de destino

Para alterar o tipo de estrutura de destino para um projeto Xamarin.Mac, faça o seguinte:

1. Abra o projeto Xamarin.Mac no Visual Studio para Mac.
2. No **Gerenciador de Soluções**, clique duas vezes no arquivo do projeto para abrir a caixa de diálogo **Opções de Projeto**.
3. Do **geral** , selecione o tipo de **Framework de destino** que atenda às necessidades de seu aplicativo:

  [![Usando a janela de opções de projeto para escolher uma estrutura de destino](target-framework-images/select-target-full.png "usando a janela de opções de projeto para escolher uma estrutura de destino")](target-framework-images/select-target-full-large.png#lightbox)

4. Clique no botão **OK** para salvar suas alterações.

Você deve **limpar** e **recriar** seu projeto Xamarin.Mac depois de alternar o tipo de estrutura de destino.

## <a name="summary"></a>Resumo

Este artigo abordou brevemente os diferentes tipos de estrutura de destino (bibliotecas de classes Base) disponível para um aplicativo Xamarin.Mac e cada tipo de estrutura deve ser usado.


## <a name="related-links"></a>Links relacionados

- [iOS e Mac compartilhamento de código](~/cross-platform/macios/index.md)
- [API Unificada](~/cross-platform/macios/unified/index.md)
- [Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)
- [Assemblies](~/cross-platform/internals/available-assemblies.md)
- [Atualizando aplicativos existentes do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
