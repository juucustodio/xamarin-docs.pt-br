---
title: Ícones de aplicativo alternativo em xamarin
description: Este documento descreve como usar os ícones de aplicativo alternativo em xamarin. Ele discute como adicionar esses ícones para um projeto de xamarin, como modificar o arquivo Info. plist e como gerenciar programaticamente o ícone do aplicativo.
ms.prod: xamarin
ms.assetid: 302fa818-33b9-4ea1-ab63-0b2cb312299a
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 1d37a29982454367c35bfdfad205abce0eb025af
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784104"
---
# <a name="alternate-app-icons-in-xamarinios"></a>Ícones de aplicativo alternativo em xamarin

_Este artigo aborda usando ícones de aplicativo alternativo no xamarin._

Apple tenha adicionado vários aprimoramentos para o iOS 10.3 que permitem que um aplicativo gerenciar seu ícone:

 - `ApplicationIconBadgeNumber` -Obtém ou define a notificação do ícone de aplicativo no Springboard.
 - `SupportsAlternateIcons` -Se `true` o aplicativo tem um conjunto alternativo de ícones.
 - `AlternateIconName` -Retorna o nome do ícone alternativo selecionado no momento ou `null` se usando o ícone do primário.
 - `SetAlternameIconName` -Use esse método para alternar o ícone do aplicativo para o ícone alternativo fornecido.

![](alternate-app-icons-images/icons04.png "Um alerta de exemplo quando um aplicativo altera seu ícone")

<a name="Adding-Alternate-Icons" />

## <a name="adding-alternate-icons-to-a-xamarinios-project"></a>Adicionando ícones alternativos para um projeto do xamarin

Para permitir que um aplicativo alternar para um ícone alternativo, uma coleção de imagens do ícone precisa ser incluído no projeto de aplicativo xamarin. Essas imagens não podem ser adicionadas ao projeto usando o típico `Assets.xcassets` método, eles devem ser adicionados para o **recursos** pasta diretamente.

Faça o seguinte:

1. Selecione as imagens do ícone obrigatório em uma pasta, selecionar tudo e arraste-os para o **recursos** pasta o **Solution Explorer**:

    ![](alternate-app-icons-images/icons00.png "Selecione as imagens de ícones de uma pasta")

2. Quando solicitado, selecione **cópia**, **usar a mesma ação para todos os arquivos selecionados** e clique no **Okey** botão:

    ![](alternate-app-icons-images/icons02.png "O arquivo de adicionar a caixa de diálogo de pasta")

3. O **recursos** pasta deve ser semelhante ao seguinte quando concluída:

    ![](alternate-app-icons-images/icons01.png "A pasta de recursos deve ter esta aparência")

<a name="Modifying-the-Info.plist-File" />

## <a name="modifying-the-infoplist-file"></a>Modificando o arquivo Info. plist

Com as imagens necessárias adicionadas para o **recursos** pasta, o [CFBundleAlternateIcons](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-SW13) chave precisará ser adicionado ao projeto de **Info. plist** arquivo. Essa chave definirá o nome do novo ícone e as imagens que compõem a ele.

Faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Info.plist** para abri-lo para edição.
2. Alterne para o **fonte** exibição.
3. Adicionar um **agrupar ícones** chave e deixe o **tipo** definida como **dicionário**.
4. Adicionar um `CFBundleAlternateIcons` e configure o **tipo** para **dicionário**.
5. Adicionar um `AppIcons2` e configure o **tipo** para **dicionário**. Esse será o nome do novo conjunto de ícone do aplicativo alternativo.
6. Adicionar um `CFBundleIconFiles` e configure o **tipo** para **matriz**
7. Adicionar uma nova cadeia de caracteres para o `CFBundleIconFiles` matriz para cada arquivo de ícone omitindo a extensão e o `@2x`, `@3x`, sufixos etc. (exemplo `100_icon`). Repita essa etapa para cada arquivo que compõe o conjunto de ícones alternativo.
8. Adicionar um `UIPrerenderedIcon` chave para o `AppIcons2` dicionário, defina o **tipo** para **booliano** e o valor para **não**.
9. Salve as alterações no arquivo.

Resultante **Info. plist** arquivo deve ser semelhante ao seguinte quando concluída:

![](alternate-app-icons-images/icons03.png "O arquivo Info. plist concluído")

Assim, se abrir ou em um editor de texto:

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

Com as imagens de ícone incluídas no projeto xamarin e **Info. plist** arquivo configurado corretamente, o desenvolvedor pode usar um dos muitos novos recursos adicionados ao iOS 10.3 para controlar o ícone do aplicativo.

O `SupportsAlternateIcons` propriedade o `UIApplication` classe permite que o desenvolvedor se um aplicativo dá suporte a ícones alternativos. Por exemplo:

```csharp
// Can the app select a different icon?
PrimaryIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
AlternateIconButton.Enabled = UIApplication.SharedApplication.SupportsAlternateIcons;
```

O `ApplicationIconBadgeNumber` propriedade o `UIApplication` classe permite que o desenvolvedor obter ou definir o número atual de notificação do ícone de aplicativo no Springboard. O valor padrão é zero (0). Por exemplo:

```csharp
// Set the badge number to 1
UIApplication.SharedApplication.ApplicationIconBadgeNumber = 1;
```

O `AlternateIconName` propriedade o `UIApplication` classe permite que o desenvolvedor obter o nome do ícone de aplicativo alternativo selecionado no momento ou retorna `null` se o aplicativo estiver usando o ícone primário. Por exemplo:

```csharp
// Get the name of the currently selected alternate
// icon set
var name = UIApplication.SharedApplication.AlternateIconName;

if (name != null ) {
    // Do something with the name
}
```

O `SetAlternameIconName` propriedade o `UIApplication` classe permite que o desenvolvedor alterar o ícone do aplicativo. Passe o nome do ícone para selecionar ou `null` para retornar para o ícone do primário. Por exemplo:

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

![](alternate-app-icons-images/icons04.png "Um alerta de exemplo quando um aplicativo altera seu ícone")

Se o usuário alterna para o ícone primário, será exibido um alerta semelhante ao seguinte:

![](alternate-app-icons-images/icons05.png "Um alerta de exemplo quando um aplicativo é alterado para o ícone primário")

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou adicionando ícones de aplicativo alternativo para um projeto do xamarin e usá-las dentro do aplicativo.



## <a name="related-links"></a>Links relacionados

- [Exemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
