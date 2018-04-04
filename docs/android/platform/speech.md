---
title: Fala de Android
description: Este artigo aborda os conceitos básicos de como usar o namespace Android.Speech muito eficiente. Desde sua criação, Android foi capaz de reconhecer fala e saída como texto. É um processo relativamente simples. Para converter texto em fala, no entanto, o processo é mais envolvido, não apenas o mecanismo de fala precisa ser levado em conta, mas também os idiomas disponíveis e instalados no sistema de texto em fala (TTS).
ms.prod: xamarin
ms.assetid: FA3B8EC4-34D2-47E3-ACEA-BD34B28115B9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/02/2018
ms.openlocfilehash: bdaa9bf09485c06551a2df15a2e3a4b410a53e75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="android-speech"></a>Fala de Android

_Este artigo aborda os conceitos básicos de como usar o namespace Android.Speech muito eficiente. Desde sua criação, Android foi capaz de reconhecer fala e saída como texto. É um processo relativamente simples. Para converter texto em fala, no entanto, o processo é mais envolvido, não apenas o mecanismo de fala precisa ser levado em conta, mas também os idiomas disponíveis e instalados no sistema de texto em fala (TTS)._

## <a name="speech-overview"></a>Visão geral de fala

Ter um sistema que "compreende" fala humana e enunciates que está sendo digitado — fala para texto e texto em fala — é uma área cada vez mais no desenvolvimento móvel à medida que aumenta a demanda de comunicação natural com nossos dispositivos. Há muitas instâncias onde tendo um recurso que converte o texto em fala, ou vice-versa, é uma ferramenta muito útil para incorporar o aplicativo android.

Por exemplo, com a fixação para baixo no uso de telefone celular ao orientando, os usuários desejam forma livre mãos de operacional seus dispositivos. A grande quantidade de fatores forma Android diferente — como desgaste Android — e a inclusão nunca widening deles capaz de usar dispositivos Android (como tablets e preenche Observação) tiver criado um maior foco em ótimos aplicativos TTS.

Google fornece aos desenvolvedores um conjunto avançado de APIs no namespace Android.Speech para cobrir a maioria das instâncias de criação de um dispositivo "fala ciente" (como software projetado para a blind).  O namespace inclui o recurso para permitir que o texto a ser convertido em fala por meio de `Android.Speech.Tts`, controle sobre o mecanismo usado para executar a conversão, bem como um número de `RecognizerIntent`s que permitem a fala a ser convertido em texto.

Enquanto as instalações existem para fala a ser compreendido, há limitações com base em hardware usado. É improvável que o dispositivo com êxito irá interpretar tudo falado a ele em todos os idiomas disponíveis.

## <a name="requirements"></a>Requisitos

Não há nenhum requisito especial para este guia, que não seja o seu dispositivo com um microfone e alto-falantes.

O núcleo de um dispositivo Android interpretar a fala é o uso de um `Intent` com um correspondente `OnActivityResult`.
É importante, no entanto, reconhecer que a fala não é compreendida — mas interpretado como texto. A diferença é importante.

### <a name="the-difference-between-understanding-and-interpreting"></a>A diferença entre a entender e interpretar

Uma definição simple de compreensão é que você possa determinar por tom e contexto o significado real de que está sendo dito. Para interpretar apenas significa fazer as palavras e retorná-los em outro formato.

Considere o seguinte exemplo simple que é usado em todos os dias conversa: 

<kbd>Olá, tudo bem?</kbd>

Sem inflexão (ênfase em palavras específicas ou partes de palavras), é uma pergunta simple. No entanto, se uma velocidade lenta é aplicada à linha, a pessoa que está escutando detectará que o usuário que criou não é muito feliz e talvez precise cheering ou se o usuário que criou está doente. Se a ênfase é colocada na "are", a pessoa que está solicitando é geralmente mais interessa na resposta.

Sem processamento para tornar muito poderosas de áudio usar o inflexões e um grau de inteligência artificial (AI) para compreender o contexto, o software não pode começar a entender o que foi dito — o melhor um telefone simple pode fazer é converter a fala em texto.

## <a name="setting-up"></a>Configurando

Antes de usar o sistema de fala, convém sempre verifique se que o dispositivo possui um microfone. Seria muito sentido tentar executar seu aplicativo em um bloco de notas Kindle ou Google sem um microfone instalado.

O exemplo de código a seguir demonstra uma consulta se um microfone está disponível e se não, para criar um alerta. Se nenhum microfone está disponível neste momento você deve encerrar a atividade ou desabilitar o recurso de gravação de voz.

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

A intenção do sistema de fala usa um tipo específico de tentativa de chamada a `RecognizerIntent`. Essa tentativa controla um grande número de parâmetros, incluindo o tempo de espera com silêncio até que a gravação é considerada em qualquer idioma adicional para reconhecer e de saída e o texto a ser incluído no `Intent`da caixa de diálogo modal como meio de instrução. Neste trecho, `VOICE` é um `readonly int` usada para o reconhecimento em `OnActivityResult`.

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

O texto interpretado a partir da fala será entregue dentro do `Intent`, que é retornado quando a atividade foi concluída e é acessada por meio de `GetStringArrayListExtra(RecognizerIntent.ExtraResults)`. Isso retornará um `IList<string>`, do qual o índice pode ser usado e exibido, dependendo do número de idiomas solicitado no propósito de chamador (especificados no e a `RecognizerIntent.ExtraMaxResults`). Como com qualquer lista, vale a pena verificação para garantir que há dados a serem exibidos.

Quando a escuta para o valor de retorno de um `StartActivityForResult`, o `OnActivityResult` método deve ser fornecido.

No exemplo a seguir, `textBox` é um `TextBox` usado para a saída que tem sido determinado. Ela igualmente pode ser usada para passar o texto em alguma forma de interpretador e de lá, o aplicativo pode comparar o texto e a ramificação para outra parte do aplicativo.

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
                  switch(matches[0].Substring(0,5).ToLower())
                  {
                     case "north":
                      MovePlayer(0);
                     break;
                   case "south":
                     MovePlayer(1);
                     break;
             }
             else
                  textBox.Text = "No speech was recognised";
        }
   }
    base.OnActivityResult(requestCode, resultVal, data);
}
```

## <a name="text-to-speech"></a>Texto em fala

Texto em fala não é bastante o inverso de fala em texto e se baseia em dois componentes principais; um mecanismo de texto em fala que está sendo instalado no dispositivo e um idioma que está sendo instalado.

Em grande parte, dispositivos Android vêm com o padrão de serviço do Google TTS instalado e pelo menos um idioma. Isso é estabelecido quando o dispositivo é primeiro configurado e se baseia em que o dispositivo é no momento (por exemplo, um telefone configurar na Alemanha instalará o idioma alemão, enquanto um na América terá inglês americano).

### <a name="step-1---instantiating-texttospeech"></a>Etapa 1: criar uma instância de TextToSpeech

`TextToSpeech` pode levar até 3 parâmetros, os dois primeiros são necessários com o terceiro opcionais (`AppContext`, `IOnInitListener`, `engine`). O ouvinte é usado para ligar para o serviço e o teste falha com o mecanismo que está sendo qualquer número de mecanismos de texto em fala disponíveis Android. No mínimo, o dispositivo terá o mecanismo do Google.

### <a name="step-2---finding-the-languages-available"></a>Etapa 2: Localizando os idiomas disponíveis

O `Java.Util.Locale` classe contém um método útil chamado `GetAvailableLocales()`. Esta lista de idiomas com suporte pelo mecanismo de fala, em seguida, pode ser testada em relação os idiomas instalados.

É uma questão trivial para gerar a lista de idiomas "entendi". Sempre haverá um idioma padrão (o idioma que o usuário definir quando eles primeiro configurarem seu dispositivo), portanto neste exemplo o `List<string>` tem "Padrão" como o primeiro parâmetro, o restante da lista será preenchido dependendo do resultado de `textToSpeech.IsLanguageAvailable(locale)`.

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

Esse código chama [TextToSpeech.IsLanguageAvailable](https://developer.xamarin.com/api/member/Android.Speech.Tts.TextToSpeech.IsLanguageAvailable/p/Java.Util.Locale/) para testar se o pacote de idioma para uma determinada localidade já está presente no dispositivo. Este método retorna um [LanguageAvailableResult](https://developer.xamarin.com/api/type/Android.Speech.Tts.LanguageAvailableResult/), que indica se o idioma para a localidade passado está disponível. Se `LanguageAvailableResult` indica que o idioma é `NotSupported`, em seguida, não há nenhum pacote de voz disponíveis (até mesmo para download) para esse idioma. Se `LanguageAvailableResult` é definido como `MissingData`, em seguida, é possível baixar um novo pacote de idioma, conforme explicado a seguir na etapa 4.

### <a name="step-3---setting-the-speed-and-pitch"></a>Etapa 3: definir a velocidade e a densidade

Android permite ao usuário alterar o som da fala alterando o `SpeechRate` e `Pitch` (a taxa de velocidade e o tom da fala). Isso vai de 0 a 1, com a fala "normal", sendo 1 para ambos.

### <a name="step-4---testing-and-loading-new-languages"></a>Etapa 4: testar e carregar novos idiomas

Baixar um novo idioma é feita usando um `Intent`. Faz com que o resultado da intenção de [OnActivityResult](https://developer.xamarin.com/api/member/Android.App.Activity.OnActivityResult/) método ser chamado. Ao contrário do exemplo de fala em texto (que é usado o [RecognizerIntent](https://developer.xamarin.com/api/type/Android.Speech.RecognizerIntent/) como um `PutExtra` parâmetro para o `Intent`), testes e carregamento `Intent`s são `Action`-com base em:

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

`TextToSpeech.Engine.ActionCheckTtsData` testa a disponibilidade de recursos de idioma. `OnActivityResult` é invocado quando esse teste é concluído. Se precisarem de recursos de idioma a ser baixado, `OnActivityResult` dispara o `TextToSpeech.Engine.ActionInstallTtsData` para iniciar uma atividade que permite que o usuário baixe os idiomas necessários. Observe que esse `OnActivityResult` implementação não verifica o `Result` código porque, neste exemplo simplificado, a determinação já foi efetuada o pacote de idiomas precisa ser baixados.

O `TextToSpeech.Engine.ActionInstallTtsData` ação faz com que o **dados de voz do Google TTS** atividade para ser apresentado ao usuário para a escolha de idiomas para download:

![Atividade de dados de voz TTS do Google](speech-images/01-google-tts-voice-data.png)

Por exemplo, o usuário pode escolher francês e clique no ícone de download para baixar os dados de voz francês:

![Exemplo de download de idioma francês](speech-images/02-selecting-french.png)

Instalação de dados ocorre automaticamente após a conclusão do download.


### <a name="step-5---the-ioninitlistener"></a>Etapa 5 - o IOnInitListener

Para uma atividade poder converter texto em fala, o método de interface `OnInit` precisa ser implementada (esse é o segundo parâmetro especificado para a instanciação do `TextToSpeech` classe). Isso inicializa o ouvinte e o resultado de testes.

O ouvinte deve testar ambos `OperationResult.Success` e `OperationResult.Failure` no mínimo.
O exemplo a seguir mostra isso:

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

Este guia, examinamos os fundamentos da conversão de texto em fala e fala em texto e métodos possíveis de como incluí-los em seus próprios aplicativos. Embora eles não abrangem cada caso específico, agora você deve ter um entendimento básico de como a fala é interpretada, como instalar novos idiomas e como aumentar a inclusivity de seus aplicativos.



## <a name="related-links"></a>Links relacionados

- [DependencyService xamarin. Forms](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Texto em fala (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/TextToSpeech)
- [Fala de texto (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SpeechToText)
- [Namespace Android.Speech](https://developer.xamarin.com/api/namespace/Android.Speech/)
- [Namespace Android.Speech.Tts](https://developer.xamarin.com/api/namespace/Android.Speech.Tts/)
