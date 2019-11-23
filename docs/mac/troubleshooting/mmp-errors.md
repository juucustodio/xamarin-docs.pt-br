---
title: Mensagens de erro do Xamarin. Mac (MMP)
description: Este documento lista os erros gerados pelo MMP, a ferramenta usada para empacotar assemblies compilados em um aplicativo Mac executável.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 32fecaff7f6896c03f3ac094d478fef3338f844d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025717"
---
# <a name="xamarinmac-error-messages-mmp"></a>Mensagens de erro do Xamarin. Mac (MMP)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx: mensagens de erro do MMP

Por exemplo, parâmetros, ambiente, ferramentas ausentes.

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000: erro inesperado-registre um relatório de bugs em https://github.com/xamarin/xamarin-macios/issues/new

Ocorreu uma condição de erro inesperada. Registre [um relatório de bugs](https://github.com/xamarin/xamarin-macios/issues/new) com o máximo de informações possível, incluindo:

* Logs de compilação completos, com detalhes máximos (por exemplo, `-v -v -v -v` nos **argumentos MMP adicionais**);
* Um caso de teste mínimo que reproduza o erro; e
* Todas as informações de versão

A maneira mais fácil de obter informações exatas sobre a versão é usar o menu **Xamarin Studio** , **sobre Xamarin Studio** item, mostrar o botão **detalhes** e copiar/colar a versão informações (você pode usar o botão **copiar informações** ).

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001: esta versão do Xamarin. Mac requer o {0} mono (a versão atual do Mono é {1}). Atualize o mono. Framework de http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003: o nome do aplicativo '{0}. exe ' está em conflito com um nome de SDK ou de assembly de produto (. dll).

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007: o assembly raiz '{0}' não existe

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008: você deve fornecer apenas um assembly raiz, {0} assemblies encontrados: '{1}'

<a name="MM0009" />

#### <a name="mm0009-error-while-loading-assemblies-"></a>MM0009: erro ao carregar assemblies: *.

Ocorreu um erro ao carregar os assemblies das referências do assembly raiz. Mais informações podem ser fornecidas na saída da compilação.

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010: não foi possível analisar os argumentos de linha de comando: {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016: a opção '{0}' foi preterida.

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017: você deve fornecer um assembly raiz

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018: argumento de linha de comando desconhecido: '{0}'

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020: as opções válidas para '{0}' são '{1}'.

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023: o nome do aplicativo '{0}. exe ' está em conflito com outro assembly de usuário.

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026: não foi possível analisar o argumento de linha de comando '{0}': {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043: não há suporte para o coletor de lixo Boehm. O coletor de lixo do SGen foi selecionado em seu lugar.

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050: você não pode fornecer um assembly raiz se--no-root-assembly for passado.

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051: um diretório de saída (--output) é necessário se--no-root-assembly for passado.

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053: não é possível desabilitar um novo Refcount com o API Unificada.

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056: não é possível encontrar o Xcode em nenhum de nossos locais padrão. Instale o Xcode ou passe um caminho personalizado usando--sdkroot =\<caminho >

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059: não foi possível localizar o Xcode atualmente selecionado no sistema: {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060: não foi possível localizar o Xcode atualmente selecionado no sistema. ' Xcode-Select--Print-path ' retornou '{0}', mas esse diretório não existe.

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068: valor inválido para a estrutura de destino: {0}.

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071: plataforma desconhecida: *. Isso geralmente indica um bug no Xamarin. Mac; Registre um relatório de bugs em https://bugzilla.xamarin.com com um caso de teste.

Isso geralmente indica um bug no Xamarin. Mac; Registre um relatório de bugs em [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) com um caso de teste.

<a name="MM0073" />

#### <a name="mm0073-xamarinmac--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MM0073: o Xamarin. Mac \* não dá suporte a um destino de implantação de \* (o mínimo é \*). Selecione um destino de implantação mais recente no info. plist do seu projeto.

O destino de implantação mínimo é aquele especificado na mensagem de erro; Selecione um destino de implantação mais recente no info. plist do projeto.

Se a atualização do destino de implantação não for possível, use uma versão mais antiga do Xamarin. Mac.

<a name="MM0074" />

#### <a name="mm0074-xamarinmac--does-not-support-a-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinmac"></a>MM0074: o Xamarin. Mac \* não dá suporte a um destino de implantação de \* (o máximo é \*). Selecione um destino de implantação mais antigo no info. plist do seu projeto ou atualize para uma versão mais recente do Xamarin. Mac.

O xamarin. Mac não oferece suporte à definição do destino de implantação mínimo para uma versão superior à versão para a qual essa versão específica do Xamarin. Mac foi criada.

Selecione um destino de implantação mínimo mais antigo no info. plist do projeto ou atualize para uma versão mais recente do Xamarin. Mac.

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079: erro interno-nenhum executável foi copiado para o pacote de aplicativos. Entre em contato com 'support@xamarin.com'

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080: desabilitar NewRefCount,--New-Refcount: false, foi preterido.

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091: esta versão do Xamarin. Mac requer o SDK do \* (fornecido com o Xcode \*). Atualize o Xcode para obter os arquivos de cabeçalho necessários ou use o registrador dinâmico ou defina o comportamento do vinculador gerenciado para vincular apenas os SDKs da plataforma ou do link Framework (para tentar evitar as novas APIs).

O Xamarin. Mac requer os arquivos de cabeçalho, da versão do SDK especificada na mensagem de erro, para compilar seu aplicativo com o registrador estático. A maneira recomendada para corrigir esse erro é atualizar o Xcode para obter o SDK necessário, incluindo todos os arquivos de cabeçalho necessários. Se você tiver várias versões do Xcode instaladas ou quiser usar um Xcode em um local não padrão, certifique-se de definir o local do Xcode correto nas preferências do IDE.

Uma solução potencial, alternativa, é habilitar o vinculador gerenciado. Isso removerá a API não usada, incluindo, na maioria dos casos, a nova API em que os arquivos de cabeçalho estão ausentes (ou incompletos). No entanto, isso não funcionará se o seu projeto usar a API que foi introduzida em um SDK mais recente do que o que o Xcode fornece.

Uma segunda solução potencial, alternativa, é usar o registrador dinâmico em vez disso. Isso irá impor um custo de inicialização ao registrar tipos dinamicamente, mas remover o requisito de arquivo de cabeçalho. 

Uma solução Straw seria usar uma versão mais antiga do Xamarin. Mac, uma que dá suporte ao SDK que seu projeto requer.

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097: o arquivo Machine. config '{0}' não pode ser encontrado.

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098: a compilação da AOT só está disponível em Unificação

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MM0099: erro interno {0}. Registre um relatório de bug com um caso de teste (https://bugzilla.xamarin.com).

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114: a compilação híbrida do AOT requer que todos os assemblies sejam compilados na AOT.

<a name="MM0129" />

#### <a name="mm0129-debugging-symbol-file-for--does-not-match-the-assembly-and-is-ignored"></a>MM0129: o arquivo de símbolo de depuração para ' * ' não corresponde ao assembly e é ignorado.

Os símbolos de depuração – um. PDB (somente PDB portátil) ou um arquivo. mdb – para o assembly especificado não puderam ser carregados.

Isso geralmente significa que o assembly é mais recente ou mais antigo do que os símbolos. Como eles não são correspondentes, eles não podem ser usados e os símbolos são ignorados.

Esse aviso não afetará o aplicativo que está sendo compilado; no entanto, talvez você não consiga depurá-lo totalmente (em particular o código do assembly especificado). Além disso, as exceções, os rastreamentos de pilha e os relatórios de falhas podem estar faltando em algumas informações.

Relate esse problema ao Publicador do pacote de assembly (por exemplo, autor do NuGet) para que isso possa ser corrigido em suas versões futuras.

<a name="MM0130" />

#### <a name="mm0130-no-root-assemblies-found-you-should-provide-at-least-one-root-assembly"></a>MM0130: nenhum assembly raiz encontrado. Você deve fornecer pelo menos um assembly raiz.

Ao executar `--runregistrar`, pelo menos um assembly raiz deve ser fornecido.

<a name="MM0131" />

#### <a name="mm0131-product-assembly-0-not-found-in-assembly-list-1"></a>MM0131: assembly de produto '{0}' não encontrado na lista de assemblies: '{1}'

Ao executar `--runregistrar`, a lista de assemblies deve incluir o assembly do produto, Xamarin. Mac, XamMac.

<a name="MM0132" />

#### <a name="mm0132-unknown-optimization--valid-values-are-"></a>MM0132: otimização desconhecida: \*. Os valores válidos são: \*

A otimização especificada não foi reconhecida.

O formato aceito é `[+|-]optimization-name`, em que `optimization-name` é um dos valores listados na mensagem de erro.

Consulte [criar otimizações](https://developer.xamarin.com/guides/cross-platform/macios/build-optimizations) para obter uma descrição completa de cada otimização.

<a name="MM0133" />

#### <a name="mm0133-found-more-than-1-assembly-matching-0-choosing-first-1"></a>MM0133: foi encontrado mais de 1 correspondência de assembly '{0}' escolhendo primeiro: '{1}'

<a name="MM0134" />

#### <a name="mm0134-32-bit-applications-should-be-migrated-to-64-bit"></a>MM0134: os aplicativos de 32 bits devem ser migrados para 64 bits.

A Apple anunciou que não permitirá envios de armazenamento de aplicativos macOS de aplicativos de 32 bits (a partir de janeiro de 2018). 

Além disso, os aplicativos de 32 bits não serão executados na versão do macOS após a alta Sierra "sem comprometimentos". 

Para obter mais detalhes: https://developer.apple.com/news/?id=06282017a

Considere atualizar seu aplicativo e quaisquer dependências para 64 bits.

<a name="MM0135" />

#### <a name="mm0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MM0135: não vinculou a estrutura do sistema '{0}' (referenciado pelo assembly '{1}') porque ele foi introduzido no {2} {3}e estamos usando o {2} {4} SDK.

Para compilar seu aplicativo, o Xamarin. Mac deve vincular as bibliotecas do sistema, alguns dos quais dependem da versão do SDK especificada na mensagem de erro. Como você está usando uma versão mais antiga do SDK, as invocações para essas APIs podem falhar em tempo de execução.

A maneira recomendada para corrigir esse erro é atualizar o Xcode para obter o SDK necessário. Se você tiver várias versões do Xcode instaladas ou quiser usar um Xcode em um local não padrão, certifique-se de definir o local do Xcode correto nas preferências do IDE.

Como alternativa, habilite o [vinculador](https://docs.microsoft.com/xamarin/mac/deploy-test/linker) gerenciado para remover APIs não usadas, incluindo (na maioria dos casos) as novas que exigem a biblioteca especificada. No entanto, isso não funcionará se o seu projeto exigir APIs introduzidas em um SDK mais recente do que o que o Xcode fornece.

Como uma solução de última Straw, use uma versão mais antiga do Xamarin. Mac que não exija que esses novos SDKs estejam presentes durante o processo de compilação.

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx: file copy/symlinks (relacionado ao projeto)

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034: não foi possível criar symlink ' {file} '-> ' {target} ': erro {Number}

### <a name="mm14xx-product-assemblies"></a>MM14xx: assemblies de produto

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401: o assembly '{0}' necessário está ausente das referências

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402: o assembly '{0}' não é compatível com esta ferramenta

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403: não foi possível encontrar o {0} '{1}'. A estrutura de destino '{0}' é inutilizável para empacotar o aplicativo.

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404: a estrutura de destino '{0}' é inválida.

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405: useFullXamMacFramework sempre deve ter como destino o Framework .NET 4,5, não '{0}', que é inválido

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406: a estrutura de destino '{0}' é inválida quando voltados Xamarin. Mac 4,5 .NET framwork.

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407: incompatibilidade entre a referência do Xamarin. Mac '{0}' e a estrutura de destino selecionada '{1}'.

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx: erros de coleta de assembly (não requer vinculador)

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501: não é possível resolver a referência: {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600: não é uma biblioteca dinâmica de Mach-O (cabeçalho desconhecido ' 0x{0}'): {1}.

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601: não é uma biblioteca estática (cabeçalho desconhecido '{0}'): {1}.

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602: não é uma biblioteca dinâmica de Mach-O (cabeçalho desconhecido ' 0x{0}'): {1}.

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603: formato desconhecido para a entrada de Fat na posição {0} em {1}.

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604: o arquivo do tipo {0} não é um arquivo MachO ({1}).

## <a name="mm2xxx-linker"></a>MM2xxx: Linker

### <a name="mm20xx-linker-general-errors"></a>MM20xx: erros do vinculador (geral)

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001: não foi possível vincular assemblies

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002: não é possível resolver a referência: {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003: a opção '{0}' será ignorada porque a vinculação está desabilitada

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004: não foi possível localizar o arquivo de definições de vinculador extra '{0}'.

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005: não foi possível analisar as definições de '{0}'.

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006: a biblioteca nativa '{0}' foi referenciada, mas não pôde ser encontrada.

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007: o Xamarin. Mac API Unificada em relação a um perfil completo do .NET não dá suporte à vinculação. Passe o sinalizador-nolink.

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009: referenciado por {0}.{1}\*\* essa mensagem está relacionada ao MM2006 \*\*

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010: `{0}`de HttpMessageHandler desconhecido. Os valores válidos são HttpClientHandler (padrão), CFNetworkHandler ou NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011: TLSProvider desconhecido '{0}.  Os valores válidos são Default ou appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012: somente a primeira {0} de {1} "referenciado por" avisos mostrados. \*\* esta mensagem relacionada a 2009 \*\*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013: falha ao resolver a referência a "{0}", referenciada em "{1}". O aplicativo não incluirá o assembly referenciado e poderá falhar em tempo de execução.

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014: as extensões Xamarin. Mac não dão suporte à vinculação. A solicitação de vinculação será ignorada. \*\* essa mensagem está obsoleta no XM 3.6 + \*\*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016: opção de `{0}` de TlsProvider inválida. O único valor válido `{1}` será usado.

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017: não foi possível processar a descrição XML: {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x: falha ao processar o otimizador de associação `...`.

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100: o Xamarin. Mac API Clássica não dá suporte à vinculação de plataforma.

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103: erro ao processar o assembly '\*': *

Ocorreu um erro inesperado ao processar um assembly.

O assembly que está causando o problema é nomeado na mensagem de erro. Para corrigir esse problema, o assembly precisará ser fornecido em um relatório de [bug](https://bugzilla.xamarin.com) junto com um log de compilação completo com detalhes habilitados (ou seja, `-v -v -v -v` nos **argumentos mTouch adicionais**).

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: não é possível vincular o assembly '{0}' porque ele é de modo misto.

Os assemblies de modo misto não podem ser processados pelo vinculador.

Consulte https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies para obter mais informações sobre assemblies de modo misto.

<a name="MM2106" />

#### <a name="mm2106-could-not-optimize-the-call-to-blockliteralsetupblockunsafe-in--at-offset--because-"></a>MM2106: não foi possível otimizar a chamada para BlockLiteral. SetupBlock [unsafe] em \* no offset \* porque \*.

O vinculador relata esse aviso quando não pode otimizar uma chamada para `BlockLiteral.SetupBlock` ou `Block.SetupBlockUnsafe`.

A mensagem apontará para o método que chama `BlockLiteral.SetupBlock[Unsafe]`e também pode fornecer pistas sobre por que a chamada não pôde ser otimizada.

Registre um [problema](https://github.com/xamarin/xamarin-macios/issues/new) junto com um log de compilação completo para que possamos investigar o que deu errado e possivelmente habilitar mais cenários no futuro.

<a name="MM2107" />

#### <a name="mm2107-its-not-safe-to-remove-the-dynamic-registrar-because-reasons"></a>MM2107: não é seguro remover o registrador dinâmico porque {Reasons}

O vinculador relata esse aviso quando o desenvolvedor solicita a remoção do registrador dinâmico (passando `--optimize:remove-dynamic-registrar` para MMP), mas o vinculador determina que não é seguro fazê-lo.

Para remover o aviso, remova o argumento de otimização para MMP ou passe `--nowarn:2107` para ignorá-lo.

Por padrão, essa opção será habilitada automaticamente sempre que for possível e segura.

<a name="MM2108" />

#### <a name="mm2108-0-was-stripped-of-architectures-except-1-to-comply-with-app-store-restrictions-this-could-break-exisiting-codesigning-signatures-consider-stripping-the-library-with-lipo-or-disabling-with---optimize-trim-architectures"></a>MM2108: '{0}' foi eliminado de arquiteturas, exceto '{1}' para atender às restrições da loja de aplicativos. Isso pode interromper as assinaturas de codesignação existentes. Considere a remoção da biblioteca com lipo ou a desabilitação com--Optimize =-arrumar-Architectures ");

A loja de aplicativos agora rejeita aplicativos que contêm bibliotecas e estruturas que contêm variantes de 32 bits. A biblioteca foi eliminada de arquiteturas não utilizadas quando copiada para o pacote de aplicativo final.

Isso é seguro em geral e reduzirá o tamanho do pacote de aplicativos como um benefício adicional. No entanto, qualquer estrutura agrupada com código assinado terá sua assinatura invalidada (e será reassinada mais tarde se o aplicativo for assinado).

Considere o uso de `lipo` para remover as arquiteturas desnecessárias permanentemente da biblioteca de origem. Se o aplicativo não estiver sendo publicado na loja de aplicativos, essa remoção poderá ser desabilitada passando `--optimize=-trim-architectures` como argumentos MMP adicionais.

<a name="MM2109"/>

#### <a name="mm2109-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2109: o Xamarin. Mac API Clássica não dá suporte à vinculação de plataforma.

## <a name="mm3xxx-aot"></a>MM3xxx: AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx: erros da AOT (geral)

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001: não foi possível AOT o assembly '{0}'

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009: a AOT de '{0}' foi solicitada, mas não foi encontrada

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010: a exclusão da AOT de '{0}' foi solicitada, mas não foi encontrada

## <a name="mm4xxx-code-generation"></a>MM4xxx: geração de código

### <a name="mm40xx-driverm"></a>MM40xx: driver. m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001: não foi possível expandir o modelo principal para `{0}`.

### <a name="mm41xx-registrar"></a>MM41xx: registrador

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134: seu aplicativo está usando a estrutura "{0}", que não está incluída no SDK do MacOS que você está usando para criar seu aplicativo (essa estrutura foi introduzida no OSX {2}, enquanto você está criando com o SDK do MacOS {1}.) Essa configuração não tem suporte com o registrador estático (Pass--registrar: dinâmico como um argumento MMP adicional na opção de Build do Mac do seu projeto para selecionar). Como alternativa, selecione um SDK mais recente nas opções de Build do Mac do seu aplicativo.

<a name="MM4173" />

#### <a name="mm4173-the-registrar-cant-compute-the-block-signature-for-the-delegate-of-type-delegate-type-in-the-method-method-because-"></a>MM4173: o registrador não pode calcular a assinatura de bloco para o delegado do tipo {delegado-Type} no método {Method} porque *.

Este é um aviso que indica que o registrador não pôde injetar a assinatura de bloco do método especificado no código do registrador gerado, pois o registrador não pôde computar o registro.

Isso significa que a assinatura de bloco deve ser computada em tempo de execução, o que é um pouco mais lento.

Atualmente, há dois motivos possíveis para este aviso:

1. O tipo do delegado gerenciado é um `System.Delegate` ou `System.MulticastDelegate`. Esses tipos não representam uma assinatura específica, o que significa que o registrador não pode também computar a assinatura nativa correspondente. Nesse caso, a correção é usar um tipo de delegado específico para o bloco (como alternativa, o aviso pode ser ignorado adicionando `--nowarn:4173` como um argumento MMP adicional nas opções de Build do Mac do projeto).
2. O registrador não pode localizar o método `Invoke` do delegado. Isso não deveria acontecer, portanto, registre um [problema](https://github.com/xamarin/xamarin-macios/issues/new) com um projeto de teste para que possamos corrigi-lo.

<a name="MT4174" />

#### <a name="mt4174-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-methods-parameter-parameter"></a>MT4174: não é possível localizar o bloco para delegar o método de conversão para o parâmetro do método {method} ' # {Parameter}.

Este é um aviso que indica que o registrador estático não pôde localizar o método para criar um delegado para um bloco Objective-C. Uma tentativa será feita em tempo de execução para localizar o método, mas provavelmente falhará também (com uma exceção MT8009).

Um motivo possível para esse aviso é gravar manualmente as associações para uma API que usa blocos. É recomendável usar um projeto de associação para associar o código de Objective-C – em particular quando ele envolve blocos – já que é bastante complicado obtê-lo ao fazer isso manualmente.

Se esse não for o caso, registre um [problema](https://github.com/xamarin/xamarin-macios/issues/new) com um caso de teste.

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx: GCC e ferramentas

### <a name="mm51xx-compilation"></a>MM51xx: compilação

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101: compilador '{0}' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode.

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103: falha ao compilar. Código de erro-{0}. Registre um relatório de bugs em http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx: vinculação

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202: Mono. Framework MDK está ausente. Instale o MDK para sua versão do mono. Framework de http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203: não é possível encontrar libxammac. a, provavelmente devido a uma instalação corrompida do Xamarin. Mac. Reinstale o Xamarin. Mac.

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x86_64-is-only-supported-on-non-classic-profiles"></a>MM5204: arquitetura inválida. Só há suporte para x86_64 em perfis não clássicos.

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x86_64-when---profilemobile"></a>MM5205: arquitetura '{0}' inválida. As arquiteturas válidas são i386 e x86_64 (quando--Profile = Mobile).

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218: não é possível ignorar o símbolo dinâmico {Symbol} (--ignore-Dynamic-Symbol = {Symbol}) porque ele não foi detectado como um símbolo dinâmico.

Consulte o [aviso de mTouch equivalente](~/ios/troubleshooting/mtouch-errors.md#MT5218).

<!-- 5206 used by mtouch -->
<!-- 5207 used by mtouch -->
<!-- 5208 used by mtouch -->
<!-- 5209 used by mtouch -->
<!-- 5210 used by mtouch -->
<!-- 5211 used by mtouch -->
<!-- 5212 used by mtouch -->
<!-- 5213 used by mtouch -->
<!-- 5214 used by mtouch -->
<!-- 5215 used by mtouch -->
<!-- 5216 used by mtouch -->
<!-- 5217 used by mtouch -->

### <a name="mm53xx-other-tools"></a>MM53xx: outras ferramentas

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301: pkg-config não pôde ser encontrado. Instale o mono. Framework do http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305: ferramenta ' otool ' ausente. Instale o componente ' ferramentas de linha de comando ' do Xcode

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306: dependências ausentes. Instale o componente ' ferramentas de linha de comando ' do Xcode

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308: o contrato de licença do Xcode pode não ter sido aceito.  Inicie o Xcode.

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1-check-build-log-for-details"></a>MM5309: falha de vinculação nativa com o código de erro 1. Verifique o log de compilação para obter detalhes.

<a name="MM5310" />

#### <a name="mm5310-install_name_tool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310: falha de install_name_tool com um código de erro '{0}'. Verifique o log de compilação para obter detalhes.

<a name="MM5311" />

#### <a name="mm5311-lipo-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5311: lipo falhou com o código de erro '{0}'. Verifique o log de compilação para obter detalhes.

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx: tempo de execução

### <a name="mm800x-misc"></a>MM800x: misc

<!-- 8000 used by mtouch -->
<!-- 8001 used by mtouch -->
<!-- 8002 used by mtouch -->
<!-- 8003 used by mtouch -->
<!-- 8004 used by mtouch -->
<!-- 8005 used by mtouch -->
<!-- 8006 used by mtouch -->
<!-- 8007 used by mtouch -->
<!-- 8008 used by mtouch -->
<!-- 8009 used by mtouch -->
<!-- 8010 used by mtouch -->
<!-- 8011 used by mtouch -->
<!-- 8012 used by mtouch -->
<!-- 8013 used by mtouch -->
<!-- 8014 used by mtouch -->
<!-- 8015 used by mtouch -->
<!-- 8016 used by mtouch -->

<a name="MM8017" />

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017: não há suporte para o coletor de lixo Boehm. Em vez disso, use SGen.

<a name="MM8025" />

#### <a name="mm8025-failed-to-compute-the-token-reference-for-the-type-typeassemblyqualifiedname-because-reasons"></a>MM8025: falha ao calcular a referência de token para o tipo ' {Type. AssemblyQualifiedName} ' porque {Reasons}

Isso indica um bug no Xamarin. Mac. Registre um bug em [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac).

Uma possível solução alternativa seria desabilitar a otimização de `register-protocols`, passando `--optimize:-register-protocols` como um argumento MMP adicional nas opções de Build do Mac do projeto.

<a name="MM8026" />

#### <a name="mm8026--is-not-supported-when-the-dynamic-registrar-has-been-linked-away"></a>MM8026: * não tem suporte quando o registrador dinâmico foi vinculado.

Isso geralmente indica um bug no Xamarin. Mac, porque o registrador dinâmico não deve ser vinculado fora se necessário. Registre um bug em [https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

É possível forçar o vinculador a manter o registrador dinâmico adicionando `--optimize=-remove-dynamic-registrar` aos argumentos adicionais do MMP nas opções de Build do Mac do projeto.
