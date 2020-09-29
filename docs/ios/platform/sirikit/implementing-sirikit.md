---
title: Implementando SiriKit no Xamarin. iOS
description: Este documento descreve as etapas necessárias para implementar o suporte do SiriKit em aplicativos Xamarin. iOS. Ele aborda as extensões de intenções e as extensões de interface do usuário de tentativas.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: ed31e0262fecccf8974961d45d02388aeb8e85ea
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434422"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implementando SiriKit no Xamarin. iOS

_Este artigo aborda as etapas necessárias para implementar o suporte do SiriKit em aplicativos Xamarin. iOS._

Novo no iOS 10, o SiriKit permite que um aplicativo Xamarin. iOS forneça serviços que são acessíveis para o usuário usando o Siri e o aplicativo Maps em um dispositivo iOS. Este artigo aborda as etapas necessárias para implementar o suporte do SiriKit nos aplicativos Xamarin. iOS adicionando as extensões retenções necessárias, as extensões de interface do usuário e o vocabulário.

O Siri trabalha com o conceito de **domínios**, grupos de ações conhecidas para tarefas relacionadas. Cada interação que o aplicativo tem com siri deve se enquadrar em um de seus domínios de serviço conhecidos da seguinte maneira:

- Chamada de áudio ou vídeo.
- Reserva de um Ride.
- Gerenciando exercícios.
- Mensagens.
- Pesquisando fotos.
- Enviando ou recebendo pagamentos.

Quando o usuário faz uma solicitação de Siri envolvendo um dos serviços da extensão do aplicativo, o SiriKit envia a extensão um objeto de **intenção** que descreve a solicitação do usuário junto com quaisquer dados de suporte. em seguida, a extensão do aplicativo gera o objeto de **resposta** apropriado para a **intenção**determinada, detalhando como a extensão pode lidar com a solicitação.

Este guia apresentará um exemplo rápido de inclusão do suporte a SiriKit em um aplicativo existente. Para fins deste exemplo, usaremos o aplicativo MonkeyChat falso:

[![O ícone de MonkeyChat](implementing-sirikit-images/monkeychat01.png)](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantém seu próprio livro de contatos dos amigos do usuário, cada um associado a um nome de tela (como bobo, por exemplo) e permite que o usuário envie chats de texto para cada amigo pelo nome da tela.

## <a name="extending-the-app-with-sirikit"></a>Estendendo o aplicativo com SiriKit

Conforme mostrado no guia de [conceitos do Understanding SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) , há três partes principais envolvidas na extensão de um aplicativo com o SiriKit:

[![Estendendo o aplicativo com o diagrama SiriKit](implementing-sirikit-images/elements01.png)](implementing-sirikit-images/elements01.png#lightbox)

Estão incluídos:

1. **Extensão de tentativas** – verifica as respostas dos usuários, confirma que o aplicativo pode lidar com a solicitação e, na verdade, executa a tarefa para atender à solicitação do usuário.
2. Extensão da interface **do usuário de tentativas**  -  *Opcional*, fornece uma interface do usuário personalizada para as respostas no ambiente Siri e pode trazer a interface do usuário de aplicativos e a identidade visual para o Siri para enriquecer a experiência dos usuários.
3. **Aplicativo** – fornece ao aplicativo os vocabulários específicos do usuário para auxiliar o Siri a trabalhar com ele. 

Todos esses elementos e as etapas para incluí-los no aplicativo serão abordadas detalhadamente nas seções abaixo.

## <a name="preparing-the-app"></a>Preparando o aplicativo

O SiriKit é criado em extensões, no entanto, antes de adicionar qualquer extensão ao aplicativo, há algumas coisas que o desenvolvedor precisa fazer para ajudar na adoção do SiriKit.

### <a name="moving-common-shared-code"></a>Movendo código compartilhado comum 

Primeiro, o desenvolvedor pode mover parte do código comum que será compartilhado entre o aplicativo e as extensões em _projetos compartilhados_, _PCLs (bibliotecas de classe portátil)_ ou _bibliotecas nativas_.

As extensões precisarão ser capazes de fazer todas as coisas que o aplicativo faz. Nos termos do aplicativo MonkeyChat de exemplo, coisas como localizar contatos, adicionar novos contatos, enviar mensagens e recuperar o histórico de mensagens.

Ao mover esse código comum para um projeto compartilhado, PCL ou biblioteca nativa, ele facilita a manutenção desse código em um local comum e garante que a extensão e o aplicativo pai forneçam experiências e funcionalidades uniformes para o usuário.

No caso do aplicativo de exemplo MonkeyChat, os modelos de dados e o código de processamento, como acesso à rede e ao banco de dado, serão movidos para uma biblioteca nativa.

Faça o seguinte:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie o Visual Studio para Mac e abra o aplicativo MonkeyChat.
2. Clique com o botão direito do mouse no nome da solução na **painel de soluções** e selecione **Adicionar**  >  **novo projeto...**: 

    [![Adicionar um novo projeto](implementing-sirikit-images/prep01.png)](implementing-sirikit-images/prep01.png#lightbox)
3. Selecione **iOS**  >  **Library**  >  **biblioteca de classes** da biblioteca do IOS e clique no botão **Avançar** : 

    [![Selecionar biblioteca de classes](implementing-sirikit-images/prep02.png)](implementing-sirikit-images/prep02.png#lightbox)
4. Insira `MonkeyChatCommon` para o **nome** e clique no botão **criar** : 

    [![Insira MonkeyChatCommon para o nome](implementing-sirikit-images/prep03.png)](implementing-sirikit-images/prep03.png#lightbox)
5. Clique com o botão direito do mouse na pasta **referências** do aplicativo principal no **Gerenciador de soluções** e selecione **Editar referências...**. Verifique o projeto **MonkeyChatCommon** e clique no botão **OK** : 

    [![Verifique o projeto MonkeyChatCommon](implementing-sirikit-images/prep05.png)](implementing-sirikit-images/prep05.png#lightbox)
6. Na **Gerenciador de soluções**, arraste o código compartilhado comum do aplicativo principal para a biblioteca nativa.
7. No caso do MonkeyChat, arraste as pastas **Datamodels** e **processadores** do aplicativo principal para a biblioteca nativa: 

    [![As pastas datamodels e processadores no Gerenciador de Soluções](implementing-sirikit-images/prep06.png)](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie o Visual Studio e abra o aplicativo MonkeyChat.
2. Clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e selecione **Adicionar**  >  **novo projeto...**.
3. Selecione **Visual C#**  >  **projeto compartilhado** do Visual C# e clique no botão **Avançar** : 

    [![Selecionar biblioteca de classes](implementing-sirikit-images/prep02.w157-sml.png)](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Insira `MonkeyChatCommon` para o **nome** e clique no botão **criar** .
5. Clique com o botão direito do mouse na pasta **referências** do aplicativo principal no **Gerenciador de soluções** e selecione **Editar referências...**. Verifique o projeto **MonkeyChatCommon** e clique no botão **OK** : 

    [![Verifique o projeto MonkeyChatCommon](implementing-sirikit-images/prep05w.png)](implementing-sirikit-images/prep05w.png#lightbox)
6. Na **Gerenciador de soluções**, arraste o código compartilhado comum do aplicativo principal para o projeto compartilhado.
7. No caso do MonkeyChat, arraste as pastas **Datamodels** e **processadores** do aplicativo principal para a biblioteca nativa.

-----

Edite qualquer um dos arquivos que foram movidos para a biblioteca nativa e altere o namespace para corresponder ao da biblioteca. Por exemplo, alterar `MonkeyChat` para `MonkeyChatCommon` :

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

Em seguida, volte para o aplicativo principal e adicione uma `using` instrução para o namespace da biblioteca nativa em qualquer lugar em que o aplicativo usa uma das classes que foram movidas:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>Arquitetando o aplicativo para extensões

Normalmente, um aplicativo se inscreverá em várias tentativas e o desenvolvedor precisa garantir que o aplicativo seja arquitetado para o número apropriado de extensões de intenção.

Na situação em que um aplicativo requer mais de uma intenção, o desenvolvedor tem a opção de colocar todo o seu tratamento de intenção em uma extensão de intenção ou criar uma extensão de intenção separada para cada tentativa.

Se optar por criar uma extensão de intenção separada para cada tentativa, o desenvolvedor poderia acabar duplicando uma grande quantidade de código clichê em cada extensão e criar uma grande quantidade de sobrecarga de processador e de memória.

Para ajudar a escolher entre as duas opções, veja se qualquer uma das tentativas naturalmente pertencerá a ela. Por exemplo, um aplicativo que fez chamadas de áudio e vídeo pode querer incluir ambas as intenções em uma única extensão de intenção, pois elas estão tratando tarefas semelhantes e podem fornecer a maior reutilização de código.

Para qualquer intenção ou grupo de tentativas que não caibam em um grupo existente, crie uma nova extensão de intenção na solução do aplicativo para contê-las.

### <a name="setting-the-required-entitlements"></a>Definindo os direitos necessários

Qualquer aplicativo Xamarin. iOS que inclui a integração do SiriKit, deve ter os direitos corretos definidos. Se o desenvolvedor não definir esses direitos necessários corretamente, eles não poderão instalar ou testar o aplicativo em hardware real iOS 10 (ou superior), que também é requisito, já que o simulador do iOS 10 não dá suporte a SiriKit.

Faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes no `Entitlements.plist` arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Alterne para a guia **Origem**.
3. Adicione a `com.apple.developer.siri` **Propriedade**, defina o **tipo** como `Boolean` e o **valor** como `Yes` : 

    [![Adicionar a propriedade com. Apple. Developer. Siri](implementing-sirikit-images/setup01.png)](implementing-sirikit-images/setup01.png#lightbox)
4. Salve as alterações no arquivo.
5. Clique duas vezes no **arquivo de projeto** no **Gerenciador de soluções** para abri-lo para edição.
6. Selecione **assinatura de pacote do IOS** e verifique se o `Entitlements.plist` arquivo está selecionado no campo **direitos personalizados** : 

    [![Selecione o arquivo. plist de direitos no campo de direitos personalizados](implementing-sirikit-images/setup02.png)](implementing-sirikit-images/setup02.png#lightbox)
7. Clique no botão **OK** para salvar as alterações.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no `Entitlements.plist` arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Adicione a `com.apple.developer.siri` **Propriedade**, defina o **tipo** como `Boolean` e o **valor** como `Yes` : 

    [![Adicionar a propriedade com. Apple. Developer. Siri](implementing-sirikit-images/setup01w.png)](implementing-sirikit-images/setup01w.png#lightbox)
3. Salve as alterações no arquivo.
4. Clique duas vezes no **arquivo de projeto** no **Gerenciador de soluções** para abri-lo para edição.
5. Selecione **assinatura de pacote do IOS** e verifique se o `Entitlements.plist` arquivo está selecionado no campo **direitos personalizados** .

-----

Quando terminar, o arquivo do aplicativo `Entitlements.plist` deverá ser semelhante ao seguinte (em aberto em um editor externo):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>Provisionamento correto do aplicativo

Devido à segurança estrita que a Apple colocou na estrutura SiriKit, qualquer aplicativo Xamarin. iOS que implemente SiriKit _deve_ ter a ID e os direitos do aplicativo corretos (consulte a seção acima) e deve ser assinado com um perfil de provisionamento adequado.

Faça o seguinte no seu Mac:

1. Em um navegador da Web, navegue até [https://developer.apple.com](https://developer.apple.com) e faça logon em sua conta.
2. Clique em **certificados**, **identificadores** e **perfis**.
3. Selecione **perfis de provisionamento** e selecione **IDs de aplicativo**e, em seguida, clique no **+** botão.
4. Insira um **nome** para o novo perfil.
5. Insira uma **ID de pacote** após a recomendação de nomenclatura da Apple.
6. Role para baixo até a seção **serviços de aplicativos** , selecione **SiriKit** e clique no botão **continuar** : 

    [![Selecionar SiriKit](implementing-sirikit-images/setup03.png)](implementing-sirikit-images/setup03.png#lightbox)
7. Verifique todas as configurações e, em seguida, **envie** a ID do aplicativo.
8. Selecione **provisionamento perfis**  >  **desenvolvimento**, clique no **+** botão, selecione a ID da **Apple**e clique em **continuar**.
9. Clique em selecionar **tudo**e em **continuar**.
10. Clique em **selecionar tudo** novamente e, em seguida, clique em **continuar**.
11. Insira um **nome de perfil** usando as sugestões de nomenclatura da Apple e clique em **continuar**.
12. Inicie o Xcode.
13. No menu do Xcode, selecione **preferências...**
14. Selecione **contas**e clique em **Exibir detalhes...** Button 

    [![Selecionar contas](implementing-sirikit-images/setup04.png)](implementing-sirikit-images/setup04.png#lightbox)
15. Clique no botão **baixar todos os perfis** no canto inferior esquerdo: 

    [![Baixar todos os perfis](implementing-sirikit-images/setup05.png)](implementing-sirikit-images/setup05.png#lightbox)
16. Verifique se o **perfil de provisionamento** criado acima foi instalado no Xcode.
17. Abra o projeto para adicionar o suporte do SiriKit ao no Visual Studio para Mac.
18. Clique duas vezes no `Info.plist` arquivo na **Gerenciador de soluções**.
19. Verifique se o **identificador do pacote** corresponde ao criado no portal do desenvolvedor da Apple acima: 

    [![O identificador do pacote](implementing-sirikit-images/setup06.png)](implementing-sirikit-images/setup06.png#lightbox)
20. No **Gerenciador de soluções**, selecione o **projeto**.
21. Clique com o botão direito do mouse no projeto e selecione **Opções**.
22. Selecione **assinatura de pacote do IOS**, selecione a **identidade de assinatura** e o **perfil de provisionamento** criados acima: 

    [![Selecionar a identidade de assinatura e o perfil de provisionamento](implementing-sirikit-images/setup07.png)](implementing-sirikit-images/setup07.png#lightbox)
23. Clique no botão **OK** para salvar as alterações.

> [!IMPORTANT]
> O teste do SiriKit funciona apenas em um dispositivo de hardware iOS 10 real e não no simulador do iOS 10. Se tiver problemas ao instalar um aplicativo Xamarin. iOS habilitado para SiriKit em hardware real, verifique se os direitos necessários, a ID do aplicativo, o identificador de assinatura e o perfil de provisionamento foram configurados corretamente no portal do desenvolvedor da Apple e Visual Studio para Mac.

### <a name="requesting-siri-authorization"></a>Solicitando autorização de Siri

Antes que o aplicativo adicione qualquer vocabulário específico do usuário ou que as extensões de intenções se conectem ao Siri, ele deve solicitar autorização do usuário para acessar o Siri.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Edite o arquivo do aplicativo `Info.plist` , alterne para o modo de exibição de **origem** e adicione a `NSSiriUsageDescription` chave com um valor de cadeia de caracteres que descreve como o aplicativo usará Siri e quais tipos de dados serão enviados. Por exemplo, o aplicativo MonkeyChat pode dizer "MonkeyChat contatos serão enviados para Siri":

[![O NSSiriUsageDescription no editor info. plist](implementing-sirikit-images/request01.png)](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Edite o arquivo do aplicativo `Info.plist` e adicione a `NSSiriUsageDescription` chave com um valor de cadeia de caracteres que descreve como o aplicativo usará Siri e quais tipos de dados serão enviados. Por exemplo, o aplicativo MonkeyChat pode dizer "MonkeyChat contatos serão enviados para Siri":

[![O NSSiriUsageDescription no editor info. plist](implementing-sirikit-images/request01w.png)](implementing-sirikit-images/request01w.png#lightbox)

-----

Chame o `RequestSiriAuthorization` método da `INPreferences` classe quando o aplicativo for iniciado pela primeira vez. Edite a `AppDelegate.cs` classe e faça com que o `FinishedLaunching` método se pareça com o seguinte:

```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });

    return true;
}
```

Na primeira vez que esse método é chamado, um alerta é exibido solicitando que o usuário acesse o Siri. A mensagem que o desenvolvedor adicionou ao `NSSiriUsageDescription` acima será exibida neste alerta. Se o usuário negar inicialmente o acesso, ele poderá usar o aplicativo de **configurações** para conceder acesso ao aplicativo.

A qualquer momento, o aplicativo pode verificar a capacidade do aplicativo de acessar o Siri chamando o `SiriAuthorizationStatus` método da `INPreferences` classe.

### <a name="localization-and-siri"></a>Localização e Siri

Em um dispositivo iOS, o usuário é capaz de selecionar um idioma para Siri que seja diferente do padrão do sistema. Ao trabalhar com dados localizados, o aplicativo precisará usar o `SiriLanguageCode` método da `INPreferences` classe para obter o código de idioma de siri. Por exemplo:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Adicionando vocabulário específico do usuário

O vocabulário específico do usuário vai fornecer palavras ou frases que sejam exclusivas para usuários individuais do aplicativo. Eles serão fornecidos em tempo de execução no aplicativo principal (não nas extensões de aplicativo) como um conjunto ordenado de termos, ordenados em uma prioridade de uso mais significativa para os usuários, com os termos mais importantes no início da lista.

O vocabulário específico do usuário deve pertencer a uma das seguintes categorias:

- Nomes de contato (que não são gerenciados pela estrutura de contatos).
- Marcas de foto.
- Nomes de álbuns de fotos.
- Nomes de ginástica.

Ao selecionar a terminologia para registrar como vocabulário personalizado, escolha apenas os termos que podem ser mal compreendidos por alguém que não esteja familiarizado com o aplicativo. Nunca Registre termos comuns, como "meu treinamento" ou "meu álbum". Por exemplo, o aplicativo MonkeyChat registrará os apelidos associados a cada contato no catálogo de endereços do usuário.

O aplicativo fornece o vocabulário específico do usuário chamando o `SetVocabularyStrings` método da `INVocabulary` classe e passando uma `NSOrderedSet` coleção do aplicativo principal. O aplicativo deve sempre chamar o `RemoveAllVocabularyStrings` método primeiro para remover quaisquer termos existentes antes de adicionar novos. Por exemplo:

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

Com esse código em vigor, ele pode ser chamado da seguinte maneira:

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> Siri trata o vocabulário personalizado como dicas e incorporará a maior parte da terminologia possível. No entanto, o espaço para o vocabulário personalizado é limitado, tornando importante registrar _apenas_ a terminologia que pode ser confusa e, portanto, manter o número total de termos registrados em um mínimo.

Para obter mais informações, consulte nossa [referência de vocabulário específica do usuário](~/ios/platform/sirikit/understanding-sirikit.md) e [especificando a referência de vocabulário personalizada](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)da Apple.

### <a name="adding-app-specific-vocabulary"></a>Adicionando vocabulário específico do aplicativo

O vocabulário específico do aplicativo define as palavras e frases específicas que serão conhecidas por todos os usuários do aplicativo, como tipos de veículo ou nomes de ginástica. Como eles fazem parte do aplicativo, eles são definidos em um `AppIntentVocabulary.plist` arquivo como parte do pacote do aplicativo principal. Além disso, essas palavras e frases devem ser localizadas.

Os termos de vocabulário específicos do aplicativo devem pertencer a uma das seguintes categorias:

- Opções de Ride.
- Nomes de ginástica.

O arquivo de vocabulário específico do aplicativo contém duas chaves de nível raiz:

- `ParameterVocabularies`**Obrigatório** -define os termos e os parâmetros de intenção personalizados do aplicativo aos quais eles se aplicam.
- `IntentPhrases`**Opcional** – contém frases de exemplo usando os termos personalizados definidos no `ParameterVocabularies` .

Cada entrada no `ParameterVocabularies` deve especificar uma cadeia de caracteres de ID, um termo e a intenção à qual o termo se aplica. Além disso, um único termo pode se aplicar a várias intenções.

Para obter uma lista completa de valores aceitáveis e da estrutura de arquivo necessária, consulte a [referência de formato de arquivo de vocabulário do aplicativo](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)da Apple.

Para adicionar um `AppIntentVocabulary.plist` arquivo ao projeto de aplicativo, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com o botão direito do mouse no nome do projeto na **Gerenciador de soluções** e selecione **Adicionar**  >  **novo arquivo...**  >  **Ios**:

    [![Adicionar uma lista de propriedades](implementing-sirikit-images/plist01.png)](implementing-sirikit-images/plist01.png#lightbox)
2. Clique duas vezes no `AppIntentVocabulary.plist` arquivo no **Gerenciador de soluções** para abri-lo para edição.
3. Clique em **+** para adicionar uma chave, defina o **nome** como `ParameterVocabularies` e o **tipo** como `Array` :

    [![Defina o nome como ParameterVocabularies e o tipo como matriz](implementing-sirikit-images/plist02.png)](implementing-sirikit-images/plist02.png#lightbox)
4. Expanda `ParameterVocabularies` e clique no **+** botão e defina o **tipo** como `Dictionary` :

    [![Definir o tipo como Dictionary](implementing-sirikit-images/plist03.png)](implementing-sirikit-images/plist03.png#lightbox)
5. Clique em **+** para adicionar uma nova chave, defina o **nome** como `ParameterNames` e o **tipo** como `Array` :

    [![Defina o nome como ParameterNames e o tipo como matriz](implementing-sirikit-images/plist04.png)](implementing-sirikit-images/plist04.png#lightbox)
6. Clique em **+** para adicionar uma nova chave com o **tipo** de `String` e o valor como um dos nomes de parâmetro disponíveis. Por exemplo `INStartWorkoutIntent.workoutName`:

    [![A chave INStartWorkoutIntent. ginásticaname](implementing-sirikit-images/plist05.png)](implementing-sirikit-images/plist05.png#lightbox)
7. Adicione a `ParameterVocabulary` chave à `ParameterVocabularies` chave com o **tipo** de `Array` :

    [![Adicione a chave ParameterVocabulary à chave ParameterVocabularies com o tipo de matriz](implementing-sirikit-images/plist06.png)](implementing-sirikit-images/plist06.png#lightbox)
8. Adicione uma nova chave com o **tipo** de `Dictionary` :

    [![Adicionar uma nova chave com o tipo de dicionário](implementing-sirikit-images/plist07.png)](implementing-sirikit-images/plist07.png#lightbox)
9. Adicione a `VocabularyItemIdentifier` chave com o **tipo** de `String` e especifique uma ID exclusiva para o termo:

    [![Adicionar a chave VocabularyItemIdentifier com o tipo de cadeia de caracteres e especificar uma ID exclusiva](implementing-sirikit-images/plist08.png)](implementing-sirikit-images/plist08.png#lightbox)
10. Adicione a `VocabularyItemSynonyms` chave com o **tipo** de `Array` :

    [![Adicionar a chave VocabularyItemSynonyms com o tipo de matriz](implementing-sirikit-images/plist09.png)](implementing-sirikit-images/plist09.png#lightbox)
11. Adicione uma nova chave com o **tipo** de `Dictionary` :

    [![Adicionar uma nova chave com o tipo de dicionário](implementing-sirikit-images/plist10.png)](implementing-sirikit-images/plist10.png#lightbox)
12. Adicione a `VocabularyItemPhrase` chave com o **tipo** de `String` e o termo que o aplicativo está definindo:

    [![Adicione a chave VocabularyItemPhrase com o tipo de cadeia de caracteres e o termo que o aplicativo está definindo](implementing-sirikit-images/plist11.png)](implementing-sirikit-images/plist11.png#lightbox)
13. Adicione a `VocabularyItemPronunciation` chave com o **tipo** `String` e a pronúncia fonética do termo:

    [![Adicionar a chave VocabularyItemPronunciation com o tipo de cadeia de caracteres e a pronúncia fonética do termo](implementing-sirikit-images/plist12.png)](implementing-sirikit-images/plist12.png#lightbox)
14. Adicione a `VocabularyItemExamples` chave com o **tipo** de `Array` :

    [![Adicionar a chave VocabularyItemExamples com o tipo de matriz](implementing-sirikit-images/plist13.png)](implementing-sirikit-images/plist13.png#lightbox)
15. Adicione algumas `String` chaves com exemplos de uso do termo:

    [![Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo](implementing-sirikit-images/plist14.png)](implementing-sirikit-images/plist14.png#lightbox)
16. Repita as etapas acima para quaisquer outros termos personalizados que o aplicativo precisa definir.
17. Recolha a `ParameterVocabularies` chave.
18. Adicione a `IntentPhrases` chave com o **tipo** de `Array` :

    [![Adicionar a chave IntentPhrases com o tipo de matriz](implementing-sirikit-images/plist15.png)](implementing-sirikit-images/plist15.png#lightbox)
19. Adicione uma nova chave com o **tipo** de `Dictionary` :

    [![Adicionar uma nova chave com o tipo de dicionário](implementing-sirikit-images/plist16.png)](implementing-sirikit-images/plist16.png#lightbox)
20. Adicione a `IntentName` chave com o **tipo** `String` e a intenção para o exemplo:

    [![Adicione a chave intendaname ao tipo de cadeia de caracteres e à intenção do exemplo](implementing-sirikit-images/plist17.png)](implementing-sirikit-images/plist17.png#lightbox)
21. Adicione a `IntentExamples` chave com o **tipo** de `Array` :

    [![Adicionar a chave IntentExamples com o tipo de matriz](implementing-sirikit-images/plist18.png)](implementing-sirikit-images/plist18.png#lightbox)
22. Adicione algumas `String` chaves com exemplos de uso do termo:

    [![Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo](implementing-sirikit-images/plist19.png)](implementing-sirikit-images/plist19.png#lightbox)
23. Repita as etapas acima para qualquer propósito em que o aplicativo precise fornecer o uso de exemplo de.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Clique com o botão direito do mouse no nome do projeto na **Gerenciador de soluções** e selecione **Adicionar > novo item... > lista de propriedades do Apple > > info. plist**:

    [![Adicionar um novo info. plist](implementing-sirikit-images/plist01.w157-sml.png)](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Clique duas vezes no `AppIntentVocabulary.plist` arquivo no **Gerenciador de soluções** para abri-lo para edição.
3. Clique em **+** para adicionar uma chave, defina o **nome** como `ParameterVocabularies` e o **tipo** como `Array` :

    [![Defina o nome como ParameterVocabularies e o tipo como matriz](implementing-sirikit-images/plist02w.png)](implementing-sirikit-images/plist02w.png#lightbox)
4. Expanda `ParameterVocabularies` e clique no **+** botão e defina o **tipo** como `Dictionary` :

    [![Definir o tipo como Dictionary](implementing-sirikit-images/plist03w.png)](implementing-sirikit-images/plist03w.png#lightbox)
5. Clique em **+** para adicionar uma nova chave, defina o **nome** como `ParameterNames` e o **tipo** como `Array` :

    [![Defina o nome como ParameterNames e o tipo como matriz](implementing-sirikit-images/plist04w.png)](implementing-sirikit-images/plist04w.png#lightbox)
6. Clique em **+** para adicionar uma nova chave com o **tipo** de `String` e o valor como um dos nomes de parâmetro disponíveis. Por exemplo `INStartWorkoutIntent.workoutName`:

    [![A chave INStartWorkoutIntent. ginásticaname](implementing-sirikit-images/plist05w.png)](implementing-sirikit-images/plist05w.png#lightbox)
7. Adicione a `ParameterVocabulary` chave à `ParameterVocabularies` chave com o **tipo** de `Array` :

    [![Adicione a chave ParameterVocabulary à chave ParameterVocabularies com o tipo de matriz](implementing-sirikit-images/plist06w.png)](implementing-sirikit-images/plist06w.png#lightbox)
8. Adicione uma nova chave com o **tipo** de `Dictionary` :

    [![Adicionar uma nova chave com o tipo de dicionário](implementing-sirikit-images/plist07w.png)](implementing-sirikit-images/plist07w.png#lightbox)
9. Adicione a `VocabularyItemIdentifier` chave com o **tipo** de `String` e especifique uma ID exclusiva para o termo:

    [![Adicione a chave VocabularyItemIdentifier com o tipo de cadeia de caracteres e especifique uma ID exclusiva para o termo](implementing-sirikit-images/plist08w.png)](implementing-sirikit-images/plist08w.png#lightbox)
10. Adicione a `VocabularyItemSynonyms` chave com o **tipo** de `Array` :

    [![Adicionar a chave VocabularyItemSynonyms com o tipo de matriz](implementing-sirikit-images/plist09w.png)](implementing-sirikit-images/plist09w.png#lightbox)
11. Adicione uma nova chave com o **tipo** de `Dictionary` :

    [![Adicionar uma nova chave com o tipo de dicionário](implementing-sirikit-images/plist10w.png)](implementing-sirikit-images/plist10w.png#lightbox)
12. Adicione a `VocabularyItemPhrase` chave com o **tipo** de `String` e o termo que o aplicativo está definindo:

    [![Adicione a chave VocabularyItemPhrase com o tipo de cadeia de caracteres e o termo que o aplicativo está definindo](implementing-sirikit-images/plist11w.png)](implementing-sirikit-images/plist11w.png#lightbox)
13. Adicione a `VocabularyItemPronunciation` chave com o **tipo** `String` e a pronúncia fonética do termo:

    [![Adicionar a chave VocabularyItemPronunciation com o tipo de cadeia de caracteres e a pronúncia fonética do termo](implementing-sirikit-images/plist12w.png)](implementing-sirikit-images/plist12w.png#lightbox)
14. Adicione a `VocabularyItemExamples` chave com o **tipo** de `Array` :

    [![Adicionar a chave VocabularyItemExamples com o tipo de matriz](implementing-sirikit-images/plist13w.png)](implementing-sirikit-images/plist13w.png#lightbox)
15. Adicione algumas `String` chaves com exemplos de uso do termo:

    [![Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo](implementing-sirikit-images/plist14w.png)](implementing-sirikit-images/plist14w.png#lightbox)
16. Repita as etapas acima para quaisquer outros termos personalizados que o aplicativo precisa definir.
17. Recolha a `ParameterVocabularies` chave.
18. Adicione a `IntentPhrases` chave com o **tipo** de `Array` :

    [![Adicionar a chave IntentPhrases com o tipo de matriz](implementing-sirikit-images/plist15w.png)](implementing-sirikit-images/plist15w.png#lightbox)
19. Adicione uma nova chave com o **tipo** de `Dictionary` :

    [![Adicionar uma nova chave com o tipo de dicionário](implementing-sirikit-images/plist16w.png)](implementing-sirikit-images/plist16w.png#lightbox)
20. Adicione a `IntentName` chave com o **tipo** `String` e a intenção para o exemplo:

    [![Adicione a chave intendaname ao tipo de cadeia de caracteres e à intenção do exemplo](implementing-sirikit-images/plist17w.png)](implementing-sirikit-images/plist17w.png#lightbox)
21. Adicione a `IntentExamples` chave com o **tipo** de `Array` :

    [![Adicionar a chave IntentExamples com o tipo de matriz](implementing-sirikit-images/plist18w.png)](implementing-sirikit-images/plist18w.png#lightbox)
22. Adicione algumas `String` chaves com exemplos de uso do termo:

    [![Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo](implementing-sirikit-images/plist19w.png)](implementing-sirikit-images/plist19w.png#lightbox)
23. Repita as etapas acima para qualquer propósito em que o aplicativo precise fornecer o uso de exemplo de.

-----

> [!IMPORTANT]
> O `AppIntentVocabulary.plist` será registrado com siri nos dispositivos de teste durante o desenvolvimento e pode levar algum tempo para que Siri incorpore o vocabulário personalizado. Como resultado, o testador precisará aguardar vários minutos antes de tentar testar o vocabulário específico do aplicativo quando ele tiver sido atualizado.

Para obter mais informações, consulte nossa [referência de vocabulário específica do aplicativo](~/ios/platform/sirikit/understanding-sirikit.md) e a [especificação do vocabulário personalizado](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1)da Apple.

## <a name="adding-an-intents-extension"></a>Adicionando uma extensão de tentativas

Agora que o aplicativo foi preparado para adotar o SiriKit, o desenvolvedor precisará adicionar uma (ou mais) extensões de retenções à solução para lidar com as tentativas necessárias para a integração com o Siri.

Para cada extensão de tentativas necessária, faça o seguinte:

- Adicione um projeto de extensão de tentativas à solução de aplicativo Xamarin. iOS.
- Configure o arquivo de extensão de tentativas `Info.plist` .
- Modifique a classe principal de extensão de tentativas.

Para obter mais informações, consulte nossa [referência de extensão de tentativas](~/ios/platform/sirikit/understanding-sirikit.md) e a Apple [criando a referência de extensão de intenções](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Criando a extensão

Para adicionar uma extensão de tentativas à solução, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com o botão direito do mouse no **nome da solução** na **painel de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto...**.
2. Na caixa de diálogo, **iOS**selecione  >  **Extensions**  >  **extensão de tentativa** de extensões do IOS e clique no botão **Avançar** : 

    [![Selecionar a extensão de intenção](implementing-sirikit-images/intents05.png)](implementing-sirikit-images/intents05.png#lightbox)
3. Em seguida, insira um **nome** para a extensão de intenção e clique no botão **Avançar** : 

    [![Insira um nome para a extensão de intenção](implementing-sirikit-images/intents06.png)](implementing-sirikit-images/intents06.png#lightbox)
4. Por fim, clique no botão **criar** para adicionar a extensão de intenção à solução de aplicativos: 

    [![Adicionar a extensão de intenção à solução de aplicativos](implementing-sirikit-images/intents07.png)](implementing-sirikit-images/intents07.png#lightbox)
5. Na **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **referências** da extensão de intenção recém-criada. Verifique o nome do projeto de biblioteca de código compartilhado comum (que o aplicativo criou acima) e clique no botão **OK** : 

    [![Selecione o nome do projeto de biblioteca de código compartilhado comum](implementing-sirikit-images/intents08.png)](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Clique com o botão direito do mouse no **nome da solução** na **Gerenciador de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto...**.
2. Na caixa de diálogo, selecione **Visual C# > extensões do iOS > extensão de intenção** e clique no botão **Avançar** :

    [![Selecionar a extensão de intenção](implementing-sirikit-images/intents05.w157-sml.png)](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Em seguida, insira um **nome** para a extensão de intenção e clique no botão **OK** .
4. Na **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **referências** da extensão de tentativas recém-criada e escolha **Adicionar referência de >**. Verifique o nome do projeto de biblioteca de código compartilhado comum (que o aplicativo criou acima) e clique no botão **OK** :

    [![Selecione o nome do projeto de biblioteca de código compartilhado comum](implementing-sirikit-images/intents08w.png)](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Repita essas etapas para o número de extensões de intenção (com base na [arquitetura do aplicativo para extensões](#architecting-the-app-for-extensions) acima) que o aplicativo precisará.

### <a name="configuring-the-infoplist"></a>Configurando o info. plist

Para cada uma das extensões de tentativas que foram adicionadas à solução do aplicativo, o deve ser configurado nos `Info.plist` arquivos para trabalhar com o aplicativo.

Assim como qualquer extensão de aplicativo típica, o aplicativo terá as chaves existentes de `NSExtension` e `NSExtensionAttributes` . Para uma extensão de tentativas, há dois novos atributos que devem ser configurados:

[![Os dois novos atributos que devem ser configurados](implementing-sirikit-images/intents01.png)](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** -é necessário e consiste em uma matriz de nomes de classe de intenção que o aplicativo deseja dar suporte a partir da extensão de intenção.
- **IntentsRestrictedWhileLocked** -é uma chave opcional para o aplicativo especificar o comportamento da tela de bloqueio da extensão. Ele consiste em uma matriz de nomes de classe de intenção que o aplicativo deseja exigir que o usuário esteja conectado para usar da extensão de intenção.

Para configurar o arquivo da extensão da intenção `Info.plist` , clique duas vezes nele no **Gerenciador de soluções** para abri-lo para edição. Em seguida, alterne para o modo de exibição de **origem** e expanda as `NSExtension` `NSExtensionAttributes` teclas e no editor:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![As chaves NSExtension e NSExtensionAttributes no editor](implementing-sirikit-images/intents02.png)](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![As chaves NSExtension e NSExtensionAttributes no editor](implementing-sirikit-images/intents02w.png)](implementing-sirikit-images/intents02w.png#lightbox)

-----

Expanda a `IntentsSupported` chave e adicione o nome de qualquer classe de intenção à qual essa extensão dará suporte. Para o aplicativo de exemplo MonkeyChat, ele dá suporte ao `INSendMessageIntent` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![A chave INSendMessageIntent](implementing-sirikit-images/intents09.png)](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![A chave INSendMessageIntent](implementing-sirikit-images/intents09w.png)](implementing-sirikit-images/intents09w.png#lightbox)

-----

Se o aplicativo opcionalmente exigir que o usuário esteja conectado ao dispositivo para usar uma determinada tentativa, expanda a `IntentRestrictedWhileLocked` chave e adicione os nomes de classe das tentativas que têm acesso restrito. Para o aplicativo de exemplo MonkeyChat, o usuário deve estar conectado para enviar uma mensagem de chat para que tenhamos adicionado `INSendMessageIntent` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![A chave INSendMessageIntent adicionada](implementing-sirikit-images/intents10.png)](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![A chave INSendMessageIntent adicionada](implementing-sirikit-images/intents10w.png)](implementing-sirikit-images/intents10w.png#lightbox)

-----

Para obter uma lista completa de domínios de intenção disponíveis, consulte a [referência de domínios de intenção](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)da Apple.

### <a name="configuring-the-main-class"></a>Configurando a classe principal

Em seguida, o desenvolvedor precisará configurar a classe principal que atua como o ponto de entrada principal para a extensão de intenção em Siri. Deve ser uma subclasse de que esteja de `INExtension` acordo com o `IINIntentHandler` delegado. Por exemplo:

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

Há um método solitários que o aplicativo deve implementar na classe principal de extensão de intenção, o `GetHandler` método. Esse método é passado como uma intenção por SiriKit e o aplicativo deve retornar um **manipulador de intenção** que corresponda ao tipo da tentativa fornecida.

Como o aplicativo de exemplo MonkeyChat lida apenas com uma intenção, ele está retornando a si mesmo no `GetHandler` método. Se a extensão tratou mais de uma intenção, o desenvolvedor adicionaria uma classe para cada tipo de tentativa e retornará uma instância aqui com base no `Intent` passado para o método.

### <a name="handling-the-resolve-stage"></a>Manipulando o estágio de resolução

O estágio resolver é o local em que a extensão de intenção esclarecerá e validará os parâmetros passados de siri e que foram definidos por meio da conversa do usuário.

Para cada parâmetro que é enviado de Siri, há um `Resolve` método. O aplicativo precisará implementar esse método para cada parâmetro que o aplicativo possa precisar de ajuda do Siri para obter a resposta correta do usuário.

No caso do aplicativo de exemplo MonkeyChat, a extensão de intenção exigirá que um ou mais destinatários enviem a mensagem. Para cada destinatário na lista, a extensão precisará fazer uma pesquisa de contato que pode ter o seguinte resultado:

- É encontrado exatamente um contato correspondente.
- Foram encontrados dois ou mais contatos correspondentes.
- Nenhum contato correspondente encontrado.

Além disso, o MonkeyChat requer conteúdo para o corpo da mensagem. Se o usuário não tiver fornecido isso, o Siri precisará solicitar o conteúdo ao usuário.

A extensão de intenção precisará lidar normalmente com cada um desses casos.

```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

Para obter mais informações, consulte nossa [referência do estágio resolver e a](~/ios/platform/sirikit/understanding-sirikit.md) [referência de tentativas de resolução e manipulação](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)da Apple.

### <a name="handling-the-confirm-stage"></a>Manipulando o estágio de confirmação

O estágio Confirm é onde a extensão de intenção verifica se ela tem todas as informações para atender à solicitação do usuário. O aplicativo deseja enviar a confirmação, todos os detalhes de suporte do que está prestes a acontecer com o Siri, para que possa ser confirmado com o usuário que essa é a ação pretendida.

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

Para obter mais informações, consulte nossa [referência de estágio Confirm](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>Processando a intenção

Esse é o ponto em que a extensão de intenção realmente executa a tarefa para atender à solicitação do usuário e passar os resultados de volta para Siri para que o usuário possa ser informado.

```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

Para obter mais informações, consulte nossa [referência de estágio de identificador](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Adicionando uma extensão de interface do usuário de tentativas

A extensão de interface do usuário de tentativas opcionais apresenta a oportunidade de trazer a interface do usuário e a identidade visual da experiência do Siri e fazer com que os usuários se sintam conectados ao aplicativo. Com essa extensão, o aplicativo pode trazer a marca, bem como o Visual e outras informações para a transcrição.

[![Uma saída de extensão de interface do usuário de exemplos de tentativas](implementing-sirikit-images/intentsui01.png)](implementing-sirikit-images/intentsui01.png#lightbox)

Assim como a extensão de intenções, o desenvolvedor fará a seguinte etapa para a extensão da interface do usuário de tentativas:

- Adicione um projeto de extensão de interface do usuário de tentativas à solução de aplicativo Xamarin. iOS.
- Configure o arquivo de extensão da interface do usuário de tentativas `Info.plist` .
- Modifique a classe principal da extensão da interface do usuário de tentativas.

Para obter mais informações, consulte nossa [referência de extensão da interface do usuário de tentativas e a](~/ios/platform/sirikit/understanding-sirikit.md) Apple [fornecendo uma referência de interface personalizada](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Criando a extensão

Para adicionar uma extensão de interface do usuário de tentativas à solução, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com o botão direito do mouse no **nome da solução** na **painel de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto...**.
2. Na caixa de diálogo, **iOS**selecione  >  **Extensions**  >  **extensão da interface do usuário da tentativa** de extensões do IOS e clique no botão **Avançar** : 

    [![Selecionar extensão da interface do usuário de intenção](implementing-sirikit-images/intents11.png)](implementing-sirikit-images/intents11.png#lightbox)
3. Em seguida, insira um **nome** para a extensão de intenção e clique no botão **Avançar** : 

    [![Insira um nome para a extensão de intenção](implementing-sirikit-images/intents12.png)](implementing-sirikit-images/intents12.png#lightbox)
4. Por fim, clique no botão **criar** para adicionar a extensão de intenção à solução de aplicativos: 

    [![Adicionar a extensão de intenção à solução de aplicativos](implementing-sirikit-images/intents13.png)](implementing-sirikit-images/intents13.png#lightbox)
5. Na **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **referências** da extensão de intenção recém-criada. Verifique o nome do projeto de biblioteca de código compartilhado comum (que o aplicativo criou acima) e clique no botão **OK** : 

    [![Selecione o nome do projeto de biblioteca de código compartilhado comum](implementing-sirikit-images/intents14.png)](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Clique com o botão direito do mouse no **nome da solução** na **Gerenciador de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto...**
2. Na caixa de diálogo, **iOS**selecione  >  **Extensions**  >  **extensão da interface do usuário da tentativa** de extensões do IOS e clique no botão **Avançar** .
3. Em seguida, insira um **nome** para a extensão de intenção e clique no botão **OK** .
4. Na **Gerenciador de soluções**, clique com o botão direito do mouse na pasta **referências** da extensão de intenção recém-criada. Verifique o nome do projeto de biblioteca de código compartilhado comum (que o aplicativo criou acima) e clique no botão **OK** .
    
-----

### <a name="configuring-the-infoplist"></a>Configurando o info. plist

Configure o arquivo da extensão da interface do usuário de tentativas `Info.plist` para trabalhar com o aplicativo.

Assim como qualquer extensão de aplicativo típica, o aplicativo terá as chaves existentes de `NSExtension` e `NSExtensionAttributes` . Para uma extensão de tentativas, há um novo atributo que deve ser configurado:

[![Um novo atributo que deve ser configurado](implementing-sirikit-images/intents03.png)](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** é obrigatório e consiste em uma matriz de nomes de classe de intenção que o aplicativo deseja dar suporte a partir da extensão de intenção.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Para configurar o arquivo da extensão da interface do usuário da intenção `Info.plist` , clique duas vezes nele no **Gerenciador de soluções** para abri-lo para edição. Em seguida, alterne para o modo de exibição de **origem** e expanda as `NSExtension` `NSExtensionAttributes` teclas e no editor:

[![As chaves NSExtension e NSExtensionAttributes no editor](implementing-sirikit-images/intents04.png)](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para configurar o arquivo da extensão da interface do usuário da intenção `Info.plist` , clique duas vezes nele no **Gerenciador de soluções** para abri-lo para edição. Expanda `NSExtension` as `NSExtensionAttributes` teclas e no editor:

[![Chaves Tnão NSExtension e NSExtensionAttributes no editor](implementing-sirikit-images/intents04w.png)](implementing-sirikit-images/intents04w.png#lightbox)

-----

Expanda a `IntentsSupported` chave e adicione o nome de qualquer classe de intenção à qual essa extensão dará suporte. Para o aplicativo de exemplo MonkeyChat, ele dá suporte ao `INSendMessageIntent` :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![A chave INSendMessageIntent](implementing-sirikit-images/intents15.png)](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![A chave INSendMessageIntent](implementing-sirikit-images/intents15w.png)](implementing-sirikit-images/intents15w.png#lightbox)

-----

Para obter uma lista completa de domínios de intenção disponíveis, consulte a [referência de domínios de intenção](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)da Apple.

### <a name="configuring-the-main-class"></a>Configurando a classe principal

Configure a classe principal que atua como o ponto de entrada principal para a extensão da interface do usuário de intenção em Siri. Ele deve ser uma subclasse que esteja de `UIViewController` acordo com a `IINUIHostedViewController` interface. Por exemplo:

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

Siri passará uma `INInteraction` instância de classe para o `Configure` método da `UIViewController` instância dentro da extensão da interface do usuário da intenção.

O `INInteraction` objeto fornece três partes-chave de informações para a extensão:

1. O objeto de intenção que está sendo processado.
2. O objeto de resposta de tentativa `Confirm` dos `Handle` métodos e da extensão de intenção.
3. O estado de interação que define o estado da interação entre o aplicativo e o Siri.

A `UIViewController` instância é a classe principal para a interação com siri e, como ela é herdada de `UIViewController` , ela tem acesso a todos os recursos de UIKit.

Quando Siri chama o `Configure` método do `UIViewController` que ele passa em um contexto de exibição, informando que o controlador de exibição será hospedado em um siri snippit ou no cartão Maps.

O Siri também passará um manipulador de conclusão que o aplicativo precisa para retornar o tamanho desejado da exibição depois que o aplicativo terminar de configurá-lo.

### <a name="design-the-ui-in-ios-designer"></a>Criar a interface do usuário no designer do iOS

Layout a interface do usuário da extensão da interface do usuário de tentativas no designer do iOS. Clique duas vezes no arquivo da extensão `MainInterface.storyboard` na **Gerenciador de soluções** para abri-lo para edição. Arraste em todos os elementos de interface do usuário necessários para criar a interface do utilizador e salvar as alterações.

> [!IMPORTANT]
> Embora seja possível adicionar elementos interativos, como `UIButtons` ou `UITextFields` até a extensão da interface do usuário da intenção `UIViewController` , eles são estritamente proibidos como a interface do usuário da intenção em não interativo e o usuário não poderá interagir com eles.

### <a name="wire-up-the-user-interface"></a>Conectar a interface do usuário

Com a interface do usuário da extensão da interface do usuário de tentativas criada no iOS designer, edite a `UIViewController` subclasse e substitua o `Configure` método da seguinte maneira:

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>Substituindo a interface do usuário do Siri padrão

A extensão da interface do usuário de tentativas sempre será exibida junto com outro conteúdo do Siri, como o ícone do aplicativo e o nome na parte superior da interface do usuário ou, com base na intenção, os botões (como enviar ou cancelar) podem ser exibidos na parte inferior.

Há algumas instâncias em que o aplicativo pode substituir as informações que o Siri está exibindo para o usuário por padrão, como mensagens ou mapas em que o aplicativo pode substituir a experiência padrão por uma personalizada para o aplicativo.

Se a extensão da interface do usuário de intenções precisar substituir elementos da interface do usuário do Siri padrão, a `UIViewController` subclasse precisará implementar a `IINUIHostedViewSiriProviding` interface e optar por exibir um elemento de interface específico.

Adicione o seguinte código à `UIViewController` subclasse para informar ao Siri que a extensão da interface do usuário da intenção já está exibindo o conteúdo da mensagem:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considerações

A Apple sugere que o desenvolvedor leve em conta as seguintes considerações ao projetar e implementar as extensões da interface do usuário da intenção:

- **Esteja atento ao uso da memória** -como as extensões da interface do usuário da intenção são temporárias e são mostradas apenas por um curto período de tempo, o sistema impõe restrições de memória mais rígidas do que as usadas com um aplicativo completo.
- **Considere os tamanhos de exibição mínimo e máximo** – Verifique se as extensões da interface do usuário da intenção estão corretas em todos os tipos, tamanho e orientação do dispositivo IOS. Além disso, o tamanho desejado que o aplicativo envia de volta para Siri pode não ser capaz de ser concedido.
- **Use padrões de layout flexíveis e adaptáveis** -novamente para garantir que a interface do usuário tenha uma aparência ótima em todos os dispositivos.

## <a name="summary"></a>Resumo

Este artigo abordou o SiriKit e mostrou como ele pode ser adicionado aos aplicativos Xamarin. iOS para fornecer serviços que podem ser acessados pelo usuário usando o Siri e o aplicativo Maps em um dispositivo iOS.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de ElizaChat](/samples/xamarin/ios-samples/ios10-elizachat)
- [Guia de programação do SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência da estrutura da interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
- [Referência de domínios de intenção](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)