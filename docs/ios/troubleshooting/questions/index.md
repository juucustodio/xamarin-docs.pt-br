---
title: Perguntas frequentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: f8264c48b3b4679d45d5603b5637df0016cd3d17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

## <a name="general-questions"></a>Perguntas gerais

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[Posso usar uma VM de Mac com o Xamarin?](mac-vm.md)
Sim, mas somente no hardware do Mac.

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[Como posso fazer downgrade do Xcode?](downgrade-xcode.md)
Este guia fornece links para acessar as versões anteriores do Xcode, bem como a versão mais recente.

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[Onde posso configurar os locais de SDK de meu iOS?](ios-sdk.md)
Para a maioria dos usuários elas são definidas automaticamente para os locais apropriados. Este guia lista os locais padrão de SDK e como alterá-las se necessário.

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[Como é possível reabilitar opções do desenvolvedor após a atualização de iOS?](update-developer-options.md)
Um erro de iOS pode fazer com que as opções de desenvolvedor para desaparecer depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Este guia descreve como as opções podem ser habilitados novamente.

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[O local do usuário não está funcionando no iOS 8](ios8-user-location.md)
Este guia explica como editar Info. plist para habilitar o usuário local no iOS 8.

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?](symbolicate-ios-crash.md)
Este guia descreve as etapas básicas para symbolicating logs de falha do iOS para ajudar a diagnosticar falhas. Também contém links para recursos adicionais para mais avançadas técnicas symbolication & informações sobre como interpretar os logs de falha do iOS.


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[Como definir variáveis de ambiente do Mono Runtime para projetos do iOS no Xamarin Studio?](xs-mono-runtime.md)
Se você precisa definir variáveis de ambiente de qualquer tempo de execução para Mono, eles podem ser definidos **opções de projeto > Executar > geral** página.

## <a name="publishing-questions"></a>Perguntas de publicação

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[Erro ao enviar ao repositório de aplicativo: "O pacote inválido - opções não podem ser inseridas em bitcode forem detectadas no envio"](invalid-bundle-bitcode.md)

Envio de aplicativos que _exigem_ bitcode, como aplicativos watchOS e tvOS, deve ser feito com o Xcode 9.

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[Alterar o caminho de saída do arquivo IPA?](ipa-output-path.md)
A partir de 7 de ciclo de Xamarin, você pode usar personalizados destinos do MSBuild para fazer isso.

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[Como copiar arquivos de saída de IPA à pasta-depósito TFS?](ipa-tfs.md)
Sim, este guia descreve como.

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[Pode adicionar ou remover arquivos de um arquivo de IPA depois de criá-lo no Visual Studio?](modify-ipa.md)
Sim, é possível, mas geralmente será necessário assinar novamente o `.app` pacote após fazer a alteração. Observe que a modificação de `.ipa` arquivo não é necessário no uso normal. Este artigo é fornecido apenas para fins informativos.

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[É possível criar um arquivo de .xcarchive do Visual Studio?](create-xcarchive.md)
A partir do Xamarin 4, é possível criar um `.xcarchive` do Windows, definindo o `ArchiveOnBuild` propriedade `true`.

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[Por que meu envio de aplicativo falha com: "Caminhos não permitidos ("iTunesMetadata.plist") encontrados em..."?](itunesmetadata-disallowed-paths.md)
Esse erro é o resultado de uma alteração no processo de verificação da Apple App Store. Esse erro específico é _não_ relacionadas à versão particular do Xamarin você instalou, portanto downgrade será _não_ ajuda. Links neste guia para obter mais informações sobre como corrigir o problema.


## <a name="diagnosing-specific-error-messages"></a>Diagnosticando mensagens de erro específicas

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[Erro de iOS Designer com RegisterServicePort](error-registerserviceport.md)
Erros com `RegisterServicePort` e mensagens de erro semelhantes como acima geralmente são um problema com spyware/malware no computador. Esses detalhes de guia confirmando o diagnóstico e informações sobre a remoção de malware/spyware.

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[Por que minha compilação do iOS falha com: Nenhuma chave de assinatura de código do iPhone foi encontrada no conjunto de chaves?](no-codesigning-keys.md)
Essa mensagem de erro ocorre quando o projeto em questão está procurando por credenciais de assinatura de código válidas, mas não conseguir encontrá-los. Assinatura de código é necessária para testes e implantações em dispositivos iOS físicos; Assim como Ad-hoc & aplicativo armazenam compilações.

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[Por que meu aplicativo do iOS 9 falha com: System.Exception: falha ao realizar marshal do objeto Objective-C?](exception-marshal-obj-c.md)
Alterações de API no iOS 9 requerem que um construtor de retorno de chamada ser usado ao chamar código não gerenciado, como a API subjacente agora espera.

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[Erro de tempo de execução: o assembly mscorlib.dll não foi encontrado ou não foi possível carregá-lo](error-mscorlib-not-found.md)
Esse problema ocorre quando o *oculta* `.monotouch-32` e `.monotouch-64` pastas estão ausentes do `.xcarchive` para assinar / criação de IPA, aciona o erro de tempo de execução.

## <a name="deprecated"></a>Preterido

> [!IMPORTANT]
> Os artigos a seguir se aplicam a problemas que foram resolvidos em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[O arquivo IPA tem 0 bytes](ipa-zero-bytes.md)
Ocorreram alguns problemas conhecidos em versões anteriores do Xamarin que pode fazer com que o arquivo de IPA no Windows para ter 0 bytes.

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[Erro IBTool: Não foi possível concluir a operação.](error-ibtool.md)
Apple [fixa](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html) isso `ibtool` bug no Xcode 6.1.1, para atualizar para o Xcode 6.1.1 ou superior é a mais fácil.

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[Erro MT1009: Não foi possível copiar o assembly](error-mt1009.md)
Isso afeta os usuários que executam o xamarin 7.2.6. Esse problema ocorre devido a permissões de arquivos que precisam de privilégios mais altos quando xamarin é instalado com uma conta de usuário diferente, em seguida, conta principal do desenvolvedor.

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe retornou ...](exception-amddevicenotificationsubscribe.md)
Essa mensagem pode aparecer em uma caixa de diálogo de erro ao iniciar o Visual Studio para Mac, ou no `mtbserver.log` arquivo. Observe que esse é um problema comum. Se o Visual Studio está tendo problemas para se conectar ao host de compilação de Mac, há outros erros que têm mais probabilidade de aparecer no `mtbserver.log` arquivo.

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe não encontrado rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
Esse erro pode aparecer no `Mac Server Log` no Visual Studio.
