---
title: Fala do Android
description: Este artigo aborda os conceitos básicos do uso do poderoso namespace Android. Speech. Desde seu início, o Android foi capaz de reconhecer a fala e a saída como texto. É um processo relativamente simples. Para conversão de texto em fala, no entanto, o processo é mais envolvido, pois não apenas o mecanismo de fala precisa ser levado em conta, mas também os idiomas disponíveis e instalados do sistema de conversão de texto em fala (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019761"
---
# <a name="android-speech"></a>Fala do Android

_Este artigo aborda os conceitos básicos do uso do poderoso namespace Android. Speech. Desde seu início, o Android foi capaz de reconhecer a fala e a saída como texto. É um processo relativamente simples. Para conversão de texto em fala, no entanto, o processo é mais envolvido, pois não apenas o mecanismo de fala precisa ser levado em conta, mas também os idiomas disponíveis e instalados do sistema de conversão de texto em fala (TTS)._

## <a name="speech-overview"></a>Visão geral de fala

Ter um sistema, que "entende" a fala humana e enunciates o que está sendo digitado — a fala em texto e a Conversão de Texto em Fala — é uma área em constante crescimento no desenvolvimento móvel, pois a demanda por comunicação natural com nossos dispositivos aumenta. Há muitas instâncias em que ter um recurso que converte texto em fala, ou vice-versa, é uma ferramenta muito útil para incorporar seu aplicativo Android.

Por exemplo, com o fixe para baixo no uso do telefone celular durante a condução, os usuários desejam uma maneira gratuita de operar seus dispositivos. A grande quantidade de diferentes fatores forma do Android, como o desgaste do Android, e a inclusão cada vez maior das pessoas capazes de usar dispositivos Android (como tablets e pads de anotações), criou um foco grande em excelentes aplicativos de TTS.

O Google fornece ao desenvolvedor um rico conjunto de APIs no namespace Android. Speech para abranger a maioria das instâncias de tornar um dispositivo "com reconhecimento de fala" (como software projetado para os cegos).  O namespace inclui a instalação para permitir que o texto seja convertido em fala por meio de `Android.Speech.Tts`, controle sobre o mecanismo usado para executar a tradução, bem como uma série de `RecognizerIntent`s que permitem que a fala seja convertida em texto.

Embora as instalações estejam lá para que a fala seja compreendida, há limitações com base no hardware usado. É improvável que o dispositivo interprete com êxito tudo falado sobre ele em todos os idiomas disponíveis.

## <a name="requirements"></a>Requisitos

Não há requisitos especiais para este guia, além de seu dispositivo ter um microfone e um palestrante.

O núcleo de um dispositivo Android interpretando a fala é o uso de um `Intent` com um `OnActivityResult`correspondente.
No entanto, é importante reconhecer que a fala não é compreendida — mas interpretada para texto. A diferença é importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>A diferença entre entender e interpretar

Uma definição simples de compreensão é que você pode determinar por Tom e contexto o significado real do que está sendo dito. Para interpretar apenas os meios de tirar as palavras e saídas em outro formulário.

Considere o seguinte exemplo simples que é usado na conversa diária:

<kbd>Olá, tudo bem?</kbd>

Sem inflexão (ênfase colocada em palavras específicas ou em partes de palavras), é uma pergunta simples. No entanto, se um ritmo lento for aplicado à linha, a pessoa que estiver ouvindo detectará que o Asker não é muito feliz e talvez precise de muita alegria ou que o Asker seja muito bom. Se a ênfase for colocada em "são", a pessoa que está solicitando geralmente é mais interessada na resposta.

Sem um processamento de áudio bastante poderoso para fazer uso da inflexão e um grau de inteligência artificial (ia) para entender o contexto, o software não pode até mesmo começar a entender o que foi dito — o melhor de um telefone simples pode converter a fala em texto.

## <a name="setting-up"></a>Configurando

Antes de usar o sistema de fala, é sempre prudente verificar se o dispositivo tem um microfone. Haveria pouco ponto tentando executar seu aplicativo em um Kindle ou no bloco de notas do Google sem um microfone instalado.

O exemplo de código a seguir demonstra como consultar se um microfone está disponível e, se não, para criar um alerta. Se nenhum microfone estiver disponível neste ponto, você sairá da atividade ou desabilitará a capacidade de gravar a fala.

```csharp
string rec = Android.Content.PM.PackageManager.FeatureMicrophone;
if (rec != "android.hardware.microphone")
{
    var alert = new AlertDialog.Builder(recButton.Context);
    alert.SetTitle("You don't seem to have a microphone to record with");
    alert.SetPositiveButton("OK", (sender, e) =>
    {
        return;
    });
    alert.Show();
}
```

### <a name="creating-the-intent"></a>Criando a intenção

A intenção do sistema de fala usa um tipo específico de intenção chamado de `RecognizerIntent`. Essa tentativa controla um grande número de parâmetros, incluindo o tempo de espera com silêncio até que a gravação seja considerada acima, quaisquer idiomas adicionais para reconhecimento e saída, e qualquer texto a ser incluído na caixa de diálogo modal do `Intent`como meio de instrução. Neste trecho de código, `VOICE` é um `readonly int` usado para reconhecimento no `OnActivityResult`.

```csharp
var voiceIntent = new Intent(RecognizerIntent.ActionRecognizeSpeech);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguageModel, RecognizerIntent.LanguageModelFreeForm);
voiceIntent.PutExtra(RecognizerIntent.ExtraPrompt, Application.Context.GetString(Resource.String.messageSpeakNow));
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputPossiblyCompleteSilenceLengthMillis, 1500);
voiceIntent.PutExtra(RecognizerIntent.ExtraSpeechInputMinimumLengthMillis, 15000);
voiceIntent.PutExtra(RecognizerIntent.ExtraMaxResults, 1);
voiceIntent.PutExtra(RecognizerIntent.ExtraLanguage, Java.Util.Locale.Default);
StartActivityForResult(voiceIntent, VOICE);
```

### <a name="conversion-of-the-speech"></a>Conversão da fala

O texto interpretado da fala será entregue dentro do `Intent`, que é retornado quando a atividade é concluída e acessado via `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Isso retornará um `IList<string>`, do qual o índice pode ser usado e exibido, dependendo do número de idiomas solicitados na intenção do chamador (e especificado no `RecognizerIntent.ExtraMaxResults`). Assim como acontece com qualquer lista, vale a pena verificar se há dados a serem exibidos.

Ao escutar o valor de retorno de um `StartActivityForResult`, o método de `OnActivityResult` deve ser fornecido.

No exemplo a seguir, `textBox` é um `TextBox` usado para gerar a saída do que foi ditado. Ele poderia ser usado igualmente para passar o texto para alguma forma de intérprete e, a partir daí, o aplicativo pode comparar o texto e a ramificação com outra parte do aplicativo.

```csharp
protected override void OnActivityResult(int requestCode, Result resultVal, Intent data)
{
    if (requestCode == VOICE)
    {
        if (resultVal == Result.Ok)
        {
            var matches = data.GetStringArrayListExtra(RecognizerIntent.ExtraResults);
            if (matches.Count != 0)
            {
                string textInput = textBox.Text + matches[0];
                textBox.Text = textInput;
                switch (matches[0].Substring(0, 5).ToLower())
                {
                    case "north":
                        MovePlayer(0);
                        break;
                    case "south":
                        MovePlayer(1);
                        break;
                }
            }
            else
            {
                textBox.Text = "No speech was recognised";
            }
        }
        base.OnActivityResult(requestCode, resultVal, data);
    }
}
```

## <a name="text-to-speech"></a>Conversão de Texto em Fala

A conversão de texto em fala não é exatamente o inverso da fala em texto e se baseia em dois componentes principais; um mecanismo de conversão de texto em fala que está sendo instalado no dispositivo e um idioma que está sendo instalado.

Em grande parte, os dispositivos Android vêm com o serviço padrão do Google TTS instalado e pelo menos um idioma. Isso é estabelecido quando o dispositivo é configurado pela primeira vez e será baseado em onde o dispositivo está no momento (por exemplo, um telefone configurado na Alemanha instalará o idioma alemão, enquanto um na América terá inglês americano).

### <a name="step-1---instantiating-texttospeech"></a>Etapa 1-criando uma instância de TextToSpeech

`TextToSpeech` pode levar até três parâmetros, os dois primeiros são necessários com o terceiro opcional (`AppContext`, `IOnInitListener``engine`). O ouvinte é usado para ligar ao serviço e testar a falha com o mecanismo que está sendo qualquer número de texto do Android disponível para mecanismos de fala. No mínimo, o dispositivo terá o próprio mecanismo do Google.

### <a name="step-2---finding-the-languages-available"></a>Etapa 2-localizando os idiomas disponíveis

A classe `Java.Util.Locale` contém um método útil chamado `GetAvailableLocales()`. Essa lista de idiomas com suporte do mecanismo de fala pode ser testada em relação aos idiomas instalados.

É uma questão comum gerar a lista de idiomas "compreendidos". Sempre haverá um idioma padrão (o idioma definido pelo usuário ao definir seu dispositivo pela primeira vez), portanto, neste exemplo, o `List<string>` tem "padrão" como o primeiro parâmetro, o restante da lista será preenchido dependendo do resultado da `textToSpeech.IsLanguageAvailable(locale)`.

```csharp
var langAvailable = new List<string>{ "Default" };
var localesAvailable = Java.Util.Locale.GetAvailableLocales().ToList();
foreach (var locale in localesAvailable)
{
    var res = textToSpeech.IsLanguageAvailable(locale);
    switch (res)
    {
        case LanguageAvailableResult.Available:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
        case LanguageAvailableResult.CountryVarAvailable:
          langAvailable.Add(locale.DisplayLanguage);
          break;
    }
}
langAvailable = langAvailable.OrderBy(t => t).Distinct().ToList();
```

Esse código chama [TextToSpeech. IsLanguageAvailable](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) para testar se o pacote de idioma de uma determinada localidade já está presente no dispositivo.
Esse método retorna um [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), que indica se o idioma para a localidade passada está disponível. Se `LanguageAvailableResult` indicar que o idioma está `NotSupported`, não haverá nenhum pacote de voz disponível (mesmo para download) para esse idioma. Se `LanguageAvailableResult` for definido como `MissingData`, será possível baixar um novo pacote de idioma, conforme explicado abaixo na etapa 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Etapa 3 – definir a velocidade e o timbre

O Android permite que o usuário altere o som da fala alterando a `SpeechRate` e `Pitch` (a taxa de velocidade e o Tom da fala). Isso vai de 0 a 1, com a fala "normal" sendo 1 para ambos.

### <a name="step-4---testing-and-loading-new-languages"></a>Etapa 4 – testando e carregando novos idiomas

O download de um novo idioma é executado usando um `Intent`. O resultado dessa tentativa faz com que o método [OnActivityResult](xref:Android.App.Activity.OnActivityResult*) seja invocado. Ao contrário do exemplo de fala a texto (que usou o [RecognizerIntent](xref:Android.Speech.RecognizerIntent) como um parâmetro de `PutExtra` para a `Intent`), os `Intent`s de teste e carregamento são baseados em `Action`:

- [TextToSpeech. Engine. ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; inicia uma atividade do mecanismo de `TextToSpeech` da plataforma para verificar a instalação e a disponibilidade corretas dos recursos de idioma no dispositivo.

- [TextToSpeech. Engine. ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; inicia uma atividade que solicita que o usuário Baixe os idiomas necessários.

O exemplo de código a seguir ilustra como usar essas ações para testar os recursos de idioma e baixar uma nova linguagem:

```csharp
var checkTTSIntent = new Intent();
checkTTSIntent.SetAction(TextToSpeech.Engine.ActionCheckTtsData);
StartActivityForResult(checkTTSIntent, NeedLang);
//
protected override void OnActivityResult(int req, Result res, Intent data)
{
    if (req == NeedLang)
    {
        var installTTS = new Intent();
        installTTS.SetAction(TextToSpeech.Engine.ActionInstallTtsData);
        StartActivity(installTTS);
    }
}
```

`TextToSpeech.Engine.ActionCheckTtsData` testes para a disponibilidade de recursos de idioma. `OnActivityResult` é invocado quando esse teste é concluído. Se for necessário baixar os recursos de idioma, `OnActivityResult` disparará a ação `TextToSpeech.Engine.ActionInstallTtsData` para iniciar uma atividade que permite ao usuário baixar os idiomas necessários. Observe que essa implementação de `OnActivityResult` não verifica o código de `Result` porque, neste exemplo simplificado, a determinação já foi feita de que o pacote de idioma precisa ser baixado.

A ação `TextToSpeech.Engine.ActionInstallTtsData` faz com que a atividade de **dados de voz TTS do Google** seja apresentada ao usuário para escolher os idiomas a serem baixados:

![Atividade de dados de voz do Google TTS](speech-images/01-google-tts-voice-data.png)

Por exemplo, o usuário pode escolher francês e clicar no ícone de download para baixar dados de voz em francês:

![Exemplo de download do idioma francês](speech-images/02-selecting-french.png)

A instalação desses dados ocorre automaticamente após a conclusão do download.

### <a name="step-5---the-ioninitlistener"></a>Etapa 5-o IOnInitListener

Para que uma atividade possa converter o texto em fala, o método de interface `OnInit` deve ser implementado (esse é o segundo parâmetro especificado para a instanciação da classe `TextToSpeech`). Isso inicializa o ouvinte e testa o resultado.

O ouvinte deve testar tanto `OperationResult.Success` quanto `OperationResult.Failure` no mínimo.
O exemplo a seguir mostra exatamente isso:

```csharp
void TextToSpeech.IOnInitListener.OnInit(OperationResult status)
{
    // if we get an error, default to the default language
    if (status == OperationResult.Error)
        textToSpeech.SetLanguage(Java.Util.Locale.Default);
    // if the listener is ok, set the lang
    if (status == OperationResult.Success)
        textToSpeech.SetLanguage(lang);
}
```

## <a name="summary"></a>Resumo

Neste guia, vimos as noções básicas da conversão de texto em fala e fala em texto e nos possíveis métodos de como incluí-los em seus próprios aplicativos. Embora eles não abranjam todos os casos específicos, agora você deve ter um entendimento básico de como a fala é interpretada, como instalar novas linguagens e como aumentar o inclusivity de seus aplicativos.

## <a name="related-links"></a>Links relacionados

- [DependencyService Xamarin. Forms](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Conversão de Texto em Fala (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Fala para texto (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Namespace do Android. Speech](xref:Android.Speech)
- [Namespace Android. Speech. TTS](xref:Android.Speech.Tts)
