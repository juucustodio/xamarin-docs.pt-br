---
title: Trabalhando com capacidades
description: A adição de capacidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para todas as capacidades.
ms.topic: article
ms.prod: xamarin
ms.assetid: 98A4676F-992B-4593-8D38-6EEB2EB0801C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: ffc694e2f513d61cf32e94d8dc3bae17c887521b
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-capabilities"></a>Trabalhando com capacidades

_A adição de funcionalidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para todas as funcionalidades._

A Apple fornece _capacidades_ aos desenvolvedores, geralmente conhecidas como _serviços de aplicativos_, como uma forma de estender as funcionalidades e ampliar o escopo daquilo que os aplicativos iOS podem fazer. As capacidades permitem que os desenvolvedores adicionem uma integração mais profunda dos recursos de plataforma em seus aplicativo, como: a capacidade de realizar transações monetárias iniciadas no aplicativo, serviços de dispositivo adicionais como Siri e muito mais.
Essas capacidades podem ser usadas com projetos Xamarin.iOS. A lista completa de serviços está descrita abaixo:

* Grupos de Aplicativos
* Domínios Associados
* Proteção de Dados
* Game Center
* HealthKit
* HomeKit
* Configuração de Acessório sem Fio
* iCloud
* Compras no Aplicativo
* Áudio entre aplicativos
* Apple Pay
* Carteira
* Notificação por Push
* VPN pessoal
* Siri
* Mapas
* Modos em tela de fundo
* Compartilhamento de conjunto de chaves
* Extensões de rede
* Configuração de hotspot
* Múltiplos caminhos
* Leitura da marca NFC


As funcionalidades podem ser habilitadas por meio do Visual Studio para Mac ou manualmente no Portal do Desenvolvedor da Apple. Determinadas capacidades, como Wallet, Apple Pay e iCloud, exigem a configuração adicional das IDs do aplicativo.

Este guia explica como habilitar cada um desses Serviços de Aplicativos em seu aplicativo no Visual Studio para Mac e manualmente por meio da central de desenvolvedores, incluindo qualquer configuração adicional que possa ser necessária. 

## <a name="adding-app-services"></a>Adicionando Serviços de Aplicativos

Para usar capacidades, o aplicativo deve ter um perfil de provisionamento válido que contenha uma ID do aplicativo com o serviço correto habilitado. A criação deste perfil de provisionamento pode ser realizada automaticamente no Visual Studio para Mac ou manualmente na Central de Desenvolvedores da Apple.

Esta seção explica como usar o provisionamento automático do Visual Studio para Mac ou a Central de Desenvolvedores para habilitar a maioria das funcionalidades. Há algumas capacidades, como Wallet, iCloud, Apple Pay e Grupos de Aplicativos que exigem uma configuração adicional. Elas são explicadas em detalhes nos guias adjacentes.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

> [!IMPORTANT]
> Nem todas as funcionalidades podem ser adicionadas e gerenciadas no Visual Studio para Mac. A lista a seguir contém os recursos compatíveis:
>
>* HealthKit 
>* HomeKit 
>* VPN pessoal 
>* Configuração de Acessório sem Fio 
>* Áudio entre aplicativos 
>* SiriKit 
>* Ponto de acesso 
>* Extensões de rede 
>* Leitura da marca NFC
>* Multipath 
>
>No momento, as funcionalidades Notificações por push, Game Center, Compra no aplicativo, Mapas, Compartilhamento de conjunto de chaves, Domínios associados e Proteção de dados não são compatíveis. Para adicionar essas funcionalidades, use o provisionamento manual e siga as etapas na seção [Central de Desenvolvedores](#devcenter).


Os recursos são adicionados ao **Entitlements.plist** no Visual Studio para Mac. Para adicionar funcionalidades, siga as etapas abaixo:

1. Abra o arquivo **Info.plist** do seu aplicativo iOS e verifique se **Gerenciar assinatura automaticamente** está selecionado. Siga as etapas no guia [Provisionamento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) se precisar de ajuda:

    ![Opção Gerenciar assinatura automaticamente](images/manage-signing.png)

2. Abra o arquivo **Entitlements.plist** e selecione a funcionalidade que deseja adicionar:

    ![Adicionar funcionalidades ao arquivo entitlements.plist](images/image17.png)

    Selecionar uma funcionalidade faz duas coisas:
    * Adiciona esse recurso à ID do aplicativo
    * Adiciona o par chave-valor do direito ao arquivo Entitlements.plist.

    O Visual Studio para Mac informará quando essas tarefas tiverem sido realizadas exibindo a seguinte mensagem de êxito:

    ![Adicionar funcionalidades ao arquivo entitlements.plist](images/image18.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Como no momento não há suporte para o provisionamento automático no Visual Studio 2017, use a [Central de Desenvolvedores](#devcenter) para criar um ID de aplicativo com os serviços de aplicativo corretos.

-----

<!--
<a name="xcode" />

## Xcode

Xamarin developers can also use Xcode to quickly create a provisioning profile with a suitable App ID. This process, described below, can be used for any app service in the list:

1.  Open Xcode and create a ‘dummy’ project. Give the dummy project the same name as your Xamarin.iOS project. The bundle identifier should be identical to the bundle identifier of your Xamarin.iOS project:

    ![Xcode Create Project](images/image1.png)

2.  Ensure **Automatically manage signing** is selected:

    ![Automatically manage signing selection](images/image2.png)

3.  Once the app has been created, go to the tab named **Capabilities**:

    ![Xcode Capabilities tab](images/image3.png)

4.  Browse to the capability that you wish to add, and move the switch to the **ON** position.
5.  This will create a provisioning profile with an App ID that contains the capability and adds the entitlement to the profile.
6.  In Visual Studio for Mac / Visual Studio, browse to **Project Options > Bundle Signing** and set the provisioning profile to the one that was just created in Xcode:

    ![Visual Studio for Mac Project Options](images/image4.png)
-->

<a name="devcenter" />

## <a name="developer-center"></a>Developer Center

Usar a central de desenvolvedores é um processo de duas etapas que requer a criação de uma ID do aplicativo e o uso dessa ID do aplicativo para criar um perfil de provisionamento. Essas etapas são detalhadas abaixo.

### <a name="creating-an-app-id-with-an-app-service"></a>Criando uma ID do aplicativo com um serviço de aplicativo

1.  Procure a [Central de desenvolvedores da Apple](https://developer.apple.com/account) em um Mac (o Mac host do build, caso esteja usando um computador Windows) e faça logon.
2.  Selecione **Certificados, Identificadores e Perfis**:

    ![Central de desenvolvedores da Apple](images/image5.png)

3.  Em **Identificadores**, selecione **IDs do aplicativo**:

    ![Seleção de ID do aplicativo na Central de desenvolvedores](images/image6.png)

4.  Pressione o botão **+** no canto superior direito para criar uma nova ID do aplicativo.
5.  Insira uma descrição de ID do Aplicativo, selecione ID do Aplicativo Explícita e insira uma ID de lote no formato `com.domain.appname`. Esta ID de lote deve corresponder à ID de lote em seu projeto:

    ![Adicionando detalhes da ID do aplicativo](images/image7.png)

6.  Em **Serviços de Aplicativos** selecione o serviço ou os serviços que são necessários para seu aplicativo:

    ![Página de seleção de Serviços de Aplicativos](images/image8.png)

7.  Pressione **Continuar**.
8.  Confirme a ID do aplicativo. Cada serviço estará em um dos seguintes estados: **Habilitado**, **Desabilitado**, ou **Configurável**, conforme é ilustrado abaixo. Se estiver **Habilitado,** estará pronto para ser usado em um perfil de provisionamento. Se estiver **Configurável**, uma configuração adicional será necessária para essa capacidade. Estas etapas adicionais estão descritas mais detalhadamente nas próximas seções.

    ![Confirmação de ID do aplicativo](images/image9.png)

9.  Clique em **Registrar** e em **Concluído**. A ID do aplicativo recém-criada deve ser exibida na lista de IDs do aplicativo iOS.


<a name="provisioningprofile" />

### <a name="creating-a-provisioning-profile"></a>Criando um perfil de provisionamento

Agora, crie um perfil de provisionamento que contenha essa ID do aplicativo. Siga as etapas abaixo:

1.  Na Central de desenvolvedores da Apple, acesse **Perfis de Provisionamento > Todos**:

    ![Seção Perfil de provisionamento](images/image10.png)

2.  Pressione o botão **+** no canto superior direito para criar um novo perfil de provisionamento.
3.  Selecione o tipo de perfil de provisionamento que você precisa e clique em **Continuar**:

    ![Seleção do perfil de provisionamento](images/image11.png)

4.  Na lista suspensa, selecione a ID do aplicativo que foi criada nas etapas acima e pressione **Continuar**:

    ![Seleção de ID do aplicativo](images/image12.png)

5.  Selecione os certificados usados para assinar o aplicativo e pressione **Continuar**:

    ![Seleção de certificado](images/image13.png)

6.  Selecione os dispositivos a serem incluídos neste perfil e pressione **Continuar**:

    ![Selecione dispositivos para o perfil de provisionamento](images/image14.png)

7.  Dê um nome ao perfil para que ele possa ser identificado e pressione **Continuar** para gerar o perfil:

    ![Nomeie o perfil de provisionamento](images/image15.png)

8.  Pressione o botão **Baixar** para baixá-lo e, em seguida, clique duas vezes no arquivo no Finder para instalar o perfil de provisionamento.

9. Se você está usando o Visual Studio para Mac, a opção **Gerenciar assinatura automaticamente** deve estar desmarcada no arquivo **Info.plist**

10. No Visual Studio para Mac/Visual Studio, procure **Opções de Projeto > Assinatura do Pacote** e defina o perfil de provisionamento para aquele que acabou de ser criado:

    ![Opções do projeto do Visual Studio para Mac](images/image16.png)

> [!IMPORTANT]
> Talvez você também precise definir as chaves de direito no arquivo Entitlement.plist e as chaves de privacidade no arquivo Info.plist. Mais informações sobre esses direitos são fornecidas no guia [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).

<a name="nextsteps" />

## <a name="next-steps"></a>Próximas etapas

Quando uma capacidade tiver sido habilitada no lado do servidor, ainda haverá trabalho a ser feito para permitir que seu aplicativo use a funcionalidade. A lista a seguir descreve as etapas adicionais que precisam ser executadas:

*   Use o namespace do framework em seu aplicativo.
*   Adicione os direitos necessários para seu aplicativo. As informações sobre os direitos necessários e como adicioná-los estão detalhadas no guia [Introdução aos direitos](~/ios/deploy-test/provisioning/entitlements.md).

<a name="troubleshooting" />

## <a name="troubleshooting-capabilities"></a>Solução de problemas de capacidades

A lista a seguir detalha alguns dos problemas mais comuns que podem criar obstáculos ao desenvolver um aplicativo com um serviço de aplicativo habilitado.

-   Verifique se a ID correta foi criada e registrada corretamente na seção **Certificados, IDs e Perfis** do Portal do Desenvolvedor da Apple.
-   Verifique se o serviço foi adicionado à ID do aplicativo (ou da extensão) e se o serviço está configurado para usar o grupo de aplicativos, a ID do comerciante ou o contêiner criado anteriormente em **Certificados, IDs e Perfis** do Portal do Desenvolvedor da Apple.
-   Verifique se os perfis de provisionamento e IDs do aplicativo foram instaladas e que **Info.plist** do aplicativo (no projeto Xamarin) está usando uma das IDs do aplicativo configuradas acima.
-   Verifique se o arquivo **Entitlements.plist** do aplicativo (no projeto Xamarin) tem o serviço correto habilitado.
-   Verifique se as chaves de privacidade apropriadas estão definidas no info.plist
-   Na **Assinatura de Pacote do iOS** do aplicativo, certifique-se de que **Direitos Personalizados** está definido como **Entitlements.plist**. Esta _não_ é a configuração padrão para builds de depuração e do simulador do iOS.

<a name="summary" />

## <a name="summary"></a>Resumo

Este guia explicou as Funcionalidades ou, _serviços de aplicativos_, e descreveu como elas podem ser habilitadas no Visual Studio e na Central de Desenvolvedores da Apple. Ele também detalhou como configurar os serviços mais complicados, como Wallet, iCloud, Apple Pay e Grupos de Aplicativos. Por fim, ele abordou as próximas etapas de configuração e opções de solução de problemas simples.