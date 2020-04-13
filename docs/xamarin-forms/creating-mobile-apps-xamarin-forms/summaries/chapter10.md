---
title: Resumo do Capítulo 10. Extensões de marcação XAML
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 10. Extensões de marcação XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334260"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumo do Capítulo 10. Extensões de marcação XAML

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalmente, o analisador XAML converte qualquer conjunto de strings como um valor de atributo para [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) o tipo da propriedade com base [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute)em conversões padrão para os tipos de dados básicos .NET, ou um derivativo anexado à propriedade ou seu tipo com um .

Mas às vezes é conveniente definir um atributo de uma fonte diferente, por exemplo, um item em um dicionário, ou o valor de uma propriedade ou campo estático, ou de um cálculo de algum tipo.

Este é o trabalho de uma extensão de *marcação XAML*. Apesar do nome, as extensões de marcação XAML *não* são uma extensão para XML. XAML é sempre legal XML.

## <a name="the-code-infrastructure"></a>A infra-estrutura de código

Uma extensão de marcação XAML [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) é uma classe que implementa a interface. Tal classe muitas vezes `Extension` tem a palavra no final de seu nome, mas geralmente aparece em XAML sem esse sufixo.

As seguintes extensões de marcação XAML são suportadas por todas as implementações do XAML:

- `x:Static`apoiado por[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`apoiado por[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`apoiado por[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`apoiado por[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`apoiado por[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Essas quatro extensões de marcação XAML são suportadas por muitas implementações de XAML, incluindo Xamarin.Forms:

- `StaticResource`apoiado por[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`apoiado por[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`apoiado pelo [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash;discutido no [Capítulo 16. Vinculação de dados](chapter16.md)
- `TemplateBinding`apoiado por [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash;não coberto no livro

Uma extensão de marcação XAML adicional está incluída [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)no Xamarin.Forms em conexão com :

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;não coberto no livro

## <a name="accessing-static-members"></a>Acessando membros estáticos

Use [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) o elemento para definir um atributo ao valor de uma propriedade estática pública, campo ou membro de enumeração. Defina [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) a propriedade para o membro estático. Geralmente é mais fácil `x:Static` especificar e o nome do membro em chaves. O nome `Member` da propriedade não precisa ser incluído, apenas o próprio membro. Essa sintaxe comum é mostrada na amostra [**SharedStatics.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) Os próprios campos estáticos são definidos na [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Esta técnica permite estabelecer constantes usadas através de um programa.

Com uma declaração de namespace XML adicional, você pode referenciar propriedades estáticas públicas, campos ou membros de enumeração definidos no framework .NET, conforme demonstrado na amostra [**SystemStatics.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics)

## <a name="resource-dictionaries"></a>Dicionários de recurso

A `VisualElement` classe define uma [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propriedade nomeada que você [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)pode definir como um objeto de tipo . Dentro do XAML, você pode armazenar itens neste `x:Key` dicionário e identificá-los com o atributo. Os itens armazenados no dicionário de recursos são compartilhados entre todas as referências ao item.

### <a name="staticresource-for-most-purposes"></a>StaticResource para a maioria dos propósitos

Na maioria dos casos, [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) você usará a extensão de marcação para referenciar um item do dicionário de recursos, conforme demonstrado pela amostra [**ResourceSharing.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) Você pode `StaticResourceExtension` usar `StaticResource` um elemento ou dentro de chaves:

[![Captura de tela tripla de compartilhamento de recursos](images/ch10fg03-small.png "Compartilhamento de Recursos")](images/ch10fg03-large.png#lightbox "Compartilhamento de Recursos")

Não confunda `x:Static` a extensão `StaticResource` de marcação e a extensão de marcação.

### <a name="a-tree-of-dictionaries"></a>Uma árvore de dicionários

Quando o analisador XAML `StaticResource`encontra um , ele começa a procurar na árvore `ResourceDictionary` visual uma `App` chave correspondente e, em seguida, olha na classe do aplicativo. Isso permite que itens em um dicionário de recursos mais profundos na árvore visual sobreponham um dicionário de recursos mais alto na árvore visual. Isso é demonstrado na amostra [**ResourceTrees.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees)

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fins especiais

A `StaticResource` extensão de marcação faz com que um item seja `InitializeComponent` recuperado do dicionário quando uma árvore visual é construída durante a chamada. Uma alternativa `StaticResource` [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)é , que mantém um link para a tecla dicionário e atualiza o destino quando o item referenciado pelas principais alterações.

A diferença `StaticResource` `DynamicResource` entre e é demonstrada na amostra [**DynamicVsStatic.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic)

Uma propriedade `DynamicResource` definida por deve ser apoiada por uma propriedade vinculável, conforme discutido no [Capítulo 11, a infra-estrutura vinculável](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensões de marcação menos utilizadas

Use [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) a extensão de marcação para definir uma propriedade para `null`.

Use [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) a extensão de marcação para `Type` definir uma propriedade como um objeto .NET.

Use [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) para definir uma matriz. Especifique o tipo de`Type`membros da `x:Type` matriz definindo a propriedade [] como uma extensão de marcação.

## <a name="a-custom-markup-extension"></a>Uma extensão de marcação personalizada

Você pode criar suas próprias extensões de marcação XAML escrevendo uma classe que implementa a [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) interface com um [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) método.

A [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe satisfaz esses requisitos. Cria um valor `Color` de tipo baseado `H`em `S` `L`valores `A`de propriedades denominadas, e . Esta classe é o primeiro item em uma biblioteca Xamarin.Forms chamada [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que é construída e usada ao longo deste livro.

A amostra [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) demonstra como referenciar essa biblioteca e usar a extensão de marcação personalizada.

## <a name="related-links"></a>Links relacionados

- [Capítulo 10 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Capítulo 10 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
