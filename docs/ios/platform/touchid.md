---
title: ID de toque no xamarin. IOS
description: Este documento descreve como usar o Touch ID, tecnologia de autenticação de impressão digital biométrica da Apple, em aplicativos xamarin. IOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 2d67bc71361e335515cfba8b5a20e157ed6b6b05
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087747"
---
# <a name="touch-id-in-xamarinios"></a>ID de toque no xamarin. IOS

ID de toque foi introduzido no iOS 7 como um meio de autenticação do usuário – semelhante a uma senha. No entanto, ele era limitado para desbloquear o dispositivo, usando o App Store, usando o iTunes e autenticar o conjunto de chaves do iCloud apenas.

Agora há duas maneiras de usar o Touch ID como um mecanismo de autenticação em um aplicativo do iOS 8 usando a API de autenticação Local. Atualmente, não é possível usar a autenticação Local para autenticar remotamente.

Para entender completamente o Touch ID e seu valor, podemos deve explorar serviços de conjunto de chaves e o que significam a essas novas alterações para dados do usuário. Conjunto de chaves de acesso também foi expandido no iOS 8 por meio do uso do novo recurso de listas de controle de acesso (ACLs).

## <a name="keychain--secure-enclave"></a>Conjunto de chaves & Enclave seguro

Conjunto de chaves é um banco de dados grande, fornecendo o armazenamento seguro de senhas, chaves, certificados e notas para uma ID da Apple individual. No iOS 8 um aplicativo sempre tem acesso a seus próprios itens de conjunto de chaves exclusivo e não pode acessar todos os itens ao conjunto de chaves de outros aplicativos. Isso difere dos X em que o conjunto de chaves é desbloqueado com uma única senha, permitindo que qualquer aplicativo com reconhecimento de Keychain Services usam o conjunto de chaves. Neste artigo, nos concentraremos no funcionamento do conjunto de chaves no iOS 8.

Conjunto de chaves é um banco de dados especializado, onde cada linha é conhecida como uma _Item de conjunto de chaves_. Cada item é descrita por atributos do conjunto de chaves e é composto de valores criptografados. Para permitir um uso eficiente de conjunto de chaves, ele é otimizado para itens pequenos, ou _segredos_.
Cada item de conjunto de chaves é protegido por um unique device secret e a senha de usuários. Itens do conjunto de chaves devem ser protegidos, mesmo quando os usuários não estão usando seus dispositivos. Isso é implementado no iOS, permitindo apenas os itens a serem disponibilizados quando o dispositivo seja desbloqueado — quando o dispositivo é bloqueado se tornam indisponíveis. Eles também podem ser armazenados em um backup criptografado. Um dos principais recursos do conjunto de chaves é para impor o controle de acesso; um aplicativo tem acesso à sua parte do conjunto de chaves, e todos os outros aplicativos serão impedidos. O diagrama a seguir ilustra como um aplicativo interage com o conjunto de chaves:

[![](touchid-images/image1.png "Este diagrama ilustra como um aplicativo interage com o conjunto de chaves")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Enclave seguro

O conjunto de chaves não é possível descriptografar o item de conjunto de chaves por si só; em vez disso, isso é feito na *Enclave seguro*. O enclave seguro é um processador de colegas dentro o chip de A7 é responsável por determinar uma correspondência com êxito dos dados de impressão digital do sensor de ID de toque em relação a uma impressão registrado. Ela, em seguida, descriptografar o item de conjunto de chaves e retornará o segredo descriptografado no conjunto de chaves.

### <a name="working-with-keychain"></a>Trabalhando com o conjunto de chaves

Primeiro, seu aplicativo deve consultar no conjunto de chaves para ver se uma senha existe. Se ele não existir, você talvez precise solicitar uma senha para que o usuário não é solicitado continuamente. Se a senha precisa ser atualizado, solicitar uma nova senha do usuário e passar o valor atualizado para o conjunto de chaves.

> [!NOTE]
> Depois de usar um segredo recuperados do conjunto de chaves, todas as referências aos dados devem ser removidas da memória. Nunca atribuí-lo a uma variável global.

## <a name="keychain-acl-and-touch-id"></a>ACL de conjunto de chaves e o Touch ID

Lista de controle de acesso é um novo atributo de item de conjunto de chaves no iOS 8 que descreve as informações sobre o que deve acontecer para permitir que uma determinada operação ocorra. Isso pode ser na forma de exibir uma caixa de diálogo de alerta ou solicitar uma senha. ACL permite que você defina a acessibilidade e a autenticação para um Item de conjunto de chaves. O diagrama a seguir mostra como esse novo atributo está associado com o restante do item ao conjunto de chaves:

[![](touchid-images/image2.png "Este diagrama mostra como esse novo atributo está associado com o restante do item ao conjunto de chaves")](touchid-images/image2.png#lightbox)

A partir do iOS 8, agora há uma nova política de presença do usuário, `SecAccessControl`, que é imposto pelo enclave seguro em um iPhone 5s e acima. Podemos ver na tabela a seguir, assim como a configuração do dispositivo pode influenciar a avaliação da política:

|Configuração do dispositivo|Avaliação de política|Mecanismo de backup|
|--- |--- |--- |
|Dispositivo sem senha|Sem acesso|Nenhum|
|Dispositivo com senha|Requer senha|Nenhum|
|Dispositivo com a ID de toque|Prefere o Touch ID|Permite que a senha|

Todas as operações dentro de Enclave seguro podem confiam uns nos outros. Isso significa que podemos usar o resultado da autenticação de ID de toque para autorizar a descriptografia de item de conjunto de chaves. O Enclave seguro também mantém um contador de correspondências com falha do Touch ID, nesse caso um usuário terá que voltar a usar a senha.
Uma nova estrutura no iOS 8, chamada _autenticação Local_, dá suporte a esse processo de autenticação do dispositivo. Vamos explorar isso na próxima seção.

## <a name="local-authentication"></a>Autenticação local

Como podemos estabelecido na seção anterior, os aplicativos podem usar autenticação Local para autenticar o usuário de acordo com a política de segurança que foi configurado no dispositivo.

Atualmente, a API fornece apenas dois recursos: Em primeiro lugar, ele ajuda os serviços existentes do conjunto de chaves com o uso de novo conjunto de chaves Access Control Lists (ACLs). Dados do conjunto de chaves podem ser desbloqueados com a autenticação bem-sucedida de uma impressão digital de usuários.

Em segundo lugar, LocalAuthentication fornece dois métodos para autenticar seu aplicativo localmente. Os desenvolvedores devem utilizar `CanEvaluatePolicy` para determinar se o dispositivo é capaz de aceitar o Touch ID e, em seguida, `EvaluatePolicy` para iniciar a operação de autenticação.

Embora os dois recursos ofereçam autenticação local, eles não fornecem um mecanismo para o aplicativo ou o usuário autenticar para um servidor remoto.
Autenticação local fornece uma nova interface de usuário padrão para autenticação. No caso do Touch ID, isso é uma exibição de alerta com dois botões, conforme ilustrado abaixo. Um botão Cancelar, e um para usar os meios de fallback de autenticação – a senha. Também é uma mensagem personalizada que deve ser definida. É recomendável usar isso para explicar ao usuário por que a autenticação de ID de toque é necessária.

[![](touchid-images/image12.png "O alerta de autenticação de ID de toque")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Com os serviços do conjunto de chaves

Nós examinamos um pouco mais cedo como um item de conjunto de chaves é descriptografada, usando o enclave seguro para verificar o nosso código de acesso. No iOS 8, podemos usar a autenticação Local para solicitar a verificação de ID de toque em conjunto com o recurso de listas de controle de acesso, que fornece a implementação do mecanismo de fallback, ou a senha.
Para usar a ACL que devemos usar o `SecAccessControl` política e, em seguida, verificar o estado do dispositivo usando `SecAccessible.WhenPasscodeSetThisDeviceOnly` ou `SecAccessible.WhenUnlocked`.

#### <a name="considerations-with-acl"></a>Considerações de ACL

Há muitas coisas que podemos deve ter em mente ao usar o ACL com o conjunto de chaves e algumas delas estão listadas abaixo:

-   Use somente com o aplicativo de primeiro plano – se você chamar qualquer operação de conjunto de chaves em um thread em segundo plano que a chamada falhará.
-   Adicionando e Atualizando itens de conjunto de chaves podem exigir autenticação.
-   Se uma solicitação retornar vários itens correspondentes no conjunto de chaves, a autenticação pode ser necessária.
-   Itens protegidos de ACL são somente para dispositivos e, portanto, não sincronizado ou submetido a backup.

### <a name="using-local-authentication-without-keychain-services"></a>Usando a autenticação Local sem os serviços de conjunto de chaves

Autenticação local foi criada como uma maneira de coletar credenciais, como senha ou ID de toque e trabalhar com o Enclave seguro para concluir a autenticação do usuário. Pense nele como uma ponte entre seu aplicativo e o Enclave seguro, nunca diretamente pode se comunicar entre si. Ele também pode ser usado para avaliação de política para o seu aplicativo.

Para fazer isso em um aplicativo chama a avaliação de política dentro de autenticação Local, que inicia a operação de Enclave seguro. Você pode aproveitar isso para fornecer autenticação ao seu aplicativo, sem consultar/acessar diretamente o Enclave seguro.

[![](touchid-images/image13a.png "Usando a autenticação Local sem os serviços de conjunto de chaves")](touchid-images/image13a.png#lightbox)

Usando a autenticação Local em seu aplicativo fornece uma maneira simple de implementar a verificação de usuário, por exemplo, para desbloquear um recurso somente para os olhos do proprietário do dispositivo, como aplicativos de serviços bancários, ou para controles dos pais do auxiliar do indivíduo aplicativo. Você também pode usá-lo como uma maneira de estender a autenticação já existe – usuários, como suas informações de segurança, mas eles também gostam de ter opções.

A segurança da autenticação local é diferente do conjunto de chaves. Por exemplo, ao usar o conjunto de chaves, a relação de confiança é entre o sistema operacional e o Enclave seguro. Com a autenticação local, ele é entre o aplicativo e o sistema operacional, o que significa que você só tiver acesso aos resultados de Enclave seguro, não o Secure Enclave em si.

Sobre o assunto de segurança, também é extremamente importante saber que há **nenhum acesso** dedos registrados ou imagens da impressão digital. O Enclave seguro é o proprietário dessas informações e, portanto, nenhum outro componente do sistema tem acesso a ele.

Para usar o Touch ID sem um conjunto de chaves, aproveitando a API de autenticação Local, há algumas funções que podemos usar. Eles são detalhados abaixo:

*   `CanEvaluatePolicy` – Isso simplesmente verificará se o dispositivo é capaz de aceitar a ID de toque.
*   `EvaluatePolicy` – Isso inicia a operação de autenticação e exibe a interface do usuário e retorna um `true` ou `false` resposta.
*   `DeviceOwnerAuthenticationWithBiometrics` – Esta é a política que pode ser usada para mostrar a tela Touch ID. Vale a pena observar que não há nenhum mecanismo de fallback senha aqui, em vez disso, você deve implementar essa fallback em seu aplicativo para permitir que os usuários ignorem a autenticação de ID de toque.

Há algumas limitações com o uso da autenticação Local, que estão listados abaixo:

*   Assim como acontece com o conjunto de chaves, ele só pode ser executado em primeiro plano. Chamando-o em um thread em segundo plano fará com que ela falhe.
*   Tenha em mente que a avaliação da política pode falhar. Um botão de senha precisará ser implementada como um fallback.
*   Você deve fornecer um `localizedReason` para explicar por que a autenticação é necessária. Isso ajuda a criar a relação de confiança com o usuário.

Em seguida, na seção abaixo, podemos examinar como implementar a API levando essas limitações em consideração.

## <a name="adding-touch-id-to-your-application"></a>Adicionando a ID de toque ao seu aplicativo

Nas seções anteriores, examinamos a teoria por trás de acesso e autenticação usando o conjunto de chaves e a autenticação Local. Agora, faremos uma olhada em como você pode integrar o Touch ID em seu aplicativo.

### <a name="walkthrough"></a>Passo a passo

Portanto, vamos examinar adicionando alguns autenticação de ID de toque para nosso aplicativo. Neste passo a passo, vamos atualizar o [tabela de Storyboard](https://developer.xamarin.com/samples/StoryboardTable/) exemplo, adicionando a autenticação local para que ele funcione como o [Storyboard tabela – autenticação Local](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) exemplo, que permite apenas usuários autenticados para adicionar tarefas à lista.

1. Baixe o exemplo e executá-lo no Visual Studio para Mac.
2. Clique duas vezes em `MainStoryboard.Storyboard` para abrir o exemplo no Designer do iOS. Com este exemplo, queremos adicionar uma nova tela ao nosso aplicativo, que controlará a autenticação. Isso irá antes atual `MasterViewController`.
3. Arraste um novo **controlador de exibição** da **caixa de ferramentas** para o **superfície de Design**. Defina isso como o **controlador de exibição de raiz** pela **Ctrl + arrastar** do **controlador de navegação**:

    [![](touchid-images/image4.png "Defina o controlador de exibição de raiz")](touchid-images/image4.png#lightbox)
4.  Nomeie o novo controlador de exibição `AuthenticationViewController`.
5. Em seguida, arraste um botão e colocá-lo no `AuthenticationViewController`. Chamá-lo `AuthenticateButton`e dê a ele o texto `Add a Chore`.
6. Criar um evento na `AuthenticateButton` chamado `AuthenticateMe`.
7. Criar um manual segue a partir `AuthenticationViewController` clicando na barra preta na parte inferior e **Ctrl + arrastar** na barra de para o `MasterViewController` e escolhendo **push** (ou **Mostrar** Se estiver usando classes de tamanho):

    [![](touchid-images/image5.png "Arraste a barra de para o MasterViewController e escolhendo o envio por push ou mostrar")](touchid-images/image6.png#lightbox)
8. Clique em recém-criado segue e dê a ele o identificador `AuthenticationSegue`, conforme ilustrado abaixo:

    [![](touchid-images/image7.png "Definir o identificador de segue AuthenticationSegue")](touchid-images/image7.png#lightbox)
9. Adicione o seguinte código ao `AuthenticationViewController`:

    ```csharp
    partial void AuthenticateMe (UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var myReason = new NSString("To add a new chore");

        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError)){
            var replyHandler = new LAContextReplyHandler((success, error) => {
                this.InvokeOnMainThread(()=> {
                    if(success)
                    {
                        Console.WriteLine("You logged in!");
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        // Show fallback mechanism here
                    }
                });
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, myReason, replyHandler);
        };
    }
    ```

Isso é todo o código que necessário para implementar a autenticação de ID de toque, usando a autenticação Local. As linhas realçadas na imagem abaixo mostram o uso da autenticação Local:

[![](touchid-images/image8.png "As linhas destacadas mostram o uso da autenticação Local")](touchid-images/image8.png#lightbox)

Primeiro, precisamos estabelecer se o dispositivo é capaz de aceitar o Touch ID de entrada, usando o `CanEvaluatePolicy` e passar a política `DeviceOwnerAuthenticationWithBiometrics`. Se isso é verdade, podemos exibir a interface de usuário de ID de toque usando `EvaluatePolicy`. Há três partes de informações que temos para passar para `EvaluatePolicy` – a política em si, explicando por que a autenticação é necessária uma cadeia de caracteres e um manipulador de resposta. O manipulador de resposta informa o aplicativo que ele deve fazer no caso de uma autenticação bem-sucedida ou malsucedida. Vamos examinar mais detalhadamente o manipulador de resposta:

[![](touchid-images/image9.png "O manipulador de resposta")](touchid-images/image9.png#lightbox)

O manipulador de resposta é especificado do tipo `LAContextReplyHandler`, que usa o sucesso de parâmetros – uma `bool` valor e um `NSError` chamado `error`. Se for bem-sucedida, isso é onde podemos irão realmente executar tudo o que é, queremos autenticar – nesse caso, exibir a tela que vamos adicionará uma nova tarefa. Lembre-se de uma das Advertências sobre autenticação Local é que ele deve ser executado em primeiro plano, portanto, certifique-se de usar `InvokeOnMainThread`:

[![](touchid-images/image10.png "Use InvokeOnMainThread para autenticação Local")](touchid-images/image10.png#lightbox)

Por fim, quando a autenticação tiver sido bem-sucedida, queremos fazer a transição para o `MasterViewController`. O `PerformSegue` método pode ser usado para fazer isso:

[![](touchid-images/image11.png "Chame o método PerformSegue para fazer a transição para o MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Resumo
Neste guia, nós examinamos ao conjunto de chaves e como isso funciona no iOS. Também exploramos ACL, o conjunto de chaves e as alterações para isso no iOS. Em seguida, demos uma olhada na estrutura de autenticação Local, o que há de novo no iOS 8 e, em seguida, examinamos Implementando a autenticação de ID de toque em nosso aplicativo.

## <a name="related-links"></a>Links relacionados

- [Tabela de storyboard – autenticação Local](https://developer.xamarin.com/samples/monotouch/StoryboardTable_LocalAuthentication/) 
- [Exemplo de conjunto de chaves WWDC](https://developer.xamarin.com/samples/KeychainTouchID/)
- [Conjunto de chaves (amostra)](https://developer.xamarin.com/samples/Keychain/)
