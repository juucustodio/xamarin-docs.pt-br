---
title: Resumo do capítulo 8. Código e XAML em harmonia
description: 'Criando aplicativos móveis com o xamarin. Forms: Resumo do capítulo 8. Código e XAML em harmonia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 70c2babef55ee6cb886b0e7da26ee57b086fa558
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241013"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumo do capítulo 8. Código e XAML em harmonia

Este capítulo explora XAML mais profundamente, e particularmente como código e XAML interagem.

## <a name="passing-arguments"></a>Argumentos de passagem

Em geral, uma classe instanciada em XAML deve ter um construtor público sem parâmetros; o objeto resultante é inicializado por meio das configurações de propriedade. No entanto, há duas outras maneiras que os objetos podem ser instanciados e inicializados.

Embora essas sejam técnicas de finalidade gerais, eles são usados principalmente em conjunto com MVVM exibir modelos.

### <a name="constructors-with-arguments"></a>Construtores com argumentos

O [ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) demonstra como usar o `x:Arguments` marca para especificar argumentos de construtor. Esses argumentos devem ser delimitados por marcas de elemento que indica o tipo do argumento. Para os tipos de dados básicos do .NET, as seguintes marcas estão disponíveis:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

### <a name="can-i-call-methods-from-xaml"></a>Pode chamar métodos do XAML?

O [ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) demonstra como usar o `x:FactoryMethod` elemento para especificar um método de fábrica que é invocado para criar um objeto. Esse é um método de fábrica deve ser público e estático e ele deve criar um objeto do tipo no qual ela está definida. (Por exemplo o [ `Color.FromRgb` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/)) método qualifica porque ele é público e estático e retorna um valor do tipo `Color`.) Os argumentos para o método de fábrica são especificados em `x:Arguments` marcas.

## <a name="the-xname-attribute"></a>O atributo X:Name

O `x:Name` atributo permite que um objeto instanciado em XAML para receber um nome. As regras para esses nomes são da mesma maneira que os nomes de variável c#. Após o retorno do `InitializeComponent` chamar no construtor, o arquivo code-behind pode fazer referência a esses nomes para acessar o elemento correspondente do XAML. Os nomes na verdade são convertidos pelo analisador XAML em campos particulares na classe parcial gerada.

O [ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) exemplo demonstra o uso de `x:Name` para permitir que o arquivo de code-behind manter dois `Label` elementos definidos em XAML atualizado com a data e hora atuais.

O mesmo nome não pode ser usado para vários elementos na mesma página. Este é um problema específico, se você usar `OnPlatform` criar paralelo objetos nomeados para cada plataforma. O [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) exemplo demonstra uma maneira melhor de fazer algo parecido.

## <a name="custom-xaml-based-views"></a>Modos de exibição personalizados baseados em XAML

Há várias maneiras de evitar a repetição de marcação em XAML. Uma técnica comum é criar uma nova classe baseada em XAML que deriva de [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/). Essa técnica é demonstrada no [ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) exemplo. O `ColorView` classe derivada de `ContentView` para exibir uma determinada cor e seu nome, enquanto o `ColorViewListPage` classe derivada de `ContentPage` como de costume e explicitamente cria 17 instâncias de `ColorView`.

Acessando o `ColorView` classe em XAML requer outra declaração de namespace XML, geralmente denominada `local` para classes no mesmo assembly.

## <a name="events-and-handlers"></a>Eventos e manipuladores

Eventos podem ser atribuídos a manipuladores de eventos no XAML, mas o manipulador de eventos deve ser implementado no arquivo code-behind. O [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) demonstra como criar uma interface de usuário do teclado em XAML e como implementar o `Clicked` manipuladores no arquivo code-behind.

## <a name="tap-gestures"></a>Gestos de toque

Qualquer `View` objeto pode obter entrada de toque e geram eventos a partir dessa entrada. O `View` classe define um [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) propriedade da coleção que pode conter uma ou mais instâncias de classes que derivam de [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/).

O [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) gera [ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/) eventos. O [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) programa demonstra como anexar `TapGestureRecognizer` objetos para quatro `BoxView` elementos para criar um jogo imitação:

[![Captura de tela tripla de toque monkey](images/ch08fg07-small.png "imitação jogo")](images/ch08fg07-large.png#lightbox "imitação jogo")

Mas o **MonkeyTap** programa realmente precisa de som. (Consulte [próximo capítulo](chapter09.md).)



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 8 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Exemplos de capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Exemplo de capítulo 8 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
