---
Título: "Resumo do capítulo 10. Extensões de marcação XAML "Descrição:" Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 10. Extensões de marcação XAML "MS. Prod: xamarin MS. Technology: xamarin-Forms MS. AssetID: 575EAE55-BD4D-470F-A583-3D065FA102E2 autor: davidbritch MS. Author: dabritch MS. Date: 07/19/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-10-xaml-markup-extensions"></a>Resumo do capítulo 10. Extensões de marcação XAML

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)

Normalmente, o analisador XAML converte qualquer conjunto de cadeias de caracteres como um valor de atributo para o tipo da propriedade com base nas conversões padrão para os tipos de dados básicos do .NET ou um [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) derivativo anexado à propriedade ou seu tipo com um [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) .

Mas, às vezes, é conveniente definir um atributo de uma fonte diferente, por exemplo, um item em um dicionário ou o valor de uma propriedade ou campo estático ou de um cálculo de algum tipo.

Esse é o trabalho de uma *extensão de marcação XAML*. Apesar do nome, as extensões de marcação XAML *não* são uma extensão para XML. O XAML é sempre XML válido.

## <a name="the-code-infrastructure"></a>A infraestrutura de código

Uma extensão de marcação XAML é uma classe que implementa a [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) interface. Essa classe geralmente tem a palavra `Extension` no final de seu nome, mas geralmente aparece em XAML sem esse sufixo.

As seguintes extensões de marcação XAML são suportadas por todas as implementações do XAML:

- `x:Static`com suporte pelo[`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension)
- `x:Reference`com suporte pelo[`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)
- `x:Type`com suporte pelo[`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension)
- `x:Null`com suporte pelo[`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension)
- `x:Array`com suporte pelo[`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension)

Essas quatro extensões de marcação XAML são suportadas por muitas implementações do XAML, incluindo Xamarin.Forms :

- `StaticResource`com suporte pelo[`StaticResourceExtension`](xref:Xamarin.Forms.Xaml.StaticResourceExtension)
- `DynamicResource`com suporte pelo[`DynamicResourceExtension`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension)
- `Binding`com suporte [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) &mdash; discutido no [capítulo 16. Associação de dados](chapter16.md)
- `TemplateBinding`com suporte [`TemplateBindingExtension`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) &mdash; não abordado no livro

Uma extensão de marcação XAML adicional está incluída no Xamarin.Forms em conexão com [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) :

- [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)&mdash;Não abordado no livro

## <a name="accessing-static-members"></a>Acessando membros estáticos

Use o [`x:Static`](xref:Xamarin.Forms.Xaml.StaticExtension) elemento para definir um atributo como o valor de uma propriedade estática pública, um campo ou um membro de enumeração. Defina a [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) propriedade como o membro estático. Geralmente, é mais fácil especificar `x:Static` e o nome do membro entre chaves. O nome da `Member` propriedade não precisa ser incluído, apenas o membro em si. Essa sintaxe comum é mostrada no exemplo [**SharedStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SharedStatics) . Os próprios campos estáticos são definidos na [`AppConstants`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter10/SharedStatics/SharedStatics/SharedStatics/AppConstants.cs) classe. Essa técnica permite estabelecer constantes usadas por meio de um programa.

Com uma declaração de namespace XML adicional, você pode fazer referência a propriedades estáticas públicas, campos ou membros de enumeração definidos no .NET Framework, conforme demonstrado no exemplo de [**SystemStatics**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/SystemStatics) .

## <a name="resource-dictionaries"></a>Dicionários de recurso

A `VisualElement` classe define uma propriedade chamada [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) que você pode definir como um objeto do tipo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . No XAML, você pode armazenar itens neste dicionário e identificá-los com o `x:Key` atributo. Os itens armazenados no dicionário de recursos são compartilhados entre todas as referências ao item.

### <a name="staticresource-for-most-purposes"></a>StaticResource para a maioria das finalidades

Na maioria dos casos, você usará a [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) extensão de marcação para fazer referência a um item do dicionário de recursos, conforme demonstrado pelo exemplo de [**ResourceSharing**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceSharing) . Você pode usar um `StaticResourceExtension` elemento ou `StaticResource` entre chaves:

[![Captura de tela tripla de compartilhamento de recursos](images/ch10fg03-small.png "Compartilhamento de Recursos")](images/ch10fg03-large.png#lightbox "Compartilhamento de Recursos")

Não confunda a `x:Static` extensão de marcação e a `StaticResource` extensão de marcação.

### <a name="a-tree-of-dictionaries"></a>Uma árvore de dicionários

Quando o analisador XAML encontra um `StaticResource` , ele começa a Pesquisar a árvore visual em busca de uma chave correspondente e, em seguida, procura na `ResourceDictionary` classe do aplicativo `App` . Isso permite que os itens em um dicionário de recursos mais profundo na árvore visual substituam um dicionário de recursos mais alto na árvore visual. Isso é demonstrado no exemplo de [**ResourceTrees**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/ResourceTrees) .

### <a name="dynamicresource-for-special-purposes"></a>DynamicResource para fins especiais

A `StaticResource` extensão de marcação faz com que um item seja recuperado do dicionário quando uma árvore visual é criada durante a `InitializeComponent` chamada. Uma alternativa `StaticResource` é [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) , que mantém um link para a chave de dicionário e atualiza o destino quando o item referenciado pela chave é alterado.

A diferença entre `StaticResource` e `DynamicResource` é demonstrada no exemplo de [**DynamicVsStatic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/DynamicVsStatic) .

Uma propriedade definida por `DynamicResource` deve ser apoiada por uma propriedade vinculável, conforme discutido no [capítulo 11, a infraestrutura vinculável](chapter11.md).

## <a name="lesser-used-markup-extensions"></a>Extensões de marcação menos usadas

Use a [`x:Null`](xref:Xamarin.Forms.Xaml.NullExtension) extensão de marcação para definir uma propriedade como `null` .

Use a [`x:Type`](xref:Xamarin.Forms.Xaml.TypeExtension) extensão de marcação para definir uma propriedade para um `Type` objeto .net.

Use [`x:Array`](xref:Xamarin.Forms.Xaml.ArrayExtension) para definir uma matriz. Especifique o tipo dos membros da matriz definindo a propriedade [ `Type` ] como uma `x:Type` extensão de marcação.

## <a name="a-custom-markup-extension"></a>Uma extensão de marcação personalizada

Você pode criar suas próprias extensões de marcação XAML escrevendo uma classe que implementa a [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) interface com um [`ProvideValue`](xref:Xamarin.Forms.Xaml.IMarkupExtension.ProvideValue(System.IServiceProvider)) método.

A [`HslColorExtension`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/HslColorExtension.cs) classe atende a esses requisitos. Ele cria um valor do tipo `Color` com base nos valores das propriedades nomeadas `H` , `S` , `L` e `A` . Essa classe é o primeiro item em uma Xamarin.Forms biblioteca chamada [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) que é criado e usado no decorrer deste livro.

O exemplo [**CustomExtensionDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10/CustomExtensionDemo) demonstra como fazer referência a essa biblioteca e usar a extensão de marcação personalizada.

## <a name="related-links"></a>Links relacionados

- [Capítulo 10 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf)
- [Exemplos do capítulo 10](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter10)
- [Extensões de marcação do XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
