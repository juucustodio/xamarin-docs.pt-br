---
title: Resumo do capítulo 7. XAML versus código
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 7. XAML versus código'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: d04012d5d2ea6a7617d5c7559aa3e1532dad15d1
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156906"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumo do capítulo 7. XAML versus código

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Xamarin. Forms dá suporte a uma linguagem de marcação baseada em XML chamada o de Extensible Application Markup Language ou XAML (pronunciada "zammel"). XAML fornece uma alternativa para c# na definição do layout da interface do usuário de um aplicativo xamarin. Forms e definir associações entre os elementos de interface do usuário e os dados subjacentes.

## <a name="properties-and-attributes"></a>Propriedades e atributos

Estruturas e classes do xamarin. Forms se tornam elementos XML em XAML e propriedades dessas classes e estruturas se tornam atributos XML. Para ser instanciada em XAML, uma classe geralmente deve ter um construtor público sem parâmetros. Todas as propriedades definidas em XAML devem ter público `set` acessadores.

Para as propriedades dos tipos de dados básicos (`string`, `double`, `bool`e assim por diante), o analisador XAML usa o padrão de `TryParse` métodos para converter as configurações de atributo para esses tipos. O analisador XAML também facilmente pode lidar com tipos de enumeração, e ele pode combinar os membros de enumeração se o tipo de enumeração é sinalizado com o `Flags` atributo.

Para ajudar o analisador XAML, os tipos mais complexos (ou propriedades desses tipos) podem incluir um [ `TypeConverterAttribute` ](xref:Xamarin.Forms.TypeConverterAttribute) que identifica uma classe que deriva de [ `TypeConverter` ](xref:Xamarin.Forms.TypeConverter) que dá suporte à conversão de valores de cadeia de caracteres para esses tipos. Por exemplo, o [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) converte cores nomes e cadeias de caracteres, como "#rrggbb", em `Color` valores.

## <a name="property-element-syntax"></a>Sintaxe de elemento de propriedade

No XAML, classes e os objetos criados a partir delas são expressas como elementos XML. Eles são conhecidos como *elementos de objeto*. A maioria das propriedades desses objetos são expressas como atributos XML. Eles são chamados *atributos de propriedade*.

Às vezes, uma propriedade deve ser definida como um objeto que não pode ser expresso como uma cadeia de caracteres simple. Nesse caso, o XAML oferece suporte a uma marca chamada de um *elemento de propriedade* que consiste o nome de classe e o nome de propriedade separados por um período. Um elemento de objeto, em seguida, pode aparecer dentro de um par de marcas de elemento de propriedade.

## <a name="adding-a-xaml-page-to-your-project"></a>Adicionando uma página XAML ao seu projeto

Uma biblioteca de classes portátil do xamarin. Forms pode conter uma página XAML quando ele é criado, ou você pode adicionar uma página XAML para um projeto existente. Na caixa de diálogo para adicionar um novo item, escolha o item que se refere a uma página XAML, ou `ContentPage` e XAML. (Não um `ContentView`.)

> [!NOTE] 
> Opções do Visual Studio foram alteradas desde que este capítulo foi escrito.

Dois arquivos são criados: um arquivo XAML com a extensão de nome de arquivo. XAML e um arquivo c# com a extensão. xaml.cs. O arquivo do c# é conhecido como o *de lógica* do arquivo XAML. O arquivo code-behind é uma definição de classe parcial que deriva de `ContentPage`. No momento da compilação, o XAML é analisado e outra definição de classe parcial é gerada para a mesma classe. Essa classe gerada inclui um método chamado `InitializeComponent` que é chamado do construtor de arquivo code-behind.

Durante o tempo de execução, na conclusão do `InitializeComponent` chamar, todos os elementos do arquivo XAML foram instanciados e inicializados como se ele tinham sido criados em código c#.

O elemento raiz no arquivo XAML é `ContentPage`. A marca raiz contém pelo menos duas declarações de namespace XML, um para os elementos de xamarin. Forms e o outro definindo um `x` prefixo para elementos e atributos intrínsecos para todas as implementações de XAML. A marca raiz também contém um `x:Class` atributo que indica o namespace e o nome da classe que deriva de `ContentPage`. Isso coincide com o nome de namespace e classe no arquivo code-behind.

A combinação de código e XAML é demonstrada pelo [ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) exemplo.

## <a name="the-xaml-compiler"></a>O compilador XAML

Xamarin. Forms tem um compilador XAML, mas seu uso é opcional com base no uso de um [ `XamlCompilationAttribute` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Se o XAML não é compilado, o XAML é analisado no momento da compilação e o arquivo XAML é incorporado na PCL, onde também é analisada em tempo de execução. Se o XAML é compilado, o processo de compilação converte a XAML em um formato binário, e o processamento de tempo de execução é mais eficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Especificidade da plataforma no arquivo XAML

No XAML, o [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) classe pode ser usada para selecionar a marcação de dependente de plataforma. Essa é uma classe genérica e deve ser instanciada com um `x:TypeArguments` atributo que corresponde ao tipo de destino. O [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) classe é semelhante, mas usados com muito menos frequência.

O uso de `OnPlatform` foi alterado desde que o livro foi publicado. Ele foi originalmente usado em conjunto com as propriedades nomeadas `iOS`, `Android`, e `WinPhone`. Agora, ele é usado com filho [ `On` ](xref:Xamarin.Forms.On) objetos. Defina as [ `Platform` ](xref:Xamarin.Forms.On.Platform) propriedade como uma cadeia de caracteres consistente com o público `const` campos do [ `Device` ](xref:Xamarin.Forms.Device) classe. Defina a [ `Value` ](xref:Xamarin.Forms.On.Value) propriedade um valor consistente com o `x:TypeArguments` atributo do `OnPlatform` marca.

`OnPlatform` é demonstrada na [ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) amostra, assim chamada porque contém blocos de XAML praticamente idêntica. A existência dessa marcação repetitivas sugere que as técnicas devem estar disponíveis para reduzi-lo.

## <a name="the-content-property-attributes"></a>Os atributos de propriedade de conteúdo

Alguns elementos de propriedade ocorrerem com muita frequência, como o `<ContentPage.Content>` marca no elemento raiz de um `ContentPage`, ou o `<StackLayout.Children>` marca que inclui os filhos do `StackLayout`.

Cada classe é permitida para identificar uma propriedade com um [ `ContentPropertyAttribute` ](xref:Xamarin.Forms.ContentPropertyAttribute) na classe. Para essa propriedade, as marcas de elemento de propriedade não são necessárias. `ContentPage` define sua propriedade de conteúdo como `Content`, e `Layout<T>` (a classe da qual `StackLayout` deriva) define sua propriedade de conteúdo como `Children`. Essas marcas de elemento de propriedade não são necessárias.

O elemento de propriedade de `Label` é `Text`.

## <a name="formatted-text"></a>Texto formatado

O [ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) exemplo contém vários exemplos de configuração do `Text` e `FormattedText` propriedades de `Label`. No XAML, `Span` objetos aparecem como filhos do `FormattedString` objeto.

 Quando uma cadeia de caracteres de várias linhas é definida como o `Text` propriedade, os caracteres de final de linha são convertidos em caracteres de espaço, mas os caracteres de final de linha são preservados quando uma cadeia de caracteres multilinha é exibido como o conteúdo do `Label` ou `Label.Text` marcas:

 [![Captura de tela tripla de variações de texto de compartilhamento](images/ch07fg03-small.png "variações de texto formatado")](images/ch07fg03-large.png#lightbox "variações de texto formatado")

## <a name="related-links"></a>Links relacionados

- [Capítulo 7 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Exemplos do capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Capítulo 7 F # de exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md)