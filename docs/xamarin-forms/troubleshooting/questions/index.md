---
title: Xamarin.Forms Perguntas frequentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 89364175-53BA-4A09-B3E2-44AC67DD971C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e520d222029e0d29a25f16aacfb6273da52cbf9e
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940207"
---
# <a name="no-locxamarinforms-frequently-asked-questions"></a>Xamarin.Forms Perguntas frequentes

## <a name="how-do-i-migrate-my-app-to-no-locxamarinforms-50"></a>[Como fazer migrar meu aplicativo para Xamarin.Forms 5,0?](forms5-migration.md)

A migração de um aplicativo para Xamarin.Forms o 5,0 exige conhecimento de suas alterações significativas.

## <a name="can-i-update-the-no-locxamarinforms-default-template-to-a-newer-nuget-package"></a>[Posso atualizar o Xamarin.Forms modelo padrão para um pacote NuGet mais recente?](update-forms-template.md)

Este guia usa o Xamarin.Forms modelo de biblioteca .net Standard como um exemplo, mas o mesmo método geral também funcionará para o Xamarin.Forms modelo de projeto compartilhado.

## <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-no-locxamarinforms-xaml-files"></a>[Por que o designer XAML do Visual Studio não funciona para Xamarin.Forms arquivos XAML?](forms-xaml-designer.md)

Xamarin.Forms Atualmente, não dá suporte a designers visuais para arquivos XAML.

## <a name="android-build-error-the-linkassemblies-task-failed-unexpectedly"></a>[Erro de compilação do Android: a tarefa "LinkAssemblies" falhou inesperadamente](android-linkassemblies-error.md)

Você poderá ver uma mensagem de erro `The "LinkAssemblies" task failed unexpectedly` ao criar um projeto Xamarin. Android que usa formulários. Isso acontece quando o vinculador está ativo (normalmente em uma compilação de *versão* para reduzir o tamanho do pacote do aplicativo); e isso ocorre porque os destinos do Android não são atualizados para a estrutura mais recente.

## <a name="why-does-my-no-locxamarinformsmaps-android-project-fail-with-compiletodalvik--unexpected-top-level-error"></a>["Por que meu Xamarin.Forms . Mapeia o projeto do Android falha com COMPILETODALVIK: erro de nível superior inesperado? "](maps-compiletodalvik-error.md)

Esse erro pode ser visto no painel de erro de Visual Studio para Mac ou na janela de saída da compilação do Visual Studio; em projetos do Android usando Xamarin.Forms . Los. Ele é normalmente resolvido aumentando o tamanho do heap do Java para seu projeto Xamarin. Android.
