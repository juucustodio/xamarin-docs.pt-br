---
title: Ícones de aplicativos alternativo no xamarin. IOS
description: Este documento descreve como usar os ícones de aplicativos alternativo no xamarin. IOS. Ele aborda como adicionar esses ícones para um projeto xamarin. IOS, como modificar o arquivo Info. plist e como gerenciar o ícone do aplicativo de forma programática.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: cc5052c8988a27605cf7680a3853f80e7afd38b7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105487"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Ícones de aplicativos alternativo no xamarin. IOS

_Este artigo aborda usando os ícones de aplicativos alternativo no xamarin. IOS._

Apple adicionou várias melhorias para o iOS 10.3 que permitem que um aplicativo gerenciar seu ícone:

 - `ApplicationIconBadgeNumber` -Obtém ou define o selo do ícone do aplicativo em do Springboard.
 - `SupportsAlternateIcons` -Se `true` o aplicativo tem um conjunto alternativo de ícones.
 - `AlternateIconName` -Retorna o nome do ícone alternativo selecionado no momento ou `null` se usando o ícone principal.
 - `SetAlternameIconName` -Use esse método para alternar o ícone do aplicativo para o ícone alternativo especificado.

![](alternate-app-icons-images/icons04.png "Um exemplo do alerta quando um aplicativo altera seu ícone")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Adicionando ícones alternativos para um projeto xamarin. IOS

Para permitir que um aplicativo alternar para um ícone alternativo, uma coleção de imagens de ícone precisará ser incluído no projeto do aplicativo xamarin. IOS. Essas imagens não podem ser adicionadas ao projeto usando o típico `Assets.xcassets` método, eles devem ser adicionados para o **recursos** pasta diretamente.

Faça o seguinte:

1. Selecione as imagens de ícone necessária em uma pasta, selecionar tudo e arraste-os para o **recursos** pasta o **Gerenciador de soluções**:

    ![](alternate-app-icons-images/icons00.png "Selecione as imagens de ícones em uma pasta")

2. Quando solicitado, selecione **cópia**, **usar a mesma ação para todos os arquivos selecionados** e clique no **Okey** botão:

    ![](alternate-app-icons-images/icons02.png "Adicionar arquivo à caixa de diálogo de pasta")

3. O **recursos** pasta deve ser semelhante ao seguinte quando concluído:

    ![](alternate-app-icons-images/icons01.png "A pasta de recursos deve ter esta aparência")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modificando o arquivo Info. plist

Com as imagens necessárias adicionadas para o **recursos** pasta, o [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) chave precisará ser adicionado ao projeto do **Info. plist** arquivo. Essa chave será definir o nome do novo ícone e as imagens que o compõem.

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Info.plist** para abri-lo para edição.
2. Alterne para o **origem** modo de exibição.
3. Adicionar um **ícones de pacote** da chave e deixe o **tipo** definido como **dicionário**.
4. Adicionar um `CFBundleAlternateIcons` da chave e defina o **tipo** à **dicionário**.
5. Adicionar um `AppIcon2` da chave e defina o **tipo** à **dicionário**. Esse será o nome do novo conjunto de ícone do aplicativo alternativo.
6. Adicionar um `CFBundleIconFiles` da chave e defina o **tipo** para **matriz**
7. Adicionar uma nova cadeia de caracteres para o `CFBundleIconFiles` matriz para cada arquivo de ícone omitindo a extensão e o `@2x`, `@3x`, sufixos etc. (exemplo `100_icon`). Repita essa etapa para todos os arquivos que compõe o conjunto de ícone alternativo.
8. Adicionar um `UIPrerenderedIcon` chave para o `AppIcon2` dicionário, defina o **tipo** para **booliano** e o valor a ser **não**.
9. Salve as alterações no arquivo.

Resultante **Info. plist** arquivo deve ser semelhante à seguinte quando concluído:

![](alternate-app-icons-images/icons03.png "O arquivo Info. plist concluído")

Ou assim, se aberto em um editor de texto:

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

Com as imagens de ícone incluídas no projeto xamarin. IOS e o **Info. plist** arquivo configurado corretamente, o desenvolvedor pode usar um dos muitos novos recursos adicionados para o iOS 10.3 para controlar o ícone do aplicativo.

O `SupportsAlternateIcons` propriedade do `UIApplication` classe permite que o desenvolvedor se um aplicativo dá suporte a ícones alternativos. Por exemplo:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

O `ApplicationIconBadgeNumber` propriedade do `UIApplication` classe permite que o desenvolvedor obter ou definir o número atual de notificação do ícone do aplicativo do Springboard. O valor padrão é zero (0). Por exemplo:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

O `AlternateIconName` propriedade do `UIApplication` classe permite que o desenvolvedor obter o nome do ícone do aplicativo alternativo selecionado no momento ou retorna `null` se o aplicativo estiver usando o ícone principal. Por exemplo:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

O `SetAlternameIconName` propriedade do `UIApplication` classe permite que o desenvolvedor alterar o ícone do aplicativo. Passe o nome do ícone para selecionar ou `null` para retornar para o ícone principal. Por exemplo:

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

Quando o aplicativo é executado e o usuário selecionar um ícone alternativo, será exibido um alerta semelhante ao seguinte:

![](alternate-app-icons-images/icons04.png "Um exemplo do alerta quando um aplicativo altera seu ícone")

Se o usuário alterna de volta para o ícone principal, será exibido um alerta semelhante ao seguinte:

![](alternate-app-icons-images/icons05.png "Um exemplo do alerta quando um aplicativo é alterado para o ícone principal")

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo cobriu adicionando ícones do aplicativo alternativo para um projeto xamarin. IOS e usá-los dentro do aplicativo.



## <a name="related-links"></a>Links relacionados

- [Exemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
