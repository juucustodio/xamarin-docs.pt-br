---
title: Fala de Android
description: Este artigo aborda os conceitos básicos de como usar o namespace do Android. Speech muito poderoso. Desde sua concepção, o Android tem sido capaz de reconhecer fala e enviá-lo como texto. É um processo relativamente simples. Para texto em fala, no entanto, o processo é mais envolvido, pois não apenas o mecanismo de fala precisam ser levadas em conta, mas também os idiomas disponíveis e instalados do sistema de texto em fala (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/02/2018
ms.openlocfilehash: 3d4c29a7d206b826046fd1f79e0513e85ea57898
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740663"
---
# <a name="android-speech"></a>Fala de Android

_Este artigo aborda os conceitos básicos de como usar o namespace do Android. Speech muito poderoso. Desde sua concepção, o Android tem sido capaz de reconhecer fala e enviá-lo como texto. É um processo relativamente simples. Para texto em fala, no entanto, o processo é mais envolvido, pois não apenas o mecanismo de fala precisam ser levadas em conta, mas também os idiomas disponíveis e instalados do sistema de texto em fala (TTS)._

## <a name="speech-overview"></a>Visão geral de fala

Ter um sistema, o que "compreende" fala humana e enunciates que está sendo digitado – conversão de fala em texto e texto em fala — é uma área cada vez mais em desenvolvimento móvel conforme a demanda de comunicação natural com nossos dispositivos aumenta. Há muitas instâncias em que ter um recurso que converte o texto em fala, ou vice-versa, é uma ferramenta muito útil para incorporar em seu aplicativo android.

Por exemplo, com o clamp para baixo no uso de telefone celular enquanto dirige, os usuários desejam uma maneira de mãos livres de operar seus dispositivos. A grande quantidade de fatores forma Android diferentes — como o Android Wear — e a inclusão de cada vez daqueles capaz de usar dispositivos Android (por exemplo, os painéis de observação e tablets), criou um foco maior no ótimos aplicativos de TTS.

Google fornece ao desenvolvedor de um conjunto avançado de APIs no namespace do Android. Speech para abranger a maioria das instâncias de criação de um dispositivo "fala ciente" (como um software projetado for the blind).  O namespace inclui o recurso para permitir que o texto a ser convertido em fala por meio `Android.Speech.Tts`, controle sobre o mecanismo usado para executar a conversão, bem como um número de `RecognizerIntent`s que permitem a conversão de fala a ser convertido em texto.

Enquanto os recursos existem para o reconhecimento de fala ser compreendido, há limitações com base em hardware usado. É improvável que o dispositivo irá interpretar com êxito tudo falado a ele em todos os idiomas disponíveis.

## <a name="requirements"></a>Requisitos

Não há nenhum requisito especial para este guia, que não seja o seu dispositivo que tem um microfone e alto-falante.

O núcleo de um dispositivo Android Interpretando fala é o uso de um `Intent` com um correspondente `OnActivityResult`.
É importante, no entanto, reconhecer que a fala não é compreendida — mas interpretados como texto. A diferença é importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>A diferença entre a compreender e interpretar

Uma definição simples de compreensão é que é capaz de determinar por tom e contexto o significado real de proferidas. Para interpretar apenas significa que pegar as palavras e retorná-los em outro formulário.

Considere o seguinte exemplo simple que é usado em todos os dias conversa:

<kbd>Olá, tudo bem?</kbd>

Sem inflexão (ênfase em palavras específicas ou partes de palavras), é uma pergunta simples. No entanto, se um ritmo lento é aplicado para a linha, a pessoa que escuta detectará que o usuário que criou não é muito feliz e talvez precise de cheering ou se o usuário que criou está doente. Se a ênfase é colocada na "are", a pessoa perguntando é geralmente mais interessada na resposta.

Sem processamento para tornar razoavelmente poderosas de áudio usar o inflexões e um grau de inteligência artificial (AI) para compreender o contexto, o software até mesmo não pode começar a entender o que foi dito — o melhor um telefone simple pode fazer é converter a fala em texto.

## <a name="setting-up"></a>Configurando

Antes de usar o sistema de fala, sempre é aconselhável verificar para garantir que o dispositivo tem um microfone. Seria muito sentido tentar executar seu aplicativo em um bloco de anotações do Kindle ou do Google sem um microfone instalado.

O exemplo de código a seguir demonstra uma consulta se um microfone está disponível e se não, para criar um alerta. Se nenhum microfone está disponível neste momento você deve encerrar a atividade ou desabilitar a capacidade de registrar a fala.

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

A intenção para o sistema de fala usa um tipo específico de intenção de chamada a `RecognizerIntent`. Essa intenção controla um grande número de parâmetros, incluindo quanto tempo aguardar com silêncio, até que a gravação é considerada ao longo de qualquer idioma adicional para reconhecer e de saída e o texto a ser incluída no `Intent`da caixa de diálogo modal como meio de instrução. Neste trecho de código `VOICE` é um `readonly int` usada para o reconhecimento na `OnActivityResult`.

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

### <a name="conversion-of-the-speech"></a>Conversão de fala

O texto interpretado de reconhecimento do fala será entregue dentro do `Intent`, que é retornado quando a atividade foi concluída e é acessada por meio de `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Isso retornará um `IList<string>`, do qual o índice pode ser usado e exibido, dependendo do número de linguagens solicitado na intenção do chamador (especificada no `RecognizerIntent.ExtraMaxResults`). Como com qualquer lista no entanto, vale a pena verificar para garantir que haja dados a serem exibidos.

Quando a escuta para o valor retornado de uma `StartActivityForResult`, o `OnActivityResult` método precisa ser fornecido.

No exemplo a seguir, `textBox` é um `TextBox` usado para gerar o que tem sido determinado. Ela igualmente pode ser usada para passar o texto em alguma forma de interpretador e a partir daí, o aplicativo pode comparar o texto e a ramificação para outra parte do aplicativo.

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

Texto em fala não é bastante o inverso da fala em texto e se baseia em dois componentes principais; um mecanismo de texto em fala que está sendo instalado no dispositivo e um idioma que está sendo instalado.

Basicamente, os dispositivos Android vêm com o padrão serviço Google TTS instalado e pelo menos um idioma. Isso é estabelecido quando o dispositivo é configurado pela primeira vez e se baseia em que o dispositivo está no momento (por exemplo, um telefone configurado na Alemanha instalará o idioma alemão, enquanto um na América terá inglês americano).

### <a name="step-1---instantiating-texttospeech"></a>Etapa 1 - TextToSpeech ao instanciar

`TextToSpeech` pode levar até 3 parâmetros, as duas primeiras são necessárias com o terceiro opcionais (`AppContext`, `IOnInitListener`, `engine`). O ouvinte é usado para ligar para o serviço e o teste falha com o mecanismo que está sendo qualquer número de mecanismos de texto em fala disponíveis Android. No mínimo, o dispositivo terá o mecanismo do Google.

### <a name="step-2---finding-the-languages-available"></a>Etapa 2: Localizando os idiomas disponíveis

O `Java.Util.Locale` classe contém um método útil chamado `GetAvailableLocales()`. Esta lista de idiomas com suporte pelo mecanismo de fala, em seguida, pode ser testada em relação os idiomas instalados.

É uma questão trivial para gerar a lista de idiomas "entendi". Sempre haverá um idioma padrão (o idioma que o usuário definir quando eles primeiro configuraram seu dispositivo), portanto, neste exemplo o `List<string>` tem o "Padrão" como o primeiro parâmetro, o restante da lista será preenchido, dependendo do resultado do `textToSpeech.IsLanguageAvailable(locale)`.

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

Esse código chama [TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/) para testar se o pacote de idioma para uma determinada localidade já está presente no dispositivo.
Esse método retorna um [LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/), que indica se o idioma para a localidade passada está disponível. Se `LanguageAvailableResult` indica que o idioma é `NotSupported`, em seguida, não há nenhum pacote de voz disponíveis (e até mesmo para download) para esse idioma. Se `LanguageAvailableResult` é definido como `MissingData`, em seguida, é possível baixar um novo pacote de idioma, conforme explicado abaixo na etapa 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Etapa 3: definir a velocidade e tom

Android permite que o usuário alterar o som de reconhecimento do fala alterando a `SpeechRate` e `Pitch` (a taxa de velocidade e o tom de fala). Isso vai de 0 a 1, com a fala "normal", sendo 1 para ambos.

### <a name="step-4---testing-and-loading-new-languages"></a>Etapa 4 - Testando e carregar novos idiomas

Baixar uma nova linguagem é executada usando um `Intent`. O resultado dessa intenção faz com que o [OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/) método a ser invocado. Ao contrário do exemplo de fala em texto (que é usado o [RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/) como um `PutExtra` parâmetro para o `Intent`), testes e carregando `Intent`s são `Action`-com base em:

-   [TextToSpeech.Engine.ActionCheckTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionCheckTtsData/) &ndash; inicia uma atividade da plataforma `TextToSpeech` mecanismo para verificar a instalação correta e a disponibilidade de recursos de idioma no dispositivo.

-   [TextToSpeech.Engine.ActionInstallTtsData](https://developer.xamarin.com/api/field/Android.Speech.Tts.TextToSpeech+Engine.ActionInstallTtsData/) &ndash; inicia uma atividade que solicita ao usuário para baixar os idiomas necessários.

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

`TextToSpeech.Engine.ActionCheckTtsData` testa a disponibilidade de recursos de idioma. `OnActivityResult` é invocado quando esse teste é concluído. Se precisam de recursos de idioma a ser baixado, `OnActivityResult` dispara o `TextToSpeech.Engine.ActionInstallTtsData` ação para iniciar uma atividade que permite que o usuário baixe os idiomas necessários. Observe que este `OnActivityResult` implementação não verifica o `Result` código porque, neste exemplo simplificado, a determinação já tenha sido feita que o pacote de idiomas precisa ser baixados.

O `TextToSpeech.Engine.ActionInstallTtsData` faz com que a ação de **dados de voz de TTS Google** atividade a ser apresentada ao usuário para a escolha de idiomas para baixar:

![Atividade de dados de voz de TTS do Google](speech-images/01-google-tts-voice-data.png)

Por exemplo, o usuário pode escolher o francês e clique no ícone de download para baixar os dados de voz francês:

![Exemplo de download de idioma francês](speech-images/02-selecting-french.png)

Instalação dos dados ocorre automaticamente após a conclusão do download.


### <a name="step-5---the-ioninitlistener"></a>Etapa 5 – o IOnInitListener

Para uma atividade para ser capaz de converter texto em fala, o método de interface `OnInit` precisa ser implementada (esse é o segundo parâmetro especificado para a instanciação do `TextToSpeech` classe). Isso inicializa o ouvinte e testa o resultado.

O ouvinte deve testar para ambos `OperationResult.Success` e `OperationResult.Failure` no mínimo.
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

Este guia, examinamos as Noções básicas da conversão de texto em fala e fala para texto e os métodos possíveis de como incluí-los em seus próprios aplicativos. Embora elas não abrangem cada caso específico, agora você deve ter um entendimento básico de como a fala é interpretada como instalar novos idiomas e como aumentar o inclusivity dos seus aplicativos.



## <a name="related-links"></a>Links relacionados

- [Xamarin.Forms DependencyService](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Texto em fala (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Reconhecimento de fala (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Namespace do Android. Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Namespace Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
