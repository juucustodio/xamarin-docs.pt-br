---
title: Registrador de xamarin. Mac
description: Este documento descreve a finalidade do registrador de xamarin. Mac e seu static parcial, estático e dinâmico (híbrido) configurações de uso.
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 21e1a2c6ae5a9ad8b6acf520851ea92a340da887
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032491"
---
# <a name="xamarinmac-registrar"></a>Registrador de xamarin. Mac

_Este documento descreve a finalidade do registrador de xamarin. Mac e suas configurações de uso diferentes._

## <a name="overview"></a>Visão geral

Xamarin. Mac preenche a lacuna entre o mundo gerenciado (.NET) e o tempo de execução do Cocoa, permitindo que as classes gerenciadas chamar a classes não gerenciadas de Objective-C e ser chamado novamente quando ocorrem eventos. O trabalho necessário para realizar essa "mágica" é tratado pelo registrador de e está, em geral, oculta da exibição.

Há implicações de desempenho do registro, especificamente na inicialização do aplicativo, o tempo e compreender um pouco do que está acontecendo "nos bastidores" às vezes, pode ser útil.

## <a name="configurations"></a>Configurações

Fundamentalmente, trabalho do registrador durante a inicialização pode ser separado em duas categorias:

- Verificar todas as classes gerenciadas para aqueles derivando de NSObject e coletar uma lista de itens a serem expostos ao tempo de execução Objective-C.
- Registre essas informações com o tempo de execução do Objective-C.

Ao longo do tempo, três configurações diferentes de registrador foram criadas para abranger os casos de uso diferentes. Cada um tem compilação diferente e as consequências de tempo de execução:

- **Registrador dinâmico** – durante a inicialização, usar a reflexão do .NET para examinar todos os tipos carregados, determinar a lista de itens relevantes e informar o tempo de execução nativo. Essa opção adiciona o tempo de zero para a compilação, mas é muito cara de computar durante a inicialização (até vários segundos).
- **Registrador estático** – durante a compilação, o conjunto de itens a ser registrado e gerar código Objective-C para lidar com o registro de computação. Esse código é invocado durante a inicialização para registrar rapidamente todos os itens. Adiciona que uma pausa significativa para compilação, mas pode recorta uma quantidade significativa de tempo de inicialização do aplicativo.
- **Estáticos "Parcial"** – uma abordagem de "híbrida" mais recente que leva a maioria das vantagens de ambos. Desde as exportações sejam **xamarin** são constantes, salve uma biblioteca pré-computadas para lidar com seu registro e vinculá-lo no. Use reflexão para lidar com bibliotecas de usuário, mas como bibliotecas de usuário exportar muito menos tipos que as associações de plataforma isso geralmente é bastante rápido. Um neglectable impacto no tempo de compilação e reduz a vasta maioria das "custo" dinâmico.

Hoje em dia estático parcial é o padrão para configuração de depuração e estático é o padrão para as configurações de versão.

Existem alguns cenários:

- Plug-ins carregados após a inicialização com classes derivadas de NSObject
- Criado dinamicamente instâncias de classe derivado de NSObject

onde o registrador é impossível saber que ele precisa registrar algum tipo de início. O `ObjCRuntime.Runtime.RegisterAssembly` método é fornecido para informar o registrador que ele tenha tipos adicionais a serem considerados.
