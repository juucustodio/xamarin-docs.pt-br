---
title: Usando a estrutura de linguagem Natural com xamarin. IOS
description: Este documento descreve a estrutura de linguagem Natural. Introduzido no iOS 12, a estrutura de linguagem Natural é a API do iOS preferencial a ser usado para o reconhecimento de idioma, identificação de partes da fala e reconhecimento de entidade nomeada.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/20/2018
ms.openlocfilehash: 0b3fb7d467ae64e2cbfdb61644b1537bc5ae1161
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233062"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Usando a estrutura de linguagem Natural com xamarin. IOS

Introduzido no iOS 12, a estrutura de linguagem Natural permite processamento em idioma natural no dispositivo. Ele dá suporte a reconhecimento de idioma, geração de tokens e marcação. Geração de tokens divide o texto em suas palavras de componente, frases ou parágrafos; marcação identifica gramaticais, pessoas, lugares e organizações.

A estrutura de linguagem Natural também pode usar modelos personalizados do Core ML para classificar e marcar o texto em contextos especializados.

O [NSLinguisticTagger](xref:Foundation.NSLinguisticTagger) classe ainda está disponível. No entanto, a estrutura de linguagem Natural é o mecanismo preferido a ser usado para processamento de idioma Natural.

## <a name="sample-app-xamarinnl"></a>Aplicativo de exemplo: XamarinNL

Para saber como usar a estrutura de linguagem Natural com xamarin. IOS, dê uma olhada a [aplicativo de exemplo XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL).
Este aplicativo de exemplo demonstra como usar a estrutura de linguagem Natural para:

- [Reconhecer idiomas](#recognizing-languages).
- [Crie tokens de texto em palavras e frases](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Marca denominada entidades e classes gramaticais](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Reconhecendo os idiomas

O **reconhecedor** guia do aplicativo de exemplo demonstra como usar um [`NLLanguageRecognizer`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguageRecognizer/)
para determinar o idioma de um bloco de texto.

> [!NOTE]
> Reconhecimento de idioma é um tipo específico de classificação de texto. A estrutura de linguagem Natural também dá suporte à classificação de texto personalizado por meio de modelos de ML Core fornecida pelo desenvolvedor. Para obter mais informações, examine os [apresentando o Framework de linguagem Natural](https://developer.apple.com/videos/play/wwdc2018/713/) sessão de WWDC 2018.

### <a name="dominant-language"></a>Idioma dominante

Toque o **linguagem** botão para identificar a linguagem dominante na entrada do usuário.

O `HandleDetermineLanguageButtonTap` método da `LanguageRecognizerViewController` usa o [`GetDominantLanguage`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage/)
método de um `NLLanguageRecognizer` para buscar o [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
para o idioma principal encontrado no texto:

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>Probabilidades de idioma

Toque o **probabilidades de linguagem** botão para obter uma lista de hipóteses de idioma para a entrada do usuário.

O `HandleLanguageProbabilitiesButtonTap` método do `LanguageRecognizerViewController` classe instancia um `NLLanguageRecognizer` e pede para [`Process`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.Process/)
texto do usuário. Depois, ele chama o reconhecedor de idioma [`GetNativeLanguageHypotheses`](https://developer.xamarin.com/api/member/NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses)
método, que busca um dicionário de idiomas e as probabilidades associadas. O `LanguageRecognizerTableViewController` classe, em seguida, processa essas linguagens e as probabilidades.

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

Potencial `NLLanguage` valores incluem:

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

Uma lista completa dos idiomas com suporte está disponível como parte das [`NLLanguage`](https://developer.xamarin.com/api/type/NaturalLanguage.NLLanguage/)
documentação de API do Enum.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Gerar tokens para texto em palavras, frases e parágrafos

O **Tokenizer** guia do aplicativo de exemplo demonstra como separar um bloco de texto em palavras seu componente ou frases com um [ `NLTokenizer` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTokenizer/).

Toque o **palavras** ou **sentenças** botão para buscar uma lista de tokens. Cada token é associado uma palavra ou frase do texto original.

`ShowTokens` divide a entrada do usuário em tokens, chamando o [`GetTokens`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTokenizer.GetTokens/)
método de um `NLTokenizer`. Esse método retorna uma matriz de [`NSValue`](xref:Foundation.NSValue)
objetos, cada quebra automática de um `NSRange` valor correspondente a um token no texto original.

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController` renderiza um único token em cada célula de tabela. Ele extrai um `NSRange` de um token `NSValue`, localiza a cadeia de caracteres correspondente no texto original e define um rótulo na célula de exibição de tabela:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Marcação de entidades nomeadas e partes da fala

O **marcador** guia o XamarinNL do aplicativo de exemplo demonstra como usar o [`NLTagger`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagger/)
classe para associar as categorias com tokens de uma cadeia de caracteres de entrada.
A estrutura de linguagem Natural inclui suporte interno para reconhecer pessoas, lugares, organizações e partes da fala.

> [!NOTE]
> A estrutura de linguagem Natural também dá suporte a esquemas de marcação personalizadas por meio de modelos de ML Core fornecida pelo desenvolvedor. Para obter mais informações, examine os [apresentando o Framework de linguagem Natural](https://developer.apple.com/videos/play/wwdc2018/713/) sessão de WWDC 2018.

Toque o **entidades nomeadas** ou **gramaticais** botão de busca:

- Uma matriz de `NSValue` objetos, cada quebra automática de um `NSRange` para um token no texto original.
- Uma matriz de [ `NLTag` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/) valores – categorias para o `NSValue` tokens no mesmo índice de matriz.

Na `LanguageTaggerViewController`, `HandlePartsOfSpeechButtonTap` e `HandleNamedEntitiesButtonTap` cada chamada `ShowTags`, passando ao longo de um [ `NLTagScheme` ](https://developer.xamarin.com/api/type/NaturalLanguage.NLTagScheme/) – qualquer um dos `NLTagScheme.LexicalClass` (para partes da fala) ou `NLTagScheme.NameType` (para entidades nomeadas).

`ShowTags` cria um `NLTagger`, criando uma instância com uma matriz de `NLTagScheme` entre os tipos de que ele será consultado (nesse caso, apenas passada do `NLTagScheme` valor). Ele usa o [`GetTags`](https://developer.xamarin.com/api/member/NaturalLanguage.NLTagger.GetTags/)
método sobre o `NLTagger` para determinar as marcas relevantes para o texto na entrada do usuário.

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

As marcas são exibidas em uma tabela, o `LanguageTaggerTableViewController`.

Potencial `NLTag` valores incluem:

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

Uma lista completa de marcas com suporte está disponível como parte das [`NLTag`](https://developer.xamarin.com/api/type/NaturalLanguage.NLTag/)
documentação de API do Enum.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – XamarinNL](https://developer.xamarin.com/samples/monotouch/iOS12/XamarinNL)
- [Introdução ao Framework de linguagem Natural](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Linguagem natural (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
