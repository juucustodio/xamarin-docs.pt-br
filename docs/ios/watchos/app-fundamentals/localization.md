---
title: Trabalhando com watchOS localização em Xamarin
description: Este documento descreve como localizar watchOS aplicativos criados com o Xamarin. Ele discute aplicativos watch, extensões de inspeção de storyboard cadeias de caracteres no código, texto, teste e muito mais.
ms.prod: xamarin
ms.assetid: 55834877-757B-4860-AF2F-933A948BE38D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 4f158f1c8699ad5090eb7fade8af8918c8881d95
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790773"
---
# <a name="working-with-watchos-localization-in-xamarin"></a>Trabalhando com watchOS localização em Xamarin

_Adaptar seus aplicativos watchOS para vários idiomas_

![](localization-images/both-languages-sml.png "Apple Watch exibindo conteúdo localizado")

watchOS aplicativos estão localizados usando os métodos padrão do iOS:

- Usando **identificação da localização** em elementos de storyboard,
- **.strings** arquivos associados do storyboard, e
- **Localizable.strings** arquivos de texto usado no código.

Os storyboards padrão e os recursos estão localizados em um **Base** directory e traduções de idioma específico e outros recursos são armazenados em **.lproj** diretórios.
iOS e inspecionar OS automaticamente usará seleção do idioma do usuário para carregar as cadeias de caracteres corretas e os recursos.

Como um aplicativo Apple Watch tem duas partes: App inspecionar e inspecionar extensão - cadeia de caracteres localizada recursos são necessários em dois locais, dependendo de como eles são usados.

O texto localizado e recursos serão *diferentes* no aplicativo inspeção e a extensão de inspeção.

## <a name="watch-app"></a>Aplicativo de inspeção

O aplicativo de inspeção contém o storyboard que descreve a interface do usuário do aplicativo. Todos os controles (como `Label` e `Image`) que têm suporte de localização um **identificação da localização**.

Cada idioma específico **.lproj** diretório deve conter **.strings** arquivos com as traduções para cada elemento (usando o **identificação da localização**), bem como imagens referenciado por storyboard.

## <a name="watch-extension"></a>Assista a extensão

A extensão de inspeção é onde o código do aplicativo é executado. Qualquer texto que é exibido para o usuário do código precisa ser localizado na extensão e não no aplicativo de inspeção.

A extensão também deve conter específico do idioma **.lproj** diretórios, mas o **.strings** arquivos exigem apenas traduções para o texto que é usado em seu código.

## <a name="globalizing-the-watch-solution"></a>Globalizando a solução de inspeção

Globalização é o processo de criação de um aplicativo localizável.
Para aplicativos de observar que isso significa criar o storyboard com diferentes tamanhos de texto em mente, garantir cada layout da tela ajusta adequadamente dependendo de qual o texto é exibido. Você também precisa garantir que cadeias de caracteres referenciadas no código de extensão inspecionar podem ser convertidas usando o `LocalizedString` método.

### <a name="watch-app"></a>Aplicativo de inspeção

Por padrão, o aplicativo watch não está configurado para a localização. Você precisa mover o arquivo de storyboard padrão e criar alguns outros diretórios para suas traduções:

1. Criar **Base.lproj** diretório e mover o **Interface.storyboard** nele.

2. Criar  **<language>.lproj** diretórios para cada idioma que você deseja dar suporte.

3. O **.lproj** diretórios devem conter um **Interface.strings** arquivo de texto (o nome do arquivo deve corresponder ao nome da storboard). Opcionalmente, você pode colocar qualquer imagem que requerem localização nesses diretórios.

O projeto de aplicativo de inspeção tem esta aparência após essas alterações foram feitas (somente em inglês e espanhol idioma arquivos foram adicionados):

  ![](localization-images/watchapp-solution.png "O projeto de aplicativo de inspeção com arquivos de idioma inglês e espanhol")

#### <a name="storyboard-text"></a>Texto do storyboard

Quando você edita o storyboard, selecione cada elemento e observe o **identificação da localização** que aparece no **propriedades** teclado:

  [![](localization-images/storyboard-sml.png "A ID de localização que aparece no painel de propriedades")](localization-images/storyboard.png#lightbox)

No **Base.lproj** pasta, criar pares chave-valor, conforme mostrado abaixo, onde a chave é formada pelo **identificação da localização** e um nome de propriedade do controle, unidas por um ponto (`.`).

```csharp
"AgC-eL-Hgc.title" = "WatchL10nEN"; // interface controller title
"0.text" = "Welcome to WatchL10n"; // Welcome
"1.text" = "Language settings are in Apple Watch App"; // How to change language
"2.title" = "Greetings"; // Greeting
"6.title" = "Detail";
"39.text" = "Second screen";
```

Observe neste exemplo que um **identificação da localização** pode ser uma cadeia simples número (por exemplo. "0", "1", etc) ou uma cadeia de caracteres mais complexa (como "controle automático de ganho-eL-Hgc"). `Label` controles têm um `Text` propriedade e `Button`s têm uma `Title` propriedade, que é refletida na maneira como conjunto de seus valores localizados - Certifique-se de usar o nome da propriedade em minúsculas, conforme mostrado no exemplo acima.

Quando o storyboard é renderizado no relógio, os valores corretos serão automaticamente extraídos e exibidos de acordo com o idioma selecionado pelo usuário.

#### <a name="storyboard-images"></a>Imagens de storyboard

A solução de exemplo também inclui um **gradient@2x.png** imagem em cada pasta de idioma. Essa imagem pode ser diferente para cada idioma (por exemplo. pode ter inserido texto que precisa de conversão, ou use localizado iconografia).

Basta definir a imagem **imagem** propriedade no storyboard e a imagem correta será renderizada no relógio de acordo com o idioma selecionado pelo usuário.

![](localization-images/storyboard-image.png "Defina as imagens de propriedade de imagem no storyboard")

Observação: porque todas as inspeções Apple tem exibe Retina, somente o **@2x** versão da imagem é necessária. Você não precisa especificar **@2x** no storyboard.

### <a name="watch-extension"></a>Assista a extensão

A extensão de inspecionar requer uma estrutura de diretórios semelhante para dar suporte a localização, embora não haja nenhum storyboard. As cadeias de caracteres localizadas na extensão são apenas aqueles que referenciados pelo código do c#.

![](localization-images/watchextension-solution.png "A estrutura de diretórios de extensão de inspeção para oferecer suporte à localização")

#### <a name="strings-in-code"></a>Cadeias de caracteres no código

O **Localizable.strings** arquivo tem uma estrutura ligeiramente diferente se ele foi associado um storyboard. Nesse caso, pode escolher qualquer cadeia de caracteres "chave"; Recomendação da Apple é usar uma chave que reflete o texto real que seria exibido no idioma padrão:

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

1. Você pode alterar um `Image` controle definindo seu valor como o nome de cadeia de caracteres de uma imagem que já existe no aplicativo Watch, por exemplo,

  ```csharp
  displayImage.SetImage("gradient"); // image in Watch App (as shown above)
  ```

2. Você pode mover uma imagem da extensão para a inspeção usando `FromBundle` e o aplicativo escolherá automaticamente a imagem correta para a seleção do idioma do usuário. Na solução de exemplo é uma imagem **language@2x.png** em cada idioma pasta e ele é exibido em `DetailController` usando o seguinte código:

  ```csharp
  using (var image = UIImage.FromBundle ("language")) {
    displayImage.SetImage (image);
  }
  ```

  Observe que você não precisa especificar o **@2x** ao fazer referência ao nome do arquivo da imagem.

O segundo método também é aplicável se você baixar uma imagem de um servidor remoto para renderizar na inspeção; No entanto nesse caso você deve garantir que a imagem que você baixar é localizada corretamente de acordo com as preferências do usuário.

## <a name="localization"></a>Localização

Depois que você configurou sua solução, serão necessário processar tradutores seu **.strings** arquivos e as imagens para cada idioma que você deseja dar suporte.

Você pode criar tantas **.lproj** diretórios que você precisam (uma para cada idioma com suporte). Elas são nomeadas usando códigos de idioma, como **en**, **es**, **Ir**, **ja**, **pt-BR**, etc. (para inglês Espanhol, alemão, japonês e português (Brasil), respectivamente).

O exemplo anexado usa traduções (geradas por computador) para demonstrar como localizar um aplicativo watchOS.

### <a name="watch-app"></a>Aplicativo de inspeção

Esses valores são usados para traduzir a interface do usuário definida no storyboard de inspeção do aplicativo. O *chave* valor é uma combinação de cada controle de storyboard **identificação da localização** e a propriedade que está sendo convertido.

É recomendável adicionar comentários que contém o texto original para o arquivo para que os tradutores saibam o que deve ser a tradução.

#### <a name="eslprojinterfacestrings"></a>es.lproj/Interface.strings

As cadeias de caracteres de idioma espanhol (traduzido) para o storyboard são mostradas abaixo. É útil adicionar comentários para cada linha, pois é difícil saber o que o **identificação da localização** se refere a outra forma:

```csharp
"AgC-eL-Hgc.title" = "Spanish"; // app screen heading
"0.text" = "Bienvenido a WatchL10n"; // Welcome to WatchL10n
"1.text" = "Ajustes de idioma están en Apple Watch App"; // Change the language in the Apple Watch App
"2.title" = "Saludos"; // Greetings
"6.title" = "2nd"; // second screen heading
"39.text" = "Segunda pantalla"; // second screen
```

### <a name="watch-extension"></a>Assista a extensão

Esses valores são usados no código para converter as informações antes de ser exibido para o usuário. O *chave* é selecionada pelo desenvolvedor enquanto ele estiver escrevendo um código e geralmente contém a cadeia de caracteres real a ser convertido.

#### <a name="eslprojlocalizablestrings-file"></a>arquivo es.lproj/Localizable.strings

As cadeias de caracteres de idioma Spansish (traduzido):

```csharp
"Breakfast time" = "la hora del desayuno"; // morning
"Lunch time" = "hora de comer"; // midday
"Dinner time" = "hora de la cena"; // evening
"Bed time" = "la hora de dormir"; // night
```

## <a name="testing"></a>Testes

O método para alterar as preferências de idioma difere entre o simulator e dispositivos físicos.

### <a name="simulator"></a>Simulador

No simulador, selecione o idioma para testar o uso do iOS **configurações** aplicativo (o ícone cinza engrenagens na tela inicial do simulador).

  ![](localization-images/sim-settings-sml.png "As configurações de localização do aplicativo do iOS")

### <a name="watch-device"></a>Dispositivo de inspeção

Durante o teste com um relógio, alterar o idioma do relógio no **Apple Watch** aplicativo no iPhone emparelhado.

  ![](localization-images/phone-settings-sml.png "Alterar idioma do relógio no aplicativo do Apple Watch no iPhone emparelhado")



## <a name="related-links"></a>Links relacionados

- [WatchLocalization (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchLocalization/)
