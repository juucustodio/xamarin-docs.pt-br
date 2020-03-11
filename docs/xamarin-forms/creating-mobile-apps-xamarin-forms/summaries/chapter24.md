---
title: Resumo do capítulo 24. Navegação da página
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 24. Navegação da página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291477"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumo do capítulo 24. Navegação da página

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Muitos aplicativos consistem em várias páginas entre os quais o usuário navega. O aplicativo sempre tem uma página *principal* ou *Home* Page e, a partir daí, o usuário navega para outras páginas, que são mantidas em uma pilha para navegar de volta. As opções de navegação adicionais são abordadas no [**capítulo 25. Variedades de páginas**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modais e sem janela restrita

`VisualElement` define uma propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) do tipo [`INavigation`](xref:Xamarin.Forms.INavigation), que inclui os dois métodos a seguir para navegar para uma nova página:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Os dois métodos aceitam uma instância de `Page` como um argumento e retornam um objeto `Task`. Os dois métodos a seguir navegue de volta para a página anterior:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se a interface do usuário tiver seu próprio botão **voltar** (como telefones com Android e Windows), não será necessário que o aplicativo chame esses métodos.

Embora esses métodos estejam disponíveis em qualquer `VisualElement`, geralmente eles são chamados da propriedade `Navigation` da instância de `Page` atual.

Aplicativos geralmente usam páginas modais quando o usuário é solicitado a fornecer algumas informações na página antes de retornar à página anterior. As páginas que não são modais são, às vezes, chamadas de modelo ou *hierárquicas*. Nada na própria página distingue-o como modal ou sem-modo; é controlado em vez disso, o método usado para navegar até ele. Para trabalhar em todas as plataformas, uma página modal deve fornecer sua própria interface do usuário para navegar de volta à página anterior.

O exemplo [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) permite que você explore a diferença entre as páginas sem janela restrita e modal. Qualquer aplicativo que usa a navegação de página deve passar seu home page para o construtor [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , geralmente na classe `App` do programa. Um bônus é que você não precisa mais definir um `Padding` na página para iOS.

Você descobrirá que, para páginas sem janela restrita, a propriedade [`Title`](xref:Xamarin.Forms.Page.Title) da página será exibida. O iOS, Android e as plataformas de tablet e área de trabalho do Windows fornecem um elemento de interface do usuário para navegar de volta para a página anterior. É claro que os dispositivos Android e Windows Phone têm um botão **voltar** padrão para voltar.

Para páginas modais, a página `Title` não é exibida e nenhum elemento de interface de usuário é fornecido para voltar à página anterior. Embora você possa usar o botão **voltar** padrão do Android e do Windows Phone para retornar à página anterior, a página modal nas outras plataformas deve fornecer seu próprio mecanismo para voltar.

### <a name="animated-page-transitions"></a>Transições de página animados

Versões alternativas dos vários métodos de navegação são fornecidas com um segundo argumento booliano definido como `true` se você quiser que a transição de página inclua uma animação:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

No entanto, os métodos de navegação de página padrão incluem a animação por padrão, portanto, esses são apenas valiosos para navegar para uma página específica na inicialização (conforme discutido no final deste capítulo) ou ao fornecer sua própria animação de entrada (como discutido em [**Chapter22. Animação**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variações de visuais e funcionais

`NavigationPage` inclui duas propriedades que podem ser definidas quando você instancia a classe em seu método `App`:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` também inclui quatro propriedades vinculáveis anexadas que afetam a página específica na qual estão definidas:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) e [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) e [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) e [`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) funcionam somente no Ios
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) e [`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) funcionam somente no Ios e Android

### <a name="exploring-the-mechanics"></a>Explorando a mecânica

Os métodos de navegação de página são todos assíncronas e devem ser usados com `await`. Após a conclusão não indica que a navegação de página foi concluída, mas apenas que é seguro examinar a pilha de navegação de página.

Quando uma página navega para outra, a primeira página geralmente obtém uma chamada para seu método [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) , e a segunda página Obtém uma chamada para seu método [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) . Da mesma forma, quando uma página retorna para outra, a primeira página Obtém uma chamada para seu método `OnDisappearing`, e a segunda página geralmente obtém uma chamada para seu método `OnAppearing`. A ordem dessas chamadas (e a conclusão dos métodos assíncronos que invoca a navegação) é dependente da plataforma. O uso da palavra "geralmente" nas duas instruções anteriores é devido a navegação de página modal Android, em que essas chamadas de método não ocorrem.

Além disso, as chamadas para os métodos `OnAppearing` e `OnDisappearing` não indicam necessariamente a navegação da página.

A interface `INavigation` inclui duas propriedades de coleção que permitem examinar a pilha de navegação:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) do tipo `IReadOnlyList<Page>` para a pilha sem janela restrita
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) do tipo `IReadOnlyList<Page>` para a pilha modal

É mais seguro acessar essas pilhas da propriedade `Navigation` da `NavigationPage` (que deve ser a propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage) da classe `App`). Só é seguro examinar essas pilhas depois de concluíram os métodos de navegação de página assíncrona. A propriedade [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) do `NavigationPage` não indica a página atual se a página atual é uma página modal, mas indica em vez disso a última página sem janela restrita.

O exemplo [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) permite explorar a navegação de página e as pilhas e os tipos legais de navegação de página:

- Uma página sem janela restrita pode navegar para outra página sem janela restrita ou em uma página modal
- Uma página restrita pode navegar somente para outra página modal

### <a name="enforcing-modality"></a>Impondo a modalidade

Um aplicativo usa uma página modal quando é necessário obter algumas informações do usuário. O usuário deve ser proibido de retornar à página anterior até que essa informação é fornecida. No iOS, é fácil fornecer um botão **voltar** e habilitá-lo somente quando o usuário tiver concluído a página. Mas para dispositivos Android e Windows Phone, o aplicativo deve substituir o método [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) e retornar `true` se o programa tiver tratado o botão **voltar** , conforme demonstrado no exemplo [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) .

O exemplo [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) demonstra como isso funciona em um cenário MVVM.

## <a name="navigation-variations"></a>Variações de navegação

Se uma determinada página modal pode ser navegada várias vezes, ele deve reter informações para que o usuário pode editar as informações em vez de digitá-lo em tudo novamente. Você pode lidar com isso, mantendo a instância específica de página modal, mas uma abordagem melhor (especialmente no iOS) é preservar as informações em um modelo de exibição.

### <a name="making-a-navigation-menu"></a>Tornando um menu de navegação

O exemplo [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) demonstra como usar um `TableView` para listar itens de menu. Cada item é associado a um objeto de `Type` para uma página específica. Quando esse item é selecionado, o programa cria uma instância de página e navega até ela.

[![Captura de tela tripla do tipo de galeria de exibição](images/ch24fg21-small.png "Itens de menu de listagem de TableView")](images/ch24fg21-large.png#lightbox "Itens de menu de listagem de TableView")

O exemplo de [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) é um pouco diferente, pois o menu contém instâncias de cada página em vez de tipos. Isso ajuda a manter as informações de cada página, mas todas as páginas devem ser instanciadas na inicialização do programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

O [**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) demonstra várias funções definidas por `INavigation` que permitem manipular a pilha de navegação de maneira estruturada:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) e [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) com animação opcional

### <a name="dynamic-page-generation"></a>Geração de página dinâmica

O exemplo [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) demonstra a construção de uma página no tempo de execução com base na entrada do usuário.

## <a name="patterns-of-data-transfer"></a>Padrões de transferência de dados

Geralmente, é necessário compartilhar dados entre páginas &mdash; para transferir dados para uma página navegada e para que uma página retorne dados para a página que a invocou. Há várias técnicas para fazer isso.

### <a name="constructor-arguments"></a>Argumentos de construtor

Ao navegar para uma nova página, é possível instanciar a classe de página com um argumento de construtor que permite que a página para se inicializar. O exemplo [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) demonstra isso. Também é possível que a página navegada tenha seu `BindingContext` definido pela página que navega até ele.

### <a name="properties-and-method-calls"></a>Propriedades e chamadas de método

Os exemplos de transferência de dados restantes exploram o problema de passar informações entre páginas quando navega de uma página para outra página e vice-versa. Nessas discussões, a *Home* Page navega até a página de *informações* e deve transferir informações inicializadas para a página *informações* . A página *informações* Obtém informações adicionais do usuário e transfere as informações para a *Home* Page.

A *Home* Page pode acessar facilmente métodos públicos e propriedades na página de *informações* assim que instancia essa página. A página *informações* também pode acessar métodos públicos e propriedades na *Home* Page, mas escolher um bom momento para isso pode ser complicado. O exemplo de [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) faz isso em sua substituição de `OnDisappearing`. Uma desvantagem é que a página de *informações* precisa saber o tipo da *Home* Page.

### <a name="messagingcenter"></a>MessagingCenter

A classe [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) Xamarin. Forms fornece outra maneira para que duas páginas se comuniquem entre si. As mensagens são identificadas por uma cadeia de caracteres de texto e podem ser acompanhadas por qualquer objeto.

Um programa que deseja receber mensagens de um tipo específico deve assiná-las usando [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) e especificar uma função de retorno de chamada. Posteriormente, ele pode cancelar a assinatura chamando [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). A função de retorno de chamada recebe qualquer mensagem enviada do tipo especificado com o nome especificado enviado por meio do método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) .

O programa [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) demonstra como transferir dados usando o centro de mensagens, mas novamente isso requer que a página de *informações* saiba o tipo da *Home* Page.

### <a name="events"></a>Eventos

O evento é uma abordagem consagrada pelo tempo para uma classe enviar informações para outra classe sem saber o tipo dessa classe. No exemplo de [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) , a classe *info* define um evento que ele dispara quando as informações estão prontas. No entanto, não há nenhum lugar conveniente para a *Home* Page desanexar o manipulador de eventos.

### <a name="the-app-class-intermediary"></a>O intermediário de classe do aplicativo

O exemplo [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) mostra como acessar as propriedades definidas na classe `App` pela *Home* Page e a página de *informações* . Isso é uma boa solução, mas a próxima seção descreve uma opção melhor.

### <a name="switching-to-a-viewmodel"></a>Alternar para um ViewModel

O uso de um ViewModel para as informações permite que a *Home* Page e a página de *informações* compartilhem a instância da classe Information. Isso é demonstrado no exemplo de [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) .

### <a name="saving-and-restoring-page-state"></a>Salvando e restaurando o estado da página

O intermediário da classe `App` ou a abordagem ViewModel é ideal quando o aplicativo deve salvar informações se o programa entrar em suspensão enquanto a página *informações* estiver ativa. O exemplo [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) demonstra isso.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvando e restaurando a pilha de navegação

Em geral, um programa de várias páginas que entra em suspensão deve navegar para a mesma página, quando ele for restaurado. Isso significa que um programa desse tipo deve salvar o conteúdo da pilha de navegação. Esta seção mostra como automatizar esse processo em uma classe projetada para essa finalidade. Essa classe também chama as páginas individuais para permitir que eles salvar e restaurar o estado da página.

A biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define uma interface chamada [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que as classes podem implementar para salvar e restaurar itens no dicionário de `Properties`.

A classe [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) na biblioteca **Xamarin. FormsBook. Toolkit** deriva de `Application`. Em seguida, você pode derivar sua classe de `App` de `MultiPageRestorableApp` e executar algumas realizações de manutenção.

O [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) demonstra o uso de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Algo como um aplicativo da vida real

O exemplo de [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) também usa `MultiPageRestorableApp` e permite a inserção e edição de anotações que são salvas no dicionário de `Properties`.

## <a name="related-links"></a>Links relacionados

- [Capítulo 24 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Capítulo 24 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md)
