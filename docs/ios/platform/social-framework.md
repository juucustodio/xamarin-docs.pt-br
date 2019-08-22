---
title: Estrutura social no Xamarin. iOS
description: A estrutura social fornece uma API unificada para interagir com redes sociais, incluindo o Twitter e o Facebook, bem como SinaWeibo para usuários na China.
ms.prod: xamarin
ms.assetid: A1C28E66-AA20-1C13-23AF-5A8712E6C752
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: b95c68b43e4c7fda4d60f6976ab626968800d3dc
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889805"
---
# <a name="social-framework-in-xamarinios"></a>Estrutura social no Xamarin. iOS

_A estrutura social fornece uma API unificada para interagir com redes sociais, incluindo o Twitter e o Facebook, bem como SinaWeibo para usuários na China._

O uso da estrutura social permite que os aplicativos interajam com redes sociais de uma única API sem a necessidade de gerenciar a autenticação. Ele inclui um controlador de exibição fornecido pelo sistema para compor postagens, bem como uma abstração que permite consumir a API de cada rede social sobre HTTP.

> [!IMPORTANT]
> Para uma API de plataforma cruzada para se conectar a várias redes sociais, consulte o componente [xamarin. social](http://components.xamarin.com/view/xamarin.social/) na loja de componentes do xamarin.

## <a name="connecting-to-twitter"></a>Conectando-se ao Twitter

### <a name="twitter-account-settings"></a>Configurações de conta do Twitter

Para se conectar ao Twitter usando a estrutura social, uma conta precisa ser configurada nas configurações do dispositivo, conforme mostrado abaixo:

 [![](social-framework-images/twitter01.png "Configurações de conta do Twitter")](social-framework-images/twitter01.png#lightbox)

Depois que uma conta tiver sido inserida e verificada com o Twitter, qualquer aplicativo no dispositivo que usa as classes da estrutura social para acessar o Twitter usará essa conta.

### <a name="sending-tweets"></a>Enviando tweets

A estrutura social inclui um controlador chamado `SLComposeViewController` que apresenta uma exibição fornecida pelo sistema para edição e envio de um tweet. A captura de tela a seguir mostra um exemplo dessa exibição:

 [![](social-framework-images/twitter02.png "Esta captura de tela mostra um exemplo de SLComposeViewController")](social-framework-images/twitter02.png#lightbox)

Para usar um `SLComposeViewController` com o Twitter, uma instância do controlador deve ser criada chamando o `FromService` método com `SLServiceType.Twitter` conforme mostrado abaixo:

```csharp
var slComposer = SLComposeViewController.FromService (SLServiceType.Twitter);
```

Depois que `SLComposeViewController` a instância é retornada, ela pode ser usada para apresentar uma interface do usuário a ser postada no Twitter. No entanto, a primeira coisa a fazer é verificar a disponibilidade da rede social, o Twitter, neste caso, chamando `IsAvailable`:

```csharp
if (SLComposeViewController.IsAvailable (SLServiceKind.Twitter)) {
  ...
}
```

 `SLComposeViewController`nunca envia um tweet diretamente sem interação com o usuário. No entanto, ele pode ser inicializado com os seguintes métodos:

- `SetInitialText`– Adiciona o texto inicial para mostrar no tweet. 
- `AddUrl`– Adiciona uma URL ao tweet.
- `AddImage`– Adiciona uma imagem ao tweet.


Depois de inicializado `PresentVIewController` , a chamada exibe o modo `SLComposeViewController`de exibição criado pelo. O usuário pode, opcionalmente, editar e enviar o tweet ou cancelar o envio. Em ambos os `CompletionHandler`casos, o controlador deve ser descartado no, em que o resultado também pode ser verificado para ver se o tweet foi enviado ou cancelado, como mostrado abaixo:

```csharp
slComposer.CompletionHandler += (result) => {
  InvokeOnMainThread (() => {
    DismissViewController (true, null);
    resultsTextView.Text = result.ToString ();
  });
};
```

#### <a name="tweet-example"></a>Exemplo de tweet

O código a seguir demonstra como `SLComposeViewController` usar o para apresentar uma exibição usada para enviar um tweet:

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

### <a name="calling-twitter-api"></a>Chamando a API do Twitter

A estrutura social também inclui suporte para fazer solicitações HTTP para redes sociais. Ele encapsula a solicitação em uma `SLRequest` classe que é usada para direcionar a API da rede social específica.

Por exemplo, o código a seguir faz uma solicitação ao Twitter para obter a linha do tempo público (expandindo o código fornecido acima):

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

Vamos examinar esse código detalhadamente. Primeiro, ele obtém acesso ao repositório de conta e Obtém o tipo de uma conta do Twitter:

```csharp
var accountStore = new ACAccountStore ();
var accountType = accountStore.FindAccountType (ACAccountType.Twitter);
```

Em seguida, ele pergunta ao usuário se seu aplicativo pode ter acesso à sua conta do Twitter e, se o acesso for concedido, a conta é carregada na memória e a interface do usuário é atualizada:

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

Quando o usuário solicita os dados da linha do tempo (tocando em um botão na interface do usuário), o aplicativo primeiro forma uma solicitação para acessar os dados do Twitter:

```csharp
// Initialize request
var parameters = new NSDictionary ();
var url = new NSUrl("https://api.twitter.com/1.1/statuses/user_timeline.json?count=10");
var request = SLRequest.Create (SLServiceKind.Twitter, SLRequestMethod.Get, url, parameters);
```

Este exemplo limita os resultados retornados às últimas dez entradas, incluindo `?count=10` na URL. Por fim, ele anexa a solicitação à conta do Twitter (que foi carregada acima) e executa a chamada para o Twitter para buscar os dados:

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

Se os dados tiverem sido carregados com êxito, os dados JSON brutos serão exibidos (como na saída de exemplo abaixo):

[![](social-framework-images/twitter03.png "Um exemplo da exibição de dados JSON brutos")](social-framework-images/twitter03.png#lightbox)

Em um aplicativo real, os resultados JSON podem ser analisados como normais e os resultados apresentados ao usuário. Confira [introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md) para obter informações sobre como analisar o JSON.

## <a name="connecting-to-facebook"></a>Conectando ao Facebook

### <a name="facebook-account-settings"></a>Configurações de conta do Facebook

Conectar-se ao Facebook com a estrutura social é praticamente idêntico ao processo usado para o Twitter mostrado acima. Uma conta de usuário do Facebook deve ser definida nas configurações do dispositivo, conforme mostrado abaixo:

[![](social-framework-images/facebook01.png "Configurações de conta do Facebook")](social-framework-images/facebook01.png#lightbox)

Uma vez configurado, qualquer aplicativo no dispositivo que usa a estrutura social usará essa conta para se conectar ao Facebook.

### <a name="posting-to-facebook"></a>Lançamento no Facebook

Como a estrutura social é uma API unificada projetada para acessar várias redes sociais, o código permanece quase idêntico, independentemente da rede social que está sendo usada.

Por exemplo, o `SLComposeViewController` pode ser usado exatamente como no exemplo do Twitter mostrado anteriormente, o único é alternar para as configurações e opções específicas do Facebook. Por exemplo:

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

Quando usado com o Facebook, `SLComposeViewController` exibe uma exibição que parece quase idêntica ao exemplo do Twitter, mostrando o **Facebook** como o título nesse caso:

[![](social-framework-images/facebook02.png "A exibição SLComposeViewController")](social-framework-images/facebook02.png#lightbox)

### <a name="calling-facebook-graph-api"></a>Chamando API do Graph do Facebook

Semelhante ao exemplo do Twitter, o objeto da `SLRequest` estrutura social pode ser usado com a API do Graph do Facebook. Por exemplo, o código a seguir retorna informações da API do Graph sobre a conta do Xamarin (expandindo o código fornecido acima):

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

A única diferença real entre esse código e a versão do Twitter apresentada acima é o requisito do Facebook para obter uma ID específica de desenvolvedor/aplicativo (que pode ser gerada no portal do desenvolvedor do Facebook) que deve ser definida como uma opção ao fazer a solicitação:

```csharp
var options = new AccountStoreOptions ();
var options.FacebookAppId = ""; // Enter your specific Facebook App ID here
...

// Request access to Facebook account
accountStore.RequestAccess (accountType, options, (granted, error) => {
    ...
});
```

A falha ao definir essa opção (ou usar uma chave inválida) resultará em um erro ou nenhum dado será retornado.

## <a name="summary"></a>Resumo

Este artigo mostrou como usar a estrutura social para interagir com o Twitter e o Facebook. Ele mostrou onde configurar contas para cada rede social nas configurações do dispositivo. Ele também abordou como usar o `SLComposeViewController` para apresentar uma exibição unificada para lançamento em redes sociais. Além disso, ele examinou a classe usada para chamar a `SLRequest` API de cada rede social.


## <a name="related-links"></a>Links relacionados

- [SocialFrameworkDemo (sample)](https://docs.microsoft.com/samples/xamarin/ios-samples/socialframeworkdemo)
- [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)
