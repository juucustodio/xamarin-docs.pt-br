---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Diretrizes de portabilidade de aplicativos de área de trabalho
description: Uma explicação simple de como desacoplar existentes do Windows Forms ou aplicativos do WPF para criar aplicativos de plataforma cruzada para executar em macOS, iOS, Android, bem como UWP/Windows 10.
ms.technology: xamarin-crossplatform
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 35ffb9c92b5d3faf48f3e76a6702c2518ca80538
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="desktop-app-porting-guidance"></a>Diretrizes de portabilidade de aplicativos de área de trabalho

A maioria dos códigos de aplicativo podem ser categorizados em uma das seguintes áreas:

* Código de interface do usuário (por exemplo. janelas e botões)
* 3º controles de terceiros (por exemplo. gráficos de)
* (Por exemplo, a lógica de negócios regras de validação)
* Acesso e armazenamento de dados local
* Serviços Web e acesso a dados remotos

Para Windows Forms e WPF aplicativos escritos com o c# (ou Visual Basic.NET) uma quantidade significativa de lógica de negócios, acesso a dados locais e código de serviços da web pode ser compartilhado entre plataformas.

## <a name="net-portability-analyzer"></a>Analisador de portabilidade do .NET

Suporte do Visual Studio 2015 e 2017 o [.NET portabilidade analisador](https://docs.microsoft.com/en-us/dotnet/articles/standard/portability-analyzer) ([baixar para Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) que pode examinar seus aplicativos existentes e lhe quanto de código pode ser movido "como está" para outras plataformas . Você pode aprender mais sobre ele neste [vídeo do Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

Há também uma ferramenta de linha de comando pode ser baixada do [portabilidade Analyzer no GitHub](https://github.com/Microsoft/dotnet-apiport) e usado para fornecer os mesmos relatórios.

## <a name="x-of-my-code-is-portable-what-next"></a>"x % do meu código é portátil. Seguir?"

Esperamos que o analisador mostra uma grande parte do seu código é portátil, mas certamente haverá algumas partes de todos os aplicativos que _não é possível_ ser movidas para outras plataformas.

Diferentes blocos de código provavelmente cairá em um destes buckets explicados em mais detalhes abaixo:

* Código portátil utilizável novamente
* Código que requer alterações
* Código que não é portátil e requer uma reescrita

### <a name="re-useable-portable-code"></a>Código portátil utilizável novamente

Código do .NET que é gravado nas APIs disponíveis em todas as plataformas pode ser tomado inalterada de plataforma cruzada. Idealmente, você poderá mover todo esse código em uma biblioteca de classes portátil, a biblioteca compartilhada ou a biblioteca padrão do .NET e, em seguida, testá-lo dentro de seu aplicativo existente.

Biblioteca compartilhada, em seguida, pode ser adicionada a projetos de aplicativo para outras plataformas (como Android, iOS, macOS).

### <a name="code-that-requires-changes"></a>Código que requer alterações

Algumas APIs .NET pode não estar disponível em todas as plataformas. Se essas APIs existirem em seu código, você precisará reescrever essas seções para usar as APIs de plataforma cruzada.

Exemplos incluem o uso de APIs de reflexão que estão disponíveis no .NET 4.6, mas não estão disponíveis em todas as plataformas.

Depois que você gravou novamente o código usando APIs portáteis, você deve ser capaz de pacote que o código em uma biblioteca compartilhada e testá-lo dentro de seu aplicativo existente.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Código que não é portátil e requer uma reescrita

Exemplos de código que não é provavelmente entre plataformas incluem:

- **Interface do usuário** – telas de Windows Forms ou WPF não podem ser usadas em projetos Android ou iOS, por exemplo. A interface do usuário precisa ser reescrito, usando esta [controles comparação](~/cross-platform/desktop/controls/index.md) como uma referência.

- **Armazenamento específica de plataforma** -código que dependa de uma tecnologia específica de plataforma (como o SQL Server Express banco de dados local). Você precisará reescrever isso usando uma alternativa de plataforma cruzada (como SQLite para o mecanismo de banco de dados).
Algumas operações de sistema de arquivos também podem precisar ser ajustado, como UWP tem APIs ligeiramente diferentes para Android e iOS (por exemplo. Alguns sistemas de arquivos diferenciam maiusculas de minúsculas e outros não).

- **componentes de terceiros 3º** – Verifique se 3º componentes de terceiros em seus aplicativos estão disponíveis em outras plataformas. Algumas, como pacotes do NuGet não visuais, podem estar disponíveis, mas outras pessoas (especialmente visuais controles como players de mídia ou gráficos)

## <a name="tips-for-making-code-portable"></a>Dicas para fazer o código portátil

- **Injeção de dependência** – fornecer implementações diferentes para cada plataforma e

- **Abordagem em camadas pode** – se MVVM, MVC, MVP ou algum outro padrão que ajuda a separar o código portátil do código de plataforma específica.

- **Mensagens** – você pode usar transmissão de mensagem em seu código para desalocar acoplar interações entre diferentes partes do aplicativo.
