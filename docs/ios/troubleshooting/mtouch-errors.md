---
title: Erros de xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 32a73232667e54eef7536f0bb0d1baa190269d8e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinios-errors"></a>Erros de xamarin

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mensagens de erro mtouch

Por exemplo, parâmetros, ambiente, falta de ferramentas.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

### <a name="a-namemt0000mt0000-unexpected-error---please-fill-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT0000"/>MT0000: Erro inesperado - preencha um relatório de erros em http://bugzilla.xamarin.com

Ocorreu uma condição de erro inesperado. Por favor, [relatório de erro](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com tanta informação quanto possível, incluindo:

* Compilação completa logs, com um máximo de detalhamento (por exemplo, `-v -v -v -v` no **argumentos adicionais mtouch**);
* Um caso de teste mínimo que reproduza o erro; e
* Todas as informações de versão

A maneira mais fácil de obter informações sobre a versão exata é usar o **Visual Studio para Mac** menu **sobre o Visual Studio para Mac** item **Mostrar detalhes** botão e copiar/colar o informações de versão (você pode usar o **copiar informações** botão).

### <a name="a-namemt0001mt0001--devname-was-provided-without-any-device-specific-action"></a><a name="MT0001"/>MT0001: '-devname' foi fornecido sem qualquer ação específicos do dispositivo

Este é um aviso é emitido se - devname for passado para mtouch quando nenhuma ação específicos do dispositivo (-logdev/installdev/killdev/launchdev /-listapps) foi solicitado.

### <a name="a-namemt0002mt0002-could-not-parse-the-environment-variable-"></a><a name="MT0002"/>MT0002: Não foi possível analisar a variável de ambiente *.

Esse erro ocorre se você tentar definir uma chave de ambiente inválida = par de valor variável. O formato correto é: `mtouch --setenv=VARIABLE=VALUE`

### <a name="a-namemt0003mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a><a name="MT0003"/>MT0003: Nome do aplicativo ' * .exe ' conflita com um nome de assembly (. dll) do SDK ou produto.

Nome do assembly executável e o nome do aplicativo não podem coincidir com o nome de qualquer dll no aplicativo. Modifique o nome do executável.

### <a name="a-namemt0004mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a><a name="MT0004"/>MT0004: Nova lógica de refcounting requer SGen ser habilitada.

Se você habilitar a extensão de refcounting você também deve habilitar o coletor de lixo SGen no iOS do projeto opções de compilação (guia Avançado).

Começando com o xamarin 7.2.1 esse requisito foi eliminada, a nova lógica de refcounting pode ser habilitada com Boehm e Coletores de lixo SGen.

### <a name="a-namemt0005mt0005-the-output-directory--does-not-exist"></a><a name="MT0005"/>MT0005: O diretório de saída * não existe.

Crie o diretório.

Esse erro não é mais gerado, mtouch criará automaticamente o diretório se ele não existir.

### <a name="a-namemt0006mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a><a name="MT0006"/>MT0006: Não há nenhuma plataforma devel em *, use--plataforma = para especificar o SDK em várias plataformas.

Xamarin não é possível localizar o diretório do SDK no local mencionado na mensagem de erro. Verifique se o caminho está correto.

### <a name="a-namemt0007mt0007-the-root-assembly--does-not-exist"></a><a name="MT0007"/>MT0007: O assembly raiz * não existe.

Xamarin não é possível localizar o assembly no local mencionado na mensagem de erro. Verifique se o caminho está correto.

### <a name="a-namemt0008mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a><a name="MT0008"/>MT0008: Você deve fornecer uma raiz assembly único, encontrado # assemblies: *.

Mais de um assembly de raiz foi passado para mtouch, enquanto que pode haver assembly somente uma raiz.

### <a name="a-namemt0009mt0009-error-while-loading-assemblies-"></a><a name="MT0009"/>MT0009: Erro ao carregar assemblies: *.

Ocorreu um erro ao carregar os assemblies as referências de assembly de raiz. Mais informações podem ser fornecidas na saída da compilação.

### <a name="a-namemt0010mt0010-could-not-parse-the-command-line-arguments-"></a><a name="MT0010"/>MT0010: Não foi possível analisar os argumentos de linha de comando: *.

Ocorreu um erro ao analisar os argumentos de linha de comando. Verifique se eles estão todos corretos.

### <a name="a-namemt0011mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a><a name="MT0011"/>MT0011: * foi desenvolvido com um tempo de execução mais recente (*) que oferece suporte a MonoTouch.

Esse aviso é relatado normalmente porque o projeto possui uma referência a uma biblioteca de classes que não foi criada usando a BCL do xamarin.

Da mesma forma que um aplicativo usando o SDK do .NET 4.0 pode não funcionar em um sistema somente com suporte do .NET 2.0, uma biblioteca criada usando o .NET 4.0 pode não funcionar em xamarin, ele pode usar a API não está presente no xamarin.

A solução geral é criar a biblioteca como uma biblioteca de classes do xamarin. Isso pode ser feito por meio da criação de um novo projeto de biblioteca de classes do xamarin e adicionar todos os arquivos de origem para ele. Se você não tiver o código-fonte para a biblioteca, deve entrar em contato com o fornecedor e solicite que eles fornecem uma versão compatível com o xamarin da sua biblioteca.

### <a name="a-namemt0012mt0012-incomplete-data-is-provided-to-complete-"></a><a name="MT0012"/>MT0012: Dados incompletos são fornecidos para concluir *.

Esse erro não é relatado mais na versão atual do xamarin.

### <a name="a-namemt0013mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a><a name="MT0013"/>MT0013: Suporte de criação de perfil requer sgen para ser habilitada.

SGen (– sgen) deve ser habilitada se a criação de perfil (– criação de perfil) está habilitado.

### <a name="a-namemt0014mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a><a name="MT0014"/>MT0014: O iOS * SDK não dá suporte a aplicativos de criação de direcionamento *.

Isso pode ocorrer nas seguintes circunstâncias:

*  ARMv6 está habilitado e Xcode 4.5 ou posterior está instalado.
*  ARMv7s está habilitado e Xcode 4.4 ou anterior é instalado.

Verifique se a versão instalada do Xcode dá suporte a arquiteturas selecionadas.

### <a name="a-namemt0015mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a><a name="MT0015"/>MT0015: ABI inválido: *. ABIs com suporte são: i386, x86_64, armv7, armv7 llvm, armv7 + llvm + thumb2, armv7s, armv7s + llvm, armv7s + llvm + thumb2, arm64 e arm64 + llvm.

Uma ABI inválido foi passado para mtouch. Especifique uma ABI válido.

### <a name="a-namemt0016mt0016-the-option--has-been-deprecated"></a><a name="MT0016"/>MT0016: A opção * foi preterido.

A opção mtouch mencionadas foi preterida e será ignorada.

### <a name="a-namemt0017mt0017-you-should-provide-a-root-assembly"></a><a name="MT0017"/>MT0017: Você deve fornecer um assembly de raiz.

É necessário especificar um assembly de raiz (normalmente o executável principal) ao criar um aplicativo.

### <a name="a-namemt0018mt0018-unknown-command-line-argument-"></a><a name="MT0018"/>MT0018: Argumento de linha de comando desconhecido: *.

Mtouch não reconhece o argumento de linha de comando mencionado na mensagem de erro.

### <a name="a-namemt0019mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a><a name="MT0019"/>MT0019: Somente um – [log | instalar | kill | Iniciar] desenvolvimento ou --[Iniciar | depurar] sim opção pode ser usada.

Há várias opções para mtouch que não podem ser usados simultaneamente:

-  --logdev
-  – installdev
-  – killdev
-  --launchdev
-  --launchdebug
-  --launchsim

### <a name="a-namemt0020mt0020-the-valid-options-for--are-"></a><a name="MT0020"/>MT0020 As opções válidas para '\*'são'\*'.

### <a name="a-namemt0021mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a><a name="MT0021"/>Compilação MT0021 não é possível usar gcc / g + + (-use gcc) ao usar o registrador estático (esse é o padrão de compilação para dispositivo). Remova--use-gcc sinalizador ou use o registro dinâmico (-registrador: dinâmico).

### <a name="a-namemt0022mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a><a name="MT0022"/>MT0022 Opções '- sem suporte - enable-genéricos-em-registrador' e '-registrador ' não são compatíveis.

Remova as opções `--unsupported--enable-generics-in-registrar` e `--registrar`. Começando com o xamarin 7.2.1 registrador padrão oferece suporte a genéricos.

Esse erro não é mostrado (o argumento de linha de comando `--unsupported--enable-generics-in-registrar` foi removido do mtouch).

### <a name="a-namemt0023mt0023-application-name-exe-conflicts-with-another-user-assembly"></a><a name="MT0023"/>Nome do aplicativo MT0023 ' * .exe ' está em conflito com outro assembly de usuário.

Nome do assembly executável e o nome do aplicativo não podem coincidir com o nome de qualquer dll no aplicativo. Modifique o nome do executável.

### <a name="a-namemt0024mt0024-could-not-find-required-file-"></a><a name="MT0024"/>Não foi possível MT0024 localizar o arquivo necessário ' *'.

### <a name="a-namemt0025mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a><a name="MT0025"/>Versão do SDK do MT0025 não foi fornecido. Adicione `--sdk=X.Y` para especificar quais iOS SDK deve ser usado para criar seu aplicativo.

### <a name="a-namemt0026mt0026-could-not-parse-the-command-line-argument--"></a><a name="MT0026"/>Não MT0026 foi possível analisar o argumento de linha de comando ' *': *

### <a name="a-namemt0027mt0027-the-options--and--are-not-compatible"></a><a name="MT0027"/>MT0027 As opções\*'e'\*' não são compatíveis.

### <a name="a-namemt0028mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a><a name="MT0028"/>MT0028 não é possível habilitar o pizza (-pizza) durante o direcionamento do iOS 4.1 ou anterior. Desabilite pizza (-pizza: false) ou definir o destino de implantação pelo menos iOS 4.2

### <a name="a-namemt0029mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a><a name="MT0029"/>MT0029: REPL (-enable-repl) só tem suporte no simulador (-sim).

Somente há suporte para REPL se você estiver criando do simulador. Isso significa que, se você passar `--enable-repl` para mtouch, você também deve transmitir `--sim`.

### <a name="a-namemt0030mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a><a name="MT0030"/>MT0030: O nome do executável (\*) e o nome do aplicativo (\*) são diferentes, isso pode impedir que os logs de falha obtendo symbolicated corretamente.

Quando o Xcode symbolicates (converte os endereços de memória para nomes de função e números de linha/arquivo) o processo poderá falhar se o executável e o aplicativo têm nomes diferentes (sem a extensão).

Para corrigir essa altere 'Nome do aplicativo' em Opções de aplicativos do iOS/Build do projeto ou altere 'Nome do Assembly' nas opções de compilação e a saída do projeto.

### <a name="a-namemt0031mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a><a name="MT0031"/>MT0031: Os argumentos de linha de comando ' – Habilitar de plano de fundo de busca ' e ' – lançamento de busca em segundo plano ' requer '-launchsim' muito.

### <a name="a-namemt0032mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a><a name="MT0032"/>MT0032: A opção '-debugtrack' é ignorado, a menos que '-debug' também for especificado.

### <a name="a-namemt0033mt0033--a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a><a name="MT0033"/>MT0033 xamarin de um projeto deve fazer referência a monotouch.dll ou Xamarin.iOS.dll

### <a name="a-namemt0034mt0034--cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a><a name="MT0034"/>MT0034 não é possível incluir 'monotouch.dll' e 'Xamarin.iOS.dll' no mesmo projeto xamarin - '\*' é referenciado explicitamente, enquanto '\*' é referenciado por ' *'.

<!-- MT0035 unused -->

### <a name="a-namemt0036mt0036--cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a><a name="MT0036"/>MT0036 não é possível iniciar um * simulador para um * aplicativo. Habilite o architecture(s) correto no iOS do seu projeto compilação opções (página Avançado).

### <a name="a-namemt0037mt0037--monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a><a name="MT0037"/>MT0037 monotouch.dll é 64 bits não compatível. A referência Xamarin.iOS.dll, ou não criar uma arquitetura de 64 bits (ARM64 e/ou x86_64).

### <a name="a-namemt0038mt0038--the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a><a name="MT0038"/>MT0038 Os registradores antigos (-registrador: oldstatic | olddynamic) não têm suporte ao fazer referência a Xamarin.iOS.dll.

### <a name="a-namemt0039mt0039--applications-targeting-armv6-cannot-reference-xamariniosdll"></a><a name="MT0039"/>Aplicativos MT0039 direcionamento ARMv6 não pode referenciar Xamarin.iOS.dll.

### <a name="a-namemt0040mt0040--could-not-find-the-assembly--referenced-by-"></a><a name="MT0040"/>MT0040 não localizou o assembly '\*', referenciado pelo'\*'.

### <a name="a-namemt0041mt0041--cannot-reference-both-monotouchdll-and-xamariniosdll"></a><a name="MT0041"/>Não é possível fazer referência a MT0041 'monotouch.dll' e 'Xamarin.iOS.dll'.

### <a name="a-namemt0042mt0042--no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a><a name="MT0042"/>Referência de MT0042 não monotouch.dll ou Xamarin.iOS.dll foi encontrada. Uma referência a monotouch.dll será adicionada.

### <a name="a-namemt0043mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a><a name="MT0043"/>MT0043: O coletor de lixo Boehm atualmente não há suporte ao fazer referência a 'Xamarin.iOS.dll'. O coletor de lixo SGen foi selecionado em vez disso.

O coletor de lixo SGen é compatível com projetos unificada. Certifique-se de que não há nenhum sinalizador adicional mtouch especificando Boehm como o coletor de lixo.

### <a name="a-namemt0044mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a><a name="MT0044"/>MT0044: - listsim só é suportado com o Xcode 6.0 ou posterior.

Instale uma versão mais recente do Xcode.

### <a name="a-namemt0045mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a><a name="MT0045"/>MT0045:-extensão só tem suporte ao usar o iOS SDK 8.0 (ou posterior).

<!-- MT0046 is not reported anymore -->

### <a name="a-namemt0047mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a><a name="MT0047"/>MT0047: O destino de implantação mínima para aplicativos unificada é 5.1.1, o destino de implantação atual é ' *'. Selecione um destino de implantação mais recente no iOS do seu projeto opções do aplicativo.

<!-- MT0048 is not reported anymore -->

### <a name="a-namemt0049mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a><a name="MT0049"/>MT0049: *.framework só é suportado se o destino de implantação é 8.0 ou posterior. * recursos podem não funcionar corretamente.

Não há suporte para a estrutura especificada refere-se o destino de implantação para a versão do iOS. Atualize o destino de implantação para uma versão mais recente do iOS ou remova o uso do framework especificado do aplicativo.

<!-- MT0050 is not reported anymore -->

### <a name="a-namemt0051mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a><a name="MT0051"/>MT0051: Xamarin * requer Xcode 5.0 ou posterior. A versão atual do Xcode (encontrado no *) é *.

Instale um Xcode mais recente.

### <a name="a-namemt0052mt0052-no-command-specified"></a><a name="MT0052"/>MT0052: Nenhum comando especificado.

Nenhuma ação foi especificada para mtouch.

<!-- 0053 is used by mmp -->

### <a name="a-namemt0054mt0054-unable-to-canonicalize-the-path--"></a><a name="MT0054"/>MT0054: Não é possível tornar canônicos o caminho ' *': *

Este é um erro interno. Se você vir esse erro, envie um bug [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt0055mt0055-the-xcode-path--does-not-exist"></a><a name="MT0055"/>MT0055: O caminho do Xcode ' *' não existe.

O caminho do Xcode passado usando `--sdkroot` não existe. Especifique um caminho válido.

### <a name="a-namemt0056mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a><a name="MT0056"/>MT0056: Não é possível localizar o Xcode no local padrão (/ Applications/Xcode.app). Instale o Xcode ou passar um caminho personalizado usando - sdkroot <path>.
### <a name="a-namemt0057mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a><a name="MT0057"/>MT0057: Não é possível determinar o caminho para Xcode.app da raiz do sdk ' *'. Especifique o caminho completo para o pacote de Xcode.app.

O caminho passado usando `--sdkroot` não especificar um aplicativo válido do Xcode. Especifique um caminho para um aplicativo do Xcode.

### <a name="a-namemt0058mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a><a name="MT0058"/>MT0058: O Xcode.app '\*' é inválido (o arquivo '\*' não existe).

O caminho passado usando `--sdkroot` não especificar um aplicativo válido do Xcode. Especifique um caminho para um aplicativo do Xcode.

### <a name="a-namemt0059mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a><a name="MT0059"/>MT0059: Não foi possível encontrar o Xcode selecionado atualmente no sistema: *

### <a name="a-namemt0060mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a><a name="MT0060"/>MT0060: Não foi possível encontrar o Xcode selecionado atualmente no sistema. 'xcode-select-caminho de impressão' retornada ' *', mas esse diretório não existe.

### <a name="a-namemt0061mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a><a name="MT0061"/>MT0061: Nenhum Xcode.app especificado (usando - sdkroot), usando o sistema Xcode conforme relatado por 'xcode-select-caminho de impressão': *

Este é um aviso informativo, explicando que Xcode será usada, pois nenhuma foi especificada.

### <a name="a-namemt0062mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a><a name="MT0062"/>MT0062: Nenhum Xcode.app especificado (usando - sdkroot ou 'xcode-select-caminho de impressão'), usando o Xcode padrão em vez disso: *

Este é um aviso informativo, explicando que Xcode será usada, pois nenhuma foi especificada.

### <a name="a-namemt0063mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a><a name="MT0063"/>MT0063: Não é possível localizar o executável na extensão de * (nenhuma entrada CFBundleExecutable no seu Info. plist)

Cada Info. plist deve ter um executável (usando a entrada CFBundleExecutable), no entanto, uma entrada deve ser gerada automaticamente durante a compilação.

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0064mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a><a name="MT0064"/>MT0064: Xamarin só dá suporte a estruturas inseridas com projetos unificada.

Xamarin só oferece suporte a estruturas incorporadas ao usar a API unificada; Atualize o projeto para usar a API unificada.

### <a name="a-namemt0065mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a><a name="MT0065"/>MT0065: Xamarin só oferece suporte a estruturas inseridas quando o destino de implantação é pelo menos 8.0 (destino de implantação atual: * inseridos estruturas: *)

Xamarin somente dá suporte a estruturas inseridas quando o destino de implantação é pelo menos 8.0 (porque versões anteriores do iOS não dá suporte a estrutura inserida).

Atualize o destino da implantação Info. plist do projeto para 8.0 ou superior.

### <a name="a-namemt0066mt0066-invalid-build-registrar-assembly-"></a><a name="MT0066"/>MT0066: Assembly do registrador de compilação inválido: *

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0067mt0067-invalid-registrar-"></a><a name="MT0067"/>MT0067: Registrador inválido: *

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0068mt0068-invalid-value-for-target-framework-"></a><a name="MT0068"/>MT0068: O valor inválido para a estrutura de destino: *.

Uma estrutura de destino inválido foi passada usando o argumento-- estrutura de destino. Especifique uma estrutura de destino válido.

<!--### <a name="MT0069"/>MT0069: currently unused -->

### <a name="a-namemt0070mt0070-invalid-target-framework--valid-target-frameworks-are-"></a><a name="MT0070"/>MT0070: Estrutura de destino inválido: *. Estrutura de destino válidos é: *.

Uma estrutura de destino inválido foi passada usando o argumento-- estrutura de destino. Especifique uma estrutura de destino válido.

### <a name="a-namemt0071mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a><a name="MT0071"/>MT0071: Plataforma desconhecida: *. Isso geralmente indica um bug no xamarin; Envie um relatório de erros em http://bugzilla.xamarin.com com um caso de teste.

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0072mt0072-extensions-are-not-supported-for-the-platform-"></a><a name="MT0072"/>MT0072: Não são permitidas extensões para a plataforma ' *'.

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0073mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a><a name="MT0073"/>MT0073: Xamarin * não oferece suporte a um destino de implantação de * (o mínimo é *). Selecione um destino de implantação mais recente no Info. plist do seu projeto.

O destino de implantação mínimo é aquele especificado na mensagem de erro; Selecione um destino de implantação mais recente no Info. plist do projeto.

Se não for possível atualizar o destino de implantação, use uma versão anterior do xamarin.

### <a name="a-namemt0074mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a><a name="MT0074"/>MT0074: Xamarin * não oferece suporte a um destino de implantação mínima de * (o máximo é *). Selecione um destino de implantação mais antigo no Info. plist seu projeto ou atualizar para uma versão mais recente do xamarin.

Xamarin não oferece suporte para definir o destino de implantação mínimo para a versão para que esta versão específica do xamarin foi criado uma versão superior.

Selecione um destino de implantação mínima mais antigo no Info. plist do projeto ou atualizar para uma versão mais recente do xamarin.

### <a name="a-namemt0075mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a><a name="MT0075"/>MT0075: Arquitetura inválida ' *' para * projetos. Arquiteturas válidas são: *

Uma arquitetura inválida foi especificada. Verifique se a arquitetura é válida.

### <a name="a-namemt0076mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a><a name="MT0076"/>MT0075: Nenhum arquitetura especificada (usando o argumento – abi). Uma arquitetura é necessária para * projetos.

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0077mt0076-watchos-projects-must-be-extensions"></a><a name="MT0077"/>MT0076: WatchOS projetos devem ser extensões.

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0078mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a><a name="MT0078"/>MT0077: Compilações incrementais são habilitadas com um destino de implantação < 8.0 (atualmente *). Isso não é suportado (o aplicativo resultante não será iniciado no iOS 9) para o destino de implantação será definido como 8.0.

Este é um aviso informando que o destino de implantação tiver sido definido como 8.0 para esta compilação para que as compilações incrementais trabalho corretamente.

Compilações incrementais são suportadas apenas quando o destino de implantação é pelo menos 8.0 (porque o aplicativo resultante não será iniciado no iOS 9 caso contrário).

### <a name="a-namemt0079mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a><a name="MT0079"/>MT0078: O Xcode versão recomendada para xamarin * é Xcode * ou posterior. A versão atual do Xcode (encontrado no *) é *.

Este é um aviso informando que a versão atual do Xcode não é a versão recomendada do Xcode para esta versão do xamarin.

Atualize o Xcode para garantir o comportamento ideal.

### <a name="a-namemt0080mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a><a name="MT0080"/>MT0080: Desabilitar NewRefCount, --new-refcount:false, foi preterido.

Este é um aviso informando que a solicitação para desativar o novo refcount (- new - refcount:false) foi ignorado.

O novo recurso refcount agora é obrigatório para todos os projetos e, portanto, não é possível desabilitar a mais.

### <a name="a-namemt0081mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a><a name="MT0081"/>MT0081: O argumento de linha de comando--relatório de falhas de download também requer - download-crash-relatório-a.
### <a name="a-namemt0082mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a><a name="MT0082"/>MT0082: REPL (-enable-repl) é suportado apenas quando a vinculação não é usada (-nolink).
### <a name="a-namemt0083mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a><a name="MT0083"/>MT0083: Bitcode somente Asm não é suportado em watchOS. Use o bitcode –: marcador ou – bitcode: completo.
### <a name="a-namemt0084mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a><a name="MT0084"/>MT0084: Bitcode não é suportado no simulador. Não passe - bitcode durante a criação do simulador.
### <a name="a-namemt0085mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a><a name="MT0085"/>MT0085: Nenhuma referência a ' *' foi encontrado. Ele será adicionado automaticamente.
### <a name="a-namemt0086mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a><a name="MT0086"/>MT0086: Uma estrutura de destino (-estrutura de destino) deve ser especificado ao criar para TVOS ou WatchOS.

Isso geralmente indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0087mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a><a name="MT0087"/>MT0087: Compilações incrementais (-fastdev) não é compatível com o GC Boehm. Compilações incrementais serão desabilitadas.

### <a name="a-namemt0088mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a><a name="MT0088"/>MT0088: O GC deve estar no modo cooperativo para aplicativos watchOS. Remova argumento-- cooperação: false para mtouch.

### <a name="a-namemt0089mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a><a name="MT0089"/>MT0089: A opção '\*'não é possível definir o valor'\*' quando o modo cooperativo está habilitado para o GC.

### <a name="a-namemt0091mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a><a name="MT0091"/>MT0091: Esta versão do xamarin requer o * SDK (fornecido com o Xcode *). A atualização Xcode para obter os arquivos de cabeçalho necessários ou definir o comportamento de vinculador gerenciado para Link Framework SDKs somente (tente evitar as novas APIs).

Xamarin requer os arquivos de cabeçalho, da versão do SDK especificado na mensagem de erro, para criar seu aplicativo. A maneira recomendada para corrigir esse erro é atualizar o Xcode para obter o SDK necessário, isso inclui todos os arquivos de cabeçalho necessários. Se você tiver várias versões do Xcode instalado, ou usar um Xcode em um local diferente do padrão, certifique-se de definir o local correto do Xcode nas preferências do IDE.

Um potencial solução alternativa é habilitar o vinculador gerenciado. Isso removerá não utilizados incluindo de API, na maioria dos casos, a nova API de onde os arquivos de cabeçalho estão ausentes (ou incompletos). No entanto isso não funcionará se seu projeto usa a API que foi introduzida em um SDK mais recente que a sua Xcode fornece.

Uma solução de última straw seria usar uma versão anterior do xamarin, que oferece suporte ao SDK do seu projeto requer.

<!-- MT0092 used by mlaunch -->

### <a name="a-namemt0093mt0093-could-not-find-mlaunch"></a><a name="MT0093"/>MT0093: Não foi possível encontrar 'mlaunch'.

<!-- MT0094 is not reported anymore -->

### <a name="a-namemt0095mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a><a name="MT0095"/>MT0095: Aot não foi possível copiar arquivos para o diretório de destino {dest}: {error}

### <a name="a-namemt0096mt0096-no-reference-to-xamariniosdll-was-found"></a><a name="MT0096"/>MT0096: Nenhuma referência a Xamarin.iOS.dll foi encontrada.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

### <a name="a-namemt0099mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a><a name="MT0099"/>MT0099: Erro interno *. Envie um relatório de erros com um caso de teste (http://bugzilla.xamarin.com).

Essa mensagem de erro é relatada quando ocorre falha na verificação de consistência interna em xamarin.

Isso indica um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0100mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a><a name="MT0100"/>MT0100: Destino de compilação do assembly inválido: ' *'. Envie um relatório de erros com um caso de teste (http://bugzilla.xamarin.com).

Essa mensagem de erro é relatada quando ocorre falha na verificação de consistência interna em xamarin.

Isso é sempre um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

### <a name="a-namemt0101mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a><a name="MT0101"/>MT0101: O assembly ' *' está especificado várias vezes no, os argumentos de destino de compilação do assembly.

O assembly mencionado na mensagem de erro é especificado várias vezes no, os argumentos de destino de compilação do assembly. Verifique se que cada assembly é oferecida apenas uma vez.

### <a name="a-namemt0102mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a><a name="MT0102"/>MT0102: Os assemblies'\*'e'\*' têm o mesmo nome de destino ('\*'), mas diferentes destinos ('\*' e ' *').

Os assemblies mencionados na mensagem de erro ter conflitantes destinos de compilação.

Por exemplo:

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

Este exemplo está tentando criar uma biblioteca dinâmica e uma estrutura com a mesma marca (`MyBinary`).

### <a name="a-namemt0103mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a><a name="MT0103"/>MT0103: O objeto estático '\*' contém mais de um assembly ('\*'), mas cada objeto estático deve corresponder com exatamente um assembly.

Os assemblies mencionados na mensagem de erro são compilados em um único objeto estático. Isso não é permitido, cada assembly deve ser compilado para um objeto estático diferente.

Por exemplo:

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

Este exemplo tenta construir um objeto estático (`MyBinary`) composta de dois assemblies (`Assembly1.dll` e `Assembly2.dll`), que não é permitido.

### <a name="a-namemt0105mt0105-no-assembly-build-target-was-specified-for-"></a><a name="MT0105"/>MT0105: Nenhum destino de compilação do assembly foi especificado para ' *'.

Quando especificar o assembly compilação de destino usando `--assembly-build-target`, cada assembly no aplicativo deve ter um destino de compilação atribuído.

Esse erro é relatado quando o assembly mencionado na mensagem de erro não tem um assembly atribuído de destino de compilação.

Consulte a documentação `--assembly-build-target` para obter mais informações.

### <a name="a-namemt0106mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a><a name="MT0106"/>MT0106: Nome do destino de compilação do assembly '\*' é inválido: o caractere '\*' não é permitido.

O nome de destino de compilação do assembly deve ser um nome de arquivo válido.

Por exemplo, esses valores disparará esse erro:

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

porque `my/path.o` não é um nome de arquivo válido devido ao caractere separador de diretório.

### <a name="a-namemt0107mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a><a name="MT0107"/>MT0107: Os assemblies'\*' têm diferentes otimizações LLVM personalizadas (\*), que não é permitido quando eles são compilados em um único binário.

### <a name="a-namemt0108mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a><a name="MT0108"/>MT0108: O assembly de destino de compilação ' *' não corresponde a todos os assemblies.

### <a name="a-namemt0109mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a><a name="MT0109"/>MT0109: O assembly '{0}' foi carregado de um caminho diferente do caminho fornecido (fornecidos o caminho: \\{1 \\}, o caminho real: 2}).

Este é um aviso indicando que um assembly referenciado pelo aplicativo foi carregado de um local diferente do solicitado.

Isso pode significar que o aplicativo faz referência a vários assemblies com o mesmo nome, mas em diferentes locais, o que podem levar a resultados inesperados (somente o primeiro conjunto será usado).

### <a name="a-namemt0110mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a><a name="MT0110"/>MT0110: Compilações incrementais foram desabilitadas porque esta versão do xamarin não oferece suporte a compilações incrementais em projetos que incluem bibliotecas de associação de terceiros e que é compilado em bitcode.

Compilações incrementais foram desabilitadas porque esta versão do xamarin não oferece suporte a compilações incrementais em projetos que incluem bibliotecas de associação de terceiros e que é compilado em bitcode (projetos tvOS e watchOS).

Nenhuma ação é necessária de sua parte, esta mensagem é apenas informativa.

Para obter mais informações, consulte bug n º[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Esse aviso não é reportado mais.

### <a name="a-namemt0111mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a><a name="MT0111"/>MT0111: Bitcode foi habilitada porque esta versão do xamarin não oferece suporte a construção watchOS projetos usando LLVM sem habilitar bitcode.

Bitcode foi habilitado automaticamente porque esta versão do xamarin não oferece suporte a construção watchOS projetos usando LLVM sem habilitar bitcode.

Nenhuma ação é necessária de sua parte, esta mensagem é apenas informativa.

Para obter mais informações, consulte bug n º[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

### <a name="a-namemt0112mt0112-native-code-sharing-has-been-disabled-because-"></a><a name="MT0112"/>MT0112: O compartilhamento de código nativo foi desabilitada porque *

Há vários motivos, compartilhamento de código pode ser desabilitado:

* porque o destino de implantação do aplicativo de contêiner é anterior ao iOS 8.0 (tem *)).

Código nativo de compartilhamento requer iOS 8.0 porque o compartilhamento de código nativo é implementada usando estruturas de usuário, que foi introduzido com o iOS 8.0.

* como o aplicativo de contêiner inclui assemblies I18N (*).

Código nativo compartilhamento atualmente não é suportado se o aplicativo de contêiner inclui assemblies I18N.

* porque o aplicativo de contêiner tem definições xml personalizado para o vinculador gerenciado (*).

Código nativo de compartilhamento requer que não há suporte para projetos que usam definições xml personalizado para o vinculador gerenciado.

### <a name="a-namemt0113mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a><a name="MT0113"/>MT0113: O compartilhamento de código nativo foi desabilitada para a extensão ' *' como *.

* como as opções de bitcode diferem entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que as opções de bitcode correspondem entre os projetos que compartilham o código.

* porque-conjunto-build-destino opções são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  Código nativo de compartilhamento requer que-conjunto-build-destino opções são idênticas entre os projetos que compartilham o código.

  Essa condição pode ocorrer se compilações incrementais não estão habilitadas ou desabilitadas em todos os projetos.

* porque os assemblies I18N são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  Compartilhamento de código nativo não há suporte para extensões que incluem I18N assemblies no momento.

* como os argumentos para o compilador AOT são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que os argumentos para o compilador AOT não diferem entre os projetos que compartilham o código.

* como os outros argumentos para o compilador AOT são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que os argumentos para o compilador AOT não diferem entre os projetos que compartilham o código.

  Essa condição ocorre se o 'executar todas as operações de float de 32 bits como float de 64 bits' difere entre projetos.

* porque LLVM não está habilitado ou desabilitado no aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que LLVM está habilitado ou desabilitado para todos os projetos que compartilham o código.

* como as configurações de vinculador gerenciado são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que as configurações de vinculador gerenciado são idênticas para todos os projetos que compartilham o código.

* porque os assemblies ignorados para o vinculador gerenciado são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que as configurações de vinculador gerenciado são idênticas para todos os projetos que compartilham o código.

* porque a extensão tem definições xml personalizado para o vinculador gerenciado (*).

  Código nativo de compartilhamento requer que não há suporte para projetos que usam definições xml personalizado para o vinculador gerenciado.

* porque o aplicativo de contêiner não compilar para a ABI * (enquanto está criando a extensão para este ABI).

  O compartilhamento de código nativo requer que seja criada para todas as arquiteturas cria qualquer extensão do aplicativo para o aplicativo de contêiner.

  Por exemplo: essa condição ocorre quando uma extensão cria para ARM64 + ARMv7, mas o aplicativo de contêiner só cria para ARM64.

* porque o aplicativo de contêiner é compilado para a ABI \*, que não é compatível com ABI a extensão (\*).

  O compartilhamento de código nativo requer que todos os projetos de compilação para a mesma API exata.

  Por exemplo: essa condição ocorre quando uma extensão cria para ARMv7 + llvm + thumb2, mas o aplicativo de contêiner só cria para ARMv7 + llvm.

* porque o aplicativo de contêiner está fazendo referência o assembly '\*'de'\*', enquanto a extensão faz referência a uma versão diferente de ' *'.

  O compartilhamento de código nativo requer que todos os projetos que compartilham o código usam as mesmas versões para todos os assemblies.

<!-- MT0114: used by mmp -->

### <a name="a-namemt0115mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a><a name="MT0115"/>MT0115: É recomendável para referenciar símbolos dinâmicos usando código (-dinâmico de símbolo de modo = code) quando bitcode está habilitado.

Xamarin projetos geralmente referenciará símbolos nativo dinamicamente, que significa que o vinculador nativo pode remover esses símbolos nativo durante o processo de vinculação nativo, porque o vinculador nativo não verá que esses símbolos são usados.

Geralmente xamarin pedirá que o vinculador nativo para manter esses símbolos (usando o `-u symbol` sinalizador de vinculador), mas quando estiver compilando para bitcode o vinculador nativo não aceita o `-u` sinalizador.

Xamarin implementou uma solução alternativa: podemos gerar código nativo extra que faz referência a esses símbolos e, portanto, o vinculador nativo verá que esses símbolos são usados. Isso é feito automaticamente durante a compilação para bitcode.

Se `--dynamic-symbol-mode=linker` é passado para mtouch, nesse alternativo solução será desabilitada e xamarin tentará passar `-u` para o vinculador nativo. Isso provavelmente resultará em erros de vinculador nativo.

A solução é remover o `--dynamic-symbol-mode=linker` argumento dos argumentos adicionais mtouch nas opções de compilação do projeto.

<!-- 0116 - 0124: free to use -->

### <a name="a-namemt0116mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a><a name="MT0116"/>MT0116: Arquitetura inválida: {arch}. Não há suporte para arquiteturas de 32 bits quando o destino de implantação é de 11 ou posterior. Verifique se que o projeto não compilado para uma arquitetura de 32 bits.

iOS 11 não tem suporte para aplicativos de 32 bits, portanto não há suporte para criar um aplicativo de 32 bits quando o destino de implantação é iOS 11 ou posterior.

Altere a arquitetura de destino nas opções de compilação do iOS do projeto para arm64, ou alterar o destino da implantação Info. plist do projeto para uma versão anterior do iOS.

### <a name="a-namemt0117mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a><a name="MT0117"/>MT0117: Não é possível iniciar um aplicativo de 32 bits em um simulador que só oferece suporte a 64 bits.

### <a name="a-namemt0118mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a><a name="MT0118"/>MT0118: Aot arquivos não foi encontrados no diretório esperado '{msymdir}'.

<!-- 0119 - 0123: free to use -->

### <a name="a-namemt0123mt0123-the-executable-assembly--does-not-reference-"></a><a name="MT0123"/>MT0123: O assembly executável * não faz referência a *.

Nenhuma referência foi encontrada para o assembly de plataforma (Xamarin.iOS.dll / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) no assembly executável.

Isso geralmente acontece quando não há nenhum código no projeto executável que usa qualquer coisa, desde o assembly de plataforma. Por exemplo, um método Main vazio (e nenhum outro código) mostraria este erro:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

Usar uma API do assembly de plataforma para solucionar o erro:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

### <a name="a-namemt0124mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a><a name="MT0124"/>MT0124: Não foi possível definir o idioma atual para '{lang}' (de acordo com a LANG = {LANG}): {exceção}

Este é um aviso, indicando que o idioma atual não pôde ser definido como o idioma na mensagem de erro.

O idioma padrão será o idioma do sistema.

### <a name="a-namemt0125mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a><a name="MT0125"/>MT0125:--conjunto--destino da compilação do argumento de linha de comando é ignorado no simulador.

Nenhuma ação é necessária, esta mensagem é apenas informativa.

### <a name="a-namemt0126mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a><a name="MT0126"/>MT0126: Compilações incrementais foram desabilitadas porque não há suporte para compilações incrementais no simulador.

Nenhuma ação é necessária, esta mensagem é apenas informativa.

### <a name="a-namemt0127mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a><a name="MT0127"/>MT0127: Compilações incrementais foram desabilitadas porque esta versão do xamarin não oferece suporte a compilações incrementais em projetos que incluem bibliotecas de associação de mais de um terceiro.

Compilações incrementais foram desabilitadas automaticamente porque esta versão do xamarin não sempre cria projetos com várias bibliotecas de terceiros associação corretamente.

Nenhuma ação é necessária, esta mensagem é apenas informativa.

Para obter mais informações, consulte bug n º[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

### <a name="a-namemt0128mt0128-could-not-touch-the-file--"></a><a name="MT0128"/>MT0128: Não foi possível tocar o arquivo ' *': *

Falha ao tocar em um arquivo (que é feito para garantir a compilações parciais são feitas corretamente).

Esse aviso provavelmente pode ser ignorado; no caso de problemas de registrar um bug (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) e são investigado.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Mensagens de erro relacionados ao projeto

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Installer / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

### <a name="a-namemt1001mt1001-could-not-find-an-application-at-the-specified-directory"></a><a name="MT1001"/>Não foi possível MT1001 localizar um aplicativo no diretório especificado

### <a name="a-namemt1002mt1002-could-not-create-symlinks-files-were-copied"></a><a name="MT1002"/>Não foi possível MT1002 criar links simbólicos, os arquivos foram copiados

### <a name="a-namemt1003mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a><a name="MT1003"/>Não MT1003 pode eliminar o aplicativo ' *'. Você precisará interromper o aplicativo manualmente.

### <a name="a-namemt1004mt1004-could-not-get-the-list-of-installed-applications"></a><a name="MT1004"/>Não MT1004 foi possível obter a lista de aplicativos instalados.

### <a name="a-namemt1005mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a><a name="MT1005"/>Não MT1005 pode eliminar o aplicativo '\*'no dispositivo'\*': *-talvez você precise interromper o aplicativo manualmente.

### <a name="a-namemt1006mt1006-could-not-install-the-application--on-the-device--"></a><a name="MT1006"/>Não foi possível MT1006 instalar o aplicativo '\*'no dispositivo'\*': *.

### <a name="a-namemt1007mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a><a name="MT1007"/>MT1007 Falha ao iniciar o aplicativo '\*'no dispositivo'\*': *. Você ainda pode iniciar o aplicativo manualmente tocando nele.

### <a name="a-namemt1008mt1008-failed-to-launch-the-simulator"></a><a name="MT1008"/>MT1008: Falha ao iniciar o simulador

Esse erro será relatado se mtouch Falha ao iniciar o simulador.   Isso pode acontecer, às vezes, porque já existe um processo de simulador obsoletos ou inativo em execução.

O seguinte comando emitido na linha de comando Unix pode ser usado para interromper processos simulador presos:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

### <a name="a-namemt1009mt1009-could-not-copy-the-assembly--to--"></a><a name="MT1009"/>Não foi possível MT1009 copie o assembly '\*'para'\*': *

Esse é um problema conhecido em determinadas versões do xamarin.

Se isso ocorrer para você, tente a seguinte solução alternativa:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

No entanto, já que esse problema foi resolvido na versão mais recente do xamarin, envie um novo bug em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com as informações de versão completa e a saída de log de compilação.

### <a name="a-namemt1010mt1010-could-not-load-the-assembly--"></a><a name="MT1010"/>Não foi possível MT1010 carregar o assembly ' *': *

### <a name="a-namemt1011mt1011-could-not-add-missing-resource-file-"></a><a name="MT1011"/>Não MT1011 foi possível adicionar o arquivo de recurso ausente: ' *'

### <a name="a-namemt1012mt1012-failed-to-list-the-apps-on-the-device--"></a><a name="MT1012"/>MT1012 Falha ao listar os aplicativos no dispositivo ' *': *

### <a name="a-namemt1013mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a><a name="MT1013"/>Erro de controle de dependência de MT1013: nenhum arquivo a ser comparado. Envie um relatório de erros em http://bugzilla.xamarin.com com um caso de teste.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caes de teste.

### <a name="a-namemt1014mt1014-failed-to-re-use-cached-version-of--"></a><a name="MT1014"/>MT1014 não conseguiu reutilizar uma versão armazenada em cache de ' *': *.

### <a name="a-namemt1015mt1015--failed-to-create-the-executable--"></a><a name="MT1015"/>MT1015 Falha ao criar o executável ' *': *

### <a name="a-namemt1015mt1015--failed-to-create-the-executable--"></a><a name="MT1015"/>MT1015 Falha ao criar o executável ' *': *

### <a name="a-namemt1016mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a><a name="MT1016"/>MT1016: Falha ao criar o arquivo de aviso porque já existe um diretório com o mesmo nome.

Remover o diretório `NOTICE` do projeto.

### <a name="a-namemt1017mt1017-failed-to-create-the-notice-file-"></a><a name="MT1017"/>MT1017: Falha ao criar o arquivo de aviso: *.

### <a name="a-namemt1018mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a><a name="MT1018"/>MT1018: Seu aplicativo falharam nas verificações de assinatura de código e não pôde ser instalado no dispositivo ' *'. Verifique os certificados, perfis, o provisionamento e ids de pacote. Provavelmente o seu dispositivo não é parte do perfil de provisionamento selecionado (erro: 0xe8008015).
### <a name="a-namemt1019mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a><a name="MT1019"/>MT1019: Seu aplicativo tenha direitos de seu perfil de provisionamento atual não tem suportados e não pôde ser instalado no dispositivo ' *'. Verifique o Log de dispositivo do iOS para obter informações mais detalhadas (erro: 0xe8008016).

Isso pode ocorrer se:

* Seu aplicativo tem direitos que o perfil de provisionamento atual não oferece suporte.
  Soluções possíveis:
  - Especifique um perfil de provisionamento diferente que suporta os direitos necessidades do seu aplicativo.
  - Remova os direitos que não tem suportados no perfil de provisionamento atual.
* O dispositivo que você está tentando implantar não está incluído no perfil de provisionamento, você está usando.
  Soluções possíveis:
  - Criar um novo aplicativo de um modelo no Xcode, selecione o mesmo perfil de provisionamento e implantar no mesmo dispositivo. Às vezes Xcode pode atualizar automaticamente os perfis de provisionamento com novos dispositivos (em outros casos que Xcode pedirá a você o que fazer).
  -Vá para o Centro de desenvolvimento do iOS e atualizar o perfil de provisionamento com o novo dispositivo e baixe o perfil de provisionamento atualizado no seu computador.

Na maioria dos casos que serão mostradas para obter mais informações sobre a falha do registro de dispositivo iOS, que pode ajudar a diagnosticar o problema.

### <a name="a-namemt1020mt1020-failed-to-launch-the-application--on-the-device--"></a><a name="MT1020"/>MT1020: Falha ao iniciar o aplicativo '\*'no dispositivo'\*': *

### <a name="a-namemt1021mt1021-could-not-copy-the-file--to--2"></a><a name="MT1021"/>MT1021: Não foi possível copiar o arquivo '\*'para'\*': 2}

Não foi possível copiar um arquivo. A mensagem de erro da operação de cópia tem mais informações sobre o erro.

### <a name="a-namemt1022mt1022-could-not-copy-the-directory--to--2"></a><a name="MT1022"/>MT1022: Não foi possível copiar o diretório '\*'para'\*': 2}

Não foi possível copiar um diretório. A mensagem de erro da operação de cópia tem mais informações sobre o erro.

### <a name="a-namemt1023mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a><a name="MT1023"/>MT1023: Não foi possível se comunicar com o dispositivo para localizar o aplicativo ' *': *

Ocorreu um erro durante a tentativa de consultar um aplicativo no dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.

### <a name="a-namemt1024mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a><a name="MT1024"/>MT1024: A assinatura do aplicativo não pôde ser verificada no dispositivo ' *'. Certifique-se de que o perfil de provisionamento está instalado e não expirou (erro: 0xe8008017).

O dispositivo rejeitou a instalação do aplicativo porque a assinatura não pôde ser verificada.

Certifique-se de que o perfil de provisionamento está instalado e não expirou.

### <a name="a-namemt1025mt1025-could-not-list-the-crash-reports-on-the-device-"></a><a name="MT1025"/>MT1025: Não foi possível listar os relatórios de falha no dispositivo *.

Ocorreu um erro ao tentar listar os relatórios de falha no dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1026mt1026-could-not-download-the-crash-report--from-the-device-"></a><a name="MT1026"/>MT1026: Não foi possível baixar o relatório de falhas * do dispositivo *.

Ocorreu um erro ao tentar baixar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1027mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a><a name="MT1027"/>MT1027: Não é possível usar o Xcode 7 + para iniciar aplicativos em dispositivos com iOS * (Xcode 7 só dá suporte a iOS 6 e posteriores).

Não é possível usar o Xcode 7 + para iniciar aplicativos em dispositivos com iOS versão abaixo 6.0.

Use uma versão mais antiga do Xcode, ou toque no aplicativo manualmente para iniciá-lo.

### <a name="a-namemt1028mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a><a name="MT1028"/>MT1028: Especificação de dispositivo inválido: ' *'. Esperado 'ios', 'watchos' ou 'all'.

A especificação do dispositivo passado usando – dispositivo é inválido. Os valores válidos são: 'ios', 'watchos' ou 'all'.

### <a name="a-namemt1029mt1029-could-not-find-an-application-at-the-specified-directory-"></a><a name="MT1029"/>MT1029: Não foi possível encontrar um aplicativo no diretório especificado: *

O caminho do aplicativo transmitido para – launchdev não existe. Especifique um pacote de aplicativo válida.

### <a name="a-namemt1030mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a><a name="MT1030"/>MT1030: Inicialização de aplicativos no dispositivo usando um identificador de pacote foi preterido. Passe o caminho completo para o pacote para iniciar.

É recomendável para passar o caminho para o aplicativo ser iniciado no dispositivo em vez de apenas a id do pacote.

### <a name="a-namemt1031mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a><a name="MT1031"/>MT1031: Não foi possível iniciar o aplicativo '\*'no dispositivo'\*' porque o dispositivo está bloqueado. . Desbloquear o dispositivo e tente novamente.

. Desbloquear o dispositivo e tente novamente.

### <a name="a-namemt1032mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a><a name="MT1032"/>MT1032: O executável de aplicativo pode ser muito grande (* MB) a ser executado no dispositivo. Se bitcode foi habilitada deseja desabilitá-lo para o desenvolvimento, só é necessário para enviar solicitações para a Apple.

### <a name="a-namemt1033mt1033-could-not-uninstall-the-application--from-the-device--"></a><a name="MT1033"/>MT1033: Não foi possível desinstalar o aplicativo '\*'do dispositivo'\*': *

<!-- 1034 used by mmp -->

### <a name="a-namemt1035mt1035-cannot-include-different-versions-of-the-framework-name"></a><a name="MT1035"/>MT1035: Não é possível incluir diferentes versões do framework '{name}'

Não é possível vincular com versões diferentes da mesma estrutura.

Isso geralmente acontece quando uma extensão faz referência a uma versão diferente de uma estrutura que o aplicativo de contêiner (possivelmente por meio de um conjunto de associação de terceiros).

Após esse erro será vários [MT1036](#MT1036) erros listando os caminhos para cada estrutura diferente.

### <a name="a-namemt1036mt1036-framework-name-included-from-path-related-to-previous-error"></a><a name="MT1036"/>MT1036: Estrutura '{name}' incluída no: {caminho} (relacionado ao erro anterior)

Esse erro é relatado apenas junto com [MT1036](#MT1036). Consulte [MT1036](#MT1036) para obter mais informações.

### <a name="mt11xx-debug-service"></a>MT11xx: Depurar o serviço

<!--
  MT11xx DebugService.cs
  -->

### <a name="a-namemt1101mt1101-could-not-start-app"></a><a name="MT1101"/>Não foi possível MT1101 Iniciar aplicativo

### <a name="a-namemt1102mt1102-could-not-attach-to-the-app-to-kill-it-"></a><a name="MT1102"/>Não foi possível MT1102 anexa ao aplicativo (para eliminá-lo): *

### <a name="a-namemt1103mt1103-could-not-detach"></a><a name="MT1103"/>Não foi possível MT1103 desanexar

### <a name="a-namemt1104mt1104-failed-to-send-packet-"></a><a name="MT1104"/>MT1104 falhou ao enviar o pacote: *

### <a name="a-namemt1105mt1105-unexpected-response-type"></a><a name="MT1105"/>Tipo de resposta MT1105 inesperado

### <a name="a-namemt1106mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a><a name="MT1106"/>Não foi possível MT1106 obter a lista de aplicativos no dispositivo: tempo limite da solicitação.

### <a name="a-namemt1107mt1107-application-failed-to-launch-"></a><a name="MT1107"/>MT1107: Falha ao iniciar o aplicativo: *

Verifique se o dispositivo estiver bloqueado.

Se você estiver implantando um aplicativo corporativo ou usando um perfil de provisionamento livre, você pode ter confiança de desenvolvedor (Isso é explicado <a href="http://stackoverflow.com/a/30726375/183422">aqui</a>).

### <a name="a-namemt1108mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a><a name="MT1108"/>MT1108: Não foi possível encontrar as ferramentas de desenvolvedor para este dispositivo XX (AA).

Algumas operações de mtouch exigem o <tt>DeveloperDiskImage.dmg</tt> arquivo esteja presente.   Esse arquivo faz parte do Xcode e normalmente está localizado em relação ao SDK que você está usando para criar, além de <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>.

Esse erro pode ocorrer em porque você não tem um DeveloperDiskImage.dmg que corresponde ao dispositivo que você se conectou.

### <a name="a-namemt1109mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a><a name="MT1109"/>MT1109: Aplicativo falha ao iniciar porque o dispositivo está bloqueado. . Desbloquear o dispositivo e tente novamente.

Verifique se o dispositivo estiver bloqueado.

### <a name="a-namemt1110mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a><a name="MT1110"/>MT1110: Falha do aplicativo ao iniciar devido a restrições de segurança do iOS. Certifique-se de que o desenvolvedor é confiável.

Se você estiver implantando um aplicativo corporativo ou usando um perfil de provisionamento livre, você pode ter confiança de desenvolvedor (Isso é explicado <a href="http://stackoverflow.com/a/30726375/183422">aqui</a>).

### <a name="a-namemt1111mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a><a name="MT1111"/>MT1111: O aplicativo iniciado com êxito, mas não é possível aguardar o aplicativo Sair conforme solicitado porque não é possível detectar o encerramento do aplicativo ao iniciar usando gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: simulador

<!--
  MT12xx simcontroller.cs
  -->

### <a name="a-namemt1201mt1201-could-not-load-the-simulator-"></a><a name="MT1201"/>MT1201: Não foi possível carregar o simulador: *
### <a name="a-namemt1202mt1202-invalid-simulator-configuration-"></a><a name="MT1202"/>MT1202: Configuração de simulador inválido: *
### <a name="a-namemt1203mt1203-invalid-simulator-specification-"></a><a name="MT1203"/>MT1203: Especificação de simulador inválido: *
### <a name="a-namemt1204mt1204-invalid-simulator-specification--runtime-not-specified"></a><a name="MT1204"/>MT1204: Especificação de simulador inválido ' *': tempo de execução não especificado.
### <a name="a-namemt1205mt1205-invalid-simulator-specification--device-type-not-specified"></a><a name="MT1205"/>MT1205: Especificação de simulador inválido ' *': tipo de dispositivo não especificado.
### <a name="a-namemt1206mt1206-could-not-find-the-simulator-runtime-"></a><a name="MT1206"/>MT1206: Não foi possível encontrar o tempo de execução do simulador ' *'.
### <a name="a-namemt1207mt1207-could-not-find-the-simulator-device-type-"></a><a name="MT1207"/>MT1207: Não foi possível encontrar o tipo de dispositivo de simulador ' *'.
### <a name="a-namemt1208mt1208-could-not-find-the-simulator-runtime-"></a><a name="MT1208"/>MT1208: Não foi possível encontrar o tempo de execução do simulador ' *'.
### <a name="a-namemt1209mt1209-could-not-find-the-simulator-device-type-"></a><a name="MT1209"/>MT1209: Não foi possível encontrar o tipo de dispositivo de simulador ' *'.
### <a name="a-namemt1210mt1210-invalid-simulator-specification--unknown-key-"></a><a name="MT1210"/>MT1210: Especificação de simulador inválido: \*, chave desconhecida '\*'
### <a name="a-namemt1211mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a><a name="MT1211"/>MT1211: A versão do simulador '\*'não oferece suporte para o tipo de simulador'\*'
### <a name="a-namemt1212mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a><a name="MT1212"/>MT1212: Falha ao criar uma versão de simulador onde digitar = * e tempo de execução = *.
### <a name="a-namemt1213mt1213-invalid-simulator-specification-for-xcode-4-"></a><a name="MT1213"/>MT1213: Especificação de simulador inválido para o Xcode 4: *
### <a name="a-namemt1214mt1214-invalid-simulator-specification-for-xcode-5-"></a><a name="MT1214"/>MT1214: Especificação de simulador inválido para o Xcode 5: *
### <a name="a-namemt1215mt1215-invalid-sdk-specified-"></a><a name="MT1215"/>MT1215: SDK inválido especificado: *
### <a name="a-namemt1216mt1216-could-not-find-the-simulator-udid-"></a><a name="MT1216"/>MT1216: Não foi possível encontrar o simulador UDID ' *'.
### <a name="a-namemt1217mt1217-could-not-load-the-app-bundle-at-"></a><a name="MT1217"/>MT1217: Não foi possível carregar o pacote de aplicativos em ' *'.
### <a name="a-namemt1218mt1218-no-bundle-identifier-found-in-the-app-at-"></a><a name="MT1218"/>MT1218: Nenhum identificador de pacote encontrado no aplicativo no ' *'.
### <a name="a-namemt1219mt1219-could-not-find-the-simulator-for-"></a><a name="MT1219"/>MT1219: Não foi possível encontrar o simulador para ' *'.
### <a name="a-namemt1220mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a><a name="MT1220"/>MT1220: Não foi possível encontrar o tempo de execução mais recente do simulador para dispositivo ' *'.

Isso geralmente indica um problema com o Xcode.

Coisas para tentar corrigir isso:

* Use o simulador de uma vez no Xcode.
* Passar uma versão do SDK explícita usando o sdk - <version>.
* Reinstale o Xcode.

### <a name="a-namemt1221mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a><a name="MT1221"/>MT1221: Não foi possível encontrar o simulador de iPhone emparelhada do simulador WatchOS ' *'.

Ao iniciar um aplicativo WatchOS em um simulador WatchOS, deve haver um par simulador de iOS também.

Assista simuladores podem ser emparelhadas com iOS simuladores usando a interface de usuário do Xcode dispositivos (dispositivos -> menu Janela).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

### <a name="a-namemt1301mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a><a name="MT1301"/>Biblioteca nativa de MT1301 `*` (\*) foi ignorada, pois ela não corresponde a architecture(s) de compilação atual (\*)

### <a name="a-namemt1302mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a><a name="MT1302"/>Não MT1302 foi possível extrair a biblioteca nativa ' *' de '+'. Verifique se que a biblioteca nativa foi inserida corretamente no assembly gerenciado (se o assembly foi compilado usando um projeto de associação, a biblioteca nativa deve ser incluída no projeto e sua ação de compilação deve ser 'ObjcBindingNativeLibrary').

### <a name="a-namemt1303mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a><a name="MT1303"/>MT1303: Não foi possível descompactar o framework nativo '\*'de'\*'. Examine o log de compilação para obter mais informações do comando nativo 'zip'.

Não foi possível descompactar o framework nativo especificado da biblioteca de ligação.

Examine o log de bulid para obter mais informações sobre a falha do comando nativo 'zip'.

### <a name="a-namemt1304mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a><a name="MT1304"/>MT1304: O framework incorporado ' *' em * é inválido: ele não contém um Info. plist.

A estrutura inserida especificada não contém um Info. plist e, portanto, não é uma estrutura válida.

Verifique se que a estrutura não é válida.

### <a name="a-namemt1305mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a><a name="MT1305"/>MT1305: A biblioteca de ligação '\*' contém uma estrutura de usuário (\*), mas as estruturas de usuário inserido requerem iOS 8.0 (o destino de implantação atual é *). Defina o destino de implantação no arquivo Info. plist para pelo menos 8.0.

A biblioteca de associação especificada contém uma estrutura inserida, mas xamarin só oferece suporte a estruturas inseridas no iOS 8.0 ou posterior.

Defina o destino de implantação no arquivo Info. plist para pelo menos 8.0 para solucionar esse erro (ou não use estruturas inseridas).

### <a name="mt14xx-crash-reports"></a>MT14xx: Relatórios de falha

<!--
  MT14xx    CrashReports.cs
  -->

### <a name="a-namemt1400mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a><a name="MT1400"/>MT1400: Não foi possível abrir o serviço de relatório de falhas: AFCConnectionOpen retornado *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1401mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a><a name="MT1401"/>MT1401: Não foi possível fechar o serviço de relatório de falhas: AFCConnectionClose retornado *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1402mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a><a name="MT1402"/>MT1402: Não foi possível ler informações do arquivo para *: AFCFileInfoOpen retornado *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1403mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a><a name="MT1403"/>MT1403: Não foi possível ler o relatório de falhas: AFCDirectoryOpen (*) retornado: *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1404mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a><a name="MT1404"/>MT1404: Não foi possível ler o relatório de falhas: AFCFileRefOpen (*) retornado: *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1405mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a><a name="MT1405"/>MT1405: Não foi possível ler o relatório de falhas: AFCFileRefRead (*) retornado: *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

### <a name="a-namemt1406mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a><a name="MT1406"/>MT1406: Não foi possível listar os relatórios de falha: AFCDirectoryOpen (*) retornado: *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

* Excluir o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com iTunes (Isso removerá os relatórios de falhas do dispositivo).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

### <a name="a-namemt1600mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a><a name="MT1600"/>MT1600: Não uma Mach-O biblioteca dinâmica (cabeçalho desconhecido '0 x *'): *.

Ocorreu um erro ao processar a biblioteca dinâmica em questão.

Verifique se que a biblioteca dinâmica é uma biblioteca de dinâmico válida do Mach-O.

O formato de uma biblioteca pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/library.dylib

### <a name="a-namemt1601mt1601-not-a-static-library-unknown-header--"></a><a name="MT1601"/>MT1601: Não uma biblioteca estática (cabeçalho desconhecido ' *'): *.

Ocorreu um erro ao processar a biblioteca estática em questão.

Verifique se que a biblioteca estática é uma biblioteca estática de Mach-O válido.

O formato de uma biblioteca pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/library.a

### <a name="a-namemt1602mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a><a name="MT1602"/>MT1602: Não uma Mach-O biblioteca dinâmica (cabeçalho desconhecido '0 x *'): *.

Ocorreu um erro ao processar a biblioteca dinâmica em questão.

Verifique se que a biblioteca dinâmica é uma biblioteca de dinâmico válida do Mach-O.

O formato de uma biblioteca pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/library.dylib

### <a name="a-namemt1603mt1603-unknown-format-for-fat-entry-at-position--in-"></a><a name="MT1603"/>MT1603: Formato desconhecido entrada fat na posição * em *.

Ocorreu um erro ao processar o arquivo fat em questão.

Verifique se que o arquivo fat é válido.

O formato de um arquivo fat pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/file

### <a name="a-namemt1604mt1604-file-of-type--is-not-a-macho-file-"></a><a name="MT1604"/>MT1604: O arquivo do tipo * não é um arquivo MachO (*).

Ocorreu um erro ao processar o arquivo MachO em questão.

Verifique se que o arquivo é uma biblioteca de dinâmico válida Mach-O.

O formato de um arquivo pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Mensagens de erro de vinculador

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

### <a name="a-namemt2001mt2001-could-not-link-assemblies"></a><a name="MT2001"/>Não foi MT2001 vincular assemblies

Esse erro significa que o vinculador gerenciado encontrou um erro inesperado, por exemplo, uma exceção e não foi possível concluir a ou salve o assembly que está sendo processado. Para obter mais informações sobre o erro exato fará parte do log de compilação, por exemplo

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

É importante para o relatório de erro para esses problemas. Na maioria dos casos para solucionar esse problema pode ser fornecido até que uma correção apropriada seja publicada. As informações acima são essenciais (junto com um caso de teste e/ou o assembly binairy) resolver o problema.

### <a name="a-namemt2002mt2002-can-not-resolve-reference-"></a><a name="MT2002"/>Não MT2002 pode resolver a referência: *

### <a name="a-namemt2003mt2003-option--will-be-ignored-since-linking-is-disabled"></a><a name="MT2003"/>Opção MT2003 ' *' serão ignoradas porque vinculação está desabilitada

### <a name="a-namemt2004mt2004-extra-linker-definitions-file--could-not-be-located"></a><a name="MT2004"/>Arquivo de definições de vinculador MT2004 Extra ' *' não pôde ser localizado.

### <a name="a-namemt2005mt2005-definitions-from--could-not-be-parsed"></a><a name="MT2005"/>Definições de MT2005 de ' *' não pôde ser analisado.

### <a name="a-namemt2006mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a><a name="MT2006"/>MT2006: Não é possível carregar o mscorlib.dll de: *. Reinstale o xamarin.

Isso geralmente indica que há um problema com a instalação do xamarin. Tente reinstalar o xamarin.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

### <a name="a-namemt2010mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a><a name="MT2010"/>MT2010: HttpMessageHandler desconhecido `*`. Os valores válidos são HttpClientHandler (padrão), CFNetworkHandler ou NSUrlSessionHandler

### <a name="a-namemt2011mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a><a name="MT2011"/>MT2011: TlsProvider desconhecido `*`.  Os valores válidos são default ou appletls.

O valor fornecido para `tls-provider=` não é um provedor válido do TLS (Transport Layer Security).

O `default` e `appletls` são os únicos valores válidos e ambos representam a mesma opção, que é fornecer suporte a SSL/TLS usando a API de TLS Apple nativo.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

### <a name="a-namemt2015mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a><a name="MT2015"/>MT2015: HttpMessageHandler inválido `*` para watchOS. O único valor válido é NSUrlSessionHandler.

Este é um aviso que ocorre porque o arquivo de projeto especifica um HttpMessageHandler inválido.

Versões anteriores das ferramentas de nossa visualização gerada por padrão um valor inválido no arquivo de projeto.

Para corrigir esse aviso, abra o arquivo de projeto em um editor de texto e remova todos os nós de HttpMessageHandler do XML.

### <a name="a-namemt2016mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a><a name="MT2016"/>MT2016: TlsProvider inválido `legacy` opção. O único valor válido `appletls` será usado.

O `legacy` provedor, que era uma SSLv3 totalmente gerenciado / TLSv1 único provedor, não é fornecido com o xamarin mais. Projetos que estavam usando esse provedor antigo e agora é compilado com o mais recente `appletls` um.

Para corrigir esse aviso, abra o arquivo de projeto em um editor de texto e remova todos os ' MtouchTlsProvider ' nós de XML.

### <a name="a-namemt2017mt2017-could-not-process-xml-description"></a><a name="MT2017"/>MT2017: Não foi possível processar a descrição do XML.

Isso significa que há um erro no [arquivo de configuração de vinculador XML](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/) fornecida, examine o arquivo.

### <a name="a-namemt2018mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a><a name="MT2018"/>MT2018: O assembly '\*' é referenciado em dois locais diferentes: '\*' e ' *'.

O assembly mencionado na mensagem de erro é carregado de vários locais. Certifique-se sempre usar a mesma versão de um assembly.

### <a name="a-namemt2019mt2019-can-not-load-the-root-assembly-"></a><a name="MT2019"/>MT2019: Não é possível carregar o assembly de raiz ' *'

Não foi possível carregar o assembly de raiz. Verifique se que o caminho na mensagem de erro se refere a um arquivo existente, e que se trata de um assembly .NET válido.

### <a name="a-namemt202xmt202x-binding-optimizer-failed-processing-"></a><a name="MT202x"/>MT202x: Falha de associação otimizador processamento `...`.

Algo inesperado ocorreu ao tentar otimizar gerado código de associação. O elemento que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema, o assembly chamado (ou que contém o tipo ou método chamado) precisará ser fornecido em um [relatório de bugs](http://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **mtouch adicional argumentos**).

O último dígito `x` será:
* `0` para um nome de assembly;
* `1` para um nome de tipo.
* `3` para um nome de método.

### <a name="a-namemt2030mt2030-remove-user-resources-failed-processing-"></a><a name="MT2030"/>MT2030: Remover recursos do usuário falha processamento `...`.

Algo inesperado ocorreu ao tentar remover recursos do usuário. O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](http://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

Recursos do usuário são incluídos em assemblies (como recursos) que precisa ser extraído no momento da compilação, ao criar o pacote de aplicativo de arquivos. Isso inclui:

* `__monotouch_content_*` e `__monotouch_pages_*` recursos; e
* Bibliotecas nativas incorporado dentro de um assembly de associação.

### <a name="a-namemt2040mt2040-default-httpmessagehandler-setter-failed-processing-"></a><a name="MT2040"/>MT2040: HttpMessageHandler padrão setter não pôde processar `...`.

Algo inesperado ocorreu ao tentar definir o padrão `HttpMessageHandler` para o aplicativo. Envie um [relatório de bugs](http://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2050mt2050-code-remover-failed-processing-"></a><a name="MT2050"/>MT2050: Falha de código removedor processamento `...`.

Algo inesperado ocorreu ao tentar remover o código de BCL com o aplicativo de envio. Envie um [relatório de bugs](http://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2060mt2060-sealer-failed-processing-"></a><a name="MT2060"/>MT2060: Sealer falha processamento `...`.

Algo inesperado ocorreu ao tentar lacrar tipos ou métodos (final) ou quando devirtualizing alguns métodos. O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](http://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2070mt2070-metadata-reducer-failed-processing-"></a><a name="MT2070"/>MT2070: Falha de metadados Redutor processamento `...`.

Algo inesperado ocorreu ao tentar reduzir os metadados do aplicativo. O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](http://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2080mt2080-marknsobjects-failed-processing-"></a><a name="MT2080"/>MT2080: MarkNSObjects falha processamento `...`.

Algo inesperado ocorreu ao tentar marcar `NSObject` subclasses do aplicativo. O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](http://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2090mt2090-inliner-failed-processing-"></a><a name="MT2090"/>MT2090: Inliner falha processamento `...`.

Algo inesperado ocorreu ao tentar o código embutido do aplicativo. O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](https://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

### <a name="a-namemt2100mt2100-smart-enum-conversion-preserver-failed-processing-"></a><a name="MT2100"/>MT2100: Falha de preservar inteligente de conversão de Enum processamento `...`.

Algo inesperado ocorreu ao tentar marcar os métodos de conversão para enums inteligente do aplicativo. O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](https://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2101mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a><a name="MT2101"/>MT2101: Não é possível resolver a referência '\*', referenciado do método'\*' em ' *'.

Uma referência de assembly inválido foi encontrada ao processar o método mencionado na mensagem de erro.

O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](https://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2102mt2102-error-processing-the-method--in-the-assembly--"></a><a name="MT2102"/>MT2102: Erro ao processar o método '\*'no assembly'\*': *

Algo inesperado ocorreu ao tentar marcar o método mencionado na mensagem de erro.

O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](https://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2103mt2103-error-processing-assembly--"></a><a name="MT2103"/>MT2103: Assembly de processamento de erro '\*': *

Um erro inesperado ocorreu durante o processamento de um assembly.

O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](https://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

### <a name="a-namemt2104mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a><a name="MT2104"/>MM2104: Não é possível vincular o assembly '{0}' porque é o modo misto.

Assemblies de modo misto não podem ser processados pelo vinculador.

Consulte https://msdn.microsoft.com/en-us/library/x0w2664k.aspx para obter mais informações sobre assemblies de modo misto.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Mensagens de erro AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

### <a name="a-namemt3001mt3001-could-not-aot-the-assembly-"></a><a name="MT3001"/>MT3001 foi AOT o assembly ' *'

Isso geralmente indica um bug no compilador AOT. Registre o erro [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um projeto que pode ser usado para reproduzir o erro.

Às vezes, é possível contornar isso desabilitando compilações incrementais no iOS do projeto, a opção de compilação (mas ainda é um bug, então informe-lo assim mesmo).

### <a name="a-namemt3002mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a><a name="MT3002"/>Restrição MT3002 AOT: método ' *' deve ser estático, pois ela está decorada com [MonoPInvokeCallback]. Consulte [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

Essa mensagem de erro vem do compilador AOT.

### <a name="a-namemt3003mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a><a name="MT3003"/>MT3003 Conflitante – debug e – llvm opções. Depuração de software está desabilitado.

Não há suporte para a depuração quando LLVM está habilitado. Se você precisar depurar o aplicativo, desabilite LLVM primeiro.

### <a name="a-namemt3004mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a><a name="MT3004"/>MT3004 foi AOT o assembly ' *' porque ele não existe.

### <a name="a-namemt3005mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a><a name="MT3005"/>MT3005 a dependência '\*'do assembly'\*' não foi encontrado. Verifique as referências do projeto.

Isso normalmente ocorre quando um assembly faz referência a outra versão de um assembly de plataforma (geralmente a versão 4 do .NET de mscorlib. dll).

Isso não é suportado e talvez não compilar ou executar corretamente (assembly pode usar o API da versão 4 do .NET do mscorlib.dll não tem a versão do xamarin).

### <a name="a-namemt3006mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a><a name="MT3006"/>Um mapa de dependência completa para o projeto não pôde MT3006 de computação. Isso resulta em tempos de compilação mais lentos porque xamarin não conseguir detectar o que precisa ser recriado (e o que não precisa ser recriado). Examine os avisos anteriores para obter mais detalhes.

 Criar ou executar corretamente (assembly pode usar o API da versão 4 do .NET do mscorlib.dll não tem a versão do xamarin).

### <a name="a-namemt3007mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a><a name="MT3007"/>MT3007: Arquivos de informações de depuração (*. mdb) não serão carregados quando llvm está habilitado.

### <a name="a-namemt3008mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a><a name="MT3008"/>MT3008: Bitcode suporte requer o uso de back-end LLVM AOT (-llvm)

Suporte de Bitcode requer o uso de back-end LLVM AOT (-llvm).

Desabilite o suporte de Bitcode ou habilitar LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Mensagens de erro de geração de código

### <a name="mt40xx-main"></a>MT40xx: Main

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

### <a name="a-namemt4001mt4001-the-main-template-could-not-be-expanded-to-"></a><a name="MT4001"/>MT4001 Modelo principal não pode ser expandido para `*`.

Ocorreu um erro ao gerar main.m. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4002mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4002"/>MT4002 falhou ao compilar o código gerado para os métodos P/Invoke. Envie um relatório de erros em http://bugzilla.xamarin.com

Falha ao compilar o código gerado para os métodos P/Invoke. Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt41xx-registrar"></a>MT41xx: Registrar

<!--
  MT41xx registrar.m
  -->

### <a name="a-namemt4101mt4101-the-registrar-cannot-build-a-signature-for-type-"></a><a name="MT4101"/>MT4101 o registrador não é possível criar uma assinatura para o tipo `*`.

Foi encontrado um tipo na API exportado que o tempo de execução não sabe como realizar marshaling de objetivo-C.

Se você acredita que xamarin deve oferecer suporte para o tipo em questão, envie uma solicitação de aprimoramento no [http://bugzilla.xamarin.com](http://bugzilla.xamarin.com).

### <a name="a-namemt4102mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a><a name="MT4102"/>MT4102 o registrador encontrado um tipo inválido `*` na assinatura para o método `*`. Use `*` em seu lugar.

Isso atualmente só acontece com um tipo: System. DateTime. Use o equivalente Objective-C (NSDate).

### <a name="a-namemt4103mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a><a name="MT4103"/>MT4103 o registrador encontrado um tipo inválido `*` na assinatura para o método `*`: O tipo implementa INativeObject, mas não tem um construtor que aceita dois (IntPtr, bool) argumentos

Isso ocorre quando o registrador de encontrar um tipo em uma assinatura com as características mencionadas. O registrador talvez seja necessário criar novas instâncias do tipo, e nesse caso ele requer um construtor com (IntPtr, bool) assinatura - o primeiro argumento (IntPtr) Especifica o identificador gerenciado, enquanto o segundo se o chamador passa pela propriedade de nativo tratar (se esse valor for false 'reter' será chamado no objeto).

### <a name="a-namemt4104mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a><a name="MT4104"/>MT4104 o registrador não é possível empacotar o valor de retorno para o tipo `*` na assinatura para o método `*`.

Foi encontrado um tipo na API exportado que o tempo de execução não sabe como realizar marshaling de objetivo-C.

Se você acredita que xamarin deve oferecer suporte para o tipo em questão, envie uma solicitação de aprimoramento no [http://bugzilla.xamarin.com](http://bugzilla.xamarin.com).

### <a name="a-namemt4105mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a><a name="MT4105"/>MT4105 o registrador não é possível empacotar o parâmetro do tipo `*` na assinatura para o método `*`.

Se você acredita que xamarin deve oferecer suporte para o tipo em questão, envie uma solicitação de aprimoramento no [http://bugzilla.xamarin.com](http://bugzilla.xamarin.com).

### <a name="a-namemt4106mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a><a name="MT4106"/>MT4106 o registrador não é possível empacotar o valor de retorno para a estrutura `*` na assinatura para o método `*`.

Foi encontrado um tipo na API exportado que o tempo de execução não sabe como realizar marshaling de objetivo-C.

Se você acredita que xamarin deve oferecer suporte para o tipo em questão, envie uma solicitação de aprimoramento no [http://bugzilla.xamarin.com](http://bugzilla.xamarin.com).

### <a name="a-namemt4107mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a><a name="MT4107"/>MT4107 o registrador não é possível empacotar o parâmetro do tipo `*` na assinatura para o método `+`.

Foi encontrado um tipo na API exportado que o tempo de execução não sabe como realizar marshaling de objetivo-C.

Se você acredita que xamarin deve oferecer suporte para o tipo em questão, envie uma solicitação de aprimoramento no [http://bugzilla.xamarin.com](http://bugzilla.xamarin.com).

### <a name="a-namemt4108mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a><a name="MT4108"/>MT4108 o registrador não é possível obter o tipo de ObjectiveC para tipo gerenciado `*`.

Foi encontrado um tipo na API exportado que o tempo de execução não sabe como realizar marshaling de objetivo-C.

Se você acredita que xamarin deve oferecer suporte para o tipo em questão, envie uma solicitação de aprimoramento no [http://bugzilla.xamarin.com](http://bugzilla.xamarin.com).

### <a name="a-namemt4109mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4109"/>MT4109 falhou ao compilar o código gerado registrador. Envie um relatório de erros em http://bugzilla.xamarin.com

Falha ao compilar o código gerado para o registrador. O log de compilação conterá a saída do compilador nativo, explicando por que o código não está compilando.

Isso é sempre um bug no xamarin; Envie um relatório de erros em [http://bugzilla.xamarin.com](http://bugzilla.xamarin.com) com seu projeto ou um caso de teste.

### <a name="a-namemt4110mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a><a name="MT4110"/>MT4110 o registrador não pode realizar marshaling do parâmetro de saída do tipo `*` na assinatura para o método `*`.

### <a name="a-namemt4111mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a><a name="MT4111"/>MT4111 o registrador não é possível criar uma assinatura para o tipo `*` no método `*`.

### <a name="a-namemt4112mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a><a name="MT4112"/>MT4112 o registrador encontrado um tipo inválido `*`. Registrando tipos genéricos Objective-C não é suportado e pode causar comportamento aleatório e/ou falhas no (para versões anteriores compatibilidade com versões anteriores do xamarin é possível ignorar o erro, passando `--unsupported--enable-generics-in-registrar` como um mtouch adicional argumento na página de opções de compilação do projeto iOS. Consulte [developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar) para obter mais informações).

### <a name="a-namemt4113mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a><a name="MT4113"/>MT4113 Registrador de encontrar um método genérico: '\*.\*'. Exportar métodos genéricos não é suportado e levará a comportamento aleatório e/ou falhas.

### <a name="a-namemt4114mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4114"/>Erro inesperado de MT4114 no registrador para o método '\*.\*'-envie um relatório de erros em http://bugzilla.xamarin.com

### <a name="a-namemt4116mt4116-could-not-register-the-assembly--"></a><a name="MT4116"/>Não foi possível MT4116 registrar o assembly ' *': *

### <a name="a-namemt4117mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a><a name="MT4117"/>MT4117 o registrador encontrada uma incompatibilidade de assinatura no método '*.*'-o seletor indica o método usa * parâmetros, enquanto o método gerenciado tem * parâmetros.

### <a name="a-namemt4118mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a><a name="MT4118"/>MT4118 não é possível registrar a dois tipos gerenciados ('\*'e'\*') com o mesmo nome nativo ('* ').

### <a name="a-namemt4119mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a><a name="MT4119"/>Não MT4119 foi possível registrar o seletor '\*'do membro'\*. *' porque o Seletor já está registrado em outro membro.

### <a name="a-namemt4120mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4120"/>MT4120 o registrador encontrado um tipo de campo desconhecido '\*'no campo'\*. *'. Envie um relatório de erros em http://bugzilla.xamarin.com

Esse erro indica um bug no xamarin. Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4121mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a><a name="MT4121"/>MT4121 não é possível usar o GCC / G + + para compilar o código gerado no registrador estático de ao usar a estrutura de contas (os arquivos de cabeçalho fornecidos pela Apple usada durante a compilação exigem Clang). Use Clang (– compilador: clang) ou o registro dinâmico (-registrador: dinâmico).

### <a name="a-namemt4122mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a><a name="MT4122"/>MT4122 não é possível usar o compilador Clang fornecido no *.* SDK para compilar o código gerado no registrador estático quando não-ASCII de nomes de tipo ('* ') estão presentes no aplicativo. Use GCC / G + + (-compilador: gcc | g + +), o registro dinâmico (-registrador: dinâmico) ou um SDK mais recente.

### <a name="a-namemt4123mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a><a name="MT4123"/>MT4123 o tipo do parâmetro variadic na função variadic ' *' deve ser System. IntPtr.

### <a name="a-namemt4124mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4124"/>MT4124 inválido * encontrado em ' *'. Envie um relatório de erros em http://bugzilla.xamarin.com

Esse erro indica um bug no xamarin. Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4125mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a><a name="MT4125"/>MT4125 o registrador encontrado um tipo inválido '\*'na assinatura para o método'\*': A interface deve ter um atributo de protocolo especificar seu tipo de wrapper.

### <a name="a-namemt4126mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a><a name="MT4126"/>MT4126 não pode registrar os dois protocolos gerenciados ('\*'e'\*') com o mesmo nome nativo ('* ').

### <a name="a-namemt4127mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a><a name="MT4127"/>MT4127 não é possível registrar mais de um método de interface para o método '\*' (que está implementando '\*').

### <a name="a-namemt4128mt4128--the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a><a name="MT4128"/>MT4128 o registrador encontrado um tipo inválido de parâmetro genérico '\*'no método'\*'. O parâmetro genérico deve ter uma restrição 'NSObject'.

### <a name="a-namemt4129mt4129--the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a><a name="MT4129"/>MT4129 o registrador encontrado um tipo de retorno genérico inválido '\*'no método'\*'. O tipo de retorno genérico deve ter uma restrição 'NSObject'.

### <a name="a-namemt4130mt4130--the-registrar-cannot-export-static-methods-in-generic-classes-"></a><a name="MT4130"/>MT4130 o registrador não é possível exportar métodos estáticos em classes genéricas ('* ').

### <a name="a-namemt4131mt4131--the-registrar-cannot-export-static-properties-in-generic-classes-"></a><a name="MT4131"/>MT4131 o registrador não é possível exportar as propriedades estáticas em classes genéricas ('\*.\*').

### <a name="a-namemt4132mt4132--the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a><a name="MT4132"/>MT4132 o registrador encontrado um tipo de retorno genérico inválido '\*'na propriedade'\*'. O tipo de retorno deve ter uma restrição 'NSObject'.

### <a name="a-namemt4133mt4133--cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a><a name="MT4133"/>MT4133 não é possível construir uma instância do tipo ' *' de Objective-C porque o tipo é genérico. [Exceção de tempo de execução]

### <a name="a-namemt4134mt4134--your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a><a name="MT4134"/>MT4134 Seu aplicativo estiver usando o ' *' framework, que não está incluída no SDK que você está usando para criar seu aplicativo iOS (essa estrutura foi introduzida no iOS *, enquanto você estiver criando com o iOS * SDK.) Selecione um SDK mais recente no iOS do seu aplicativo opções de compilação.

### <a name="a-namemt4135mt4135--the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a><a name="MT4135"/>MT4135 o membro '\*.\*' tem um atributo de exportação que não especifica um seletor. Um seletor é necessário.

### <a name="a-namemt4136mt4136--the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a><a name="MT4136"/>MT4136 o registrador não é possível empacotar o tipo de parâmetro '\*'do parâmetro'\*'no método'\*. *'

<!-- MT4137 is unused -->

### <a name="a-namemt4138mt4138--the-registrar-cannot-marshal-the-property-type--of-the-property-"></a><a name="MT4138"/>MT4138 o registrador não é possível empacotar o tipo da propriedade '\*'da propriedade'\*. *'.

### <a name="a-namemt4139mt4139--the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a><a name="MT4139"/>MT4139 o registrador não é possível empacotar o tipo da propriedade '\*'da propriedade'\*. *'. Propriedades com o atributo [conectar] devem ter um tipo de propriedade de NSObject (ou uma subclasse de NSObject).

### <a name="a-namemt4140mt4140--the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a><a name="MT4140"/>MT4140 o registrador encontrada uma incompatibilidade de assinatura no método '*.*'-o seletor indica o método variadic usa * parâmetros, enquanto o método gerenciado tem * parâmetros.

### <a name="a-namemt4141mt4141--cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a><a name="MT4141"/>MT4141 não é possível registrar o seletor '\*'no membro'\*' porque o xamarin implicitamente registra este seletor.

Isso ocorre quando subclasses de um tipo de estrutura e tentando implementar uma 'Manter', 'versão' ou 'dealloc' método:

```csharp
class MyNSObject : NSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

É porém possível substituir esses métodos se a classe não for a primeira subclasse do framework digite:

```csharp
class MyNSObject : NSObject
{
}

class MyCustomNSObject : MyNSObject
{
    [Export ("retain")]
    new void Retain () {}

    [Export ("release")]
    new void Release () {}

    [Export ("dealloc")]
    new void Dealloc () {}
}
```

Nesse caso substituirão xamarin `retain`, `release` e `dealloc` no `MyNSObject` classe, e não há nenhum conflito.

### <a name="a-namemt4142mt4142-failed-to-register-the-type-"></a><a name="MT4142"/>MT4142: Falha ao registrar o tipo de ' *'.
### <a name="a-namemt4143mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a><a name="MT4143"/>MT4143: A classe de ObjectiveC ' *' não pôde ser registrado, ele parece não deriva de alguma classe ObjectiveC conhecido (incluindo NSObject).
### <a name="a-namemt4144mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4144"/>MT4144: Não é possível registrar o método ' *' porque ele não tem um trampoline associado. Envie um relatório de erros em http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4145mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a><a name="MT4145"/>MT4145: Enumeração inválido ' *': enums com o atributo [nativo] deve ter um tipo subjacente de enum 'ulong' ou 'long'.
### <a name="a-namemt4146mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a><a name="MT4146"/>MT4146: O parâmetro de nome do atributo da classe registrador\*' ('\*') contém um caractere inválido: '\*' (\*).

O nome de uma classe Objectice-C não pode conter espaços em branco, o que significa que o `Register` atributo da classe gerenciada correspondente não pode ter um `Name` parâmetro ou não pode conter espaço em branco.

Verifique se o `Register` atributo da classe gerenciada mencionado na mensagem de erro não contém qualquer espaço em branco.

### <a name="a-namemt4147mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a><a name="MT4147"/>MT4147: Detectado um protocolo herdando o protocolo JSExport ao usar o registro dinâmico. Não é possível exportar protocolos para JavaScriptCore dinamicamente; o registrador estático deve ser usado (Adicionar '-registrador: estática para os argumentos adicionais mtouch no iOS do projeto opções de compilação para selecionar o registrador estático).
### <a name="a-namemt4148mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a><a name="MT4148"/>MT4148: O registrador encontrado um protocolo genérico: ' *'. Não há suporte para a exportação de protocolos genéricos.
### <a name="a-namemt4149mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a><a name="MT4149"/>MT4149: Não é possível registrar o método '\*.\*' porque o tipo do primeiro parâmetro ('\*') não corresponde ao tipo de categoria ('\*').
### <a name="a-namemt4150mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a><a name="MT4150"/>MT4150: Não é possível registrar o tipo '\*' porque a propriedade de tipo ('\*') em sua categoria atributo não herda de NSObject.
### <a name="a-namemt4151mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a><a name="MT4151"/>MT4151: Não é possível registrar o tipo ' *' porque a propriedade de tipo em seu atributo de categoria não está definida.
### <a name="a-namemt4152mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a><a name="MT4152"/>MT4152: Não é possível registrar o tipo ' *' como uma categoria porque ele implementa as subclasses NSObject ou INativeObject.
### <a name="a-namemt4153mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a><a name="MT4153"/>MT4153: Não é possível registrar o tipo '\*' como uma categoria porque é genérico.
### <a name="a-namemt4154mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a><a name="MT4154"/>MT4154: Não é possível registrar o método '\*' como um método de categoria porque é genérico.
### <a name="a-namemt4155mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a><a name="MT4155"/>MT4155: Não é possível registrar o método '\*'com o seletor de'\*' como um método de categoria em ' *' porque o Objective-C já tem uma implementação para este seletor.
### <a name="a-namemt4156mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a><a name="MT4156"/>MT4156: Não é possível registrar duas categorias ('\*'e'\*') com o mesmo nome nativo ('* ').
### <a name="a-namemt4157mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a><a name="MT4157"/>MT4157: Não é possível registrar o método de categoria '\*' porque pelo menos um parâmetro é necessário (e seu tipo deve corresponder ao tipo de categoria '\*')
### <a name="a-namemt4158mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a><a name="MT4158"/>MT4158: Não é possível registrar o construtor * na categoria * porque não há suporte para construtores em categorias.
### <a name="a-namemt4159mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a><a name="MT4159"/>MT4159: Não é possível registrar o método ' *' como um método de categoria como métodos de categoria devem ser estáticos.

### <a name="a-namemt4160mt4160-invalid-character---found-in-selector--for-"></a><a name="MT4160"/>MT4160: Caractere inválido '\*' (\*) encontrada no seletor '\*'for'\*'.

### <a name="a-namemt4161mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a><a name="MT4161"/>MT4161: O registrador encontrada uma estrutura sem suporte '\*': todos os campos em uma estrutura também devem ser estruturas (campo '\*' com tipo '2}' não é uma estrutura).

O registrador de encontrar uma estrutura com campos sem suporte.

Todos os campos em uma estrutura que é exposto para Objective-C também devem ser estruturas (não classes).

### <a name="a-namemt4162mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a><a name="MT4162"/>MT4162: O tipo '\*' (usada como * 2}) não está disponível no * * (foi introduzido em * *)\* Compile com uma versão mais recente * SDK (geralmente feita usando a versão mais recente do Xcode.

O registrador encontrado um tipo que não está incluído no SDK do atual.

Atualize o Xcode.

### <a name="a-namemt4163mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT4163"/>MT4163: Erro interno no registrador de (*). Envie um relatório de erros em http://bugzilla.xamarin.com

Esse erro indica um bug no xamarin. Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4164mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a><a name="MT4164"/>MT4164: Não é possível exportar a propriedade '\*' porque seu seletor '\*' é uma palavra-chave Objective-C. Use um nome diferente.

O seletor para a propriedade em questão não é um identificador válido do Objective-C.

Use um identificador válido do Objective-C como seletores.

### <a name="a-namemt4165mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a><a name="MT4165"/>MT4165: O registrador não foi possível encontrar o tipo 'System. Void' em qualquer um dos assemblies referenciados.

Esse erro provavelmente indica um bug no xamarin. Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4166mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a><a name="MT4166"/>MT4166: Não é possível registrar o método '\*' porque a assinatura contém um tipo (\*) que não é um tipo de referência.

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4167mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a><a name="MT4167"/>MT4167: Não é possível registrar o método '\*' porque a assinatura contém um tipo genérico (\*) com um tipo de argumento genérico que não é uma subclasse de NSObject (*).

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt4168mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a><a name="MT4168"/>MT4168: Não é possível registrar o tipo ' {gerenciado\_name}' porque seu nome Objective-C ' {exportado\_nome}' é uma palavra-chave Objective-C. Use um nome diferente.

O nome de Objective-C para o tipo em questão não é um identificador válido do Objective-C.

Use um identificador válido do Objective-C.

### <a name="a-namemt4169mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a><a name="MT4169"/>MT4169: Falha ao gerar um wrapper de P/Invoke para {método}: {message}

Xamarin Falha ao gerar uma função de wrapper de P/Invoke para o mencionadas.
Verifique a mensagem de erro relatado para saber a causa subjacente.

### <a name="a-namemt4170mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a><a name="MT4170"/>MT4170: O registrador não pode converter de '{tipo gerenciado}' para '{tipo nativo}' para o valor de retorno no método {método}.

Consulte a descrição do erro <a href="#MT4172">MT4172</a>.

### <a name="a-namemt4171mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a><a name="MT4171"/>MT4171: O atributo BindAs no membro {membro} é inválido: o tipo de BindAs {type} é diferente do tipo de propriedade {type}.

Verifique se que o tipo do atributo BindAs corresponde ao tipo do membro a que é anexado.

### <a name="a-namemt4172mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a><a name="MT4172"/>MT4172: O registrador não pode converter de '{tipo nativo}' para '{tipo gerenciado}' para o parâmetro '{nome do parâmetro}' no método {método}.

O registrador não dá suporte à conversão entre os tipos mencionados.

Este é um bug no xamarin se a API em questão é fornecida pelo xamarin; Registre um bug no [http://bugzilla.xamarin.com][1].

Se você encontrar isso durante o desenvolvimento de um projeto de associação para uma biblioteca nativa, estamos abertas para adicionar suporte para novas combinações de tipos. Se esse for o caso, envie uma solicitação de aprimoramento ([http://bugzilla.xamarin.com][2]) com um teste de caso e vai avaliar.

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: Mensagens de erro GCC e ferramentas

### <a name="mt51xx-compilation"></a>MT51xx: compilação

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

### <a name="a-namemt5101mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a><a name="MT5101"/>MT5101 ausente ' *' compilador. Instale o Xcode componente 'Ferramentas de linha de comando'

### <a name="a-namemt5102mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5102"/>MT5102 Falha ao montar o arquivo ' *'. Envie um relatório de erros em http://bugzilla.xamarin.com

### <a name="a-namemt5103mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5103"/>MT5103 Falha ao compilar o arquivo ' *'. Envie um relatório de erros em http://bugzilla.xamarin.com

### <a name="a-namemt5104mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a><a name="MT5104"/>MT5104 não localizou nenhum o '\*'ou'\*' compilador. Instale o Xcode componente 'Ferramentas de linha de comando'

<!-- 5105 is used by mmp -->

### <a name="a-namemt5106mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5106"/>MT5106: Não foi possível compilar o arquivo (s) ' *'. Envie um relatório de erros em http://bugzilla.xamarin.com

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt52xx-linking"></a>MT52xx: Linking

<!--
  MT52xx linking
  -->

### <a name="a-namemt5201mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a><a name="MT5201"/>Falha ao vincular MT5201 nativo. Examine o log de compilação e os sinalizadores do usuário fornecidos para gcc: *

### <a name="a-namemt5202mt5202-native-linking-failed-please-review-the-build-log"></a><a name="MT5202"/>Falha ao vincular MT5202 nativo. Examine o log de compilação.

### <a name="a-namemt5203mt5203-native-linking-warning-"></a><a name="MT5203"/>MT5203 nativo vinculação Aviso: *

<!--- 5204-5208 are not used -->

### <a name="a-namemt5209mt5209-native-linking-error-"></a><a name="MT5209"/>MT5209 nativo erro de vinculação: *

### <a name="a-namemt5210mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a><a name="MT5210"/>MT5210: Vinculando nativo falha, o símbolo indefinido: *. Verifique se todas as estruturas necessárias foram referenciadas e bibliotecas nativas são vinculadas corretamente.

Isso acontece quando o vinculador nativo não é possível encontrar um símbolo que é referenciado em algum lugar. Há várias razões, que isso pode ocorrer:

* Uma associação de terceiros requer uma estrutura, mas a associação não especificar isso no seu `[LinkWith]` atributo. Soluções:
  - Se você for o autor da associação de terceiros ou ter acesso à sua origem, modifique a associação `[LinkWith]` atributo para incluir o framework de que precisa:

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - Se você não pode modificar a associação de terceiros, você pode vincular manualmente com o framework requerido passando `-gcc_flags '-framework SystemFramework'` para `mtouch` (Isso é feito modificando os argumentos mtouch adicionais na página de opções de compilação do projeto iOS. Lembre-se de que isso deve ser feito para cada configuração de projeto).
* Em alguns casos uma associação gerenciada é composta por várias bibliotecas nativas e todos devem ser incluídos nas associações. É possível ter mais de uma biblioteca nativa em cada projeto de vinculação para a solução é apenas adicionar todas as bibliotecas nativas necessárias para o projeto de associação.</li>
* Uma associação gerenciada se refere ao símbolo nativo que não existe na biblioteca nativa.
    Isso geralmente ocorre quando uma associação existe há algum tempo, e o código nativo foi modificado durante esse tempo para que uma determinada classe nativo foi removida ou renomeada, enquanto a associação não foi atualizada.
* Um valor de P/Invoke refere-se a um símbolo nativo que não existe. Começando com o xamarin 7.4 um <a href="#MT5214">MT5214</a> erro será relatado para esse caso (consulte MT5214 para obter mais informações).
* Uma associação de terceiros / biblioteca foi criada usando C++, mas a associação não especificar isso no seu `[LinkWith]` atributo. Isso geralmente é razoavelmente fácil de reconhecer, porque têm os símbolos são símbolos de C++ danificados (um exemplo comum é `__ZNKSt9exception4whatEv`).
  - Se você for o autor da associação de terceiros ou ter acesso à sua origem, modifique a associação `[LinkWith]` atributo para definir o `IsCxx` sinalizador:

            [LinkWith ("mylib.a", IsCxx = true)]

  - Se você não pode modificar a associação de terceiros, ou você está vinculando manualmente com uma biblioteca de terceiros, você pode definir o sinalizador equivalente ao passar <code>-cxx</code> para mtouch (Isso é feito modificando os argumentos mtouch adicionais na página de opções de compilação do projeto iOS . Lembre-se de que isso deve ser feito para cada configuração de projeto).

### <a name="a-namemt5211mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a><a name="MT5211"/>MT5211: Vinculando nativo falha, classe indefinida de Objective-C: \*. O símbolo '\*' não pôde ser encontrado em qualquer uma das bibliotecas ou estruturas vinculadas ao seu aplicativo.

Isso acontece quando o vinculador nativo não é possível localizar uma classe Objective-C que é referenciada em algum lugar. Há várias razões, isso pode acontecer: os mesmos para [MT5210](#MT5210) e Além disso:

* Uma associação de terceiros associado a um protocolo Objective-C, mas não anotá-lo com o <code>[Protocol]</code> atributo em sua definição de api. Soluções:
  - Adicionar o ausentes `[Protocol]` atributo:

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

### <a name="a-namemt5212mt5212-native-linking-failed-duplicate-symbol-"></a><a name="MT5212"/>MT5212: Vinculando nativo falha, duplicar o símbolo: *.

Isso ocorre quando o vinculador nativo encontra símbolos duplicados entre todas as bibliotecas nativas. Após esse erro pode haver um ou mais [MT5213](#MT5213) erros com o local para cada ocorrência do símbolo. Possíveis motivos para esse erro:

* Da mesma biblioteca nativa é incluída duas vezes.
* Duas bibliotecas nativas distintas acontecem definir os símbolos mesmo.
* Uma biblioteca nativa não está compilada corretamente e contém o mesmo símbolo mais de uma vez.
  Você pode confirmar isso usando o seguinte conjunto de comandos de um terminal (substitua i386 x86_64/armv7/armv7s/arm64 de acordo com a arquitetura do qual você está criando para):

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  Há algumas maneiras possíveis de corrigir isso:

  - Solicite que o provedor da biblioteca nativa de corrigi-lo e fornecer a versão atualizada.
  - Corrigi-lo por conta própria, removendo os arquivos de objeto extra (isso só funcionará se o problema é, na verdade, os arquivos de objeto duplicado)

            # Find out if the library is a fat library, and which
            # architectures it contains.
            lipo -info libNative.a

            # Extract each architecture (i386/x86_64/armv7/armv7s/arm64) to a separate file
            lipo libNative.a -thin ARCH -output libNative.ARCH.a

            # Extract the object files for the offending architecture
            # This will remove the duplicates by overwriting them
            # (since they have the same filename)
            mkdir -p ARCH
            cd ARCH
            ar -x ../libNative.ARCH.a

            # Reassemble the object files in an .a
            ar -r ../libNative.ARCH.a *.o
            cd ..

            # Reassemble the fat library
            lipo *.a -create -output libNative.a

  - Solicitar o vinculador para remover o código não utilizado. Xamarin faz isso automaticamente se todas as seguintes condições forem atendidas:
    - Todas as associações de terceiros `[LinkWith]` atributos habilitou SmartLink:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - Não `-gcc_flags` é passado para mtouch (no campo argumentos adicionais mtouch do iOS do projeto opções de compilação).
    - Também é possível solicitar o vinculador diretamente para remover o código não utilizado adicionando `-gcc_flags -dead_strip` opções de compilação para os argumentos adicionais mtouch no iOS do projeto.

### <a name="a-namemt5213mt5213-duplicate-symbol-in--location-related-to-previous-error"></a><a name="MT5213"/>MT5213: Duplicar símbolo: * (local relacionado ao erro anterior)

Esse erro é relatado apenas junto com [MT5212](#MT5212). Consulte [MT5212](#MT5212) para obter mais informações.

### <a name="a-namemt5214mt5214--native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a><a name="MT5214"/>Indefinido MT5214 nativo vinculação com falha, símbolo: *. Este símbolo foi referenciado o membro gerenciado *. Verifique se todas as estruturas necessárias foram bibliotecas referenciadas e nativo vinculadas.

Esse erro é relatado quando o código gerenciado contém um P/Invoke para um método nativo que não existe. Por exemplo:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Há algumas soluções possíveis:

  -  Remova de P/Invokes em questão do código-fonte.
  -  Habilite o vinculador gerenciado para todos os assemblies (Isso é feito no iOS do projeto opções de compilação, definindo "Comportamento do vinculador" para "Todos os assemblies"). Isso efetivamente removerá todos os de P/Invokes não use do aplicativo (automaticamente, em vez de manualmente como o ponto anterior). A desvantagem é que isso fará com que suas compilações simulador um pouco mais lentas, e pode dividir seu aplicativo se ele está usando a reflexão - para obter mais informações sobre o vinculador podem ser encontradas [aqui](~/ios/deploy-test/linker.md) )
  -  Crie uma biblioteca nativa de segundo, que contém os símbolos nativo ausentes. Observe que isso é apenas uma solução alternativa (se você tentar chamar essas funções, o aplicativo falhará).

### <a name="a-namemt5215mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a><a name="MT5215"/>MT5215: As referências a ' *' pode exigir adicionais - framework = XXX ou lXXX - instruções para o vinculador nativo

Este é um aviso, indicando que foi detectado um P/Invoke para referenciar a biblioteca em questão, mas o aplicativo não é a vinculação com ele.

### <a name="a-namemt5216mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT5216"/>MT5216: Vinculando nativo falha para *. Envie um relatório de erros em http://bugzilla.xamarin.com

Esse erro é relatado ao vincular a saída do compilador AOT.

Esse erro provavelmente indica um bug no xamarin. Envie um relatório de erros em [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt5217mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a><a name="MT5217"/>MT5217: Vinculando nativo possivelmente falhou porque a linha de comando do vinculador era muito longa (* caracteres).

Vinculando nativo falha e é possível que isso ocorreu porque o comando do vinculador era muito longo.

Xamarin projetos geralmente referenciará símbolos nativo dinamicamente, que significa que o vinculador nativo pode remover esses símbolos nativo durante o processo de vinculação nativo, porque o vinculador nativo não verá que esses símbolos são usados.

Geralmente xamarin pedirá que o vinculador nativo para manter esses símbolos usando o `-u symbol` sinalizador vinculador, mas se houver muitos tais símbolos, toda a linha de comando poderá exceder o comprimento máximo de linha de comando conforme especificado pelo sistema operacional.

Há alguns possíveis fontes para tal símbolos dinâmicos:

* P/Invokes para métodos nas bibliotecas vinculadas estaticamente (onde é o nome da dll `__Internal` no atributo DllImport `[DllImport ("__Internal")]`).
* Campo referências a locais da memória em bibliotecas vinculadas estaticamente de associação de projetos (`[Field]` atributos).
* Classes de Objective-C referenciados em bibliotecas vinculadas estaticamente da associação de projetos (ao usar compilações incrementais ou quando não estiver usando o registrador estático).

Soluções possíveis:

* Habilite o vinculador gerenciado (se for possível para todos os assemblies em vez de apenas assemblies do SDK). Isso poderá remover suficiente as fontes para símbolos dinâmicos para que o vinculador 's de linha de comando não excedeu o máximo.
* Reduza o número de P/Invokes, referências de campo e/ou classes Objective-C.
* Reescreva os símbolos dinâmicos para ter nomes mais curtos.
* Passar `-dlsym:false` opções de compilação como um argumento mtouch adicionais no iOS do projeto. Com essa opção, xamarin irá gerar uma referência nativa no código compilado AOT e não será necessário solicitar que o vinculador para manter este símbolo. No entanto, isso só funciona para dispositivo cria e causará erros de vinculador se houver P/Invokes para as funções que não existem na biblioteca estática.
* Passar `--dynamic-symbol-mode=code` como argumentos adicionais mtouch no iOS do projeto opções de compilação. Com essa opção, xamarin irá gerar código nativo adicional que faz referência a esses símbolos em vez de solicitar o vinculador nativo para manter esses símbolos usando argumentos de linha de comando. A desvantagem dessa abordagem é que ela aumentará o tamanho do executável um pouco.
* Habilitar o registrador estático passando `--registrar:static` como um argumento mtouch adicionais no iOS do projeto compilação opções (para compilações de simulador, pois o registrador estático já é o padrão para compilações do dispositivo). O registrador estático irá gerar o código que faz referência a classes Objective-C estaticamente, portanto não é necessário para solicitar que o vinculador nativo para manter essas classes.
* Desabilite compilações incrementais (para compilações do dispositivo). Quando as compilações incrementais estão habilitadas, o código gerado pelo registrador de estático não ser considerado pelo vinculador nativo, que significa que o xamarin ainda devem solicitar o vinculador para manter referenciado classes Objective-C. Desabilitar assim compilações incrementais impedirá a essa necessidade.

### <a name="a-namemt5218mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a><a name="MT5218"/>MT5218: Não é possível ignorar o símbolo dinâmico {símbolo} (– símbolo ignorar de dinâmico = {símbolo}) porque não foi detectada como um símbolo dinâmico.

O argumento de linha de comando `--ignore-dynamic-symbol=symbol` foi passado, mas este símbolo não é um símbolo que foi reconhecido como um símbolo dinâmico que deve ser preservado manualmente.

Há duas razões principais para isso:

* O nome do símbolo está incorreto.
    * Não insira um sublinhado para o nome do símbolo.
    * O símbolo para classes Objective-C é `OBJC_CLASS_$_<classname>`.
* O símbolo está correto, mas é um símbolo que já é preservado por meios normais (alguns compilação causas opções lista exata de símbolos dinâmicos para variar).

### <a name="mt53xx-other-tools"></a>MT53xx: Outras ferramentas

<!--
  MT53xx other tools
  -->

### <a name="a-namemt5301mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a><a name="MT5301"/>MT5301: Faltando 'Remover' ferramenta. Instale o Xcode componente 'Ferramentas de linha de comando'

### <a name="a-namemt5302mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a><a name="MT5302"/>MT5302: Ferramenta de 'dsymutil' ausente. Instale o Xcode componente 'Ferramentas de linha de comando'

### <a name="a-namemt5303mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a><a name="MT5303"/>MT5303: Falha ao gerar os símbolos de depuração (dSYM directory). Examine o log de compilação.

Ocorreu um erro ao executar dsymutil no diretório App final para criar os símbolos de depuração. Examine o log de compilação para ver a saída do dsymutil e ver como ele pode ser corrigido.

### <a name="a-namemt5304mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a><a name="MT5304"/>MT5304: Falha ao remover o binário final. Examine o log de compilação.

Ocorreu um erro ao executar a ferramenta 'Remover' para remover as informações de depuração do aplicativo.

### <a name="a-namemt5305mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a><a name="MT5305"/>MT5305: Ferramenta de 'lipo' ausente. Instale o Xcode componente 'Ferramentas de linha de comando'

### <a name="a-namemt5306mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a><a name="MT5306"/>MT5306: Falha ao criar a uma biblioteca fat. Examine o log de compilação.

Ocorreu um erro ao executar a ferramenta 'lipo'. Examine o log de compilação para ver o erro relatado por 'lipo'.

### <a name="a-namemt5307mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a><a name="MT5307"/>MT5307: Falha ao assinar o executável. Examine o log de compilação.

Ocorreu um erro ao assinar o aplicativo. Examine o log de compilação para ver o erro relatado por 'codesign'.

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mensagens de erro das ferramentas mtouch interno

### <a name="mt600x-stripper"></a>MT600x: Stripper

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

### <a name="a-namemt6001mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a><a name="MT6001"/>MT6001: Versão de Cecil não dá suporte a colocação de assembly

### <a name="a-namemt6002mt6002-could-not-strip-assembly-"></a><a name="MT6002"/>MT6002: Não foi possível remover assembly `*`.

Ocorreu um erro quando a remoção de código gerenciado (removendo o código IL) dos assemblies no aplicativo.

### <a name="a-namemt6003mt6003-unauthorizedaccessexception-message"></a><a name="MT6003"/>MT6003: [UnauthorizedAccessException mensagem]

Erro segurança ao retirando símbolos do aplicativo de depuração.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Mensagens de erro do MSBuild

<!--
 MT7xxx msbuild errors
  -->

### <a name="a-namemt7001mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a><a name="MT7001"/>MT7001: Não foi possível resolver IPs de host para as configurações do depurador de Wi-Fi.

*Tarefa MSBuild: DetectDebugNetworkConfigurationTaskBase*

Etapas de solução de problemas:

- Tente executar `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (deve ser um endereço IP e não um erro obviamente).
- Tente executar "ping \`hostname\`" que pode fornecer mais informações, como: `cannot resolve MyHost.local: Unknown host`

Em alguns casos, ele é um problema de "rede local" e ele poderá ser resolvido adicionando o host desconhecido `127.0.0.1   MyHost.local` em `/etc/hosts`.

### <a name="a-namemt7002mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a><a name="MT7002"/>MT7002: Este computador não tem nenhum adaptador de rede. Isso é necessário quando a depuração ou a criação de perfil no dispositivo em Wi-Fi.

*Tarefa MSBuild: DetectDebugNetworkConfigurationTaskBase*

### <a name="a-namemt7003mt7003-the-app-extension--does-not-contain-an-infoplist"></a><a name="MT7003"/>MT7003: A extensão do aplicativo ' *' não contém um Info. plist.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7004mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a><a name="MT7004"/>MT7004: A extensão do aplicativo ' *' não especifica um CFBundleIdentifier.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7005mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a><a name="MT7005"/>MT7005: A extensão do aplicativo ' *' não especifica um CFBundleExecutable.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7006mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7006"/>MT7006: A extensão do aplicativo '\*' tem um CFBundleIdentifier inválido (\*), ele não começa com CFBundleIdentifier do pacote aplicativo principal (*).

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7007mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a><a name="MT7007"/>MT7007: A extensão do aplicativo '\*' tem um CFBundleIdentifier (\*) que termina com o sufixo ilegal "chave".

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7008mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a><a name="MT7008"/>MT7008: A extensão do aplicativo ' *' não especifica um CFBundleShortVersionString.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7009mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a><a name="MT7009"/>MT7009: A extensão do aplicativo ' *' tem um Info. plist inválido: ele não contém um dicionário NSExtension.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7010mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a><a name="MT7010"/>MT7010: A extensão do aplicativo ' *' tem um Info. plist inválido: o dicionário NSExtension não contém um valor de NSExtensionPointIdentifier.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7011mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a><a name="MT7011"/>MT7011: A extensão de WatchKit ' *' tem um Info. plist inválido: o dicionário NSExtension não contém um dicionário NSExtensionAttributes.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7012mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a><a name="MT7012"/>MT7012: A extensão de WatchKit ' *' não tem exatamente um aplicativo de inspeção.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7013mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a><a name="MT7013"/>MT7013: A extensão de WatchKit ' *' tem um Info. plist inválido: UIRequiredDeviceCapabilities deve conter o recurso 'inspecionar complementar'.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7014mt7014-the-watch-app--does-not-contain-an-infoplist"></a><a name="MT7014"/>MT7014: O aplicativo de inspeção ' *' não contém um Info. plist.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7015mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a><a name="MT7015"/>MT7015: O aplicativo de inspeção ' *' não especifica um CFBundleIdentifier.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7016mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7016"/>MT7016: O aplicativo Watch '\*' tem um CFBundleIdentifier inválido (\*), ele não começa com CFBundleIdentifier do pacote aplicativo principal (*).

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7017mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a><a name="MT7017"/>MT7017: O aplicativo de inspeção '\*' não tem um valor de UIDeviceFamily válido. Esperado '(4) observar' mas foi encontrado '\* (*)'.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7018mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a><a name="MT7018"/>MT7018: O aplicativo de inspeção ' *' não especifica um CFBundleExecutable

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7019mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7019"/>MT7019: O aplicativo Watch '\*' tem um valor de WKCompanionAppBundleIdentifier inválido ('\*'), não corresponde a CFBundleIdentifier do pacote de aplicativo principal ('* ').

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7020mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a><a name="MT7020"/>MT7020: O aplicativo de inspeção ' *' tem um Info. plist inválido: a chave WKWatchKitApp deve estar presente e tem um valor 'true'.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7021mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a><a name="MT7021"/>MT7021: O aplicativo Watch ' *' tem um Info. plist inválido: a chave LSRequiresIPhoneOS não deve estar presente.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7022mt7022-the-watch-app--does-not-contain-a-watch-extension"></a><a name="MT7022"/>MT7022: O aplicativo de inspeção ' *' não tem uma extensão de inspeção.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7023mt7023-the-watch-extension--does-not-contain-an-infoplist"></a><a name="MT7023"/>MT7023: A extensão de inspeção ' *' não contém um Info. plist.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7024mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a><a name="MT7024"/>MT7024: A extensão de inspeção ' *' não especifica um CFBundleIdentifier.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7025mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a><a name="MT7025"/>MT7025: A extensão de inspeção ' *' não especifica um CFBundleExecutable.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7026mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a><a name="MT7026"/>MT7026: A extensão de inspeção '\*' tem um CFBundleIdentifier inválido (\*), ele não começa com CFBundleIdentifier do pacote aplicativo principal (*).

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7027mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a><a name="MT7027"/>MT7027: A extensão de inspeção '\*' tem um CFBundleIdentifier (\*) que termina com o sufixo ilegal "chave".

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7028mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a><a name="MT7028"/>MT7028: A extensão de inspeção ' *' tem um Info. plist inválido: ele não contém um dicionário NSExtension.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7029mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a><a name="MT7029"/>MT7029: A extensão de inspeção ' *' tem um Info. plist inválido: o NSExtensionPointIdentifier deve ser "com.apple.watchkit".

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7030mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a><a name="MT7030"/>MT7030: A extensão de inspeção ' *' tem um Info. plist inválido: o dicionário NSExtension deve conter NSExtensionAttributes.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7031mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a><a name="MT7031"/>MT7031: A extensão de inspeção ' *' tem um Info. plist inválido: o dicionário NSExtensionAttributes deve conter um WKAppBundleIdentifier.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7032mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a><a name="MT7032"/>MT7032: A extensão de WatchKit ' *' tem um Info. plist inválido: UIRequiredDeviceCapabilities não deve conter a funcionalidade 'inspecionar complementar'.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7033mt7033-the-watch-app--does-not-contain-an-infoplist"></a><a name="MT7033"/>MT7033: O aplicativo de inspeção ' *' não contém um Info. plist.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7034mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a><a name="MT7034"/>MT7034: O aplicativo de inspeção ' *' não especifica um CFBundleIdentifier.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7035mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a><a name="MT7035"/>MT7035: O aplicativo de inspeção '\*' não tem um valor de UIDeviceFamily válido. Esperado '\*'mas encontrado'\* (\*)'.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7036mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a><a name="MT7036"/>MT7036: O aplicativo de inspeção ' *' não especifica um CFBundleExecutable.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7037mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a><a name="MT7037"/>MT7037: A extensão WatchKit '{extensionName}' tem um valor de WKAppBundleIdentifier inválido ('\*'), não corresponde a CFBundleIdentifier inspeção do aplicativo ('\*').

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7038mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a><a name="MT7038"/>MT7038: O aplicativo Watch ' *' tem um Info. plist inválido: o WKCompanionAppBundleIdentifier deve existir e deve corresponder CFBundleIdentifier do pacote de aplicativo principal.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7039mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a><a name="MT7039"/>MT7039: O aplicativo Watch ' *' tem um Info. plist inválido: a chave LSRequiresIPhoneOS não deve estar presente.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7040mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a><a name="MT7040"/>MT7040: O pacote de aplicativos {AppBundlePath} não contém um Info. plist.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7041mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a><a name="MT7041"/>MT7041: O caminho principal Info. plist não especifica um CFBundleIdentifier.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7042mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a><a name="MT7042"/>MT7042: O caminho principal Info. plist não especifica um CFBundleExecutable.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7043mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a><a name="MT7043"/>MT7043: O caminho principal Info. plist não especifica um CFBundleSupportedPlatforms.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7044mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a><a name="MT7044"/>MT7044: O caminho principal Info. plist não especifica um UIDeviceFamily.

*Tarefa MSBuild: ValidateAppBundleTaskBase*

### <a name="a-namemt7045mt7045-unrecognized-format-"></a><a name="MT7045"/>MT7045: Formato não reconhecido: *.

*Tarefa MSBuild: PropertyListEditorTaskBase*

Onde * pode ser:

- cadeia de caracteres
- array
- dict
- bool
- real
- inteiro
- date
- Dados

### <a name="a-namemt7046mt7046-add-entry--incorrectly-specified"></a><a name="MT7046"/>MT7046: Adicionar: entrada, *, especificado incorretamente.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7047mt7047-add-entry--contains-invalid-array-index"></a><a name="MT7047"/>MT7047: Adicionar: entrada, *, contém o índice de matriz inválido.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7048mt7048-add--entry-already-exists"></a><a name="MT7048"/>MT7048: Adicionar: * entrada já existe.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7049mt7049-add-cant-add-entry--to-parent"></a><a name="MT7049"/>MT7049: Adicionar: não é possível adicionar a entrada, *, para o pai.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7050mt7050-delete-cant-delete-entry--from-parent"></a><a name="MT7050"/>MT7050: Excluir: não é possível excluir a entrada, *, do pai.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7051mt7051-delete-entry--contains-invalid-array-index"></a><a name="MT7051"/>MT7051: Excluir: entrada, *, contém o índice de matriz inválido.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7052mt7052-delete-entry--does-not-exist"></a><a name="MT7052"/>MT7052: Excluir: entrada, *, não existe.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7053mt7053-import-entry--incorrectly-specified"></a><a name="MT7053"/>MT7053: Importar: entrada, *, especificado incorretamente.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7054mt7054-import-entry--contains-invalid-array-index"></a><a name="MT7054"/>MT7054: Importar: entrada, *, contém o índice de matriz inválido.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7055mt7055-import-error-reading-file-"></a><a name="MT7055"/>MT7055: Importar: erro ao ler arquivo: *.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7056mt7056-import-cant-add-entry--to-parent"></a><a name="MT7056"/>MT7056: Importar: não é possível adicionar a entrada, *, para o pai.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7057mt7057-merge-cant-add-array-entries-to-dict"></a><a name="MT7057"/>MT7057: Mesclar: não é possível adicionar entradas de matriz para dict.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7058mt7058-merge-specified-entry-must-be-a-container"></a><a name="MT7058"/>MT7058: Mesclar: especificado a entrada deve ser um contêiner.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7059mt7059-merge-entry--contains-invalid-array-index"></a><a name="MT7059"/>MT7059: Mesclar: entrada, *, contém o índice de matriz inválido.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7060mt7060-merge-entry--does-not-exist"></a><a name="MT7060"/>MT7060: Mesclar: entrada, *, não existe.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7061mt7061-merge-error-reading-file-"></a><a name="MT7061"/>MT7061: Mesclar: erro ao ler arquivo: *.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7062mt7062-set-entry--incorrectly-specified"></a><a name="MT7062"/>MT7062: Definir: entrada, *, especificado incorretamente.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7063mt7063-set-entry--contains-invalid-array-index"></a><a name="MT7063"/>MT7063: Definir: entrada, *, contém o índice de matriz inválido.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7064mt7064-set-entry--does-not-exist"></a><a name="MT7064"/>MT7064: Definir: entrada, *, não existe.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7065mt7065-unknown-propertylist-editor-action-"></a><a name="MT7065"/>MT7065: Ação de editor desconhecido PropertyList: *.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7066mt7066-error-loading--"></a><a name="MT7066"/>MT7066: Erro ao carregar ' *': *.

*Tarefa MSBuild: PropertyListEditorTaskBase*

### <a name="a-namemt7067mt7067-error-saving--"></a><a name="MT7067"/>MT7067: Erro ao salvar ' *': *.

*Tarefa MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Mensagens de erro de tempo de execução

<!--
 MT8xxx runtime
  MT800x misc
  -->

### <a name="a-namemt8001mt8001--version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a><a name="MT8001"/>Incompatibilidade de versão de MT8001 entre o tempo de execução nativo xamarin e monotouch.dll. Reinstale o xamarin.

### <a name="a-namemt8002mt8002--could-not-find-the-method--in-the-type-"></a><a name="MT8002"/>MT8002 não localizou o método '\*'no tipo'\*'.

### <a name="a-namemt8003mt8003--failed-to-find-the-closed-generic-method--on-the-type-"></a><a name="MT8003"/>MT8003 Falha ao localizar o método genérico fechado '\*'no tipo'\*'.

### <a name="a-namemt8004mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a><a name="MT8004"/>MT8004: Não é possível criar uma instância de * para o objeto nativo 0 x * (do tipo ' *'), porque já existe outra instância deste objeto nativo (do tipo *).

### <a name="a-namemt8005mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a><a name="MT8005"/>MT8005: Tipo de Wrapper '\*'está faltando a sua classe ObjectiveC nativo'\*'.

### <a name="a-namemt8006mt8006-failed-to-find-the-selector--on-the-type-"></a><a name="MT8006"/>MT8006: Falha ao localizar o seletor '\*'no tipo'\*'

### <a name="a-namemt8007mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a><a name="MT8007"/>MT8007: Não é possível obter o descritor de método para o seletor de '\*'no tipo'\*', pois o seletor não corresponde a um método

### <a name="a-namemt8008mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8008"/>MT8008: A versão carregada do Xamarin.iOS.dll foi compilada para * bits, enquanto o processo é * bits. Registre um bug no http://bugzilla.xamarin.com.

Isso indica que algo está errado no processo de compilação. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8009mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8009"/>MT8009: Não é possível localizar o bloco para delegar método de conversão para o método *.*' s parâmetro #*. Registre um bug no http://bugzilla.xamarin.com.

Isso indica que uma API não foi associada corretamente. Se esta for uma API exposta por Xamarin, registrar um bug em nosso bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)), se ele é uma associação de terceiros, entre em contato com o fornecedor.

### <a name="a-namemt8010mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a><a name="MT8010"/>MT8010: Incompatibilidade de tamanho tipo nativo entre Xamarin. [iOS | . Dll de MAC] e a arquitetura de execução. Xamarin. [iOS | . Dll de MAC] foi criado para *-bit, enquanto o processo atual é *-bits.

Isso indica que algo está errado no processo de compilação. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8011mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8011"/>MT8011: Não é possível localizar o delegado ao atributo de conversão de bloco ([DelegateProxy]) para o valor de retorno para o método *.*. Registre um bug no http://bugzilla.xamarin.com.

Xamarin não pôde localizar um método necessário em tempo de execução (para converter um delegado a um bloco).

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8012mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8012"/>MT8012: O DelegateProxyAttribute inválido para o valor de retorno para o método *.*: DelegateType é nulo. Registre um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8013mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8013"/>MT8013: O DelegateProxyAttribute inválido para o valor de retorno para o método *.*: DelegateType (2}) especifica um tipo sem um campo 'Handler'. Registre um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8014mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8014"/>MT8014: O DelegateProxyAttribute inválido para o valor de retorno para o método *.*: O DelegateType (2}) campo 'Handler' é nulo. Registre um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8015mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8015"/>MT8015: O DelegateProxyAttribute inválido para o valor de retorno para o método *.*: O DelegateType (2}) campo 'Handler' não é um delegado, que é um *. Registre um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8016mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a><a name="MT8016"/>MT8016: Não é possível converter o representante para bloquear o valor de retorno para o método *.*, pois a entrada não é um delegado é um *. Registre um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin; Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<!-- 8017 is used by mmp -->

### <a name="a-namemt8018mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT8018"/>MT8018: Erro de consistência interno. Envie um relatório de erros em http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8019mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a><a name="MT8019"/>MT8019: Não foi possível encontrar o assembly * nos assemblies carregados.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8020mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a><a name="MT8020"/>MT8020: Não foi possível localizar o módulo com MetadataToken * no assembly *.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8021mt8021-unknown-implicit-token-type-"></a><a name="MT8021"/>MT8021: Tipo de token implícita desconhecido: *.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8022mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT8022"/>MT8022: Esperado a referência de token * para ser um *, mas é um *. Envie um relatório de erros em http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8023mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a><a name="MT8023"/>MT8023: Um objeto de instância é necessário para construir um método genérico fechado para o método genérico aberto: * (referência de token: *). Envie um relatório de erros em http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="a-namemt8024mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a><a name="MT8024"/>MT8024: Não foi possível encontrar um tipo de extensão válido para enum inteligente '{smart_type}'. Registre um bug no https://bugzilla.xamarin.com.

Isso indica um bug no xamarin. Registre um bug no [http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).
