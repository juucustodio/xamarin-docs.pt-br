---
title: AuthenticateUsers com um provedor de identidade
description: Este artigo explica como usar auth para gerenciar o processo de autenticação em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 0fa433de7fd1acb6fb27741f1615a644315f373f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292174"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Autenticar usuários com um provedor de identidade

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_O Xamarin. auth é um SDK de plataforma cruzada para autenticar usuários e armazenar suas contas. Ele inclui autenticadores OAuth que fornecem suporte para o consumo de provedores de identidade, como Google, Microsoft, Facebook e Twitter. Este artigo explica como usar o Xamarin. auth para gerenciar o processo de autenticação em um aplicativo Xamarin. Forms._

OAuth é um padrão aberto para autenticação e permite que o proprietário do recurso notificar um provedor de recursos que a permissão deve ser concedida a um terceiro para acessar suas informações sem compartilhar a identidade de proprietários de recurso. Um exemplo disso seria ser habilitação de um usuário notificar um provedor de identidade (por exemplo, Google, Microsoft, Facebook ou Twitter) que deve ser concedida permissão a um aplicativo para acessar seus dados, sem compartilhar a identidade do usuário. Normalmente, ele é usado como uma abordagem para que os usuários para entrar para sites e aplicativos usando um provedor de identidade, mas sem expor sua senha para o site ou aplicativo.

Uma visão geral do fluxo de autenticação durante o consumo de um provedor de identidade OAuth é da seguinte maneira:

1. O aplicativo navega de um navegador para uma URL do provedor de identidade.
1. O provedor de identidade lida com a autenticação de usuário e retorna um código de autorização para o aplicativo.
1. O aplicativo troca o código de autorização para um token de acesso do provedor de identidade.
1. O aplicativo usa o token de acesso para acessar as APIs no provedor de identidade, como uma API para solicitar dados básicos do usuário.

O aplicativo de exemplo demonstra como usar auth para implementar um fluxo de autenticação nativa no Google. Enquanto o Google é usado como o provedor de identidade neste tópico, a abordagem é igualmente aplicável a outros provedores de identidade. Para obter mais informações sobre a autenticação usando o ponto de extremidade OAuth 2,0 do Google, consulte [usando o OAuth 2.0 para acessar as APIs do Google](https://developers.google.com/identity/protocols/OAuth2) no site do Google.

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
> O ATS pode ser recusado se não for possível usar o protocolo `HTTPS` e a comunicação segura para recursos da Internet. Isso pode ser feito atualizando o arquivo **info. plist** do aplicativo. Para obter mais informações, consulte [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Usando auth para autenticar usuários

AUTH dá suporte a duas abordagens para os aplicativos interagem com o ponto de extremidade de autorização do provedor de identidade:

1. Usando uma exibição da web incorporadas. Embora isso tenha sido uma prática comum, ele não é mais recomendado pelos seguintes motivos:

    - O aplicativo que hospeda a exibição da web pode acessar a credencial do usuário autenticação completa, não apenas a concessão de autorização do OAuth foi destinada para o aplicativo. Isso viola o princípio de privilégios mínimos, que o aplicativo tenha acesso a credenciais mais poderosas do que o necessário, possivelmente, aumentando a superfície de ataque do aplicativo.
    - O aplicativo host pode capturar os nomes de usuário e senhas, automaticamente enviar formulários e ignorar o consentimento do usuário e copiar os cookies de sessão e usá-los para executar ações autenticadas como o usuário.
    - Modos de exibição da web incorporadas não compartilham o estado de autenticação com outros aplicativos ou navegador da web do dispositivo, exigir que o usuário entrar para cada solicitação de autorização que é considerada uma experiência de usuário de qualidade inferior.
    - Alguns pontos de extremidade de autorização tomar medidas para detectar e bloquear solicitações de autorização que vêm de exibições da web.

1. Usando o navegador da web do dispositivo, que é a abordagem recomendada. Usar o navegador do dispositivo para solicitações do OAuth melhora a usabilidade de um aplicativo, como os usuários precisam apenas entrar para o provedor de identidade uma vez por dispositivo, melhorando as taxas de conversão de fluxos de entrada e autorização no aplicativo. O navegador do dispositivo também fornece segurança aprimorada como os aplicativos capazes de inspecionar e modificar o conteúdo em uma exibição da web, mas não os conteúdo exibido no navegador. Isso é a abordagem usada neste artigo e exemplo de aplicativo.

Uma visão geral de como o aplicativo de exemplo usa auth para autenticar usuários e recuperar seus dados básicos é mostrada no diagrama a seguir:

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

O aplicativo faz uma solicitação de autenticação para o Google usando a classe `OAuth2Authenticator`. Uma resposta de autenticação é retornada, depois que o usuário foi autenticado com êxito com o Google por meio de sua página de entrada, que inclui um token de acesso. Em seguida, o aplicativo faz uma solicitação ao Google para dados básicos do usuário, usando a classe `OAuth2Request`, com o token de acesso que está sendo incluído na solicitação.

### <a name="setup"></a>Instalação

Um projeto de Console de API do Google deve ser criado para integrar o logon do Google com um aplicativo xamarin. Forms. Isso pode ser feito da seguinte maneira:

1. Vá para o site do [console do Google API](https://console.developers.google.com) e entre com as credenciais da conta do Google.
1. Do projeto lista suspensa, selecione um projeto existente ou crie um novo.
1. Na barra lateral em "Gerenciador de API", selecione **credenciais**e, em seguida, selecione a **guia da tela de consentimento do OAuth**. Escolha um **endereço de email**, especifique o **nome do produto mostrado aos usuários**e pressione **salvar**.
1. Na guia **credenciais** , selecione a lista suspensa **criar credenciais** e escolha **ID do cliente OAuth**.
1. Em **tipo de aplicativo**, selecione a plataforma na qual o aplicativo móvel será executado (**Ios** ou **Android**).
1. Preencha os detalhes necessários e selecione o botão **criar** .

> [!NOTE]
> Uma ID de cliente permite que um aplicativo acessar Google APIs habilitadas e para aplicativos móveis é exclusiva para uma única plataforma. Portanto, uma **ID de cliente OAuth** deve ser criada para cada plataforma que usará o logon do Google.

Depois de executar essas etapas, AUTH pode ser usado para iniciar um fluxo de autenticação OAuth2 com o Google.

### <a name="creating-and-configuring-an-authenticator"></a>Criando e configurando um autenticador

A classe `OAuth2Authenticator` do Xamarin. auth é responsável por manipular o fluxo de autenticação OAuth. O exemplo de código a seguir mostra a instanciação da classe `OAuth2Authenticator` ao executar a autenticação usando o navegador da Web do dispositivo:

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

A classe `OAuth2Authenticator` requer vários parâmetros, que são os seguintes:

- **ID do cliente** – identifica o cliente que está fazendo a solicitação e pode ser recuperado do projeto no [console de API do Google](https://console.developers.google.com).
- **Segredo do cliente** – deve ser `null` ou `string.Empty`.
- **Escopo** – identifica o acesso à API que está sendo solicitado pelo aplicativo e o valor informa a tela de consentimento que é mostrada ao usuário. Para obter mais informações sobre escopos, consulte [autorizar solicitações](https://developers.google.com/docs/api/how-tos/authorizing) no site do Google.
- **Autorizar URL** – identifica a URL para a qual o código de autorização será obtido.
- **URL de redirecionamento** – identifica a URL em que a resposta será enviada. O valor desse parâmetro deve corresponder a um dos valores que aparece na guia **credenciais** do projeto no console de desenvolvedores do [Google](https://console.developers.google.com/).
- **URL AccessToken** – identifica a URL usada para solicitar tokens de acesso depois que um código de autorização é obtido.
- **GetUserNameAsync Func** – um `Func` opcional que será usado para recuperar de forma assíncrona o nome de usuário da conta depois que ela for autenticada com êxito.
- **Usar interface do usuário nativa** – um valor `boolean` indicando se o navegador da Web do dispositivo deve ser usado para executar a solicitação de autenticação.

### <a name="setup-authentication-event-handlers"></a>Configurar manipuladores de eventos de autenticação

Antes de apresentar a interface do usuário, um manipulador de eventos para o evento `OAuth2Authenticator.Completed` deve ser registrado, conforme mostrado no exemplo de código a seguir:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Esse evento será acionado quando o usuário é autenticado com êxito ou cancela na entrada.

Opcionalmente, um manipulador de eventos para o evento `OAuth2Authenticator.Error` também pode ser registrado.

### <a name="presenting-the-sign-in-user-interface"></a>Apresentando a Interface do usuário de entrada

A interface do usuário de entrada pode ser apresentada ao usuário por meio de um apresentador de logon AUTH, que deve ser inicializado em cada projeto de plataforma. O exemplo de código a seguir mostra como inicializar um apresentador de logon na classe `AppDelegate` no projeto do iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

O exemplo de código a seguir mostra como inicializar um apresentador de logon na classe `MainActivity` no projeto do Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

O projeto de biblioteca .NET Standard pode, em seguida, invoque o apresentador de logon da seguinte maneira:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Observe que o argumento para o método `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` é a instância `OAuth2Authenticator`. Quando o método de `Login` é invocado, a interface do usuário de entrada é apresentada ao usuário em uma guia do navegador da Web do dispositivo, que é mostrada nas seguintes capturas de tela:

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Processar a URL de redirecionamento

Depois que o usuário concluir o processo de autenticação, o controle retornará ao aplicativo na guia navegador da Web. Isso é feito registrando um esquema de URL personalizado para a URL de redirecionamento retornada do processo de autenticação e, em seguida, detectando e manipulando a URL personalizada depois de enviada.

Ao escolher um esquema de URL personalizado a ser associado a um aplicativo, os aplicativos devem usar um esquema com base em um nome de seu controle. Isso pode ser feito usando o nome do identificador de pacote no iOS e o nome do pacote no Android e, em seguida, revertendo-las para tornar o esquema de URL. No entanto, alguns provedores de identidade, como Google, atribua os identificadores de cliente com base em nomes de domínio, que, em seguida, são revertidos e usados como o esquema de URL. Por exemplo, se o Google criar uma ID de cliente de `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, o esquema de URL será `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Observe que apenas um único `/` pode aparecer após o componente de esquema. Portanto, um exemplo completo de uma URL de redirecionamento utilizando um esquema de URL personalizado é `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Quando o navegador da web recebe uma resposta do provedor de identidade que contém um esquema de URL personalizado, ele tenta carregar a URL, que falhará. Em vez disso, o esquema de URL personalizado é relatado para o sistema operacional, gerando um evento. O sistema operacional, em seguida, verifica para esquemas registradas e, caso seja encontrado, o sistema operacional inicie o aplicativo que registrou o esquema e enviá-lo a URL de redirecionamento.

O mecanismo para registrar um esquema de URL personalizado com o sistema operacional e lidar com o esquema é específico para cada plataforma.

#### <a name="ios"></a>iOS

No iOS, um esquema de URL personalizado é registrado em **info. plist**, conforme mostrado na seguinte captura de tela:

![](oauth-images/info-plist.png "URL Scheme Registration")

O valor do **identificador** pode ser qualquer coisa e o valor da **função** deve ser definido como **Visualizador**. O valor de **esquemas de URL** , que começa com `com.googleusercontent.apps`, pode ser obtido da ID do cliente do IOS para o projeto no console de [API do Google](https://console.developers.google.com).

Quando o provedor de identidade conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento do aplicativo. Como a URL usa um esquema personalizado, ele resulta no iOS iniciando o aplicativo, passando a URL como um parâmetro de inicialização, onde ele é processado pela substituição de `OpenUrl` da classe `AppDelegate` do aplicativo, que é mostrada no exemplo de código a seguir:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

O método `OpenUrl` converte a URL recebida de um `NSUrl` em um `Uri`.NET, antes de processar a URL de redirecionamento com o método `OnPageLoading` de um objeto de `OAuth2Authenticator` público. Isso faz com que auth para fechar a guia do navegador da web e analisar os dados recebidos do OAuth.

#### <a name="android"></a>Android

No Android, um esquema de URL personalizado é registrado especificando um atributo [`IntentFilter`](xref:Android.App.IntentFilterAttribute) no `Activity` que manipulará o esquema. Quando o provedor de identidade conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento do aplicativo. Como a URL usa um esquema personalizado, ele resulta no Android iniciando o aplicativo, passando a URL como um parâmetro de inicialização, onde ele é processado pelo método `OnCreate` do `Activity` registrado para lidar com o esquema de URL personalizado. O exemplo de código a seguir mostra a classe do aplicativo de exemplo que manipula o esquema de URL personalizado:

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

A propriedade `DataSchemes` da [`IntentFilter`](xref:Android.App.IntentFilterAttribute) deve ser definida como o identificador de cliente invertido que é obtido da ID do cliente Android para o projeto no [console de API do Google](https://console.developers.google.com).

O método `OnCreate` converte a URL recebida de um `Android.Net.Url` em um `Uri`.NET, antes de processar a URL de redirecionamento com o método `OnPageLoading` de um objeto de `OAuth2Authenticator` público. Isso faz com que AUTH fechar a guia do navegador da web e analisar os dados recebidos do OAuth.

> [!IMPORTANT]
> No Android, o Xamarin. auth usa a API `CustomTabs` para se comunicar com o navegador da Web e o sistema operacional. No entanto, não há garantia de que um navegador compatível `CustomTabs` será instalado no dispositivo do usuário.

### <a name="examining-the-oauth-response"></a>Examinar a resposta do OAuth

Depois de analisar os dados do OAuth recebidos, o Xamarin. auth irá gerar o evento `OAuth2Authenticator.Completed`. No manipulador de eventos para esse evento, a propriedade `AuthenticatorCompletedEventArgs.IsAuthenticated` pode ser usada para identificar se a autenticação foi bem-sucedida, conforme mostrado no exemplo de código a seguir:

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

Os dados coletados de uma autenticação bem-sucedida estão disponíveis na propriedade `AuthenticatorCompletedEventArgs.Account`. Isso inclui um token de acesso, que pode ser usado para assinar as solicitações de dados para uma API fornecida pelo provedor de identidade.

### <a name="making-requests-for-data"></a>Fazer solicitações para dados

Depois que o aplicativo obtém um token de acesso, ele é usado para fazer uma solicitação para a API de `https://www.googleapis.com/oauth2/v2/userinfo`, para solicitar dados básicos do usuário do provedor de identidade. Essa solicitação é feita com a classe `OAuth2Request` do Xamarin. auth, que representa uma solicitação que é autenticada usando uma conta recuperada de uma instância `OAuth2Authenticator`, conforme mostrado no exemplo de código a seguir:

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

Assim como o método HTTP e a URL da API, a `OAuth2Request` instância também especifica uma instância `Account` que contém o token de acesso que assina a solicitação para a URL especificada pela propriedade `Constants.UserInfoUrl`. O provedor de identidade, em seguida, retorna dados básicos do usuário como uma resposta JSON, incluindo o nome dos usuários e o endereço de email, desde que ele reconhece o token de acesso como válido. Em seguida, a resposta JSON é lida e desserializada na variável `user`.

Para obter mais informações, consulte [chamando uma API do Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) no portal de desenvolvedores do Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Armazenar e recuperar informações de conta em dispositivos

O Xamarin. auth armazena com segurança `Account` objetos em um repositório de conta para que os aplicativos nem sempre precisem autenticar os usuários novamente. A classe `AccountStore` é responsável pelo armazenamento de informações da conta e é apoiada pelos serviços do conjunto de chaves no iOS e pela classe `KeyStore` no Android.

> [!IMPORTANT]
> A classe `AccountStore` no Xamarin. auth foi preterida e a classe do Xamarin. Essentials `SecureStorage` deve ser usada em seu lugar. Para obter mais informações, consulte [migrando de AccountStore para Xamarin. Essentials SecureStorage](https://github.com/xamarin/Xamarin.Auth/wiki/Migrating-from-AccountStore-to-Xamarin.Essentials-SecureStorage).

O exemplo de código a seguir mostra como um objeto de `Account` é salvo com segurança:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

As contas salvas são identificadas exclusivamente usando uma chave composta pela propriedade `Username` da conta e uma ID de serviço, que é uma cadeia de caracteres que é usada ao buscar contas do repositório de contas. Se um `Account` tiver sido salvo anteriormente, chamar o método de `Save` novamente irá substituí-lo.

`Account` objetos para um serviço podem ser recuperados chamando o método `FindAccountsForService`, conforme mostrado no exemplo de código a seguir:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

O método `FindAccountsForService` retorna uma coleção `IEnumerable` de objetos `Account`, com o primeiro item na coleção que está sendo definida como a conta correspondente.

## <a name="troubleshooting"></a>solução de problemas

- No Android, se você receber uma notificação do sistema quando fechar o navegador após a autenticação e quiser interromper a notificação do sistema, adicione o seguinte código ao projeto do Android depois de inicializar o Xamarin. auth:

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- No Android, se o navegador não fechar automaticamente, uma solução temporária será fazer o downgrade do pacote Xamarin. auth para a versão 1.5.0.3. Em seguida, adicione o [2.0.147 de criptografia PCL v](https://www.nuget.org/packages/PCLCrypto/2.0.147) ao projeto do Android.

## <a name="summary"></a>Resumo

Este artigo explicou como usar auth para gerenciar o processo de autenticação em um aplicativo xamarin. Forms. O Xamarin. auth fornece as classes `OAuth2Authenticator` e `OAuth2Request` que são usadas por aplicativos Xamarin. Forms para consumir provedores de identidade, como Google, Microsoft, Facebook e Twitter.

## <a name="related-links"></a>Links relacionados

- [OAuthNativeFlow (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2,0 para aplicativos nativos](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Usando o OAuth 2.0 para acessar as APIs do Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin. auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin. auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
