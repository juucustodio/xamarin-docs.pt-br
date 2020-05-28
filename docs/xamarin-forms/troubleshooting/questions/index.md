---
title: Xamarin.FormsPerguntas frequentes
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: edd6cfefe18ff3d5cc97ec58f3bce867f11df7c8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135870"
---
# <a name="xamarinforms-frequently-asked-questions"></a>Xamarin.FormsPerguntas frequentes

## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[Posso atualizar o Xamarin.Forms modelo padrão para um pacote NuGet mais recente?](update-forms-template.md)
Este guia usa o Xamarin.Forms modelo de biblioteca .net Standard como um exemplo, mas o mesmo método geral também funcionará para o Xamarin.Forms modelo de projeto compartilhado.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Por que o designer XAML do Visual Studio não funciona para Xamarin.Forms arquivos XAML?](forms-xaml-designer.md)
Xamarin.FormsAtualmente, não dá suporte a designers visuais para arquivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Erro de compilação do Android: a tarefa "LinkAssemblies" falhou inesperadamente](android-linkassemblies-error.md)
Você poderá ver uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` ao criar um projeto Xamarin. Android que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em uma compilação de *versão* para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos do Android não são atualizados para a estrutura mais recente. 

## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["Por que meu Xamarin.Forms . Mapeia o projeto do Android falha com COMPILETODALVIK: erro de nível superior inesperado? "](maps-compiletodalvik-error.md)
Esse erro pode ser visto no painel de erro de Visual Studio para Mac ou na janela de saída da compilação do Visual Studio; em projetos do Android usando Xamarin.Forms . Los. Ele é normalmente resolvido aumentando o tamanho do heap do Java para seu projeto Xamarin. Android.
