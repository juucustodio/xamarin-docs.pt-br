---
title: Introdução ao Xamarin.Essentials
description: Xamarin.Essentials fornece uma única API de plataforma cruzada que funciona com qualquer aplicativo iOS, Android ou UWP que pode ser acessado do código compartilhado, independentemente de como a interface do usuário é criada.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 05/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f8a65c7bd3476114b849bb0e9383227e2ec78c7c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432617"
---
# <a name="get-started-with-no-locxamarinessentials"></a>Introdução ao Xamarin.Essentials

Xamarin.Essentials fornece uma única API de plataforma cruzada que funciona com qualquer aplicativo iOS, Android ou UWP que pode ser acessado do código compartilhado, independentemente de como a interface do usuário é criada. Confira o [guia de suporte a plataformas e recursos](platform-feature-support.md) para obter mais informações sobre os sistemas operacionais compatíveis.

## <a name="installation"></a>Instalação

Xamarin.Essentials está disponível como um pacote NuGet e está incluído em todos os novos projetos no Visual Studio. Ele também pode ser adicionado a qualquer projeto existente usando o Visual Studio com as etapas a seguir.

1. Faça o download e instale o [Visual Studio](https://visualstudio.microsoft.com/) com as [Ferramentas do Visual Studio para Xamarin](~/get-started/installation/index.md).

2. Abra um projeto existente ou crie um novo projeto usando o modelo Aplicativo em branco no **C# do Visual Studio** (Android, iPhone e iPad ou plataforma cruzada).

    > [!IMPORTANT]
    > Se adicionar a um projeto UWP, verifique se o Build 16299 ou superior está definido nas propriedades do projeto.

3. Adicione o [**Xamarin.Essentials**](https://www.nuget.org/packages/Xamarin.Essentials/) pacote NuGet a cada projeto:

    <!--markdownlint-disable MD023 -->
    # <a name="visual-studio"></a>[Visual Studio](#tab/windows)

    No painel do Gerenciador de Soluções, clique com o botão direito do mouse no nome da solução e escolha **Gerenciar pacotes NuGet**. Procure **Xamarin.Essentials** e instale o pacote em **todos os** projetos, incluindo as bibliotecas Android, Ios, UWP e .net Standard.

    # <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

    No painel de Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto e selecione **adicionar > adicionar pacotes NuGet...**. Procure **Xamarin.Essentials** e instale o pacote em **todos os** projetos, incluindo as bibliotecas do Android, Ios e .net Standard.

    -----

4. Adicione uma referência a Xamarin.Essentials em qualquer classe C# para fazer referência às APIs.

    ```csharp
    using Xamarin.Essentials;
    ```

5. Xamarin.Essentials requer a instalação específica da plataforma:

    # <a name="android"></a>[Android](#tab/android)

    Xamarin.Essentials dá suporte a uma versão mínima do Android 4,4, correspondente ao nível da API 19, mas a versão do Android de destino para compilação deve ser 9,0 ou 10,0, correspondendo ao nível da API 28 e nível 29. (No Visual Studio, essas duas versões são definidas na caixa de diálogo Propriedades do projeto para o projeto do Android, na guia manifesto do Android. No Visual Studio para Mac, eles são definidos na caixa de diálogo opções do projeto para o projeto do Android, na guia aplicativo do Android.)

    Ao compilar no Android 9,0, o Xamarin.Essentials instala a versão 28.0.0.3 das bibliotecas Xamarin. Android. support necessárias. Qualquer outra biblioteca Xamarin. Android. support que seu aplicativo requer também deve ser atualizada para a versão 28.0.0.3 usando o Gerenciador de pacotes NuGet. Todas as bibliotecas Xamarin. Android. support usadas pelo seu aplicativo devem ser as mesmas e devem ter pelo menos a versão 28.0.0.3. Consulte a [página de solução de problemas](troubleshooting.md) se você tiver problemas para adicionar o Xamarin.Essentials NuGet ou atualizar o NuGets em sua solução.

    Começando com a versão 1.5.0 ao compilar no Android 10,0, Xamarin.Essentials Instale as bibliotecas de suporte do AndroidX necessárias. Leia a [documentação do AndroidX](../android/platform/androidx.md) se você ainda não fez a transição.

    No projeto do Android `MainLauncher` ou em qualquer um `Activity` que seja iniciado, Xamarin.Essentials o deve ser inicializado no `OnCreate` método:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    Para manipular permissões de tempo de execução no Android, é Xamarin.Essentials necessário receber qualquer um `OnRequestPermissionsResult` . Adicione o seguinte código a todas as classes `Activity`:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="ios"></a>[iOS](#tab/ios)

    Não exige mais configurações.

    # <a name="uwp"></a>[UWP](#tab/uwp)

    Não exige mais configurações.

    -----

6. Siga os [ Xamarin.Essentials guias](index.md) que permitem copiar e colar trechos de código para cada recurso.

## <a name="no-locxamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials -APIs de plataforma cruzada para aplicativos móveis (vídeo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Outros recursos

Recomendamos que os novos desenvolvedores do Xamarin confiram a [Introdução ao desenvolvimento do Xamarin](~/cross-platform/getting-started/index.md).

Visite o [ Xamarin.Essentials repositório do GitHub](https://github.com/xamarin/Essentials) para ver o código-fonte atual, o que vem em seguida, execute exemplos e clone o repositório. Contribuições da comunidade são bem-vindas!

Navegue pela [documentação da API](xref:Xamarin.Essentials) para cada recurso do Xamarin.Essentials .
