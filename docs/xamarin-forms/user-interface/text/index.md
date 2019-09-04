---
title: Texto no xamarin. Forms
description: Xamarin. Forms tem três modos de exibição primários para trabalhar com texto, e este artigo explica como usá-los para inserir e exibir texto em aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2018
ms.openlocfilehash: 7b7cd9445ec9f28211037608ab04ffc45bec705c
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227977"
---
# <a name="text-in-xamarinforms"></a>Texto no xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Usando o xamarin. Forms para inserir ou exibir texto._

Xamarin. Forms tem três modos de exibição primários para trabalhar com texto:

- **[Rótulo](#Label)**  &mdash; para a apresentação de texto único ou várias linha. Pode mostrar o texto com várias opções de formatação na mesma linha.
- **[Entrada](#Entry)**  &mdash; para inserção de texto que é apenas uma linha. Entrada tem um modo de senha.
- **[Editor](#Editor)**  &mdash; para inserção de texto que pode levar mais de uma linha.

Aparência do texto pode ser alterada usando internas ou personalizadas [estilos](#Styles) e alguns controles oferecem suporte personalizado [fontes](#Fonts).

<a name="Label" />

## <a name="labellabelmd"></a>[Rótulo](label.md)

O `Label` exibição é usada para exibir o texto. Ele pode mostrar várias linhas de texto ou uma única linha de texto. `Label` pode apresentar texto com várias opções de formatação usadas em embutido. O modo de exibição do rótulo pode encapsular ou truncar o texto quando ela não couber em uma única linha.

![Exemplo de rótulo](images/label.png)

Consulte a [rótulo](label.md) do artigo para obter mais informações.

Para obter informações sobre como personalizar a fonte usada em um rótulo, consulte [fontes](fonts.md).

<a name="Entry" />

## <a name="entryentrymd"></a>[Entry](entry.md)

`Entry` é usado para aceitar a entrada de texto de linha única. `Entry` controle de ofertas sobre fontes e cores. `Entry` tem um modo de senha e pode mostrar o texto de espaço reservado até que o texto é inserido.

![Exemplo de entrada](images/entry.png)

Consulte a [entrada](entry.md) artigo para obter mais informações.

Observe que, diferentemente `Label`, `Entry` não pode ter configurações de fonte personalizado.

<a name="Editor" />

## <a name="editoreditormd"></a>[Editor](editor.md)

`Editor` é usado para aceitar a entrada de texto de várias linhas. `Editor` controle de ofertas sobre fontes e cores.

![Exemplo de editor](images/editor.png)

Consulte a [Editor](editor.md) artigo para obter mais informações.

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[Fontes](fonts.md)

Muitos controles dão suporte a diferentes configurações de fonte usando as fontes internas em cada plataforma ou fontes personalizadas incluídas em seu aplicativo. Consulte a [fontes](fonts.md) do artigo para obter mais informações.

<a name="Styles" />

## <a name="stylesstylesmd"></a>[Estilos](styles.md)

Consulte a [trabalhar com estilos](~/xamarin-forms/user-interface/styles/index.md) para saber como configurar a fonte, [cor](~/xamarin-forms/user-interface/colors.md)e outras propriedades de exibição que se aplicam a vários controles.

## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
