---
title: Resumo do capítulo 10. Extensões de marcação XAML
description: 'Criando aplicativos móveis com xamarin. Forms: Resumo do capítulo 10. Extensões de marcação XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672628"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumo do capítulo 10. Extensões de marcação XAML

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalmente, o analisador XAML Converte qualquer cadeia de caracteres definido como um valor de atributo para o tipo da propriedade com base em conversões padrão para os tipos de dados básicos do .NET, ou um [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) derivativo anexado à propriedade ou seu tipo com um [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Mas, às vezes, é conveniente definir um atributo de uma fonte diferente, por exemplo, um item em um dicionário ou o valor da propriedade ou campo, ou de um cálculo de algum tipo.

Esse é o trabalho de um *extensão de marcação XAML*. Apesar do nome, extensões de marcação XAML são *não* uma extensão do XML. XAML é sempre XML legal.

## <a name="the-code-infrastructure"></a>A infraestrutura de código

Uma extensão de marcação XAML é uma classe que implementa o [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) interface. Essa classe tem muitas vezes a palavra `Extension` no final de seu nome, mas geralmente aparece no XAML sem o sufixo.

As seguintes extensões de marcação XAML têm suporte de todas as implementações de XAML:

- `x:Static` com suporte [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` com suporte [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` com suporte [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` com suporte [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` com suporte [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Essas quatro extensões de marcação XAML têm suporte por muitas implementações de XAML, incluindo o xamarin. Forms:

- `StaticResource` com suporte [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` com suporte [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` com suporte pelo [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash;discutidos [capítulo 16. Associação de dados](chapter16.md)
- `TemplateBinding` com suporte pelo [ `TemplateBindingExtension` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash;não abordados no livro

Uma extensão de marcação XAML adicional é incluída no xamarin. Forms em conexão com [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;não são abordados no livro

## <a name="accessing-static-members"></a>Acesso a membros estáticos

Use o [ `x:Static` ](xref:Xamarin.Forms.Xaml.StaticExtension) elemento para definir um atributo para o valor de um membro de enumeração, campo ou propriedade estático público. Defina as [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) propriedade para o membro estático. É geralmente mais fácil especificar `x:Static` e o nome do membro entre chaves. O nome da `Member` propriedade não precisam ser incluídos, apenas o próprio membro. Essa sintaxe comum é mostrada na [ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) exemplo. Os campos estáticos em si são definidos na [ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Essa técnica permite que você estabeleça constantes usadas por meio de um programa.

Com uma declaração de namespace XML adicional, você pode fazer referência a propriedades públicas estáticas, campos ou membros de enumeração definidos no .NET framework, conforme demonstrado a [ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) exemplo .

## <a name="resource-dictionaries"></a>Dicionários de recurso

O `VisualElement` classe define uma propriedade chamada [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) que podem ser definidas para um objeto do tipo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Dentro do XAML, você pode armazenar itens neste dicionário e identificá-los com o `x:Key` atributo. Os itens armazenados no dicionário de recursos são compartilhados entre todas as referências para o item.

### <a name="staticresource-for-most-purposes"></a>StaticResource para a maioria das finalidades

Na maioria dos casos você usará o [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) extensão de marcação para referenciar um item do dicionário de recursos, como demonstrado pelo [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) exemplo . Você pode usar um `StaticResourceExtension` elemento ou `StaticResource` entre chaves:

[![Captura de tela de compartilhamento de recursos tripla](images/ch10fg03-small.png "compartilhamento de recursos")](images/ch10fg03-large.png#lightbox "compartilhamento de recursos")

Não confunda a `x:Static` extensão de marcação e o `StaticResource` extensão de marcação.

### <a name="a-tree-of-dictionaries"></a>Uma árvore de dicionários

Quando o analisador XAML encontra uma `StaticResource`, ele começa a procurar na árvore visual para uma chave correspondente e, em seguida, examina os `ResourceDictionary` na caixa de diálogo `App` classe. Isso permite que os itens em um dicionário de recurso mais profundo na árvore visual para substituir um dicionário de recursos superior na árvore visual. Isso é demonstrado na [ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) exemplo.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fins especiais

O `StaticResource` extensão de marcação faz com que um item a ser recuperado do dicionário quando uma árvore visual é criada durante a `InitializeComponent` chamar. Uma alternativa à `StaticResource` está [ `DynamicResource` ](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), que mantém um link para a chave do dicionário e atualiza o destino quando o item referenciado pelas alterações de chave.

A diferença entre `StaticResource` e `DynamicResource` é demonstrada na [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) exemplo.

Uma propriedade definida pelo `DynamicResource` deve ser retornado por uma propriedade vinculável, conforme discutido em [capítulo 11, a infraestrutura associável](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensões de marcação menos usados

Use o [ `x:Null` ](xref:Xamarin.Forms.Xaml.NullExtension) extensão de marcação para definir uma propriedade `null`.

Use o [ `x:Type` ](xref:Xamarin.Forms.Xaml.TypeExtension) extensão de marcação para definir uma propriedade para um .NET `Type` objeto.

Use [ `x:Array` ](xref:Xamarin.Forms.Xaml.ArrayExtension) para definir uma matriz. Especifique o tipo dos membros da matriz, definindo o [`Type`] propriedade para um `x:Type` extensão de marcação.

## <a name="a-custom-markup-extension"></a>Uma extensão de marcação personalizada

Você pode criar suas próprias extensões de marcação XAML, escrevendo uma classe que implementa o [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) interface com um [ `ProvideValue` ](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) método.

O [ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe atende a esses requisitos. Ele cria um valor do tipo `Color` com base nos valores de propriedades nomeadas `H`, `S`, `L`, e `A`. Essa classe é o primeiro item em uma biblioteca do xamarin. Forms denominada [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que é criado e usado ao longo deste manual.

O [ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) que demonstra como usar a extensão de marcação personalizada e fazer referência a essa biblioteca.

## <a name="related-links"></a>Links relacionados

- [Capítulo 10 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Capítulo 10 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
