---
title: Registrador Xamarin.Mac
description: Este documento descreve a finalidade do registrador de Xamarin.Mac e suas configurações de uso diferentes.
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 4b70ac2271b23b54e7942fdc870e0f49548e6154
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinmac-registrar"></a>Registrador Xamarin.Mac

_Este documento descreve a finalidade do registrador de Xamarin.Mac e suas configurações de uso diferentes._

## <a name="overview"></a>Visão geral

Xamarin.Mac preenche a lacuna entre o mundo gerenciado (.NET) e o tempo de execução do Cocoa, permitindo que as classes gerenciadas chamar classes não gerenciadas de Objective-C e ser chamado quando os eventos ocorrem. O trabalho necessário para realizar essa "mágica" é tratado pelo registrador e é, em geral, ocultos na exibição.

Há implicações de desempenho desse registro, especificamente na inicialização do aplicativo o tempo, e entender o que está acontecendo "nos bastidores" um pouco, às vezes, pode ser útil.

## <a name="configurations"></a>Configurações

Basicamente o trabalho do registrador na inicialização pode ser separado em duas categorias:

- Verificar todas as classes gerenciadas para aqueles derivando de NSObject e obter uma lista dos itens a serem expostas no tempo de execução Objective-C.
- Registre essas informações com o tempo de execução Objective-C.

Ao longo do tempo, foram criadas três configurações de registrador diferente para abranger os casos de uso diferentes. Cada um tem compilação diferente e consequências de tempo de execução:

- **Registrador dinâmico** – durante a inicialização, usar reflexão do .NET para examinar todos os tipos carregados, determinar a lista de itens relevantes e informar o tempo de execução nativo. Essa opção adiciona tempo zero para a compilação, mas é muito cara para computação durante inicialização (até vários segundos).
- **Registrador estático** – durante a compilação, o conjunto de itens a ser registrado e gerar código Objective-C para lidar com o registro de computação. Esse código é chamado durante a inicialização para registrar rapidamente todos os itens. Adiciona que uma pausa significativa para a compilação, mas pode recorta uma quantidade significativa de tempo de inicialização do aplicativo.
- **Estático "Parcial"** – uma abordagem "híbrida" mais recente que apresenta a maioria das vantagens de ambos. Desde que as exportações de **Xamarin.Mac.dll** são constantes, salve uma biblioteca pré-calculadas para lidar com o seu registro e vincular que no. Use reflexão para lidar com bibliotecas de usuário, mas como bibliotecas do usuário exportar tipos muito menos que as associações de plataforma isso geralmente é bastante rápido. Um neglectable impacto no tempo de compilação e reduz a grande maioria dos "custo" dinâmico.

Hoje estático parcial é o padrão para a configuração de depuração e estático é o padrão para configurações de versão.

Existem alguns cenários:

- Plug-ins são carregados após o lançamento com classes derivadas de NSObject
- Criado dinamicamente as instâncias da classe derivando de NSObject

onde o registrador é impossível saber que ele precisa registrar um tipo no início. O `ObjCRuntime.Runtime.RegisterAssembly` método é fornecido para informar o registrador que tem tipos adicionais a serem considerados.
