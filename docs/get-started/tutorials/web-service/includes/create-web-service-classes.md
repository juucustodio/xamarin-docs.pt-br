---
ms.openlocfilehash: 3b1603b6af5ebb5558c3cd764f41fdbe24351b9b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "68669522"
---
Solicitações REST são feitas por HTTP usando os mesmos verbos HTTP que os navegadores da Web usam para recuperar páginas e para enviar dados para os servidores. Neste exercício, você criará uma classe que usa o verbo GET para recuperar dados da API Web [OpenWeatherMap](https://openweathermap.org/). Essa API Web pode ser usada para recuperar dados de previsão do tempo para uma localização especificada. Para usar essa API Web, é necessário inscrever-se para receber uma chave de API.

> [!div class="nextstepaction"]
> [Inscrever-se para receber chave de API](https://home.openweathermap.org/users/sign_up)

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `Constants` ao projeto. Em seguida, em **Constants.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public const string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public const string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Este código define duas constantes. A constante `OpenWeatherMapEndpoint` define o ponto de extremidade ao qual as solicitações da Web serão feitas e a constante `OpenWeatherMapAPIKey` define sua chave de API pessoal para o serviço [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > Você precisa definir sua chave de API pessoal de OpenWeatherMap como o valor da constante `OpenWeatherMapAPIKey`.

1. No **Gerenciador de Soluções**, no projeto **WebServicesTutorial**, adicione uma nova classe chamada `WeatherData` ao projeto. Em seguida, em **WeatherData.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Este código define quatro classes usadas para modelar os dados JSON recuperados do serviço Web. Cada propriedade é decorada com um atributo `JsonProperty`, que contém um nome de campo JSON. Newtonsoft.Json usará esse mapeamento de nomes de campos JSON para propriedades CLR ao desserializar os dados JSON em objetos de modelo.

    > [!NOTE]
    > As definições de classe acima foram simplificadas e não modelam totalmente os dados JSON recuperados do serviço Web. Para ver um exemplo de modelo de dados completo, confira a amostra [Weather App](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/).

1. No **Gerenciador de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `RestService` ao projeto. Em seguida, em **RestService.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
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

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Esse código define um único método, `GetWeatherDataAsync`, que recupera dados de clima referentes a uma localização especificada da API Web [OpenWeatherMap](https://openweathermap.org/). Esse método usa o método `HttpClient.GetAsync` para enviar uma solicitação GET à API Web especificada pelo argumento `uri`. A API Web envia uma resposta que é armazenada em um objeto `HttpResponseMessage`. A resposta inclui um código de status HTTP que indica se a solicitação HTTP foi bem-sucedida ou falhou. Se a solicitação for bem-sucedida, a API Web responderá com o código de status HTTP 200 (OK) e uma resposta JSON, que está na propriedade `HttpResponseMessage.Content`. Esses dados JSON são lidos para um `string` usando o método `HttpContent.ReadAsStringAsync` antes de serem desserializados em um objeto `WeatherData` usando o método `JsonConvert.DeserializeObject`. Esse método usa os mapeamentos entre os nomes de campos JSON e as propriedades CLR, definidos na classe `WeatherData`, para executar a desserialização.

1. Compile a solução para garantir que não haja erros.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `Constants` ao projeto. Em seguida, em **Constants.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    namespace WebServiceTutorial
    {
        public static class Constants
        {
            public static string OpenWeatherMapEndpoint = "https://api.openweathermap.org/data/2.5/weather";
            public static string OpenWeatherMapAPIKey = "INSERT_API_KEY_HERE";
        }
    }
    ```

    Este código define duas constantes. A constante `OpenWeatherMapEndpoint` define o ponto de extremidade ao qual as solicitações da Web serão feitas e a constante `OpenWeatherMapAPIKey` define sua chave de API pessoal para o serviço [OpenWeatherMap](https://openweathermap.org/).

    > [!IMPORTANT]
    > Você precisa definir sua chave de API pessoal de OpenWeatherMap como o valor da constante `OpenWeatherMapAPIKey`.

1. No **Painel de Soluções**, no projeto **WebServicesTutorial**, adicione uma nova classe chamada `WeatherData` ao projeto. Em seguida, em **WeatherData.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using Newtonsoft.Json;

    namespace WebServiceTutorial
    {
        public class WeatherData
        {
            [JsonProperty("name")]
            public string Title { get; set; }

            [JsonProperty("weather")]
            public Weather[] Weather { get; set; }

            [JsonProperty("main")]
            public Main Main { get; set; }

            [JsonProperty("visibility")]
            public long Visibility { get; set; }

            [JsonProperty("wind")]
            public Wind Wind { get; set; }
        }

        public class Main
        {
            [JsonProperty("temp")]
            public double Temperature { get; set; }

            [JsonProperty("humidity")]
            public long Humidity { get; set; }
        }

        public class Weather
        {
            [JsonProperty("main")]
            public string Visibility { get; set; }
        }

        public class Wind
        {
            [JsonProperty("speed")]
            public double Speed { get; set; }
        }
    }
    ```

    Este código define quatro classes usadas para modelar os dados JSON recuperados do serviço Web. Cada propriedade é decorada com um atributo `JsonProperty`, que contém um nome de campo JSON. Newtonsoft.Json usará esse mapeamento de nomes de campos JSON para propriedades CLR ao desserializar os dados JSON em objetos de modelo.

    > [!NOTE]
    > As definições de classe acima foram simplificadas e não modelam totalmente os dados JSON recuperados do serviço Web. Para ver um exemplo de modelo de dados completo, confira a amostra [Weather App](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/weather/).

1. No **Painel de Soluções**, no projeto **WebServiceTutorial**, adicione uma nova classe chamada `RestService` ao projeto. Em seguida, em **RestService.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
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

            public async Task<WeatherData> GetWeatherDataAsync(string uri)
            {
                WeatherData weatherData = null;
                try
                {
                    HttpResponseMessage response = await _client.GetAsync(uri);
                    if (response.IsSuccessStatusCode)
                    {
                        string content = await response.Content.ReadAsStringAsync();
                        weatherData = JsonConvert.DeserializeObject<WeatherData>(content);
                    }
                }
                catch (Exception ex)
                {
                    Debug.WriteLine("\tERROR {0}", ex.Message);
                }

                return weatherData;
            }
        }
    }
    ```

    Esse código define um único método, `GetWeatherDataAsync`, que recupera dados de clima referentes a uma localização especificada da API Web [OpenWeatherMap](https://openweathermap.org/). Esse método usa o método `HttpClient.GetAsync` para enviar uma solicitação GET à API Web especificada pelo argumento `uri`. A API Web envia uma resposta que é armazenada em um objeto `HttpResponseMessage`. A resposta inclui um código de status HTTP que indica se a solicitação HTTP foi bem-sucedida ou falhou. Se a solicitação for bem-sucedida, a API Web responderá com o código de status HTTP 200 (OK) e uma resposta JSON, que está na propriedade `HttpResponseMessage.Content`. Esses dados JSON são lidos para um `string` usando o método `HttpContent.ReadAsStringAsync` antes de serem desserializados em um objeto `WeatherData` usando o método `JsonConvert.DeserializeObject`. Esse método usa os mapeamentos entre os nomes de campos JSON e as propriedades CLR, definidos na classe `WeatherData`, para executar a desserialização.

1. Compile a solução para garantir que não haja erros.
