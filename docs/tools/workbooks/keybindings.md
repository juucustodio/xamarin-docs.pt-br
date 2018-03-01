---
title: Atalhos de teclado do Editor de pastas de trabalho do Xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: 6375A371-3215-4A7C-B97B-A19E58BE96D6
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: cbbf070a9c94221d98dedcd1df884a897ff7df3e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="xamarin-workbooks-editor-keyboard-shortcuts"></a>Atalhos de teclado do Editor de pastas de trabalho do Xamarin

## <a name="the-return-key-and-its-nuances"></a>A chave de retorno e seus nuances

A tabela a seguir descreve as várias associações de chave para a execução de código e redução de criação. Levamos muito cuidado ao escolher a sensatas e consistentes associações de chave que são familiares e fluido.

<table>
  <tr>
    <th>Chave de associação</th>
    <th>Célula de código</th>
    <th>Redução de célula</th>
  </tr>
  <tr>
    <td><kbd>Return</kbd></td>
    <td>
      <p>Se o cursor estiver no final da célula buffer e a célula podem ser analisados com êxito, ele será executado e os resultados serão exibidos abaixo do buffer e uma nova célula de código será inserida e voltada para a célula após a célula executada.</p>
      
      <p>Se a análise não for bem-sucedida, uma nova linha será inserida no buffer. Diagnóstico do compilador não será produzido se a análise não for bem-sucedida.</p>
    </td>
    <td>
      <p>
        <kbd>Retornar</kbd> apresenta um comportamento diferente dependendo do contexto de Markdown no cursor.
      </p>
      <ul>
        <li>
Se o cursor estiver em um bloco de código de Markdown, uma nova linha literal é inserida.
        </li>
        <li>
Se o cursor estiver em um bloco de lista de Markdown, criar um novo item de lista ou dividir o item atual da lista.
        </li>
        <li>
Se o cursor estiver em qualquer outro tipo de bloco de Markdown, crie um novo bloco de parágrafo ou dividir o bloco atual.
        </li>
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑Return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Return</kbd></dd>
      </dl>
    </td>
    <td>
      <p>Sempre tenta analisar e executar o conteúdo da célula. Se a compilação for bem-sucedida, os resultados (incluindo exceções de execução) serão exibidos abaixo do buffer, e se não houver nenhuma célula subsequente, um novo será criado e foco.</p>
      
      <p>Se não houver nenhum erro de compilação, diagnóstico será exibido e o buffer será concentre-se com a posição do cursor inalterada.</p>
    </td>
    <td>
Insere e concentra-se uma nova célula código após a célula atual de redução.
    </td>
  </tr>
  <tr>
    <td>
      <dl>
        <dt>Mac</dt>
        <dd><kbd>Command‑Shift‑Return</kbd></dd>
        <dt>Win</dt>
        <dd><kbd>Control‑Shift‑Return</kbd></dd>
      </dl>
    </td>
    <td>
Insere e concentra-se uma nova célula markdown após a célula atual.
    </td>
    <td>
Mesmo comportamento <kbd>retornar</kbd>
    </td>
  </tr>
  <tr>
    <td><kbd>Shift‑Return</kbd></td>
    <td>
Sempre insere uma nova linha, independentemente do local do cursor ou conteúdo.
    </td>
    <td>
Insere uma quebra de linha definida dentro do bloco de Markdown atual.
    </td>
  </tr>
</table>
