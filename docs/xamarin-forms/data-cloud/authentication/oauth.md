---
title: "Autenticação de usuários com um provedor de identidade"
description: "Xamarin.Auth é um SDK de plataforma cruzada para autenticar usuários e armazenar suas contas. Ele inclui autenticadores de OAuth que oferecem suporte para o consumo de provedores de identidade, como Google, Microsoft, Facebook e Twitter. Este artigo explica como usar Xamarin.Auth para gerenciar o processo de autenticação em um aplicativo xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: ff0403fedf75ab22986f5fc83d16db3dbf8d92b6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="authenticating-users-with-an-identity-provider"></a>Autenticação de usuários com um provedor de identidade

_Xamarin.Auth é um SDK de plataforma cruzada para autenticar usuários e armazenar suas contas. Ele inclui autenticadores de OAuth que oferecem suporte para o consumo de provedores de identidade, como Google, Microsoft, Facebook e Twitter. Este artigo explica como usar Xamarin.Auth para gerenciar o processo de autenticação em um aplicativo xamarin. Forms._

OAuth é um padrão aberto para autenticação e permite que um proprietário do recurso notificar um provedor de recursos que deve receber permissão para um terceiro para acessar suas informações sem compartilhar a identidade de proprietários de recursos. Um exemplo disso seria habilitar um usuário notificar um provedor de identidade (como Google, Microsoft, Facebook ou Twitter) que deve receber permissão para um aplicativo para acessar seus dados, sem compartilhar a identidade do usuário. Ele costuma ser usado como uma abordagem para os usuários para entrar em sites e aplicativos que usam um provedor de identidade, mas sem expor sua senha para o site ou aplicativo.

Uma visão geral do fluxo de autenticação durante o consumo de um provedor de identidade OAuth é o seguinte:

1. O aplicativo navega um navegador para uma URL do provedor de identidade.
1. O provedor de identidade lida com a autenticação de usuário e retorna um código de autorização para o aplicativo.
1. O aplicativo troca o código de autorização para um token de acesso do provedor de identidade.
1. O aplicativo usa o token de acesso para acessar APIs no provedor de identidade, como uma API para solicitar dados de usuário básica.

O aplicativo de exemplo demonstra como usar Xamarin.Auth para implementar um fluxo de autenticação nativo no Google. Enquanto o Google é usado como o provedor de identidade neste tópico, a abordagem é igualmente aplicável para outros provedores de identidade. Para obter mais informações sobre autenticação usando o ponto de extremidade do Google OAuth 2.0, consulte [para OAuth 2.0 usando APIs do Google acesso](https://developers.google.com/identity/protocols/OAuth2) no site do Google.

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
> ATS pode ser incluído em se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Usando Xamarin.Auth para autenticar os usuários

Xamarin.Auth dá suporte a dois métodos para aplicativos a interagir com o ponto de extremidade de autorização do provedor de identidade:

1. Usando uma exibição da web interno. Embora isso tenha sido uma prática comum, não é recomendável pelos seguintes motivos:

    - O aplicativo que hospeda a exibição da web pode acessar as credenciais de autenticação completa do usuário, não apenas a concessão de autorização de OAuth que foi criada para o aplicativo. Isso viola o princípio de menos privilégios, como o aplicativo tem acesso a credenciais mais eficientes que o necessário, aumentar potencialmente a superfície de ataque do aplicativo.
    - O aplicativo host pode capturar os nomes de usuário e senhas, automaticamente enviar formulários e ignorar o consentimento do usuário e copiar os cookies de sessão e usá-los para executar ações autenticadas como o usuário.
    - Modos de exibição da web interno não compartilhem o estado de autenticação com outros aplicativos ou navegador da web do dispositivo, exigir que o usuário entrar para cada solicitação de autorização que é considerada uma experiência de usuário de qualidade inferior.
    - Alguns pontos de extremidade de autorização adotar medidas para detectar e bloquear solicitações de autorização que vêm de exibições da web.

1. Usando um navegador da web do dispositivo, o que é a abordagem recomendada. Usando o navegador de dispositivo para solicitações do OAuth melhora a usabilidade de um aplicativo, como os usuários só precisam entrar no provedor de identidade uma vez por dispositivo, melhorando as taxas de conversão de fluxos de entrada e autorização no aplicativo. O navegador de dispositivo também fornece maior segurança como aplicativos são capazes de inspecionar e modificar o conteúdo em uma exibição da web, mas não o conteúdo exibido no navegador. Essa é a abordagem usada neste aplicativo de exemplo e o artigo.

Uma visão geral de como o aplicativo de exemplo usa Xamarin.Auth para autenticar usuários e recuperar seus dados básicos é mostrada no diagrama a seguir:

![](oauth-images/google-auth.png "Usando Xamarin.Auth para autenticar com o Google")

O aplicativo faz uma solicitação de autenticação para o Google usando o `OAuth2Authenticator` classe. Uma resposta de autenticação é retornada quando o usuário foi autenticado com êxito com o Google por meio de sua página de entrada, que inclui um token de acesso. Em seguida, o aplicativo faz uma solicitação para o Google para dados de usuário básico, usando o `OAuth2Request` classe com o token de acesso sejam incluído na solicitação.

### <a name="setup"></a>Configuração

Um projeto de Console de API do Google deve ser criado para integrar Google entrar com um aplicativo xamarin. Forms. Isso pode ser feito da seguinte maneira:

1. Vá para o [Console de API do Google](http://console.developers.google.com) site e entre com as credenciais de conta do Google.
1. De projeto lista suspensa, selecione um projeto existente ou crie um novo.
1. Na barra lateral em "Gerenciador de API", selecione **credenciais**, em seguida, selecione o **guia de tela de consentimento de OAuth**. Escolha um **endereço de Email**, especifique um **nome do produto mostrado aos usuários**e pressione **salvar**.
1. No **credenciais** guia, selecione o **criar credenciais** suspensa lista e escolha **ID do cliente OAuth**.
1. Em **tipo de aplicativo**, selecione a plataforma que serão executados no aplicativo móvel (**iOS** ou **Android**).
1. Preencha os detalhes necessários e selecione o **criar** botão.

> [!NOTE]
> Uma ID de cliente permite que um aplicativo acessar APIs do Google habilitado e para aplicativos móveis é exclusiva para uma única plataforma. Portanto, um **ID do cliente OAuth** devem ser criados para cada plataforma que usará a entrada do Google.

Depois de executar essas etapas, Xamarin.Auth pode ser usado para iniciar um fluxo de autenticação OAuth2 com o Google.

### <a name="creating-and-configuring-an-authenticator"></a>Criando e configurando um autenticador

Do Xamarin.Auth `OAuth2Authenticator` classe é responsável por gerenciar o fluxo de autenticação OAuth. O exemplo de código a seguir mostra a instanciação do `OAuth2Authenticator` classe ao executar a autenticação usando um navegador da web do dispositivo:

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

O `OAuth2Authenticator` classe requer um número de parâmetros, que são da seguinte maneira:

- **ID do cliente** – Isso identifica o cliente que está fazendo a solicitação e pode ser recuperado do projeto no [Console de API do Google](http://console.developers.google.com).
- **Segredo do cliente** – isso deve ser `null` ou `string.Empty`.
- **Escopo** – Isso identifica o acesso de API que está sendo solicitado pelo aplicativo e o valor informa a tela de consentimento é mostrada ao usuário. Para obter mais informações sobre escopos, consulte [solicitação de autorização de API](https://developers.google.com/+/web/api/rest/oauth) no site do Google.
- **Autorizar URL** – Isso identifica a URL onde o código de autorização será obtido no.
- **URL de redirecionamento** – Isso identifica a URL em que a resposta será enviada. O valor desse parâmetro deve corresponder a um dos valores que aparece no **credenciais** guia para o projeto no [Console de desenvolvedores do Google](https://console.developers.google.com/).
- **Url de AccessToken** – Isso identifica a URL usada para solicitar tokens de acesso depois que um código de autorização é obtido.
- **GetUserNameAsync Func** – um opcional `Func` que será usado para recuperar assincronamente o nome de usuário da conta depois que ele é autenticado com êxito.
- **Usar a interface do usuário nativa** – um `boolean` valor que indica se deve usar o navegador da web do dispositivo para executar a solicitação de autenticação.

### <a name="setup-authentication-event-handlers"></a>Instalação de manipuladores de eventos de autenticação

Antes de apresentar a interface do usuário, um manipulador de eventos para o `OAuth2Authenticator.Completed` o evento deve ser registrado, conforme mostrado no exemplo de código a seguir:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Esse evento será acionado quando o usuário é autenticado com êxito ou cancela na entrada.

Opcionalmente, um manipulador de eventos para o `OAuth2Authenticator.Error` evento também pode ser registrado.

### <a name="presenting-the-sign-in-user-interface"></a>Apresentar a Interface do usuário de entrada

A interface do usuário de entrada pode ser apresentada ao usuário usando um apresentador de logon Xamarin.Auth, que deve ser inicializado em cada projeto da plataforma. O exemplo de código a seguir mostra como inicializar um apresentador de logon na `AppDelegate` classe no projeto do iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

O exemplo de código a seguir mostra como inicializar um apresentador de logon na `MainActivity` classe no projeto do Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

O projeto de biblioteca de classe portátil (PCL) pode invocar o apresentador de login da seguinte maneira:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Observe que o argumento para o `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` método é o `OAuth2Authenticator` instância. Quando o `Login` método é chamado, a interface do usuário de entrada é apresentada ao usuário em uma guia do navegador da web do dispositivo, o que é mostrado nas capturas de tela seguir:

![](oauth-images/login.png "Entrada do Google")

### <a name="processing-the-redirect-url"></a>Processar a URL de redirecionamento

Depois que o usuário concluir o processo de autenticação, controle retorna para o aplicativo a partir da guia do navegador da web. Isso é obtido registrando um esquema de URL personalizado para a URL de redirecionamento que é retornada o processo de autenticação e, em seguida, detectar e tratar a URL personalizada quando ele é enviado.

Ao escolher um esquema de URL personalizado para associar um aplicativo, os aplicativos devem usar um esquema baseado em um nome de seu controle. Isso pode ser feito usando o nome do identificador de pacote no iOS e o nome do pacote no Android e, em seguida, revertendo-las para que o esquema de URL. No entanto, alguns provedores de identidade, como Google, atribua os identificadores de cliente com base em nomes de domínio, que são revertidos e usados como o esquema de URL. Por exemplo, se o Google cria uma id de cliente `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, o esquema de URL será `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Observe que apenas um único `/` pode aparecer após o componente de esquema. Portanto, é um exemplo completo de uma URL de redirecionamento utilizando um esquema de URL personalizado `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Quando o navegador da web recebe uma resposta do provedor de identidade que contém um esquema de URL personalizado, ele tenta carregar a URL, que irá falhar. Em vez disso, o esquema de URL personalizado é relatado para o sistema operacional, gerando um evento. O sistema operacional, em seguida, verifica os esquemas registrados e se uma for encontrada, o sistema operacional iniciar o aplicativo que registrou o esquema e enviá-lo a URL de redirecionamento.

O mecanismo para registrar um esquema de URL personalizado com o sistema operacional e lidar com o esquema é específico para cada plataforma.

#### <a name="ios"></a>iOS

No iOS, um esquema de URL personalizado é registrado no **Info. plist**, conforme mostrado na seguinte captura de tela:

![](oauth-images/info-plist.png "Registro de esquema de URL")

O **identificador** valor pode ser qualquer coisa e o **função** valor deve ser definido como **visualizador**. O **esquemas de Url** valor, que começa com `com.googleusercontent.apps`, pode ser obtido da id do cliente do iOS para o projeto no [Console de API do Google](http://console.developers.google.com).

Quando o provedor de identidade conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento do aplicativo. Como a URL usa um esquema personalizado resulta na inicialização do aplicativo do iOS, passando a URL como um parâmetro de inicialização, onde ela é processada pelo `OpenUrl` substituir o aplicativo `AppDelegate` classe, que é mostrado no exemplo de código a seguir:

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

O `OpenUrl` método converte a URL recebida de um `NSUrl` para .NET `Uri`, antes de processar a URL de redirecionamento com o `OnPageLoading` método de um público `OAuth2Authenticator` objeto. Isso faz com que Xamarin.Auth para fechar a guia do navegador da web e analisar os dados recebidos do OAuth.

#### <a name="android"></a>Android

No Android, um esquema de URL personalizado está registrado, especificando um [ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) atributo no `Activity` que manipulará o esquema. Quando o provedor de identidade conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento do aplicativo. Como a URL usa um esquema personalizado resulta na inicialização do aplicativo do Android, passando a URL como um parâmetro de inicialização, onde ela é processada pelo `OnCreate` método o `Activity` registrado para manipular o esquema de URL personalizado. O exemplo de código a seguir mostra a classe do aplicativo de exemplo que trata o esquema de URL personalizado:

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

O `DataSchemes` propriedade o [ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) deve ser definido como o identificador de cliente revertida é obtido da id do cliente do Android para o projeto em [Console de API do Google](http://console.developers.google.com).

O `OnCreate` método converte a URL recebida de um `Android.Net.Url` para .NET `Uri`, antes de processar a URL de redirecionamento com o `OnPageLoading` método de um público `OAuth2Authenticator` objeto. Isso faz com que Xamarin.Auth fechar a guia do navegador da web e analisar os dados recebidos do OAuth.

> [!IMPORTANT]
> No Android, Xamarin.Auth usa o `CustomTabs` API para se comunicar com o navegador da web e o sistema operacional. No entanto, ele tem não garantia de que um `CustomTabs` navegador compatível será instalado no dispositivo do usuário.

### <a name="examining-the-oauth-response"></a>Examinando a resposta do OAuth

Depois de analisar os dados recebidos do OAuth, Xamarin.Auth irá gerar o `OAuth2Authenticator.Completed` evento. No manipulador de eventos para esse evento, o `AuthenticatorCompletedEventArgs.IsAuthenticated` propriedade pode ser usada para identificar se a autenticação bem-sucedida, conforme mostrado no exemplo de código a seguir:

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

Os dados coletados a partir de uma autenticação bem-sucedida estão disponíveis na `AuthenticatorCompletedEventArgs.Account` propriedade. Isso inclui um token de acesso, que pode ser usado para assinar solicitações de dados para uma API fornecida pelo provedor de identidade.

### <a name="making-requests-for-data"></a>Fazer solicitações de dados

Depois que o aplicativo obtém um token de acesso, ele é usado para fazer uma solicitação para o `https://www.googleapis.com/oauth2/v2/userinfo` API para solicitar dados de usuário básico do provedor de identidade. Essa solicitação é feita do Xamarin.Auth `OAuth2Request` classe que representa uma solicitação que é autenticada usando uma conta recuperada de um `OAuth2Authenticator` de instância, conforme mostrado no exemplo de código a seguir:

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

Bem como o método HTTP e a URL de API, o `OAuth2Request` instância também especifica um `Account` instância que contém o token de acesso que faz a solicitação para a URL especificada pelo `Constants.UserInfoUrl` propriedade. O provedor de identidade, em seguida, retorna os dados de usuário básico como uma resposta JSON, incluindo o nome do usuário e endereço de email, desde que ele reconheça o token de acesso como válido. A resposta JSON é lido e desserializada o `user` variável.

Para obter mais informações, consulte [chamar uma API do Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) no portal de desenvolvedores do Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Armazenar e recuperar informações de conta em dispositivos

Xamarin.Auth armazena com segurança `Account` objetos em uma conta de armazenam para que os aplicativos não é sempre necessário autenticar novamente os usuários. O `AccountStore` classe é responsável pelo armazenamento de informações da conta e tem o apoio de serviços de conjunto de chaves no iOS e o `KeyStore` classe no Android.

O seguinte exemplo de código mostra como uma `Account` objeto for salvo com segurança:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Contas salvas são identificadas exclusivamente usando uma chave composta da conta de `Username` propriedade e uma ID de serviço, que é uma cadeia de caracteres que é usada quando a busca de contas do repositório de conta. Se um `Account` salvo anteriormente, chamar o `Save` método novamente irá substituí-la.

`Account` objetos de um serviço pode ser recuperado chamando o `FindAccountsForService` método, conforme mostrado no exemplo de código a seguir:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

O `FindAccountsForService` método retorna um `IEnumerable` coleção de `Account` objetos, com o primeiro item na coleção que está sendo definido como a conta correspondente.

## <a name="summary"></a>Resumo

Este artigo explicou como usar Xamarin.Auth para gerenciar o processo de autenticação em um aplicativo xamarin. Forms. Xamarin.Auth fornece o `OAuth2Authenticator` e `OAuth2Request` classes que são usadas por aplicativos xamarin. Forms para consumir os provedores de identidade, como Google, Microsoft, Facebook e Twitter.


## <a name="related-links"></a>Links relacionados

- [OAuthNativeFlow (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [OAuth 2.0 para aplicativos nativos](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Usando o OAuth 2.0 para acessar as APIs do Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
