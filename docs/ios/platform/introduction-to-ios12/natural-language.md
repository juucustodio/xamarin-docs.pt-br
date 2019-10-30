---
title: Usando a estrutura de linguagem natural com o Xamarin. iOS
description: Este documento descreve a estrutura de linguagem natural. Introduzido no iOS 12, a estrutura de linguagem natural é a API do iOS preferencial a ser usada para reconhecimento de idioma, partes da identificação de fala e reconhecimento de entidade nomeada.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/20/2018
ms.openlocfilehash: 1598bad7bdbea8334b7fdfa2b950400b698579b0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031990"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Usando a estrutura de linguagem natural com o Xamarin. iOS

Introduzido no iOS 12, a estrutura de linguagem natural habilita o processamento de idioma natural no dispositivo. Ele dá suporte ao reconhecimento de idioma, à geração de tokens e à marcação. A geração de tokens divide o texto em suas palavras de componentes, frases ou parágrafos; a marcação identifica partes de fala, pessoas, lugares e organizações.

A estrutura de linguagem natural também pode usar modelos de ML de núcleo personalizados para classificar e marcar texto em contextos especializados.

A classe [NSLinguisticTagger](xref:Foundation.NSLinguisticTagger) ainda está disponível. No entanto, a estrutura de linguagem natural é o mecanismo preferencial a ser usado para processamento de idioma natural.

## <a name="sample-app-xamarinnl"></a>Aplicativo de exemplo: XamarinNL

Para saber como usar a estrutura de linguagem natural com o Xamarin. iOS, dê uma olhada no [aplicativo de exemplo XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl).
Este aplicativo de exemplo demonstra como usar a estrutura de idioma natural para:

- [Reconheça os idiomas](#recognizing-languages).
- [Indexe o texto em palavras e sentenças](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Marque entidades nomeadas e partes de fala](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Reconhecendo idiomas

A guia **Recognizer** do aplicativo de exemplo demonstra como usar um [`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
para determinar o idioma de um bloco de texto.

> [!NOTE]
> Reconhecimento de idioma é um tipo específico de classificação de texto. A estrutura de linguagem natural também dá suporte à classificação de texto personalizada por meio de modelos de ML de núcleo fornecidos pelo desenvolvedor. Para obter mais informações, veja a sessão [introdução ao Framework natural language](https://developer.apple.com/videos/play/wwdc2018/713/) do WWDC 2018.

### <a name="dominant-language"></a>Linguagem dominante

Toque no botão **idioma** para identificar o idioma dominante na entrada do usuário.

O método `HandleDetermineLanguageButtonTap` da `LanguageRecognizerViewController` usa o [`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
método de um `NLLanguageRecognizer` para buscar o [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
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

Toque no botão **probabilidades de idioma** para buscar uma lista de informações de idioma para a entrada do usuário.

O método `HandleLanguageProbabilitiesButtonTap` da classe `LanguageRecognizerViewController` instancia uma `NLLanguageRecognizer` e solicita que ela [`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
o texto do usuário. Em seguida, ele chama o [`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*) do reconhecedor de idioma
método, que busca um dicionário de idiomas e probabilidades associadas. Em seguida, a classe `LanguageRecognizerTableViewController` renderiza essas linguagens e probabilidades.

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

Os valores possíveis de `NLLanguage` incluem:

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

Uma lista completa de idiomas com suporte está disponível como parte do [`NLLanguage`](xref:NaturalLanguage.NLLanguage)
documentação da API de enumeração.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Geração de tokens de texto em palavras, frases e parágrafos

A guia **criador** do aplicativo de exemplo demonstra como separar um bloco de texto em suas palavras ou sentenças de componente com um [`NLTokenizer`](xref:NaturalLanguage.NLTokenizer).

Toque no botão **palavras** ou **sentenças** para buscar uma lista de tokens. Cada token é associado a uma palavra ou frase no texto original.

`ShowTokens` divide a entrada do usuário em tokens chamando o [`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
método de um `NLTokenizer`. Esse método retorna uma matriz de [`NSValue`](xref:Foundation.NSValue)
os objetos, cada um encapsulando um valor `NSRange` correspondente a um token no texto original.

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

`LanguageTokenizerTableViewController` renderiza um único token em cada célula da tabela. Ele extrai uma `NSRange` de um token `NSValue`, localiza a cadeia de caracteres correspondente no texto original e define um rótulo na célula de exibição de tabela:

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Marcando entidades nomeadas e partes de fala

A guia de **marcação** do aplicativo de exemplo XamarinNL demonstra como usar o [`NLTagger`](xref:NaturalLanguage.NLTagger)
classe para associar categorias a tokens de uma cadeia de caracteres de entrada.
A estrutura de linguagem natural inclui suporte interno para reconhecer pessoas, lugares, organizações e partes de fala.

> [!NOTE]
> A estrutura de linguagem natural também dá suporte a esquemas de marcação personalizados por meio de modelos de ML de núcleo fornecidos pelo desenvolvedor. Para obter mais informações, veja a sessão [introdução ao Framework natural language](https://developer.apple.com/videos/play/wwdc2018/713/) do WWDC 2018.

Toque no botão **entidades nomeadas** ou **partes de fala** para buscar:

- Uma matriz de objetos `NSValue`, cada um encapsulando um `NSRange` para um token no texto original.
- Uma matriz de valores de [`NLTag`](xref:NaturalLanguage.NLTag) – categorias para os tokens de `NSValue` no mesmo índice de matriz.

Em `LanguageTaggerViewController`, `HandlePartsOfSpeechButtonTap` e `HandleNamedEntitiesButtonTap` cada chamada `ShowTags`, passando um [`NLTagScheme`](xref:NaturalLanguage.NLTagScheme) – `NLTagScheme.LexicalClass` (para partes de fala) ou `NLTagScheme.NameType` (para entidades nomeadas).

`ShowTags` cria um `NLTagger`, instanciando-o com uma matriz de tipos de `NLTagScheme` para os quais ele será consultado (nesse caso, somente o valor de `NLTagScheme` passado). Em seguida, ele usa o [`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
no `NLTagger` para determinar as marcas relevantes para o texto na entrada do usuário.

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

As marcas são exibidas em uma tabela pelo `LanguageTaggerTableViewController`.

Os valores possíveis de `NLTag` incluem:

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

Uma lista completa de marcas com suporte está disponível como parte do [`NLTag`](xref:NaturalLanguage.NLTag)
documentação da API de enumeração.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)
- [Introdução à estrutura de linguagem natural](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Linguagem natural (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
