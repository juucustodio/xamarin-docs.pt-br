---
title: Pode adicionar ou remover arquivos de um arquivo de IPA após compilá-lo no Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 366308774a7302e54b0d47753256638e89d97b82
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350976"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Pode adicionar ou remover arquivos de um arquivo de IPA após compilá-lo no Visual Studio?

Sim, é possível, mas ele normalmente exigirão que você assine novamente o `.app` pacote depois de fazer a alteração.

Observe que a modificação de `.ipa` arquivo não é necessário o uso normal. Este artigo é fornecido meramente para fins informativos.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Exemplo: remover um arquivo de um `.ipa` arquivo morto

Para este exemplo, suponha que o nome do projeto xamarin. IOS está `iPhoneApp1` e o `generated session id` é `cc530d20d6b19da63f6f1c6f67a0a254`

1.  Compilar o `.ipa` de arquivo como normal do Visual Studio.

2.  Alternar para o host de build do Mac.

3.  Encontre a compilação no `~/Library/Caches/Xamarin/mtbs/builds` pasta. Você pode colar esse caminho em **Finder > Ir > vá para a pasta** para procurar a pasta no Finder. Procure a pasta que corresponde ao nome do projeto. Dentro dessa pasta, procure a pasta que corresponde a `generated session id` da compilação. Isso provavelmente será a subpasta que tenha a hora da modificação mais recente.

4.  Abra uma nova `Terminal.app` janela.

5.  Tipo de `cd ` na janela de terminal e, em seguida, arrastar e soltar o `generated session id` pasta para o `Terminal.app` janela:

    ![](modify-ipa-images/session-id-folder.png "Localizar a pasta de id de sessão gerado no Finder")

6.  Digite a chave de retornada para alterar o diretório para o `generated session id` pasta.

7.  Descompacte o `.ipa` arquivo para um temporário `old/` pasta usando o comando a seguir. Ajustar a `Ad-Hoc` e `iPhoneApp1` nomes conforme necessário para seu projeto específico.

    > idem - xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa antigo /

8.  Manter o `Terminal.app` janela aberta.

9.  Exclua os arquivos desejados do `.ipa`. Você pode movê-los para a Lixeira usando o Finder ou excluí-las na linha de comando usando `Terminal.app`. Para exibir o conteúdo a `Payload/iPhone` o arquivo do arquivo no Finder, CTRL + clique e selecione **Mostrar conteúdo do pacote**.

10.  Usando a mesma abordagem geral, como na etapa 3, localize o arquivo de log sob `~/Library/Logs/Xamarin/MonoTouchVS/` que tem o nome do projeto e o `generated session id` no nome: ![ ] (modify-ipa-images/build-log.png "localizar o log de build do projeto no Finder")

11.  Abra o log de compilação da etapa 10, por exemplo clicando duas vezes nele.

12.  Localize a linha que inclui `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13.  Tipo `/usr/bin/codesign ` na janela de terminal da etapa 8.

14.  Copiar todos os argumentos a partir do `-v` da linha na etapa 12 e cole-os na janela de terminal.

15.  Alterar o último argumento para ser o `.app` pacote localizado dentro de `old/Payload/` pasta e, em seguida, execute o comando.

```bash
/usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
```

16.  Altere para o `old/` diretório no Terminal:

```bash
cd old
```

17.  Compacte o conteúdo do diretório em uma nova `.ipa` de arquivos usando o `zip` comando. Você pode alterar o `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argumento para a saída de `.ipa` onde quer que você gostaria de arquivo:

```bash
zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
```

## <a name="common-error-messages"></a>Mensagens de erro comuns

Se você vir `Invalid Signature. A sealed resource is missing or invalid.`, que geralmente significa que algo foi alterado dentro de `.app` pacote e que o `.app` pacote não foi corretamente novamente assinado posteriormente. Observe também que, se você quiser criar uma `.ipa` com um perfil de distribuição, você _deve_ build original `.ipa` com um perfil de distribuição. Caso contrário, o `Entitlements.xcent` estarão incorretas.

Para dar um exemplo concreto de como esse erro pode ocorrer, se você executar o seguinte `codesign --verify` comando na janela do Terminal após a etapa 9, você verá o erro, juntamente com a causa precisa do erro:

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

E o processo de verificação da App Store relatará uma mensagem de erro semelhante:

> Erro ITMS-90035: "assinatura inválida. Um recurso lacrado está ausente ou inválido. O binário no caminho [iPhoneApp1.app/iPhoneApp1] contém uma assinatura inválida. Verifique se que você tiver assinado o seu aplicativo com um certificado de distribuição, e não um certificado do ad-hoc ou um certificado de desenvolvimento. Verifique se as configurações de assinatura de código em Xcode estão corretas no nível de destino (o que substituir quaisquer valores no nível do projeto). Além disso, verifique se o pacote que você está carregando foi criado usando um destino de versão no Xcode, não um destino do simulador. Se você tiver certeza de que as configurações de assinatura de código estão corretas, escolha "Limpar tudo" no Xcode, exclua o diretório "build" no Finder e recompilar seu destino de versão. Para obter mais informações, consulte [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)"
