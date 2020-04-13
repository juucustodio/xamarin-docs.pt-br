---
title: Resumo do Capítulo 24. Navegação da página
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 24. Navegação da página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291477"
---
# <a name="summary-of-chapter-24-page-navigation"></a>Resumo do Capítulo 24. Navegação da página

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

Muitos aplicativos consistem em várias páginas entre as quais o usuário navega. O aplicativo sempre tem uma página *principal* ou *página inicial,* e a partir daí o usuário navega para outras páginas, que são mantidas em uma pilha para navegar de volta. Opções adicionais de navegação são cobertas no [**Capítulo 25. Variedades de página**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>Páginas modais e páginas modeladas

`VisualElement`define uma [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade [`INavigation`](xref:Xamarin.Forms.INavigation)de tipo, que inclui os dois métodos a seguir para navegar até uma nova página:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

Ambos os métodos aceitam uma `Page` `Task` instância como argumento e devolvem um objeto. Os dois métodos a seguir navegam de volta à página anterior:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

Se a interface do usuário tiver seu próprio botão **Back** (como os telefones Android e Windows fazem), então não é necessário que o aplicativo chame esses métodos.

Embora esses métodos estejam `VisualElement`disponíveis de qualquer, `Navigation` geralmente são `Page` chamados da propriedade da instância atual.

Os aplicativos geralmente usam páginas modais quando o usuário é obrigado a fornecer algumas informações na página antes de retornar à página anterior. Páginas que não são modais às vezes são chamadas de modeless ou *hierárquicas.* Nada na página em si o distingue como modal ou modeless; é governado pelo método usado para navegar até ele. Para trabalhar em todas as plataformas, uma página modal deve fornecer sua própria interface de usuário para navegar de volta à página anterior.

A amostra [**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal) permite explorar a diferença entre modeless e páginas modais. Qualquer aplicativo que use navegação de página [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) deve passar sua página inicial `App` para o construtor, geralmente na classe do programa. Um bônus é que você não `Padding` precisa mais definir uma na página para iOS.

Você descobrirá que, para páginas de [`Title`](xref:Xamarin.Forms.Page.Title) modelagem, a propriedade da página é exibida. O iOS, o Android e as plataformas de tablet e desktop do Windows fornecem um elemento de interface de usuário para navegar de volta à página anterior. Claro, os dispositivos telefônicos Android e Windows têm um botão **de volta** padrão para voltar.

Para páginas modais, `Title` a página não é exibida e nenhum elemento de interface de usuário é fornecido para voltar à página anterior. Embora você possa usar o botão **De volta** padrão do telefone Android e Windows para retornar à página anterior, a página modal nas outras plataformas deve fornecer seu próprio mecanismo para voltar.

### <a name="animated-page-transitions"></a>Transições de página animada

Versões alternativas dos vários métodos de navegação são fornecidas `true` com um segundo argumento booleano que você define se quiser que a transição de página inclua uma animação:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [Popasync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

No entanto, os métodos padrão de navegação de página incluem a animação por padrão, de modo que estes são apenas valiosos para navegar para uma determinada página na inicialização (como discutido no final deste capítulo) ou ao fornecer sua própria animação de entrada (como discutido no [**Capítulo22. Animação**](chapter22.md)).

### <a name="visual-and-functional-variations"></a>Variações visuais e funcionais

`NavigationPage`inclui duas propriedades que você pode definir quando `App` instanciar a classe em seu método:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage`também inclui quatro propriedades vinculáveis anexadas que afetam a página específica na qual estão definidas:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean))E[`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean))E[`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String))e [`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject)) trabalhar apenas no iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource))e [`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) trabalhar apenas no iOS e Android

### <a name="exploring-the-mechanics"></a>Explorando a mecânica

Os métodos de navegação de página são todos `await`assíncronos e devem ser usados com . A conclusão não indica que a navegação de página foi concluída, mas apenas que é seguro examinar a pilha de navegação de página.

Quando uma página navega para outra, a primeira [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) página geralmente recebe uma chamada para [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) o seu método, e a segunda página recebe uma chamada para o seu método. Da mesma forma, quando uma página retorna para outra, a primeira página recebe uma chamada para o seu `OnDisappearing` método, e a segunda página geralmente recebe uma chamada para o seu `OnAppearing` método. A ordem dessas chamadas (e a conclusão dos métodos assíncronos que invocam a navegação) depende da plataforma. O uso da palavra "geralmente" nas duas declarações anteriores deve-se à navegação de página modal do Android, na qual essas chamadas de método não ocorrem.

Além disso, `OnAppearing` chamadas `OnDisappearing` para os métodos e métodos não indicam necessariamente navegação de página.

A `INavigation` interface inclui duas propriedades de coleção que permitem examinar a pilha de navegação:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)de `IReadOnlyList<Page>` tipo para a pilha de modeless
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)de `IReadOnlyList<Page>` tipo para a pilha modal

É mais seguro acessar essas `Navigation` pilhas `NavigationPage` a partir da `App` propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage) do (que deve ser propriedade da classe). Só é seguro examinar essas pilhas depois que os métodos assíncronos de navegação de página tiverem sido concluídos. A [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) propriedade `NavigationPage` do não indica a página atual se a página atual é uma página modal, mas indica, em vez disso, a última página modeless.

A amostra [**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation) permite explorar a navegação de página e as pilhas e os tipos legais de navegação de página:

- Uma página de modelagem pode navegar para outra página modeless ou uma página modal
- Uma página modal pode navegar apenas para outra página modal

### <a name="enforcing-modality"></a>Modalidade de imposição

Um aplicativo usa uma página modal quando é necessário obter algumas informações do usuário. O usuário deve ser proibido de retornar à página anterior até que essas informações sejam fornecidas. No iOS, é fácil fornecer um botão **Voltar** e habilitá-lo somente quando o usuário terminar com a página. Mas para dispositivos telefônicos Android e Windows, [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) o `true` aplicativo deve substituir o método e retornar se o programa tiver lidado com o próprio botão **Voltar,** como demonstrado na amostra [**ModalEnforcement.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)

A amostra [**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement) demonstra como isso funciona em um cenário MVVM.

## <a name="navigation-variations"></a>Variações de navegação

Se uma página modal específica pode ser navegada várias vezes, ela deve reter informações para que o usuário possa editar as informações em vez de digitar tudo novamente. Você pode lidar com isso mantendo a instância específica da página modal, mas uma abordagem melhor (particularmente no iOS) é preservar as informações em um modelo de exibição.

### <a name="making-a-navigation-menu"></a>Fazendo um menu de navegação

A amostra [**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType) `TableView` demonstra o uso de itens de menu para listar. Cada item está `Type` associado a um objeto para uma página específica. Quando esse item é selecionado, o programa instancia a página e navega até ela.

[![Captura de tela tripla do Tipo de Galeria de Exibição](images/ch24fg21-small.png "Itens do menu de listagem tableview")](images/ch24fg21-large.png#lightbox "Itens do menu de listagem tableview")

A amostra [**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst) é um pouco diferente na época em que o menu contém instâncias de cada página em vez de tipos. Isso ajuda a reter as informações de cada página, mas todas as páginas devem ser instanciadas na inicialização do programa.

### <a name="manipulating-the-navigation-stack"></a>Manipulação da pilha de navegação

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) demonstra várias `INavigation` funções definidas por que permitem manipular a pilha de navegação de forma estruturada:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)e [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) com animação opcional

### <a name="dynamic-page-generation"></a>Geração dinâmica de páginas

A amostra [**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage) demonstra a construção de uma página em tempo de execução com base na entrada do usuário.

## <a name="patterns-of-data-transfer"></a>Padrões de transferência de dados

Muitas vezes é necessário compartilhar &mdash; dados entre páginas para transferir dados para uma página navegada e para que uma página retorne dados para a página que os invocou. Existem várias técnicas para fazer isso.

### <a name="constructor-arguments"></a>Argumentos de construtores

Ao navegar para uma nova página, é possível instanciar a classe de página com um argumento construtor que permite que a página se inicialize. A [**amostra de estudantes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents) e estudantes demonstra isso. Também é possível que a página navegada tenha sua `BindingContext` configuração pela página que navega até ela.

### <a name="properties-and-method-calls"></a>Propriedades e chamadas de método

Os exemplos restantes de transferência de dados exploram o problema de passar informações entre páginas quando uma página navega para outra página e volta. Nessas discussões, a *página inicial* navega para a página de *informações* e deve transferir informações inicializadas para a página *de informações.* A página *de informações* obtém informações adicionais do usuário e transfere as informações para a *página inicial.*

A *página inicial* pode acessar facilmente métodos e propriedades públicas na página de *informações* assim que instanciaessa essa página. A página *de informações* também pode acessar métodos e propriedades públicas na *página inicial,* mas escolher um bom momento para isso pode ser complicado. A amostra [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1) faz `OnDisappearing` isso em sua substituição. Uma desvantagem é que a página *de informações* precisa saber o tipo da *página inicial.*

### <a name="messagingcenter"></a>MessagingCenter

A classe Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) fornece outra maneira para que duas páginas se comuniquem entre si. As mensagens são identificadas por uma seqüência de texto e podem ser acompanhadas por qualquer objeto.

Um programa que deseja receber mensagens de um [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) determinado tipo deve subscrevê-las usando e especificar uma função de retorno de chamada. Mais tarde, ele [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)pode cancelar a inscrição ligando . A função de retorno de chamada recebe qualquer mensagem enviada [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) do tipo especificado com o nome especificado enviado através do método.

O programa [**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2) demonstra como transferir dados usando o centro de mensagens, mas novamente isso requer que a página de *informações* saiba o tipo da *página inicial.*

### <a name="events"></a>Eventos

O evento é uma abordagem honrada para uma classe enviar informações para outra classe sem saber o tipo dessa classe. Na amostra [**DateTransfer3,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) a classe *de informações* define um evento que é acionado quando as informações estão prontas. No entanto, não há um lugar conveniente para a *página inicial* para desprender o manipulador de eventos.

### <a name="the-app-class-intermediary"></a>O intermediário da classe App

A amostra [**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4) mostra como acessar `App` propriedades definidas na classe tanto pela página *inicial* quanto pela página *de informações.* Esta é uma boa solução, mas a próxima seção descreve algo melhor.

### <a name="switching-to-a-viewmodel"></a>Mudando para um ViewModel

O uso de um ViewModel para obter as informações permite que a *página inicial* e a página *de informações* compartilhem a instância da classe de informações. Isso é demonstrado na amostra [**DateTransfer5.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)

### <a name="saving-and-restoring-page-state"></a>Salvando e restaurando o estado da página

O `App` intermediário de classe ou a abordagem ViewModel é ideal quando o aplicativo deve salvar informações se o programa for dormir enquanto a página *de informações* estiver ativa. A amostra [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6) demonstra isso.

## <a name="saving-and-restoring-the-navigation-stack"></a>Salvando e restaurando a pilha de navegação

No caso geral, um programa de várias páginas que vai dormir deve navegar para a mesma página quando for restaurado. Isso significa que tal programa deve salvar o conteúdo da pilha de navegação. Esta seção mostra como automatizar esse processo em uma classe projetada para este fim. Esta classe também chama as páginas individuais para permitir que elas salvem e restaurem seu estado de página.

A biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) uma interface nomeada que as classes podem `Properties` implementar para salvar e restaurar itens no dicionário.

A [`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) classe na biblioteca **Xamarin.FormsBook.Toolkit** deriva de `Application`. Você pode, `App` então, `MultiPageRestorableApp` obter sua classe e realizar algumas limpezas.

O [**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo) demonstra `MultiPageRestorableApp`o uso de .

### <a name="something-like-a-real-life-app"></a>Algo como um aplicativo da vida real

A amostra [**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker) também `MultiPageRestorableApp` faz uso e permite a entrada e `Properties` edição de notas que são salvas no dicionário.

## <a name="related-links"></a>Links relacionados

- [Capítulo 24 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [Capítulo 24 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md)
