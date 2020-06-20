---
title: Resumo do capítulo 7. XAML versus código
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 7. XAML versus código'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b92988e1e838072fca0d8a284455a62db05e757
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136847"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumo do capítulo 7. XAML versus código

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

Xamarin.Formsdá suporte a uma linguagem de marcação baseada em XML chamada de Extensible Application Markup Language ou XAML (pronuncia-se "zammel"). O XAML fornece uma alternativa ao C# na definição do layout da interface do usuário de um Xamarin.Forms aplicativo e na definição de associações entre elementos da interface do usuário e dados subjacentes.

## <a name="properties-and-attributes"></a>Propriedades e atributos

Xamarin.Formsclasses e estruturas se tornam elementos XML em XAML, e as propriedades dessas classes e estruturas se tornam atributos XML. Para ser instanciado em XAML, uma classe geralmente deve ter um construtor público sem parâmetros. Todas as propriedades definidas em XAML devem ter `set` acessadores públicos.

Para propriedades dos tipos de dados básicos ( `string` , `double` , `bool` e assim por diante), o analisador XAML usa os `TryParse` métodos padrão para converter as configurações de atributo para esses tipos. O analisador XAML também pode facilmente lidar com tipos de enumeração e pode combinar membros de enumeração se o tipo de enumeração for sinalizado com o `Flags` atributo.

Para auxiliar o analisador XAML, tipos mais complexos (ou propriedades desses tipos) podem incluir um [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) que identifica uma classe que é derivada da [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) qual dá suporte à conversão de valores de cadeia de caracteres para esses tipos. Por exemplo, o [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) converte os nomes de cor e cadeias de caracteres, como "#rrggbb", em `Color` valores.

## <a name="property-element-syntax"></a>Sintaxe de elemento de propriedade

Em XAML, classes e objetos criados a partir deles são expressos como elementos XML. Eles são conhecidos como *elementos de objeto*. A maioria das propriedades desses objetos é expressa como atributos XML. Esses são chamados de *atributos de propriedade*.

Às vezes, uma propriedade deve ser definida como um objeto que não pode ser expresso como uma cadeia de caracteres simples. Nesse caso, o XAML dá suporte a uma marca chamada um *elemento de propriedade* que consiste no nome da classe e no nome da propriedade separados por um ponto. Um elemento Object pode aparecer em um par de marcas de elemento de propriedade.

## <a name="adding-a-xaml-page-to-your-project"></a>Adicionando uma página XAML ao seu projeto

Uma Xamarin.Forms biblioteca de classes portátil pode conter uma página XAML quando é criada pela primeira vez, ou você pode adicionar uma página XAML a um projeto existente. Na caixa de diálogo para adicionar um novo item, escolha o item que se refere a uma página XAML ou `ContentPage` XAML. (Não é um `ContentView` .)

> [!NOTE]
> As opções do Visual Studio foram alteradas desde que este capítulo foi escrito.

Dois arquivos são criados: um arquivo XAML com a extensão de nome de arquivo. XAML e um arquivo C# com a extensão. xaml.cs. O arquivo C# é geralmente chamado de *code-behind* do arquivo XAML. O arquivo code-behind é uma definição de classe parcial que deriva de `ContentPage` . No momento da compilação, o XAML é analisado e outra definição de classe parcial é gerada para a mesma classe. Essa classe gerada inclui um método chamado `InitializeComponent` que é chamado a partir do construtor do arquivo code-behind.

Durante o tempo de execução, na conclusão da `InitializeComponent` chamada, todos os elementos do arquivo XAML foram instanciados e inicializados da mesma forma como se tivessem sido criados no código C#.

O elemento raiz no arquivo XAML é `ContentPage` . A marca raiz contém pelo menos duas declarações de namespace XML, uma para os Xamarin.Forms elementos e outra definindo um `x` prefixo para elementos e atributos intrínsecos a todas as implementações de XAML. A marca raiz também contém um `x:Class` atributo que indica o namespace e o nome da classe derivada de `ContentPage` . Isso corresponde ao namespace e ao nome da classe no arquivo code-behind.

A combinação de XAML e código é demonstrada pelo exemplo de [**CodePlusXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07) .

## <a name="the-xaml-compiler"></a>O compilador XAML

Xamarin.Formstem um compilador XAML, mas seu uso é opcional com base no uso de um [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) . Se o XAML não for compilado, o XAML será analisado no momento da compilação e o arquivo XAML será inserido no PCL, onde também será analisado no tempo de execução. Se o XAML for compilado, o processo de compilação converterá o XAML em um formato binário e o processamento em tempo de execução será mais eficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Especifica a especificidade de plataforma no arquivo XAML

Em XAML, a [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe pode ser usada para selecionar a marcação dependente de plataforma. Essa é uma classe genérica e deve ser instanciada com um `x:TypeArguments` atributo que corresponda ao tipo de destino. A [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe é semelhante, mas é usada com muito menos frequência.

O uso de `OnPlatform` foi alterado desde que o livro foi publicado. Ele foi usado originalmente em conjunto com as propriedades chamadas `iOS` , `Android` e `WinPhone` . Ele agora é usado com [`On`](xref:Xamarin.Forms.On) objetos filho. Defina a [`Platform`](xref:Xamarin.Forms.On.Platform) propriedade como uma cadeia de caracteres consistente com os `const` campos públicos da [`Device`](xref:Xamarin.Forms.Device) classe. Defina a [`Value`](xref:Xamarin.Forms.On.Value) propriedade com um valor consistente com o `x:TypeArguments` atributo da `OnPlatform` marca.

`OnPlatform`é demonstrado no exemplo [**ScaryColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) , portanto, chamado porque contém blocos de XAML quase idêntico. A existência dessa marcação redundante sugere que as técnicas estejam disponíveis para reduzi-las.

## <a name="the-content-property-attributes"></a>Os atributos de propriedade de conteúdo

Alguns elementos de propriedade ocorrem com muita frequência, como a `<ContentPage.Content>` marca no elemento raiz de a `ContentPage` , ou a `<StackLayout.Children>` marca que inclui os filhos `StackLayout` .

Cada classe tem permissão para identificar uma propriedade com uma [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) na classe. Para essa propriedade, as marcas de elemento de propriedade não são necessárias. `ContentPage`define sua propriedade de conteúdo como `Content` e `Layout<T>` (a classe da qual `StackLayout` deriva) define sua propriedade de conteúdo como `Children` . Essas marcas de elemento de propriedade não são necessárias.

O elemento Property de `Label` é `Text` .

## <a name="formatted-text"></a>Texto formatado

O exemplo [**Textvariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contém vários exemplos de como definir `Text` as `FormattedText` Propriedades e de `Label` . Em XAML, `Span` os objetos aparecem como filhos do `FormattedString` objeto.

 Quando uma cadeia de caracteres de várias linhas é definida como a `Text` propriedade, os caracteres de fim de linha são convertidos em caracteres de espaço, mas os caracteres de fim de linha são preservados quando uma cadeia de caracteres de várias linhas é exibida como o conteúdo das `Label` `Label.Text` marcas ou:

 [![Captura de tela tripla de compartilhamento de variações de texto](images/ch07fg03-small.png "Variações de texto formatado")](images/ch07fg03-large.png#lightbox "Variações de texto formatado")

## <a name="related-links"></a>Links relacionados

- [Capítulo 7 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Exemplos do capítulo 7](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Capítulo 7 exemplo de F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
