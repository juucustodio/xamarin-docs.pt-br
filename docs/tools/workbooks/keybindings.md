---
title: Atalhos de teclado do Editor de pastas de trabalho do Xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 65424f8c213c17b58f0ce1ad4acc9f6dcdaa192c
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Atalhos de teclado do Editor de pastas de trabalho do Xamarin

## <a name="the-return-key-and-its-nuances"></a>A chave de retorno e seus nuances

A tabela a seguir descreve as várias associações de chave para a execução de código e redução de criação. Levamos muito cuidado ao escolher a sensatas e consistentes associações de chave que são familiares e fluido.

|Chave de associação|Célula de código|Redução de célula|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Se o cursor estiver no final da célula buffer e a célula podem ser analisados com êxito, ele será executado e os resultados serão exibidos abaixo do buffer e uma nova célula de código será inserida e voltada para a célula após a célula executada.</p><p>Se a análise não for bem-sucedida, uma nova linha será inserida no buffer. Diagnóstico do compilador não será produzido se a análise não for bem-sucedida.</p>|<p><kbd>Retornar</kbd> apresenta um comportamento diferente dependendo do contexto de Markdown no cursor.</p><ul><li>Se o cursor estiver em um bloco de código de Markdown, uma nova linha literal é inserida.</li><li>Se o cursor estiver em um bloco de lista de Markdown, criar um novo item de lista ou dividir o item atual da lista.</li><li>Se o cursor estiver em qualquer outro tipo de bloco de Markdown, crie um novo bloco de parágrafo ou dividir o bloco atual.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑Return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>Sempre tenta analisar e executar o conteúdo da célula. Se a compilação for bem-sucedida, os resultados (incluindo exceções de execução) serão exibidos abaixo do buffer, e se não houver nenhuma célula subsequente, um novo será criado e foco.</p><p>Se não houver nenhum erro de compilação, diagnóstico será exibido e o buffer será concentre-se com a posição do cursor inalterada.</p>|Insere e concentra-se uma nova célula código após a célula atual de redução.|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑Return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑Return</kbd></dd></dl>|Insere e concentra-se uma nova célula markdown após a célula atual.|Mesmo comportamento <kbd>retornar</kbd>|
|<kbd>Shift‑Return</kbd>|Sempre insere uma nova linha, independentemente do local do cursor ou conteúdo.|Insere uma quebra de linha definida dentro do bloco de Markdown atual.|
