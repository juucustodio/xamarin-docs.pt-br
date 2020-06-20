---
title: Por que o designer XAML do Visual Studio não funciona para Xamarin.Forms arquivos XAML?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9f9cf570da0d8e078d1d05e74dc0f65994080c6c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135883"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Por que o designer XAML do Visual Studio não funciona para Xamarin.Forms arquivos XAML?

Xamarin.FormsAtualmente, não dá suporte a designers visuais para arquivos XAML. Por isso, ao tentar abrir um arquivo XAML de formulário no *Designer de interface do usuário XAML* do Visual Studio ou *Designer de interface do usuário XAML com codificação*, a seguinte mensagem de erro é gerada:

> "O arquivo não pode ser aberto com o editor selecionado. Escolha outro editor. "

Essa limitação é descrita no guia [ Xamarin.Forms básico de XAML](~/xamarin-forms/xaml/xaml-basics/index.md) :

> "Ainda não há um designer visual para gerar XAML em Xamarin.Forms aplicativos, portanto, todo o XAML deve ser escrito à mão."

No entanto, o Xamarin.Forms modo de exibição de XAML pode ser exibido selecionando a opção de menu **Exibir > outra > Xamarin.Forms visualização do Windows** .
