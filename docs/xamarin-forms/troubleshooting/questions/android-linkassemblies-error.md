---
title: Erro de build do Android – tarefa o LinkAssemblies falhou inesperadamente
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EB3BE685-CB72-48E3-89D7-C845E76B9FA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: f517aaa770fa7b2f1463954638f0afc95168bf65
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250732"
---
# <a name="android-build-error--the-linkassemblies-task-failed-unexpectedly"></a>Erro de build do Android – tarefa o LinkAssemblies falhou inesperadamente

Você poderá ver uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` quando a criação de um projeto xamarin. Android que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em um *versão* build para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos Android não são atualizados para o framework mais recente. (Mais informações: [Xamarin. Forms para Android requisitos](~/get-started/requirements.md#android))

A resolução para esse problema é certificar-se de ter as versões do SDK do Android mais recente com suporte e defina as **estrutura de destino** para a plataforma mais recente instalada. Também é recomendável que você defina as **versão destino Android** para a plataforma instalada mais recente e o **versão mínima do Android** para API 19 ou superior. Isso é considerado a configuração com suporte.

## <a name="setting-in-visual-studio-for-mac"></a>Configuração no Visual Studio para Mac

1.  Clique com o botão direito no projeto do Android e selecione **opções** no menu.
2.  No **opções de projeto** caixa de diálogo, vá para **Build > geral**.
3.  Defina o **compilar usando a versão do Android: (Estrutura de destino)**  para a plataforma mais recente instalada.
4.  No **opções de projeto** caixa de diálogo, vá para **compilar > aplicativo Android**.
5.  Defina a **versão mínima do Android** à API nível 19 ou superior e o **versão destino Android** a mais recente instalada plataforma escolhida (3).

## <a name="setting-in-visual-studio"></a>Configuração no Visual Studio

1.  Clique com o botão direito no projeto do Android e selecione **dimensões** no menu.
2.  Nas propriedades do projeto, vá para **aplicativo**.
3.  Defina o **compilar usando a versão do Android: (Estrutura de destino)**  para a plataforma mais recente instalada.
4.  Nas propriedades do projeto, vá para **manifesto do Android**.
5.  Defina a **versão mínima do Android** à API nível 19 ou superior e o **versão destino Android** a mais recente instalada plataforma escolhida (3).

Depois de atualizar essas configurações,. Limpe e recompile o projeto para garantir que as alterações são escolhidas.
