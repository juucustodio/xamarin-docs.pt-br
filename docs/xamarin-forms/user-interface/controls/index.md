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
ms.openlocfilehash: 90d97e690c4d1fcdb22f17bbc856f9f9b2d0900c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374648"
---
# <a name="controls-reference"></a>Referência de Controles

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/formsgallery/)

A interface do usuário de um Xamarin.Forms aplicativo é construída de objetos que são mapeados para os controles nativos de cada plataforma de destino. Isso permite que aplicativos específicos da plataforma para iOS, Android e o Plataforma Universal do Windows usem o Xamarin.Forms código contido em uma [biblioteca de .net Standard](~/cross-platform/app-fundamentals/net-standard.md).

Os quatro grupos principais de controle usados para criar a interface do usuário de um Xamarin.Forms aplicativo são os seguintes:

- [**Páginas**](pages.md)
- [**Layouts**](layouts.md)
- [**Exibições**](views.md)
- [**Células**](cells.md)

Uma Xamarin.Forms página geralmente ocupa a tela inteira. A página geralmente contém um layout, que contém exibições e possivelmente outros layouts. As células são componentes especializados usados em conexão com o [`TableView`](xref:Xamarin.Forms.TableView) e o [`ListView`](xref:Xamarin.Forms.ListView) . Um diagrama de classe que mostra a hierarquia de tipos que normalmente são usados para criar uma interface do usuário no Xamarin.Forms pode ser encontrado em [ Xamarin.Forms controles hierarquia de classe](~/xamarin-forms/internals/class-hierarchy.md).

Nos quatro artigos sobre [**páginas**](pages.md), [**layouts**](layouts.md), [**exibições**](views.md)e [**células**](cells.md), cada tipo de controle é descrito com links para a documentação da API, um artigo que descreve seu uso (se houver) e um ou mais programas de exemplo (se existirem). Cada tipo de controle também acompanha uma captura de tela mostrando uma página do exemplo [**FormsGallery**](/samples/xamarin/xamarin-forms-samples/formsgallery) em execução em dispositivos IOS e Android. Abaixo de cada captura de tela há links para o código-fonte da página C#, a página XAML equivalente e (quando apropriado) o arquivo code-behind do C# para a página XAML.

> [!NOTE]
> Páginas, layouts e exibições derivam da `VisualElement` classe. A `VisualElement` classe fornece uma variedade de propriedades, métodos e eventos que são úteis na derivação de classes. Para obter mais informações, consulte [Propriedades, métodos e eventos do visualelement](common-properties.md).

Além dos controles fornecidos com, os Xamarin.Forms controles de terceiros estão disponíveis. Para obter mais informações, consulte [controles](thirdparty.md)de terceiros.

## <a name="related-links"></a>Links Relacionados

- [Xamarin.Forms Exemplo de FormsGallery](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Hierarquia de classes de controles](~/xamarin-forms/internals/class-hierarchy.md)
- [Documentação da API](/dotnet/api/xamarin.forms?view=xamarin-forms)