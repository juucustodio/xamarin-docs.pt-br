---
title: Modificadores de campo XAML no Xamarin. Forms
description: O atributo namespace x:FieldModifier especifica o nível de acesso para campos gerados para elementos XAML nomeados.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/02/2019
ms.openlocfilehash: 0f6050de943ca9878cf41b448d44bf222689be56
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739443"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificadores de campo XAML no Xamarin. Forms

O `x:FieldModifier` atributo namespace Especifica o nível de acesso para campos gerados para elementos XAML nomeados. Os valores válidos do atributo são:

- `private`– Especifica que o campo gerado para o elemento XAML é acessível somente dentro do corpo da classe na qual ele é declarado.
- `public`– Especifica que o campo gerado para o elemento XAML não tem restrições de acesso.
- `protected`– Especifica que o campo gerado para o elemento XAML pode ser acessado dentro de sua classe e por instâncias de classe derivadas.
- `internal`– Especifica que o campo gerado para o elemento XAML é acessível somente dentro de tipos no mesmo assembly.
- `notpublic`– Especifica que o campo gerado para o elemento XAML é acessível somente dentro de tipos no mesmo assembly.

Por padrão, se o valor do atributo não for definido, o campo gerado para o elemento será `private`.

> [!NOTE]
> O valor do atributo pode usar qualquer maiúsculas e minúsculas, pois ele será convertido em minúsculas pelo Xamarin. Forms.

As seguintes condições devem ser atendidas para `x:FieldModifier` que um atributo seja processado:

- O elemento XAML de nível superior deve ser um válido `x:Class`.
- O elemento XAML atual tem um `x:Name` especificado.

O XAML a seguir mostra exemplos de como definir o atributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> O `x:FieldModifier` atributo não pode ser usado para especificar o nível de acesso de uma classe XAML.
