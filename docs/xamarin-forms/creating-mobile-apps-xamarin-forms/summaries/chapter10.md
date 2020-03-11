---
title: Resumo do capítulo 10. Extensões de marcação XAML
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 10. Extensões de marcação XAML'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 575EAE55-BD4D-470F-A583-3D065FA102E2
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 076e9f5155492e5a69d906c587b24495fe39d3f1
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334260"
---
# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumo do capítulo 10. Extensões de marcação XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalmente, o analisador XAML converte qualquer conjunto de cadeias de caracteres como um valor de atributo para o tipo da propriedade com base nas conversões padrão para os tipos de dados básicos do .NET, ou um [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) derivativo anexado à propriedade ou seu tipo com um [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute).

Mas, às vezes, é conveniente definir um atributo de uma fonte diferente, por exemplo, um item em um dicionário ou o valor da propriedade ou campo, ou de um cálculo de algum tipo.

Esse é o trabalho de uma *extensão de marcação XAML*. Apesar do nome, as extensões de marcação XAML *não* são uma extensão para XML. XAML é sempre XML legal.

## <a name="the-code-infrastructure"></a>A infraestrutura de código

Uma extensão de marcação XAML é uma classe que implementa a interface [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) . Essa classe geralmente tem a palavra `Extension` no final do nome, mas geralmente aparece em XAML sem esse sufixo.

As seguintes extensões de marcação XAML têm suporte de todas as implementações de XAML:

- `x:Static` com suporte pelo [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference` com suporte pelo [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type` com suporte pelo [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null` com suporte pelo [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array` com suporte pelo [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Essas quatro extensões de marcação XAML têm suporte por muitas implementações de XAML, incluindo o xamarin. Forms:

- `StaticResource` com suporte pelo [`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource` com suporte pelo [`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding` suportadas pelo [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)&mdash;discutidas no [capítulo 16. Associação de dados](chapter16.md)
- `TemplateBinding` com suporte pelo [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)&mdash;não cobertos no livro

Uma extensão de marcação XAML adicional está incluída no Xamarin. Forms em conexão com o [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;não cobertos no livro

## <a name="accessing-static-members"></a>Acesso a membros estáticos

Use o elemento [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) para definir um atributo como o valor de uma propriedade estática pública, um campo ou um membro de enumeração. Defina a propriedade [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) como o membro estático. Geralmente, é mais fácil especificar `x:Static` e o nome do membro entre chaves. O nome da propriedade `Member` não precisa ser incluído, apenas o membro em si. Essa sintaxe comum é mostrada no exemplo [**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) . Os próprios campos estáticos são definidos na classe [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) . Essa técnica permite que você estabeleça constantes usadas por meio de um programa.

Com uma declaração de namespace XML adicional, você pode fazer referência a propriedades estáticas públicas, campos ou membros de enumeração definidos no .NET Framework, conforme demonstrado no exemplo de [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) .

## <a name="resource-dictionaries"></a>Dicionários de recurso

A classe `VisualElement` define uma propriedade chamada [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) que você pode definir como um objeto do tipo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). No XAML, você pode armazenar itens neste dicionário e identificá-los com o atributo `x:Key`. Os itens armazenados no dicionário de recursos são compartilhados entre todas as referências para o item.

### <a name="staticresource-for-most-purposes"></a>StaticResource para a maioria das finalidades

Na maioria dos casos, você usará a extensão de marcação [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) para fazer referência a um item do dicionário de recursos, conforme demonstrado pelo exemplo de [**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) . Você pode usar um elemento `StaticResourceExtension` ou `StaticResource` entre chaves:

[![Captura de tela tripla de compartilhamento de recursos](images/ch10fg03-small.png "Compartilhamento de recursos")](images/ch10fg03-large.png#lightbox "Compartilhamento de recursos")

Não confunda a extensão de marcação de `x:Static` e a extensão de marcação `StaticResource`.

### <a name="a-tree-of-dictionaries"></a>Uma árvore de dicionários

Quando o analisador XAML encontra uma `StaticResource`, ele começa a Pesquisar a árvore visual em busca de uma chave correspondente e, em seguida, examina a `ResourceDictionary` na classe `App` do aplicativo. Isso permite que os itens em um dicionário de recurso mais profundo na árvore visual para substituir um dicionário de recursos superior na árvore visual. Isso é demonstrado no exemplo de [**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) .

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fins especiais

A extensão de marcação de `StaticResource` faz com que um item seja recuperado do dicionário quando uma árvore visual é criada durante a chamada de `InitializeComponent`. Uma alternativa para `StaticResource` é [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension), que mantém um link para a chave de dicionário e atualiza o destino quando o item referenciado pela chave é alterado.

A diferença entre `StaticResource` e `DynamicResource` é demonstrada no exemplo [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) .

Uma propriedade definida por `DynamicResource` deve ser apoiada por uma propriedade vinculável, conforme discutido no [capítulo 11, a infraestrutura vinculável](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensões de marcação menos usados

Use a extensão de marcação [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) para definir uma propriedade como `null`.

Use a extensão de marcação [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) para definir uma propriedade para um objeto .net `Type`.

Use [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) para definir uma matriz. Especifique o tipo dos membros da matriz definindo a propriedade [`Type`] como uma extensão de marcação `x:Type`.

## <a name="a-custom-markup-extension"></a>Uma extensão de marcação personalizada

Você pode criar suas próprias extensões de marcação XAML escrevendo uma classe que implementa a interface [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) com um método [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) .

A classe [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) satisfaz esses requisitos. Ele cria um valor do tipo `Color` com base nos valores das propriedades nomeadas `H`, `S`, `L`e `A`. Essa classe é o primeiro item em uma biblioteca Xamarin. Forms chamada [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que é criada e usada no decorrer deste livro.

O exemplo [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) demonstra como fazer referência a essa biblioteca e usar a extensão de marcação personalizada.

## <a name="related-links"></a>Links relacionados

- [Capítulo 10 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Exemplos do capítulo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
