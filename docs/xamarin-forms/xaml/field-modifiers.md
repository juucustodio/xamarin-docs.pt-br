---
title: Modificadores de campo XAML no xamarin. Forms
description: O atributo de namespace X:FieldModifier Especifica o nível de acesso para campos gerados para elementos XAML nomeados.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209498"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificadores de campo XAML no xamarin. Forms

_O `x:FieldModifier` atributo namespace Especifica o nível de acesso para campos gerados para elementos XAML nomeados._

## <a name="overview"></a>Visão geral

Os valores válidos do atributo são:

- `Public` – Especifica que o campo gerado para o elemento XAML `public`.
- `NotPublic` – Especifica que o campo gerado para o elemento XAML `internal` ao assembly.

Se o valor do atributo não está definido, o campo gerado para o elemento será `private`.

As seguintes condições devem ser atendidas para uma `x:FieldModifier` atributo a ser processado:

- O elemento de nível superior XAML deve ser um válido `x:Class`.
- O elemento atual XAML tem um `x:Name` especificado.

O XAML a seguir mostra exemplos de configuração do atributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> O `x:FieldModifier` atributo não pode ser usado para especificar o nível de acesso de uma classe XAML.
