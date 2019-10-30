---
title: Visual Basic e .NET Standard
description: Este guia explicará como Visual Basic pode ser usado para escrever .NET Standard projetos que podem ser usados em soluções voltadas para Xamarin. iOS e Xamarin. Android.
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 594f7584e914b7bd8f4d7b72b3c82c42bb2fb73e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014579"
---
# <a name="visual-basic-and-net-standard"></a>Visual Basic e .NET Standard

Os projetos do Xamarin Android e iOS não dão suporte nativo a Visual Basic; no entanto, os desenvolvedores podem usar [.net Standard](~/cross-platform/app-fundamentals/net-standard.md) bibliotecas para migrar o código Visual Basic existente para Android e Ios, ou para gravar uma parte significativa da lógica do aplicativo no Visual Basic. Os aplicativos Xamarin. Forms podem ser criados inteiramente em Visual Basic (excluindo renderizadores personalizados, serviços de dependência e code-behind XAML).

## <a name="requirements"></a>Requisitos

Para criar e Compilar Visual Basic bibliotecas de .NET Standard, você deve usar o Visual Studio no Windows (Visual Studio 2017 ou mais recente).

> [!NOTE]
> Visual Basic bibliotecas só podem ser criadas e compiladas usando o Visual Studio. O xamarin. Android e o Xamarin. iOS não dão suporte ao idioma Visual Basic.
>
> Se você trabalhar apenas no Visual Studio, poderá fazer referência ao projeto Visual Basic de projetos Xamarin. Android e Xamarin. iOS.
>
> Se seus projetos Android e iOS também devem ser carregados em Visual Studio para Mac você deve referenciar o assembly de saída do assembly Visual Basic.

## <a name="creating-a-visual-basicnet-net-standard-library"></a>Criando uma biblioteca de .NET Standard Visual Basic.NET

Esta seção explica como criar uma biblioteca Visual Basic .NET Standard usando o Visual Studio 2019.
A biblioteca pode então ser referenciada em outros projetos, incluindo aplicativos Xamarin. Android, Xamarin. iOS e Xamarin. Forms.

Ao adicionar um Visual Basic .NET Standard biblioteca no Visual Studio, você deve ter cuidado para escolher o tipo de projeto correto:

1. No Visual Studio 2019, escolha **criar um novo projeto**.

2. Digite **Visual Basic biblioteca** para filtrar as opções de projeto e escolha a opção **biblioteca de classes (.net Standard)** com o ícone de Visual Basic:

    [Filtro de![para Visual Basic biblioteca](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

3. Na próxima tela, digite um nome para o projeto e pressione **criar**.

4. O projeto Visual Basic aparecerá conforme mostrado na **Gerenciador de soluções** assim:

    [![Visual Basic projeto vazio](images/new-library-sml.png)](images/new-library.png#lightbox)

O projeto agora está pronto para que Visual Basic código seja adicionado. .NET Standard projetos podem ser referenciados por outros projetos (projetos de aplicativo ou projetos de biblioteca).

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir Visual Basic código em aplicativos Xamarin usando o Visual Studio. Embora o Xamarin não ofereça suporte a Visual Basic diretamente, a compilação de Visual Basic em uma biblioteca .NET Standard permite que o código escrito com Visual Basic seja incluído nos aplicativos Android e iOS.

As páginas a seguir descrevem como usar o Visual Basic.NET .NET Standard bibliotecas em aplicativos nativos ou Xamarin. Forms:

- [Compilando aplicativos Xamarin. iOS e Xamarin. Android nativos que usam o VB](native-apps.md)
- [Criando aplicativos Xamarin. Forms com VB](xamarin-forms.md)

## <a name="related-links"></a>Links relacionados

- [TaskyVB (exemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)
- [XamarinFormsVB (exemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [.NET Standard e Xamarin](~/cross-platform/app-fundamentals/net-standard.md)
- [.NET Standard](/dotnet/standard/net-standard/)
