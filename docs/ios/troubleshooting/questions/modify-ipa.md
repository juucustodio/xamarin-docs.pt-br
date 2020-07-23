---
title: Posso adicionar arquivos ou remover arquivos de um arquivo IPA depois de criá-los no Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: e3b535b8624f03e9c832f1719237409cb9ff26c2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939900"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Posso adicionar arquivos ou remover arquivos de um arquivo IPA depois de criá-los no Visual Studio?

Sim, é possível, mas geralmente exigirá que você assine novamente o `.app` pacote depois de fazer a alteração.

Observe que a modificação do `.ipa` arquivo não é necessária no uso normal. Este artigo é fornecido puramente para fins informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Exemplo: removendo um arquivo de um `.ipa` arquivamento

Para este exemplo, suponha que o nome do projeto Xamarin. iOS é `iPhoneApp1` e o `generated session id` é`cc530d20d6b19da63f6f1c6f67a0a254`

1. Compile o `.ipa` arquivo normalmente no Visual Studio.

2. Alterne para o host de Build do Mac.

3. Localize a compilação na `~/Library/Caches/Xamarin/mtbs/builds` pasta. Você pode colar esse caminho no **finder > ir > ir para a pasta** para procurar a pasta no localizador. Procure a pasta que corresponde ao nome do projeto. Dentro dessa pasta, procure a pasta que corresponde ao `generated session id` da compilação. Isso provavelmente será a subpasta que tem a hora de modificação mais recente.

4. Abra uma nova `Terminal.app` janela.

5. Digite `cd` na janela terminal. app e, em seguida, arraste & solte a `generated session id` pasta na `Terminal.app` janela:

    ![Localizando a pasta de ID de sessão gerada no localizador](modify-ipa-images/session-id-folder.png)

6. Digite a chave de retorno para alterar o diretório na `generated session id` pasta.

7. Descompacte o `.ipa` arquivo em uma `old/` pasta temporária usando o comando a seguir. Ajuste os `Ad-Hoc` `iPhoneApp1` nomes e conforme necessário para seu projeto específico.

    > Idem-XK bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. ipa Old/

8. Mantenha a `Terminal.app` janela aberta.

9. Exclua os arquivos desejados do `.ipa` . Você pode movê-los para a lixeira usando o Finder ou excluí-los na linha de comando usando `Terminal.app` . Para exibir o conteúdo do `Payload/iPhone` arquivo no Finder, clique no arquivo e selecione **Mostrar conteúdo do pacote**.

10. Usando a mesma abordagem geral da etapa 3, localize o arquivo de log `~/Library/Logs/Xamarin/MonoTouchVS/` que tem o nome do projeto e o `generated session id` no nome: ![ localize o log de compilação do projeto no Finder](modify-ipa-images/build-log.png)

11. Abra o log de compilação da etapa 10, por exemplo, clicando duas vezes nele.

12. Localize a linha que inclui `tool /usr/bin/codesign execution started with arguments: -v --force --sign` .

13. Digite `/usr/bin/codesign` na janela terminal. app da etapa 8.

14. Copie todos os argumentos começando com `-v` da linha na etapa 12 e cole-os na janela terminal. app.

15. Altere o último argumento para o `.app` pacote localizado na `old/Payload/` pasta e execute o comando.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Altere para o `old/` diretório no terminal:

    ```bash
    cd old
    ```

17. Compacte o conteúdo do diretório em um novo `.ipa` arquivo usando o `zip` comando. Você pode alterar o `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argumento para gerar o `.ipa` arquivo sempre que desejar:

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Mensagens de erro comuns

Se você vir `Invalid Signature. A sealed resource is missing or invalid.` , isso geralmente significa que algo foi alterado no `.app` pacote e que o `.app` pacote não foi assinado corretamente depois. Observe também que, se você quiser criar um `.ipa` com um perfil de distribuição, _deverá_ criar o original `.ipa` com um perfil de distribuição. Caso contrário, o `Entitlements.xcent` estará incorreto.

Para dar um exemplo concreto de como esse erro pode surgir, se você executar o comando a seguir `codesign --verify` na janela do terminal após a etapa 9, verá o erro junto com a causa exata do erro:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E o processo de verificação da loja de aplicativos relatará uma mensagem de erro semelhante:

> ERRO ITMS-90035: "assinatura inválida. Um recurso lacrado está ausente ou é inválido. O binário no caminho [iPhoneApp1. app/iPhoneApp1] contém uma assinatura inválida. Certifique-se de que você assinou seu aplicativo com um certificado de distribuição, não um certificado ad hoc ou um certificado de desenvolvimento. Verifique se as configurações de assinatura de código no Xcode estão corretas no nível de destino (que substituem quaisquer valores no nível do projeto). Além disso, certifique-se de que o pacote que você está carregando foi criado usando um destino de versão no Xcode, não um destino de simulador. Se você tiver certeza de que suas configurações de assinatura de código estão corretas, escolha "limpar tudo" no Xcode, exclua o diretório "Build" no localizador e recompile o destino da versão. Para obter mais informações, consulte [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html) "
