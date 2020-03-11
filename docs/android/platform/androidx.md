---
title: AndroidX
description: Como começar a desenvolver aplicativos com o AndroidX usando o Xamarin. Android.
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291952"
---
# <a name="androidx-with-xamarin"></a>AndroidX com o Xamarin

_Como começar a desenvolver aplicativos com o AndroidX usando o Xamarin. Android._

O AndroidX é uma grande melhoria na biblioteca original de suporte do Android, que não é mais mantida. Os pacotes **AndroidX** substituem totalmente a biblioteca de suporte do Android fornecendo paridade de recursos e novas bibliotecas que você pode usar em seus aplicativos Android.

O AndroidX inclui os seguintes recursos:

- Todos os pacotes dentro de AndroidX agora têm um namespace consistente que começa com `androidx`. Isso significa que todos os pacotes de biblioteca de suporte do Android são mapeados para um pacote de `androidx.*` correspondente.
- `androidx` pacotes são mantidos e atualizados separadamente. Isso significa que você pode atualizar bibliotecas AndroidX independentemente umas das outras.
- A partir de v28 da biblioteca de suporte do Android, não haverá mais versões. Todo o desenvolvimento será incluído em `androidx` em vez disso.

![Logotipo do AndroidX](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>Requisitos

A lista a seguir é necessária para usar os recursos do AndroidX em aplicativos baseados no Xamarin:

- **Visual Studio** -no Windows Update para o visual Studio 2019 versão 16,4 ou posterior. No macOS, atualize para o Visual Studio 2019 para Mac versão 8,4 ou posterior.
- **Xamarin. Android** -Xamarin. Android 10,0 ou posterior deve ser instalado com o Visual Studio (o Xamarin. Android é instalado automaticamente como parte do **desenvolvimento móvel com** carga de trabalho do .net no Windows e instalado como parte do **instalador do Visual Studio para Mac**)
- **Java Developer Kit** -o desenvolvimento do Xamarin. Android 10,0 requer o JDK 8. A distribuição da Microsoft do OpenJDK é instalada automaticamente como parte do Visual Studio.
- A API de SDK do Android de **SDK do Android** 28 ou superior deve ser instalada por meio do gerenciador de SDK do Android.

## <a name="get-started"></a>Introdução

Você pode começar a usar o AndroidX incluindo qualquer [pacote NuGet do AndroidX](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) dentro de seu projeto Android. Saiba mais sobre como instalar e usar um pacote no [Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) ou [Visual Studio para Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)

## <a name="behavior-changes"></a>Alterações de comportamento

Como o AndroidX é um redesign da biblioteca de suporte do Android, ele inclui etapas de migração que afetarão os aplicativos Android criados com a biblioteca de suporte do Android.

### <a name="package-name-change"></a>Alteração do nome do pacote
Os nomes de pacote foram alterados entre os pacotes novos e antigos. Abaixo, você pode ver um exemplo dessas alterações:

| Antigo                    | Novo                    |
| ---------------------- | ---------------------- |
| Android. support. * *     | androidx. @             |
| Android. Design. * *      | com. google. Android. material. @ |
| Android. support. Test. * * | androidx. Test. @       |
| Android. Arch. * *        | androidx. @             |
| Android. Arch. persistence. Room. * * | androidx. Room. @ |
| Android. Arch. persistence. * * | androidx. sqlite. @ |

Para obter mais detalhes sobre a nomenclatura de pacotes, [consulte a documentação a seguir](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings).

## <a name="migration-tooling"></a>Ferramentas de migração

Há três etapas de migração que você desejará conhecer para seu aplicativo.

1. Se seu aplicativo incluir **namespaces da biblioteca de suporte do Android e você quiser migrá-los para namespaces do AndroidX**, poderá usar nossas ferramentas **migrar para AndroidX** IDE para cuidar da maioria dos cenários de namespace. 

Habilite o **AndroidX migrador** por meio de **ferramentas > opções > Xamarin > configurações do Android** no Visual Studio 2019 (você pode ignorar esta etapa em Visual Studio para Mac).

![Habilitar o AndroidX Migrator](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

Clique com o botão direito do mouse no projeto e **migre para o AndroidX**.

![Migrar para o AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> Você precisará fazer algumas alterações manuais de namespace para cenários que a ferramenta não cobre. Embora possamos mapear o pacote correto para você, é recomendável que você examine os [mapeamentos de artefatos](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings) oficiais e os [mapeamentos de classe](https://developer.android.com/jetpack/androidx/migrate/class-mappings) para ajudar a migração do seu projeto.

2. Se seu aplicativo incluir **quaisquer dependências que não foram migradas para o namespace AndroidX**, você precisará usar a [biblioteca de suporte do Android para o pacote de migração do AndroidX.](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. Se seu aplicativo não **incluir nenhuma dependência que exija a migração do namespace AndroidX**, você poderá usar as [bibliotecas do AndroidX no NuGet hoje](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22).

## <a name="troubleshooting"></a>solução de problemas

- Determinados pacotes de arquitetura em AndroidX entrarão em conflito com as versões da biblioteca de suporte. Para corrigir isso, você deve usar a versão AndroidX desses pacotes e remover a versão da biblioteca de suporte. Por exemplo, se você estiver fazendo referência a `Xamarin.Android.Arch.Work.Runtime` em seu projeto, ele entrará em conflito com os tipos do pacote de `AndroidX.Work` recém-adicionado.

## <a name="summary"></a>Resumo

Este artigo introduziu o AndroidX e explicou como instalar e configurar as ferramentas e os pacotes mais recentes para o desenvolvimento do Xamarin. Android com o AndroidX. Ele forneceu uma visão geral do que é o AndroidX. Ele inclui links para documentação da API e tópicos para desenvolvedores do Android para ajudá-lo a começar a criar aplicativos usando o AndroidX. Ele também realçou as alterações de comportamento AndroidX mais importantes e os tópicos de solução de problemas que poderiam afetar os aplicativos existentes.

## <a name="related-links"></a>Links relacionados

- [Introdução ao AndroidX | O Xamarin show](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Repositório GitHub do Xamarin AndroidX](https://github.com/xamarin/AndroidX)
- [Repositório GitHub de migração do Xamarin AndroidX](https://github.com/xamarin/XamarinAndroidXMigration)
