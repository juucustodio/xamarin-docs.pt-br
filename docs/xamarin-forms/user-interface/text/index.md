---
title: Texto emXamarin.Forms
description: Xamarin.Formstem três exibições principais para trabalhar com texto, e este artigo explica como usá-las para inserir e exibir texto em Xamarin.Forms aplicativos.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 579ff44e9c58d7eea538d5478e99b4c480d44ac0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136182"
---
# <a name="text-in-xamarinforms"></a>Texto emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Usando Xamarin.Forms para inserir ou exibir texto._

Xamarin.Formstem três exibições principais para trabalhar com texto:

- **[Rótulo](#Label)** &mdash; do para apresentar texto de linha única ou de várias linhas. Pode mostrar texto com várias opções de formatação na mesma linha.
- **[Entrada](#Entry)** &mdash; do para inserir o texto que é apenas uma linha. A entrada tem um modo de senha.
- **[Editor](#Editor)** &mdash; do para inserir texto que poderia levar mais de uma linha.

A aparência do texto pode ser alterada usando [estilos](#Styles) internos ou personalizados e alguns controles dão suporte a [fontes](#Fonts)personalizadas.

<a name="Label" />

## <a name="label"></a>[Chamada](label.md)

A `Label` exibição é usada para exibir texto. Ele pode mostrar várias linhas de texto ou uma única linha de texto. `Label`pode apresentar texto com várias opções de formatação usadas em linha. O modo de exibição de rótulo pode encapsular ou truncar texto quando ele não couber em uma linha.

![Exemplo de rótulo](images/label.png)

Consulte o artigo [rótulo](label.md) para obter informações mais detalhadas.

Para obter informações sobre como personalizar a fonte usada em um rótulo, consulte [fontes](fonts.md).

<a name="Entry" />

## <a name="entry"></a>[Inicial](entry.md)

`Entry`é usado para aceitar entrada de texto de linha única. `Entry`oferece controle sobre cores e fontes. `Entry`tem um modo de senha e pode mostrar o texto do espaço reservado até que o texto seja inserido.

![Exemplo de entrada](images/entry.png)

Consulte o artigo de [entrada](entry.md) para obter mais informações.

Observe que, ao contrário `Label` de, `Entry` não é possível ter configurações de fonte personalizadas.

<a name="Editor" />

## <a name="editor"></a>[Editor](editor.md)

`Editor`é usado para aceitar a entrada de texto de várias linhas. `Editor`oferece controle sobre cores e fontes.

![Exemplo de editor](images/editor.png)

Consulte o artigo do [Editor](editor.md) para obter mais informações.

<a name="Fonts" />

## <a name="fonts"></a>[Fontes](fonts.md)

Muitos controles dão suporte a diferentes configurações de fonte usando as fontes internas em cada plataforma ou fontes personalizadas incluídas em seu aplicativo. Consulte o artigo [fontes](fonts.md) para obter informações mais detalhadas.

<a name="Styles" />

## <a name="styles"></a>[Estilos](styles.md)

Consulte [trabalhando com estilos](~/xamarin-forms/user-interface/styles/index.md) para saber como configurar fontes, [cores](~/xamarin-forms/user-interface/colors.md)e outras propriedades de exibição que se aplicam a vários controles.

## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
