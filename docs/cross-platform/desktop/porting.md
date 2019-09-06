---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Diretrizes de portabilidade de aplicativos para desktop
description: Uma explicação simples de como desacoplar aplicativos Windows Forms ou WPF existentes para criar aplicativos de plataforma cruzada para serem executados no macOS, iOS, Android, bem como UWP/Windows 10.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: 2a678c2b1880cb69b1bece85ed558309aa611a62
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290339"
---
# <a name="desktop-app-porting-guidance"></a>Diretrizes de portabilidade de aplicativos para desktop

A maioria dos códigos de aplicativo pode ser categorizada em uma das seguintes áreas:

- Código da interface do usuário (por exemplo, janelas e botões)
- controles de terceiros (por exemplo, spersão
- Lógica de negócios (por exemplo, regras de validação)
- Armazenamento e acesso a dados locais
- Serviços Web e acesso a dados remotos

Para aplicativos Windows Forms e WPF escritos com C# (ou Visual Basic.net), uma quantidade surpreendente de lógica de negócios, acesso a dados locais e código de serviços Web pode ser compartilhada entre plataformas.

## <a name="net-portability-analyzer"></a>Analisador de portabilidade .NET

O Visual Studio 2017 e versões posteriores dão suporte ao [.net Portability Analyzer](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([download para Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)), que pode examinar seus aplicativos existentes e informar a quantidade de código que pode ser transportada "no estado em que se encontra" para outras plataformas. Você pode aprender mais sobre isso neste [vídeo do Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

Também há uma ferramenta de linha de comando que pode ser baixada do [analisador de portabilidade no GitHub](https://github.com/Microsoft/dotnet-apiport) e usada para fornecer os mesmos relatórios.

## <a name="x-of-my-code-is-portable-what-next"></a>"x% do meu código é portátil. O que vem a seguir? "

Felizmente, o analisador mostra que uma grande parte do seu código é portátil, mas certamente haverá algumas partes de cada aplicativo que _não podem_ ser movidas para outras plataformas.

Partes diferentes de código provavelmente se enquadram em um desses buckets, explicados em mais detalhes abaixo:

- Código portátil reutilizado
- Código que requer alterações
- Código que não é portátil e requer uma nova gravação

### <a name="re-useable-portable-code"></a>Código portátil reutilizado

O código .NET que é gravado em relação às APIs disponíveis em todas as plataformas pode ser feito de plataforma cruzada. Idealmente, você poderá mover todo esse código para uma biblioteca de classes portátil, uma biblioteca compartilhada ou uma biblioteca de .NET Standard e, em seguida, testá-lo em seu aplicativo existente.

Essa biblioteca compartilhada pode então ser adicionada a projetos de aplicativos para outras plataformas (como Android, iOS, macOS).

### <a name="code-that-requires-changes"></a>Código que requer alterações

Algumas APIs do .NET podem não estar disponíveis em todas as plataformas. Se essas APIs existirem no seu código, você precisará reescrever essas seções para usar APIs de plataforma cruzada.

Exemplos disso incluem o uso de APIs de reflexão que estão disponíveis no .NET 4,6, mas não estão disponíveis em todas as plataformas.

Depois de reescrever o código usando APIs portáteis, você deve ser capaz de empacotar esse código em uma biblioteca compartilhada e testá-lo em seu aplicativo existente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Código que não é portátil e requer uma nova gravação

Exemplos de código que não é provável que sejam entre plataformas, incluem:

- **Interface do usuário** – as telas Windows Forms ou WPF não podem ser usadas em projetos no Android ou Ios, por exemplo. Sua interface de usuário precisará ser reescrita, usando essa comparação de [controles](~/cross-platform/desktop/controls/index.md) como uma referência.

- Código de **Armazenamento específico da plataforma** que se baseia em uma tecnologia específica da plataforma (como um banco de dados SQL Server Express local). Você precisará escrever novamente isso usando uma alternativa de plataforma cruzada (como o SQLite para o mecanismo de banco de dados).
Algumas operações do sistema de arquivos também podem precisar ser ajustadas, já que a UWP tem APIs um pouco diferentes para Android e iOS (por exemplo, alguns sistemas de filediferenciam maiúsculas de minúsculas e outros não.

- **componentes de terceiros** – Verifique se os componentes de terceiros em seus aplicativos estão disponíveis em outras plataformas. Alguns, como pacotes NuGet não visuais, podem estar disponíveis, mas outros (especialmente controles visuais como gráficos ou media players)

## <a name="tips-for-making-code-portable"></a>Dicas para tornar o código portátil

- **Injeção de dependência** – fornecer implementações diferentes para cada plataforma e

- **Abordagem em camadas** – seja MVVM, MVC, MVP ou algum outro padrão que ajude a separar o código portátil do código específico da plataforma.

- **Mensagens** – você pode usar a passagem de mensagens em seu código para desassociar interações entre diferentes partes do aplicativo.
