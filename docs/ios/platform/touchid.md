---
title: ID de toque no Xamarin. iOS
description: Este documento descreve como usar o Touch ID, a tecnologia de autenticação de impressão digital biométrica da Apple, em aplicativos Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: cb71fc75b01621ed381f42b41df68fc80309f22c
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121297"
---
# <a name="touch-id-in-xamarinios"></a>ID de toque no Xamarin. iOS

A ID de toque foi introduzida no iOS 7 como um meio de autenticar o usuário-semelhante a uma senha. No entanto, ele estava limitado a desbloquear o dispositivo, usando a loja de aplicativos, usando o iTunes e Autenticando apenas o conjunto de chaves do iCloud.

Agora há duas maneiras de usar o Touch ID como um mecanismo de autenticação em um aplicativo iOS 8 usando a API de autenticação local. No momento, não é possível usar a autenticação local para autenticar remotamente.

Para entender totalmente o Touch ID e seu valor, devemos explorar os serviços de conjunto de chaves e o que essas novas alterações significam para os dados do usuário. O acesso ao conjunto de chaves também foi expandido no iOS 8 com o uso do novo recurso ACLs (listas de controle de acesso).

## <a name="keychain--secure-enclave"></a>Conjunto de chaves & enclave seguro

O conjunto de chaves é um banco de dados grande que fornece armazenamento seguro para senhas, teclas, certificados e notas para uma ID da Apple individual. No iOS 8, um aplicativo sempre tem acesso a seus próprios itens de conjunto de chaves exclusivos e não pode acessar nenhum item de conjunto de chaves de outros aplicativos. Isso difere do OS X em que o conjunto de chaves está desbloqueado com uma única senha, permitindo que qualquer aplicativo com reconhecimento de conjunto de chaves use o conjunto de chaves. Neste artigo, nos concentraremos em como o conjunto de chaves funciona no iOS 8.

O conjunto de chaves é um banco de dados especializado, em que cada linha é conhecida como um item de conjunto de _chaves_. Cada item é descrito por atributos de conjunto de chaves e é composto por valores criptografados. Para permitir o uso eficiente do conjunto de chaves, ele é otimizado para itens pequenos ou _segredos_.
Cada item do conjunto de chaves é protegido pela senha dos usuários e por um segredo de dispositivo exclusivo. Os itens do conjunto de chaves devem ser protegidos mesmo quando os usuários não estiverem usando seus dispositivos. Isso é implementado no iOS apenas permitindo que os itens fiquem disponíveis quando o dispositivo estiver desbloqueado — quando o dispositivo estiver bloqueado, eles ficarão indisponíveis. Eles também podem ser armazenados em um backup criptografado. Um dos principais recursos do conjunto de chaves é impor o controle de acesso; um aplicativo tem acesso à sua parte do conjunto de chaves e todos os outros aplicativos serão impedidos. O diagrama a seguir ilustra como um aplicativo interage com o conjunto de chaves:

[![](touchid-images/image1.png "Este diagrama ilustra como um aplicativo interage com o conjunto de chaves")](touchid-images/image1.png#lightbox)

### <a name="secure-enclave"></a>Proteger enclave

O conjunto de chaves não pode descriptografar o item do conjunto de chaves por si só; em vez disso, isso é feito no *Secure enclave*. O Secure enclave é um coprocessador dentro do chip a7 que é responsável por determinar uma correspondência bem-sucedida dos dados de impressão digital do sensor de ID de toque em relação a uma impressão registrada. Em seguida, ele descriptografará o item do conjunto de chaves e retornará o segredo descriptografado para o conjunto de chaves.

### <a name="working-with-keychain"></a>Trabalhando com o conjunto de chaves

Primeiro, o aplicativo deve consultar o conjunto de chaves para ver se existe uma senha. Se ele não existir, talvez seja necessário solicitar uma senha para que o usuário não seja solicitado continuamente. Se a senha precisar ser atualizada, solicite ao usuário uma nova senha e passe o valor atualizado para o conjunto de chaves.

> [!NOTE]
> Depois de usar um segredo recuperado do conjunto de chaves, todas as referências aos dados devem ser limpas da memória. Nunca o atribua a uma variável global.

## <a name="keychain-acl-and-touch-id"></a>ACL de conjunto de chaves e ID de toque

A lista de controle de acesso é um novo atributo de item do conjunto de chaves no iOS 8 que descreve as informações sobre o que deve ocorrer para permitir que uma determinada operação ocorra. Isso pode estar na forma de exibir uma caixa de diálogo de alerta ou solicitar uma senha. A ACL permite que você defina a acessibilidade e a autenticação para um item de conjunto de chaves. O diagrama a seguir mostra como esse novo atributo se vincula com o restante do item do conjunto de chaves:

[![](touchid-images/image2.png "Este diagrama mostra como esse novo atributo se vincula ao restante do item do conjunto de chaves")](touchid-images/image2.png#lightbox)

A partir do IOS 8, agora há uma nova política de presença de `SecAccessControl`usuário,, que é imposta pelo Secure enclave em um iPhone 5S e superior. Podemos ver na tabela abaixo, como a configuração do dispositivo pode influenciar a avaliação da política:

|Configuração do dispositivo|Avaliação de política|Mecanismo de backup|
|--- |--- |--- |
|Dispositivo sem senha|Sem acesso|Nenhum|
|Dispositivo com senha|Requer senha|Nenhum|
|Dispositivo com ID de toque|Prefere a ID de toque|Permite senha|

Todas as operações dentro do Secure enclave podem confiar umas nas outras. Isso significa que podemos usar o resultado de autenticação de ID de toque para autorizar a descriptografia do item do conjunto de chaves. O enclave seguro também mantém um contador de correspondências de ID de toque com falha, caso em que um usuário terá que reverter para usar a senha.
Uma nova estrutura no iOS 8, chamada _autenticação local_, dá suporte a esse processo de autenticação dentro do dispositivo. Exploraremos isso na próxima seção.

## <a name="local-authentication"></a>Autenticação local

Como estabelecemos na seção anterior, os aplicativos podem usar a autenticação local para autenticar o usuário em conformidade com a política de segurança que foi configurada no dispositivo.

Atualmente, a API fornece apenas dois recursos: Em primeiro lugar, ele ajuda os serviços de conjunto de chaves existentes por meio do uso de novas listas de controle de acesso (ACLs) do conjunto de chaves. Os dados do conjunto de chaves podem ser desbloqueados com a autenticação bem-sucedida de uma impressão digital dos usuários.

Em segundo lugar, o LocalAuthentication fornece dois métodos para autenticar seu aplicativo localmente. Os desenvolvedores devem `CanEvaluatePolicy` usar o para determinar se o dispositivo é capaz de aceitar a ID de `EvaluatePolicy` toque e, em seguida, iniciar a operação de autenticação.

Embora os dois recursos ofereçam autenticação local, eles não fornecem um mecanismo para o aplicativo ou para o usuário se autenticar em um servidor remoto.
A autenticação local fornece uma nova interface de usuário padrão para autenticação. No caso do touch ID, esse é um modo de exibição de alerta com dois botões, conforme ilustrado abaixo. Um botão a ser cancelado e outro para usar os meios de fallback de autenticação – a senha. Há também uma mensagem personalizada que deve ser definida. É uma boa prática usar isso para explicar ao usuário por que a autenticação do touch ID é necessária.

[![](touchid-images/image12.png "O alerta de autenticação de ID de toque")](touchid-images/image12.png#lightbox)

### <a name="with-keychain-services"></a>Com os serviços de conjunto de chaves

Vimos um pouco antes de como um item do conjunto de chaves é descriptografado, usando o Secure enclave para verificar nossa senha. No iOS 8, podemos usar a autenticação local para solicitar a verificação da ID de toque em conjunto com o recurso listas de controle de acesso, que fornece a implementação do mecanismo de fallback ou a senha.
Para usar a ACL, devemos usar a `SecAccessControl` política e, em seguida, verificar o estado do dispositivo `SecAccessible.WhenPasscodeSetThisDeviceOnly` usando `SecAccessible.WhenUnlocked`ou.

#### <a name="considerations-with-acl"></a>Considerações com ACL

Há muitas coisas que devemos ter em mente ao usar a ACL com o conjunto de chaves e algumas delas estão listadas abaixo:

- Usar somente com aplicativo de primeiro plano – se você chamar qualquer operação de conjunto de chaves em um thread em segundo plano, a chamada falhará.
- Adicionar e atualizar itens do conjunto de chaves pode exigir autenticação.
- Se uma solicitação retornar vários itens correspondentes no conjunto de chaves, a autenticação poderá ser necessária.
- Os itens protegidos por ACL são somente dispositivo e, portanto, não são sincronizados ou submetidos a backup.

### <a name="using-local-authentication-without-keychain-services"></a>Usando a autenticação local sem serviços de conjunto de chaves

A autenticação local foi criada como uma maneira de coletar credenciais, como senha ou ID de toque, e para trabalhar com o enclave seguro para concluir a autenticação do usuário. Imagine-o como uma ponte entre seu aplicativo e o enclave seguro, que nunca pode se comunicar diretamente entre si. Ele também pode ser usado para avaliação de política para seu aplicativo.

Para fazer isso, um aplicativo chama a avaliação da política dentro da autenticação local, que inicia a operação dentro do Secure enclave. Você pode aproveitar isso para fornecer autenticação ao seu aplicativo, sem consultar diretamente/acessar o Secure enclave.

[![](touchid-images/image13a.png "Usando a autenticação local sem serviços de conjunto de chaves")](touchid-images/image13a.png#lightbox)

O uso da autenticação local em seu aplicativo fornece uma maneira simples de implementar a verificação do usuário, por exemplo, para desbloquear um recurso exclusivamente para os olhos do proprietário do dispositivo, como aplicativos bancários, ou para auxiliar os controles dos pais para o indivíduo aplicativo. Você também pode usá-lo como uma maneira de estender a autenticação que já existe – os usuários como suas informações para serem seguros, mas também gostam de ter opções.

A segurança da autenticação local difere da do conjunto de chaves. Por exemplo, ao usar o conjunto de chaves, a relação de confiança é entre o sistema operacional e o enclave seguro. Com a autenticação local, ele está entre o aplicativo e o sistema operacional, o que significa que você só tem acesso aos resultados do enclave seguro, não ao próprio enclave seguro.

No assunto da segurança, também é extremamente importante saber que não há **acesso** a dedos registrados ou imagens de impressão digital. O Secure enclave é o proprietário dessas informações e, portanto, nenhum outro componente do sistema tem acesso a ela.

Para usar o Touch ID sem o conjunto de chaves aproveitando a API de autenticação local, há algumas funções que podemos usar. Eles são detalhados abaixo:

- `CanEvaluatePolicy`– Isso simplesmente verificará se o dispositivo é capaz de aceitar a ID de toque.
- `EvaluatePolicy`– Isso inicia a operação de autenticação e exibe a interface do usuário e `true` retorna `false` uma resposta ou.
- `DeviceOwnerAuthenticationWithBiometrics`– Esta é a política que pode ser usada para mostrar a tela de ID de toque. Vale a pena observar que não há nenhum mecanismo de fallback de senha aqui. em vez disso, você deve implementar esse fallback em seu aplicativo para permitir que os usuários ignorem a autenticação de ID de toque.

Há algumas limitações com o uso da autenticação local, que estão listadas abaixo:

- Assim como acontece com o conjunto de chaves, ele só pode ser executado em primeiro plano. Chamá-lo em um thread em segundo plano fará com que ele falhe.
- Tenha em mente que a avaliação da política pode falhar. Um botão de senha precisará ser implementado como um retorno.
- Você deve fornecer um `localizedReason` para explicar por que a autenticação é necessária. Isso ajuda a criar confiança com o usuário.

Em seguida, na seção abaixo, veremos como implementar a API levando essas advertências em consideração.

## <a name="adding-touch-id-to-your-application"></a>Adicionando a ID de toque ao seu aplicativo

Nas seções anteriores, examinamos a teoria por trás do acesso e da autenticação usando o conjunto de chaves e a autenticação local. Agora vamos dar uma olhada em como você pode integrar o Touch ID em seu aplicativo.

### <a name="walkthrough"></a>Passo a passo

Então, vamos dar uma olhada na adição de uma autenticação de ID de toque ao nosso aplicativo. Neste tutorial, vamos atualizar o exemplo de [tabela storyboard](https://docs.microsoft.com/samples/xamarin/ios-samples/data/storyboardtable/) , adicionando autenticação local para que ele funcione como o exemplo de [autenticação de storyboard – local Authentication](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication) , que permite apenas que usuários autenticados adicionem tarefas à lista.

1. Baixe o exemplo e execute-o em Visual Studio para Mac.
2. Clique duas vezes `MainStoryboard.Storyboard` em on para abrir o exemplo no designer do Ios. Com este exemplo, queremos adicionar uma nova tela ao nosso aplicativo, que controlará a autenticação. Isso vai antes do atual `MasterViewController`.
3. Arraste um novo **controlador de exibição** da **caixa de ferramentas** para a **design Surface**. Defina como o **controlador de exibição raiz** por **Ctrl + arrastar** do **controlador de navegação**:

    [![](touchid-images/image4.png "Definir o controlador de exibição raiz")](touchid-images/image4.png#lightbox)
4. Nomeie o novo controlador `AuthenticationViewController`de exibição.
5. Em seguida, arraste um botão e coloque-o `AuthenticationViewController`no. Chame isso `AuthenticateButton`e dê a ele o texto `Add a Chore`.
6. Crie um evento no `AuthenticateButton` chamado. `AuthenticateMe`
7. Crie um transição manual do `AuthenticationViewController` clicando na barra preta na parte inferior e **Ctrl + arraste** da barra para a `MasterViewController` e escolhendo **enviar por push** (ou **Mostrar** se estiver usando classes de tamanho):

    [![](touchid-images/image5.png "Arraste da barra até o MasterViewController e escolha enviar ou mostrar")](touchid-images/image6.png#lightbox)
8. Clique no transição criado recentemente e dê a ele o identificador `AuthenticationSegue`, conforme ilustrado abaixo:

    [![](touchid-images/image7.png "Definir o identificador transição como AuthenticationSegue")](touchid-images/image7.png#lightbox)
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

Esse é todo o código de que você precisa para implementar a autenticação de ID de toque usando a autenticação local. As linhas realçadas na imagem abaixo mostram o uso da autenticação local:

[![](touchid-images/image8.png "As linhas destacadas mostram o uso da autenticação local")](touchid-images/image8.png#lightbox)

Primeiro, precisamos estabelecer se o dispositivo é capaz de aceitar a entrada de ID de toque, usando o `CanEvaluatePolicy` e passando a política. `DeviceOwnerAuthenticationWithBiometrics` Se isso for verdadeiro, poderemos exibir a interface do usuário da ID `EvaluatePolicy`de toque usando. Há três informações que temos para passar `EvaluatePolicy` – a própria política, uma cadeia de caracteres explicando por que a autenticação é necessária e um manipulador de resposta. O manipulador de resposta informa ao aplicativo o que ele deve fazer no caso de uma autenticação bem-sucedida ou malsucedida. Vamos examinar mais de perto o manipulador de resposta:

[![](touchid-images/image9.png "O manipulador de resposta")](touchid-images/image9.png#lightbox)

O manipulador de resposta é especificado do `LAContextReplyHandler`tipo, que toma os parâmetros Success – `bool` um valor e um `NSError` chamado `error`. Se for bem-sucedido, é aí que executaremos qualquer coisa que quisermos autenticar – nesse caso, exibindo a tela que nos permitirá adicionar uma nova tarefa. Lembre-se de que uma das advertências da autenticação local é que ela deve ser executada em primeiro plano, portanto, `InvokeOnMainThread`certifique-se de usar:

[![](touchid-images/image10.png "Usar InvokeOnMainThread para autenticação local")](touchid-images/image10.png#lightbox)

Por fim, quando a autenticação tiver sido bem-sucedida, queremos fazer a transição para `MasterViewController`o. O `PerformSegue` método pode ser usado para fazer isso:

[![](touchid-images/image11.png "Chamar o método PerformSegue para fazer a transição para o MasterViewController")](touchid-images/image11.png#lightbox)

## <a name="summary"></a>Resumo

Neste guia, examinamos o conjunto de chaves e como isso funciona no iOS. Também exploramos a ACL do conjunto de chaves e mudamos para isso no iOS. Em seguida, examinamos a estrutura de autenticação local, que é nova no iOS 8 e depois examinamos a implementação da autenticação de ID de toque em nosso aplicativo.

## <a name="related-links"></a>Links relacionados

- [Tabela storyboard – autenticação local](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable-localauthentication)
- [Exemplo de WWDC de conjunto de chaves](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-keychaintouchid/)
- [Conjunto de chaves (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/keychain/)
