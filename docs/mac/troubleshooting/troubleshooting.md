---
title: Dicas de solução de problemas do xamarin. Mac
description: Este documento descreve as abordagens para resolver os problemas encontrados durante o desenvolvimento de aplicativos xamarin. Mac. Ele também discute as maneiras de obter suporte.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: c2c0e516c864ec4073d5bb54b77754f58064d6b0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116739"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Dicas de solução de problemas do xamarin. Mac

## <a name="overview"></a>Visão geral

Às vezes, todos nós ficar preso enquanto estiver trabalhando em um projeto, incapacidade para uma API para trabalhar da maneira que gostaríamos de obter ou tentar contornar um bug. Nossa meta no Xamarin é para ser bem-sucedido na escrita de seus aplicativos móveis e de desktop e fornecemos alguns recursos para ajudar.

Com qualquer um desses recursos, há algumas etapas de preparação, que você pode tomar para ajudá-los a resolver seu problema rapidamente:

- Determine a causa raiz do problema da melhor forma possível para relatar falhas gerais:
 
     - "Falha de meu aplicativo" é difícil de diagnosticar. "Meu aplicativo falha quando eu retornar uma matriz vazia para esta chamada de" é muito mais fácil trabalhar na correção.

     - "Não é possível obter NSTable trabalhar" é menos útil que "Nenhum dos métodos no meu NSTableDelegate parecem ser chamado nesse caso."

- Se possível, forneça um programa pequeno exemplo que mostra o problema. Aprofundar-se por meio das páginas de código-fonte procurando o problema exija ordens de magnitude mais tempo e esforço.

- Saber o que as alterações feitas ao seu aplicativo para causar um problema seja exibido podem restringi-lo rapidamente a origem do problema. Observando se você atualizou recentemente versões do xamarin. Mac, cortando seções do seu aplicativo para localizar a parte que está causando o problema, ou teste anterior compila para localizar quais alterações introduziu o problema pode ser muito útil.


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>O que fazer quando seu aplicativo falhar sem saída

Na maioria dos casos, o depurador do Visual Studio para Mac irá capturar exceções e falhas em seu aplicativo e ajudá-lo a rastrear a causa raiz. No entanto, há alguns casos em que seu aplicativo Elástico no encaixe e, em seguida, sair com pouca ou nenhuma saída. Eles podem incluir:

- Problemas de assinatura de código.
- Falhas de determinado tempo de execução mono.
- Algumas exceções Objective-c e falhas.
- Algumas falhas muito cedo o tempo de vida do processo.
- Alguns estouros de pilha.
- A versão do macOS listada em seu **Info. plist** mais recente que a versão atualmente instalada do macOS ou é inválido.

Esses programas de depuração pode ser frustrante, como localizar as informações necessárias podem ser difícil. Aqui estão algumas abordagens que podem ajudar a:

- Certifique-se de que a versão do macOS listada na **Info. plist** é o mesmo que a versão do macOS atualmente instalados no computador.
- Verifique o Visual Studio para a saída do aplicativo Mac (**modo de exibição** -> **Pads** -> **saída do aplicativo**) para rastreamentos de pilha ou de saída em vermelho do Cocoa que pode descrever a saída.
- Executar o aplicativo da linha de comando e examine a saída (na **Terminal** aplicativo), usando: 

     `MyApp.app/Contents/MacOS/MyApp` (onde `MyApp` é o nome do seu aplicativo)
- Você pode aumentar a saída adicionando "MONO_LOG_LEVEL" ao seu comando na linha de comando, por exemplo: 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- Você pode anexar um depurador nativo (`lldb`) para o processo para ver se isso fornece mais informações (Isso requer uma licença paga). Por exemplo, faça o seguinte:

    1. Insira `lldb MyApp.app/Contents/MacOS/MyApp` no Terminal.
    2. Insira `run` no Terminal.
    3. Insira `c` no Terminal.
    4. Sair quando terminar a depuração.
- Como último recurso, antes de chamar `NSApplication.Init` em seu `Main` método (ou em outros lugares conforme necessário), você poderia escrever texto em um arquivo em um local conhecido para rastrear em qual etapa de inicialização que você estiver executando em apuros.

## <a name="known-issues"></a>Problemas conhecidos

As seções a seguir abordam os problemas conhecidos e suas soluções.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Não é possível conectar ao depurador em aplicativos de área restrita

O depurador se conectar a aplicativos xamarin. MAC por meio de TCP, o que significa que por padrão quando você habilita a área restrita, não é possível se conectar ao aplicativo, portanto, se você tentar executar o aplicativo sem as permissões adequadas habilitadas, você receberá um erro *"não é possível se conectar ao o depurador"*. 

[![Editando os direitos](troubleshooting-images/debug01.png "editando os direitos")](troubleshooting-images/debug01-large.png#lightbox)

O **permitir saída conexões de rede (cliente)** permissão é aquela exigida para que o depurador, habilitar esse permitirá depuração normalmente. Uma vez que você não pode depurar sem ele, atualizamos o `CompileEntitlements` de destino para `msbuild` para adicionar automaticamente essa permissão para os direitos para compilações de qualquer aplicativo que está na área restrita para depuração somente. Builds de versão devem usar os direitos especificados no arquivo de direitos, sem modificações.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: não há dados disponíveis para a codificação 437
 
Ao incluir 3ª bibliotecas de terceiros em seu aplicativo xamarin. Mac, você poderá receber um erro no formato "System. NotSupportedException: nenhum dado está disponível para a codificação 437" ao tentar compilar e executar o aplicativo. Por exemplo, bibliotecas, como `Ionic.Zip.ZipFile`, pode gerar essa exceção durante a operação.

Isso pode ser resolvido, abrindo as opções para o projeto xamarin. Mac, vai **Build do Mac** > **internacionalização** e verificando o **Oeste** internacionalização:

[![Editar as opções de build](troubleshooting-images/issue01.png "Editar as opções de build")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Falha ao compilar (mm5103)

Esse erro geralmente é causado quando uma nova versão do Xcode é a versão e você tiver instalado a nova versão, mas não ainda executá-lo. Antes de tentar compilar com uma nova versão do Xcode, você precisa ser executado pela primeira vez que a versão de pelo menos uma vez.

Na primeira vez que você executar uma nova versão do Xcode, ele instala várias ferramentas de linha de comando que são exigidas pelo xamarin. Mac. Além disso, você deve fazer uma compilação limpa depois de atualizar o Xcode ou sua versão do xamarin. Mac.

Se você não pode resolver esse problema, faça [arquivar um bug](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Entitlements. plist ausente

A versão mais recente do Visual Studio para Mac removeu a seção de direitos do **Info. plist** editor e o colocou em separado **Entitlements. plist** editor (para um melhor suporte de plataforma cruzada com o xamarin. IOS).

Com o novo Visual Studio para Mac instalado, quando você cria um novo projeto de aplicativo do xamarin. Mac, uma **Entitlements. plist** arquivo será automaticamente adicionado à árvore de projeto:

![Selecionar direitos](troubleshooting-images/entitlements01.png "selecionar direitos")

Se você clicar duas vezes o **Entitlements. plist** arquivo, o Editor de direitos será exibido:

[![Editando os direitos](troubleshooting-images/entitlements02.png "editando os direitos")](troubleshooting-images/entitlements02-large.png#lightbox)

Para projetos existentes do xamarin. Mac, você precisará criar manualmente a **Entitlements. plist** arquivo pelo botão direito do mouse no projeto na **painel de soluções** e selecionando **Add**  >  **Novo arquivo...** . Em seguida, selecione **xamarin. Mac** > **lista de propriedades vazio**:

![Adicionando uma nova lista de propriedades](troubleshooting-images/entitlements03.png "adicionando uma nova lista de propriedades")

Insira `Entitlements` para o nome e clique o **New** botão. Se seu projeto anteriormente incluído um arquivo de direitos, você será solicitado a adicioná-lo ao projeto em vez de criar um novo arquivo:

[![Verificando a substituição de um arquivo](troubleshooting-images/entitlements04.png "verificando a substituição de um arquivo")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="contacting-support-business-or-enterprise-licenses"></a>Contatando o suporte (licenças de business ou enterprise)

Se você tiver uma licença de enterprise ou business, você está qualificado para solicitar ajuda diretamente de engenheiros de Xamarin por meio de tíquetes de suporte. Ver [xamarin.com/support](http://xamarin.com/support) para obter detalhes.

## <a name="community-support-on-the-forums"></a>Suporte da comunidade nos fóruns

A comunidade de desenvolvedores que usam os produtos Xamarin é incrível e muitos Visite nossos [fóruns do xamarin. Mac](http://forums.xamarin.com/categories/mac) compartilhar experiências e seus conhecimentos. Além disso, os engenheiros de Xamarin periodicamente visite o fórum para ajudar.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Arquivar um bug

Seus comentários são importantes para nós. Se você encontrar quaisquer problemas com o xamarin. Mac:

- Pesquise o [repositório de problemas](https://github.com/xamarin/xamarin-macios/issues) 
- Antes de mudar para problemas do GitHub, os problemas do Xamarin eram rastreados no [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Procure lá por problemas correspondentes.
- Se você não encontrar um problema correspondente, envie um novo problema no [repositório de problemas do GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Os problemas do GitHub são todos públicos. Não é possível ocultar comentários ou anexos. 

Inclua tanto do seguinte quanto possível:                                                                                                                                          

- Um exemplo simples reproduzindo o problema. Isso é **inestimável**, quando possível. 
- O rastreamento de pilha completo da falha.
- O código C# ao redor da falha. 
