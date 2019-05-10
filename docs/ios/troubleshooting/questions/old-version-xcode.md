---
title: Posso usar uma versão anterior do Xcode ou xamarin. IOS
description: Este guia descreve os problemas com o uso de versões mais antigas do xamarin. IOS ou o Xcode (que a versão estável atual).
ms.prod: xamarin
ms.assetid: 27CF7EB7-9251-435F-BEA5-F20F8DD7DC17
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 04/16/2019
ms.openlocfilehash: 7cbc14e0a912fe9c55ff672796e839a8dcdfd9b5
ms.sourcegitcommit: 864f47c4f79fa588b65ff7f721367311ff2e8f8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "64347113"
---
# <a name="can-i-use-an-older-version-of-xcode-or-xamarinios"></a>Posso usar uma versão anterior do Xcode ou xamarin. IOS?

Documentação do Xamarin pressupõe o uso do xamarin. IOS mais recente e Xcode, que é recomendado. No entanto, alguns clientes preferem usar xamarin. IOS mais antigas e/ou Xcode e gostaria de obter detalhes sobre as consequências.

As notas de versão contêm o seguinte aviso:

> [!WARNING]
> **Usando uma versão mais antiga do Xcode**
>
> Usando uma versão mais antiga do Xcode (mencionadas acima daquele [requisitos](https://docs.microsoft.com/xamarin/ios/release-notes/12/12.8#requirements)) geralmente é possível, mas alguns recursos podem não estar disponíveis. Também algumas limitações podem exigir soluções alternativas, por exemplo:
>
> - O registrador estático exige que os arquivos de cabeçalhos do Xcode para criar aplicativos, levando a [ `MT0091` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT0091) ou [ `MT4109` ](https://docs.microsoft.com/xamarin/ios/troubleshooting/mtouch-errors#MT4109) erros se as APIs estão ausentes. Na maioria dos casos habilitar o vinculador gerenciado ajudará (removendo a API).
> - Compilações de Bitcode (para tvOS e watchOS) podem falhar o envio para a App Store, a menos que uma cadeia de ferramentas do Xcode 9.0 + é usada.

## <a name="further-information"></a>Informações adicionais

Microsoft recomenda o uso o Xcode mais recente e a versão mais recente do xamarin. IOS ao desenvolver e envio de aplicativos. Apple requer o uso mais recente do Xcode durante o envio de aplicativos.

Observe que usar o mais recente do Xcode não impede que seu aplicativo de direcionamento de versões anteriores do iOS. As versões do iOS que você oferece suporte é baseado no seu **Info. plist** entrada e as APIs do seu aplicativo usa.

É possível instalar várias versões do Xcode lado a lado, com nomes diferentes, como **Xcode101.app** e **Xcode102.app**. Se você usar várias versões, certifique-se de definir o Xcode ativo no [Visual Studio para Mac configurações](~/ios/troubleshooting/questions/ios-sdk.md) e com o [ `xcode-select` ](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_) [ferramenta de linha de comando](https://developer.apple.com/library/archive/technotes/tn2339/_index.html#//apple_ref/doc/uid/DTS40014588-CH1-HOW_DO_I_SELECT_THE_DEFAULT_VERSION_OF_XCODE_TO_USE_FOR_MY_COMMAND_LINE_TOOLS_).

No entanto, raramente pode exigir o uso de componentes mais antigos. Esta documentação descreve os desafios gerais, você pode enfrentar ao usar versões anteriores à mais recente.

Cada versão da Apple é exclusiva no entanto, e você pode se deparar com outras armadilhas não documentadas aqui.

Às vezes, esses desafios são triviais para resolver isso sempre que possível continuar com a configuração com suporte da mais recente Xcode e xamarin. IOS mais recente.

## <a name="use-of-an-old-xamarinios-with-an-old-xcode"></a>Uso de um antigo xamarin. IOS com Xcode um antigo

Atualizar o xamarin. IOS e o Xcode não é possível, pelo menos por algum tempo. O limite é que, em algum momento, Apple exija uma versão mínima do Xcode para enviar seus aplicativos. Neste ponto, você deve atualizar todos os seus componentes (macOS, Xcode e xamarin. IOS) para as versões mais recentes (ou a versão nova e mínima do Xcode exigido pela Apple e a versão de xamarin. IOS correspondente).

É geralmente mais fácil atualizar gradualmente e acompanhe as pequenas alterações. Para projetos grandes em que as atualizações podem ser mais difícil manter-se atualizado com, manter-se com o conjunto de trabalho conhecidos pode ser uma boa solução.

## <a name="use-of-new-xamarinios-with-older-xcode"></a>Uso do novo xamarin. IOS com Xcode mais antigo

Em geral, o xamarin. IOS dá suporte a versões mais antigas do Xcode sempre que possível. Alguns desafios em potencial incluem:

- O xamarin. IOS mais recentes podem dar suporte a alguns recursos e APIs não está presente no Xcode selecionado. 
- O **registrador estático** exige que os arquivos de cabeçalhos do Xcode para criar aplicativos, levando a [ `MT0091` ](~/ios/troubleshooting/mtouch-errors.md#MT0091) ou [ `MT4109` ](~/ios/troubleshooting/mtouch-errors.md#MT4109) erros se as APIs estão ausentes.
  - Na maioria dos casos ajudará a habilitar o vinculador gerenciado (removendo as associações de gerenciado para a nova API) se não for utilizada.
- Compilações de Bitcode (para tvOS e watchOS) podem falhar o envio para a App Store, a menos que uma cadeia de ferramentas do Xcode 9.0 + é usada.

## <a name="use-of-new-xcode-with-older-xamarinios"></a>Uso do novo Xcode com xamarin. IOS mais antigos

Esse caso de uso é significativamente mais difícil, pois o xamarin. IOS não pode prever os requisitos em constante mudança do Xcode novo. Atualizações do macOS também podem introduzir problemas e sem patches compatibilidade muitas partes do xamarin. IOS podem ser afetados. 

Há várias áreas potenciais que podem dar errado incluindo:

- Incompatibilidades com `mlaunch`:
  - Suporte do simulador pode não funcionar corretamente (ou)
  - Suporte a dispositivos pode não funcionar corretamente (ou)
- Suporte a desconhecido `mtouch` 
  - Não há suporte para novas estruturas
  - Não há suporte para novos direitos
  - Não há suporte para ferramentas novas ou atualizadas
    - Isso pode afetar também de assinatura de código

### <a name="new-appstore-submission-rules"></a>Novas regras de envio de loja de aplicativos

Apple reserva o direito a atualizações para as regras de envios de loja de aplicativos a qualquer momento. Essas alterações de regra apenas algumas vezes são anunciadas com antecedência. Algumas dessas alterações exigem alterações para dar suporte, o que exigem um componente do xamarin. IOS atualizado de ferramentas.

Além das alterações de regra, a Apple geralmente adiciona validações adicionais para aplicativos enviados ou reforça as existentes. Alguns desses exigem alterações em nossas ferramentas (por exemplo, uma nova lista de bloqueios de símbolos). Muitos deles são encontrados pela primeira vez por clientes para enviar, pois não há nenhum anúncio (ou lista) das regras.

## <a name="summary"></a>Resumo

Sempre que possível, evite riscos da Apple seguir diretrizes e desenvolver no e enviando com o Xcode mais recente lançado na Store do aplicativo.

Por sua vez, use o xamarin. IOS mais recentes lançados. Isso irá controlar as correções mais recentes que podem afetar aplicativos enviados e estar de acordo com as alterações mais recentes da regra.

Em que não é possível, considere o uso de uma versão Xcode e xamarin. IOS correspondente mais antiga. Isso pode funcionar por um tempo, mas em algum momento Apple insistirão após as ferramentas mais recentes, portanto, planeje adequadamente.
