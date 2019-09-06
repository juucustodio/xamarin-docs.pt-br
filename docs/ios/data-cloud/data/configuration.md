---
title: Configurando o SQLite no Xamarin. iOS
description: Este documento descreve como determinar o local de um arquivo de banco de dados SQLite em um aplicativo Xamarin. iOS. Esses conceitos são relevantes independentemente do mecanismo de acesso a dados selecionado.
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: c0a8f57e3f4f351cf5b874ded2639b975ea71cad
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281904"
---
# <a name="configuring-sqlite-in-xamarinios"></a>Configurando o SQLite no Xamarin. iOS

Para usar o SQLite em seu aplicativo Xamarin. iOS, você precisará determinar o local de arquivo correto para o arquivo de banco de dados.

## <a name="database-file-path"></a>Caminho do arquivo de banco de dados

Independentemente do método de acesso a dados que você usar, você deve criar um arquivo de banco de dados antes que eles possam ser armazenados com o SQLite. Dependendo de qual plataforma você está direcionando, o local do arquivo será diferente. Para o iOS, você pode usar a classe de ambiente para construir um caminho válido, conforme mostrado no seguinte trecho de código:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Há outras coisas a serem levadas em consideração ao decidir onde armazenar o arquivo de banco de dados. No iOS, você pode desejar que o banco de dados seja submetido a backup automaticamente (ou não).

Se você quiser usar um local diferente em cada plataforma em seu aplicativo de plataforma cruzada, poderá usar uma diretiva de compilador, conforme mostrado para gerar um caminho diferente para cada plataforma:

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

Consulte o artigo [trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md) para obter mais informações sobre quais locais de arquivo usar no Ios. Consulte o documento [criando aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) para obter mais informações sobre como usar diretivas de compilador para escrever código específico para cada plataforma.

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
- [Receitas de dados do iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
