---
title: Perguntas frequentes sobre o xamarin. IOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 8b90b76cad5277fe76fc476a0bcd6f600e91b256
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421812"
---
# <a name="ios-frequently-asked-questions"></a>Perguntas frequentes do iOS

## <a name="general-questions"></a>Perguntas gerais

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[Posso usar uma VM de Mac com o Xamarin?](mac-vm.md)
Sim, mas somente no hardware do Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Como posso fazer downgrade do Xcode?](downgrade-xcode.md)
Este guia fornece links para acessar as versões anteriores do Xcode, bem como a versão mais recente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Onde posso configurar os locais de SDK de meu iOS?](ios-sdk.md)
Para a maioria dos usuários elas são definidas automaticamente para os locais apropriados. Este guia lista os locais de SDK padrão e como alterá-las se necessário.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Como é possível reabilitar as opções do desenvolvedor após a atualização do iOS?](update-developer-options.md)
Um erro de iOS pode causar as opções do desenvolvedor desaparecer depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Este guia descreve como as opções podem ser habilitados novamente.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[O local do usuário não está funcionando no iOS 8](ios8-user-location.md)
Este guia lhe mostra como editar Info. plist para habilitar o local do usuário no iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?](symbolicate-ios-crash.md)
Este guia descreve as etapas básicas para symbolicating logs de falha do iOS para ajudar a diagnosticar falhas. Ele também fornece links para recursos adicionais para mais avançadas técnicas simbolização & informações sobre como interpretar os logs de falha do iOS.


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?](xs-mono-runtime.md)
Se você precisar definir variáveis de ambiente de qualquer tempo de execução para o Mono, eles podem ser definidos **opções de projeto > Executar > geral** página.

## <a name="publishing-questions"></a>Perguntas de publicação

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Erro ao enviar para a App Store: "O pacote inválido – as opções não podem ser inseridas no bitcode são detectadas no envio"](invalid-bundle-bitcode.md)

Enviar aplicativos que _exigem_ bitcode, como aplicativos watchOS e tvOS, deve ser feito com o Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[Posso alterar o caminho de saída do arquivo IPA?](ipa-output-path.md)
A partir de 7 de ciclo de Xamarin, você pode usar destinos personalizados do MSBuild para fazer isso.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Como copiar arquivos de saída de IPA para a pasta-depósito TFS?](ipa-tfs.md)
Sim, este guia descreve como.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[Pode adicionar ou remover arquivos de um arquivo de IPA após compilá-lo no Visual Studio?](modify-ipa.md)
Sim, é possível, mas ele normalmente exigirão que você assine novamente o `.app` pacote depois de fazer a alteração. Observe que a modificação de `.ipa` arquivo não é necessário o uso normal. Este artigo é fornecido meramente para fins informativos.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[É possível criar um arquivo. xcarchive no Visual Studio?](create-xcarchive.md)
A partir do Xamarin 4, é possível criar uma `.xcarchive` do Windows, definindo o `ArchiveOnBuild` propriedade `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Por que o envio do meu aplicativo falha com: "Disallowed paths ( "iTunesMetadata.plist" ) found at ..." ?](itunesmetadata-disallowed-paths.md)
Esse erro é o resultado de uma alteração no processo de verificação da Apple App Store. Esse erro específico é _não_ relacionadas à versão particular do Xamarin que você instalou, portanto, fazer o downgrade serão _não_ ajuda. Links neste guia para obter mais informações sobre como corrigir o problema.


## <a name="diagnosing-specific-error-messages"></a>Diagnosticando mensagens de erro específicas

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Erro de iOS Designer com RegisterServicePort](error-registerserviceport.md)
Erros com `RegisterServicePort` e mensagens de erro semelhantes, como acima são normalmente um problema com spyware/malware no computador. Este guia detalha confirmando o diagnóstico e informações sobre como remover malware/spyware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Por que minha compilação do iOS falha com: Nenhuma chave de assinatura de código do iPhone foi encontrada no conjunto de chaves?](no-codesigning-keys.md)
Essa mensagem de erro ocorre quando o projeto em questão está procurando por credenciais válidas de assinatura de código, mas não conseguir encontrá-los. Assinatura de código é necessária para testes e implantações em dispositivos iOS físicos; Assim como Ad-hoc & aplicativo armazenam compilações.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Por que meu aplicativo iOS 9 falha com: System.Exception: Failed to marshal the Objective-C object?](exception-marshal-obj-c.md)
Alterações de API no iOS 9 exigem que um construtor de retorno de chamada usado ao chamar código não gerenciado, como a API básica agora espera que ele.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Erro de tempo de execução: The assembly mscorlib.dll was not found or could not be loaded](error-mscorlib-not-found.md)
Esse problema ocorre quando o *ocultos* `.monotouch-32` e `.monotouch-64` pastas estão ausentes do `.xcarchive` para assinar / criação de IPA, aciona o erro de tempo de execução.

## <a name="deprecated"></a>Preterido

> [!IMPORTANT]
> Os artigos a seguir se aplicam a problemas que foram resolvidos em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[O arquivo IPA tem 0 bytes](ipa-zero-bytes.md)
Havia alguns problemas conhecidos em versões anteriores do Xamarin que pode fazer com que o arquivo de IPA no Windows para ser de 0 bytes.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Erro de IBTool: The operation couldn’t be completed.](error-ibtool.md)
Apple [corrigido](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) isso `ibtool` bug no Xcode 6.1.1, portanto, a atualização para o Xcode 6.1.1 ou superior é a correção mais fácil.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Erro de MT1009: Could not copy the assembly](error-mt1009.md)
Isso afeta os usuários que executam o xamarin. IOS 7.2.6. Esse problema é devido à necessidade de privilégios mais altos quando o xamarin. IOS é instalado com uma conta de usuário diferente de permissões de arquivo, em seguida, conta de principal do desenvolvedor.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe retornou ...](exception-amddevicenotificationsubscribe.md)
Essa mensagem pode aparecer em uma caixa de diálogo de erro quando você começa a Visual Studio para Mac ou no `mtbserver.log` arquivo. Observe que esse é um problema incomum. Se o Visual Studio está tendo problemas para se conectar ao host de build do Mac, há outros erros que mais provavelmente aparecerão no `mtbserver.log` arquivo.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Esse erro pode aparecer no `Mac Server Log` no Visual Studio.
