---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Diretrizes de portabilidade do aplicativo da área de trabalho
description: Uma explicação simple de como desacoplar formulários existentes do Windows ou aplicativos WPF para criar aplicativos de plataforma cruzada para ser executado no macOS, iOS, Android, bem como UWP/Windows 10.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 4bf1dea170bd6b63209693963d54cc2e16163eea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270017"
---
# <a name="desktop-app-porting-guidance"></a>Diretrizes de portabilidade do aplicativo da área de trabalho

A maioria dos códigos de aplicativo podem ser categorizadas em uma das seguintes áreas:

* Código de interface do usuário (por exemplo. janelas e botões)
* 3º controles de terceiros (por exemplo. gráficos)
* (Por exemplo, a lógica de negócios regras de validação)
* Acesso e armazenamento de dados local
* Serviços da Web e acesso a dados remotos

Para aplicativos Windows Forms e WPF escritos com o C# (ou Visual Basic.NET) uma quantidade surpreendente da lógica de negócios, o acesso a dados locais e o código de serviços da web pode ser compartilhada entre plataformas.

## <a name="net-portability-analyzer"></a>.NET portability Analyzer

Visual Studio 2017 e posteriores dão suporte à [.NET Portability Analyzer](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([baixar para Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) que pode examinar seus aplicativos existentes e lhe dizer quanto de código pode ser portado "como está" outros plataformas. Você pode aprender mais sobre ele neste [vídeo do Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

Há também uma ferramenta de linha de comando pode ser baixada do [Portability Analyzer no GitHub](https://github.com/Microsoft/dotnet-apiport) e usado para fornecer os mesmos relatórios.

## <a name="x-of-my-code-is-portable-what-next"></a>"x % do meu código é portátil. O que fazer agora?"

Espero que o analisador mostra uma grande parte do código é portátil, mas certamente haverá algumas partes de cada aplicativo que _não é possível_ ser movidas para outras plataformas.

Diferentes partes do código provavelmente cairá em um desses buckets, explicadas em mais detalhes abaixo:

* Código portátil pode ser utilizado novamente
* Código que requer alterações
* Código que não é portátil e requer uma reescrita

### <a name="re-useable-portable-code"></a>Código portátil pode ser utilizado novamente

Código .NET escrito em relação a APIs disponíveis em todas as plataformas pode ser adotado multiplataforma inalterada. Idealmente, você poderá mover todo esse código em uma biblioteca de classes portátil, a biblioteca compartilhada ou o .NET Standard Library e, em seguida, testá-lo dentro de seu aplicativo existente.

Essa biblioteca compartilhada, em seguida, pode ser adicionada a projetos de aplicativo para outras plataformas (como Android, iOS, macOS).

### <a name="code-that-requires-changes"></a>Código que requer alterações

Algumas APIs .NET podem não estar disponíveis em todas as plataformas. Se essas APIs existem em seu código, você precisará gravar novamente essas seções para usar as APIs de plataforma cruzada.

Exemplos incluem usam APIs de reflexão que estão disponíveis no .NET 4.6, mas não estão disponíveis em todas as plataformas.

Depois que você escreveu o código usando APIs portáteis novamente, você poderá empacotar o código em uma biblioteca compartilhada e testá-lo dentro de seu aplicativo existente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Código que não é portátil e requer uma reescrita

Exemplos de código que não é provavelmente será de plataforma cruzada:

- **Interface do usuário** – telas de Windows Forms ou WPF não podem ser usadas em projetos no Android ou iOS, por exemplo. Interface do usuário precisará ser gravadas novamente, usando esse [comparação controles](~/cross-platform/desktop/controls/index.md) como uma referência.

- **Armazenamento específico da plataforma** -código que se baseia em uma tecnologia específica da plataforma (por exemplo, o SQL Server Express banco de dados local). Você precisará gravar novamente isso usando uma alternativa de plataforma cruzada (como o SQLite para o mecanismo de banco de dados).
Algumas operações de sistema de arquivos talvez precise ser ajustado, já que a UWP tem um pouco diferentes APIs para Android e iOS (por exemplo. Alguns sistemas de arquivos diferenciam maiusculas de minúsculas e outros não são).

- **componentes de terceiros 3ª** – verificar se os componentes de terceiros 3ª em seus aplicativos estão disponíveis em outras plataformas. Alguns deles, como pacotes do NuGet não visual, podem estar disponíveis, mas outras pessoas (especialmente visuais controles como players de mídia ou gráficos)

## <a name="tips-for-making-code-portable"></a>Dicas para fazer o código portátil

- **Injeção de dependência** – fornecer implementações diferentes para cada plataforma, e

- **Abordagem em camadas** – indica se o MVVM, MVC, MVP ou algum outro padrão que ajuda você a separar o código portátil do código específico da plataforma.

- **Mensagens** – você pode usar a passagem de mensagem em seu código desprovisionar acoplar interações entre diferentes partes do aplicativo.
