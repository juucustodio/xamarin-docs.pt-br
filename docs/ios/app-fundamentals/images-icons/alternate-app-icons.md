---
title: Ícones de aplicativo alternativos no Xamarin. iOS
description: Este documento descreve como usar ícones de aplicativo alternativos no Xamarin. iOS. Ele aborda como adicionar esses ícones a um projeto Xamarin. iOS, como modificar o arquivo info. plist e como gerenciar o ícone do aplicativo programaticamente.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 39a18a775946c2f139b4c032d2c360bc5680a0e7
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937911"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Ícones de aplicativo alternativos no Xamarin. iOS

_Este artigo aborda o uso de ícones de aplicativo alternativos no Xamarin. iOS._

A Apple adicionou vários aprimoramentos ao iOS 10,3 que permitem que um aplicativo Gerencie seu ícone:

- `ApplicationIconBadgeNumber`-Obtém ou define o emblema do ícone do aplicativo no Springboard.
- `SupportsAlternateIcons`-Se `true` o aplicativo tiver um conjunto alternativo de ícones.
- `AlternateIconName`-Retorna o nome do ícone alternativo selecionado no momento ou `null` se estiver usando o ícone primário.
- `SetAlternameIconName`-Use este método para alternar o ícone do aplicativo para o ícone alternativo fornecido.

![Um alerta de exemplo quando um aplicativo altera seu ícone](alternate-app-icons-images/icons04.png)

<a name="Adding-Alternate-Icons"></a>

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Adicionando ícones alternativos a um projeto do Xamarin. iOS

Para permitir que um aplicativo mude para um ícone alternativo, uma coleção de imagens de ícone precisará ser incluída no projeto de aplicativo Xamarin. iOS. Essas imagens não podem ser adicionadas ao projeto usando o `Assets.xcassets` método típico, elas devem ser adicionadas diretamente à pasta **recursos** .

Faça o seguinte:

1. Selecione as imagens de ícone necessárias em uma pasta, selecione tudo e arraste-as para a pasta **recursos** no **Gerenciador de soluções**:

    ![Selecionar os ícones imagens de uma pasta](alternate-app-icons-images/icons00.png)

2. Quando solicitado, selecione **copiar**, **use a mesma ação para todos os arquivos selecionados** e clique no botão **OK** :

    ![A caixa de diálogo Adicionar arquivo à pasta](alternate-app-icons-images/icons02.png)

3. A pasta de **recursos** deve ser semelhante ao seguinte quando concluída:

    ![A pasta de recursos deve ser parecida com esta](alternate-app-icons-images/icons01.png)

<a name="Modifying-the-Info.plist-File"></a>

## <a name="modifying-the-infoplist-file"></a>Modificando o arquivo info. plist

Com as imagens necessárias adicionadas à pasta **Resources** , a chave [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) precisará ser adicionada ao arquivo **info. plist** do projeto. Essa chave definirá o nome do novo ícone e as imagens que o compõem.

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Info.plist** para abri-lo para edição.
2. Alterne para a exibição **Origem**.
3. Adicione uma chave de **ícones de pacote** e deixe o **tipo** definido como **dicionário**.
4. Adicione uma `CFBundleAlternateIcons` chave e defina o **tipo** como **Dictionary**.
5. Adicione uma `AppIcon2` chave e defina o **tipo** como **Dictionary**. Esse será o nome do novo conjunto de ícones de aplicativo alternativo.
6. Adicionar uma `CFBundleIconFiles` chave e definir o **tipo** como **matriz**
7. Adicione uma nova cadeia de caracteres à `CFBundleIconFiles` matriz para cada arquivo de ícone deixando a extensão e `@2x` os `@3x` sufixos,, etc. (exemplo `100_icon` ). Repita essa etapa para cada arquivo que compõe o conjunto de ícones alternativo.
8. Adicione uma `UIPrerenderedIcon` chave ao `AppIcon2` dicionário, defina o **tipo** como **booliano** e o valor como **não**.
9. Salve as alterações no arquivo.

O arquivo **info. plist** resultante deve ser semelhante ao seguinte quando concluído:

![O arquivo info. plist concluído](alternate-app-icons-images/icons03.png)

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

<a name="Managing-the-Apps-Icon"></a>

## <a name="managing-the-apps-icon"></a>Gerenciando o ícone do aplicativo 

Com as imagens de ícone incluídas no projeto Xamarin. iOS e o arquivo **info. plist** corretamente configurados, o desenvolvedor pode usar um dos muitos novos recursos adicionados ao Ios 10,3 para controlar o ícone do aplicativo.

A `SupportsAlternateIcons` propriedade da `UIApplication` classe permite que o desenvolvedor veja se um aplicativo dá suporte a ícones alternativos. Por exemplo:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

A `ApplicationIconBadgeNumber` propriedade da `UIApplication` classe permite que o desenvolvedor obtenha ou defina o número de notificação atual do ícone do aplicativo no Springboard. O valor padrão é zero (0). Por exemplo:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

A `AlternateIconName` propriedade da `UIApplication` classe permite que o desenvolvedor obtenha o nome do ícone do aplicativo alternativo selecionado no momento ou que ele retorne `null` se o aplicativo estiver usando o ícone primário. Por exemplo:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

A `SetAlternameIconName` propriedade da `UIApplication` classe permite que o desenvolvedor altere o ícone do aplicativo. Passe o nome do ícone para selecionar ou `null` para retornar ao ícone principal. Por exemplo:

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

![Um alerta de exemplo quando um aplicativo altera seu ícone](alternate-app-icons-images/icons04.png)

Se o usuário voltar ao ícone primário, um alerta semelhante ao seguinte será exibido:

![Um alerta de exemplo quando um aplicativo é alterado para o ícone primário](alternate-app-icons-images/icons05.png)

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou a adição de ícones de aplicativo alternativos a um projeto Xamarin. iOS e usá-los dentro do aplicativo.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
