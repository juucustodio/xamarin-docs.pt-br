---
title: Provedores funcionam como conteúdo
ms.prod: xamarin
ms.assetid: B9E2EF89-7EBE-45F5-1ED9-7D2C70BE792C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: df4c2e10e34c308e4fadb44fba9c6a14714ae1b9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952840"
---
# <a name="how-content-providers-work"></a>Provedores funcionam como conteúdo

Há duas classes envolvidas em um `ContentProvider` interação:

- **ContentProvider** &ndash; implementa uma API que expõe um conjunto de dados de uma maneira padrão. Os principais métodos são a consulta, inserção, atualização e exclusão.

- **ContentResolver** &ndash; um proxy estático que se comunica com um `ContentProvider` para acessar seus dados, seja de dentro do mesmo aplicativo ou de outro aplicativo.

Um provedor de conteúdo normalmente é apoiado por um banco de dados SQLite, mas a API significa que consome o código não precisa saber nada sobre o SQL subjacente. Consultas são feitas por meio de um Uri usando constantes para fazer referência a nomes de coluna (para reduzir as dependências na estrutura de dados subjacente) e um `ICursor` é retornado para o código de consumo iterar sobre.


## <a name="consuming-a-contentprovider"></a>Consumindo um ContentProvider

`ContentProviders` expor sua funcionalidade por meio de um Uri que é registrado na **androidmanifest. XML** do aplicativo que publica os dados. Há uma convenção em que o Uri e as colunas de dados que são expostas devem estar disponíveis como constantes de tornar mais fácil associar aos dados. Interno do Android `ContentProviders` tudo fornecem classes de conveniência com constantes que fazem referência a estrutura de dados em de [ `Android.Providers` ](https://developer.xamarin.com/api/namespace/Android.Provider/) namespace.



### <a name="built-in-providers"></a>Provedores internos

Android oferece acesso a uma ampla variedade de sistema e dados de usuário usando `ContentProviders`:

- *Navegador* &ndash; indicadores e o histórico do navegador (requer a permissão `READ_HISTORY_BOOKMARKS` e/ou `WRITE_HISTORY_BOOKMARKS`).

- *CallLog* &ndash; chamadas recentes feitas ou recebidas com o dispositivo.

- *Contatos* &ndash; detalhados de informações da lista de contatos do usuário, incluindo as pessoas, telefones, fotos e grupos.

- *MediaStore* &ndash; conteúdo de dispositivo do usuário: áudio (álbuns, artistas, gêneros, listas de reprodução), (incluindo miniaturas) de imagens e vídeos.

- *As configurações* &ndash; preferências e configurações de dispositivo de todo o sistema.

- *UserDictionary* &ndash; conteúdo do dicionário definido pelo usuário usado para a entrada de texto preditivo.

- *Caixa postal* &ndash; histórico de mensagens de correio de voz.



## <a name="classes-overview"></a>Visão geral de classes

Classes primárias usadas ao trabalhar com um `ContentProvider` são mostrados aqui:

[![Diagrama de classe do aplicativo do provedor de conteúdo e interações de aplicativo de consumo](how-it-works-images/classdiagram1.png)](how-it-works-images/classdiagram1.png#lightbox)

Neste diagrama, o `ContentProvider` implementa consultas e registra o URI que outros aplicativos usam para localizar os dados. O `ContentResolver` atua como um proxy para o `ContentProvider` (consultar, inserir, atualizar e excluir métodos). O `SQLiteOpenHelper` contém dados usados pelo `ContentProvider`, mas ele não é diretamente exposto a aplicativos que consomem.
O `CursorAdapter` passa o cursor retornado pela `ContentResolver` para exibir em um `ListView`. O `UriMatcher` é uma classe auxiliar que analisa os URIs durante o processamento de consultas.

A finalidade de cada classe é descrita abaixo:

- **ContentProvider** &ndash; implementar métodos dessa classe abstrata para expor os dados. A API é disponibilizada para outros aplicativos por meio do atributo de Uri que é adicionado à definição de classe e classes.

- **SQLiteOpenHelper** &ndash; ajuda a implementa o armazenamento de dados SQLite que é exposto pelo `ContentProvider`.

- **UriMatcher** &ndash; uso `UriMatcher` em seu `ContentProvider` implementação para ajudar a gerenciar os Uris que são usados para consultar o conteúdo.

- **ContentResolver** &ndash; consumo código usa um `ContentResolver` para acessar um `ContentProvider` instância. As duas classes juntos cuidam dos problemas de comunicação entre processos, permitindo que os dados sejam facilmente compartilhados entre aplicativos. Nunca consumo código cria uma `ContentProvider` classe explicitamente; em vez disso, os dados são acessados com a criação de um cursor com base em um Uri exposto pelo `ContentProvider` aplicativo.

- **CursorAdapter** &ndash; uso `CursorAdapter` ou `SimpleCursorAdapter` para exibir os dados acessados por meio de um `ContentProvider`.

O `ContentProvider` API permite que os consumidores executar uma variedade de operações nos dados, tais como:

-  Consultar os dados para retornar listas ou registros individuais.
-  Modificar registros individuais.
-  Adicione novos registros.
-  Exclua registros.

Este documento contém um exemplo que usa um sistema forneceu `ContentProvider`, bem como um exemplo simples de somente leitura que implementa um personalizado `ContentProvider`.

