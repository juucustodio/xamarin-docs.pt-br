---
title: Introdução ao Xamarin.Essentials
description: O Xamarin.Essentials oferece uma API única entre plataformas que funciona com qualquer aplicativo iOS, Android ou UWP e que pode ser acessado no código compartilhado, independentemente da forma como a interface do usuário é criada.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.custom: video
ms.date: 11/04/2018
ms.openlocfilehash: d8562cd74b661b5f72f373151b9aa0acc36f8477
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869748"
---
# <a name="get-started-with-xamarinessentials"></a>Introdução ao Xamarin.Essentials

O Xamarin.Essentials oferece uma API única entre plataformas que funciona com qualquer aplicativo iOS, Android ou UWP e que pode ser acessado no código compartilhado, independentemente da forma como a interface do usuário é criada.

## <a name="platform-support"></a>Suporte de plataforma

O Xamarin.Essentials é compatível com os seguintes sistemas operacionais e plataformas:

| Plataforma | Versão |
| --- | --- |
| Android | 4.4 (API 19) ou superior |
| iOS |10.0 ou superior |
| UWP | 10.0.16299.0 ou superior |

## <a name="installation"></a>Instalação

O Xamarin.Essentials está disponível como um pacote NuGet que pode ser adicionado a qualquer projeto, novo ou existente, através do Visual Studio.

1. Faça o download e instale o [Visual Studio](http://visualstudio.com) com as [Ferramentas do Visual Studio para Xamarin](~/get-started/installation/index.md).

2. Abra um projeto existente ou crie um novo projeto usando o modelo Aplicativo em branco no **C# do Visual Studio** (Android, iPhone e iPad ou plataforma cruzada). **Importante**: Se adicionar a um projeto UWP, verifique se o Build 16299 ou superior está definido nas propriedades do projeto.

3. Adicione o pacote NuGet do **Xamarin.Essentials** a cada projeto:

    # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    No painel do Gerenciador de Soluções, clique com o botão direito do mouse no nome da solução e escolha **Gerenciar pacotes NuGet**. Pesquise **Xamarin.Essentials** e instale o pacote em **TODOS** os projetos, incluindo bibliotecas do Android, iOS, UWP e .NET Standard.

    # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    No painel do Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto e escolha **Adicionar > Adicionar pacotes NuGet...**. Pesquise **Xamarin.Essentials** e instale o pacote em **TODOS** os projetos, incluindo bibliotecas do Android, iOS e .NET Standard.

    -----

4. Adicione uma referência ao Xamarin.Essentials na classe C# para fazer referência às APIs.

    ```csharp
    using Xamarin.Essentials;
    ```

5. O Xamarin.Essentials requer uma configuração específica da plataforma:

    # <a name="androidtabandroid"></a>[Android](#tab/android)

    O Xamarin.Essentials é compatível com, no mínimo, a versão 4.4 do Android, correspondente ao nível da API 19, mas, para compilação, a versão de destino do Android deve ser 9.0, que corresponde ao nível da API 28. (No Visual Studio, essas duas versões são definidas na caixa de diálogo Propriedades do Projeto para projetos do Android, na guia Manifesto do Android. No Visual Studio para Mac, elas são definidas na caixa de diálogo Opções do Projeto para projetos do Android, na guia Aplicativo Android).

    O Xamarin.Essentials instala a versão 28.0.0.1 das bibliotecas Xamarin.Android.Support necessárias. As outras bibliotecas Xamarin.Android.Support exigidas pelo aplicativo também devem ser atualizadas para a versão 28.0.0.1 usando o gerenciador de pacotes NuGet. Todas as bibliotecas Xamarin.Android.Support usadas pelo aplicativo devem ser as mesmas e devem ter, pelo menos, a versão 28.0.0.1. Confira a [página de solução de problemas](troubleshooting.md) se tiver problemas para adicionar o NuGet do Xamarin.Essentials ou para atualizar NuGets na sua solução.

    No `MainLauncher` do projeto do Android ou qualquer `Activity` que é iniciada, o Xamarin.Essentials deve ser inicializado no método `OnCreate`:

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState) {
        //...
        base.OnCreate(savedInstanceState);
        Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
        //...
    ```

    Para lidar com permissões de tempo de execução no Android, o Xamarin.Essentials deve receber qualquer `OnRequestPermissionsResult`. Adicione o seguinte código a todas as classes `Activity`:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Android.Content.PM.Permission[] grantResults)
    {
        Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
    ```

    # <a name="iostabios"></a>[iOS](#tab/ios)

    Não exige mais configurações.

    # <a name="uwptabuwp"></a>[UWP](#tab/uwp)

    Não exige mais configurações.

    -----

6. Siga os [guias do Xamarin.Essentials](index.md) que permitem copiar e colar os trechos de código para cada recurso.

## <a name="xamarinessentials---cross-platform-apis-for-mobile-apps-video"></a>Xamarin.Essentials – APIs multiplataforma para aplicativos móveis (vídeo)

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-XamarinEssentials-Cross-Platform-APIs-for-Mobile-Apps/player]

## <a name="other-resources"></a>Outros recursos

Recomendamos que os novos desenvolvedores do Xamarin confiram a [Introdução ao desenvolvimento do Xamarin](~/cross-platform/getting-started/index.md).

Visite o [Repositório do GitHub do Xamarin.Essentials](https://github.com/xamarin/Essentials) para ver o código-fonte atual, o que vem a seguir, colocar exemplos em prática e clonar o repositório. Contribuições da comunidade são bem-vindas!

Navegue pela [documentação da API](xref:Xamarin.Essentials) de todos os recursos do Xamarin.Essentials.
