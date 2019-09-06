---
title: Recursos iCloud no Xamarin.iOS
description: A adição de capacidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades do iCloud.
ms.prod: xamarin
ms.assetid: 3CBAC982-D8DE-48DD-97CD-32B551D9DB85
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 0972b7c6fa686f40ca548f8267f8e514e9244f05
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286146"
---
# <a name="icloud-capabilities-in-xamarinios"></a>Recursos iCloud no Xamarin.iOS

_A adição de funcionalidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades do iCloud._

O iCloud fornece aos usuários do iOS uma maneira fácil e simples de armazenar conteúdo e compartilhá-lo entre dispositivos. Os desenvolvedores podem usar o iCloud para fornecer um meio de armazenamento para seus usuários de quatro maneiras: Armazenamento de chave-valor, Armazenamento de UIDocument, CoreData e uso do CloudKit diretamente para fornecer armazenamento para arquivos e diretórios individuais. Para obter mais informações sobre essas maneiras, consulte o guia [Introdução ao iCloud](~/ios/data-cloud/introduction-to-icloud.md).

Adicionar a capacidade do iCloud a um aplicativo é um pouco mais difícil do que os outros Serviços de Aplicativos por causa dos _contêineres_. Os contêineres são usados no iCloud para armazenar informações de um aplicativo e permitir que todas as informações contidas em uma única conta do iCloud sejam separadas, como a área restrita em um dispositivo iOS do usuário. Para obter mais informações sobre contêineres, consulte o guia [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md).

> [!IMPORTANT]
> A Apple [fornece ferramentas](https://developer.apple.com/support/allowing-users-to-manage-data/) para ajudar os desenvolvedores a lidar adequadamente com o GDPR (Regulamento Geral sobre a Proteção de Dados) da União Europeia.

<a name="icloud-developer-center" />

## <a name="developer-center"></a>Developer Center

Ao provisionar um novo aplicativo por meio da central de desenvolvedores há duas etapas que precisam ser executadas:

1. Criar um contêiner.
2. Criar uma ID do aplicativo com a capacidade do iCloud e adicionar o contêiner.
3. Criar um perfil de provisionamento que inclua a ID do aplicativo

As etapas abaixo orientam nessas etapas:

1. Procure a [Central de Desenvolvedores da Apple](https://developer.apple.com/account/) e acesse a seção Certificados, Identificador e Perfis: 
    
     ![Página principal da Central de Desenvolvedores da Apple](icloud-capabilities-images/image22.png)

2. Em **Identificadores** selecione **Contêineres do iCloud** e, em seguida, selecione o **+** para criar um novo contêiner:  
    
    ![Tela de contêiner do iCloud](icloud-capabilities-images/image23.png)

3. Insira uma **Descrição** e um **Identificador** exclusivo para o contêiner do iCloud: 
    
    ![Tela de registro de contêiner do iCloud](icloud-capabilities-images/image24.png)

4. Pressione **Continuar**, verifique se as informações estão corretas e pressione **Registrar** para criar o contêiner do iCloud:  
    
    ![Tela de registro de contêiner do iCloud](icloud-capabilities-images/image25.png)

Para criar uma nova ID do aplicativo e adicionar um contêiner, faça o seguinte:

1. Na [Central de Desenvolvedores](https://developer.apple.com/account/), clique em **IDs do aplicativo** em **Identificadores**: 
    
    ![Seção Identificador de seção na Central de Desenvolvedores](icloud-capabilities-images/image26.png)

2. Selecione o botão **+** para adicionar uma nova ID do aplicativo: 
    
    ![Botão Adicionar nova ID do aplicativo](icloud-capabilities-images/image27.png)

3. Insira um **Nome** para a ID de aplicativo e dê a ele uma **ID de Aplicativo Explícita**:
    
    ![Insira os detalhes da nova ID do aplicativo](icloud-capabilities-images/image28.png)

4. Em **Serviços de Aplicativos** selecione **iCloud** e escolha **Incluir suporte do CloudKit**:
    
    ![Selecione os serviços de aplicativos do iCloud](icloud-capabilities-images/image29.png)

5. Selecione **Continuar** e, em seguida, **Registrar**. Observe que, na tela de confirmação, o iCloud será exibido com Configurável selecionado, com um símbolo amarelo:   
    
    ![Tela de confirmação](icloud-capabilities-images/image30.png)

6. Retorne à lista de IDs do aplicativo e selecione aquele que você acabou de criar: 
    
    ![Tela de seleção da ID do aplicativo](icloud-capabilities-images/image31.png)

7. Role até o final desta seção expandida e clique em **Editar**:
    
    ![Editar ID do aplicativo](icloud-capabilities-images/image32.png)

8. Role a lista até o iCloud e clique no botão **Editar**:  
    
    ![Editar ID do aplicativo do iCloud](icloud-capabilities-images/image33.png)

9. Selecione o contêiner a ser usado com essa ID do aplicativo:  
    
    ![Tela de seleção de contêiner](icloud-capabilities-images/image34.png)

10. Confirme as atribuições do contêiner e pressione **Atribuir**.
 
Agora esta ID do aplicativo pode ser usada para gerar, ou gerar novamente, um novo perfil de provisionamento, conforme descrito no guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md). 

Para obter mais informações sobre como usar o iCloud, consulte os seguintes guias:

* [Introdução ao iCloud](~/ios/data-cloud/introduction-to-icloud.md)
* [Introdução ao CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)
* [Introdução ao Seletor de Documentos](~/ios/platform/document-picker.md)

## <a name="next-steps"></a>Próximas etapas
 
A lista a seguir descreve as etapas adicionais que precisam ser executadas:

* Use o namespace do framework em seu aplicativo.
* Adicione os direitos necessários para seu aplicativo. As informações sobre os direitos necessários e como adicioná-los estão detalhadas no guia [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).
* Na **Assinatura de Pacote do iOS** do Aplicativo, verifique se a opção **Direitos Personalizados** está definida como **Entitlements.plist**. Esta _não_ é a configuração padrão para builds de Depuração e do Simulador do iOS.

Se você encontrar problemas com serviços de aplicativos, consulte a seção [Solução de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) do guia principal.
