---
title: ID de toque
description: "ID de toque é uma tecnologia de autenticação de impressão digital biométrica da Apple."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 7ee2af392a00e045b1992d189a15d7a0ee04b02f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="touch-id"></a>ID de toque

ID de toque foi introduzido no iOS 7 como um meio de autenticação do usuário - semelhante a uma senha. No entanto, era limitado para desbloquear o dispositivo, usando o armazenamento de aplicativo, usando o iTunes e autenticar o conjunto de chaves iCloud somente.

Agora, há duas maneiras de usar Touch ID como um mecanismo de autenticação em um aplicativo do iOS 8 usando a API de autenticação Local. No momento não é possível usar a autenticação Local para autenticar remotamente.

Para entender completamente a ID de toque e seu valor, deverá exploraremos serviços do conjunto de chaves e o que significam esses novas alterações para dados do usuário. Acesso de conjunto de chaves também foi expandido no iOS 8 com o uso do novo recurso de listas de controle de acesso (ACLs).

## <a name="keychain--secure-enclave"></a>Conjunto de chaves & Enclave segura

Conjunto de chaves é um banco de dados grande, fornecendo o armazenamento seguro de senhas, chaves, certificados e anotações para uma ID da Apple individuais. No iOS 8 um aplicativo sempre terá acesso aos itens de seu próprio conjunto de chaves exclusivo e não pode acessar os itens do conjunto de chaves de outros aplicativos. Isso difere dos X em que o conjunto de chaves é desbloqueado com uma única senha, permitindo que qualquer aplicativo com reconhecimento de Keychain Services use o conjunto de chaves. Neste artigo, nos concentraremos sobre o funcionamento do conjunto de chaves no iOS 8.

Conjunto de chaves é um banco de dados especializado, onde cada linha é conhecida como um _Item de conjunto de chaves_. Cada item é descrito por atributos do conjunto de chaves e é composto de valores criptografados. Para permitir o uso eficiente de conjunto de chaves, ele é otimizado para itens pequenos, ou _segredos_.
Cada item de conjunto de chaves é protegido por senha de usuários e um segredo de dispositivo exclusivo. Itens de conjunto de chaves devem ser protegidos, mesmo quando os usuários não estão usando seus dispositivos. Isso é implementado no iOS, permitindo que apenas os itens a serem ficam disponíveis quando o dispositivo seja desbloqueado — quando o dispositivo é bloqueado se tornam indisponíveis. Eles também podem ser armazenados em um backup criptografado. Um dos principais recursos do conjunto de chaves é para impor o controle de acesso; um aplicativo tem acesso à sua parte do conjunto de chaves, e todos os outros aplicativos serão impedidos. O diagrama a seguir ilustra como um aplicativo interage com o conjunto de chaves:

[![](touchid-images/image1.png "Este diagrama ilustra como um aplicativo interage com o conjunto de chaves")](touchid-images/image1.png)

### <a name="secure-enclave"></a>Enclave segura

O conjunto de chaves não é possível descriptografar o item de conjunto de chaves sozinho. em vez disso, isso é feito o *Enclave proteger*. O enclave seguro é um processador colegas dentro o chip TPM A7 é responsável por determinar uma correspondência de dados de impressão digital do sensor Touch ID em relação a uma impressão registrado. Ele será descriptografar o item de conjunto de chaves e retornar o segredo descriptografado para o conjunto de chaves.

### <a name="working-with-keychain"></a>Trabalhando com o conjunto de chaves

Primeiro, seu aplicativo deve consultar no conjunto de chaves para verificar se existe uma senha. Se não existir, você precisará solicitar uma senha para que o usuário não é solicitado continuamente. Se a senha precisa ser atualizado, solicitar ao usuário uma nova senha e passar o valor atualizado ao conjunto de chaves.


> ℹ️ **Observação**: com informações secretas receber do banco de dados, ele não é prática recomendada apenas, mas espera-se que nenhum segredos serão mantidos na memória. Você só deve manter um segredo para como tempo é necessário e absolutamente não o atribua a uma variável global!




## <a name="keychain-acl-and-touch-id"></a>ID do conjunto de chaves ACL e toque

Lista de controle de acesso é um novo atributo de item de conjunto de chaves no iOS 8 que descreve as informações sobre o que deve acontecer para permitir que uma determinada operação ocorra. Isso pode ser na forma de exibir uma caixa de diálogo alerta ou solicitar uma senha. ACL permite que você defina a acessibilidade e a autenticação para um Item de conjunto de chaves. O diagrama a seguir mostra como esse novo atributo está associado com o restante do item de conjunto de chaves:

[![](touchid-images/image2.png "Este diagrama mostra como esse novo atributo está associado com o restante do item de conjunto de chaves")](touchid-images/image2.png)

A partir do iOS 8, agora há uma nova política de presença de usuário, `SecAccessControl`, que é imposto pelo enclave seguro em um iPhone 5s e acima. Podemos ver na tabela a seguir, assim como a configuração do dispositivo pode influenciar a avaliação de política:

<table width="100%" border="1px">
<thead>
<tr>
    <td>Configuração do dispositivo</td>
    <td>Avaliação de política</td>
    <td>Mecanismo de backup</td>
</tr>
</thead>
<tbody>
<tr>
    <td>Dispositivo sem senha</td>
    <td>Sem acesso</td>
    <td>Nenhum</td>
</tr>
<tr>
    <td>Dispositivo com senha</td>
    <td>Requer a senha</td>
    <td>Nenhum</td>
</tr>
<tr>
    <td>Dispositivo com a ID de toque</td>
    <td>Prefere a ID de toque</td>
    <td>Permite que a senha</td>
</tr>
</tbody>
</table>

Todas as operações dentro de Enclave seguro podem confiar em si. Isso significa que podemos usar o resultado da autenticação de ID de toque para autorizar a descriptografia de item de conjunto de chaves. O Enclave proteger também mantém um contador de correspondências de ID de toque com falha, nesse caso um usuário terá que voltar a usar a senha.
Uma nova estrutura do iOS 8, chamada _autenticação Local_, dá suporte a esse processo de autenticação do dispositivo. Iremos explorar isso na próxima seção.

## <a name="local-authentication"></a>Autenticação local

Medida estabelecidos na seção anterior, os aplicativos podem usar a autenticação Local para autenticar o usuário de acordo com a política de segurança que foi configurado no dispositivo.

Atualmente, a API fornece apenas dois recursos: em primeiro lugar, ele ajuda os serviços de conjunto de chaves existentes com o uso de novo conjunto de chaves controle listas de acesso (ACLs). Dados do conjunto de chaves podem ser desbloqueados com a autenticação bem-sucedida de uma impressão digital de usuários.

Em segundo lugar, LocalAuthentication fornece dois métodos para autenticar seu aplicativo localmente. Os desenvolvedores devem usar `CanEvaluatePolicy` para determinar se o dispositivo é capaz de aceitar o Touch ID e, em seguida, `EvaluatePolicy` para iniciar a operação de autenticação.

Embora os dois recursos oferecem autenticação local, eles não fornecem um mecanismo para o aplicativo ou o usuário autenticar para um servidor remoto.
Autenticação local fornece uma nova interface de usuário padrão para autenticação. No caso de ID de toque, isso é uma exibição de alerta com dois botões, conforme ilustrado abaixo. Um botão para cancelar e usar os meios de fallbacks de autenticação – a senha. Também é uma mensagem personalizada que deve ser definida. É recomendável usar isso para explicar para o usuário por que a autenticação de ID de toque é necessária.

[![](touchid-images/image12.png "O alerta de autenticação de ID de toque")](touchid-images/image12.png)

### <a name="with-keychain-services"></a>Com os serviços de conjunto de chaves

Um pouco mais cedo examinamos como um item de conjunto de chaves é descriptografado, usando o enclave segura para verificar o nosso senha. No iOS 8, podemos usar autenticação Local para a solicitação de verificação de ID de toque em conjunto com o recurso de listas de controle de acesso, que fornece a implementação do mecanismo de fallback, ou a senha.
Para usar a ACL que devemos usar o `SecAccessControl` política e, em seguida, verificar o estado do dispositivo usando `SecAccessible.WhenPasscodeSetThisDeviceOnly` ou `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Considerações de ACL

Há muitas coisas que devem ter em mente ao usar ACL com o conjunto de chaves e eles estão listados abaixo:

-   Use somente com aplicativos de primeiro plano – se você chamar qualquer operação de conjunto de chaves em um thread em segundo plano que a chamada falhará.
-   Adicionar e atualizar itens de conjunto de chaves podem exigir autenticação.
-   Se uma solicitação retornar vários itens correspondentes no conjunto de chaves, autenticação pode ser necessária.
-   Itens protegidos de ACL são somente para dispositivos e portanto não sincronizado ou backup.

### <a name="using-local-authentication-without-keychain-services"></a>Usando a autenticação Local sem os serviços de conjunto de chaves

Autenticação local foi criada como uma maneira de coletar credenciais, como senha ou ID de toque e trabalhar com o Enclave seguro para concluir a autenticação do usuário. Pense nisso como uma ponte entre o aplicativo e o Enclave seguro, nunca diretamente pode se comunicar entre si. Ele também pode ser usado para avaliação de política para o seu aplicativo.

Para fazer isso em um aplicativo chama a avaliação de política dentro de autenticação Local, que inicia a operação de Enclave segura. Você pode aproveitar isso para fornecer autenticação para seu aplicativo, sem consultar/acessar diretamente o Enclave segura.

[![](touchid-images/image13a.png "Usando a autenticação Local sem os serviços de conjunto de chaves")](touchid-images/image13a.png)

Usando a autenticação Local em seu aplicativo fornece uma maneira simples de implementação de verificação do usuário, por exemplo, para desbloquear um recurso somente para os olhos do proprietário do dispositivo, como aplicativos de serviços bancários, ou para controles dos pais do auxiliar do indivíduo aplicativo. Você também pode usá-lo como uma maneira de estender a autenticação que já existe – usuários, como suas informações de segurança, mas também deseja ter opções.

A segurança da autenticação local é diferente do que o conjunto de chaves. Por exemplo, ao usar o conjunto de chaves, a relação de confiança é entre o sistema operacional e o Enclave segura. Com a autenticação local, é entre o aplicativo e o sistema operacional, o que significa que você somente terá acesso aos resultados do Enclave segura, não o Secure Enclave em si.

Sobre o assunto de segurança, também é extremamente importante saber se há **nenhum acesso** dedos registrados ou imagens da impressão digital. O Enclave seguro é o proprietário dessas informações e portanto nenhum outro componente do sistema tem acesso a ele.

Para usar a ID de toque sem o conjunto de chaves utilizando a API de autenticação Local, existem algumas funções que podemos usar. Eles são detalhados abaixo:

*   `CanEvaluatePolicy` – Isso simplesmente verifica se o dispositivo é capaz de aceitar a ID de toque.
*   `EvaluatePolicy` – Isso inicia a operação de autenticação e exibe a interface do usuário e retorna um `true` ou `false` resposta.
*   `DeviceOwnerAuthenticationWithBiometrics` – Esta é a política que pode ser usada para mostrar a tela Touch ID. Vale a pena observar que não há nenhum mecanismo de fallback senha aqui, em vez disso, você deve implementar essa fallback em seu aplicativo para permitir que os usuários ignorar a autenticação de ID de toque.

Há algumas limitações com o uso da autenticação Local, que estão listados abaixo:

*   Assim como ocorre com o conjunto de chaves, ele só pode ser executado em primeiro plano. Chamando-o em um thread em segundo plano fará com que ele falha.
*   Tenha em mente que a avaliação de política pode falhar. Um botão de senha precisará ser implementada como um fallback.
*   Você deve fornecer um `localizedReason` para explicar por que a autenticação é necessária. Isso ajuda a criar a relação de confiança com o usuário.

Em seguida, na seção abaixo, examinaremos como implementar o API levando essas limitações em consideração.

## <a name="adding-touch-id-to-your-application"></a>Adicionando a ID de toque para seu aplicativo

Nas seções anteriores, examinamos a teoria por trás de acesso e autenticação usando o conjunto de chaves e autenticação Local. Agora, obtemos uma olhada em como você pode integrar o Touch ID no seu aplicativo.

### <a name="walkthrough"></a>Passo a passo

Então, vamos adicionar alguns autenticação de ID de toque para nosso aplicativo. Este passo a passo, vamos usar o [tabela Storyboard](https://developer.xamarin.com/samples/StoryboardTable/) exemplo. Queremos garantir que somente o proprietário do dispositivo pode adicionar algo a essa lista, nós não desejamos para ocupar, permitindo que qualquer pessoa que adicionar um item!

1.  Baixe o exemplo e execute-o no Visual Studio para Mac.
2.  Clique duas vezes em `MainStoryboard.Storyboard` para abrir o exemplo no Designer de iOS. Com este exemplo, queremos adicionar uma nova tela de nosso aplicativo, que controlará a autenticação. Isso irá antes atual `MasterViewController`.
3.  Arraste um novo **View Controller** do **caixa de ferramentas** para o **superfície de Design**. Definir isso como o **raiz View Controller** por **Ctrl + arrastar** do **navegação controlador**:

    [![](touchid-images/image4.png "Defina o controlador de exibição de raiz")](touchid-images/image4.png)
4.  Nomeie o novo controlador de exibição `AuthenticationViewController`.
5.  Em seguida, arraste um botão e colocá-lo no `AuthenticationViewController`. Chamar essa `AuthenticateButton`e dê a ele o texto `Add a Chore`.
6.  Criar um evento no `AuthenticateButton` chamado `AuthenticateMe`.
7.  Criar um manual atender de `AuthenticationViewController` clicando na barra preta na parte inferior e **Ctrl + arrastar** da barra de para o `MasterViewController` e escolhendo **push** (ou **Mostrar** Se estiver usando classes de tamanho):

    [![](touchid-images/image5.png "Arraste na barra de para o MasterViewController e optando por push ou mostrar")](touchid-images/image6.png)
8.  Clique em atender recém-criado e atribua o identificador `AuthenticationSegue`, conforme ilustrado abaixo:

    [![](touchid-images/image7.png "Definir o identificador de segue AuthenticationSegue")](touchid-images/image7.png)
9.  Adicione o seguinte código ao `AuthenticationViewController`:

    ```
    partial void AuthenticateMe (UIButton sender)
        {
            var context = new LAContext();
            NSError AuthError;
            var myReason = new NSString("To add a new chore");


            if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
                var replyHandler = new LAContextReplyHandler((success, error) => {

                    this.InvokeOnMainThread(()=>{
                        if(success){
                            Console.WriteLine("You logged in!");
                            PerformSegue("AuthenticationSegue", this);
                        }
                        else{
                            //Show fallback mechanism here
                        }
                    });

                });
                context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
            };
        }
    ```

Isso é todo o código que necessário para implementar a autenticação de ID de toque usando a autenticação Local. As linhas destacadas na imagem abaixo mostram o uso da autenticação Local:

[![](touchid-images/image8.png "As linhas destacadas mostram o uso da autenticação Local")](touchid-images/image8.png)

Primeiro, é preciso estabelecer se o dispositivo é capaz de aceitar a ID de toque de entrada, usando o `CanEvaluatePolicy` e passar a política `DeviceOwnerAuthenticationWithBiometrics`. Se isso for verdadeiro, podemos exibir a interface de usuário do ID de toque usando `EvaluatePolicy`. Há três partes de informações que temos para passar para `EvaluatePolicy` – a política em si, explicando por que a autenticação é necessária uma cadeia de caracteres e um manipulador de resposta. O manipulador de resposta informa ao aplicativo que ele deve fazer no caso de uma autenticação bem-sucedida ou não. Vamos analisar mais detalhadamente o manipulador de resposta:

[![](touchid-images/image9.png "O manipulador de resposta")](touchid-images/image9.png)

O manipulador de resposta é especificado do tipo `LAContextReplyHandler`, que usa o sucesso de parâmetros – um `bool` valor e um `NSError` chamado `error`. Se for bem-sucedida, isso é onde podemos realmente executará tudo o que é que você deseja autenticar – nesse caso, exibindo a tela que vamos adicionar uma nova tarefa. Lembre-se de uma das condições de autenticação Local que deve ser executado em primeiro plano, portanto certifique-se de usar `InvokeOnMainThread`:

[![](touchid-images/image10.png "Use InvokeOnMainThread para a autenticação Local")](touchid-images/image10.png)

Finalmente, quando a autenticação for bem-sucedida, queremos fazer a transição para o `MasterViewController`. O `PerformSegue` método pode ser usado para fazer isso:

[![](touchid-images/image11.png "Chamar o método PerformSegue a transição para o MasterViewController")](touchid-images/image11.png)

## <a name="summary"></a>Resumo
Este guia examinamos conjunto de chaves e como isso funciona em iOS. Podemos também explorou ACL, o conjunto de chaves e as alterações a este no iOS. Em seguida, demos uma olhada na estrutura de autenticação Local, o que há de novo no iOS 8 e, em seguida, pesquisados na implementação da autenticação de ID de toque em nosso aplicativo.

## <a name="related-links"></a>Links relacionados

- [Exemplo de ID de toque](https://developer.xamarin.com/samples/StoryboardTable_LocalAuthentication)
- [Exemplo de conjunto de chaves WWDC](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Conjunto de chaves (exemplo)](https://developer.xamarin.com/samples/Keychain/)
