---
title: Resumo do capítulo 2. Anatomia de um aplicativo
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 2. Anatomia de um aplicativo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 78da3ed91acea0c056074d712d368de70b251392
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136910"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumo do capítulo 2. Anatomia de um aplicativo

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)

> [!NOTE]
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

Em um Xamarin.Forms aplicativo, os objetos que ocupam espaço na tela são conhecidos como *elementos visuais*, encapsulados pela [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe. Elementos visuais podem ser divididos em três categorias correspondentes a essas classes:

- [Página](xref:Xamarin.Forms.Page)
- [Layout](xref:Xamarin.Forms.Layout)
- [Exibir](xref:Xamarin.Forms.View)

Um `Page` derivativo ocupa a tela inteira ou quase toda a tela. Geralmente, o filho de uma página é um `Layout` derivativo para organizar elementos visuais filho. Os filhos de `Layout` podem ser outras `Layout` classes ou `View` derivativos (geralmente chamados de *elementos*), que são objetos familiares, como texto, bitmaps, controles deslizantes, botões, caixas de listagem e assim por diante.

Este capítulo demonstra como criar um aplicativo concentrando-se no [`Label`](xref:Xamarin.Forms.Label) , que é o `View` derivativo que exibe o texto.

## <a name="say-hello"></a>Diga Olá

Com a plataforma Xamarin instalada, você pode criar uma nova Xamarin.Forms solução no Visual Studio ou Visual Studio para Mac. A solução [**Hello**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) usa uma biblioteca de classes portátil para o código comum.

> [!NOTE]
> As bibliotecas de classes portáteis foram substituídas por bibliotecas de .NET Standard. Todo o código de exemplo do livro foi convertido para usar bibliotecas do .NET Standard.

Este exemplo demonstra uma Xamarin.Forms solução criada no Visual Studio sem modificações. A solução consiste em quatro projetos:

- [**Olá**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), uma PCL (biblioteca de classes portátil) compartilhada por outros projetos
- [**Olá. Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), um projeto de aplicativo para Android
- [**Hello. Ios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), um projeto de aplicativo para IOS
- [**Olá. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), um projeto de aplicativo para o plataforma universal do Windows (Windows 10 e Windows 10 Mobile)

> [!NOTE]
> Xamarin.FormsNão dá mais suporte a Windows 8.1, Windows Phone 8,1 ou Windows 10 Mobile, mas Xamarin.Forms os aplicativos são executados na área de trabalho do Windows 10.

Você pode fazer com que qualquer um desses projetos de aplicativo seja o projeto de inicialização e, em seguida, compilar e executar o programa em um dispositivo ou simulador.

Em muitos de seus Xamarin.Forms programas, você não modificará os projetos de aplicativo. Eles geralmente permanecem pequenos stubs apenas para iniciar o programa. A maior parte do seu foco será a biblioteca comum a todos os aplicativos.

## <a name="inside-the-files"></a>Dentro dos arquivos

Os visuais exibidos pelo programa **Hello** são definidos no construtor da [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App`deriva da Xamarin.Forms classe [`Application`](xref:Xamarin.Forms.Application) .

> [!NOTE]
> Os modelos de solução do Visual Studio para Xamarin.Forms criar uma página com um arquivo XAML. O XAML não é abordado neste livro até o [capítulo 7](chapter07.md).

A seção de **referências** do projeto **Hello** PCL inclui os seguintes Xamarin.Forms assemblies:

- **Xamarin.Forms. Core**
- **Xamarin.Forms. XAML**
- **Xamarin.Forms. Plataforma**

As seções de **referências** dos cinco projetos de aplicativo incluem assemblies adicionais que se aplicam às plataformas individuais:

- **Xamarin.Forms. Platform. Android**
- **Xamarin.Forms. Platform. iOS**
- **Xamarin.Forms. Platform. UWP**
- **Xamarin.Forms. Platform. WinRT**
- **Xamarin.Forms. Platform. WinRT. Tablet**
- **Xamarin.Forms. Plataforma. WinRT. Phone**

> [!NOTE]
> As seções de **referências** desses projetos não listam mais os assemblies. Em vez disso, o arquivo de projeto contém uma marca **PackageReference** que faz referência ao Xamarin.Forms pacote NuGet. A seção de **referências** no Visual Studio lista o **Xamarin.Forms** pacote em vez dos Xamarin.Forms assemblies.

Cada um dos projetos de aplicativo contém uma chamada para o `Forms.Init` método estático no `Xamarin.Forms` namespace. Isso inicializa a Xamarin.Forms biblioteca. Uma versão diferente do `Forms.Init` é definida para cada plataforma. As chamadas para esse método podem ser encontradas nas seguintes classes:

- Cisco[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` classe, `OnLaunched` método](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)

Além disso, cada plataforma deve instanciar o `App` local da classe na biblioteca compartilhada. Isso ocorre em uma chamada para `LoadApplication` nas seguintes classes:

- Cisco[`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android[`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP[`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)

Caso contrário, esses projetos de aplicativos são programas normais de "não fazer nada".

## <a name="pcl-or-sap"></a>PCL ou SAP?

É possível criar uma Xamarin.Forms solução com o código comum em uma PCL (biblioteca de classes portátil) ou em um projeto de ativo compartilhado (SAP). Para criar uma solução SAP, selecione a opção compartilhada no Visual Studio. A solução [**HelloSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) demonstra o modelo SAP sem modificações.

> [!NOTE]
> As bibliotecas de classes portáteis foram substituídas por bibliotecas de .NET Standard. Todo o código de exemplo do livro foi convertido para usar bibliotecas do .NET Standard. Caso contrário, as bibliotecas PCL e .NET Standard são conceitualmente muito semelhantes.

A abordagem de biblioteca agrupa todo o código comum em um projeto de biblioteca referenciado pelos projetos de aplicativo da plataforma. Com a abordagem do SAP, o código comum existe efetivamente em todos os projetos de aplicativos da plataforma e é compartilhado entre eles.

A maioria dos Xamarin.Forms desenvolvedores prefere a abordagem da biblioteca. Neste livro, a maioria das soluções usa uma biblioteca. Aqueles que usam o SAP incluem um sufixo **SAP** no nome do projeto.

Com a abordagem do SAP, o código no projeto compartilhado pode executar código diferente para as várias plataformas usando diretivas de pré-processador do C# ( `#if` , # `elif` e `#endif` ) com esses identificadores predefinidos:

- Cisco`__IOS__`
- Android: `__ANDROID__`
- UWP`WINDOWS_UWP`

Em uma biblioteca compartilhada, você pode determinar em qual plataforma você está executando em tempo de execução, como você verá mais adiante neste capítulo.

## <a name="labels-for-text"></a>Rótulos para texto

A solução [**Greetings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) demonstra como adicionar um novo arquivo C# ao projeto **Greetings** . Esse arquivo define uma classe chamada `GreetingsPage` derivada de `ContentPage` . Neste livro, a maioria dos projetos contém um único `ContentPage` derivativo cujo nome é o nome do projeto com o sufixo `Page` anexado.

O `GreetingsPage` construtor instancia uma [`Label`](xref:Xamarin.Forms.Label) exibição, que é a Xamarin.Forms exibição que exibe o texto. A [`Text`](xref:Xamarin.Forms.Label.Text) propriedade é definida como o texto exibido pelo `Label` . Este programa define o `Label` para a `Content` propriedade de `ContentPage` . O construtor da `App` classe, em seguida, cria uma instância `GreetingsPage` e a define como sua `MainPage` propriedade.

O texto é exibido no canto superior esquerdo da página. No iOS, isso significa que ele se sobrepõe à barra de status da página. Há várias soluções para esse problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solução 1. Incluir preenchimento na página

Defina uma [`Padding`](xref:Xamarin.Forms.Page.Padding) Propriedade na página. `Padding`é do tipo [`Thickness`](xref:Xamarin.Forms.Thickness) , uma estrutura com quatro propriedades:

- [`Left`](xref:Xamarin.Forms.Thickness.Left)
- [`Top`](xref:Xamarin.Forms.Thickness.Top)
- [`Right`](xref:Xamarin.Forms.Thickness.Right)
- [`Bottom`](xref:Xamarin.Forms.Thickness.Bottom)

`Padding`define uma área dentro de uma página em que o conteúdo é excluído. Isso permite que o `Label` Evite substituir a barra de status do Ios.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solução 2. Incluir preenchimento apenas para iOS (somente SAP)

Defina uma propriedade ' Padding ' somente no iOS usando um SAP com uma diretiva de pré-processador do C#. Isso é demonstrado na solução [**GreetingsSap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) .

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solução 3. Incluir preenchimento apenas para iOS (PCL ou SAP)

Na versão do Xamarin.Forms usada para o livro, uma `Padding` propriedade específica para Ios em um PCL ou SAP pode ser selecionada usando o [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) [`Device.OnPlatform<T>`](xref:Xamarin.Forms.Device.OnPlatform*) método estático ou. Esses métodos agora estão preteridos

Os `Device.OnPlatform` métodos são usados para executar o código específico da plataforma ou para selecionar valores específicos da plataforma. Internamente, eles usam a [`Device.OS`](xref:Xamarin.Forms.Device.OS) propriedade somente leitura estática, que retorna um membro da [`TargetPlatform`](xref:Xamarin.Forms.TargetPlatform) enumeração:

- [`iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)
- [`Android`](xref:Xamarin.Forms.TargetPlatform.Android)
- [`Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)para dispositivos UWP.

Os `Device.OnPlatform` métodos, a `Device.OS` propriedade e a `TargetPlatform` Enumeração agora são todos preteridos. Em vez disso, use a [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) propriedade e compare o `string` valor de retorno com os seguintes campos estáticos:

- [`iOS`](xref:Xamarin.Forms.Device.iOS), a cadeia de caracteres "iOS"
- [`Android`](xref:Xamarin.Forms.Device.Android), a cadeia de caracteres "Android"
- [`UWP`](xref:Xamarin.Forms.Device.UWP), a cadeia de caracteres "UWP", referindo-se ao Plataforma Universal do Windows

A [`Device.Idiom`](xref:Xamarin.Forms.Device.Idiom) propriedade somente leitura estática está relacionada. Isso retorna um membro do [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) , que tem estes membros:

- [`Desktop`](xref:Xamarin.Forms.TargetIdiom.Desktop)
- [`Tablet`](xref:Xamarin.Forms.TargetIdiom.Tablet)
- [`Phone`](xref:Xamarin.Forms.TargetIdiom.Phone)
- [`Unsupported`](xref:Xamarin.Forms.TargetIdiom.Unsupported)Não é usado

Para iOS e Android, o corte entre `Tablet` e `Phone` é uma largura de retrato de 600 unidades. Para a plataforma Windows, `Desktop` indica um aplicativo UWP em execução no Windows 10 e `Phone` indica um aplicativo UWP em execução no aplicativo do Windows 10.

## <a name="solution-3a-set-margin-on-the-label"></a>Solução 3a. Definir margem no rótulo

A [`Margin`](xref:Xamarin.Forms.View.Margin) propriedade foi introduzida tarde demais para ser incluída no livro, mas também é do tipo `Thickness` e você pode defini-la no `Label` para definir uma área fora da exibição que está incluída no cálculo do layout da exibição.

A `Padding` propriedade só está disponível no [`Layout`](xref:Xamarin.Forms.Layout) e em [`Page`](xref:Xamarin.Forms.Page) derivativos. A `Margin` propriedade está disponível em todos os [`View`](xref:Xamarin.Forms.View) derivativos.

## <a name="solution-4-center-the-label-within-the-page"></a>Solução 4. Centralizar o rótulo dentro da página

Você pode centralizar o `Label` dentro do `Page` (ou colocá-lo em um dos oito outros locais) definindo [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) as [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e de `Label` para um valor do tipo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) . A `LayoutOptions` estrutura define duas propriedades:

- Uma [`Alignment`](xref:Xamarin.Forms.LayoutOptions.Alignment) Propriedade do tipo [`LayoutAlignment`](xref:Xamarin.Forms.LayoutAlignment) , uma enumeração com quatro membros: [`Start`](xref:Xamarin.Forms.LayoutAlignment.Start) , que significa esquerda ou superior, dependendo da orientação, [`Center`](xref:Xamarin.Forms.LayoutAlignment.Center) , [`End`](xref:Xamarin.Forms.LayoutAlignment.End) , que significa direita ou inferior, dependendo da orientação e [`Fill`](xref:Xamarin.Forms.LayoutAlignment.Fill) .

- Uma [`Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) Propriedade do tipo `bool` .

Geralmente, essas propriedades não são usadas diretamente. Em vez disso, as combinações dessas duas propriedades são fornecidas por oito propriedades somente leitura estáticas do tipo `LayoutOptions` :

- [`LayoutOptions.Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`LayoutOptions.Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`LayoutOptions.End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`LayoutOptions.StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`LayoutOptions.CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`LayoutOptions.EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`HorizontalOptions`e `VerticalOptions` são as propriedades mais importantes no Xamarin.Forms layout e são discutidas mais detalhadamente no [**capítulo 4. Rolando a pilha**](chapter04.md).

Aqui está o resultado com as `HorizontalOptions` `VerticalOptions` Propriedades e de `Label` ambas definidas como `LayoutOptions.Center` :

[![Captura de tela tripla do programa Greetings](images/ch02fg05-small.png "Rótulo centralizado horizontal e verticalmente")](images/ch02fg05-large.png#lightbox "Rótulo centralizado horizontal e verticalmente")

## <a name="solution-5-center-the-text-within-the-label"></a>Solução 5. Centralizar o texto dentro do rótulo

Você também pode centralizar o texto (ou colocá-lo em oito outros locais na página) definindo as [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) Propriedades e de `Label` como um membro da [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) enumeração:

- [`Start`](xref:Xamarin.Forms.TextAlignment.Start), significando esquerda ou superior (dependendo da orientação)
- [`Center`](xref:Xamarin.Forms.TextAlignment.Center)
- [`End`](xref:Xamarin.Forms.TextAlignment.End), o que significa direita ou inferior (dependendo da orientação)

Essas duas propriedades são definidas somente pelo `Label` , enquanto as `HorizontalAlignment` `VerticalAlignment` Propriedades e são definidas por `View` e herdadas por todos os `View` derivativos. Os resultados visuais podem parecer semelhantes, mas são muito diferentes, como demonstra o próximo capítulo.

## <a name="related-links"></a>Links relacionados

- [Capítulo 2 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Exemplos do capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Capítulo 2 amostras de F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introdução comXamarin.Forms](~/get-started/index.yml)
