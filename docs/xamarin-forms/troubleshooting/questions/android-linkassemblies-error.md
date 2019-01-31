---
title: Erro de build do Android – tarefa o LinkAssemblies falhou inesperadamente
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: b027dd23b9144a865bc16b55ebac71855bae0725
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292032"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Erro de build do Android – tarefa o LinkAssemblies falhou inesperadamente

Você poderá ver uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` quando a criação de um projeto xamarin. Android que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em um *versão* build para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos Android não são atualizados para o framework mais recente. (Mais informações: [Xamarin. Forms para Android requisitos](~/get-started/installation.md#android))

A resolução para esse problema é certificar-se de ter as versões do SDK do Android mais recente com suporte e defina as **estrutura de destino** à **usar a plataforma mais recente instalada**. Também é recomendável que você defina as **versão destino Android** para **Use a versão do Target Framework** e o **versão mínima do Android** para API 15 ou superior. Isso é considerado a configuração com suporte.

## <a name="setting-in-visual-studio-for-mac"></a>Configuração no Visual Studio para Mac

1.  Clique com botão direito no projeto do Android.
2.  Vá para **Build > Geral > estrutura de destino**.
3.  Defina o **do Framework de destino: Usar a plataforma mais recente instalada**.
4.  Ainda em Opções de projeto, vá para **compilar > aplicativo Android**.
5.  Defina a **versão mínima do Android** à API nível 15 ou superior & o **versão destino do Android** para **automático - use a versão do target framework**.

## <a name="setting-in-visual-studio"></a>Configuração no Visual Studio

1.  Clique com botão direito no projeto do Android.
2.  Vá para **aplicativo** nas opções do projeto.
3.  Defina a **compilar usando a versão do Android** & **versão destino Android** configurações a serem **usar a plataforma mais recente** / **uso Compilar usando a versão do SDK**.
4.  Defina as **Android mínimo para o destino** definindo a API 19 ou superior.

Depois de atualizar essas configurações,. Limpe e recompile o projeto para garantir que as alterações são escolhidas.
