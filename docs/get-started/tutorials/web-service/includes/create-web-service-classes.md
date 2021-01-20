---
ms.openlocfilehash: 7a5acca4169b1f763e178e0a05b01548765ff45d
ms.sourcegitcommit: 4d260b655cb52b990dda79c239a9721f2e964625
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570747"
---
Solicitações REST são feitas por HTTP usando os mesmos verbos HTTP que os navegadores da Web usam para recuperar páginas e para enviar dados para os servidores. Neste exercício, você criará uma classe que usa o verbo GET para recuperar os dados do repositório .NET da API Web do GitHub.

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `Constants` ao projeto. Em seguida, em **Constants.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string GitHubReposEndpoint = "https://api.github.com/orgs/dotnet/repos";
        }
    }
    ```

    Esse código define uma única constante – o ponto de extremidade em relação ao qual as solicitações da Web são feitas.

1. No **Gerenciador de Soluções**, no projeto **WebServicesTutorial**, adicione uma nova classe chamada `Repository` ao projeto. Em seguida, em **Repository.cs**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```csharp
    using System;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class Repository
        {
            [JsonProperty("name")]
            public string Name { get; set; }

            [JsonProperty("description")]
            public string Description { get; set; }

            [JsonProperty("html_url")]
            public Uri GitHubHomeUrl { get; set; }

            [JsonProperty("homepage")]
            public Uri Homepage { get; set; }

            [JsonProperty("watchers")]
            public int Watchers { get; set; }
        }
    }
    ```

    Esse código define uma classe `Repository` usada para modelar os dados JSON recuperados do serviço Web. Cada propriedade é decorada com um atributo `JsonProperty`, que contém um nome de campo JSON. Newtonsoft.Json usará esse mapeamento de nomes de campos JSON para propriedades CLR ao desserializar os dados JSON em objetos de modelo.

    > [!NOTE]
    > A definição de classe acima foi simplificada e não modela totalmente os dados JSON recuperados do serviço Web.

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `RestService` ao projeto. Em seguida, em **RestService.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<List<Repository>> GetRepositoriesAsync(string uri)
            {
                List<Repository> repositories = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        repositories = JsonConvert.DeserializeObject<List<Repository>>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return repositories;
            }
        }
    }
    ```

    Esse código define um único método, `GetRepositoriesAsync`, que recupera os dados do repositório .NET da API Web do GitHub. Esse método usa o método `HttpClient.GetAsync` para enviar uma solicitação GET à API Web especificada pelo argumento `uri`. A API Web envia uma resposta que é armazenada em um objeto `HttpResponseMessage`. A resposta inclui um código de status HTTP que indica se a solicitação HTTP foi bem-sucedida ou falhou. Se a solicitação for bem-sucedida, a API Web responderá com o código de status HTTP 200 (OK) e uma resposta JSON, que está na propriedade `HttpResponseMessage.Content`. Esses dados JSON são lidos para um `string` usando o método `HttpContent.ReadAsStringAsync` antes de serem desserializados em um objeto `List<Repository>` usando o método `JsonConvert.DeserializeObject`. Esse método usa os mapeamentos entre os nomes de campos JSON e as propriedades CLR, definidos na classe `Repository`, para executar a desserialização.

1. Compile a solução para garantir que não haja erros.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `Constants` ao projeto. Em seguida, em **Constants.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string GitHubReposEndpoint = "https://api.github.com/orgs/dotnet/repos";
        }
    }
    ```

    Esse código define uma única constante – o ponto de extremidade em relação ao qual as solicitações da Web são feitas.

1. No **Painel de Soluções**, no projeto **WebServicesTutorial**, adicione uma nova classe chamada `Repository` ao projeto. Em seguida, em **Repository.cs**, remova todo o código do modelo e substitua-o pelo seguinte código:

    ```csharp
    using System;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class Repository
        {
            [JsonProperty("name")]
            public string Name { get; set; }

            [JsonProperty("description")]
            public string Description { get; set; }

            [JsonProperty("html_url")]
            public Uri GitHubHomeUrl { get; set; }

            [JsonProperty("homepage")]
            public Uri Homepage { get; set; }

            [JsonProperty("watchers")]
            public int Watchers { get; set; }
        }
    }
    ```

    Esse código define uma classe `Repository` usada para modelar os dados JSON recuperados do serviço Web. Cada propriedade é decorada com um atributo `JsonProperty`, que contém um nome de campo JSON. Newtonsoft.Json usará esse mapeamento de nomes de campos JSON para propriedades CLR ao desserializar os dados JSON em objetos de modelo.

    > [!NOTE]
    > A definição de classe acima foi simplificada e não modela totalmente os dados JSON recuperados do serviço Web.

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `RestService` ao projeto. Em seguida, em **RestService.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class RestService
        {
            HttpClient _client;

            public RestService()
            {
                _client = new HttpClient();
            }

            public async Task<List<Repository>> GetRepositoriesAsync(string uri)
            {
                List<Repository> repositories = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        repositories = JsonConvert.DeserializeObject<List<Repository>>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return repositories;
            }
        }
    }
    ```

    Esse código define um único método, `GetRepositoriesAsync`, que recupera os dados do repositório .NET da API Web do GitHub. Esse método usa o método `HttpClient.GetAsync` para enviar uma solicitação GET à API Web especificada pelo argumento `uri`. A API Web envia uma resposta que é armazenada em um objeto `HttpResponseMessage`. A resposta inclui um código de status HTTP que indica se a solicitação HTTP foi bem-sucedida ou falhou. Se a solicitação for bem-sucedida, a API Web responderá com o código de status HTTP 200 (OK) e uma resposta JSON, que está na propriedade `HttpResponseMessage.Content`. Esses dados JSON são lidos para um `string` usando o método `HttpContent.ReadAsStringAsync` antes de serem desserializados em um objeto `List<Repository>` usando o método `JsonConvert.DeserializeObject`. Esse método usa os mapeamentos entre os nomes de campos JSON e as propriedades CLR, definidos na classe `Repository`, para executar a desserialização.

1. Compile a solução para garantir que não haja erros.
