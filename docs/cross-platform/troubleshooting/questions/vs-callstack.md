---
title: Como posso coletar as pilhas de chamadas atual do processo do Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: e81c28f0610a0df2e4fe06349685ef5e0744071a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919755"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Como posso coletar as pilhas de chamadas atual do processo do Visual Studio?

Quando a GUI trava congela (trava) no Visual Studio, uma parte importante das informações de diagnósticas para coletar é o conjunto de pilhas de chamadas de todos os threads de processo do Visual Studio. Para salvar essas informações para uma instância de travamento do Visual Studio, você pode usar uma segunda instância do Visual Studio:

1. Inicie uma segunda instância (uma nova janela) do Visual Studio.

2. Feche quaisquer soluções abertas na nova instância do Visual Studio.

3. Selecione **Depurar > Anexar ao processo**.

  ![](vs-callstack-images/image1.png "Selecione Depurar > Anexar ao processo")

4. Selecione a instância original travada de `devenv.exe` da lista de **processos Disponíveis**.

5. Selecione **Depurar > Interromper Tudo**.

  ![](vs-callstack-images/image2.png "Selecione Depurar > interromper tudo")

6. Selecione **Depurar > Salvar despejo como**.

  ![](vs-callstack-images/image3.png "Selecione Depurar > Salvar despejo como")

7. Alterar **Salvar como tipo** para **minidespejo (\*. dmp)**. Isso produzirá um arquivo menor que **minidespejo com Heap**, e o heap geralmente não é relevante para diagnosticar congelar.

  ![](vs-callstack-images/image4.png "Isso produzirá um arquivo menor que minidespejo com Heap e o heap geralmente não é relevante para diagnosticar congela")

8. Salve o arquivo de despejo de memória. Se o envio de arquivo online, você pode zip para reduzir o tamanho.
