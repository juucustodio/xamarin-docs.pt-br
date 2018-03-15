---
title: Provisionamento gratuito
description: "Com a versão da Apple do Xcode 7, surgiu uma alteração importante para todos os desenvolvedores de iOS: provisionamento gratuito."
ms.topic: article
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 4e93696f8eef44030ffacbdbaa8ebcd860a402f6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="free-provisioning"></a>Provisionamento gratuito

_Com a versão da Apple do Xcode 7, surgiu uma alteração importante para todos os desenvolvedores de iOS: provisionamento gratuito._

O provisionamento gratuito permite que os desenvolvedores implantem seu aplicativo Xamarin.iOS em seu dispositivo iOS **sem** fazer parte de qualquer **Programa de Desenvolvedor da Apple**. Isso é extremamente vantajoso para desenvolvedores, já que testes em um dispositivo permitem muitos benefícios em relação aos testes no simulador, incluindo, mas sem limitação, a memória, armazenamento, conectividade de rede, entre outros.

O provisionamento sem uma conta de Desenvolvedor da Apple deve ser executado por meio do Xcode, que cria uma *Identidade de Assinatura* (que contém um certificado de desenvolvedor e chave privada) e um *Perfil de Provisionamento* (que contém uma ID do Aplicativo explícita e o UDID do seu dispositivo iOS conectado).

## <a name="requirements"></a>Requisitos

Para aproveitar a implantação de aplicativos do seu Xamarin.iOS em um dispositivo com provisionamento gratuito, você deve estar usando o Xcode 7 ou superior.

**A ID Apple que está sendo usada não deve estar conectada a nenhum Programa de Desenvolvedor da Apple.**

A ID do pacote usada em seu aplicativo deve ser exclusiva e não pode ter sido usada em outro aplicativo anteriormente. NENHUMA ID de pacote usada com provisionamento gratuito pode ser usada novamente. Caso já tenha distribuído um aplicativo, você não poderá provisionar este aplicativo com o provisionamento gratuito. 

Consulte os [guias de Distribuição de Aplicativos](~/ios/deploy-test/app-distribution/index.md) para obter mais informações.

Caso seu aplicativo utilize os Serviços de Aplicativos, você precisará criar um perfil de provisionamento conforme detalhado no guia de [provisionamento do dispositivo](~/ios/get-started/installation/device-provisioning/index.md#appservices). Você pode ver mais limitações na [seção relevante](#limitations) abaixo.


## <a name="a-namelaunching--launching-your-app"></a><a name="launching" /> Iniciar seu Aplicativo

Para usar o provisionamento gratuito a fim de implantar um aplicativo em um dispositivo, você utilizará o Xcode para criar os perfis de provisionamento e de identidade de assinatura e, em seguida, usará o Visual Studio para Mac ou o Visual Studio para escolher o perfil correto para assinar nosso aplicativo. Siga o passo a passo abaixo para fazer isso:

1. Se você não tiver uma ID Apple, crie um em [appleid.apple.com](https://appleid.apple.com/account).
2. Abra o Xcode e navegue até **Xcode > Preferências**.
3. Em **Contas**, use o botão **+** para adicionar sua ID Apple existente. Deve ter uma aparência semelhante à captura de tela a seguir:

  [![](free-provisioning-images/launchapp1.png "Xcode > Preferências > Contas")](free-provisioning-images/launchapp1.png#lightbox)

4. Conecte o dispositivo iOS que deseja implantar e crie um novo projeto do iOS de exibição única em branco no Xcode. Defina a lista suspensa **Equipe** como a ID da Apple que você acabou de adicionar. Ele deve ter um formato semelhante a `your name (Personal Team - your Apple ID)`:

  [![](free-provisioning-images/launchapp2.png "Criar a Identidade de Assinatura")](free-provisioning-images/launchapp2.png#lightbox)

5. Na seção **Geral > Identidade**, certifique-se de que o Identificador de Pacote corresponde _exatamente_ ao Identificador de Pacote de seu aplicativo Xamarin.iOS e verifique se o destino da implantação é igual ou menor que o dispositivo iOS conectado. Esta etapa é extremamente importante, pois o Xcode criará somente um perfil de provisionamento com uma ID do Aplicativo explícita:

  [![](free-provisioning-images/launchapp5.png "Criar um perfil de provisionamento com uma ID de aplicativo explícito")](free-provisioning-images/launchapp5.png#lightbox)

6. Na seção Assinatura, selecione **Gerenciar Assinatura Automaticamente** e escolha sua equipe na lista suspensa:

  [![](free-provisioning-images/launchapp6.png "Selecione Gerenciar Assinatura Automaticamente e escolha sua equipe na lista suspensa")](free-provisioning-images/launchapp6.png#lightbox)

7. A etapa anterior gerará automaticamente um perfil de provisionamento e uma identidade de assinatura para você. Você pode exibi-los clicando no ícone de informações ao lado do perfil de provisionamento:

  [![](free-provisioning-images/launchapp7.png "Exibir o perfil de provisionamento")](free-provisioning-images/launchapp7.png#lightbox)

8. Para testar o Xcode, implante o aplicativo em branco em seu dispositivo ao clicar no botão de execução.

9. Volte para o IDE, com o mesmo dispositivo conectado e clique com o botão direito do mouse no nome do seu projeto Xamarin.iOS para abrir a caixa de diálogo **Opções de Projeto**. Navegue até a seção de Assinatura do Pacote iOS e defina explicitamente sua identidade de assinatura e seu perfil de provisionamento:

  [![](free-provisioning-images/launchapp8.png "Definir a identidade de assinatura e o perfil de provisionamento")](free-provisioning-images/launchapp8.png#lightbox)

Se você não conseguir ver sua identidade de assinatura ou o perfil de provisionamento correto no seu IDE, poderá ser necessário reiniciá-lo.


## <a name="a-namelimitations-limitations"></a><a name="limitations" />Limitações

A Apple impôs uma série de limitações sobre quando e como você pode usar o provisionamento gratuito para executar seu aplicativo em um dispositivo iOS, garantindo que você possa implantar somente no *seu* dispositivo. Elas estão listadas nesta seção.

O acesso ao iTunes Connect também é limitado e, portanto, serviços como a publicação na App Store e no TestFlight não estão disponíveis para desenvolvedores que provisionam gratuitamente seus aplicativos. Uma Conta de Desenvolvedor da Apple (Empresarial ou Pessoal) é necessária para distribuir pelos meios Internos e Ad Hoc.

Os Perfis de Provisionamento criados dessa forma expirarão após uma semana e as Identidades de Assinatura depois de um ano. Além disso, os perfis de provisionamento serão criados somente com IDs de Aplicativos explícitos e, portanto, você precisará seguir as instruções [acima](#launching) para cada aplicativo que deseja instalar.

Também não é possível realizar o provisionamento para a maioria dos serviços de aplicativo com o provisionamento gratuito. Isso inclui:

- Apple Pay
- Game Center
- iCloud
- Compras no Aplicativo
- Notificações por Push
- Wallet (era Passbook)

Uma lista completa é fornecida pela Apple em seu guia de [Recursos com Suporte](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/SupportedCapabilities/SupportedCapabilities.html#//apple_ref/doc/uid/TP40012582-CH38-SW1). Para provisionar seu aplicativo para uso com serviços de aplicativos, visite os guias [Trabalhando com capacidades](~/ios/deploy-test/provisioning/capabilities/index.md).


## <a name="summary"></a>Resumo

Este guia explorou as vantagens e limitações do uso de provisionamento gratuito para instalar aplicativos em um dispositivo iOS. Também percorreu, passo a passo, o uso do provisionamento gratuito para instalar um aplicativo Xamarin.iOS.

## <a name="related-links"></a>Links relacionados

- [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md)
- [Provisionando para Serviços de Aplicativos](~/ios/get-started/installation/device-provisioning/index.md#appservices)
