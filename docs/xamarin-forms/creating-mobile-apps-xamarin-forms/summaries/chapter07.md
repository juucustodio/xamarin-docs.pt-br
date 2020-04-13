---
title: Resumo do Capítulo 7. XAML versus código
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 7. XAML versus código'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E91F387B-CE90-481C-8D90-CB25519BFD2B
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: ce4dde3716176daf826678809339afb84c25d84a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334718"
---
# <a name="summary-of-chapter-7-xaml-vs-code"></a>Resumo do Capítulo 7. XAML versus código

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

> [!NOTE]
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Xamarin.Forms suporta uma linguagem de marcação baseada em XML chamada Linguagem de Marcação de Aplicativos Extensíveis ou XAML (pronuncia-se "zammel"). O XAML fornece uma alternativa ao C# na definição do layout da interface de usuário de um aplicativo Xamarin.Forms e na definição de vinculações entre elementos de interface do usuário e dados subjacentes.

## <a name="properties-and-attributes"></a>Propriedades e atributos

Xamarin.Forms classes e estruturas tornam-se elementos XML em XAML, e propriedades dessas classes e estruturas tornam-se atributos XML. Para ser instanciado em XAML, uma classe geralmente deve ter um construtor público sem parâmetros. Todas as propriedades definidas em `set` XAML devem ter acessórios públicos.

Para propriedades dos tipos`string`de `double` `bool`dados básicos ( , , e assim por `TryParse` diante), o analisador XAML usa os métodos padrão para converter configurações de atributos para esses tipos. O analisador XAML também pode lidar facilmente com tipos de enumeração, e pode combinar `Flags` membros de enumeração se o tipo de enumeração for sinalizado com o atributo.

Para ajudar o analisador XAML, tipos mais complexos (ou propriedades desses tipos) podem incluir uma que identifica uma [`TypeConverterAttribute`](xref:Xamarin.Forms.TypeConverterAttribute) classe que deriva da [`TypeConverter`](xref:Xamarin.Forms.TypeConverter) qual suporta a conversão de valores de string para esses tipos. Por exemplo, [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) o converte nomes de cores e `Color` strings, como "#rrggbb", em valores.

## <a name="property-element-syntax"></a>Sintaxe de elemento sionto de propriedade

No XAML, as classes e os objetos criados a partir deles são expressos como elementos XML. Estes são conhecidos como *elementos de objeto.* A maioria das propriedades desses objetos são expressas como atributos XML. Estes são chamados *atributos de propriedade.*

Às vezes, uma propriedade deve ser definida como um objeto que não pode ser expresso como uma simples string. Nesse caso, xAML suporta uma tag chamada um elemento de *propriedade* que consiste no nome da classe e nome da propriedade separados por um período. Um elemento de objeto pode então aparecer dentro de um par de tags de elemento de propriedade.

## <a name="adding-a-xaml-page-to-your-project"></a>Adicionando uma página XAML ao seu projeto

Uma Biblioteca de classes portáteis Xamarin.Forms pode conter uma página XAML quando ela é criada pela primeira vez, ou você pode adicionar uma página XAML a um projeto existente. Na caixa de diálogo para adicionar um novo item, escolha o item que se refere a uma página XAML, ou `ContentPage` xAML. (Não `ContentView`é um .)

> [!NOTE]
> As opções do Visual Studio mudaram desde que este capítulo foi escrito.

Dois arquivos são criados: um arquivo XAML com a extensão filename .xaml e um arquivo C# com a extensão .xaml.cs. O arquivo C# é frequentemente referido como o *código-atrás* do arquivo XAML. O arquivo de código atrás é uma `ContentPage`definição de classe parcial que deriva de . No momento da compilação, o XAML é analisado e outra definição parcial de classe é gerada para a mesma classe. Esta classe gerada inclui `InitializeComponent` um método chamado do construtor do arquivo de código atrás.

Durante o tempo de execução, ao final da `InitializeComponent` chamada, todos os elementos do arquivo XAML foram instanciados e inicializados como se tivessem sido criados em código C#.

O elemento raiz no arquivo `ContentPage`XAML é . A tag raiz contém pelo menos duas declarações de namespace XML, uma `x` para os elementos Xamarin.Forms e a outra definindo um prefixo para elementos e atributos intrínsecos a todas as implementações XAML. A tag raiz `x:Class` também contém um atributo que indica o `ContentPage`namespace e o nome da classe que deriva de . Isso corresponde ao namespace e nome da classe no arquivo de código atrás.

A combinação de XAML e código é demonstrada pela amostra [**CodePlusXaml.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)

## <a name="the-xaml-compiler"></a>O compilador XAML

Xamarin.Forms tem um compilador XAML, mas seu uso é [`XamlCompilationAttribute`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)opcional com base no uso de um . Se o XAML não for compilado, o XAML será analisado no tempo de compilação, e o arquivo XAML será incorporado no PCL, onde também é analisado em tempo de execução. Se o XAML for compilado, o processo de compilação converte o XAML em uma forma binária, e o processamento em tempo de execução é mais eficiente.

## <a name="platform-specificity-in-the-xaml-file"></a>Especificidade da plataforma no arquivo XAML

No XAML, [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) a classe pode ser usada para selecionar marcação dependente da plataforma. Esta é uma classe genérica e deve `x:TypeArguments` ser instanciada com um atributo que corresponde ao tipo de destino. A [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe é semelhante, mas usada com muito menos freqüência.

O uso `OnPlatform` de mudou desde que o livro foi publicado. Foi originalmente usado em conjunto `iOS` `Android`com `WinPhone`propriedades nomeadas , e . Agora é usado [`On`](xref:Xamarin.Forms.On) com objetos infantis. Defina [`Platform`](xref:Xamarin.Forms.On.Platform) a propriedade como uma `const` seqüência consistente com os campos públicos da [`Device`](xref:Xamarin.Forms.Device) classe. Defina [`Value`](xref:Xamarin.Forms.On.Value) a propriedade como `x:TypeArguments` um valor `OnPlatform` consistente com o atributo da tag.

`OnPlatform`é demonstrado na amostra [**ScaryColorList,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/ScaryColorList) assim chamada porque contém blocos de XAML quase idêntico. A existência dessa marcação repetitiva sugere que as técnicas devem estar disponíveis para reduzi-la.

## <a name="the-content-property-attributes"></a>Os atributos de propriedade de conteúdo

Alguns elementos de propriedade ocorrem `<ContentPage.Content>` com bastante freqüência, como a tag no elemento raiz de a `ContentPage`, ou a `<StackLayout.Children>` tag que inclui os filhos de `StackLayout`.

Cada classe pode identificar uma [`ContentPropertyAttribute`](xref:Xamarin.Forms.ContentPropertyAttribute) propriedade com um na classe. Para esta propriedade, as etiquetas de elemento de propriedade não são necessárias. `ContentPage`define sua propriedade `Content`de `Layout<T>` conteúdo como , `StackLayout` e (a classe da `Children`qual deriva) define sua propriedade de conteúdo como . Essas etiquetas de elemento de propriedade não são necessárias.

O elemento `Label` de `Text`propriedade de é .

## <a name="formatted-text"></a>Texto formatado

A amostra [**TextVariations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/TextVariations) contém vários `Text` exemplos `FormattedText` de `Label`definir as propriedades de . Em XAML, `Span` objetos aparecem `FormattedString` como filhos do objeto.

 Quando uma seqüência de `Text` caracteres multilinha é definida como propriedade, os caracteres de fim de linha são convertidos em `Label` caracteres espaciais, mas os caracteres de fim de linha são preservados quando uma seqüência de caracteres multilinha aparece como conteúdo das tags: `Label.Text`

 [![Captura de tela tripla do compartilhamento de variações de texto](images/ch07fg03-small.png "Variações de texto formatado")](images/ch07fg03-large.png#lightbox "Variações de texto formatado")

## <a name="related-links"></a>Links relacionados

- [Capítulo 7 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf)
- [Capítulo 7 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07)
- [Capítulo 7 F# amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter07/FS/CodePlusXaml)
- [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
