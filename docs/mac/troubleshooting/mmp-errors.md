---
title: Mensagens de erro Xamarin.Mac (mmp)
description: Um guia de referência de erros para mmp.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: df6a848023febcb7fc65cf6616aeae3b43b39262
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2018
---
# <a name="xamarinmac-error-messages-mmp"></a>Mensagens de erro Xamarin.Mac (mmp)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx: mensagens de erro mmp

Por exemplo, parâmetros, ambiente, falta de ferramentas.

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM0000: Erro inesperado - arquivo um bug informe em http://bugzilla.xamarin.com

Ocorreu uma condição de erro inesperado. Por favor, [relatório de erro](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) com tanta informação quanto possível, incluindo:

* Compilação completa logs, com um máximo de detalhamento (por exemplo, `-v -v -v -v` no **argumentos adicionais mmp**);
* Um caso de teste mínimo que reproduza o erro; e
* Todas as informações de versão

A maneira mais fácil de obter informações sobre a versão exata é usar o **Xamarin Studio** menu **Xamarin Studio** item, **Mostrar detalhes** botão e a versão de copiar/colar informações (você pode usar o **copiar informações** botão).

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001: Esta versão do Xamarin.Mac requer {0} Mono (a versão atual do Mono é \\{1 \\}). Atualize o Mono.framework de http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003: Aplicativo nome '{0} .exe' conflita com um nome de assembly (. dll) do SDK ou produto.

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007: O assembly de raiz '{0}' não existe

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008: Você deve fornecer uma raiz assembly único, encontrado {0} assemblies: '\\{1 \\}'

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010: Não foi possível analisar os argumentos de linha de comando: {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016: A opção '{0}' foi preterida.

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017: Você deve fornecer um assembly de raiz

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018: Argumento de linha de comando desconhecido: '{0}'

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020: As opções válidas para '{0}' são '\\{1 \\}'.

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023: Aplicativo nome '{0} .exe' está em conflito com outro assembly de usuário.

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026: Não foi possível analisar o argumento de linha de comando '{0}': \\{1 \\}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043: Não há suporte para o coletor de lixo Boehm. O coletor de lixo SGen foi selecionado em vez disso.

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050: Você não pode fornecer um assembly raiz se - assembly de raiz não for passado.

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051: Um diretório de saída (-saída) é necessário se - assembly de raiz não for passado.

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053: Não é possível desabilitar refcount novo com a API unificada.

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056: Não é possível localizar o Xcode em qualquer um dos nossos locais padrão. Instale o Xcode ou passar um caminho personalizado usando - sdkroot =<path>

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059: Não foi possível encontrar o Xcode selecionado atualmente no sistema: {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060: Não foi possível encontrar o Xcode selecionado atualmente no sistema. 'xcode-select-caminho de impressão' retornou '{0}', mas esse diretório não existe.

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068: O valor inválido para a estrutura de destino: {0}.

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071: Plataforma desconhecida: *. Isso geralmente indica um bug no Xamarin.Mac; Envie um relatório de erros em https://bugzilla.xamarin.com com um caso de teste.

Isso geralmente indica um bug no Xamarin.Mac; Envie um relatório de erros em [ https://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac) com um caso de teste.

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079: Erro interno - não há nenhum executável foi copiado para o pacote de aplicativos. Entre em contato com 'support@xamarin.com'

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080: Desabilitar NewRefCount, --new-refcount:false, foi preterido.

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091: Esta versão do Xamarin.Mac requer o * SDK (fornecido com o Xcode *). A atualização Xcode para obter os arquivos de cabeçalho necessários ou use o registro dinâmico ou definir o comportamento de vinculador gerenciado para a plataforma de Link ou Link Framework SDKs apenas (para tentar evitar as novas APIs).

Xamarin.Mac requer os arquivos de cabeçalho, da versão do SDK especificado na mensagem de erro, para criar seu aplicativo com o registrador estático. A maneira recomendada para corrigir esse erro é atualizar o Xcode para obter o SDK necessário, isso inclui todos os arquivos de cabeçalho necessários. Se você tiver várias versões do Xcode instalado, ou usar um Xcode em um local diferente do padrão, certifique-se de definir o local correto do Xcode nas preferências do IDE.

É uma solução de possível, alternativa habilitar o vinculador gerenciado. Isso removerá não utilizados incluindo de API, na maioria dos casos, a nova API de onde os arquivos de cabeçalho estão ausentes (ou incompletos). No entanto isso não funcionará se seu projeto usa a API que foi introduzida em um SDK mais recente que a sua Xcode fornece.

Uma potencial segundo solução alternativa é usar o registro dinâmico. Isso será impõe um custo de inicialização do dinamicamente registrar tipos mas remover o requisito de arquivo de cabeçalho. 

Uma solução de última straw seria usar uma versão mais antiga do Xamarin.Mac, que oferece suporte ao SDK do seu projeto requer.

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097: o arquivo Machine. config '{0}' não pode ser encontrado.

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098: A compilação de AOT só está disponível na Unificação

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MM0099: Erro interno {0}. Envie um relatório de erros com um caso de teste (http://bugzilla.xamarin.com).

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114: Híbrida AOT compilação requer que todos os assemblies a serem AOT compilado.

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx: cópia do arquivo / links simbólicos (projeto relacionado)

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034: Não foi possível criar symlink '{arquivo}' -> '{target}': erro {number}

### <a name="mm14xx-product-assemblies"></a>MM14xx: Assemblies de produtos

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401: O assembly necessário '{0}' está ausente das referências

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402: O assembly '{0}' não é compatível com esta ferramenta

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403: {0} '\\{1 \\}' não pôde ser encontrado. Estrutura de destino '{0}' não é utilizável para empacotar o aplicativo.

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404: A estrutura de destino '{0}' é inválida.

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405: useFullXamMacFramework devem sempre ter como destino do framework .NET 4.5, não '{0}' que é inválido

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406: A estrutura de destino '{0}' é inválido quando direcionando Xamarin.Mac 4.5 .NET estrutura.

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407: Incompatibilidade entre a estrutura de '{0}' e o destino de referência Xamarin.Mac selecionado '\\{1 \\}'.

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx: Erros Assembly coleta (não exigem o vinculador)

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501: Não é possível resolver a referência: {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600: Não uma Mach-O biblioteca dinâmica (cabeçalho desconhecido ' 0 x {0}'): \\{1 \\}.

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601: Não uma biblioteca estática (cabeçalho desconhecido '{0}'): \\{1 \\}.

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602: Não uma Mach-O biblioteca dinâmica (cabeçalho desconhecido ' 0 x {0}'): \\{1 \\}.

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603: Formato desconhecido entrada fat na posição {0} no \\{1 \\}.

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604: O arquivo do tipo {0} não é um arquivo MachO (\\{1 \\}).

## <a name="mm2xxx-linker"></a>MM2xxx: Linker

### <a name="mm20xx-linker-general-errors"></a>MM20xx: Erros de vinculador (geral)

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001: Não foi possível vincular assemblies

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002: Não é possível resolver a referência: {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003: Opção '{0}' será ignorada porque a vinculação está desabilitada

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004: Arquivo de definições de vinculador Extra '{0}' não pôde ser localizado.

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005: Definições de '{0}' não podem ser analisadas.

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006: Biblioteca nativa '{0}' foi referenciada, mas não pôde ser encontrada.

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007: Xamarin.Mac Unified API em relação a um perfil completo do .NET não suporta vinculação. Passe o sinalizador - nolink.

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009: Referenciada por {0}. \ {1 \} * * esta mensagem está relacionada ao MM2006 * *

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010: HttpMessageHandler desconhecido `{0}`. Os valores válidos são HttpClientHandler (padrão), CFNetworkHandler ou NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011: TLSProvider desconhecido ' {0}.  Os valores válidos são default ou appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012: Somente primeiro {0} de \\{1 \\} "referenciado por" avisos apresentados. * * Esta mensagem relacionada a 2009 * *

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013: Falha ao resolver a referência a "{0}", referenciada na "\\{1 \\}". O aplicativo não incluirá o assembly referenciado e pode falhar em tempo de execução.

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014: Xamarin.Mac extensões não dão suporte a vinculação. A solicitação de vinculação será ignorada. * * Esta mensagem está obsoleta em XM 3.6 + * *

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016: TlsProvider inválido `{0}` opção. O único valor válido `{1}` será usado.

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017: Não foi possível processar a descrição de XML: {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x: Falha de associação otimizador processamento `...`.

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100: Xamarin.Mac clássico API não oferece suporte a vinculação de plataforma.

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103: Assembly de processamento de erro '\*': *

Um erro inesperado ocorreu durante o processamento de um assembly.

O assembly que está causando o problema é denominado na mensagem de erro. Para corrigir esse problema o assembly precisa ser fornecido em um [relatório de bugs](https://bugzilla.xamarin.com) juntamente com um log de compilação completo com detalhamento habilitado (ou seja, `-v -v -v -v` no **argumentos adicionais mtouch**).

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104: Não é possível vincular o assembly '{0}' porque é o modo misto.

Assemblies de modo misto não podem ser processados pelo vinculador.

Consulte https://msdn.microsoft.com/en-us/library/x0w2664k.aspx para obter mais informações sobre assemblies de modo misto.

## <a name="mm3xxx-aot"></a>MM3xxx: AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx: Erros AOT (geral)

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001: Foi AOT o assembly '{0}'

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009: AOT do '{0}' foi solicitado, mas não foi encontrado

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010: Exclusão de AOT do '{0}' foi solicitado, mas não foi encontrado

## <a name="mm4xxx-code-generation"></a>MM4xxx: geração de código

### <a name="mm40xx-driverm"></a>MM40xx: driver.m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001: O modelo principal não pode ser expandido para `{0}`.

### <a name="mm41xx-registrar"></a>MM41xx: registrador

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134: O aplicativo está usando a estrutura '{0}', que não está incluída no SDK do MacOS que você está usando para criar seu aplicativo (essa estrutura foi introduzida em 2} OSX, enquanto você estiver criando com \\{1 \\} MacOS SDK). Essa configuração não é compatível com o registrador estático (pass - registrador: dinâmico como um argumento mmp adicionais na opção de Mac de compilação do projeto para selecionar). Como alternativa, selecione um SDK mais recente nas opções de Mac de compilação do seu aplicativo.

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx: GCC e ferramentas

### <a name="mm51xx-compilation"></a>MM51xx: compilação

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101: Faltando '{0}' compilador. Instale o Xcode componente 'Ferramentas de linha de comando'.

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103: Falha ao compilar. Código de erro - {0}. Envie um relatório de erros em http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx: vinculando

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202: Mono.framework MDK está ausente. Instale o MDK para sua versão Mono.framework do http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203: Não é possível localizar libxammac.a, provavelmente devido a uma instalação corrompida do Xamarin.Mac. Reinstale o Xamarin.Mac.

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x8664-is-only-supported-on-non-classic-profiles"></a>MM5204: Arquitetura inválida. Somente há suporte para x86_64 perfis não clássico.

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x8664-when---profilemobile"></a>MM5205: Arquitetura inválida '{0}'. Arquiteturas válidas são i386 e x86_64 (quando - perfil = móvel).

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218: Não é possível ignorar o símbolo dinâmico {símbolo} (– símbolo ignorar de dinâmico = {símbolo}) porque não foi detectada como um símbolo dinâmico.

Consulte o [mtouch equivalente aviso](~/ios/troubleshooting/mtouch-errors.md#MT5218).

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

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301: não foi possível encontrar a configuração do pacote. Instale o Mono.framework de http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305: Ferramenta de 'otool' ausente. Instale o Xcode componente 'Ferramentas de linha de comando'

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306: Dependências ausentes. Instale o Xcode componente 'Ferramentas de linha de comando'

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308: Contrato de licença do Xcode pode não foi aceito.  Inicialize o Xcode.

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1--check-build-log-for-details"></a>MM5309: Vinculando nativo falhou com código de erro 1.  Verifique o log de compilação para obter detalhes.

<a name="MM5310" />

#### <a name="mm5310-installnametool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310: install_name_tool falha com um código de erro '{0}'. Verifique o log de compilação para obter detalhes.

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

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017: Não há suporte para o coletor de lixo Boehm. Use SGen.

