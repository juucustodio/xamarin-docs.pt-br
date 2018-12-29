---
title: Linguagem de marcação de aplicativo extensível (XAML)
description: XAML é uma linguagem de marcação declarativa que pode ser usada para definir interfaces do usuário. A interface do usuário é definida em um arquivo XML usando a sintaxe XAML, enquanto o comportamento de tempo de execução é definido em um arquivo code-behind separado.
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 9924e588808783fe35dbd830bbc9af288f37e7ea
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53813954"
---
# <a name="extensible-application-markup-language-xaml"></a>Linguagem de marcação de aplicativo extensível (XAML)

_XAML é uma linguagem de marcação declarativa que pode ser usada para definir interfaces do usuário. A interface do usuário é definida em um arquivo XML usando a sintaxe XAML, enquanto o comportamento de tempo de execução é definido em um arquivo code-behind separado._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evolui 2016: Tornando-se um mestre de XAML**

> [!NOTE]
> Experimente o [visualização padrão de XAML](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[Noções básicas de XAML](xaml-basics/index.md)

XAML permite que os desenvolvedores definam interfaces do usuário em aplicativos xamarin. Forms usando marcação em vez de código. XAML nunca é necessária em um programa do xamarin. Forms, mas ele é compatível com ferramentas e geralmente é mais visualmente coerente e mais sucinta do que o código equivalente. XAML é especialmente adequado para uso com a arquitetura de aplicativo populares Model-View-ViewModel (MVVM): XAML define o modo de exibição que esteja vinculado ao código do ViewModel por meio de ligações de dados com base em XAML.

## <a name="xaml-compilationxamlcmd"></a>[compilação de XAML](xamlc.md)

Opcionalmente, XAML pode ser compilado direto na IL (linguagem intermediária) com o compilador XAML (XAMLC). Este artigo descreve como usar o XAMLC e seus benefícios.

## <a name="xaml-previewerxaml-previewermd"></a>[Visualizador de XAML](xaml-previewer.md)

O [XAML pré-visualizador](~/xamarin-forms/xaml/xaml-previewer.md) renderiza uma visualização dinâmica de uma página lado a lado com a marcação XAML, permitindo que você veja sua interface do usuário renderizado conforme você digita.

## <a name="xaml-namespacesnamespacesmd"></a>[Namespaces XAML](namespaces.md)

XAML usa a `xmlns` atributo XML para declarações de namespace. Este artigo apresenta a sintaxe do namespace XAML e demonstra como declarar um namespace XAML para um tipo de acesso.

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[Extensões de marcação XAML](markup-extensions/index.md)

XAML inclui extensões de marcação para definir atributos para valores ou objetos além do que podem ser expressas com cadeias de caracteres simples. Isso inclui a referência de constantes, propriedades estáticas e campos, dicionários de recursos e as associações de dados.

## <a name="field-modifiersfield-modifiersmd"></a>[Modificadores de campo](field-modifiers.md)

O `x:FieldModifier` atributo namespace Especifica o nível de acesso para os campos gerados para elementos nomeados do XAML.

## <a name="passing-argumentspassing-argumentsmd"></a>[Passar argumentos](passing-arguments.md)

XAML pode ser usado para passar argumentos para os construtores não padrão ou a métodos de fábrica. Este artigo demonstra como usar os atributos XAML que podem ser usados para passar argumentos para construtores, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico.

## <a name="bindable-propertiesbindable-propertiesmd"></a>[Propriedades vinculáveis](bindable-properties.md)

No xamarin. Forms, a funcionalidade de propriedades comuns de runtime (CLR) de linguagem é estendida por propriedades associáveis. Uma propriedade associável é um tipo especial de propriedade, em que o valor da propriedade é controlado pelo sistema de propriedades de xamarin. Forms. Este artigo fornece uma introdução a propriedades associáveis e demonstra como criar e consumi-los.

## <a name="attached-propertiesattached-propertiesmd"></a>[Propriedades anexadas](attached-properties.md)

Uma propriedade anexada é um tipo especial de propriedade associável, definido em uma classe, mas anexado a outros objetos e reconhecível no XAML como um atributo que contém uma classe e um nome de propriedade separados por um período. Este artigo fornece uma introdução para propriedades anexadas e demonstra como criar e consumi-los.

## <a name="resource-dictionariesresource-dictionariesmd"></a>[Dicionários de recurso](resource-dictionaries.md)

Recursos XAML são definições de objetos que podem ser usados mais de uma vez. Um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) permite que os recursos sejam definidos em um único local e reutilizadas em todo um aplicativo xamarin. Forms. Este artigo demonstra como criar e consumir um `ResourceDictionary`e como mesclar um `ResourceDictionary` em outro.

## <a name="loading-xaml-at-runtimeruntime-loadmd"></a>[Carregando XAML em tempo de execução](runtime-load.md)

XAML pode ser carregado e analisado em tempo de execução com o [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensão.
