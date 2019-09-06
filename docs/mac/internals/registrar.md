---
title: Registrador do Xamarin. Mac
description: Este documento descreve a finalidade do registrador Xamarin. Mac e suas configurações de uso estático dinâmico, estático e parcial (híbrido).
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: d44f445b0c3bcc6fd498372f6cdf3e20be39d5b5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290092"
---
# <a name="xamarinmac-registrar"></a>Registrador do Xamarin. Mac

_Este documento descreve a finalidade do registrador Xamarin. Mac e suas configurações de uso diferentes._

## <a name="overview"></a>Visão geral

O Xamarin. Mac preenche a lacuna entre o mundo gerenciado (.NET) e o tempo de execução do Cocoa, permitindo que classes gerenciadas chamem classes Objective-C não gerenciadas e sejam chamadas de volta quando ocorrerem eventos. O trabalho necessário para realizar essa "mágica" é tratado pelo registrador e, em geral, oculto da exibição.

Há implicações de desempenho desse registro, especificamente sobre o tempo de inicialização do aplicativo, e a compreensão de um pouco do que acontece "nos bastidores" às vezes pode ser útil.

## <a name="configurations"></a>Configurações

Fundamentalmente, o trabalho do registrador na inicialização pode ser separado em dois categorias:

- Verifique cada classe gerenciada para as derivações de NSObject e colete uma lista de itens a serem expostos para o tempo de execução Objective-C.
- Registre essas informações com o tempo de execução Objective-C.

Com o tempo, três configurações de registrador diferentes foram criadas para cobrir diferentes casos de uso. Cada um tem consequências de tempo de execução e de compilação diferentes:

- **Registrador dinâmico** – durante a inicialização, use a reflexão do .net para verificar cada tipo carregado, determinar a lista de itens relevantes e informar o tempo de execução nativo. Essa opção adiciona um tempo zero para a compilação, mas é muito cara para computar durante a inicialização (até vários segundos).
- **Registrador estático** – durante a compilação, COMPUTE o conjunto de itens a serem registrados e gere o código Objective-C para lidar com o registro. Esse código é invocado durante a inicialização para registrar rapidamente todos os itens. Adiciona uma pausa significativa para compilar, mas pode cortar uma quantidade significativa de tempo desde o início do aplicativo.
- **"Parcial" estática** – uma abordagem "híbrida" mais recente que traz a maioria das vantagens de ambos. Como as exportações de **Xamarin. Mac. dll** são constantes, salve uma biblioteca em computação para lidar com seu registro e vincule-a. Use a reflexão para lidar com as bibliotecas de usuários, mas como as bibliotecas de usuários exportam muito menos tipos que a plataforma vincula a isso é, muitas vezes, rápida. Um impacto inativo no tempo de compilação e reduz a grande parte do "custo" do dinâmico.

Hoje, o estático parcial é o padrão para configuração de depuração e estático é o padrão para as configurações de versão.

Há alguns cenários:

- Plug-ins carregados após a inicialização com classes derivadas de NSObject
- Instâncias de classe criadas dinamicamente derivando de NSObject

onde o registrador não consegue saber que precisa registrar algum tipo no início. O `ObjCRuntime.Runtime.RegisterAssembly` método é fornecido para informar ao registrador que ele tem tipos adicionais a serem considerados.
