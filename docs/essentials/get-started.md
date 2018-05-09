---
title: Xamarin.Essentials
description: Xamarin.Essentials fornece uma API única entre plataformas que funciona com qualquer iOS, Android ou UWP que pode ser acessado pelo aplicativo compartilhado código, independentemente de como a interface do usuário é criada.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 960682c4b7776214ab303e861be4e3a8bfa9388a
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-xamarinessentials"></a>Introdução ao Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials fornece uma API única entre plataformas que funciona com qualquer iOS, Android ou UWP que pode ser acessado pelo aplicativo compartilhado código, independentemente de como a interface do usuário é criada.

## <a name="platform-support"></a>Suporte de plataforma

Xamarin.Essentials suporta os seguintes sistemas operacionais e plataformas:

| Plataforma | Versão |
| --- | --- |
| Android | 4.4 (API 19) ou superior |
| iOS |10,0 ou superior |
| UWP | 10.0.6299.0 ou superior |

## <a name="installation"></a>Instalação

Xamarin.Essentials está disponível como um pacote NuGet que pode ser adicionado a qualquer projeto novo ou existente usando o Visual Studio.

1. Baixe e instale o [Visual Studio](http://visualstudio.com) com o [Visual Studio tools para Xamarin](~/cross-platform/get-started/installation/index.md).

2. Abrir um projeto existente ou crie um novo projeto usando o modelo de aplicativo em branco em **Visual Studio c#** (Android, iPhone e iPad ou plataforma cruzada). **Importante**: se adicionar a um projeto UWP Certifique-se de compilação 16299 ou superior é definida nas propriedades do projeto.

3. Adicionar o **Xamarin.Essentials** pacote NuGet para cada projeto:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    No painel do Gerenciador de soluções, clique com o botão direito no nome da solução e selecione **gerenciar pacotes NuGet**. Procurar **Xamarin.Essentials** e instale o pacote em **todos os** projetos, incluindo bibliotecas Android, iOS, UWP e .NET padrão.

    > [!TIP]
    > Verifique o **incluir pré-lançamento** caixa durante o [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está em visualização.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    No painel do Gerenciador de soluções, clique com o botão direito no nome do projeto e selecione **Adicionar > Adicionar pacotes do NuGet...** . Procurar **Xamarin.Essentials** e instale o pacote em **todos os** projetos, incluindo bibliotecas, iOS e Android .NET padrão.

    > [!TIP]
    > Verifique o **Mostrar pacotes de pré-lançamento** caixa durante o [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está em visualização.

    -----

4. Adicione uma referência a Xamarin.Essentials em nenhuma classe c# para referenciar as APIs.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials requer configuração específica de plataforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    O projeto Android `MainLauncher` ou qualquer `Activity` que é iniciado Xamarin.Essentials deve ser inicializado no `OnCreate` método:

    ```csharp
    Xamarin.Essentials.Platform.Init(this, bundle);
    ```

    Para lidar com permissões de tempo de execução no Android, Xamarin.Essentials devem receber qualquer `OnRequestPermissionsResult`. Adicione o seguinte código para todos os `Activity` classes:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    Nenhuma configuração adicional necessária.

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    Nenhuma configuração adicional necessária.

    -----

6. Siga o [Xamarin.Essentials guias](index.md) permitem que você copie e cole os trechos de código para cada recurso.

## <a name="other-resources"></a>Outros recursos

Recomendamos que os desenvolvedores novos visita Xamarin [Introdução ao desenvolvimento do Xamarin](~/cross-platform/getting-started/index.md).

Visite o [repositório do GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) para ver atual da fonte de código, o que vem em seguida, executar os exemplos e feche o repositório. Contribuições da comunidade são bem-vindas!

Percorra a [documentação da API](xref:Xamarin.Essentials) para cada recurso do Xamarin.Essentials.
