---
title: Como posso coletar as pilhas de chamadas atuais do processo do Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 64c24b09-2c4a-43ad-b94d-6cd05a1aee44
author: asb3993
ms.author: amburns
ms.date: 03/30/2017
ms.openlocfilehash: e81c28f0610a0df2e4fe06349685ef5e0744071a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159097"
---
# <a name="how-do-i-collect-the-current-call-stacks-of-the-visual-studio-process"></a>Como posso coletar as pilhas de chamadas atuais do processo do Visual Studio?

Quando a GUI é bloqueada (trava, congela) no Visual Studio, uma parte importante de informações de diagnóstico para coletar é o conjunto de pilhas de chamadas de todos os threads de processo do Visual Studio. Para salvar essas informações para uma instância travada do Visual Studio, você pode usar uma segunda instância do Visual Studio:

1. Inicie uma segunda instância (uma nova janela) do Visual Studio.

2. Feche soluções abertas na nova instância do Visual Studio.

3. Selecione **Depurar > Anexar ao processo**.

  ![](vs-callstack-images/image1.png "Selecione Depurar > Anexar ao processo")

4. Selecione a instância original travada de `devenv.exe` da lista de **processos Disponíveis**.

5. Selecione **Depurar > Interromper Tudo**.

  ![](vs-callstack-images/image2.png "Selecione Depurar > interromper tudo")

6. Selecione **Depurar > Salvar despejo como**.

  ![](vs-callstack-images/image3.png "Selecione Depurar > Salvar despejo como")

7. Alteração **Salvar como tipo** à **minidespejo (\*. dmp)**. Isso produzirá um arquivo menor que **minidespejo com Heap**, e o heap geralmente não é relevante para diagnosticar congela.

  ![](vs-callstack-images/image4.png "Isso produzirá um arquivo menor que o minidespejo com Heap e o heap geralmente não é relevante para diagnosticar congela")

8. Salve o arquivo de despejo. Se enviar o arquivo online, você pode zip-lo para reduzir o tamanho.
