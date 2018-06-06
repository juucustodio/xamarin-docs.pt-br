---
title: Configurando SQLite no xamarin
description: Este documento descreve como determinar o local de um arquivo de banco de dados SQLite em um aplicativo xamarin. Esses conceitos são relevantes, independentemente do mecanismo de acesso de dados selecionada.
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 57645c0bb947a60a3d74436b752210d1bac18124
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784375"
---
# <a name="configuring-sqlite-in-xamarinios"></a>Configurando SQLite no xamarin

Para usar SQLite em seu aplicativo xamarin, você precisará determinar o local de arquivo correto para o arquivo de banco de dados.

## <a name="database-file-path"></a>Caminho do arquivo de banco de dados

Independentemente de qual método de acesso de dados que você usar, você deve criar um arquivo de banco de dados antes de dados podem ser armazenados com SQLite. Dependendo de qual plataforma de destino, o local do arquivo será diferente. Para iOS você pode usar a classe de ambiente para construir um caminho válido, conforme mostrado no trecho de código a seguir:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Há outras coisas que devem ser considerados ao decidir onde armazenar o arquivo de banco de dados. No iOS, você pode preferir o banco de dados para backup automaticamente (ou não).

Se você quiser usar um local diferente em cada plataforma de aplicativo da plataforma cruzada você pode usar uma diretiva de compilador conforme mostrado para gerar um caminho diferente para cada plataforma:

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

Consulte o [trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md) artigo para obter mais informações sobre quais locais de arquivo a ser usado no iOS. Consulte o [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento para obter mais informações sobre como usar diretivas de compilador para escrever código específico para cada plataforma.

## <a name="threading"></a>Threading

Você não deve usar a mesma conexão de banco de dados SQLite entre vários threads. Tenha cuidado para abrir, usar e, em seguida, feche quaisquer conexões que você criar no mesmo thread.

Para garantir que seu código não está tentando acessar o banco de dados SQLite de vários threads ao mesmo tempo, execute manualmente um bloqueio sempre que você pretende acessar o banco de dados, como este:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Todo o acesso de banco de dados (leituras, gravações, atualizações, etc.) deve ser encapsulado com o mesmo bloqueio. Deve-se ter cuidado para evitar uma situação de deadlock, garantindo que o trabalho dentro da cláusula de bloqueio é mantido simple e não chamar outros métodos que também podem usar um bloqueio!


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
