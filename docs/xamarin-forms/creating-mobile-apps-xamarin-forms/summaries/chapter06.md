---
title: "Resumo do capítulo 6. Cliques de botão"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 44dbb4d2cdc573e721bb772fdd05d392c90b746a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-6-button-clicks"></a>Resumo do capítulo 6. Cliques de botão

O [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) é o modo de exibição que permite que o usuário iniciar um comando. Um `Button` é identificado por texto (e, opcionalmente, uma imagem conforme demonstrado no [capítulo 13, Bitmaps](chapter13.md)). Consequentemente, `Button` define muitas das mesmas propriedades de `Label`:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/)
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontFamily/)
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/)
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontAttributes/)
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/)

`Button` também define três propriedades que controlam a aparência de sua borda, mas o suporte a essas propriedades e seu independência mútua é específico da plataforma:

- [`BorderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderColor/) do tipo `Color`
- [`BorderWidth`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderWidth/) do tipo `Double`
- [`BorderRadius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/) do tipo `Double`

`Button` também herda todas as propriedades do `VisualElement` e `View`, incluindo `BackgroundColor`, `HorizontalOptions`, e `VerticalOptions`.

## <a name="processing-the-click"></a>Clique em de processamento

O `Button` classe define um [ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/) evento é acionado quando o usuário toca o `Button`. O `Click` manipulador é do tipo `EventHandler`. O primeiro argumento é o `Button` gera o evento do objeto; o segundo argumento é uma `EventArgs` objeto que não fornece nenhuma informação adicional.

O [ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) demonstra simples `Clicked` tratamento.

## <a name="sharing-button-clicks"></a>Cliques de botão de compartilhamento

Vários `Button` exibições podem compartilhar a mesma `Clicked` manipulador, mas o manipulador geralmente precisa determinar qual `Button` é responsável por um evento específico. Uma abordagem é armazenar vários `Button` objetos como campos e verificar qual deles está acionando o evento no manipulador.

O [ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) demonstra essa técnica. O programa também demonstra como definir a [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) propriedade de um `Button` para `false` ao pressionar o `Button` não é mais válido. Um desativado `Button` não gera um `Clicked` eventos.

## <a name="anonymous-event-handlers"></a>Manipuladores de eventos anônimo

É possível definir `Clicked` manipuladores como funções anônimas lambda, como o [ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) demonstra. No entanto, manipuladores de anônimos não podem ser compartilhados sem um código de reflexão confuso.

## <a name="distinguishing-views-with-ids"></a>Distinção entre exibições com IDs

Vários `Button` objetos também podem ser distinguidos definindo o [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) propriedade ou [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/) propriedade para um `string`. Essa propriedade é definida por `Element` , mas ele não é usado em xamarin. Forms. Ele destina-se a ser usado exclusivamente pelos programas de aplicativo.

O [ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) exemplo usa o mesmo manipulador de eventos para todas as chaves de número 10 em um teclado numérico e faz distinção entre eles com o `StyleId` propriedade:

[![Captura de tela de tripla do teclado mais simples](images/ch06fg04-small.png "Calculadora")](images/ch06fg04-large.png "Calculadora")

## <a name="saving-transient-data"></a>Salvando dados transitórios

Muitos aplicativos necessárias para salvar os dados quando um programa é encerrado e recarregar os dados quando o programa for iniciado novamente. O [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) classe define vários membros que ajudam a salvar e restaurar dados transitórios seu programa:

- O [ `Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/) propriedade é um dicionário com `string` chaves e `object` itens. O conteúdo do dicionário automaticamente é salvos no armazenamento local do aplicativo antes do encerramento do programa e recarregado quando o programa for iniciado.
- O `Application` classe define três métodos virtuais protegidos que do padrão de programa `App` substituições de classe: [ `OnStart` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnStart()/), [ `OnSleep` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnSleep()/), e [ `OnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnResume()/). Elas fazem referência a *ciclo de vida do aplicativo* eventos.
- O [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) método salva o conteúdo do dicionário.

Não é necessário chamar `SavePropertiesAsync`. O conteúdo do dicionário é automaticamente salvas antes do encerramento do programa e recuperado antes da inicialização do programa. É útil durante o teste de programa para salvar os dados se o programa falhar.

Também é útil:

- [`Application.Current`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Current/), uma propriedade estática que retorna atual `Application` objeto que você pode usar para obter o `Properties` dicionário.

A primeira etapa é identificar todas as variáveis na página que você deseja manter quando o programa é encerrado. Se você souber todos os locais onde alterar essas variáveis, basta adicioná-los para o `Properties` dicionário nesse ponto. No construtor da página, você pode definir as variáveis a partir do `Properties` dicionário se a chave existir.

Um programa maior provavelmente precisará lidar com eventos de ciclo de vida do aplicativo. O mais importante é o `OnSleep` método. Uma chamada para esse método indica que o programa tiver saído do primeiro plano. Talvez o usuário pressionou a **início** botão no dispositivo, exibidos todos os aplicativos ou desligar o telefone. Uma chamada para `OnSleep` é a única notificação que um programa recebe antes da finalização. O programa deve aproveitar essa oportunidade para garantir que o `Properties` dicionário está atualizado.

Uma chamada para `OnResume` indica que o programa não foi encerrado após a última chamada para `OnSleep` , mas está em execução em primeiro plano novamente. O programa pode usar essa oportunidade para atualizar as conexões de internet (por exemplo).

Uma chamada para `OnStart` ocorre durante a inicialização do programa. Não é necessário aguardar até que chame esse método para acessar o `Properties` dicionário porque o conteúdo já ter sido restaurado quando o `App` construtor seja chamado.

O [ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) exemplo é muito semelhante ao **SimplestKeypad** exceto que o programa usa a `OnSleep` substituição para salvar a entrada de teclado atual, e o construtor da página para restaurar os dados.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 6 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Exemplos de capítulo 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Exemplos de capítulo 6 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
