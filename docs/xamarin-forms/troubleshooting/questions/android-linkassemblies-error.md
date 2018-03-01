---
title: "Erro de compilação Android – LinkAssemblies a tarefa falhou inesperadamente"
ms.topic: article
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 667381e7f27f08f8b1d8b6c1979a7d0b26775177
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Erro de compilação Android – LinkAssemblies a tarefa falhou inesperadamente

Você verá uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` ao compilar um projeto do xamarin que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em um *versão* build para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos do Android não são atualizados para o framework mais recente. (Mais informações: [xamarin. Forms para Android requisitos](~/xamarin-forms/get-started/installation.md#android))

A resolução para esse problema é certificar-se de que você tem as versões de SDK do Android mais recentes com suporte e defina o **Framework de destino** para **plataforma de uso mais recente instalado**. Também é recomendável que você defina o **versão destino do Android** para **versão do Framework de destino Use** e **versão mínima do Android** para API 15 ou posterior. Isso é considerado a configuração com suporte.

## <a name="setting-in-visual-studio-for-mac"></a>Configuração no Visual Studio para Mac

1.  Clique com botão direito no projeto Android.
2.  Vá para **Build > Geral > do Framework de destino**.
3.  Definir o **Framework de destino: Use mais recente instalado plataforma**.
4.  Ainda nas opções de projeto, vá para **Build > aplicativo Android**.
5.  Definir o **versão do Android mínimo** para API nível 15 ou posterior & o **versão destino Android** para **automático - versão do framework de destino use**.

## <a name="setting-in-visual-studio"></a>Configuração no Visual Studio

1.  Clique com botão direito no projeto Android.
2.  Vá para **aplicativo** nas opções de projeto.
3.  Definir o **compilar usando a versão do Android** & **versão destino Android** configurações **plataforma mais recente de uso** / **uso Compilar com SDK versão**.
4.  Definir o **mínimo Android para o destino** definindo a API 15 ou posterior.

Depois de atualizar essas configurações, por favor, limpar e recompilar o projeto para garantir que as alterações são aplicadas.
