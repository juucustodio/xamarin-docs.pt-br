---
title: Configuração
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: d418cb174861b962060757d4d1e0914e82ecd6fb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119794"
---
# <a name="configuration"></a>Configuração

Para usar o SQLite em seu aplicativo xamarin. Android, você precisará determinar o local de arquivo correto para seu arquivo de banco de dados.

## <a name="database-file-path"></a>Caminho do arquivo de banco de dados

Independentemente de qual método de acesso de dados que você usa, você deve criar um arquivo de banco de dados antes de dados podem ser armazenados com o SQLite. Dependendo da plataforma que você estiver direcionando o local do arquivo serão diferente. Para o Android você pode usar a classe de ambiente para construir um caminho válido, conforme mostrado no trecho de código a seguir:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Há outras coisas a levar em consideração ao decidir onde armazenar o arquivo de banco de dados. Por exemplo, no Android você pode optar por usar o armazenamento interno ou externo.

Se você quiser usar um local diferente em cada plataforma no seu aplicativo de plataforma cruzada você pode usar uma diretiva de compilador conforme mostrado para gerar um caminho diferente para cada plataforma:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Para obter dicas sobre como usar o sistema de arquivos no Android, consulte o [procurar arquivos](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files) receita. Consulte a [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento para obter mais informações sobre como usar diretivas de compilador para escrever código específico para cada plataforma.

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
- [Receitas de dados do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
