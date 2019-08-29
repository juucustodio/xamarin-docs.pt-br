---
title: Dicas de solução de problemas do Xamarin. Mac
description: Este documento descreve as abordagens para resolver os problemas encontrados ao desenvolver aplicativos Xamarin. Mac. Ele também discute maneiras de obter suporte.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: dea7337702e37895d1e8dec55bd433d8ac4d64c0
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065610"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Dicas de solução de problemas do Xamarin. Mac

## <a name="overview"></a>Visão geral

Às vezes, todos estamos presos enquanto trabalhamos em um projeto, na incapacidade de fazer com que uma API funcione da maneira desejada ou tentar contornar um bug. Nossa meta no Xamarin é que você tenha êxito ao escrever seus aplicativos móveis e de desktop, e fornecemos alguns recursos para ajudar.

Com qualquer um desses recursos, há algumas etapas de preparação que você pode tomar para ajudá-los a resolver seu problema rapidamente:

- Determine a causa raiz do problema o melhor possível para relatar falhas:

  - "Meu aplicativo falha" é difícil de diagnosticar. "Meu aplicativo falha quando eu retorno uma matriz vazia para esta chamada" é muito mais fácil de trabalhar na correção.

  - "Não consigo fazer o NSTable funcionar" é menos útil do que "nenhum dos métodos em meu NSTableDelegate parece ser chamado nesse caso".

- Se possível, forneça um pequeno programa de exemplo mostrando o problema. A análise de páginas de código-fonte que procuram pelo problema leva mais tempo e esforço.

- Saber quais alterações você fez em seu aplicativo para que um problema apareça possa reduzir rapidamente a origem do problema. Observando se você atualizou recentemente as versões do Xamarin. Mac, aparando as seções do seu aplicativo para encontrar a parte que está causando o problema ou testando as compilações anteriores para encontrar qual alteração introduzida o problema pode ser muito útil.


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>O que fazer quando seu aplicativo falhar sem nenhuma saída

Na maioria dos casos, o depurador no Visual Studio para Mac detectará exceções e falhas em seu aplicativo e ajudará você a rastrear a causa raiz. No entanto, há alguns casos em que seu aplicativo irá devolver o Dock e, em seguida, sair com pouca ou nenhuma saída. Eles podem incluir:

- Problemas de assinatura de código.
- Determinadas falhas de tempo de execução mono.
- Algumas exceções de Objective-c e falhas.
- Algumas travam logo no início do tempo de vida do processo.
- Alguns estouros de pilha.
- A versão do macOS listada no seu **info. plist** é mais recente do que a versão do MacOS instalada no momento ou é inválida.

A depuração desses programas pode ser frustrante, pois encontrar as informações necessárias pode ser difícil. Aqui estão algumas abordagens que podem ajudar:

- Verifique se a versão do macOS listada no **info. plist** é a mesma que a versão do MacOS instalada no momento no computador.
- Verifique a saída do aplicativo Visual Studio para Mac (**saída do aplicativo** **View** -> **pads** -> ) para rastreamentos de pilha ou saída em vermelho de Cocoa que pode descrever a saída.
- Execute o aplicativo na linha de comando e examine a saída (no aplicativo de **terminal** ) usando:

  `MyApp.app/Contents/MacOS/MyApp`(onde `MyApp` é o nome do seu aplicativo)
- Você pode aumentar a saída adicionando "MONO_LOG_LEVEL" ao comando na linha de comando, por exemplo:

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- Você pode anexar um depurador nativo (`lldb`) ao processo para ver se isso fornece mais informações (isso requer uma licença paga). Por exemplo, faça o seguinte:

  1. Insira `lldb MyApp.app/Contents/MacOS/MyApp` no terminal.
  2. Insira `run` no terminal.
  3. Insira `c` no terminal.
  4. Sair quando terminar a depuração.
- Como último recurso, antes de chamar `NSApplication.Init` em seu `Main` método (ou em outros locais, conforme necessário), você pode gravar o texto em um arquivo em um local conhecido para rastrear em qual etapa da inicialização você está executando problemas.

## <a name="known-issues"></a>Problemas conhecidos

As seções a seguir abordam problemas conhecidos e suas soluções.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Não é possível conectar-se ao depurador em aplicativos em área restrita

O depurador se conecta a aplicativos Xamarin. Mac por meio de TCP, o que significa que, por padrão, quando você habilita a área restrita, ele não consegue se conectar ao aplicativo, portanto, se você tentar executar o aplicativo sem as permissões apropriadas habilitadas, você receberá um erro *"não é possível se conectar ao depurador"* .

[![Editando os direitos](troubleshooting-images/debug01.png "Editando os direitos")](troubleshooting-images/debug01-large.png#lightbox)

A permissão **permitir conexões de rede de saída (cliente)** é a necessária para o depurador, permitindo que a depuração seja normalmente ativada. Como não é possível depurar sem ele, atualizamos o `CompileEntitlements` destino do `msbuild` para adicionar automaticamente essa permissão aos direitos de qualquer aplicativo que esteja em área restrita somente para compilações de depuração. As compilações de versão devem usar os direitos especificados no arquivo de direitos, sem modificações.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: não há dados disponíveis para codificação 437

Ao incluir bibliotecas de terceiros em seu aplicativo Xamarin. Mac, você poderá receber um erro no formato "System. NotSupportedException: Não há dados disponíveis para codificação 437 "ao tentar compilar e executar o aplicativo. Por exemplo, bibliotecas, `Ionic.Zip.ZipFile`como, podem gerar essa exceção durante a operação.

Isso pode ser resolvido abrindo as opções para o projeto Xamarin. Mac, indo para a internacionalização de **Build** > do Mac e verificando a internacionalização **oeste** :

[![Editar as opções de build](troubleshooting-images/issue01.png "Editar as opções de build")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Falha ao compilar (mm5103)

Esse erro geralmente ocorre quando uma nova versão do Xcode é lançada e você instalou a nova versão, mas não a executa. Antes de tentar compilar com uma nova versão do Xcode, primeiro você precisa executar essa versão pelo menos uma vez.

Na primeira vez que você executar uma nova versão do Xcode, ele instalará várias ferramentas de linha de comando exigidas pelo Xamarin. Mac. Além disso, você deve fazer uma compilação limpa depois de atualizar o Xcode ou sua versão do Xamarin. Mac.

Se você não puder resolver esse problema, registre [um bug](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Direitos ausentes. plist

A versão mais recente do Visual Studio para Mac removeu a seção de direitos do editor do **info. plist** e a colocou no editor de **direitos separados. plist** (para melhor suporte de plataforma cruzada com Xamarin. Ios).

Com o novo Visual Studio para Mac instalado, quando você cria um novo projeto de aplicativo Xamarin. Mac, um arquivo de **direitos. plist** será automaticamente adicionado à árvore do projeto:

![Selecionando direitos](troubleshooting-images/entitlements01.png "Selecionando direitos")

Se você clicar duas vezes no arquivo retitles **. plist** , o editor de direitos será exibido:

[![Editando os direitos](troubleshooting-images/entitlements02.png "Editando os direitos")](troubleshooting-images/entitlements02-large.png#lightbox)

Para projetos Xamarin. Mac existentes, você precisará criar manualmente o arquivo Rights **. plist** clicando com o botão direito do mouse no projeto na **painel de soluções** e selecionando **Adicionar** > **novo arquivo...** . Em seguida, selecione**lista de propriedades vazia**do **Xamarin. Mac** > :

![Adicionando uma nova lista de propriedades](troubleshooting-images/entitlements03.png "Adicionando uma nova lista de propriedades")

Insira `Entitlements` para o nome e clique no botão **novo** . Se o projeto tiver incluído um arquivo de direitos anteriormente, você será solicitado a adicioná-lo ao projeto em vez de criar um novo arquivo:

[![Verificando a substituição de um arquivo](troubleshooting-images/entitlements04.png "Verificando a substituição de um arquivo")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>Suporte da Comunidade nos fóruns

A comunidade de desenvolvedores que usam produtos Xamarin é incrível e muitos visitam nossos [fóruns do xamarin. Mac](http://forums.xamarin.com/categories/mac) para compartilhar experiências e seus conhecimentos. Além disso, os engenheiros do Xamarin visitam periodicamente o fórum para ajudar.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Arquivando um bug

Seus comentários são importantes para nós. Se você encontrar problemas com o Xamarin. Mac:

- Pesquise o [repositório de problemas](https://github.com/xamarin/xamarin-macios/issues)
- Antes de mudar para problemas do GitHub, os problemas do Xamarin eram rastreados no [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Procure lá por problemas correspondentes.
- Se você não encontrar um problema correspondente, envie um novo problema no [repositório de problemas do GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Os problemas do GitHub são todos públicos. Não é possível ocultar comentários ou anexos.

Inclua tanto do seguinte quanto possível:

- Um exemplo simples reproduzindo o problema. Isso é **inestimável**, quando possível.
- O rastreamento de pilha completo da falha.
- O código C# ao redor da falha.
