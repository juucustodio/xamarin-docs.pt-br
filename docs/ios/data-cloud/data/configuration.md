---
title: Configurando o SQLite no xamarin. IOS
description: Este documento descreve como determinar o local para um arquivo de banco de dados SQLite em um aplicativo xamarin. IOS. Esses conceitos são relevantes, independentemente do mecanismo de acesso de dados selecionada.
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: afb582129a5587e6a386a0ce2c23368af9bcd619
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650254"
---
# <a name="configuring-sqlite-in-xamarinios"></a>Configurando o SQLite no xamarin. IOS

Para usar o SQLite em seu aplicativo xamarin. IOS, você precisará determinar o local de arquivo correto para seu arquivo de banco de dados.

## <a name="database-file-path"></a>Caminho do arquivo de banco de dados

Independentemente de qual método de acesso de dados que você usa, você deve criar um arquivo de banco de dados antes de dados podem ser armazenados com o SQLite. Dependendo da plataforma que você estiver direcionando o local do arquivo serão diferente. Para iOS você pode usar a classe de ambiente para construir um caminho válido, conforme mostrado no trecho de código a seguir:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Há outras coisas a levar em consideração ao decidir onde armazenar o arquivo de banco de dados. No iOS pode ser o banco de dados para backup automaticamente (ou não).

Se você quiser usar um local diferente em cada plataforma no seu aplicativo de plataforma cruzada você pode usar uma diretiva de compilador conforme mostrado para gerar um caminho diferente para cada plataforma:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Consulte a [trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md) artigo para obter mais informações sobre quais locais de arquivo para usar no iOS. Consulte a [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento para obter mais informações sobre como usar diretivas de compilador para escrever código específico para cada plataforma.

## <a name="threading"></a>Threading

Você não deve usar a mesma conexão de banco de dados SQLite entre vários threads. Tenha cuidado para abrir, usar e, em seguida, feche todas as conexões que você criar no mesmo thread.

Para garantir que seu código não está tentando acessar o banco de dados SQLite de vários threads ao mesmo tempo, execute manualmente um bloqueio sempre que você pretende acessar o banco de dados, como este:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Todo o acesso de banco de dados (leituras, gravações, atualizações, etc.) deve ser empacotado com o mesmo bloqueio. Tome cuidado para evitar uma situação de deadlock, garantindo que o trabalho dentro da cláusula de bloqueio é mantido simple e não chamar outros métodos que também podem usar um bloqueio!


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
