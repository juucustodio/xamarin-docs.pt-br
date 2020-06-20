---
title: Resumo do capítulo 8. Código e XAML em harmonia
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 8. Código e XAML em harmonia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 5970DEEB-1FC9-4F78-B4F6-D403E16D22ED
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 90db8b4f11095a2a56c82c3f563844efbcf7e2b1
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136819"
---
# <a name="summary-of-chapter-8-code-and-xaml-in-harmony"></a>Resumo do capítulo 8. Código e XAML em harmonia

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)

Este capítulo explora o XAML mais profundamente e, particularmente, como o código e o XAML interagem.

## <a name="passing-arguments"></a>Passar argumentos

No caso geral, uma classe instanciada em XAML deve ter um construtor público sem parâmetros; o objeto resultante é inicializado por meio de configurações de propriedade. No entanto, há duas outras maneiras pelas quais os objetos podem ser instanciados e inicializados.

Embora sejam técnicas de finalidade geral, elas são usadas principalmente na conexão com os modelos de exibição do MVVM.

### <a name="constructors-with-arguments"></a>Construtores com argumentos

O exemplo [**ParameteredConstructorDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ParameteredConstructorDemo) demonstra como usar a `x:Arguments` marca para especificar argumentos de construtor. Esses argumentos devem ser delimitados por marcas de elemento que indicam o tipo do argumento. Para os tipos de dados básicos do .NET, as seguintes marcas estão disponíveis:

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

### <a name="can-i-call-methods-from-xaml"></a>Posso chamar métodos do XAML?

O exemplo [**FactoryMethodDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FactoryMethodDemo) demonstra como usar o `x:FactoryMethod` elemento para especificar um método de fábrica que é invocado para criar um objeto. Esse método de fábrica deve ser público e estático e deve criar um objeto do tipo no qual ele está definido. (Por exemplo [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) , o método é qualificado porque é público e estático e retorna um valor do tipo `Color` .) Os argumentos para o método de fábrica são especificados dentro de `x:Arguments` marcas.

## <a name="the-xname-attribute"></a>O atributo x:Name

O `x:Name` atributo permite que um objeto instanciado em XAML receba um nome. As regras para esses nomes são as mesmas para nomes de variáveis C#. Após o retorno da `InitializeComponent` chamada no construtor, o arquivo code-behind pode se referir a esses nomes para acessar o elemento XAML correspondente. Os nomes são realmente convertidos pelo analisador XAML em campos privados na classe parcial gerada.

O exemplo [**XamlClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlClock) demonstra o uso de `x:Name` para permitir que o arquivo code-behind mantenha dois `Label` elementos definidos em XAML atualizados com a data e hora atuais.

O mesmo nome não pode ser usado para vários elementos na mesma página. Esse é um problema específico se você usar o `OnPlatform` para criar objetos nomeados paralelos para cada plataforma. O exemplo [**PlatformSpecificLabele**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/PlatformSpecificLabels) demonstra uma maneira melhor de fazer algo assim.

## <a name="custom-xaml-based-views"></a>Exibições personalizadas baseadas em XAML

Há várias maneiras de evitar a repetição de marcação em XAML. Uma técnica comum é criar uma nova classe baseada em XAML derivada de [`ContentView`](xref:Xamarin.Forms.ContentView) . Essa técnica é demonstrada no exemplo de [**ColorViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/ColorViewList) . A `ColorView` classe deriva de `ContentView` para exibir uma determinada cor e seu nome, enquanto a `ColorViewListPage` classe deriva de, `ContentPage` como de costume, e explicitamente cria 17 instâncias de `ColorView` .

O acesso à `ColorView` classe em XAML requer outra declaração de namespace XML, normalmente denominada `local` para classes no mesmo assembly.

## <a name="events-and-handlers"></a>Eventos e manipuladores

Os eventos podem ser atribuídos a manipuladores de eventos em XAML, mas o próprio manipulador de eventos deve ser implementado no arquivo code-behind. O [**XamlKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/XamlKeypad) demonstra como criar uma interface do usuário de teclado em XAML e como implementar os `Clicked` manipuladores no arquivo code-behind.

## <a name="tap-gestures"></a>Gestos de toque

Qualquer `View` objeto pode obter entrada por toque e gerar eventos dessa entrada. A `View` classe define uma [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) propriedade de coleção que pode conter uma ou mais instâncias de classes que derivam de [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) .

O [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) gera [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) eventos. O programa [**MonkeyTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/MonkeyTap) demonstra como anexar `TapGestureRecognizer` objetos a quatro `BoxView` elementos para criar um jogo imitação:

[![Captura de tela tripla do macaco Tap](images/ch08fg07-small.png "Jogo imitação")](images/ch08fg07-large.png#lightbox "Jogo imitação")

Mas o programa **MonkeyTap** realmente precisa de som. (Consulte [o próximo capítulo](chapter09.md).)

## <a name="related-links"></a>Links relacionados

- [Capítulo 8 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf)
- [Exemplos de capítulo 8](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08)
- [Capítulo 8 exemplo de F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter08/FS/XamlKeypad)
- [Passando argumentos em XAML](~/xamarin-forms/xaml/passing-arguments.md)
