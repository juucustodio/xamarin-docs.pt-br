---
title: Meu arquivo Resource.designer.cs do Android não será atualizado
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/19/2017
ms.openlocfilehash: 65f7c6d8a573501ffec4aa1b8eaf28ff1e6479e8
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864228"
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Meu arquivo Resource.designer.cs do Android não será atualizado

> [!NOTE]
> Esse problema foi resolvido no Xamarin Studio 5.1.4 e versões posteriores. No entanto, se o problema ocorre no Visual Studio para Mac, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.

Um bug no xamarin. Studio 5.1 anteriormente corrompido arquivos. csproj, parcial ou completamente, excluindo o código xml no arquivo. csproj. Isso faria com que importantes de sistema (como atualizar o Resource.designer.cs do Android) de compilação de partes do Android falhe. A partir de 5.1.4 estável de versão no dia 15 de julho, esse bug foi corrigido; mas, em muitos casos o arquivo de projeto deve ser reparado manualmente, conforme descrito abaixo.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Duas abordagens possíveis para corrigir o arquivo de projeto

**Ambos:**

1. Criar um novo projeto de aplicativo do xamarin. Android, defina todas as propriedades do projeto para corresponder ao seu projeto antigo e adicionar todos os seus recursos, arquivos de origem, etc. de volta para o projeto.

   **OU**

2. Faça uma cópia de backup do arquivo. csproj do projeto original, abri-lo em um editor de texto e adicione-os em elementos ausentes de um arquivo. csproj gerado corretamente.

### <a name="if-this-does-not-solve-the-problem"></a>Se isso não resolver o problema

Depois de testar esses elementos, você pode perceber que depois de adicionar novamente os elementos e recompilar o projeto, o arquivo Resource.designer.cs atualizaria, mas, em seguida, você ainda terá que fechar e reabrir a solução para obter a conclusão de código para reconhecer os novos tipos contidos em Resource.designer.cs. 
