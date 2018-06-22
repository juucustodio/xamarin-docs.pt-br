---
title: Uso de recursos do Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 337a1ed82010658adce40e8946ed1b0361fb6094
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762991"
---
# <a name="using-android-assets"></a>Uso de recursos do Android

_Ativos_ fornecem uma maneira de incluir arquivos arbitrários como texto, xml, fontes, música e vídeo em seu aplicativo. Se você tentar incluir esses arquivos como "recursos", Android processá-los em seu sistema de recursos e você não poderá obter os dados brutos. Se você quiser acessar os dados inalterados, ativos são uma maneira de fazer isso.

Ativos adicionados ao seu projeto aparecerá como um sistema de arquivos que pode ler usando seu aplicativo [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
Nesta demonstração simples, vamos adicionar um ativo de arquivo de texto ao nosso projeto, leia-o usando `AssetManager`e o exibe em um TextView.


## <a name="add-asset-to-project"></a>Adicionar ativo ao projeto

Ativos entrar o `Assets` pasta do seu projeto. Adicionar um novo arquivo de texto para essa pasta chamado `read_asset.txt`. Coloque algum texto nele, como "veio de um ativo!".

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

O Visual Studio deve ter definido a **ação de compilação** para esse arquivo para **AndroidAsset**:

![Definir a ação de compilação como AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O Visual Studio para Mac deve ter definido a **ação de compilação** para esse arquivo para **AndroidAsset**:

[![Definir a ação de compilação como AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

A seleção correta **BuildAction** garante que o arquivo será empacotado no APK em tempo de compilação.


## <a name="reading-assets"></a>Ativos de leitura

Ativos são lidos usando um [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Uma instância do `AssetManager` está disponível, acessando o [ativos](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) propriedade em um `Android.Content.Context`, como uma atividade.
No código a seguir, abrimos nossa **read_asset.txt** ativo, leia o conteúdo e exibi-lo usando um TextView.

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

- [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/)
- [Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
