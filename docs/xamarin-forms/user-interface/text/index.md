---
title: Texto em Xamarin.Forms
description: Xamarin.Forms tem três exibições principais para trabalhar com texto, e este artigo explica como usá-las para inserir e exibir texto em Xamarin.Forms aplicativos.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37973db6288f092d756a29de61731a296eac3924
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375402"
---
# <a name="text-in-no-locxamarinforms"></a>Texto em Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Usando Xamarin.Forms para inserir ou exibir texto._

Xamarin.Forms tem três exibições principais para trabalhar com texto:

- **[Rótulo](#label)** &mdash; do para apresentar texto de linha única ou de várias linhas. Pode mostrar texto com várias opções de formatação na mesma linha.
- **[Entrada](#entry)** &mdash; do para inserir o texto que é apenas uma linha. A entrada tem um modo de senha.
- **[Editor](#editor)** &mdash; do para inserir texto que poderia levar mais de uma linha.

A aparência do texto pode ser alterada usando [estilos](#styles) internos ou personalizados e alguns controles dão suporte a [fontes](#fonts)personalizadas.

## <a name="label"></a>[Rotular](label.md)

A `Label` exibição é usada para exibir texto. Ele pode mostrar várias linhas de texto ou uma única linha de texto. `Label` pode apresentar texto com várias opções de formatação usadas em linha. O modo de exibição de rótulo pode encapsular ou truncar texto quando ele não couber em uma linha.

![Exemplo de rótulo](images/label.png)

Consulte o artigo [rótulo](label.md) para obter informações mais detalhadas.

Para obter informações sobre como personalizar a fonte usada em um rótulo, consulte [fontes](fonts.md).

## <a name="entry"></a>[Entrada](entry.md)

`Entry` é usado para aceitar entrada de texto de linha única. `Entry` oferece controle sobre cores e fontes. `Entry` tem um modo de senha e pode mostrar o texto do espaço reservado até que o texto seja inserido.

![Exemplo de entrada](images/entry.png)

Consulte o artigo de [entrada](entry.md) para obter mais informações.

Observe que, ao contrário `Label` de, `Entry` não é possível ter configurações de fonte personalizadas.

## <a name="editor"></a>[Editor](editor.md)

`Editor` é usado para aceitar a entrada de texto de várias linhas. `Editor` oferece controle sobre cores e fontes.

![Exemplo de editor](images/editor.png)

Consulte o artigo do [Editor](editor.md) para obter mais informações.

## <a name="fonts"></a>[Fontes](fonts.md)

Muitos controles dão suporte a diferentes configurações de fonte usando as fontes internas em cada plataforma ou fontes personalizadas incluídas em seu aplicativo. Consulte o artigo [fontes](fonts.md) para obter informações mais detalhadas.

## <a name="styles"></a>[Estilos](styles.md)

Consulte [trabalhando com estilos](~/xamarin-forms/user-interface/styles/index.md) para saber como configurar fontes, [cores](~/xamarin-forms/user-interface/colors.md)e outras propriedades de exibição que se aplicam a vários controles.

## <a name="related-links"></a>Links Relacionados

- [Texto (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-text)