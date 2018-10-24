---
title: Introdução ao Xamarin.Essentials
description: Xamarin.Essentials fornece uma API única entre plataformas que funciona com qualquer iOS, Android ou UWP que pode ser acessado de aplicativo compartilhado independentemente de como a interface do usuário é criada.
ms.assetid: B2669C48-B659-4854-BD80-FEB0E876F5B9
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c72c1c66a465075770ce739270cb4b1f2c6fba7a
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353770"
---
# <a name="get-started-with-xamarinessentials"></a>Introdução ao Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

Xamarin.Essentials fornece uma API única entre plataformas que funciona com qualquer iOS, Android ou UWP que pode ser acessado de aplicativo compartilhado independentemente de como a interface do usuário é criada.

## <a name="platform-support"></a>Suporte de plataforma

Xamarin.Essentials suporta os seguintes sistemas operacionais e plataformas:

| Plataforma | Versão |
| --- | --- |
| Android | 4.4 (API 19) ou superior |
| iOS |10,0 ou superior |
| UWP | 10.0.16299.0 ou superior |

## <a name="installation"></a>Instalação

Xamarin.Essentials está disponível como um pacote do NuGet que pode ser adicionado a qualquer projeto novo ou existente usando o Visual Studio.

1. Baixe e instale [Visual Studio](http://visualstudio.com) com o [ferramentas do Visual Studio para Xamarin](~/cross-platform/get-started/installation/index.md).

2. Abra um projeto existente ou crie um novo projeto usando o modelo de aplicativo em branco sob **Visual Studio c#** (Android, iPhone e iPad ou plataforma cruzada). **Importante**: se adicionar a um projeto UWP Certifique-se de compilação 16299 ou superior é definida nas propriedades do projeto.

3. Adicione a **Xamarin.Essentials** pacote do NuGet para cada projeto:

   # <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

   No painel Gerenciador de soluções, clique com o botão direito no nome da solução e selecione **gerenciar pacotes NuGet**. Pesquise **Xamarin.Essentials** e instalar o pacote **todos os** projetos, incluindo bibliotecas de Android, iOS, UWP e .NET Standard.

   > [!TIP]
   > Verifique a **incluir pré-lançamento** caixa enquanto os [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está em versão prévia.

   # <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

   No painel Gerenciador de soluções, clique com o botão direito no nome do projeto e selecione **Adicionar > Adicionar pacotes NuGet...** . Pesquise **Xamarin.Essentials** e instalar o pacote **todos os** projetos, incluindo bibliotecas Android, iOS e .NET Standard.

   > [!TIP]
   > Verifique a **Mostrar pacotes de pré-lançamento** caixa enquanto os [ **Xamarin.Essentials** NuGet](https://www.nuget.org/packages/Xamarin.Essentials) está em versão prévia.

    -----

4. Adicione uma referência ao Xamarin.Essentials em qualquer classe do c# para referenciar as APIs.

   ```csharp
   using Xamarin.Essentials;
   ```

5. Xamarin.Essentials requer uma configuração específica da plataforma:

   # <a name="androidtabandroid"></a>[Android](#tab/android)

   Xamarin.Essentials dá suporte a uma versão mínima do Android do 4.4, correspondente ao nível de API 19, mas a versão do Android de destino de compilação deve ser 8.1, correspondente ao nível 27 da API. (No Visual Studio, essas duas versões são definidas na caixa de diálogo Propriedades do projeto para o projeto Android, na guia do manifesto do Android. No Visual Studio para Mac, eles são definidos na caixa de diálogo Opções de projeto para o projeto Android, na guia aplicativo do Android.)

   Xamarin.Essentials instala a versão 27.0.2.1 das bibliotecas Support exigidos por ele. Quaisquer outras bibliotecas de Support requer que seu aplicativo também devem ser atualizadas para a versão 27.0.2.1 usando o Gerenciador de pacotes do NuGet. Todas as bibliotecas de support usadas pelo seu aplicativo deve ser o mesmo e deve ser pelo menos versão 27.0.2.1. Consulte a [página de solução de problemas](troubleshooting.md) se você tiver problemas, adicionando o Xamarin.Essentials NuGet ou atualizando NuGets em sua solução.

   No projeto Android `MainLauncher` ou qualquer `Activity` que é iniciado Xamarin.Essentials deve ser inicializado no `OnCreate` método:

   ```csharp
   Xamarin.Essentials.Platform.Init(this, bundle);
   ```

   Para lidar com permissões de tempo de execução no Android, Xamarin.Essentials deve receber qualquer `OnRequestPermissionsResult`. Adicione o seguinte código para todos os `Activity` classes:

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

6. Siga as [Xamarin.Essentials guias](index.md) que permitem que você copie e cole os trechos de código para cada recurso.

## <a name="other-resources"></a>Outros recursos

Recomendamos que os novos desenvolvedores no Xamarin visitem [Introdução ao desenvolvimento do Xamarin](~/cross-platform/getting-started/index.md).

Visite o [repositório do GitHub Xamarin.Essentials](http://github.com/xamarin/Essentials) para ver o código-fonte atual, o que está por vir, executar os exemplos e feche o repositório. Contribuições da comunidade são bem-vindos!

Percorra a [documentação da API](xref:Xamarin.Essentials) para todos os recursos de Xamarin.Essentials.
