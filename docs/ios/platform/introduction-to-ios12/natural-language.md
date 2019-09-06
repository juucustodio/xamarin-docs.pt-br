---
title: Usando a estrutura de linguagem natural com o Xamarin. iOS
description: Este documento descreve a estrutura de linguagem natural. Introduzido no iOS 12, a estrutura de linguagem natural é a API do iOS preferencial a ser usada para reconhecimento de idioma, partes da identificação de fala e reconhecimento de entidade nomeada.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/20/2018
ms.openlocfilehash: 7b0d07f2153a0395146506a371631e0bcf75ebef
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292921"
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

A guia **Recognizer** do aplicativo de exemplo demonstra como usar um[`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
para determinar o idioma de um bloco de texto.

> [!NOTE]
> Reconhecimento de idioma é um tipo específico de classificação de texto. A estrutura de linguagem natural também dá suporte à classificação de texto personalizada por meio de modelos de ML de núcleo fornecidos pelo desenvolvedor. Para obter mais informações, veja a sessão [introdução ao Framework natural language](https://developer.apple.com/videos/play/wwdc2018/713/) do WWDC 2018.

### <a name="dominant-language"></a>Linguagem dominante

Toque no botão **idioma** para identificar o idioma dominante na entrada do usuário.

O `HandleDetermineLanguageButtonTap` método`LanguageRecognizerViewController` do usa o[`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
método de um `NLLanguageRecognizer` para buscar o[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
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

O `HandleLanguageProbabilitiesButtonTap` método `NLLanguageRecognizer` da classe instancia um e solicita que ele `LanguageRecognizerViewController`[`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
o texto do usuário. Em seguida, ele chama o reconhecedor de idioma[`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
método, que busca um dicionário de idiomas e probabilidades associadas. Em `LanguageRecognizerTableViewController` seguida, a classe renderiza essas linguagens e probabilidades.

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

Os `NLLanguage` valores possíveis incluem:

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

Uma lista completa dos idiomas com suporte está disponível como parte do[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
documentação da API de enumeração.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Geração de tokens de texto em palavras, frases e parágrafos

A guia **criador** do aplicativo de exemplo demonstra como separar um bloco de texto em suas palavras ou sentenças de componente com [`NLTokenizer`](xref:NaturalLanguage.NLTokenizer)um.

Toque no botão **palavras** ou **sentenças** para buscar uma lista de tokens. Cada token é associado a uma palavra ou frase no texto original.

`ShowTokens`divide a entrada do usuário em tokens chamando o[`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
método de um `NLTokenizer`. Esse método retorna uma matriz de[`NSValue`](xref:Foundation.NSValue)
, cada um encapsulando `NSRange` um valor correspondente a um token no texto original.

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

`LanguageTokenizerTableViewController`renderiza um único token em cada célula da tabela. Ele extrai um `NSRange` de um token `NSValue`, localiza a cadeia de caracteres correspondente no texto original e define um rótulo na célula de exibição de tabela:

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

A guia de **marcação** do aplicativo de exemplo XamarinNL demonstra como usar o[`NLTagger`](xref:NaturalLanguage.NLTagger)
classe para associar categorias a tokens de uma cadeia de caracteres de entrada.
A estrutura de linguagem natural inclui suporte interno para reconhecer pessoas, lugares, organizações e partes de fala.

> [!NOTE]
> A estrutura de linguagem natural também dá suporte a esquemas de marcação personalizados por meio de modelos de ML de núcleo fornecidos pelo desenvolvedor. Para obter mais informações, veja a sessão [introdução ao Framework natural language](https://developer.apple.com/videos/play/wwdc2018/713/) do WWDC 2018.

Toque no botão **entidades nomeadas** ou **partes de fala** para buscar:

- Uma matriz de `NSValue` objetos, cada um encapsulando um `NSRange` para um token no texto original.
- Uma matriz de [`NLTag`](xref:NaturalLanguage.NLTag) valores – categorias para os `NSValue` tokens no mesmo índice de matriz.

Em `LanguageTaggerViewController`, `HandlePartsOfSpeechButtonTap` e `HandleNamedEntitiesButtonTap` `NLTagScheme.NameType` cada chamada ,`ShowTags`passando um [`NLTagScheme`](xref:NaturalLanguage.NLTagScheme) – (paraaspartesdefala)ou(paraentidadesnomeadas).`NLTagScheme.LexicalClass`

`ShowTags`Cria um `NLTagger`, instanciando-o com uma matriz `NLTagScheme` de tipos para os quais ele será consultado (nesse caso, `NLTagScheme` apenas o valor passado). Em seguida, ele usa o[`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
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

Os `NLTag` valores possíveis incluem:

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

Uma lista completa de marcas com suporte está disponível como parte do[`NLTag`](xref:NaturalLanguage.NLTag)
documentação da API de enumeração.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)
- [Introdução à estrutura de linguagem natural](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Linguagem natural (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
