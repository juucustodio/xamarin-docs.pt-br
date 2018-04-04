---
title: Implementando SiriKit
description: Este artigo aborda as etapas necessárias para implementar SiriKit suporte em um aplicativos xamarin.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 5fdc05de19799ce7b553428c23f860186909bcbb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="implementing-sirikit"></a>Implementando SiriKit

_Este artigo aborda as etapas necessárias para implementar SiriKit suporte em um aplicativos xamarin._



Novo para iOS 10, SiriKit permite que um aplicativo xamarin fornecer os serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS. Este artigo aborda as etapas necessárias para implementar SiriKit suporte nos aplicativos do xamarin adicionando a extensões de propósitos necessárias, extensões de interface do usuário de tentativas e vocabulário.

Siri funciona com o conceito de **domínios**, saber de grupos de ações para tarefas relacionadas. Cada interação com o aplicativo com Siri deve estar em um de seus domínios de serviço conhecido da seguinte maneira:

- Áudio ou vídeo chamada.
- Reserva uma jornada.
- Gerenciando exercícios.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação de Siri que envolvem um dos serviços de extensão do aplicativo, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário junto com os dados de suporte. A extensão do aplicativo, em seguida, gera apropriada **resposta** de objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

Este guia apresentará um exemplo rápido de incluindo SiriKit suporte em um aplicativo existente. Para este exemplo, usaremos o aplicativo MonkeyChat falso:

[![](implementing-sirikit-images/monkeychat01.png "O ícone de MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantém seu próprio catálogo de contato de amigos do usuário, cada um associado a um nome de tela (como Bobo, por exemplo) e permite que o usuário enviar texto chats para cada friend por seu nome de tela.

## <a name="extending-the-app-with-sirikit"></a>Estendendo o aplicativo com SiriKit

Conforme o [Compreendendo SiriKit conceitos](~/ios/platform/sirikit/understanding-sirikit.md) guia, há três partes principais envolvidas na extensão de um aplicativo com SiriKit:

[![](implementing-sirikit-images/elements01.png "Estendendo o aplicativo com o diagrama de SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Elas incluem:

1. **Extensão de tentativas** -verifica as respostas dos usuários, confirma se o aplicativo pode manipular a solicitação e, na verdade, executa a tarefa para atender a solicitação do usuário.
2. **Extensão de interface do usuário de tentativas** - *opcional*, fornece uma interface de usuário personalizada para as respostas no ambiente de Siri e pode colocar o interface do usuário de aplicativos e identidade visual em Siri para enriquecer a experiência do usuário.
3. **Aplicativo** -fornece o aplicativo com vocabulários específicos do usuário para ajudar a Siri trabalhar com ele. 

Todos esses elementos e as etapas para incluí-los no aplicativo serão abordados em detalhes nas seções a seguir.


## <a name="preparing-the-app"></a>Preparando o aplicativo

SiriKit baseia-se nas extensões, no entanto, antes de adicionar as extensões para o aplicativo, há algumas coisas que o desenvolvedor precisa fazer para ajudar com a adoção de SiriKit.

### <a name="moving-common-shared-code"></a>Movendo o código compartilhado comum 

Primeiro, o desenvolvedor pode mover alguns códigos comuns que serão compartilhados entre o aplicativo e as extensões em um _projetos compartilhados_, _bibliotecas de classes portáteis (PCLs)_ ou _nativo Bibliotecas_.

As extensões precisará ser capaz de fazer todas as coisas que o aplicativo. Em termos do aplicativo de exemplo MonkeyChat, coisas como localização de contatos, adicionar novos contatos, enviar mensagens e recuperar o histórico de mensagens.

Ao mover esse código comum em um projeto compartilhado, PCL ou biblioteca nativa ele torna mais fácil manter esse código em um local comum e garante que a extensão e o aplicativo-pai fornecem funcionalidade e experiência uniforme para o usuário.

No caso do aplicativo de exemplo MonkeyChat, os modelos de dados e o código de processamento, como acesso de rede e o banco de dados serão movidos para uma biblioteca nativa.

Faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac e abra o aplicativo MonkeyChat.
2. Com o botão direito no nome da solução no **solução preenchimento** e selecione **adicionar** > **novo projeto...** : 

    [![](implementing-sirikit-images/prep01.png "Adicionar um novo projeto")](implementing-sirikit-images/prep01.png#lightbox)
3. Selecione **iOS** > **biblioteca** > **biblioteca de classes** e clique no **próximo** botão: 

    [![](implementing-sirikit-images/prep02.png "Selecione a biblioteca de classes")](implementing-sirikit-images/prep02.png#lightbox)
4. Digite `MonkeyChatCommon` para o **nome** e clique no **criar** botão: 

    [![](implementing-sirikit-images/prep03.png "Digite MonkeyChatCommon para o nome")](implementing-sirikit-images/prep03.png#lightbox)
5. Clique duas vezes no **referências** pasta do aplicativo principal no **Solution Explorer** e selecione **Editar referências...** . Verifique o **MonkeyChatCommon** do projeto e clique no **Okey** botão: 

    [![](implementing-sirikit-images/prep05.png "Verifique o projeto MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. No **Solution Explorer**, arraste o código compartilhado comum do aplicativo principal para a biblioteca nativa.
7. No caso de MonkeyChat, arraste o **DataModels** e **processadores** pastas do aplicativo principal para a biblioteca nativa: 

    [![](implementing-sirikit-images/prep06.png "As pastas DataModels e os processadores no Gerenciador de soluções")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Visual Studio e abra o aplicativo MonkeyChat.
2. Clique com botão direito no nome da solução no **Solution Explorer** e selecione **adicionar** > **novo projeto...** .
3. Selecione **Visual C#** > **projeto compartilhado** e clique no **próximo** botão: 

    [![](implementing-sirikit-images/prep02w.png "Selecione a biblioteca de classes")](implementing-sirikit-images/prep02w.png#lightbox)
4. Digite `MonkeyChatCommon` para o **nome** e clique no **criar** botão.
5. Clique duas vezes no **referências** pasta do aplicativo principal no **Solution Explorer** e selecione **Editar referências...** . Verifique o **MonkeyChatCommon** do projeto e clique no **Okey** botão: 

    [![](implementing-sirikit-images/prep05w.png "Verifique o projeto MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. No **Solution Explorer**, arraste o código compartilhado comum do aplicativo principal para o projeto compartilhado.
7. No caso de MonkeyChat, arraste o **DataModels** e **processadores** pastas do aplicativo principal para a biblioteca nativa.

-----

Editar qualquer um dos arquivos que foram movidos para a biblioteca nativa e altere o namespace para coincidir com a biblioteca. Por exemplo, a alteração `MonkeyChat` para `MonkeyChatCommon`:

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

Em seguida, volte para o aplicativo principal e adicionar um `using` declaração de namespace da biblioteca nativa em qualquer lugar do aplicativo usa uma das classes que foram movidas:

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

### <a name="architecting-the-app-for-extensions"></a>A arquitetura do aplicativo para extensões

Normalmente, um aplicativo será inscrever-se para vários propósitos e o desenvolvedor precisa garantir que o aplicativo foi projetado para o número apropriado de extensões de intenção.

Em uma situação em que um aplicativo requer mais de uma tentativa, o desenvolvedor tem a opção de colocar todas a manipulação de intenção em uma tentativa de extensão ou criando uma extensão de intenção separado para cada tentativa.

Se escolher criar uma extensão de intenção separado para cada tentativa, o desenvolvedor pode acabar duplicando uma grande quantidade de código clichê em cada extensão e crie uma grande quantidade de sobrecarga de memória e de processador.

Para ajudar a escolher entre as duas opções, consulte se qualquer um das tentativas de pertencem naturalmente juntos. Por exemplo, um aplicativo que fez a chamadas de áudio e vídeos pode querer incluir ambas as tentativas em uma única extensão de intenção de como eles são manipulação de tarefas semelhantes e pode fornecer a maioria dos reutilização de código.

Para qualquer propósito ou grupo de tentativas que não se encaixam em um grupo existente, crie uma nova extensão de intenção de solução do aplicativo para contê-los.


### <a name="setting-the-required-entitlements"></a>Configurando os direitos necessários

Qualquer aplicativo xamarin que inclui a integração de SiriKit, devem ter os direitos corretos definido. Se o desenvolvedor não definida corretamente esses direitos necessários, eles não poderão instalar ou testar o aplicativo no iOS real 10 (ou superior) hardware, que também é um requisito desde o iOS 10 simulador não dá suporte a SiriKit.

Faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique duas vezes o `Entitlements.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
2. Alterne para o **fonte** guia.
3. Adicionar o `com.apple.developer.siri` **propriedade**, defina o **tipo** para `Boolean` e **valor** para `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Adicione a propriedade com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Salve as alterações no arquivo.
5. Clique duas vezes o **arquivo de projeto** no **Solution Explorer** para abri-lo para edição.
6. Selecione **iOS de assinatura de pacote** e certifique-se de que o `Entitlements.plist` arquivo está selecionado no **personalizado direitos** campo: 

    [![](implementing-sirikit-images/setup02.png "Selecione o arquivo Entitlements.plist no campo personalizado de direitos")](implementing-sirikit-images/setup02.png#lightbox)
7. Clique no botão **OK** para salvar as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique duas vezes o `Entitlements.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
3. Adicionar o `com.apple.developer.siri` **propriedade**, defina o **tipo** para `Boolean` e **valor** para `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Adicione a propriedade com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
4. Salve as alterações no arquivo.
5. Clique duas vezes o **arquivo de projeto** no **Solution Explorer** para abri-lo para edição.
6. Selecione **iOS de assinatura de pacote** e certifique-se de que o `Entitlements.plist` arquivo está selecionado no **personalizado direitos** campo.

-----

Quando terminar, o aplicativo `Entitlements.plist` arquivo deve ser semelhante ao seguinte (em aberto em um editor externo):

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

### <a name="correctly-provisioning-the-app"></a>Provisionamento corretamente o aplicativo

Devido à segurança de estrito Apple colocou em torno do framework SiriKit, qualquer aplicativo xamarin que implementa SiriKit _deve_ têm o ID do aplicativo correto e os direitos (consulte a seção acima) e devem ser assinados com apropriadas Perfil de provisionamento.

Faça o seguinte no seu Mac:

1. Em um navegador da web, navegue até [ http://developer.apple.com ](http://developer.apple.com) e de log em sua conta.
2. Clique em **certificados**, **identificadores** e **perfis**.
3. Selecione **perfis de provisionamento** e selecione **IDs de aplicativo**, em seguida, clique no **+** botão.
4. Insira um **nome** para o novo perfil.
5. Insira um **ID do pacote** seguir Apple nomes da recomendação.
6. Role para baixo até o **serviços de aplicativos** seção, selecione **SiriKit** e clique no **continuar** botão: 

    [![](implementing-sirikit-images/setup03.png "Selecione SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Verifique se todas as configurações, em seguida, **enviar** ID. o App
8. Selecione **perfis de provisionamento** > **desenvolvimento**, clique no **+** botão, selecione o **ID da Apple**, em seguida, clique em **continuar**.
9. Clique em selecionar **todos os**, em seguida, clique em **continuar**.
10. Clique em **Selecionar tudo** novamente, em seguida, clique em **continuar**.
11. Insira um **nome do perfil** usando o Apple de nomeação sugestões, em seguida, clique em **continuar**.
12. Inicie o Xcode.
13. No menu do Xcode selecione **preferências...**
14. Selecione **contas**, em seguida, clique no **exibir detalhes...** botão: 

    [![](implementing-sirikit-images/setup04.png "Selecione contas")](implementing-sirikit-images/setup04.png#lightbox)
15. Clique o **baixar todos os perfis** botão no canto inferior esquerdo: 

    [![](implementing-sirikit-images/setup05.png "Baixar todos os perfis")](implementing-sirikit-images/setup05.png#lightbox)
16. Certifique-se de que o **perfil de provisionamento** criado acima foi instalado no Xcode.
17. Abra o projeto para adicionar o suporte para SiriKit no Visual Studio para Mac.
18. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções**.
18. Certifique-se de que o **identificador de pacote** corresponde a um criado no Portal do desenvolvedor da Apple acima: 

    [![](implementing-sirikit-images/setup06.png "O identificador de pacote")](implementing-sirikit-images/setup06.png#lightbox)
18. No **Solution Explorer**, selecione o **projeto**.
19. Clique com o botão direito e selecione **opções**.
21. Selecione **iOS de assinatura de pacote**, selecione o **assinatura identidade** e **perfil de provisionamento** criado acima: 

    [![](implementing-sirikit-images/setup07.png "Selecione a assinatura de identidade e o perfil de provisionamento")](implementing-sirikit-images/setup07.png#lightbox)
22. Clique no botão **OK** para salvar as alterações.

> [!IMPORTANT]
> Teste SiriKit só funciona em um iOS real 10 dispositivos de Hardware e não no iOS 10 simulador. Se o aplicativo xamarin no hardware real habilitado para ter problemas ao instalar um SiriKit, certifique-se de que os direitos necessários, a ID do aplicativo, o identificador de assinatura e o perfil de provisionamento de tem sido configurado corretamente no Portal do desenvolvedor da Apple e Visual Studio para Mac.

### <a name="requesting-siri-authorization"></a>Solicitar autorização Siri

Antes do aplicativo adiciona um vocabulário específico do usuário ou as extensões de propósitos se conecta ao Siri, ele deve solicita autorização do usuário para acessar o Siri.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Editar o aplicativo `Info.plist` de arquivos, alterne para o **fonte** exibir e adicionar o `NSSiriUsageDescription` chave com um valor de cadeia de caracteres que descreve como o aplicativo usará Siri e que tipos de dados serão enviados. Por exemplo, o aplicativo MonkeyChat poderia informar "MonkeyChat contatos serão enviados ao Siri":

[![](implementing-sirikit-images/request01.png "NSSiriUsageDescription no editor de info. plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Editar o aplicativo `Info.plist` e adicione o `NSSiriUsageDescription` chave com um valor de cadeia de caracteres que descreve como o aplicativo usará Siri e quais tipos de dados serão enviados. Por exemplo, o aplicativo MonkeyChat poderia informar "MonkeyChat contatos serão enviados ao Siri":

[![](implementing-sirikit-images/request01w.png "NSSiriUsageDescription no editor de info. plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Chamar o `RequestSiriAuthorization` método o `INPreferences` classe quando o aplicativo é iniciado pela primeira vez. Editar o `AppDelegate.cs` classe e verifique o `FinishedLaunching` método aparência semelhante ao seguinte:


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

Na primeira vez que esse método é chamado, um alerta é exibido solicitando que o usuário para permitir que o aplicativo acesse o Siri. A mensagem de que o desenvolvedor é adicionado para o `NSSiriUsageDescription` acima será exibido no alerta. Se o usuário inicialmente nega o acesso, eles podem usar o **configurações** aplicativo para conceder acesso ao aplicativo.

A qualquer momento, o aplicativo pode verificar a capacidade do aplicativo para acessar o Siri chamando o `SiriAuthorizationStatus` método o `INPreferences` classe.

### <a name="localization-and-siri"></a>Localização e Siri

Em um dispositivo iOS, o usuário é capaz de selecionar um idioma para Siri diferente, em seguida, o padrão do sistema. Ao trabalhar com dados localizados, o aplicativo precisa usar o `SiriLanguageCode` método o `INPreferences` classe para obter o código de idioma do Siri. Por exemplo:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Adicionando um vocabulário específico do usuário

O vocabulário específico do usuário irá fornecer palavras ou frases que são exclusivos para usuários individuais do aplicativo. Essas serão fornecidas em tempo de execução do aplicativo principal (e não as extensões de aplicativo) como um conjunto ordenado de termos, ordenados na prioridade de uso mais significativa para os usuários, com os termos mais importantes no início da lista.

Vocabulário específico do usuário deve pertencer a uma das seguintes categorias:

- Entre em contato com os nomes (que não são gerenciados pelo framework contatos).
- Foto de marcas.
- Nomes de álbuns de fotos.
- Nomes de treinamento.

Ao selecionar a terminologia para registrar como vocabulário personalizado, escolha somente termos que podem ser interpretados incorretamente por alguém que não estão familiarizados com o aplicativo. Nunca register termos comuns, como "Meu treinamento" ou "Meu álbum". Por exemplo, o aplicativo MonkeyChat registrará os apelidos associados a cada contato no catálogo de endereços do usuário.

O aplicativo fornece o vocabulário específico do usuário chamando o `SetVocabularyStrings` método o `INVocabulary` classe e passando um `NSOrderedSet` coleção do aplicativo principal. O aplicativo deve sempre chamar o `RemoveAllVocabularyStrings` método primeiro, remova quaisquer termos existentes antes de adicionar novos. Por exemplo:

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

Com esse código no local, ele pode ser chamado da seguinte maneira:

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
> Siri trata vocabulário personalizado como dicas e incorporará tanta da terminologia possível. No entanto, espaço vocabulário personalizado é limitado tornando importante registrar _somente_ a terminologia que pode ser confusa, manter, portanto, o número total de termos registrados ao mínimo.

Para obter mais informações, consulte nosso [referência de vocabulário específicas do usuário](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [referência de vocabulário personalizada especificando](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Adicionando um vocabulário específico do aplicativo

O vocabulário específico de aplicativo define as palavras e frases específicas que serão conhecidas para todos os usuários do aplicativo, como tipos de veículo ou nomes de treinamento. Como esses são parte do aplicativo, elas são definidas em um `AppIntentVocabulary.plist` arquivo como parte do pacote de aplicativo principal. Além disso, essas palavras e frases devem ser localizados.

Termos de vocabulário específicas de aplicativo devem pertencer a uma das seguintes categorias:

- Propagar opções.
- Nomes de treinamento.

O arquivo de vocabulário específico do aplicativo contém duas chaves de nível raiz:

- `ParameterVocabularies` **Necessário** -define o aplicativo termos personalizados e parâmetros de tentativa de se aplicam.
- `IntentPhrases` **Opcional** -contém frases de exemplo usando os termos personalizados definidos a `ParameterVocabularies`.

Cada entrada de `ParameterVocabularies` deve especificar uma cadeia de caracteres de ID, o termo e a intenção de que o termo aplica-se a. Além disso, um único termo pode se aplicar a várias tentativas.

Para obter uma lista completa de valores aceitáveis e estrutura de arquivos necessários, consulte da Apple [referência de formato de arquivo do aplicativo vocabulário](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Para adicionar um `AppIntentVocabulary.plist` arquivo ao projeto de aplicativo, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique no nome do projeto no **Solution Explorer** e selecione **adicionar** > **novo arquivo...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Adicionar uma lista de propriedades")](implementing-sirikit-images/plist01.png#lightbox) 
2. Clique duas vezes o `AppIntentVocabulary.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
3. Clique o **+** para adicionar uma chave, defina o **nome** para `ParameterVocabularies` e **tipo** para `Array`:

    [![](implementing-sirikit-images/plist02.png "Defina o nome como ParameterVocabularies e o tipo de matriz")](implementing-sirikit-images/plist02.png#lightbox)
4. Expanda `ParameterVocabularies` e clique no **+** botão e defina o **tipo** para `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Defina o tipo de dicionário")](implementing-sirikit-images/plist03.png#lightbox)
5. Clique o **+** para adicionar uma nova chave, defina o **nome** para `ParameterNames` e **tipo** para `Array`:

    [![](implementing-sirikit-images/plist04.png "Defina o nome como ParameterNames e o tipo de matriz")](implementing-sirikit-images/plist04.png#lightbox)
6. Clique o **+** para adicionar uma nova chave com o **tipo** de `String` e o valor como um dos nomes de parâmetro disponíveis. Por exemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "A chave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Adicionar o `ParameterVocabulary` chave para o `ParameterVocabularies` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06.png "Adicionar a chave de ParameterVocabulary à chave ParameterVocabularies com o tipo de matriz")](implementing-sirikit-images/plist06.png#lightbox)
8. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist07.png#lightbox)
9. Adicionar o `VocabularyItemIdentifier` chave com o **tipo** de `String` e especifique uma ID exclusiva para a condição:

    [![](implementing-sirikit-images/plist08.png "Adicionar a chave de VocabularyItemIdentifier com o tipo de cadeia de caracteres e especifique uma ID exclusiva")](implementing-sirikit-images/plist08.png#lightbox)
10. Adicionar o `VocabularyItemSynonyms` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09.png "Adicionar a chave de VocabularyItemSynonyms com o tipo de matriz")](implementing-sirikit-images/plist09.png#lightbox)
11. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist10.png#lightbox)
12. Adicionar o `VocabularyItemPhrase` chave com o **tipo** de `String` e o termo que o aplicativo está definindo:

    [![](implementing-sirikit-images/plist11.png "Adicionar a chave de VocabularyItemPhrase com o tipo de cadeia de caracteres e o termo de definição de aplicativo")](implementing-sirikit-images/plist11.png#lightbox)
13. Adicionar o `VocabularyItemPronunciation` chave com o **tipo** de `String` e a pronúncia fonética do termo:

    [![](implementing-sirikit-images/plist12.png "Adicionar a chave de VocabularyItemPronunciation com o tipo de cadeia de caracteres e a pronúncia fonética do termo")](implementing-sirikit-images/plist12.png#lightbox)
14. Adicionar o `VocabularyItemExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13.png "Adicionar a chave de VocabularyItemExamples com o tipo de matriz")](implementing-sirikit-images/plist13.png#lightbox)
15. Adicionar alguns `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist14.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist14.png#lightbox)
16. Repita as etapas acima para outros termos de personalizado que o aplicativo precisa definir.
17. Recolher o `ParameterVocabularies` chave.
18. Adicionar o `IntentPhrases` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15.png "Adicionar a chave de IntentPhrases com o tipo de matriz")](implementing-sirikit-images/plist15.png#lightbox)
19. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist16.png#lightbox)
20. Adicionar o `IntentName` chave com o **tipo** de `String` e propósito para o exemplo:

    [![](implementing-sirikit-images/plist17.png "Adicionar a chave de IntentName com o tipo de cadeia de caracteres e a tentativa para o exemplo")](implementing-sirikit-images/plist17.png#lightbox)
21. Adicionar o `IntentExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18.png "Adicionar a chave de IntentExamples com o tipo de matriz")](implementing-sirikit-images/plist18.png#lightbox)
22. Adicionar alguns `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist19.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist19.png#lightbox)
23. Repita as etapas acima para quaisquer tentativas que o aplicativo precisa fornecer o exemplo de uso do.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique no nome do projeto no **Solution Explorer** e selecione **adicionar** > **novo arquivo...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01w.png "Adicionar um novo Info. plist")](implementing-sirikit-images/plist01w.png#lightbox) 
2. Clique duas vezes o `AppIntentVocabulary.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
3. Clique o **+** para adicionar uma chave, defina o **nome** para `ParameterVocabularies` e **tipo** para `Array`:

    [![](implementing-sirikit-images/plist02w.png "Defina o nome como ParameterVocabularies e o tipo de matriz")](implementing-sirikit-images/plist02w.png#lightbox)
4. Expanda `ParameterVocabularies` e clique no **+** botão e defina o **tipo** para `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Defina o tipo de dicionário")](implementing-sirikit-images/plist03w.png#lightbox)
5. Clique o **+** para adicionar uma nova chave, defina o **nome** para `ParameterNames` e **tipo** para `Array`:

    [![](implementing-sirikit-images/plist04w.png "Defina o nome como ParameterNames e o tipo de matriz")](implementing-sirikit-images/plist04w.png#lightbox)
6. Clique o **+** para adicionar uma nova chave com o **tipo** de `String` e o valor como um dos nomes de parâmetro disponíveis. Por exemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "A chave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Adicionar o `ParameterVocabulary` chave para o `ParameterVocabularies` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06w.png "Adicionar a chave de ParameterVocabulary à chave ParameterVocabularies com o tipo de matriz")](implementing-sirikit-images/plist06w.png#lightbox)
8. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist07w.png#lightbox)
9. Adicionar o `VocabularyItemIdentifier` chave com o **tipo** de `String` e especifique uma ID exclusiva para a condição:

    [![](implementing-sirikit-images/plist08w.png "Adicionar a chave de VocabularyItemIdentifier com o tipo de cadeia de caracteres e especifique uma ID exclusiva para o termo")](implementing-sirikit-images/plist08w.png#lightbox)
10. Adicionar o `VocabularyItemSynonyms` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09w.png "Adicionar a chave de VocabularyItemSynonyms com o tipo de matriz")](implementing-sirikit-images/plist09w.png#lightbox)
11. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist10w.png#lightbox)
12. Adicionar o `VocabularyItemPhrase` chave com o **tipo** de `String` e o termo que o aplicativo está definindo:

    [![](implementing-sirikit-images/plist11w.png "Adicionar a chave de VocabularyItemPhrase com o tipo de cadeia de caracteres e o termo de definição de aplicativo")](implementing-sirikit-images/plist11w.png#lightbox)
13. Adicionar o `VocabularyItemPronunciation` chave com o **tipo** de `String` e a pronúncia fonética do termo:

    [![](implementing-sirikit-images/plist12w.png "Adicionar a chave de VocabularyItemPronunciation com o tipo de cadeia de caracteres e a pronúncia fonética do termo")](implementing-sirikit-images/plist12w.png#lightbox)
14. Adicionar o `VocabularyItemExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13w.png "Adicionar a chave de VocabularyItemExamples com o tipo de matriz")](implementing-sirikit-images/plist13w.png#lightbox)
15. Adicionar alguns `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist14w.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist14w.png#lightbox)
16. Repita as etapas acima para outros termos de personalizado que o aplicativo precisa definir.
17. Recolher o `ParameterVocabularies` chave.
18. Adicionar o `IntentPhrases` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15w.png "Adicionar a chave de IntentPhrases com o tipo de matriz")](implementing-sirikit-images/plist15w.png#lightbox)
19. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist16w.png#lightbox)
20. Adicionar o `IntentName` chave com o **tipo** de `String` e propósito para o exemplo:

    [![](implementing-sirikit-images/plist17w.png "Adicionar a chave de IntentName com o tipo de cadeia de caracteres e a tentativa para o exemplo")](implementing-sirikit-images/plist17w.png#lightbox)
21. Adicionar o `IntentExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18w.png "Adicionar a chave de IntentExamples com o tipo de matriz")](implementing-sirikit-images/plist18w.png#lightbox)
22. Adicionar alguns `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist19w.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist19w.png#lightbox)
23. Repita as etapas acima para quaisquer tentativas que o aplicativo precisa fornecer o exemplo de uso do.

-----

> [!IMPORTANT]
> O `AppIntentVocabulary.plist` será registrado com Siri no teste de dispositivos durante o desenvolvimento e ele podem levar algum tempo para Siri incorporar o vocabulário personalizado. Como resultado, o testador precisará aguardar alguns minutos antes de tentar testar vocabulário específico do aplicativo quando ele foi atualizado.

Para obter mais informações, consulte nosso [referência de vocabulário específicas do aplicativo](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [referência de vocabulário personalizada especificando](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Adicionar uma extensão de tentativas

Agora que o aplicativo tenha sido preparado para adotar SiriKit, o desenvolvedor precisa adicionar extensões de propósitos de uma (ou mais) para a solução para tratar as tentativas de necessárias para a integração de Siri.

Para cada extensão de tentativas de necessário, faça o seguinte:

- Adicione um projeto de extensão de tentativas para a solução de aplicativo xamarin.
- Configurar a extensão de tentativas `Info.plist` arquivo.
- Modifique a classe principal da extensão de tentativas.

Para obter mais informações, consulte nosso [a referência de extensão de tentativas](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [criar a referência de extensão de tentativas](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Criando a extensão

Para adicionar uma extensão de tentativas para a solução, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Com o botão direito no **nome da solução** no **solução preenchimento** e selecione **adicionar** > **adicionar novo projeto...** .
2. Na caixa de diálogo Selecionar **iOS** > **extensões** > **intenção extensão** e clique no **próximo** botão: 

    [![](implementing-sirikit-images/intents05.png "Selecione a extensão intencional")](implementing-sirikit-images/intents05.png#lightbox)
3. Em seguida digite uma **nome** para a tentativa de extensão e clique no **próximo** botão: 

    [![](implementing-sirikit-images/intents06.png "Insira um nome para a extensão de tentativa")](implementing-sirikit-images/intents06.png#lightbox)
4. Por fim, clique no **criar** botão para adicionar a extensão de tentativa para a solução de aplicativos: 

    [![](implementing-sirikit-images/intents07.png "Adicionar a extensão de tentativa para a solução de aplicativos")](implementing-sirikit-images/intents07.png#lightbox)
5. No **Solution Explorer**, com o botão direito no **referências** pasta da extensão do intenção recém-criado. Verifique o nome do projeto a biblioteca comum do código compartilhado (que o aplicativo criado acima) e clique no **Okey** botão: 

    [![](implementing-sirikit-images/intents08.png "Selecione o nome do projeto de biblioteca de código compartilhado comum")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Com o botão direito no **nome da solução** no **Solution Explorer** e selecione **adicionar** > **adicionar novo projeto...** .
2. Na caixa de diálogo Selecionar **iOS** > **extensões** > **intenção extensão** e clique no **próximo** botão: 

    [![](implementing-sirikit-images/intents05w.png "Selecione a extensão intencional")](implementing-sirikit-images/intents05w.png#lightbox)
3. Em seguida digite uma **nome** para a tentativa de extensão e clique no **Okey** botão.
5. No **Solution Explorer**, com o botão direito no **referências** pasta da extensão do intenção recém-criado. Verifique o nome do projeto a biblioteca comum do código compartilhado (que o aplicativo criado acima) e clique no **Okey** botão: 

    [![](implementing-sirikit-images/intents08w.png "Selecione o nome do projeto de biblioteca de código compartilhado comum")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Repita essas etapas para o número de extensões de intenção (com base em [a arquitetura de aplicativo para extensões](#Architecting-the-App-for-Extensions) seção acima) que o aplicativo exigirá.

### <a name="configuring-the-infoplist"></a>Configurando o Info. plist

Para cada uma das extensões de propósitos de ter adicionado à solução do aplicativo, deve ser configurado no `Info.plist` arquivos para trabalhar com o aplicativo.

Assim como qualquer extensão de aplicativo típico, o aplicativo terá as chaves existentes de `NSExtension` e `NSExtensionAttributes`. Para uma extensão de tentativas, há dois novos atributos que devem ser configurados:

[![](implementing-sirikit-images/intents01.png "Dois novos atributos que devem ser configurados")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** - é necessário e consiste em uma matriz de nomes de classe intenção que o aplicativo deseja dar suporte a extensão de intenção.
- **IntentsRestrictedWhileLocked** -é uma chave opcional para o aplicativo especificar o comportamento de tela de bloqueio da extensão. Ele consiste em uma matriz de nomes de classe intenção que o aplicativo deseja exigir que o usuário a ser registrada usar da extensão de intenção.

Para configurar a extensão de intenção `Info.plist` de arquivos, clique duas vezes no **Solution Explorer** para abri-lo para edição. Em seguida, alterne para o **fonte** exibir, em seguida, expanda o `NSExtension` e `NSExtensionAttributes` chaves no editor:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents02.png "As chaves NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents02w.png "As chaves NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Expanda o `IntentsSupported` da chave e adicione o nome de qualquer classe intenção oferecerá suporte a essa extensão. Para o exemplo de aplicativo MonkeyChat, ele dá suporte a `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents09.png "A chave INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents09w.png "A chave INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Se o aplicativo opcionalmente requer que o usuário estar conectado ao dispositivo para usar uma determinada tentativa, expanda o `IntentRestrictedWhileLocked` chave e adicione os nomes de classe das tentativas que tenham acesso restrito. Para o exemplo de aplicativo de MonkeyChat, o usuário deve estar conectado ao enviar uma mensagem de bate-papo, portanto, adicionamos `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents10.png "A chave INSendMessageIntent adicionada")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents10w.png "A chave INSendMessageIntent adicionada")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Para obter uma lista completa de domínios de intenção disponíveis, consulte da Apple [intenção de domínios de referência](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurando a classe principal

Em seguida, o desenvolvedor precisa configurar a classe principal que atua como o ponto de entrada principal para a extensão de intenção em Siri. Ele deve ser uma subclasse de `INExtension` que está em conformidade com a `IINIntentHandler` delegate. Por exemplo:

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

Há um método única que o aplicativo deve implementar na classe intenção extensão principal, o `GetHandler` método. Esse método é passado a um propósito por SiriKit e o aplicativo deve retornar um **intenção manipulador** que corresponde ao tipo da intenção a determinado.

Desde que o exemplo de aplicativo MonkeyChat apenas manipula uma intenção, ele retorna se o `GetHandler` método. Se a extensão tratada mais de uma tentativa, o desenvolvedor deve adicionar uma classe para cada tipo de tentativa e retornar uma instância com base no `Intent` passado para o método.

### <a name="handling-the-resolve-stage"></a>Manipulando o estágio de resolução

O estágio de resolver é onde a intenção de extensão será esclarecer e validar os parâmetros passados do Siri e foi definido por meio de conversa do usuário.

Para cada parâmetro que é enviado do Siri, há um `Resolve` método. O aplicativo precisará implementar esse método para cada parâmetro que o aplicativo pode precisar de Ajuda do Siri para obter a resposta correta do usuário.

No caso de MonkeyChat o exemplo de aplicativo, a extensão de intenção exigirá um ou mais destinatários enviar a mensagem. Para cada destinatário na lista, a extensão será necessário fazer uma pesquisa de contato que pode ter o seguinte resultado:

- Exatamente um contato correspondente foi encontrado.
- Dois ou mais contatos correspondentes encontrados.
- Nenhum contato correspondente encontrado.

Além disso, MonkeyChat requer o conteúdo do corpo da mensagem. Se o usuário não forneceu isso, Siri precisa solicitar ao usuário para o conteúdo.

A extensão de intenção precisará lidar com cada um dos casos.


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

Para obter mais informações, consulte nosso [a resolver a referência de estágio](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [Resolvendo e tratamento de tentativas de referência](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>A fase de confirmação de tratamento

A fase de confirmação é onde a extensão de intenção verifica para ver se ele tem todas as informações para atender a solicitação do usuário. O aplicativo deseja enviar confirmação ao longo será todos os detalhes de suporte do que está prestes a ocorrer para Siri para que ele possa ser confirmado com o usuário que esta é a ação pretendida.

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

Para obter mais informações, consulte nosso [a referência de estágio confirmar](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>A tentativa de processamento

Este é o ponto em que a extensão de intenção de realmente executa a tarefa para atender a solicitação do usuário e passar os resultados para Siri para que o usuário possa ser informado.


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

Para obter mais informações, consulte nosso [a referência de estágio para lidar com](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Adicionar uma extensão de interface do usuário de tentativas

A extensão opcional de interface do usuário de tentativas apresenta a oportunidade de exibir a interface do usuário do aplicativo e identidade visual na experiência de Siri e fazer com que os usuários se sentir conectados ao aplicativo. Com essa extensão, o aplicativo pode colocar marca, bem como visual e outras informações para a transcrição.

[![](implementing-sirikit-images/intentsui01.png "Um exemplo de saída de extensão de tentativas de IU")](implementing-sirikit-images/intentsui01.png#lightbox)

Assim como a extensão de tentativas, o desenvolvedor fará a etapa a seguir para a extensão de interface do usuário de propósitos:

- Adicione um projeto de extensão de interface do usuário de tentativas para a solução de aplicativo xamarin.
- Configurar a extensão de interface do usuário de tentativas `Info.plist` arquivo.
- Modifique a classe principal da extensão de interface do usuário de tentativas.

Para obter mais informações, consulte nosso [a referência de extensão de interface do usuário tentativas](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [fornecendo uma referência de Interface personalizada](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Criando a extensão

Para adicionar uma extensão de interface do usuário de tentativas para a solução, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Com o botão direito no **nome da solução** no **solução preenchimento** e selecione **adicionar** > **adicionar novo projeto...** .
2. Na caixa de diálogo Selecionar **iOS** > **extensões** > **intenção da interface do usuário extensão** e clique no **próximo** botão: 

    [![](implementing-sirikit-images/intents11.png "Selecione a extensão de intenção da interface do usuário")](implementing-sirikit-images/intents11.png#lightbox)
3. Em seguida digite uma **nome** para a tentativa de extensão e clique no **próximo** botão: 

    [![](implementing-sirikit-images/intents12.png "Insira um nome para a extensão de tentativa")](implementing-sirikit-images/intents12.png#lightbox)
4. Por fim, clique no **criar** botão para adicionar a extensão de tentativa para a solução de aplicativos: 

    [![](implementing-sirikit-images/intents13.png "Adicionar a extensão de tentativa para a solução de aplicativos")](implementing-sirikit-images/intents13.png#lightbox)
5. No **Solution Explorer**, com o botão direito no **referências** pasta da extensão do intenção recém-criado. Verifique o nome do projeto a biblioteca comum do código compartilhado (que o aplicativo criado acima) e clique no **Okey** botão: 

    [![](implementing-sirikit-images/intents14.png "Selecione o nome do projeto de biblioteca de código compartilhado comum")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Com o botão direito no **nome da solução** no **Solution Explorer** e selecione **adicionar** > **adicionar novo projeto...**
2. Na caixa de diálogo Selecionar **iOS** > **extensões** > **intenção da interface do usuário extensão** e clique no **próximo** botão.
3. Em seguida digite uma **nome** para a tentativa de extensão e clique no **Okey** botão.
5. No **Solution Explorer**, com o botão direito no **referências** pasta da extensão do intenção recém-criado. Verifique o nome do projeto a biblioteca comum do código compartilhado (que o aplicativo criado acima) e clique no **Okey** botão.
    
-----

### <a name="configuring-the-infoplist"></a>Configurando o Info. plist

Configurar a extensão de interface do usuário de tentativas `Info.plist` arquivo para trabalhar com o aplicativo.

Assim como qualquer extensão de aplicativo típico, o aplicativo terá as chaves existentes de `NSExtension` e `NSExtensionAttributes`. Para uma extensão de tentativas, há um novo atributo que deve ser configurado:

[![](implementing-sirikit-images/intents03.png "Um novo atributo deve ser configurado")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** é necessário e consiste em uma matriz de nomes de classe intenção que o aplicativo deseja dar suporte a extensão de intenção.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para configurar a extensão de interface do usuário de intenção `Info.plist` de arquivos, clique duas vezes no **Solution Explorer** para abri-lo para edição. Em seguida, alterne para o **fonte** exibir, em seguida, expanda o `NSExtension` e `NSExtensionAttributes` chaves no editor:

[![](implementing-sirikit-images/intents04.png "As chaves NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para configurar a extensão de interface do usuário de intenção `Info.plist` de arquivos, clique duas vezes no **Solution Explorer** para abri-lo para edição. Expanda o `NSExtension` e `NSExtensionAttributes` chaves no editor:

[![](implementing-sirikit-images/intents04w.png "Chaves Tthe NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Expanda o `IntentsSupported` da chave e adicione o nome de qualquer classe intenção oferecerá suporte a essa extensão. Para o exemplo de aplicativo MonkeyChat, ele dá suporte a `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents15.png "A chave INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents15w.png "A chave INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Para obter uma lista completa de domínios de intenção disponíveis, consulte da Apple [intenção de domínios de referência](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurando a classe principal

Configure a classe principal que atua como o ponto de entrada principal para a extensão de interface do usuário da intenção em Siri. Ele deve ser uma subclasse de `UIViewController` que está em conformidade com a `IINUIHostedViewController` interface. Por exemplo:

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

Siri passará um `INInteraction` instância da classe a `Configure` método o `UIViewController` instância dentro a extensão de interface do usuário de intenção.

O `INInteraction` objeto fornece três partes principais de informações para a extensão:

1. O objeto intenção sendo processado.
2. O objeto de resposta de intenção do `Confirm` e `Handle` métodos de extensão de intenção.
3. O estado de interação que define o estado da interação entre o aplicativo e o Siri.

O `UIViewController` instância é a classe de entidade de segurança para a interação com o Siri e porque ele herda de `UIViewController`, ele tem acesso a todos os recursos de UIKit.

Quando Siri chama o `Configure` método o `UIViewController` transmiti-la em um contexto de exibição informando que o controlador de exibição ou será hospedado em uma Siri Snippit ou um cartão de mapas.

Siri também passará um manipulador de conclusão que o aplicativo precisar retornar o tamanho desejado da exibição quando o aplicativo terminar de configurá-lo.

### <a name="design-the-ui-in-ios-designer"></a>Design de interface do usuário no Designer do iOS

Layout de interface do usuário da extensão de interface do usuário de tentativas no Designer de iOS. Clique duas vezes a extensão `MainInterface.storyboard` arquivo o **Solution Explorer** para abri-lo para edição. Arraste em todos os elementos de interface de usuário necessários para criar a Interface do usuário e salve as alterações.

> [!IMPORTANT]
> Embora seja possível adicionar elementos interativos, como `UIButtons` ou `UITextFields` para a extensão de interface do usuário de intenção `UIViewController`, esses são estritamente proibidos como a UI de intenção de não interativo e o usuário não será capaz de interagir com eles.

### <a name="wire-up-the-user-interface"></a>A Interface do usuário durante a transmissão

Com a Interface de usuário da extensão de interface do usuário tentativas criados no Designer do iOS, edite o `UIViewController` subclasse e substituir o `Configure` método da seguinte maneira:

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

### <a name="overriding-the-default-siri-ui"></a>Substituindo a interface do usuário do padrão Siri

A extensão de interface do usuário de tentativas será sempre exibida juntamente com outro conteúdo Siri como o ícone do aplicativo e o nome na parte superior da interface do usuário ou, com base na intenção, botões (como enviar ou Cancelar) podem ser exibidos na parte inferior.

Há alguns casos em que o aplicativo pode substituir as informações que o Siri está exibindo para o usuário por padrão, como mensagens ou mapas onde o aplicativo pode substituir a experiência de padrão com um personalizado para o aplicativo.

Se a extensão de interface do usuário de tentativas deve substituir elementos do padrão Siri UI, o `UIViewController` subclasse precisará implementar a `IINUIHostedViewSiriProviding` interface e aceitação na exibição de um elemento de interface específica.

Adicione o seguinte código para o `UIViewController` subclasse dizer que o Siri se a extensão de interface do usuário intenção já está exibindo o conteúdo da mensagem:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considerações

Apple sugere que o desenvolvedor fazer as seguintes considerações em consideração ao projetar e implementar as extensões de interface do usuário de intenção de:

- **Estar consciente do uso de memória** - extensões de interface do usuário intenção porque são temporárias e somente mostrado por um curto período, o sistema impõe restrições de memória mais do que o utilizado com um aplicativo completo.
- **Considere a possibilidade de mínimo e máximo tamanhos de exibição** -Certifique-se de que as extensões de interface do usuário intenção parece bom em cada tipo de dispositivo iOS, tamanho e orientação. Além disso, o tamanho desejado que o aplicativo envia para Siri não poderá ser concedido.
- **Usar padrões de Layout adaptável e flexível** - novamente, para garantir que a interface do usuário é excelente em cada dispositivo.

## <a name="summary"></a>Resumo

Este artigo tem coberto SiriKit e mostrado como podem ser adicionado a aplicativos xamarin para fornecer os serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS.




## <a name="related-links"></a>Links relacionados

- [Exemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência do Framework de interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
- [Tentativa de domínios referência](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
