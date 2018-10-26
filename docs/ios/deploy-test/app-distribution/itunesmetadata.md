---
title: O arquivo iTunesMetadata.plist em aplicativos Xamarin.iOS
description: Esse artigo aborda o arquivo iTunesMetadata.plist usado para fornecer informações ao iTunes sobre um aplicativo iOS que utiliza a distribuição ad hoc para testes ou implantação corporativa.
ms.prod: xamarin
ms.assetid: 70676eba-6a99-4a3a-bccc-84359fe9c2c3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: af1ad32e605bbbe899ec8596a60cdabf226372db
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111298"
---
# <a name="the-itunesmetadataplist-file-in-xamarinios-apps"></a>O arquivo iTunesMetadata.plist em aplicativos Xamarin.iOS

_Este artigo aborda o arquivo iTunesMetadata.plist usado para fornecer informações ao iTunes sobre um aplicativo iOS que utiliza a distribuição ad hoc para testes ou implantação corporativa._

Quando um aplicativo iOS é criado no iTune Connect (seja para venda ou para lançamento gratuito pela iTunes App Store), o desenvolvedor pode especificar informações, como o gênero e o subgênero do aplicativo, a notificação de direitos autorais, os dispositivos iOS com suporte e os recursos obrigatórios do dispositivo. Para os aplicativos iOS que são entregues para testadores ou para usuários empresariais por meio da distribuição ad hoc, essas informações estão ausentes.

Para fornecer as informações ausentes a uma distribuição ad hoc, um arquivo `iTunesMetadata.plist` opcional pode ser criado e incluído no arquivo IPA de aplicativos. Esse arquivo plist é um arquivo XML especialmente formatado (consulte o [Propert List Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html) (Guia de Programação da Lista de Propriedade) da Apple para obter mais informações), que contém pares de chave/valor que definem as informações sobre um determinado aplicativo iOS.

<a name="iTunesMetadata_contents" />

## <a name="the-itunesmetadataplist-contents"></a>O Conteúdo do iTunesMetadata.plist

O exemplo a seguir mostra um arquivo `iTunesMetadata.plist` típico usado para definir as informações do iTunes para uma distribuição ad hoc:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>UIRequiredDeviceCapabilities</key>
    <dict>
        <key>armv7</key>
        <true/>
        <key>front-facing-camera</key>
        <true/>
    </dict>
    <key>artistName</key>
    <string>Company, Inc.</string>
    <key>bundleDisplayName</key>
    <string>App Name</string>
    <key>bundleShortVersionString</key>
    <string>1.5.1</string>
    <key>bundleVersion</key>
    <string>1.5.1</string>
    <key>copyright</key>
    <string>© 2015 Company, Inc.</string>
    <key>drmVersionNumber</key>
    <integer>0</integer>
    <key>fileExtension</key>
    <string>.app</string>
    <key>gameCenterEnabled</key>
    <false/>
    <key>gameCenterEverEnabled</key>
    <false/>
    <key>genre</key>
    <string>Games</string>
    <key>genreId</key>
    <integer>6014</integer>
    <key>itemName</key>
    <string>App Name</string>
    <key>kind</key>
    <string>software</string>
    <key>playlistArtistName</key>
    <string>Company, Inc.</string>
    <key>playlistName</key>
    <string>App Name</string>
    <key>releaseDate</key>
    <string>2015-11-18T03:23:10Z</string>
    <key>s</key>
    <integer>143441</integer>
    <key>softwareIconNeedsShine</key>
    <false/>
    <key>softwareSupportedDeviceIds</key>
    <array>
        <integer>9</integer>
    </array>
    <key>softwareVersionBundleId</key>
    <string>com.company.appid</string>
    <key>subgenres</key>
    <array>
        <dict>
            <key>genre</key>
            <string>Puzzle</string>
            <key>genreId</key>
            <integer>7012</integer>
        </dict>
        <dict>
            <key>genre</key>
            <string>Word</string>
            <key>genreId</key>
            <integer>7019</integer>
        </dict>
    </array>
    <key>versionRestrictions</key>
    <integer>16843008</integer>
</dict>
</plist>

```

Os valores para as chaves individuais serão abordados em detalhes abaixo.

### <a name="uirequireddevicecapabilities"></a>UIRequiredDeviceCapabilities

A chave `UIRequiredDeviceCapabilities` permite que o iTunes saiba quais recursos específicos de dispositivo um aplicativo iOS exige antes que ele possa ser instalado em um determinado dispositivo iOS. Ele é fornecido como um dicionário (`<dict>...</dict>`) de recursos (`<key>...</key>`) e um valor booliano para cada recurso. Se o valor de um recurso for `true`, esse recurso deverá estar presente. Se ele for `false`, o recurso não deverá estar presente no dispositivo. Por exemplo:

```xml
<key>UIRequiredDeviceCapabilities</key>
<dict>
    <key>armv7</key>
    <true/>
    <key>front-facing-camera</key>
    <true/>
</dict>
```
Especifica que o dispositivo iOS deve dar suporte para o conjunto de instruções ARM7 e ter uma câmera frontal antes que esse aplicativo possa ser instalado no dispositivo. Para obter uma lista completa dos valores permitidos, consulte a documentação [UIRequiredDeviceCapabilities](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW3) da Apple.

### <a name="artistname-and-playlistartistname"></a>artistName e playlistArtistName

Use as chaves `artistName` e `playlistArtistName` para definir o nome da empresa que criou o aplicativo iOS que será exibido no iTunes. Exemplo:

```xml
<key>artistName</key>
<string>Company, Inc.</string>
...
<key>playlistArtistName</key>
<string>Company, Inc.</string>
```

### <a name="bundledisplayname-itemname-and-playlistname"></a>bundleDisplayName, itemName e playlistName

Use as chaves `bundleDisplayName`, `itemName` e `playlistName` para definir o nome do aplicativo iOS que será exibido no iTunes. Exemplo:

```xml
<key>bundleDisplayName</key>
<string>App Name</string>
...
<key>itemName</key>
<string>App Name</string>
...
<key>playlistName</key>
<string>App Name</string>
```

### <a name="bundleshortversionstring-and-bundleversion"></a>bundleShortVersionString e bundleVersion

Use as chaves `bundleShortVersionString` e `bundleVersion` para definir o número de versão do aplicativo iOS que será exibido no iTunes. Exemplo:

```xml
<key>bundleShortVersionString</key>
<string>1.5.1</string>
<key>bundleVersion</key>
<string>1.5.1</string>
```

### <a name="softwareversionbundleid"></a>softwareVersionBundleId

Use a chave `softwareVersionBundleId` para especificar a ID do pacote para o aplicativo iOS. Exemplo:

```xml
<key>softwareVersionBundleId</key>
<string>com.company.appid</string>
```

### <a name="copyright"></a>direitos autorais

Use a chave `copyright` para definir a notificação de direitos autorais que é exibida no iTunes. Exemplo:

```xml
<key>copyright</key>
<string>© 2015 Company, Inc.</string>
```

### <a name="releasedate"></a>releaseDate

Use a chave `releaseDate` para fornecer uma data de lançamento para o aplicativo iOS que será exibido no iTunes. Exemplo:

```xml
<key>releaseDate</key>
<string>2015-11-18T03:23:10Z</string>
```

### <a name="softwareiconneedsshine"></a>softwareIconNeedsShine

Use a chave `softwareIconNeedsShine` para informar o iTunes se o ícone do aplicativo iOS exige um _realce_ no iOS 6 (e nas versões anteriores). Exemplo:

```xml
<key>softwareIconNeedsShine</key>
<false/>
```

### <a name="gamecenterenabled-and-gamecentereverenabled"></a>gameCenterEnabled e gameCenterEverEnabled

Use as chaves `gameCenterEnabled` e `gameCenterEverEnabled` para informar o iTunes se o aplicativo iOS dá suporte ao Game Center da Apple. Exemplo:

```xml
<key>gameCenterEnabled</key>
<false/>
<key>gameCenterEverEnabled</key>
<false/>
```

### <a name="genre-genreid-and-subgenres"></a>genre, genreId e subgenres

Use as chaves `genre` e `genreId` para informar ao iTunes em qual gênero o aplicativo iOS pertence. Exemplo:

```xml
<key>genre</key>
<string>Games</string>
<key>genreId</key>
<integer>6014</integer>
```

Como alternativa, a chave `subgenres` pode ser usada para definir até dois subgêneros para o aplicativo iOS. Exemplo:

```xml
<key>subgenres</key>
<array>
    <dict>
        <key>genre</key>
        <string>Puzzle</string>
        <key>genreId</key>
        <integer>7012</integer>
    </dict>
    <dict>
        <key>genre</key>
        <string>Word</string>
        <key>genreId</key>
        <integer>7019</integer>
    </dict>
</array>
```

Para aplicativos iOS, a Apple atualmente define os seguintes gêneros e IDs de gênero:

[!include[](~/ios/includes/table-appstore.md)]

Para obter mais informações, consulte a documentação [Genre IDs Appendix](http://www.apple.com/itunes/affiliates/resources/documentation/genre-mapping.html) (Apêndice de IDs de Gênero) da Apple.

### <a name="softwaresupporteddeviceids"></a>softwareSupportedDeviceIds

Use a chave `softwareSupportedDeviceIds` para informar ao iTunes quais dispositivos iOS dão suporte a esse aplicativo iOS. Exemplo:

```xml
<key>softwareSupportedDeviceIds</key>
<array>
    <integer>9</integer>
</array>
```

Onde os valores a seguir estão disponíveis:

- 1 – iPhones Clássicos
- 2 – iPod Touch
- 4 – iPad
- 9 – iPhones Modernos

### <a name="standard-keys"></a>Chaves Padrão

As chaves a seguir são incluídas em todos os arquivos `iTunesMetadata.plist` nos aplicativos iOS e sempre têm os mesmos valores:

```xml
<key>drmVersionNumber</key>
<integer>0</integer>
<key>fileExtension</key>
<string>.app</string>
...
<key>kind</key>
<string>software</string>
...
<key>s</key>
<integer>143441</integer>
...
<key>versionRestrictions</key>
<integer>16843008</integer>
```

<a name="iTunesMetadata_creating" />

## <a name="creating-an-itunesmetadataplist-file"></a>Criando um Arquivo iTunesMetadata.plist

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 Ao trabalhar com um arquivo `iTunesMetadata.plist` no Visual Studio para Mac, você tem duas opções:

- Criar e manter o arquivo usando o editor de plist visual do Visual Studio para Mac.
- Criar e manter o arquivo em um editor de texto sem formatação.

 Ambas as opções serão abordadas em detalhes abaixo.

### <a name="using-the-visual-plist-editor"></a>Usando o Editor Visual Plist

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no arquivo de projeto Xamarin.iOS e selecione **Adicionar** > **Novo Arquivo...** 
2. Na caixa de diálogo Novo Arquivo, selecione **iOS** > **Lista de Propriedades**:

    ![](itunesmetadata-images/image01.png "Selecionar a Lista de Propriedades do iOS")
3. Digite `iTunesMetadata` para o **Nome** e clique no botão **Novo**.
4. Clique duas vezes no arquivo `iTunesMetadata.plist` no **Gerenciador de Soluções** para abri-lo para edição:

    ![](itunesmetadata-images/image02.png "O editor iTunesMetadata.plist")
5. Clique no **+** verde para criar uma nova entrada e digite `UIRequiredDeviceCapabilities` como o nome da chave:

    ![](itunesmetadata-images/image03.png "Criar uma nova entrada e digitar UIRequiredDeviceCapabilities como o nome da chave")
6. Clique no tipo de valor da **Cadeia de caracteres** e selecione **Dicionário** na lista pop-up:

    ![](itunesmetadata-images/image04.png "Selecionar um Dicionário na lista pop-up")
7. Clique na dobra à esquerda do nome da Propriedade para revelar as entradas do dicionário:

    ![](itunesmetadata-images/image05.png "Revelar as entradas do dicionário")
8. Clique no texto **Adicionar nova entrada** e, em seguida, clique no **+** verde para adicionar uma entrada ao dicionário:

    ![](itunesmetadata-images/image06.png "Adicionar uma entrada ao dicionário")
9. Digite `armv7` para o nome da chave, selecione um tipo de **booliano** e digite **Sim** como o valor:

    ![](itunesmetadata-images/image07.png "Insira armv7 como o nome da chave, selecione um tipo de Booliano e digite Sim como o valor")
10. Repita as etapas acima até que você tenha preenchido o arquivo `iTunesMetadata.plist` com todos os pares de chave/valor necessários (confira a seção [O Conteúdo do iTunesMetadata.plist](#iTunesMetadata_contents) acima para obter mais detalhes).

11. Salve as alterações no arquivo plist.

### <a name="using-a-plain-text-editor"></a>Usando um Editor de Texto Sem Formatação

Faça o seguinte:

1. Em um editor de texto sem formatação, crie um novo arquivo de texto e nomeie-o como `iTunesMetadata.plist`.
2. Copie o conteúdo de exemplo da seção [O Conteúdo do iTunesMetadata.plist](#iTunesMetadata_contents) acima.
3. Cole o conteúdo no arquivo e edite-o conforme necessário.
4. Salve o arquivo e retorne ao Visual Studio para Mac.
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no arquivo de projeto Xamarin.iOS e selecione **Adicionar** > **Arquivos Existentes...**.
6. Na caixa de diálogo Abrir Arquivo, selecione o arquivo `iTunesMetadata.plist` que foi criado acima e clique no botão **OK**.
7. Deixe a **Ação de Build** desse arquivo definida como **Nenhuma**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

O plug-in do Xamarin para o Visual Studio dá suporte apenas para um editor visual para os arquivos `Info.plist` e `Entitlement.plist`, portanto você precisará criar seu arquivo `iTunesMetadata.plist` em um editor de texto padrão e incluí-lo manualmente no seu projeto Xamarin.iOS.

Faça o seguinte:

1. Em um editor de texto sem formatação, crie um novo arquivo de texto e nomeie-o como `iTunesMetadata.plist`.
2. Copie o conteúdo de exemplo da seção [O Conteúdo do iTunesMetadata.plist](#iTunesMetadata_contents) acima.
3. Cole o conteúdo no arquivo e edite-o conforme necessário.
4. Salve o arquivo e retorne ao Visual Studio.
5. No **Gerenciador de Soluções**, clique com o botão direito do mouse no arquivo de projeto Xamarin.iOS e selecione **Adicionar** > **Arquivos Existentes...**.
6. Na caixa de diálogo Abrir Arquivo, selecione o arquivo `iTunesMetadata.plist` que você criou anteriormente e clique no botão **Abrir**.
7. Deixe a **Ação de Build** desse arquivo definida como **Nenhuma**.

-----

Posteriormente, selecione esse arquivo `iTunesMetadata.plist` enquanto se prepara para criar o IPA no IDE.

## <a name="summary"></a>Resumo

Esse artigo abordou o arquivo `iTunesMetadata.plist` que pode ser usado para informar o iTunes sobre um aplicativo iOS ad hoc entregue. Ele discutiu a chave padrão no arquivo plist e como criar e manter o arquivo no Visual Studio e no Visual Studio para Mac.

## <a name="related-links"></a>Links relacionados

- [Distribuição da App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Como configurar um aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicando na App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribuição interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuição Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Suporte a IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
