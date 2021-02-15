---
title: Perguntas frequentes do Xamarin. iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 5e258f350256945c7794ee67f814d30d09894c9a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432179"
---
# <a name="ios-frequently-asked-questions"></a>Perguntas frequentes sobre o iOS

## <a name="general-questions"></a>Perguntas gerais

### <a name="can-i-use-a-mac-vm-with-xamarin"></a>[Posso usar uma VM de Mac com o Xamarin?](mac-vm.md)
Sim, mas somente no hardware do Mac.

### <a name="how-can-i-downgrade-xcode"></a>[Como posso fazer downgrade do Xcode?](./previous-xcode.md)
Este guia fornece links para acessar versões anteriores do Xcode, bem como a versão mais recente.

### <a name="where-can-i-set-my-ios-sdk-locations"></a>[Onde posso configurar os locais de SDK de meu iOS?](ios-sdk.md)
Para a maioria dos usuários, eles são definidos para os locais apropriados automaticamente. Este guia lista os locais do SDK padrão e como alterá-los, se necessário.

### <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>[Como posso reabilitar as opções de desenvolvedor depois de atualizar o iOS?](update-developer-options.md)
Um bug do iOS pode fazer com que as opções do desenvolvedor desapareçam depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Este guia descreve como as opções podem ser reabilitadas.

### <a name="user-location-not-working-in-ios-8"></a>[O local do usuário não está funcionando no iOS 8](ios8-user-location.md)
Este guia mostra como editar o info. plist para habilitar o local do usuário no iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>[Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?](symbolicate-ios-crash.md)
Este guia descreve as etapas básicas para os logs de falha do symbolicating iOS para ajudar no diagnóstico de falhas. Ele também fornece links para recursos adicionais para técnicas de simbólicação mais avançadas & informações sobre como interpretar os logs de falha do iOS.

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>[Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?](xs-mono-runtime.md)
Se você precisar definir qualquer variável de ambiente de tempo de execução para mono, elas poderão ser definidas nas **Opções de projeto > executar > página Geral** .

## <a name="publishing-questions"></a>Perguntas de publicação

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>[Erro ao enviar para a loja de aplicativos: "as opções de agrupamento inválidas que não podem ser inseridas em BitCode são detectadas no envio"](invalid-bundle-bitcode.md)

O envio de aplicativos que _exigem_ BitCode, como aplicativos WatchOS e tvOS, deve ser feito com o Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-file"></a>[Posso alterar o caminho de saída do arquivo IPA?](ipa-output-path.md)
A partir do Xamarin Cycle 7, você pode usar destinos do MSBuild personalizados para conseguir isso.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>[Como copiar arquivos de saída do IPA para a pasta de destino do TFS?](ipa-tfs.md)
Sim, este guia descreve como.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>[Posso adicionar arquivos ou remover arquivos de um arquivo IPA depois de criá-los no Visual Studio?](modify-ipa.md)
Sim, é possível, mas geralmente exigirá que você assine novamente o `.app` pacote depois de fazer a alteração. Observe que a modificação do `.ipa` arquivo não é necessária no uso normal. Este artigo é fornecido puramente para fins informativos.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>[É possível criar um arquivo. xcarchive do Visual Studio?](create-xcarchive.md)
A partir do Xamarin 4, agora é possível criar um `.xcarchive` do Windows definindo a `ArchiveOnBuild` propriedade como `true` .

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>[Por que o envio do meu aplicativo falha com: "caminhos não permitidos (" iTunesMetadata. plist ") encontrados em..."?](itunesmetadata-disallowed-paths.md)
Esse erro é o resultado de uma alteração no processo de verificação da loja de aplicativos da Apple. Esse erro específico _não_ está relacionado à versão específica do Xamarin que você instalou, portanto, o downgrade _não_ ajudará. Este guia fornece links para mais informações sobre como corrigir o problema.

## <a name="diagnosing-specific-error-messages"></a>Diagnosticando mensagens de erro específicas

### <a name="ios-designer-error-with-registerserviceport"></a>[Erro de iOS Designer com RegisterServicePort](error-registerserviceport.md)
Erros com `RegisterServicePort` e mensagens de erro semelhantes, como acima, são geralmente um problema com spyware/malware no computador. Este guia fornece detalhes sobre como confirmar o diagnóstico e as informações sobre como remover o spyware/malware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>[Por que meu Build do iOS falha com: nenhuma chave de assinatura de código do iPhone válida encontrada no conjunto de chaves?](no-codesigning-keys.md)
Essa mensagem de erro ocorre quando o projeto em questão está procurando credenciais de assinatura de código válidas, mas não consegue encontrá-las. A assinatura de código é necessária para testes e implantações em dispositivos iOS físicos; assim como as compilações ad hoc & App Store.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>[Por que meu aplicativo iOS 9 falha com: System. Exception: falha ao realizar marshaling do objeto Objective-C?](exception-marshal-obj-c.md)
As alterações de API no iOS 9 exigem que um construtor de retorno de chamada seja usado ao chamar código não gerenciado, pois a API subjacente agora o espera.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>[Erro de tempo de execução: o mscorlib.dll do assembly não foi encontrado ou não pôde ser carregado](error-mscorlib-not-found.md)
Esse problema ocorre quando as pastas e *ocultas* `.monotouch-32` `.monotouch-64` estão ausentes na `.xcarchive` criação de assinatura/IPA, disparando o erro de tempo de execução.

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>[Erro de compilação: não é possível codificar o deslocamento X na realocação de dispersão resultante](error-encode-offset-scattered-relocation.md)
Esse problema ocorre durante a compilação de arquiteturas de 32 bits, como ARMv7, quando o binário final é muito grande para o ferramentas nativo.

## <a name="deprecated"></a>Preterido

> [!IMPORTANT]
> Os artigos a seguir se aplicam a problemas que foram resolvidos em versões recentes do Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

### <a name="ipa-file-is-0-bytes"></a>[O arquivo IPA tem 0 bytes](ipa-zero-bytes.md)
Houve alguns problemas conhecidos em versões anteriores do Xamarin que poderiam fazer com que o arquivo IPA no Windows fosse de 0 bytes.

### <a name="ibtool-error-the-operation-couldnt-be-completed"></a>[Erro de IBTool: não foi possível concluir a operação.](error-ibtool.md)
A Apple corrigiu esse `ibtool` bug no Xcode 6.1.1, portanto, atualizar para o Xcode 6.1.1 ou superior é a correção mais fácil.

### <a name="error-mt1009-could-not-copy-the-assembly"></a>[Erro MT1009: não foi possível copiar o assembly](error-mt1009.md)
Isso afeta os usuários que executam o Xamarin. iOS 7.2.6. Esse problema ocorre devido a permissões de arquivo que precisam de privilégios mais altos quando o Xamarin. iOS é instalado com uma conta de usuário diferente, em seguida, a conta principal do desenvolvedor.

### <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>[System.Exception AMDeviceNotificationSubscribe retornou ...](exception-amddevicenotificationsubscribe.md)
Essa mensagem pode aparecer em uma caixa de diálogo de erro quando você inicia pela primeira vez Visual Studio para Mac ou no `mtbserver.log` arquivo. Observe que esse é um problema incomum. Se o Visual Studio estiver tendo problemas para se conectar ao host de Build do Mac, haverá outros erros que têm mais probabilidade de aparecer no `mtbserver.log` arquivo.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>[MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Esse erro pode aparecer no `Mac Server Log` Visual Studio.