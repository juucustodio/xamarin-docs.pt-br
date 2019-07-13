---
title: Implantando aplicativos do watchOS para a App Store
description: Este documento descreve como implantar aplicativos watchOS criados com o Xamarin para a App Store. Ele examina o iTunes Connect e perfis de provisionamento de distribuição, e ele também fornece algumas dicas de solução de problemas.
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 58e3593dc09c76439a3e128e51f354c169d7e72e
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865976"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>Implantando aplicativos do watchOS para a App Store

> [!IMPORTANT]
> Certifique-se de examinar [guia de envio do Kit da Apple Watch](https://developer.apple.com/app-store/watch/)e consulte o [solução de problemas](#troubleshooting) seção para quaisquer problemas que você pode ter.

- Verifique se que você tem:
  - [**Perfis de provisionamento de distribuição** ](#provisioning) criado para seus projetos.
  - O **destino de implantação** (`MinimumOSVersion`) para o iOS pai aplicativo definido como **8.2** ou anterior (não há suporte para 8.3).

- Na [ **iTunes Connect**](#iTunes_Connect):

  - Criar seu iOS entrada de aplicativo (ou adicione uma **nova versão** a um aplicativo existente).
  - Adicione ícone de inspeção e capturas de tela.

- Em seguida, na [Visual Studio para Mac](#xamarin_studio) (Visual Studio não é suportado):

  - O aplicativo do iOS com o botão direito e escolha **definir como projeto de inicialização**.
  - Altere para o **App Store** configuração.
  - Use o **arquivamento** recurso de criar um arquivo morto de aplicativo.

- Por fim, alterne para [Xcode 6.2 +](#xcode)

  - Vá para o **Janela > organizador** e escolha **arquivos mortos**.
  - Selecione o aplicativo e o arquivamento na lista.
  - (Opcionalmente) **Validar...**  o arquivo morto.
  - **Enviar...**  o arquivo morto e siga as etapas para carregar para o iTunes Connect para revisão e aprovação.

Leia as dicas específicas relacionadas a esses itens a seguir. Consulte a [solução de problemas](#troubleshooting) seção se você tiver problemas.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Perfis de provisionamento de distribuição

Para compilar para implantação de App Store, você precisa criar uma **perfil de provisionamento de distribuição** para cada ID do aplicativo em sua solução.

Se você tiver um ID do aplicativo curinga *somente um perfil de provisionamento será necessário*; mas se você tiver uma ID de aplicativo separada para cada projeto, você precisará de um perfil de provisionamento para cada ID do aplicativo:

![](appstore-images/provisioningprofile-distribution-sml.png "O perfil de distribuição de Store de aplicativo")

Depois de criar todos os três perfis, eles aparecerão na lista. Lembre-se baixar e instalar cada um deles (clicando duas vezes nele):

![](appstore-images/provisioningprofiles-sml.png "A lista de perfis disponíveis")

Você pode verificar se o perfil de provisionamento na **opções de projeto** selecionando o **compilar > assinatura do pacote iOS** tela e selecionando o **AppStore | iPhone** configuração.

O **perfil de provisionamento** lista mostrará todos os perfis de correspondência – você deverá ver os perfis de correspondência que você criou na lista suspensa.

![](appstore-images/options-selectprofile-sml.png "A caixa de diálogo de assinatura do pacote do iOS")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

Siga as [visão geral da distribuição de aplicativo](~/ios/deploy-test/app-distribution/index.md), em particular:

- [Configurar um aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicando na App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Ao configurar o aplicativo no iTunes Connect, não se esqueça de adicionar o ícone de inspeção e capturas de tela:

![](appstore-images/itunesconnect-watch-sml.png "O ícone de inspeção e capturas de tela no iTunes Connect")

O arquivo de ícone deve ser 1024 x 1024 pixels e terá uma máscara circular aplicada a ele quando ele for exibido. O ícone não deve ter um canal alfa.

Captura de tela de pelo menos um é necessária, até cinco pode ser enviada.
Eles devem ser 312 x 390 pixels e demonstrar seu aplicativo de inspeção em ação.
Você pode usar o simulador de inspeção de 42mm fazer capturas de tela nesse tamanho.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Certifique-se de que o aplicativo iOS é o projeto de inicialização. Caso contrário, clique com botão direito para configurá-lo:

   ![](appstore-images/xs-startup.png "Definindo o projeto de inicialização")

2. Escolha o **AppStore** configuração de compilação:

   ![](appstore-images/xs-appstore.png "A configuração de build da loja de aplicativos")

3. Escolha o **compilar > arquivo morto** item de menu para iniciar o processo de arquivamento:

   ![](appstore-images/xs-archive.png "O menu Build")

Você também pode escolher o **exibição > arquivos mortos...**  item de menu para ver os arquivos que foram criados anteriormente.

  ![](appstore-images/xs-archives-sml.png "O modo de exibição de arquivos mortos")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode mostrará automaticamente os arquivos criados no Visual Studio para Mac.

1. Inicie o Xcode e escolha **Janela > organizador**:

   ![](appstore-images/xc-organizer.png "Menu janela")

2. Alterne para o **arquivos mortos** guia e selecione o arquivo que foi criado com o Visual Studio para Mac:

   ![](appstore-images/xc-archives.png "A guia arquivos")

3. Opcionalmente, **validar...**  o arquivo morto, em seguida, escolha **enviar...**  para carregar o aplicativo no iTunes Connect.

4. Escolha a equipe de desenvolvimento (se você pertencer a mais de uma) e, em seguida, confirme se o envio:

   ![](appstore-images/xc-submit1.png "A seção da equipe de desenvolvimento")

5. Visite o iTunes Connect novamente para ver o binário carregado. Acesse a página de configuração do seu aplicativo e escolha **pré-lançamento** no menu superior para ver as **compilações** lista:

   [![](appstore-images/itc-prerelease-sml.png "A página de configuração de aplicativos no iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

Em seguida, você pode enviar o aplicativo de aprovação na **versões** página. Consulte a [visão geral da distribuição de aplicativo do iOS](~/ios/deploy-test/app-distribution/index.md) para obter mais informações.


## <a name="troubleshooting"></a>Solução de problemas

Aqui estão alguns erros que você pode encontrar durante o envio para a App Store e as etapas que você pode tomar para corrigi-los.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>A opção de menu Arquivo não estiver visível no Visual Studio para Mac

Siga as [etapas acima](#xamarin_studio) para configurar a solução de arquivamento. Se você não pode definir o projeto de inicialização corretamente, verifique se que a configuração de compilação é definida primeiro ao depurar ou versão antes de tentar alterar o projeto de inicialização. Em seguida, defina a configuração de build de volta para **AppStore**.

### <a name="invalid-icon"></a>Ícone inválido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Siga as [instruções para remover o canal alfa](~/ios/watchos/troubleshooting.md) de seus ícones.

### <a name="cfbundleversion-mismatch"></a>Incompatibilidade de CFBundleVersion

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Todos os projetos em sua solução de aplicativo do iOS, a extensão de inspeção e o aplicativo Watch - devem estar usando o mesmo número de versão. Edite cada **Info. plist** arquivo para que o número de versão corresponde exatamente.

### <a name="missing-icons"></a>Ícones ausentes

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Siga as instruções na [trabalhando com ícones](~/ios/watchos/app-fundamentals/icons.md) para adicionar as imagens necessárias para o projeto de aplicativo de inspeção.

### <a name="missing-icon"></a>Ícone ausente

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Verifique se você tem a versão mais recente do Visual Studio para Mac e que seu **AppIcon.appiconset** contém um conjunto completo de imagens. Se você ainda estiver vendo este erro, exiba a origem do **Contents.json** para confirmar se ele contém uma entrada para todas as imagens. Como alternativa, depois que você tiver assegurado que você está usando a versão mais recente do Xamarin, excluir e recriar a **AppIcon.appiconset**.

> [!IMPORTANT]
> Há um bug conhecido no Visual Studio para suporte de ícone de inspeção do Mac: ele espera que uma imagem de pixel de 88 x 88 para o **29x29@3x** imagem (que deve ser 87 x 87 pixels).


Você não pode corrigir isso no Visual Studio para Mac - editar o ativo de imagem no Xcode ou editar manualmente o **Contents.json** arquivo (para corresponder à [Este exemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Suporte de WatchKit inválido

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Essa mensagem pode aparecer durante a validação e o envio ou em um email automatizado do iTunes Connect após um upload bem-sucedido aparentemente.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Você deve **arquivamento** seu aplicativo no Visual Studio para Mac e, em seguida, alterne para o Xcode 6.2 + validar e carregar para o iTunes Connect.


Use o canal estável Xamarin e o Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Perfil de provisionamento inválido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**Perfis de provisionamento de distribuição** deve ser fornecido para todos os três projetos em uma solução de aplicativo de inspeção: o aplicativo do iOS, a extensão de inspeção e o aplicativo Watch - o explicitamente (três perfis) ou por meio de um perfil curinga. Verifique se os perfis de provisionamento existem no Centro de desenvolvimento do iOS e que você tenha baixado e adicioná-las ao seu Mac.

### <a name="invalid-code-signing-entitlements"></a>Direitos de assinatura de código inválido

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Certifique-se de perfis de provisionamento está configurada corretamente no Centro de desenvolvimento da Apple, e que você baixou e instalou-los. Verifique também o que são definidos no Visual Studio para a janela de propriedades do Mac para cada projeto.

### <a name="invalid-architecture"></a>Arquitetura inválida

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Você só pode adicionar aplicativos do Watch [API unificada (64 bits)](~/cross-platform/macios/unified/index.md) aplicativos xamarin. IOS.
Clique com botão direito no projeto de aplicativo do iOS e em seguida, vá para **opções > Build > Build do iOS > guia Avançado** e certifique-se de que o **arquiteturas com suporte** para iPhone AppStore configuração inclui **ARM64** (por exemplo. **ARMv7 + ARM64**).

### <a name="this-bundle-is-invalid"></a>Este pacote é inválido.

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

Seu aplicativo do iOS pai deve ter o MinimumOSVersion definido como '8.2' ou anterior.

### <a name="non-public-api-usage"></a>Uso da API não públicos

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

Verifique se que você estiver usando a versão mais recente das ferramentas do Xamarin e do Xcode.
Seu código não deve acessar quaisquer APIs não públicos.

### <a name="build-error-mt5309"></a>Compilar erro MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Esse erro provavelmente é o resultado da sua tendo renomeado a instalação do Xcode partir **Xcode. App**. Por exemplo, este erro ocorrerá se você renomear sua instalação **XCode 6.2.app**.



## <a name="related-links"></a>Links relacionados

- [Guia de envio de WatchKit da Apple](https://developer.apple.com/app-store/watch/)
