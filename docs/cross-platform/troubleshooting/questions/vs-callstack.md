---
title: Como posso coletar as pilhas de chamadas atuais do processo do Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 9ed79b2273758b8051a96169d4c9b53870de1fb1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013033"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Como posso coletar as pilhas de chamadas atuais do processo do Visual Studio?

Quando a GUI é bloqueada (trava, congela) no Visual Studio, uma parte importante das informações de diagnóstico a serem coletadas é o conjunto de pilhas de chamadas de todos os threads do processo do Visual Studio. Para salvar essas informações para uma instância suspensa do Visual Studio, você pode usar uma segunda instância do Visual Studio:

1. Inicie uma segunda instância (uma nova janela) do Visual Studio.

2. Feche as soluções abertas na nova instância do Visual Studio.

3. Selecione **Depurar > Anexar ao processo**.

   ![](vs-callstack-images/image1.png "Select Debug > Attach to Process")

4. Selecione a instância original travada de `devenv.exe` da lista de **processos Disponíveis**.

5. Selecione **Depurar > Interromper Tudo**.

   ![](vs-callstack-images/image2.png "Select Debug > Break All")

6. Selecione **depurar > Salvar despejo como**.

   ![](vs-callstack-images/image3.png "Select Debug > Save Dump As")

7. Altere **salvar como tipo** para **minidespejo (\*. dmp)** . Isso produzirá um arquivo muito menor do que o **minidespejo com heap**, e o heap geralmente não é relevante para o diagnóstico de congelamento.

   ![](vs-callstack-images/image4.png "This will produce a much smaller file than Minidump with Heap, and the heap is usually not relevant for diagnosing freezes")

8. Salve o arquivo de despejo. Se estiver enviando o arquivo online, você poderá recompactá-lo para reduzir o tamanho.
