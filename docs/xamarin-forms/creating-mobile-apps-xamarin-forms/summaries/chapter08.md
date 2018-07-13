---
title: Resumo do capítulo 8. Código e XAML em harmonia
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 8. Código e XAML em harmonia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b08355db6cc90381b16f51ce7bf23be8e8bd4e14
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994527"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumo do capítulo 8. Código e XAML em harmonia

Este capítulo explora mais profundamente o XAML, e particularmente como código e XAML interagem.

## <a name="passing-arguments"></a>Passando argumentos

Em geral, uma classe instanciada em XAML deve ter um construtor público sem parâmetros; o objeto resultante é inicializado por meio das configurações de propriedade. No entanto, há duas outras maneiras de objetos podem ser instanciados e inicializados.

Embora essas sejam as técnicas de finalidade geral, eles são usados principalmente em conjunto com os modelos de exibição do MVVM.

### <a name="constructors-with-arguments"></a>Construtores com argumentos

O [ **ParameteredConstructorDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) exemplo demonstra como usar o `x:Arguments` marca para especificar argumentos de construtor. Esses argumentos devem ser delimitados por marcas de elemento que indica o tipo do argumento. Para os tipos de dados básicos do .NET, as seguintes marcas estão disponíveis:

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

### <a name="can-i-call-methods-from-xaml"></a>Pode chamar métodos de XAML?

O [ **FactoryMethodDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) exemplo demonstra como usar o `x:FactoryMethod` elemento para especificar um método de fábrica é invocado para criar um objeto. Tal método de fábrica deve ser público e estático, e ele deve criar um objeto do tipo no qual ele está definido. (Por exemplo o [ `Color.FromRgb` ](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) método qualifica porque ele é público e estático e retorna um valor do tipo `Color`.) Os argumentos para o método de fábrica são especificados dentro do `x:Arguments` marcas.

## <a name="the-xname-attribute"></a>O atributo X:Name

O `x:Name` atributo permite que um objeto instanciado no XAML a ser dado um nome. As regras para esses nomes são os mesmos nomes de variáveis em C#. Após o retorno do `InitializeComponent` chamar no construtor, o arquivo code-behind pode fazer referência a esses nomes para acessar o elemento XAML correspondente. Os nomes são realmente convertidos pelo analisador XAML em campos particulares na classe parcial gerada.

O [ **XamlClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) exemplo demonstra o uso de `x:Name` para permitir que o arquivo code-behind manter dois `Label` elementos definidos no XAML atualizado com a data e hora atuais.

O mesmo nome não pode ser usado para vários elementos na mesma página. Isso é um problema específico, se você usar `OnPlatform` paralela de criar objetos nomeados para cada plataforma. O [ **PlatformSpecificLabele** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) exemplo demonstra uma maneira melhor de fazer algo parecido.

## <a name="custom-xaml-based-views"></a>Modos de exibição personalizados baseados em XAML

Há várias maneiras de evitar a repetição de marcação no XAML. Uma técnica comum é criar uma nova classe baseada em XAML que deriva de [ `ContentView` ](xref:Xamarin.Forms.ContentView). Essa técnica é demonstrada na [ **ColorViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) exemplo. O `ColorView` classe deriva `ContentView` para exibir uma cor específica e seu nome, enquanto o `ColorViewListPage` classe deriva `ContentPage` como de costume e explicitamente cria instâncias de 17 de `ColorView`.

Acessando o `ColorView` classe em XAML exige outra declaração de namespace XML, chamada normalmente `local` para classes no mesmo assembly.

## <a name="events-and-handlers"></a>Eventos e manipuladores

Eventos podem ser atribuídos a manipuladores de eventos no XAML, mas o manipulador de eventos deve ser implementado no arquivo code-behind. O [ **XamlKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) demonstra como criar uma interface de usuário de teclado em XAML e como implementar o `Clicked` manipuladores no arquivo code-behind.

## <a name="tap-gestures"></a>Gestos de toque

Qualquer `View` objeto pode obter entrada de toque e gerar eventos de entrada. O `View` classe define um [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) propriedade de coleção que pode conter um ou mais instâncias de classes que derivam [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer).

O [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) gera [ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) eventos. O [ **MonkeyTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) programa demonstra como anexar `TapGestureRecognizer` objetos para quatro `BoxView` elementos para criar um jogo uma imitação:

[![Tripla captura de tela de toque monkey](images/ch08fg07-small.png "imitação jogo")](images/ch08fg07-large.png#lightbox "jogo de imitação")

Mas o **MonkeyTap** programa realmente precisa de som. (Consulte [próximo capítulo](chapter09.md).)



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 8 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Exemplos do capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Capítulo 8 F # de exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
