---
title: Estrutura sociais do xamarin
description: A estrutura Social fornece uma API unificada para interagir com redes sociais, incluindo o Facebook e Twitter, bem como SinaWeibo para os usuários na China.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 9b7269282b18adc46f53b708a0af4934a1621d23
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788189"
---
# <a name="social-framework-in-xamarinios"></a>Estrutura sociais do xamarin

_A estrutura Social fornece uma API unificada para interagir com redes sociais, incluindo o Facebook e Twitter, bem como SinaWeibo para os usuários na China._

Usando a estrutura Social permite que aplicativos a interagir com redes sociais de uma única API sem a necessidade de gerenciar a autenticação. Ele inclui um sistema fornecido controlador de exibição para compor postagens, bem como uma abstração que permite que API da cada rede social de consumo por HTTP.

> [!IMPORTANT]
> Para uma API de plataforma cruzada para se conectar a várias redes sociais, consulte o [Xamarin.Social](http://components.xamarin.com/view/xamarin.social/) componente no armazenamento do componente Xamarin.

## <a name="connecting-to-twitter"></a>Conectar com o Twitter

### <a name="twitter-account-settings"></a>Configurações de conta do Twitter

Para se conectar ao Twitter usando a estrutura Social, uma conta deve ser configurado nas configurações do dispositivo, conforme mostrado abaixo:

 [![](social-framework-images/twitter01.png "Configurações de conta do Twitter")](social-framework-images/twitter01.png#lightbox)

Depois que uma conta foi inserida e verificada com o Twitter, qualquer aplicativo no dispositivo que usa as classes do Framework Social para acessar o Twitter usará essa conta.

### <a name="sending-tweets"></a>Tweets de enviadas

A estrutura Social inclui um controlador chamado `SLComposeViewController` que apresenta um modo de exibição para editar e enviar um tweet fornecido pelo sistema. Captura de tela a seguir mostra um exemplo dessa exibição:

 [![](social-framework-images/twitter02.png "Esta captura de tela mostra um exemplo da SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Para usar um `SLComposeViewController` com o Twitter, uma instância do controlador deve ser criada chamando o `FromService` método com `SLServiceType.Twitter` conforme mostrado abaixo:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Após a `SLComposeViewController` instância é retornada, ele pode ser usado para apresentar uma interface do usuário para postar no Twitter. No entanto, a primeira coisa a fazer é verificar a disponibilidade de rede social, Twitter nesse caso, chamando `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController` nunca envia um tweet diretamente sem interação do usuário. No entanto, ele pode ser inicializado com os seguintes métodos:

-   `SetInitialText` – Adiciona o texto inicial para mostrar o tweet. 
-  `AddUrl` – Adiciona uma Url para o tweet.
-  `AddImage` – Adiciona uma imagem para o tweet.


Depois de inicializadas, chamando `PresentVIewController` exibe o modo de exibição criado pelo `SLComposeViewController`. O usuário pode, opcionalmente, editar e enviar o tweet ou Cancelar enviá-la. Em ambos os casos, o controlador deve ser descartado no `CompletionHandler`, onde o resultado também pode ser verificado para ver se o tweet foi enviado ou cancelado, conforme mostrado abaixo:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Exemplo de Tweet

O código a seguir demonstra como usar o `SLComposeViewController` para apresentar uma exibição usada para enviar um tweet:

```csharp
using System;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _twitterComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
        #endregion

        #region Computed Properties
        public bool isTwitterAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Twitter); }
        }

        public SLComposeViewController TwitterComposer {
            get { return _twitterComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            SendTweet.Enabled = isTwitterAvailable;
        }
        #endregion

        #region Actions
        partial void SendTweet_TouchUpInside (UIButton sender)
        {
            // Set initial message
            TwitterComposer.SetInitialText ("Hello Twitter!");
            TwitterComposer.AddImage (UIImage.FromFile ("Icon.png"));
            TwitterComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (TwitterComposer, true, null);
        }
        #endregion
    }
}
```

### <a name="calling-twitter-api"></a>Chamar a API do Twitter

A estrutura Social também inclui suporte para fazer solicitações HTTP para redes sociais. Ele encapsula a solicitação em um `SLRequest` classe que é usada para o destino de API da rede social específica.

Por exemplo, o código a seguir faz uma solicitação para o Twitter para obter a linha do tempo pública (expandindo no código acima):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _twitterAccount;
#endregion

#region Computed Properties
public ACAccount TwitterAccount {
    get { return _twitterAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    SendTweet.Enabled = isTwitterAvailable;
    RequestTwitterTimeline.Enabled = false;

    // Initialize Twitter Account access 
    var accountStore = new ACAccountStore ();
    var accountType = accountStore.FindAccountType (ACAccountType.Twitter);

    // Request access to Twitter account
    accountStore.RequestAccess (accountType, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestTwitterTimeline.Enabled = true;
            });
        }
    });
}
#endregion

#region Actions
partial void RequestTwitterTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
    var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = TwitterAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

Vamos examinar este código em detalhes. Primeiro, ele obtém acesso ao armazenamento de conta e obtém o tipo de uma conta do Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Em seguida, pede ao usuário se o seu aplicativo pode ter acesso à sua conta do Twitter e, se o acesso é concedido, a conta é carregada na memória e a interface do usuário atualizada:

```csharp
// Request access to Twitter account
accountStore.RequestAccess (accountType, (granted, error) => {
    // Allowed by user?
    if (granted) {
        // Get account
        _twitterAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
        InvokeOnMainThread (() => {
            // Update UI
            RequestTwitterTimeline.Enabled = true;
        });
    }
});
```

Quando o usuário solicita os dados da linha do tempo (tocando em um botão na interface de usuário), o aplicativo primeiro formulários uma solicitação para acessar os dados do Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```
Este exemplo é limitar os resultados retornados para os dez últimos entradas, incluindo `?count=10` na URL. Finalmente, ele anexa a solicitação para a conta do Twitter (que foi carregada acima) e executa a chamada para o Twitter para buscar os dados:

```csharp
// Request data
request.Account = TwitterAccount;
request.PerformRequest ((data, response, error) => {
    // Was there an error?
    if (error == null) {
        // Was the request successful?
        if (response.StatusCode == 200) {
            // Yes, display it
            InvokeOnMainThread (() => {
                Results.Text = data.ToString ();
            });
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", response.StatusCode);
            });
        }
    } else {
        // No, display error
        InvokeOnMainThread (() => {
            Results.Text = string.Format ("Error: {0}", error);
        });
    }
});
```

Se os dados foram carregados com êxito, os dados brutos do JSON serão exibidos (como a saída de exemplo abaixo):

[![](social-framework-images/twitter03.png "Um exemplo de exibição de dados bruto do JSON")](social-framework-images/twitter03.png#lightbox)

Em um aplicativo real, os resultados JSON, em seguida, podem ser analisados como normal e os resultados apresentados ao usuário. Consulte [serviços da Web de Introdução](~/cross-platform/data-cloud/web-services/index.md) para obter informações sobre como analisar o JSON.

## <a name="connecting-to-facebook"></a>Conectar com o Facebook

### <a name="facebook-account-settings"></a>Configurações de conta do Facebook

Conectar com o Facebook com o Framework Social é praticamente idêntica ao processo usado para Twitter mostrado acima. Uma conta de usuário do Facebook deve ser configurada nas configurações do dispositivo, conforme mostrado abaixo:

[![](social-framework-images/facebook01.png "Configurações de conta do Facebook")](social-framework-images/facebook01.png#lightbox)

Uma vez configurado, qualquer aplicativo no dispositivo que usa a estrutura Social usará essa conta para se conectar ao Facebook.

### <a name="posting-to-facebook"></a>Lançamentos de Facebook

Como o Framework Social é uma API unificada projetada para acessar várias redes sociais, o código permanece praticamente idêntico, independentemente de rede social que está sendo usada.

Por exemplo, o `SLComposeViewController` pode ser usado exatamente como no exemplo de Twitter mostrado anteriormente, apenas diferentes é alternar para as opções e configurações específicas do Facebook. Por exemplo:

```csharp
using System;
using Foundation;
using Social;
using UIKit;

namespace SocialFrameworkDemo
{
    public partial class ViewController : UIViewController
    {
        #region Private Variables
        private SLComposeViewController _facebookComposer = SLComposeViewController.FromService (SLServiceType.Facebook);
        #endregion

        #region Computed Properties
        public bool isFacebookAvailable {
            get { return SLComposeViewController.IsAvailable (SLServiceKind.Facebook); }
        }

        public SLComposeViewController FacebookComposer {
            get { return _facebookComposer; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update UI based on state
            PostToFacebook.Enabled = isFacebookAvailable;
        }
        #endregion

        #region Actions
        partial void PostToFacebook_TouchUpInside (UIButton sender)
        {
            // Set initial message
            FacebookComposer.SetInitialText ("Hello Facebook!");
            FacebookComposer.AddImage (UIImage.FromFile ("Icon.png"));
            FacebookComposer.CompletionHandler += (result) => {
                InvokeOnMainThread (() => {
                    DismissViewController (true, null);
                    Console.WriteLine ("Results: {0}", result);
                });
            };

            // Display controller
            PresentViewController (FacebookComposer, true, null);
        }
        #endregion
    }
}
```

Quando usado com o Facebook, o `SLComposeViewController` exibe uma exibição que é praticamente idêntica ao exemplo Twitter, mostrando **Facebook** como o título nesse caso:

[![](social-framework-images/facebook02.png "A exibição SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Chamar a API de gráfico do Facebook

Semelhante do exemplo de Twitter, a estrutura Social `SLRequest` objeto pode ser usado com a API do graph do Facebook. Por exemplo, o código a seguir retorna informações de API do graph sobre a conta Xamarin (expandindo no código acima):

```csharp
using Accounts;
...

#region Private Variables
private ACAccount _facebookAccount;
#endregion

#region Computed Properties
public ACAccount FacebookAccount {
    get { return _facebookAccount; }
}
#endregion

#region Override Methods
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update UI based on state
    PostToFacebook.Enabled = isFacebookAvailable;
    RequestFacebookTimeline.Enabled = false;

    // Initialize Facebook Account access 
    var accountStore = new ACAccountStore ();
    var options = new AccountStoreOptions ();
    var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
    accountType = accountStore.FindAccountType (ACAccountType.Facebook);

    // Request access to Facebook account
    accountStore.RequestAccess (accountType, options, (granted, error) => {
        // Allowed by user?
        if (granted) {
            // Get account
            _facebookAccount = accountStore.Accounts [accountStore.Accounts.Length - 1];
            InvokeOnMainThread (() => {
                // Update UI
                RequestFacebookTimeline.Enabled = true;
            });
        }
    });

}
#endregion

#region Actions
partial void RequestFacebookTimeline_TouchUpInside (UIButton sender)
{
    // Initialize request
    var parameters = new NSDictionary ();
    var url = new NSUrl ("https://graph.facebook.com/283148898401104");
    var request = SLRequest.Create (SLServiceKind.Facebook, SLRequestMethod.Get, url, parameters);

    // Request data
    request.Account = FacebookAccount;
    request.PerformRequest ((data, response, error) => {
        // Was there an error?
        if (error == null) {
            // Was the request successful?
            if (response.StatusCode == 200) {
                // Yes, display it
                InvokeOnMainThread (() => {
                    Results.Text = data.ToString ();
                });
            } else {
                // No, display error
                InvokeOnMainThread (() => {
                    Results.Text = string.Format ("Error: {0}", response.StatusCode);
                });
            }
        } else {
            // No, display error
            InvokeOnMainThread (() => {
                Results.Text = string.Format ("Error: {0}", error);
            });
        }
    });
}
#endregion
```

A única diferença entre esse código e a versão do Twitter apresentados a seguir, é o requisito do Facebook para obter uma desenvolvedor/específica ID do aplicativo (que pode ser gerado a partir do Portal do desenvolvedor do Facebook) que deve ser definida como uma opção ao fazer a solicitação:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

Falha ao definir essa opção (ou usando uma chave inválida) resultará em erro ou nenhum dado está sendo retornado.

## <a name="summary"></a>Resumo

Este artigo mostrou como usar a estrutura Social para interagir com o Twitter e Facebook. Ele mostrou onde configurar contas para cada rede social nas configurações do dispositivo. Ele também descreve como usar o `SLComposeViewController` para apresentar uma exibição unificada de lançamento para redes sociais. Além disso, ele examinou o `SLRequest` classe que é usada para chamar API da cada rede social.


## <a name="related-links"></a>Links relacionados

- [SocialFrameworkDemo (sample)](https://developer.xamarin.com/samples/SocialFrameworkDemo/)
- [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)
