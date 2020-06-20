---
title: Resumo do capítulo 24. Navegação na página
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 24. Navegação na página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09622adc269027b589a7345a7d4411c3dcecbf0c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136637"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumo do capítulo 24. Navegação na página

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Muitos aplicativos consistem em várias páginas entre as quais o usuário navega. O aplicativo sempre tem uma página *principal* ou *Home* Page e, a partir daí, o usuário navega para outras páginas, que são mantidas em uma pilha para navegar de volta. As opções de navegação adicionais são abordadas no [**capítulo 25. Variedades de páginas**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modais e páginas sem janela restrita

`VisualElement`define uma [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) Propriedade do tipo [`INavigation`](xref:Xamarin.Forms.INavigation) , que inclui os dois métodos a seguir para navegar para uma nova página:

- [ `PushAsync` ] (xref: Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Página))
- [ `PushModalAsync` ] (xref: Xamarin.Forms . INavigation. PushModalAsync ( Xamarin.Forms . Página))

Os dois métodos aceitam uma `Page` instância como um argumento e retornam um `Task` objeto. Os dois métodos a seguir navegam de volta para a página anterior:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se a interface do usuário tiver seu próprio botão **voltar** (como telefones com Android e Windows), não será necessário que o aplicativo chame esses métodos.

Embora esses métodos estejam disponíveis em qualquer um `VisualElement` , geralmente eles são chamados da `Navigation` propriedade da instância atual `Page` .

Os aplicativos geralmente usam páginas modais quando o usuário é solicitado a fornecer algumas informações na página antes de retornar à página anterior. As páginas que não são modais são, às vezes, chamadas de modelo ou *hierárquicas*. Nada na própria página o distingue como modal ou sem janela restrita; em vez disso, ele é regido pelo método usado para navegar até ele. Para trabalhar em todas as plataformas, uma página modal deve fornecer sua própria interface de usuário para navegar de volta para a página anterior.

O exemplo [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) permite que você explore a diferença entre as páginas sem janela restrita e modal. Qualquer aplicativo que usa a navegação de página deve passar seu home page para o [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) Construtor, geralmente na classe do programa `App` . Um bônus é que você não precisa mais definir um `Padding` na página para Ios.

Você descobrirá que, para páginas sem janela restrita, a [`Title`](xref:Xamarin.Forms.Page.Title) propriedade da página será exibida. O iOS, Android e as plataformas Windows Tablet e desktop fornecem um elemento user-interface para navegar de volta para a página anterior. É claro que os dispositivos Android e Windows Phone têm um botão **voltar** padrão para voltar.

Para páginas modais, a página `Title` não é exibida e nenhum elemento de interface de usuário é fornecido para voltar à página anterior. Embora você possa usar o botão **voltar** padrão do Android e do Windows Phone para retornar à página anterior, a página modal nas outras plataformas deve fornecer seu próprio mecanismo para voltar.

### <a name="animated-page-transitions"></a>Transições de página animadas

Versões alternativas dos vários métodos de navegação são fornecidas com um segundo argumento booliano definido como `true` se você quiser que a transição de página inclua uma animação:

- PushAsync (xref: Xamarin.Forms . INavigation. PushAsync ( Xamarin.Forms . Page, System. Boolean))
- [PushModalAsync] (xref: Xamarin.Forms . INavigation. PushModalAsync ( Xamarin.Forms . Page, System. Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

No entanto, os métodos de navegação de página padrão incluem a animação por padrão, portanto, esses são apenas valiosos para navegar para uma página específica na inicialização (conforme discutido no final deste capítulo) ou ao fornecer sua própria animação de entrada (como discutido em [**Chapter22. Animação**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variações visuais e funcionais

`NavigationPage`inclui duas propriedades que você pode definir ao instanciar a classe em seu `App` método:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage`também inclui quatro propriedades vinculáveis anexadas que afetam a página específica na qual elas são definidas:

- [ `SetHasBackButton` ] (xref: Xamarin.Forms . NavigationPage. SetHasBackButton ( Xamarin.Forms . Page, System. Boolean)) e [ `GetHasBackButton` ] (xref: Xamarin.Forms . NavigationPage. GetHasBackButton ( Xamarin.Forms . Página))
- [ `SetHasNavigationBar` ] (xref: Xamarin.Forms . NavigationPage. SetHasNavigationBar ( Xamarin.Forms . Vinculobject, System. Boolean)) e [ `GetHasNavigationBar` ] (xref: Xamarin.Forms . NavigationPage. GetHasNavigationBar ( Xamarin.Forms . Bindobject))
- [ `SetBackButtonTitle` ] (xref: Xamarin.Forms . NavigationPage. SetBackButtonTitle ( Xamarin.Forms . Vinculobject, System. String) e [ `GetBackButtonTitle` ] (xref: Xamarin.Forms . NavigationPage. GetBackButtonTitle ( Xamarin.Forms . Acopláobject)) funciona somente no iOS
- [ `SetTitleIcon` ] (xref: Xamarin.Forms . NavigationPage. SetTitleIcon ( Xamarin.Forms . Vinculobject, Xamarin.Forms . Fileimagename)) e [ `GetTitleIcon` ] (xref: Xamarin.Forms . NavigationPage. GetTitleIcon ( Xamarin.Forms . Acopláobject)) funciona apenas em iOS e Android

### <a name="exploring-the-mechanics"></a>Explorando a mecânica

Os métodos de navegação de página são todos assíncronos e devem ser usados com `await` . A conclusão não indica que a navegação da página foi concluída, mas apenas que é seguro examinar a pilha de navegação de página.

Quando uma página navega para outra, a primeira página geralmente obtém uma chamada para seu [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) método e a segunda página Obtém uma chamada para seu [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) método. Da mesma forma, quando uma página retorna para outra, a primeira página Obtém uma chamada para seu `OnDisappearing` método, e a segunda página geralmente obtém uma chamada para seu `OnAppearing` método. A ordem dessas chamadas (e a conclusão dos métodos assíncronos que invocam a navegação) é dependente da plataforma. O uso da palavra "geralmente" nas duas instruções anteriores é devido à navegação de página restrita do Android, na qual essas chamadas de método não ocorrem.

Além disso, as chamadas para os `OnAppearing` `OnDisappearing` métodos e não indicam necessariamente a navegação da página.

A `INavigation` interface inclui duas propriedades de coleção que permitem examinar a pilha de navegação:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)do tipo `IReadOnlyList<Page>` para a pilha sem janela restrita
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)do tipo `IReadOnlyList<Page>` para a pilha modal

É mais seguro acessar essas pilhas a partir da `Navigation` Propriedade do `NavigationPage` (que deve ser a `App` propriedade da classe [`MainPage`](xref:Xamarin.Forms.Application.MainPage) ). Só é seguro examinar essas pilhas após a conclusão dos métodos de navegação de página assíncrona. A [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) propriedade de não `NavigationPage` indicará a página atual se a página atual for uma página modal, mas indicará a última página sem janela restrita.

O exemplo [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) permite explorar a navegação de página e as pilhas e os tipos legais de navegação de página:

- Uma página sem janela restrita pode navegar para outra página de modelos ou uma página modal
- Uma página modal pode navegar apenas para outra página modal

### <a name="enforcing-modality"></a>Impondo a modalidade

Um aplicativo usa uma página modal quando é necessário obter algumas informações do usuário. O usuário deve ser proibido de retornar à página anterior até que essas informações sejam fornecidas. No iOS, é fácil fornecer um botão **voltar** e habilitá-lo somente quando o usuário tiver concluído a página. Mas para dispositivos Android e Windows Phone, o aplicativo deve substituir o [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) método e retornar `true` se o programa tratou o botão **voltar** , conforme demonstrado no exemplo de [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) .

O exemplo [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) demonstra como isso funciona em um cenário MVVM.

## <a name="navigation-variations"></a>Variações de navegação

Se uma determinada página modal pode ser navegada para várias vezes, ela deve reter informações para que o usuário possa editar as informações em vez de digitá-las novamente. Você pode lidar com isso retendo a instância específica da página modal, mas uma abordagem melhor (especialmente no iOS) é preservar as informações em um modelo de exibição.

### <a name="making-a-navigation-menu"></a>Criando um menu de navegação

O exemplo [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) demonstra o uso de um `TableView` para itens de menu de lista. Cada item é associado a um `Type` objeto para uma página específica. Quando esse item é selecionado, o programa instancia a página e navega para ela.

[![Captura de tela tripla do tipo de galeria de exibição](images/ch24fg21-small.png "Itens de menu de listagem de TableView")](images/ch24fg21-large.png#lightbox "Itens de menu de listagem de TableView")

O exemplo de [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) é um pouco diferente, pois o menu contém instâncias de cada página em vez de tipos. Isso ajuda a reter as informações de cada página, mas todas as páginas devem ser instanciadas na inicialização do programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

O [**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) demonstra várias funções definidas pelo `INavigation` que permitem manipular a pilha de navegação de maneira estruturada:

- [ `RemovePage` ] (xref: Xamarin.Forms . INavigation. RemovePage ( Xamarin.Forms . Página))
- [ `InsertPageBefore` ] (xref: Xamarin.Forms . INavigation. InsertPageBefore ( Xamarin.Forms . Página, Xamarin.Forms . Página))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)e [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) com animação opcional

### <a name="dynamic-page-generation"></a>Geração de página dinâmica

O exemplo [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) demonstra a construção de uma página no tempo de execução com base na entrada do usuário.

## <a name="patterns-of-data-transfer"></a>Padrões de transferência de dados

Geralmente, é necessário compartilhar dados entre páginas &mdash; para transferir dados para uma página navegada e para que uma página retorne dados para a página que a invocou. Há várias técnicas para fazer isso.

### <a name="constructor-arguments"></a>Argumentos do Construtor

Ao navegar para uma nova página, é possível instanciar a classe de página com um argumento de construtor que permite que a página se inicialize. O exemplo [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) demonstra isso. Também é possível que a página navegada tenha sua `BindingContext` definição pela página que navega até ela.

### <a name="properties-and-method-calls"></a>Propriedades e chamadas de método

Os exemplos de transferência de dados restantes exploram o problema de passar informações entre páginas quando uma página navega para outra página e vice-versa. Nessas discussões, a *Home* Page navega até a página de *informações* e deve transferir informações inicializadas para a página *informações* . A página *informações* Obtém informações adicionais do usuário e transfere as informações para a *Home* Page.

A *Home* Page pode acessar facilmente métodos públicos e propriedades na página de *informações* assim que instancia essa página. A página *informações* também pode acessar métodos públicos e propriedades na *Home* Page, mas escolher um bom momento para isso pode ser complicado. O exemplo de [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) faz isso em sua `OnDisappearing` substituição. Uma desvantagem é que a página de *informações* precisa saber o tipo da *Home* Page.

### <a name="messagingcenter"></a>MessagingCenter

A Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe fornece outra maneira para que duas páginas se comuniquem umas com as outras. As mensagens são identificadas por uma cadeia de texto e podem ser acompanhadas por qualquer objeto.

Um programa que deseja receber mensagens de um tipo específico deve assiná-las usando [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) e especificar uma função de retorno de chamada. Posteriormente, ele pode cancelar a assinatura chamando [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) . A função de retorno de chamada recebe qualquer mensagem enviada do tipo especificado com o nome especificado enviado por meio do [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método.

O programa [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) demonstra como transferir dados usando o centro de mensagens, mas novamente isso requer que a página de *informações* saiba o tipo da *Home* Page.

### <a name="events"></a>Eventos

O evento é uma abordagem com tempo hábil para uma classe enviar informações para outra classe sem saber o tipo dessa classe. No exemplo de [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) , a classe *info* define um evento que ele dispara quando as informações estão prontas. No entanto, não há nenhum lugar conveniente para a *Home* Page desanexar o manipulador de eventos.

### <a name="the-app-class-intermediary"></a>O intermediário da classe de aplicativo

O exemplo [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) mostra como acessar as propriedades definidas na `App` classe pela *Home* Page e a página de *informações* . Essa é uma boa solução, mas a próxima seção descreve algo melhor.

### <a name="switching-to-a-viewmodel"></a>Alternando para um ViewModel

O uso de um ViewModel para as informações permite que a *Home* Page e a página de *informações* compartilhem a instância da classe Information. Isso é demonstrado no exemplo de [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5) .

### <a name="saving-and-restoring-page-state"></a>Salvando e restaurando o estado da página

A `App` abordagem de classe intermediária ou ViewModel é ideal quando o aplicativo deve salvar informações se o programa entrar em suspensão enquanto a página de *informações* estiver ativa. O exemplo [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) demonstra isso.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvando e restaurando a pilha de navegação

No caso geral, um programa de multipáginas que vai para a suspensão deve navegar para a mesma página quando é restaurado. Isso significa que um programa desse tipo deve salvar o conteúdo da pilha de navegação. Esta seção mostra como automatizar esse processo em uma classe criada para essa finalidade. Essa classe também chama as páginas individuais para permitir que elas salvem e restaurem seu estado de página.

A biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define uma interface chamada [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) que as classes podem implementar para salvar e restaurar itens no `Properties` dicionário.

A [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe na biblioteca **Xamarin. FormsBook. Toolkit** deriva de `Application` . Em seguida, você pode derivar sua `App` classe de `MultiPageRestorableApp` e executar algumas realizações de manutenção.

O [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) demonstra o uso de `MultiPageRestorableApp` .

### <a name="something-like-a-real-life-app"></a>Algo como um aplicativo da vida real

O exemplo de [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) também usa `MultiPageRestorableApp` e permite a inserção e edição de anotações que são salvas no `Properties` dicionário.

## <a name="related-links"></a>Links relacionados

- [Capítulo 24 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Capítulo 24 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md)
