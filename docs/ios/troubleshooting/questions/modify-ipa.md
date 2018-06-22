---
title: Pode adicionar ou remover arquivos de um arquivo de IPA depois de criá-lo no Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b8b61ba38491b2085233dd1b30a82bc57d2baaed
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777925"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Pode adicionar ou remover arquivos de um arquivo de IPA depois de criá-lo no Visual Studio?

Sim, é possível, mas geralmente será necessário assinar novamente o `.app` pacote após fazer a alteração.

Observe que a modificação de `.ipa` arquivo não é necessário no uso normal. Este artigo é fornecido apenas para fins informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Exemplo: remover um arquivo de um `.ipa` arquivamento

Para este exemplo, suponha que é o nome do projeto xamarin `iPhoneApp1` e `generated session id` é `cc530d20d6b19da63f6f1c6f67a0a254`

1.  Criar o `.ipa` arquivo como normal do Visual Studio.

2.  Alternar para o host de compilação de Mac.

3.  Localizar a compilação no `~/Library/Caches/Xamarin/mtbs/builds` pasta. Você pode colar esse caminho em **localizador > Ir > vá para a pasta** para procurar a pasta no localizador. Procure a pasta que corresponde ao nome do projeto. Dentro dessa pasta, procure a pasta que corresponde a `generated session id` da compilação. Isso provavelmente será a subpasta que tem o tempo de modificação mais recente.

4.  Abra uma nova `Terminal.app` janela.

5.  Tipo `cd ` na janela Terminal.app e, em seguida, arrastar e soltar o `generated session id` pasta para o `Terminal.app` janela:

    ![](modify-ipa-images/session-id-folder.png "Localizando a pasta de id de sessão gerado no localizador")

6.  Digite a chave de retorno para alterar o diretório para o `generated session id` pasta.

7.  Descompacte o `.ipa` arquivo para um temporário `old/` pasta usando o comando a seguir. Ajustar o `Ad-Hoc` e `iPhoneApp1` nomes conforme necessário para seu projeto específico.

    > idem - xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa antigo /

8.  Manter o `Terminal.app` janela aberta.

9.  Excluir os arquivos desejados do `.ipa`. Você pode movê-los para a Lixeira usando o localizador ou excluí-las na linha de comando usando `Terminal.app`. Para exibir o conteúdo de `Payload/iPhone` no localizador, Control, clique o arquivo e selecione **Mostrar conteúdo do pacote**.

10.  Usando a mesma abordagem geral da etapa 3, localize o arquivo de log em `~/Library/Logs/Xamarin/MonoTouchVS/` que tem o nome do projeto e o `generated session id` no nome: ![ ] (modify-ipa-images/build-log.png "localizar o log de compilação de projeto no localizador")

11.  Abra o log de compilação da etapa 10, por exemplo clicando duas vezes nele.

12.  Localize a linha que inclui `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13.  Tipo `/usr/bin/codesign ` na janela Terminal.app da etapa 8.

14.  Copiar todos os argumentos começando com `-v` da linha na etapa 12 e colá-las na janela Terminal.app.

15.  Alterar o último argumento para ser o `.app` pacote localizado dentro de `old/Payload/` pasta e, em seguida, execute o comando.

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  Alterar para o `old/` diretório no Terminal:

```bash
cd old
```

17.  Compacte o conteúdo do diretório em uma nova `.ipa` arquivo usando o `zip` comando. Você pode alterar o `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argumento para a saída de `.ipa` sempre que você gostaria de arquivo:

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>Mensagens de erro comuns

Se você vir `Invalid Signature. A sealed resource is missing or invalid.`, que geralmente significa que algo foi alterado no `.app` pacote e que o `.app` pacote não foi corretamente novamente assinado posteriormente. Observe também que, se você deseja criar um `.ipa` com um perfil de distribuição, você _deve_ criar original `.ipa` com um perfil de distribuição. Caso contrário, o `Entitlements.xcent` estará incorreta.

Para dar um exemplo concreto de como esse erro pode ocorrer, se você executar o seguinte `codesign --verify` comando na janela do Terminal após a etapa 9, você verá o erro com a causa precisa do erro:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E o processo de verificação da App Store relatará uma mensagem de erro semelhante:

> Erro ITMS-90035: "assinatura inválida. Um recurso lacrado está ausente ou inválido. O binário no caminho [iPhoneApp1.app/iPhoneApp1] contém uma assinatura inválida. Certifique-se de que ter entrado seu aplicativo com um certificado de distribuição, e não um certificado ad hoc ou um certificado de desenvolvimento. Verifique se as configurações de assinatura de código no Xcode estão corretas no nível de destino (ou substituir os valores no nível de projeto). Além disso, verifique se o pacote que você está carregando foi criado usando um destino de versão no Xcode, não um destino do simulador. Se você tiver certeza de que as configurações de assinatura de código estão corretas, selecione "Limpar tudo" no Xcode, exclua o diretório "build" no localizador e recriar sua versão de destino. Para obter mais informações, consulte [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
