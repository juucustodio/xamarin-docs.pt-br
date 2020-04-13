---
title: Meu arquivo Resource.designer.cs do Android não será atualizado
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/19/2017
ms.openlocfilehash: 4683bbaa5aa48c7b5de5fb9a87a4cd3fbc0aeada
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019508"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Meu arquivo Resource.designer.cs do Android não será atualizado

> [!NOTE]
> Esse problema foi resolvido no Xamarin Studio 5.1.4 e nas versões posteriores. No entanto, se o problema ocorrer no Visual Studio para Mac, por favor, arquive um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações de versão completa e saída completa do registro de compilação.

Um bug no Xamarin.Studio 5.1 corrompeu anteriormente arquivos .csproj, excluindo parcial ou completamente o código xml no arquivo .csproj. Isso faria com que partes importantes do sistema de compilação do Android (como atualizar o Android Resource.designer.cs) falhassem. A partir da versão estável de 5.1.4 em 15 de julho, este bug foi corrigido; mas, em muitos casos, o arquivo do projeto tem que ser reparado manualmente, como descrito abaixo.

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Duas abordagens possíveis para corrigir o arquivo do projeto

**Ou:**

1. Crie um novo projeto de aplicativo Xamarin.Android, defina todas as propriedades do projeto para combinar com seu projeto antigo e adicione todos os seus recursos, arquivos de origem, etc. de volta ao projeto.

   **OU**

2. Faça uma cópia de backup do arquivo .csproj do projeto original e abra-o em um editor de texto e adicione de volta os elementos faltantes de um arquivo .csproj gerado de forma limpa.

### <a name="if-this-does-not-solve-the-problem"></a>Se isso não resolver o problema

Depois de experimentar esses elementos, você pode notar que depois de adicionar de volta os elementos e reconstruir o projeto, o arquivo Resource.designer.cs seria atualizado, mas então você ainda pode ter que fechar e reabrir a solução para obter a conclusão do código para reconhecer os novos tipos contidos em Resource.designer.cs. 
