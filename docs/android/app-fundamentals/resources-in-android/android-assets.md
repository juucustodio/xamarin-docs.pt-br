---
title: Usando ativos do Android
ms.prod: xamarin
ms.assetid: 70ECDDC9-FA40-03B4-BF04-E7CFFFE4260D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 1e9a71de7725c8382e133d85977407bcc859fc58
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114854"
---
# <a name="using-android-assets"></a>Usando ativos do Android

_Ativos_ fornecem uma maneira de incluir arquivos arbitrários como texto, xml, fontes, música e vídeo em seu aplicativo. Se você tentar incluir esses arquivos como "recursos", o Android processá-los em seu sistema de recursos e não será capaz de obter os dados brutos. Se você quiser acessar os dados inalterados, ativos são uma maneira de fazê-lo.

Ativos adicionados ao seu projeto serão exibido como um sistema de arquivos que pode ler usando seu aplicativo [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/).
Nesta simples demonstração, vamos adicionar um ativo de arquivo de texto ao nosso projeto, a leitura usando `AssetManager`e exibi-lo em um TextView.


## <a name="add-asset-to-project"></a>Adicionar ativo ao projeto

Ativos de entrar a `Assets` pasta do seu projeto. Adicione um novo arquivo de texto para essa pasta chamado `read_asset.txt`. Coloque um texto nele, como "Eu VIM de um ativo!".

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Visual Studio deve ter definido as **ação de compilação** para que esse arquivo **AndroidAsset**:

![Definir a ação de compilação como AndroidAsset](android-assets-images/asset-properties-vs.png) 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac deve ter definido as **ação de compilação** para que esse arquivo **AndroidAsset**:

[![Definir a ação de compilação como AndroidAsset](android-assets-images/asset-properties-xs-sml.png)](android-assets-images/asset-properties-xs.png#lightbox)

-----

Selecionar o correto **BuildAction** garante que o arquivo será empacotado no APK no tempo de compilação.


## <a name="reading-assets"></a>Leitura de ativos

Ativos são lidas usando um [AssetManager](https://developer.xamarin.com/api/type/Android.Content.Res.AssetManager/). Uma instância das `AssetManager` está disponível, acessando a [ativos](https://developer.xamarin.com/api/property/Android.Content.Context.Assets/) propriedade em um `Android.Content.Context`, como uma atividade.
No código a seguir, nós abrimos nossa **read_asset.txt** ativo, leia o conteúdo e exibi-lo usando um TextView.

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
- [Contexto](https://developer.xamarin.com/api/type/Android.Content.Context/)
