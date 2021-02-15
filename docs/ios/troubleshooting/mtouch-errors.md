---
title: Erros do Xamarin. iOS
description: Este documento descreve vários erros gerados pelo mTouch, a ferramenta usada para agrupar aplicativos Xamarin. iOS. Os erros são listados por código e recebem uma descrição completa.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9F76162B-D622-45DA-996B-2FBF8017E208
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 4064b5561569124ab15f77b9614eea5b91eadad4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429973"
---
# <a name="xamarinios-errors"></a>Erros do Xamarin. iOS

## <a name="mt0xxx-mtouch-error-messages"></a>MT0xxx: mensagens de erro do mTouch

Por ex.: parâmetros, ambiente, ferramentas ausentes.

<!--
 MT0xxx mtouch itself, e.g. parameters, environment (e.g. missing tools)
 https://github.com/xamarin/xamarin-macios/blob/master/tools/mtouch/error.cs
  -->

<a name="MT0000"></a>

### <a name="mt0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MT0000: erro inesperado-preencha um relatório de bug em https://github.com/xamarin/xamarin-macios/issues/new

Ocorreu uma condição de erro inesperada. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com o máximo de informações possível, incluindo:

- Logs de compilação completos, com detalhes máximos (por exemplo, `-v -v -v -v` nos **argumentos mTouch adicionais**);
- Um caso de teste mínimo que reproduza o erro; e
- Todas as informações de versão

A maneira mais fácil de obter informações exatas sobre a versão é usar o menu **Visual Studio para Mac** , **sobre Visual Studio para Mac** item, mostrar o botão **detalhes** e copiar/colar a versão informações (você pode usar o botão **copiar informações** ).

<a name="MT0001"></a>

### <a name="mt0001--devname-was-provided-without-any-device-specific-action"></a>MT0001: '-DEVNAME ' foi fornecido sem nenhuma ação específica do dispositivo

Esse é um aviso emitido se-DEVNAME for passado para mTouch quando nenhuma ação específica do dispositivo (-logdev/-installdev/-killdev/-launchdev/-listapps) for solicitada.

<a name="MT0002"></a>

### <a name="mt0002-could-not-parse-the-environment-variable-"></a>MT0002: não foi possível analisar a variável de ambiente *.

Esse erro ocorrerá se você tentar definir um par de variável de ambiente = valor inválido. O formato correto é: `mtouch --setenv=VARIABLE=VALUE`

<a name="MT0003"></a>

### <a name="mt0003-application-name-exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MT0003: o nome do aplicativo ' *. exe ' está em conflito com um nome de SDK ou assembly de produto (. dll).

O nome do assembly executável e o nome do aplicativo não podem corresponder ao nome de qualquer DLL no aplicativo. Modifique o nome do seu executável.

<a name="MT0004"></a>

### <a name="mt0004-new-refcounting-logic-requires-sgen-to-be-enabled-too"></a>MT0004: a lógica New refcounting requer que o SGen esteja habilitado também.

Se você habilitar a extensão refcounting, também deverá habilitar o coletor de lixo SGen nas opções de Build do iOS do projeto (guia Avançado).

A partir do Xamarin. iOS 7.2.1 esse requisito foi levantado, a nova lógica refcounting pode ser habilitada com coletores de lixo Boehm e SGen.

<a name="MT0005"></a>

### <a name="mt0005-the-output-directory--does-not-exist"></a>MT0005: o diretório de saída * não existe.

Crie o diretório.

Esse erro não é mais gerado, o mTouch criará automaticamente o diretório se ele não existir.

<a name="MT0006"></a>

### <a name="mt0006-there-is-no-devel-platform-at--use---platformplat-to-specify-the-sdk"></a>MT0006: não há nenhuma plataforma desenvolvedor em *, use--Platform = PLAT para especificar o SDK.

O Xamarin. iOS não pode localizar o diretório do SDK no local mencionado na mensagem de erro. Verifique se o caminho está correto.

<a name="MT0007"></a>

### <a name="mt0007-the-root-assembly--does-not-exist"></a>MT0007: o assembly raiz * não existe.

O Xamarin. iOS não pode localizar o assembly no local mencionado na mensagem de erro. Verifique se o caminho está correto.

<a name="MT0008"></a>

### <a name="mt0008-you-should-provide-one-root-assembly-only-found--assemblies-"></a>MT0008: você deve fornecer apenas um assembly raiz, encontrado # assemblies: *.

Mais de um assembly raiz foi passado para mTouch, embora possa haver apenas um assembly raiz.

<a name="MT0009"></a>

### <a name="mt0009-error-while-loading-assemblies-"></a>MT0009: erro ao carregar assemblies: *.

Ocorreu um erro ao carregar os assemblies das referências do assembly raiz. Mais informações podem ser fornecidas na saída da compilação.

<a name="MT0010"></a>

### <a name="mt0010-could-not-parse-the-command-line-arguments-"></a>MT0010: não foi possível analisar os argumentos de linha de comando: *.

Ocorreu um erro ao analisar os argumentos de linha de comando. Verifique se estão todos corretos.

<a name="MT0011"></a>

### <a name="mt0011--was-built-against-a-more-recent-runtime--than-monotouch-supports"></a>MT0011: \* foi criado com base em um tempo de execução mais recente ( \* ) do que o MonoTouch dá suporte.

Esse aviso normalmente é relatado porque o projeto tem uma referência a uma biblioteca de classes que não foi criada usando a BCL do Xamarin. iOS.

Da mesma forma que um aplicativo que usa o SDK do .NET 4,0 pode não funcionar em um sistema que suporta apenas o .NET 2,0, uma biblioteca criada usando o .NET 4,0 pode não funcionar no Xamarin. iOS, ela pode usar a API não presente no Xamarin. iOS.

A solução geral é criar a biblioteca como uma biblioteca de classes do Xamarin. iOS. Isso pode ser feito criando um novo projeto de biblioteca de classes Xamarin. iOS e adicionar todos os arquivos de origem a ele. Se você não tiver o código-fonte da biblioteca, entre em contato com o fornecedor e solicite que eles forneçam uma versão compatível com o Xamarin. iOS de sua biblioteca.

<a name="MT0012"></a>

### <a name="mt0012-incomplete-data-is-provided-to-complete-"></a>MT0012: dados incompletos são fornecidos para concluir *.

Esse erro não é mais relatado na versão atual do Xamarin. iOS.

<a name="MT0013"></a>

### <a name="mt0013-profiling-support-requires-sgen-to-be-enabled-too"></a>MT0013: o suporte à criação de perfil requer que o SGen esteja habilitado também.

O SGen (--SGen) deve ser habilitado se a criação de perfil (--profiling) estiver habilitada.

<a name="MT0014"></a>

### <a name="mt0014-the-ios--sdk-does-not-support-building-applications-targeting-"></a>MT0014: o SDK do iOS não \* dá suporte à criação de aplicativos destinados \* .

Isso pode acontecer nas seguintes circunstâncias:

- O ARMv6 está habilitado e o Xcode 4,5 ou posterior está instalado.
- O ARMv7s está habilitado e o Xcode 4,4 ou anterior está instalado.

Verifique se a versão instalada do Xcode dá suporte às arquiteturas selecionadas.

<a name="MT0015"></a>

### <a name="mt0015-invalid-abi--supported-abis-are-i386-x86_64--armv7-armv7llvm-armv7llvmthumb2-armv7s-armv7sllvm-armv7sllvmthumb2-arm64-and-arm64llvm"></a>MT0015: ABI inválida: *. Os ABIs com suporte são: i386, x86_64, ARMv7, ARMv7 + LLVM, ARMv7 + LLVM + thumb2, armv7s, armv7s + LLVM, armv7s + LLVM + thumb2, arm64 e arm64 + LLVM.

Uma ABI inválida foi passada para mTouch. Especifique uma ABI válida.

<a name="MT0016"></a>

### <a name="mt0016-the-option--has-been-deprecated"></a>MT0016: a opção * foi preterida.

A opção mTouch mencionada foi preterida e será ignorada.

<a name="MT0017"></a>

### <a name="mt0017-you-should-provide-a-root-assembly"></a>MT0017: você deve fornecer um assembly raiz.

É necessário especificar um assembly raiz (normalmente o executável principal) ao criar um aplicativo.

<a name="MT0018"></a>

### <a name="mt0018-unknown-command-line-argument-"></a>MT0018: argumento de linha de comando desconhecido: *.

MTouch não reconhece o argumento de linha de comando mencionado na mensagem de erro.

<a name="MT0019"></a>

### <a name="mt0019-only-one---loginstallkilllaunchdev-or---launchdebugsim-option-can-be-used"></a>MT0019: apenas uma opção--[log | install | Kill | Launch] dev ou--[Launch | Debug] Sim pode ser usada.

Há várias opções para mTouch que não podem ser usadas simultaneamente:

- --logdev
- --installdev
- --killdev
- --launchdev
- --launchdebug
- --launchsim

<a name="MT0020"></a>

### <a name="mt0020-the-valid-options-for--are-"></a>MT0020: as opções válidas para ' \* ' são ' \* '.

<a name="MT0021"></a>

### <a name="mt0021-cannot-compile-using-gccg---use-gcc-when-using-the-static-registrar-this-is-the-default-when-compiling-for-device-either-remove-the---use-gcc-flag-or-use-the-dynamic-registrar---registrardynamic"></a>MT0021: não é possível compilar usando gcc/g + + (--use-gcc) ao usar o registrador estático (esse é o padrão ao compilar para o dispositivo). Remova o sinalizador--use-gcc ou use o registrador dinâmico (--registrador: dinâmico).

<a name="MT0022"></a>

### <a name="mt0022-the-options---unsupported--enable-generics-in-registrar-and---registrar-are-not-compatible"></a>MT0022: as opções "--não suportadas--habilitar-genéricos-in-registrador" e "--registrador" não são compatíveis.

Remova as opções `--unsupported--enable-generics-in-registrar` e `--registrar` . A partir do Xamarin. iOS 7.2.1, o registrador padrão oferece suporte a genéricos.

Esse erro não é mais mostrado (o argumento de linha de comando foi `--unsupported--enable-generics-in-registrar` removido de mTouch).

<a name="MT0023"></a>

### <a name="mt0023-application-name-exe-conflicts-with-another-user-assembly"></a>MT0023: o nome do aplicativo ' *. exe ' está em conflito com outro assembly de usuário.

O nome do assembly executável e o nome do aplicativo não podem corresponder ao nome de qualquer DLL no aplicativo. Modifique o nome do seu executável.

<a name="MT0024"></a>

### <a name="mt0024-could-not-find-required-file-"></a>MT0024: não foi possível encontrar o arquivo necessário ' * '.

<a name="MT0025"></a>

### <a name="mt0025-no-sdk-version-was-provided-please-add---sdkxy-to-specify-which-ios-sdk-should-be-used-to-build-your-application"></a>MT0025: nenhuma versão do SDK foi fornecida. Adicione `--sdk=X.Y` para especificar qual SDK do IOS deve ser usado para criar seu aplicativo.

<a name="MT0026"></a>

### <a name="mt0026-could-not-parse-the-command-line-argument--"></a>MT0026: não foi possível analisar o argumento de linha de comando ' \* ': \*

<a name="MT0027"></a>

### <a name="mt0027-the-options--and--are-not-compatible"></a>MT0027: as opções ' \* ' e ' \* ' não são compatíveis.

<a name="MT0028"></a>

### <a name="mt0028-cannot-enable-pie--pie-when-targeting-ios-41-or-earlier-please-disable-pie--piefalse-or-set-the-deployment-target-to-at-least-ios-42"></a>MT0028: não é possível habilitar pizza (pizza) ao direcionar para iOS 4,1 ou anterior. Desabilite a pizza (-pizza: falso) ou defina o destino de implantação para pelo menos iOS 4,2

<a name="MT0029"></a>

### <a name="mt0029-repl---enable-repl-is-only-supported-in-the-simulator---sim"></a>MT0029: REPL (--Enable-repl) só tem suporte no Simulator (--Sim).

O REPL só terá suporte se você estiver criando para o simulador. Isso significa que, se você passar `--enable-repl` para mTouch, também deverá passar `--sim` .

<a name="MT0030"></a>

### <a name="mt0030-the-executable-name--and-the-app-name--are-different-this-may-prevent-crash-logs-from-getting-symbolicated-properly"></a>MT0030: o nome do executável ( \* ) e o nome do aplicativo ( \* ) são diferentes, isso pode impedir que os logs de falhas sejam simbolizado corretamente.

Quando o Xcode symbolicates (traduz os endereços de memória para nomes de função e números de arquivo/linha), o processo poderá falhar se o executável e o aplicativo tiverem nomes diferentes (sem a extensão).

Para corrigir isso, altere o ' nome do aplicativo ' nas opções do aplicativo Build/iOS do projeto ou altere ' Assembly Name ' nas opções de compilação/saída do projeto.

<a name="MT0031"></a>

### <a name="mt0031-the-command-line-arguments---enable-background-fetch-and---launch-for-background-fetch-require---launchsim-too"></a>MT0031: os argumentos de linha de comando '--Enable-background-FETCH ' e '--Launch-for-background-FETCH ' exigem '--launchsim ' também.

<a name="MT0032"></a>

### <a name="mt0032-the-option---debugtrack-is-ignored-unless---debug-is-also-specified"></a>MT0032: a opção '--debugtrack ' é ignorada a menos que '--Debug ' também seja especificado.

<a name="MT0033"></a>

### <a name="mt0033-a-xamarinios-project-must-reference-either-monotouchdll-or-xamariniosdll"></a>MT0033: um projeto Xamarin. iOS deve fazer referência a monotouch.dll ou Xamarin.iOS.dll

<a name="MT0034"></a>

### <a name="mt0034-cannot-include-both-monotouchdll-and-xamariniosdll-in-the-same-xamarinios-project----is-referenced-explicitly-while--is-referenced-by-"></a>MT0034: não é possível incluir ' monotouch.dll ' e ' Xamarin.iOS.dll ' no mesmo projeto Xamarin. iOS-' \* ' é referenciado explicitamente, enquanto ' \* ' é referenciado por ' * '.

<!-- MT0035 unused -->

<a name="MT0036"></a>

### <a name="mt0036-cannot-launch-a--simulator-for-a--app-please-enable-the-correct-architectures-in-your-projects-ios-build-options-advanced-page"></a>MT0036: não é possível iniciar um \* simulador para um \* aplicativo. Habilite as arquiteturas corretas nas opções de compilação do iOS do seu projeto (página avançado).

<a name="MT0037"></a>

### <a name="mt0037-monotouchdll-is-not-64-bit-compatible-either-reference-xamariniosdll-or-do-not-build-for-a-64-bit-architecture-arm64-andor-x86_64"></a>MT0037: monotouch.dll não é compatível com 64 bits. Faça referência a Xamarin.iOS.dll ou não crie uma arquitetura de 64 bits (ARM64 e/ou x86_64).

<a name="MT0038"></a>

### <a name="mt0038-the-old-registrars---registraroldstaticolddynamic-are-not-supported-when-referencing-xamariniosdll"></a>MT0038: não há suporte para os registradores antigos (--registrador: oldstatic | olddynamic) ao fazer referência a Xamarin.iOS.dll.

<a name="MT0039"></a>

### <a name="mt0039-applications-targeting-armv6-cannot-reference-xamariniosdll"></a>MT0039: os aplicativos destinados a ARMv6 não podem fazer referência a Xamarin.iOS.dll.

<a name="MT0040"></a>

### <a name="mt0040-could-not-find-the-assembly--referenced-by-"></a>MT0040: não foi possível encontrar o assembly ' \* ', referenciado por ' \* '.

<a name="MT0041"></a>

### <a name="mt0041-cannot-reference-both-monotouchdll-and-xamariniosdll"></a>MT0041: não é possível referenciar ' monotouch.dll ' e ' Xamarin.iOS.dll '.

<a name="MT0042"></a>

### <a name="mt0042-no-reference-to-either-monotouchdll-or-xamariniosdll-was-found-a-reference-to-monotouchdll-will-be-added"></a>MT0042: nenhuma referência a monotouch.dll ou Xamarin.iOS.dll foi encontrada. Uma referência a monotouch.dll será adicionada.

<a name="MT0043"></a>

### <a name="mt0043-the-boehm-garbage-collector-is-currently-not-supported-when-referencing-xamariniosdll-the-sgen-garbage-collector-has-been-selected-instead"></a>MT0043: atualmente, não há suporte para o coletor de lixo Boehm ao fazer referência a ' Xamarin.iOS.dll '. O coletor de lixo do SGen foi selecionado em seu lugar.

Somente o coletor de lixo do SGen tem suporte com projetos unificados. Verifique se não há nenhum sinalizador mTouch adicional especificando Boehm como o coletor de lixo.

<a name="MT0044"></a>

### <a name="mt0044---listsim-is-only-supported-with-xcode-60-or-later"></a>MT0044:--listsim só tem suporte com o Xcode 6,0 ou posterior.

Instale uma versão mais recente do Xcode.

<a name="MT0045"></a>

### <a name="mt0045---extension-is-only-supported-when-using-the-ios-80-or-later-sdk"></a>MT0045:--a extensão só tem suporte ao usar o SDK do iOS 8,0 (ou posterior).

<!-- MT0046 is not reported anymore -->

<a name="MT0047"></a>

### <a name="mt0047-the-minimum-deployment-target-for-unified-applications-is-511-the-current-deployment-target-is--please-select-a-newer-deployment-target-in-your-projects-ios-application-options"></a>MT0047: o destino de implantação mínimo para aplicativos unificados é 5.1.1, o destino de implantação atual é ' * '. Selecione um destino de implantação mais recente nas opções de aplicativo iOS do seu projeto.

<!-- MT0048 is not reported anymore -->

<a name="MT0049"></a>

### <a name="mt0049-framework-is-supported-only-if-deployment-target-is-80-or-later--features-might-not-work-correctly"></a>MT0049: \* . Framework só terá suporte se o destino de implantação for 8,0 ou posterior. \* os recursos podem não funcionar corretamente.

A estrutura especificada não tem suporte na versão do iOS à qual o destino de implantação se refere. Atualize o destino de implantação para uma versão mais recente do iOS ou remova o uso da estrutura especificada do aplicativo.

<!-- MT0050 is not reported anymore -->

<a name="MT0051"></a>

### <a name="mt0051-xamarinios--requires-xcode-50-or-later-the-current-xcode-version-found-in--is-"></a>MT0051: Xamarin. iOS \* requer o Xcode 5,0 ou posterior. A versão atual do Xcode (encontrada em \* ) é \* .

Instale um Xcode mais recente.

<a name="MT0052"></a>

### <a name="mt0052-no-command-specified"></a>MT0052: nenhum comando especificado.

Nenhuma ação foi especificada para mTouch.

<!-- 0053 is used by mmp -->

<a name="MT0054"></a>

### <a name="mt0054-unable-to-canonicalize-the-path--"></a>MT0054: não é possível padronizar o caminho ' \* ': \*

Esse é um erro interno. Se você vir esse erro, registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0055"></a>

### <a name="mt0055-the-xcode-path--does-not-exist"></a>MT0055: o caminho do Xcode ' * ' não existe.

O caminho do Xcode passado usando não `--sdkroot` existe. Especifique um caminho válido.

<a name="MT0056"></a>

### <a name="mt0056-cannot-find-xcode-in-the-default-location-applicationsxcodeapp-please-install-xcode-or-pass-a-custom-path-using---sdkroot-path"></a>MT0056: não é possível localizar o Xcode no local padrão (/Applications/Xcode.app). Instale o Xcode ou passe um caminho personalizado usando--sdkroot \<path> .

<a name="MT0057"></a>

### <a name="mt0057-cannot-determine-the-path-to-xcodeapp-from-the-sdk-root--please-specify-the-full-path-to-the-xcodeapp-bundle"></a>MT0057: não é possível determinar o caminho para o Xcode. app da raiz do SDK ' * '. Especifique o caminho completo para o pacote Xcode. app.

O caminho passado usando não `--sdkroot` especifica um aplicativo Xcode válido. Especifique um caminho para um aplicativo do Xcode.

<a name="MT0058"></a>

### <a name="mt0058-the-xcodeapp--is-invalid-the-file--does-not-exist"></a>MT0058: o Xcode. app ' \* ' é inválido (o arquivo ' \* ' não existe).

O caminho passado usando não `--sdkroot` especifica um aplicativo Xcode válido. Especifique um caminho para um aplicativo do Xcode.

<a name="MT0059"></a>

### <a name="mt0059-could-not-find-the-currently-selected-xcode-on-the-system-"></a>MT0059: não foi possível localizar o Xcode atualmente selecionado no sistema: *

<a name="MT0060"></a>

### <a name="mt0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned--but-that-directory-does-not-exist"></a>MT0060: não foi possível localizar o Xcode atualmente selecionado no sistema. ' Xcode-Select--Print-path ' retornou ' * ', mas esse diretório não existe.

<a name="MT0061"></a>

### <a name="mt0061-no-xcodeapp-specified-using---sdkroot-using-the-system-xcode-as-reported-by-xcode-select---print-path-"></a>MT0061: nenhum Xcode. app especificado (usando--sdkroot), usando o Xcode do sistema conforme relatado por ' Xcode-Select--Print-path ': *

Este é um aviso informativo, explicando qual Xcode será usado, já que nenhum foi especificado.

<a name="MT0062"></a>

### <a name="mt0062-no-xcodeapp-specified-using---sdkroot-or-xcode-select---print-path-using-the-default-xcode-instead-"></a>MT0062: nenhum Xcode. app especificado (usando--sdkroot ou "Xcode-Select--Print-Path"), usando o Xcode padrão em vez disso: *

Este é um aviso informativo, explicando qual Xcode será usado, já que nenhum foi especificado.

<a name="MT0063"></a>

### <a name="mt0063-cannot-find-the-executable-in-the-extension--no-cfbundleexecutable-entry-in-its-infoplist"></a>MT0063: não é possível encontrar o executável na extensão * (nenhuma entrada CFBundleExecutable em seu info. plist)

Cada info. plist deve ter um executável (usando a entrada CFBundleExecutable), no entanto, uma entrada deve ser gerada automaticamente durante a compilação.

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0064"></a>

### <a name="mt0064-xamarinios-only-supports-embedded-frameworks-with-unified-projects"></a>MT0064: o Xamarin. iOS só dá suporte a estruturas inseridas com projetos unificados.

O Xamarin. iOS dá suporte apenas a estruturas inseridas ao usar o API Unificada; Atualize seu projeto para usar o API Unificada.

<a name="MT0065"></a>

### <a name="mt0065-xamarinios-only-supports-embedded-frameworks-when-deployment-target-is-at-least-80-current-deployment-target--embedded-frameworks-"></a>MT0065: o Xamarin. iOS só dá suporte a estruturas inseridas quando o destino de implantação é pelo menos 8,0 (destino de implantação atual: \* estruturas inseridas: \* )

O Xamarin. iOS só dá suporte a estruturas inseridas quando o destino de implantação é de pelo menos 8,0 (porque as versões anteriores do iOS não oferecem suporte a estruturas inseridas).

Atualize o destino de implantação no info. plist do projeto para 8,0 ou superior.

<a name="MT0066"></a>

### <a name="mt0066-invalid-build-registrar-assembly-"></a>MT0066: assembly do registrador de compilação inválido: *

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0067"></a>

### <a name="mt0067-invalid-registrar-"></a>MT0067: registrador inválido: *

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0068"></a>

### <a name="mt0068-invalid-value-for-target-framework-"></a>MT0068: valor inválido para a estrutura de destino: *.

Uma estrutura de destino inválida foi passada usando o argumento--Target-Framework. Especifique uma estrutura de destino válida.

<a name="MT0069"></a>

<!--### MT0069: currently unused -->

<a name="MT0070"></a>

### <a name="mt0070-invalid-target-framework--valid-target-frameworks-are-"></a>MT0070: estrutura de destino inválida: *. As estruturas de destino válidas são: *.

Uma estrutura de destino inválida foi passada usando o argumento--Target-Framework. Especifique uma estrutura de destino válida.

<a name="MT0071"></a>

### <a name="mt0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinios-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT0071: plataforma desconhecida: *. Isso geralmente indica um bug no Xamarin. iOS; Registre um relatório de bugs em http://bugzilla.xamarin.com com um caso de teste.

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0072"></a>

### <a name="mt0072-extensions-are-not-supported-for-the-platform-"></a>MT0072: não há suporte para extensões para a plataforma ' * '.

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0073"></a>

### <a name="mt0073-xamarinios--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MT0073: o Xamarin. iOS \* não dá suporte a um destino de implantação de \* (o mínimo é \* ). Selecione um destino de implantação mais recente no info. plist do seu projeto.

O destino de implantação mínimo é aquele especificado na mensagem de erro; Selecione um destino de implantação mais recente no info. plist do projeto.

Se a atualização do destino de implantação não for possível, use uma versão mais antiga do Xamarin. iOS.

<a name="MT0074"></a>

### <a name="mt0074-xamarinios--does-not-support-a-minimum-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinios"></a>MT0074: Xamarin. iOS \* não dá suporte a um destino de implantação mínimo de \* (o máximo é \* ). Selecione um destino de implantação mais antigo no info. plist do seu projeto ou atualize para uma versão mais recente do Xamarin. iOS.

O xamarin. iOS não dá suporte à definição do destino de implantação mínimo para uma versão superior à versão para a qual essa versão específica do Xamarin. iOS foi criada.

Selecione um destino de implantação mínimo mais antigo no info. plist do projeto ou atualize para uma versão mais recente do Xamarin. iOS.

<a name="MT0075"></a>

### <a name="mt0075-invalid-architecture--for--projects-valid-architectures-are-"></a>MT0075: arquitetura ' ' inválida \* para \* projetos. As arquiteturas válidas são: \*

Uma arquitetura inválida foi especificada. Verifique se a arquitetura é válida.

<a name="MT0076"></a>

### <a name="mt0076-no-architecture-specified-using-the---abi-argument-an-architecture-is-required-for--projects"></a>MT0076: nenhuma arquitetura especificada (usando o argumento--Abi). Uma arquitetura é necessária para * projetos.

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0077"></a>

### <a name="mt0077-watchos-projects-must-be-extensions"></a>MT0077: projetos WatchOS devem ser extensões.

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0078"></a>

### <a name="mt0078-incremental-builds-are-enabled-with-a-deployment-target--80-currently--this-is-not-supported-the-resulting-application-will-not-launch-on-ios-9-so-the-deployment-target-will-be-set-to-80"></a>MT0078: as compilações incrementais são habilitadas com um destino de implantação < 8,0 (atualmente *). Isso não é suportado (o aplicativo resultante não será iniciado no iOS 9), portanto, o destino de implantação será definido como 8,0.

Este é um aviso informando que o destino da implantação foi definido como 8,0 para essa compilação para que as compilações incrementais funcionem corretamente.

As compilações incrementais só têm suporte quando o destino de implantação é de pelo menos 8,0 (porque o aplicativo resultante não será iniciado no iOS 9 caso contrário).

<a name="MT0079"></a>

### <a name="mt0079-the-recommended-xcode-version-for-xamarinios--is-xcode--or-later-the-current-xcode-version-found-in--is-"></a>MT0079: a versão recomendada do Xcode para Xamarin. iOS \* é o Xcode \* ou posterior. A versão atual do Xcode (encontrada em \* ) é \* .

Este é um aviso informando que a versão atual do Xcode não é a versão recomendada do Xcode para esta versão do Xamarin. iOS.

Atualize o Xcode para garantir o comportamento ideal.

<a name="MT0080"></a>

### <a name="mt0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MT0080: desabilitar NewRefCount,--New-Refcount: false, foi preterido.

Este é um aviso informando que a solicitação para desabilitar o novo Refcount (--New-Refcount: false) foi ignorada.

O novo recurso Refcount agora é obrigatório para todos os projetos e, portanto, não é possível desabilitar mais.

<a name="MT0081"></a>

### <a name="mt0081-the-command-line-argument---download-crash-report-also-requires---download-crash-report-to"></a>MT0081: o argumento de linha de comando--download-falha-relatório também requer--download-Crash-Report-to.

<a name="MT0082"></a>

### <a name="mt0082-repl---enable-repl-is-only-supported-when-linking-is-not-used---nolink"></a>MT0082: REPL (--Enable-repl) só tem suporte quando a vinculação não é usada (--nolink).

<a name="MT0083"></a>

### <a name="mt0083-asm-only-bitcode-is-not-supported-on-watchos-use-either---bitcodemarker-or---bitcodefull"></a>MT0083: não há suporte para BitCode somente ASM em watchOS. Use--BitCode: Marker ou--BitCode: Full.

<a name="MT0084"></a>

### <a name="mt0084-bitcode-is-not-supported-in-the-simulator-do-not-pass---bitcode-when-building-for-the-simulator"></a>MT0084: não há suporte para BitCode no simulador. Não passe-BitCode ao compilar para o simulador.

<a name="MT0085"></a>

### <a name="mt0085-no-reference-to--was-found-it-will-be-added-automatically"></a>MT0085: nenhuma referência a ' * ' foi encontrada. Ele será adicionado automaticamente.

<a name="MT0086"></a>

### <a name="mt0086-a-target-framework---target-framework-must-be-specified-when-building-for-tvos-or-watchos"></a>MT0086: uma estrutura de destino (--Target-Framework) deve ser especificada ao compilar para TVOS ou WatchOS.

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT0087"></a>

### <a name="mt0087-incremental-builds---fastdev-is-not-supported-with-the-boehm-gc-incremental-builds-will-be-disabled"></a>MT0087: não há suporte para compilações incrementais (--fastdev) com o Boehm GC. As compilações incrementais serão desabilitadas.

<a name="MT0088"></a>

### <a name="mt0088-the-gc-must-be-in-cooperative-mode-for-watchos-apps-please-remove-the---coopfalse-argument-to-mtouch"></a>MT0088: o GC deve estar no modo cooperativo para aplicativos watchOS. Remova o argumento--Coop: false para mTouch.

<a name="MT0089"></a>

### <a name="mt0089-the-option--cannot-take-the-value--when-cooperative-mode-is-enabled-for-the-gc"></a>MT0089: a opção ' \* ' não pode obter o valor ' \* ' quando o modo cooperativo está habilitado para o GC.

<a name="MT0091"></a>

### <a name="mt0091-this-version-of-xamarinios-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-set-the-managed-linker-behaviour-to-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MT0091: esta versão do Xamarin. iOS requer o \* SDK (fornecido com o Xcode \* ). Atualize o Xcode para obter os arquivos de cabeçalho necessários ou defina o comportamento do vinculador gerenciado para vincular somente os SDKs do Framework (para tentar evitar as novas APIs).

O Xamarin. iOS requer os arquivos de cabeçalho, da versão do SDK especificada na mensagem de erro, para compilar seu aplicativo. A maneira recomendada para corrigir esse erro é atualizar o Xcode para obter o SDK necessário, incluindo todos os arquivos de cabeçalho necessários. Se você tiver várias versões do Xcode instaladas ou quiser usar um Xcode em um local não padrão, certifique-se de definir o local do Xcode correto nas preferências do IDE.

Uma solução potencial e alternativa é habilitar o vinculador gerenciado. Isso removerá a API não usada, incluindo, na maioria dos casos, a nova API em que os arquivos de cabeçalho estão ausentes (ou incompletos). No entanto, isso não funcionará se o seu projeto usar a API que foi introduzida em um SDK mais recente do que o que o Xcode fornece.

Uma solução Straw seria usar uma versão mais antiga do Xamarin. iOS, uma que dá suporte ao SDK que seu projeto requer.

<!-- MT0092 used by mlaunch -->

<a name="MT0093"></a>

### <a name="mt0093-could-not-find-mlaunch"></a>MT0093: não foi possível encontrar ' mlaunch '.

<!-- MT0094 is not reported anymore -->

<a name="MT0095"></a>

### <a name="mt0095-aot-files-could-not-be-copied-to-the-destination-directory-dest-error"></a>MT0095: não foi possível copiar os arquivos AOT para o diretório de destino {dest}: {Error}

<a name="MT0096"></a>

### <a name="mt0096-no-reference-to-xamariniosdll-was-found"></a>MT0096: nenhuma referência a Xamarin.iOS.dll foi encontrada.

<!-- MT0097: used by mmp -->
<!-- MT0098: used by mmp -->

<a name="MT0099"></a>

### <a name="mt0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MT0099: erro interno *. Registre um relatório de bug com um caso de teste ( https://bugzilla.xamarin.com) .

Essa mensagem de erro é relatada quando uma verificação de consistência interna no Xamarin. iOS falha.

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0100"></a>

### <a name="mt0100-invalid-assembly-build-target--please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MT0100: destino de compilação de assembly inválido: ' * '. Registre um relatório de bug com um caso de teste ( https://bugzilla.xamarin.com) .

Essa mensagem de erro é relatada quando uma verificação de consistência interna no Xamarin. iOS falha.

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT0101"></a>

### <a name="mt0101-the-assembly--is-specified-multiple-times-in---assembly-build-target-arguments"></a>MT0101: o assembly "*" é especificado várias vezes nos argumentos assembly-Build-Target.

O assembly mencionado na mensagem de erro é especificado várias vezes nos argumentos assembly-Build-Target. Certifique-se de que cada assembly seja mencionado apenas uma vez.

<a name="MT0102"></a>

### <a name="mt0102-the-assemblies--and--have-the-same-target-name--but-different-targets--and-"></a>MT0102: os assemblies ' \* ' e ' \* ' têm o mesmo nome de destino (' \* '), mas destinos diferentes (' \* ' e ' * ').

Os assemblies mencionados na mensagem de erro têm destinos de compilação conflitantes.

Por exemplo:

```
  --assembly-build-target:Assembly1.dll=framework=MyBinary --assembly-build-target:Assembly2.dll=dynamiclibrary=MyBinary
```

Este exemplo está tentando criar uma biblioteca dinâmica e uma estrutura com a mesma marca ( `MyBinary` ).

<a name="MT0103"></a>

### <a name="mt0103-the-static-object--contains-more-than-one-assembly--but-each-static-object-must-correspond-with-exactly-one-assembly"></a>MT0103: o objeto estático ' \* ' contém mais de um assembly (' \* '), mas cada objeto estático deve corresponder a exatamente um assembly.

Os assemblies mencionados na mensagem de erro são todos compilados para um único objeto estático. Isso não é permitido, cada assembly deve ser compilado em um objeto estático diferente.

Por exemplo:

```
--assembly-build-target:Assembly1.dll=staticobject=MyBinary --assembly-build-target:Assembly2.dll=staticobject=MyBinary
```

Este exemplo tenta criar um objeto estático ( `MyBinary` ) composto de dois assemblies ( `Assembly1.dll` e `Assembly2.dll` ), o que não é permitido.

<a name="MT0105"></a>

### <a name="mt0105-no-assembly-build-target-was-specified-for-"></a>MT0105: nenhum destino de compilação do assembly foi especificado para ' * '.

Ao especificar o destino da compilação do assembly usando `--assembly-build-target` , cada assembly no aplicativo deve ter um destino de compilação atribuído.

Esse erro é relatado quando o assembly mencionado na mensagem de erro não tem um destino de compilação de assembly atribuído.

Consulte a documentação sobre `--assembly-build-target` para obter mais informações.

<a name="MT0106"></a>

### <a name="mt0106-the-assembly-build-target-name--is-invalid-the-character--is-not-allowed"></a>MT0106: o nome de destino da compilação do assembly ' \* ' é inválido: o caractere ' \* ' não é permitido.

O nome de destino da compilação do assembly deve ser um nome de arquivo válido.

Por exemplo, esses valores dispararão esse erro:

```
--assembly-build-target:Assembly1.dll=staticobject=my/path.o
```

Porque `my/path.o` não é um nome de arquivo válido devido ao caractere separador de diretório.

<a name="MT0107"></a>

### <a name="mt0107-the-assemblies--have-different-custom-llvm-optimizations--which-is-not-allowed-when-they-are-all-compiled-to-a-single-binary"></a>MT0107: os assemblies ' \* ' têm otimizações de LLVM personalizadas diferentes ( \* ), o que não é permitido quando todos são compilados em um único binário.

<a name="MT0108"></a>

### <a name="mt0108-the-assembly-build-target--did-not-match-any-assemblies"></a>MT0108: o destino da compilação do assembly ' * ' não correspondeu a nenhum assembly.

<a name="MT0109"></a>

### <a name="mt0109-the-assembly-0-was-loaded-from-a-different-path-than-the-provided-path-provided-path-1-actual-path-2"></a>MT0109: o assembly ' {0} ' foi carregado de um caminho diferente do caminho fornecido (caminho fornecido: {1} , caminho real: {2} ).

Este é um aviso que indica que um assembly referenciado pelo aplicativo foi carregado de um local diferente do solicitado.

Isso pode significar que o aplicativo está referenciando vários assemblies com o mesmo nome, mas de diferentes locais, o que pode levar a resultados inesperados (somente o primeiro assembly será usado).

<a name="MT0110"></a>

### <a name="mt0110-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-third-party-binding-libraries-and-that-compiles-to-bitcode"></a>MT0110: compilações incrementais foram desabilitadas porque esta versão do Xamarin. iOS não oferece suporte a compilações incrementais em projetos que incluem bibliotecas de associação de terceiros e que são compiladas no BitCode.

Compilações incrementais foram desabilitadas porque esta versão do Xamarin. iOS não oferece suporte a compilações incrementais em projetos que incluem bibliotecas de associação de terceiros e que são compiladas em BitCode (projetos tvOS e watchOS).

Nenhuma ação é necessária em sua parte, essa mensagem é puramente informativa.

Para obter mais informações, consulte bug n º[51710](https://bugzilla.xamarin.com/show_bug.cgi?id=51710).

Este aviso não é mais relatado.

<a name="MT0111"></a>

### <a name="mt0111-bitcode-has-been-enabled-because-this-version-of-xamarinios-does-not-support-building-watchos-projects-using-llvm-without-enabling-bitcode"></a>MT0111: BitCode foi habilitado porque esta versão do Xamarin. iOS não dá suporte à criação de projetos watchOS usando LLVM sem habilitar BitCode.

O BitCode foi habilitado automaticamente porque esta versão do Xamarin. iOS não oferece suporte à criação de projetos watchOS usando o LLVM sem habilitar o BitCode.

Nenhuma ação é necessária em sua parte, essa mensagem é puramente informativa.

Para obter mais informações, consulte bug n º[51634](https://bugzilla.xamarin.com/show_bug.cgi?id=51634).

<a name="MT0112"></a>

### <a name="mt0112-native-code-sharing-has-been-disabled-because-"></a>MT0112: o compartilhamento de código nativo foi desabilitado porque *

Há várias razões pelas quais o compartilhamento de código pode ser desabilitado:

- Porque o destino de implantação do aplicativo de contêiner é anterior ao iOS 8,0 (é *)).

O compartilhamento de código nativo requer iOS 8,0 porque o compartilhamento de código nativo é implementado usando estruturas de usuário, que foi introduzida com o iOS 8,0.

- como o aplicativo de contêiner inclui assemblies de I18N (*).

O compartilhamento de código nativo não tem suporte no momento se o aplicativo de contêiner incluir assemblies de I18N.

- Porque o aplicativo de contêiner tem definições XML personalizadas para o vinculador gerenciado (*).

O compartilhamento de código nativo requer não tem suporte para projetos que usam definições XML personalizadas para o vinculador gerenciado.

<a name="MT0113"></a>

### <a name="mt0113-native-code-sharing-has-been-disabled-for-the-extension--because-"></a>MT0113: o compartilhamento de código nativo foi desabilitado para a extensão "*" porque *.

- Porque as opções BitCode diferem entre o aplicativo de contêiner ( \* ) e a extensão ( \* ).

  O compartilhamento de código nativo requer que as opções BitCode correspondam entre os projetos que compartilham o código.

- como as opções--assembly-Build-Target são diferentes entre o aplicativo de contêiner ( \* ) e a extensão ( \* ).

  O compartilhamento de código nativo requer que as opções--assembly-Build-Target sejam idênticas entre os projetos que compartilham o código.

  Essa condição pode ocorrer se as compilações incrementais não estiverem habilitadas ou desabilitadas em todos os projetos.

- Porque os assemblies de I18N são diferentes entre o aplicativo de contêiner ( \* ) e a extensão ( \* ).

  O compartilhamento de código nativo não tem suporte atualmente para extensões que incluem assemblies de I18N.

- como os argumentos para o compilador de AOT são diferentes entre o aplicativo de contêiner ( \* ) e a extensão ( \* ).

  O compartilhamento de código nativo requer que os argumentos para o compilador AOT não sejam diferentes entre projetos que compartilham código.

- como os outros argumentos para o compilador de AOT são diferentes entre o aplicativo de contêiner ( \* ) e a extensão ( \* ).

  O compartilhamento de código nativo requer que os argumentos para o compilador AOT não sejam diferentes entre projetos que compartilham código.

  Essa condição ocorre se o ' executar todas as operações float de 32 bits como 64-bit float ' diferir entre projetos.

- como LLVM não está habilitado ou desabilitado no aplicativo de contêiner ( \* ) e na extensão ( \* ).

  O compartilhamento de código nativo requer que o LLVM seja habilitado ou desabilitado para todos os projetos que compartilham código.

- Porque as configurações do vinculador gerenciados são diferentes entre o aplicativo de contêiner ( \* ) e a extensão ( \* ).

  O compartilhamento de código nativo requer que as configurações do vinculador gerenciados sejam idênticas para todos os projetos que compartilham código.

- Porque os assemblies ignorados para o vinculador gerenciado são diferentes entre o aplicativo de contêiner ( \* ) e a extensão ( \* ).

  O compartilhamento de código nativo requer que as configurações do vinculador gerenciados sejam idênticas para todos os projetos que compartilham código.

- Porque a extensão tem definições XML personalizadas para o vinculador gerenciado (*).

  O compartilhamento de código nativo requer não tem suporte para projetos que usam definições XML personalizadas para o vinculador gerenciado.

- Porque o aplicativo de contêiner não é compilado para a ABI * (enquanto a extensão está compilando para essa ABI).

  O compartilhamento de código nativo requer que o aplicativo de contêiner seja criado para todas as arquiteturas que qualquer extensão de aplicativo compila.

  Por exemplo: essa condição ocorre quando uma extensão é compilada para ARM64 + ARMv7, mas o aplicativo de contêiner é compilado apenas para ARM64.

- Porque o aplicativo de contêiner está criando para a ABI \* , que não é compatível com a Abi () da extensão \* .

  O compartilhamento de código nativo requer que todos os projetos sejam compilados para exatamente a mesma API.

  Por exemplo: essa condição ocorre quando uma extensão é compilada para ARMv7 + LLVM + thumb2, mas o aplicativo de contêiner é compilado somente para ARMv7 + LLVM.

- Porque o aplicativo de contêiner está referenciando o assembly ' \* ' de ' \* ', enquanto a extensão faz referência a uma versão diferente de ' * '.

  O compartilhamento de código nativo requer que todos os projetos que compartilham o código usem as mesmas versões para todos os assemblies.

<!-- MT0114: used by mmp -->

<a name="MT0115"></a>

### <a name="mt0115-it-is-recommended-to-reference-dynamic-symbols-using-code---dynamic-symbol-modecode-when-bitcode-is-enabled"></a>MT0115: é recomendável referenciar símbolos dinâmicos usando código (--Dynamic-Symbol-Mode = Code) quando BitCode está habilitado.

Os projetos do Xamarin. iOS geralmente referenciam símbolos nativos dinamicamente, o que significa que o vinculador nativo pode remover esses símbolos nativos durante o processo de vinculação nativo, pois o vinculador nativo não vê que esses símbolos são usados.

Normalmente, o Xamarin. iOS solicitará que o vinculador nativo Mantenha esses símbolos (usando o `-u symbol` sinalizador do vinculador), mas ao compilar para BitCode, o vinculador nativo não aceitará o `-u` sinalizador.

O Xamarin. iOS implementou uma solução alternativa: geramos código nativo extra que faz referência a esses símbolos e, portanto, o vinculador nativo verá que esses símbolos são usados. Isso é feito automaticamente durante a compilação para o BitCode.

Se `--dynamic-symbol-mode=linker` for passado para mTouch, essa solução alternativa será desabilitada e o Xamarin. Ios tentará passar `-u` para o vinculador nativo. Isso provavelmente resultará em erros do vinculador nativo.

A solução é remover o `--dynamic-symbol-mode=linker` argumento dos argumentos mTouch adicionais nas opções de Build do projeto.

<!-- 0116 - 0124: free to use -->

<a name="MT0116"></a>

### <a name="mt0116-invalid-architecture-arch-32-bit-architectures-are-not-supported-when-deployment-target-is-11-or-later-make-sure-the-project-does-not-build-for-a-32-bit-architecture"></a>MT0116: arquitetura inválida: {Arch}. Não há suporte para arquiteturas de 32 bits quando o destino de implantação é 11 ou posterior. Verifique se o projeto não é compilado para uma arquitetura de 32 bits.

o iOS 11 não contém suporte para aplicativos de 32 bits, portanto, não há suporte para compilação para um aplicativo de 32 bits quando o destino de implantação é iOS 11 ou posterior.

Altere a arquitetura de destino nas opções de Build do iOS do projeto para arm64 ou altere o destino de implantação no info. plist do projeto para uma versão anterior do iOS.

<a name="MT0117"></a>

### <a name="mt0117-cant-launch-a-32-bit-app-on-a-simulator-that-only-supports-64-bit"></a>MT0117: não é possível iniciar um aplicativo de 32 bits em um simulador que dá suporte apenas a 64 bits.

<a name="MT0118"></a>

### <a name="mt0118-aot-files-could-not-be-found-at-the-expected-directory-msymdir"></a>MT0118: não foi possível encontrar arquivos AOT no diretório esperado ' {msymdir} '.

<!-- 0119 - 0123: free to use -->

<a name="MT0123"></a>

### <a name="mt0123-the-executable-assembly--does-not-reference-"></a>MT0123: o assembly executável \* não faz referência a \* .

Nenhuma referência foi encontrada para o assembly da plataforma (Xamarin.iOS.dll/Xamarin.TVOS.dll/Xamarin.WatchOS.dll) no assembly executável.

Isso normalmente acontece quando não há nenhum código no projeto executável que usa qualquer coisa do assembly da plataforma; por exemplo, um método principal vazio (e nenhum outro código) mostraria esse erro:

```csharp
class Program {
    void Main (string[] args)
    {
    }
}
```

O uso de uma API do assembly da plataforma resolverá o erro:

```csharp
class Program {
    void Main (string[] args)
    {
        System.Console.WriteLine (typeof (UIKit.UIWindow));
    }
}
```

<a name="MT0124"></a>

### <a name="mt0124-could-not-set-the-current-language-to-lang-according-to-langlang-exception"></a>MT0124: não foi possível definir o idioma atual para ' {Lang} ' (de acordo com LANG = {LANG}): {Exception}

Este é um aviso, indicando que o idioma atual não pôde ser definido como o idioma na mensagem de erro.

O idioma atual usará como padrão o idioma do sistema.

<a name="MT0125"></a>

### <a name="mt0125-the---assembly-build-target-command-line-argument-is-ignored-in-the-simulator"></a>MT0125: o argumento de linha de comando--assembly-Build-Target é ignorado no simulador.

Nenhuma ação é necessária, essa mensagem é puramente informativa.

<a name="MT0126"></a>

### <a name="mt0126-incremental-builds-have-been-disabled-because-incremental-builds-are-not-supported-in-the-simulator"></a>MT0126: compilações incrementais foram desabilitadas porque não há suporte para compilações incrementais no simulador.

Nenhuma ação é necessária, essa mensagem é puramente informativa.

<a name="MT0127"></a>

### <a name="mt0127-incremental-builds-have-been-disabled-because-this-version-of-xamarinios-does-not-support-incremental-builds-in-projects-that-include-more-than-one-third-party-binding-libraries"></a>MT0127: compilações incrementais foram desabilitadas porque esta versão do Xamarin. iOS não oferece suporte a compilações incrementais em projetos que incluem mais de uma biblioteca de associações de terceiros.

Compilações incrementais foram desabilitadas automaticamente porque essa versão do Xamarin. iOS nem sempre cria projetos com várias bibliotecas de associação de terceiros corretamente.

Nenhuma ação é necessária, essa mensagem é puramente informativa.

Para obter mais informações, consulte bug n º[52727](https://bugzilla.xamarin.com/show_bug.cgi?id=52727).

<a name="MT0128"></a>

### <a name="mt0128-could-not-touch-the-file--"></a>MT0128: não foi possível tocar no arquivo ' \* ': \*

Ocorreu uma falha ao tocar um arquivo (que é feito para garantir que compilações parciais sejam feitas corretamente).

Esse aviso pode provavelmente ser ignorado; no caso de problemas, execute um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) e ele será investigado.

<a name="MT0135"></a>

### <a name="mt0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MT0135: não vinculou a estrutura do sistema ' {0} ' (referenciada pelo assembly ' {1} ') porque ela foi introduzida no {2} {3} e estamos usando o {2} {4} SDK.

Para compilar seu aplicativo, o Xamarin. iOS deve vincular as bibliotecas do sistema, alguns dos quais dependem da versão do SDK especificada na mensagem de erro. Como você está usando uma versão mais antiga do SDK, as invocações para essas APIs podem falhar em tempo de execução.

A maneira recomendada para corrigir esse erro é atualizar o Xcode para obter o SDK necessário. Se você tiver várias versões do Xcode instaladas ou quiser usar um Xcode em um local não padrão, certifique-se de definir o local do Xcode correto nas preferências do IDE.

Como alternativa, habilite o [vinculador](../deploy-test/linker.md) gerenciado para remover APIs não usadas, incluindo (na maioria dos casos) as novas que exigem a biblioteca especificada. No entanto, isso não funcionará se o seu projeto exigir APIs introduzidas em um SDK mais recente do que o que o Xcode fornece.

Como uma solução Straw, use uma versão mais antiga do Xamarin. iOS que não exija que esses novos SDKs estejam presentes durante o processo de compilação.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: mensagens de erro relacionadas ao projeto

### <a name="mt10xx-installer--mtouch"></a>MT10xx: instalador/mTouch

<!--
 MT1xxx file copy / symlinks (project related)
  MT10xx installer.cs / mtouch.cs
  -->

<a name="MT1001"></a>

### <a name="mt1001-could-not-find-an-application-at-the-specified-directory"></a>MT1001: não foi possível encontrar um aplicativo no diretório especificado

<a name="MT1002"></a>

### <a name="mt1002-could-not-create-symlinks-files-were-copied"></a>MT1002: não foi possível criar symlinks, os arquivos foram copiados

<a name="MT1003"></a>

### <a name="mt1003-could-not-kill-the-application--you-may-have-to-kill-the-application-manually"></a>MT1003: não foi possível eliminar o aplicativo ' * '. Talvez seja necessário encerrar o aplicativo manualmente.

<a name="MT1004"></a>

### <a name="mt1004-could-not-get-the-list-of-installed-applications"></a>MT1004: não foi possível obter a lista de aplicativos instalados.

## <a name="mt1xxx-project-related-error-messages"></a>MT1xxx: mensagens de erro relacionadas ao projeto

<a name="MT1005"></a>

### <a name="mt1005-could-not-kill-the-application--on-the-device----you-may-have-to-kill-the-application-manually"></a>MT1005: não foi possível eliminar o aplicativo ' \* ' no dispositivo ' \* ': *-Talvez você precise encerrar o aplicativo manualmente.

<a name="MT1006"></a>

### <a name="mt1006-could-not-install-the-application--on-the-device--"></a>MT1006: não foi possível instalar o aplicativo ' \* ' no dispositivo ' \* ': *.

<a name="MT1007"></a>

### <a name="mt1007-failed-to-launch-the-application--on-the-device---you-can-still-launch-the-application-manually-by-tapping-on-it"></a>MT1007: falha ao iniciar o aplicativo ' \* ' no dispositivo ' \* ': *. Você ainda pode iniciar o aplicativo manualmente tocando nele.

<a name="MT1008"></a>

### <a name="mt1008-failed-to-launch-the-simulator"></a>MT1008: falha ao iniciar o simulador

Esse erro será relatado se mTouch falhar ao iniciar o simulador.   Isso pode acontecer às vezes porque já existe um processo de simulador obsoleto ou inativo em execução.

O comando a seguir emitido na linha de comando do UNIX pode ser usado para eliminar processos de simuladores bloqueados:

```bash
$ launchctl list|grep UIKitApplication|awk '{print $3}'|xargs launchctl remove
```

<a name="MT1009"></a>

### <a name="mt1009-could-not-copy-the-assembly--to--"></a>MT1009: não foi possível copiar o assembly ' \* ' para ' \* ': *

Esse é um problema conhecido em determinadas versões do Xamarin. iOS.

Se isso ocorrer, tente a seguinte solução alternativa:

```bash
sudo chmod 0644 /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/*/*.mdb
```

No entanto, como esse problema foi resolvido na versão mais recente do Xamarin. iOS, registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com suas informações de versão completas e crie a saída do log.

<a name="MT1010"></a>

### <a name="mt1010-could-not-load-the-assembly--"></a>MT1010: não foi possível carregar o assembly ' \* ': \*

<a name="MT1011"></a>

### <a name="mt1011-could-not-add-missing-resource-file-"></a>MT1011: não foi possível adicionar o arquivo de recurso ausente: ' * '

<a name="MT1012"></a>

### <a name="mt1012-failed-to-list-the-apps-on-the-device--"></a>MT1012: falha ao listar os aplicativos no dispositivo ' \* ': \*

<a name="MT1013"></a>

### <a name="mt1013-dependency-tracking-error-no-files-to-compare-please-file-a-bug-report-at-httpbugzillaxamarincom-with-a-test-case"></a>MT1013: erro de controle de dependência: nenhum arquivo a ser comparado. Registre um relatório de bugs em http://bugzilla.xamarin.com com um caso de teste.

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

<a name="MT1014"></a>

### <a name="mt1014-failed-to-re-use-cached-version-of--"></a>MT1014: falha ao reutilizar a versão armazenada em cache de ' * ': *.

<a name="MT1015"></a>

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: falha ao criar o executável ' \* ': \*

<a name="MT1015"></a>

### <a name="mt1015-failed-to-create-the-executable--"></a>MT1015: falha ao criar o executável ' \* ': \*

<a name="MT1016"></a>

### <a name="mt1016-failed-to-create-the-notice-file-because-a-directory-already-exists-with-the-same-name"></a>MT1016: falha ao criar o arquivo de aviso porque já existe um diretório com o mesmo nome.

Remova o diretório `NOTICE` do projeto.

<a name="MT1017"></a>

### <a name="mt1017-failed-to-create-the-notice-file-"></a>MT1017: falha ao criar o arquivo de aviso: *.

<a name="MT1018"></a>

### <a name="mt1018-your-application-failed-code-signing-checks-and-could-not-be-installed-on-the-device--check-your-certificates-provisioning-profiles-and-bundle-ids-probably-your-device-is-not-part-of-the-selected-provisioning-profile-error-0xe8008015"></a>MT1018: seu aplicativo falhou em verificações de assinatura de código e não pôde ser instalado no dispositivo "*". Verifique seus certificados, perfis de provisionamento e IDs de pacote. Provavelmente, o dispositivo não faz parte do perfil de provisionamento selecionado (erro: 0xe8008015).

<a name="MT1019"></a>

### <a name="mt1019-your-application-has-entitlements-not-supported-by-your-current-provisioning-profile-and-could-not-be-installed-on-the-device--please-check-the-ios-device-log-for-more-detailed-information-error-0xe8008016"></a>MT1019: seu aplicativo tem direitos sem suporte pelo seu perfil de provisionamento atual e não pôde ser instalado no dispositivo ' * '. Consulte o log do dispositivo iOS para obter informações mais detalhadas (erro: 0xe8008016).

Isso pode ocorrer se:

- Seu aplicativo tem direitos para os quais o perfil de provisionamento atual não oferece suporte.
  Soluções possíveis:
  - Especifique um perfil de provisionamento diferente que dê suporte aos direitos de que seu aplicativo precisa.
  - Remova os direitos sem suporte no perfil de provisionamento atual.
- O dispositivo que você está tentando implantar não está incluído no perfil de provisionamento que você está usando.
  Soluções possíveis:
  - Crie um novo aplicativo com base em um modelo no Xcode, selecione o mesmo perfil de provisionamento e implante no mesmo dispositivo. Às vezes, o Xcode pode atualizar automaticamente os perfis de provisionamento com novos dispositivos (em outros casos, o Xcode perguntará o que fazer).
  -Vá para o centro de desenvolvimento do iOS e atualize o perfil de provisionamento com o novo dispositivo e baixe o perfil de provisionamento atualizado em seu computador.

Na maioria dos casos, mais informações sobre a falha serão impressas no log do dispositivo iOS, o que pode ajudar a diagnosticar o problema.

<a name="MT1020"></a>

### <a name="mt1020-failed-to-launch-the-application--on-the-device--"></a>MT1020: falha ao iniciar o aplicativo ' \* ' no dispositivo ' \* ': *

<a name="MT1021"></a>

### <a name="mt1021-could-not-copy-the-file--to--2"></a>MT1021: não foi possível copiar o arquivo ' \* ' para ' \* ': {2}

Não foi possível copiar um arquivo. A mensagem de erro da operação de cópia tem mais informações sobre o erro.

<a name="MT1022"></a>

### <a name="mt1022-could-not-copy-the-directory--to--2"></a>MT1022: não foi possível copiar o diretório ' \* ' para ' \* ': {2}

Não foi possível copiar um diretório. A mensagem de erro da operação de cópia tem mais informações sobre o erro.

<a name="MT1023"></a>

### <a name="mt1023-could-not-communicate-with-the-device-to-find-the-application---"></a>MT1023: não foi possível se comunicar com o dispositivo para localizar o aplicativo ' \* ': \*

Ocorreu um erro ao tentar Pesquisar um aplicativo no dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.

<a name="MT1024"></a>

### <a name="mt1024-the-application-signature-could-not-be-verified-on-device--please-make-sure-that-the-provisioning-profile-is-installed-and-not-expired-error-0xe8008017"></a>MT1024: não foi possível verificar a assinatura do aplicativo no dispositivo ' * '. Certifique-se de que o perfil de provisionamento esteja instalado e não tenha expirado (erro: 0xe8008017).

O dispositivo rejeitou a instalação do aplicativo porque a assinatura não pôde ser verificada.

Certifique-se de que o perfil de provisionamento esteja instalado e não tenha expirado.

<a name="MT1025"></a>

### <a name="mt1025-could-not-list-the-crash-reports-on-the-device-"></a>MT1025: não foi possível listar os relatórios de falhas no dispositivo *.

Ocorreu um erro ao tentar listar os relatórios de falhas no dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1026"></a>

### <a name="mt1026-could-not-download-the-crash-report--from-the-device-"></a>MT1026: não foi possível baixar o relatório \* de falhas do dispositivo \* .

Ocorreu um erro ao tentar baixar os relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1027"></a>

### <a name="mt1027-cant-use-xcode-7-to-launch-applications-on-devices-with-ios--xcode-7-only-supports-ios-6"></a>MT1027: não é possível usar o Xcode 7 + para iniciar aplicativos em dispositivos com iOS * (o Xcode 7 dá suporte apenas ao iOS 6 +).

Não é possível usar o Xcode 7 + para iniciar aplicativos em dispositivos com a versão do iOS abaixo de 6,0.

Use uma versão mais antiga do Xcode ou toque no aplicativo manualmente para iniciá-lo.

<a name="MT1028"></a>

### <a name="mt1028-invalid-device-specification--expected-ios-watchos-or-all"></a>MT1028: especificação de dispositivo inválida: ' * '. ' Ios ', ' watchos ' ou ' all' esperado.

A especificação do dispositivo passada usando--o dispositivo é inválido. Os valores válidos são: ' Ios ', ' watchos ' ou ' all'.

<a name="MT1029"></a>

### <a name="mt1029-could-not-find-an-application-at-the-specified-directory-"></a>MT1029: não foi possível encontrar um aplicativo no diretório especificado: *

O caminho do aplicativo passado para--launchdev não existe. Especifique um pacote de aplicativos válido.

<a name="MT1030"></a>

### <a name="mt1030-launching-applications-on-device-using-a-bundle-identifier-is-deprecated-please-pass-the-full-path-to-the-bundle-to-launch"></a>MT1030: iniciar aplicativos no dispositivo usando um identificador de pacote é preterido. Passe o caminho completo para o pacote a ser iniciado.

É recomendável passar o caminho para o aplicativo a ser iniciado no dispositivo, em vez de apenas a ID do pacote.

<a name="MT1031"></a>

### <a name="mt1031-could-not-launch-the-app--on-the-device--because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1031: não foi possível iniciar o aplicativo ' \* ' no dispositivo ' ' \* porque o dispositivo está bloqueado. Desbloqueie o dispositivo e tente novamente.

Desbloqueie o dispositivo e tente novamente.

<a name="MT1032"></a>

### <a name="mt1032-this-application-executable-might-be-too-large--mb-to-execute-on-device-if-bitcode-was-enabled-you-might-want-to-disable-it-for-development-it-is-only-required-to-submit-applications-to-apple"></a>MT1032: este executável de aplicativo pode ser muito grande (* MB) para ser executado no dispositivo. Se o BitCode foi habilitado, talvez você queira desabilitá-lo para desenvolvimento. ele só é necessário para enviar aplicativos para a Apple.

<a name="MT1033"></a>

### <a name="mt1033-could-not-uninstall-the-application--from-the-device--"></a>MT1033: não foi possível desinstalar o aplicativo ' \* ' do dispositivo ' ' \* : *

<!-- 1034 used by mmp -->

<a name="MT1035"></a>

### <a name="mt1035-cannot-include-different-versions-of-the-framework-name"></a>MT1035: não é possível incluir versões diferentes da estrutura ' {name} '

Não é possível vincular a versões diferentes da mesma estrutura.

Isso normalmente acontece quando uma extensão faz referência a uma versão diferente de uma estrutura do que o aplicativo de contêiner (possivelmente por meio de um assembly de associação de terceiros).

Após esse erro, haverá vários erros de [MT1036](#MT1036) que listam os caminhos para cada estrutura diferente.

<a name="MT1036"></a>

### <a name="mt1036-framework-name-included-from-path-related-to-previous-error"></a>MT1036: estrutura ' {name} ' incluída de: {Path} (relacionado ao erro anterior)

Esse erro é relatado apenas junto com [MT1036](#MT1036). Consulte [MT1036](#MT1036) para obter mais informações.

### <a name="mt11xx-debug-service"></a>MT11xx: serviço de depuração

<!--
  MT11xx DebugService.cs
  -->

<a name="MT1101"></a>

### <a name="mt1101-could-not-start-app"></a>MT1101: não foi possível iniciar o aplicativo

<a name="MT1102"></a>

### <a name="mt1102-could-not-attach-to-the-app-to-kill-it-"></a>MT1102: não foi possível anexar ao aplicativo (para eliminar): *

<a name="MT1103"></a>

### <a name="mt1103-could-not-detach"></a>MT1103: não foi possível desanexar

<a name="MT1104"></a>

### <a name="mt1104-failed-to-send-packet-"></a>MT1104: falha ao enviar o pacote: *

<a name="MT1105"></a>

### <a name="mt1105-unexpected-response-type"></a>MT1105: tipo de resposta inesperado

<a name="MT1106"></a>

### <a name="mt1106-could-not-get-list-of-applications-on-the-device-request-timed-out"></a>MT1106: não foi possível obter a lista de aplicativos no dispositivo: o tempo limite da solicitação foi atingido.

<a name="MT1107"></a>

### <a name="mt1107-application-failed-to-launch-"></a>MT1107: falha ao iniciar o aplicativo: *

Verifique se o dispositivo está bloqueado.

Se estiver implantando um aplicativo empresarial ou usando um perfil de provisionamento gratuito, você poderá confiar no desenvolvedor (isso é explicado <a href="https://stackoverflow.com/a/30726375/183422">aqui</a>).

<a name="MT1108"></a>

### <a name="mt1108-could-not-find-developer-tools-for-this-xx-yy-device"></a>MT1108: não foi possível encontrar ferramentas de desenvolvedor para este dispositivo XX (AA).

Algumas operações do mTouch exigem que o `DeveloperDiskImage.dmg` arquivo esteja presente.   Esse arquivo é parte do Xcode e geralmente está localizado em relação ao SDK que você está usando para se basear no `Xcode.app/Contents/Developer/iPhoneOS.platform/DeviceSupport/VERSION/DeveloperDiskImage.dmg` .

Esse erro pode ocorrer porque você não tem um DeveloperDiskImage. dmg que corresponda ao dispositivo que você conectou.

<a name="MT1109"></a>

### <a name="mt1109-application-failed-to-launch-because-the-device-is-locked-please-unlock-the-device-and-try-again"></a>MT1109: falha ao iniciar o aplicativo porque o dispositivo está bloqueado. Desbloqueie o dispositivo e tente novamente.

Verifique se o dispositivo está bloqueado.

<a name="MT1110"></a>

### <a name="mt1110-application-failed-to-launch-because-of-ios-security-restrictions-please-ensure-the-developer-is-trusted"></a>MT1110: falha na inicialização do aplicativo devido a restrições de segurança do iOS. Verifique se o desenvolvedor é confiável.

Se estiver implantando um aplicativo empresarial ou usando um perfil de provisionamento gratuito, você poderá confiar no desenvolvedor (isso é explicado <a href="https://stackoverflow.com/a/30726375/183422">aqui</a>).

<a name="MT1111"></a>

### <a name="mt1111-application-launched-successfully-but-its-not-possible-to-wait-for-the-app-to-exit-as-requested-because-its-not-possible-to-detect-app-termination-when-launching-using-gdbserver"></a>MT1111: o aplicativo foi iniciado com êxito, mas não é possível aguardar que o aplicativo seja encerrado como solicitado porque não é possível detectar o encerramento do aplicativo ao iniciar usando gdbserver.

### <a name="mt12xx-simulator"></a>MT12xx: simulador

<!--
  MT12xx simcontroller.cs
  -->

<a name="MT1201"></a>

### <a name="mt1201-could-not-load-the-simulator-"></a>MT1201: não foi possível carregar o simulador: *

<a name="MT1202"></a>

### <a name="mt1202-invalid-simulator-configuration-"></a>MT1202: configuração de simulador inválida: *

<a name="MT1203"></a>

### <a name="mt1203-invalid-simulator-specification-"></a>MT1203: especificação de simulador inválida: *

<a name="MT1204"></a>

### <a name="mt1204-invalid-simulator-specification--runtime-not-specified"></a>MT1204: especificação de simulador ' * ' inválida: tempo de execução não especificado.

<a name="MT1205"></a>

### <a name="mt1205-invalid-simulator-specification--device-type-not-specified"></a>MT1205: especificação de simulador ' * ' inválida: tipo de dispositivo não especificado.

<a name="MT1206"></a>

### <a name="mt1206-could-not-find-the-simulator-runtime-"></a>MT1206: não foi possível localizar o tempo de execução do simulador ' * '.

<a name="MT1207"></a>

### <a name="mt1207-could-not-find-the-simulator-device-type-"></a>MT1207: não foi possível encontrar o tipo de dispositivo do simulador ' * '.

<a name="MT1208"></a>

### <a name="mt1208-could-not-find-the-simulator-runtime-"></a>MT1208: não foi possível localizar o tempo de execução do simulador ' * '.

<a name="MT1209"></a>

### <a name="mt1209-could-not-find-the-simulator-device-type-"></a>MT1209: não foi possível encontrar o tipo de dispositivo do simulador ' * '.

<a name="MT1210"></a>

### <a name="mt1210-invalid-simulator-specification--unknown-key-"></a>MT1210: especificação de simulador inválida: \* , chave desconhecida ' \* '

<a name="MT1211"></a>

### <a name="mt1211-the-simulator-version--does-not-support-the-simulator-type-"></a>MT1211: a versão do simulador ' \* ' não oferece suporte ao tipo de simulador ' \* '

<a name="MT1212"></a>

### <a name="mt1212-failed-to-create-a-simulator-version-where-type---and-runtime--"></a>MT1212: falha ao criar uma versão de simulador em que Type = \* e Runtime = \* .

<a name="MT1213"></a>

### <a name="mt1213-invalid-simulator-specification-for-xcode-4-"></a>MT1213: especificação de simulador inválida para Xcode 4: *

<a name="MT1214"></a>

### <a name="mt1214-invalid-simulator-specification-for-xcode-5-"></a>MT1214: especificação de simulador inválida para Xcode 5: *

<a name="MT1215"></a>

### <a name="mt1215-invalid-sdk-specified-"></a>MT1215: SDK inválido especificado: *

<a name="MT1216"></a>

### <a name="mt1216-could-not-find-the-simulator-udid-"></a>MT1216: não foi possível encontrar o simulador UDID ' * '.

<a name="MT1217"></a>

### <a name="mt1217-could-not-load-the-app-bundle-at-"></a>MT1217: não foi possível carregar o pacote de aplicativos em ' * '.

<a name="MT1218"></a>

### <a name="mt1218-no-bundle-identifier-found-in-the-app-at-"></a>MT1218: nenhum identificador de pacote encontrado no aplicativo em ' * '.

<a name="MT1219"></a>

### <a name="mt1219-could-not-find-the-simulator-for-"></a>MT1219: não foi possível encontrar o simulador para ' * '.

<a name="MT1220"></a>

### <a name="mt1220-could-not-find-the-latest-simulator-runtime-for-device-"></a>MT1220: não foi possível encontrar o tempo de execução do simulador mais recente para o dispositivo ' * '.

Isso geralmente indica um problema com o Xcode.

Coisas para tentar corrigir isso:

- Use o simulador uma vez no Xcode.
- Passe uma versão do SDK explícita usando--SDK \<version> .
- Reinstale o Xcode.

<a name="MT1221"></a>

### <a name="mt1221-could-not-find-the-paired-iphone-simulator-for-the-watchos-simulator-"></a>MT1221: não foi possível encontrar o simulador de iPhone emparelhado para o simulador de WatchOS ' * '.

Ao iniciar um aplicativo WatchOS em um simulador de WatchOS, também deve haver um simulador de iOS emparelhado.

Os simuladores de inspeção podem ser emparelhados com simuladores do iOS usando a interface do usuário de dispositivos do Xcode (janela de menu > dispositivos).

### <a name="mt13xx-linkwith"></a>MT13xx: [LinkWith]

<!--
  MT13xx [LinkWith]
  -->

<a name="MT1301"></a>

### <a name="mt1301-native-library---was-ignored-since-it-does-not-match-the-current-build-architectures-"></a>MT1301: a biblioteca nativa `*` ( \* ) foi ignorada porque não corresponde às arquiteturas de compilação atuais ( \* )

<a name="MT1302"></a>

### <a name="mt1302-could-not-extract-the-native-library--from--please-ensure-the-native-library-was-properly-embedded-in-the-managed-assembly-if-the-assembly-was-built-using-a-binding-project-the-native-library-must-be-included-in-the-project-and-its-build-action-must-be-objcbindingnativelibrary"></a>MT1302: não foi possível extrair a biblioteca nativa ' * ' de ' + '. Verifique se a biblioteca nativa foi inserida corretamente no assembly gerenciado (se o assembly foi criado usando um projeto de associação, a biblioteca nativa deve ser incluída no projeto e sua ação de compilação deve ser ' ObjcBindingNativeLibrary ').

<a name="MT1303"></a>

### <a name="mt1303-could-not-decompress-the-native-framework--from--please-review-the-build-log-for-more-information-from-the-native-zip-command"></a>MT1303: não foi possível descompactar a estrutura nativa ' \* ' de ' \* '. Examine o log de compilação para obter mais informações do comando ' zip ' nativo.

Não foi possível descompactar a estrutura nativa especificada da biblioteca de associação.

Examine o log do bulid para obter mais informações sobre essa falha do comando ' zip ' nativo.

<a name="MT1304"></a>

### <a name="mt1304-the-embedded-framework--in--is-invalid-it-does-not-contain-an-infoplist"></a>MT1304: a estrutura inserida ' \* ' em \* é inválida: ela não contém um info. plist.

A estrutura inserida especificada não contém um info. plist e, portanto, não é uma estrutura válida.

Certifique-se de que a estrutura é válida.

<a name="MT1305"></a>

### <a name="mt1305-the-binding-library--contains-a-user-framework--but-embedded-user-frameworks-require-ios-80-the-current-deployment-target-is--please-set-the-deployment-target-in-the-infoplist-file-to-at-least-80"></a>MT1305: a biblioteca de associação ' \* ' contém uma estrutura de usuário ( \* ), mas as estruturas de usuário inseridas exigem o Ios 8,0 (o destino de implantação atual é *). Defina o destino de implantação no arquivo info. plist para pelo menos 8,0.

A biblioteca de associação especificada contém uma estrutura inserida, mas o Xamarin. iOS só dá suporte a estruturas inseridas no iOS 8,0 ou posterior.

Defina o destino de implantação no arquivo info. plist para pelo menos 8,0 para resolver esse erro (ou não use estruturas inseridas).

### <a name="mt14xx-crash-reports"></a>MT14xx: relatórios de falha

<!--
  MT14xx    CrashReports.cs
  -->

<a name="MT1400"></a>

### <a name="mt1400-could-not-open-crash-report-service-afcconnectionopen-returned-"></a>MT1400: não foi possível abrir o serviço de relatório de falhas: AFCConnectionOpen retornou *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1401"></a>

### <a name="mt1401-could-not-close-crash-report-service-afcconnectionclose-returned-"></a>MT1401: não foi possível fechar o serviço de relatório de falhas: AFCConnectionClose retornou *

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1402"></a>

### <a name="mt1402-could-not-read-file-info-for--afcfileinfoopen-returned-"></a>MT1402: não foi possível ler as informações do arquivo para \* : AFCFileInfoOpen retornado \*

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1403"></a>

### <a name="mt1403-could-not-read-crash-report-afcdirectoryopen--returned-"></a>MT1403: não foi possível ler o relatório de falhas: AFCDirectoryOpen ( \* ) retornou: \*

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1404"></a>

### <a name="mt1404-could-not-read-crash-report-afcfilerefopen--returned-"></a>MT1404: não foi possível ler o relatório de falhas: AFCFileRefOpen ( \* ) retornou: \*

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1405"></a>

### <a name="mt1405-could-not-read-crash-report-afcfilerefread--returned-"></a>MT1405: não foi possível ler o relatório de falhas: AFCFileRefRead ( \* ) retornou: \*

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<a name="MT1406"></a>

### <a name="mt1406-could-not-list-crash-reports-afcdirectoryopen--returned-"></a>MT1406: não foi possível listar os relatórios de falha: AFCDirectoryOpen ( \* ) retornado: \*

Ocorreu um erro ao tentar acessar relatórios de falha do dispositivo.

Coisas para tentar resolver isso:

- Exclua o aplicativo do dispositivo e tente novamente.
- Desconecte o dispositivo e reconecte-o.
- Reinicialize o dispositivo.
- Reinicialize o Mac.
- Sincronize o dispositivo com o iTunes (isso removerá todos os relatórios de falha do dispositivo).

<!--- 1407 used by mmp -->

### <a name="mt16xx-macho"></a>MT16xx: MachO

<!--
  MT16xx    MachO.cs
  -->

<a name="MT1600"></a>

### <a name="mt1600-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1600: não é uma biblioteca dinâmica de Mach-O (cabeçalho desconhecido ' 0x * '): *.

Ocorreu um erro ao processar a biblioteca dinâmica em questão.

Certifique-se de que a biblioteca dinâmica é uma biblioteca dinâmica de Mach-O válida.

O formato de uma biblioteca pode ser verificado usando o `file` comando de um terminal:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1601"></a>

### <a name="mt1601-not-a-static-library-unknown-header--"></a>MT1601: não é uma biblioteca estática (cabeçalho desconhecido ' * '): *.

Ocorreu um erro ao processar a biblioteca estática em questão.

Certifique-se de que a biblioteca estática seja uma biblioteca estática de Mach-O válida.

O formato de uma biblioteca pode ser verificado usando o `file` comando de um terminal:

```
file -arch all -l /path/to/library.a
```

<a name="MT1602"></a>

### <a name="mt1602-not-a-mach-o-dynamic-library-unknown-header-0x-"></a>MT1602: não é uma biblioteca dinâmica de Mach-O (cabeçalho desconhecido ' 0x * '): *.

Ocorreu um erro ao processar a biblioteca dinâmica em questão.

Certifique-se de que a biblioteca dinâmica é uma biblioteca dinâmica de Mach-O válida.

O formato de uma biblioteca pode ser verificado usando o `file` comando de um terminal:

```
file -arch all -l /path/to/library.dylib
```

<a name="MT1603"></a>

### <a name="mt1603-unknown-format-for-fat-entry-at-position--in-"></a>MT1603: formato desconhecido para a entrada de Fat na posição \* em \* .

Ocorreu um erro ao processar o arquivo FAT em questão.

Certifique-se de que o arquivo FAT é válido.

O formato de um arquivo FAT pode ser verificado usando o `file` comando de um terminal:

```
file -arch all -l /path/to/file
```

<a name="MT1604"></a>

### <a name="mt1604-file-of-type--is-not-a-macho-file-"></a>MT1604: o arquivo do tipo \* não é um arquivo MachO ( \* ).

Ocorreu um erro ao processar o arquivo MachO em questão.

Verifique se o arquivo é uma biblioteca dinâmica de Mach-O válida.

O formato de um arquivo pode ser verificado usando o `file` comando de um terminal:

```
file -arch all -l /path/to/file
```

## <a name="mt2xxx-linker-error-messages"></a>MT2xxx: mensagens de erro do vinculador

<!--
 MT2xxx Linker
  MT20xx Linker (general) errors
  -->

<a name="MT2001"></a>

### <a name="mt2001-could-not-link-assemblies"></a>MT2001: não foi possível vincular assemblies

Esse erro significa que o vinculador gerenciado encontrou um erro inesperado, por exemplo, uma exceção, e não pôde concluir ou salvar o assembly que está sendo processado. Mais informações sobre o erro exato serão parte do log de compilação, por exemplo,

```
error MT2001: Could not link assemblies.
    Method: `System.Void Todo.TodoListPageCS/<<-ctor>b__1_0>d::SetStateMachine(System.Runtime.CompilerServices.IAsyncStateMachine)`
    Assembly: `QuickTodo, Version=1.0.6297.28241, Culture=neutral, PublicKeyToken=null`
Reason: Value cannot be null.
Parameter name: instruction
```

É importante arquivar um relatório de bugs para esses problemas. Na maioria dos casos, uma solução alternativa pode ser fornecida até que uma correção adequada seja publicada. As informações acima são críticas (juntamente com um caso de teste e/ou o assembly binairy) para resolver o problema.

<a name="MT2002"></a>

### <a name="mt2002-can-not-resolve-reference-"></a>MT2002: não é possível resolver a referência: *

<a name="MT2003"></a>

### <a name="mt2003-option--will-be-ignored-since-linking-is-disabled"></a>MT2003: a opção ' * ' será ignorada porque a vinculação está desabilitada

<a name="MT2004"></a>

### <a name="mt2004-extra-linker-definitions-file--could-not-be-located"></a>MT2004: não foi possível localizar o arquivo de definições de vinculador extra ' * '.

<a name="MT2005"></a>

### <a name="mt2005-definitions-from--could-not-be-parsed"></a>MT2005: não foi possível analisar as definições de ' * '.

<a name="MT2006"></a>

### <a name="mt2006-can-not-load-mscorlibdll-from--please-reinstall-xamarinios"></a>MT2006: não é possível carregar mscorlib.dll de: *. Reinstale o Xamarin. iOS.

Isso geralmente indica que há um problema com a instalação do Xamarin. iOS. Tente reinstalar o Xamarin. iOS.

<!--- 2007 used by mmp -->
<!--- 2009 used by mmp -->

<a name="MT2010"></a>

### <a name="mt2010-unknown-httpmessagehandler--valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MT2010: HttpMessageHandler desconhecido `*` . Os valores válidos são HttpClientHandler (padrão), CFNetworkHandler ou NSUrlSessionHandler

<a name="MT2011"></a>

### <a name="mt2011-unknown-tlsprovider---valid-values-are-default-or-appletls"></a>MT2011: TlsProvider desconhecido `*` .  Os valores válidos são Default ou appletls.

O valor fornecido para `tls-provider=` não é um provedor de TLS (segurança de camada de transporte) válido.

O `default` e `appletls` são os únicos valores válidos e ambos representam a mesma opção, que é fornecer o suporte a SSL/TLS usando a API nativa do TLS da Apple.

<!--- 2012 used by mmp -->
<!--- 2013 used by mmp -->
<!--- 2014 used by mmp -->

<a name="MT2015"></a>

### <a name="mt2015-invalid-httpmessagehandler--for-watchos-the-only-valid-value-is-nsurlsessionhandler"></a>MT2015: HttpMessageHandler inválido `*` para watchOS. O único valor válido é NSUrlSessionHandler.

Este é um aviso que ocorre porque o arquivo de projeto especifica um HttpMessageHandler inválido.

Versões anteriores de nossas ferramentas de visualização geradas por padrão um valor inválido no arquivo de projeto.

Para corrigir esse aviso, abra o arquivo de projeto em um editor de texto e remova todos os nós HttpMessageHandler do XML.

<a name="MT2016"></a>

### <a name="mt2016-invalid-tlsprovider-legacy-option-the-only-valid-value-appletls-will-be-used"></a>MT2016: opção TlsProvider inválida `legacy` . O único valor válido `appletls` será usado.

O `legacy` provedor, que era um provedor totalmente gerenciado de SSLv3/TLSv1, não é mais fornecido com o Xamarin. Ios. Projetos que estavam usando esse provedor antigo e agora são criados com o mais recente `appletls` .

Para corrigir esse aviso, abra o arquivo de projeto em um editor de texto e remova todos os nós ' MtouchTlsProvider ' do XML.

<a name="MT2017"></a>

### <a name="mt2017-could-not-process-xml-description"></a>MT2017: não foi possível processar a descrição XML.

Isso significa que há um erro no [arquivo de configuração do vinculador XML personalizado](~/cross-platform/deploy-test/linker.md) fornecido por você. Examine o arquivo.

<a name="MT2018"></a>

### <a name="mt2018-the-assembly--is-referenced-from-two-different-locations--and-"></a>MT2018: o assembly ' \* ' é referenciado de dois locais diferentes: ' \* ' e ' * '.

O assembly mencionado na mensagem de erro é carregado de vários locais. Certifique-se de sempre usar a mesma versão de um assembly.

<a name="MT2019"></a>

### <a name="mt2019-can-not-load-the-root-assembly-"></a>MT2019: não é possível carregar o assembly raiz ' * '

Não foi possível carregar o assembly raiz. Verifique se o caminho na mensagem de erro refere-se a um arquivo existente e se é um assembly .NET válido.

<a name="MT202x"></a>

### <a name="mt202x-binding-optimizer-failed-processing-"></a>MT202x: o processamento da ligação do otimizador falhou `...` .

Ocorreu algo inesperado ao tentar otimizar o código de associação gerado. O elemento que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly chamado (ou que contém o tipo ou o método nomeado) precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

O último dígito `x` será:

- `0` para um nome de assembly;
- `1` para um nome de tipo;
- `3` para um nome de método;

<a name="MT2030"></a>

### <a name="mt2030-remove-user-resources-failed-processing-"></a>MT2030: Remove o processamento de recursos do usuário com falha `...` .

Ocorreu algo inesperado ao tentar remover os recursos do usuário. O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

Os recursos do usuário são arquivos incluídos dentro de assemblies (como recursos) que precisam ser extraídos, no momento da compilação, para criar o pacote de aplicativos. Isso inclui:

- `__monotouch_content_*` e `__monotouch_pages_*` recursos; e
- Bibliotecas nativas inseridas dentro de um assembly de associação;

<a name="MT2040"></a>

### <a name="mt2040-default-httpmessagehandler-setter-failed-processing-"></a>MT2040: falha no processamento do setter do HttpMessageHandler padrão `...` .

Ocorreu algo inesperado ao tentar definir o padrão `HttpMessageHandler` para o aplicativo. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2050"></a>

### <a name="mt2050-code-remover-failed-processing-"></a>MT2050: falha no processamento do removedor de código `...` .

Algo inesperado ocorreu ao tentar remover o código do envio de BCL com o aplicativo. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) junto com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2060"></a>

### <a name="mt2060-sealer-failed-processing-"></a>MT2060: falha no processamento do selo `...` .

Ocorreu algo inesperado ao tentar lacrar tipos ou métodos (final) ou ao desvirtualizar alguns métodos. O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2070"></a>

### <a name="mt2070-metadata-reducer-failed-processing-"></a>MT2070: falha no processamento de redutor de metadados `...` .

Ocorreu algo inesperado ao tentar reduzir os metadados do aplicativo. O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2080"></a>

### <a name="mt2080-marknsobjects-failed-processing-"></a>MT2080: MarkNSObjects falhou ao processar `...` .

Ocorreu algo inesperado ao tentar marcar `NSObject` subclasses a partir do aplicativo. O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2090"></a>

### <a name="mt2090-inliner-failed-processing-"></a>MT2090: falha no processamento do inlineer `...` .

Ocorreu algo inesperado ao tentar o código embutido do aplicativo. O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<!-- MT21xx: more linker errors -->

<!--- 2100 used by mmp -->

<a name="MT2100"></a>

### <a name="mt2100-smart-enum-conversion-preserver-failed-processing-"></a>MT2100: falha no processamento do servidor de conversão de enumeração inteligente `...` .

Ocorreu algo inesperado ao tentar marcar os métodos de conversão para enums inteligentes do aplicativo. O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2101"></a>

### <a name="mt2101-cant-resolve-the-reference--referenced-from-the-method--in-"></a>MT2101: não é possível resolver a referência ' \* ', referenciada do método ' \* ' em ' * '.

Foi encontrada uma referência de assembly inválida ao processar o método mencionado na mensagem de erro.

O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2102"></a>

### <a name="mt2102-error-processing-the-method--in-the-assembly--"></a>MT2102: erro ao processar o método ' \* ' no assembly ' \* ': *

Ocorreu algo inesperado ao tentar marcar o método mencionado na mensagem de erro.

O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2103"></a>

### <a name="mt2103-error-processing-assembly--"></a>MT2103: erro ao processar o assembly ' \* ': *

Ocorreu um erro inesperado ao processar um assembly.

O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) , juntamente com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MT2104"></a>

### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: não é possível vincular o assembly ' {0} ' porque ele é de modo misto.

Os assemblies de modo misto não podem ser processados pelo vinculador.

Consulte https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies para obter mais informações sobre assemblies de modo misto.

## <a name="mt3xxx-aot-error-messages"></a>MT3xxx: mensagens de erro da AOT

<!--
 MT3xxx AOT
  MT30xx AOT (general) errors
  -->

<a name="MT3001"></a>

### <a name="mt3001-could-not-aot-the-assembly-"></a>MT3001: não foi possível AOT o assembly ' * '

Isso geralmente indica um bug no compilador AOT. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um projeto que pode ser usado para reproduzir o erro.

Às vezes, é possível contornar isso desabilitando compilações incrementais na opção de Build do iOS do projeto (mas ainda é um bug, portanto, informe-o de qualquer forma).

<a name="MT3002"></a>

### <a name="mt3002-aot-restriction-method--must-be-static-since-it-is-decorated-with-monopinvokecallback-see-developerxamarincomguidesiosadvanced_topicslimitationsreverse_callbacks"></a>MT3002: restrição de AOT: o método ' * ' deve ser estático, pois está decorado com [MonoPInvokeCallback]. Consulte [developer.xamarin.com/guides/ios/advanced_topics/limitations/#Reverse_Callbacks](~/ios/internals/limitations.md#reverse-callbacks)

Essa mensagem de erro é proveniente do compilador AOT.

<a name="MT3003"></a>

### <a name="mt3003-conflicting---debug-and---llvm-options-soft-debugging-is-disabled"></a>MT3003: opções conflitantes--Debug e--LLVM. A depuração reversível está desabilitada.

Não há suporte para depuração quando o LLVM está habilitado. Se você precisar depurar o aplicativo, desabilite o LLVM primeiro.

<a name="MT3004"></a>

### <a name="mt3004-could-not-aot-the-assembly--because-it-doesnt-exist"></a>MT3004: não foi possível AOT o assembly ' * ' porque ele não existe.

<a name="MT3005"></a>

### <a name="mt3005-the-dependency--of-the-assembly--was-not-found-please-review-the-projects-references"></a>MT3005: a dependência ' \* ' do assembly ' \* ' não foi encontrada. Examine as referências do projeto.

Isso normalmente ocorre quando um assembly faz referência a outra versão de um assembly de plataforma (geralmente a versão .NET 4 do mscorlib.dll).

Isso não tem suporte e pode não ser compilado ou executado corretamente (o assembly pode usar a API da versão .NET 4 do mscorlib.dll que a versão do Xamarin. iOS não tem).

<a name="MT3006"></a>

### <a name="mt3006-could-not-compute-a-complete-dependency-map-for-the-project-this-will-result-in-slower-build-times-because-xamarinios-cant-properly-detect-what-needs-to-be-rebuilt-and-what-does-not-need-to-be-rebuilt-please-review-previous-warnings-for-more-details"></a>MT3006: não foi possível computar um mapa de dependência completo para o projeto. Isso resultará em tempos de compilação mais lentos, pois o Xamarin. iOS não pode detectar corretamente o que precisa ser recriado (e o que não precisa ser reconstruído). Examine os avisos anteriores para obter mais detalhes.

 Compile ou execute corretamente (o assembly pode usar a API da versão .NET 4 do mscorlib.dll que a versão Xamarin. iOS não tem).

<a name="MT3007"></a>

### <a name="mt3007-debug-info-files-mdb-will-not-be-loaded-when-llvm-is-enabled"></a>MT3007: os arquivos de informações de depuração (*. mdb) não serão carregados quando LLVM estiver habilitado.

<a name="MT3008"></a>

### <a name="mt3008-bitcode-support-requires-the-use-of-the-llvm-aot-backend---llvm"></a>MT3008: o suporte do BitCode requer o uso do back-end da AOT LLVM (--LLVM)

O suporte do BitCode requer o uso do back-end da AOT do LLVM (--LLVM).

Desabilite o suporte do BitCode ou habilite o LLVM.

<!--- 3009 used by mmp -->
<!--- 3010 used by mmp -->

## <a name="mt4xxx-code-generation-error-messages"></a>MT4xxx: mensagens de erro de geração de código

### <a name="mt40xx-main"></a>MT40xx: Main

<!--
 MT4xxx code generation
  MT40xx main.m
  -->

<a name="MT4001"></a>

### <a name="mt4001-the-main-template-could-not-be-expanded-to-"></a>MT4001: não foi possível expandir o modelo principal para `*` .

Ocorreu um erro ao gerar `main.m` . Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4002"></a>

### <a name="mt4002-failed-to-compile-the-generated-code-for-pinvoke-methods-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4002: falha ao compilar o código gerado para métodos P/Invoke. Registre um relatório de bugs em http://bugzilla.xamarin.com

Falha ao compilar o código gerado para métodos P/Invoke. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt41xx-registrar"></a>MT41xx: registrador

<!--
  MT41xx registrar.m
  -->

<a name="MT4101"></a>

### <a name="mt4101-the-registrar-cannot-build-a-signature-for-type-"></a>MT4101: o registrador não pode criar uma assinatura para o tipo `*` .

Foi encontrado um tipo na API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acreditar que o Xamarin. iOS deve dar suporte ao tipo em questão, registre uma solicitação de aprimoramento no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4102"></a>

### <a name="mt4102-the-registrar-found-an-invalid-type--in-signature-for-method--use--instead"></a>MT4102: o registrador encontrou um tipo inválido `*` na assinatura para o método `*` . Use `*` em vez disso.

Atualmente, isso ocorre apenas com um tipo: System. DateTime. Em vez disso, use o equivalente de Objective-C (NSDate).

<a name="MT4103"></a>

### <a name="mt4103-the-registrar-found-an-invalid-type--in-signature-for-method--the-type-implements-inativeobject-but-does-not-have-a-constructor-that-takes-two-intptr-bool-arguments"></a>MT4103: o registrador encontrou um tipo inválido `*` na assinatura para `*` o método: o tipo implementa INativeObject, mas não tem um construtor que aceite dois argumentos (IntPtr, bool)

Isso ocorre quando o registrador encontra um tipo em uma assinatura com as características mencionadas. O registrador pode precisar criar novas instâncias do tipo e, nesse caso, ele requer um construtor com a assinatura (IntPtr, bool) – o primeiro argumento (IntPtr) especifica o identificador gerenciado, enquanto o segundo se o chamador passa pela propriedade do identificador nativo (se esse valor for false, ' Retain ' será chamado no objeto).

<a name="MT4104"></a>

### <a name="mt4104-the-registrar-cannot-marshal-the-return-value-for-type--in-signature-for-method-"></a>MT4104: o registrador não pode realizar marshaling do valor de retorno para o tipo `*` na assinatura do método `*` .

Foi encontrado um tipo na API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acreditar que o Xamarin. iOS deve dar suporte ao tipo em questão, registre uma solicitação de aprimoramento no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4105"></a>

### <a name="mt4105-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4105: o registrador não pode realizar marshaling do parâmetro do tipo `*` na assinatura para o método `*` .

Se você acreditar que o Xamarin. iOS deve dar suporte ao tipo em questão, registre uma solicitação de aprimoramento no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4106"></a>

### <a name="mt4106-the-registrar-cannot-marshal-the-return-value-for-structure--in-signature-for-method-"></a>MT4106: o registrador não pode realizar marshaling do valor de retorno para a estrutura `*` na assinatura do método `*` .

Foi encontrado um tipo na API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acreditar que o Xamarin. iOS deve dar suporte ao tipo em questão, registre uma solicitação de aprimoramento no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4107"></a>

### <a name="mt4107-the-registrar-cannot-marshal-the-parameter-of-type--in-signature-for-method-"></a>MT4107: o registrador não pode realizar marshaling do parâmetro do tipo `*` na assinatura para o método `+` .

Foi encontrado um tipo na API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acreditar que o Xamarin. iOS deve dar suporte ao tipo em questão, registre uma solicitação de aprimoramento no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4108"></a>

### <a name="mt4108-the-registrar-cannot-get-the-objectivec-type-for-managed-type-"></a>MT4108: o registrador não pode obter o tipo ObjectiveC para o tipo gerenciado `*` .

Foi encontrado um tipo na API exportada que o tempo de execução não sabe como realizar marshaling de/para Objective-C.

Se você acreditar que o Xamarin. iOS deve dar suporte ao tipo em questão, registre uma solicitação de aprimoramento no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4109"></a>

### <a name="mt4109-failed-to-compile-the-generated-registrar-code-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4109: falha ao compilar o código de registrador gerado. Registre um relatório de bugs em http://bugzilla.xamarin.com

Falha ao compilar o código gerado para o registrador. O log de compilação conterá a saída do compilador nativo, explicando por que o código não está compilando.

Esse é sempre um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com seu projeto ou um caso de teste.

<a name="MT4110"></a>

### <a name="mt4110-the-registrar-cannot-marshal-the-out-parameter-of-type--in-signature-for-method-"></a>MT4110: o registrador não pode realizar marshaling do parâmetro out do tipo `*` na assinatura para o método `*` .

<a name="MT4111"></a>

### <a name="mt4111-the-registrar-cannot-build-a-signature-for-type--in-method-"></a>MT4111: o registrador não pode criar uma assinatura para o tipo `*` no método `*` .

<a name="MT4112"></a>

### <a name="mt4112-the-registrar-found-an-invalid-type--registering-generic-types-with-objective-c-is-not-supported-and-may-lead-to-random-behavior-andor-crashes-for-backwards-compatibility-with-older-versions-of-xamarinios-it-is-possible-to-ignore-this-error-by-passing---unsupported--enable-generics-in-registrar-as-an-additional-mtouch-argument-in-the-projects-ios-build-options-page-see-developerxamarincomguidesiosadvanced_topicsregistrar-for-more-information"></a>MT4112: o registrador encontrou um tipo inválido `*` . Não há suporte para o registro de tipos genéricos com Objective-C e isso pode levar a um comportamento aleatório e/ou falhas (para compatibilidade com versões anteriores do Xamarin. iOS, é possível ignorar esse erro passando `--unsupported--enable-generics-in-registrar` como um argumento mTouch adicional na página de opções de Build do projeto Ios. Consulte [Developer.xamarin.com/Guides/Ios/advanced_topics/registrar](~/ios/internals/registrar.md) para obter mais informações).

<a name="MT4113"></a>

### <a name="mt4113-the-registrar-found-a-generic-method--exporting-generic-methods-is-not-supported-and-will-lead-to-random-behavior-andor-crashes"></a>MT4113: o registrador encontrou um método genérico: ' \* . \* '. Não há suporte para a exportação de métodos genéricos e isso resultará em comportamento aleatório e/ou falhas.

<a name="MT4114"></a>

### <a name="mt4114-unexpected-error-in-the-registrar-for-the-method----please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4114: erro inesperado no registrador para o método ' \* . \* ' – registre um relatório de bug em http://bugzilla.xamarin.com

<a name="MT4116"></a>

### <a name="mt4116-could-not-register-the-assembly--"></a>MT4116: não foi possível registrar o assembly ' \* ': \*

<a name="MT4117"></a>

### <a name="mt4117-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4117: o registrador encontrou uma incompatibilidade de assinatura no método ' \* . \* ' – o seletor indica que o método usa \* parâmetros, enquanto o método gerenciado tem \* parâmetros.

<a name="MT4118"></a>

### <a name="mt4118-cannot-register-two-managed-types--and--with-the-same-native-name-"></a>MT4118: não é possível registrar dois tipos gerenciados (' \* ' e ' \* ') com o mesmo nome nativo (' * ').

<a name="MT4119"></a>

### <a name="mt4119-could-not-register-the-selector--of-the-member--because-the-selector-is-already-registered-on-a-different-member"></a>MT4119: não foi possível registrar o seletor ' \* ' do membro ' \* . * ' porque o seletor já está registrado em um membro diferente.

<a name="MT4120"></a>

### <a name="mt4120-the-registrar-found-an-unknown-field-type--in-field--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4120: o registrador encontrou um tipo de campo desconhecido ' \* ' no campo ' \* . * '. Registre um relatório de bugs em http://bugzilla.xamarin.com

Esse erro indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4121"></a>

### <a name="mt4121-cannot-use-gccg-to-compile-the-generated-code-from-the-static-registrar-when-using-the-accounts-framework-the-header-files-provided-by-apple-used-during-the-compilation-require-clang-either-use-clang---compilerclang-or-the-dynamic-registrar---registrardynamic"></a>MT4121: não é possível usar GCC/G + + para compilar o código gerado do registrador estático ao usar a estrutura de contas (os arquivos de cabeçalho fornecidos pela Apple usados durante a compilação exigem Clang). Use Clang (--Compiler: Clang) ou o registrador dinâmico (--registrador: dinâmico).

<a name="MT4122"></a>

### <a name="mt4122-cannot-use-the-clang-compiler-provided-in-the--sdk-to-compile-the-generated-code-from-the-static-registrar-when-non-ascii-type-names--are-present-in-the-application-either-use-gccg---compilergccg-the-dynamic-registrar---registrardynamic-or-a-newer-sdk"></a>MT4122: não é possível usar o compilador Clang fornecido no *.* SDK para compilar o código gerado do registrador estático quando os nomes de tipo não ASCII (' * ') estiverem presentes no aplicativo. Use GCC/G + + (--Compiler: gcc | G + +), o registrador dinâmico (--registrador: dinâmico) ou um SDK mais recente.

<a name="MT4123"></a>

### <a name="mt4123-the-type-of-the-variadic-parameter-in-the-variadic-function--must-be-systemintptr"></a>MT4123: o tipo do parâmetro Variadic na função Variadic ' * ' deve ser System. IntPtr.

<a name="MT4124"></a>

### <a name="mt4124-invalid--found-on--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4124: \* encontrado em ' \* ' inválido. Registre um relatório de bugs em http://bugzilla.xamarin.com

Esse erro indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4125"></a>

### <a name="mt4125-the-registrar-found-an-invalid-type--in-signature-for-method--the-interface-must-have-a-protocol-attribute-specifying-its-wrapper-type"></a>MT4125: o registrador encontrou um tipo inválido ' \* ' na assinatura para o método ' \* ': a interface deve ter um atributo de protocolo especificando seu tipo de wrapper.

<a name="MT4126"></a>

### <a name="mt4126-cannot-register-two-managed-protocols--and--with-the-same-native-name-"></a>MT4126: não é possível registrar dois protocolos gerenciados (' \* ' e ' \* ') com o mesmo nome nativo (' * ').

<a name="MT4127"></a>

### <a name="mt4127-cannot-register-more-than-one-interface-method-for-the-method--which-is-implementing-"></a>MT4127: não é possível registrar mais de um método de interface para o método ' \* ' (que está implementando ' \* ').

<a name="MT4128"></a>

### <a name="mt4128-the-registrar-found-an-invalid-generic-parameter-type--in-the-method--the-generic-parameter-must-have-an-nsobject-constraint"></a>MT4128: o registrador encontrou um tipo de parâmetro genérico inválido ' \* ' no método ' \* '. O parâmetro genérico deve ter uma restrição ' NSObject '.

<a name="MT4129"></a>

### <a name="mt4129-the-registrar-found-an-invalid-generic-return-type--in-the-method--the-generic-return-type-must-have-an-nsobject-constraint"></a>MT4129: o registrador encontrou um tipo de retorno genérico inválido ' \* ' no método ' \* '. O tipo de retorno genérico deve ter uma restrição ' NSObject '.

<a name="MT4130"></a>

### <a name="mt4130-the-registrar-cannot-export-static-methods-in-generic-classes-"></a>MT4130: o registrador não pode exportar métodos estáticos em classes genéricas (' * ').

<a name="MT4131"></a>

### <a name="mt4131-the-registrar-cannot-export-static-properties-in-generic-classes-"></a>MT4131: o registrador não pode exportar propriedades estáticas em classes genéricas (' \* . \* ').

<a name="MT4132"></a>

### <a name="mt4132-the-registrar-found-an-invalid-generic-return-type--in-the-property--the-return-type-must-have-an-nsobject-constraint"></a>MT4132: o registrador encontrou um tipo de retorno genérico inválido ' \* ' na propriedade ' \* '. O tipo de retorno deve ter uma restrição ' NSObject '.

<a name="MT4133"></a>

### <a name="mt4133-cannot-construct-an-instance-of-the-type--from-objective-c-because-the-type-is-generic-runtime-exception"></a>MT4133: não é possível construir uma instância do tipo "*" de Objective-C porque o tipo é genérico. [Exceção de tempo de execução]

<a name="MT4134"></a>

### <a name="mt4134-your-application-is-using-the--framework-which-isnt-included-in-the-ios-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-ios--while-youre-building-with-the-ios--sdk-please-select-a-newer-sdk-in-your-apps-ios-build-options"></a>MT4134: seu aplicativo está usando a \* estrutura "", que não está incluída no SDK do IOS que você está usando para criar seu aplicativo (essa estrutura foi introduzida no Ios \* , enquanto você está criando com o SDK do IOS \* .) Selecione um SDK mais recente nas opções de Build do iOS do seu aplicativo.

<a name="MT4135"></a>

### <a name="mt4135-the-member--has-an-export-attribute-that-doesnt-specify-a-selector-a-selector-is-required"></a>MT4135: o membro ' \* . \* ' tem um atributo de exportação que não especifica um seletor. Um seletor é necessário.

<a name="MT4136"></a>

### <a name="mt4136-the-registrar-cannot-marshal-the-parameter-type--of-the-parameter--in-the-method-"></a>MT4136: o registrador não pode realizar marshaling do tipo de parâmetro ' \* ' do parâmetro ' \* ' no método ' \* . * '

<!-- MT4137 is unused -->

<a name="MT4138"></a>

### <a name="mt4138-the-registrar-cannot-marshal-the-property-type--of-the-property-"></a>MT4138: o registrador não pode realizar marshaling do tipo de propriedade ' \* ' da propriedade ' \* . * '.

<a name="MT4139"></a>

### <a name="mt4139-the-registrar-cannot-marshal-the-property-type--of-the-property--properties-with-the-connect-attribute-must-have-a-property-type-of-nsobject-or-a-subclass-of-nsobject"></a>MT4139: o registrador não pode realizar marshaling do tipo de propriedade ' \* ' da propriedade ' \* . * '. As propriedades com o atributo [Connect] devem ter um tipo de propriedade de NSObject (ou uma subclasse de NSObject).

<a name="MT4140"></a>

### <a name="mt4140-the-registrar-found-a-signature-mismatch-in-the-method----the-selector-indicates-the-variadic-method-takes--parameters-while-the-managed-method-has--parameters"></a>MT4140: o registrador encontrou uma incompatibilidade de assinatura no método ' \* . \* ' – o seletor indica que o método Variadic usa \* parâmetros, enquanto o método gerenciado tem \* parâmetros.

<a name="MT4141"></a>

### <a name="mt4141-cannot-register-the-selector--on-the-member--because-xamarinios-implicitly-registers-this-selector"></a>MT4141: não é possível registrar o seletor ' \* ' no membro ' \* ' porque o Xamarin. Ios registra implicitamente esse seletor.

Isso ocorre ao subclasse de um tipo de estrutura e ao tentar implementar um método "Retain", "Release" ou "Dealloc":

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

No entanto, é possível substituir esses métodos se a classe não for a primeira subclasse do tipo de estrutura:

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

Nesse caso, o Xamarin. iOS substituirá `retain` `release` e `dealloc` na `MyNSObject` classe, e não haverá conflito.

<a name="MT4142"></a>

### <a name="mt4142-failed-to-register-the-type-"></a>MT4142: falha ao registrar o tipo ' * '.

<a name="MT4143"></a>

### <a name="mt4143-the-objectivec-class--could-not-be-registered-it-does-not-seem-to-derive-from-any-known-objectivec-class-including-nsobject"></a>MT4143: a classe ObjectiveC ' * ' não pôde ser registrada, ela não parece derivar de nenhuma classe ObjectiveC conhecida (incluindo NSObject).

<a name="MT4144"></a>

### <a name="mt4144-cannot-register-the-method--since-it-does-not-have-an-associated-trampoline-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4144: não é possível registrar o método ' * ', pois ele não tem um trampoline associado. Registre um relatório de bugs em http://bugzilla.xamarin.com .

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4145"></a>

### <a name="mt4145-invalid-enum--enums-with-the-native-attribute-must-have-a-underlying-enum-type-of-either-long-or-ulong"></a>MT4145: enumeração ' * ' inválida: enums com o atributo [Native] devem ter um tipo de enumeração subjacente de ' Long ' ou ' ULONG '.

<a name="MT4146"></a>

### <a name="mt4146-the-name-parameter-of-the-registrar-attribute-on-the-class---contains-an-invalid-character--"></a>MT4146: o parâmetro Name do atributo registrador na classe ' \* ' (' \* ') contém um caractere inválido: ' \* ' ( \* ).

O nome de uma classe Objectice-C não pode conter espaço em branco, o que significa que o `Register` atributo na classe gerenciada correspondente não pode ter um `Name` parâmetro que também pode conter espaço em branco.

Verifique se o `Register` atributo na classe gerenciada mencionada na mensagem de erro não contém nenhum espaço em branco.

<a name="MT4147"></a>

### <a name="mt4147-detected-a-protocol-inheriting-from-the-jsexport-protocol-while-using-the-dynamic-registrar-it-is-not-possible-to-export-protocols-to-javascriptcore-dynamically-the-static-registrar-must-be-used-add---registrarstatic-to-the-additional-mtouch-arguments-in-the-projects-ios-build-options-to-select-the-static-registrar"></a>MT4147: foi detectado um protocolo herdado do protocolo JSExport ao usar o registrador dinâmico. Não é possível exportar protocolos para JavaScriptCore dinamicamente; o registrador estático deve ser usado (Adicionar '--registrador: estático para os argumentos adicionais de mTouch nas opções de Build do iOS do projeto para selecionar o registrador estático).

<a name="MT4148"></a>

### <a name="mt4148-the-registrar-found-a-generic-protocol--exporting-generic-protocols-is-not-supported"></a>MT4148: o registrador encontrou um protocolo genérico: ' * '. Não há suporte para a exportação de protocolos genéricos.

<a name="MT4149"></a>

### <a name="mt4149-cannot-register-the-method--because-the-type-of-the-first-parameter--does-not-match-the-category-type-"></a>MT4149: não é possível registrar o método ' \* . \* ' porque o tipo do primeiro parâmetro (' \* ') não corresponde ao tipo de categoria (' \* ').

<a name="MT4150"></a>

### <a name="mt4150-cannot-register-the-type--because-the-type-property--in-its-category-attribute-does-not-inherit-from-nsobject"></a>MT4150: não é possível registrar o tipo ' \* ' porque a propriedade Type (' \* ') em seu atributo category não herda de NSObject.

<a name="MT4151"></a>

### <a name="mt4151-cannot-register-the-type--because-the-type-property-in-its-category-attribute-isnt-set"></a>MT4151: não é possível registrar o tipo ' * ' porque a propriedade Type em seu atributo category não está definida.

<a name="MT4152"></a>

### <a name="mt4152-cannot-register-the-type--as-a-category-because-it-implements-inativeobject-or-subclasses-nsobject"></a>MT4152: não é possível registrar o tipo ' * ' como uma categoria porque ele implementa INativeObject ou subclasses NSObject.

<a name="MT4153"></a>

### <a name="mt4153-cannot-register-the-type--as-a-category-because-its-generic"></a>MT4153: não é possível registrar o tipo ' \* ' como uma categoria porque ele é genérico.

<a name="MT4154"></a>

### <a name="mt4154-cannot-register-the-method--as-a-category-method-because-its-generic"></a>MT4154: não é possível registrar o método ' \* ' como um método de categoria porque ele é genérico.

<a name="MT4155"></a>

### <a name="mt4155-cannot-register-the-method--with-the-selector--as-a-category-method-on--because-the-objective-c-already-has-an-implementation-for-this-selector"></a>MT4155: não é possível registrar o método ' \* ' com o seletor ' \* ' como um método de categoria em ' * ' porque o Objective-C já tem uma implementação para este seletor.

<a name="MT4156"></a>

### <a name="mt4156-cannot-register-two-categories--and--with-the-same-native-name-"></a>MT4156: não é possível registrar duas categorias (' \* ' e ' \* ') com o mesmo nome nativo (' * ').

<a name="MT4157"></a>

### <a name="mt4157-cannot-register-the-category-method--because-at-least-one-parameter-is-required-and-its-type-must-match-the-category-type-"></a>MT4157: não é possível registrar o método de categoria ' \* ' porque pelo menos um parâmetro é necessário (e seu tipo deve corresponder ao tipo de categoria ' \* ')

<a name="MT4158"></a>

### <a name="mt4158-cannot-register-the-constructor--in-the-category--because-constructors-in-categories-are-not-supported"></a>MT4158: não é possível registrar o construtor \* na categoria \* porque não há suporte para construtores em categorias.

<a name="MT4159"></a>

### <a name="mt4159-cannot-register-the-method--as-a-category-method-because-category-methods-must-be-static"></a>MT4159: não é possível registrar o método ' * ' como um método de categoria porque os métodos de categoria devem ser estáticos.

<a name="MT4160"></a>

### <a name="mt4160-invalid-character---found-in-selector--for-"></a>MT4160: caractere inválido ' \* ' ( \* ) encontrado no seletor ' \* ' para ' \* '.

<a name="MT4161"></a>

### <a name="mt4161-the-registrar-found-an-unsupported-structure--all-fields-in-a-structure-must-also-be-structures-field--with-type-2-is-not-a-structure"></a>MT4161: o registrador encontrou uma estrutura sem suporte ' \* ': todos os campos em uma estrutura também devem ser estruturas (o campo ' \* ' com {2} o tipo ' ' não é uma estrutura).

O registrador encontrou uma estrutura com campos sem suporte.

Todos os campos em uma estrutura que é exposta a Objective-C também devem ser estruturas (não classes).

<a name="MT4162"></a>

### <a name="mt4162-the-type--used-as--2-is-not-available-in---it-was-introduced-in---please-build-with-a-newer--sdk-usually-done-by-using-the-most-recent-version-of-xcode"></a>MT4162: o tipo ' \* ' (usado como * {2} ) não está disponível em * * (ele foi introduzido em * *) \* crie com um SDK * mais recente (geralmente feito usando a versão mais recente do Xcode.

O registrador encontrou um tipo que não está incluído no SDK atual.

Atualize o Xcode.

<a name="MT4163"></a>

### <a name="mt4163-internal-error-in-the-registrar--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT4163: erro interno no registrador (*). Registre um relatório de bugs em http://bugzilla.xamarin.com

Esse erro indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4164"></a>

### <a name="mt4164-cannot-export-the-property--because-its-selector--is-an-objective-c-keyword-please-use-a-different-name"></a>MT4164: não é possível exportar a propriedade ' \* ' porque seu seletor ' \* ' é uma palavra-chave Objective-C. Use um nome diferente.

O seletor da propriedade em questão não é um identificador Objective-C válido.

Use um identificador Objective-C válido como seletores.

<a name="MT4165"></a>

### <a name="mt4165-the-registrar-couldnt-find-the-type-systemvoid-in-any-of-the-referenced-assemblies"></a>MT4165: o registrador não pôde encontrar o tipo ' System. void ' em nenhum dos assemblies referenciados.

Esse erro provavelmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4166"></a>

### <a name="mt4166-cannot-register-the-method--because-the-signature-contains-a-type--that-isnt-a-reference-type"></a>MT4166: não é possível registrar o método ' \* ' porque a assinatura contém um tipo ( \* ) que não é um tipo de referência.

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4167"></a>

### <a name="mt4167-cannot-register-the-method--because-the-signature-contains-a-generic-type--with-a-generic-argument-type-that-isnt-an-nsobject-subclass-"></a>MT4167: não é possível registrar o método ' \* ' porque a assinatura contém um tipo genérico ( \* ) com um tipo de argumento genérico que não é uma subclasse NSObject (*).

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT4168"></a>

### <a name="mt4168-cannot-register-the-type-managed_name-because-its-objective-c-name-exported_name-is-an-objective-c-keyword-please-use-a-different-name"></a>MT4168: não é possível registrar o tipo ' {Managed \_ name} ' porque seu nome Objective-c ' {exported \_ name} ' é uma palavra-chave Objective-c. Use um nome diferente.

O nome Objective-C para o tipo em questão não é um identificador Objective-C válido.

Use um identificador Objective-C válido.

<a name="MT4169"></a>

### <a name="mt4169-failed-to-generate-a-pinvoke-wrapper-for-method-message"></a>MT4169: falha ao gerar um wrapper P/Invoke para {Method}: {Message}

O Xamarin. iOS não pôde gerar uma função de wrapper P/Invoke para o mencionado.
Verifique a mensagem de erro relatada para a causa subjacente.

<a name="MT4170"></a>

### <a name="mt4170-the-registrar-cant-convert-from-managed-type-to-native-type-for-the-return-value-in-the-method-method"></a>MT4170: o registrador não pode converter de ' {Managed Type} ' para ' {Native Type} ' para o valor de retorno no método {Method}.

Consulte a descrição do erro <a href="#MT4172">MT4172</a>.

<a name="MT4171"></a>

### <a name="mt4171-the-bindas-attribute-on-the-member-member-is-invalid-the-bindas-type-type-is-different-from-the-property-type-type"></a>MT4171: o atributo bindas no membro {member} é inválido: o tipo Bindity {Type} é diferente do tipo de propriedade {Type}.

Verifique se o tipo no atributo Binde corresponde ao tipo do membro ao qual ele está anexado.

<a name="MT4172"></a>

### <a name="mt4172-the-registrar-cant-convert-from-native-type-to-managed-type-for-the-parameter-parameter-name-in-the-method-method"></a>MT4172: o registrador não pode converter de ' {Native Type} ' em ' {Managed Type} ' para o parâmetro ' {Parameter Name} ' no método {Method}.

O registrador não oferece suporte à conversão entre os tipos mencionados.

Esse é um bug no Xamarin. iOS se a API em questão for fornecida pelo Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Se você encontrar isso ao desenvolver um projeto de associação para uma biblioteca nativa, estamos abertos para adicionar suporte a novas combinações de tipos. Se esse for o caso, registre uma solicitação de aprimoramento no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste e vamos avaliá-lo.

## <a name="mt5xxx-gcc-and-toolchain-error-messages"></a>MT5xxx: mensagens de erro GCC e ferramentas

### <a name="mt51xx-compilation"></a>MT51xx: compilação

<!--
 MT5xxx GCC and toolchain
  MT51xx compilation
  -->

<a name="MT5101"></a>

### <a name="mt5101-missing--compiler-please-install-xcode-command-line-tools-component"></a>MT5101: compilador ' * ' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode

<a name="MT5102"></a>

### <a name="mt5102-failed-to-assemble-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5102: falha ao montar o arquivo ' * '. Registre um relatório de bugs em http://bugzilla.xamarin.com

<a name="MT5103"></a>

### <a name="mt5103-failed-to-compile-the-file--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5103: falha ao compilar o arquivo ' * '. Registre um relatório de bugs em http://bugzilla.xamarin.com

<a name="MT5104"></a>

### <a name="mt5104-could-not-find-neither-the--nor-the--compiler-please-install-xcode-command-line-tools-component"></a>MT5104: não foi possível localizar nem o \* compilador ' ' nem o ' \* '. Instale o componente ' ferramentas de linha de comando ' do Xcode

<!-- 5105 is used by mmp -->

<a name="MT5106"></a>

### <a name="mt5106-could-not-compile-the-files--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5106: não foi possível compilar os arquivos ' * '. Registre um relatório de bugs em http://bugzilla.xamarin.com

Isso geralmente indica um bug no Xamarin. iOS; Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

### <a name="mt52xx-linking"></a>MT52xx: vinculação

<!--
  MT52xx linking
  -->

<a name="MT5201"></a>

### <a name="mt5201-native-linking-failed-please-review-the-build-log-and-the-user-flags-provided-to-gcc-"></a>MT5201: falha na vinculação nativa. Examine o log de compilação e os sinalizadores de usuário fornecidos para o gcc: *

<a name="MT5202"></a>

### <a name="mt5202-native-linking-failed-please-review-the-build-log"></a>MT5202: falha na vinculação nativa. Examine o log de compilação.

<a name="MT5203"></a>

### <a name="mt5203-native-linking-warning-"></a>MT5203: aviso de vinculação nativa: *

<!--- 5204-5208 are not used -->

<a name="MT5209"></a>

### <a name="mt5209-native-linking-error-"></a>MT5209: erro de vinculação nativa: *

<a name="MT5210"></a>

### <a name="mt5210-native-linking-failed-undefined-symbol--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-are-properly-linked-in"></a>MT5210: falha de vinculação nativa, símbolo indefinido: *. Verifique se todas as estruturas necessárias foram referenciadas e se as bibliotecas nativas estão vinculadas corretamente.

Isso acontece quando o vinculador nativo não consegue encontrar um símbolo que é referenciado em algum lugar. Há vários motivos pelos quais isso pode ocorrer:

- Uma associação de terceiros requer uma estrutura, mas a associação não especifica isso em seu `[LinkWith]` atributo. Soluções:
  - Se você for o autor da Associação de terceiros ou tiver acesso à sua fonte, modifique o atributo da Associação `[LinkWith]` para incluir a estrutura necessária:

    ```csharp
    [LinkWith ("mylib.a", Frameworks = "SystemConfiguration")]
    ```

  - Se não for possível modificar a associação de terceiros, você poderá vincular manualmente com a estrutura necessária passando `-gcc_flags '-framework SystemFramework'` para `mtouch` (isso é feito modificando os argumentos adicionais de mTouch na página de opções de Build do IOS do projeto. Lembre-se de que isso deve ser feito para cada configuração de projeto.
- Em alguns casos, uma associação gerenciada é composta por várias bibliotecas nativas, e todas devem ser incluídas nas associações. É possível ter mais de uma biblioteca nativa em cada projeto de associação, portanto, a solução é apenas adicionar todas as bibliotecas nativas necessárias ao projeto de associação.</li>
- Uma associação gerenciada refere-se a símbolos nativos que não existem na biblioteca nativa.
    Isso geralmente acontece quando uma associação existe por algum tempo, e o código nativo foi modificado durante esse tempo para que uma classe nativa específica tenha sido removida ou renomeada, enquanto a associação não foi atualizada.
- P/Invoke refere-se a um símbolo nativo que não existe. A partir do Xamarin. iOS 7,4, um erro <a href="#MT5214">MT5214</a> será relatado para esse caso (consulte MT5214 para obter mais informações).
- Uma associação/biblioteca de terceiros foi criada usando C++, mas a associação não especifica isso em seu `[LinkWith]` atributo. Normalmente, isso é razoavelmente fácil de reconhecer, pois os símbolos têm símbolos C++ desconfigurados (um exemplo comum é `__ZNKSt9exception4whatEv` ).
  - Se você for o autor da Associação de terceiros ou tiver acesso à sua fonte, modifique o atributo da Associação `[LinkWith]` para definir o `IsCxx` sinalizador:

    ```csharp
    [LinkWith ("mylib.a", IsCxx = true)]
    ```

  - Se você não puder modificar a associação de terceiros, ou se estiver vinculando manualmente com uma biblioteca de terceiros, poderá definir o sinalizador equivalente passando `-cxx` para mTouch (isso é feito modificando os argumentos mTouch adicionais na página de opções de Build do projeto do Ios. Lembre-se de que isso deve ser feito para cada configuração de projeto.

<a name="MT5211"></a>

### <a name="mt5211-native-linking-failed-undefined-objective-c-class--the-symbol--could-not-be-found-in-any-of-the-libraries-or-frameworks-linked-with-your-application"></a>MT5211: falha de vinculação nativa, classe Objective-C indefinida: \* . \*Não foi possível encontrar o símbolo ' ' em nenhuma das bibliotecas ou estruturas vinculadas ao seu aplicativo.

Isso acontece quando o vinculador nativo não consegue encontrar uma classe Objective-C que é referenciada em algum lugar. Há vários motivos pelos quais isso pode ocorrer: o mesmo que para [MT5210](#MT5210) e, além disso:

- Uma associação de terceiros associada a um protocolo Objective-C, mas não o anotava com o `[Protocol]` atributo em sua definição de API. Soluções:
  - Adicione o `[Protocol]` atributo ausente:

    ```csharp
    [BaseType (typeof (NSObject))]
    [Protocol] // Add this
    public interface MyProtocol
    {
    }
    ```

<a name="MT5212"></a>

### <a name="mt5212-native-linking-failed-duplicate-symbol-"></a>MT5212: falha de vinculação nativa, símbolo duplicado: *.

Isso acontece quando o vinculador nativo encontra símbolos duplicados entre todas as bibliotecas nativas. Após esse erro, pode haver um ou mais erros [MT5213](#MT5213) com o local para cada ocorrência do símbolo. Possíveis motivos para esse erro:

- A mesma biblioteca nativa é incluída duas vezes.
- Duas bibliotecas nativas distintas acontecem para definir os mesmos símbolos.
- Uma biblioteca nativa não está compilada corretamente e contém o mesmo símbolo mais de uma vez.
  Você pode confirmar isso usando o seguinte conjunto de comandos de um terminal (substitua i386 por x86_64/ARMv7/armv7s/arm64 de acordo com a arquitetura que você está compilando):

  ```
  # Native libraries are usually fat libraries, containing binary code for
  # several architectures in the same file. First we extract the binary
  # code for the architecture we're interested in.
  lipo libNative.a -thin i386 -output libNative.i386.a

  # Now query the native library for the duplicated symbol.
  nm libNative.i386.a | fgrep 'SYMBOL'

  # You can also list the object files inside the native library.
  # In most cases this will reveal duplicated object files.
  ar -t libNative.i386.a
  ```

  Há algumas maneiras possíveis de corrigir isso:

  - Solicite que o provedor da biblioteca nativa corrija-o e forneça a versão atualizada.
  - Corrija-o por conta própria removendo os arquivos de objeto extra (isso só funciona se o problema estiver em arquivos de objeto duplicados de fato)

  ```
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
  ```

  - Peça ao vinculador para remover o código não utilizado. O Xamarin. iOS fará isso automaticamente se todas as seguintes condições forem atendidas:
    - Todos os atributos de associações de terceiros `[LinkWith]` têm o SmartLink habilitado:

      ```csharp
      [assembly: LinkWith ("libNative.a", SmartLink = true)]
      ```

    - Não `-gcc_flags` é passado para mTouch (no campo argumentos adicionais de mTouch das opções de Build do IOS do projeto).
    - Também é possível pedir ao vinculador diretamente para remover o código não utilizado adicionando `-gcc_flags -dead_strip` aos argumentos mTouch adicionais nas opções de Build do IOS do projeto.

<a name="MT5213"></a>

### <a name="mt5213-duplicate-symbol-in--location-related-to-previous-error"></a>MT5213: símbolo duplicado em: * (local relacionado ao erro anterior)

Esse erro é relatado apenas junto com [MT5212](#MT5212). Consulte [MT5212](#MT5212) para obter mais informações.

<a name="MT5214"></a>

### <a name="mt5214-native-linking-failed-undefined-symbol--this-symbol-was-referenced-the-managed-member--please-verify-that-all-the-necessary-frameworks-have-been-referenced-and-native-libraries-linked"></a>MT5214: falha de vinculação nativa, símbolo indefinido: *. Este símbolo foi referenciado no membro gerenciado *. Verifique se todas as estruturas necessárias foram referenciadas e as bibliotecas nativas estão vinculadas.

Esse erro é relatado quando o código gerenciado contém um P/Invoke para um método nativo que não existe. Por exemplo:

```csharp
using System.Runtime.InteropServices;
class MyImports {
   [DllImport ("__Internal")]
   public static extern void MyInexistentFunc ();
}
```

Há algumas soluções possíveis:

- Remova os P/Invokes em questão do código-fonte.
- Habilite o vinculador gerenciado para todos os assemblies (isso é feito nas opções de Build do iOS do projeto, definindo "comportamento do vinculador" como "todos os assemblies"). Isso removerá efetivamente todos os P/Invokes que você não usa do aplicativo (automaticamente, em vez de ser manualmente como o ponto anterior). A desvantagem é que isso fará com que o simulador seja criado um pouco mais devagar e poderá interromper seu aplicativo se estiver usando a reflexão-mais informações sobre o vinculador podem ser encontradas  [aqui](~/ios/deploy-test/linker.md) )
- Crie uma segunda biblioteca nativa que contenha os símbolos nativos ausentes. Observe que isso é meramente uma solução alternativa (se você tentar chamar essas funções, seu aplicativo falhará).

<a name="MT5215"></a>

### <a name="mt5215-references-to--might-require-additional--frameworkxxx-or--lxxx-instructions-to-the-native-linker"></a>MT5215: referências a ' * ' podem exigir instruções adicionais de estrutura = XXX ou-lXXX para o vinculador nativo

Esse é um aviso, indicando que um P/Invoke foi detectado para referenciar a biblioteca em questão, mas o aplicativo não está vinculando a ele.

<a name="MT5216"></a>

### <a name="mt5216-native-linking-failed-for--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT5216: falha de vinculação nativa para *. Registre um relatório de bugs em http://bugzilla.xamarin.com

Esse erro é relatado ao vincular a saída do compilador AOT.

Esse erro provavelmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT5217"></a>

### <a name="mt5217-native-linking-possibly-failed-because-the-linker-command-line-was-too-long--characters"></a>MT5217: a vinculação nativa possivelmente falhou porque a linha de comando do vinculador era muito longa (* caracteres).

Falha na vinculação nativa. isso é possível porque o comando do vinculador era muito longo.

Os projetos do Xamarin. iOS geralmente referenciam símbolos nativos dinamicamente, o que significa que o vinculador nativo pode remover esses símbolos nativos durante o processo de vinculação nativo, pois o vinculador nativo não vê que esses símbolos são usados.

Normalmente, o Xamarin. iOS solicitará que o vinculador nativo Mantenha esses símbolos usando o `-u symbol` sinalizador do vinculador, mas se houver muitos desses símbolos, a linha de comando inteira poderá exceder o comprimento máximo da linha de comando, conforme especificado pelo sistema operacional.

Há algumas fontes possíveis para esses símbolos dinâmicos:

- P/invoca para métodos em bibliotecas estaticamente vinculadas (onde o nome da dll está `__Internal` no atributo DllImport `[DllImport ("__Internal")]` ).
- Referências de campo para locais de memória em bibliotecas estaticamente vinculadas de projetos de associação ( `[Field]` atributos).
- Classes Objective-C referenciadas em bibliotecas estaticamente vinculadas de projetos de associação (ao usar compilações incrementais ou quando não estiver usando o registrador estático).

Soluções possíveis:

- Habilite o vinculador gerenciado (se possível para todos os assemblies em vez de apenas assemblies do SDK). Isso pode remover o suficiente das fontes de símbolos dinâmicos para que a linha de comando do vinculador não exceda o máximo.
- Reduza o número de P/Invokes, referências de campo e/ou classes Objective-C.
- Reescreva os símbolos dinâmicos para ter nomes mais curtos.
- Passe `-dlsym:false` como um argumento mTouch adicional nas opções de Build do IOS do projeto. Com essa opção, o Xamarin. iOS gerará uma referência nativa no código compilado por AOT e não precisará solicitar que o vinculador mantenha esse símbolo. No entanto, isso só funciona para compilações de dispositivo e causará erros de vinculador se houver P/Invokes para funções que não existem na biblioteca estática.
- Passe `--dynamic-symbol-mode=code` como um argumento mTouch adicional nas opções de Build do IOS do projeto. Com essa opção, o Xamarin. iOS gerará código nativo adicional que faz referência a esses símbolos em vez de solicitar que o vinculador nativo Mantenha esses símbolos usando argumentos de linha de comando. A desvantagem dessa abordagem é que ela aumentará o tamanho do executável de alguma forma.
- Habilite o registrador estático passando `--registrar:static` como um argumento mTouch adicional nas opções de Build do IOS do projeto (para compilações do simulador, como o registrador estático já é o padrão para compilações de dispositivo). O registrador estático gerará um código que referencie as classes Objective-C estaticamente, portanto, não há necessidade de solicitar que o vinculador nativo Mantenha essas classes.
- Desabilite compilações incrementais (para Builds de dispositivos). Quando as compilações incrementais estão habilitadas, o código gerado pelo registrador estático não será considerado pelo vinculador nativo, o que significa que o Xamarin. iOS ainda deve solicitar que o vinculador Mantenha as classes Objective-C referenciadas. Assim, desabilitar compilações incrementais evitará essa necessidade.

<a name="MT5218"></a>

### <a name="mt5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MT5218: não é possível ignorar o símbolo dinâmico {Symbol} (--ignore-Dynamic-Symbol = {Symbol}) porque ele não foi detectado como um símbolo dinâmico.

O argumento de linha de comando `--ignore-dynamic-symbol=symbol` foi passado, mas esse símbolo não é um símbolo que foi reconhecido como um símbolo dinâmico que deve ser preservado manualmente.

Há dois motivos principais para isso:

- O nome do símbolo está incorreto.
  - Não coloque um sublinhado no nome do símbolo.
  - O símbolo para as classes Objective-C é `OBJC_CLASS_$_<classname>` .
- O símbolo está correto, mas é um símbolo que já é preservado por meios normais (algumas opções de compilação fazem com que a lista exata de símbolos dinâmicos varie).

### <a name="mt53xx-other-tools"></a>MT53xx: outras ferramentas

<!--
  MT53xx other tools
  -->

<a name="MT5301"></a>

### <a name="mt5301-missing-strip-tool-please-install-xcode-command-line-tools-component"></a>MT5301: ferramenta ' strip ' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode

<a name="MT5302"></a>

### <a name="mt5302-missing-dsymutil-tool-please-install-xcode-command-line-tools-component"></a>MT5302: ferramenta ' dsymutil ' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode

<a name="MT5303"></a>

### <a name="mt5303-failed-to-generate-the-debug-symbols-dsym-directory-please-review-the-build-log"></a>MT5303: falha ao gerar os símbolos de depuração (diretório dSYM). Examine o log de compilação.

Ocorreu um erro ao executar dsymutil no diretório final. app para criar os símbolos de depuração. Examine o log de compilação para ver a saída do dsymutil e veja como ele pode ser corrigido.

<a name="MT5304"></a>

### <a name="mt5304-failed-to-strip-the-final-binary-please-review-the-build-log"></a>MT5304: falha ao remover o binário final. Examine o log de compilação.

Ocorreu um erro ao executar a ferramenta ' strip ' para remover informações de depuração do aplicativo.

<a name="MT5305"></a>

### <a name="mt5305-missing-lipo-tool-please-install-xcode-command-line-tools-component"></a>MT5305: ferramenta ' lipo ' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode

<a name="MT5306"></a>

### <a name="mt5306-failed-to-create-the-a-fat-library-please-review-the-build-log"></a>MT5306: falha ao criar a biblioteca de Fat. Examine o log de compilação.

Ocorreu um erro ao executar a ferramenta ' lipo '. Examine o log de compilação para ver o erro relatado por ' lipo '.

<a name="MT5307"></a>

### <a name="mt5307-failed-to-sign-the-executable-please-review-the-build-log"></a>MT5307: falha ao assinar o executável. Examine o log de compilação.

Ocorreu um erro ao assinar o aplicativo. Examine o log de compilação para ver o erro relatado por ' codesign '.

<!-- 5308 is used by mmp -->
<!-- 5309 is used by mmp -->
<!-- 5310 is used by mmp -->

## <a name="mt6xxx-mtouch-internal-tools-error-messages"></a>MT6xxx: mensagens de erro de ferramentas internas do mTouch

### <a name="mt600x-stripper"></a>MT600x: remoção

<!--
 MT6xxx mtouch internal tools
  MT600x Stripper
  -->

<a name="MT6001"></a>

### <a name="mt6001-running-version-of-cecil-doesnt-support-assembly-stripping"></a>MT6001: a execução da versão do Cecil não dá suporte à remoção de assembly

<a name="MT6002"></a>

### <a name="mt6002-could-not-strip-assembly-"></a>MT6002: não foi possível retirar o assembly `*` .

Ocorreu um erro ao remover o código gerenciado (removendo o código IL) dos assemblies no aplicativo.

<a name="MT6003"></a>

### <a name="mt6003-unauthorizedaccessexception-message"></a>MT6003: [mensagem de UnauthorizedAccessException]

Ocorreu um erro de segurança durante a remoção de símbolos de depuração do aplicativo.

## <a name="mt7xxx-msbuild-error-messages"></a>MT7xxx: mensagens de erro do MSBuild

<!--
 MT7xxx msbuild errors
  -->

<a name="MT7001"></a>

### <a name="mt7001-could-not-resolve-host-ips-for-wifi-debugger-settings"></a>MT7001: não foi possível resolver IPs de host para configurações do depurador WiFi.

*Tarefa do MSBuild: DetectDebugNetworkConfigurationTaskBase*

Etapas para solucionar problemas:

- Tente executar `csharp -e 'System.Net.Dns.GetHostEntry (System.Net.Dns.GetHostName ()).AddressList'` (que deve fornecer um endereço IP e não um erro, obviamente).
- Tente executar "ping \` hostname \` ", que pode fornecer mais informações, como: `cannot resolve MyHost.local: Unknown host`

Em alguns casos, é um problema de "rede local" e pode ser resolvido com a adição do host desconhecido `127.0.0.1    MyHost.local` no `/etc/hosts` .

<a name="MT7002"></a>

### <a name="mt7002-this-machine-does-not-have-any-network-adapters-this-is-required-when-debugging-or-profiling-on-device-over-wifi"></a>MT7002: este computador não tem nenhum adaptador de rede. Isso é necessário durante a depuração ou a criação de perfil no dispositivo por Wi-Fi.

*Tarefa do MSBuild: DetectDebugNetworkConfigurationTaskBase*

<a name="MT7003"></a>

### <a name="mt7003-the-app-extension--does-not-contain-an-infoplist"></a>MT7003: a extensão do aplicativo ' * ' não contém um info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7004"></a>

### <a name="mt7004-the-app-extension--does-not-specify-a-cfbundleidentifier"></a>MT7004: a extensão do aplicativo ' * ' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7005"></a>

### <a name="mt7005-the-app-extension--does-not-specify-a-cfbundleexecutable"></a>MT7005: a extensão do aplicativo ' * ' não especifica um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7006"></a>

### <a name="mt7006-the-app-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7006: a extensão do aplicativo ' \* ' tem um CFBundleIdentifier ( \* ) inválido; ele não começa com o CFBundleIdentifier do pacote do aplicativo principal (*).

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7007"></a>

### <a name="mt7007-the-app-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7007: a extensão do aplicativo ' \* ' tem um CFBundleIdentifier ( \* ) que termina com o sufixo inválido ". Key".

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7008"></a>

### <a name="mt7008-the-app-extension--does-not-specify-a-cfbundleshortversionstring"></a>MT7008: a extensão do aplicativo ' * ' não especifica um CFBundleShortVersionString.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7009"></a>

### <a name="mt7009-the-app-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7009: a extensão do aplicativo ' * ' tem um info. plist inválido: ele não contém um dicionário NSExtension.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7010"></a>

### <a name="mt7010-the-app-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionpointidentifier-value"></a>MT7010: a extensão do aplicativo ' * ' tem um info. plist inválido: o dicionário NSExtension não contém um valor NSExtensionPointIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7011"></a>

### <a name="mt7011-the-watchkit-extension--has-an-invalid-infoplist-the-nsextension-dictionary-does-not-contain-an-nsextensionattributes-dictionary"></a>MT7011: a extensão WatchKit ' * ' tem um info. plist inválido: o dicionário NSExtension não contém um dicionário NSExtensionAttributes.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7012"></a>

### <a name="mt7012-the-watchkit-extension--does-not-have-exactly-one-watch-app"></a>MT7012: a extensão WatchKit ' * ' não tem exatamente um aplicativo Watch.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7013"></a>

### <a name="mt7013-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-must-contain-the-watch-companion-capability"></a>MT7013: a extensão WatchKit ' * ' tem um info. plist inválido. o UIRequiredDeviceCapabilities deve conter o recurso ' Watch-Companion '.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7014"></a>

### <a name="mt7014-the-watch-app--does-not-contain-an-infoplist"></a>MT7014: o aplicativo de inspeção ' * ' não contém um info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7015"></a>

### <a name="mt7015-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7015: o aplicativo de inspeção ' * ' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7016"></a>

### <a name="mt7016-the-watch-app--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7016: o aplicativo de inspeção ' \* ' tem um CFBundleIdentifier ( \* ) inválido; ele não começa com o CFBundleIdentifier do pacote de aplicativo principal (*).

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7017"></a>

### <a name="mt7017-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected-watch-4-but-found--"></a>MT7017: o aplicativo de inspeção ' \* ' não tem um valor de UIDeviceFamily válido. Esperado ' Watch (4) ', mas foi encontrado ' \* (*) '.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7018"></a>

### <a name="mt7018-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7018: o aplicativo de inspeção ' * ' não especifica um CFBundleExecutable

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7019"></a>

### <a name="mt7019-the-watch-app--has-an-invalid-wkcompanionappbundleidentifier-value--it-does-not-match-the-main-app-bundles-cfbundleidentifier-"></a>MT7019: o aplicativo de inspeção ' \* ' tem um valor de WKCompanionAppBundleIdentifier inválido (' \* '); ele não corresponde ao CFBundleIdentifier do grupo de aplicativos principal (' * ').

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7020"></a>

### <a name="mt7020-the-watch-app--has-an-invalid-infoplist-the-wkwatchkitapp-key-must-be-present-and-have-a-value-of-true"></a>MT7020: o aplicativo de inspeção ' * ' tem um info. plist inválido: a chave WKWatchKitApp deve estar presente e ter um valor de ' true '.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7021"></a>

### <a name="mt7021-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7021: o aplicativo de inspeção ' * ' tem um info. plist inválido: a chave LSRequiresIPhoneOS não deve estar presente.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7022"></a>

### <a name="mt7022-the-watch-app--does-not-contain-a-watch-extension"></a>MT7022: o aplicativo de inspeção ' * ' não contém uma extensão Watch.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7023"></a>

### <a name="mt7023-the-watch-extension--does-not-contain-an-infoplist"></a>MT7023: a extensão de inspeção ' * ' não contém um info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7024"></a>

### <a name="mt7024-the-watch-extension--does-not-specify-a-cfbundleidentifier"></a>MT7024: a extensão de inspeção ' * ' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7025"></a>

### <a name="mt7025-the-watch-extension--does-not-specify-a-cfbundleexecutable"></a>MT7025: a extensão de inspeção ' * ' não especifica um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7026"></a>

### <a name="mt7026-the-watch-extension--has-an-invalid-cfbundleidentifier--it-does-not-begin-with-the-main-app-bundles-cfbundleidentifier-"></a>MT7026: a extensão de inspeção ' \* ' tem um CFBundleIdentifier inválido ( \* ); ela não começa com o CFBundleIdentifier do pacote de aplicativo principal (*).

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7027"></a>

### <a name="mt7027-the-watch-extension--has-a-cfbundleidentifier--that-ends-with-the-illegal-suffix-key"></a>MT7027: a extensão Watch ' \* ' tem um CFBundleIdentifier ( \* ) que termina com o sufixo inválido ". Key".

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7028"></a>

### <a name="mt7028-the-watch-extension--has-an-invalid-infoplist-it-does-not-contain-an-nsextension-dictionary"></a>MT7028: a extensão Watch ' * ' tem um info. plist inválido: ele não contém um dicionário NSExtension.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7029"></a>

### <a name="mt7029-the-watch-extension--has-an-invalid-infoplist-the-nsextensionpointidentifier-must-be-comapplewatchkit"></a>MT7029: a extensão de inspeção ' * ' tem um info. plist inválido: o NSExtensionPointIdentifier deve ser "com. Apple. watchkit".

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7030"></a>

### <a name="mt7030-the-watch-extension--has-an-invalid-infoplist-the-nsextension-dictionary-must-contain-nsextensionattributes"></a>MT7030: a extensão de inspeção ' * ' tem um info. plist inválido: o dicionário NSExtension deve conter NSExtensionAttributes.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7031"></a>

### <a name="mt7031-the-watch-extension--has-an-invalid-infoplist-the-nsextensionattributes-dictionary-must-contain-a-wkappbundleidentifier"></a>MT7031: a extensão de inspeção ' * ' tem um info. plist inválido: o dicionário NSExtensionAttributes deve conter um WKAppBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7032"></a>

### <a name="mt7032-the-watchkit-extension--has-an-invalid-infoplist-uirequireddevicecapabilities-should-not-contain-the-watch-companion-capability"></a>MT7032: a extensão WatchKit ' * ' tem um info. plist inválido. o UIRequiredDeviceCapabilities não deve conter o recurso ' Watch-Companion '.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7033"></a>

### <a name="mt7033-the-watch-app--does-not-contain-an-infoplist"></a>MT7033: o aplicativo de inspeção ' * ' não contém um info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7034"></a>

### <a name="mt7034-the-watch-app--does-not-specify-a-cfbundleidentifier"></a>MT7034: o aplicativo de inspeção ' * ' não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7035"></a>

### <a name="mt7035-the-watch-app--does-not-have-a-valid-uidevicefamily-value-expected--but-found--"></a>MT7035: o aplicativo de inspeção ' \* ' não tem um valor de UIDeviceFamily válido. Esperado ' \* ', mas encontrado ' \* ( \* ) '.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7036"></a>

### <a name="mt7036-the-watch-app--does-not-specify-a-cfbundleexecutable"></a>MT7036: o aplicativo de inspeção ' * ' não especifica um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7037"></a>

### <a name="mt7037-the-watchkit-extension-extensionname-has-an-invalid-wkappbundleidentifier-value--it-does-not-match-the-watch-apps-cfbundleidentifier-"></a>MT7037: a extensão WatchKit ' {ExtensionName} ' tem um valor de WKAppBundleIdentifier inválido (' \* '); ela não corresponde à CFBundleIdentifier do aplicativo de inspeção (' \* ').

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7038"></a>

### <a name="mt7038-the-watch-app--has-an-invalid-infoplist-the-wkcompanionappbundleidentifier-must-exist-and-must-match-the-main-app-bundles-cfbundleidentifier"></a>MT7038: o aplicativo de inspeção ' * ' tem um info. plist inválido: o WKCompanionAppBundleIdentifier deve existir e deve corresponder ao CFBundleIdentifier do pacote do aplicativo principal.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7039"></a>

### <a name="mt7039-the-watch-app--has-an-invalid-infoplist-the-lsrequiresiphoneos-key-must-not-be-present"></a>MT7039: o aplicativo de inspeção ' * ' tem um info. plist inválido: a chave LSRequiresIPhoneOS não deve estar presente.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7040"></a>

### <a name="mt7040-the-app-bundle-appbundlepath-does-not-contain-an-infoplist"></a>MT7040: o pacote de aplicativos {AppBundlePath} não contém um info. plist.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7041"></a>

### <a name="mt7041-main-infoplist-path-does-not-specify-a-cfbundleidentifier"></a>MT7041: o caminho do info. plist principal não especifica um CFBundleIdentifier.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7042"></a>

### <a name="mt7042-main-infoplist-path-does-not-specify-a-cfbundleexecutable"></a>MT7042: o caminho do info. plist principal não especifica um CFBundleExecutable.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7043"></a>

### <a name="mt7043-main-infoplist-path-does-not-specify-a-cfbundlesupportedplatforms"></a>MT7043: o caminho do info. plist principal não especifica um CFBundleSupportedPlatforms.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7044"></a>

### <a name="mt7044-main-infoplist-path-does-not-specify-a-uidevicefamily"></a>MT7044: o caminho do info. plist principal não especifica um UIDeviceFamily.

*Tarefa do MSBuild: ValidateAppBundleTaskBase*

<a name="MT7045"></a>

### <a name="mt7045-unrecognized-format-"></a>MT7045: formato não reconhecido: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

Onde * pode ser:

- string
- matriz
- dict
- bool
- real
- Número inteiro
- date
- data

<a name="MT7046"></a>

### <a name="mt7046-add-entry--incorrectly-specified"></a>MT7046: Add: entry, *, especificado incorretamente.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7047"></a>

### <a name="mt7047-add-entry--contains-invalid-array-index"></a>MT7047: Add: entry, *, contém índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7048"></a>

### <a name="mt7048-add--entry-already-exists"></a>MT7048: Add: * a entrada já existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7049"></a>

### <a name="mt7049-add-cant-add-entry--to-parent"></a>MT7049: Add: não é possível adicionar a entrada, *, ao pai.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7050"></a>

### <a name="mt7050-delete-cant-delete-entry--from-parent"></a>MT7050: Delete: não é possível excluir a entrada, *, do pai.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7051"></a>

### <a name="mt7051-delete-entry--contains-invalid-array-index"></a>MT7051: Delete: entry, *, contém índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7052"></a>

### <a name="mt7052-delete-entry--does-not-exist"></a>MT7052: Delete: entry, *, não existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7053"></a>

### <a name="mt7053-import-entry--incorrectly-specified"></a>MT7053: import: entry, *, especificado incorretamente.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7054"></a>

### <a name="mt7054-import-entry--contains-invalid-array-index"></a>MT7054: import: entry, *, contém índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7055"></a>

### <a name="mt7055-import-error-reading-file-"></a>MT7055: import: erro ao ler o arquivo: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7056"></a>

### <a name="mt7056-import-cant-add-entry--to-parent"></a>MT7056: import: não é possível adicionar a entrada, *, ao pai.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7057"></a>

### <a name="mt7057-merge-cant-add-array-entries-to-dict"></a>MT7057: Merge: não é possível adicionar entradas de matriz ao dict.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7058"></a>

### <a name="mt7058-merge-specified-entry-must-be-a-container"></a>MT7058: Merge: a entrada especificada deve ser um contêiner.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7059"></a>

### <a name="mt7059-merge-entry--contains-invalid-array-index"></a>MT7059: Merge: entry, *, contém índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7060"></a>

### <a name="mt7060-merge-entry--does-not-exist"></a>MT7060: Merge: entry, *, não existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7061"></a>

### <a name="mt7061-merge-error-reading-file-"></a>MT7061: Merge: erro ao ler o arquivo: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7062"></a>

### <a name="mt7062-set-entry--incorrectly-specified"></a>MT7062: Set: entry, *, especificado incorretamente.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7063"></a>

### <a name="mt7063-set-entry--contains-invalid-array-index"></a>MT7063: Set: entry, *, contém índice de matriz inválido.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7064"></a>

### <a name="mt7064-set-entry--does-not-exist"></a>MT7064: Set: entry, *, não existe.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7065"></a>

### <a name="mt7065-unknown-propertylist-editor-action-"></a>MT7065: ação do editor PropertyList desconhecida: *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7066"></a>

### <a name="mt7066-error-loading--"></a>MT7066: erro ao carregar ' * ': *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

<a name="MT7067"></a>

### <a name="mt7067-error-saving--"></a>MT7067: erro ao salvar ' * ': *.

*Tarefa do MSBuild: PropertyListEditorTaskBase*

## <a name="mt8xxx-runtime-error-messages"></a>MT8xxx: mensagens de erro de tempo de execução

<!--
 MT8xxx runtime
  MT800x misc
  -->

<a name="MT8001"></a>

### <a name="mt8001-version-mismatch-between-the-native-xamarinios-runtime-and-monotouchdll-please-reinstall-xamarinios"></a>MT8001: incompatibilidade de versão entre o tempo de execução do Xamarin. iOS nativo e o monotouch.dll. Reinstale o Xamarin. iOS.

<a name="MT8002"></a>

### <a name="mt8002-could-not-find-the-method--in-the-type-"></a>MT8002: não foi possível localizar o método ' \* ' no tipo ' \* '.

<a name="MT8003"></a>

### <a name="mt8003-failed-to-find-the-closed-generic-method--on-the-type-"></a>MT8003: falha ao localizar o método genérico fechado ' \* ' no tipo ' \* '.

<a name="MT8004"></a>

### <a name="mt8004-cannot-create-an-instance-of--for-the-native-object-0x-of-type--because-another-instance-already-exists-for-this-native-object-of-type-"></a>MT8004: não é possível criar uma instância do \* para o objeto nativo 0x * (do tipo " \* "), pois outra instância já existe para esse objeto nativo (do tipo \* ).

<a name="MT8005"></a>

### <a name="mt8005-wrapper-type--is-missing-its-native-objectivec-class-"></a>MT8005: o tipo de wrapper ' \* ' não tem sua classe ObjectiveC nativa ' \* '.

<a name="MT8006"></a>

### <a name="mt8006-failed-to-find-the-selector--on-the-type-"></a>MT8006: falha ao localizar o seletor ' \* ' no tipo ' \* '

<a name="MT8007"></a>

### <a name="mt8007-cannot-get-the-method-descriptor-for-the-selector--on-the-type--because-the-selector-does-not-correspond-to-a-method"></a>MT8007: não é possível obter o descritor de método para o seletor ' \* ' no tipo ' \* ', porque o seletor não corresponde a um método

<a name="MT8008"></a>

### <a name="mt8008-the-loaded-version-of-xamariniosdll-was-compiled-for--bits-while-the-process-is--bits-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8008: a versão carregada do Xamarin.iOS.dll foi compilada para \* bits, enquanto o processo é \* bits. Registre um bug em http://bugzilla.xamarin.com .

Isso indica que algo está errado no processo de compilação. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8009"></a>

### <a name="mt8009-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-s-parameter--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8009: não é possível localizar o bloco para delegar o método de conversão para o método *.* s parâmetro # *. Registre um bug em http://bugzilla.xamarin.com .

Isso indica que uma API não foi associada corretamente. Se essa for uma API exposta pelo Xamarin, registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new). Se for uma associação de terceiros, entre em contato com o fornecedor.

<a name="MT8010"></a>

### <a name="mt8010-native-type-size-mismatch-between-xamariniosmacdll-and-the-executing-architecture-xamariniosmacdll-was-built-for--bit-while-the-current-process-is--bit"></a>MT8010: incompatibilidade de tamanho de tipo nativo entre Xamarin. [iOS | Mac]. dll e a arquitetura em execução. Xamarin. [iOS | O Mac]. dll foi criado para o * bit, enquanto o processo atual é de * bit.

Isso indica que algo está errado no processo de compilação. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8011"></a>

### <a name="mt8011-unable-to-locate-the-delegate-to-block-conversion-attribute-delegateproxy-for-the-return-value-for-the-method--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8011: não é possível localizar o delegate para bloquear o atributo de conversão ([DelegateProxy]) para o valor de retorno para o método *.*. Registre um bug em http://bugzilla.xamarin.com .

O Xamarin. iOS não pôde localizar um método necessário em tempo de execução (para converter um delegado em um bloco).

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8012"></a>

### <a name="mt8012-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8012: DelegateProxyAttribute inválido para o valor de retorno para o método *.*: DelegateType é nulo. Registre um bug em http://bugzilla.xamarin.com .

O atributo DelegateProxy do método em questão é inválido.

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8013"></a>

### <a name="mt8013-invalid-delegateproxyattribute-for-the-return-value-for-the-method--delegatetype-2-specifies-a-type-without-a-handler-field-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8013: DelegateProxyAttribute inválido para o valor de retorno do método *.*: DelegateType ( {2} ) especifica um tipo sem um campo ' Handler '. Registre um bug em http://bugzilla.xamarin.com .

O `[DelegateProxy]` atributo do método em questão é inválido.

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8014"></a>

### <a name="mt8014-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-null-please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8014: DelegateProxyAttribute inválido para o valor de retorno para o método *.*: o {2} campo ' Handler ' do representante ' () é nulo. Registre um bug em http://bugzilla.xamarin.com .

O `[DelegateProxy]` atributo do método em questão é inválido.

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8015"></a>

### <a name="mt8015-invalid-delegateproxyattribute-for-the-return-value-for-the-method--the-delegatetypes-2-handler-field-is-not-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8015: DelegateProxyAttribute inválido para o valor de retorno para o método *.*: o {2} campo ' Handler ' do representante ' () não é um delegado, é um *. Registre um bug em http://bugzilla.xamarin.com .

O atributo DelegateProxy do método em questão é inválido.

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8016"></a>

### <a name="mt8016-unable-to-convert-delegate-to-block-for-the-return-value-for-the-method--because-the-input-isnt-a-delegate-its-a--please-file-a-bug-at-httpbugzillaxamarincom"></a>MT8016: não é possível converter delegate para Block para o valor de retorno do método *.*, porque a entrada não é um delegado, é um *. Registre um bug em http://bugzilla.xamarin.com .

O `[DelegateProxy]` atributo do método em questão é inválido.

Isso geralmente indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<!-- 8017 is used by mmp -->

<a name="MT8018"></a>

### <a name="mt8018-internal-consistency-error-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8018: erro de consistência interna. Registre um relatório de bugs em http://bugzilla.xamarin.com .

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8019"></a>

### <a name="mt8019-could-not-find-the-assembly--in-the-loaded-assemblies"></a>MT8019: não foi possível encontrar o assembly * nos assemblies carregados.

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8020"></a>

### <a name="mt8020-could-not-find-the-module-with-metadatatoken--in-the-assembly-"></a>MT8020: não foi possível encontrar o módulo com MetadataToken \* no assembly \* .

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8021"></a>

### <a name="mt8021-unknown-implicit-token-type-"></a>MT8021: tipo de token implícito desconhecido: *.

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8022"></a>

### <a name="mt8022-expected-the-token-reference--to-be-a--but-its-a--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8022: espera-se que a referência de token \* seja um \* , mas é um \* . Registre um relatório de bugs em http://bugzilla.xamarin.com .

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8023"></a>

### <a name="mt8023-an-instance-object-is-required-to-construct-a-closed-generic-method-for-the-open-generic-method--token-reference--please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MT8023: um objeto de instância é necessário para construir um método genérico fechado para o método genérico aberto: \* (referência de token: \* ). Registre um relatório de bugs em http://bugzilla.xamarin.com .

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

<a name="MT8024"></a>

### <a name="mt8024-could-not-find-a-valid-extension-type-for-the-smart-enum-smart_type-please-file-a-bug-at-httpsbugzillaxamarincom"></a>MT8024: não foi possível encontrar um tipo de extensão válido para o Smart enum ' {smart_type} '. Registre um bug em https://bugzilla.xamarin.com .

Isso indica um bug no Xamarin. iOS. Registre um novo problema no [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).