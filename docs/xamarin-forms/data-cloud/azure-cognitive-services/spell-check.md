---
title: ''
description: Verificação Ortográfica do Bing executa a verificação ortográfica contextual para texto, fornecendo sugestões embutidas para palavras incorretas. Este artigo explica como usar a API REST do Verificação Ortográfica do Bing para corrigir erros de ortografia em um Xamarin.Forms aplicativo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1703f0049408381a86da73fb28696ef8708cc790
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139289"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Verificação ortográfica usando a API de Verificação Ortográfica do Bing

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Verificação Ortográfica do Bing executa a verificação ortográfica contextual para texto, fornecendo sugestões embutidas para palavras incorretas. Este artigo explica como usar a API REST do Verificação Ortográfica do Bing para corrigir erros de ortografia em um Xamarin.Forms aplicativo._

## <a name="overview"></a>Visão geral

A API REST do Verificação Ortográfica do Bing tem dois modos de operação, e um modo deve ser especificado ao fazer uma solicitação para a API:

- `Spell`corrige texto curto (até 9 palavras) sem nenhuma alteração de maiúsculas e minúsculas.
- `Proof`corrige texto longo, fornece correções de maiúsculas e minúsculas e pontuação básica e suprime as correções agressivas.

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

Uma chave de API deve ser obtida para usar a API Verificação Ortográfica do Bing. Isso pode ser obtido em [experimentar serviços cognitivas](https://azure.microsoft.com/try/cognitive-services/)

Para obter uma lista dos idiomas com suporte pela API de Verificação Ortográfica do Bing, consulte [idiomas com suporte](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Para obter mais informações sobre a API de Verificação Ortográfica do Bing, consulte [verificação ortográfica do Bing documentação](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Autenticação

Cada solicitação feita à API de Verificação Ortográfica do Bing requer uma chave de API que deve ser especificada como o valor do `Ocp-Apim-Subscription-Key` cabeçalho. O exemplo de código a seguir mostra como adicionar a chave de API ao `Ocp-Apim-Subscription-Key` cabeçalho de uma solicitação:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Falha ao passar uma chave de API válida para a API de Verificação Ortográfica do Bing resultará em um erro de resposta 401.

## <a name="performing-spell-checking"></a>Executando verificação ortográfica

A verificação ortográfica pode ser obtida fazendo uma solicitação GET ou POST para a `SpellCheck` API em `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck` . Ao fazer uma solicitação GET, o texto a ser verificado pela ortografia é enviado como um parâmetro de consulta. Ao fazer uma solicitação POST, o texto a ser verificado pela ortografia é enviado no corpo da solicitação. As solicitações GET são limitadas à verificação ortográfica de 1500 caracteres devido à limitação do comprimento da cadeia de caracteres do parâmetro de consulta. Portanto, as solicitações POST normalmente devem ser feitas, a menos que as cadeias de caracteres curtas estejam sendo verificadas com ortografia.

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

O `SpellCheckTextAsync` método gera um URI de solicitação e, em seguida, envia a solicitação para a `SpellCheck` API, que retorna uma resposta JSON que contém o resultado. A resposta JSON é desserializada, com o resultado sendo retornado para o método de chamada para exibição.

### <a name="configuring-spell-checking"></a>Configurando a verificação ortográfica

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

Esse método define o texto a ser marcado para verificação ortográfica e o modo de verificação ortográfica.

Para obter mais informações sobre a API REST do Verificação Ortográfica do Bing, consulte [referência do API de verificação ortográfica v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Enviando a solicitação

O `SendRequestAsync` método faz a solicitação get para a API REST do verificação ortográfica do Bing e retorna a resposta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Esse método envia a solicitação GET para a `SpellCheck` API, com a URL de solicitação especificando o texto a ser traduzido e o modo de verificação ortográfica. Em seguida, a resposta é lida e retornada ao método de chamada.

A `SpellCheck` API enviará o código de status HTTP 200 (OK) na resposta, desde que a solicitação seja válida, o que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta. Para obter uma lista de objetos de resposta, consulte [objetos de resposta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Processando a resposta

A resposta da API é retornada no formato JSON. Os dados JSON a seguir mostram a mensagem de resposta para o texto digitado incorretamente `Go shappin tommorow` :

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

A `flaggedTokens` matriz contém uma matriz de palavras no texto que foram sinalizadas como não sendo escritas corretamente ou que estão gramaticalmente incorretas. A matriz estará vazia se nenhum erro de ortografia ou gramática for encontrado. As marcas dentro da matriz são:

- `offset`– um deslocamento de base zero desde o início da cadeia de texto até a palavra que foi sinalizada.
- `token`– a palavra na cadeia de caracteres de texto que não está grafada corretamente ou está gramaticalmente incorreta.
- `type`– o tipo do erro que fez com que a palavra fosse sinalizada. Há dois valores possíveis – `RepeatedToken` e `UnknownToken` .
- `suggestions`– uma matriz de palavras que corrigirá o erro ortográfico ou gramatical. A matriz é composta por um `suggestion` e um `score` , o que indica o nível de confiança de que a correção sugerida está correta.

No aplicativo de exemplo, a resposta JSON é desserializada em uma `SpellCheckResult` instância, com o resultado sendo retornado para o método de chamada para exibição. O exemplo de código a seguir mostra como a `SpellCheckResult` instância é processada para exibição:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Esse código faz a iteração pela `FlaggedTokens` coleção e substitui palavras gramaticalmente incorretamente ou incorretas no texto de origem pela primeira sugestão. As capturas de tela a seguir mostram antes e depois da verificação ortográfica:

![](spell-check-images/before-spell-check.png "Before Spell Check")

![](spell-check-images/after-spell-check.png "After Spell Check")

> [!NOTE]
> O exemplo acima usa `Replace` para simplificar, mas em uma grande quantidade de texto, ele pode substituir o token errado. A API fornece o `offset` valor que deve ser usado em aplicativos de produção para identificar o local correto no texto de origem para executar uma atualização.

## <a name="summary"></a>Resumo

Este artigo explicou como usar a API REST do Verificação Ortográfica do Bing para corrigir erros de ortografia em um Xamarin.Forms aplicativo. Verificação Ortográfica do Bing executa a verificação ortográfica contextual para texto, fornecendo sugestões embutidas para palavras incorretas.

## <a name="related-links"></a>Links relacionados

- [Documentação do Verificação Ortográfica do Bing](/azure/cognitive-services/bing-spell-check/)
- [Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Serviços cognitivas todo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Referência da API v7 do Verificação Ortográfica do Bing](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
