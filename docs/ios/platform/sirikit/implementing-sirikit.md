---
title: Implementar SiriKit no xamarin. IOS
description: Este documento descreve as etapas necessárias para implementar SiriKit suporte em um aplicativos xamarin. IOS. Ele aborda as extensões de propósitos e extensões de propósitos da interface do usuário.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: c8d5c1394bee2ca2419904b077522fac9d03d825
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669434"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implementar SiriKit no xamarin. IOS

_Este artigo aborda as etapas necessárias para implementar SiriKit suporte em um aplicativos xamarin. IOS._

Novo para o iOS 10, o SiriKit permite que um aplicativo xamarin. IOS fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS. Este artigo aborda as etapas necessárias para implementar SiriKit suporte nos aplicativos do xamarin. IOS, adicionando as extensões de propósitos necessárias, extensões de interface do usuário de tentativas e vocabulário.

Siri funciona com o conceito de **domínios**, sabe de grupos de ações para tarefas relacionadas. Cada interação que o aplicativo tem com Siri deve se enquadram em um de seus domínios de serviço conhecido da seguinte maneira:

- Chamada de vídeo ou de áudio.
- Uma jornada de reserva.
- Gerenciando exercícios físicos.
- Sistema de mensagens.
- Pesquisando fotos.
- Enviar ou receber pagamentos.

Quando o usuário faz uma solicitação do Siri que envolvem um dos serviços de aplicativo da extensão, SiriKit envia a extensão de um **intenção** objeto que descreve a solicitação do usuário, juntamente com quaisquer dados de suporte. A extensão de aplicativo, em seguida, gera apropriado **resposta** do objeto para o determinado **intenção**, detalhando como a extensão pode lidar com a solicitação.

Este guia apresentará um exemplo rápido de inclusão de suporte de SiriKit em um aplicativo existente. Para este exemplo, usaremos o aplicativo MonkeyChat fictício:

[![](implementing-sirikit-images/monkeychat01.png "O ícone de MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantém seu próprio catálogo de contato de amigos do usuário, cada um associado com um nome de tela (como Bobo, por exemplo) e permite que o usuário enviar bate-papos do texto para cada amigo por seu nome de tela.

## <a name="extending-the-app-with-sirikit"></a>Estendendo o aplicativo com SiriKit

Conforme o [Noções básicas sobre os conceitos de SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) guia, há três partes principais envolvidas na extensão de um aplicativo com o SiriKit:

[![](implementing-sirikit-images/elements01.png "Estendendo o aplicativo com o diagrama de SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Elas incluem:

1. **Extensão de tentativas** -verifica as respostas de usuários, confirma se o aplicativo pode manipular a solicitação e, na verdade, executa a tarefa para atender à solicitação do usuário.
2. **Extensão de interface do usuário de tentativas** - *opcional*, fornece uma interface do usuário personalizada para as respostas no ambiente do Siri e pode colocar os aplicativos da interface do usuário e a identidade visual em Siri para enriquecer a experiência do usuário.
3. **Aplicativo** -fornece o aplicativo com vocabulários específicos do usuário para ajudar a Siri trabalhar com ele. 

Todos esses elementos e as etapas para incluí-los no aplicativo serão abordados em detalhes nas seções a seguir.


## <a name="preparing-the-app"></a>Preparação do aplicativo

SiriKit baseia-se nas extensões, no entanto, antes de adicionar quaisquer extensões para o aplicativo, há algumas coisas que o desenvolvedor precisa fazer para ajudar com a adoção de SiriKit.

### <a name="moving-common-shared-code"></a>Movendo o código compartilhado comum 

Primeiro, o desenvolvedor pode mover alguns dos códigos comuns que serão compartilhados entre o aplicativo e as extensões em um _projetos compartilhados_, _bibliotecas de classes portáteis (PCLs)_ ou _nativo Bibliotecas_.

As extensões precisará ser capaz de fazer todas as coisas que o aplicativo faz. Nos termos do aplicativo de exemplo MonkeyChat, coisas como localizar os contatos, adicionar novos contatos, enviar mensagens e recuperar o histórico de mensagens.

Ao mover esse código comum para um projeto compartilhado, PCL ou biblioteca nativa, ele torna mais fácil de manter esse código em um lugar comum e garante que a extensão e o aplicativo pai oferecem experiências uniformes e funcionalidade para o usuário.

No caso do aplicativo de exemplo MonkeyChat, os modelos de dados e o código de processamento, como o acesso de rede e banco de dados serão movidos para uma biblioteca nativa.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie o Visual Studio para Mac e abra o aplicativo MonkeyChat.
2. Clique com botão direito no nome da solução nos **painel de soluções** e selecione **Add** > **novo projeto...** : 

    [![](implementing-sirikit-images/prep01.png "Adicionar um novo projeto")](implementing-sirikit-images/prep01.png#lightbox)
3. Selecione **iOS** > **biblioteca** > **biblioteca de classes** e clique no **próxima** botão: 

    [![](implementing-sirikit-images/prep02.png "Selecione a biblioteca de classes")](implementing-sirikit-images/prep02.png#lightbox)
4. Insira `MonkeyChatCommon` para o **nome** e clique no **criar** botão: 

    [![](implementing-sirikit-images/prep03.png "Digite MonkeyChatCommon para o nome")](implementing-sirikit-images/prep03.png#lightbox)
5. Clique com botão direito no **referências** pasta do aplicativo principal na **Gerenciador de soluções** e selecione **Editar referências...** . Verifique as **MonkeyChatCommon** do projeto e clique no **Okey** botão: 

    [![](implementing-sirikit-images/prep05.png "Verifique o projeto MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. No **Gerenciador de soluções**, arraste o código compartilhado comum do aplicativo principal para a biblioteca nativa.
7. No caso de MonkeyChat, arraste a **DataModels** e **processadores** pastas do aplicativo principal para a biblioteca nativa: 

    [![](implementing-sirikit-images/prep06.png "As pastas de processadores e DataModels no Gerenciador de soluções")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie o Visual Studio e abra o aplicativo MonkeyChat.
2. Clique com botão direito no nome da solução nos **Gerenciador de soluções** e selecione **Add** > **novo projeto...** .
3. Selecione **Visual C#**   >  **projeto compartilhado** e clique no **próximo** botão: 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Selecione a biblioteca de classes")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Insira `MonkeyChatCommon` para o **nome** e clique no **criar** botão.
5. Clique com botão direito no **referências** pasta do aplicativo principal na **Gerenciador de soluções** e selecione **Editar referências...** . Verifique as **MonkeyChatCommon** do projeto e clique no **Okey** botão: 

    [![](implementing-sirikit-images/prep05w.png "Verifique o projeto MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. No **Gerenciador de soluções**, arraste o código compartilhado comum do aplicativo principal para o projeto compartilhado.
7. No caso de MonkeyChat, arraste a **DataModels** e **processadores** pastas do aplicativo principal para a biblioteca nativa.

-----

Editar qualquer um dos arquivos que foram movidos para a biblioteca nativa e alterar o namespace para corresponder da biblioteca. Por exemplo, alterando `MonkeyChat` para `MonkeyChatCommon`:

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

Em seguida, volte para o aplicativo principal e adicione um `using` instrução para o namespace da biblioteca nativa em qualquer lugar o aplicativo usa uma das classes que foram movidas:

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

### <a name="architecting-the-app-for-extensions"></a>A arquitetura de aplicativo para extensões

Normalmente, um aplicativo se inscreverá para várias tentativas e o desenvolvedor precisa garantir que o aplicativo foi projetado para o número apropriado de intenção de extensões.

A situação em que um aplicativo requer mais de uma intenção, o desenvolvedor tem a opção de colocar todas as sua intenção manipulação em uma extensão de intenção ou criando uma extensão de intenção separado para cada tentativa.

Se escolher criar uma extensão de intenção separada para cada tentativa, o desenvolvedor poderia acabar duplicando uma grande quantidade de código clichê em cada extensão e criar uma grande quantidade de processador e a sobrecarga de memória.

Para ajudar a escolher entre as duas opções, consulte se qualquer uma das intenções naturalmente pertencem juntas. Por exemplo, um aplicativo que fez a chamadas de áudio e vídeos talvez queira incluir ambas essas intenções em uma única extensão de intenção de como eles lidam tarefas semelhantes e pode fornecer a reutilização de código mais.

Para qualquer intenção ou o grupo de tentativas que não se encaixam em um grupo existente, crie uma nova extensão de intenção na solução do aplicativo para contê-los.


### <a name="setting-the-required-entitlements"></a>Configurando os direitos necessários

Qualquer aplicativo xamarin. IOS que inclui a integração de SiriKit, devem ter os direitos corretos definido. Se o desenvolvedor não definida corretamente esses direitos necessários, eles não poderão instalar ou testar o aplicativo no iOS reais 10 (ou superior) hardware, que também é um requisito desde o iOS 10 Simulator não dá suporte a SiriKit.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes o `Entitlements.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
2. Alterne para o **origem** guia.
3. Adicione a `com.apple.developer.siri` **propriedade**, defina as **tipo** para `Boolean` e o **valor** para `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Adicione a propriedade com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Salve as alterações no arquivo.
5. Clique duas vezes o **arquivo de projeto** na **Gerenciador de soluções** para abri-lo para edição.
6. Selecione **assinatura do pacote iOS** e certifique-se de que o `Entitlements.plist` arquivo for selecionado no **direitos personalizados** campo: 

    [![](implementing-sirikit-images/setup02.png "Selecione o arquivo Entitlements. plist no campo de direitos personalizados")](implementing-sirikit-images/setup02.png#lightbox)
7. Clique no botão **OK** para salvar as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes o `Entitlements.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
3. Adicione a `com.apple.developer.siri` **propriedade**, defina as **tipo** para `Boolean` e o **valor** para `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Adicione a propriedade com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
4. Salve as alterações no arquivo.
5. Clique duas vezes o **arquivo de projeto** na **Gerenciador de soluções** para abri-lo para edição.
6. Selecione **assinatura do pacote iOS** e certifique-se de que o `Entitlements.plist` arquivo for selecionado no **direitos personalizados** campo.

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

### <a name="correctly-provisioning-the-app"></a>Provisionando corretamente o aplicativo

Devido à segurança estrita de Apple colocou em torno do framework SiriKit, qualquer aplicativo xamarin. IOS que implementa o SiriKit _deve_ tem a ID do aplicativo e os direitos (consulte a seção acima) a correto e devem ser assinados com apropriadas Perfil de provisionamento.

Faça o seguinte em seu Mac:

1. Em um navegador da web, navegue até [ https://developer.apple.com ](https://developer.apple.com) e faça logon em sua conta.
2. Clique em **certificados**, **identificadores** e **perfis**.
3. Selecione **perfis de provisionamento** e selecione **IDs do aplicativo**, em seguida, clique o **+** botão.
4. Insira um **nome** para o novo perfil.
5. Insira um **ID do pacote** seguir Apple de nomes da recomendação.
6. Role para baixo até a **serviços de aplicativos** seção, selecione **SiriKit** e clique no **continuar** botão: 

    [![](implementing-sirikit-images/setup03.png "Selecione SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Verifique se todas as configurações, em seguida **enviar** ID. o App
8. Selecione **perfis de provisionamento** > **desenvolvimento**, clique no **+** botão, selecione o **ID da Apple**, em seguida, clique em **continuar**.
9. Clique em selecionar **todos os**, em seguida, clique em **continuar**.
10. Clique em **Selecionar tudo** novamente, em seguida, clique em **continuar**.
11. Insira um **nome do perfil** usando o Apple de nomenclatura do sugestões, em seguida, clique em **continuar**.
12. Inicie o Xcode.
13. No menu do Xcode selecione **preferências...**
14. Selecione **contas**, em seguida, clique o **exibir detalhes...** Botão: 

    [![](implementing-sirikit-images/setup04.png "Selecione contas")](implementing-sirikit-images/setup04.png#lightbox)
15. Clique o **baixar todos os perfis** botão no canto inferior esquerdo: 

    [![](implementing-sirikit-images/setup05.png "Baixar todos os perfis")](implementing-sirikit-images/setup05.png#lightbox)
16. Certifique-se de que o **perfil de provisionamento** criado acima foi instalado no Xcode.
17. Abra o projeto para adicionar o suporte para SiriKit no Visual Studio para Mac.
18. Clique duas vezes o `Info.plist` arquivo o **Gerenciador de soluções**.
18. Certifique-se de que o **identificador de pacote** coincide com aquele criado no Portal do desenvolvedor da Apple acima: 

    [![](implementing-sirikit-images/setup06.png "The Bundle Identifier")](implementing-sirikit-images/setup06.png#lightbox)
18. No **Gerenciador de soluções**, selecione o **projeto**.
19. Clique com botão direito no projeto e selecione **opções**.
21. Selecione **assinatura do pacote iOS**, selecione o **identidade de assinatura** e **perfil de provisionamento** criado acima: 

    [![](implementing-sirikit-images/setup07.png "Selecione a identidade de assinatura e o perfil de provisionamento")](implementing-sirikit-images/setup07.png#lightbox)
22. Clique no botão **OK** para salvar as alterações.

> [!IMPORTANT]
> Teste SiriKit só funciona em um iOS reais 10 dispositivos de Hardware e não no iOS 10 Simulator. Se o aplicativo xamarin. IOS em hardwares reais habilitado para tendo problemas ao instalar um SiriKit, certifique-se de que os direitos necessários, a ID do aplicativo, o identificador de assinatura e o perfil de provisionamento foram corretamente configurados no Portal do desenvolvedor da Apple e Visual Studio para Mac.

### <a name="requesting-siri-authorization"></a>Solicitando a autorização da Siri

Antes do aplicativo adiciona um vocabulário específico do usuário ou as extensões de propósitos conecta-se a Siri, ele deve solicita autorização do usuário para acessar o Siri.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Editar o aplicativo `Info.plist` do arquivo, alterne para o **fonte** exibir e adicionar o `NSSiriUsageDescription` chave com um valor de cadeia de caracteres que descreve como o aplicativo usará Siri e quais tipos de dados serão enviados. Por exemplo, o aplicativo MonkeyChat poderia informar "MonkeyChat contatos serão enviados ao Siri":

[![](implementing-sirikit-images/request01.png "O NSSiriUsageDescription no editor de info. plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Editar o aplicativo `Info.plist` arquivo e adicione o `NSSiriUsageDescription` chave com um valor de cadeia de caracteres que descreve como o aplicativo usará Siri e quais tipos de dados serão enviados. Por exemplo, o aplicativo MonkeyChat poderia informar "MonkeyChat contatos serão enviados ao Siri":

[![](implementing-sirikit-images/request01w.png "O NSSiriUsageDescription no editor de info. plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Chame o `RequestSiriAuthorization` método da `INPreferences` classe quando o aplicativo é iniciado pela primeira vez. Editar o `AppDelegate.cs` de classe e fazer o `FinishedLaunching` método são semelhantes ao seguinte:


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

A primeira vez que esse método é chamado, um alerta é exibido solicitando que o usuário para permitir que o aplicativo acesse Siri. A mensagem de que o desenvolvedor é adicionado para o `NSSiriUsageDescription` acima será exibido neste alerta. Se o usuário inicialmente nega acesso, eles podem usar o **configurações** aplicativo para conceder acesso ao aplicativo.

A qualquer momento, o aplicativo pode verificar a capacidade do aplicativo para acessar o Siri chamando o `SiriAuthorizationStatus` método da `INPreferences` classe.

### <a name="localization-and-siri"></a>Siri e localização

Em um dispositivo iOS, o usuário é capaz de selecionar um idioma para Siri diferente, em seguida, o padrão do sistema. Ao trabalhar com dados localizados, o aplicativo precisará usar o `SiriLanguageCode` método da `INPreferences` classe para obter o código de idioma do Siri. Por exemplo:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Adicionando o vocabulário específico do usuário

O vocabulário específico do usuário irá fornecer palavras ou frases que são exclusivos para usuários individuais do aplicativo. Eles serão fornecidos em tempo de execução do aplicativo principal (e não as extensões de aplicativo) como um conjunto ordenado de termos, ordenados na prioridade de uso mais significativa para os usuários, com os termos mais importantes no início da lista.

Vocabulário específico do usuário deve pertencer a uma das seguintes categorias:

- Nomes de contato (que não são gerenciados pela estrutura de contatos).
- Marcas de foto.
- Nomes de álbuns de fotos.
- Nomes de treinamento.

Ao selecionar a terminologia para registrar como vocabulário personalizada, escolha apenas termos que podem ser interpretados incorretamente por alguém que não esteja familiarizado com o aplicativo. Nunca register termos comuns, como "My ginástica" ou "Meu álbum". Por exemplo, o aplicativo MonkeyChat registrará os apelidos associados com cada contato no catálogo de endereços do usuário.

O aplicativo fornece o vocabulário específico do usuário chamando o `SetVocabularyStrings` método da `INVocabulary` classe e passando um `NSOrderedSet` coleção do aplicativo principal. O aplicativo sempre deve chamar o `RemoveAllVocabularyStrings` método primeiro para remover quaisquer termos existentes antes de adicionar novos. Por exemplo:

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

Com esse código, ele pode ser chamado da seguinte maneira:

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
> Siri trata de vocabulário personalizada como dicas e irá incorporar o máximo da terminologia quanto possível. No entanto, espaço de vocabulário personalizada está limitado tornando importante registrar _apenas_ a terminologia que pode ser confusa, portanto, mantendo o número total de termos registrados em um mínimo.

Para obter mais informações, consulte nosso [referência de vocabulário específico do usuário](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [referência de vocabulário personalizado especificando](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Adicionando o vocabulário específico do aplicativo

O vocabulário específico do aplicativo define as palavras e frases específicas que serão conhecidos para todos os usuários do aplicativo, como tipos de veículo ou nomes de treinamento. Como eles são parte do aplicativo, eles são definidos em um `AppIntentVocabulary.plist` arquivo como parte do pacote de aplicativo principal. Além disso, essas palavras e frases devem ser localizadas.

Termos de vocabulário específico do aplicativo devem pertencer a uma das seguintes categorias:

- Propagar as opções.
- Nomes de treinamento.

O arquivo de vocabulário específico do aplicativo contém duas chaves de nível raiz:

- `ParameterVocabularies` **Necessário** -define termos personalizados e os parâmetros de intenção que elas se aplicam a do aplicativo.
- `IntentPhrases` **Opcional** -contém frases de exemplo usando os termos personalizados definidos no `ParameterVocabularies`.

Cada entrada no `ParameterVocabularies` deve especificar uma cadeia de caracteres de ID, o termo e a intenção de que o termo se aplica a. Além disso, um único termo pode se aplicar a várias tentativas.

Para obter uma lista completa de valores aceitáveis e estrutura de arquivo necessário, consulte da Apple [referência de formato de arquivo do aplicativo vocabulário](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Para adicionar um `AppIntentVocabulary.plist` arquivo ao projeto de aplicativo, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com botão direito no nome do projeto na **Gerenciador de soluções** e selecione **Add** > **novo arquivo...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Adicionar uma lista de propriedades")](implementing-sirikit-images/plist01.png#lightbox)
2. Clique duas vezes o `AppIntentVocabulary.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
3. Clique o **+** para adicionar uma chave, defina as **nome** para `ParameterVocabularies` e o **tipo** para `Array`:

    [![](implementing-sirikit-images/plist02.png "Defina o nome como ParameterVocabularies e o tipo de matriz")](implementing-sirikit-images/plist02.png#lightbox)
4. Expandir `ParameterVocabularies` e clique em de **+** botão e defina o **tipo** para `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Defina o tipo ao dicionário")](implementing-sirikit-images/plist03.png#lightbox)
5. Clique o **+** para adicionar uma nova chave, defina as **nome** para `ParameterNames` e o **tipo** para `Array`:

    [![](implementing-sirikit-images/plist04.png "Defina o nome como ParameterNames e o tipo de matriz")](implementing-sirikit-images/plist04.png#lightbox)
6. Clique o **+** para adicionar uma nova chave com o **tipo** de `String` e o valor como um dos nomes de parâmetro disponíveis. Por exemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "A chave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Adicione a `ParameterVocabulary` chave para o `ParameterVocabularies` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06.png "Adicionar a chave ParameterVocabulary para a chave de ParameterVocabularies com o tipo de matriz")](implementing-sirikit-images/plist06.png#lightbox)
8. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist07.png#lightbox)
9. Adicione a `VocabularyItemIdentifier` chave com o **tipo** de `String` e especifique uma ID exclusiva para o termo:

    [![](implementing-sirikit-images/plist08.png "Adicionar a chave de VocabularyItemIdentifier com o tipo de cadeia de caracteres e especifique uma ID exclusiva")](implementing-sirikit-images/plist08.png#lightbox)
10. Adicione a `VocabularyItemSynonyms` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09.png "Adicionar a chave de VocabularyItemSynonyms com o tipo de matriz")](implementing-sirikit-images/plist09.png#lightbox)
11. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist10.png#lightbox)
12. Adicione a `VocabularyItemPhrase` chave com o **tipo** de `String` e o termo que o aplicativo está definindo:

    [![](implementing-sirikit-images/plist11.png "Adicionar a chave de VocabularyItemPhrase com o tipo de cadeia de caracteres e o termo que o aplicativo está definindo")](implementing-sirikit-images/plist11.png#lightbox)
13. Adicione a `VocabularyItemPronunciation` chave com o **tipo** de `String` e a pronúncia fonética do termo:

    [![](implementing-sirikit-images/plist12.png "Adicionar a chave de VocabularyItemPronunciation com o tipo de cadeia de caracteres e a pronúncia fonética do termo")](implementing-sirikit-images/plist12.png#lightbox)
14. Adicione a `VocabularyItemExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13.png "Adicionar a chave de VocabularyItemExamples com o tipo de matriz")](implementing-sirikit-images/plist13.png#lightbox)
15. Adicionar algumas `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist14.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist14.png#lightbox)
16. Repita as etapas acima para quaisquer outros termos personalizados que o aplicativo precisa definir.
17. Recolher o `ParameterVocabularies` chave.
18. Adicione a `IntentPhrases` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15.png "Adicionar a chave de IntentPhrases com o tipo de matriz")](implementing-sirikit-images/plist15.png#lightbox)
19. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist16.png#lightbox)
20. Adicionar o `IntentName` chave com o **tipo** de `String` e intenção, por exemplo:

    [![](implementing-sirikit-images/plist17.png "Adicionar a chave de IntentName com o tipo de cadeia de caracteres e a intenção do exemplo")](implementing-sirikit-images/plist17.png#lightbox)
21. Adicione a `IntentExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18.png "Adicionar a chave de IntentExamples com o tipo de matriz")](implementing-sirikit-images/plist18.png#lightbox)
22. Adicionar algumas `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist19.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist19.png#lightbox)
23. Repita as etapas acima para propósitos do aplicativo precisa fornecer o exemplo de uso do.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique com botão direito no nome do projeto na **Gerenciador de soluções** e selecione **Adicionar > Novo Item... > Apple > lista de Propriedades > Info. plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Adicionar um novo Info. plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Clique duas vezes o `AppIntentVocabulary.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
3. Clique o **+** para adicionar uma chave, defina as **nome** para `ParameterVocabularies` e o **tipo** para `Array`:

    [![](implementing-sirikit-images/plist02w.png "Defina o nome como ParameterVocabularies e o tipo de matriz")](implementing-sirikit-images/plist02w.png#lightbox)
4. Expandir `ParameterVocabularies` e clique em de **+** botão e defina o **tipo** para `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Defina o tipo ao dicionário")](implementing-sirikit-images/plist03w.png#lightbox)
5. Clique o **+** para adicionar uma nova chave, defina as **nome** para `ParameterNames` e o **tipo** para `Array`:

    [![](implementing-sirikit-images/plist04w.png "Defina o nome como ParameterNames e o tipo de matriz")](implementing-sirikit-images/plist04w.png#lightbox)
6. Clique o **+** para adicionar uma nova chave com o **tipo** de `String` e o valor como um dos nomes de parâmetro disponíveis. Por exemplo, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "A chave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Adicione a `ParameterVocabulary` chave para o `ParameterVocabularies` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist06w.png "Adicionar a chave ParameterVocabulary para a chave de ParameterVocabularies com o tipo de matriz")](implementing-sirikit-images/plist06w.png#lightbox)
8. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist07w.png#lightbox)
9. Adicione a `VocabularyItemIdentifier` chave com o **tipo** de `String` e especifique uma ID exclusiva para o termo:

    [![](implementing-sirikit-images/plist08w.png "Adicionar a chave de VocabularyItemIdentifier com o tipo de cadeia de caracteres e especifique uma ID exclusiva para o termo")](implementing-sirikit-images/plist08w.png#lightbox)
10. Adicione a `VocabularyItemSynonyms` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist09w.png "Adicionar a chave de VocabularyItemSynonyms com o tipo de matriz")](implementing-sirikit-images/plist09w.png#lightbox)
11. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist10w.png#lightbox)
12. Adicione a `VocabularyItemPhrase` chave com o **tipo** de `String` e o termo que o aplicativo está definindo:

    [![](implementing-sirikit-images/plist11w.png "Adicionar a chave de VocabularyItemPhrase com o tipo de cadeia de caracteres e o termo que o aplicativo está definindo")](implementing-sirikit-images/plist11w.png#lightbox)
13. Adicione a `VocabularyItemPronunciation` chave com o **tipo** de `String` e a pronúncia fonética do termo:

    [![](implementing-sirikit-images/plist12w.png "Adicionar a chave de VocabularyItemPronunciation com o tipo de cadeia de caracteres e a pronúncia fonética do termo")](implementing-sirikit-images/plist12w.png#lightbox)
14. Adicione a `VocabularyItemExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist13w.png "Adicionar a chave de VocabularyItemExamples com o tipo de matriz")](implementing-sirikit-images/plist13w.png#lightbox)
15. Adicionar algumas `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist14w.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist14w.png#lightbox)
16. Repita as etapas acima para quaisquer outros termos personalizados que o aplicativo precisa definir.
17. Recolher o `ParameterVocabularies` chave.
18. Adicione a `IntentPhrases` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist15w.png "Adicionar a chave de IntentPhrases com o tipo de matriz")](implementing-sirikit-images/plist15w.png#lightbox)
19. Adicionar uma nova chave com o **tipo** de `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Adicione uma nova chave com o tipo de dicionário")](implementing-sirikit-images/plist16w.png#lightbox)
20. Adicionar o `IntentName` chave com o **tipo** de `String` e intenção, por exemplo:

    [![](implementing-sirikit-images/plist17w.png "Adicionar a chave de IntentName com o tipo de cadeia de caracteres e a intenção do exemplo")](implementing-sirikit-images/plist17w.png#lightbox)
21. Adicione a `IntentExamples` chave com o **tipo** de `Array`:

    [![](implementing-sirikit-images/plist18w.png "Adicionar a chave de IntentExamples com o tipo de matriz")](implementing-sirikit-images/plist18w.png#lightbox)
22. Adicionar algumas `String` chaves com exemplos de uso do termo:

    [![](implementing-sirikit-images/plist19w.png "Adicionar algumas chaves de cadeia de caracteres com exemplos de uso do termo")](implementing-sirikit-images/plist19w.png#lightbox)
23. Repita as etapas acima para propósitos do aplicativo precisa fornecer o exemplo de uso do.

-----

> [!IMPORTANT]
> O `AppIntentVocabulary.plist` será registrado com o Siri no teste de dispositivos durante o desenvolvimento e ele podem levar algum tempo para Siri para incorporar o vocabulário personalizada. Como resultado, o testador será necessário aguardar alguns minutos antes de tentar testar vocabulário específico do aplicativo quando ele tiver sido atualizado.

Para obter mais informações, consulte nosso [referência de vocabulário específico do aplicativo](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [referência de vocabulário personalizado especificando](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Adicionando uma extensão de tentativas

Agora que o aplicativo tiver sido preparado para adotar o SiriKit, o desenvolvedor precisa adicionar extensões de propósitos de uma (ou mais) para a solução para lidar com as tentativas necessárias para a integração do Siri.

Para cada extensão de tentativas necessárias, faça o seguinte:

- Adicione um projeto de extensão de tentativas para a solução do aplicativo xamarin. IOS.
- Configurar a extensão de tentativas `Info.plist` arquivo.
- Modifique a classe principal da extensão de tentativas.

Para obter mais informações, consulte nosso [a referência de extensão de tentativas](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [criar a referência de extensão de tentativas](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Criando a extensão

Para adicionar uma extensão de tentativas para a solução, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com botão direito no **nome da solução** no **painel de soluções** e selecione **Add** > **adicionar novo projeto...** .
2. Na caixa de diálogo Selecionar **iOS** > **extensões** > **intenção extensão** e clique no **próxima** botão: 

    [![](implementing-sirikit-images/intents05.png "Selecione a extensão de intenção")](implementing-sirikit-images/intents05.png#lightbox)
3. Em seguida, insira um **nome** para o propósito de extensão e clique o **próxima** botão: 

    [![](implementing-sirikit-images/intents06.png "Insira um nome para a extensão de intenção")](implementing-sirikit-images/intents06.png#lightbox)
4. Por fim, clique o **criar** botão para adicionar a extensão de intenção para a solução de aplicativos: 

    [![](implementing-sirikit-images/intents07.png "Adicionar a extensão de intenção para a solução de aplicativos")](implementing-sirikit-images/intents07.png#lightbox)
5. No **Gerenciador de soluções**, clique com botão direito no **referências** pasta da intenção de extensão recém-criada. Verifique o nome do projeto de biblioteca de código compartilhado comum (o que o aplicativo criado acima) e clique no **Okey** botão: 

    [![](implementing-sirikit-images/intents08.png "Selecione o nome do projeto de biblioteca de código compartilhado comum")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Com o botão direito no **nome da solução** na **Gerenciador de soluções** e selecione **Add** > **adicionar novo projeto...** .
2. Na caixa de diálogo Selecionar **Visual C# > extensões do iOS > extensão intenção** e clique em de **próxima** botão:

    [![](implementing-sirikit-images/intents05.w157-sml.png "Selecione a extensão de intenção")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Em seguida, insira um **nome** para o propósito de extensão e clique o **Okey** botão.
1. No **Gerenciador de soluções**, clique com botão direito no **referências** pasta da extensão de tentativas recém-criado e escolha **Adicionar > referência**. Verifique o nome do projeto de biblioteca de código compartilhado comum (o que o aplicativo criado acima) e clique no **Okey** botão:

    [![](implementing-sirikit-images/intents08w.png "Selecione o nome do projeto de biblioteca de código compartilhado comum")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Repita essas etapas para o número de extensões de intenção (com base em [arquitetar o aplicativo para extensões](#Architecting-the-App-for-Extensions) seção acima) que o aplicativo exigirá.

### <a name="configuring-the-infoplist"></a>Configurando o Info. plist

Para cada uma das extensões de propósitos que adicionou à solução do aplicativo, deve ser configurado no `Info.plist` arquivos para trabalhar com o aplicativo.

Assim como qualquer extensão de aplicativo típica, o aplicativo terá as chaves existentes de `NSExtension` e `NSExtensionAttributes`. Para uma extensão de tentativas, há dois novos atributos que devem ser configurados:

[![](implementing-sirikit-images/intents01.png "Os dois novos atributos que devem ser configurados")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** - é necessário e consiste em uma matriz de nomes de classe de intenção que o aplicativo deseja dar suporte a partir da extensão de intenção.
- **IntentsRestrictedWhileLocked** -é uma chave opcional para o aplicativo especificar o comportamento de tela de bloqueio da extensão. Ele consiste em uma matriz de nomes de classe de intenção que o aplicativo deseja exigir que o usuário estar conectado para usar a extensão de intenção de.

Para configurar a extensão de intenção `Info.plist` de arquivos, clique duas vezes no **Gerenciador de soluções** para abri-lo para edição. Em seguida, alterne para o **fonte** exibir em seguida, expanda o `NSExtension` e `NSExtensionAttributes` chaves no editor:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "As chaves NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "As chaves NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Expanda o `IntentsSupported` da chave e adicione o nome de qualquer classe de intenção dará suporte a essa extensão. Para o exemplo de aplicativo MonkeyChat, ele dá suporte a `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Se o aplicativo, opcionalmente, exigir que o usuário estar conectado ao dispositivo para usar uma determinada tentativa, expanda o `IntentRestrictedWhileLocked` da chave e adicione os nomes de classe das intenções que tenham acesso restrito. Para o aplicativo MonkeyChat de exemplo, o usuário deve estar conectado para enviar uma mensagem de bate-papo, portanto, adicionamos `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "A chave INSendMessageIntent adicionada")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "A chave INSendMessageIntent adicionada")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Para obter uma lista completa de domínios de intenção disponíveis, consulte da Apple [referência de domínios de intenção](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurando a classe principal

Em seguida, o desenvolvedor precisa configurar a classe principal que atua como ponto de entrada principal para a extensão de intenção em Siri. Ele deve ser uma subclasse de `INExtension` que está de acordo com o `IINIntentHandler` delegar. Por exemplo:

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

Há um método solitário que o aplicativo deve implementar na classe principal intenção de extensão, o `GetHandler` método. Esse método é passado uma intenção por SiriKit e o aplicativo deve retornar um **intenção manipulador** que corresponde ao tipo da intenção determinado.

Como o exemplo de aplicativo MonkeyChat apenas lida com uma intenção, ele está retornando em si no `GetHandler` método. Se a extensão manipulado mais de uma intenção, o desenvolvedor seria adicionar uma classe para cada tipo de tentativa e retornar uma instância com base no `Intent` passado para o método.

### <a name="handling-the-resolve-stage"></a>Tratando o estágio de resolução

O estágio de resolver é onde a extensão de intenção será esclarecer e validar os parâmetros passados do Siri e tiverem sido definidos por meio de conversação do usuário.

Para cada parâmetro que é enviado da Siri, há um `Resolve` método. O aplicativo precisará implementar esse método para cada parâmetro que o aplicativo pode precisar de Ajuda da Siri para obter a resposta correta do usuário.

No caso do aplicativo MonkeyChat de exemplo, a extensão de intenção exigirá um ou mais destinatários ao enviar a mensagem. Para cada destinatário na lista, a extensão será necessário fazer uma pesquisa de contato que pode ter o seguinte resultado:

- Exatamente um contato correspondente for encontrado.
- Dois ou mais contatos correspondentes são encontrados.
- Nenhum contato correspondente localizado.

Além disso, MonkeyChat requer o conteúdo do corpo da mensagem. Se o usuário não tiver fornecido a isso, Siri precisa solicitar ao usuário para o conteúdo.

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

Para obter mais informações, consulte nosso [a resolver a referência do estágio](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [Resolving e tratamento de referência de intenções](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Tratando o estágio de confirmar

O estágio de confirmação é onde a extensão de intenção verifica para ver que ele tem todas as informações para atender à solicitação do usuário. O aplicativo deseja enviar será de confirmação ao longo de todos os detalhes de suporte do que está prestes a acontecer para Siri para que ele possa ser confirmado com o usuário que esta é a ação pretendida.

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

Para obter mais informações, consulte nosso [a confirmar a referência de estágio](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>A intenção de processamento

Isso é o ponto em que a extensão de intenção realmente executa a tarefa para atender à solicitação do usuário e retornar os resultados para Siri para que o usuário pode ser informado.


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

Para obter mais informações, consulte nosso [a lidar com a referência de estágio](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Adicionando uma extensão de interface do usuário de tentativas

A extensão de interface do usuário de tentativas opcional apresenta a oportunidade para trazer a interface do usuário do aplicativo e a identidade visual na experiência de Siri e fazer com que os usuários se sinta conectado ao aplicativo. Com essa extensão, o aplicativo pode trazer marca, bem como o visuais e outras informações na transcrição.

[![](implementing-sirikit-images/intentsui01.png "Um exemplo de saída de extensão de interface do usuário de tentativas")](implementing-sirikit-images/intentsui01.png#lightbox)

Assim como a extensão de tentativas, o desenvolvedor fará a etapa a seguir para a extensão de interface do usuário de tentativas:

- Adicione um projeto de extensão de interface do usuário de tentativas para a solução do aplicativo xamarin. IOS.
- Configurar a extensão de interface do usuário de tentativas `Info.plist` arquivo.
- Modifique a classe principal da extensão de interface do usuário de tentativas.

Para obter mais informações, consulte nosso [a referência de extensão de interface do usuário intenções](~/ios/platform/sirikit/understanding-sirikit.md) da Apple [fornecer uma referência de Interface personalizada](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Criando a extensão

Para adicionar uma extensão de interface do usuário de tentativas para a solução, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com botão direito no **nome da solução** no **painel de soluções** e selecione **Add** > **adicionar novo projeto...** .
2. Na caixa de diálogo Selecionar **iOS** > **extensões** > **intenção de extensão de interface do usuário** e clique no **próxima** botão: 

    [![](implementing-sirikit-images/intents11.png "Selecione a extensão de intenção de interface do usuário")](implementing-sirikit-images/intents11.png#lightbox)
3. Em seguida, insira um **nome** para o propósito de extensão e clique o **próxima** botão: 

    [![](implementing-sirikit-images/intents12.png "Insira um nome para a extensão de intenção")](implementing-sirikit-images/intents12.png#lightbox)
4. Por fim, clique o **criar** botão para adicionar a extensão de intenção para a solução de aplicativos: 

    [![](implementing-sirikit-images/intents13.png "Adicionar a extensão de intenção para a solução de aplicativos")](implementing-sirikit-images/intents13.png#lightbox)
5. No **Gerenciador de soluções**, clique com botão direito no **referências** pasta da intenção de extensão recém-criada. Verifique o nome do projeto de biblioteca de código compartilhado comum (o que o aplicativo criado acima) e clique no **Okey** botão: 

    [![](implementing-sirikit-images/intents14.png "Selecione o nome do projeto de biblioteca de código compartilhado comum")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Com o botão direito no **nome da solução** na **Gerenciador de soluções** e selecione **Add** > **adicionar novo projeto...**
2. Na caixa de diálogo Selecionar **iOS** > **extensões** > **intenção de extensão de interface do usuário** e clique no **próxima** botão.
3. Em seguida, insira um **nome** para o propósito de extensão e clique o **Okey** botão.
5. No **Gerenciador de soluções**, clique com botão direito no **referências** pasta da intenção de extensão recém-criada. Verifique o nome do projeto de biblioteca de código compartilhado comum (o que o aplicativo criado acima) e clique no **Okey** botão.
    
-----

### <a name="configuring-the-infoplist"></a>Configurando o Info. plist

Configurar a extensão de interface do usuário de tentativas `Info.plist` arquivo para trabalhar com o aplicativo.

Assim como qualquer extensão de aplicativo típica, o aplicativo terá as chaves existentes de `NSExtension` e `NSExtensionAttributes`. Para uma extensão de tentativas, há um novo atributo que deve ser configurado:

[![](implementing-sirikit-images/intents03.png "Um novo atributo deve ser configurado")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** é necessária e consiste em uma matriz de nomes de classe de intenção que deseja que o aplicativo para dar suporte a partir da extensão de intenção.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para configurar a extensão de interface do usuário de intenção `Info.plist` de arquivos, clique duas vezes no **Gerenciador de soluções** para abri-lo para edição. Em seguida, alterne para o **fonte** exibir em seguida, expanda o `NSExtension` e `NSExtensionAttributes` chaves no editor:

[![](implementing-sirikit-images/intents04.png "As chaves NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para configurar a extensão de interface do usuário de intenção `Info.plist` de arquivos, clique duas vezes no **Gerenciador de soluções** para abri-lo para edição. Expanda o `NSExtension` e `NSExtensionAttributes` chaves no editor:

[![](implementing-sirikit-images/intents04w.png "Chaves de Tthe NSExtension e NSExtensionAttributes no editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Expanda o `IntentsSupported` da chave e adicione o nome de qualquer classe de intenção dará suporte a essa extensão. Para o exemplo de aplicativo MonkeyChat, ele dá suporte a `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Para obter uma lista completa de domínios de intenção disponíveis, consulte da Apple [referência de domínios de intenção](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurando a classe principal

Configure a classe principal que atua como ponto de entrada principal para a extensão de interface do usuário da intenção em Siri. Ele deve ser uma subclasse de `UIViewController` que está de acordo com o `IINUIHostedViewController` interface. Por exemplo:

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

Siri passará um `INInteraction` instância da classe para o `Configure` método da `UIViewController` instância dentro a extensão de interface do usuário de intenção.

O `INInteraction` objeto fornece três informações cruciais para a extensão:

1. O objeto intencional que está sendo processado.
2. O objeto de resposta de intenção do `Confirm` e `Handle` métodos de extensão a intenção.
3. O estado de interação que define o estado da interação entre o aplicativo e o Siri.

O `UIViewController` instância é a classe de entidade para a interação com o Siri e porque ele herda de `UIViewController`, ele tem acesso a todos os recursos de UIKit.

Quando o Siri chama o `Configure` método da `UIViewController` transmiti-la em um contexto de exibição informando que o controlador de exibição também será hospedado em um Siri Snippit ou mapas de cartão.

Siri irá passar em um manipulador de conclusão que o aplicativo precisa retornar o tamanho desejado da exibição depois que o aplicativo terminar de configurá-lo.

### <a name="design-the-ui-in-ios-designer"></a>Projetar a interface do usuário no Designer do iOS

Interface de usuário da extensão interface do usuário de tentativas de layout no Designer do iOS. Clique duas vezes a extensão `MainInterface.storyboard` arquivo o **Gerenciador de soluções** para abri-lo para edição. Arraste em todos os elementos de interface do usuário necessários para criar a Interface do usuário e salve as alterações.

> [!IMPORTANT]
> Embora seja possível adicionar elementos interativos, como `UIButtons` ou `UITextFields` para a extensão de interface do usuário de intenção `UIViewController`, esses são estritamente proibidos como a intenção da interface do usuário no não interativo e o usuário não será capaz de interagir com eles.

### <a name="wire-up-the-user-interface"></a>Wire-Up da Interface do usuário

Com a Interface do usuário da extensão de interface do usuário intenções criados no Designer do iOS, edite o `UIViewController` subclasse e substituir o `Configure` método da seguinte maneira:

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

A extensão de interface do usuário de tentativas será sempre exibida juntamente com outros tipos de conteúdo, como o ícone do aplicativo e o nome na parte superior da interface do usuário Siri ou, com base na intenção, botões (como enviar ou Cancelar) podem ser exibido na parte inferior.

Há alguns casos em que o aplicativo pode substituir as informações que Siri está exibindo ao usuário por padrão, como mensagens ou mapas em que o aplicativo pode substituir a experiência padrão com um personalizado para o aplicativo.

Se a extensão de interface do usuário de tentativas precisar substituir elementos do padrão Siri UI, o `UIViewController` subclasse precisará implementar a `IINUIHostedViewSiriProviding` interface opt-in e para a exibição de um elemento de interface específica.

Adicione o seguinte código para o `UIViewController` subclasse para informar que o Siri se a extensão de interface do usuário de intenção já está exibindo o conteúdo da mensagem:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considerações

A Apple sugere que o desenvolvedor levar as considerações a seguir em consideração ao projetar e implementar as extensões de interface do usuário de intenção:

- **Estar consciente de uso de memória** - extensões de interface do usuário de intenção porque são temporárias e somente mostrado por um curto período, o sistema impõe restrições de memória mais do que o utilizado com um aplicativo completo.
- **Considere o mínimo e tamanhos máximos de exibição** -Certifique-se de que as extensões de interface do usuário de intenção tenha aparência satisfatório em cada tipo de dispositivo iOS, tamanho e orientação. Além disso, o tamanho desejado que o aplicativo envia para o Siri não poderá ser concedido.
- **Usar padrões de Layout adaptável e flexível** – novamente, para garantir que a interface do usuário fica ótima em todos os dispositivos.

## <a name="summary"></a>Resumo

Este artigo tem abordado SiriKit e mostrado como ele pode ser adicionado aos aplicativos xamarin. IOS para fornecer serviços que são acessíveis para o usuário usando o Siri e o aplicativo de mapas em um dispositivo iOS.




## <a name="related-links"></a>Links relacionados

- [Exemplo de ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guia de programação de SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Referência de estrutura de tentativas](https://developer.apple.com/reference/intents)
- [Referência de estrutura de interface do usuário de tentativas](https://developer.apple.com/reference/intentsui)
- [Referência de intenção de domínios](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
