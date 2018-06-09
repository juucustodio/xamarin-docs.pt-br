---
title: Tema claro xamarin. Forms
description: Este artigo explica como utilizar o tema de luz xamarin. Forms em um aplicativo.
ms.prod: xamarin
ms.assetid: D5D16AE3-F51F-4359-B37A-E1087ECE512B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 7f40e375d653acec60f8848627234ab46fcce8de
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245241"
---
# <a name="xamarinforms-light-theme"></a>Tema claro xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em visualização")

> [!NOTE]
> Temas requerem a versão de visualização 2.3 xamarin. Forms. Verifique o [dicas de solução de problemas](~/xamarin-forms/user-interface/themes/index.md) se ocorrerem erros.

Para usar o tema claro:

## <a name="1-add-nuget-packages"></a>1. Adicione pacotes Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Light

## <a name="2-add-to-the-resource-dictionary"></a>2. Adicionar ao dicionário de recurso

No **App** arquivo adicionar um novo personalizado `xmlns` para o tema e, em seguida, certifique-se de que os recursos do tema são mesclados com o dicionário de recursos do aplicativo.
Um arquivo XAML de exemplo é mostrado abaixo:

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:light="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light">
    <Application.Resources>
        <ResourceDictionary MergedWith="light:LightThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3. Classes de tema de carga

Siga este [etapa de solução de problemas](~/xamarin-forms/user-interface/themes/index.md) e adicione o código necessário em projetos de aplicativo do Android e iOS.

## <a name="4-use-styleclass"></a>4. Use StyleClass

Aqui está um exemplo de botões e rótulos no tema claro, junto com a marcação que produz-los.

[![](light-images/light-theme-sml.png "Botões e rótulos no tema claro")](light-images/light-theme.png#lightbox "botões e rótulos no tema claro")

```xaml
<StackLayout Padding="20">
    <Button Text="Button Default" />
    <Button Text="Button Class Default" StyleClass="Default" />
    <Button Text="Button Class Primary" StyleClass="Primary" />
    <Button Text="Button Class Success" StyleClass="Success" />
    <Button Text="Button Class Info" StyleClass="Info" />
    <Button Text="Button Class Warning" StyleClass="Warning" />
    <Button Text="Button Class Danger" StyleClass="Danger" />
    <Button Text="Button Class Link" StyleClass="Link" />
    <Button Text="Button Class Default Small" StyleClass="Small" />
    <Button Text="Button Class Default Large" StyleClass="Large" />
</StackLayout>
```

O [lista completa de classes internas](~/xamarin-forms/user-interface/themes/index.md) mostra quais estilos disponíveis para alguns controles comuns.
