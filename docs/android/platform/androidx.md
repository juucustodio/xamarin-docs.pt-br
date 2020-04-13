---
title: AndroidX
description: Como começar a desenvolver aplicativos com AndroidX usando Xamarin.Android.
ms.assetid: CC21BD28-EF67-4132-8C0D-CF25B78BA78B
author: JonDouglas
ms.author: jodou
ms.date: 02/20/2020
ms.openlocfilehash: ad6ea2f68fc01183f7ed42e85094f6be5fb3d9f9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291952"
---
# <a name="androidx-with-xamarin"></a>AndroidX com Xamarin

_Como começar a desenvolver aplicativos com AndroidX usando Xamarin.Android._

O AndroidX é uma grande melhoria para a Biblioteca de Suporte Android original, que não é mais mantida. Os pacotes **AndroidX** substituem totalmente a Biblioteca de Suporte android, fornecendo paridade de recursos e novas bibliotecas que você pode usar em seus aplicativos Android.

O AndroidX inclui os seguintes recursos:

- Todos os pacotes dentro do AndroidX `androidx`agora têm um namespace consistente começando com . Isso significa que todos os pacotes `androidx.*` da Biblioteca de Suporte android mapeiam para um pacote correspondente.
- `androidx`os pacotes são mantidos separadamente e atualizados. Isso significa que você pode atualizar bibliotecas AndroidX independentemente umas das outras.
- A partir do v28 da Biblioteca de Suporte para Android, não haverá mais lançamentos. Todo o desenvolvimento `androidx` será incluído em seu lugar.

![Logotipo do AndroidX](~/android/platform/androidx-images/AndroidXLogo.png)

## <a name="requirements"></a>Requisitos

A lista a seguir é necessária para usar os recursos do AndroidX em aplicativos baseados em Xamarin:

- **Visual Studio** - Na atualização do Windows para visual studio 2019 versão 16.4 ou posterior. No macOS, atualize para o Visual Studio 2019 para mac versão 8.4 ou posterior.
- **Xamarin.Android** - Xamarin.Android 10.0 ou posterior deve ser instalado com o Visual Studio (Xamarin.Android é automaticamente instalado como parte do desenvolvimento móvel com carga de trabalho **.NET** no Windows e instalado como parte do **Visual Studio para Mac Installer**)
- **Java Developer Kit** - Xamarin.Android 10.0 desenvolvimento requer JDK 8. A distribuição do OpenJDK pela Microsoft é automaticamente instalada como parte do Visual Studio.
- **Android SDK** - Android SDK API 28 ou superior deve ser instalado através do Android SDK Manager.

## <a name="get-started"></a>Introdução

Você pode começar com o AndroidX incluindo qualquer [pacote AndroidX NuGet](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22) dentro do seu projeto Android. Saiba mais sobre como instalar e usar um pacote no [Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio) ou Visual Studio [para Mac](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio-mac)

## <a name="behavior-changes"></a>Alterações de comportamento

Como o AndroidX é um redesenho da Biblioteca de Suporte para Android, ele inclui etapas de migração que afetarão aplicativos Android construídos com a Biblioteca de Suporte para Android.

### <a name="package-name-change"></a>Mudança de nome do pacote
Os nomes dos pacotes foram alterados entre os pacotes antigos e novos. Abaixo você pode ver um exemplo dessas mudanças:

| Velho                    | Novo                    |
| ---------------------- | ---------------------- |
| android.support.**     | androidx. @             |
| android.design.**      | com.google.android.material. @ |
| android.support.test.** | androidx.test. @       |
| android.arch.**        | androidx. @             |
| android.arch.persistence.room.** | androidx.room. @ |
| android.arch.persistence.** | androidx.sqlite. @ |

Para obter mais detalhes sobre a nomeação do pacote, [consulte a documentação a seguir](https://developer.android.com/jetpack/androidx/migrate#artifact_mappings).

## <a name="migration-tooling"></a>Ferramentas de migração

Existem três etapas de migração que você deseja estar ciente para sua aplicação.

1. Se o seu aplicativo incluir **espaços de nome da Biblioteca de Suporte android e você gostaria de migrá-los para os namespaces do AndroidX,** você pode usar a nossa ferramenta **Migrate para AndroidX** IDE para cuidar da maioria dos cenários de namespace. 

Habilite o **Migrator AndroidX** via **Ferramentas > Opções > Xamarin > Configurações do Android** dentro do Visual Studio 2019 (você pode pular este passo no Visual Studio para Mac).

![Habilitar o AndroidX Migrator](~/android/platform/androidx-images/EnableAndroidXMigrator.png)

Clique com o botão direito do mouse no seu projeto e **migre para o AndroidX**.

![Migrar para o AndroidX](~/android/platform/androidx-images/MigrateToAndroidX.png)

> [!NOTE] 
> Você precisará fazer algumas alterações manuais de namespace para cenários que a ferramenta não cobre. Embora mapeiemos o pacote correto para você, é encorajado que você dê uma olhada nos [mapeamentos](https://developer.android.com/jetpack/androidx/migrate/artifact-mappings) oficiais de artefatos e [mapeamentos de classe](https://developer.android.com/jetpack/androidx/migrate/class-mappings) para ajudar na migração do seu projeto.

2. Se o seu aplicativo incluir **quaisquer dependências que não tenham sido migradas para o namespace do AndroidX,** você terá que usar a Biblioteca de Suporte android para o [pacote de migração AndroidX.](https://www.nuget.org/packages/Xamarin.AndroidX.Migration)
3. Se o seu aplicativo **não incluir quaisquer dependências que exijam migração de namespace do AndroidX,** você pode usar as [bibliotecas AndroidX no NuGet hoje](https://www.nuget.org/packages?q=Tags%3A%22AndroidX%22+Authors%3A%22Microsoft%22).

## <a name="troubleshooting"></a>Solução de problemas

- Certos pacotes de arquitetura dentro do AndroidX entrarão em conflito com as versões da Biblioteca de Suporte. Para corrigir isso, você deve usar a versão AndroidX desses pacotes e remover a versão da Biblioteca de Suporte. Por exemplo, se você `Xamarin.Android.Arch.Work.Runtime` estiver fazendo referência ao seu projeto, `AndroidX.Work` ele entrará em conflito com os tipos do pacote recém-adicionado.

## <a name="summary"></a>Resumo

Este artigo introduziu o AndroidX e explicou como instalar e configurar as ferramentas e pacotes mais recentes para o desenvolvimento do Xamarin.Android com AndroidX. Ele forneceu uma visão geral do que é o AndroidX. Ele incluiu links para documentação da API e tópicos do Android Developer para ajudá-lo a começar a criar aplicativos usando o AndroidX. Ele também destacou as mudanças de comportamento mais importantes do AndroidX e tópicos de solução de problemas que poderiam impactar os aplicativos existentes.

## <a name="related-links"></a>Links relacionados

- [Introdução ao AndroidX | O Show de Xamarin](https://www.youtube.com/watch?v=M_l3RjTev5A)
- [AndroidX](https://developer.android.com/jetpack/androidx)
- [Repositório Xamarin AndroidX GitHub](https://github.com/xamarin/AndroidX)
- [Repositório de migração Do Xamarin AndroidX GitHub](https://github.com/xamarin/XamarinAndroidXMigration)
