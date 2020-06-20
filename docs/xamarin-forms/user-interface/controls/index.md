---
title: Referência de Controles
description: Uma descrição de todos os elementos da interface do usuário usados para construir um Xamarin.Forms aplicativo. Este artigo lista os grupos de controle que compõem a interface do usuário de um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e843f0e42f4f66a6ce4e60c2f5d8a233d19f1df6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136390"
---
# <a name="controls-reference"></a>Referência de Controles

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

A interface do usuário de um Xamarin.Forms aplicativo é construída de objetos que são mapeados para os controles nativos de cada plataforma de destino. Isso permite que aplicativos específicos da plataforma para iOS, Android e o Plataforma Universal do Windows usem o Xamarin.Forms código contido em uma [biblioteca de .net Standard](~/cross-platform/app-fundamentals/net-standard.md).

Os quatro grupos principais de controle usados para criar a interface do usuário de um Xamarin.Forms aplicativo são os seguintes:

- [**Páginas**](pages.md)
- [**Layouts**](layouts.md)
- [**Exibições**](views.md)
- [**Células**](cells.md)

Uma Xamarin.Forms página geralmente ocupa a tela inteira. A página geralmente contém um layout, que contém exibições e possivelmente outros layouts. As células são componentes especializados usados em conexão com o [`TableView`](views.md#tableview) e o [`ListView`](views.md#listview) . Um diagrama de classe que mostra a hierarquia de tipos que normalmente são usados para criar uma interface do usuário no Xamarin.Forms pode ser encontrado em [ Xamarin.Forms controles hierarquia de classe](~/xamarin-forms/internals/class-hierarchy.md).

Nos quatro artigos sobre [**páginas**](pages.md), [**layouts**](layouts.md), [**exibições**](views.md)e [**células**](cells.md), cada tipo de controle é descrito com links para a documentação da API, um artigo que descreve seu uso (se houver) e um ou mais programas de exemplo (se existirem). Cada tipo de controle também acompanha uma captura de tela mostrando uma página do exemplo [**FormsGallery**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery) em execução em dispositivos IOS e Android. Abaixo de cada captura de tela há links para o código-fonte da página C#, a página XAML equivalente e (quando apropriado) o arquivo code-behind do C# para a página XAML.

> [!NOTE]
> Páginas, layouts e exibições derivam da `VisualElement` classe. A `VisualElement` classe fornece uma variedade de propriedades, métodos e eventos que são úteis na derivação de classes. Para obter mais informações, consulte [Propriedades, métodos e eventos do visualelement](common-properties.md).

Além dos controles fornecidos com, os Xamarin.Forms controles de terceiros estão disponíveis. Para obter mais informações, consulte [controles](thirdparty.md)de terceiros.

## <a name="related-links"></a>Links relacionados

- [Xamarin.FormsExemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsHierarquia de classes de controles](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentação da API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
