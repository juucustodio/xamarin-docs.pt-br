---
title: Publicação Independente
ms.prod: xamarin
ms.assetid: 6FB4DEF2-01AD-C5FE-0950-CE1BF088A9C6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: d7135611c3c0a13794b1ed538d62a0788227b7bd
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977748"
---
# <a name="publishing-independently"></a>Publicação Independente

É possível publicar um aplicativo sem usar nenhum dos mercados Android existentes. Esta seção explicará esses outros métodos de publicação e os níveis de licenciamento do Xamarin.Android.

## <a name="xamarin-licensing"></a>Licenciamento do Xamarin

Algumas licenças estão disponíveis para desenvolvimento, implantação e distribuição de aplicativos Xamarin.Android:

-   **Comunidade do Visual Studio** &ndash; Para estudantes, pequenas equipes e desenvolvedores de SO que usam o Windows.

-   **Visual Studio Professional** &ndash; Para desenvolvedores individuais ou pequenas equipes (somente Windows). Esta licença oferece uma assinatura padrão ou de nuvem e nenhuma restrição de uso.

-   **Visual Studio Enterprise** &ndash; Para equipes de qualquer tamanho (somente Windows). Esta licença inclui recursos da empresa, uma assinatura padrão ou de nuvem.

Visite a [visualstudio.com](https://visualstudio.microsoft.com/xamarin/) para baixar as edições de comunidade ou para saber mais sobre como comprar as edições Professional e Enterprise.


## <a name="allow-installation-from-unknown-sources"></a>Permitir instalação de fontes desconhecidas

Por padrão, o Android impede que os usuários baixem e instalem aplicativos de locais diferentes do Google Play. Para permitir a instalação de fontes diferentes do marketplace, um usuário deve habilitar a configuração *Fontes desconhecidas* em um dispositivo antes de tentar instalar um aplicativo. A configuração para isso pode ser encontrada em **Configurações > Segurança**, conforme mostrado no diagrama a seguir:

[![Tela de Configurações de segurança](publishing-independently-images/settings.png)](publishing-independently-images/settings.png#lightbox)


> [!IMPORTANT]
> Alguns provedores de rede podem impedir a instalação de aplicativos de fontes desconhecidas, independentemente dessa configuração.



## <a name="publishing-by-e-mail"></a>Publicando por email

Anexar a versão APK a um email é uma maneira rápida e fácil de distribuir um aplicativo para usuários. Quando o usuário abre o email em um dispositivo com Android, o Android reconhecerá o anexo APK e exibirá um botão **Instalar**, conforme mostrado na imagem a seguir:

[![Botão Instalar para anexo](publishing-independently-images/publishing-via-email.png)](publishing-independently-images/publishing-via-email.png#lightbox)

Embora a distribuição por email seja simples, ela fornece algumas proteções contra pirataria ou distribuição não autorizada. Ela é melhor reservada para situações em que os destinatários do aplicativo sejam poucos e eles são confiáveis para não distribuir o aplicativo.


## <a name="publishing-by-web"></a>Publicando pela Web

É possível distribuir um aplicativo por um servidor Web. Isso é feito ao carregar o aplicativo para o servidor Web e, em seguida, fornecer um link de download para os usuários. Quando um dispositivo com Android navega até um link e, em seguida, baixa o aplicativo, esse aplicativo será instalado automaticamente quando o download for concluído.


## <a name="manually-installing-an-apk"></a>Instalar manualmente um APK

Instalação manual é uma terceira opção de instalação de aplicativos. Para efetuar uma instalação manual de um aplicativo:

1.   **Distribuir uma cópia do APK para usuário** &ndash; Por exemplo, essa cópia pode ser distribuída em um CD ou unidade flash USB.
1.   **(O usuário) instala o aplicativo em um dispositivo Android**  &ndash; Usar a linha de comando *Ferramenta Android Debug Bridge* (**adb**). **adb** é uma ferramenta de linha de comando versátil que permite a comunicação com uma instância do emulador ou um dispositivo Android. O SDK do Android inclui o **adb**; ele pode ser encontrado no diretório **<sdk>/platform-tools/**.

O dispositivo Android deve estar conectado com um cabo USB no computador.
Computadores com Windows também podem exigir drivers USB adicionais do fornecedor do telefone para serem reconhecidos pelo **adb**. Instruções de instalação para esses drivers adicionais de USB estão além do escopo deste documento.

Antes de emitir qualquer comando **adb**, é útil saber quais instâncias de emuladores ou dispositivos estão conectados, caso haja algum. É possível ver uma lista do que está conectado por meio do comando `devices`, conforme demonstrado no seguinte snippet:

```shell
$ adb devices
List of devices attached
        0149B2EC03012005device
```

Depois de confirmar os dispositivos conectados, o aplicativo pode ser instalado, emitindo o comando `install` com **adb**:

```shell
$ adb install <path-to-apk>
```

O snippet a seguir mostra um exemplo de instalação de um aplicativo em um dispositivo conectado:

```shell
$ adb install helloworld.apk
3772 KB/s (3013594 bytes in 0.780s)
        pkg: /data/local/tmp/helloworld.apk
Success
```

Se o aplicativo já estiver instalado, o `adb install` não poderá instalar o APK e reportará uma falha, conforme mostrado no exemplo a seguir:

```shell
$ adb install helloworld.apk
4037 KB/s (3013594 bytes in 0.728s)
        pkg: /data/local/tmp/helloworld.apk
Failure [INSTALL_FAILED_ALREADY_EXISTS]
```

Será necessário desinstalar o aplicativo do dispositivo. Primeiro, execute o comando `adb uninstall`:

```shell
adb uninstall <package_name>
```

O snippet a seguir é um exemplo de desinstalação de um aplicativo:

```shell
$ adb uninstall mono.samples.helloworld
Success
```
