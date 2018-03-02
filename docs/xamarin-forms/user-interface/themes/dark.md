---
title: Tema escuro
ms.topic: article
ms.prod: xamarin
ms.assetid: 43A3798D-6F05-4734-AF5E-97235B46D9B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0b3a714e8295ed60f201c202fbe30e45dbdcc205
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="dark-theme"></a>Tema escuro

![](~/media/shared/preview.png "Esta API está atualmente em visualização")

> [!NOTE]
> Temas requerem a versão de visualização 2.3 xamarin. Forms. Verifique o [dicas de solução de problemas](~/xamarin-forms/user-interface/themes/index.md) se ocorrerem erros.

Para usar o tema escuro:

## <a name="1-add-nuget-packages"></a>1. Adicione pacotes Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Dark

## <a name="2-add-to-the-resource-dictionary"></a>2. Adicionar ao dicionário de recurso

No **App** arquivo adicionar um novo personalizado `xmlns` para o tema e, em seguida, certifique-se de que os recursos do tema são mesclados com o dicionário de recursos do aplicativo.
Um arquivo XAML de exemplo é mostrado abaixo:

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

Siga este [etapa de solução de problemas](~/xamarin-forms/user-interface/themes/index.md) e adicione o código necessário em projetos de aplicativo do Android e iOS.

## <a name="4-use-styleclass"></a>4. Use StyleClass

Aqui está um exemplo de botões e rótulos no tema escuro, junto com a marcação que produz-los.

[ ![](dark-images/dark-theme-sml.png "Botões e rótulos no tema escuro")](dark-images/dark-theme.png "botões e rótulos no tema escuro")

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

