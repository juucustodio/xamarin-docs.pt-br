---
title: Estrutura de destino para xamarin. Mac
description: Este artigo aborda as estruturas de destino (Base Class Libraries) disponíveis para xamarin. Mac e as implicações de usá-los em seu projeto xamarin. Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 15c93126f80917df45a5b80fb84397dc6ef0d5fd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075608"
---
# <a name="target-framework-for-xamarinmac"></a>Estrutura de destino para xamarin. Mac

_Este artigo aborda as estruturas de destino (Base Class Libraries) disponíveis para xamarin. Mac e as implicações de usá-los em seu projeto xamarin. Mac._

![Opções de estrutura para xamarin. MAC de destino](target-framework-images/select-target.png "opções de estrutura para xamarin. MAC de destino")

## <a name="background"></a>Informações preliminares

Cada programa .NET ou a biblioteca depende da funcionalidade fornecida pela classe biblioteca BCL (Base). Este BCL inclui assemblies como mscorlib, System, System.NET. HTTP e System. XML que fornecem a funcionalidade comum incorporada a todas as linguagens .NET.

Ao longo dos anos, ter desenvolvido várias versões diferentes desse BCL, otimizados para diferentes casos de uso. A BCL "desktop" inclui um conjunto mais rico de bibliotecas que podem ser muito pesados para outros casos de uso, enquanto mobile se concentra em assegurar que as APIs são seguras para a vinculação, que remove o código não utilizado para reduzir a superfície do aplicativo.

Um das repercussões mais importantes dessas estruturas de destino diferente, é que todos os assemblies em um determinado programa *deve* assemblies BCL compatíveis de destino. Se isso não era o caso, você poderia ter dois assemblies vinculados em relação a versões diferentes do **System. dll** disagreeing sobre a assinatura de um determinado tipo. Uma biblioteca compartilhada pode qualquer destino [.NET Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), que é o subconjunto comum de estruturas de destino ou uma estrutura de destino específico.

Há três opções de estrutura de destino disponível para xamarin. Mac, cada um com diferentes vantagens e compensações:

- **Modernos** (chamado Mobile na documentação mais antiga) – um subconjunto muito semelhante a quais poderes xamarin. IOS, altamente ajustadas para desempenho e tamanho. Essa estrutura de destino é vinculador seguro, portanto, esses projetos podem ter sua superfície final drasticamente reduzida, removendo código não utilizado.

- **Completo** (chamado XM 4.5 em documentação mais antiga) – um subconjunto muito semelhante à BCL "desktop", com alguns remoções pequeno. Como a estrutura de destino é quase idêntica ao TargetFramework="net45 (e posterior), pode facilmente consumir nugets muitos que não oferecem qualquer netstandard2 ou compilações do xamarin. MAC específico. No entanto, devido ao uso de System. Configuration é incompatível com a vinculação.

- **Não há suporte para** (chamado de sistema na documentação mais antiga) – em vez disso, de vincular a um BCL fornecido pelo xamarin. Mac, use o mono atual do sistema instalado. Isso fornece ao conjunto completo de assemblies, incluindo alguns reconhecidamente problemáticos (System. Drawing por exemplo). Essa opção só existe tem um "último recurso" e é altamente recomendável para esgotar as outras opções antes de usá-lo. Como o nome implica, uso não tem suporte pelos canais de suporte oficial.

## <a name="setting-the-target-framework"></a>Definindo a estrutura de destino

Para alterar o tipo de estrutura de destino para um projeto xamarin. Mac, faça o seguinte:

1. Abra o projeto Xamarin.Mac no Visual Studio para Mac.
2. No **Gerenciador de Soluções**, clique duas vezes no arquivo do projeto para abrir a caixa de diálogo **Opções de Projeto**.
3. Dos **gerais** , selecione o tipo de **estrutura de destino** que atenda às necessidades do seu aplicativo:

  [![Usando a janela de opções de projeto para escolher uma estrutura de destino](target-framework-images/select-target-full.png "usando a janela de opções de projeto para escolher uma estrutura de destino")](target-framework-images/select-target-full-large.png#lightbox)

4. Clique no botão **OK** para salvar suas alterações.

Você deve **Clean** e, em seguida **recompilar** seu projeto xamarin. MAC Depois de alternar o tipo de estrutura de destino.

## <a name="summary"></a>Resumo

Este artigo cobriu brevemente os diferentes tipos de estruturas de destino (Base Class Libraries) disponível para um aplicativo xamarin. Mac e quando cada tipo de estrutura deve ser usado.


## <a name="related-links"></a>Links relacionados

- [compartilhamento de código de Mac e iOS](~/cross-platform/macios/index.md)
- [API Unificada](~/cross-platform/macios/unified/index.md)
- [Bibliotecas de classes portáteis](~/cross-platform/app-fundamentals/pcl.md)
- [Assemblies](~/cross-platform/internals/available-assemblies.md)
- [Atualizando aplicativos existentes do Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
