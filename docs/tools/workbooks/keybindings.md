---
title: Atalhos de teclado do Editor de pastas de trabalho de Xamarin
description: Este documento descreve os atalhos de teclado disponíveis para uso no editor de pastas de trabalho do Xamarin. Em particular, ele examina a tecla Return é usada de várias maneiras.
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 87af9f824117b20250c02a3e070652607626de44
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341044"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Atalhos de teclado do Editor de pastas de trabalho de Xamarin

## <a name="the-return-key-and-its-nuances"></a>A chave de retorno e seus nuances

A tabela a seguir descreve as várias associações de teclas para executar o código e a criação de markdown. Tomamos muito cuidado ao escolher as associações de teclas adequadas e consistentes que são familiares e fluida.

|Associação de chave|Célula de código|Célula de markdown|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Se o cursor estiver no final da célula de buffer e a célula podem ser analisadas com êxito, ele será executado e resultados serão exibidos abaixo do buffer e uma nova célula de código será inserida e voltada para a célula após a célula executada.</p><p>Se a análise não for bem-sucedida, uma nova linha será inserida no buffer. Diagnóstico do compilador não será produzido se a análise não for bem-sucedida.</p>|<p><kbd>Retornar</kbd> exibe um comportamento diferente dependendo do contexto Markdown na posição do cursor.</p><ul><li>Se o cursor estiver em um bloco de código de Markdown, uma nova linha literal é inserida.</li><li>Se o cursor estiver em um bloco de lista de Markdown, crie um novo item de lista ou dividir o item de lista atual.</li><li>Se o cursor estiver em qualquer outro tipo de bloco de Markdown, crie um novo bloco de parágrafo ou dividir o bloco atual.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Command‑Return</kbd></dd><dt>Win</dt><dd><kbd>Control‑Return</kbd></dd></dl>|<p>Sempre tenta analisar e executar o conteúdo da célula. Se a compilação for bem-sucedida, os resultados (incluindo as exceções de execução) serão exibidos abaixo do buffer, e se não houver nenhuma célula subsequente, um novo será criado e com foco.</p><p>Se houver quaisquer erros de compilação, o diagnóstico será exibido e o buffer permanecerá com o objetivo com a posição do cursor inalterada.</p>|Insere e concentra-se uma nova célula de código após a célula atual de markdown.|
|<dl><dt>Mac</dt><dd><kbd>Command‑Shift‑Return</kbd><dd><dt>Win</dt><dd><kbd>Control‑Shift‑Return</kbd></dd></dl>|Insere e concentra-se uma nova célula de markdown após a célula atual.|Mesmo comportamento que <kbd>retornar</kbd>|
|<kbd>Shift‑Return</kbd>|Sempre insere uma nova linha, independentemente do local do cursor ou conteúdo.|Insere uma quebra de linha definida dentro do bloco de Markdown atual.|
