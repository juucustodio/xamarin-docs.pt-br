---
title: Recursos de plataforma Android
description: Adicionando funcionalidade específica do Android para aplicativos xamarin. Forms
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2016
ms.openlocfilehash: 3648f6f5f576a77bf7887668352c4f3d11f3906d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="android-platform-features"></a>Recursos de plataforma Android

## <a name="platform-support"></a>Suporte de plataforma

O padrão de projeto Android xamarin. Forms usa um estilo antigo do controle renderering comum antes do Android 5.0. Os aplicativos criados usando o modelo tem `FormsApplicationActivity` como a classe base da sua atividade principal.

## <a name="material-design-via-appcompat"></a>Design de material por meio de AppCompat

Xamarin. Forms também tem um recurso opcional `FormsAppCompatActivity` que usa **AppCompat** recursos fornecidos pelo Android para implementar os temas de Material de Design.

Para adicionar os temas de Design de Material ao seu projeto Android xamarin. Forms, execute o [suportam a instruções de instalação para AppCompat](appcompat.md)

Aqui está o **Todo** exemplo com o padrão `FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Exemplo de todo o aplicativo sem AppCompat")](images/before-appcompat.png#lightbox "aplicativo de exemplo de tarefas sem AppCompat")

E isso é o mesmo código depois de atualizar o projeto para usar `FormsAppCompatActivity` (e adicionar as informações adicionais de tema):

[![](images/post-appcompat-sml.png "Aplicativo de exemplo de tarefas com AppCompat e temas")](images/post-appcompat.png#lightbox "aplicativo de exemplo de tarefas com AppCompat e temas")

> [!NOTE]
> Ao usar `FormsAppCompatActivity`, o [classes base para alguns processadores personalizados Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) serão diferentes.


## <a name="related-links"></a>Links relacionados

- [Adicionar suporte para Material de Design](appcompat.md)
