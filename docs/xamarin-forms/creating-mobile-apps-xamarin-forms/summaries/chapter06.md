---
title: Resumo do capítulo 6. Cliques de botão
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 6. Cliques de botão'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334542"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Resumo do capítulo 6. Cliques de botão

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

O [`Button`](xref:Xamarin.Forms.Button) é a exibição que permite ao usuário iniciar um comando. Uma `Button` é identificada pelo texto (e, opcionalmente, uma imagem, como demonstrado no [capítulo 13, bitmaps](chapter13.md)). Consequentemente, `Button` define muitas das mesmas propriedades que `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` também define três propriedades que regem a aparência de sua borda, mas o suporte dessas propriedades e sua independência mútua são específicos da plataforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) do tipo `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) do tipo `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) do tipo `Double`

`Button` também herda todas as propriedades de `VisualElement` e `View`, incluindo `BackgroundColor`, `HorizontalOptions`e `VerticalOptions`.

## <a name="processing-the-click"></a>O clique de processamento

A classe `Button` define um evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) que é acionado quando o usuário toca no `Button`. O manipulador de `Click` é do tipo `EventHandler`. O primeiro argumento é o `Button` objeto que gera o evento; o segundo argumento é um objeto `EventArgs` que não fornece nenhuma informação adicional.

O exemplo [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) demonstra manipulação simples de `Clicked`.

## <a name="sharing-button-clicks"></a>Cliques de botão de compartilhamento

Várias exibições de `Button` podem compartilhar o mesmo manipulador de `Clicked`, mas o manipulador geralmente precisa determinar qual `Button` é responsável por um evento específico. Uma abordagem é armazenar os vários objetos `Button` como campos e verificar qual deles está acionando o evento no manipulador.

O exemplo [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) demonstra essa técnica. O programa também demonstra como definir a propriedade [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) de uma `Button` como `false` ao pressionar o `Button` não é mais válido. Um `Button` desabilitado não gera um evento de `Clicked`.

## <a name="anonymous-event-handlers"></a>Manipuladores de eventos anônimos

É possível definir manipuladores de `Clicked` como funções lambda anônimas, como demonstra o exemplo de [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) . No entanto, os manipuladores de anônimos não podem ser compartilhados sem um código confuso de reflexão.

## <a name="distinguishing-views-with-ids"></a>Distinção entre exibições com IDs

Vários objetos `Button` também podem ser diferenciados pela definição da propriedade [`StyleId`](xref:Xamarin.Forms.Element.StyleId) ou [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) Propriedade como um `string`. Essa propriedade é definida por `Element`, mas não é usada dentro do Xamarin. Forms. Ele destina-se a ser usado exclusivamente pelos programas de aplicativo.

O exemplo [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) usa o mesmo manipulador de eventos para todas as 10 chaves de número em um teclado numérico e faz distinção entre elas com a propriedade `StyleId`:

[![Captura de tela tripla do teclado mais simples](images/ch06fg04-small.png "Calculadora")](images/ch06fg04-large.png#lightbox "Calculadora")

## <a name="saving-transient-data"></a>Salvando dados transitórios

Muitos aplicativos precisam para salvar os dados quando um programa é encerrado e recarregar os dados quando o programa for iniciado novamente. A classe [`Application`](xref:Xamarin.Forms.Application) define vários membros que ajudam o programa a salvar e restaurar dados transitórios:

- A propriedade [`Properties`](xref:Xamarin.Forms.Application.Properties) é um dicionário com chaves de `string` e itens de `object`. O conteúdo do dicionário automaticamente é salvos no armazenamento local do aplicativo antes do encerramento do programa e recarregado quando o programa é iniciado.
- A classe `Application` define três métodos virtuais protegidos que a classe de `App` padrão do programa substitui: [`OnStart`](xref:Xamarin.Forms.Application.OnStart), [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep)e [`OnResume`](xref:Xamarin.Forms.Application.OnResume). Eles se referem a eventos de *ciclo de vida do aplicativo* .
- O método [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) salva o conteúdo do dicionário.

Não é necessário chamar `SavePropertiesAsync`. O conteúdo do dicionário é automaticamente salvas antes do encerramento do programa e recuperado antes da inicialização do programa. É útil durante o teste de programa para salvar dados em caso de falha do programa.

Também é útil:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), uma propriedade estática que retorna o objeto `Application` atual que você pode usar para obter o dicionário de `Properties`.

A primeira etapa é identificar todas as variáveis na página que você deseja persistir quando o programa é encerrado. Se você souber todos os locais em que essas variáveis são alteradas, poderá simplesmente adicioná-las ao dicionário de `Properties` nesse ponto. No construtor da página, você pode definir as variáveis do dicionário de `Properties` se a chave existir.

Um programa maior provavelmente precisará lidar com eventos de ciclo de vida do aplicativo. O mais importante é o método `OnSleep`. Uma chamada para esse método indica que o programa tenha deixado em primeiro plano. Talvez o usuário tenha pressionado o botão **página inicial** no dispositivo ou exibido todos os aplicativos ou esteja encerrando o telefone. Uma chamada para `OnSleep` é a única notificação que um programa recebe antes de ser encerrado. O programa deve levar essa oportunidade para garantir que o dicionário de `Properties` esteja atualizado.

Uma chamada para `OnResume` indica que o programa não foi encerrado após a última chamada para `OnSleep`, mas agora está em execução no primeiro plano novamente. O programa pode usar essa oportunidade para atualizar as conexões de internet (por exemplo).

Uma chamada para `OnStart` ocorre durante a inicialização do programa. Não é necessário aguardar até que essa chamada de método acesse o dicionário de `Properties` porque o conteúdo já foi restaurado quando o construtor de `App` é chamado.

O exemplo de [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) é muito semelhante a **SimplestKeypad** , exceto pelo fato de que o programa usa a substituição `OnSleep` para salvar a entrada do teclado atual e o construtor da página para restaurar esses dados.

> [!NOTE]
> Outra abordagem para salvar as configurações do programa é fornecida pela classe de [preferências](~/essentials/preferences.md) do Xamarin. Essentials.

## <a name="related-links"></a>Links relacionados

- [Capítulo 6 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Exemplos do capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Exemplos do F# capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Botão Xamarin. Forms](~/xamarin-forms/user-interface/button.md)
