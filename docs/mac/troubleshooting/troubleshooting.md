---
title: Dicas de solução de problemas de Xamarin.Mac
description: Este documento descreve as abordagens para resolver os problemas encontrados durante o desenvolvimento de aplicativos Xamarin.Mac. Ele também discute como obter suporte.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 5e6cd5b338034cfa9956244015d4585b4f005793
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792961"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Dicas de solução de problemas de Xamarin.Mac

## <a name="overview"></a>Visão geral

Às vezes, todos os nós presa enquanto estiver trabalhando em um projeto, na incapacidade de obter uma API para funcionar da maneira que desejamos ou tentando solucionar um bug. Nosso objetivo no Xamarin é para ser bem-sucedido ao gravar seus aplicativos móveis e desktop e fornecemos alguns recursos para ajudar.

Com qualquer um desses recursos, há algumas etapas de preparação, que você pode tomar para ajudá-lo a solucionar o problema rapidamente:

- Determine a causa raiz do problema da melhor forma possível para relatar falhas gerais:
 
     - "Falha de meu aplicativo" é difícil diagnosticar. "Meu aplicativo falha ao retornar uma matriz vazia para esta chamada" é muito mais fácil trabalhar na correção.

     - "Não é possível obter NSTable trabalhar" é menos útil de "Nenhum dos métodos na minha NSTableDelegate parece ser chamado nesse caso."

- Se possível, forneça um programa de exemplo pequeno mostrando o problema. Aprofundar-se por meio de páginas de código-fonte procurando o problema tem ordens de magnitude mais tempo e esforço.

- Saber o que as alterações feitas ao seu aplicativo para causar um problema aparecem podem restringir a origem do problema. Observar se você atualizou recentemente versões do Xamarin.Mac, cortar seções do seu aplicativo para localizar a parte que está causando o problema, ou teste anterior cria para localizar quais alterações introduziu o problema pode ser muito útil.


### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>O que fazer quando seu aplicativo falhar sem saída

Na maioria dos casos, o depurador do Visual Studio para Mac irá capturar exceções e falhas em seu aplicativo e ajudá-lo a identificar a causa raiz. No entanto, há alguns casos em que seu aplicativo será Elástico no encaixe e, em seguida, sair com pouca ou nenhuma saída. Eles podem incluir:

- Problemas de assinatura de código.
- Falha de determinadas mono tempo de execução.
- Algumas exceções Objective-c e falhas.
- Algumas falhas muito cedo o tempo de vida do processo.
- Alguns estouro de pilha.
- A versão do macOS listada no seu **Info. plist** mais recente que a versão atualmente instalada macOS ou é inválido.

Esses programas de depuração pode ser frustrante, como localizar as informações necessárias podem ser difícil. Aqui estão algumas abordagens que podem ajudar a:

- Certifique-se de que a versão do macOS listada no **Info. plist** é o mesmo que a versão do macOS atualmente instalado no computador.
- Verifique o Visual Studio para a saída do aplicativo Mac (**exibição** -> **preenche** -> **saída do aplicativo**) para rastreamentos de pilha ou de saída em vermelho do Cocoa que pode descrever a saída.
- Execute o aplicativo de linha de comando e examine a saída (no **Terminal** aplicativo), usando: 

     `MyApp.app/Contents/MacOS/MyApp` (onde `MyApp` é o nome do seu aplicativo)
- Você pode aumentar a saída adicionando "MONO_LOG_LEVEL" para o comando na linha de comando, por exemplo: 

     `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- Você pode anexar um depurador nativo (`lldb`) para o processo para ver se que fornece mais informações (Isso requer uma licença paga). Por exemplo, faça o seguinte:

    1. Insira `lldb MyApp.app/Contents/MacOS/MyApp` no Terminal.
    2. Insira `run` no Terminal.
    3. Insira `c` no Terminal.
    4. Quando terminar a depuração de saída.
- Como último recurso, antes de chamar `NSApplication.Init` no seu `Main` método (ou em outros lugares conforme necessário), você poderia escrever texto em um arquivo em um local conhecido para rastrear em qual etapa de inicialização que você tenha problemas.

## <a name="known-issues"></a>Problemas conhecidos

As seções a seguir abordam os problemas conhecidos e suas soluções.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Não é possível conectar o depurador em aplicativos da área restrita

O depurador se conecta ao Xamarin.Mac aplicativos por meio de TCP, o que significa que por padrão quando você habilita a área restrita, não é possível se conectar ao aplicativo, portanto, se você tentar executar o aplicativo sem as permissões adequadas habilitadas, você obterá um erro *"não é possível se conectar ao o depurador"*. 

[![Os direitos de edição](troubleshooting-images/debug01.png "os direitos de edição")](troubleshooting-images/debug01-large.png#lightbox)

O **permitir saída conexões de rede (cliente)** a permissão é necessária para o depurador, habilitar esta permite que a depuração normalmente. Desde que você não pode depurar sem ele, atualizamos o `CompileEntitlements` de destino para `msbuild` para adicionar automaticamente essa permissão para os direitos para qualquer aplicativo que está na área restrita para depuração somente compilações. Compilações de versão devem usar as autorizações especificadas no arquivo de direitos, sem modificações.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException: não há dados disponíveis para codificação 437
 
Ao incluir 3º bibliotecas de terceiros em seu aplicativo Xamarin.Mac, você poderá receber um erro no formato "NotSupportedException: não há dados disponíveis para codificação 437" ao tentar compilar e executar o aplicativo. Por exemplo, bibliotecas, como `Ionic.Zip.ZipFile`, pode gerar essa exceção durante a operação.

Isso pode ser resolvido, abra as opções para o projeto Xamarin.Mac, vai **Mac criar** > **internacionalização** e verificando o **Oeste** internacionalização:

[![Editar as opções de build](troubleshooting-images/issue01.png "Editar as opções de build")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Falha ao compilar (mm5103)

Esse erro normalmente ocorre quando uma nova versão do Xcode é a versão e você tiver instalado a nova versão, mas não, ainda executá-lo. Antes de tentar compilar com uma nova versão do Xcode, você precisa executar pela primeira vez que a versão de pelo menos uma vez.

Na primeira vez que você executar uma nova versão do Xcode, ele instala várias ferramentas de linha de comando que são exigidas pelo Xamarin.Mac. Além disso, você deve fazer uma compilação limpa após a atualização Xcode ou sua versão Xamarin.Mac.

Se você não pode resolver esse problema, [registrar um bug](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Entitlements.plist ausente

A versão mais recente do Visual Studio para Mac removeu a seção de direitos do **Info. plist** editor e colocou em separado **Entitlements.plist** editor (para um melhor suporte de plataforma cruzada com o xamarin).

Com o novo Visual Studio para Mac instalado, quando você cria um novo projeto de aplicativo Xamarin.Mac um **Entitlements.plist** arquivo será automaticamente adicionado à árvore de projeto:

![Selecionar direitos](troubleshooting-images/entitlements01.png "selecionando direitos")

Se você clicar duas vezes o **Entitlements.plist** arquivo, o Editor de direitos será exibido:

[![Os direitos de edição](troubleshooting-images/entitlements02.png "os direitos de edição")](troubleshooting-images/entitlements02-large.png#lightbox)

Para projetos de Xamarin.Mac existentes, você precisará criar manualmente o **Entitlements.plist** arquivo clicando no projeto no **solução preenchimento** e selecionando **adicionar**  >  **Novo arquivo...** . Em seguida, selecione **Xamarin.Mac** > **lista vazia de propriedade**:

![Adicionando uma nova lista de propriedade](troubleshooting-images/entitlements03.png "adicionando uma nova lista de propriedades")

Digite `Entitlements` para o nome e clique o **novo** botão. Se seu projeto incluídos anteriormente um arquivo de direitos, você deverá adicioná-lo para o projeto em vez de criar um novo arquivo:

[![Verificando a substituição de um arquivo](troubleshooting-images/entitlements04.png "verificando a substituição de um arquivo")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="contacting-support-business-or-enterprise-licenses"></a>Entrar em contato com suporte (licenças de negócios ou enterprise)

Se você tiver uma empresa ou licença empresarial, você está qualificado para solicitar ajuda diretamente dos engenheiros do Xamarin por tíquetes de suporte. Consulte [xamarin.com/support](http://xamarin.com/support) para obter detalhes.

## <a name="community-support-on-the-forums"></a>Suporte nos fóruns da comunidade

A comunidade de desenvolvedores que usam o Xamarin produtos é o incrível e muitos visite nossa [Xamarin.Mac fóruns](http://forums.xamarin.com/categories/mac) compartilhe experiências e sua experiência. Além disso, os engenheiros Xamarin periodicamente visite o fórum para ajudar.

<a name="filing-a-bug"/>

## <a name="filing-a-bug"></a>Arquivar um bug

Seus comentários são importantes para nós. Se você encontrar problemas com Xamarin.Mac:

- Pesquise o [repositório de problemas](https://github.com/xamarin/xamarin-macios/issues) 
- Antes de mudar para problemas do GitHub, os problemas do Xamarin eram rastreados no [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Procure lá por problemas correspondentes.
- Se você não encontrar um problema correspondente, envie um novo problema no [repositório de problemas do GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Os problemas do GitHub são todos públicos. Não é possível ocultar comentários ou anexos. 

Inclua tanto do seguinte quanto possível:                                                                                                                                          

- Um exemplo simples reproduzindo o problema. Isso é **inestimável**, quando possível. 
- O rastreamento de pilha completo da falha.
- O código C# ao redor da falha. 
