---
title: Texto em xamarin. Forms
description: Xamarin. Forms tem três modos de exibição primários para trabalhar com texto, e este artigo explica como usá-las para inserir e exibir o texto em aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 4DBA7689-E5C8-4583-8FB4-02AB208B4416
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: c5bd157299c9388b561f316e65f2ba290bd15224
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244981"
---
# <a name="text-in-xamarinforms"></a>Texto em xamarin. Forms

_Usando o xamarin. Forms para inserir ou exibir texto._

Xamarin. Forms tem três modos de exibição primários para trabalhar com texto:

- **[Rótulo](#Label)**  &mdash; para apresentar o texto de único ou multilinha. Pode mostrar texto com várias opções de formatação na mesma linha.
- **[Entrada](#Entry)**  &mdash; para inserir o texto que é apenas uma linha. Entrada tem um modo de senha.
- **[Editor de](#Editor)**  &mdash; para inserir texto que pode levar mais de uma linha.

A aparência do texto pode ser alterada usando internas ou personalizadas [estilos](#Styles) e suportam a alguns controles personalizados [fontes](#Fonts).

<a name="Label" />

## <a name="labellabelmd"></a>[Rótulo](label.md)

O `Label` exibição é usada para exibir texto. Ele pode mostrar várias linhas de texto ou uma única linha de texto. `Label` pode apresentar o texto com várias opções de formatação usadas em embutido. O modo de exibição de rótulo pode encapsular ou truncar texto quando ela não couber em uma linha.

![](images/label.png "Exemplo de rótulo")

Consulte o [rótulo](label.md) do artigo para obter mais informações.

Para obter informações sobre como personalizar a fonte usada em um rótulo, consulte [fontes](fonts.md).

<a name="Entry" />

## <a name="entryentrymd"></a>[Entry](entry.md)

`Entry` é usado para aceitar a entrada de texto de linha única. `Entry` oferece controle sobre as cores, mas não pode ter personalizado fontes. `Entry` apresenta um modo de senha e pode mostrar texto do espaço reservado até que o texto é inserido.

![](images/entry.png "Exemplo de entrada")

Consulte o [entrada](entry.md) artigo para obter mais informações.

Observe que, ao contrário de `Label`, `Entry` não pode ter configurações de fonte personalizada.

<a name="Editor" />

## <a name="editoreditormd"></a>[Editor](editor.md)

`Editor` é usado para aceitar a entrada de texto de várias linhas. `Editor` pode ter uma cor de plano de fundo personalizado, mas a cor do texto e a fonte não pode ser alterada.

![](images/editor.png "Exemplo de editor")

Consulte o [Editor](editor.md) artigo para obter mais informações.

<a name="Fonts" />

## <a name="fontsfontsmd"></a>[Fontes](fonts.md)

O `Label` controle oferece suporte às configurações de fonte diferente usando as fontes internas em cada plataforma ou fontes personalizadas que acompanha seu aplicativo. Consulte o [fontes](fonts.md) do artigo para obter mais informações.

<a name="Styles" />

## <a name="stylesstylesmd"></a>[Estilos](styles.md)

Consulte [trabalhando com estilos](~/xamarin-forms/user-interface/styles/index.md) para saber como configurar a fonte, [cor](~/xamarin-forms/user-interface/colors.md)e outras propriedades de exibição que se aplicam a vários controles.



## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
