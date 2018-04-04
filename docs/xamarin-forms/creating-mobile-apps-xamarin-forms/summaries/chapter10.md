---
title: Resumo do capítulo 10. Extensões de marcação XAML
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 3223bc20448bd8354e84dc67ee64a8dc435f7667
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumo do capítulo 10. Extensões de marcação XAML

Normalmente, o analisador XAML Converte qualquer cadeia de caracteres definida como um valor de atributo para o tipo da propriedade com base em conversões padrão para os tipos de dados básicos do .NET, ou um [ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/) derivativo anexada à propriedade ou o seu tipo com um [`TypeConverterAttribute`](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/).

Mas, às vezes, é conveniente definir um atributo de uma fonte diferente, por exemplo, um item em um dicionário ou o valor de uma propriedade ou campo estático, ou de um cálculo de algum tipo.

Esse é o trabalho de um *extensão de marcação XAML*. Apesar do nome, extensões de marcação XAML são *não* uma extensão do XML. XAML é sempre um XML válido.

## <a name="the-code-infrastructure"></a>A infraestrutura de código

Uma extensão de marcação XAML é uma classe que implementa o [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) interface. Geralmente, essa classe tem a palavra `Extension` no final do seu nome mas geralmente aparece no XAML sem esse sufixo.

As extensões de marcação XAML a seguir são suportadas por todas as implementações de XAML:

- `x:Static` Com suporte [`StaticExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/)
- `x:Reference` Com suporte [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/)
- `x:Type` Com suporte [`TypeExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/)
- `x:Null` Com suporte [`NullExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/)
- `x:Array` Com suporte [`ArrayExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/)

Essas quatro extensões de marcação XAML têm suporte por muitas implementações de XAML, incluindo xamarin. Forms:

- `StaticResource` Com suporte [`StaticResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/)
- `DynamicResource` Com suporte [`DynamicResourceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/)
- `Binding` suporte [ `BindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) &mdash;discutidos [capítulo 16. Associação de dados](#chapter16)
- `TemplateBinding` suporte [ `TemplateBindingExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/) &mdash;não abordadas no catálogo

Uma extensão de marcação XAML adicional está incluída no xamarin. Forms em conexão com [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/):

- [`ConstraintExpression`](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/)&mdash;o catálogo não são abordados

## <a name="accessing-static-members"></a>Acessando membros estáticos

Use o [ `x:Static` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) elemento para definir um atributo para o valor de um membro de enumeração, campo ou propriedade estático público. Definir o [ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/) propriedade para o membro estático. É geralmente mais fácil especificar `x:Static` e o nome de membro entre chaves. O nome do `Member` propriedade precisam ser incluídos, não apenas o próprio membro. Essa sintaxe comum é mostrada no [ **SharedStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) exemplo. Os próprios campos estáticos são definidos no [ `AppConstants` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Essa técnica permite estabelecer constantes usadas por meio de um programa.

Com uma declaração de namespace XML adicional, você pode fazer referência públicas propriedades estáticas, campos ou membros de enumeração definidos no .NET framework, conforme demonstrado no [ **SystemStatics** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) exemplo .

## <a name="resource-dictionaries"></a>Dicionários de recurso

O `VisualElement` classe define uma propriedade chamada [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) que podem ser definidas para um objeto do tipo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Em XAML, você pode armazenar os itens neste dicionário e identificá-los com o `x:Key` atributo. Os itens armazenados no dicionário de recursos são compartilhados entre todas as referências para o item.

### <a name="staticresource-for-most-purposes"></a>StaticResource geral

Na maioria dos casos você usará o [ `StaticResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/) extensão de marcação para fazer referência a um item de dicionário de recurso, conforme demonstrado a [ **ResourceSharing** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) exemplo . Você pode usar um `StaticResourceExtension` elemento ou `StaticResource` entre chaves:

[![Captura de tela tripla de compartilhamento de recursos](images/ch10fg03-small.png "compartilhamento de recursos")](images/ch10fg03-large.png#lightbox "compartilhamento de recursos")

Não confunda o `x:Static` extensão de marcação e o `StaticResource` extensão de marcação.

### <a name="a-tree-of-dictionaries"></a>Uma árvore de dicionários

Quando o analisador XAML encontra um `StaticResource`, ele começa a procurar na árvore visual para uma chave correspondente e, em seguida, procura no `ResourceDictionary` do aplicativo `App` classe. Isso permite que os itens em um dicionário de recursos mais profundo na árvore visual para substituir um dicionário de recursos superior na árvore visual. Isso é demonstrado no [ **ResourceTrees** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) exemplo.

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fins especiais

O `StaticResource` extensão de marcação faz com que um item a ser recuperado do dicionário quando uma árvore visual é criada durante a `InitializeComponent` chamar. Uma alternativa para `StaticResource` é [ `DynamicResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.DynamicResourceExtension/), que mantém um link para a chave de dicionário e atualiza o destino quando o item referenciado pelas alterações de chave.

A diferença entre `StaticResource` e `DynamicResource` é demonstrada no [ **DynamicVsStatic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) exemplo.

Uma propriedade definida pelo `DynamicResource` deve ser feito por uma propriedade ligável conforme discutido em [capítulo 11, a infraestrutura associável](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensões de marcação menos usada

Use o [ `x:Null` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/) extensão de marcação para definir uma propriedade `null`.

Use o [ `x:Type` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/) extensão de marcação para definir uma propriedade para um .NET `Type` objeto.

Use [ `x:Array` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/) para definir uma matriz. Especificar o tipo dos membros da matriz, definindo o [`Type`] propriedade para um `x:Type` extensão de marcação.

## <a name="a-custom-markup-extension"></a>Uma extensão de marcação personalizada

Você pode criar suas próprias extensões de marcação XAML, escrevendo uma classe que implementa o [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) interface com um [ `ProvideValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue/p/System.IServiceProvider/) método.

O [ `HslColorExtension` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe atenda a esses requisitos. Ele cria um valor do tipo `Color` com base nos valores de propriedades denominadas `H`, `S`, `L`, e `A`. Essa classe é o primeiro item em uma biblioteca do xamarin. Forms denominado [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que é criada e usada ao longo deste livro.

O [ **CustomExtensionDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) demonstra como fazer referência a esta biblioteca e usar a extensão de marcação personalizada.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 10 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Exemplos de capítulo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
