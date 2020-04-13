---
title: Resumo do Capítulo 2. Anatomia de um aplicativo
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 2. Anatomia de um aplicativo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: f900cb1532ba4415127c95b07e777881e1d74994
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292463"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumo do Capítulo 2. Anatomia de um aplicativo

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Em um aplicativo Xamarin.Forms, objetos que ocupam espaço na tela [`VisualElement`](xref:Xamarin.Forms.VisualElement) são conhecidos como *elementos visuais,* encapsulados pela classe. Os Elementos Visuais podem ser divididos em três categorias correspondentes a essas classes:

- [Página](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Exibir](xref:Xamarin.Forms.View)

Um `Page` derivativo ocupa toda a tela, ou quase toda a tela. Muitas vezes, o filho `Layout` de uma página é um derivado para organizar elementos visuais infantis. Os filhos `Layout` podem ser `Layout` outras classes ou `View` derivados (muitas vezes chamados de *elementos),* que são objetos familiares como texto, bitmaps, controles deslizantes, botões, caixas de lista, e assim por diante.

Este capítulo demonstra como criar um aplicativo [`Label`](xref:Xamarin.Forms.Label)focando `View` no , que é a derivada que exibe texto.

## <a name="say-hello"></a>Diga olá

Com a plataforma Xamarin instalada, você pode criar uma nova solução Xamarin.Forms no Visual Studio ou Visual Studio para Mac. A solução [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa uma Biblioteca de Classe Portátil para o código comum.

> [!NOTE]
> Bibliotecas de classe portáteis foram substituídas por bibliotecas padrão .NET. Todo o código de amostra do livro foi convertido para usar bibliotecas padrão .NET.

Esta amostra demonstra uma solução Xamarin.Forms criada no Visual Studio sem modificações. A solução consiste em quatro projetos:

- [**Olá**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), uma Biblioteca de Classe Portátil (PCL) compartilhada pelos outros projetos
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), um projeto de aplicativo para Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), um projeto de aplicativo para iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), um projeto de aplicativo para a Plataforma Universal Windows (Windows 10 e Windows 10 Mobile)

> [!NOTE]
> O Xamarin.Forms não suporta mais o Windows 8.1, o Windows Phone 8.1 ou o Windows 10 Mobile, mas os aplicativos Xamarin.Forms são executados na área de trabalho do Windows 10.

Você pode fazer qualquer um desses projetos de aplicação o projeto de inicialização e, em seguida, construir e executar o programa em um dispositivo ou simulador.

Em muitos de seus programas Xamarin.Forms, você não estará modificando os projetos de aplicativos. Estes geralmente permanecem minúsculos stubs apenas para iniciar o programa. A maior parte do seu foco será a biblioteca comum a todos os aplicativos.

## <a name="inside-the-files"></a>Dentro dos arquivos

Os visuais exibidos pelo programa **Hello** são definidos [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) no construtor da classe. `App`deriva da classe [`Application`](xref:Xamarin.Forms.Application)Xamarin.Forms .

> [!NOTE]
> Os modelos de solução do Visual Studio para Xamarin.Forms criam uma página com um arquivo XAML. XAML não está coberto neste livro até [o capítulo 7](chapter07.md).

A seção **Referências** do projeto **Hello** PCL inclui os seguintes conjuntos Xamarin.Forms:

- **Xamarin.Forms.Core**
- **Xamarin.Forms.Xaml**
- **Xamarin.Forms.Platform**

As **seções Referências** dos cinco projetos de aplicação incluem assembléias adicionais que se aplicam às plataformas individuais:

- **Xamarin.Forms.Platform.Android**
- **Xamarin.Forms.Platform.iOS**
- **Xamarin.Forms.Platform.UWP**
- **Xamarin.Forms.Platform.WinRT**
- **Xamarin.Forms.Platform.WinRT.Tablet**
- **Xamarin.Forms.Platform.WinRT.Phone**

> [!NOTE]
> As **seções de Referências** desses projetos não listam mais as assembleias. Em vez disso, o arquivo do projeto contém uma **tagS PackageReference** referente ao pacote Xamarin.Forms NuGet. A seção **Referências** no Visual Studio lista o pacote **Xamarin.Forms** em vez dos conjuntos Xamarin.Forms.

Cada um dos projetos de aplicação `Forms.Init` contém `Xamarin.Forms` uma chamada para o método estático no namespace. Isso inicializa a biblioteca Xamarin.Forms. Uma versão `Forms.Init` diferente é definida para cada plataforma. As chamadas para este método podem ser encontradas nas seguintes classes:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` classe, `OnLaunched` método](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Além disso, cada plataforma `App` deve instanciar o local da aula na biblioteca compartilhada. Isso ocorre em `LoadApplication` uma chamada para as seguintes classes:

- Ios:[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android:[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Uwp:[`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

Caso contrário, esses projetos de aplicativos são programas normais de "não fazer nada".

## <a name="pcl-or-sap"></a>PCL ou SAP?

É possível criar uma solução Xamarin.Forms com o código comum em uma Biblioteca de Classe Portátil (PCL) ou em um SAP (SAP). Para criar uma solução SAP, selecione a opção Compartilhada no Visual Studio. A solução [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) demonstra o modelo SAP sem modificações.

> [!NOTE]
> Bibliotecas de classe portáteis foram substituídas por bibliotecas padrão .NET. Todo o código de amostra do livro foi convertido para usar bibliotecas padrão .NET. Caso contrário, as bibliotecas PCL e .NET Standard são conceitualmente muito semelhantes.

A abordagem da biblioteca reúne todo o código comum em um projeto de biblioteca referenciado pelos projetos de aplicativos da plataforma. Com a abordagem SAP, o código comum existe efetivamente em todos os projetos de aplicativos da plataforma e é compartilhado entre eles.

A maioria dos desenvolvedores do Xamarin.Forms prefere a abordagem da biblioteca. Neste livro, a maioria das soluções usa uma biblioteca. Aqueles que usam SAP incluem um sufixo **Sap** no nome do projeto.

Com a abordagem SAP, o código no projeto compartilhado pode executar códigos diferentes`#if`para`elif`as `#endif`várias plataformas usando diretivas de pré-processador C# ( , # , e ) com esses identificadores predefinidos:

- Ios:`__IOS__`
- Android: `__ANDROID__`
- Uwp:`WINDOWS_UWP`

Em uma biblioteca compartilhada, você pode determinar em qual plataforma você está executando em tempo de execução, como você verá mais tarde neste capítulo.

## <a name="labels-for-text"></a>Rótulos para texto

A solução [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) demonstra como adicionar um novo arquivo C# ao projeto **Saudações.** Este arquivo define uma `GreetingsPage` classe nomeada `ContentPage`que deriva de . Neste livro, a maioria `ContentPage` dos projetos contém um único derivado cujo `Page` nome é o nome do projeto com o sufixo anexado.

O `GreetingsPage` construtor instancia [`Label`](xref:Xamarin.Forms.Label) uma exibição, que é a exibição Xamarin.Forms que exibe texto. A [`Text`](xref:Xamarin.Forms.Label.Text) propriedade é definida como o `Label`texto exibido pelo . Este programa `Label` define `Content` a `ContentPage`propriedade de . O construtor da `App` classe então `GreetingsPage` instancia e `MainPage` a coloca em sua propriedade.

O texto é exibido no canto superior esquerdo da página. No iOS, isso significa que ele se sobrepõe à barra de status da página. Existem várias soluções para este problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solução 1. Inclua preenchimento na página

Defina [`Padding`](xref:Xamarin.Forms.Page.Padding) uma propriedade na página. `Padding`é do [`Thickness`](xref:Xamarin.Forms.Thickness)tipo , uma estrutura com quatro propriedades:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding`define uma área dentro de uma página onde o conteúdo é excluído. Isso permite `Label` evitar a substituição da barra de status do iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solução 2. Inclua preenchimento apenas para iOS (somente sap)

Defina uma propriedade 'Preenchimento' somente no iOS usando um SAP com uma diretiva de pré-processador C#. Isso é demonstrado na solução [**GreetingsSap.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap)

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solução 3. Inclua preenchimento apenas para iOS (PCL ou SAP)

Na versão de Xamarin.Forms usada para `Padding` o livro, uma propriedade específica para iOS em [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) um PCL ou SAP pode ser selecionada usando o método ou estática. Esses métodos são agora preteridos

Os `Device.OnPlatform` métodos são usados para executar código específico da plataforma ou para selecionar valores específicos da plataforma. Internamente, eles fazem [`Device.OS`](xref:Xamarin.Forms.Device.OS) uso da propriedade somente leitura estática, que retorna um membro da [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) enumeração:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)para dispositivos UWP.

Os `Device.OnPlatform` métodos, `Device.OS` a propriedade `TargetPlatform` e a enumeração estão todos agora preteridos. Em vez [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) disso, use `string` a propriedade e compare o valor de retorno com os seguintes campos estáticos:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), a string "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), a string "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), a string "UWP", referindo-se à Plataforma Universal windows

A [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) propriedade estática somente leitura está relacionada. Isso retorna um [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)membro do , que tem esses membros:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)não é usado

Para iOS e Android, `Tablet` o `Phone` corte entre e é uma largura retrato de 600 unidades. Para a plataforma `Desktop` Windows, indica um aplicativo UWP `Phone` em execução no Windows 10 e indica um aplicativo UWP em execução sob o aplicativo Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solução 3a. Definir margem no rótulo

A [`Margin`](xref:Xamarin.Forms.View.Margin) propriedade foi introduzida tarde demais para ser incluída no `Thickness` livro, mas também `Label` é do tipo e você pode defini-la para definir uma área fora da vista que está incluída no cálculo do layout da vista.

A `Padding` propriedade só [`Layout`](xref:Xamarin.Forms.Layout) está [`Page`](xref:Xamarin.Forms.Page) disponível em e derivativos. A `Margin` propriedade está [`View`](xref:Xamarin.Forms.View) disponível em todos os derivativos.

## <a name="solution-4-center-the-label-within-the-page"></a>Solução 4. Centralizar o rótulo dentro da página

Você pode `Label` centralizar `Page` o dentro do (ou colocá-lo em [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) um dos `Label` oito outros [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions)lugares) definindo as propriedades do para um valor de tipo . A `LayoutOptions` estrutura define duas propriedades:

- Uma [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) propriedade [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment)do tipo, uma enumeração com quatro [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start)membros: , [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center)o [`End`](xref:Xamarin.Forms.LayoutAlignment.End)que significa esquerda ou superior, [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill)dependendo da orientação, , , que significa direita ou inferior, dependendo da orientação, e .

- Uma [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) propriedade `bool`do tipo.

Geralmente essas propriedades não são usadas diretamente. Em vez disso, as combinações dessas duas propriedades são `LayoutOptions`fornecidas por oito propriedades estáticas somente de leitura do tipo:

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions`e `VerticalOptions` são as propriedades mais importantes no layout Xamarin.Forms, e são discutidas com mais detalhes no [**Capítulo 4. Rolando a pilha**](chapter04.md).

Aqui está o resultado `HorizontalOptions` `VerticalOptions` com `Label` as propriedades `LayoutOptions.Center`de ambos definidos para:

[![Captura de tela tripla do programa de saudações](images/ch02fg05-small.png "Etiqueta centralizada horizontal e verticalmente")](images/ch02fg05-large.png#lightbox "Etiqueta centralizada horizontal e verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Solução 5. Centralizar o texto dentro do Rótulo

Você também pode centralizar o texto (ou colocá-lo em [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) oito `Label` outros locais na [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) página) definindo as propriedades e propriedades de um membro da enumeração:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), ou seja, esquerda ou superior (dependendo da orientação)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), ou seja, direita ou inferior (dependendo da orientação)

Essas duas propriedades são `Label`definidas `HorizontalAlignment` apenas `VerticalAlignment` por , `View` enquanto as `View` propriedades e são definidas por e herdadas por todos os derivados. Os resultados visuais podem parecer semelhantes, mas são muito diferentes como o próximo capítulo demonstra.

## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Capítulo 2 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Capítulo 2 Amostras F#](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introdução ao Xamarin.Forms](~/get-started/index.yml)
