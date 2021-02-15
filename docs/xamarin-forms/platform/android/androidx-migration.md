---
title: Migração do AndroidX no Xamarin.Forms
description: Este artigo explica por que o AndroidX existe e como migrar para o AndroidX em seu Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 98884003-E65A-4EB4-842D-66CFE27344A4
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 01/13/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b24ab21bf7ae5812916a968da3a59169b975e33a
ms.sourcegitcommit: 86663f94f8eddb808eb4504cd32ddaf217b6406c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166622"
---
# <a name="androidx-migration-in-no-locxamarinforms"></a>Migração do AndroidX no Xamarin.Forms

AndroidX substitui a biblioteca de suporte do Android. Este artigo explica por que o AndroidX existe, como ele afeta Xamarin.Forms e como migrar seu aplicativo para usar as bibliotecas do AndroidX.

> [!IMPORTANT]
> Se você estiver migrando um aplicativo para Xamarin.Forms 5,0, consulte [como fazer migrar meu aplicativo para Xamarin.Forms 5,0?](~/xamarin-forms/troubleshooting/questions/forms5-migration.md).

## <a name="history-of-androidx"></a>Histórico de AndroidX

A biblioteca de suporte do Android foi criada para fornecer recursos mais recentes em versões anteriores do Android. É uma camada de compatibilidade que permite que os desenvolvedores usem funcionalidades que podem não existir em todas as versões do sistema operacional Android e tenham fallbacks normais para versões mais antigas. A biblioteca de suporte também inclui classes de conveniência e auxiliar, ferramentas de depuração e utilitário e classes sofisticadas que dependem de outras classes de biblioteca de suporte para funcionar.

Embora a biblioteca de suporte tenha sido originalmente um único binário, ela cresceu e evoluiu para um conjunto de bibliotecas, que são quase essenciais para o desenvolvimento de aplicativos modernos. Esses são alguns recursos comumente usados da biblioteca de suporte:

- A `Fragment` classe de suporte.
- O `RecyclerView` , usado para gerenciar listas longas.
- Suporte a Multidex para aplicativos com mais de 65.536 métodos.
- A classe `ActivityCompat`.

AndroidX é uma substituição para a biblioteca de suporte, que não é mais mantida. todo o novo desenvolvimento de biblioteca ocorrerá na biblioteca AndroidX. AndroidX é uma biblioteca reprojetada que usa controle de versão semântico, nomes de pacote mais claros e melhor suporte para padrões comuns de arquitetura de aplicativo. A versão 1.0.0 do AndroidX é o equivalente binário para dar suporte à versão 28.0.0 da biblioteca. Para obter uma lista completa de mapeamentos de classe da biblioteca de suporte para o AndroidX, consulte [suporte a mapeamentos de classe de biblioteca](https://developer.android.com/jetpack/androidx/migrate/class-mappings) em developer.Android.com.

O Google criou um processo de migração chamado Jetifier com AndroidX. O Jetifier inspeciona o código de bytes jar durante o processo de compilação e remapeia as referências de biblioteca de suporte, tanto em códigos de aplicativo quanto em dependências, para seu equivalente AndroidX.

Em um Xamarin.Forms aplicativo, assim como em um aplicativo Java Android, as dependências do jar devem ser migradas para AndroidX. No entanto, as associações do Xamarin também devem ser migradas para apontar para os arquivos jar corretos e subjacentes. Xamarin.Forms Adicionado suporte para a migração automática de AndroidX na versão 4,5.

Para obter mais informações sobre AndroidX, consulte [visão geral do AndroidX](https://developer.android.com/jetpack/androidx) em developer.Android.com.

## <a name="automatic-migration-in-no-locxamarinforms"></a>Migração automática no Xamarin.Forms

Para migrar automaticamente para o AndroidX, um Xamarin.Forms projeto da plataforma Android deve:

- API do Android de destino versão 29 ou superior.
- Use a Xamarin.Forms versão 4,5 ou superior.
- Ter dependências diretas ou transitivas em bibliotecas de suporte do Android.

Depois de confirmar essas configurações em seu projeto, compile o aplicativo Android no Visual Studio 2019. Durante o processo de compilação, a IL (linguagem intermediária) é inspecionada e dá suporte às dependências de biblioteca e as associações são trocadas por dependências AndroidX. Se seu aplicativo tiver todas as dependências de AndroidX necessárias para compilar, você não observará nenhuma diferença no processo de compilação.

> [!IMPORTANT]
> A migração manual para o AndroidX resultará no processo de compilação mais rápido para seu aplicativo e é a abordagem recomendada para a migração do AndroidX. Isso envolve a substituição de dependências de biblioteca de suporte com dependências AndroidX e a atualização de seu código para consumir tipos AndroidX. Para obter mais informações, consulte [usar tipos de AndroidX](~/xamarin-forms/troubleshooting/questions/forms5-migration.md#use-androidx-types).

Se forem detectadas dependências AndroidX que não fazem parte do projeto, será relatado um erro de compilação que indica quais pacotes AndroidX estão ausentes. Um exemplo de erro de compilação é mostrado abaixo:

```
Could not find 37 AndroidX assemblies, make sure to install the following NuGet packages:
- Xamarin.AndroidX.Lifecycle.LiveData
- Xamarin.AndroidX.Browser
- Xamarin.Google.Android.Material
- Xamarin.AndroidX.Legacy.Supportv4
You can also copy and paste the following snippit into your .csproj file:
 <PackageReference Include="Xamarin.AndroidX.Lifecycle.LiveData" Version="2.1.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Browser" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.Google.Android.Material" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Legacy.Support.V4" Version="1.0.0-rc1" />
```

Os pacotes NuGet ausentes podem ser instalados por meio do Gerenciador de pacotes NuGet no Visual Studio ou instalados com a edição do arquivo Android. csproj para incluir os `PackageReference` itens XML listados no erro.

Depois que os pacotes ausentes são resolvidos, a recriação do projeto carrega os pacotes ausentes e seu projeto é compilado usando dependências de AndroidX em vez de oferecer suporte a dependências de biblioteca.

> [!NOTE]
> Se o projeto e as dependências do projeto não fizerem referência a bibliotecas de suporte do Android, o processo de migração não fará nada e não será executado.

## <a name="related-links"></a>Links relacionados

- [Como fazer migrar meu aplicativo para Xamarin.Forms 5,0?](~/xamarin-forms/troubleshooting/questions/forms5-migration.md)
- [Visão geral da biblioteca de suporte do Android](https://developer.android.com/topic/libraries/support-library/index) no Developer.Android.com
- [Visão geral do AndroidX](https://developer.android.com/jetpack/androidx) no Developer.Android.com
- [Mapeamentos de classe AndroidX](https://github.com/xamarin/AndroidX/blob/master/mappings/androidx-class-mapping.csv)
- [Assemblies de AndroidX](https://github.com/xamarin/AndroidX/blob/master/mappings/androidx-assemblies.csv)
