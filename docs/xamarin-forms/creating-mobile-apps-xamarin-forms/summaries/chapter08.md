---
title: Resumo do Capítulo 8. Código e XAML em harmonia
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 8. Código e XAML em harmonia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 75f153499edb6d979f9a0269a1439eaf8ca53878
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334229"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumo do Capítulo 8. Código e XAML em harmonia

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

Este capítulo explora xaml mais profundamente, e particularmente como código e XAML interagem.

## <a name="passing-arguments"></a>Passar argumentos

No caso geral, uma classe instanciada em XAML deve ter um construtor público sem parâmetros; o objeto resultante é inicializado através de configurações de propriedade. No entanto, existem duas outras maneiras de que os objetos podem ser instantâneos e inicializados.

Embora sejam técnicas de uso geral, elas são usadas principalmente em conexão com os Modelos de Exibição MVVM.

### <a name="constructors-with-arguments"></a>Construtores com argumentos

A [**amostra ParametedConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) demonstra `x:Arguments` como usar a tag para especificar argumentos de construtores. Esses argumentos devem ser delimitados por tags de elemento que indicam o tipo de argumento. Para os tipos básicos de dados .NET, as seguintes tags estão disponíveis:

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

### <a name="can-i-call-methods-from-xaml"></a>Posso chamar métodos da XAML?

A amostra [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) demonstra `x:FactoryMethod` como usar o elemento para especificar um método de fábrica que é invocado para criar um objeto. Tal método de fábrica deve ser público e estático, e deve criar um objeto do tipo em que é definido. (Por exemplo, o [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) método se qualifica porque é público `Color`e estático e retorna um valor do tipo .) Os argumentos para o método `x:Arguments` de fábrica são especificados dentro das tags.

## <a name="the-xname-attribute"></a>O atributo x:Nome

O `x:Name` atributo permite que um objeto instanciado em XAML receba um nome. As regras para esses nomes são as mesmas para nomes de variável C#. Após o retorno `InitializeComponent` da chamada no construtor, o arquivo de código atrás pode se referir a esses nomes para acessar o elemento XAML correspondente. Os nomes são realmente convertidos pelo analisador XAML em campos privados na classe parcial gerada.

A amostra [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) demonstra `x:Name` o uso de permitir que `Label` o arquivo de código atrás mantenha dois elementos definidos em XAML atualizados com a data e a hora atuais.

O mesmo nome não pode ser usado para vários elementos na mesma página. Este é um problema `OnPlatform` particular se você usar para criar objetos nomeados paralelos para cada plataforma. A amostra [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) demonstra uma maneira melhor de fazer algo assim.

## <a name="custom-xaml-based-views"></a>Visualizações personalizadas baseadas em XAML

Existem várias maneiras de evitar a repetição da marcação no XAML. Uma técnica comum é criar uma nova classe baseada [`ContentView`](xref:Xamarin.Forms.ContentView)em XAML que deriva de . Essa técnica é demonstrada na amostra [**ColorViewList.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) A `ColorView` classe deriva `ContentView` de exibir uma determinada cor `ColorViewListPage` e seu `ContentPage` nome, enquanto a classe `ColorView`deriva de como de costume e cria explicitamente 17 instâncias de .

Acessar a `ColorView` classe em XAML requer outra declaração `local` de namespace XML, comumente nomeada para classes no mesmo conjunto.

## <a name="events-and-handlers"></a>Eventos e manipuladores

Os eventos podem ser atribuídos aos manipuladores de eventos no XAML, mas o manipulador de eventos em si deve ser implementado no arquivo de código atrás. O [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) demonstra como construir uma interface de usuário de `Clicked` teclado no XAML e como implementar os manipuladores no arquivo de código atrás.

## <a name="tap-gestures"></a>Toque em gestos

Qualquer `View` objeto pode obter entrada de toque e gerar eventos a partir dessa entrada. A `View` classe define [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) uma propriedade de coleção que pode conter [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer)uma ou mais instâncias de classes derivadas de .

O [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) gera eventos. O programa [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) demonstra `TapGestureRecognizer` como `BoxView` anexar objetos a quatro elementos para criar um jogo de imitação:

[![Captura tripla de torneira de macaco](images/ch08fg07-small.png "Jogo de Imitação")](images/ch08fg07-large.png#lightbox "Jogo de Imitação")

Mas o programa **MonkeyTap** realmente precisa de som. (Veja [o próximo capítulo](chapter09.md).)

## <a name="related-links"></a>Links relacionados

- [Capítulo 8 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Capítulo 8 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Capítulo 8 F# amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
