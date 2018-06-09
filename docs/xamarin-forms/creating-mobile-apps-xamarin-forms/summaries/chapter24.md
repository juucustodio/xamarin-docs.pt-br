---
title: Resumo do capítulo 24. Navegação da página
description: 'Criando aplicativos móveis com o xamarin. Forms: Resumo do capítulo 24. Navegação da página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 048b002cc3a250fe74a41bbeb756f8484a15abf1
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241361"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumo do capítulo 24. Navegação da página

Muitos aplicativos consistem em várias páginas entre os quais o usuário navega. O aplicativo sempre tem um *principal* página ou *inicial* página, e a partir daí, o usuário navega para outras páginas, que são mantidas em uma pilha de navegação de volta. Opções adicionais de navegação são abordadas em [ **Capítulo 25. Página variedades**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modais e sem janela restrita

`VisualElement` define uma [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade do tipo [ `INavigation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INavigation/), que inclui os dois métodos para navegar para uma nova página a seguir:

- [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/)
- [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/)

Ambos os métodos aceitam um `Page` instância como um argumento e retornar um `Task` objeto. Os dois métodos a seguir navegue de volta para a página anterior:

- [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync()/)
- [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/)

Se a interface do usuário tem seu próprio **novamente** botão (como telefones Android e Windows) não é necessário para o aplicativo chamar esses métodos.

Embora esses métodos estão disponíveis em qualquer `VisualElement`, geralmente são chamados de `Navigation` propriedade `Page` instância.

Aplicativos geralmente usam páginas restritas quando o usuário é solicitado a fornecer algumas informações na página antes de retornar à página anterior. Páginas que não são restritas às vezes são chamadas sem janela restrita ou *hierárquica*. Nada na própria página distingue-o como modal ou sem janela restrita; é controlado em vez disso, o método usado para navegar até ele. Para trabalhar em todas as plataformas, uma página restrita deve fornecer sua própria interface de usuário para navegar de volta à página anterior.

O [ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) exemplo permite que você explore a diferença entre as páginas modais e sem janela restrita. Qualquer aplicativo que use a navegação de página deve passar sua home page para o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) construtor, geralmente o programa `App` classe. Um bônus é que você não precisa definir um `Padding` na página do iOS.

Você descobrirá que sem janela restrita páginas, a página do [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) propriedade é exibida. O iOS, Android e as plataformas de tablet e área de trabalho do Windows fornecem um elemento de interface do usuário, volte à página anterior. Do curso, Android e Windows dispositivos de telefone tem um padrão **novamente** botão para voltar.

Para páginas modais, a página `Title` não for exibido, e nenhum elemento de interface do usuário é fornecido para voltar à página anterior. Embora você possa usar o padrão de Android e Windows phone **novamente** botão para retornar à página anterior, a página restrita em outras plataformas deve fornecer seu próprio mecanismo para voltar.

### <a name="animated-page-transitions"></a>Transições de página animado

Versões alternativas dos vários métodos de navegação são fornecidas com um segundo argumento booliano que você definir para `true` se você quiser que a transição de página para incluir uma animação:

- [PushAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PushModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PopAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync/p/System.Boolean/)
- [PopModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync/p/System.Boolean/)

No entanto, os métodos de navegação de página padrão incluem a animação por padrão, portanto, só são importantes para navegar para uma determinada página na inicialização (como discutido em direção ao final deste capítulo) ou ao fornecer sua própria animação de entrada (como discutido em [ **Chapter22. Animação**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variações de visuais e funcionais

`NavigationPage` inclui duas propriedades que podem ser definidas quando você criar uma instância de classe no seu `App` método:

- [`BarBackgroundColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)
- [`BarTextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarTextColor/)

`NavigationPage` também inclui quatro propriedades vinculáveis anexadas que afetam a página específica no qual eles são definidos:

- [`SetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasBackButton/p/Xamarin.Forms.Page/System.Boolean/) e [`GetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasBackButton/p/Xamarin.Forms.Page/)
- [`SetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasNavigationBar/p/Xamarin.Forms.BindableObject/System.Boolean/) e [`GetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasNavigationBar/p/Xamarin.Forms.BindableObject/)
- [`SetBackButtonTitle`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetBackButtonTitle/p/Xamarin.Forms.BindableObject/System.String/) e [ `GetBackButtonTitle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetBackButtonTitle/p/Xamarin.Forms.BindableObject/) funcionam no iOS somente
- [`SetTitleIcon`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetTitleIcon/p/Xamarin.Forms.BindableObject/Xamarin.Forms.FileImageSource/) e [ `GetTitleIcon` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetTitleIcon/p/Xamarin.Forms.BindableObject/) funcionam em somente Android e iOS

### <a name="exploring-the-mechanics"></a>Explorando a mecânica

Os métodos de navegação de página são assíncronos e deve ser usados com `await`. A conclusão não indica que a navegação de página foi concluída, mas apenas que é seguro examinar a pilha de navegação de página.

Quando uma página navega para outra, a primeira página geralmente obtém uma chamada para seu [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/) método e a segunda página obtém uma chamada para seu [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) método. Da mesma forma, quando uma página retorna para outra, a primeira página obtém uma chamada para seu `OnDisappearing` método e a segunda página geralmente obtém uma chamada para seu `OnAppearing` método. A ordem dessas chamadas (e a conclusão dos métodos assíncronos que invoca o painel de navegação) é dependente de plataforma. O uso da palavra "geral" nas duas instruções acima é devido a Android navegação de página modal, na qual essas chamadas de método não ocorrerem.

Além disso, chamadas para o `OnAppearing` e `OnDisappearing` métodos não necessariamente a navegação de página.

O `INavigation` interface inclui duas propriedades de coleção que permitem que você examine a pilha de navegação:

- [`NavigationStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) tipo `IReadOnlyList<Page>` para a pilha sem janela restrita
- [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) tipo `IReadOnlyList<Page>` para a pilha de janela restrita

É mais seguro acessar esses conjuntos do `Navigation` propriedade o `NavigationPage` (que deve ser o `App` da classe [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propriedade). Somente é seguro examinar esses conjuntos de tem concluído os métodos assíncronos de navegação de página. O [ `CurrentPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.CurrentPage/) propriedade o `NavigationPage` não indica a página atual se a página atual for uma página restrita, mas indica em vez disso, a última página sem janela restrita.

O [ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) exemplo permite que você explorar a navegação de página e as pilhas e os tipos válidos de navegação de página:

- Uma página restrita pode navegar para outra página sem janela restrita ou uma página restrita
- Uma página restrita pode navegar somente para outra página modal

### <a name="enforcing-modality"></a>Impondo modalidade

Um aplicativo usa uma página restrita quando é necessário obter algumas informações do usuário. O usuário deve ser proibido retorne à página anterior até que essa informação é fornecida. No iOS, é fácil dar uma **novamente** botão e habilitá-lo somente quando o usuário termina com a página. Mas para dispositivos de Android e Windows phone, o aplicativo deve substituir o [ `OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed()/) método e retornar `true` se o programa tratou a **novamente** botão em si, conforme demonstrado no o [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) exemplo.

O [ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) demonstra como isso funciona em um cenário MVVM.

## <a name="navigation-variations"></a>Variações de navegação

Se uma determinada página modal pode ser navegada várias vezes, ele deve reter informações para que o usuário pode editar as informações em vez de digitá-la em todos os novamente. Você pode lidar com isso, mantendo a instância específica da página restrita, mas uma abordagem melhor (principalmente em iOS) é preserva as informações em um modelo de exibição.

### <a name="making-a-navigation-menu"></a>Fazer um menu de navegação

O [ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) exemplo demonstra como usar um `TableView` para itens de menu da lista. Cada item é associado com um `Type` objeto para uma determinada página. Quando esse item é selecionado, o programa cria a página e navega para ele.

[![Captura de tela tripla do tipo de exibição de galeria](images/ch24fg21-small.png "itens de Menu do modo de tabela listando")](images/ch24fg21-large.png#lightbox "itens de Menu do modo de tabela listando")

O [ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) exemplo é um pouco diferente em que o menu contém instâncias de cada página em vez de tipos. Isso ajuda a manter as informações de cada página, mas todas as páginas devem ser instanciadas na inicialização do programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) demonstra várias funções definidas pelo `INavigation` que permitem a manipulação da pilha de navegação de uma maneira estruturada:

- [`RemovePage`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage/p/Xamarin.Forms.Page/)
- [`InsertPageBefore`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore/p/Xamarin.Forms.Page/Xamarin.Forms.Page/)
- [`PopToRootAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync()/) e [ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync/p/System.Boolean/) com animação opcional

### <a name="dynamic-page-generation"></a>Geração de página dinâmica

O [ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) exemplo demonstra como criar uma página em tempo de execução com base na entrada do usuário.

## <a name="patterns-of-data-transfer"></a>Padrões de transferência de dados

Geralmente é necessário compartilhar dados entre páginas &mdash; para transferir dados para uma página navegada e para uma página retornar dados para a página que o chamou. Há várias técnicas para fazer isso.

### <a name="constructor-arguments"></a>Argumentos de construtor

Ao navegar para uma nova página, é possível instanciar a classe da página com um argumento de construtor que permite que a página para se inicializar. O [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) exemplo demonstra isso. Também é possível para a página navegada para ter seu `BindingContext` definido pela página que navega para ele.

### <a name="properties-and-method-calls"></a>Propriedades e chamadas de método

Os exemplos de transferência de dados restantes exploram o problema de transmitir informações entre páginas quando navega de uma página para outra página e vice-versa. Essas discussões no *inicial* página navega para o *informações* página e deve transferir informações inicializadas para o *informações* página. O *informações* página obtém informações adicionais do usuário e transfere as informações para o *inicial* página.

O *inicial* página possa acessar facilmente métodos públicos e propriedades no *informações* página assim que ele cria uma instância dessa página. O *informações* página também pode acessar métodos públicos e propriedades no *inicial* página, mas a escolher um bom momento para isso pode ser complicado. O [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) exemplo faz isso no seu `OnDisappearing` substituir. Uma desvantagem é que o *informações* página precisa saber o tipo do *inicial* página.

### <a name="messagingcenter"></a>MessagingCenter

O xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) fornece uma classe de outra forma para duas páginas para se comunicar uns com os outros. As mensagens são identificadas por uma cadeia de caracteres de texto e podem ser acompanhadas por qualquer objeto.

Um programa que deseja receber mensagens de um determinado tipo deve assiná-los usando [ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender,TArgs%7D/p/System.Object/System.String/System.Action%7BTSender,TArgs%7D/TSender/) e especificar uma função de retorno de chamada. Depois ele pode cancelar sua assinatura chamando [ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/). A função de retorno de chamada recebe qualquer mensagem enviada do tipo especificado com o nome especificado enviado por meio de [ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/) método.

O [ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) programa demonstra como transferir dados usando o Centro de mensagens, mas novamente isso requer que o *informações* página saber o tipo da *inicial* página.

### <a name="events"></a>Eventos

O evento é uma abordagem de horários para uma classe enviar informações para outra classe sem saber o tipo dessa classe. No [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) exemplo o *informações* classe define um evento que dispara quando as informações estiverem prontas. No entanto, há um local conveniente para o *inicial* página para desanexar o manipulador de eventos.

### <a name="the-app-class-intermediary"></a>O intermediário de classe do aplicativo

O [ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) exemplo mostra como acessar as propriedades definidas no `App` classe ambos o *inicial* página e o *informações*página. Esta é uma boa solução, mas a próxima seção descreve uma opção melhor.

### <a name="switching-to-a-viewmodel"></a>Alternar para um ViewModel

Usando um ViewModel permite que as informações de *inicial* página e o *informações* página compartilhar a instância da classe de informações. Isso é demonstrado no [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) exemplo.

### <a name="saving-and-restoring-page-state"></a>Salvar e restaurar o estado da página

O `App` intermediário de classe ou a abordagem de ViewModel é ideal quando o aplicativo deve salvar informações se o programa entra em suspensão enquanto o *informações* página está ativa. O [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) exemplo demonstra isso.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvar e restaurar a pilha de navegação

Em geral, um programa de várias páginas que entra em suspensão deve navegar para a mesma página quando ele for restaurado. Isso significa que esse programa deve salvar o conteúdo da pilha de navegação. Esta seção mostra como automatizar esse processo em uma classe projetada para essa finalidade. Essa classe também chama as páginas individuais para permiti-las salvar e restaurar o estado da página.

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define uma interface denominada [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que classes podem implementar para salvar e restaurar os itens a `Properties`dicionário.

O [ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe no **Xamarin.FormsBook.Toolkit** biblioteca deriva de `Application`. Em seguida, você pode derivar seu `App` classe `MultiPageRestorableApp` e executar a limpeza.

O [ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) demonstra o uso de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Algo parecido com um aplicativo da vida real

O [ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) exemplo também usa `MultiPageRestorableApp` e permite a inserção e a edição das anotações que são salvos no `Properties` dicionário.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 24 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Exemplos de capítulo 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md)
