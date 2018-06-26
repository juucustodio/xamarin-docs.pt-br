---
title: Obter um Google mapeia a chave de API
description: Como obter uma chave de API do Google mapeia para adicionar funcionalidade é mapeado para seu aplicativo.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: 365bc56c70ef903622c3a4583a30460f907b4ec9
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935048"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obter um Google mapeia a chave de API

Para usar a funcionalidade do Google Maps no Android, você precisa registrar uma chave de API de mapas com o Google. Até que você fizer isso, você verá somente uma grade em branco em vez de um mapa em seus aplicativos. Você deve obter uma chave de API do Android do Google Maps v2 - chaves do v1 chave de API do Google Maps Android mais antigos não funcionarão.

Como obter uma chave de API Maps v2 envolve as seguintes etapas:

1.  Recupere a impressão digital de SHA-1 do armazenamento de chaves que é usado para assinar o aplicativo.
2.  Crie um projeto no console de APIs do Google.
3.  Obter a chave de API.


## <a name="obtaining-your-signing-key-fingerprint"></a>Obter sua impressão digital da chave assinatura

Para solicitar uma chave de API de mapas do Google, você precisa saber a impressão digital de SHA-1 do armazenamento de chaves que é usado para assinar o aplicativo.
Normalmente, isso significa que você terá que determinar a impressão digital de SHA-1 para o armazenamento de chaves de depuração e, em seguida, o SHA-1 impressão digital para o armazenamento de chaves que é usado para assinar seu aplicativo para versão.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Por padrão, o armazenamento de chaves que é usado para assinar as versões de depuração de um xamarin aplicativo pode ser encontrado no seguinte local:

**C:\\usuários\\[USERNAME]\\AppData\\Local\\Xamarin\\Mono para Android\\debug.keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Essa ferramenta normalmente é encontrada no diretório bin do Java:

**C:\\arquivos de programa (x86)\\Java\\jdk [VERSION]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Por padrão, o armazenamento de chaves que é usado para assinar as versões de depuração de um xamarin aplicativo pode ser encontrado no seguinte local:

**/Users/[USERNAME]/.local/Share/Xamarin/mono para Android/debug.keystore**

Informações sobre um repositório de chaves são obtidas executando o comando `keytool` no JDK. Essa ferramenta normalmente é encontrada no diretório bin do Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


Execute keytool usando o seguinte comando (usando os caminhos de arquivo mostrados acima):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Exemplo de Debug.keystore

Para a chave de depuração padrão (que é criada automaticamente para você para depuração), use este comando:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>Chaves de produção

Ao implantar um aplicativo para o Google Play, ele deve ser [assinado com uma chave privada](~/android/deploy-test/signing/index.md).
O `keytool` precisa ser executado com os detalhes da chave privados e a impressão de digital de SHA-1 resultante usado para criar uma chave de API do Google Maps de produção. Lembre-se de atualizar o **AndroidManifest.xml** arquivo com a chave de API do Google Maps correta antes da implantação.

### <a name="keytool-output"></a>Saída de keytool

Você verá algo parecido com a seguinte saída na janela do console:

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

Você usará a impressão digital de SHA-1 (listados depois **SHA1**) mais adiante neste guia.

## <a name="creating-an-api-project"></a>Criando um projeto de API

Depois de recuperar a impressão digital de SHA-1 da chave de armazenamento de assinatura, é necessário criar um novo projeto no console de APIs do Google (ou adicionar o serviço do Google Maps Android API v2 a um projeto existente).

1. Em um navegador, navegue até o [API de Console de desenvolvedores do Google & Painel Services](https://console.developers.google.com/apis/dashboard/) e clique em **seleciona um projeto**. Clique no nome de um projeto ou criar um novo clicando em **novo projeto**:

   [![Botão Criar projeto de Console de desenvolvedor Google](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Se você criou um novo projeto, insira o nome do projeto no **novo projeto** caixa de diálogo que é exibida. Essa caixa de diálogo produzirá um ID exclusivo do projeto com base no nome do projeto. Em seguida, clique o **criar** botão como mostrado neste exemplo:

   [![Novo projeto é denominado XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Após um minuto ou menos, o projeto é criado e você será direcionado para o **painel** página do projeto. A partir daí, clique em **habilitar APIS e serviços**:

   [![Clicando em API Android do Google Maps na seção de biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Do **biblioteca API** , clique em **mapas SDK para Android**. Na próxima página, clique em **habilitar** para ativar o serviço para este projeto:

   [![Clicar no botão Ativar na seção de painel](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

Neste ponto o projeto de API foi criado e Google Maps Android API v2 foi adicionado a ele. No entanto, você não pode usar essa API em seu projeto até que você criar credenciais para ele. A próxima seção explica como criar uma chave de API e um aplicativo xamarin de lista de permissões para que ele está autorizado a usar essa chave.

## <a name="obtaining-the-api-key"></a>Como obter a chave de API

Após o **Console de desenvolvedor do Google** API projeto tiver sido criado, é necessário criar uma chave de API do Android. Aplicativos xamarin devem ter uma chave de API antes de conceder acesso à API do Android mapa v2.

1. No **mapas SDK para Android** página exibida (depois de clicar em **habilitar** na etapa anterior), vá para o **credenciais** guia e clique no **criar credenciais** botão:

   [![SDK de mapas de mensagem de credenciais Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Clique em **chave API**:

   [![Adicionar credenciais para a caixa de diálogo do projeto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Depois de clicar nesse botão, a chave de API é gerada. Next é necessário restringir a essa chave para que apenas o aplicativo pode chamar APIs com essa chave. Clique em **chave RESTRICT**:

   [![Clicando em restringir chave na página de credenciais](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Alterar o **nome** campo de **1 da chave de API** para um nome que ajudará a lembrar-se de que a chave é usada (**XamarinMapsDemoKey** é usada neste exemplo). Em seguida, clique o **aplicativos Android** botão de opção:

   [![Selecionando aplicativos Android na página de credenciais](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para adicionar a impressão digital de SHA-1, clique em **+ Adicionar nome do pacote e a impressão digital**:

   [![Clicar em Adicionar nome do pacote e a impressão digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Insira o nome do pacote do aplicativo e impressão digital do certificado o SHA-1 (obtido por meio de `keytool` conforme explicado anteriormente neste guia). No exemplo a seguir, o pacote de nomes para `XamarinMapsDemo` é inserido, seguido pela impressão digital do certificado SHA-1 obtida **debug.keystore**:

   [![Nome do pacote inserido é com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Observe que, para o APK acessar o Google Maps, você deve incluir a impressão digital de SHA-1 e nomes para cada armazenamento de chaves (debug e release) que você usa para assinar seu APK do pacote. Por exemplo, se você usar um computador para outro computador para gerar a versão APK e de depuração, você deve incluir a impressão digital do certificado SHA-1 do repositório de chaves de depuração do primeiro computador e a impressão digital do certificado SHA-1 do repositório de chaves de versão o segundo computador. Clique em **+ Adicionar nome do pacote e a impressão digital** para adicionar outro nome de pacote e a impressão digital, como mostrado neste exemplo:

   [![Adicionando a impressão digital outro cria outro certificado de SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Clique no botão **Save (Salvar)** para salvar suas alterações. Em seguida, você retornará à lista de suas chaves de API. Se você tiver outras chaves de API que você criou anteriormente, eles serão também listados aqui. Neste exemplo, somente uma chave de API (criada nas etapas anteriores) é listada:

   [![XamarinMapsDemoKey é mostrado na lista de chaves de API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conectar o projeto a uma conta faturável

A partir 11 de junho, de 2018, a chave de API não funcionará se o projeto não está conectado a uma conta faturável (mesmo se o serviço está ainda livre para aplicativos móveis).

1. Clique no botão de menu hambúrguer e selecione o **cobrança** página:

   [![Selecionar a seção de cobrança do menu hambúrguer](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Vincular o projeto a uma conta de cobrança clicando **vincular uma conta de cobrança** seguido por **criar conta de cobrança** no pop-up do exibido (se você não tiver uma conta, você será orientado para criar um novo):

   [![Projeto de link para a conta de cobrança](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Adicionando a chave ao seu projeto

Finalmente, adicione essa chave de API para o **AndroidManifest.XML** arquivo de seu aplicativo xamarin. No exemplo a seguir, `YOUR_API_KEY` a ser substituído com a chave de API gerada nas etapas anteriores:

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
