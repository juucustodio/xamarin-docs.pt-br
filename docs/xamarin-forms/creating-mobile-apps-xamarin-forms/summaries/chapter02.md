---
title: Resumo do capítulo 2. Anatomia de um aplicativo
description: 'Criando aplicativos móveis com o xamarin. Forms: Resumo do capítulo 2. Anatomia de um aplicativo'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8764EB7D-8331-4CF7-9BE1-26D0DEE9E0BB
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 208cf28341ceaa43d1c56b4f5086dc98febee6be
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242741"
---
# <a name="summary-of-chapter-2-anatomy-of-an-app"></a>Resumo do capítulo 2. Anatomia de um aplicativo

Em um aplicativo xamarin. Forms, os objetos que ocupam espaço na tela são conhecidos como *elementos visuais*, encapsulados pelo [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe. Elementos visuais podem ser divididos em três categorias correspondente a essas classes:

- [Página](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)
- [Layout](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/)
- [Exibir](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)

Um `Page` derivado ocupa a tela inteira ou quase toda a tela. Geralmente, o filho de uma página é uma `Layout` derivada para organizar os elementos visuais do filho. Os filhos do `Layout` pode ser outro `Layout` classes ou `View` derivados (geralmente chamados de *elementos*), que são familiares objetos como texto, bitmaps, controles deslizantes, botões, caixas de listagem e assim por diante.

Este capítulo demonstra como criar um aplicativo focalizando o [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), que é o `View` derivada que exibe o texto.

## <a name="say-hello"></a>Diga Olá

Com a plataforma Xamarin instalado, você pode criar uma nova solução xamarin. Forms no Visual Studio ou Visual Studio para Mac. O [ **Hello** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello) solução usa uma biblioteca de classes portátil para o código comum. Ele demonstra uma solução xamarin. Forms criada no Visual Studio sem modificações. A solução consiste em seis projetos (últimos dois dos quais não são criados com os modelos de solução xamarin. Forms atuais):

- [**Olá**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello), uma biblioteca de classe portátil (PCL) compartilhado por outros projetos
- [**Hello.Droid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Droid), um projeto de aplicativo para Android
- [**Hello.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.iOS), um projeto de aplicativo para iOS
- [**Hello.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.UWP), um projeto de aplicativo para a plataforma Universal do Windows (Windows 10 e Windows 10 Mobile)
- [**Hello.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.Windows), um projeto de aplicativo para Windows 8.1
- [**Hello.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Hello/Hello/Hello.WinPhone), um projeto de aplicativo para Windows Phone 8.1

Você pode tornar qualquer um desses projetos de aplicativo do projeto de inicialização e, em seguida, compilar e executar o programa em um dispositivo ou simulador.

Em muitos dos seus programas xamarin. Forms, você não modificar os projetos de aplicativo. Esses permanecerem pequenas stubs para iniciar o programa. A maioria de seu foco será a biblioteca de classes portátil comuns a todos os aplicativos.

## <a name="inside-the-files"></a>Os arquivos

Os elementos visuais exibidos pelo **Hello** programa são definidos no construtor do [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello/App.cs) classe. `App` deriva da classe xamarin. Forms [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/).

O **referências** seção o **Hello** projeto PCL inclui os seguintes assemblies xamarin. Forms:

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

Cada um dos projetos de aplicativo contém uma chamada para estático `Forms.Init` método o `Xamarin.Forms` namespace. Isso inicializa a biblioteca xamarin. Forms. Uma versão diferente do `Forms.Init` está definido para cada plataforma. As chamadas para esse método podem ser encontradas nas seguintes classes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [ `App` classe `OnLaunched` método](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- Windows 8.1: [ `App` classe `OnLaunched` método](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/App.xaml.cs#L65)
- Windows Phone 8.1: [ `App` classe `OnLaunched` método](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WinPhone/App.xaml.cs#L67)

Além disso, cada plataforma deve instanciar o `App` classe local a PCL. Isso ocorre em uma chamada para `LoadApplication` nas seguintes classes:

- iOS: [`AppDelegate`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.iOS/AppDelegate.cs)
- Android: [`MainActivity`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Droid/MainActivity.cs)
- UWP: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.UWP/MainPage.xaml.cs)
- Windows 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.Windows/MainPage.xaml.cs)
- Windows Phone 8.1: [`MainPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter02/Hello/Hello/Hello.WindowsPhone/MainPage.xaml.cs)

Caso contrário, esses projetos de aplicativo são programas normal "não fazer nada".

## <a name="pcl-or-sap"></a>PCL ou SAP?

É possível criar uma solução xamarin. Forms com o código comum em uma biblioteca de classe portátil (PCL) ou um projeto de ativo compartilhado (SAP). Para criar uma solução do SAP, selecione a opção compartilhado no Visual Studio. O [ **HelloSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/HelloSap) solução demonstra o modelo do SAP sem modificações.

Os pacotes de abordagem PCL todas as comuns de código em um projeto de biblioteca referenciado pelos projetos de aplicativo de plataforma. Com a abordagem do SAP, o código comum efetivamente existe em todos os projetos de aplicativo da plataforma e é compartilhado entre elas.

A maioria dos desenvolvedores do xamarin. Forms preferir a abordagem PCL. Neste manual, a maioria das soluções é PCL. Aqueles que usam SAP incluem um **Sap** sufixo no nome do projeto.

Para dar suporte a todas as plataformas xamarin. Forms, a versão do .NET usado pelo PCL deve acomodar as seguintes plataformas:

- .NET Framework 4.5
- Windows 8
- Windows Phone 8.1
- Xamarin.Android
- Xamarin.iOS
- Xamarin (clássico)

Isso é conhecido como 111 de perfil de computador.

Com a abordagem SAP o código no projeto compartilhado pode executar código diferente para várias plataformas usando diretivas de pré-processador c# (`#if`, #`elif`, e `#endif`) com esses predefinido identificadores:

- iOS: `__IOS__`
- Android: `__ANDROID__`
- UWP: `WINDOWS_UWP`
- Windows 8.1: `WINDOWS_APP`
- Windows Phone 8.1: `WINDOWS_PHONE_APP`

Em um PCL, você pode determinar quais plataformas estiver executando em tempo de execução, como você verá neste capítulo.

## <a name="labels-for-text"></a>Rótulos de texto

O [ **saudações** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/Greetings) solução demonstra como adicionar um arquivo c# novo para o **saudações** projeto. Esse arquivo define uma classe denominada `GreetingsPage` que deriva de `ContentPage`. Neste manual, a maioria dos projetos contêm um único `ContentPage` derivada cujo nome é o nome do projeto com o sufixo `Page` anexado.

O `GreetingsPage` construtor instancia um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) exibição, que é o modo de exibição do xamarin. Forms que exibe o texto. O [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedade é definida como o texto exibido, o `Label`. Este programa define a `Label` para o `Content` propriedade `ContentPage`. O construtor do `App` classe instancia `GreetingsPage` e o configura para seu `MainPage` propriedade.

O texto é exibido no canto superior esquerdo da página. No iOS, isso significa que ele se sobrepõe a barra de status da página. Há várias soluções para esse problema:

### <a name="solution-1-include-padding-on-the-page"></a>Solução 1. Incluir o preenchimento da página

Definir um [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) propriedade na página. `Padding` é do tipo [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/), uma estrutura com quatro propriedades:

- [`Left`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Left/)
- [`Top`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Top/)
- [`Right`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Right/)
- [`Bottom`](https://developer.xamarin.com/api/property/Xamarin.Forms.Thickness.Bottom/)

`Padding` define uma área dentro de uma página em que o conteúdo foi excluído. Isso permite que o `Label` para evitar a substituição da barra de status do iOS.

### <a name="solution-2-include-padding-just-for-ios-sap-only"></a>Solução 2. Incluir preenchimento apenas para iOS (SAP)

Defina uma propriedade de 'Preenchimento' apenas no iOS usando um SAP com uma diretiva de pré-processador C#. Isso é demonstrado no [ **GreetingsSap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/GreetingsSap) solução.

### <a name="solution-3-include-padding-just-for-ios-pcl-or-sap"></a>Solução de 3. Incluir preenchimento apenas para iOS (PCL ou SAP)

Na versão do xamarin. Forms usada para o registro, um `Padding` propriedade específica do iOS em PCL ou SAP pode ser selecionada por meio de [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) ou [ `Device.OnPlatform<T>` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform%7BT%7D/p/T/T/T/) método estático. Esses métodos agora são preteridos

O `Device.OnPlatform` métodos são usados para executar código específico da plataforma ou para selecionar valores específicos de plataforma. Internamente, façam usam o [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) estática propriedade somente leitura que retorna um membro do [ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/) enumeração:

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/)
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/)
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) para Windows 8.1, Windows Phone 8.1 e todos os dispositivos UWP.
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), anteriormente usado para identificar o Windows Phone 8.0, mas é agora não utilizado
- [`Other`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Other/) não é usado

O `Device.OnPlatform` métodos, o `Device.OS` propriedade e o `TargetPlatform` enumeração são todos agora preterido. Em vez disso, use o [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) propriedade e comparar o `string` valor com os seguintes campos estáticos de retorno:

- [`iOS`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), a cadeia de caracteres "iOS"
- [`Android`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), a cadeia de caracteres "Android"
- [`UWP`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), a cadeia de caracteres "UWP", que faz referência para a plataforma de tempo de execução do Windows
- [`Windows`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Windows/), a cadeia de caracteres "Windows" para o tempo de execução do Windows (Windows 8.1 e Windows Phone 8.1)
- [`WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/), a cadeia de caracteres "WinPhone" para o Windows Phone 8.0

O [ `Device.Idiom` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.Idiom/) está relacionada a propriedade estática somente leitura. Isso retorna um membro do [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/), que tem estes membros:

- [`Desktop`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Desktop/)
- [`Tablet`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Tablet/)
- [`Phone`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Phone/)
- [`Unsupported`](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetIdiom.Unsupported/) não é usado

Para iOS e Android, o corte entre `Tablet` e `Phone` é uma largura de retrato de 600 unidades. Para a plataforma Windows, `Desktop` indica um aplicativo de UWP em execução no Windows 10, `Tablet` é um programa do Windows 8.1, e `Phone` indica um aplicativo de UWP em execução no Windows 10 ou um aplicativo do Windows Phone 8.1.

## <a name="solution-3a-set-margin-on-the-label"></a>Solução 3a. Definir margem no rótulo

O [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propriedade foi introduzida tarde demais para serem incluídas no catálogo, mas também é do tipo `Thickness` e você pode definir o `Label` para definir uma área fora do modo de exibição que está incluído no cálculo da layout do modo de exibição.

O `Padding` propriedade só está disponível em [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) e [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) derivados. O `Margin` propriedade está disponível em todos os [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) derivados.

## <a name="solution-4-center-the-label-within-the-page"></a>Solução de 4. Centralizar o rótulo dentro da página

Você pode centralizar o `Label` dentro de `Page` (ou colocá-lo em um dos oito outros locais) definindo o [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades do `Label` para um valor do tipo [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/). O `LayoutOptions` estrutura define duas propriedades:

- Um [ `Alignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Alignment/) propriedade do tipo [ `LayoutAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutAlignment/), uma enumeração com quatro membros: [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Start/), que significa esquerda ou superior, dependendo da orientação, [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Center/), [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.End/), direita ou inferior, dependendo da orientação, o que significa e [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutAlignment.Fill/).

- Um [ `Expands` ](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) propriedade do tipo `bool`.

Geralmente essas propriedades não são usadas diretamente. Em vez disso, combinações dessas duas propriedades são fornecidas pelo oito propriedades estáticas somente leitura do tipo `LayoutOptions`:

- [`LayoutOptions.Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`LayoutOptions.Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`LayoutOptions.End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`LayoutOptions.StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`LayoutOptions.CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`LayoutOptions.EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`HorizontalOptions` e `VerticalOptions` são as propriedades mais importantes no layout do xamarin. Forms e são discutidas mais detalhadamente em [ **capítulo 4. A pilha de rolagem**](chapter04.md).

Aqui está o resultado com o `HorizontalOptions` e `VerticalOptions` propriedades de `Label` definido como `LayoutOptions.Center`:

[![Tripla captura de tela do programa de saudações](images/ch02fg05-small.png "horizontalmente e verticalmente centralizado rótulo")](images/ch02fg05-large.png#lightbox "horizontalmente e verticalmente centralizado de rótulo")

## <a name="solution-5-center-the-text-within-the-label"></a>Solução de 5. Centralizar o texto dentro do rótulo

Você pode centralizar o texto (ou coloque-o em oito outros locais na página) definindo o [ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/) e [ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) propriedades de `Label` a um membro da [ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/) enumeração:

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/), significado esquerda ou superior (dependendo da orientação)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.End/), que significa que a direita ou inferior (dependendo da orientação)

Essas duas propriedades são definidas somente pelo `Label`, enquanto o `HorizontalAlignment` e `VerticalAlignment` propriedades são definidas por `View` e herdada por todas as `View` derivados. Os resultados visuais podem parecer semelhantes, mas eles são muito diferentes, como mostra o próximo capítulo.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 2 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch02-Apr2016.pdf)
- [Exemplos do capítulo 2](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02)
- [Exemplos de capítulo 2 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter02/FS)
- [Introdução ao xamarin. Forms](~/xamarin-forms/get-started/index.md)
