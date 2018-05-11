---
title: Perguntas frequentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 5a36c6ab14fdc7bfc5916456670be9c8fe4476ff
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes


## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[Posso atualizar o modelo padrão Xamarin.Forms para um pacote NuGet mais recente?](update-forms-template.md)
Este guia usa o modelo de biblioteca xamarin. Forms .NET padrão como um exemplo, mas o mesmo método geral também funcionará para o modelo de projeto compartilhado do xamarin. Forms. 

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Por que o designer XAML do Visual Studio não funciona para arquivos XAML do Xamarin.Forms?](forms-xaml-designer.md)
Xamarin. Forms atualmente não dá suporte a designers visuais para arquivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Erro de compilação do Android: a tarefa "LinkAssemblies" falhou inesperadamente](android-linkassemblies-error.md)
Você verá uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` ao compilar um projeto do xamarin que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em um *versão* build para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos do Android não são atualizados para o framework mais recente. 


## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["Por que meu projeto Xamarin.Forms.Maps Android falha com COMPILETODALVIK: erro INESPERADO de nível superior?"](maps-compiletodalvik-error.md)
Esse erro pode ser visto no painel de erro do Visual Studio para Mac ou na janela de saída de compilação do Visual Studio; em projetos Android usando Xamarin.Forms.Maps. Ele geralmente é resolvido, aumentando o tamanho do Heap do Java para o seu projeto xamarin.

