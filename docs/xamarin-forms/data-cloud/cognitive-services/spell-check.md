---
title: Verificação ortográfica usando a API de verificação ortográfica do Bing
description: Verificação ortográfica do Bing executa ortográfica contextual para texto, fornecendo sugestões embutido para palavras incorretas. Este artigo explica como usar a API de REST de verificação ortográfica de Bing para corrigir erros de ortografia em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 08ac86674e4f10d6bd17d765de2bcdf7c2d3f901
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061753"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Verificação ortográfica usando a API de verificação ortográfica do Bing

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_Verificação ortográfica do Bing executa ortográfica contextual para texto, fornecendo sugestões embutido para palavras incorretas. Este artigo explica como usar a API de REST de verificação ortográfica de Bing para corrigir erros de ortografia em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

A API de REST de verificação ortográfica de Bing tem dois modos de operação e um modo deve ser especificado ao fazer uma solicitação para a API:

- `Spell` corrige o texto curto (palavras até 9) sem quaisquer alterações de maiusculas e minúsculas.
- `Proof` corrige o texto longo, fornece correções de maiusculas e minúsculas e pontuação básica e suprime correções agressivas.

Uma chave de API deve ser obtida para usar a API de verificação de ortografia do Bing. Isso pode ser obtido no [Experimente os serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/)

Para obter uma lista dos idiomas com suporte a API de verificação de ortografia do Bing, consulte [idiomas com suporte](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Para obter mais informações sobre a API de verificação de ortografia do Bing, consulte [documentação de verificação ortográfica de Bing](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API de verificação de ortografia do Bing requer uma chave de API que deve ser especificada como o valor da `Ocp-Apim-Subscription-Key` cabeçalho. O exemplo de código a seguir mostra como adicionar a chave de API para o `Ocp-Apim-Subscription-Key` cabeçalho de uma solicitação:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Falha ao passar uma chave de API válida para a API de verificação de ortografia do Bing resultará em um erro de 401 resposta.

## <a name="performing-spell-checking"></a>Executar a verificação ortográfica

Verificação ortográfica pode ser obtida por meio de uma solicitação GET ou POST para o `SpellCheck` API em `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. Quando você faz uma solicitação GET, o texto a ser ortografia verificada é enviado como um parâmetro de consulta. Quando você faz uma solicitação POST, o texto a ser ortografia verificada é enviado no corpo da solicitação. As solicitações GET são limitadas a caracteres de 1500 devido à limitação de comprimento da cadeia de caracteres de parâmetro consulta de verificação ortográfica. Portanto, as solicitações POST normalmente devem ser feitas, a menos que as cadeias de caracteres curtas estão sendo ortografia verificada.

No aplicativo de exemplo, o `SpellCheckTextAsync` método invoca o processo de verificação ortográfica:

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

O `SpellCheckTextAsync` método gera um URI de solicitação e, em seguida, envia a solicitação para o `SpellCheck` API, que retorna uma resposta JSON que contém o resultado. A resposta JSON é desserializada, com o resultado retornado para o método de chamada para exibição.

### <a name="configuring-spell-checking"></a>Configurar a verificação ortográfica

O processo de verificação ortográfica pode ser configurado especificando parâmetros de consulta HTTP:

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Esse método define o texto a ser ortografia verificada e o modo de verificação ortográfica.

Para obter mais informações sobre a API de REST de verificação ortográfica de Bing, consulte [referência do API de verificação ortográfica v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz a solicitação GET para a API de REST de verificação ortográfica de Bing e retorna a resposta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Esse método envia a solicitação GET para o `SpellCheck` API, com a URL da solicitação especifica o texto a ser traduzido e o modo de verificação ortográfica. A resposta é, em seguida, ler e retornada para o método de chamada.

O `SpellCheck` API enviará o código de status HTTP 200 (Okey) em resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de objetos de resposta, consulte [os objetos de resposta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Processar a resposta

A resposta da API é retornada em formato JSON. Os dados JSON a seguir mostram a mensagem de resposta para o texto digitado incorretamente `Go shappin tommorow`:

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

O `flaggedTokens` matriz contém uma matriz de palavras no texto que foram marcados como não sendo escrito corretamente ou são gramaticalmente incorreto. A matriz será vazia se nenhum ortografia ou erros gramaticais forem encontrados. As marcas dentro da matriz são:

- `offset` – um deslocamento de base zero desde o início da cadeia de caracteres de texto para a palavra que foi marcado.
- `token` – a palavra na cadeia de texto que não está escrita corretamente ou está gramaticalmente incorreto.
- `type` – o tipo de erro que causou a palavra a ser sinalizado. Há dois valores possíveis – `RepeatedToken` e `UnknownToken`.
- `suggestions` – uma matriz de palavras que corrigirá o erro de ortografia ou gramática. A matriz é composta de um `suggestion` e um `score`, que indica o nível de confiança de que a correção sugerida é correta.

No aplicativo de exemplo, a resposta JSON é desserializada em um `SpellCheckResult` instância, com o resultado retornado para o método de chamada para exibição. O seguinte exemplo de código mostra como o `SpellCheckResult` instância é processada para exibição:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Esse código itera por meio de `FlaggedTokens` coleta e substitui qualquer incorretas ou gramaticalmente incorretas palavras no texto de origem com a primeira sugestão. As capturas de tela a seguir mostram antes e após a verificação ortográfica:

![](spell-check-images/before-spell-check.png "Antes de verificação ortográfica")

![](spell-check-images/after-spell-check.png "Após a verificação ortográfica")

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API de REST de verificação ortográfica de Bing para corrigir erros de ortografia em um aplicativo xamarin. Forms. Verificação ortográfica do Bing executa ortográfica contextual para texto, fornecendo sugestões embutido para palavras incorretas.

## <a name="related-links"></a>Links relacionados

- [Documentação de verificação ortográfica do Bing](/azure/cognitive-services/bing-spell-check/)
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Serviços Cognitivos de tarefas pendentes (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Referência do API de verificação ortográfica do Bing v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
