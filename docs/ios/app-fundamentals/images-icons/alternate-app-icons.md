---
title: Ícones de aplicativo alternativos no Xamarin. iOS
description: Este documento descreve como usar ícones de aplicativo alternativos no Xamarin. iOS. Ele aborda como adicionar esses ícones a um projeto Xamarin. iOS, como modificar o arquivo info. plist e como gerenciar o ícone do aplicativo programaticamente.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: ed31f1dca3f823ccd0374b4fcbac1bbd9e80e022
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004310"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Ícones de aplicativo alternativos no Xamarin. iOS

_Este artigo aborda o uso de ícones de aplicativo alternativos no Xamarin. iOS._

A Apple adicionou vários aprimoramentos ao iOS 10,3 que permitem que um aplicativo Gerencie seu ícone:

- `ApplicationIconBadgeNumber`-Obtém ou define o emblema do ícone do aplicativo no Springboard.
- `SupportsAlternateIcons`-se `true` o aplicativo tem um conjunto alternativo de ícones.
- `AlternateIconName`-retorna o nome do ícone alternativo selecionado no momento ou `null` se estiver usando o ícone principal.
- `SetAlternameIconName`-Use esse método para alternar o ícone do aplicativo para o ícone alternativo fornecido.

![](alternate-app-icons-images/icons04.png "A sample alert when an app changes its icon")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Adicionando ícones alternativos a um projeto do Xamarin. iOS

Para permitir que um aplicativo mude para um ícone alternativo, uma coleção de imagens de ícone precisará ser incluída no projeto de aplicativo Xamarin. iOS. Essas imagens não podem ser adicionadas ao projeto usando o método `Assets.xcassets` típico, elas devem ser adicionadas diretamente à pasta **recursos** .

Faça o seguinte:

1. Selecione as imagens de ícone necessárias em uma pasta, selecione tudo e arraste-as para a pasta **recursos** no **Gerenciador de soluções**:

    ![](alternate-app-icons-images/icons00.png "Select the icons images from a folder")

2. Quando solicitado, selecione **copiar**, **use a mesma ação para todos os arquivos selecionados** e clique no botão **OK** :

    ![](alternate-app-icons-images/icons02.png "The Add File to Folder dialog box")

3. A pasta de **recursos** deve ser semelhante ao seguinte quando concluída:

    ![](alternate-app-icons-images/icons01.png "The Resources folder should look like this")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modificando o arquivo info. plist

Com as imagens necessárias adicionadas à pasta **Resources** , a chave [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) precisará ser adicionada ao arquivo **info. plist** do projeto. Essa chave definirá o nome do novo ícone e as imagens que o compõem.

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Info.plist** para abri-lo para edição.
2. Alterne para o modo de exibição de **origem** .
3. Adicione uma chave de **ícones de pacote** e deixe o **tipo** definido como **dicionário**.
4. Adicione uma chave de `CFBundleAlternateIcons` e defina o **tipo** como **Dictionary**.
5. Adicione uma chave de `AppIcon2` e defina o **tipo** como **Dictionary**. Esse será o nome do novo conjunto de ícones de aplicativo alternativo.
6. Adicionar uma chave de `CFBundleIconFiles` e definir o **tipo** como **matriz**
7. Adicione uma nova cadeia de caracteres à matriz de `CFBundleIconFiles` para cada arquivo de ícone deixando a extensão e o `@2x`, `@3x`, etc. sufixos (exemplo `100_icon`). Repita essa etapa para cada arquivo que compõe o conjunto de ícones alternativo.
8. Adicione uma chave de `UIPrerenderedIcon` ao dicionário de `AppIcon2`, defina o **tipo** como **booliano** e o valor como **não**.
9. Salve as alterações no arquivo.

O arquivo **info. plist** resultante deve ser semelhante ao seguinte quando concluído:

![](alternate-app-icons-images/icons03.png "The completed Info.plist file")

Ou assim, se for aberto em um editor de texto:

```xml
<key>CFBundleIcons</key>
<dict>
    <key>CFBundleAlternateIcons</key>
    <dict>
        <key>AppIcon2</key>
        <dict>
            <key>CFBundleIconFiles</key>
            <array>
                <string>100_icon</string>
                <string>114_icon</string>
                <string>120_icon</string>
                <string>144_icon</string>
                <string>152_icon</string>
                <string>167_icon</string>
                <string>180_icon</string>
                <string>29_icon</string>
                <string>40_icon</string>
                <string>50_icon</string>
                <string>512_icon</string>
                <string>57_icon</string>
                <string>58_icon</string>
                <string>72_icon</string>
                <string>76_icon</string>
                <string>80_icon</string>
                <string>87_icon</string>
            </array>
            <key>UIPrerenderedIcon</key>
            <false/>
        </dict>
    </dict>
</dict>
```

<a name="Managing-the-Apps-Icon" />

## <a name="managing-the-apps-icon"></a>Gerenciando o ícone do aplicativo 

Com as imagens de ícone incluídas no projeto Xamarin. iOS e o arquivo **info. plist** corretamente configurados, o desenvolvedor pode usar um dos muitos novos recursos adicionados ao Ios 10,3 para controlar o ícone do aplicativo.

A propriedade `SupportsAlternateIcons` da classe `UIApplication` permite que o desenvolvedor veja se um aplicativo dá suporte a ícones alternativos. Por exemplo:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

A propriedade `ApplicationIconBadgeNumber` da classe `UIApplication` permite que o desenvolvedor obtenha ou defina o número de notificação atual do ícone do aplicativo no Springboard. O valor padrão é zero (0). Por exemplo:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

A propriedade `AlternateIconName` da classe `UIApplication` permite que o desenvolvedor obtenha o nome do ícone do aplicativo alternativo selecionado no momento ou que ele retorne `null` se o aplicativo estiver usando o ícone primário. Por exemplo:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

A propriedade `SetAlternameIconName` da classe `UIApplication` permite que o desenvolvedor altere o ícone do aplicativo. Passe o nome do ícone para selecionar ou `null` para retornar ao ícone principal. Por exemplo:

```csharp
partial void UsePrimaryIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName (null, (err) => {
        Console.WriteLine ("Set Primary Icon: {0}", err);
    });
}

partial void UseAlternateIcon (Foundation.NSObject sender)
{
    UIApplication.SharedApplication.SetAlternateIconName ("AppIcon2", (err) => {
        Console.WriteLine ("Set Alternate Icon: {0}", err);
    });
}
```

Quando o aplicativo é executado e o usuário seleciona um ícone alternativo, um alerta semelhante ao seguinte será exibido:

![](alternate-app-icons-images/icons04.png "A sample alert when an app changes its icon")

Se o usuário voltar ao ícone primário, um alerta semelhante ao seguinte será exibido:

![](alternate-app-icons-images/icons05.png "A sample alert when an app changes to the primary icon")

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a adição de ícones de aplicativo alternativos a um projeto Xamarin. iOS e usá-los dentro do aplicativo.

## <a name="related-links"></a>Links relacionados

- [Exemplo de iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
