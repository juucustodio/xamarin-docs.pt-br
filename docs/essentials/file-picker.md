---
title: 'Xamarin.Essentials: Seletor de arquivos'
description: A classe fileescolhidor no Xamarin.Essentials permite que um usuário escolha um único ou vários arquivos do dispositivo.
ms.assetid: 00bdbd57-56b1-47ca-8abe-cebe1b01f61a
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7a9701a1607333022b7356f4485f041d66b410c6
ms.sourcegitcommit: 3a68a8a48eb5a0ea7d6e0f861e2f7ecefcc49715
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810734"
---
# <a name="no-locxamarinessentials-file-picker"></a>Xamarin.Essentials: Seletor de arquivos

A classe **Fileescolhar** permite que um usuário escolha um único ou vários arquivos do dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Fileescolhar** , a configuração específica da plataforma a seguir é necessária.

# <a name="android"></a>[Android](#tab/android)

A permissão `ReadExternalStorage` é necessária e deve ser configurada no projeto do Android. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte no nó do **manifesto** .

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **manifesto do Android** , localize as **permissões necessárias:** área e verifique essa permissão. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwp"></a>[UWP](#tab/uwp)

Não exige mais configurações.

-----

> [!TIP]
> Todos os métodos devem ser chamados no thread da interface do usuário porque as verificações de permissão e as solicitações são manipuladas automaticamente pelo Xamarin.Essentials .

## <a name="pick-file"></a>Escolher arquivo

`FilePicker.PickAsync()` o método permite que o usuário escolha um arquivo do dispositivo. Você pode `PickOptions` especificar diferentes ao chamar o método, permitindo que você especifique o título a ser exibido e os tipos de arquivo que o usuário tem permissão para escolher. Por padrão

```csharp
async Task<FileResult> PickAndShow(PickOptions options)
{
    try
    {
        var result = await FilePicker.PickAsync();
        if (result != null)
        {
            Text = $"File Name: {result.FileName}";
            if (result.FileName.EndsWith("jpg", StringComparison.OrdinalIgnoreCase) ||
                result.FileName.EndsWith("png", StringComparison.OrdinalIgnoreCase))
            {
                var stream = await result.OpenReadAsync();
                Image = ImageSource.FromStream(() => stream);
            }
        }
    }
    catch (Exception ex)
    {
        // The user canceled or something went wrong
    }
}
```

Os tipos de arquivo padrão são fornecidos com `FilePickerFileType.Images` , `FilePickerFileType.Png` e `FilePickerFilerType.Videos` . Você pode especificar tipos de arquivos personalizados ao criar o `PickOptions` e eles podem ser personalizados por plataforma. Por exemplo, aqui está como você deve especificar tipos de arquivo de Comic específicos:

```csharp
var customFileType =
    new FilePickerFileType(new Dictionary<DevicePlatform, IEnumerable<string>>
    {
        { DevicePlatform.iOS, new[] { "public.my.comic.extension" } }, // or general UTType values
        { DevicePlatform.Android, new[] { "application/comics" } },
        { DevicePlatform.UWP, new[] { ".cbr", ".cbz" } },
        { DevicePlatform.Tizen, new[] { "*/*" } },
        { DevicePlatform.macOS, new[] { "cbr", "cbz" } }, // or general UTType values
    });
var options = new PickOptions
{
    PickerTitle = "Please select a comic file",
    FileTypes = customFileType,
};
```

## <a name="pick-multiple-files"></a>Escolher vários arquivos

Se você quiser que o usuário escolha vários arquivos, você pode chamar o `FilePicker.PickMultipleAsync()` método. Ele também usa `PickOptions` como um parâmetro para especificar informações adicionais. Os resultados são os mesmos `PickAsync` que, mas em vez de um único, `FileResult` `IEnumerable<FileResult>` é retornado que pode ser iterado.


## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="android"></a>[Android](#tab/android)

- O URI do arquivo resultante pode não ser persistido entre as reinicializações.

# <a name="ios"></a>[iOS](#tab/ios)

Sem diferenças entre plataformas.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="api"></a>API

- [Código-fonte do fileseletor](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/FilePicker)
- [Documentação da API do fileseparar](xref:Xamarin.Essentials.FilePicker)
