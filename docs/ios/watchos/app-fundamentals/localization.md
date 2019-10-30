---
title: Trabalhando com a localização do watchOS no Xamarin
description: Este documento descreve como localizar aplicativos watchOS criados com o Xamarin. Ele aborda aplicativos de inspeção, extensões de inspeção, cadeias de caracteres em código, texto de storyboard, teste e muito mais.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 82b739697705ac4c90390a36405d755a5f523159
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028412"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Trabalhando com a localização do watchOS no Xamarin

_Adaptando seus aplicativos watchOS para vários idiomas_

![](localization-images/both-languages-sml.png "Apple Watch displaying localized content")

os aplicativos watchOS são localizados usando os métodos iOS padrão:

- Usando a **ID de localização** em elementos de storyboard,
- arquivos **. Strings** associados ao storyboard e
- Arquivos **. Strings localizáveis** para texto usado no código.

Os storyboards e os recursos padrão estão localizados em um diretório **base** , e as traduções específicas a um idioma e outros recursos são armazenados em diretórios **. lproj** .
os sistemas operacionais iOS e Watch usarão automaticamente a seleção de idioma do usuário para carregar os recursos e cadeias de caracteres corretos.

Como um aplicativo de Apple Watch tem duas partes – Observe o aplicativo e a extensão de inspeção-os recursos de cadeia de caracteres localizados são necessários em dois locais, dependendo de como eles são usados.

O texto e os recursos localizados serão *diferentes* no aplicativo Watch e na extensão Watch.

## <a name="watch-app"></a>Inspecionar aplicativo

O aplicativo Watch contém o storyboard que descreve a interface do usuário do aplicativo. Todos os controles (como `Label` e `Image`) que dão suporte à localização têm uma **ID de localização**.

Cada diretório **. lproj** específico do idioma deve conter arquivos **. Strings** com as traduções de cada elemento (usando a **ID de localização**), bem como imagens referenciadas pelo Storyboard.

## <a name="watch-extension"></a>Extensão de inspeção

A extensão Watch é onde o código do aplicativo é executado. Qualquer texto que é exibido para o usuário do código precisa ser localizado na extensão e não no aplicativo Watch.

A extensão também deve conter diretórios **. lproj** específicos do idioma, mas os arquivos **. Strings** só exigem traduções para texto que é usado em seu código.

## <a name="globalizing-the-watch-solution"></a>Globalizando a solução Watch

A globalização é o processo de tornar um aplicativo localizável.
Para aplicativos observados, isso significa criar o storyboard com diferentes comprimentos de texto em mente, garantindo que cada layout de tela se ajuste adequadamente dependendo de qual texto é exibido. Você também precisa garantir que todas as cadeias de caracteres referenciadas no código de extensão de inspeção possam ser traduzidas usando o método `LocalizedString`.

### <a name="watch-app"></a>Inspecionar aplicativo

Por padrão, o aplicativo Watch não está configurado para localização. Você precisa mover o arquivo de storyboard padrão e criar alguns outros diretórios para suas traduções:

1. Crie o diretório **base. lproj** e mova a **interface. Storyboard** para ele.

2. Crie **\<idioma > diretórios. lproj** para cada idioma ao qual você deseja dar suporte.

3. Os diretórios **. lproj** devem conter um arquivo de texto **interface. Strings** (o filename deve corresponder ao nome do storboard). Opcionalmente, você pode inserir qualquer imagem que exija a localização nesses diretórios.

O projeto do aplicativo Watch parece com isso após essas alterações terem sido feitas (somente arquivos de idioma inglês e espanhol foram adicionados):

  ![](localization-images/watchapp-solution.png "The watch app project with English and Spanish language files")

#### <a name="storyboard-text"></a>Texto do storyboard

Ao editar o storyboard, selecione cada elemento e observe a **ID de localização** que aparece no painel de **Propriedades** :

  [![](localization-images/storyboard-sml.png "The Localization ID that appears in the Properties pad")](localization-images/storyboard.png#lightbox)

Na pasta **base. lproj** , crie pares chave-valor, conforme mostrado abaixo, em que a chave é formada pela **ID de localização** e um nome de propriedade no controle, Unido por um ponto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Observe neste exemplo que uma **ID de localização** pode ser uma cadeia de caracteres de número simples (por exemplo, "0", "1", etc.) ou uma cadeia de caracteres mais complexa (como "AgC-eL-HGC"). `Label` controles têm uma propriedade `Text` e `Button`s têm uma propriedade `Title`, que é refletida na maneira como seus valores localizados são definidos-certifique-se de usar o nome da propriedade minúscula, conforme mostrado no exemplo acima.

Quando o storyboard é renderizado na inspeção, os valores corretos serão extraídos e exibidos automaticamente de acordo com o idioma selecionado pelo usuário.

#### <a name="storyboard-images"></a>Imagens de storyboard

A solução de exemplo também inclui uma imagem **gradient@2x.png** em cada pasta de idioma. Essa imagem pode ser diferente para cada idioma (por exemplo, Ele pode ter um texto inserido que precisa ser traduzido ou usar iconografia localizadas).

Basta definir a propriedade **Image** da imagem no storyboard e a imagem correta será renderizada no Watch de acordo com o idioma selecionado pelo usuário.

![](localization-images/storyboard-image.png "Set the images Image property in the storyboard")

Observação: como todos os relógios da Apple têm telas de retina, somente a versão **@2x** da imagem é necessária. Você não precisa especificar **@2x** no storyboard.

### <a name="watch-extension"></a>Extensão de inspeção

A extensão Watch requer uma estrutura de diretório semelhante para dar suporte à localização, no entanto, não há nenhum Storyboard. As cadeias de caracteres localizadas na extensão são apenas C# aquelas referenciadas pelo código.

![](localization-images/watchextension-solution.png "The watch extension directory structure to support localization")

#### <a name="strings-in-code"></a>Cadeias de caracteres no código

O arquivo **localizável. Strings** tem uma estrutura um pouco diferente do que se ele estava associado a um Storyboard. Nesse caso, podemos escolher qualquer cadeia de caracteres de "chave"; A recomendação da Apple é usar uma chave que reflita o texto real que seria exibido no idioma padrão:

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

O método `NSBundle.MainBundle.LocalizedString` é usado para resolver cadeias de caracteres em suas contrapartes convertidas, conforme mostrado no código abaixo.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Imagens no código

As imagens preenchidas pelo código podem ser definidas de duas maneiras.

1. Você pode alterar um controle de `Image` definindo seu valor como o nome da cadeia de caracteres de uma imagem que já existe no aplicativo Watch, por exemplo

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. Você pode mover uma imagem da extensão para a inspeção usando `FromBundle` e o aplicativo escolherá automaticamente a imagem correta para a seleção de idioma do usuário. Na solução de exemplo, há uma **language@2x.png** de imagem em cada pasta de idioma e ela é exibida em `DetailController` usando o seguinte código:

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    Observe que você não precisa especificar o **@2x** ao se referir ao nome de arquivo da imagem.

O segundo método também será aplicável se você baixar uma imagem de um servidor remoto para renderizar no relógio; no entanto, nesse caso, você deve garantir que a imagem baixada seja corretamente localizada de acordo com as preferências do usuário.

## <a name="localization"></a>Localização

Depois de configurar sua solução, os tradutores precisarão processar seus arquivos **. Strings** e imagens para cada idioma ao qual você deseja dar suporte.

Você pode criar quantos diretórios **. lproj** forem necessários (um para cada idioma com suporte). Eles são nomeados usando códigos de idioma, como **en**, **es**, **de**, **ja**, **pt-br**, etc. (para inglês, espanhol, alemão, japonês e Português (Brasil), respectivamente).

O exemplo anexado usa traduções (geradas por computador) para demonstrar como localizar um aplicativo watchOS.

### <a name="watch-app"></a>Inspecionar aplicativo

Esses valores são usados para converter a interface do usuário definida no storyboard do aplicativo de inspeção. O valor da *chave* é uma combinação de cada ID de **localização** de controle de storyboard e a propriedade que está sendo traduzida.

É recomendável adicionar comentários que contenham o texto original ao arquivo para que os tradutores saibam qual deve ser a tradução.

#### <a name="eslprojinterfacestrings"></a>es. lproj/interface. Strings

As cadeias de caracteres do idioma espanhol (traduzido pelo computador) para o storyboard são mostradas abaixo. É útil adicionar comentários a cada linha, pois é difícil saber qual **ID de localização** está se referindo, caso contrário:

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Extensão de inspeção

Esses valores são usados no código para converter informações antes de serem exibidas ao usuário. A *chave* é selecionada pelo desenvolvedor enquanto está gravando código e geralmente contém a cadeia de caracteres real a ser convertida.

#### <a name="eslprojlocalizablestrings-file"></a>arquivo es. lproj/localizável. Strings

As cadeias de caracteres do idioma Spansish (traduzido pelo computador):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Testes

O método para alterar as preferências de idioma difere entre o simulador e os dispositivos físicos.

### <a name="simulator"></a>Simulador

No simulador, selecione o idioma a ser testado usando o aplicativo de **configurações** do IOS (o ícone de engrenagens cinza na tela inicial do simulador).

  ![](localization-images/sim-settings-sml.png "The iOS Settings app Localization settings")

### <a name="watch-device"></a>Inspecionar dispositivo

Ao testar com uma inspeção, altere o idioma do observador no aplicativo **Apple Watch** no iPhone emparelhado.

  ![](localization-images/phone-settings-sml.png "Change the watch's language in the Apple Watch app on the paired iPhone")

## <a name="related-links"></a>Links relacionados

- [WatchLocalization (exemplo)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
