---
title: Trabalhando com o watchOS localização no Xamarin
description: Este documento descreve como localizar aplicativos watchOS criados com o Xamarin. Ele discute aplicativos do watch, extensões de inspeção de storyboard cadeias de caracteres no código, texto, teste e muito mais.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 10f240a8e245f24d4b8f646eb972cbe21d28b75c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289934"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Trabalhando com o watchOS localização no Xamarin

_Adaptando os aplicativos watchOS para vários idiomas_

![](localization-images/both-languages-sml.png "Apple Watch exibindo o conteúdo localizado")

aplicativos watchOS são localizados usando os métodos padrão do iOS:

- Usando o **ID de localização** em elementos de storyboard
- **.strings** arquivos associados ao storyboard, e
- **Localizable.strings** arquivos de texto usado no código.

Os storyboards padrão e os recursos estão localizados em uma **Base** diretório e traduções de idioma específico e outros recursos são armazenados em **.lproj** diretórios.
iOS e o sistema operacional de inspeção automaticamente usará seleção do idioma do usuário para carregar as cadeias de caracteres corretas e os recursos.

Como um aplicativo de Apple Watch tem duas partes - aplicativo de inspeção e a extensão de inspeção - cadeia de caracteres localizada recursos são necessários em dois locais, dependendo de como eles são usados.

O texto localizado e recursos serão *diferentes* no aplicativo de inspeção e a extensão de inspeção.

## <a name="watch-app"></a>Aplicativo de inspeção

O aplicativo watch contém o storyboard que descreve a interface do usuário do aplicativo. Todos os controles (como `Label` e `Image`) que o suporte de localização têm uma **ID de localização**.

Cada idioma específico **.lproj** diretório deve conter **.strings** arquivos com as traduções para cada elemento (usando o **ID de localização**), bem como imagens referenciado pelo storyboard.

## <a name="watch-extension"></a>Extensão de inspeção

A extensão de inspeção de variáveis é onde o código do aplicativo é executado. Qualquer texto que é exibido ao usuário do código deve ser localizada na extensão e não no aplicativo de inspeção.

A extensão também deve conter específico do idioma **.lproj** diretórios, mas o **.strings** arquivos exigem apenas traduções para o texto que é usado em seu código.

## <a name="globalizing-the-watch-solution"></a>Globalizando a solução de inspeção

Globalização é o processo de criação de um aplicativo localizável.
Para aplicativos do watch que isso significa desenvolver o storyboard com comprimentos de texto diferentes em mente, garantir que cada layout de tela ajusta adequadamente dependendo do que o texto é exibido. Você também precisa garantir que qualquer referenciadas no código de extensão de inspeção de cadeias de caracteres podem ser convertidas usando o `LocalizedString` método.

### <a name="watch-app"></a>Aplicativo de inspeção

Por padrão, o aplicativo watch não está configurado para a localização. Você precisará mover o arquivo de storyboard padrão e criar alguns outros diretórios para suas traduções:

1. Crie **Base.lproj** diretório e mova as **Interface.storyboard** nele.

2. Crie o idioma > diretórios  **.lprojparacadaidiomaaoqualvocêdesejadarsuporte\<** .

3. O **.lproj** diretórios devem conter um **Interface.strings** arquivo de texto (o nome do arquivo deve corresponder ao nome do storboard). Opcionalmente, você pode colocar todas as imagens que requerem localização nesses diretórios.

O projeto de aplicativo de inspeção se parece com isso depois que essas alterações foram feitas (apenas em inglês e espanhol linguagem arquivos foram adicionados):

  ![](localization-images/watchapp-solution.png "O projeto de aplicativo de inspeção com arquivos de idioma inglês e espanhol")

#### <a name="storyboard-text"></a>Texto de storyboard

Quando você edita o storyboard, selecione cada elemento e observe que o **ID de localização** que aparece na **propriedades** painel:

  [![](localization-images/storyboard-sml.png "A ID de localização que aparece no painel de propriedades")](localization-images/storyboard.png#lightbox)

No **Base.lproj** pasta, crie pares chave-valor, conforme mostrado abaixo, em que a chave é formada pela **ID de localização** e um nome de propriedade no controle, unidas por um ponto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Observe que neste exemplo que um **ID de localização** pode ser uma simple cadeia numérica (por exemplo. "0", "1", etc) ou uma cadeia de caracteres mais complexa (por exemplo, "controle automático de ganho-eL-Hgc"). `Label` controles têm uma `Text` propriedade e `Button`s têm uma `Title` propriedade, que é refletida na maneira como conjunto de seus valores localizados - Certifique-se de usar o nome da propriedade em minúsculas, conforme mostrado no exemplo acima.

Quando o storyboard é renderizado no watch, os valores corretos serão automaticamente extraídos e exibidos de acordo com o idioma selecionado pelo usuário.

#### <a name="storyboard-images"></a>Imagens de storyboard

A solução de exemplo também inclui um **gradient@2x.png** imagem em cada pasta de idioma. Essa imagem pode ser diferente para cada idioma (por exemplo. pode ter inserido texto que precisa converter ou use localizadas iconografia).

Basta definir a imagem **imagem** propriedade no storyboard e a imagem correta será renderizada no relógio de acordo com o idioma selecionado pelo usuário.

![](localization-images/storyboard-image.png "Defina as imagens de propriedade de imagem no storyboard")

Observação: como todos os Apple Watches têm exibe Retina, somente o **@2x** versão da imagem é necessária. Você não precisará especificar **@2x** no storyboard.

### <a name="watch-extension"></a>Extensão de inspeção

A extensão de inspeção exige uma estrutura de diretórios semelhante para dar suporte à localização, embora não haja nenhum storyboard. As cadeias de caracteres localizadas na extensão são apenas aquelas referenciadas por C# código.

![](localization-images/watchextension-solution.png "A estrutura de diretórios de extensão de inspeção para dar suporte à localização")

#### <a name="strings-in-code"></a>Cadeias de caracteres no código

O **Localizable.strings** arquivo tem uma estrutura ligeiramente diferente se ela foi associada um storyboard. Nesse caso, podemos escolher qualquer cadeia de caracteres "chave"; Recomendação da Apple é usar uma chave que reflete o texto real que seria exibido no idioma padrão:

```csharp
"Breakfast time" = "Breakfast time!"; // morning
"Lunch time" = "Lunch time!"; // midday
"Dinner time" = "Dinner time!"; // evening
"Bed time" = "Bed time!"; // night
```

O `NSBundle.MainBundle.LocalizedString` método é usado para resolver cadeias de caracteres em suas contrapartes traduzidos, conforme mostrado no código a seguir.

```csharp
var display = "Breakfast time";
var localizedDisplay =
  NSBundle.MainBundle.LocalizedString (display, comment:"greeting");
displayText.SetText (localizedDisplay);
```

#### <a name="images-in-code"></a>Imagens em código

Imagens que são preenchidas, o código podem ser definidas de duas maneiras.

1. Você pode alterar um `Image` controle definindo seu valor como o nome da cadeia de caracteres de uma imagem que já existe no aplicativo Watch, por exemplo

    ```csharp
    displayImage.SetImage("gradient"); // image in Watch App (as shown above)
    ```

2. Você pode mover uma imagem da extensão para o watch usando `FromBundle` e o aplicativo escolherá automaticamente a imagem correta para a seleção do idioma do usuário. Na solução de exemplo é uma imagem **language@2x.png** em cada linguagem pasta e ele é exibido em `DetailController` usando o seguinte código:

    ```csharp
    using (var image = UIImage.FromBundle ("language")) {
        displayImage.SetImage (image);
    }
    ```

    Observe que você não precisa especificar o **@2x** ao fazer referência ao nome de arquivo da imagem.

O segundo método também é aplicável se você baixar uma imagem de um servidor remoto para renderizar no watch; No entanto nesse caso, você deve garantir que a imagem que você baixe corretamente é localizada de acordo com as preferências do usuário.

## <a name="localization"></a>Localização

Depois de configurar sua solução, serão necessário processar os tradutores sua **.strings** arquivos e imagens para cada idioma que você deseja dar suporte.

Você pode criar tantos **.lproj** diretórios que você precisam (uma para cada idioma com suporte). Elas são nomeadas usando códigos de idioma, como **en**, **es**, **Alemanha**, **ja**, **pt-BR**, etc. (para inglês Espanhol, alemão, japonês e português (Brasil), respectivamente).

O exemplo anexado usa traduções (gerado pelo computador) para demonstrar como localizar um aplicativo watchOS.

### <a name="watch-app"></a>Aplicativo de inspeção

Esses valores são usados para traduzir a interface do usuário definida no storyboard do aplicativo watch. O *chave* valor é uma combinação de cada controle de storyboard **ID de localização** e a propriedade que está sendo convertido.

É recomendável adicionar comentários que contém o texto original para o arquivo para que os tradutores saibam o que deve ser a tradução.

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

As cadeias de caracteres de idioma espanhol (traduzido por máquina) para o storyboard são mostradas abaixo. É útil adicionar comentários a cada linha, pois é difícil saber o que o **ID de localização** fizer referência a outra forma:

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Extensão de inspeção

Esses valores são usados no código para converter as informações antes de serem exibidos ao usuário. O *chave* está selecionada pelo desenvolvedor enquanto eles agora escrevem código e geralmente contém a cadeia de caracteres real a ser traduzido.

#### <a name="eslprojlocalizablestrings-file"></a>arquivo es.lproj/Localizable.strings

As cadeias de caracteres de idioma Spansish (traduzido por máquina):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Testes

O método para alterar as preferências de idioma é diferente entre o simulador e dispositivos físicos.

### <a name="simulator"></a>Simulador

No simulador, selecione o idioma para testar o uso do iOS **configurações** aplicativo (o ícone de engrenagens cinza na tela inicial do simulador).

  ![](localization-images/sim-settings-sml.png "O configurações de localização do aplicativo de configurações do iOS")

### <a name="watch-device"></a>Dispositivo de inspeção

Durante o teste com uma inspeção, mude o idioma do relógio na **Apple Watch** aplicativo no iPhone emparelhado.

  ![](localization-images/phone-settings-sml.png "Alterar idioma do relógio no aplicativo do Apple Watch no iPhone emparelhado")



## <a name="related-links"></a>Links relacionados

- [WatchLocalization (amostra)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchLocalization/)
