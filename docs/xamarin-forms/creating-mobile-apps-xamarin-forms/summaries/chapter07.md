---
title: Resumo do capítulo 7. XAML e código
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 97f1ad1f818c74a294421f223c4cea0123b83373
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumo do capítulo 7. XAML e código

Xamarin. Forms oferece suporte a uma linguagem de marcação baseada em XML chamada Extensible Application Markup Language ou XAML (pronunciado "zammel"). XAML fornece uma alternativa para c# para definir o layout da interface do usuário de um aplicativo xamarin. Forms e definir associações entre elementos de interface do usuário e dados subjacentes.

## <a name="properties-and-attributes"></a>Propriedades e atributos

Estruturas e classes do xamarin. Forms tornam-se elementos XML no XAML, e propriedades dessas classes e estruturas se tornam atributos XML. Para ser instanciado em XAML, uma classe geralmente deve ter um construtor público sem parâmetros. Quaisquer propriedades definidas em XAML devem ter pública `set` acessadores.

Para propriedades de tipos de dados básicos (`string`, `double`, `bool`, e assim por diante), o analisador XAML usa o padrão de `TryParse` métodos para converter esses tipos de configurações de atributo. O analisador XAML facilmente pode lidar com tipos de enumeração e ele pode combinar os membros de enumeração se o tipo de enumeração será sinalizado com o `Flags` atributo.

Para ajudar o analisador XAML, tipos mais complexos (ou propriedades desses tipos) podem incluir um [ `TypeConverterAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverterAttribute/) que identifica uma classe que deriva de [ `TypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TypeConverter/) que dá suporte à conversão de valores de cadeia de caracteres para esses tipos. Por exemplo, o [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/) converte cadeias de caracteres, como "#rrggbb" e nomes de cor em `Color` valores.

## <a name="property-element-syntax"></a>Sintaxe de elemento de propriedade

No XAML, classes e os objetos criados a partir delas são expressas como elementos XML. Eles são conhecidos como *elementos do objeto*. A maioria das propriedades desses objetos são expressos como atributos XML. Esses são chamados de *atributos da propriedade*.

Às vezes, uma propriedade deve ser definida como um objeto que não pode ser expresso como uma cadeia de caracteres simple. Nesse caso, o XAML oferece suporte a uma marca chamada um *elemento de propriedade* que consiste o nome da classe e o nome de propriedade separado por um ponto. Um elemento de objeto, em seguida, pode aparecer dentro de um par de marcas de elemento de propriedade.

## <a name="adding-a-xaml-page-to-your-project"></a>Adicionando uma página XAML ao seu projeto

Uma biblioteca de classes portátil xamarin. Forms pode conter uma página XAML quando ele é criado, ou você pode adicionar uma página XAML para um projeto existente. Na caixa de diálogo para adicionar um novo item, escolha o item que se refere a uma página XAML, ou `ContentPage` e XAML. (Não um `ContentView`.)

Dois arquivos são criados: um arquivo XAML com a extensão de nome de arquivo. XAML e um arquivo c# com a extensão. xaml.cs. O arquivo c# é conhecido como o *por trás do código* do arquivo XAML. O arquivo code-behind é uma definição de classe parcial que deriva de `ContentPage`. No momento da compilação, o XAML é analisado e outra definição de classe parcial é gerada para a mesma classe. Essa classe gerada inclui um método chamado `InitializeComponent` que é chamado do construtor de arquivo code-behind.

Durante a execução, ao final de `InitializeComponent` chamar, todos os elementos do arquivo XAML foram instanciados e inicializados como se foi criados em código c#.

O elemento raiz no arquivo XAML é `ContentPage`. A marca raiz contém pelo menos duas declarações de namespace XML, um para os elementos de xamarin. Forms e a outros definindo um `x` prefixo para elementos e atributos intrínsecos para todas as implementações de XAML. A marca raiz também contém um `x:Class` atributo que indica o namespace e o nome da classe que deriva de `ContentPage`. Isso corresponde ao nome de namespace e classe no arquivo code-behind.

A combinação de XAML e código é demonstrada pelo [ **CodePlusXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) exemplo.

## <a name="the-xaml-compiler"></a>O compilador XAML

Xamarin. Forms tem um compilador XAML, mas seu uso é opcional, com base no uso de um [ `XamlCompilationAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationAttribute/). Se o XAML não está compilado, o XAML é analisado em tempo de compilação e o arquivo XAML é inserido em PCL, onde também é analisada em tempo de execução. Se o XAML é compilado, o processo de compilação converte o XAML em um formato binário, e o processamento de tempo de execução é mais eficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Especificidade de plataforma no arquivo XAML

Em XAML, o [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) classe pode ser usada para selecionar a marcação dependente de plataforma. Essa é uma classe genérica e deve ser instanciada com um `x:TypeArguments` atributo que corresponde ao tipo de destino. O [ `OnIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnIdiom%3CT%3E/) classe é semelhante, mas usada muito menos frequência.

O uso de `OnPlatform` foi alterado desde que o catálogo foi publicado. Foi originalmente usado em conjunto com propriedades chamadas `iOS`, `Android`, e `WinPhone`. Agora, ele é usado com filho [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) objetos. Definir o [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) propriedade como uma cadeia de caracteres consistente com o público `const` campos do [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe. Definir o [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Value/) propriedade para um valor consistente com o `x:TypeArguments` atributo do `OnPlatform` marca.

`OnPlatform` é demonstrada no [ **ScaryColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) exemplo, assim chamado porque ela contém blocos de XAML quase idêntico. A existência dessa marcação repetitivas sugere que técnicas devem estar disponíveis para reduzi-lo.

## <a name="the-content-property-attributes"></a>Os atributos de propriedade de conteúdo

Alguns elementos de propriedade ocorrem com bastante frequência, como o `<ContentPage.Content>` marca no elemento raiz de um `ContentPage`, ou o `<StackLayout.Children>` marca que inclui os filhos de `StackLayout`.

Cada classe é permitida para identificar uma propriedade com um [ `ContentPropertyAttribute` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPropertyAttribute/) na classe. Para essa propriedade, as marcas de elemento de propriedade não são necessárias. `ContentPage` Define a propriedade como seu conteúda `Content`, e `Layout<T>` (a classe da qual `StackLayout` deriva) define sua propriedade de conteúdo como `Children`. Essas marcas de elemento de propriedade não são necessárias.

O elemento de propriedade de `Label` é `Text`.

## <a name="formatted-text"></a>Texto formatado

O [ **TextVariations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) exemplo contém vários exemplos de configuração de `Text` e `FormattedText` propriedades de `Label`. Em XAML, `Span` objetos aparecem como filhos do `FormattedString` objeto.

 Quando uma cadeia de caracteres de várias linhas é definida como o `Text` propriedade, caracteres de final de linha são convertidos em caracteres de espaço, mas os caracteres de final de linha são preservados quando uma cadeia de caracteres de várias linhas é exibida como conteúdo do `Label` ou `Label.Text` marcas:

 [![Captura de tela tripla de variações de texto de compartilhamento](images/ch07fg03-small.png "variações de texto formatado")](images/ch07fg03-large.png#lightbox "variações de texto formatado")



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 7 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Exemplos de capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Exemplo de capítulo 7 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
