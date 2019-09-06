---
title: Posso adicionar arquivos ou remover arquivos de um arquivo IPA depois de criá-los no Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: d1546b83304d8c66f7433bd77c5ebecc9dc95aaa
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278476"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Posso adicionar arquivos ou remover arquivos de um arquivo IPA depois de criá-los no Visual Studio?

Sim, é possível, mas geralmente exigirá que você assine novamente o `.app` pacote depois de fazer a alteração.

Observe que a modificação `.ipa` do arquivo não é necessária no uso normal. Este artigo é fornecido puramente para fins informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Exemplo: removendo um arquivo de `.ipa` um arquivamento

Para este exemplo, suponha que o nome do projeto Xamarin. Ios é `iPhoneApp1` e o `generated session id` é`cc530d20d6b19da63f6f1c6f67a0a254`

1. Compile o `.ipa` arquivo normalmente no Visual Studio.

2. Alterne para o host de Build do Mac.

3. Localize a compilação na `~/Library/Caches/Xamarin/mtbs/builds` pasta. Você pode colar esse caminho no **finder > ir > ir para a pasta** para procurar a pasta no localizador. Procure a pasta que corresponde ao nome do projeto. Dentro dessa pasta, procure a pasta que corresponde ao `generated session id` da compilação. Isso provavelmente será a subpasta que tem a hora de modificação mais recente.

4. Abra uma nova `Terminal.app` janela.

5. Digite `cd` na janela terminal. app e, em seguida, arraste & solte `generated session id` a pasta na `Terminal.app` janela:

    ![](modify-ipa-images/session-id-folder.png "Localizando a pasta de ID de sessão gerada no localizador")

6. Digite a chave de retorno para alterar o `generated session id` diretório na pasta.

7. Descompacte o `.ipa` arquivo em `old/` uma pasta temporária usando o comando a seguir. Ajuste os `Ad-Hoc` nomes `iPhoneApp1` e conforme necessário para seu projeto específico.

    > ditto -xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa old/

8. Mantenha a `Terminal.app` janela aberta.

9. Exclua os arquivos desejados do `.ipa`. Você pode movê-los para a lixeira usando o Finder ou excluí-los na linha de comando `Terminal.app`usando. Para exibir o conteúdo do `Payload/iPhone` arquivo no Finder, clique no arquivo e selecione **Mostrar conteúdo do pacote**.

10. Usando a mesma abordagem geral da etapa 3, localize o arquivo `~/Library/Logs/Xamarin/MonoTouchVS/` de log que tem o nome do projeto e o `generated session id` no nome: ![](modify-ipa-images/build-log.png "Localize o log de compilação do projeto no Finder")

11. Abra o log de compilação da etapa 10, por exemplo, clicando duas vezes nele.

12. Localize a linha que inclui `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13. Digite `/usr/bin/codesign` na janela terminal. app da etapa 8.

14. Copie todos os argumentos começando com `-v` da linha na etapa 12 e cole-os na janela terminal. app.

15. Altere o último argumento para o `.app` pacote localizado `old/Payload/` na pasta e execute o comando.

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

Se você vir `Invalid Signature. A sealed resource is missing or invalid.`, isso geralmente significa que algo foi alterado `.app` no pacote e que o `.app` pacote não foi assinado corretamente depois. Observe também que, se você quiser criar um `.ipa` com um perfil de distribuição, _deverá_ criar o original `.ipa` com um perfil de distribuição. Caso contrário `Entitlements.xcent` , o estará incorreto.

Para dar um exemplo concreto de como esse erro pode surgir, se você executar o comando `codesign --verify` a seguir na janela do terminal após a etapa 9, verá o erro junto com a causa exata do erro:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E o processo de verificação da loja de aplicativos relatará uma mensagem de erro semelhante:

> ERRO ITMS-90035: "Assinatura inválida. Um recurso lacrado está ausente ou é inválido. O binário no caminho [iPhoneApp1. app/iPhoneApp1] contém uma assinatura inválida. Certifique-se de que você assinou seu aplicativo com um certificado de distribuição, não um certificado ad hoc ou um certificado de desenvolvimento. Verifique se as configurações de assinatura de código no Xcode estão corretas no nível de destino (que substituem quaisquer valores no nível do projeto). Além disso, certifique-se de que o pacote que você está carregando foi criado usando um destino de versão no Xcode, não um destino de simulador. Se você tiver certeza de que suas configurações de assinatura de código estão corretas, escolha "limpar tudo" no Xcode, exclua o diretório "Build" no localizador e recompile o destino da versão. Para obter mais informações, consulte [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
