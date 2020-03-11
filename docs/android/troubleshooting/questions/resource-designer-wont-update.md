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
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019508"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Meu arquivo Resource.designer.cs do Android não será atualizado

> [!NOTE]
> Esse problema foi resolvido no Xamarin Studio 5.1.4 e em versões posteriores. No entanto, se o problema ocorrer em Visual Studio para Mac, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

Um bug no Xamarin. Studio 5,1 arquivo. csproj corrompido anteriormente, excluindo parcialmente ou completamente o código XML no arquivo. csproj. Isso faria com que partes importantes do sistema de compilação do Android (como atualizar o Android Resource.designer.cs) falhem. A partir da versão estável do 5.1.4 em 15 de julho, esse bug foi corrigido; Mas, em muitos casos, o arquivo de projeto precisa ser reparado manualmente, conforme descrito abaixo.

## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Duas abordagens possíveis para corrigir o arquivo de projeto

**O**

1. Crie um novo projeto de aplicativo Xamarin. Android, defina todas as propriedades do projeto para corresponder ao projeto antigo e adicione todos os seus recursos, arquivos de origem, etc. de volta ao projeto.

   **OR**

2. Faça uma cópia de backup do arquivo. csproj do projeto original, abra-o em um editor de texto e adicione de volta os elementos ausentes de um arquivo. csproj gerado de forma limpa.

### <a name="if-this-does-not-solve-the-problem"></a>Se isso não resolver o problema

Depois de experimentar esses elementos, você pode observar que, depois de adicionar novamente os elementos e recompilar o projeto, o arquivo Resource.designer.cs seria atualizado, mas, em seguida, talvez você ainda precise fechar e reabrir a solução para obter a conclusão do código para reconhecê-la os novos tipos contidos em Resource.designer.cs. 
