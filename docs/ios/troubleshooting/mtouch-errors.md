---
title: Erros do xamarin. IOS
description: Este documento descreve vários erros gerados pelo mtouch, a ferramenta usada para agrupar os aplicativos xamarin. IOS. Erros são listados por código e receber uma descrição completa.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: e6e3a989db922dc2941cca4c888c862ffe159241
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422046"
---
# <a name="xamarinios-errors"></a>Erros do xamarin. IOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mensagens de erro do mtouch

Por exemplo, parâmetros, ambiente, falta de ferramentas.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
    -->

<a name="MT0000" />

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000: Erro inesperado - preencha um relatório de bugs no https://github.com/xamarin/xamarin-macios/issues/new

Ocorreu uma condição de erro inesperado. Por favor [relatório de erro](https://github.com/xamarin/xamarin-macios/issues/new) com tantas informações quanto possível, incluindo:

* Build completo logs, com detalhamento máximo (por exemplo, `-v -v -v -v` no **argumentos adicionais do mtouch**);
* Um caso de teste mínimo que reproduza o erro; e
* Todas as informações de versão

A maneira mais fácil para obter informações de versão exata é usar o **Visual Studio para Mac** menu **sobre o Visual Studio para Mac** item, **Mostrar detalhes** botão e copiar/colar o informações de versão (você pode usar o **copiar informações** botão).

<a name="MT0001" />

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: '-devname' foi fornecido sem qualquer ação específica do dispositivo

Este é um aviso que é emitido se - devname é passado para o mtouch quando nenhuma ação específica do dispositivo (-logdev/installdev/killdev/launchdev /-listapps) foi solicitado.

<a name="MT0002" />

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: Não foi possível analisar a variável de ambiente *.

Esse erro ocorre se você tentar definir uma chave de ambiente inválida = par de valor variável. O formato correto é: `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003" />

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: Nome do aplicativo ' * .exe ' conflita com um nome de assembly (. dll) do SDK ou o produto.

Nome do assembly executável e o nome do aplicativo não podem corresponder ao nome de qualquer dll no aplicativo. Modifique o nome do seu executável.

<a name="MT0004" />

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: Nova lógica de refcounting requer SGen seja habilitado também.

Se você habilitar a extensão de refcounting você também deve habilitar o coletor de lixo SGen em Opções de compilação (guia Avançado) do iOS do projeto.

Começando com o xamarin. IOS 7.2.1 esse requisito foi eliminada, a nova lógica de refcounting pode ser habilitada com Boehm e Coletores de lixo SGen.

<a name="MT0005" />

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: O diretório de saída * não existe.

Crie o diretório.

Esse erro não é mais gerado, mtouch automaticamente criará o diretório se ele não existir.

<a name="MT0006" />

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: Não há nenhuma plataforma devel em *, use--plataforma = PLAT para especificar o SDK.

Xamarin. IOS não é possível localizar o diretório do SDK no local mencionado na mensagem de erro. Verifique se o caminho está correto.

<a name="MT0007" />

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: O assembly raiz * não existe.

Xamarin. IOS não é possível localizar o assembly no local mencionado na mensagem de erro. Verifique se o caminho está correto.

<a name="MT0008" />

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: Você deve fornecer um assemblies de # único, encontrado do assembly raiz: *.

Mais de um assembly raiz foi passado para o mtouch, enquanto que pode haver assembly somente uma raiz.

<a name="MT0009" />

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: Erro ao carregar assemblies: *.

Ocorreu um erro ao carregar os assemblies de referências de assembly raiz. Mais informações podem ser fornecidas na saída da compilação.

<a name="MT0010" />

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: Não foi possível analisar os argumentos de linha de comando: *.

Ocorreu um erro ao analisar os argumentos de linha de comando. Verifique se eles estão todos corretos.

<a name="MT0011" />

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: * foi criado em relação a um tempo de execução mais recente (*), que dá suporte a MonoTouch.

Esse aviso é relatado normalmente porque o projeto tem uma referência a uma biblioteca de classes que não foi criada usando a BCL do xamarin. IOS.

Da mesma forma que um aplicativo usando o SDK do .NET 4.0 pode não funcionar em um sistema .NET 2.0, que dão suporte a apenas uma biblioteca compilada usando o .NET 4.0 pode não funcionar no xamarin. IOS, ele pode usar a API não está presente no xamarin. IOS.

A solução geral é criar a biblioteca como uma biblioteca de classes xamarin. IOS. Isso pode ser feito criando um novo projeto de biblioteca de classes xamarin. IOS e adicione todos os arquivos de origem para ele. Se você não tiver o código-fonte para a biblioteca, deve contatar o fornecedor e solicitar que eles fornecem uma versão compatível com o xamarin. IOS da sua biblioteca.

<a name="MT0012" />

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: Dados incompletos são fornecidos para concluir *.

Esse erro não é relatado mais na versão atual do xamarin. IOS.

<a name="MT0013" />

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: Suporte de criação de perfil requer sgen seja habilitado também.

SGen (– sgen) deve ser habilitado se a criação de perfil (– criação de perfil) está habilitado.

<a name="MT0014" />

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: O iOS * SDK não oferece suporte para a criação de aplicativos de direcionamento *.

Isso pode ocorrer nas seguintes circunstâncias:

*  ARMv6 está habilitado e Xcode 4.5 ou posterior está instalado.
*  ARMv7s está habilitado e Xcode 4.4 ou anterior está instalado.

Verifique se a versão instalada do Xcode dá suporte a arquiteturas selecionadas.

<a name="MT0015" />

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x8664--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: ABI inválido: *. ABIs com suporte são: i386, x86_64, armv7, armv7 + llvm, armv7 + llvm + thumb2, armv7s, armv7s + llvm, armv7s + llvm + thumb2, arm64 e arm64 + llvm.

Uma ABI inválida foi passada para o mtouch. Especifique uma ABI válida.

<a name="MT0016" />

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: A opção * foi preterido.

A opção de mtouch mencionado foi preterida e será ignorada.

<a name="MT0017" />

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: Você deve fornecer um assembly de raiz.

É necessário especificar um assembly de raiz (normalmente o executável principal) ao criar um aplicativo.

<a name="MT0018" />

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: Argumento de linha de comando desconhecido: *.

Mtouch não reconhece o argumento de linha de comando mencionado na mensagem de erro.

<a name="MT0019" />

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: Apenas um – [log | instalar | kill | Iniciar] dev ou --[Iniciar | depurar] sim opção pode ser usada.

Há várias opções para mtouch não podem ser usados simultaneamente:

-  --logdev
-  --installdev
-  --killdev
-  --launchdev
-  --launchdebug
-  --launchsim

<a name="MT0020" />

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: As opções válidas para '\*'são'\*'.

<a name="MT0021" />

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: Não é possível compilar usando gcc / g + + (-uso gcc) ao usar o registrador estático (esse é o padrão de compilação para o dispositivo). Remova--use-gcc sinalizar ou usar o registrador dinâmico (– registrador: dinâmico).

<a name="MT0022" />

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: O opções '– sem suporte – enable-genéricos-no-registrador' e '-registrador ' não são compatíveis.

Remova as opções `--unsupported--enable-generics-in-registrar` e `--registrar`. Começando com o xamarin. IOS 7.2.1 o registrador padrão oferece suporte a genéricos.

Esse erro não é exibido (o argumento de linha de comando `--unsupported--enable-generics-in-registrar` foi removido do mtouch).

<a name="MT0023" />

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: Nome do aplicativo ' * .exe ' está em conflito com outro assembly de usuário.

Nome do assembly executável e o nome do aplicativo não podem corresponder ao nome de qualquer dll no aplicativo. Modifique o nome do seu executável.

<a name="MT0024" />

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: Não foi possível encontrar o arquivo necessário ' *'.

<a name="MT0025" />

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: Nenhuma versão do SDK foi fornecido. Adicione `--sdk=X.Y` para especificar quais iOS SDK deve ser usado para criar seu aplicativo.

<a name="MT0026" />

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: Não foi possível analisar o argumento de linha de comando ' *': *

<a name="MT0027" />

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: As opções de\*'e'\*' não são compatíveis.

<a name="MT0028" />

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: Não é possível habilitar a pizza (-pizza) ao direcionar o iOS 4.1 ou anterior. Desabilite pizza (-pizza: false) ou definir o destino de implantação para pelo menos o iOS 4.2

<a name="MT0029" />

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (– enable-repl) é compatível apenas com o simulador (– sim).

Somente há suporte para REPL se você estiver criando para o simulador. Isso significa que, se você passar `--enable-repl` para mtouch, você também deve transmitir `--sim`.

<a name="MT0030" />

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: O nome do executável (\*) e o nome do aplicativo (\*) forem diferentes, isso pode impedir que os logs de falha obtendo manipulado corretamente.

Quando o Xcode symbolicates (converte endereços de memória para nomes de função e números de linha/arquivo) o processo pode falhar se o executável e o aplicativo têm nomes diferentes (sem extensão).

Para corrigir esse altere 'Nome do aplicativo' em Opções de compilação/iOS aplicativos ou alteração de 'Nome do Assembly' nas opções de saída do Build do projeto do projeto.

<a name="MT0031" />

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: Os argumentos de linha de comando ' – Habilitar busca de tela de fundo ' e ' – inicialização de busca em segundo plano ' requer ' – launchsim' muito.

<a name="MT0032" />

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: A opção '-debugtrack' é ignorado, a menos que '-debug' também for especificado.

<a name="MT0033" />

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: Um projeto xamarin. IOS deve referenciar MonoTouch. dll ou xamarin

<a name="MT0034" />

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: Não é possível incluir 'MonoTouch. dll' e 'Xamarin' no mesmo projeto xamarin. IOS - '\*' é referenciada explicitamente, enquanto '\*' é referenciado por ' *'.

<!-- MT0035 unused -->

<a name="MT0036" />

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: Não é possível iniciar um * simulador para um * aplicativo. Habilite o architecture(s) correto em Opções de compilação (página Avançado) do iOS do seu projeto.

<a name="MT0037" />

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x8664"></a>MT0037: MonoTouch. dll é compatível com 64 bits não. O xamarin de referência ou não são compilados para uma arquitetura de 64 bits (ARM64 e/ou x86_64).

<a name="MT0038" />

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: Os registradores antigos (– registrador: oldstatic | olddynamic) não dá suporte ao fazer referência a xamarin.

<a name="MT0039" />

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: Aplicativos destinados a ARMv6 não podem referenciar o xamarin.

<a name="MT0040" />

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: Não foi possível encontrar o assembly '\*', referenciada por'\*'.

<a name="MT0041" />

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: Não é possível fazer referência a 'MonoTouch. dll' e 'Xamarin'.

<a name="MT0042" />

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: Nenhuma referência ao MonoTouch. dll ou xamarin foi encontrada. Uma referência ao MonoTouch. dll será adicionada.

<a name="MT0043" />

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: O coletor de lixo Boehm atualmente não há suporte ao fazer referência a 'Xamarin'. O coletor de lixo SGen foi selecionado em vez disso.

O coletor de lixo SGen é compatível com projetos unificado. Certifique-se de que não há nenhum sinalizador adicionais do mtouch especificando Boehm como o coletor de lixo.

<a name="MT0044" />

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044: – listsim tem suporte apenas com o Xcode 6.0 ou posterior.

Instale uma versão mais recente do Xcode.

<a name="MT0045" />

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045: – extensão tem suporte apenas ao uso do iOS SDK 8.0 (ou posterior).

<!-- MT0046 is not reported anymore -->

<a name="MT0047" />

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: O destino da implantação mínima para aplicativos unificados é 5.1.1, o destino da implantação atual é ' *'. Selecione um destino de implantação mais recente de opções de aplicativo do iOS do seu projeto.

<!-- MT0048 is not reported anymore -->

<a name="MT0049" />

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: *.framework suporte somente se o destino de implantação é 8.0 ou posterior. * recursos podem não funcionar corretamente.

Não há suporte para a estrutura especificada no refere-se o destino de implantação para a versão do iOS. Atualize o destino de implantação para uma versão mais recente do iOS ou remover o uso da estrutura especificado do aplicativo.

<!-- MT0050 is not reported anymore -->

<a name="MT0051" />

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin. IOS * exige o Xcode 5.0 ou posterior. A versão atual do Xcode (encontrada no *) é *.

Instale o Xcode uma mais recente.

<a name="MT0052" />

### <a name="mt0052-no-command-specified"></a>MT0052: Nenhum comando especificado.

Nenhuma ação foi especificada para o mtouch.

<!-- 0053 is used by mmp -->

<a name="MT0054" />

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: Não é possível canoniza o caminho ' *': *

Este é um erro interno. Se você vir esse erro, envie um bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT0055" />

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: O caminho do Xcode ' *' não existe.

O caminho do Xcode passado usando `--sdkroot` não existe. Especifique um caminho válido.

<a name="MT0056" />

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: Não é possível localizar o Xcode no local padrão (/ /Applications/Xcode.app). Instale o Xcode ou passar um caminho personalizado usando-- sdkroot <path>.

<a name="MT0057" />

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: Não é possível determinar o caminho para o Xcode. App da raiz do sdk ' *'. Especifique o caminho completo para o pacote do Xcode. App.

O caminho passado usando `--sdkroot` não especifica um aplicativo válido do Xcode. Especifique um caminho para um aplicativo do Xcode.

<a name="MT0058" />

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: O Xcode. App '\*' é inválido (o arquivo '\*' não existe).

O caminho passado usando `--sdkroot` não especifica um aplicativo válido do Xcode. Especifique um caminho para um aplicativo do Xcode.

<a name="MT0059" />

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: Não foi possível encontrar o Xcode selecionado no momento no sistema: *

<a name="MT0060" />

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: Não foi possível encontrar o Xcode selecionado no momento no sistema. 'xcode-select--print-path' retornado ' *', mas esse diretório não existe.

<a name="MT0061" />

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: Nenhum Xcode. App especificado (usando-- sdkroot), usando o sistema do Xcode conforme relatado por 'xcode-select--print-path': *

Este é um aviso informativo, explicando que Xcode será usado, já que nenhuma foi especificada.

<a name="MT0062" />

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: Nenhum Xcode. App especificado (usando-- sdkroot ou 'xcode-select--print-path'), usando o Xcode padrão em vez disso: *

Este é um aviso informativo, explicando que Xcode será usado, já que nenhuma foi especificada.

<a name="MT0063" />

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: Não é possível localizar o executável na extensão * (nenhuma entrada de CFBundleExecutable em seu Info. plist)

Cada Info. plist deve ter um executável (usando a entrada CFBundleExecutable), no entanto, uma entrada deve ser gerada automaticamente durante a compilação.

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0064" />

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: Xamarin. IOS dá suporte apenas a estruturas incorporadas com unificado de projetos.

Xamarin. IOS dá suporte apenas a estruturas incorporadas ao usar a API unificada; Atualize seu projeto para usar a API unificada.

<a name="MT0065" />

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: Xamarin. IOS dá suporte apenas a estruturas incorporadas ao destino de implantação é pelo menos 8.0 (destino de implantação atual: * inseridos estruturas: *)

Xamarin. IOS só dá suporte a estruturas incorporadas quando o destino de implantação é pelo menos 8.0 (porque as versões anteriores do iOS não oferece suporte a estruturas incorporadas).

Atualize o destino de implantação no Info. plist do projeto para 8.0 ou superior.

<a name="MT0066" />

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: Assembly do registrador de compilação inválido: *

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0067" />

### <a name="mt0067-invalid-registrar-"></a>MT0067: Registrador inválido: *

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0068" />

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: Valor inválido para a estrutura de destino: *.

Uma estrutura de destino inválido foi passada usando o argumento-- estrutura de destino. Especifique uma estrutura de destino válido.

<a name="MT0069" />

<!--### MT0069: currently unused -->

<a name="MT0070" />

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: Estrutura de destino inválido: *. Estruturas de destino válidos são: *.

Uma estrutura de destino inválido foi passada usando o argumento-- estrutura de destino. Especifique uma estrutura de destino válido.

<a name="MT0071" />

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: Plataforma desconhecida: *. Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no http://bugzilla.xamarin.com com um caso de teste.

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0072" />

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: Não há suporte para extensões para a plataforma ' *'.

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0073" />

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: Xamarin. IOS * não oferece suporte a um destino de implantação de * (o mínimo é *). Selecione um destino de implantação mais recente no Info. plist do seu projeto.

O destino de implantação mínimo é aquele especificado na mensagem de erro; Selecione um destino de implantação mais recente no Info. plist do projeto.

Se não for possível atualizar o destino de implantação, use uma versão mais antiga do xamarin. IOS.

<a name="MT0074" />

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin. IOS * não oferece suporte a um destino de implantação mínima de * (o máximo é *). Selecione um destino de implantação mais antigo no Info. plist do seu projeto ou atualizar para uma versão mais recente do xamarin. IOS.

Xamarin. IOS não dá suporte para definir o destino de implantação mínima para uma versão superior à versão que esta versão específica do xamarin. IOS foi criado para o.

Selecione um destino de implantação mínima mais antigo no Info. plist do projeto, ou atualizar para uma versão mais recente do xamarin. IOS.

<a name="MT0075" />

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: Arquitetura inválida ' *' para * projetos. Arquiteturas válidas são: *

Uma arquitetura inválida foi especificada. Verifique se a arquitetura é válida.

<a name="MT0076" />

### <a name="mt0075-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0075: Nenhuma arquitetura especificada (usando o argumento – a abi). Uma arquitetura é necessária para * projetos.

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0077" />

### <a name="mt0076-watchos-projects-must-be-extensions"></a>MT0076: Projetos do WatchOS devem ser extensões.

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0078" />

### <a name="mt0077-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0077: Builds incrementais estão habilitados com um destino de implantação < 8.0 (atualmente *). Isso não é suportado (o aplicativo resultante não será iniciado no iOS 9), portanto, o destino de implantação será definido como 8.0.

Este é um aviso informando que o destino de implantação foi definido para 8.0 para esta compilação para que as compilações incrementais funcionem corretamente.

Builds incrementais são suportados apenas quando o destino de implantação é pelo menos 8.0 (porque o aplicativo resultante não será iniciado no iOS 9 caso contrário).

<a name="MT0079" />

### <a name="mt0078-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0078: A versão recomendada do Xcode para xamarin. IOS * é Xcode * ou posterior. A versão atual do Xcode (encontrada no *) é *.

Este é um aviso informando que a versão atual do Xcode não é a versão recomendada do Xcode para esta versão do xamarin. IOS.

Atualize o Xcode para garantir o comportamento ideal.

<a name="MT0080" />

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: Desabilitando NewRefCount, --new-refcount:false, foi preterido.

Este é um aviso informando que a solicitação para desabilitar o novo refcount (– new - refcount:false) foi ignorado.

O novo recurso refcount agora é obrigatório para todos os projetos e, portanto, não é possível desabilitar o mais.

<a name="MT0081" />

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: O argumento de linha de comando – relatório de falhas de download também requer - download-falhas-relatório-to.

<a name="MT0082" />

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (– enable-repl) é suportado apenas quando a vinculação não é usada (-nolink).

<a name="MT0083" />

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: Não há suporte para somente ASM bitcode no watchOS. Use qualquer um dos bitcode –: marcador ou – bitcode: completo.

<a name="MT0084" />

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: Não há suporte para o Bitcode no simulador. Não passe – bitcode ao compilar para o simulador.

<a name="MT0085" />

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: Nenhuma referência a ' *' foi encontrado. Ele será adicionado automaticamente.

<a name="MT0086" />

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: Uma estrutura de destino (– estrutura de destino) deve ser especificada ao criar para TVOS e WatchOS.

Isso geralmente indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0087" />

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: Builds incrementais (– fastdev) não é compatível com o GC Boehm. Builds incrementais serão desabilitados.

<a name="MT0088" />

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: O GC deve estar no modo cooperativo para aplicativos watchOS. Remova o argumento-- coop: false para mtouch.

<a name="MT0089" />

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: A opção '\*'não é possível usar o valor'\*' quando o modo cooperativo está habilitado para o GC.

<a name="MT0091" />

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: Esta versão do xamarin. IOS requer o * SDK (fornecido com o Xcode *). A atualização do Xcode para obter os arquivos de cabeçalho necessários ou definir o comportamento do vinculador gerenciado para vincular somente SDKs de estruturas (para tentar evitar as novas APIs).

Xamarin. IOS requer os arquivos de cabeçalho da versão do SDK especificada na mensagem de erro, para criar seu aplicativo. A maneira recomendada para corrigir esse erro é atualizar o Xcode para obter o SDK do necessário, isso incluirá todos os arquivos de cabeçalho necessárias. Se você tiver várias versões do Xcode instalada ou quiser usar um Xcode em um local não padrão, certifique-se de definir o local correto do Xcode nas preferências do seu IDE.

Um potencial solução alternativa é habilitar o vinculador gerenciado. Isso removerá não utilizados API, incluindo, na maioria dos casos, a nova API de onde os arquivos de cabeçalho estão ausentes (ou incompletos). No entanto isso não funcionará se seu projeto usa a API que foi introduzido em um SDK mais recente daquela que o Xcode fornece.

Uma solução de última Gota seria usar uma versão mais antiga do xamarin. IOS, que dá suporte ao SDK do seu projeto requer.

<!-- MT0092 used by mlaunch -->

<a name="MT0093" />

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: Não foi possível encontrar 'mlaunch'.

<!-- MT0094 is not reported anymore -->

<a name="MT0095" />

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: Não foi possível copiar arquivos de AOT para o diretório de destino {dest}: {error}

<a name="MT0096" />

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: Nenhuma referência ao xamarin foi encontrada.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099" />

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0099: Erro interno *. Envie um relatório de bug com um caso de teste (http://bugzilla.xamarin.com).

Essa mensagem de erro é relatada quando ocorre falha na verificação de consistência interna no xamarin. IOS.

Isso indica um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0100" />

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MT0100: Destino de build do assembly inválido: ' *'. Envie um relatório de bug com um caso de teste (http://bugzilla.xamarin.com).

Essa mensagem de erro é relatada quando ocorre falha na verificação de consistência interna no xamarin. IOS.

Isso é sempre um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um caso de teste.

<a name="MT0101" />

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: O assembly ' *' está especificado várias vezes em-- argumentos de destino de compilação do assembly.

O assembly mencionado na mensagem de erro é especificado várias vezes em-- argumentos de destino de compilação do assembly. Verifique se que cada assembly é oferecida apenas uma vez.

<a name="MT0102" />

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: Dos assemblies\*'e'\*' têm o mesmo nome de destino ('\*'), os destinos, mas diferentes ('\*' e ' *').

Os assemblies mencionados na mensagem de erro têm conflitantes destinos de compilação.

Por exemplo:

    --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary

Este exemplo está tentando criar uma biblioteca dinâmica e uma estrutura com a mesma marca (`MyBinary`).

<a name="MT0103" />

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: O objeto estático '\*' contém mais de um assembly ('\*'), mas cada objeto estático deve corresponder a exatamente um assembly.

Os assemblies mencionados na mensagem de erro são compilados em um único objeto estático. Isso não é permitido, cada assembly deve ser compilada em um objeto estático diferente.

Por exemplo:

    --assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary

Este exemplo tenta construir um objeto estático (`MyBinary`) composto de dois assemblies (`Assembly1.dll` e `Assembly2.dll`), que não é permitido.

<a name="MT0105" />

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: Nenhum destino de compilação do assembly foi especificado para ' *'.

Quando especificando o assembly de build de destino usando `--assembly-build-target`, cada assembly no aplicativo deve ter um destino de compilação atribuído.

Esse erro é relatado quando o assembly mencionado na mensagem de erro não tem um assembly atribuído de destino de build.

Consulte a documentação `--assembly-build-target` para obter mais informações.

<a name="MT0106" />

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: Nome de destino de compilação do assembly '\*' é inválido: o caractere '\*' não é permitido.

O nome de destino de compilação do assembly deve ser um nome de arquivo válido.

Por exemplo, esses valores disparará esse erro:

    --assembly-build-target:Assembly1.dll=staticobject=my/path.o

porque `my/path.o` não é um nome de arquivo válida devido ao caractere de separador de diretório.

<a name="MT0107" />

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: Dos assemblies\*' têm diferentes otimizações de LLVM personalizadas (\*), que não é permitido quando eles são compilados em um único binário.

<a name="MT0108" />

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: O assembly de destino de build ' *' não correspondeu a todos os assemblies.

<a name="MT0109" />

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: O assembly '{0}' foi carregado de um caminho diferente do caminho fornecido (fornecido caminho: {1}, o caminho real: {2}).

Isso é um aviso indicando que um assembly referenciado pelo aplicativo foi carregado de um local diferente que solicitado.

Isso pode significar que o aplicativo faz referência a vários assemblies com o mesmo nome, mas em locais diferentes, que podem levar a resultados inesperados (somente o primeiro conjunto será usado).

<a name="MT0110" />

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: Builds incrementais foram desabilitados porque esta versão do xamarin. IOS não dá suporte a compilações incrementais em projetos que incluem bibliotecas de associação de terceiros e que é compilado em bitcode.

Builds incrementais foram desabilitados porque esta versão do xamarin. IOS não dá suporte a compilações incrementais em projetos que incluem bibliotecas de associação de terceiros e que é compilado em bitcode (projetos tvOS e watchOS).

Nenhuma ação é necessária de sua parte, esta mensagem é meramente informativa.

Para obter mais informações, consulte bug n º[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Esse aviso não é relatado mais.

<a name="MT0111" />

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: Bitcode foi habilitada porque esta versão do xamarin. IOS não dá suporte a construção do watchOS projetos que usam LLVM sem habilitar bitcode.

Bitcode foi habilitada automaticamente porque esta versão do xamarin. IOS não dá suporte a construção do watchOS projetos que usam LLVM sem habilitar bitcode.

Nenhuma ação é necessária de sua parte, esta mensagem é meramente informativa.

Para obter mais informações, consulte bug n º[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112" />

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: Compartilhamento de código nativo foi desabilitada porque *

Há vários motivos para compartilhamento de código pode ser desabilitado:

* como o destino de implantação do aplicativo de contêiner é anterior ao iOS 8.0 (ele tem *)).

Compartilhamento de código nativo requer iOS 8.0 como o compartilhamento de código nativo é implementado usando estruturas de usuário, que foi introduzido com o iOS 8.0.

* como o aplicativo de contêiner inclui I18N assemblies (*).

Compartilhamento de código nativo no momento, não há suporte se o aplicativo de contêiner inclui assemblies I18N.

* porque o aplicativo de contêiner tem definições de xml personalizado para o vinculador gerenciado (*).

Compartilhamento de código nativo exige que não há suporte para projetos que usam definições de xml personalizado para o vinculador gerenciado.

<a name="MT0113" />

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: Compartilhamento de código nativo foi desabilitada para a extensão ' *' como *.

* como as opções de bitcode diferem entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que as opções de bitcode correspondem entre os projetos que compartilham código.

* porque--assembly-build-target opções são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que--assembly-build-target as opções são idênticas entre os projetos que compartilham código.

  Essa condição pode ocorrer se a compilações incrementais não estão habilitadas ou desabilitadas em todos os projetos.

* porque os assemblies I18N são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  Compartilhamento de código nativo não há suporte para extensões que incluem I18N assemblies no momento.

* como os argumentos para o compilador AOT são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo exige que os argumentos para o compilador AOT não diferem entre os projetos que compartilham código.

* como os outros argumentos para o compilador AOT são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo exige que os argumentos para o compilador AOT não diferem entre os projetos que compartilham código.

  Essa condição ocorre se o 'executar todas as operações de float de 32 bits como float de 64 bits' difere entre projetos.

* porque LLVM não está habilitado ou desabilitado no aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que LLVM está habilitado ou desabilitado para todos os projetos que compartilham código.

* como as configurações do vinculador gerenciados são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que as configurações de vinculador gerenciado são idênticas para todos os projetos que compartilham código.

* porque os assemblies foi ignorados para o vinculador gerenciado são diferentes entre o aplicativo de contêiner (\*) e a extensão (\*).

  O compartilhamento de código nativo requer que as configurações de vinculador gerenciado são idênticas para todos os projetos que compartilham código.

* porque a extensão tem definições de xml personalizado para o vinculador gerenciado (*).

  Compartilhamento de código nativo exige que não há suporte para projetos que usam definições de xml personalizado para o vinculador gerenciado.

* porque não compila o aplicativo de contêiner para a ABI * (enquanto a extensão está criando para este ABI).

  O compartilhamento de código nativo requer que o aplicativo de contêiner cria para todas as arquiteturas de qualquer extensão de aplicativo cria para.

  Por exemplo: essa condição ocorre quando uma extensão cria para ARMv7 + ARM64, mas só compila o aplicativo de contêiner para ARM64.

* porque o aplicativo de contêiner está compilando para a ABI \*, que não é compatível com ABI da extensão (\*).

  O compartilhamento de código nativo requer que todos os projetos de compilação para a API exatamente igual.

  Por exemplo: essa condição ocorre quando uma extensão cria para ARMv7 + llvm + thumb2, mas só compila o aplicativo de contêiner para ARMv7 + llvm.

* porque o aplicativo de contêiner está fazendo referência ao assembly '\*'de'\*', enquanto a extensão faz referência a uma versão diferente do ' *'.

  O compartilhamento de código nativo requer que todos os projetos que compartilham o código usam as mesmas versões para todos os assemblies.

<!-- MT0114: used by mmp -->

<a name="MT0115" />

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: É recomendável fazer referência a símbolos dinâmicos usando o código (– modo de símbolo dinâmico = código) quando o bitcode é habilitado.

Projetos xamarin. IOS geralmente referenciará símbolos nativa dinamicamente, que significa que o vinculador nativo pode remover esses símbolos nativos durante o processo de vinculação nativo, pois o vinculador nativo não verá que esses símbolos são usados.

Normalmente, xamarin. IOS solicitará o vinculador nativo para manter esses símbolos (usando o `-u symbol` sinalizador de vinculador), mas quando compilar o bitcode o vinculador nativo não aceita o `-u` sinalizador.

Xamarin. IOS tiver implementado uma solução alternativa: geramos o código nativo extra que faz referência a esses símbolos, e, portanto, o vinculador nativo verá que esses símbolos são usados. Isso é feito automaticamente durante a compilação para bitcode.

Se `--dynamic-symbol-mode=linker` é passado para o mtouch, nesse alternativo solução será desabilitada e xamarin. IOS tentar passar `-u` para o vinculador nativo. Isso provavelmente resultará em erros de vinculador nativo.

A solução é remover o `--dynamic-symbol-mode=linker` argumento dos argumentos adicionais do mtouch nas opções de compilação do projeto.

<!-- 0116 - 0124: free to use -->

<a name="MT0116" />

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: Arquitetura inválida: {arch}. Não há suporte para arquiteturas de 32 bits quando o destino de implantação é 11 ou posterior. Verifique se que o projeto não será compilado para uma arquitetura de 32 bits.

iOS 11 não há suporte para aplicativos de 32 bits, portanto, não há suporte para compilar para um aplicativo de 32 bits quando o destino de implantação é iOS 11 ou posterior.

Altere a arquitetura de destino nas opções de compilação do projeto iOS para arm64, ou alterar o destino de implantação no Info. plist do projeto para uma versão anterior do iOS.

<a name="MT0117" />

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: Não é possível iniciar um aplicativo de 32 bits em um simulador que só oferece suporte a 64 bits.

<a name="MT0118" />

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: Não foi possível encontrar o AOT arquivos no diretório esperado '{msymdir}'.

<!-- 0119 - 0123: free to use -->

<a name="MT0123" />

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: O assembly executável * não faz referência a *.

Nenhuma referência foi encontrada para o assembly de plataforma (xamarin / Xamarin.TVOS.dll / Xamarin.WatchOS.dll) no assembly executável.

Isso geralmente acontece quando não há nenhum código no projeto executável que usa qualquer coisa, desde o assembly de plataforma. Por exemplo, um método Main vazio (e nenhum outro código) mostraria este erro:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

Usando uma API do assembly de plataforma para resolver o erro:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124" />

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: Não foi possível definir o idioma atual para '{lang}' (de acordo com a LANG = {LANG}): {exception}

Este é um aviso, indicando que o idioma atual não pode ser definido para a linguagem na mensagem de erro.

O idioma atual padrão será o idioma do sistema.

<a name="MT0125" />

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125: --Assembly-build-target argumento de linha de comando será ignorado no simulador.

Nenhuma ação é necessária, essa mensagem é meramente informativa.

<a name="MT0126" />

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: Builds incrementais foram desabilitados porque não há suporte para builds incrementais no simulador.

Nenhuma ação é necessária, essa mensagem é meramente informativa.

<a name="MT0127" />

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: Builds incrementais foram desabilitados porque esta versão do xamarin. IOS não dá suporte a compilações incrementais em projetos que incluem bibliotecas de associação de mais de um terceiro.

Builds incrementais foram desabilitados automaticamente porque esta versão do xamarin. IOS não sempre compila projetos com várias bibliotecas de terceiros associação corretamente.

Nenhuma ação é necessária, essa mensagem é meramente informativa.

Para obter mais informações, consulte bug n º[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128" />

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: Não foi possível tocar o arquivo ' *': *

Ocorreu uma falha ao tocar em um arquivo (que é feito para garantir que as compilações parciais sejam realizadas corretamente).

Provavelmente, esse aviso pode ser ignorado; em caso de problemas, registre um bug (https://bugzilla.xamarin.com] (https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)) e ser investigado.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: Mensagens de erro relacionadas do projeto

### <a name="mt10xx-installer--mtouch"></a>MT10xx: Instalador / mtouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001" />

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: Não foi possível localizar um aplicativo no diretório especificado

<a name="MT1002" />

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: Não foi possível criar links simbólicos, os arquivos foram copiados

<a name="MT1003" />

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: Não foi possível eliminar o aplicativo ' *'. Talvez você precise interromper o aplicativo manualmente.

<a name="MT1004" />

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: Não foi possível obter a lista de aplicativos instalados.

<a name="MT1005" />

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: Não foi possível eliminar o aplicativo '\*'no dispositivo'\*': *-talvez você precise interromper o aplicativo manualmente.

<a name="MT1006" />

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: Não foi possível instalar o aplicativo '\*'no dispositivo'\*': *.

<a name="MT1007" />

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: Falha ao iniciar o aplicativo '\*'no dispositivo'\*': *. Você ainda pode iniciar o aplicativo manualmente ao tocar nele.

<a name="MT1008" />

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: Falha ao iniciar o simulador

Esse erro será relatado se mtouch Falha ao iniciar o simulador.   Isso pode acontecer, às vezes, porque já existe um processo de simulador obsoletos ou inativo em execução.

O seguinte comando emitido na linha de comando Unix pode ser usado para interromper os processos de simulador paralisado:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009" />

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: Não foi possível copiar o assembly '\*'para'\*': *

Esse é um problema conhecido em determinadas versões do xamarin. IOS.

Se isso ocorrer para você, experimente a seguinte solução alternativa:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

No entanto, uma vez que esse problema foi resolvido na versão mais recente do xamarin. IOS, envie um novo bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com suas informações de versão completo e o log de build.

<a name="MT1010" />

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: Não foi possível carregar o assembly ' *': *

<a name="MT1011" />

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: Não foi possível adicionar o arquivo de recurso ausente: ' *'

<a name="MT1012" />

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: Falha ao listar os aplicativos no dispositivo ' *': *

<a name="MT1013" />

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: Erro de acompanhamento de dependência: nenhum arquivo a ser comparado. Envie um relatório de bug no http://bugzilla.xamarin.com com um caso de teste.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com caes um teste.

<a name="MT1014" />

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: Falha ao usar novamente a versão em cache do ' *': *.

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Falha ao criar o executável ' *': *

<a name="MT1015" />

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: Falha ao criar o executável ' *': *

<a name="MT1016" />

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: Falha ao criar o arquivo NOTICE porque já existe um diretório com o mesmo nome.

Remover o diretório `NOTICE` do projeto.

<a name="MT1017" />

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: Falha ao criar o arquivo NOTICE: *.

<a name="MT1018" />

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: Seu aplicativo verificações de assinatura de código com falha e não pôde ser instalado no dispositivo ' *'. Verifique os certificados, perfis, o provisionamento e ids de pacote. Provavelmente seu dispositivo não é parte do perfil de provisionamento selecionado (erro: 0xe8008015).

<a name="MT1019" />

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: Seu aplicativo tenha direitos para seu perfil de provisionamento atual não tem suportados e não pôde ser instalado no dispositivo ' *'. Verifique se o Log do dispositivo do iOS para obter informações mais detalhadas (erro: 0xe8008016).

Isso pode acontecer se:

* Seu aplicativo tem os direitos que o perfil de provisionamento atual não oferece suporte.
  Soluções possíveis:
  - Especificar um perfil de provisionamento diferente que suporta os direitos do seu aplicativo precisa.
  - Remova os direitos que não tem suportados no perfil de provisionamento atual.
* O dispositivo que você está tentando implantar não está incluído no perfil de provisionamento que você está usando.
  Soluções possíveis:
  - Criar um novo aplicativo de um modelo no Xcode, selecione o mesmo perfil de provisionamento e implantar no mesmo dispositivo. Às vezes, Xcode pode atualizar automaticamente os perfis de provisionamento com novos dispositivos (em outros casos que Xcode solicitará que você o que fazer).
  -Vá para o Centro de desenvolvimento do iOS e atualizar o perfil de provisionamento com o novo dispositivo, baixe o perfil de provisionamento atualizado no seu computador.

Na maioria dos casos para obter mais informações sobre a falha serão impresso para o Log do dispositivo iOS, que pode ajudar a diagnosticar o problema.

<a name="MT1020" />

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: Falha ao iniciar o aplicativo '\*'no dispositivo'\*': *

<a name="MT1021" />

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: Não foi possível copiar o arquivo '\*'para'\*': {2}

Não foi possível copiar um arquivo. A mensagem de erro da operação de cópia tem mais informações sobre o erro.

<a name="MT1022" />

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: Não foi possível copiar o diretório '\*'para'\*': {2}

Não foi possível copiar um diretório. A mensagem de erro da operação de cópia tem mais informações sobre o erro.

<a name="MT1023" />

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: Não foi possível se comunicar com o dispositivo para localizar o aplicativo ' *': *

Ocorreu um erro ao tentar pesquisar um aplicativo no dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.

<a name="MT1024" />

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: Não foi possível verificar a assinatura do aplicativo no dispositivo ' *'. Certifique-se de que o perfil de provisionamento está instalado e não expirado (erro: 0xe8008017).

O dispositivo rejeitou a instalação do aplicativo porque a assinatura não pôde ser verificada.

Certifique-se de que o perfil de provisionamento está instalado e não expirou.

<a name="MT1025" />

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: Não foi possível listar os relatórios de falha no dispositivo *.

Ocorreu um erro ao tentar listar os relatórios de falha no dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1026" />

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: Não foi possível baixar o relatório de falha * do dispositivo *.

Ocorreu um erro ao tentar baixar os relatórios de falha do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1027" />

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: Não é possível usar o Xcode 7 + para iniciar aplicativos em dispositivos com iOS * (Xcode 7 só dá suporte ao iOS 6 +).

Não é possível usar o Xcode 7 + para iniciar aplicativos em dispositivos com iOS versão abaixo 6.0.

Use uma versão mais antiga do Xcode, ou toque no aplicativo manualmente para iniciá-lo.

<a name="MT1028" />

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: A especificação de dispositivo inválido: ' *'. Esperado 'ios', 'watchos' ou 'all'.

A especificação do dispositivo passado usando-- dispositivo é inválido. Os valores válidos são: 'ios', 'watchos' ou 'all'.

<a name="MT1029" />

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: Não foi possível localizar um aplicativo no diretório especificado: *

O caminho do aplicativo passado para launchdev – não existe. Especifique um pacote de aplicativo válido.

<a name="MT1030" />

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: Inicialização de aplicativos no dispositivo usando um identificador de pacote foi preterido. Passe o caminho completo para o pacote para iniciar.

É recomendável passar o caminho para o aplicativo ser iniciado no dispositivo, em vez de apenas a id do pacote.

<a name="MT1031" />

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: Não foi possível iniciar o aplicativo '\*'no dispositivo'\*' porque o dispositivo está bloqueado. . Desbloquear o dispositivo e tente novamente.

. Desbloquear o dispositivo e tente novamente.

<a name="MT1032" />

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: Esse executável de aplicativo pode ser muito grande (* MB) para executar no dispositivo. Se o bitcode foi habilitado você talvez queira desabilitá-lo para o desenvolvimento, só é necessário para enviar aplicativos para a Apple.

<a name="MT1033" />

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: Não foi possível desinstalar o aplicativo '\*'do dispositivo'\*': *

<!-- 1034 used by mmp -->

<a name="MT1035" />

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: Não é possível incluir versões diferentes do framework '{name}'

Não é possível vincular com diferentes versões da mesma estrutura.

Isso geralmente acontece quando uma extensão faz referência a uma versão diferente de uma estrutura que o aplicativo de contêiner (possivelmente por meio de um assembly de associação de terceiros).

Após esse erro haverá vários [MT1036](#MT1036) erros listando os caminhos para cada estrutura diferente.

<a name="MT1036" />

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: Framework '{name}' incluída no: {caminho} (relacionado ao erro anterior)

Esse erro é relatado apenas junto com [MT1036](#MT1036). Consulte [MT1036](#MT1036) para obter mais informações.

### <a name="mt11xx-debug-service"></a>MT11xx: Depurar o serviço

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101" />

### <a name="mt1101-could-not-start-app"></a>MT1101: Não foi possível iniciar o aplicativo

<a name="MT1102" />

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: Não foi possível anexar ao aplicativo (para eliminá-lo): *

<a name="MT1103" />

### <a name="mt1103-could-not-detach"></a>MT1103: Não foi possível desanexar

<a name="MT1104" />

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: Falha ao enviar o pacote: *

<a name="MT1105" />

### <a name="mt1105-unexpected-response-type"></a>MT1105: Tipo de resposta inesperado

<a name="MT1106" />

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: Não foi possível obter a lista de aplicativos no dispositivo: Solicitação atingiu o tempo limite.

<a name="MT1107" />

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: Falha ao iniciar o aplicativo: *

Verifique se o dispositivo estiver bloqueado.

Se você estiver implantando um aplicativo empresarial ou usando um perfil de provisionamento gratuito, você pode ter o desenvolvedor de confiança (Isso é explicado <a href="https://stackoverflow.com/a/30726375/183422">aqui</a>).

<a name="MT1108" />

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: Não foi possível encontrar as ferramentas de desenvolvedor para este dispositivo XX (AA).

Algumas operações do mtouch exigem o <tt>DeveloperDiskImage.dmg</tt> arquivo esteja presente.   Esse arquivo é parte do Xcode e normalmente está localizado em relação ao SDK que você está usando para compilar em relação, além de <tt>Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg</tt>.

Esse erro pode ocorrer qualquer um, porque você não tem um DeveloperDiskImage.dmg que corresponde ao dispositivo que você se conectou.

<a name="MT1109" />

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: Aplicativo falha ao iniciar porque o dispositivo está bloqueado. . Desbloquear o dispositivo e tente novamente.

Verifique se o dispositivo estiver bloqueado.

<a name="MT1110" />

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: Aplicativo falha ao iniciar devido a restrições de segurança do iOS. Certifique-se de que o desenvolvedor é confiável.

Se você estiver implantando um aplicativo empresarial ou usando um perfil de provisionamento gratuito, você pode ter o desenvolvedor de confiança (Isso é explicado <a href="https://stackoverflow.com/a/30726375/183422">aqui</a>).

<a name="MT1111" />

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: Aplicativo foi iniciado com êxito, mas ele não 's possível aguardar o aplicativo Sair conforme solicitado porque ele não é possível detectar o encerramento do aplicativo ao iniciar usando o gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: Simulador

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201" />

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: Não foi possível carregar o simulador: *

<a name="MT1202" />

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: Configuração de simulador inválidos: *

<a name="MT1203" />

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: Especificação de simulador inválidos: *

<a name="MT1204" />

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: Especificação de simulador inválido ' *': não especificado de tempo de execução.

<a name="MT1205" />

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: Especificação de simulador inválido ' *': tipo de dispositivo não especificado.

<a name="MT1206" />

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: Não foi possível localizar o tempo de execução do simulador ' *'.

<a name="MT1207" />

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: Não foi possível encontrar o tipo de dispositivo do simulator ' *'.

<a name="MT1208" />

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: Não foi possível localizar o tempo de execução do simulador ' *'.

<a name="MT1209" />

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: Não foi possível encontrar o tipo de dispositivo do simulator ' *'.

<a name="MT1210" />

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: Especificação de simulador inválidos: \*, chave desconhecida '\*'

<a name="MT1211" />

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: O simulator versão '\*'não oferece suporte para o tipo de simulador'\*'

<a name="MT1212" />

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: Falha ao criar uma versão de simulador onde digitar = * tempo de execução e = *.

<a name="MT1213" />

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: Especificação de simulador inválidos para o Xcode 4: *

<a name="MT1214" />

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: Especificação de simulador inválidos para o Xcode 5: *

<a name="MT1215" />

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: SDK inválido especificado: *

<a name="MT1216" />

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: Não foi possível encontrar o simulador UDID ' *'.

<a name="MT1217" />

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: Não foi possível carregar o pacote de aplicativo em ' *'.

<a name="MT1218" />

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: Nenhum identificador de pacote encontrado no aplicativo no ' *'.

<a name="MT1219" />

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: Não foi possível encontrar o simulador para ' *'.

<a name="MT1220" />

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: Não foi possível localizar o tempo de execução mais recente do simulador de dispositivo ' *'.

Isso geralmente indica um problema com o Xcode.

Coisas a tentar corrigir esse problema:

* Use o simulador de uma vez no Xcode.
* Passar uma versão do SDK explícita usando o sdk – <version>.
* Reinstale o Xcode.

<a name="MT1221" />

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: Não foi possível encontrar o simulador do iPhone emparelhado para o simulador WatchOS ' *'.

Ao iniciar um aplicativo do WatchOS em um simulador WatchOS, deve haver um par de iOS Simulator também.

Assista simuladores podem ser emparelhadas com iOS simuladores usando a interface do usuário de dispositivos do Xcode (menu Janela -> dispositivos).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301" />

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: Biblioteca nativa `*` (\*) foi ignorada, pois ele não coincide com o architecture(s) build atual (\*)

<a name="MT1302" />

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: Não foi possível extrair a biblioteca nativa ' *' de '+'. Certifique-se de que a biblioteca nativa foi inserida corretamente no assembly gerenciado (se o assembly foi compilado usando um projeto de associação, a biblioteca nativa deve ser incluída no projeto e sua ação de compilação deve ser 'ObjcBindingNativeLibrary').

<a name="MT1303" />

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: Não foi possível descompactar a estrutura nativa '\*'de'\*'. Examine o log de compilação para obter mais informações do comando nativo 'zip'.

Não foi possível descompactar o framework nativo especificado da biblioteca de ligação.

Examine o log de bulid para obter mais informações sobre a falha do comando nativo 'zip'.

<a name="MT1304" />

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: A estrutura inserida ' *' no * é inválido: ele não contém um Info. plist.

A estrutura inserida especificada não contém um Info. plist e, portanto, não é uma estrutura válida.

Verifique se que a estrutura é válida.

<a name="MT1305" />

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: A biblioteca de associação '\*' contém uma estrutura de usuários (\*), mas as estruturas de usuário inserido requerem iOS 8.0 (é o destino da implantação atual *). Defina o destino de implantação no arquivo Info. plist para pelo menos 8.0.

A biblioteca de associação especificada contém uma estrutura inserida, mas o xamarin. IOS dá suporte apenas a estruturas incorporadas no iOS 8.0 ou posterior.

Defina o destino de implantação no arquivo Info. plist para pelo menos 8.0 para resolver esse erro (ou não use estruturas incorporadas).

### <a name="mt14xx-crash-reports"></a>MT14xx: Relatórios de falha

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400" />

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: Não foi possível abrir o serviço de relatório de falhas: AFCConnectionOpen retornado *

Ocorreu um erro ao tentar acessar os relatórios de falhas do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1401" />

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: Não foi possível fechar o serviço de relatório de falhas: AFCConnectionClose retornado *

Ocorreu um erro ao tentar acessar os relatórios de falhas do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1402" />

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: Não foi possível ler informações do arquivo para *: AFCFileInfoOpen retornado *

Ocorreu um erro ao tentar acessar os relatórios de falhas do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1403" />

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: Não foi possível ler o relatório de falhas: AFCDirectoryOpen (*) retornado: *

Ocorreu um erro ao tentar acessar os relatórios de falhas do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1404" />

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: Não foi possível ler o relatório de falhas: AFCFileRefOpen (*) retornado: *

Ocorreu um erro ao tentar acessar os relatórios de falhas do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1405" />

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: Não foi possível ler o relatório de falhas: AFCFileRefRead (*) retornado: *

Ocorreu um erro ao tentar acessar os relatórios de falhas do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<a name="MT1406" />

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: Não foi possível listar os relatórios de falhas: AFCDirectoryOpen (*) retornado: *

Ocorreu um erro ao tentar acessar os relatórios de falhas do dispositivo.

Coisas a tentar resolver isso:

* Exclua o aplicativo do dispositivo e tente novamente.
* Desconecte o dispositivo e reconectá-lo.
* Reinicie o dispositivo.
* Reinicialize o Mac.
* Sincronize o dispositivo com o iTunes (Isso removerá quaisquer relatórios de falha do dispositivo).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600" />

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: Não uma Mach-O biblioteca dinâmica (cabeçalho desconhecido '0 x *'): *.

Ocorreu um erro ao processar a biblioteca dinâmica em questão.

Verifique se que a biblioteca dinâmica é uma biblioteca dinâmica de Mach-O válido.

O formato de uma biblioteca pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/library.dylib

<a name="MT1601" />

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: Não é uma biblioteca estática (cabeçalho desconhecido ' *'): *.

Ocorreu um erro ao processar a biblioteca estática em questão.

Verifique se que a biblioteca estática é uma biblioteca estática de Mach-O válido.

O formato de uma biblioteca pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/library.a

<a name="MT1602" />

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: Não uma Mach-O biblioteca dinâmica (cabeçalho desconhecido '0 x *'): *.

Ocorreu um erro ao processar a biblioteca dinâmica em questão.

Verifique se que a biblioteca dinâmica é uma biblioteca dinâmica de Mach-O válido.

O formato de uma biblioteca pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/library.dylib

<a name="MT1603" />

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: Formato desconhecido para entrada fat na posição * em *.

Ocorreu um erro ao processar o arquivo morto fat em questão.

Verifique se que o arquivo fat é válido.

O formato de um arquivo fat pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/file

<a name="MT1604" />

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: Arquivo do tipo * não é um arquivo MachO (*).

Ocorreu um erro ao processar o arquivo MachO em questão.

Verifique se que o arquivo é uma biblioteca de dinâmico válida Mach-O.

O formato de um arquivo pode ser verificado usando o `file` comando em um terminal:

    file -arch all -l /path/to/file

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: Mensagens de erro do vinculador

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001" />

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: Não foi possível vincular assemblies

Esse erro significa que o vinculador gerenciado encontrou um erro inesperado, por exemplo, uma exceção e não foi possível concluir ou salve o assembly que está sendo processado. Para obter mais informações sobre o erro exato farão parte do log de compilação, por exemplo

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

É importante registrar um relatório de bug para esses problemas. Em muitos casos, uma solução alternativa pode ser fornecida até que uma correção apropriada seja publicada. As informações acima são fundamental (junto com um caso de teste e/ou o assembly binairy) resolver o problema.

<a name="MT2002" />

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: Não é possível resolver a referência: *

<a name="MT2003" />

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: Opção ' *' será ignorado, pois a vinculação está desabilitada

<a name="MT2004" />

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: Arquivo de definições de vinculador extra ' *' não pôde ser localizado.

<a name="MT2005" />

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: Definições de ' *' não pôde ser analisado.

<a name="MT2006" />

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: Não é possível carregar o mscorlib. dll do: *. Reinstale o xamarin. IOS.

Isso geralmente indica que há um problema com a instalação do xamarin. IOS. Tente reinstalar o xamarin. IOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010" />

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: O HttpMessageHandler desconhecido `*`. Os valores válidos são HttpClientHandler (padrão), CFNetworkHandler ou NSUrlSessionHandler

<a name="MT2011" />

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: TlsProvider desconhecido `*`.  Os valores válidos são default ou appletls.

O valor fornecido para `tls-provider=` não é um provedor válido do TLS (Transport Layer Security).

O `default` e `appletls` são os únicos valores válidos e ambos representam a mesma opção é fornecer suporte a SSL/TLS usando a API de TLS nativo da Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015" />

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: O HttpMessageHandler inválido `*` para watchOS. O único valor válido é NSUrlSessionHandler.

Este é um aviso que ocorre porque o arquivo de projeto especifica um HttpMessageHandler inválido.

Versões anteriores das nossas ferramentas de visualização gerado por padrão um valor inválido no arquivo de projeto.

Para corrigir este aviso, abra o arquivo de projeto em um editor de texto e remova todos os nós de HttpMessageHandler do XML.

<a name="MT2016" />

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: TlsProvider inválido `legacy` opção. O único valor válido `appletls` será usado.

O `legacy` provedor, que era uma SSLv3 totalmente gerenciado / TLSv1 único provedor, não é fornecido com o xamarin. IOS mais. Projetos que estavam usando esse provedor antigo e agora Compile com a mais recente `appletls` um.

Para corrigir este aviso, abra o arquivo de projeto em um editor de texto e remova todos os ' MtouchTlsProvider ' nós do XML.

<a name="MT2017" />

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: Não foi possível processar a descrição de XML.

Isso significa que há um erro no [arquivo de configuração de vinculador XML personalizado](https://developer.xamarin.com/guides/cross-platform/advanced/custom_linking/) você forneceu, examine o arquivo.

<a name="MT2018" />

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: O assembly '\*' é referenciado em dois locais diferentes: '\*' e ' *'.

O assembly mencionado na mensagem de erro é carregado de vários locais. Certifique-se sempre usar a mesma versão de um assembly.

<a name="MT2019" />

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: Não é possível carregar o assembly raiz ' *'

Não foi possível carregar o assembly raiz. Verifique se que o caminho na mensagem de erro se refere a um arquivo existente, e que se trata de um assembly .NET válido.

<a name="MT202x" />

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: Falha ao associar o otimizador de processamento `...`.

Algo inesperado ocorreu ao tentar otimizar gerou o código de associação. O elemento que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly chamado (ou que contém o tipo ou o método chamado) precisará ser fornecido em uma [relatório de bugs](http://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **adicionais do mtouch argumentos**).

O último dígito `x` será:
* `0` para um nome de assembly;
* `1` para um nome de tipo;
* `3` para um nome de método;

<a name="MT2030" />

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: Remover recursos do usuário falha no processamento `...`.

Algo inesperado ocorreu ao tentar remover recursos do usuário. O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](http://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

Recursos do usuário são incluídos em assemblies (como recursos) que precisa ser extraído no momento da compilação, para criar o pacote de aplicativo de arquivos. Isso inclui:

* `__monotouch_content_*` e `__monotouch_pages_*` recursos; e
* Bibliotecas nativas incorporados em um assembly de associação;

<a name="MT2040" />

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: Padrão HttpMessageHandler setter falha no processamento `...`.

Algo inesperado ocorreu ao tentar definir o padrão `HttpMessageHandler` para o aplicativo. Envie uma [relatório de bugs](http://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2050" />

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: Removedor de código falha no processamento `...`.

Algo inesperado ocorreu ao tentar remover o código de envio com o aplicativo de BCL. Envie uma [relatório de bugs](http://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2060" />

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: Sealer falha no processamento `...`.

Algo inesperado ocorreu ao tentar lacrar tipos ou métodos (final) ou quando devirtualizing alguns métodos. O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](http://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2070" />

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: Metadados Redutor falha no processamento `...`.

Algo inesperado ocorreu ao tentar reduzir os metadados do aplicativo. O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](http://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2080" />

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: MarkNSObjects falha no processamento `...`.

Algo inesperado ocorreu ao tentar marcar `NSObject` subclasses do aplicativo. O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](http://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2090" />

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: Inliner falha no processamento `...`.

Algo inesperado ocorreu ao tentar o código embutido do aplicativo. O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](https://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100" />

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: Preservar inteligente de conversão de Enum falha no processamento `...`.

Algo inesperado ocorreu ao tentar marcar os métodos de conversão para enums inteligente do aplicativo. O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](https://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2101" />

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: Não é possível resolver a referência '\*', referenciada do método'\*' em ' *'.

Uma referência de assembly inválido foi encontrada ao processar o método mencionado na mensagem de erro.

O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](https://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2102" />

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: Erro ao processar o método '\*'no assembly'\*': *

Algo inesperado ocorreu ao tentar marcar o método mencionado na mensagem de erro.

O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](https://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2103" />

### <a name="mt2103-error-processing-assembly--"></a>MT2103: Erro ao processar o assembly '\*': *

Erro inesperado durante o processamento de um assembly.

O assembly que está causando o problema é chamado na mensagem de erro. Para corrigir esse problema assembly precisará ser fornecido em uma [relatório de bugs](https://bugzilla.xamarin.com) junto com um log de compilação completa, com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais do mtouch**).

<a name="MT2104" />

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Não foi possível vincular o assembly '{0}' como ele é o modo misto.

Assemblies de modo misto não podem ser processados pelo vinculador.

Consulte https://msdn.microsoft.com/library/x0w2664k.aspx para obter mais informações sobre assemblies de modo misto.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: Mensagens de erro AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001" />

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: Poderia AOT o assembly ' *'

Isso geralmente indica um bug no compilador AOT. Envie um bug [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS) com um projeto que pode ser usado para reproduzir o erro.

Às vezes, é possível contornar esse problema desabilitando as compilações incrementais na opção de Build do iOS do projeto (mas ainda é um bug, portanto, reporte-o assim mesmo).

<a name="MT3002" />

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-httpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbackshttpsdeveloperxamarincomguidesiosadvancedtopicslimitationsreversecallbacks"></a>MT3002: Restrição de AOT: Método ' *' deve ser estático, pois ela será decorada com [MonoPInvokeCallback]. Consulte [https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](https://developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks)

Essa mensagem de erro vem do compilador do AOT.

<a name="MT3003" />

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: Conflitantes – opções de depuração e – llvm. Soft-depuração está desabilitada.

Não há suporte para a depuração quando LLVM está habilitado. Se você precisar depurar o aplicativo, desabilite LLVM primeiro.

<a name="MT3004" />

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: Poderia AOT o assembly ' *' porque ele não existe.

<a name="MT3005" />

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: A dependência '\*'do assembly'\*' não foi encontrado. Examine as referências do projeto.

Isso normalmente ocorre quando um assembly faz referência a outra versão de um assembly de plataforma (normalmente, a versão 4 do .NET de mscorlib. dll).

Isso não é suportado e pode não compilar ou executar corretamente (o assembly possa usar o API da versão 4 do .NET de mscorlib. dll que não tem a versão do xamarin. IOS).

<a name="MT3006" />

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: Não foi possível calcular um mapa de dependência completa para o projeto. Isso resultará em tempos de build mais lentos porque o xamarin. IOS não pode detectar corretamente o que precisa ser reconstruído (e o que não precisa ser recriado). Examine os avisos anteriores para obter mais detalhes.

 Criar ou executar corretamente (o assembly possa usar o API da versão 4 do .NET de mscorlib. dll que não tem a versão do xamarin. IOS).

<a name="MT3007" />

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: Arquivos de informações de depuração (*. mdb) não serão carregados quando llvm está habilitado.

<a name="MT3008" />

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: O suporte de Bitcode requer o uso de back-end de LLVM AOT (– llvm)

O suporte de Bitcode requer o uso de back-end de LLVM AOT (– llvm).

Desabilite o suporte de Bitcode ou habilitar LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: Mensagens de erro de geração de código

### <a name="mt40xx-main"></a>MT40xx: Principal

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001" />

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: O modelo principal não pode ser expandido para `*`.

Ocorreu um erro ao gerar main.m. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4002" />

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: Falha ao compilar o código gerado para métodos P/Invoke. Envie um relatório de bug no http://bugzilla.xamarin.com

Falha ao compilar o código gerado para métodos P/Invoke. Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt41xx-registrar"></a>MT41xx: Registrador

<!--
  MT41xx registrar.m
  -->

<a name="MT4101" />

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: O registrador não é possível criar uma assinatura para o tipo `*`.

Um tipo foi encontrado no API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acredita que o xamarin. IOS deve dar suporte para o tipo em questão, envie uma solicitação de aprimoramento no [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4102" />

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: O registrador encontrado um tipo inválido `*` na assinatura para o método `*`. Use `*` em seu lugar.

Isso atualmente só acontece com um tipo: System.DateTime. Use o equivalente do Objective-C (NSDate).

<a name="MT4103" />

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: O registrador encontrado um tipo inválido `*` na assinatura para o método `*`: O tipo implementa INativeObject, mas não tem um construtor que aceita dois (IntPtr, bool) argumentos

Isso ocorre quando o registrador de encontrar um tipo em uma assinatura com as características mencionadas. O registrador talvez seja necessário criar novas instâncias do tipo e, nesse caso, ele exige um construtor com o (IntPtr, bool) assinatura - o primeiro argumento (IntPtr) Especifica o identificador gerenciado, enquanto o segundo se o chamador passa a propriedade do nativo manipule (se esse valor for false 'reter' será chamado no objeto).

<a name="MT4104" />

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: O registrador não é possível empacotar o valor para o tipo de retorno `*` na assinatura para o método `*`.

Um tipo foi encontrado no API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acredita que o xamarin. IOS deve dar suporte para o tipo em questão, envie uma solicitação de aprimoramento no [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4105" />

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: O registrador não pode realizar marshaling para o parâmetro de tipo `*` na assinatura para o método `*`.

Se você acredita que o xamarin. IOS deve dar suporte para o tipo em questão, envie uma solicitação de aprimoramento no [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4106" />

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: O registrador não é possível empacotar o valor de retorno para a estrutura `*` na assinatura para o método `*`.

Um tipo foi encontrado no API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acredita que o xamarin. IOS deve dar suporte para o tipo em questão, envie uma solicitação de aprimoramento no [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4107" />

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: O registrador não pode realizar marshaling para o parâmetro de tipo `*` na assinatura para o método `+`.

Um tipo foi encontrado no API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acredita que o xamarin. IOS deve dar suporte para o tipo em questão, envie uma solicitação de aprimoramento no [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4108" />

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: O registrador não é possível obter o tipo de ObjectiveC para tipo gerenciado `*`.

Um tipo foi encontrado no API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acredita que o xamarin. IOS deve dar suporte para o tipo em questão, envie uma solicitação de aprimoramento no [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com).

<a name="MT4109" />

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: Falha ao compilar o código gerado do registrador. Envie um relatório de bug no http://bugzilla.xamarin.com

Falha ao compilar o código gerado para o registrador. O log de build conterá a saída do compilador nativo, explicando por que o código não está compilando.

Isso é sempre um bug no xamarin. IOS; Envie um relatório de bug no [ http://bugzilla.xamarin.com ](http://bugzilla.xamarin.com) com seu projeto ou um caso de teste.

<a name="MT4110" />

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: O registrador não pode realizar marshaling para o parâmetro de saída do tipo `*` na assinatura para o método `*`.

<a name="MT4111" />

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: O registrador não é possível criar uma assinatura para o tipo `*` no método `*`.

<a name="MT4112" />

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvancedtopicsregistrarhttpsdeveloperxamarincomguidesiosadvancedtopicsregistrar-for-more-information"></a>MT4112: O registrador encontrado um tipo inválido `*`. Registrando tipos genéricos com Objective-C não tem suporte e pode levar a comportamento aleatório e/ou falhas (para versões anteriores a compatibilidade com versões mais antigas do xamarin. IOS é possível ignorar esse erro, passando `--unsupported--enable-generics-in-registrar` como adicionais do mtouch argumento na página de opções de Build do projeto iOS. Ver [developer.xamarin.com/guides/ios/advanced_topics/registrar](https://developer.xamarin.com/guides/ios/advanced_topics/registrar) para obter mais informações).

<a name="MT4113" />

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: O registrador encontrado um método genérico: '\*.\*'. Exportação de métodos genéricos não é suportado e resultará em comportamento aleatório e/ou falhas.

<a name="MT4114" />

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: Erro inesperado no registrador para o método '\*.\*'-envie um relatório de bug no http://bugzilla.xamarin.com

<a name="MT4116" />

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: Não foi possível registrar o assembly ' *': *

<a name="MT4117" />

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: O registrador encontrada uma incompatibilidade de assinatura no método '*.*'-o seletor de indica que o método utiliza * parâmetros, enquanto o método gerenciado tem * parâmetros.

<a name="MT4118" />

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: Não é possível registrar dois tipos gerenciados ('\*'e'\*') com o mesmo nome nativo ('* ').

<a name="MT4119" />

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: Não foi possível registrar o seletor de '\*'do membro'\*. *' porque o Seletor já está registrado em outro membro.

<a name="MT4120" />

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: O registrador encontrado um tipo de campo desconhecido '\*'no campo'\*. *'. Envie um relatório de bug no http://bugzilla.xamarin.com

Esse erro indica um bug no xamarin. IOS. Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4121" />

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: Não é possível usar o GCC / G + + para compilar o código gerado no registrador estático de ao usar a estrutura de contas (os arquivos de cabeçalho fornecidos pela Apple usada durante a compilação exigem Clang). Use Clang (– compilador: clang) ou o registrador dinâmico (– registrador: dinâmico).

<a name="MT4122" />

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: Não é possível usar o compilador Clang fornecido no *.* SDK para compilar o código gerado no registrador estático quando não-ASCII de nomes de tipo ('* ') estão presentes no aplicativo. Use GCC / G + + (-: gcc do compilador | g + +), o registrador dinâmico (– registrador: dinâmico) ou um SDK mais recente.

<a name="MT4123" />

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: O tipo do parâmetro variadic na função variadic ' *' deve ser System. IntPtr.

<a name="MT4124" />

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: Inválido * encontrado em ' *'. Envie um relatório de bug no http://bugzilla.xamarin.com

Esse erro indica um bug no xamarin. IOS. Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4125" />

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: O registrador encontrado um tipo inválido '\*'na assinatura para o método'\*': A interface deve ter um atributo do protocolo especificar seu tipo de wrapper.

<a name="MT4126" />

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: Não é possível registrar dois protocolos gerenciados ('\*'e'\*') com o mesmo nome nativo ('* ').

<a name="MT4127" />

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: Não é possível registrar mais de um método de interface para o método '\*' (que é a implementação '\*').

<a name="MT4128" />

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: O registrador encontrado um tipo inválido de parâmetro genérico '\*'no método'\*'. O parâmetro genérico deve ter uma restrição 'NSObject'.

<a name="MT4129" />

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: O registrador encontrado um tipo de retorno genérico inválido '\*'no método'\*'. O tipo de retorno genérico deve ter uma restrição 'NSObject'.

<a name="MT4130" />

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: O registrador não é possível exportar métodos estáticos em classes genéricas ('* ').

<a name="MT4131" />

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: O registrador não é possível exportar as propriedades estáticas em classes genéricas ('\*.\*').

<a name="MT4132" />

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: O registrador encontrado um tipo de retorno genérico inválido '\*'na propriedade'\*'. O tipo de retorno deve ter uma restrição 'NSObject'.

<a name="MT4133" />

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: Não é possível construir uma instância do tipo ' *' do Objective-C porque o tipo é genérico. [Exceção de tempo de execução]

<a name="MT4134" />

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: Seu aplicativo está usando o ' *' framework, que não está incluído no SDK que você está usando para criar seu aplicativo iOS (essa estrutura foi introduzida no iOS *, enquanto você está criando com o iOS * SDK.) Selecione um SDK mais recente de opções de Build do iOS do seu aplicativo.

<a name="MT4135" />

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: O membro '\*.\*' tem um atributo de exportação que não especifica um seletor. Um seletor é necessário.

<a name="MT4136" />

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: O registrador não é possível empacotar o tipo de parâmetro '\*'do parâmetro'\*'no método'\*. *'

<!-- MT4137 is unused -->

<a name="MT4138" />

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: O registrador não é possível empacotar o tipo da propriedade '\*'da propriedade'\*. *'.

<a name="MT4139" />

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: O registrador não é possível empacotar o tipo da propriedade '\*'da propriedade'\*. *'. Propriedades com o atributo [Connect] devem ter um tipo de propriedade de NSObject (ou uma subclasse de NSObject).

<a name="MT4140" />

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: O registrador encontrada uma incompatibilidade de assinatura no método '*.*'-o seletor de indica que o método variadic usa * parâmetros, enquanto o método gerenciado tem * parâmetros.

<a name="MT4141" />

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: Não é possível registrar o seletor de '\*'no membro'\*' porque o xamarin. IOS implicitamente registra esse seletor.

Isso ocorre quando um tipo de estrutura de subclasses e tentando implementar uma 'reter', 'versão' ou 'dealloc' método:

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

É no entanto digite possível substituir esses métodos se a classe não for a primeira subclasse do framework:

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

Nesse caso, o xamarin. IOS substituirá `retain`, `release` e `dealloc` sobre o `MyNSObject` classe, e não há nenhum conflito.

<a name="MT4142" />

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: Falha ao registrar o tipo de ' *'.

<a name="MT4143" />

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: A classe ObjectiveC ' *' não pôde ser registrado, ele parece não derivar de uma classe ObjectiveC conhecida (incluindo NSObject).

<a name="MT4144" />

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: Não é possível registrar o método ' *', pois ele não tem um trampoline associado. Envie um relatório de bug no http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4145" />

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: Enumeração inválido ' *': enums com o atributo [nativo] deve ter um tipo subjacente de enumeração de 'long' ou 'ulong'.

<a name="MT4146" />

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: O parâmetro de nome do atributo na classe de registrador\*' ('\*') contém um caractere inválido: '\*' (\*).

O nome de uma classe Objectice C não pode conter espaço em branco, o que significa que o `Register` atributo na classe gerenciado correspondente não pode ter um `Name` parâmetro ou não pode conter espaço em branco.

Verifique se que o `Register` atributo na classe gerenciada mencionado na mensagem de erro não contém nenhum espaço em branco.

<a name="MT4147" />

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: Detectado um protocolo herdando o protocolo JSExport ao usar o registrador dinâmico. Não é possível exportar protocolos para JavaScriptCore dinamicamente; o registrador estático deve ser usado (Adicionar ' – registrador: estático para os argumentos adicionais mtouch em Opções de compilação para selecionar o registrador estático do iOS do projeto).

<a name="MT4148" />

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: O registrador encontrado um protocolo genérico: ' *'. Não há suporte para a exportação de protocolos genéricos.

<a name="MT4149" />

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: Não é possível registrar o método '\*.\*' porque o tipo do primeiro parâmetro ('\*') não coincide com o tipo de categoria ('\*').

<a name="MT4150" />

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: Não é possível registrar o tipo '\*' porque a propriedade Type ('\*') em sua categoria atributo não herda de NSObject.

<a name="MT4151" />

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: Não é possível registrar o tipo ' *' porque a propriedade de tipo em seu atributo de categoria não está definida.

<a name="MT4152" />

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: Não é possível registrar o tipo ' *' como uma categoria porque ela implementa INativeObject ou subdivide NSObject em classes.

<a name="MT4153" />

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: Não é possível registrar o tipo '\*' como uma categoria porque ela é genérica.

<a name="MT4154" />

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: Não é possível registrar o método '\*' como um método de categoria porque ela é genérica.

<a name="MT4155" />

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: Não é possível registrar o método '\*'com o seletor de'\*' como um método de categoria em ' *' porque o Objective-C já tem uma implementação para este seletor.

<a name="MT4156" />

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: Não é possível registrar duas categorias ('\*'e'\*') com o mesmo nome nativo ('* ').

<a name="MT4157" />

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: Não é possível registrar o método de categoria '\*' porque pelo menos um parâmetro é necessário (e seu tipo deve corresponder ao tipo de categoria '\*')

<a name="MT4158" />

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: Não é possível registrar o construtor * na categoria * porque não há suporte para construtores em categorias.

<a name="MT4159" />

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: Não é possível registrar o método ' *' como um método de categoria porque os métodos de categoria devem ser estáticos.

<a name="MT4160" />

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: Caractere inválido '\*' (\*) encontradas no seletor '\*'for'\*'.

<a name="MT4161" />

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: O registrador encontrada uma estrutura sem suporte '\*': Todos os campos em uma estrutura também devem ser estruturas (campo '\*'com o tipo'{2}' não é uma estrutura).

O registrador encontrada uma estrutura com campos sem suporte.

Todos os campos em uma estrutura que é exposto ao Objective-C também devem ser estruturas (não classes).

<a name="MT4162" />

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: O tipo '\*' (usado como * {2}) não está disponível no * * (ele foi introduzido em * *)\* Compile com uma versão mais recente * SDK (geralmente, é feito usando a versão mais recente do Xcode.

O registrador encontrado um tipo que não está incluído no SDK do atual.

Atualize o Xcode.

<a name="MT4163" />

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: Erro interno no registrador (*). Envie um relatório de bug no http://bugzilla.xamarin.com

Esse erro indica um bug no xamarin. IOS. Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4164" />

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: Não é possível exportar a propriedade '\*' porque seu seletor '\*' é uma palavra-chave Objective-C. Use um nome diferente.

O seletor para a propriedade em questão não é um identificador válido do Objective-C.

Use um identificador válido do Objective-C como seletores.

<a name="MT4165" />

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: O registrador não foi possível encontrar o tipo 'System. Void' em qualquer um dos assemblies referenciados.

Esse erro provavelmente indica um bug no xamarin. IOS. Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4166" />

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: Não é possível registrar o método '\*' porque a assinatura contém um tipo (\*) que não é um tipo de referência.

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4167" />

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: Não é possível registrar o método '\*' porque a assinatura contém um tipo genérico (\*) com um tipo de argumento genérico que não é uma subclasse de NSObject (*).

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT4168" />

### <a name="mt4168-cannot-register-the-type-managedname-because-its-objective-c-name-exportedname-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: Não é possível registrar o tipo ' {managed\_nome}' porque seu nome de Objective-C ' {exportado\_nome}' é uma palavra-chave Objective-C. Use um nome diferente.

O nome de Objective-C para o tipo em questão não é um identificador válido do Objective-C.

Use um identificador válido do Objective-C.

<a name="MT4169" />

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: Falha ao gerar um wrapper de P/Invoke para {método}: {message}

Xamarin. IOS Falha ao gerar uma função de wrapper de P/Invoke para mencionada.
Verifique se a mensagem de erro relatado para a causa subjacente.

<a name="MT4170" />

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: O registrador não pode converter de '{tipo gerenciado}' para '{tipo nativo}' para o valor retornado no método {método}.

Consulte a descrição do erro <a href="#MT4172">MT4172</a>.

<a name="MT4171" />

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: O atributo BindAs no membro {membro} é inválido: o tipo de BindAs {type} é diferente do tipo de propriedade {type}.

Verifique se que o tipo no atributo BindAs corresponde ao tipo do membro a que é anexado.

<a name="MT4172" />

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: O registrador não é possível converter de '{tipo nativo}' para '{tipo gerenciado}' para o parâmetro '{nome}' no método {método}.

O registrador não oferece suporte à conversão entre tipos mencionados.

Este é um bug no xamarin. IOS, se a API em questão for fornecida pelo xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ] [ 1].

Se você enfrentar isso durante o desenvolvimento de um projeto de associação para uma biblioteca nativa, estamos abertos à adição de suporte para novas combinações de tipos. Se esse for o caso, envie uma solicitação de aprimoramento ([http://bugzilla.xamarin.com][2]) com um teste de caso e podemos avaliará-lo.

[1]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS
[2]: https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS&component=General&bug_severity=enhancement

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: Mensagens de erro GCC e cadeia de ferramentas

### <a name="mt51xx-compilation"></a>MT51xx: Compilação

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101" />

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: Ausente ' *' compilador. Instale o componente 'Ferramentas de linha de comando' do Xcode

<a name="MT5102" />

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: Falha ao montar o arquivo ' *'. Envie um relatório de bug no http://bugzilla.xamarin.com

<a name="MT5103" />

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: Falha ao compilar o arquivo ' *'. Envie um relatório de bug no http://bugzilla.xamarin.com

<a name="MT5104" />

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: Não foi possível encontrar nenhum dos dois o '\*'ou'\*' compilador. Instale o componente 'Ferramentas de linha de comando' do Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106" />

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: Não é possível compilar o arquivo (s) ' *'. Envie um relatório de bug no http://bugzilla.xamarin.com

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

### <a name="mt52xx-linking"></a>MT52xx: Vinculação

<!--
  MT52xx linking
  -->

<a name="MT5201" />

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: Falha ao vincular nativo. Examine o log de compilação e os sinalizadores do usuário fornecidos para gcc: *

<a name="MT5202" />

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: Falha ao vincular nativo. Examine o log de compilação.

<a name="MT5203" />

### <a name="mt5203-native-linking-warning-"></a>MT5203: Nativo vinculação Aviso: *

<!--- 5204-5208 are not used -->

<a name="MT5209" />

### <a name="mt5209-native-linking-error-"></a>MT5209: Nativo de vinculação de erro: *

<a name="MT5210" />

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: Nativo de vinculação com falha, indefinido símbolo: *. Verifique se todas as estruturas necessárias foram mencionadas e bibliotecas nativas são vinculadas corretamente.

Isso acontece quando o vinculador nativo não é possível encontrar um símbolo que é referenciado em algum lugar. Há vários motivos, que isso pode ocorrer:

* Uma associação de terceiros requer uma estrutura, mas a associação não especificar isso no seu `[LinkWith]` atributo. Soluções:
  - Se você for o autor da associação de terceiros ou ter acesso à sua origem, modifique a associação `[LinkWith]` atributo para incluir o framework de que precisa:

            [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]

  - Se você não pode modificar a associação de terceiros, você pode vincular manualmente com a estrutura exigida, passando `-gcc_flags '-framework SystemFramework'` para `mtouch` (Isso é feito modificando os argumentos mtouch adicionais na página de opções de Build do projeto iOS. Lembre-se de que isso deve ser feito para todas as configurações de projeto).
* Em alguns casos uma associação gerenciada é composta de várias bibliotecas nativas e todos devem ser incluídos nas associações. É possível ter mais de uma biblioteca nativa em cada projeto de associação, portanto, a solução é simplesmente adicionar todas as bibliotecas nativas necessárias para o projeto de associação.</li>
* Uma associação gerenciada refere-se aos símbolos nativos que não existem na biblioteca nativa.
    Isso geralmente acontece quando uma associação existe há algum tempo, e o código nativo foi modificado durante esse tempo para que uma determinada classe nativa foi removida ou renomeada, enquanto a associação não foi atualizada.
* Um P/Invoke refere-se a um símbolo nativo que não existe. Começando com o xamarin. IOS 7.4 uma <a href="#MT5214">MT5214</a> erro será relatado para esse caso (consulte MT5214 para obter mais informações).
* Uma associação de terceiros / biblioteca foi criada usando C++, mas a associação não especificar isso no seu `[LinkWith]` atributo. Isso geralmente é bem simples reconhecer, porque têm os símbolos são símbolos de C++ danificados (um exemplo comum é `__ZNKSt9exception4whatEv`).
  - Se você for o autor da associação de terceiros ou ter acesso à sua origem, modifique a associação `[LinkWith]` atributo a ser definido o `IsCxx` sinalizador:

            [LinkWith ("mylib.a", IsCxx = true)]

  - Se você não pode modificar a associação de terceiros, ou você está vinculando manualmente com uma biblioteca de terceiros, você pode definir o sinalizador equivalente, passando <code>-cxx</code> para mtouch (Isso é feito modificando os argumentos mtouch adicionais na página de opções de Build do projeto iOS . Lembre-se de que isso deve ser feito para todas as configurações de projeto).

<a name="MT5211" />

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: Nativo de vinculação com falha, indefinido classe Objective-C: \*. O símbolo '\*' não pôde ser encontrado em qualquer uma das bibliotecas ou estruturas vinculadas com o seu aplicativo.

Isso acontece quando o vinculador nativo não é possível localizar uma classe de Objective-C que é referenciada em algum lugar. Há vários motivos, isso pode acontecer: os mesmos para [MT5210](#MT5210) e Além disso:

* Uma associação de terceiros associado a um protocolo de Objective-C, mas não anotá-lo com o <code>[Protocol]</code> atributo em sua definição de api. Soluções:
  - Adicionar o ausente `[Protocol]` atributo:

              [BaseType (typeof (NSObject))]
              [Protocol] // Add this
              public interface MyProtocol
              {
              }

<a name="MT5212" />

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: Nativo de vinculação com falha, duplicar o símbolo: *.

Isso acontece quando o vinculador nativo encontra símbolos duplicados entre todas as bibliotecas nativas. Após esse erro pode haver um ou mais [MT5213](#MT5213) erros com o local para cada ocorrência do símbolo. Possíveis motivos para esse erro:

* A mesma biblioteca nativa é incluída duas vezes.
* Acontecem duas bibliotecas nativas distintas definir os mesmos símbolos.
* Uma biblioteca nativa não é compilada corretamente e contém o símbolo mesmo mais de uma vez.
  Você pode confirmar isso usando o seguinte conjunto de comandos em um terminal (substitua i386 x86_64/armv7/armv7s/arm64 de acordo com a qual arquitetura você está compilando para):

        # Native libraries are usually fat libraries, containing binary code for
        # several architectures in the same file. First we extract the binary
        # code for the architecture we're interested in.
        lipo libNative.a -thin i386 -output libNative.i386.a

        # Now query the native library for the duplicated symbol.
        nm libNative.i386.a | fgrep 'SYMBOL'

        # You can also list the object files inside the native library.
        # In most cases this will reveal duplicated object files.
        ar -t libNative.i386.a

  Há algumas maneiras possíveis para corrigir esse problema:

  - Solicite que o provedor da biblioteca nativa corrigi-lo e fornecer a versão atualizada.
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

  - Peça o vinculador para remover o código não utilizado. Xamarin. IOS fará isso automaticamente se todas as seguintes condições forem atendidas:
    - Todas as associações por terceiros `[LinkWith]` atributos habilitou SmartLink:

            [assembly: LinkWith ("libNative.a", SmartLink = true)]

    - Não `-gcc_flags` é passado para o mtouch (no campo argumentos mtouch adicionais de opções de Build do iOS do projeto).
    - Também é possível solicitar que o vinculador diretamente para remover o código não utilizado adicionando `-gcc_flags -dead_strip` opções de compilação para os argumentos adicionais do mtouch no iOS do projeto.

<a name="MT5213" />

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: Duplicar o símbolo em: * (local relacionado ao erro anterior)

Esse erro é relatado apenas junto com [MT5212](#MT5212). Consulte [MT5212](#MT5212) para obter mais informações.

<a name="MT5214" />

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: Nativo de vinculação com falha, indefinido símbolo: *. Esse símbolo foi referenciado o membro gerenciado *. Verifique se que todas as estruturas necessárias foram bibliotecas referenciadas e nativas vinculadas.

Esse erro é relatado quando o código gerenciado contém um P/Invoke para um método nativo que não existe. Por exemplo:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Há algumas soluções possíveis:

  -  Remova o P/Invokes em questão de código-fonte.
  -  Habilite o vinculador gerenciado para todos os assemblies (Isso é feito no iOS do projeto opções de compilação definindo o "comportamento do vinculador" a "Todos os assemblies"). Isso efetivamente removerá todos os de P/Invokes não usar do aplicativo (automaticamente, em vez de manualmente como o ponto anterior). A desvantagem é que isso fará com que suas compilações de simulador um pouco mais lento, e pode dividir seu aplicativo se ele está usando reflexão, obter mais informações sobre o vinculador podem ser encontradas [aqui](~/ios/deploy-test/linker.md) )
  -  Crie uma biblioteca nativa de segundo, que contém os símbolos nativos ausentes. Observe que isso é simplesmente uma solução alternativa (se você tentar chamar essas funções, seu aplicativo falhará).

<a name="MT5215" />

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: As referências a ' *' pode exigir adicionais - framework = XXX ou lXXX - instruções para o vinculador nativo

Este é um aviso, indicando que um P/Invoke foi detectado para fazer referência à biblioteca em questão, mas o aplicativo não está vinculando com ele.

<a name="MT5216" />

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: Vinculando nativo falhou para *. Envie um relatório de bug no http://bugzilla.xamarin.com

Esse erro é relatado ao vincular a saída do compilador do AOT.

Esse erro provavelmente indica um bug no xamarin. IOS. Envie um relatório de bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT5217" />

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: Vinculando nativo possivelmente falhou porque a linha de comando do vinculador era muito longa (* caracteres).

Vinculando nativo falha e é possível que isso ocorreu porque o comando do vinculador era muito longo.

Projetos xamarin. IOS geralmente referenciará símbolos nativa dinamicamente, que significa que o vinculador nativo pode remover esses símbolos nativos durante o processo de vinculação nativo, pois o vinculador nativo não verá que esses símbolos são usados.

Normalmente, xamarin. IOS solicitará o vinculador nativo para manter esses símbolos usando o `-u symbol` sinalizador do vinculador, mas se houver muitos esses símbolos, toda a linha de comando poderá exceder o comprimento máximo de linha de comando conforme especificado pelo sistema operacional.

Há alguns possíveis fontes para esses símbolos dinâmicos:

* P/Invokes para métodos em bibliotecas vinculadas estaticamente (em que é o nome da dll `__Internal` no atributo DllImport `[DllImport ("__Internal")]`).
* Referências a locais de memória em bibliotecas estaticamente vinculadas de projetos de associação de campo (`[Field]` atributos).
* Classes de Objective-C referenciadas em bibliotecas estaticamente vinculadas de projetos de associação (ao usar builds incrementais ou quando não estiver usando o registrador estático).

Soluções possíveis:

* Habilite o vinculador gerenciado (se for possível para todos os assemblies em vez de apenas assemblies do SDK). Isso poderá remover suficiente das fontes para símbolos dinâmicos para que o vinculador 's de linha de comando não excedeu o máximo.
* Reduza o número de P/Invokes, referências de campo e/ou classes de Objective-C.
* Reescreva os símbolos de dinâmicos para ter nomes mais curtos.
* Passar `-dlsym:false` opções de Build como um argumento adicionais do mtouch no iOS do projeto. Com essa opção, o xamarin. IOS gerará uma referência nativa no código compilado AOT e não será necessário pedir o vinculador para manter esse símbolo. No entanto, isso só funciona para o dispositivo cria e causará erros de vinculador se não houver P/Invokes para as funções que não existem na biblioteca estática.
* Passar `--dynamic-symbol-mode=code` opções de Build como um argumentos mtouch adicionais no iOS do projeto. Com essa opção, o xamarin. IOS gerará código nativo adicional que faz referência a esses símbolos em vez de solicitar o vinculador nativo para manter esses símbolos usando os argumentos de linha de comando. A desvantagem dessa abordagem é que ela aumentará o tamanho do executável um pouco.
* Habilitar o registrador estático ao passar `--registrar:static` como um argumento adicionais do mtouch no iOS do projeto de Build opções (para compilações de simulador, uma vez que o registrador estático já é o padrão para compilações de dispositivo). O registrador estático irá gerar o código que faz referência a classes de Objective-C estaticamente, portanto, não é necessário pedir o vinculador nativo para manter essas classes.
* Desabilite a compilações incrementais (para compilações de dispositivo). Quando as compilações incrementais estão habilitadas, o código gerado pelo registrador de estáticos não será considerado pelo vinculador nativo, que significa que o xamarin. IOS ainda deve pedir ao vinculador para manter referenciado classes Objective-C. Desativando portanto a compilações incrementais impedirá a essa necessidade.

<a name="MT5218" />

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: Não é possível ignorar o símbolo dinâmico {symbol} (– ignorar-dynamic-symbol = {símbolo}) porque ele não foi detectado como um símbolo dinâmico.

O argumento de linha de comando `--ignore-dynamic-symbol=symbol` foi passado, mas esse símbolo não é um símbolo que foi reconhecido como um símbolo dinâmico que deve ser preservado manualmente.

Há duas razões principais para isso:

* O nome do símbolo está incorreto.
    * Não preceda um caractere de sublinhado para o nome do símbolo.
    * O símbolo para classes de Objective-C é `OBJC_CLASS_$_<classname>`.
* O símbolo está correto, mas é um símbolo que já é preservado por meios normais (algumas build causas de opções a lista exata de símbolos de dinâmicos para variar).

### <a name="mt53xx-other-tools"></a>MT53xx: Outras ferramentas

<!--
  MT53xx other tools
  -->

<a name="MT5301" />

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: Ferramenta 'retirar' ausente. Instale o componente 'Ferramentas de linha de comando' do Xcode

<a name="MT5302" />

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: Ferramenta de 'dsymutil' ausente. Instale o componente 'Ferramentas de linha de comando' do Xcode

<a name="MT5303" />

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: Falha ao gerar os símbolos de depuração (dSYM directory). Examine o log de compilação.

Ocorreu um erro ao executar o dsymutil no diretório. App final para criar os símbolos de depuração. Examine o log de compilação para ver a saída do dsymutil e ver como ele pode ser corrigido.

<a name="MT5304" />

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: Falha ao retirar o binário final. Examine o log de compilação.

Ocorreu um erro ao executar a ferramenta 'Remover' para remover informações de depuração do aplicativo.

<a name="MT5305" />

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: Ferramenta de 'lipo' ausente. Instale o componente 'Ferramentas de linha de comando' do Xcode

<a name="MT5306" />

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: Falha ao criar a uma biblioteca fat. Examine o log de compilação.

Ocorreu um erro ao executar a ferramenta 'lipo'. Examine o log de compilação para ver o erro relatado por 'lipo'.

<a name="MT5307" />

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: Falha ao assinar o executável. Examine o log de compilação.

Ocorreu um erro ao assinar o aplicativo. Examine o log de compilação para ver o erro relatado por 'codesign'.

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mensagens de erro das ferramentas mtouch interno

### <a name="mt600x-stripper"></a>MT600x: Remover formatação

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001" />

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: Executando a versão da Cecil não dá suporte a remoção de assembly

<a name="MT6002" />

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: Não foi possível remover o assembly `*`.

Ocorreu um erro quando a remoção de código gerenciado (removendo o código de IL) de assemblies no aplicativo.

<a name="MT6003" />

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [Message UnauthorizedAccessException]

Uma segurança erro durante a remoção de símbolos do aplicativo de depuração.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: Mensagens de erro do MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001" />

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: Não foi possível resolver o host IPs para as configurações do depurador de Wi-Fi.

*Tarefa do MSBuild: DetectDebugNetworkConfigurationTaskBase*

Etapas de solução de problemas:

- Tente executar `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (que deve fornecer um endereço IP e não um erro obviamente).
- Tente executar "ping \`hostname\`" que pode fornecer mais informações, como: `cannot resolve MyHost.local: Unknown host`

Em alguns casos, ele é um problema de "rede local" e ele poderá ser resolvido com a adição de host desconhecido `127.0.0.1   MyHost.local` em `/etc/hosts`.

<a name="MT7002" />

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: Este computador não tem todos os adaptadores de rede. Isso é necessário quando a depuração ou criação de perfil no dispositivo via WiFi.

*Tarefa do MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003" />

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: A extensão do aplicativo ' *' não contém um Info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004" />

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: A extensão do aplicativo ' *' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005" />

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: A extensão do aplicativo ' *' não especifica um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006" />

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: A extensão do aplicativo '\*' tem um CFBundleIdentifier inválido (\*), ele não começa com CFBundleIdentifier do pacote aplicativo principal (*).

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007" />

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: A extensão do aplicativo '\*' tem um CFBundleIdentifier (\*) que termina com o sufixo ilegal "chave".

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008" />

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: A extensão do aplicativo ' *' não especifica um CFBundleShortVersionString.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009" />

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: A extensão do aplicativo ' *' tem um Info. plist inválido: ele não contém um dicionário NSExtension.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010" />

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: A extensão do aplicativo ' *' tem um Info. plist inválido: o dicionário NSExtension não contém um valor de NSExtensionPointIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011" />

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: A extensão WatchKit ' *' tem um Info. plist inválido: o dicionário NSExtension não contém um dicionário NSExtensionAttributes.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012" />

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: A extensão WatchKit ' *' não tem exatamente um aplicativo de inspeção.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013" />

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: A extensão WatchKit ' *' tem um Info. plist inválido: UIRequiredDeviceCapabilities deve conter o recurso 'inspeção complementar'.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014" />

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: O aplicativo Watch ' *' não contém um Info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015" />

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: O aplicativo Watch ' *' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016" />

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: O aplicativo Watch '\*' tem um CFBundleIdentifier inválido (\*), ele não começa com CFBundleIdentifier do pacote aplicativo principal (*).

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017" />

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: O aplicativo Watch '\*' não tem um valor de UIDeviceFamily válido. Esperado 'Watch (4)', mas foi encontrado '\* (*)'.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018" />

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: O aplicativo Watch ' *' não especifica um CFBundleExecutable

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019" />

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: O aplicativo Watch '\*' tem um valor de WKCompanionAppBundleIdentifier inválido ('\*'), não corresponde ao CFBundleIdentifier do pacote de aplicativo principal ('* ').

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020" />

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: O aplicativo Watch ' *' tem um Info. plist inválida: a chave WKWatchKitApp deve estar presente e tem um valor 'true'.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021" />

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: O aplicativo Watch ' *' tem um Info. plist inválida: a chave LSRequiresIPhoneOS não deve estar presente.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022" />

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: O aplicativo Watch ' *' não contém uma extensão de inspeção.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023" />

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: A extensão de inspeção ' *' não contém um Info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024" />

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: A extensão de inspeção ' *' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025" />

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: A extensão de inspeção ' *' não especifica um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026" />

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: A extensão de inspeção '\*' tem um CFBundleIdentifier inválido (\*), ele não começa com CFBundleIdentifier do pacote aplicativo principal (*).

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027" />

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: A extensão de inspeção '\*' tem um CFBundleIdentifier (\*) que termina com o sufixo ilegal "chave".

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028" />

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: A extensão de inspeção ' *' tem um Info. plist inválido: ele não contém um dicionário NSExtension.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029" />

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: A extensão de inspeção ' *' tem um Info. plist inválido: o NSExtensionPointIdentifier deve ser "com.apple.watchkit".

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030" />

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: A extensão de inspeção ' *' tem um Info. plist inválido: o dicionário de NSExtension deve conter NSExtensionAttributes.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031" />

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: A extensão de inspeção ' *' tem um Info. plist inválido: o dicionário de NSExtensionAttributes deve conter um WKAppBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032" />

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: A extensão WatchKit ' *' tem um Info. plist inválido: UIRequiredDeviceCapabilities não deve conter o recurso 'inspeção complementar'.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033" />

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: O aplicativo Watch ' *' não contém um Info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034" />

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: O aplicativo Watch ' *' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035" />

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: O aplicativo Watch '\*' não tem um valor de UIDeviceFamily válido. Esperado '\*', mas foram encontradas'\* (\*)'.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036" />

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: O aplicativo Watch ' *' não especifica um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037" />

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: A extensão WatchKit '{extensionName}' tem um valor de WKAppBundleIdentifier inválido ('\*'), não corresponde ao CFBundleIdentifier do aplicativo Watch ('\*').

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038" />

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: O aplicativo Watch ' *' tem um Info. plist inválido: o WKCompanionAppBundleIdentifier deve existir e deve corresponder ao CFBundleIdentifier do pacote de aplicativo principal.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039" />

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: O aplicativo Watch ' *' tem um Info. plist inválida: a chave LSRequiresIPhoneOS não deve estar presente.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040" />

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: Pacote de aplicativo {AppBundlePath} não contém um Info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041" />

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: Info Main caminho não especificar um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042" />

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: Info Main caminho não especificar um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043" />

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: Info Main caminho não especificar um CFBundleSupportedPlatforms.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044" />

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: Info Main caminho não especificar um UIDeviceFamily.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045" />

### <a name="mt7045-unrecognized-format-"></a>MT7045: Não reconhecida formato: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

Em que * pode ser:

- cadeia de caracteres
- array
- dict
- bool
- real
- inteiro
- date
- Dados

<a name="MT7046" />

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Adicione: Entrada, *, especificado incorretamente.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7047" />

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Adicione: Entrada, *, contém o índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7048" />

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Adicione: * entrada já existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7049" />

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Adicione: Não é possível adicionar a entrada, *, para o pai.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7050" />

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Excluir: Não é possível excluir a entrada, *, do pai.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7051" />

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Excluir: Entrada, *, contém o índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7052" />

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Excluir: Entrada, *, não existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7053" />

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: Importação: Entrada, *, especificado incorretamente.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7054" />

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: Importação: Entrada, *, contém o índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7055" />

### <a name="mt7055-import-error-reading-file-"></a>MT7055: Importação: Erro ao ler o arquivo: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7056" />

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: Importação: Não é possível adicionar a entrada, *, para o pai.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7057" />

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Mesclagem: Não é possível adicionar as entradas de matriz para dict.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7058" />

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Mesclagem: Entrada especificada deve ser um contêiner.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7059" />

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Mesclagem: Entrada, *, contém o índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7060" />

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Mesclagem: Entrada, *, não existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7061" />

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Mesclagem: Erro ao ler o arquivo: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7062" />

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Defina: Entrada, *, especificado incorretamente.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7063" />

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Defina: Entrada, *, contém o índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7064" />

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Defina: Entrada, *, não existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7065" />

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: Ação de editor PropertyList desconhecida: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7066" />

### <a name="mt7066-error-loading--"></a>MT7066: Erro ao carregar ' *': *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7067" />

### <a name="mt7067-error-saving--"></a>MT7067: Erro ao salvar ' *': *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: Mensagens de erro de tempo de execução

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001" />

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: Incompatibilidade de versão entre o tempo de execução do xamarin. IOS nativo e MonoTouch. dll. Reinstale o xamarin. IOS.

<a name="MT8002" />

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: Não foi possível encontrar o método '\*'no tipo'\*'.

<a name="MT8003" />

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: Falha ao localizar o método genérico fechado '\*'no tipo'\*'.

<a name="MT8004" />

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: Não é possível criar uma instância de * para o objeto nativo 0 x * (do tipo ' *'), porque já existe outra instância para este objeto nativo (do tipo *).

<a name="MT8005" />

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: Tipo de wrapper '\*'está ausente de sua classe ObjectiveC nativo'\*'.

<a name="MT8006" />

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: Falha ao localizar o seletor de '\*'no tipo'\*'

<a name="MT8007" />

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: Não é possível obter o descritor de método para o seletor de '\*'no tipo'\*', pois o seletor não corresponde a um método

<a name="MT8008" />

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: A versão carregada do xamarin foi compilada para * bits, enquanto o processo é * bits. Envie um bug no http://bugzilla.xamarin.com.

Isso indica que algo está errado no processo de compilação. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8009" />

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: Não é possível localizar o bloco para o método de conversão para o método de representante *.*' s parâmetro #*. Envie um bug no http://bugzilla.xamarin.com.

Isso indica que uma API não foi associada corretamente. Quando se trata de uma API exposta pelo Xamarin, registre um bug em nosso bugzilla ([http://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)), se ele é uma associação de terceiros, entre em contato com o fornecedor.

<a name="MT8010" />

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: Incompatibilidade de tamanho de tipo nativo entre Xamarin. [iOS | DLL de MAC] e a arquitetura em execução. Xamarin. [iOS | . Dll de MAC] foi criado para o *-bit, enquanto o processo atual é *-bit.

Isso indica que algo está errado no processo de compilação. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8011" />

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: Não é possível localizar o delegado ao atributo de conversão de bloco ([DelegateProxy]) para o valor retornado do método *.*. Envie um bug no http://bugzilla.xamarin.com.

Xamarin. IOS não pôde localizar um método necessário em tempo de execução (para converter um delegado em um bloco).

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8012" />

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: DelegateProxyAttribute inválido para o valor retornado do método *.*: DelegateType é nulo. Envie um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8013" />

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: DelegateProxyAttribute inválido para o valor retornado do método *.*: DelegateType ({2}) especifica um tipo sem um campo 'Handler'. Envie um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8014" />

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: DelegateProxyAttribute inválido para o valor retornado do método *.*: O DelegateType ({2}) 'Manipulador' campo é nulo. Envie um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8015" />

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: DelegateProxyAttribute inválido para o valor retornado do método *.*: O DelegateType ({2}) o campo 'Manipulador' não é um delegado, que é um *. Envie um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8016" />

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: Não é possível converter o delegado para bloquear o valor de retorno para o método *.*, porque a entrada não é um delegado é um *. Envie um bug no http://bugzilla.xamarin.com.

O atributo DelegateProxy para o método em questão é inválido.

Isso geralmente indica um bug no xamarin. IOS; Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<!-- 8017 is used by mmp -->

<a name="MT8018" />

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: Erro de consistência interno. Envie um relatório de bug no http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8019" />

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: Não foi possível encontrar o assembly * nos assemblies carregados.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8020" />

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: Não foi possível encontrar o módulo com MetadataToken * no assembly *.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8021" />

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: Tipo de token desconhecido implícito: *.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8022" />

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: Espera-se a referência de token * para ser um *, mas é um *. Envie um relatório de bug no http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8023" />

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: Um objeto de instância é necessário para construir um método genérico fechado para o método genérico aberto: * (referência de token: *). Envie um relatório de bug no http://bugzilla.xamarin.com.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

<a name="MT8024" />

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smarttype-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: Não foi possível encontrar um tipo de extensão válido para o enum inteligente '{smart_type}'. Envie um bug no https://bugzilla.xamarin.com.

Isso indica um bug no xamarin. IOS. Envie um bug no [ http://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).
