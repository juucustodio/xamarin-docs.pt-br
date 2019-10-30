---
title: Atalhos de teclado do editor de Xamarin Workbooks
description: Este documento descreve os atalhos de teclado disponíveis para uso no editor de Xamarin Workbooks. Em particular, ele examina várias maneiras de usar a chave de retorno.
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: f8c76f96ea16b9341d349ca110a3eb39207a4d30
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019101"
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Atalhos de teclado do editor de Xamarin Workbooks

## <a name="the-return-key-and-its-nuances"></a>A chave de retorno e suas nuances

A tabela a seguir descreve as várias associações de chave para executar o código e a redução da criação. Tomamos muito cuidado para escolher associações de chave sensíveis e consistentes que são familiares e fluidos.

|Associação de teclas|Célula de código|Célula de redução|
|--- |--- |--- |
|<kbd>Return</kbd>|<p>Se o cursor estiver no final do buffer de célula e a célula puder ser analisada com êxito, ele será executado e os resultados serão exibidos abaixo do buffer, e uma nova célula de código será inserida e focalizada a célula após a célula executada.</p><p>Se a análise não for bem-sucedida, uma nova linha será inserida no buffer. O diagnóstico do compilador não será produzido se a análise não for bem-sucedida.</p>|<p><kbd>Return</kbd> exibe um comportamento diferente dependendo do contexto de redução no cursor.</p><ul><li>Se o cursor estiver em um bloco de código de redução, uma nova linha literal será inserida.</li><li>Se o cursor estiver em um bloco de lista de redução, crie um novo item de lista ou divida o item de lista atual.</li><li>Se o cursor estiver em qualquer outro tipo de bloco de redução, crie um novo bloco de parágrafo ou divida o bloco atual.</li></ul>|
|<dl><dt>Mac</dt><dd><kbd>Retorno de comando</kbd></dd><dt>Venceu</dt><dd><kbd>Controle-retornar</kbd></dd></dl>|<p>Sempre tenta analisar e executar o conteúdo da célula. Se a compilação for bem-sucedida, os resultados (incluindo exceções de execução) serão exibidos abaixo do buffer e, se não houver células subsequentes, um novo será criado e focalizado.</p><p>Se houver erros de compilação, o diagnóstico será exibido e o buffer permanecerá focalizado com a posição do cursor inalterado.</p>|Insere e concentra uma nova célula de código após a célula de redução atual.|
|<dl><dt>Mac</dt><dd><kbd>Comando-Shift-Return</kbd><dd><dt>Venceu</dt><dd><kbd>Controle-Shift-Return</kbd></dd></dl>|Insere e concentra uma nova célula de redução após a célula atual.|Mesmo comportamento que o <kbd>retorno</kbd>|
|<kbd>Shift-retornar</kbd>|Sempre insere uma nova linha, independentemente do local ou do conteúdo do cursor.|Insere uma quebra de linha rígida dentro do bloco de redução atual.|
