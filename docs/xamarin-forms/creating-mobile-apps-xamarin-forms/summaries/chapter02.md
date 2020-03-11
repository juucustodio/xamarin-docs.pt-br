---
title: Resumo do capítulo 2. Anatomia de um aplicativo
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 2. Anatomia de um aplicativo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292463"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumo do capítulo 2. Anatomia de um aplicativo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Em um aplicativo Xamarin. Forms, os objetos que ocupam espaço na tela são conhecidos como *elementos visuais*, encapsulados pela classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Elementos visuais podem ser divididos em três categorias correspondentes a essas classes:

- [Página](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Exibir](xref:Xamarin.Forms.View)

Um `Page` derivativo ocupa a tela inteira ou quase toda a tela. Geralmente, o filho de uma página é um `Layout` derivativo para organizar elementos visuais filho. Os filhos do `Layout` podem ser outras classes `Layout` ou `View` derivativos (geralmente chamados de *elementos*), que são objetos familiares, como texto, bitmaps, controles deslizantes, botões, caixas de listagem e assim por diante.

Este capítulo demonstra como criar um aplicativo concentrando-se no [`Label`](xref:Xamarin.Forms.Label), que é o derivativo `View` que exibe texto.

## <a name="say-hello"></a>Diga Olá

Com a plataforma Xamarin instalado, você pode criar uma nova solução xamarin. Forms no Visual Studio ou Visual Studio para Mac. A solução [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa uma biblioteca de classes portátil para o código comum.

> [!NOTE]
> Bibliotecas de classes portáteis foram substituídas por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET.

Este exemplo demonstra uma solução xamarin. Forms criada no Visual Studio sem modificações. A solução consiste em quatro projetos:

- [**Olá**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), uma PCL (biblioteca de classes portátil) compartilhada por outros projetos
- [**Olá. Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), um projeto de aplicativo para Android
- [**Hello. Ios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), um projeto de aplicativo para IOS
- [**Olá. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), um projeto de aplicativo para o plataforma universal do Windows (Windows 10 e Windows 10 Mobile)

> [!NOTE]
> Xamarin. Forms não oferece suporte a Windows 8.1, Windows Phone 8.1 ou Windows 10 Mobile, mas os aplicativos xamarin. Forms são executados no Windows 10 desktop.

Você pode tornar o projeto de inicialização, a qualquer um desses projetos de aplicativo e, em seguida, compilar e executar o programa em um dispositivo ou simulador.

Em muitos dos seus programas do xamarin. Forms, você não modificar os projetos de aplicativo. Eles geralmente permanecem stubs pequenas apenas para iniciar o programa. A maioria de seu foco será a biblioteca comum para todos os aplicativos.

## <a name="inside-the-files"></a>Dentro dos arquivos

Os visuais exibidos pelo programa **Hello** são definidos no construtor da classe [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) . `App` deriva da classe Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application).

> [!NOTE]
> Os modelos de solução do Visual Studio para xamarin. Forms criam uma página com um arquivo XAML. O XAML não é abordado neste livro até o [capítulo 7](chapter07.md).

A seção de **referências** do projeto **Hello** PCL inclui os seguintes assemblies do Xamarin. Forms:

- **Xamarin. Forms. Core**
- **Xamarin. Forms. XAML**
- **Xamarin. Forms. Platform**

As seções de **referências** dos cinco projetos de aplicativo incluem assemblies adicionais que se aplicam às plataformas individuais:

- **Xamarin. Forms. Platform. Android**
- **Xamarin. Forms. Platform. iOS**
- **Xamarin. Forms. Platform. UWP**
- **Xamarin. Forms. Platform. WinRT**
- **Xamarin. Forms. Platform. WinRT. Tablet**
- **Xamarin. Forms. Platform. WinRT. Phone**

> [!NOTE]
> As seções de **referências** desses projetos não listam mais os assemblies. Em vez disso, o arquivo de projeto contém uma marca **PackageReference** que faz referência ao pacote NuGet do Xamarin. Forms. A seção de **referências** no Visual Studio lista o pacote **xamarin. Forms** em vez dos assemblies do xamarin. Forms.

Cada um dos projetos de aplicativo contém uma chamada para o método de `Forms.Init` estático no namespace `Xamarin.Forms`. Isso inicializa a biblioteca xamarin. Forms. Uma versão diferente do `Forms.Init` é definida para cada plataforma. As chamadas para esse método podem ser encontradas nas seguintes classes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [classe`App`, método `OnLaunched`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Além disso, cada plataforma deve instanciar o local da classe `App` na biblioteca compartilhada. Isso ocorre em uma chamada para `LoadApplication` nas seguintes classes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

Caso contrário, esses projetos de aplicativo são programas de "não fazer nada" normal.

## <a name="pcl-or-sap"></a>PCL ou SAP?

É possível criar uma solução xamarin. Forms com o código comum em uma biblioteca de classe portátil (PCL) ou um projeto de ativo compartilhado (SAP). Para criar uma solução do SAP, selecione a opção de Shared no Visual Studio. A solução [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) demonstra o modelo SAP sem modificações.

> [!NOTE]
> Bibliotecas de classes portáteis foi substituída por bibliotecas .NET Standard. O código de exemplo do livro foi convertido para usar bibliotecas padrão do .NET. Caso contrário, as bibliotecas PCL e .NET Standard são conceitualmente muito semelhantes.

Os pacotes de abordagem de biblioteca todas as comuns de código em um projeto de biblioteca referenciado pelos projetos de aplicativo de plataforma. Com a abordagem do SAP, o código comum efetivamente existe em todos os projetos de aplicativo de plataforma e é compartilhado entre elas.

A maioria dos desenvolvedores de xamarin. Forms prefiro a abordagem de biblioteca. Neste livro, a maioria das soluções de usa uma biblioteca. Aqueles que usam o SAP incluem um sufixo **SAP** no nome do projeto.

Com a abordagem do SAP, o código no projeto compartilhado pode executar código diferente para as várias plataformas usando C# diretivas de pré-processador (`#if`, #`elif`e `#endif`) com estes identificadores predefinidos:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`

Em uma biblioteca compartilhada, você pode determinar qual plataforma você está executando em tempo de execução, como você verá neste capítulo.

## <a name="labels-for-text"></a>Rótulos de texto

A solução [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) demonstra como adicionar um novo C# arquivo ao projeto **Greetings** . Esse arquivo define uma classe chamada `GreetingsPage` que deriva de `ContentPage`. Neste livro, a maioria dos projetos contém um único `ContentPage` derivativo cujo nome é o nome do projeto com o sufixo `Page` acrescentado.

O construtor de `GreetingsPage` instancia uma exibição de [`Label`](xref:Xamarin.Forms.Label) , que é o modo de exibição Xamarin. Forms que exibe texto. A propriedade [`Text`](xref:Xamarin.Forms.Label.Text) é definida como o texto exibido pelo `Label`. Este programa define o `Label` para a propriedade `Content` de `ContentPage`. O construtor da classe `App`, em seguida, instancia `GreetingsPage` e a define como sua propriedade `MainPage`.

O texto é exibido no canto superior esquerdo da página. No iOS, isso significa que ele se sobrepõe a barra de status da página. Há várias soluções para esse problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solução 1. Incluir o preenchimento da página

Defina uma propriedade [`Padding`](xref:Xamarin.Forms.Page.Padding) na página. `Padding` é do tipo [`Thickness`](xref:Xamarin.Forms.Thickness), uma estrutura com quatro propriedades:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding` define uma área dentro de uma página em que o conteúdo é excluído. Isso permite que o `Label` Evite substituir a barra de status do iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solução 2. Incluir o preenchimento apenas para iOS (SAP)

Definir uma propriedade de 'Enchimento' apenas no iOS usando um SAP com uma C# diretiva de pré-processador. Isso é demonstrado na solução [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) .

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solução 3. Incluir o preenchimento apenas para iOS (PCL ou SAP)

Na versão do Xamarin. Forms usada para o livro, uma propriedade `Padding` específica para iOS em um PCL ou SAP pode ser selecionada usando o [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) ou [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) método estático. Esses métodos agora são preteridos.

Os métodos de `Device.OnPlatform` são usados para executar o código específico da plataforma ou para selecionar valores específicos da plataforma. Internamente, eles usam a propriedade somente leitura estática [`Device.OS`](xref:Xamarin.Forms.Device.OS) , que retorna um membro da enumeração [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) :

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) para dispositivos UWP.

Os métodos `Device.OnPlatform`, a propriedade `Device.OS` e a enumeração `TargetPlatform` estão todos agora preteridos. Em vez disso, use a propriedade [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) e compare o valor de retorno de `string` com os seguintes campos estáticos:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), a cadeia de caracteres "Ios"
- [`Android`](xref:Xamarin.Forms.Device.Android), a cadeia de caracteres "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), a cadeia de caracteres "UWP", referindo-se ao plataforma universal do Windows

A propriedade somente leitura estática [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) está relacionada. Isso retorna um membro da [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom), que tem estes membros:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported) não é usado

Para iOS e Android, o corte entre `Tablet` e `Phone` é uma largura de retrato de 600 unidades. Para a plataforma Windows, `Desktop` indica um aplicativo UWP em execução no Windows 10 e `Phone` indica um aplicativo UWP em execução no aplicativo do Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solução 3a. Definir margem no rótulo

A propriedade [`Margin`](xref:Xamarin.Forms.View.Margin) foi introduzida tarde demais para ser incluída no livro, mas também é do tipo `Thickness` e você pode defini-la no `Label` para definir uma área fora da exibição que está incluída no cálculo do layout da exibição.

A propriedade `Padding` só está disponível em derivativos [`Layout`](xref:Xamarin.Forms.Layout) e [`Page`](xref:Xamarin.Forms.Page) . A propriedade `Margin` está disponível em todas as derivações de [`View`](xref:Xamarin.Forms.View) .

## <a name="solution-4-center-the-label-within-the-page"></a>Solução 4. O rótulo dentro da página do Centro

Você pode centralizar o `Label` dentro do `Page` (ou colocá-lo em um dos oito outros locais) definindo as propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) do `Label` como um valor do tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions). A estrutura de `LayoutOptions` define duas propriedades:

- Uma propriedade [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) do tipo [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment), uma enumeração com quatro membros: [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start), que significa esquerda ou superior, dependendo da orientação, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center), [`End`](xref:Xamarin.Forms.LayoutAlignment.End), que significa direita ou inferior, dependendo da orientação e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill).

- Uma propriedade [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) do tipo `bool`.

Geralmente, essas propriedades não são usadas diretamente. Em vez disso, as combinações dessas duas propriedades são fornecidas por oito propriedades somente leitura estáticas do tipo `LayoutOptions`:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions` e `VerticalOptions` são as propriedades mais importantes no layout do Xamarin. Forms e são discutidas mais detalhadamente no [**capítulo 4. Rolando a pilha**](chapter04.md).

Aqui está o resultado com as propriedades `HorizontalOptions` e `VerticalOptions` de `Label` definidas como `LayoutOptions.Center`:

[![Captura de tela tripla do programa Greetings](images/ch02fg05-small.png "Rótulo centralizado horizontal e verticalmente")](images/ch02fg05-large.png#lightbox "Rótulo centralizado horizontal e verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Solução 5. Centralizar o texto no rótulo

Você também pode centralizar o texto (ou colocá-lo em oito outros locais na página) definindo as propriedades [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) e [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) de `Label` para um membro da enumeração [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) :

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), o que significa esquerda ou superior (dependendo da orientação)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), significando direita ou inferior (dependendo da orientação)

Essas duas propriedades são definidas somente por `Label`, enquanto as propriedades `HorizontalAlignment` e `VerticalAlignment` são definidas por `View` e herdadas por todas as derivações de `View`. Os resultados visuais podem parecer semelhantes, mas eles são muito diferentes, como demonstra o capítulo seguinte.

## <a name="related-links"></a>Links relacionados

- [Capítulo 2 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Exemplos do capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Exemplos do F# capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introdução com Xamarin. Forms](~/get-started/index.yml)
