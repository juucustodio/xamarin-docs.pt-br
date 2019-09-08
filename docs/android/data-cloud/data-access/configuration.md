---
title: Configuração
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 5ebafa70239305210da631c3e9c34278f83b272b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754658"
---
# <a name="configuration"></a>Configuração

Para usar o SQLite em seu aplicativo Xamarin. Android, você precisará determinar o local de arquivo correto para o arquivo de banco de dados.

## <a name="database-file-path"></a>Caminho do arquivo de banco de dados

Independentemente do método de acesso a dados que você usar, você deve criar um arquivo de banco de dados antes que eles possam ser armazenados com o SQLite. Dependendo de qual plataforma você está direcionando, o local do arquivo será diferente. Para o Android, você pode usar a classe de ambiente para construir um caminho válido, conforme mostrado no seguinte trecho de código:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Há outras coisas a serem levadas em consideração ao decidir onde armazenar o arquivo de banco de dados. Por exemplo, no Android, você pode escolher se deseja usar o armazenamento interno ou externo.

Se você quiser usar um local diferente em cada plataforma em seu aplicativo de plataforma cruzada, poderá usar uma diretiva de compilador, conforme mostrado para gerar um caminho diferente para cada plataforma:

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

Para obter dicas sobre como usar o sistema de arquivos no Android, consulte a receita [procurar arquivos](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files) . Consulte o documento [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) para obter mais informações sobre como usar diretivas de compilador para escrever código específico para cada plataforma.

## <a name="threading"></a>Threading

Você não deve usar a mesma conexão de banco de dados SQLite entre vários threads. Tenha cuidado para abrir, usar e fechar todas as conexões que você criar no mesmo thread.

Para garantir que seu código não esteja tentando acessar o banco de dados SQLite a partir de vários threads ao mesmo tempo, faça um bloqueio manualmente sempre que você for acessar o banco de dados, desta forma:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Todo o acesso ao banco de dados (leituras, gravações, atualizações, etc.) deve ser encapsulado com o mesmo bloqueio. Deve-se ter cuidado para evitar uma situação de deadlock, garantindo que o trabalho dentro da cláusula Lock seja mantido simples e não chame outros métodos que também possam ter um bloqueio!

## <a name="related-links"></a>Links relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
