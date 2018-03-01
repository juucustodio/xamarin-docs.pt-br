---
title: Perguntas frequentes
ms.topic: article
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 95728bf6bf1009db1cc834bf1d9d0be6a8fc5ef5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes


## <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-packageupdate-forms-templatemd"></a>[Pode atualizar o modelo de padrão de xamarin. Forms a um pacote NuGet mais recente?](update-forms-template.md)
Este guia usa o modelo xamarin. Forms PCL como um exemplo, mas o mesmo método geral também funcionará para o modelo de projeto compartilhado do xamarin. Forms. 

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-filesforms-xaml-designermd"></a>[Por que o designer XAML do Visual Studio não funciona para arquivos XAML xamarin. Forms?](forms-xaml-designer.md)
Xamarin. Forms atualmente não dá suporte a designers visuais para arquivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedlyandroid-linkassemblies-errormd"></a>[Erro de compilação Android: A tarefa "LinkAssemblies" falhou inesperadamente](android-linkassemblies-error.md)
Você verá uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` ao compilar um projeto do xamarin que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em um *versão* build para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos do Android não são atualizados para o framework mais recente. 


## <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-errormaps-compiletodalvik-errormd"></a>["Por que meu projeto Xamarin.Forms.Maps Android falha com COMPILETODALVIK: erro INESPERADO de nível superior?"](maps-compiletodalvik-error.md)
Esse erro pode ser visto no painel de erro do Visual Studio para Mac ou na janela de saída de compilação do Visual Studio; em projetos Android usando Xamarin.Forms.Maps. Ele geralmente é resolvido, aumentando o tamanho do Heap do Java para o seu projeto xamarin.

