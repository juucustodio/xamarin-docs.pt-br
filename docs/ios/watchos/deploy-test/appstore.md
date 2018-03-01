---
title: Implantando a loja de aplicativos
description: "Implantação de aplicativos de inspeção para a loja de aplicativos"
ms.topic: article
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: eed12c84b9952ef5c3dd27847071f05392bc16c4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="deploying-to-the-app-store"></a>Implantando a loja de aplicativos

> [!IMPORTANT]
>  Certifique-se de examinar [Watch Kit envio guia da Apple](https://developer.apple.com/app-store/watch/)e consulte o [solução de problemas](#Troubleshooting) seção problemas que você pode ter.

- Certifique-se de que você tem:
  - [**Perfis de provisionamento de distribuição** ](#provisioning) criado para seus projetos.
  - O **destino de implantação** (`MinimumOSVersion`) para os iOS pai definido de aplicativo como **8.2** ou anterior (não há suporte para 8.3).

- Em [ **iTunes conectar**](#iTunes_Connect):

  - Criar aplicativo de entrada de seu iOS (ou adicionar um **nova versão** para um aplicativo existente).
  - Adicione capturas de tela e o ícone de observação.

- Em seguida, em [Visual Studio para Mac](#xamarin_studio) (Visual Studio não é suportado atualmente):

  - Clique com botão direito do aplicativo do iOS e escolha **definir como projeto de inicialização**.
  - Altere para o **App Store** configuração.
  - Use o **arquivamento** recurso criar um arquivo de aplicativo.

- Por fim, alterne para [Xcode 6.2 +](#xcode)

  - Vá para o **Janela > organizador** e escolha **arquivos**.
  - Selecione o aplicativo e o arquivo da lista.
  - (Opcionalmente) **Validar...**  o arquivo morto.
  - **Enviar...**  o arquivo e siga as etapas para carregar iTunes conectar-se para revisão e aprovação.

Leia as dicas específicas relacionadas a esses itens a seguir. Consulte o [solução de problemas](#Troubleshooting) seção se você tiver problemas.

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>Perfis de provisionamento de distribuição

Para compilar para implantação de loja de aplicativos, você precisa criar um **perfil de provisionamento de distribuição** para cada ID de aplicativo em sua solução.

Se você tiver um curinga ID do aplicativo, *somente um perfil de provisionamento será necessário*; mas se você tem uma ID de aplicativo separado para cada projeto, você precisará de um perfil de provisionamento para cada ID do aplicativo:

![](appstore-images/provisioningprofile-distribution-sml.png "O perfil de distribuição de loja de aplicativo")

Depois que você tenha criado todos os três perfis, elas aparecerão na lista. Lembre-se de baixar e instalar cada um deles (clicando duas vezes nele):

![](appstore-images/provisioningprofiles-sml.png "A lista de perfis disponíveis")

Você pode verificar o perfil de provisionamento no **opções de projeto** selecionando o **Build > iOS de assinatura de pacote** tela e selecionando o **AppStore | iPhone** configuração.

O **perfil de provisionamento de** lista mostrará todos os perfis de correspondência – você deve ver os perfis de correspondência que você criou na lista suspensa.

![](appstore-images/options-selectprofile-sml.png "A caixa de diálogo de assinatura de pacote do iOS")

## <a name="itunes-connect"></a>conectar-se de iTunes

Siga o [visão geral de distribuição do aplicativo](~/ios/deploy-test/app-distribution/index.md), em particular:

- [Configurar um aplicativo na iTunes conectar](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicando na App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

Ao configurar o aplicativo na iTunes conectar, não se esqueça de adicionar o ícone de observação e capturas de tela:

![](appstore-images/itunesconnect-watch-sml.png "O ícone de observação e capturas de tela no iTunes conectar")

O arquivo de ícone deve ter 1024 x 1024 pixels e terá uma máscara circular aplicada a ele quando ele for exibido. O ícone não deve ter um canal alfa.

Captura de tela de pelo menos um é necessária, até cinco pode ser enviada.
Que eles devem ser 312 x 390 pixels e demonstrar o seu aplicativo de inspeção em ação.
Você pode usar o simulador de inspeção de 42mm fazer capturas de tela nesse tamanho.


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. Certifique-se de que o aplicativo do iOS é o projeto de inicialização. Caso contrário, clique com botão direito para configurá-lo:

  ![](appstore-images/xs-startup.png "Configurando o projeto de inicialização")

2. Escolha o **AppStore** configuração de compilação:

  ![](appstore-images/xs-appstore.png "A configuração de compilação AppStore")

3. Escolha o **Build > arquivamento** item de menu para iniciar o processo de arquivamento:

  ![](appstore-images/xs-archive.png "O menu Build")

Você também pode escolher o **exibição > arquivos...**  item de menu para ver os arquivos que foram criados anteriormente.

  ![](appstore-images/xs-archives-sml.png "O modo de exibição de arquivos")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode mostrará automaticamente os arquivos criados no Visual Studio para Mac.

1. Inicie o Xcode e escolha **Janela > organizador**:

  ![](appstore-images/xc-organizer.png "Menu janela")

2. Alterne para o **arquivos** guia e selecione o arquivo que foi criado com o Visual Studio para Mac:

  ![](appstore-images/xc-archives.png "A guia arquivos")

3. Opcionalmente, **validar...**  o arquivo, em seguida, escolha **enviar...**  para carregar o aplicativo ao conectar-se de iTunes.

4. Escolha a equipe de desenvolvimento (se você pertencer a mais de uma) e, em seguida, confirme o envio:

  ![](appstore-images/xc-submit1.png "A seção da equipe de desenvolvimento")

5. Visite iTunes conectar-se novamente para ver o binário carregado. Acesse a página de configuração do aplicativo e escolha **pré-lançamento** no menu superior, para ver o **cria** lista:

  [ ![](appstore-images/itc-prerelease-sml.png "Página de configuração de aplicativos do iTunes conectar")](appstore-images/itc-prerelease.png)

Em seguida, você pode enviar o aplicativo para aprovação no **versões** página. Consulte o [visão geral de distribuição de aplicativo do iOS](~/ios/deploy-test/app-distribution/index.md) para obter mais informações.


## <a name="troubleshooting"></a>Solução de problemas

Aqui estão alguns erros encontrados ao enviar para a loja de aplicativos e as etapas que você pode tomar para corrigi-los.

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>A opção de menu Arquivo não estiver visível no Visual Studio para Mac

Siga o [etapas acima](#xamarin_studio) para configurar a solução para arquivamento. Se você não pode definir o projeto de inicialização corretamente, certifique-se de que a configuração de compilação é primeiro definida como depurar ou versão antes de tentar alterar o projeto de inicialização. Em seguida, definir a configuração de compilação de volta para **AppStore**.

### <a name="invalid-icon"></a>Ícone inválido

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcons27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

Siga o [instruções para remover o canal alfa](~/ios/watchos/troubleshooting.md) nos seus ícones.

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion Mismatch

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

Todos os projetos em sua solução, o aplicativo do iOS, a extensão de inspeção e o aplicativo Watch - devem usar o mesmo número de versão. Editar cada **Info. plist** arquivos de forma que o número de versão com correspondência exata.

### <a name="missing-icons"></a>Ícones ausentes

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

Siga as instruções de [trabalhando com ícones](~/ios/watchos/app-fundamentals/icons.md) para adicionar todas as imagens ao projeto de aplicativo de inspeção.

### <a name="missing-icon"></a>Ícone ausente

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

Verifique se você tem a versão mais recente do Visual Studio para Mac e que sua **AppIcons.appiconset** contém um conjunto completo de imagens. Se você ainda estiver vendo este erro, exiba a origem do **Contents.json** para confirmar que ele contém uma entrada para todas as imagens. Como alternativa, depois que você garantiu que você está usando a versão mais recente do Xamarin, exclua e recrie o **AppIcons.appiconset**.

> [!IMPORTANT]
> Observação: Há um bug conhecido no Visual Studio para suporte de ícone de observação do Mac: ele espera uma imagem de pixel de 88 x 88 o  **29x29@3x**  imagem (que deve ser 87 x 87 pixels).


Você não pode corrigir isso no Visual Studio para Mac - editar o ativo de imagem no Xcode ou editar manualmente o **Contents.json** arquivo (para corresponder [Este exemplo](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)).



### <a name="invalid-watchkit-support"></a>Suporte de WatchKit inválido

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

Essa mensagem pode aparecer durante a validação e o envio ou em um email automatizado do iTunes conectar após um carregamento aparentemente bem-sucedida.
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> Observação: Você deve **arquivamento** seu aplicativo no Visual Studio para Mac e, em seguida, alternar para o Xcode 6.2 + validar e carregar para conectar-se de iTunes.


Use o canal Xamarin estável e Xcode 6.2 +.



### <a name="invalid-provisioning-profile"></a>Perfil de provisionamento inválido

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**Perfis de provisionamento de distribuição** devem ser fornecidos para todos os três projetos em uma solução de aplicativo de inspeção: o aplicativo do iOS, a extensão de inspeção e o aplicativo Watch - o explicitamente (três perfis) ou por meio de um perfil de curinga. Verifique a existam de perfis de provisionamento no Centro de desenvolvimento do iOS e que você baixou e adicioná-las ao seu Mac.

### <a name="invalid-code-signing-entitlements"></a>Direitos de assinatura de código inválido

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

Certifique-se de perfis de provisionamento são configuração corretamente no Centro de desenvolvimento da Apple, e que você baixou e instalou-los. Além disso, verifique que são definidos no Visual Studio para a janela de propriedades do Mac para cada projeto.

### <a name="invalid-architecture"></a>Arquitetura inválida

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

Você só pode adicionar inspeção aplicativos [API unificada (64 bits)](~/cross-platform/macios/unified/index.md) xamarin aplicativos.
Clique com botão direito no projeto de aplicativo do iOS, em seguida, vá para **opções > compilar > iOS compilação > guia Avançado** e certifique-se de que o **arquiteturas com suporte** para iPhone AppStore configuração inclui **ARM64** (por exemplo. **ARMv7 + ARM64**).

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

Certifique-se de que você está usando a versão mais recente de ferramentas Xcode e do Xamarin.
Seu código não deve acessar quaisquer APIs não público.

### <a name="build-error-mt5309"></a>MT5309 de erro de compilação

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

Esse erro geralmente é o resultado de sua necessidade renomeados sua instalação Xcode de **Xcode.app**. Por exemplo, este erro ocorrerá se você renomear a instalação para **6.2.app XCode**.



## <a name="related-links"></a>Links relacionados

- [Guia da Apple WatchKit envio](https://developer.apple.com/app-store/watch/)
