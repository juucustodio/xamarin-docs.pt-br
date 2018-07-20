---
title: Resumo do capítulo 24. Navegação da página
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 24. Navegação da página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 35130baac4025fe69dbc7aa9b6928f824b35c573
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156698"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumo do capítulo 24. Navegação da página

Muitos aplicativos consistem em várias páginas entre os quais o usuário navega. O aplicativo sempre tem um *principal* página ou *doméstica* página, e a partir daí, o usuário navega para outras páginas, que são mantidas em uma pilha para navegar de volta. Opções adicionais de navegação são abordadas [ **Capítulo 25. Página variedades**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modais e sem janela restrita

`VisualElement` define uma [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade do tipo [ `INavigation` ](xref:Xamarin.Forms.INavigation), que inclui os dois métodos para navegar até uma nova página a seguir:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Os dois métodos aceitam uma `Page` instância como um argumento e retornam um `Task` objeto. Os dois métodos a seguir navegue de volta para a página anterior:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se a interface do usuário tem seu próprio **volta** botão (como fazem em telefones Android e Windows) não é necessário para o aplicativo chamar esses métodos.

Embora esses métodos estão disponíveis de qualquer `VisualElement`, em geral, eles são chamados da `Navigation` propriedade da atual `Page` instância.

Aplicativos geralmente usam páginas modais quando o usuário é solicitado a fornecer algumas informações na página antes de retornar à página anterior. Páginas que não são restritas às vezes são chamadas sem janela restrita ou *hierárquica*. Nada na própria página distingue-o como modal ou sem-modo; é controlado em vez disso, o método usado para navegar até ele. Para trabalhar em todas as plataformas, uma página modal deve fornecer sua própria interface do usuário para navegar de volta à página anterior.

O [ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) amostra permite que você explore a diferença entre as páginas modais e sem janela restrita. Qualquer aplicativo que usa a navegação de página deve passar a sua página inicial para o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) construtor, geralmente no programa `App` classe. Um gratificação é que você não precisa definir um `Padding` na página do iOS.

Você descobrirá que sem janela restrita páginas, a página do [ `Title` ](xref:Xamarin.Forms.Page.Title) propriedade é exibida. O iOS, Android e as plataformas de tablet e área de trabalho do Windows fornecem um elemento de interface do usuário para navegar de volta para a página anterior. Curso, Android e Windows dispositivos phone têm um padrão **volta** botão Voltar.

Páginas modais, a página `Title` não for exibido, e nenhum elemento de interface do usuário é fornecido para voltar à página anterior. Embora você possa usar o padrão de Android e Windows phone **volta** botão para retornar à página anterior, a página modal em outras plataformas deve fornecer seu próprio mecanismo para voltar.

### <a name="animated-page-transitions"></a>Transições de página animados

Versões alternativas dos vários métodos de navegação são fornecidas com um segundo argumento booliano que é definido como `true` se você quiser que a transição de página para incluir uma animação:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

No entanto, os métodos de navegação de página padrão incluem a animação por padrão, somente são valiosos para navegar para uma determinada página na inicialização (como discutido no final deste capítulo) ou ao fornecer sua própria animação de entrada (como discutido em [ **Chapter22. Animação**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variações de visuais e funcionais

`NavigationPage` inclui duas propriedades que podem ser definidas quando você instancia a classe no seu `App` método:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` também inclui quatro propriedades vinculáveis anexadas que afetam a página específica no qual eles são definidos:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) e [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) e [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) e [ `GetBackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) funcionam somente no iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) e [ `GetTitleIcon` ](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) funcionam no iOS e Android somente

### <a name="exploring-the-mechanics"></a>Explorando a mecânica

Os métodos de navegação de página são todos assíncronos e deve ser usados com `await`. Após a conclusão não indica que a navegação de página foi concluída, mas apenas que é seguro examinar a pilha de navegação de página.

Quando uma página navega para outra, a primeira página geralmente recebe uma chamada para seu [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) método e a segunda página recebe uma chamada para sua [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) método. Da mesma forma, quando uma página é retornado para outra, a primeira página recebe uma chamada para seu `OnDisappearing` método e a segunda página geralmente obtém uma chamada para seu `OnAppearing` método. A ordem dessas chamadas (e a conclusão dos métodos assíncronos que invoca a navegação) é dependente da plataforma. O uso da palavra "geralmente" nas duas instruções anteriores é devido a navegação de página modal Android, em que essas chamadas de método não ocorrem.

Além disso, chamadas para o `OnAppearing` e `OnDisappearing` métodos não indicam necessariamente a navegação de página.

O `INavigation` interface inclui duas propriedades de coleção que permitem que você examinar a pilha de navegação:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) do tipo `IReadOnlyList<Page>` para a pilha sem janela restrita
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) do tipo `IReadOnlyList<Page>` para a pilha modal

É mais seguro acessar essas pilhas do `Navigation` propriedade do `NavigationPage` (que deve ser a `App` da classe [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propriedade). Só é seguro examinar essas pilhas depois de concluíram os métodos de navegação de página assíncrona. O [ `CurrentPage` ](xref:Xamarin.Forms.NavigationPage.CurrentPage) propriedade o `NavigationPage` não indica a página atual se a página atual for uma página modal, mas indica em vez disso, a última página sem janela restrita.

O [ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) exemplo permite que você explore a navegação de página e as pilhas e os tipos legais de navegação de página:

- Uma página sem janela restrita pode navegar para outra página sem janela restrita ou em uma página modal
- Uma página restrita pode navegar somente para outra página modal

### <a name="enforcing-modality"></a>Impondo a modalidade

Um aplicativo usa uma página modal quando é necessário obter algumas informações do usuário. O usuário deve ser proibido de retornar à página anterior até que essa informação é fornecida. No iOS, é fácil fornecer um **volta** botão e habilitá-lo somente quando o usuário termina com a página. Mas para dispositivos com Android e Windows phone, o aplicativo deve substituir a [ `OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) método e retorne `true` se o programa tratou o **volta** botão em si, conforme demonstrado no o [ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) exemplo.

O [ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) exemplo demonstra como isso funciona em um cenário do MVVM.

## <a name="navigation-variations"></a>Variações de navegação

Se uma determinada página modal pode ser navegada várias vezes, ele deve reter informações para que o usuário pode editar as informações em vez de digitá-lo em tudo novamente. Você pode lidar com isso, mantendo a instância específica de página modal, mas uma abordagem melhor (especialmente no iOS) é preservar as informações em um modelo de exibição.

### <a name="making-a-navigation-menu"></a>Tornando um menu de navegação

O [ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) exemplo demonstra como usar um `TableView` para listar itens de menu. Cada item está associado com um `Type` objeto para uma determinada página. Quando esse item é selecionado, o programa cria uma instância de página e navega até ela.

[![Captura de tela do tipo de exibição de galeria tripla](images/ch24fg21-small.png "itens de Menu do modo de tabela listando")](images/ch24fg21-large.png#lightbox "itens de Menu do modo de tabela listando")

O [ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) exemplo é um pouco diferente em que o menu contém instâncias de cada página em vez de tipos. Isso ajuda a manter as informações de cada página, mas todas as páginas devem ser instanciadas na inicialização do programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) demonstra as várias funções definidas pelo `INavigation` que permitem que você manipule a pilha de navegação de uma maneira estruturada:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) e [ `PopToRootAsync` ](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) com animação opcional

### <a name="dynamic-page-generation"></a>Geração de página dinâmica

O [ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) exemplo demonstra a construção de uma página em tempo de execução com base na entrada do usuário.

## <a name="patterns-of-data-transfer"></a>Padrões de transferência de dados

Geralmente é necessário compartilhar dados entre páginas &mdash; para transferir dados para uma página navegada e para uma página retornar dados para a página que a invocou. Há várias técnicas para fazer isso.

### <a name="constructor-arguments"></a>Argumentos de construtor

Ao navegar para uma nova página, é possível instanciar a classe de página com um argumento de construtor que permite que a página para se inicializar. O [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) demonstra isso. Também é possível para a página navegada ter seu `BindingContext` definido por página que navega até ela.

### <a name="properties-and-method-calls"></a>Propriedades e chamadas de método

Os exemplos de transferência de dados restantes exploram o problema de passar informações entre páginas quando navega de uma página para outra página e vice-versa. Nessas discussões, o *doméstica* página navega para o *informações* da página e deve transferir informações inicializadas para o *informações* página. O *info* página obtém informações adicionais do usuário e transfere as informações para o *doméstica* página.

O *doméstica* página pode facilmente acessar métodos públicos e propriedades na *informações* página assim que ele cria uma instância dessa página. O *info* página também pode acessar métodos públicos e propriedades na *doméstica* página, mas escolher um bom momento para que isso pode ser complicado. O [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) exemplo faz isso no seu `OnDisappearing` substituir. Uma desvantagem é que o *info* precisa saber o tipo de página a *doméstica* página.

### <a name="messagingcenter"></a>MessagingCenter

O xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe fornece outra maneira para duas páginas para se comunicar entre si. As mensagens são identificadas por uma cadeia de caracteres de texto e podem ser acompanhadas por qualquer objeto.

Um programa que deseja receber mensagens de um tipo específico deve se inscrever neles usando [ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*) e especificar uma função de retorno de chamada. Mais tarde pode cancelar assinatura chamando [ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*). A função de retorno de chamada recebe qualquer mensagem enviada do tipo especificado com o nome especificado, enviado por meio de [ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*) método.

O [ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) programa demonstra como transferir os dados usando o Centro de mensagens, mas novamente isso requer que o *informações* página saber o tipo dos *doméstica* página.

### <a name="events"></a>Eventos

O evento é uma abordagem consagrada pelo tempo para uma classe enviar informações para outra classe sem saber o tipo dessa classe. No [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) exemplo o *informações* classe define um evento que é acionado quando as informações estão prontas. No entanto, há um local conveniente para o *doméstica* página para desanexar o manipulador de eventos.

### <a name="the-app-class-intermediary"></a>O intermediário de classe do aplicativo

O [ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) exemplo mostra como acessar propriedades definidas no `App` classe por ambos o *inicial* página e o *informações*página. Isso é uma boa solução, mas a próxima seção descreve uma opção melhor.

### <a name="switching-to-a-viewmodel"></a>Alternar para um ViewModel

Usando um ViewModel para permite que as informações de *doméstica* página e o *informações* página compartilhar a instância da classe de informações. Isso é demonstrado na [ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) exemplo.

### <a name="saving-and-restoring-page-state"></a>Salvando e restaurando o estado da página

O `App` intermediário de classe ou a abordagem de ViewModel é ideal quando o aplicativo deve salvar informações se o programa entrar em suspensão enquanto o *info* página está ativa. O [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) demonstra isso.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvando e restaurando a pilha de navegação

Em geral, um programa de várias páginas que entra em suspensão deve navegar para a mesma página, quando ele for restaurado. Isso significa que um programa desse tipo deve salvar o conteúdo da pilha de navegação. Esta seção mostra como automatizar esse processo em uma classe projetada para essa finalidade. Essa classe também chama as páginas individuais para permitir que eles salvar e restaurar o estado da página.

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define uma interface denominada [ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que classes podem implementar para salvar e restaurar itens na `Properties`dicionário.

O [ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe os **Xamarin.FormsBook.Toolkit** deriva de biblioteca `Application`. Em seguida, você pode derivar sua `App` classe `MultiPageRestorableApp` e executar um pouco de manutenção.

O [ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) demonstra o uso de `MultiPageRestorableApp`.

### <a name="something-like-a-real-life-app"></a>Algo como um aplicativo da vida real

O [ **NoteTaker** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) exemplo também usa `MultiPageRestorableApp` e permite a inserção e a edição de anotações são salvos no `Properties` dicionário.



## <a name="related-links"></a>Links relacionados

- [Capítulo 24 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Exemplos do capítulo 24](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md)
