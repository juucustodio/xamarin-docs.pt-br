---
title: Provisionamento gratuito para aplicativos Xamarin.iOS
description: Este documento descreve como os desenvolvedores do Xamarin.iOS podem testar os aplicativos em um dispositivo físico sem a necessidade de se inscrever no Programa para Desenvolvedores pago da Apple.
ms.prod: xamarin
ms.assetid: A5CE2ECF-8057-49ED-8393-EB0C5977FE4C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/16/2018
ms.openlocfilehash: 951b019a4a0bf503629b6ac63853bd5b1c818bce
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022752"
---
# <a name="free-provisioning-for-xamarinios-apps"></a>Provisionamento gratuito para aplicativos Xamarin.iOS

O provisionamento gratuito permite que desenvolvedores do Xamarin.iOS implantem e testem seus aplicativos em dispositivos iOS **sem** fazer parte do **Programa de Desenvolvedor da Apple**.
Embora testes em simuladores sejam valiosos e convenientes, também é essencial testar os aplicativos em dispositivos iOS físicos para garantir que eles funcionam corretamente sob restrições de memória, armazenamento e conectividade de rede do mundo real.

Para usar o provisionamento gratuito para implantar um aplicativo em um dispositivo:

- Use o Xcode para criar a *identidade de assinatura* (certificado de desenvolvedor e chave privada) e o *perfil de provisionamento* (contendo uma ID do aplicativo explícita e a UDID de um dispositivo iOS conectado) necessários.
- Use a identidade de assinatura e o perfil de provisionamento criados pelo Xcode no Visual Studio para Mac ou no Visual Studio 2019 para implantar seu aplicativo Xamarin.iOS.

> [!IMPORTANT]
> O [provisionamento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) permite que o Visual Studio para Mac ou o Visual Studio 2019 configure automaticamente um dispositivo para testes de desenvolvedor. No entanto, o provisionamento automático não é compatível com o provisionamento gratuito. Para usar o provisionamento automático, você precisa ter uma conta paga do Programa de Desenvolvedor da Apple.

## <a name="requirements"></a>Requisitos

Para implantar seus aplicativos Xamarin.iOS em um dispositivo com provisionamento gratuito:

- A ID da Apple usada não pode estar conectada a nenhum Programa de Desenvolvedor da Apple.
- Seu aplicativo Xamarin.iOS precisa usar uma ID do aplicativo explícita, não uma ID do aplicativo curinga.
- O identificador do pacote usado em seu aplicativo Xamarin.iOS precisa ser exclusivo e não pode ter sido usado em outro aplicativo anteriormente. Identificadores de pacote usados com o provisionamento gratuito **não podem** ser usados novamente.
- Caso já tenha distribuído um aplicativo, você não poderá implantar esse aplicativo com o provisionamento gratuito.
- Se seu aplicativo utilizar os Serviços de Aplicativos, você precisará criar um perfil de provisionamento de acordo com o que está detalhado no guia de [provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services). 

Confira a seção [Limitações](#limitations) deste documento para obter mais informações sobre as limitações associadas ao provisionamento gratuito e consulte os [Guias de distribuição de aplicativos](~/ios/deploy-test/app-distribution/index.md) para obter mais informações sobre a distribuição de aplicativos iOS.

## <a name="testing-on-device-with-free-provisioning"></a>Testes em dispositivos com provisionamento gratuito

Siga as etapas abaixo para testar seu aplicativo Xamarin.iOS com o provisionamento gratuito.

### <a name="use-xcode-to-create-a-signing-identity-and-provisioning-profile"></a>Use o Xcode para criar uma identidade de assinatura e um perfil de provisionamento

1. Se não tiver uma ID da Apple, [crie uma](https://appleid.apple.com).
2. Abra o Xcode e navegue até **Xcode > Preferências**.
3. Em **Contas**, use o botão **+** para adicionar sua ID Apple existente. Deve ter uma aparência semelhante à captura de tela a seguir:

    ![Preferências xcode – Contas](free-provisioning-images/launchapp1.png "Preferências xcode – Contas")

4. Feche as preferências do Xcode.
5. Conecte o dispositivo iOS no qual gostaria de implantar o aplicativo.
6. No Xcode, crie um novo projeto. Escolha **Arquivo > Novo > Projeto** e selecione **Aplicativo de Exibição Única**.
7. Na caixa de diálogo do novo projeto, defina **Equipe** como a ID da Apple que você acabou de adicionar. Na lista suspensa, ela deve ser semelhante a **Seu nome (Equipe pessoal)**:

    ![Criar um novo aplicativo](free-provisioning-images/launchapp2.png "Criar um novo aplicativo")

8. Após o novo projeto ter sido criado, escolha um esquema de build do Xcode direcionado para seu dispositivo iOS (em vez de um simulador).

    ![Selecione um esquema de compilação Xcode](free-provisioning-images/xcodescheme.png "Selecione um esquema de compilação Xcode")

9. Abra as configurações de projeto do aplicativo selecionando seu nó de nível superior no **Navegador de Projeto** do Xcode.
10. Em **Geral > Identidade**, certifique-se de que o **Identificador de Pacote** _corresponda exatamente_ ao identificador de pacote de seu aplicativo Xamarin.iOS.

    ![Defina um identificador de pacote](free-provisioning-images/launchapp5.png "Defina um identificador de pacote")

    > [!IMPORTANT]
    > O Xcode criará um perfil de provisionamento somente para uma ID do aplicativo explícita, e ela precisa ser idêntica à ID do aplicativo Xamarin.iOS.
    > Se elas forem diferentes, você não poderá usar o provisionamento gratuito para implantar o aplicativo Xamarin.iOS.

11. Em **Informações de Implantação**, certifique-se de que o destino da implantação tenha uma versão igual ou inferior à versão do iOS instalada em seu dispositivo iOS conectado.
12. Em **Assinatura**, selecione **Gerenciar assinatura automaticamente** e selecione sua equipe na lista suspensa:

    ![Gerencie automaticamente a assinatura](free-provisioning-images/launchapp6.png "Gerencie automaticamente a assinatura")

    O Xcode gerará automaticamente um perfil de provisionamento e uma identidade de assinatura para você. Você pode exibi-los clicando no ícone de informações ao lado do perfil de provisionamento:

    ![Exibir o perfil de provisionamento](free-provisioning-images/launchapp7.png "Exibir o perfil de provisionamento")

    > [!TIP]
    > Se ocorrer uma falha quando o Xcode tentar gerar um perfil de provisionamento, verifique se o esquema de build do Xcode selecionado atualmente tem como destino o dispositivo iOS conectado em vez de um simulador.

13. Para testar o Xcode, implante o aplicativo em branco em seu dispositivo ao clicar no botão de execução.

### <a name="deploy-your-xamarinios-app"></a>Implantar seu aplicativo Xamarin.iOS

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Conecte seu dispositivo iOS ao host de build do Mac via USB ou usando uma conexão [sem fio](~/ios/deploy-test/wireless-deployment.md).
2. No **Painel de Soluções** do Visual Studio para Mac, clique duas vezes em **Info.plist**.
3. Em **Assinatura**, selecione **Provisionamento Manual**.
4. Clique no botão **Assinatura de Pacote do iOS…** .
5. Em **Configuração**, selecione **Depurar**.
6. Em **Plataforma**, selecione **iPhone**.
7. Selecione a **Identidade de Assinatura** criada pelo Xcode.
8. Selecione o **Perfil de Provisionamento** criado pelo Xcode.

    ![Definir a identidade de assinatura e o perfil de provisionamento](free-provisioning-images/launchapp8.png "Definir a identidade de assinatura e o perfil de provisionamento")

    > [!TIP]
    > Se você não vir sua identidade de assinatura ou o perfil de provisionamento correto, poderá ser necessário reiniciar o Visual Studio para Mac.

9. Clique em **OK** para salvar e fechar as **Opções de Projeto**.
10. Selecione seu dispositivo iOS e execute o aplicativo.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Verifique se o Visual Studio 2019 ou o Visual Studio 2017 foi [emparelhado com um host de build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. Conecte seu dispositivo iOS ao host de build do Mac via USB ou usando uma conexão [sem fio](~/ios/deploy-test/wireless-deployment.md).
3. No **Gerenciador de Soluções** do Visual Studio 2019 ou Visual Studio 2017, clique com o botão direito do mouse no projeto Xamarin.iOS e selecione **Propriedades**.
4. Navegue até **Assinatura de Pacote do iOS**.
5. Em **Configuração**, selecione **Depurar**.
6. Em **Plataforma**, selecione **iPhone**.
7. Selecione **Provisionamento manual**.
8. Selecione a **Identidade de Assinatura** criada pelo Xcode.
9. Selecione o **Perfil de Provisionamento** criado pelo Xcode.
    
    ![Definir a identidade de assinatura e o perfil de provisionamento](free-provisioning-images/setprofile-w157.png "Definir a identidade de assinatura e o perfil de provisionamento")

    > [!TIP]
    > O Xcode criou essa identidade de assinatura e esse perfil de provisionamento e os armazenou em seu host de build do Mac. Eles são acessíveis para o Visual Studio 2019 ou o Visual Studio 2017, pois ele foi [emparelhado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) com o host de build do Mac. Se não estiverem listados, talvez você precise reiniciar o Visual Studio 2019 ou o Visual Studio 2017.

10. Salve e feche as propriedades do projeto.
11. Selecione seu dispositivo iOS e execute o aplicativo.

-----

## <a name="limitations"></a>Limitações

A Apple impôs uma série de limitações sobre quando e como você pode usar provisionamento gratuito para executar seu aplicativo em um dispositivo iOS, garantindo que você só possa implantar em *seu* dispositivo:

- O acesso ao iTunes Connect é limitado e, portanto, serviços como a publicação na App Store e no TestFlight não estão disponíveis para desenvolvedores que provisionam seus aplicativos gratuitamente. Uma Conta de Desenvolvedor da Apple (Empresarial ou Pessoal) é necessária para distribuir pelos meios Internos e Ad Hoc.
- Perfis de provisionamento criados com o provisionamento gratuito expiram após uma semana e as identidades de assinatura após um ano. 
- Como o Xcode cria perfis de provisionamento somente para IDs de Aplicativos explícitas, você precisará seguir as [instruções acima](#testing-on-device-with-free-provisioning) para cada aplicativo que quiser instalar.
- Para a maioria dos serviços de aplicativo, não é possível fazer o provisionamento usando o provisionamento gratuito. Isso inclui Apple Pay, Game Center, iCloud, compras no aplicativo, Notificações por Push e Wallet. A Apple fornece uma lista completa de recursos em [Supported capabilities (iOS)](https://help.apple.com/developer-account/#/dev21218dfd6) (Recursos compatíveis [iOS]). Para prover seu aplicativo para uso com serviços de aplicativos, visite os [guias de trabalho com recursos.](~/ios/deploy-test/provisioning/capabilities/index.md)

## <a name="summary"></a>Resumo

Este guia explorou as vantagens e limitações do uso do provisionamento gratuito para instalar aplicativos em um dispositivo iOS. Ele forneceu uma explicação passo a passo que demonstrou como usar o provisionamento gratuito para instalar um aplicativo Xamarin.iOS.

## <a name="related-links"></a>Links relacionados

- [Provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)
- [Provisionamento para serviços de aplicativo](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services)
