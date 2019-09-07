---
title: Uso de ativos do Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: e3b7ccf74773e5a391b8f133ccc241ca0e18bfcb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755153"
---
# <a name="using-android-assets"></a>Uso de ativos do Android

Os _ativos_ fornecem uma maneira de incluir arquivos arbitrários, como texto, XML, fontes, música e vídeo em seu aplicativo. Se você tentar incluir esses arquivos como "recursos", o Android os processará em seu sistema de recursos e você não poderá obter os dados brutos. Se você quiser acessar dados inalterados, os ativos são uma maneira de fazer isso.

Os ativos adicionados ao seu projeto serão mostrados como um sistema de arquivos que pode ser lido por seu aplicativo usando o [assetManager](xref:Android.Content.Res.AssetManager).
Nesta demonstração simples, vamos adicionar um ativo de arquivo de texto ao nosso projeto, lê-lo usando `AssetManager`e exibi-lo em um TextView.

## <a name="add-asset-to-project"></a>Adicionar ativo ao projeto

Os ativos são acessados `Assets` na pasta do seu projeto. Adicione um novo arquivo de texto a essa pasta `read_asset.txt`chamada. Coloque algum texto em ti, como "Eu recebi de um ativo!".

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

O Visual Studio deve ter definido a **ação de compilação** para esse arquivo como **AndroidAsset**:

![Definindo a ação de Build como AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac deve ter definido a **ação de compilação** para esse arquivo como **AndroidAsset**:

[![Definindo a ação de Build como AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

A seleção da **compilaçãoaction** correta garante que o arquivo será empacotado no apk em tempo de compilação.

## <a name="reading-assets"></a>Lendo ativos

Os ativos são lidos usando um [assetManager](xref:Android.Content.Res.AssetManager). Uma instância do `AssetManager` está disponível acessando a propriedade [ativos](xref:Android.Content.Context.Assets) em um `Android.Content.Context`, como uma atividade.
No código a seguir, podemos abrir nosso ativo **read_asset. txt** , ler o conteúdo e exibi-lo usando um TextView.

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Create a new TextView and set it as our view
    TextView tv = new TextView (this);
    
    // Read the contents of our asset
    string content;
    AssetManager assets = this.Assets;
    using (StreamReader sr = new StreamReader (assets.Open ("read_asset.txt")))
    {
        content = sr.ReadToEnd ();
    }

    // Set TextView.Text to our asset content
    tv.Text = content;
    SetContentView (tv);
}
```

## <a name="running-the-application"></a>Executando o aplicativo

Execute o aplicativo e você verá o seguinte:

![Captura de tela de exemplo](android-assets-images/screenshot.png)

## <a name="related-links"></a>Links relacionados

- [AssetManager](xref:Android.Content.Res.AssetManager)
- [Noticioso](xref:Android.Content.Context)
