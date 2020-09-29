---
title: Posso usar uma versão mais antiga do Xcode ou Xamarin. iOS
description: Este guia descreve os problemas com o uso de versões mais antigas do Xamarin. iOS ou do Xcode (que a versão estável atual).
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 6c6a0491f7989f2f76afabc3412e38be74a06da4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431581"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>Posso usar uma versão mais antiga do Xcode ou Xamarin. iOS?

A documentação do Xamarin pressupõe o uso do Xamarin. iOS e do Xcode mais recente, que é recomendado. No entanto, alguns clientes preferem usar o Xamarin. iOS e/ou o Xcode mais antigo e gostaria de obter detalhes sobre as consequências.

As notas de versão contêm o seguinte aviso:

> [!WARNING]
> **Usando uma versão mais antiga do Xcode**
>
> Usar uma versão mais antiga do Xcode (do que a mencionada nos [requisitos](/xamarin/ios/release-notes/12/12.8#requirements)acima) geralmente é possível, mas alguns recursos podem não estar disponíveis. Além disso, algumas limitações podem exigir soluções alternativas, por exemplo:
>
> - O registrador estático requer que os arquivos de cabeçalhos do Xcode criem aplicativos, levando a [`MT0091`](../mtouch-errors.md#MT0091) ou [`MT4109`](../mtouch-errors.md#MT4109) erros se as APIs estiverem ausentes. Na maioria dos casos, habilitar o vinculador gerenciado ajudará (removendo a API).
> - BitCode Builds (para tvOS e watchOS) podem falhar no envio para a loja de aplicativos, a menos que um Xcode 9.0 + ferramentas seja usado.

## <a name="further-information"></a>Informações adicionais

A Microsoft recomenda enfaticamente o uso do Xcode mais recente e da versão mais recente do Xamarin. iOS ao desenvolver e enviar aplicativos. A Apple requer o uso do Xcode mais recente ao enviar aplicativos.

Observe que o uso do Xcode mais recente não impede que o aplicativo se destinasse a versões mais antigas do iOS. As versões do iOS com suporte baseiam-se na entrada **info. plist** e nas APIs que seu aplicativo usa.

É possível instalar várias versões do Xcode lado a lado, com nomes diferentes, como **Xcode101. app** e **Xcode102. app**. Se você usar várias versões, certifique-se de definir o Xcode ativo no [Visual Studio para Mac configurações](~/ios/troubleshooting/questions/ios-sdk.md) e com a [`xcode-select`](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [ferramenta de linha de comando](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_).

No entanto, circunstâncias raras podem exigir o uso de componentes mais antigos. Esta documentação descreve os desafios gerais que você pode enfrentar ao usar versões anteriores ao mais recente.

No entanto, cada versão da Apple é exclusiva, e você pode vir por outras armadilhas não documentadas aqui.

Esses desafios às vezes são não triviais para resolver, assim, sempre que possível, para a configuração com suporte do Xcode mais recente e do Xamarin. iOS mais recente.

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>Uso de um Xamarin. iOS antigo com um Xcode antigo

Não é possível atualizar o Xamarin. iOS e o Xcode, pelo menos por um período de tempo. O limite é que, em algum momento, a Apple exigirá uma versão mínima do Xcode para enviar seus aplicativos. Neste ponto, você deve atualizar todos os seus componentes (macOS, Xcode e Xamarin. iOS) para as versões mais recentes (ou a nova versão mínima do Xcode exigida pela Apple e a versão correspondente do Xamarin. iOS).

Geralmente, é mais fácil atualizar gradualmente e acompanhar as pequenas alterações. Para projetos grandes em que as atualizações podem ser mais difíceis de acompanhar, permanecer com um conjunto de trabalho conhecido pode ser um bom comprometimento.

## <a name="use-of-new-xamarinios-with-older-xcode"></a>Uso do novo Xamarin. iOS com o Xcode mais antigo

O Xamarin. iOS em geral dá suporte a versões mais antigas do Xcode sempre que possível. Alguns desafios potenciais incluem:

- O Xamarin. iOS mais recente pode dar suporte a alguns recursos e APIs que não estão presentes no Xcode selecionado. 
- O **registrador estático** requer que os arquivos de cabeçalhos do Xcode criem aplicativos, levando a [`MT0091`](~/ios/troubleshooting/mtouch-errors.md#MT0091) ou [`MT4109`](~/ios/troubleshooting/mtouch-errors.md#MT4109) erros se as APIs estiverem ausentes.
  - Na maioria dos casos, habilitar o vinculador gerenciado ajudará (removendo as associações gerenciadas para a nova API) se não for usado.
- BitCode Builds (para tvOS e watchOS) podem falhar no envio para a loja de aplicativos, a menos que um Xcode 9.0 + ferramentas seja usado.

## <a name="use-of-new-xcode-with-older-xamarinios"></a>Uso do novo Xcode com o Xamarin. iOS mais antigo

Esse caso de uso é significativamente mais difícil, pois o Xamarin. iOS não pode prever os requisitos de alteração do novo Xcode. As atualizações do macOS também podem introduzir problemas e, sem patches de compatibilidade, muitas partes do Xamarin. iOS podem ser afetadas. 

Há várias áreas potenciais nas quais as coisas podem dar errado, incluindo:

- Incompatibilidades com `mlaunch` :
  - O suporte ao simulador pode não funcionar corretamente (ou tudo)
  - O suporte a dispositivos pode não funcionar corretamente (ou tudo)
- Suporte desconhecido para `mtouch` 
  - Não há suporte para novas estruturas
  - Não há suporte para novos direitos
  - Não há suporte para ferramentas novas ou atualizadas
    - Isso também pode afetar a assinatura de código

### <a name="new-appstore-submission-rules"></a>Novas regras de envio de AppStore

A Apple reserva o direito de atualizações para as regras de envios de AppStore a qualquer momento. Essas alterações de regra, às vezes, são anunciadas com antecedência. Algumas dessas alterações exigem alterações de ferramentas para dar suporte, o que exigiria um componente Xamarin. iOS atualizado.

Além das alterações de regra, a Apple geralmente adiciona validações adicionais a aplicativos enviados ou fortalece os existentes. Algumas delas exigem alterações em nossas ferramentas (por exemplo, um novo símbolo de lista negra). Muitos deles são encontrados primeiro por clientes que enviam, pois não há comunicado (ou lista) das regras.

## <a name="summary"></a>Resumo

Sempre que possível, jogue com segurança seguindo as diretrizes da Apple e desenvolvendo e enviando com o Xcode mais recente lançado na loja de aplicativos.

Por sua vez, use o Xamarin. iOS mais recente lançado. Isso acompanhará as correções mais recentes que podem afetar os aplicativos enviados e obedecer às alterações de regra mais recentes.

Em que isso não é possível, considere usar uma versão do Xcode e Xamarin. iOS mais antiga correspondente. Isso pode funcionar por um tempo, mas, em algum momento, a Apple insiste nas ferramentas mais recentes, portanto, planeje adequadamente.