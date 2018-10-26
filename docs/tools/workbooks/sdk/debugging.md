---
title: Integrações de depuração
description: Este documento descreve como depurar integrações de pastas de trabalho do Xamarin, o lado do agente e o lado do cliente no Windows e Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 86d9c6af93e7f59eb0e819730e46324688df7566
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105994"
---
# <a name="debugging-integrations"></a>Integrações de depuração

## <a name="debugging-agent-side-integrations"></a>Integrações do lado do agente de depuração

Integrações do lado do agente de depuração é mais bem efetuada usando os métodos de registro em log fornecidos pela `Log` classe `Xamarin.Interactive.Logging`. Consulte a [ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/) para os métodos para chamar.

No macOS, mensagens de log são exibidos no menu de Visualizador de log (**Janela > Visualizador de Log**) e no log do cliente. No Windows, as mensagens só são exibidos no log do cliente, pois não há nenhum visualizador de log.

É o log do cliente nos seguintes locais no macOS e no Windows:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

Uma coisa para estar atento é que, ao carregar integrações por meio de normais `#r` mecanismo durante o desenvolvimento, o assembly de integração será capturado como uma _dependência_ da pasta de trabalho e empacotado com ele, se for um caminho absoluto não usado. Isso pode causar alterações sejam exibidas não propagar, como se recriar a integração não fez nada.

## <a name="debugging-client-side-integrations"></a>Integrações do lado do cliente de depuração

Como as integrações do lado do cliente são gravadas em JavaScript e carregadas no nosso superfície do navegador da web (consulte a [arquitetura](~/tools/workbooks/sdk/architecture.md) documentação), a melhor maneira de depurá-los é usando as ferramentas de desenvolvedor do WebKit no Mac ou usando o seletor de F12 no Windows .

Ambos os conjuntos de ferramentas permitem que você exibir código-fonte do TypeScript/JavaScript, definir pontos de interrupção, exibir a saída do console e inspecionar e modificar o DOM.

### <a name="mac"></a>Mac

Para habilitar as ferramentas de desenvolvedor para pastas de trabalho do Xamarin no Mac, execute o seguinte comando no terminal:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

e, em seguida, reinicie o Xamarin Workbooks. Quando você fizer isso, você deverá ver **inspecionar elemento** aparecem no seu menu de contexto e uma nova **desenvolvedor** painel estará disponível nas preferências de pastas de trabalho. Essa opção permite que você escolha se deseja que as ferramentas de desenvolvimento abertas na inicialização:

[![Painel do desenvolvedor](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Essa preferência é somente de reinicialização também — você precisará reiniciar o cliente de pastas de trabalho para que ele entre em vigor em novas pastas de trabalho. Ativar as ferramentas de desenvolvedor por meio do menu de contexto ou as preferências de mostrará a interface do usuário familiar do Safari:

[![Ferramentas de desenvolvimento do Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Para obter informações sobre como usar as ferramentas de desenvolvedor do Safari, consulte o [documentação do Inspetor WebKit][webkit-docs].

### <a name="windows"></a>Windows

No Windows, a equipe do IE fornece uma ferramenta conhecida como "Seletor de F12" que é um depurador remoto para instâncias do Internet Explorer embedded. Você pode encontrar a ferramenta no seguinte local:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Seletor de execução F12 e você deverá ver a instância incorporada que alimenta a superfície de cliente de pastas de trabalho na lista. Escolha e familiar F12 do Internet Explorer as ferramentas de depuração serão exibida, anexado ao cliente:

[![Ferramentas de F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
