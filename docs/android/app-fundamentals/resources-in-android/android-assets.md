---
title: Uso de ativos do Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 795f80f69294abdfd7bf412225ab77cbbe5cb5b1
ms.sourcegitcommit: d2daaa6ca5fe630f80d5a8151985d9f96a2fc93b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512987"
---
# <a name="using-android-assets"></a>Uso de ativos do Android

Os _ativos_ fornecem uma maneira de incluir arquivos arbitrários, como texto, XML, fontes, música e vídeo em seu aplicativo. Se você tentar incluir esses arquivos como "recursos", o Android os processará em seu sistema de recursos e você não poderá obter os dados brutos. Se você quiser acessar dados inalterados, os ativos são uma maneira de fazer isso.

Os ativos adicionados ao seu projeto serão mostrados como um sistema de arquivos que pode ser lido por seu aplicativo usando o [assetManager](xref:Android.Content.Res.AssetManager).
Nesta demonstração simples, vamos adicionar um ativo de arquivo de texto ao nosso projeto, lê-lo usando `AssetManager` e exibi-lo em um TextView.

## <a name="add-asset-to-project"></a>Adicionar ativo ao projeto

Os ativos são acessados na `Assets` pasta do seu projeto. Adicione um novo arquivo de texto a essa pasta chamada `read_asset.txt` . Coloque algum texto em ti, como "Eu recebi de um ativo!".

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

O Visual Studio deve ter definido a **ação de compilação** para esse arquivo como **AndroidAsset**:

![Definindo a ação de Build como AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac deve ter definido a **ação de compilação** para esse arquivo como **AndroidAsset**:

[![Definindo a ação de Build como AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

A seleção da **compilaçãoaction** correta garante que o arquivo será empacotado no apk em tempo de compilação.

## <a name="reading-assets"></a>Lendo ativos

Os ativos são lidos usando um [assetManager](xref:Android.Content.Res.AssetManager). Uma instância do `AssetManager` está disponível acessando a propriedade [ativos](xref:Android.Content.Context.Assets) em um `Android.Content.Context` , como uma atividade.
No código a seguir, podemos abrir nosso **read_asset.txt** ativo, ler o conteúdo e exibi-lo usando um TextView.

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

### <a name="reading-binary-assets"></a>Lendo ativos binários

O uso do `StreamReader` no exemplo acima é ideal para ativos de texto. Para ativos binários, use o seguinte código:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Read the contents of our asset
    const int maxReadSize = 256 * 1024;
    byte[] content;
    AssetManager assets = this.Assets;
    using (BinaryReader br = new BinaryReader (assets.Open ("mydatabase.db")))
    {
        content = br.ReadBytes (maxReadSize);
    }

    // Do something with it...

}
```

## <a name="running-the-application"></a>Executando o aplicativo

Execute o aplicativo e você verá o seguinte:

![Captura de tela de exemplo](android-assets-images/screenshot.png)

## <a name="related-links"></a>Links Relacionados

- [Ativomanager](xref:Android.Content.Res.AssetManager)
- [Contexto](xref:Android.Content.Context)
