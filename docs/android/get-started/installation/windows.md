---
title: "Instalação do Windows"
description: Este guia descreve as etapas para instalar o Xamarin.Android para Visual Studio no Windows e explica como configurar o Xamarin.Android para compilar seu primeiro aplicativo Xamarin.Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 10cfb790fca30b67faaa8f7720a358ec34c25663
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="windows-installation"></a>Instalação do Windows

_Este guia descreve as etapas para instalar o Xamarin.Android para Visual Studio no Windows e explica como configurar o Xamarin.Android para compilar seu primeiro aplicativo Xamarin.Android._

<a name="overview" />

## <a name="overview"></a>Visão geral

Como o Xamarin agora está incluído em todas as edições do Visual Studio sem custos adicionais e sem precisar de uma licença separada, você pode usar o instalador do Visual Studio para baixar e instalar ferramentas Xamarin.Android.
(As etapas manuais de instalação e licenciamento que eram necessárias em versões anteriores do Xamarin.Android deixaram de ser necessárias.) Neste guia, você aprenderá o seguinte:

-   Como configurar locais personalizados para o Java Development Kit, o SDK do Android e o NDK do Android.

-   Como iniciar o Gerenciador de SDK do Android para baixar e instalar componentes adicionais do SDK do Android.

-   Como preparar um emulador ou um dispositivo Android para depuração e teste.

-   Como criar seu primeiro projeto de aplicativo do Xamarin.Android.

No final deste guia, você terá uma instalação funcional do Xamarin.Android integrada ao Visual Studio e estará pronto para começar a compilar seu primeiro aplicativo Xamarin.Android.

## <a name="installation"></a>Instalação

Para obter informações detalhadas sobre como instalar o Xamarin para ser usado com o Visual Studio no Windows, consulte o guia de [Instalação do Windows](~/cross-platform/get-started/installation/windows.md).

<a name="configuration" />

## <a name="configuration"></a>Configuração

O Xamarin.Android usa o JDK (Java Development Kit) e o SDK do Android para criar aplicativos. Durante a instalação, o instalador do Visual Studio coloca essas ferramentas em seus locais padrão e configura o ambiente de desenvolvimento com a configuração de caminho apropriada. Você pode exibir e alterar esses locais clicando em **Ferramentas > Opções > Xamarin > Configurações do Android**:

![Captura de tela da caixa de diálogo de configurações do Xamarin Android](windows-images/07-settings.png)

Para a maioria dos usuários, os locais padrão funcionam sem que sejam feitas mais alterações. No entanto, você pode configurar o Visual Studio com locais personalizados para essas ferramentas (por exemplo, se tiver instalado o JDK do Java, o NDK ou o SDK do Android em um local diferente). Clique em **Alterar** ao lado do caminho que deseja alterar e, em seguida, navegue até o novo local.

O Xamarin.Android usa o [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), que é necessário quando você está desenvolvendo para o nível de API 24 ou superior (o JDK 8 também é compatível com níveis de API anteriores ao 24). Você poderá continuar usando o [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

> [!IMPORTANT]
> **Observação:** o Xamarin.Android não é compatível com o JDK 9.

<a name="sdk_manager" />

### <a name="android-sdk-manager"></a>Gerenciador de SDK do Android

O Android usa várias configurações de nível da API do Android para determinar a compatibilidade de seu aplicativo com as diferentes versões do Android (para obter mais informações sobre os níveis da API do Android, consulte as [Noções básicas sobre os níveis da API do Android](~/android/app-fundamentals/android-api-levels.md)).
Dependendo dos níveis da API do Android que quiser ter como destino, você precisará baixar e instalar componentes adicionais do SDK do Android. Além disso, talvez você precise instalar ferramentas opcionais e imagens do emulador fornecidas no SDK do Android. Para fazer isso, use o **Gerenciador de SDK do Android**. É possível iniciar o **Gerenciador de SDK do Android** clicando em **Ferramentas > Android > Gerenciador de SDK do Android**:

[![Como iniciar o Gerenciador de SDK do Android](windows-images/08-sdk-manager-sml.png)](windows-images/08-sdk-manager.png)

Por padrão, o Visual Studio instala o Gerenciador de SDK do Android do Google:

![Captura de tela de exemplo do Gerenciador de SDK do Android do Google](windows-images/09-google-sdk-manager.png)

Você pode usar o Gerenciador de SDK do Android do Google para instalar as versões do pacote de Ferramentas do SDK do Android que vão até a versão 25.2.3. No entanto, se precisar usar uma versão posterior do pacote de Ferramentas do SDK do Android, você precisará instalar o plug-in do Gerenciador de SDK do Android Xamarin para Visual Studio (disponível no Visual Studio Marketplace). Isso é necessário porque o Gerenciador de SDK autônomo do Google foi preterido na versão 25.2.3 do pacote de Ferramentas do SDK do Android. 

Para saber mais sobre o uso do Gerenciador de SDK do Android do Xamarin, consulte [Instalação do SDK do Android](~/android/get-started/installation/android-sdk.md).

<a name="emulator" />

### <a name="android-emulator"></a>Emulador do Android

Se não tem um dispositivo Android físico para usar em testes, você pode usar um emulador do Android para testar seu aplicativo. Para saber mais sobre o Android Emulator do Google, consulte [Emulador do SDK do Android](~/android/deploy-test/debugging/android-sdk-emulator/index.md).

O emulador de Android do Google usa o HAXM (Hardware Accelerated Execution Manager) da Intel, que pode entrar em conflito com as tecnologias de virtualização usadas por outros emuladores. As três principais tecnologias de virtualização são:

-   **Hyper-V** (usado pelo Emulador do Visual Studio para Android e pelo emulador do Windows Phone) 

-   **Virtual Box** (usado pelo Genymotion)

-   **Intel HAXM** (usado pelo emulador do SDK do Android do Google) 

Como a CPU de um computador de desenvolvimento dá suporte a apenas uma tecnologia de virtualização por vez, é melhor ter apenas uma delas em uso no computador de desenvolvimento.

<a name="device" />

### <a name="android-device"></a>Dispositivo Android

Se você tem um dispositivo Android físico para usar em testes, este é um bom momento para configurá-lo para uso de desenvolvimento. Consulte [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md) para configurar seu dispositivo Android para desenvolvimento e, em seguida, conecte-o a seu computador para executar e depurar aplicativos Xamarin.Android.


<a name="create_app" />

## <a name="create-an-application"></a>Criar um Aplicativo

Agora que instalou o Xamarin.Android, você pode iniciar o Visual Studio para criar um novo projeto. Clique em **Arquivo > Novo > Projeto** para começar a criar seu aplicativo:

![Como criar um novo projeto](windows-images/10-new-project.png)

Na caixa de diálogo **Novo Projeto**, selecione **Android** em **Modelos** e clique em **Aplicativo em Branco (Android)** no painel à direita. Insira um nome para o aplicativo (na captura de tela abaixo, o aplicativo se chama **MyApp**) e clique em **OK**:

[![Captura de tela da caixa de diálogo Novo Projeto criando um aplicativo Android em branco](windows-images/11-first-app-sml.png)](windows-images/11-first-app.png)

É só isso! Agora, você está pronto para usar o Xamarin.Android para criar aplicativos Android!


<a name="summary" />

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu a configurar e instalar a plataforma Xamarin.Android no Windows, a configurar o Visual Studio (opcionalmente) com locais de instalação do JDK do Java e do SDK do Android personalizados, a iniciar o Gerenciador de SDK para instalar componentes adicionais do SDK do Android, a configurar um emulador ou um dispositivo Android e a começar a criar seu primeiro aplicativo.

A próxima etapa é dar uma olhada nos tutoriais [Olá, Android](~/android/get-started/hello-android/index.md) para aprender a criar um aplicativo Xamarin.Android funcional.


## <a name="related-links"></a>Links relacionados

- [Baixar o Visual Studio](https://www.visualstudio.com/vs/)
- [Instalar as Ferramentas do Visual Studio para Xamarin](~/cross-platform/get-started/installation/windows.md)
- [Requisitos do sistema](~/cross-platform/get-started/requirements.md)
- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Emulador do SDK do Android](~/android/get-started/installation/android-emulator/index.md)
- [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md)
