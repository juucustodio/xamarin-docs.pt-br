---
title: ''
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c0403203345ed78fae9f30c623c3e501f24ea0e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135896"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Erro de compilação do Android – a tarefa LinkAssemblies falhou inesperadamente

Você poderá ver uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` ao criar um projeto Xamarin. Android que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em uma compilação de *versão* para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos do Android não são atualizados para a estrutura mais recente. (Mais informações: [ Xamarin.Forms plataformas com suporte](~/get-started/supported-platforms.md#android-platform-support))

A resolução para esse problema é verificar se você tem as versões de SDK do Android mais recentes com suporte e definir a **estrutura de destino** para a plataforma instalada mais recente. Também é recomendável que você defina a **versão de destino do Android** para a plataforma instalada mais recente e a **versão mínima do Android** para API 19 ou superior. Isso é considerado a configuração com suporte.

## <a name="setting-in-visual-studio-for-mac"></a>Configuração em Visual Studio para Mac

1. Clique com o botão direito do mouse no projeto do Android e selecione **Opções** no menu.
2. Na caixa de diálogo **Opções do projeto** , vá para **Build > geral**.
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
