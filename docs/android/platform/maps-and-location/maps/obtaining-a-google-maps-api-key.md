---
title: Obter uma chave de API do Google Maps
description: Como obter uma chave de API do Google Maps para adicionar a funcionalidade de mapas ao seu aplicativo.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: a56405aaed84be5015e5d58ef9a13ba8e270ea58
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757532"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obter uma chave de API do Google Maps

Para usar a funcionalidade do Google Maps no Android, você precisa se registrar para uma chave de API de mapas com o Google. Até fazer isso, você verá apenas uma grade em branco em vez de um mapa em seus aplicativos. Você deve obter uma API do Google Maps Android v2 chave-chaves da antiga API do Google Maps Android v1 não funcionará.

A obtenção de uma chave de API de mapas v2 envolve as seguintes etapas:

1. Recupere a impressão digital SHA-1 do keystore que é usada para assinar o aplicativo.
2. Crie um projeto no console de APIs do Google.
3. Obtendo a chave de API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Obtendo a impressão digital da chave de assinatura

Para solicitar uma chave de API de mapas do Google, você precisa saber a impressão digital SHA-1 do repositório de chaves que é usado para assinar o aplicativo.
Normalmente, isso significa que você precisará determinar a impressão digital SHA-1 para o armazenamento de chaves de depuração e, em seguida, a impressão digital SHA-1 para o repositório de chaves que é usado para assinar seu aplicativo para a versão.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Por padrão, o repositório de chaves que é usado para assinar versões de depuração de um aplicativo Xamarin. Android pode ser encontrado no seguinte local:

**C:\\Users\\[username\\]\\AppData\\localXamarin\\mono para\\Android Debug. keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Normalmente, essa ferramenta é encontrada no diretório bin do Java:

**C:\\arquivos de programas (x86\\)\\Java JDK [versão\\]\\bin keytool. exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Por padrão, o repositório de chaves que é usado para assinar versões de depuração de um aplicativo Xamarin. Android pode ser encontrado no seguinte local:

**/Users/[USERNAME]/.local/share/Xamarin/Mono para Android/Debug. keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Normalmente, essa ferramenta é encontrada no diretório bin do Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----

Execute keytool usando o seguinte comando (usando os caminhos de arquivo mostrados acima):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Exemplo de Debug. keystore

Para a chave de depuração padrão (que é criada automaticamente para você para depuração), use este comando:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>Chaves de produção

Ao implantar um aplicativo no Google Play, ele deve ser [assinado com uma chave privada](~/android/deploy-test/signing/index.md).
O `keytool` precisará ser executado com os detalhes da chave privada e a impressão digital do SHA-1 resultante usada para criar uma chave de API de produção do Google Maps. Lembre-se de atualizar o arquivo **AndroidManifest. xml** com a chave de API do Google Maps correta antes da implantação.

### <a name="keytool-output"></a>Saída de keytool

Você deverá ver algo parecido com a seguinte saída na janela do console:

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

Você usará a impressão digital SHA-1 (listada após **SHA1**) mais adiante neste guia.

## <a name="creating-an-api-project"></a>Criando um projeto de API

Depois de ter recuperado a impressão digital SHA-1 do repositório de chaves de assinatura, é necessário criar um novo projeto no console de APIs do Google (ou adicionar o serviço do Google Maps Android API v2 a um projeto existente).

1. Em um navegador, navegue até a [API do console de desenvolvedores do Google & painel de serviços](https://console.developers.google.com/apis/dashboard/) e clique em **selecionar um projeto**. Clique em um nome de projeto ou crie um novo clicando em **novo projeto**:

   [![Botão Criar projeto do console do desenvolvedor do Google](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Se você criou um novo projeto, insira o nome do projeto na caixa de diálogo **novo projeto** que é exibida. Esta caixa de diálogo fabricará uma ID de projeto exclusiva com base no nome do projeto. Em seguida, clique no botão **criar** , conforme mostrado neste exemplo:

   [![O novo projeto é denominado XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Após um minuto, o projeto é criado e você é levado para a página **painel** do projeto. A partir daí, clique em **habilitar APIs e serviços**:

   [![Clicando em API do Google Maps do Android na seção biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Na página **biblioteca de API** , clique em **mapas SDK para Android**. Na página seguinte, clique em **habilitar** para ativar o serviço para este projeto:

   [![Clicando no botão habilitar na seção painel](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

Neste ponto, o projeto de API foi criado e o Google Maps Android API v2 foi adicionado a ele. No entanto, você não pode usar essa API em seu projeto até criar credenciais para ela. A próxima seção explica como criar uma chave de API e uma lista branca de um aplicativo Xamarin. Android para que ele esteja autorizado a usar essa chave.

## <a name="obtaining-the-api-key"></a>Obtendo a chave de API

Após a criação do projeto de API do **console do desenvolvedor do Google** , é necessário criar uma chave de API do Android. Os aplicativos Xamarin. Android devem ter uma chave de API antes de receberem acesso à API de mapa do Android v2.

1. Na página **SDK do Maps para Android** exibida (depois de clicar em **habilitar** na etapa anterior), vá para a guia **credenciais** e clique no botão **criar credenciais** :

   [![SDK de mapas para mensagem de credenciais do Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Clique em **chave de API**:

   [![Caixa de diálogo Adicionar credenciais ao seu projeto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Depois que esse botão é clicado, a chave de API é gerada. Em seguida, é necessário restringir essa chave para que apenas seu aplicativo possa chamar APIs com essa chave. Clique em **restringir chave**:

   [![Clicando em restringir chave na página credenciais](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Altere o campo de **nome** da **chave de API 1** para um nome que ajudará você a se lembrar do que a chave é usada (**XamarinMapsDemoKey** é usado neste exemplo). Em seguida, clique no botão de opção **aplicativos Android** :

   [![Selecionando aplicativos Android na página credenciais](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para adicionar a impressão digital SHA-1, clique em **+ adicionar nome do pacote e impressão digital**:

   [![Clicando em adicionar nome do pacote e impressão digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Insira o nome do pacote do aplicativo e insira a impressão digital do certificado SHA-1 `keytool` (obtida por meio do, conforme explicado anteriormente neste guia). No exemplo a seguir, o nome do pacote `XamarinMapsDemo` para é inserido, seguido pela impressão digital do certificado SHA-1 obtida do **debug. keystore**:

   [![O nome do pacote inserido é com. xamarin. docs. Android. map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Observe que, na ordem de seu APK acessar o Google Maps, você deve incluir as impressões digitais de SHA-1 e nomes para cada repositório de chaves (depuração e versão) que você usa para assinar o APK do pacote. Por exemplo, se você usar um computador para outro computador para gerar a versão APK e de depuração, você deve incluir a impressão digital SHA-1 do certificado do repositório de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do repositório de chaves de versão o segundo computador. Clique em **+ adicionar nome do pacote e impressão digital** para adicionar outra impressão digital e o nome do pacote, conforme mostrado neste exemplo:

   [![Adicionar outra impressão digital cria outro certificado SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Clique no botão **Save (Salvar)** para salvar suas alterações. Em seguida, você retornará à lista de suas chaves de API. Se você tiver outras chaves de API que você criou anteriormente, elas também serão listadas aqui. Neste exemplo, apenas uma chave de API (criada nas etapas anteriores) está listada:

   [![XamarinMapsDemoKey é mostrado na lista de chaves de API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conectar o projeto a uma conta Faturável

A partir de junho de 2018, a chave de API não funcionará se o projeto não estiver conectado a uma conta faturável (mesmo que o serviço ainda esteja livre para aplicativos móveis).

1. Clique no botão de menu de e selecione a página de **cobrança** :

   [![Selecionando a seção de cobrança do menu de](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Vincule o projeto a uma conta de cobrança clicando em **vincular uma conta de cobrança** seguida por **criar conta de cobrança** no pop-up exibido (se você não tiver uma conta, você será guiado para criar uma nova):

   [![Vincular projeto à conta de cobrança](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Adicionando a chave ao seu projeto

Por fim, adicione essa chave de API ao arquivo **AndroidManifest. xml** do seu aplicativo Xamarin. Android. No exemplo a seguir, `YOUR_API_KEY` deve ser substituído pela chave de API gerada nas etapas anteriores:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>Links relacionados

- [Console de APIs do Google](https://code.google.com/apis/console/)
- [A chave de API do Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
