---
title: Depuração de integrações
description: Este documento descreve como depurar Xamarin Workbooks integrações, tanto do lado do agente quanto do lado do cliente no Windows e no Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: davidortinau
ms.author: daortin
ms.date: 06/19/2018
ms.openlocfilehash: 3030bf907d1ddbb02884f997f178b55950b442d4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018817"
---
# <a name="debugging-integrations"></a>Depuração de integrações

## <a name="debugging-agent-side-integrations"></a>Depuração de integrações do lado do agente

A depuração de integrações do lado do agente é melhor feita usando os métodos de registro em log fornecidos pela classe `Log` no `Xamarin.Interactive.Logging`.

No macOS, as mensagens de log aparecem no menu do Visualizador de log (**Window > Log Viewer**) e no log do cliente. No Windows, as mensagens aparecem apenas no log do cliente, pois não há nenhum Visualizador de log lá.

O log do cliente está nos seguintes locais no macOS e no Windows:

- Mac: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

Uma coisa a ser observada é que, ao carregar integrações por meio do mecanismo de `#r` usual durante o desenvolvimento, o assembly de integração será coletado como uma _dependência_ da pasta de trabalho e empacotado com ela se um caminho absoluto não for usado. Isso pode fazer com que as alterações pareçam não se propagar, já que a recriação da integração não fez nada.

## <a name="debugging-client-side-integrations"></a>Depurando integrações do lado do cliente

Como as integrações do lado do cliente são escritas em JavaScript e carregadas em nossa superfície de navegador da Web (consulte a documentação da [arquitetura](~/tools/workbooks/sdk/architecture.md) ), a melhor maneira de depurá-las é usando as ferramentas de desenvolvedor WebKit no Mac ou usando o Chooser F12 no Windows.

Ambos os conjuntos de ferramentas permitem exibir a origem de JavaScript/TypeScript, definir pontos de interrupção, exibir a saída do console e inspecionar e modificar o DOM.

### <a name="mac"></a>Mac

Para habilitar as ferramentas de desenvolvedor para Xamarin Workbooks no Mac, execute o seguinte comando em seu terminal:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

e reinicie o Xamarin Workbooks. Depois de fazer isso, você deverá ver o **elemento inspecionar** aparecer no menu de contexto de clique com o botão direito do mouse e um novo painel do **desenvolvedor** estará disponível nas preferências das pastas de trabalho. Essa opção permite que você escolha se deseja que as ferramentas de desenvolvedor sejam abertas na inicialização:

[![painel de desenvolvedor](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Essa preferência também é de reinicialização – você precisará reiniciar o cliente de pastas de trabalho para que ele tenha efeito nas novas pastas de trabalho. Ativar as ferramentas de desenvolvedor por meio do menu de contexto ou das preferências mostrará a interface do usuário familiar do Safari:

[ferramentas de desenvolvimento do![Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Para obter informações sobre como usar as ferramentas de desenvolvedor do Safari, consulte a [documentação do Inspetor de WebKit][webkit-docs].

### <a name="windows"></a>Windows

No Windows, a equipe do IE fornece uma ferramenta conhecida como "Chooser F12", que é um depurador remoto para instâncias do Internet Explorer inseridas. Você pode encontrar a ferramenta no seguinte local:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Execute o Chooser F12 e você deverá ver a instância inserida que alimenta a superfície do cliente de pastas de trabalho na lista. Escolha-o e as ferramentas de depuração F12 conhecidas do Internet Explorer serão exibidas, anexadas ao cliente:

[Ferramentas F12![](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
