---
title: Perguntas frequentes do Xamarin. iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: e79fca8c59ae49d27cd335106ca57945be106031
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031080"
---
# <a name="ios-frequently-asked-questions"></a>Perguntas frequentes sobre o iOS

## <a name="general-questions"></a>Perguntas gerais

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[Posso usar uma VM de Mac com o Xamarin?](mac-vm.md)
Sim, mas somente no hardware do Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Como posso fazer downgrade do Xcode?](downgrade-xcode.md)
Este guia fornece links para acessar versões anteriores do Xcode, bem como a versão mais recente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Onde posso configurar os locais de SDK de meu iOS?](ios-sdk.md)
Para a maioria dos usuários, eles são definidos para os locais apropriados automaticamente. Este guia lista os locais do SDK padrão e como alterá-los, se necessário.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Como posso reabilitar as opções de desenvolvedor depois de atualizar o iOS?](update-developer-options.md)
Um bug do iOS pode fazer com que as opções do desenvolvedor desapareçam depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Este guia descreve como as opções podem ser reabilitadas.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[O local do usuário não está funcionando no iOS 8](ios8-user-location.md)
Este guia mostra como editar o info. plist para habilitar o local do usuário no iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?](symbolicate-ios-crash.md)
Este guia descreve as etapas básicas para os logs de falha do symbolicating iOS para ajudar no diagnóstico de falhas. Ele também fornece links para recursos adicionais para técnicas de simbólicação mais avançadas & informações sobre como interpretar os logs de falha do iOS.

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?](xs-mono-runtime.md)
Se você precisar definir qualquer variável de ambiente de tempo de execução para mono, elas poderão ser definidas nas **Opções de projeto > executar > página Geral** .

## <a name="publishing-questions"></a>Perguntas de publicação

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Erro ao enviar para a loja de aplicativos: "as opções de agrupamento inválidas que não podem ser inseridas em BitCode são detectadas no envio"](invalid-bundle-bitcode.md)

O envio de aplicativos que _exigem_ BitCode, como aplicativos WatchOS e tvOS, deve ser feito com o Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[Posso alterar o caminho de saída do arquivo IPA?](ipa-output-path.md)
A partir do Xamarin Cycle 7, você pode usar destinos do MSBuild personalizados para conseguir isso.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Como copiar arquivos de saída do IPA para a pasta de destino do TFS?](ipa-tfs.md)
Sim, este guia descreve como.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[Posso adicionar arquivos ou remover arquivos de um arquivo IPA depois de criá-los no Visual Studio?](modify-ipa.md)
Sim, é possível, mas geralmente exigirá que você assine novamente o grupo de `.app` depois de fazer a alteração. Observe que a modificação do arquivo de `.ipa` não é necessária no uso normal. Este artigo é fornecido puramente para fins informativos.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[É possível criar um arquivo. xcarchive do Visual Studio?](create-xcarchive.md)
A partir do Xamarin 4, agora é possível criar uma `.xcarchive` do Windows, definindo a propriedade `ArchiveOnBuild` como `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Por que meu envio de aplicativo falha com: "Caminhos não permitidos ("iTunesMetadata.plist") encontrados em..."?](itunesmetadata-disallowed-paths.md)
Esse erro é o resultado de uma alteração no processo de verificação da loja de aplicativos da Apple. Esse erro específico _não_ está relacionado à versão específica do Xamarin que você instalou, portanto, o downgrade _não_ ajudará. Este guia fornece links para mais informações sobre como corrigir o problema.

## <a name="diagnosing-specific-error-messages"></a>Diagnosticando mensagens de erro específicas

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Erro de iOS Designer com RegisterServicePort](error-registerserviceport.md)
Erros com `RegisterServicePort` e mensagens de erro semelhantes, como acima, são geralmente um problema com spyware/malware no computador. Este guia fornece detalhes sobre como confirmar o diagnóstico e as informações sobre como remover o spyware/malware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Por que minha compilação do iOS falha com: Nenhuma chave de assinatura de código do iPhone foi encontrada no conjunto de chaves?](no-codesigning-keys.md)
Essa mensagem de erro ocorre quando o projeto em questão está procurando credenciais de assinatura de código válidas, mas não consegue encontrá-las. A assinatura de código é necessária para testes e implantações em dispositivos iOS físicos; assim como as compilações ad hoc & App Store.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Por que meu aplicativo do iOS 9 falha com: System.Exception: falha ao realizar marshal do objeto Objective-C?](exception-marshal-obj-c.md)
As alterações de API no iOS 9 exigem que um construtor de retorno de chamada seja usado ao chamar código não gerenciado, pois a API subjacente agora o espera.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Erro de tempo de execução: o assembly mscorlib.dll não foi encontrado ou não foi possível carregá-lo](error-mscorlib-not-found.md)
Esse problema ocorre quando as pastas *ocultas* `.monotouch-32` e `.monotouch-64` estão faltando na `.xcarchive` para a criação de assinatura/IPA, disparando o erro de tempo de execução.

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocationerror-encode-offset-scattered-relocationmd"></a>[Erro de compilação: não é possível codificar o deslocamento X na realocação de dispersão resultante](error-encode-offset-scattered-relocation.md)
Esse problema ocorre durante a compilação de arquiteturas de 32 bits, como ARMv7, quando o binário final é muito grande para o ferramentas nativo.

## <a name="deprecated"></a>Preterido

> [!IMPORTANT]
> Os artigos a seguir se aplicam a problemas que foram resolvidos em versões recentes do Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[O arquivo IPA tem 0 bytes](ipa-zero-bytes.md)
Houve alguns problemas conhecidos em versões anteriores do Xamarin que poderiam fazer com que o arquivo IPA no Windows fosse de 0 bytes.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Erro IBTool: Não foi possível concluir a operação.](error-ibtool.md)
A Apple [corrigiu](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) esse `ibtool` bug no Xcode 6.1.1, portanto, a atualização para o Xcode 6.1.1 ou superior é a correção mais fácil.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Erro MT1009: Não foi possível copiar o assembly](error-mt1009.md)
Isso afeta os usuários que executam o Xamarin. iOS 7.2.6. Esse problema ocorre devido a permissões de arquivo que precisam de privilégios mais altos quando o Xamarin. iOS é instalado com uma conta de usuário diferente, em seguida, a conta principal do desenvolvedor.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe retornou ...](exception-amddevicenotificationsubscribe.md)
Essa mensagem pode aparecer em uma caixa de diálogo de erro quando você inicia pela primeira vez Visual Studio para Mac ou no arquivo `mtbserver.log`. Observe que esse é um problema incomum. Se o Visual Studio estiver tendo problemas para se conectar ao host de Build do Mac, haverá outros erros que têm mais probabilidade de aparecer no arquivo de `mtbserver.log`.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Esse erro pode aparecer no `Mac Server Log` no Visual Studio.
