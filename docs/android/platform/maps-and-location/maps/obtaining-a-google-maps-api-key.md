---
title: Obter uma chave de API do Google Maps
description: Como obter uma chave de API do Google Maps para adicionar funcionalidade de mapas ao seu aplicativo.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 371876d087c7027d4cfe2d2d9ada8b0dbedb5dd5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75488966"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obter uma chave de API do Google Maps

Para usar a funcionalidade do Google Maps no Android, você precisa se registrar para uma chave de API do Maps com o Google. Até fazer isso, você verá apenas uma grade em branco em vez de um mapa em seus aplicativos. Você deve obter uma chave API v2 do Google Maps - as chaves da chave V1 do Google Maps Android mais antigas não funcionarão.

A obtenção de uma chave API v2 do Maps envolve as seguintes etapas:

1. Recupere a impressão digital SHA-1 do keystore que é usado para assinar o aplicativo.
2. Crie um projeto no console do Google APIs.
3. Obtendo a chave API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Obtenção da impressão digital da chave de assinatura

Para solicitar uma chave de API do Maps do Google, você precisa saber a impressão digital SHA-1 do keystore que é usado para assinar o aplicativo.
Normalmente, isso significa que você terá que determinar a impressão digital SHA-1 para o armazenamento de chaves de depuração e, em seguida, a impressão digital SHA-1 para o armazenamento de chaves que é usado para assinar seu aplicativo de liberação.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Por padrão, o armazenamento de chaves que é usado para assinar versões de depuração de um aplicativo Xamarin.Android pode ser encontrado no seguinte local:

**C:\\\\\\Usuários [USERNAME]\\\\AppData\\Local Xamarin Mono para Android\\debug.keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Esta ferramenta é normalmente encontrada no diretório Java bin:

**C:\\Arquivos\\\\do programa\\Android jdk microsoft_dist_openjdk_[VERSION]\\bin\\keytool.exe**

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Por padrão, o armazenamento de chaves que é usado para assinar versões de depuração de um aplicativo Xamarin.Android pode ser encontrado no seguinte local:

**/Users/[USERNAME]/.local/share/Xamarin/Mono para Android/debug.keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Esta ferramenta é normalmente encontrada no diretório Java bin:

**/Sistema/Biblioteca/Java/JavaMáquinas Virtuais/[VERSION].jdk/Conteúdo/Home/bin/keytool**

-----

Executar ferramenta de tecla usando o seguinte comando (usando os caminhos de arquivo mostrados acima):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Exemplo de debug.keystore

Para a chave de depuração padrão (que é criada automaticamente para você para depuração), use este comando:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>Chaves de Produção

Ao implantar um aplicativo no Google Play, ele deve ser [assinado com uma chave privada](~/android/deploy-test/signing/index.md).
O `keytool` testamento precisa ser executado com os detalhes da chave privada e a impressão digital SHA-1 resultante usada para criar uma chave API do Google Maps de produção. Lembre-se de atualizar o arquivo **AndroidManifest.xml** com a chave API correta do Google Maps antes da implantação.

### <a name="keytool-output"></a>Saída de ferramenta de chave

Você deve ver algo como a seguinte saída na janela do console:

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

Você usará a impressão digital SHA-1 (listada após **SHA1**) mais tarde neste guia.

## <a name="creating-an-api-project"></a>Criando um projeto de API

Depois de recuperar a impressão digital SHA-1 do armazenamento de chaves de assinatura, é necessário criar um novo projeto no console do Google APIs (ou adicionar o serviço Google Maps Android API v2 a um projeto existente).

1. Em um navegador, navegue até o [Painel de Serviços de & de Console do Google e](https://console.developers.google.com/apis/dashboard/) clique em Selecionar um **projeto**. Clique em um nome de projeto ou crie um novo clicando em **NOVO PROJETO**:

   [![Botão Google Developer Console CREATE PROJECT](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Se você criou um novo projeto, digite o nome do projeto na caixa de diálogo **Projeto Novo** que é exibido. Este diálogo irá fabricar um ID de projeto exclusivo que é baseado no nome do seu projeto. Em seguida, clique no botão **Criar** como mostrado neste exemplo:

   [![Novo projeto se chama XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Após um minuto ou mais, o projeto é criado e você é levado para a página **dashboard** do projeto. A partir daí, clique **EM ATIVAR APIS E SERVIÇOS**:

   [![Clicando na API android do Google Maps na seção Biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Na página da **Biblioteca da API,** clique em **Mapas SDK para Android**. Na próxima página, clique em **ATIVAR** para ativar o serviço para este projeto:

   [![Clicando no botão ATIVAR na seção Painel de instrumentos](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

Neste ponto, o projeto de API foi criado e o Google Maps Android API v2 foi adicionado a ele. No entanto, você não pode usar esta API em seu projeto até criar credenciais para ele. A próxima seção explica como criar uma chave aPI e uma lista branca de um aplicativo Xamarin.Android para que ele esteja autorizado a usar essa chave.

## <a name="obtaining-the-api-key"></a>Obtenção da chave de API

Depois que o projeto de API do **Google Developer Console** foi criado, é necessário criar uma chave de API para Android. Os aplicativos Xamarin.Android devem ter uma chave de API antes de terem acesso à API v2 do Mapa do Android.

1. Na página **Do SDK do Maps para Android** exibida (depois de clicar em **ENABLE** na etapa anterior), vá para a guia **Credenciais** e clique no botão **Criar credenciais:**

   [![Mapeia sdk para mensagem de credenciais do Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Clique **na tecla API**:

   [![Adicione credenciais à caixa de diálogo do projeto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Depois que este botão é clicado, a tecla API é gerada. Em seguida, é necessário restringir essa chave para que apenas seu aplicativo possa chamar APIs com esta chave. Clique **em RESTRINGIR CHAVE:**

   [![Clicando em restringir a tecla na página Credenciais](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Altere o campo **Nome** da Chave 1 da **API** para um nome que o ajudará a lembrar para que a chave é usada **(XamarinMapsDemoKey** é usada neste exemplo). Em seguida, clique no botão de rádio **dos aplicativos Android:**

   [![Selecionando aplicativos para Android na página Credenciais](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para adicionar a impressão digital SHA-1, clique **em + Adicionar nome do pacote e impressão digital**:

   [![Clicando em Adicionar nome do pacote e impressão digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Digite o nome do pacote do seu aplicativo e `keytool` digite a impressão digital do certificado SHA-1 (obtida via como explicado anteriormente neste guia). No exemplo a seguir, `XamarinMapsDemo` o nome do pacote para é inserido, seguido pela impressão digital do certificado SHA-1 obtida em **debug.keystore**:

   [![O nome do pacote inserido é com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Observe que, para que seu APK acesse o Google Maps, você deve incluir impressões digitais SHA-1 e nomes de pacotes para cada keystore (depuração e liberação) que você usa para assinar seu APK. Por exemplo, se você usar um computador para depuração e outro computador para gerar o APK de lançamento, você deve incluir a impressão digital do certificado SHA-1 do armazenamento de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do armazenamento de chaves de lançamento do segundo computador. Clique **em + Adicionar nome do pacote e impressão digital** para adicionar outra impressão digital e nome do pacote, como mostrado neste exemplo:

   [![Adicionar outra impressão digital cria outro certificado SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Clique no botão **Salvar** para salvar suas alterações. Em seguida, você é devolvido à lista de suas chaves de API. Se você tiver outras chaves de API que você criou anteriormente, elas também serão listadas aqui. Neste exemplo, apenas uma chave de API (criada nas etapas anteriores) está listada:

   [![XamarinMapsDemoKey é mostrado na lista de chaves da API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conecte o projeto a uma conta faturada

A partir de 11 de junho de 2018, a chave da API não funcionará se o projeto não estiver conectado a uma conta faturada (mesmo que o serviço ainda seja gratuito para aplicativos móveis).

1. Clique no botão do menu do hambúrguer e selecione a página **De faturamento:**

   [![Selecionando a seção de faturamento do menu do hambúrguer](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Vincule o projeto a uma conta de cobrança clicando em **Vincular uma conta de faturamento** seguida por CREATE **BILLING ACCOUNT** no popup exibido (se você não tiver uma conta, você será orientado a criar uma nova):

   [![Link projeto para conta de faturamento](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Adicionando a chave ao seu projeto

Finalmente, adicione esta chave de API ao arquivo **AndroidManifest.XML** do seu aplicativo Xamarin.Android. No exemplo a `YOUR_API_KEY` seguir, deve ser substituído pela chave API gerada nas etapas anteriores:

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

- [Google APIs Console](https://code.google.com/apis/console/)
- [A chave da API do Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
