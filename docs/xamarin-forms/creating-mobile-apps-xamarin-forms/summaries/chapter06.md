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
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057582"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Resumo do capítulo 6. Cliques de botão

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

O [ `Button` ](xref:Xamarin.Forms.Button) é o modo de exibição que permite que o usuário iniciar um comando. Um `Button` é identificado pelo texto (e, opcionalmente, uma imagem conforme demonstrado [capítulo 13, Bitmaps](chapter13.md)). Consequentemente, `Button` define muitas das mesmas propriedades que `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` também define três propriedades que determinam a aparência de sua borda, mas o suporte dessas propriedades e seu mútua independência é específico da plataforma:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) do tipo `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) do tipo `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) do tipo `Double`

`Button` também herda todas as propriedades de `VisualElement` e `View`, incluindo `BackgroundColor`, `HorizontalOptions`, e `VerticalOptions`.

## <a name="processing-the-click"></a>O clique de processamento

O `Button` classe define um [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento que é acionado quando o usuário toca o `Button`. O `Click` manipulador é do tipo `EventHandler`. O primeiro argumento é o `Button` gera o evento de objeto; o segundo argumento é um `EventArgs` objeto que não fornece nenhuma informação adicional.

O [ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) demonstra simples `Clicked` tratamento.

## <a name="sharing-button-clicks"></a>Cliques de botão de compartilhamento

Vários `Button` modos de exibição podem compartilhar a mesma `Clicked` manipulador, mas o manipulador geralmente precisa determinar qual `Button` é responsável por um evento específico. Uma abordagem é armazenar vários `Button` objetos como campos e verificar qual deles está acionando o evento no manipulador.

O [ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) que demonstra essa técnica. O programa também demonstra como definir a [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade de uma `Button` para `false` ao pressionar o `Button` não é mais válido. R desabilitado `Button` não gera um `Clicked` eventos.

## <a name="anonymous-event-handlers"></a>Manipuladores de eventos anônimos

É possível definir `Clicked` manipuladores como funções lambda anônima, como o [ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) demonstra. No entanto, os manipuladores de anônimos não podem ser compartilhados sem um código confuso de reflexão.

## <a name="distinguishing-views-with-ids"></a>Distinção entre exibições com IDs

Vários `Button` objetos também podem ser diferenciados, definindo o [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propriedade ou [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) propriedade para um `string`. Essa propriedade é definida por `Element` , mas ele não é usado dentro do xamarin. Forms. Ele destina-se a ser usado exclusivamente pelos programas de aplicativo.

O [ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) o exemplo usa o mesmo manipulador de eventos para todas as chaves de número 10 em um teclado numérico e faz a distinção entre eles com o `StyleId` propriedade:

[![Captura de tela do teclado mais simples tripla](images/ch06fg04-small.png "Calculadora")](images/ch06fg04-large.png#lightbox "Calculadora")

## <a name="saving-transient-data"></a>Salvando dados transitórios

Muitos aplicativos precisam para salvar os dados quando um programa é encerrado e recarregar os dados quando o programa for iniciado novamente. O [ `Application` ](xref:Xamarin.Forms.Application) classe define vários membros que ajudam a salvar e restaurar dados transitórios seu programa:

- O [ `Properties` ](xref:Xamarin.Forms.Application.Properties) propriedade é um dicionário com `string` chaves e `object` itens. O conteúdo do dicionário automaticamente é salvos no armazenamento local do aplicativo antes do encerramento do programa e recarregado quando o programa é iniciado.
- O `Application` classe define três métodos virtuais protegidos que o programa do padrão `App` substituições de classe: [ `OnStart` ](xref:Xamarin.Forms.Application.OnStart), [ `OnSleep` ](xref:Xamarin.Forms.Application.OnSleep), e [ `OnResume` ](xref:Xamarin.Forms.Application.OnResume). Elas fazem referência a *ciclo de vida do aplicativo* eventos.
- O [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) método salva o conteúdo do dicionário.

Não é necessário chamar `SavePropertiesAsync`. O conteúdo do dicionário é automaticamente salvas antes do encerramento do programa e recuperado antes da inicialização do programa. É útil durante o teste de programa para salvar dados em caso de falha do programa.

Também é útil:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), uma propriedade estática que retorna o atual `Application` objeto que, em seguida, você pode usar para obter o `Properties` dicionário.

A primeira etapa é identificar todas as variáveis na página que você deseja persistir quando o programa é encerrado. Se você souber todos os locais em que alterar essas variáveis, você pode simplesmente adicioná-los para o `Properties` dicionário nesse ponto. No construtor da página, você pode definir as variáveis a partir de `Properties` dicionário se a chave existir.

Um programa maior provavelmente precisará lidar com eventos de ciclo de vida do aplicativo. O mais importante é o `OnSleep` método. Uma chamada para esse método indica que o programa tenha deixado em primeiro plano. Talvez o usuário pressiona o **Home** botão no dispositivo, ou exibidos todos os aplicativos ou está sendo desligado o telefone. Uma chamada para `OnSleep` é a única notificação que um programa recebe antes que seja finalizado. O programa deve aproveitar esta oportunidade para garantir que o `Properties` dicionário está atualizado.

Uma chamada para `OnResume` indica que o programa não foi encerrado após a última chamada para `OnSleep` , mas agora está em execução em primeiro plano novamente. O programa pode usar essa oportunidade para atualizar as conexões de internet (por exemplo).

Uma chamada para `OnStart` ocorre durante a inicialização do programa. Não é necessário aguardar até que esse método de chamada para o acesso a `Properties` dicionário porque o conteúdo já ter sido restaurado quando o `App` construtor é chamado.

O [ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) exemplo é muito semelhante à **SimplestKeypad** exceto que o programa usa o `OnSleep` substituição é para salvar a entrada de teclado atual, e o construtor de página para restaurar os dados.

> [!NOTE]
> Outra abordagem para salvar as configurações do programa é fornecida pelo Xamarin.Essentials [preferências](~/essentials/preferences.md) classe.

## <a name="related-links"></a>Links relacionados

- [Capítulo 6 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Exemplos do capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Capítulo 6 F# amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Botão de xamarin. Forms](~/xamarin-forms/user-interface/button.md)
