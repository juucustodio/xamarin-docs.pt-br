---
title: Recursos Apple Pay no Xamarin.iOS
description: A adição de capacidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades do Apple Pay.
ms.prod: xamarin
ms.assetid: 735CC916-16A4-471B-87F7-0535E24288D7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 856ed31cd2ee8fdc657cd1437f21052e1d7a79f2
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065492"
---
# <a name="apple-pay-capabilities-in-xamarinios"></a>Recursos Apple Pay no Xamarin.iOS

_A adição de funcionalidades a um aplicativo geralmente requer uma configuração de provisionamento adicional. Este guia explica a configuração necessária para as funcionalidades do Apple Pay._

O Apple Pay permite que os usuários paguem produtos físicos usando o dispositivo iOS. Esta seção descreve como criar todos os componentes necessários para o Apple Pay na Central de Desenvolvedores da Apple.

Ao provisionar um novo aplicativo por meio da central de desenvolvedores, há três etapas que precisam ser executadas:

1. Criar uma ID do comerciante.
2. Criar uma ID do aplicativo com a capacidade do Apple Pay e adicionar o comerciante.
3. Gerar um certificado para a ID do comerciante.

As etapas a seguir orientam a criação dos itens acima:

<a name="merchantid" />

## <a name="create-merchant-id"></a>Criar ID do comerciante

A ID do comerciante é usada para informar o Apple Pay que você pode aceitar pagamentos e é passada ao método `PaymentRequest` do PassKit e usada no direito do Apple Pay:

1. Procure a [Central de Desenvolvedores da Apple](https://developer.apple.com/account/) e acesse a seção Certificados, Identificador e Perfis: 
 
    ![Seleção de ID do comerciante na Central de Desenvolvedores](apple-pay-capabilities-images/image57.png)

2. Em **Identificadores**, selecione **IDs do comerciante** e, em seguida, escolha **+** para criar uma nova ID do comerciante:  

3. Preencha o formulário ilustrado abaixo, com uma nova descrição e um novo identificador. A descrição torna a ID identificável para você e pode ser alterada posteriormente. O identificador deve ser exclusivo para você e deve começar com a cadeia de caracteres `merchant`. A Apple recomenda que o identificador esteja no seguinte formato: `merchant.com.[Your-App-Name]`:
   
    ![Detalhes da nova ID do comerciante](apple-pay-capabilities-images/image58.png)

4. Confirme os detalhes e **Registre** sua ID: 
    
    ![Confirmação de ID do comerciante](apple-pay-capabilities-images/image59.png)

<a name="appid" />

## <a name="create-an-app-id-with-the-apple-pay-capability-that-includes-the-merchant-id"></a>Crie uma ID do aplicativo com a capacidade do Apple Pay que inclui a ID do comerciante

1. Na [Central de Desenvolvedores](https://developer.apple.com/account/) clique em **IDs do aplicativo** em **Identificadores**: 
    
    ![Selecione a ID do aplicativo na Central de Desenvolvedores](apple-pay-capabilities-images/image6.png)

2. Selecione o botão **+** para adicionar uma nova ID do aplicativo: 
   
    ![Botão Adicionar nova ID do aplicativo](apple-pay-capabilities-images/image27.png)

3. Insira um Nome para a ID de aplicativo e dê a ele uma ID de Aplicativo Explícita:    
   
    ![Tela de detalhes da ID do aplicativo](apple-pay-capabilities-images/image35.png)

4. Em Serviços de Aplicativos, selecione Apple Pay:    
  
    ![Serviços de aplicativos Apple Pay](apple-pay-capabilities-images/image36.png)

5. Selecione **Continuar** e, em seguida, **Registrar**. Observe que, na tela de confirmação, o Apple Pay será exibido com Configurável selecionado, com um símbolo amarelo: 
   
    ![Tela de confirmação do Apple Pay](apple-pay-capabilities-images/image37.png)

6. Retorne à lista de IDs do aplicativo e selecione aquele que você acabou de criar:  
   
    ![Editar ID do aplicativo](apple-pay-capabilities-images/image38.png)

7. Role até o final desta seção expandida e clique em **Editar**.
8. Role na lista até o Apple Pay e clique no botão **Editar**:  
    
    ![Editar detalhes da ID do aplicativo do Apple Pay](apple-pay-capabilities-images/image39.png)

9. Selecione a ID do comerciante a ser usada com essa ID do aplicativo e clique em **Continuar**:  
    
    ![Selecione a ID do comerciante a ser usada para a ID do aplicativo](apple-pay-capabilities-images/image40.png)

10. Confirme as atribuições de ID do comerciante e pressione **Atribuir**:  
    
    ![Tela de confirmação](apple-pay-capabilities-images/image41.png)

Agora esta ID do aplicativo pode ser usada para gerar, ou gerar novamente, um novo perfil de provisionamento, conforme descrito no guia [Trabalhando com funcionalidades](~/ios/deploy-test/provisioning/capabilities/index.md). 

<a name="certificate" />

## <a name="create-a-certificate-for-your-merchant-id"></a>Criar um certificado para a ID do comerciante

A Apple exige um certificado para criptografar os dados confidenciais associados à transação. Cada ID do comerciante criada deve ter seu próprio certificado. 

Para criar um certificado, execute as etapas a seguir:

1. Selecione a ID do comerciante que foi criada acima e pressione **Editar**: 
    
    ![Diálogo Editar ID do comerciante](apple-pay-capabilities-images/image42.png)

2. Na tela Configurações da ID do comerciante do iOS, clique em **Criar Certificado**: 
   
    ![Criar certificado de processamento de pagamento](apple-pay-capabilities-images/image43.png)

3. Responda à seguinte pergunta: 

    ![indicar se os pagamentos serão processados exclusivamente na China](apple-pay-capabilities-images/image44.png)

4. Neste ponto, será solicitado que você crie uma _solicitação de assinatura de certificado_: 

    ![Criando uma solicitação de assinatura de certificado](apple-pay-capabilities-images/image45.png)
    
    > [!IMPORTANT]
    > Se você estiver usando um provedor de pagamento para o Apple Pay, como JudoPay ou Stripe, ele fornecerá uma CSR (solicitação de assinatura de certificado) formatada corretamente que poderá ser usada neste momento. Informações sobre como solicitar isso são encontradas nos sites do [JudoPay](https://www.judopay.com/docs/version-52/apple-pay/getting-started/#create-an-apple-pay-certificate) e do [Strip](https://stripe.com/docs/apple-pay/apps#csr). Para criar sua própria CSR, siga as etapas 5 a 8 abaixo. Quando já tiver uma CSR, vá para a etapa 9.

5. Abra o aplicativo Acesso ao Conjunto de Chaves e procure **Acesso ao Conjunto de Chaves > Assistente de Certificado > Solicitar um Certificado de uma Autoridade de Certificação:** 

     ![Criar uma CSR usando o conjunto de chaves em um Mac](apple-pay-capabilities-images/image46.png)

6. Insira seu endereço de email, insira um nome para a chave privada, deixe o endereço de email da AC vazio, selecione a opção **Salvar em Disco** e selecione **Permita-me especificar as informações do par de chaves**:

     ![Diálogo de informações de certificado](apple-pay-capabilities-images/image47.png)

7. Salve a CSR em um local conveniente: 

     ![Salvando a CSR no computador local](apple-pay-capabilities-images/image48.png)

8. Na tela de informações Par de Chaves, defina **Tamanho de Chave** para **256 bits** e **Algoritmo** para **ECC** e clique em **Continuar**:

     ![Diálogo para inserir informações do par de chaves](apple-pay-capabilities-images/image49.png)

9. Na Central de Desenvolvedores, clique em **Continuar** para carregar a CSR: 

     ![Preparar para carregar a CSR na central de desenvolvedores](apple-pay-capabilities-images/image50.png)

10. Clique em **Escolher Arquivo...** para selecionar a CSR e pressione **Continuar** para carregá-la no portal do desenvolvedor: 

     ![Carregar a CSR na central de desenvolvedores](apple-pay-capabilities-images/image51.png)

11. Depois que o certificado for gerado, baixe-o e clique duas vezes nele para instalá-lo no conjunto de chaves.

Para obter mais informações sobre o uso do Apple Pay, consulte o guia a seguir:

* [Introdução ao Apple Pay](~/ios/platform/apple-pay.md)

## <a name="next-steps"></a>Próximas etapas
 
A lista a seguir descreve as etapas adicionais que precisam ser executadas:

* Use o namespace do framework em seu aplicativo.
* Adicione os direitos necessários para seu aplicativo. As informações sobre os direitos necessários e como adicioná-los estão detalhadas no guia [Trabalhando com direitos](~/ios/deploy-test/provisioning/entitlements.md).
* Na **Assinatura de Pacote do iOS** do Aplicativo, verifique se a opção **Direitos Personalizados** está definida como **Entitlements.plist**. Esta _não_ é a configuração padrão para builds de Depuração e do Simulador do iOS.

Se você encontrar problemas com serviços de aplicativos, consulte a seção [Solução de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) do guia principal.
