---
title: Resumo do Capítulo 6. Cliques no botão
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 6. Cliques no botão'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334542"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Resumo do Capítulo 6. Cliques no botão

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

A [`Button`](xref:Xamarin.Forms.Button) é a visão que permite ao usuário iniciar um comando. A `Button` é identificado por texto (e opcionalmente uma imagem como demonstrado no [Capítulo 13, Bitmaps](chapter13.md)). Consequentemente, `Button` define muitas das mesmas propriedades que: `Label`

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button`também define três propriedades que regem o aparecimento de sua fronteira, mas o apoio dessas propriedades e sua independência mútua é específico da plataforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)de tipo`Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)de tipo`Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)de tipo`Double`

`Button`também herda todas `VisualElement` as `View`propriedades `BackgroundColor` `HorizontalOptions`de `VerticalOptions`e , incluindo , e .

## <a name="processing-the-click"></a>Processando o clique

A `Button` classe define [`Clicked`](xref:Xamarin.Forms.Button.Clicked) um evento que é acionado `Button`quando o usuário toca no . O `Click` manipulador é `EventHandler`do tipo . O primeiro argumento `Button` é o objeto que gera o evento; o segundo argumento `EventArgs` é um objeto que não fornece informações adicionais.

A amostra [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) demonstra um manuseio simples. `Clicked`

## <a name="sharing-button-clicks"></a>Cliques no botão de compartilhamento

Várias `Button` visualizações `Clicked` podem compartilhar o mesmo manipulador, `Button` mas o manipulador geralmente precisa determinar qual é o responsável por um evento específico. Uma abordagem é `Button` armazenar os vários objetos como campos e verificar qual deles está disparando o evento no manipulador.

A amostra [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) demonstra essa técnica. O programa também demonstra [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) como definir `Button` `false` a propriedade `Button` de a quando pressionar o não é mais válido. Um `Button` deficiente não `Clicked` gera um evento.

## <a name="anonymous-event-handlers"></a>Manipuladores anônimos de eventos

É possível definir `Clicked` manipuladores como funções de lambda anônimas, como demonstra a amostra [**ButtonLambdas.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) No entanto, manipuladores anônimos não podem ser compartilhados sem algum código de reflexão confuso.

## <a name="distinguishing-views-with-ids"></a>Distinguindo pontos de vista com IDs

Vários `Button` objetos também podem ser [`StyleId`](xref:Xamarin.Forms.Element.StyleId) distinguidos definindo a propriedade ou [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) propriedade como um `string`. Esta propriedade é `Element` definida por, mas não é usada dentro de Xamarin.Forms. Destina-se a ser usado exclusivamente por programas de aplicação.

A amostra [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) usa o mesmo manipulador de eventos para todas as teclas de `StyleId` 10 números em um teclado numérico e distingue entre eles com a propriedade:

[![Captura de tela tripla do teclado mais simples](images/ch06fg04-small.png "Calculator")](images/ch06fg04-large.png#lightbox "Calculator")

## <a name="saving-transient-data"></a>Salvando dados transitórios

Muitos aplicativos precisam salvar dados quando um programa é encerrado e recarregar esses dados quando o programa é iniciado novamente. A [`Application`](xref:Xamarin.Forms.Application) classe define vários membros que ajudam seu programa a salvar e restaurar dados transitórios:

- A [`Properties`](xref:Xamarin.Forms.Application.Properties) propriedade é `string` um `object` dicionário com chaves e itens. O conteúdo do dicionário é automaticamente salvo no armazenamento local do aplicativo antes do término do programa e recarregado quando o programa é iniciado.
- A `Application` classe define três métodos virtuais protegidos `App` que a [`OnStart`](xref:Xamarin.Forms.Application.OnStart) [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep)classe [`OnResume`](xref:Xamarin.Forms.Application.OnResume)padrão do programa substitui: , e . Estes referem-se a eventos *do ciclo de vida do aplicativo.*
- O [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) método salva o conteúdo do dicionário.

Não é necessário `SavePropertiesAsync`ligar. O conteúdo do dicionário é salvo automaticamente antes do término do programa e recuperado antes da inicialização do programa. É útil durante os testes do programa para salvar dados se o programa falhar.

Também é útil:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), uma propriedade estática `Application` que retorna o objeto `Properties` atual que você pode usar para obter o dicionário.

O primeiro passo é identificar todas as variáveis na página que você deseja persistir quando o programa terminar. Se você conhece todos os lugares onde essas variáveis mudam, `Properties` você pode simplesmente adicioná-las ao dicionário nesse ponto. No construtor da página, você pode definir as `Properties` variáveis do dicionário se a chave existir.

Um programa maior provavelmente precisará lidar com eventos do ciclo de vida do aplicativo. O mais importante `OnSleep` é o método. Uma chamada para este método indica que o programa deixou o primeiro plano. Talvez o usuário tenha pressionado o botão **Home** no dispositivo, ou exibido todos os aplicativos, ou está desligando o telefone. Uma chamada `OnSleep` é a única notificação que um programa recebe antes de ser encerrado. O programa deve aproveitar essa `Properties` oportunidade para garantir que o dicionário esteja atualizado.

Uma chamada `OnResume` para indicar que o programa não `OnSleep` terminou após a última chamada, mas agora está sendo executado em primeiro plano novamente. O programa pode usar essa oportunidade para atualizar conexões com a internet (por exemplo).

Uma chamada `OnStart` para ocorrer durante a inicialização do programa. Não é necessário esperar até que este `Properties` método chame para acessar o `App` dicionário porque os conteúdos já foram restaurados quando o construtor é chamado.

A amostra [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) é muito semelhante ao **SimplestKeypad,** exceto que o programa usa a `OnSleep` substituição para salvar a entrada atual do teclado e o construtor de páginas para restaurar esses dados.

> [!NOTE]
> Outra abordagem para salvar as configurações do programa é fornecida pela classe Xamarin.Essentials [Preferences.](~/essentials/preferences.md)

## <a name="related-links"></a>Links relacionados

- [Capítulo 6 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Capítulo 6 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Capítulo 6 F# amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Botão Xamarin.Forms](~/xamarin-forms/user-interface/button.md)
