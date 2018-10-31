---
title: Recursos Wallet no Xamarin.iOS
description: A adição de capacidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades da Carteira.
ms.prod: xamarin
ms.assetid: BD9475E6-F586-488C-93D4-8A2A1629B99B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 3dc68d0879d3ce4d5dd9b658a3bce7a00c348c09
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121237"
---
# <a name="wallet-capabilities-in-xamarinios"></a>Recursos Wallet no Xamarin.iOS

_A adição de funcionalidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades da Wallet._

Wallet é um aplicativo que armazena e exibe códigos de barras e outros conteúdos que permitem aos usuários exibir tíquetes, passagens aéreas e cupons diretamente do dispositivo. Essas informações são armazenadas em um _passe_. Por exemplo, uma passagem aérea ou um único tíquete seriam um único passe. 

Os desenvolvedores podem trabalhar com o Wallet de várias maneiras:

*   Para criar um passe, um aplicativo não precisa ser criado. Um Passfile é um arquivo compactado que contém alguns arquivos JSON e arquivos de metadados opcionais. Para prepará-lo, são necessários uma [ID de Tipo de Passe](~/ios/platform/passkit.md) e um [Certificado de passe](~/ios/platform/passkit.md). Em seguida, essas informações são declaradas em um arquivo JSON. Mais informações de como provisionar um Passfile podem ser encontradas no guia [Introdução ao PassKit](~/ios/platform/passkit.md).

*   Aplicativos complementares são gravados para distribuir passes. Eles também têm a funcionalidade de criar, editar e atualizar passes e, em seguida, adicioná-los no aplicativo Wallet. Um bom exemplo desse tipo de aplicativo seria um aplicativo de cinema. Quando um usuário compra um tíquete usando o aplicativo, esse tíquete pode ser adicionado diretamente do aplicativo ao Wallet. Para usar um aplicativo complementar, o perfil de provisionamento deve incluir uma ID do aplicativo com os recursos do Wallet, que podem ser definidos seguindo as etapas abaixo. Seu aplicativo também deve incluir os direitos necessários.

*   Aplicativos de canal são aplicativos que não manipulam passes diretamente. Eles têm uma interação mínima com o passe além de recebê-lo e dar ao usuário a opção de adicioná-lo ao Wallet. Esses aplicativos não precisam de nenhum provisionamento ou direito especial, mas usam alguns métodos do PassKit Framework.

## <a name="developer-center"></a>Developer Center

Para criar um novo perfil de provisionamento a ser usado com o Wallet, faça o seguinte:

1.  Procure a seção [Certificados, Identificadores e Perfis](https://developer.apple.com/account/ios/certificate/) do Portal do Desenvolvedor da Apple.
2.  Em **Identificadores**, procure **IDs do aplicativo**: 
    
    ![Seleção de ID do aplicativo](wallet-capabilities-images/image17.png)

3.  Clique no ícone **+** no canto superior direito da página.
4.  Registre uma nova ID do aplicativo, dando a ela um **nome** e um identificador de lote. (Observe que esse identificador de lote deve corresponder à ID de lote no projeto):
   
    ![Adicionar detalhes da ID do aplicativo](wallet-capabilities-images/image18.png)

5.  Selecione o serviço de aplicativo **Wallet** na lista de serviços:
    
    ![Tela de seleção de serviço](wallet-capabilities-images/image19.png)

6.  Pressione **Continuar** e, em seguida, **Registrar** para criar a ID do aplicativo.

Se necessário, as IDs do aplicativo existentes poderão ser editadas para adicionar a capacidade do Wallet.

Agora esta ID do aplicativo pode ser usada para gerar, ou gerar novamente, um novo perfil de provisionamento, conforme descrito no guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md):

![Usando a ID do aplicativo recém-criada para criar o perfil de provisionamento](wallet-capabilities-images/image20.png)


Para obter mais informações de como usar o Wallet, consulte os seguintes guias:

*   [Introdução ao PassKit](~/ios/platform/passkit.md)
 
## <a name="next-steps"></a>Próximas etapas
 
A lista a seguir descreve as etapas adicionais que precisam ser executadas:

* Use o namespace do framework em seu aplicativo.
* Adicione os direitos necessários para seu aplicativo. As informações sobre os direitos necessários e como adicioná-los estão detalhadas no guia [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).
* Na **Assinatura de Pacote do iOS** do Aplicativo, verifique se a opção **Direitos Personalizados** está definida como **Entitlements.plist**. Esta _não_ é a configuração padrão para builds de Depuração e do Simulador do iOS.

Se você encontrar problemas com serviços de aplicativos, consulte a seção [Solução de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) do guia principal.