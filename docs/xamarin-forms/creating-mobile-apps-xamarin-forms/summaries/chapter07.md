---
title: Resumo do capítulo 7. XAML versus código
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 7. XAML versus código'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334718"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumo do capítulo 7. XAML versus código

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Xamarin. Forms dá suporte a uma linguagem de marcação baseada em XML chamada o de Extensible Application Markup Language ou XAML (pronunciada "zammel"). XAML fornece uma alternativa para c# na definição do layout da interface do usuário de um aplicativo xamarin. Forms e definir associações entre os elementos de interface do usuário e os dados subjacentes.

## <a name="properties-and-attributes"></a>Propriedades e atributos

Estruturas e classes do xamarin. Forms se tornam elementos XML em XAML e propriedades dessas classes e estruturas se tornam atributos XML. Para ser instanciada em XAML, uma classe geralmente deve ter um construtor público sem parâmetros. Todas as propriedades definidas em XAML devem ter acessadores públicos `set`.

Para propriedades dos tipos de dados básicos (`string`, `double`, `bool`e assim por diante), o analisador XAML usa os métodos de `TryParse` padrão para converter as configurações de atributo para esses tipos. O analisador XAML também pode facilmente lidar com tipos de enumeração e pode combinar membros de enumeração se o tipo de enumeração for sinalizado com o atributo `Flags`.

Para auxiliar o analisador XAML, tipos mais complexos (ou propriedades desses tipos) podem incluir um [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) que identifica uma classe derivada de [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) que dá suporte à conversão de valores de cadeia de caracteres para esses tipos. Por exemplo, o [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) converte nomes de cor e cadeias de caracteres, como "#rrggbb", em valores `Color`.

## <a name="property-element-syntax"></a>Sintaxe de elemento de propriedade

No XAML, classes e os objetos criados a partir delas são expressas como elementos XML. Eles são conhecidos como *elementos de objeto*. A maioria das propriedades desses objetos são expressas como atributos XML. Esses são chamados de *atributos de propriedade*.

Às vezes, uma propriedade deve ser definida como um objeto que não pode ser expresso como uma cadeia de caracteres simple. Nesse caso, o XAML dá suporte a uma marca chamada um *elemento de propriedade* que consiste no nome da classe e no nome da propriedade separados por um ponto. Um elemento de objeto, em seguida, pode aparecer dentro de um par de marcas de elemento de propriedade.

## <a name="adding-a-xaml-page-to-your-project"></a>Adicionando uma página XAML ao seu projeto

Uma biblioteca de classes portátil do xamarin. Forms pode conter uma página XAML quando ele é criado, ou você pode adicionar uma página XAML para um projeto existente. Na caixa de diálogo para adicionar um novo item, escolha o item que se refere a uma página XAML, ou `ContentPage` e XAML. (Não é um `ContentView`.)

> [!NOTE]
> Opções do Visual Studio foram alteradas desde que este capítulo foi escrito.

Dois arquivos são criados: um arquivo XAML com a extensão de nome de arquivo. XAML e um arquivo c# com a extensão. xaml.cs. O C# arquivo é geralmente chamado de *code-behind* do arquivo XAML. O arquivo code-behind é uma definição de classe parcial que deriva de `ContentPage`. No momento da compilação, o XAML é analisado e outra definição de classe parcial é gerada para a mesma classe. Essa classe gerada inclui um método chamado `InitializeComponent` que é chamado a partir do construtor do arquivo code-behind.

Durante o tempo de execução, na conclusão da chamada de `InitializeComponent`, todos os elementos do arquivo XAML foram instanciados e inicializados da mesma forma como se tivessem sido C# criados no código.

O elemento raiz no arquivo XAML é `ContentPage`. A marca raiz contém pelo menos duas declarações de namespace XML, uma para os elementos Xamarin. Forms e outra definindo um prefixo de `x` para elementos e atributos intrínsecos a todas as implementações de XAML. A marca raiz também contém um atributo `x:Class` que indica o namespace e o nome da classe que deriva de `ContentPage`. Isso coincide com o nome de namespace e classe no arquivo code-behind.

A combinação de XAML e código é demonstrada pelo exemplo de [**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) .

## <a name="the-xaml-compiler"></a>O compilador XAML

O Xamarin. Forms tem um compilador XAML, mas seu uso é opcional com base no uso de um [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute). Se o XAML não é compilado, o XAML é analisado no momento da compilação e o arquivo XAML é incorporado na PCL, onde também é analisada em tempo de execução. Se o XAML é compilado, o processo de compilação converte a XAML em um formato binário, e o processamento de tempo de execução é mais eficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Especificidade da plataforma no arquivo XAML

Em XAML, a classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) pode ser usada para selecionar a marcação dependente de plataforma. Essa é uma classe genérica e deve ser instanciada com um atributo `x:TypeArguments` que corresponda ao tipo de destino. A classe [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) é semelhante, mas é usada com muito menos frequência.

O uso de `OnPlatform` foi alterado desde que o livro foi publicado. Ele foi usado originalmente em conjunto com propriedades chamadas `iOS`, `Android`e `WinPhone`. Ele agora é usado com objetos de [`On`](xref:Xamarin.Forms.On) filhos. Defina a propriedade [`Platform`](xref:Xamarin.Forms.On.Platform) como uma cadeia de caracteres consistente com os campos de `const` públicos da classe [`Device`](xref:Xamarin.Forms.Device) . Defina a propriedade [`Value`](xref:Xamarin.Forms.On.Value) com um valor consistente com o atributo `x:TypeArguments` da marca `OnPlatform`.

`OnPlatform` é demonstrado no exemplo [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) , portanto, chamado porque contém blocos de XAML quase idêntico. A existência dessa marcação repetitivas sugere que as técnicas devem estar disponíveis para reduzi-lo.

## <a name="the-content-property-attributes"></a>Os atributos de propriedade de conteúdo

Alguns elementos de propriedade ocorrem com muita frequência, como a marca de `<ContentPage.Content>` no elemento raiz de um `ContentPage`ou a marca `<StackLayout.Children>` que inclui os filhos de `StackLayout`.

Cada classe tem permissão para identificar uma propriedade com um [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) na classe. Para essa propriedade, as marcas de elemento de propriedade não são necessárias. `ContentPage` define sua propriedade Content como `Content`e `Layout<T>` (a classe da qual `StackLayout` deriva) define sua propriedade Content como `Children`. Essas marcas de elemento de propriedade não são necessárias.

O elemento Property de `Label` é `Text`.

## <a name="formatted-text"></a>Texto formatado

O exemplo [**Textvariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contém vários exemplos de definição das propriedades `Text` e `FormattedText` de `Label`. Em XAML, os objetos `Span` aparecem como filhos do objeto `FormattedString`.

 Quando uma cadeia de caracteres de várias linhas é definida como a propriedade `Text`, os caracteres de fim de linha são convertidos em caracteres de espaço, mas os caracteres de fim de linha são preservados quando uma cadeia de caracteres de várias linhas é exibida como conteúdo das marcas `Label` ou `Label.Text`:

 [![Captura de tela tripla de compartilhamento de variações de texto](images/ch07fg03-small.png "Variações de texto formatado")](images/ch07fg03-large.png#lightbox "Variações de texto formatado")

## <a name="related-links"></a>Links relacionados

- [Capítulo 7 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Exemplos do capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Exemplo do F# capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
