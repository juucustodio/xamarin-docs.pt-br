---
title: "Meu arquivo Resource.designer.cs Android não serão atualizadas."
ms.topic: article
ms.prod: xamarin
ms.assetid: 3F7376E3-59CC-4722-AEED-BB50E4D952AA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/19/2017
ms.openlocfilehash: 1b1496f4a6a504c8e991f853c92f937015797aa6
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="my-android-resourcedesignercs-file-will-not-update"></a>Meu arquivo Resource.designer.cs Android não serão atualizadas.

> [!NOTE]
> Esse problema foi resolvido no Xamarin Studio 5.1.4 e versões posteriores. No entanto, se o problema ocorrer no Visual Studio para Mac, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.

Um bug no Xamarin.Studio 5.1. csproj arquivos corrompidos anteriormente excluindo parcialmente ou totalmente o código xml no arquivo. csproj. Isso causaria um importante de sistema (como atualizar o Android Resource.designer.cs) de compilação de partes do Android que a falha. A partir de 5.1.4 estável de versão no dia 15 de julho, esse bug foi corrigido; mas, em muitos casos o arquivo de projeto deve ser reparado manualmente, conforme descrito abaixo.


## <a name="two-possible-approaches-to-fixing-up-the-project-file"></a>Duas abordagens para corrigir o arquivo de projeto

### <a name="either"></a>Possíveis causas:

1) Criar um novo projeto de aplicativo xamarin, defina todas as propriedades de projeto para coincidir com seu projeto antigo e adicionar todos os seus recursos, arquivos de origem, etc. de volta para o projeto.

### <a name="or"></a>OU

2) Faça uma cópia de backup do arquivo. csproj do seu projeto original, abri-lo em um editor de texto e adicione-os em elementos ausentes de um arquivo. csproj gerado corretamente.

### <a name="if-this-does-not-solve-the-problem"></a>Se isso não resolver o problema

Depois de testar esses elementos, você pode perceber que depois de adicionar novamente os elementos e recompilar o projeto, o arquivo Resource.designer.cs atualizaria, mas, em seguida, você ainda terá que fechar e reabrir a solução para obter a conclusão de código para reconhecer os novos tipos contidos em Resource.designer.cs. 
