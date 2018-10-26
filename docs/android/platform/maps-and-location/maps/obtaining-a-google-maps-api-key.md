---
title: Obter chave de API um Google Maps
description: Como obter uma chave de API do Google Maps para adicionar mapas a funcionalidade ao seu aplicativo.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: bfeb9d8fa2a0b5a9b18ab8266500586e2e3b6c68
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120015"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obter chave de API um Google Maps

Para usar a funcionalidade do Google Maps no Android, você precisa se registrar para uma chave de API de mapas com o Google. Enquanto isso, você verá apenas uma grade em branco em vez de um mapa em seus aplicativos. Você deve obter uma chave de API do Google Maps Android v2 - chaves do v1 de chave de API do Google Maps Android mais antigos não funcionarão.

Como obter uma chave de API de mapas v2 envolve as seguintes etapas:

1.  Recupere a impressão digital de SHA-1 do repositório de chaves que é usado para assinar o aplicativo.
2.  Crie um projeto no console de APIs do Google.
3.  Como obter a chave de API.


## <a name="obtaining-your-signing-key-fingerprint"></a>Como obter sua impressão digital da chave assinatura

Para solicitar uma chave de API de mapas do Google, você precisa saber a impressão digital de SHA-1 do repositório de chaves que é usado para assinar o aplicativo.
Normalmente, isso significa que você terá que determinar a impressão digital de SHA-1 para o repositório de chaves de depuração e, em seguida, o SHA-1 de impressão digital para o repositório de chaves que é usado para assinar seu aplicativo para lançamento.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Por padrão, o repositório de chaves que é usado para assinar as versões de depuração de um xamarin. Android aplicativo pode ser encontradas no seguinte local:

**C:\\os usuários\\[USERNAME]\\AppData\\Local\\Xamarin\\Mono para Android\\Debug. keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Essa ferramenta normalmente é encontrada no diretório bin do Java:

**C:\\arquivos de programas (x86)\\Java\\jdk [versão]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Por padrão, o repositório de chaves que é usado para assinar as versões de depuração de um xamarin. Android aplicativo pode ser encontradas no seguinte local:

**/Users/[USERNAME]/.local/Share/Xamarin/mono para Android/debug.keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Essa ferramenta normalmente é encontrada no diretório bin do Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


Execute o keytool usando o seguinte comando (usando os caminhos de arquivo mostrados acima):

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
O `keytool` precisará ser executado com os detalhes da chave privados e a impressão de digital de SHA-1 resultante usado para criar uma chave de API do Google Maps de produção. Lembre-se de atualizar o **androidmanifest. XML** arquivo com a chave de API do Google Maps correta antes da implantação.

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

Você usará a impressão digital de SHA-1 (listadas após **SHA1**) mais adiante neste guia.

## <a name="creating-an-api-project"></a>Criando um projeto de API

Depois de recuperar a impressão digital de SHA-1 da chave de armazenamento de assinatura, é necessário criar um novo projeto no console do Google APIs (ou adicione o serviço v2 de API do Google Maps Android a um projeto existente).

1. Em um navegador, navegue até a [API de Console de desenvolvedores do Google e o painel de serviços](https://console.developers.google.com/apis/dashboard/) e clique em **seleciona um projeto**. Clique em um nome de projeto ou criar um novo clicando **novo projeto**:

   [![Botão Criar projeto de Console de desenvolvedor Google](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Se você criou um novo projeto, insira o nome do projeto na **novo projeto** caixa de diálogo que é exibida. Essa caixa de diálogo produzirá um ID exclusivo do projeto com base no nome do seu projeto. Em seguida, clique o **criar** botão, conforme mostrado neste exemplo:

   [![Novo projeto é denominado XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Após um minuto, o projeto é criado e você será direcionado para o **Dashboard** página do projeto. A partir daí, clique em **habilitar APIS e serviços**:

   [![Clicar em API do Google Maps Android a seção da biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Dos **biblioteca de API** , clique em **Maps SDK para Android**. Na próxima página, clique em **habilitar** para ativar o serviço para este projeto:

   [![Clicando no botão Habilitar na seção painel](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

Neste ponto o projeto de API foi criado e a API do Google Maps Android v2 foi adicionado a ele. No entanto, é possível usar essa API em seu projeto até que você crie credenciais para ele. A próxima seção explica como criar uma chave de API e um aplicativo xamarin. Android de lista de permissões para que ele está autorizado a usar essa chave.

## <a name="obtaining-the-api-key"></a>Como obter a chave de API

Após o **Console de desenvolvedor Google** projeto de API tiver sido criado, é necessário criar uma chave de API do Android. Aplicativos xamarin. Android devem ter uma chave de API antes de conceder acesso à API do Android mapa v2.

1. No **Maps SDK para Android** página que é exibida (depois de clicar em **habilitar** na etapa anterior), vá para o **credenciais** guia e clique no **criar credenciais** botão:

   [![Mapas do SDK para mensagem de credenciais do Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Clique em **chave de API**:

   [![Adicionar credenciais para sua caixa de diálogo do projeto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Depois que esse botão é clicado, a chave de API é gerada. Em seguida é necessário restringir essa chave para que apenas seu aplicativo pode chamar APIs com essa chave. Clique em **chave RESTRICT**:

   [![Clicar em restringir chave na página de credenciais](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Alterar o **nome** campo **1 da chave de API** para um nome que ajudará você a se lembrar de que a chave é usada para (**XamarinMapsDemoKey** é usado neste exemplo). Em seguida, clique o **aplicativos Android** botão de opção:

   [![Selecionando aplicativos Android na página de credenciais](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para adicionar a impressão digital de SHA-1, clique em **+ Adicionar nome do pacote e a impressão digital**:

   [![Clicar em Adicionar nome do pacote e a impressão digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Insira o nome do pacote do aplicativo e insira a impressão digital do certificado o SHA-1 (obtido por meio do `keytool` conforme explicado anteriormente neste guia). No exemplo a seguir, o nome para `XamarinMapsDemo` é inserido, seguido pela impressão digital do certificado SHA-1 obtida **Debug. keystore**:

   [![Nome do pacote inserido é com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Observe que, na ordem de seu APK acessar o Google Maps, você deve incluir as impressões digitais de SHA-1 e nomes para cada repositório de chaves (depuração e versão) que você usa para assinar o APK do pacote. Por exemplo, se você usar um computador para outro computador para gerar a versão APK e de depuração, você deve incluir a impressão digital SHA-1 do certificado do repositório de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do repositório de chaves de versão o segundo computador. Clique em **+ Adicionar nome do pacote e a impressão digital** para adicionar outro nome de pacote e a impressão digital, conforme mostrado neste exemplo:

   [![Adicionando a impressão digital do outro cria outro certificado SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Clique no botão **Save (Salvar)** para salvar suas alterações. Em seguida, você retornará à lista de suas chaves de API. Se você tiver outras chaves de API que você criou anteriormente, eles serão também listados aqui. Neste exemplo, somente uma chave de API (criada nas etapas anteriores) é listada:

   [![XamarinMapsDemoKey é mostrado na lista de chaves de API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conectar o projeto a uma conta faturável

A partir 11 de junho de 2018, a chave de API não funcionará se o projeto não está conectado a uma conta faturável (mesmo se o serviço ainda é gratuito para aplicativos móveis).

1. Clique no botão de menu de hambúrguer e selecione o **cobrança** página:

   [![Selecionando a seção de cobrança de menu de hambúrguer](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Vincular o projeto a uma conta de cobrança clicando **vincular uma conta de cobrança** seguido **criar conta de cobrança** no pop-up exibido (se você não tiver uma conta, você será guiado para criar um novo):

   [![Projeto de link para a conta de cobrança](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Adicionar a chave ao seu projeto

Por fim, adicione esta chave de API para o **androidmanifest. XML** arquivo do seu aplicativo xamarin. Android. No exemplo a seguir, `YOUR_API_KEY` deve ser substituída pela chave de API gerado nas etapas anteriores:

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
