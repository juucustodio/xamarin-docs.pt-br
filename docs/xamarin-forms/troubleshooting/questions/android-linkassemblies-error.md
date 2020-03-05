---
title: Erro de compilação do Android – a tarefa LinkAssemblies falhou inesperadamente
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: a90c56102f77e3f64d9333eec03b025d24888977
ms.sourcegitcommit: a3b7e016fb25584dbf57bae89b64a9f98031e7c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "78291802"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Erro de compilação do Android – a tarefa LinkAssemblies falhou inesperadamente

Você pode ver uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` ao criar um projeto Xamarin. Android que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em uma compilação de *versão* para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos do Android não são atualizados para a estrutura mais recente. (Mais informações: [plataformas com suporte para Xamarin. Forms](~/get-started/supported-platforms.md#android-platform-support))

A resolução para esse problema é verificar se você tem as versões de SDK do Android mais recentes com suporte e definir a **estrutura de destino** para a plataforma instalada mais recente. Também é recomendável que você defina a **versão de destino do Android** para a plataforma instalada mais recente e a **versão mínima do Android** para API 19 ou superior. Isso é considerado a configuração com suporte.

## <a name="setting-in-visual-studio-for-mac"></a>Configuração em Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do Android e selecione **Opções** no menu.
2. Na caixa de diálogo **Opções do projeto** , vá para **Build > Geral**.
3. Defina a **compilação usando a versão do Android: (estrutura de destino)** para a plataforma instalada mais recente.
4. Na caixa de diálogo **Opções do projeto** , vá para **Compilar > aplicativo Android**.
5. Defina a **versão mínima do Android** para o nível de API 19 ou superior e a **versão do Android de destino** para a plataforma instalada mais recente que você escolheu em (3).

## <a name="setting-in-visual-studio"></a>Configurando no Visual Studio

1. Clique com o botão direito do mouse no projeto do Android e selecione as **Propriedades** no menu.
2. Nas propriedades do projeto, vá para **aplicativo**.
3. Defina a **compilação usando a versão do Android: (estrutura de destino)** para a plataforma instalada mais recente.
4. Nas propriedades do projeto, vá para o **manifesto do Android**.
5. Defina a **versão mínima do Android** para o nível de API 19 ou superior e a **versão do Android de destino** para a plataforma instalada mais recente que você escolheu em (3).

Depois de atualizar essas configurações, limpe e reconstrua seu projeto para garantir que suas alterações sejam selecionadas.
