---
title: Temas do xamarin. Forms
description: Este artigo apresenta os temas do xamarin. Forms, que definem as aparências específicas para modos de exibição padrão.
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 1c5b2635dca6aa74fd0dfb92d7e62e6da3140538
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902783"
---
# <a name="xamarinforms-themes"></a>Temas do xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)

![](~/media/shared/preview.png "Essa API está atualmente em versão prévia")

Temas do xamarin. Forms foram lançados no Evolve 2016 e estão disponíveis como uma visualização para que os clientes experimentar e fornecer comentários.

Um tema é adicionado a um aplicativo xamarin. Forms, incluindo o **Xamarin.Forms.Theme.Base** de pacote do Nuget, além de um pacote adicional que define um tema específico (por exemplo. Xamarin.Forms.Theme.Light) ou outro um tema local pode ser definido para o aplicativo.

Consulte a [tema claro](light.md) e [tema escuro](dark.md) páginas para obter instruções sobre como adicioná-los a um aplicativo ou fazer check-out a [tema personalizado de exemplo](custom.md).

**IMPORTANTE:** Você também deve seguir as etapas a serem [carregar assemblies de tema (abaixo)](#loadtheme) adicionando um código clichê para o iOS `AppDelegate` e Android `MainActivity`. Isso será melhorado em uma versão de visualização futura.


## <a name="control-appearance"></a>Aparência do controle

O [Light](light.md) e [escuro](dark.md) ambos os temas definem uma aparência visual específica para os controles padrão. Depois que você adiciona um tema para o dicionário de recursos do aplicativo, a aparência dos controles padrão será alterado.

A marcação XAML a seguir mostra alguns controles comuns:

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

Essas capturas de tela mostram esses controles com:

* Nenhum tema aplicado
* Tema claro (apenas diferenças sutis que devem não ter nenhum tema)
* Tema escuro

![](images/standard-none-sml.png "Controles sem temas") ![](images/standard-light-sml.png "controles com tema claro") ![](images/standard-dark-sml.png "controles com tema escuro")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

O `StyleClass` propriedade permite que a aparência do modo de exibição a ser alterado de acordo com uma definição fornecida por um tema.

O [Light](light.md) e [escuro](dark.md) ambos os temas definem três aparências diferentes para um `BoxView`: `HorizontalRule`, `Circle`, e `Rounded`. Essa marcação mostra três diferentes `BoxView`s com classes diferentes de estilo aplicadas:

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Isso renderiza com claro e escuro da seguinte maneira:

![](images/boxview-light-sml.png "BoxView com um tema claro StyleClass") ![](images/boxview-dark-sml.png "BoxView com StyleClass um tema escuro")

<a name="builtin" />

## <a name="built-in-classes"></a>Classes internas

Além de definir o estilo automaticamente comum controla a luz e temas escuros atualmente suportam as seguintes classes que podem ser aplicadas, definindo o `StyleClass` nesses controles:

**BoxView**

* HorizontalRule
* Círculo
* Arredondado

**Image**

* Círculo
* Arredondado
* Miniatura

**Button**

* Padrão
* Primária
* Êxito
* Info
* Aviso
* Perigo
* Link
* Pequeno
* Grande

**Rótulo**

* Cabeçalho
* Subcabeçalho
* Corpo
* Link
* Inverso


## <a name="troubleshooting"></a>Solução de problemas

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Não foi possível carregar arquivo ou assembly 'Xamarin.Forms.Theme.Light' ou uma de suas dependências

Na versão de visualização, temas podem não ser capazes de carregar no tempo de execução. Adicione o código mostrado abaixo nos projetos relevantes para corrigir esse erro.

**iOS**

No **AppDelegate.cs** adicione as seguintes linhas depois de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

No **MainActivity.cs** adicione as seguintes linhas depois de `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>Links relacionados

- [Exemplo de ThemesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
