---
title: Implantando aplicativos watchOS na loja de aplicativos
description: Este documento descreve como implantar aplicativos watchOS criados com o Xamarin na loja de aplicativos. Ele analisa os perfis de provisionamento de distribuição e o iTunes Connect, além de fornecer algumas dicas de solução de problemas.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: a622684461bfe2e4a57b910288ee1f9afb54c694
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292248"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Implantando aplicativos watchOS na loja de aplicativos

> [!IMPORTANT]
> Lembre-se de examinar o [Guia de envio do kit de inspeção da Apple](https://developer.apple.com/app-store/watch/)e ver a seção de [solução de problemas](#troubleshooting) para quaisquer problemas que você possa ter.

- Verifique se você tem:
  - [**Perfis de provisionamento de distribuição**](#provisioning) criados para seus projetos.
  - O **destino de implantação** (`MinimumOSVersion`) para o aplicativo pai do IOS definido como **8,2** ou anterior (não há suporte para 8,3).

- No [**iTunes Connect**](#iTunes_Connect):

  - Crie sua entrada de aplicativo iOS (ou adicione uma **nova versão** a um aplicativo existente).
  - Adicione o ícone de inspeção e capturas de tela.

- Em seguida, no [Visual Studio para Mac](#xamarin_studio) (no momento, não há suporte para o Visual Studio):

  - Clique com o botão direito do mouse no aplicativo iOS e escolha **definir como projeto de inicialização**.
  - Altere para a configuração da **App Store** .
  - Use o recurso de **arquivamento** criar um arquivo morto de aplicativo.

- Por fim, alterne para o [Xcode 6.2 +](#xcode)

  - Vá para a **janela > organizador** e escolha **arquivos**.
  - Selecione o aplicativo e o arquivo morto na lista.
  - Opcionalmente **Validar...** o arquivo morto.
  - **Enviar...** o arquivo morto e siga as etapas para carregar no iTunes Connect para revisão e aprovação.

Leia dicas específicas relacionadas a esses itens abaixo. Consulte a seção de [solução de problemas](#troubleshooting) se você tiver problemas.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Perfis de provisionamento de distribuição

Para compilar para a implantação da loja de aplicativos, você precisa criar um **perfil de provisionamento de distribuição** para cada ID de aplicativo em sua solução.

Se você tiver uma ID de aplicativo curinga, *somente um perfil de provisionamento será necessário*; Mas se você tiver uma ID de aplicativo separada para cada projeto, precisará de um perfil de provisionamento para cada ID de aplicativo:

![](appstore-images/provisioningprofile-distribution-sml.png "The App Store Distribution profile")

Depois de criar todos os três perfis, eles aparecerão na lista. Lembre-se de baixar e instalar cada uma delas (clicando duas vezes nela):

![](appstore-images/provisioningprofiles-sml.png "The list of available profiles")

Você pode verificar o perfil de provisionamento nas opções do **projeto** selecionando a tela **Build > assinatura de pacote do IOS** e selecionando a configuração **AppStore | iPhone** .

A lista de **perfis de provisionamento** mostrará todos os perfis de correspondência – você deverá ver os perfis de correspondência criados nesta lista suspensa.

![](appstore-images/options-selectprofile-sml.png "The iOS Bundle Signing dialog")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Siga a [visão geral da distribuição do aplicativo](~/ios/deploy-test/app-distribution/index.md), em particular:

- [Configurar um aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicando na App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Ao configurar o aplicativo no iTunes Connect, não se esqueça de adicionar o ícone de inspeção e capturas de tela:

![](appstore-images/itunesconnect-watch-sml.png "The Watch icon and screenshots in iTunes Connect")

O arquivo de ícone deve ser 1024x1024 pixels e terá uma máscara circular aplicada a ele quando for exibido. O ícone não deve ter um canal alfa.

Pelo menos uma captura de tela é necessária, até cinco podem ser enviadas.
Eles devem ser 312x390 pixels e demonstrar seu aplicativo Watch em ação.
Você pode usar o simulador de inspeção do 42 mm para tirar capturas de tela neste tamanho.

<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Verifique se o aplicativo iOS é o projeto de inicialização. Caso contrário, clique com o botão direito do mouse para defini-lo:

   ![](appstore-images/xs-startup.png "Setting the startup project")

2. Escolha a configuração de Build do **AppStore** :

   ![](appstore-images/xs-appstore.png "The AppStore build configuration")

3. Escolha o item de menu **criar > arquivo morto** para iniciar o processo de arquivamento:

   ![](appstore-images/xs-archive.png "The Build menu")

Você também pode escolher o item de menu **exibir > arquivos mortos...** para ver os arquivos que foram criados anteriormente.

  ![](appstore-images/xs-archives-sml.png "The Archives view")

<a name="xcode" />

## <a name="xcode"></a>Xcode

O Xcode mostrará automaticamente os arquivos criados no Visual Studio para Mac.

1. Inicie o Xcode e escolha **windows > organizador**:

   ![](appstore-images/xc-organizer.png "The Window menu")

2. Alterne para a guia **arquivos mortos** e selecione o arquivo que foi criado com Visual Studio para Mac:

   ![](appstore-images/xc-archives.png "The Archives tab")

3. Opcionalmente, **valide...** o arquivo e, em seguida, escolha **Enviar...** para carregar o aplicativo no iTunes Connect.

4. Escolha a equipe de desenvolvimento (se você pertencer a mais de um) e, em seguida, confirme o envio:

   ![](appstore-images/xc-submit1.png "The development team section")

5. Visite o iTunes Connect novamente para ver o binário carregado. Vá para a página de configuração do aplicativo e escolha **pré-lançamento** no menu superior para ver a lista de **compilações** :

   [![](appstore-images/itc-prerelease-sml.png "The apps configuration page in iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

Em seguida, você pode enviar o aplicativo para aprovação na página **versões** . Consulte a [visão geral da distribuição de aplicativo do IOS](~/ios/deploy-test/app-distribution/index.md) para obter mais informações.

## <a name="troubleshooting"></a>solução de problemas

Aqui estão alguns erros que podem ser encontrados durante o envio à loja de aplicativos e as etapas que você pode executar para corrigi-los.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>A opção de menu arquivo morto não está visível no Visual Studio para Mac

Siga as [etapas acima](#xamarin_studio) para configurar a solução para arquivamento. Se você não puder definir o projeto de inicialização corretamente, verifique se a configuração da compilação está definida primeiro como depurar ou liberar antes de tentar alterar o projeto de inicialização. Em seguida, defina a configuração de Build de volta para **AppStore**.

### <a name="invalid-icon"></a>Ícone inválido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Siga as [instruções para remover o canal alfa](~/ios/watchos/troubleshooting.md) dos ícones.

### <a name="cfbundleversion-mismatch"></a>Incompatibilidade de CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Todos os projetos em sua solução – o aplicativo iOS, a extensão de inspeção e o aplicativo de inspeção devem estar usando o mesmo número de versão. Edite cada arquivo **info. plist** para que o número de versão corresponda exatamente.

### <a name="missing-icons"></a>Ícones ausentes

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Siga as instruções nos [ícones trabalhando com](~/ios/watchos/app-fundamentals/icons.md) para adicionar todas as imagens necessárias ao projeto de aplicativo Watch.

### <a name="missing-icon"></a>Ícone ausente

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Verifique se você tem a versão mais recente do Visual Studio para Mac e se o **appIcon. appiconset** contém um conjunto completo de imagens. Se você ainda estiver vendo esse erro, exiba a origem do **Contents. JSON** para confirmar que ele contém uma entrada para todas as imagens necessárias. Como alternativa, depois de ter garantido que você está usando a versão mais recente do Xamarin, exclua e recrie o **appIcon. appiconset**.

> [!IMPORTANT]
> Há um bug conhecido no ícone de observação do Visual Studio para Mac suporte: ele espera uma imagem de pixel 88x88 para a imagem de **29x29@3x** (que deve ser 87x87 pixels).

Não é possível corrigir isso em Visual Studio para Mac-edite o ativo da imagem no Xcode ou edite manualmente o arquivo **Contents. JSON** .

### <a name="invalid-watchkit-support"></a>Suporte a WatchKit inválido

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Essa mensagem pode aparecer durante a validação e o envio, ou em um email automatizado do iTunes Connect após um upload aparentemente bem-sucedido.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Você deve **arquivar** seu aplicativo no Visual Studio para Mac e, em seguida, alternar para o Xcode 6.2 + para validar e carregar no iTunes Connect.

Use o canal do Xamarin estável e o Xcode 6.2 +.

### <a name="invalid-provisioning-profile"></a>Perfil de provisionamento inválido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

Os **perfis de provisionamento de distribuição** devem ser fornecidos para todos os três projetos em uma solução de aplicativo de inspeção: o aplicativo IOS, a extensão de inspeção e o aplicativo de inspeção, explicitamente (três perfis) ou por meio de um único perfil curinga. Verifique se os perfis de provisionamento existem no centro de desenvolvimento do iOS e se você baixou e adicionou-os ao seu Mac.

### <a name="invalid-code-signing-entitlements"></a>Direitos de assinatura de código inválidos

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Verifique se os perfis de provisionamento estão configurados corretamente no centro de desenvolvimento da Apple e se você os baixou e instalou. Verifique também se eles estão definidos na janela Propriedades do Visual Studio para Mac para cada projeto.

### <a name="invalid-architecture"></a>Arquitetura inválida

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Você só pode adicionar aplicativos [de inspeção API unificada (64 bits)](~/cross-platform/macios/unified/index.md) Xamarin. Ios.
Clique com o botão direito do mouse no projeto do aplicativo iOS e vá para **opções > compilar > Build do ios > guia Avançado** e verifique se as **arquiteturas com suporte** para a configuração do AppStore-iPhone incluem **ARM64** (por exemplo, **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Este pacote é inválido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

Seu aplicativo do iOS pai deve ter o MinimumOSVersion definido como ' 8,2 ' ou anterior.

### <a name="non-public-api-usage"></a>Uso de API não pública

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Verifique se você está usando a versão mais recente das ferramentas do Xcode e do Xamarin.
Seu código não deve acessar nenhuma API não-pública.

### <a name="build-error-mt5309"></a>Erro de compilação MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Esse erro é provavelmente o resultado de sua renomeação da instalação do Xcode do **Xcode. app**. Por exemplo, esse erro ocorrerá se você renomear a instalação para o **Xcode 6.2. app**.

## <a name="related-links"></a>Links relacionados

- [Guia de envio do Apple WatchKit](https://developer.apple.com/app-store/watch/)
