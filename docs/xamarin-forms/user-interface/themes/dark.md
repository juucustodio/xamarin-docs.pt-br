---
title: Tema escuro do xamarin. Forms
description: Este artigo explica como utilizar o tema escuro do xamarin. Forms em um aplicativo.
ms.prod: xamarin
ms.assetid: 43A3798D-6F05-4734-AF5E-97235B46D9B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 1fc329f506afde04b0dc59dc637d999865aafbe1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186327"
---
# <a name="xamarinforms-dark-theme"></a>Tema escuro do xamarin. Forms

![](~/media/shared/preview.png "Essa API está atualmente em versão prévia")

> [!NOTE]
> Temas exigem a versão de visualização do xamarin. Forms 2.3. Verifique as [dicas de solução de problemas](~/xamarin-forms/user-interface/themes/index.md) se ocorrerem erros.

Para usar o tema escuro:

## <a name="1-add-nuget-packages"></a>1. Adicionar pacotes Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Dark

## <a name="2-add-to-the-resource-dictionary"></a>2. Adicionar ao dicionário de recursos

No **App. XAML** arquivo de adicionar um novo personalizado `xmlns` do tema e, em seguida, verifique se os recursos do tema são mesclados com o dicionário de recursos do aplicativo.
Um exemplo de arquivo XAML é mostrado abaixo:

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:dark="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Dark">
    <Application.Resources>
        <ResourceDictionary MergedWith="dark:DarkThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3. Classes de tema de carga

Siga esse [etapa de solução de problemas](~/xamarin-forms/user-interface/themes/index.md) e adicione o código necessário no iOS e projetos de aplicativo Android.

## <a name="4-use-styleclass"></a>4. Usar StyleClass

Aqui está um exemplo de botões e rótulos no tema escuro, juntamente com a marcação que produz-los.

[![](dark-images/dark-theme-sml.png "Botões e rótulos no tema escuro")](dark-images/dark-theme.png#lightbox "botões e rótulos no tema escuro")

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

O [uma lista completa de classes internas](~/xamarin-forms/user-interface/themes/index.md) mostra quais estilos disponíveis para alguns controles comuns.
