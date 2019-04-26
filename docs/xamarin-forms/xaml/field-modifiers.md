---
title: Modificadores de campo XAML no xamarin. Forms
description: O atributo de namespace X:FieldModifier Especifica o nível de acesso para os campos gerados para elementos nomeados do XAML.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075292"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificadores de campo XAML no xamarin. Forms

_O `x:FieldModifier` atributo namespace Especifica o nível de acesso para os campos gerados para elementos nomeados do XAML._

## <a name="overview"></a>Visão geral

Os valores válidos do atributo são:

- `Public` – Especifica que o campo gerado para o elemento XAML é `public`.
- `NotPublic` – Especifica que o campo gerado para o elemento XAML é `internal` ao assembly.

Se o valor do atributo não for definido, o campo gerado para o elemento será `private`.

As seguintes condições devem ser atendidas para uma `x:FieldModifier` atributo a ser processado:

- O elemento XAML deve ser válido `x:Class`.
- O elemento XAML atual tiver um `x:Name` especificado.

O XAML a seguir mostra exemplos de configuração do atributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> O `x:FieldModifier` atributo não pode ser usado para especificar o nível de acesso de uma classe XAML.
