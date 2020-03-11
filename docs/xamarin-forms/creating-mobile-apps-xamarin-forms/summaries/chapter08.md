---
title: Resumo do capítulo 8. Código e XAML em harmonia
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 8. Código e XAML em harmonia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334229"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumo do capítulo 8. Código e XAML em harmonia

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

Este capítulo explora mais profundamente o XAML, e particularmente como código e XAML interagem.

## <a name="passing-arguments"></a>Passar argumentos

Em geral, uma classe instanciada em XAML deve ter um construtor público sem parâmetros; o objeto resultante é inicializado por meio das configurações de propriedade. No entanto, há duas outras maneiras de objetos podem ser instanciados e inicializados.

Embora essas sejam as técnicas de finalidade geral, eles são usados principalmente em conjunto com os modelos de exibição do MVVM.

### <a name="constructors-with-arguments"></a>Construtores com argumentos

O exemplo [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) demonstra como usar a marca de `x:Arguments` para especificar argumentos de construtor. Esses argumentos devem ser delimitados por marcas de elemento que indica o tipo do argumento. Para os tipos de dados básicos do .NET, as seguintes marcas estão disponíveis:

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

O exemplo [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) demonstra como usar o elemento `x:FactoryMethod` para especificar um método de fábrica que é invocado para criar um objeto. Tal método de fábrica deve ser público e estático, e ele deve criar um objeto do tipo no qual ele está definido. (Por exemplo, o método [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) é qualificado porque é público e estático e retorna um valor do tipo `Color`.) Os argumentos para o método de fábrica são especificados em marcas de `x:Arguments`.

## <a name="the-xname-attribute"></a>O atributo X:Name

O atributo `x:Name` permite que um objeto instanciado em XAML receba um nome. As regras para esses nomes são os mesmos nomes de variáveis em C#. Após o retorno da chamada de `InitializeComponent` no construtor, o arquivo code-behind pode se referir a esses nomes para acessar o elemento XAML correspondente. Os nomes são realmente convertidos pelo analisador XAML em campos particulares na classe parcial gerada.

O exemplo [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) demonstra o uso de `x:Name` para permitir que o arquivo code-behind mantenha dois elementos `Label` definidos em XAML atualizados com a data e a hora atuais.

O mesmo nome não pode ser usado para vários elementos na mesma página. Esse é um problema específico se você usar `OnPlatform` para criar objetos nomeados paralelos para cada plataforma. O exemplo [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) demonstra uma maneira melhor de fazer algo assim.

## <a name="custom-xaml-based-views"></a>Modos de exibição personalizados baseados em XAML

Há várias maneiras de evitar a repetição de marcação no XAML. Uma técnica comum é criar uma nova classe baseada em XAML derivada de [`ContentView`](xref:Xamarin.Forms.ContentView). Essa técnica é demonstrada no exemplo de [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) . A classe `ColorView` deriva de `ContentView` para exibir uma determinada cor e seu nome, enquanto a classe `ColorViewListPage` deriva de `ContentPage` como de costume e cria explicitamente 17 instâncias de `ColorView`.

O acesso à classe `ColorView` no XAML requer outra declaração de namespace XML, comumente denominada `local` para classes no mesmo assembly.

## <a name="events-and-handlers"></a>Eventos e manipuladores

Eventos podem ser atribuídos a manipuladores de eventos no XAML, mas o manipulador de eventos deve ser implementado no arquivo code-behind. O [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) demonstra como criar uma interface do usuário de teclado em XAML e como implementar os manipuladores de `Clicked` no arquivo code-behind.

## <a name="tap-gestures"></a>Gestos de toque

Qualquer objeto de `View` pode obter entrada por toque e gerar eventos dessa entrada. A classe `View` define uma propriedade de coleção de [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) que pode conter uma ou mais instâncias de classes que derivam de [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer).

O [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) gera eventos de [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) . O programa [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) demonstra como anexar objetos de `TapGestureRecognizer` a quatro elementos `BoxView` para criar um jogo imitação:

[![Captura de tela tripla do macaco Tap](images/ch08fg07-small.png "Jogo imitação")](images/ch08fg07-large.png#lightbox "Jogo imitação")

Mas o programa **MonkeyTap** realmente precisa de som. (Consulte [o próximo capítulo](chapter09.md).)

## <a name="related-links"></a>Links relacionados

- [Capítulo 8 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Exemplos de capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Capítulo 8 F# de exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
