---
title: Temas
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: fca5a0cb2e188df822efb15b090ef7986cf4a2d8
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848090"
---
# <a name="themes"></a>Temas

![](~/media/shared/preview.png "Esta API está atualmente em visualização")

Xamarin. Forms temas foram lançados no Evolve 2016 e estão disponíveis como uma visualização para os clientes tente e fornecer comentários.

Um tema é adicionado a um aplicativo xamarin. Forms, incluindo o **Xamarin.Forms.Theme.Base** pacote Nuget, além de um pacote adicional que define um tema específico (por exemplo. Xamarin.Forms.Theme.Light) ou outro um tema local pode ser definido para o aplicativo.

Consulte o [tema claro](light.md) e [tema escuro](dark.md) páginas para obter instruções sobre como adicioná-los a um aplicativo ou check-out de [tema personalizado de exemplo](custom.md).

**IMPORTANTE:** você também deve seguir as etapas para [carregar assemblies de tema (abaixo)](#loadtheme) adicionando um código clichê para o iOS `AppDelegate` e Android `MainActivity`. Isso será aprimorado em uma versão futura de visualização.


## <a name="control-appearance"></a>Controlar a aparência

O [Light](light.md) e [escuro](dark.md) ambos os temas definem uma aparência visual específica para controles padrão. Quando você adicionar um tema ao dicionário de recursos do aplicativo, irá alterar a aparência dos controles padrão.

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

![](images/standard-none-sml.png "Controles sem temas") ![ ] (images/standard-light-sml.png "controles com tema claro") ![ ] (images/standard-dark-sml.png "controles com tema escuro")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

O `StyleClass` propriedade permite que a aparência do modo de exibição a ser alterada de acordo com uma definição fornecida por um tema.

O [Light](light.md) e [escuro](dark.md) ambos os temas definem três aparências diferentes para um `BoxView`: `HorizontalRule`, `Circle`, e `Rounded`. Essa marcação mostra três diferentes `BoxView`s com classes diferentes de estilo aplicadas:

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Isso renderiza com claro e escuro da seguinte maneira:

![](images/boxview-light-sml.png "BoxView com um tema claro StyleClass") ![ ] (images/boxview-dark-sml.png "BoxView com StyleClass um tema escuro")

<a name="builtin" />

## <a name="built-in-classes"></a>Classes internas

Além de estilo automaticamente comuns controla a luz e temas escuros atualmente suportam as seguintes classes que podem ser aplicadas, definindo o `StyleClass` nesses controles:

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
* Risco
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

Na versão de visualização, temas não poderá carregar em tempo de execução. Adicione o código mostrado abaixo, os projetos relevantes para corrigir esse erro.

**iOS**

No **appdelegate. CS** adicione as seguintes linhas depois de `LoadApplication`

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
