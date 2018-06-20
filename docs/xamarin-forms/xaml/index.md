---
title: Aplicativo de linguagem XAML (eXtensible Markup)
description: XAML é uma linguagem de marcação declarativa que pode ser usada para definir as interfaces de usuário. A interface do usuário é definida em um arquivo XML usando a sintaxe XAML, enquanto o comportamento de tempo de execução é definido em um arquivo de code-behind separado.
ms.prod: xamarin
ms.assetid: CD30EECC-8AC1-4CF5-A4FE-348420A6231E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/18/2018
ms.openlocfilehash: c040c12829708418d0a705b8e9f930989900c678
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209421"
---
# <a name="extensible-application-markup-language-xaml"></a>Aplicativo de linguagem XAML (eXtensible Markup)

_XAML é uma linguagem de marcação declarativa que pode ser usada para definir as interfaces de usuário. A interface do usuário é definida em um arquivo XML usando a sintaxe XAML, enquanto o comportamento de tempo de execução é definido em um arquivo de code-behind separado._

> [!VIDEO https://youtube.com/embed/H6UOrSyhTEE]

**Evoluir 2016: Tornando-se um mestre de XAML**

> [!NOTE]
> Experimente o [visualização padrão do XAML](standard/index.md)

<a name="xaml" />

## <a name="xaml-basicsxaml-basicsindexmd"></a>[Noções básicas de XAML](xaml-basics/index.md)

XAML permite aos desenvolvedores a definição de interfaces de usuário em aplicativos xamarin. Forms usando marcação em vez do código. XAML nunca é necessária em um programa xamarin. Forms, mas é editável e geralmente é mais coerente visualmente e mais sucinta que código equivalente. XAML é especialmente adequado para uso com a arquitetura de aplicativos populares Model-View-ViewModel (MVVM): XAML define o modo de exibição que está vinculado ao ViewModel código por meio de ligações de dados baseados em XAML.

## <a name="xaml-compilationxamlcmd"></a>[compilação de XAML](xamlc.md)

Opcionalmente, XAML pode ser compilado direto na IL (linguagem intermediária) com o compilador XAML (XAMLC). Este artigo descreve como usar XAMLC e seus benefícios.

## <a name="xaml-previewerxaml-previewermd"></a>[Visualizador de XAML](xaml-previewer.md)

O [XAML visualizador](~/xamarin-forms/xaml/xaml-previewer.md) anunciadas no Xamarin evoluir 2016 está disponível para teste no canal alfa.

## <a name="xaml-namespacesnamespacesmd"></a>[Namespaces XAML](namespaces.md)

XAML usa o `xmlns` atributo XML para declarações de namespace. Este artigo apresenta a sintaxe do namespace XAML e demonstra como declarar um namespace XAML para um tipo de acesso.

## <a name="xaml-markup-extensionsmarkup-extensionsindexmd"></a>[Extensões de marcação XAML](markup-extensions/index.md)

XAML inclui extensões de marcação para definir atributos para valores ou objetos além do que podem ser expressas com cadeias de caracteres simples. Isso inclui propriedades estáticas, constantes e campos, dicionários de recursos e associações de dados de referência.

## <a name="field-modifiersfield-modifiersmd"></a>[Modificadores de campo](field-modifiers.md)

O `x:FieldModifier` atributo namespace Especifica o nível de acesso para campos gerados para elementos XAML nomeados.

## <a name="passing-argumentspassing-argumentsmd"></a>[Passar argumentos](passing-arguments.md)

XAML pode ser usado para passar argumentos para construtores não padrão ou métodos de fábrica. Este artigo demonstra como usar os atributos XAML que podem ser usados para passar argumentos para construtores, para chamar os métodos de fábrica e para especificar o tipo de um argumento genérico.

## <a name="bindable-propertiesbindable-propertiesmd"></a>[Propriedades vinculáveis](bindable-properties.md)

No xamarin. Forms, a funcionalidade das common language runtime (CLR) é estendida por propriedades vinculáveis. Uma propriedade ligável é um tipo especial de propriedade, em que o valor da propriedade é controlado pelo sistema de propriedade xamarin. Forms. Este artigo fornece uma introdução às propriedades vinculáveis e demonstra como criar e consumi-los.

## <a name="attached-propertiesattached-propertiesmd"></a>[Propriedades anexadas](attached-properties.md)

Uma propriedade anexada é um tipo especial de propriedade associável, definido em uma classe, mas anexado a outros objetos e reconhecível em XAML, como um atributo que contém uma classe e um nome de propriedade separado por um ponto. Este artigo fornece uma introdução às propriedades anexadas e demonstra como criar e consumi-los.

## <a name="resource-dictionariesresource-dictionariesmd"></a>[Dicionários de recurso](resource-dictionaries.md)

Recursos XAML são definições de objetos que podem ser usados mais de uma vez. Um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) permite que os recursos a serem definidos em um único local e reutilizadas em um aplicativo xamarin. Forms. Este artigo demonstra como criar e consumir um `ResourceDictionary`e como mesclar um `ResourceDictionary` em outro.
