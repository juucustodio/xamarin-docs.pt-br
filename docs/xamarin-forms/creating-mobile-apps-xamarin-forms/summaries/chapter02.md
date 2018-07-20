---
title: Resumo do capítulo 2. Anatomia de um aplicativo
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 2. Anatomia de um aplicativo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: fe6a8c3d17cf1fe6f489f6425bbdaa3cd30f390a
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156672"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumo do capítulo 2. Anatomia de um aplicativo

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Em um aplicativo xamarin. Forms, os objetos que ocupam espaço na tela são conhecidos como *elementos visuais*, encapsulado pelo [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe. Elementos visuais podem ser divididos em três categorias correspondentes a essas classes:

- [Página](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Exibir](xref:Xamarin.Forms.View)

Um `Page` derivativo ocupa a tela inteira ou quase toda a tela. Muitas vezes, o filho de uma página é um `Layout` derivativo para organizar os elementos visuais do filho. Os filhos do `Layout` pode ser outra `Layout` classes ou `View` derivados (geralmente chamado *elementos*), que são objetos familiares, como texto, bitmaps, controles deslizantes, botões, caixas de listagem e assim por diante.

Este capítulo demonstra como criar um aplicativo se concentrando na [ `Label` ](xref:Xamarin.Forms.Label), que é o `View` derivativo que exibe texto.

## <a name="say-hello"></a>Diga Olá

Com a plataforma Xamarin instalado, você pode criar uma nova solução xamarin. Forms no Visual Studio ou Visual Studio para Mac. O [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) solução usa uma biblioteca de classes portátil para o código comum. 

> [!NOTE] 
> Bibliotecas de classes portáteis foram substituídas por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET.

Este exemplo demonstra uma solução xamarin. Forms criada no Visual Studio sem modificações. A solução consiste em seis projetos:

- [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), uma biblioteca de classe portátil (PCL) compartilhado por outros projetos
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), um projeto de aplicativo para Android
- [**Hello. IOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), um projeto de aplicativo para iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), um projeto de aplicativo para a plataforma Universal do Windows (Windows 10 e Windows 10 Mobile)
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows), um projeto de aplicativo para Windows 8.1
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone), um projeto de aplicativo para Windows Phone 8.1

> [!NOTE] 
> Xamarin. Forms não oferece suporte a Windows 8.1, Windows Phone 8.1 ou Windows 10 Mobile, mas os aplicativos xamarin. Forms são executados no Windows 10 desktop. 

Você pode tornar o projeto de inicialização, a qualquer um desses projetos de aplicativo e, em seguida, compilar e executar o programa em um dispositivo ou simulador.

Em muitos dos seus programas do xamarin. Forms, você não modificar os projetos de aplicativo. Eles geralmente permanecem stubs pequenas apenas para iniciar o programa. A maioria de seu foco será a biblioteca comum para todos os aplicativos.

## <a name="inside-the-files"></a>Dentro dos arquivos

Os elementos visuais exibidos pelo **Hello** programa são definidos no construtor do [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App` deriva da classe xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application).

> [!NOTE] 
> Os modelos de solução do Visual Studio para xamarin. Forms criam uma página com um arquivo XAML. XAML não é abordada neste livro até [capítulo 7](chapter07.md).

O **referências** seção o **Hello** projeto PCL inclui os seguintes assemblies do xamarin. Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

O **referências** seções dos projetos de cinco aplicativo incluem assemblies adicionais que se aplicam às plataformas individuais:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

> [!NOTE] 
> O **referências** seções desses projetos não listam os assemblies. Em vez disso, o arquivo de projeto contém um **PackageReference** marcas referenciando o pacote do NuGet xamarin. Forms. O **referências** seção em listas do Visual Studio a **xamarin. Forms** do pacote em vez dos assemblies do xamarin. Forms. 

Cada um dos projetos de aplicativo contém uma chamada para estático `Forms.Init` método no `Xamarin.Forms` namespace. Isso inicializa a biblioteca xamarin. Forms. Uma versão diferente do `Forms.Init` é definida para cada plataforma. As chamadas para esse método podem ser encontradas nas seguintes classes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` classe, `OnLaunched` método](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Além disso, cada plataforma deve instanciar a `App` classe local na biblioteca compartilhada. Isso ocorre em uma chamada para `LoadApplication` nas seguintes classes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

Caso contrário, esses projetos de aplicativo são programas de "não fazer nada" normal.

## <a name="pcl-or-sap"></a>PCL ou SAP?

É possível criar uma solução xamarin. Forms com o código comum em uma biblioteca de classe portátil (PCL) ou um projeto de ativo compartilhado (SAP). Para criar uma solução do SAP, selecione a opção de Shared no Visual Studio. O [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) solução demonstra o modelo SAP sem modificações.

> [!NOTE] 
> Bibliotecas de classes portáteis foi substituída por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET. Caso contrário, as bibliotecas PCL e .NET Standard são conceitualmente muito semelhantes.

Os pacotes de abordagem de biblioteca todas as comuns de código em um projeto de biblioteca referenciado pelos projetos de aplicativo de plataforma. Com a abordagem do SAP, o código comum efetivamente existe em todos os projetos de aplicativo de plataforma e é compartilhado entre elas.

A maioria dos desenvolvedores de xamarin. Forms prefiro a abordagem de biblioteca. Neste livro, a maioria das soluções de usa uma biblioteca. Aqueles que usam SAP incluem uma **Sap** sufixo no nome do projeto.

Com a abordagem do SAP o código no projeto compartilhado pode executar código diferente para várias plataformas usando diretivas de pré-processador c# (`#if`, #`elif`, e `#endif`) com esses predefinido identificadores:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

Em uma biblioteca compartilhada, você pode determinar qual plataforma você está executando em tempo de execução, como você verá neste capítulo.

## <a name="labels-for-text"></a>Rótulos de texto

O [ **saudações** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) solução demonstra como adicionar um novo arquivo c# para o **saudações** projeto. Esse arquivo define uma classe chamada `GreetingsPage` que deriva de `ContentPage`. Neste livro, a maioria dos projetos contêm uma única `ContentPage` derivativo cujo nome é o nome do projeto com o sufixo `Page` acrescentado.

O `GreetingsPage` construtor instancia uma [ `Label` ](xref:Xamarin.Forms.Label) exibição, que é o modo de exibição do xamarin. Forms que exibe texto. O [ `Text` ](xref:Xamarin.Forms.Label.Text) estiver definida como o texto exibido pelo `Label`. Este programa define a `Label` para o `Content` propriedade de `ContentPage`. O construtor do `App` , em seguida, cria uma instância de classe `GreetingsPage` e o configura para seu `MainPage` propriedade.

O texto é exibido no canto superior esquerdo da página. No iOS, isso significa que ele se sobrepõe a barra de status da página. Há várias soluções para esse problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solução 1. Incluir o preenchimento da página

Definir um [ `Padding` ](xref:Xamarin.Forms.Page.Padding) propriedade na página. `Padding` é do tipo [ `Thickness` ](xref:Xamarin.Forms.Thickness), uma estrutura com quatro propriedades:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` define uma área dentro de uma página em que o conteúdo é excluído. Isso permite que o `Label` para evitar a substituição da barra de status do iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solução 2. Incluir o preenchimento apenas para iOS (SAP)

Defina uma propriedade de 'Enchimento' apenas no iOS usando um SAP com uma diretiva de pré-processador c#. Isso é demonstrado na [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) solução.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solução 3. Incluir o preenchimento apenas para iOS (PCL ou SAP)

Na versão do xamarin. Forms usado para o catálogo, um `Padding` propriedade específica do iOS em um PCL ou SAP pode ser selecionada usando o [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) ou [ `Device.OnPlatform<T>` ](xref:Xamarin.Forms.Device.OnPlatform*) método estático. Esses métodos agora são preteridos.

O `Device.OnPlatform` métodos são usados para executar o código específico da plataforma ou para selecionar valores específicos de plataforma. Internamente, eles fazem uso do [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) propriedade somente leitura estática, que retorna um membro dos [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) enumeração:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) para dispositivos UWP.

O `Device.OnPlatform` métodos, o `Device.OS` propriedade e o `TargetPlatform` enumeração são todos preterido. Em vez disso, use o [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) propriedade e comparar o `string` retornar valor com os seguintes campos estáticos:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), a cadeia de caracteres "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), a cadeia de caracteres "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), a cadeia de caracteres "UWP", referindo-se à plataforma Universal do Windows

O [ `Device.Idiom` ](xref:Xamarin.Forms.Device.Idiom) está relacionada a propriedade estática de somente leitura. Isso retorna um membro de [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom), que tem estes membros:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) não é usado

Para iOS e Android, o corte entre `Tablet` e `Phone` é uma largura de retrato de unidades de 600. Para a plataforma Windows, `Desktop` indica um aplicativo UWP em execução no Windows 10, e `Phone` indica um aplicativo UWP em execução no aplicativo do Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solução 3a. Definir margem no rótulo

O [ `Margin` ](xref:Xamarin.Forms.View.Margin) propriedade foi introduzida tarde demais para serem incluídos no catálogo, mas também é do tipo `Thickness` e você pode defini-lo no `Label` para definir uma área fora do modo de exibição que está incluído no cálculo das layout do modo de exibição.

O `Padding` propriedade só está disponível no [ `Layout` ](xref:Xamarin.Forms.Layout) e [ `Page` ](xref:Xamarin.Forms.Page) derivados. O `Margin` propriedade está disponível em todos os [ `View` ](xref:Xamarin.Forms.View) derivados.

## <a name="solution-4-center-the-label-within-the-page"></a>Solução 4. O rótulo dentro da página do Centro

Você pode centralizar o `Label` dentro de `Page` (ou coloque-o em um dos oito outros lugares) definindo o [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedades do `Label` para um valor do tipo [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions). O `LayoutOptions` estrutura define duas propriedades:

- Uma [ `Alignment` ](xref:Xamarin.Forms.LayoutOptions.Alignment) propriedade do tipo [ `LayoutAlignment` ](xref:Xamarin.Forms.LayoutAlignment), uma enumeração com quatro membros: [ `Start` ](xref:Xamarin.Forms.LayoutAlignment.Start), que significa esquerda ou superior, dependendo do orientação [ `Center` ](xref:Xamarin.Forms.LayoutAlignment.Center), [ `End` ](xref:Xamarin.Forms.LayoutAlignment.End), que significa que a parte direita ou inferior, dependendo da orientação, e [ `Fill` ](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Uma [ `Expands` ](xref:Xamarin.Forms.LayoutOptions.Expands) propriedade do tipo `bool`.

Geralmente, essas propriedades não são usadas diretamente. Em vez disso, combinações dessas duas propriedades são fornecidas por oito propriedades de somente leitura estáticas do tipo `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` e `VerticalOptions` são as propriedades mais importantes no layout do xamarin. Forms e são discutidos em mais detalhes [ **capítulo 4. Rolagem da pilha**](chapter04.md).

Aqui está o resultado com o `HorizontalOptions` e `VerticalOptions` propriedades de `Label` definidos como `LayoutOptions.Center`:

[![Tripla captura de tela do programa de saudações](images/ch02fg05-small.png "horizontalmente e verticalmente Centered rotular")](images/ch02fg05-large.png#lightbox "horizontalmente e verticalmente centralizado de rótulo")

## <a name="solution-5-center-the-text-within-the-label"></a>Solução 5. Centralizar o texto no rótulo

Você pode centralizar o texto (ou coloque-o em oito outros locais na página), definindo a [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) e [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) propriedades de `Label` a um membro da [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) enumeração:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), significado esquerda ou superior (dependendo da orientação)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), que significa que a parte direita ou inferior (dependendo da orientação)

Essas duas propriedades são definidas somente pelo `Label`, enquanto a `HorizontalAlignment` e `VerticalAlignment` propriedades são definidas pelos `View` e herdada por todas as `View` derivados. Os resultados visuais podem parecer semelhantes, mas eles são muito diferentes, como demonstra o capítulo seguinte.

## <a name="related-links"></a>Links relacionados

- [Capítulo 2 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Exemplos do capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Exemplos do capítulo 2 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introdução ao xamarin. Forms](~/xamarin-forms/get-started/index.md)
