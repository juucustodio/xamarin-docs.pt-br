---
title: "Integrações de depuração"
ms.topic: article
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: a0873c6b902e29174da5e27a09e8f580d6d69eb7
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="debugging-integrations"></a>Integrações de depuração

## <a name="debugging-agent-side-integrations"></a>Depuração integrações do lado do agente

Depuração no lado do agente integrações melhor é feito usando os métodos de registro em log fornecidos pelo `Log` classe em `Xamarin.Interactive.Logging`. Consulte o [ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/) para os métodos para chamar.

Na macOS, mensagens de log são exibidas no menu de Visualizador de log (**Janela > Visualizador de Log**) e no log do cliente. No Windows, as mensagens será só aparecem no log do cliente, pois não há nenhum visualizador de log.

O log de cliente estiver nos locais a seguir no macOS e Windows:

- Mac: `~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

Uma coisa a serem consideradas é que, ao carregar integrações via o usual `#r` mecanismo durante o desenvolvimento, o conjunto de integração será retirado como um _dependência_ da pasta de trabalho e empacotado com ele, se for um caminho absoluto não usado. Isso pode causar alterações apareçam não se propague, como se a recriação de integração não fez nada.

## <a name="debugging-client-side-integrations"></a>Depuração integrações do lado do cliente

Como as integrações do lado do cliente são escritas em JavaScript e carregadas no nosso superfície do navegador da web (consulte a [arquitetura](~/tools/workbooks/sdk/architecture.md) documentação), a melhor maneira de depurá-los é usando as ferramentas de desenvolvedor WebKit no Mac ou usando o seletor de F12 no Windows .

Ambos os conjuntos de ferramentas permitem exibir origem TypeScript/JavaScript, definir pontos de interrupção, exibir a saída do console e inspecionar e modificar o DOM.

### <a name="mac"></a>Mac

Para habilitar as ferramentas de desenvolvedor para pastas de trabalho do Xamarin Mac, execute o comando a seguir em seu terminal:

```shell
defaults write com.xamarin.Inspector WebKitDeveloperExtras -bool true
```

e, em seguida, reinicie o Xamarin pastas de trabalho. Quando você fizer isso, você deverá ver **elemento inspecionar** aparecer no seu menu de contexto e um novo **desenvolvedor** painel estarão disponível nas preferências de pastas de trabalho. Essa opção permite que você escolha se deseja que as ferramentas de desenvolvedor abertas durante a inicialização:

[![Painel do desenvolvedor](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Esta preferência é somente para reinicialização também — você precisará reiniciar o cliente de pastas de trabalho para que ele entre em vigor em novas pastas de trabalho. Ativar as ferramentas de desenvolvedor por meio do menu de contexto ou as preferências mostrará a interface de usuário familiar do Safari:

[![Ferramentas de desenvolvimento do Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Para obter informações sobre como usar as ferramentas de desenvolvedor do Safari, consulte o [documentação do Inspetor WebKit][webkit-docs].

### <a name="windows"></a>Windows

No Windows, a equipe do IE fornece uma ferramenta conhecida como "Seletor F12" que é um depurador remoto para instâncias do Internet Explorer incorporados. Você pode encontrar a ferramenta no seguinte local:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Seletor de execução F12 e você verá a instância incorporada que habilita a superfície de cliente de pastas de trabalho na lista. Escolha o e o familiar F12 ferramentas de depuração do Internet Explorer serão exibida, conectados ao cliente:

[![Ferramentas F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector