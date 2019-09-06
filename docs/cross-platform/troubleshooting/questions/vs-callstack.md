---
title: Como posso coletar as pilhas de chamadas atuais do processo do Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: f07bc4437293bdc49e4811c0104fd7245ccf9738
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282949"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Como posso coletar as pilhas de chamadas atuais do processo do Visual Studio?

Quando a GUI é bloqueada (trava, congela) no Visual Studio, uma parte importante das informações de diagnóstico a serem coletadas é o conjunto de pilhas de chamadas de todos os threads do processo do Visual Studio. Para salvar essas informações para uma instância suspensa do Visual Studio, você pode usar uma segunda instância do Visual Studio:

1. Inicie uma segunda instância (uma nova janela) do Visual Studio.

2. Feche as soluções abertas na nova instância do Visual Studio.

3. Selecione **Depurar > Anexar ao processo**.

   ![](vs-callstack-images/image1.png "Selecione Depurar > anexar ao processo")

4. Selecione a instância original travada de `devenv.exe` da lista de **processos Disponíveis**.

5. Selecione **Depurar > Interromper Tudo**.

   ![](vs-callstack-images/image2.png "Selecione Depurar > interromper tudo")

6. Selecione **depurar > Salvar despejo como**.

   ![](vs-callstack-images/image3.png "Selecione Depurar > Salvar despejo como")

7. Altere **salvar como tipo** para **minidespejo (\*. dmp)** . Isso produzirá um arquivo muito menor do que o **minidespejo com heap**, e o heap geralmente não é relevante para o diagnóstico de congelamento.

   ![](vs-callstack-images/image4.png "Isso produzirá um arquivo muito menor do que o minidespejo com heap e o heap geralmente não é relevante para diagnosticar congelamento")

8. Salve o arquivo de despejo. Se estiver enviando o arquivo online, você poderá recompactá-lo para reduzir o tamanho.
