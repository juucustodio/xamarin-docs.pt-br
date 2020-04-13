---
title: Discurso do Android
description: Este artigo abrange o básico de usar o muito poderoso Espaço de nome Android.Speech. Desde a sua criação, o Android tem sido capaz de reconhecer a fala e desinhá-la como texto. É um processo relativamente simples. Para o texto-a-fala, no entanto, o processo é mais envolvido, pois não só o mecanismo de fala tem que ser levado em conta, mas também os idiomas disponíveis e instalados a partir do sistema Text To Speech (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/02/2018
ms.openlocfilehash: e8c7d1a4fb3537644ed3b7737158a5e50abcdae5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019761"
---
# <a name="android-speech"></a>Discurso do Android

_Este artigo abrange o básico de usar o muito poderoso Espaço de nome Android.Speech. Desde a sua criação, o Android tem sido capaz de reconhecer a fala e desinhá-la como texto. É um processo relativamente simples. Para o texto-a-fala, no entanto, o processo é mais envolvido, pois não só o mecanismo de fala tem que ser levado em conta, mas também os idiomas disponíveis e instalados a partir do sistema Text To Speech (TTS)._

## <a name="speech-overview"></a>Visão geral do discurso

Ter um sistema, que "entende" a fala humana e enuncia o que está sendo digitado — Fala ao Texto e Texto para Falar — é uma área cada vez maior dentro do desenvolvimento móvel à medida que a demanda por comunicação natural com nossos dispositivos aumenta. Existem muitos casos em que ter um recurso que converte texto em fala, ou vice-versa, é uma ferramenta muito útil para incorporar em seu aplicativo android.

Por exemplo, com o grampo no uso do celular enquanto dirige, os usuários querem uma maneira livre de mãos dadas de operar seus dispositivos. A infinidade de diferentes fatores de forma androide — como o Android Wear — e a inclusão cada vez maior daqueles capazes de usar dispositivos Android (como tablets e blocos de notas), criou um foco maior em grandes aplicativos TTS.

O Google fornece ao desenvolvedor um rico conjunto de APIs no espaço de nome Android.Speech para cobrir a maioria das instâncias de fazer um dispositivo "conhecimento de fala" (como software projetado para cegos).  O namespace inclui a facilidade de permitir que o `Android.Speech.Tts`texto seja traduzido em fala através de controle `RecognizerIntent`sobre o motor usado para realizar a tradução, bem como um número de s que permitem que a fala seja convertida em texto.

Embora as facilidades estejam lá para que a fala seja compreendida, há limitações baseadas no hardware utilizado. É improvável que o dispositivo interprete com sucesso tudo o que foi falado com ele em todas as línguas disponíveis.

## <a name="requirements"></a>Requisitos

Não há requisitos especiais para este guia, exceto o seu dispositivo ter um microfone e alto-falante.

O núcleo de um dispositivo Android interpretando `Intent` a `OnActivityResult`fala é o uso de um com um correspondente .
É importante, porém, reconhecer que o discurso não é compreendido — mas interpretado como texto. A diferença é importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>A diferença entre compreensão e interpretação

Uma definição simples de compreensão é que você é capaz de determinar por tom e contexto o real significado do que está sendo dito. Interpretar apenas significa pegar as palavras e produzir de outra forma.

Considere o seguinte exemplo simples que é usado na conversa cotidiana:

<kbd>Olá, tudo bem?</kbd>

Sem inflexão (ênfase colocada em palavras específicas ou partes de palavras), é uma pergunta simples. No entanto, se um ritmo lento for aplicado à linha, a pessoa que estiver ouvindo detectará que o asker não está muito feliz e talvez precise se animar ou que o asker não esteja bem. Se a ênfase é colocada em "are", a pessoa que pergunta geralmente está mais interessada na resposta.

Sem processamento de áudio bastante poderoso para fazer uso da inflexão, e um grau de inteligência artificial (IA) para entender o contexto, o software não pode sequer começar a entender o que foi dito — o melhor que um simples telefone pode fazer é converter a fala em texto.

## <a name="setting-up"></a>Configurando

Antes de usar o sistema de fala, é sempre prudente verificar se o dispositivo tem um microfone. Não adiantaria tentar executar seu aplicativo em um Kindle ou google note pad sem um microfone instalado.

A amostra de código abaixo demonstra a consulta se um microfone está disponível e, se não, para criar um alerta. Se nenhum microfone estiver disponível neste momento, você desistiria da atividade ou desativaria a capacidade de gravar a fala.

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

A intenção para o sistema de fala `RecognizerIntent`usa um tipo particular de intenção chamado . Essa intenção controla um grande número de parâmetros, incluindo quanto tempo esperar com silêncio até que a gravação `Intent`seja considerada mais, quaisquer linguagens adicionais para reconhecer e produzir, e qualquer texto para incluir no diálogo modal como meio de instrução. Neste trecho, `VOICE` é usado `readonly int` para reconhecimento `OnActivityResult`em .

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

### <a name="conversion-of-the-speech"></a>Conversão do discurso

O texto interpretado a partir do `Intent`discurso será entregue dentro do , que é `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`devolvido quando a atividade foi concluída e é acessada via . Isso retornará `IList<string>`um , do qual o índice pode ser usado e exibido, dependendo do número de `RecognizerIntent.ExtraMaxResults`idiomas solicitados na intenção de chamada (e especificado no ). Como em qualquer lista, porém, vale a pena verificar se há dados a serem exibidos.

Ao ouvir o valor `StartActivityForResult`de `OnActivityResult` retorno de a, o método deve ser fornecido.

No exemplo abaixo, `textBox` `TextBox` é usado para outputting o que foi ditado. Ele poderia igualmente ser usado para passar o texto para alguma forma de intérprete e, a partir daí, o aplicativo pode comparar o texto e o ramo com outra parte da aplicação.

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

## <a name="text-to-speech"></a>Texto em fala

Texto para fala não é bem o inverso da fala para o texto e conta com dois componentes-chave; um mecanismo texto-para-fala sendo instalado no dispositivo e um idioma sendo instalado.

Em grande parte, os dispositivos Android vêm com o serviço padrão do Google TTS instalado e pelo menos um idioma. Isso é estabelecido quando o dispositivo é configurado pela primeira vez e será baseado em onde o dispositivo está no momento (por exemplo, um telefone configurado na Alemanha instalará o idioma alemão, enquanto um na América terá inglês americano).

### <a name="step-1---instantiating-texttospeech"></a>Passo 1 - Texto instantâneoToSpeech

`TextToSpeech`podem ter até 3 parâmetros, os dois primeiros`AppContext`são `IOnInitListener` `engine`necessários, sendo o terceiro opcional ( , ). O ouvinte é usado para vincular-se ao serviço e testar a falha com o motor sendo qualquer número de texto Android disponível para motores de fala. No mínimo, o dispositivo terá o próprio motor do Google.

### <a name="step-2---finding-the-languages-available"></a>Passo 2 - Encontrar os idiomas disponíveis

A `Java.Util.Locale` classe contém um `GetAvailableLocales()`método útil chamado . Esta lista de idiomas suportados pelo mecanismo de fala pode então ser testada contra os idiomas instalados.

É uma questão trivial gerar a lista de línguas "entendidas". Sempre haverá um idioma padrão (a linguagem que o usuário definiu quando configurou `List<string>` seu dispositivo pela primeira vez), então neste exemplo o tem "Padrão" `textToSpeech.IsLanguageAvailable(locale)`como primeiro parâmetro, o restante da lista será preenchido dependendo do resultado do .

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

Este código chama [TextToSpeech.IsLanguageDisponível](xref:Android.Speech.Tts.TextToSpeech.IsLanguageAvailable*) para testar se o pacote de idioma para um determinado local já está presente no dispositivo.
Este método retorna um [LanguageAvailableResult](xref:Android.Speech.Tts.LanguageAvailableResult), que indica se o idioma para o local passado está disponível. Se `LanguageAvailableResult` indicar que `NotSupported`o idioma é, então não há pacote de voz disponível (mesmo para download) para esse idioma. Se `LanguageAvailableResult` estiver `MissingData`definido para , então é possível baixar um novo pacote de idiomas, conforme explicado abaixo na Etapa 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Passo 3 - Definindo a velocidade e o arremesso

O Android permite que o usuário altere `SpeechRate` o `Pitch` som da fala alterando o e (a taxa de velocidade e o tom da fala). Isso vai de 0 a 1, com o discurso "normal" sendo 1 para ambos.

### <a name="step-4---testing-and-loading-new-languages"></a>Passo 4 - Testar e carregar novos idiomas

O download de um novo `Intent`idioma é realizado usando um . O resultado dessa intenção faz com que o método [OnActivityResult](xref:Android.App.Activity.OnActivityResult*) seja invocado. Ao contrário do exemplo de fala para texto (que usou o [RecognizerIntent](xref:Android.Speech.RecognizerIntent) como `PutExtra` parâmetro para o), `Intent`os testes e carregamentos `Intent`são baseados `Action`em:

- [TextToSpeech.Engine.ActionCheckTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionCheckTtsData) &ndash; Inicia uma atividade `TextToSpeech` do mecanismo da plataforma para verificar a instalação e a disponibilidade adequadas de recursos de idioma no dispositivo.

- [TextToSpeech.Engine.ActionInstallTtsData](xref:Android.Speech.Tts.TextToSpeech.Engine.ActionInstallTtsData) &ndash; Inicia uma atividade que solicita ao usuário que baixe os idiomas necessários.

O exemplo de código a seguir ilustra como usar essas ações para testar recursos de idioma e baixar um novo idioma:

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

`TextToSpeech.Engine.ActionCheckTtsData`testes para a disponibilidade de recursos linguísticos. `OnActivityResult`é invocado quando este teste é concluído. Se os recursos do idioma `OnActivityResult` precisarem `TextToSpeech.Engine.ActionInstallTtsData` ser baixados, aciona a ação para iniciar uma atividade que permite ao usuário baixar os idiomas necessários. Observe que `OnActivityResult` esta implementação `Result` não verifica o código porque, neste exemplo simplificado, já foi feita a determinação de que o pacote de idiomas precisa ser baixado.

A `TextToSpeech.Engine.ActionInstallTtsData` ação faz com que a atividade **de dados de voz do Google TTS** seja apresentada ao usuário para escolher idiomas para baixar:

![Atividade de dados de voz do Google TTS](speech-images/01-google-tts-voice-data.png)

Como exemplo, o usuário pode escolher francês e clicar no ícone de download para baixar dados de voz franceses:

![Exemplo de baixar a língua francesa](speech-images/02-selecting-french.png)

A instalação desses dados acontece automaticamente após a conclusão do download.

### <a name="step-5---the-ioninitlistener"></a>Passo 5 - O IOnInitListener

Para que uma atividade seja capaz de converter `OnInit` o texto em fala, o método de interface deve ser `TextToSpeech` implementado (este é o segundo parâmetro especificado para a instanciação da classe). Isso inicializa o ouvinte e testa o resultado.

O ouvinte deve testar `OperationResult.Success` `OperationResult.Failure` para ambos e no mínimo.
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

Neste guia, analisamos o básico da conversão de texto para fala e fala em texto e possíveis métodos de como incluí-los dentro de seus próprios aplicativos. Embora eles não cobrem todos os casos específicos, agora você deve ter uma compreensão básica de como a fala é interpretada, como instalar novos idiomas e como aumentar a inclusão de seus aplicativos.

## <a name="related-links"></a>Links relacionados

- [Xamarin.Forms DependencyService](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice//)
- [Texto para fala (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-texttospeech)
- [Fala ao Texto (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-speechtotext)
- [Espaço de nome android.Speech](xref:Android.Speech)
- [Espaço de nome Android.Speech.Tts](xref:Android.Speech.Tts)
