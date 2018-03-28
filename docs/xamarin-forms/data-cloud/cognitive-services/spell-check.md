---
title: Verificação ortográfica usando a verificação de ortografia API do Bing
description: De verificação ortográfica Bing executa ortográfica contextual para texto, fornecendo embutido sugestões de ortografia. Este artigo explica como usar o API de REST do Bing ortográfica verificar para corrigir erros de ortografia em um aplicativo xamarin. Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 420eea4622d9c90c3587899fb24e707524990b19
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Verificação ortográfica usando a verificação de ortografia API do Bing

_De verificação ortográfica Bing executa ortográfica contextual para texto, fornecendo embutido sugestões de ortografia. Este artigo explica como usar o API de REST do Bing ortográfica verificar para corrigir erros de ortografia em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

O API de REST do Bing ortográfica verificar tem dois modos de operação e um modo deve ser especificado ao fazer uma solicitação para a API:

- `Spell` corrige texto curto (palavras até 9) sem quaisquer alterações de maiusculas e minúsculas.
- `Proof` corrige texto longo, fornece correções de maiusculas e minúsculas e pontuação básica e suprime correções agressivas.

Uma chave de API deve ser obtida para usar a API do Bing ortográfica verificar. Isso pode ser obtido no [tente serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/)

Para obter uma lista dos idiomas com suporte a API do Bing ortográfica verificar, consulte [idiomas com suporte](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Para obter mais informações sobre a API do Bing ortográfica verificar, consulte [Bing ortográfica verificar documentação](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Autenticação

Todas as solicitações feitas para a API do Bing ortográfica verificação requer uma chave de API deve ser especificada como o valor da `Ocp-Apim-Subscription-Key` cabeçalho. O exemplo de código a seguir mostra como adicionar a chave de API para o `Ocp-Apim-Subscription-Key` cabeçalho de uma solicitação:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Falha ao passar uma chave de API válida para a API do Bing ortográfica verificar resultará em um erro de 401 resposta.

## <a name="performing-spell-checking"></a>Executar verificação de ortografia

A verificação ortográfica pode ser obtida fazendo uma solicitação GET ou POST para o `SpellCheck` API em `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. Ao fazer uma solicitação GET, o texto a ser ortográfica check é enviado como um parâmetro de consulta. Ao fazer uma solicitação POST, o texto a ser ortográfica check é enviado no corpo da solicitação. As solicitações GET são limitadas a 1500 caracteres devido à limitação de comprimento de cadeia de caracteres de parâmetro de consulta de verificação ortográfica. Portanto, as solicitações POST normalmente devem ser feitas, a menos que cadeias de caracteres curtas estão sendo ortográfica marcada.

O aplicativo de exemplo, o `SpellCheckTextAsync` método invoca o processo de verificação ortográfica:

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

### <a name="configuring-spell-checking"></a>Configurando verificação ortográfica

O processo de verificação ortográfica pode ser configurado com a especificação de parâmetros de consulta HTTP:

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Esse método define o texto a ser ortográfica verificada e o modo de verificação ortográfica.

Para obter mais informações sobre a API do Bing ortográfica verificar REST, consulte [referência à API de verificação de ortografia v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Enviar a solicitação

O `SendRequestAsync` método faz com que a solicitação GET para a API de REST de verificação ortográfica de Bing e retorna a resposta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Este método cria a solicitação de obtenção, adicionando a chave de API como o valor de `Ocp-Apim-Subscription-Key` cabeçalho. A solicitação GET é enviada para o `SpellCheck` API, com a URL da solicitação especifica o texto a ser traduzido e o modo de verificação ortográfica. A resposta é, em seguida, ler e retornada para o método de chamada.

O `SpellCheck` API enviará o código de status HTTP 200 (Okey) na resposta, fornecida que a solicitação é válida, que indica que a solicitação foi bem-sucedida e se as informações solicitadas estão na resposta. Para obter uma lista de objetos de resposta, consulte [objetos de resposta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Processamento da resposta

A resposta de API é retornada no formato JSON. Os dados JSON a seguir mostram a mensagem de resposta para o texto digitado incorretamente `Go shappin tommorow`:

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

O `flaggedTokens` matriz contém uma matriz de palavras no texto que foi sinalizado como não sendo digitado corretamente ou são gramaticalmente incorreto. A matriz estará vazia se nenhum ortografia ou erros gramaticais forem encontrados. As marcas dentro da matriz são:

- `offset` – um deslocamento com base em zero do início da cadeia de caracteres de texto para a palavra que foi marcado.
- `token` – a palavra na cadeia de texto que não foi digitada corretamente ou está gramaticalmente incorreto.
- `type` – o tipo de erro que fez a palavra ser sinalizada. Há dois valores possíveis – `RepeatedToken` e `UnknownToken`.
- `suggestions` – uma matriz de palavras que corrigirá o erro de ortografia e gramática. A matriz é composta de uma `suggestion` e um `score`, que indica o nível de confiança de que a correção sugerida é correta.

O aplicativo de exemplo, a resposta JSON é desserializada em um `SpellCheckResult` instância, com o resultado retornado para o método de chamada para exibição. O seguinte exemplo de código mostra como o `SpellCheckResult` instância é processada para exibição:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Esse código itera por meio de `FlaggedTokens` coleta e substitui qualquer incorretas ou palavras gramaticalmente incorretas no texto de origem com a primeira sugestão. As seguintes capturas de tela mostram antes e após a verificação ortográfica:

![](spell-check-images/before-spell-check.png "Antes de verificação ortográfica")

![](spell-check-images/after-spell-check.png "Após a verificação ortográfica")

## <a name="summary"></a>Resumo

Este artigo explicou como usar o API de REST do Bing ortográfica verificar para corrigir erros de ortografia em um aplicativo xamarin. Forms. De verificação ortográfica Bing executa ortográfica contextual para texto, fornecendo embutido sugestões de ortografia.

## <a name="related-links"></a>Links relacionados

- [Verifique a ortografia Bing documentação](/azure/cognitive-services/bing-spell-check/)
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Serviços Cognitivos de tarefas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Referência do API do Bing ortográfica verificar v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
