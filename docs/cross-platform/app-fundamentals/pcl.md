---
title: Introdução às bibliotecas de classes portáteis (PCL)
description: Este artigo apresenta projetos PCL (biblioteca de classes portáteis) e percorre a criação e o consumo de projetos PCL no Visual Studio para Mac e no Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: 63ef7045051f21259e01c36fc5f702585b04a57b
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934340"
---
# <a name="portable-class-libraries-pcl"></a>PCL (Bibliotecas de classe portáteis)

> [!TIP]
> As PCLs (bibliotecas de classes portáteis) são consideradas preteridas nas versões mais recentes do Visual Studio.
> Embora você ainda possa abrir, editar e compilar PCLs, para novos projetos, é recomendável usar [.net Standard bibliotecas](~/cross-platform/app-fundamentals/net-standard.md) para acessar uma área de superfície de API maior.

Um componente fundamental da criação de aplicativos de plataforma cruzada é a capacidade de compartilhar código em vários projetos específicos da plataforma. No entanto, isso é complicado pelo fato de que diferentes plataformas geralmente usam um subconjunto diferente da BCL (base Class Library) do .NET e, portanto, são, na verdade, compiladas em um perfil de biblioteca do .NET Core diferente. Isso significa que cada plataforma só pode usar bibliotecas de classes destinadas ao mesmo perfil para que pareçam exigir projetos de biblioteca de classes separados para cada plataforma.

Há três abordagens principais para o compartilhamento de código que abordam esse problema: **projetos de .net Standard**, **projetos de ativos compartilhados**e **projetos PCL (biblioteca de classes portáteis)**.

- **.Net Standard projetos** são a abordagem preferida para compartilhar código .net, leia mais sobre [.net Standard projetos e Xamarin](~/cross-platform/app-fundamentals/net-standard.md).
- Os **projetos de ativos compartilhados** usam um único conjunto de arquivos e oferecem uma maneira rápida e simples de compartilhar código em uma solução e geralmente emprega diretivas de compilação condicional para especificar caminhos de código para várias plataformas que o usarão (para obter mais informações, consulte o [artigo projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)).
- Os projetos **PCL** visam perfis específicos que dão suporte a um conjunto conhecido de classes/recursos de BCL. No entanto, o lado para o PCL é que eles geralmente exigem um esforço de arquitetura extra para separar o código específico do perfil em suas próprias bibliotecas.

Esta página explica como criar um projeto **PCL** que tem como alvo um perfil específico, que pode ser referenciado por vários projetos específicos da plataforma.

## <a name="what-is-a-portable-class-library"></a>O que é uma biblioteca de classes portátil?

Quando você cria um projeto de aplicativo ou um projeto de biblioteca, a DLL resultante é restrita ao trabalho na plataforma específica para a qual é criada. Isso impede que você grave um assembly para um aplicativo do Windows e, em seguida, reutilize-o em Xamarin. iOS e Xamarin. Android.

No entanto, quando você cria uma biblioteca de classes portátil, pode escolher uma combinação de plataformas nas quais você deseja que seu código seja executado. As opções de compatibilidade que você faz ao criar uma biblioteca de classes portátil são convertidas em um identificador de "perfil", que descreve as plataformas às quais a biblioteca dá suporte.

A tabela a seguir mostra alguns dos recursos que variam de acordo com a plataforma .NET. Para gravar um assembly PCL com garantia de execução em dispositivos/plataformas específicas, você simplesmente escolhe qual suporte é necessário ao criar o projeto.

|Recurso|.NET Framework|Aplicativos UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Núcleo|S|S|S|S|S|
|LINQ|S|S|S|S|S|
|IQueryable|S|S|S|7,5 +|S|
|Serialização|S|S|S|S|S|
|Anotações de dados|4.0.3 +|S|S||S|

A coluna Xamarin reflete o fato de que o Xamarin. iOS e o Xamarin. Android dão suporte a todos os perfis fornecidos com o Visual Studio, e a disponibilidade dos recursos em qualquer biblioteca que você criar será limitada somente pelas outras plataformas que você escolher para dar suporte.

Isso inclui perfis que são combinações de:

- .NET 4 ou .NET 4,5
- Silverlight 5
- Windows Phone 8
- Aplicativos UWP

Você pode ler mais sobre os recursos de perfis diferentes no [site da Microsoft](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) e ver o [Resumo do perfil PCL](https://portablelibraryprofiles.stephencleary.com/) de outro membro da Comunidade, que inclui informações de estrutura com suporte e outras observações.

**Benefícios**

1. Compartilhamento de código centralizado – escrever e testar código em um único projeto que pode ser consumido por outras bibliotecas ou aplicativos.
2. As operações de refatoração afetarão todo o código carregado na solução (a biblioteca de classes portátil e os projetos específicos da plataforma).
3. O projeto PCL pode ser facilmente referenciado por outros projetos em uma solução, ou o assembly de saída pode ser compartilhado para que outras pessoas façam referência em suas soluções.

**Desvantagens**

1. Como a mesma biblioteca de classes portátil é compartilhada entre vários aplicativos, bibliotecas específicas de plataforma não podem ser referenciadas (por exemplo, Community. CsharpSqlite. WP7).
2. O subconjunto da biblioteca de classes portátil pode não incluir classes que, de outra forma, estarão disponíveis tanto em MonoTouch quanto em mono para Android (como DllImport ou System. IO. File).

> [!NOTE]
> As bibliotecas de classes portáteis foram preteridas na versão mais recente do Visual Studio e, em vez disso, as [bibliotecas de .net Standard](net-standard.md) são recomendadas.

Até certo ponto, ambas as desvantagens podem ser burladas usando o padrão do provedor ou injeção de dependência para codificar a implementação real nos projetos da plataforma em relação a uma interface ou classe base que é definida na biblioteca de classes portátil.

Este diagrama mostra a arquitetura de um aplicativo de plataforma cruzada usando uma biblioteca de classes portátil para compartilhar código, mas também usando injeção de dependência para transmitir recursos dependentes da plataforma:

[![Este diagrama mostra a arquitetura de um aplicativo de plataforma cruzada usando uma biblioteca de classes portátil para compartilhar código, mas também usando injeção de dependência para transmitir recursos dependentes da plataforma](pcl-images/image1.png)](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Instruções Visual Studio para Mac

Esta seção explica como criar e usar uma biblioteca de classes portátil usando Visual Studio para Mac. Consulte a seção exemplo de PCL para obter uma implementação completa.

### <a name="creating-a-pcl"></a>Criando um PCL

Adicionar uma biblioteca de classes portátil à sua solução é muito semelhante a adicionar um projeto de biblioteca regular.

1. Na caixa de diálogo **novo projeto** , selecione a opção biblioteca de **> multiplataforma > a biblioteca portátil** :

    ![Criar um novo projeto PCL](pcl-images/image2.png)

1. Quando um PCL é criado no Visual Studio para Mac ele é configurado automaticamente com um perfil que funciona para Xamarin. iOS e Xamarin. Android. O projeto PCL será exibido conforme mostrado nesta captura de tela:

    ![Projeto PCL no painel de solução](pcl-images/image3.png)

A PCL agora está pronta para que o código seja adicionado. Ele também pode ser referenciado por outros projetos (projetos de aplicativo, projetos de biblioteca e até mesmo outros projetos PCL).

### <a name="editing-pcl-settings"></a>Editando configurações de PCL

Para exibir e alterar as configurações de PCL para este projeto, clique com o botão direito do mouse no projeto e escolha **opções > compilação > geral** para ver a tela mostrada aqui:

[![Opções de projeto PCL para definir o perfil](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

Clique em **alterar...** para alterar o perfil de destino para esta biblioteca de classes portátil.

Se o perfil for alterado depois que o código já tiver sido adicionado ao PCL, é possível que a biblioteca não seja mais compilada se o código fizer referência a recursos que não fazem parte do perfil selecionado recentemente.

## <a name="working-with-a-pcl"></a>Trabalhando com um PCL

Quando o código é escrito em uma biblioteca PCL, o editor de Visual Studio para Mac reconhecerá as limitações do perfil selecionado e ajustará adequadamente as opções de preenchimento automático. Por exemplo, esta captura de tela mostra as opções de preenchimento automático para System.IO usando o perfil padrão (Profile136) usado em Visual Studio para Mac – Observe que a barra de rolagem que indica cerca de metade das classes disponíveis é exibida (na verdade, há apenas 14 classes disponíveis).

[![Lista do IntelliSense de 14 classes na classe System.IO de um PCL](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

Compare isso com a conclusão automática de System.IO em um projeto Xamarin. iOS ou Xamarin. Android – há 40 classes disponíveis, incluindo classes comumente usadas, como `File` e `Directory` que não estão em nenhum perfil PCL.

[![Lista do IntelliSense de classes 40 no namespace System.IO .NET Framework](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

Isso reflete a compensação subjacente de usar PCL – a capacidade de compartilhar código diretamente em várias plataformas significa que determinadas APIs não estão disponíveis para você porque não têm implementações comparáveis em todas as plataformas possíveis.

### <a name="using-pcl"></a>Usando PCL

Depois que um projeto PCL tiver sido criado, você poderá adicionar uma referência a ele de qualquer aplicativo compatível ou projeto de biblioteca da mesma maneira que normalmente adiciona referências. Em Visual Studio para Mac, clique com o botão direito do mouse no nó referências e escolha **Editar referências...** em seguida, alterne para a guia **projetos** , conforme mostrado:

[![Adicionar uma referência a uma PCL por meio da opção Editar referências](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

A captura de tela a seguir mostra o painel de solução para o aplicativo de exemplo TaskyPortable, mostrando a biblioteca PCL na parte inferior e uma referência a essa biblioteca PCL no projeto Xamarin. iOS.

[![Solução de exemplo de TaskyPortable mostrando o projeto PCL](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

A saída de uma PCL (por ex., a DLL do assembly resultante) também pode ser adicionada como uma referência à maioria dos projetos. Isso torna o PCL uma maneira ideal de fornecer componentes e bibliotecas entre plataformas.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Passo a passos do Visual Studio

Esta seção explica como criar e usar uma biblioteca de classes portátil usando o Visual Studio. Consulte a seção exemplo de PCL para obter uma implementação completa.

### <a name="creating-a-pcl"></a>Criando um PCL

Adicionar um PCL à sua solução no Visual Studio é um pouco diferente para adicionar um projeto regular:

1. Na tela **Adicionar novo projeto** , selecione a opção **biblioteca de classes (portátil herdado)** . Observe que a descrição à direita aconselha que este tipo de projeto foi preterido.

    [![Janela novo projeto para criar uma biblioteca de classes portátil](pcl-images/image10-sml.png "Biblioteca de Classes Portátil")](pcl-images/image10.png#lightbox)

2. O Visual Studio solicitará imediatamente a seguinte caixa de diálogo para que o perfil possa ser configurado.
 Marque as plataformas que você precisa para dar suporte e pressione OK.

    [![Selecionar as plataformas de destino para a biblioteca](pcl-images/image11-sml.png "Marque as plataformas que você precisa para dar suporte e pressione OK")](pcl-images/image11.png#lightbox)

3. O projeto PCL será exibido conforme mostrado na Gerenciador de Soluções &ndash; o texto **(portátil)** aparece ao lado do nome do projeto para indicar que ele é um PCL:

    ![.NET Framework definido pelo perfil PCL](pcl-images/image12.png ".NET Framework definido pelo perfil PCL")

A PCL agora está pronta para que o código seja adicionado. Ele também pode ser referenciado por outros projetos (projetos de aplicativo, projetos de biblioteca e até mesmo outros projetos PCL).

### <a name="editing-pcl-settings"></a>Editando configurações de PCL

As configurações de PCL podem ser exibidas e alteradas clicando-se com o botão direito do mouse no projeto e escolhendo **propriedades > biblioteca** , conforme mostrado nesta captura de tela:

[![Editar os destinos da plataforma](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

Se o perfil for alterado depois que o código já tiver sido adicionado ao PCL, é possível que a biblioteca não seja mais compilada se o código fizer referência a recursos que não fazem parte do perfil selecionado recentemente.

> [!TIP]
> Também há uma mensagem avisando que **. O netstandard é o método recomendado para compartilhar o código**. Essa é uma indicação de que, embora o PCLs ainda tenha suporte, é recomendável atualizar para .NET Standard.

### <a name="working-with-a-pcl"></a>Trabalhando com um PCL

Quando o código é escrito em uma biblioteca PCL, o Visual Studio reconhecerá as limitações do perfil selecionado e ajustará as opções do IntelliSense de forma adequada. Por exemplo, esta captura de tela mostra as opções de preenchimento automático para System.IO usando o perfil padrão (Profile136) – Observe que a barra de rolagem que indica cerca de metade das classes disponíveis é exibida (na verdade, há apenas 14 classes disponíveis).

[![Número reduzido de classes de e/s disponíveis em um PCL](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

Compare isso com a conclusão automática de System.IO em um projeto regular – há 40 classes disponíveis, incluindo classes comumente usadas, como `File` e `Directory` que não estão em nenhum perfil PCL.

[![Muito mais classes de e/s disponíveis no .NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

Isso reflete a compensação subjacente de usar PCL – a capacidade de compartilhar código diretamente em várias plataformas significa que determinadas APIs não estão disponíveis para você porque não têm implementações comparáveis em todas as plataformas possíveis.

> [!TIP]
> .NET Standard 2,0 representa uma área de superfície de API muito maior que PCLs, incluindo o namespace System.IO. Para novos projetos, .NET Standard é recomendada sobre PCL.

### <a name="using-pcl"></a>Usando PCL

Depois que um projeto PCL tiver sido criado, você poderá adicionar uma referência a ele de qualquer aplicativo compatível ou projeto de biblioteca da mesma maneira que normalmente adiciona referências. No Visual Studio, clique com o botão direito do mouse no nó referências e escolha `Add Reference...` alternar para a guia **solução > projetos** , conforme mostrado:

[![Adicionar uma referência a um PCL por meio da guia adicionar projetos de referência](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

A captura de tela a seguir mostra o painel de solução para o aplicativo de exemplo TaskyPortable, mostrando a biblioteca PCL na parte inferior e uma referência a essa biblioteca PCL no projeto Xamarin. iOS.

[![Solução de exemplo de TaskyPortable mostrando uma biblioteca PCL](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

A saída de uma PCL (por ex., a DLL do assembly resultante) também pode ser adicionada como uma referência à maioria dos projetos.
Isso torna o PCL uma maneira ideal de fornecer componentes e bibliotecas entre plataformas.

-----

## <a name="pcl-example"></a>Exemplo de PCL

O aplicativo de exemplo [TaskyPortable](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/) demonstra como uma biblioteca de classes portátil pode ser usada com o Xamarin.
Aqui estão algumas capturas de tela dos aplicativos resultantes em execução no iOS e no Android:

[![Aqui estão algumas capturas de tela dos aplicativos resultantes em execução no iOS, Android e Windows Phone](pcl-images/image18.png)](pcl-images/image18.png#lightbox)

Ele compartilha vários dados e classes lógicas que são puramente códigos portáteis e também demonstra como incorporar requisitos específicos da plataforma usando injeção de dependência para a implementação do banco de dados SQLite.

A estrutura da solução é mostrada abaixo (no Visual Studio para Mac e no Visual Studio, respectivamente):

[![A estrutura da solução é mostrada aqui no Visual Studio para Mac e no Visual Studio, respectivamente](pcl-images/image19.png)](pcl-images/image19.png#lightbox)

Como o código SQLite-NET tem partes específicas da plataforma (para trabalhar com as implementações do SQLite em cada sistema operacional diferente) para fins de demonstração, ele foi refatorado em uma classe abstrata que pode ser compilada em uma biblioteca de classes portátil e o código real implementado como subclasses nos projetos iOS e Android.

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

A biblioteca de classes portátil é limitada aos recursos do .NET aos quais ele pode dar suporte. Como ele é compilado para ser executado em várias plataformas, ele não pode fazer uso da `[DllImport]` funcionalidade que é usada no SQLite-net. Em vez disso, o SQLite-NET é implementado como uma classe abstrata e, em seguida, referenciado por meio do restante do código compartilhado. Uma extração da API abstrata é mostrada abaixo:

```csharp
public abstract class SQLiteConnection : IDisposable {

    public string DatabasePath { get; private set; }
    public bool TimeExecution { get; set; }
    public bool Trace { get; set; }
    public SQLiteConnection(string databasePath) {
         DatabasePath = databasePath;
    }
    public abstract int CreateTable<T>();
    public abstract SQLiteCommand CreateCommand(string cmdText, params object[] ps);
    public abstract int Execute(string query, params object[] args);
    public abstract List<T> Query<T>(string query, params object[] args) where T : new();
    public abstract TableQuery<T> Table<T>() where T : new();
    public abstract T Get<T>(object pk) where T : new();
    public bool IsInTransaction { get; protected set; }
    public abstract void BeginTransaction();
    public abstract void Rollback();
    public abstract void Commit();
    public abstract void RunInTransaction(Action action);
    public abstract int Insert(object obj);
    public abstract int Update(object obj);
    public abstract int Delete<T>(T obj);

    public void Dispose()
    {
        Close();
    }
    public abstract void Close();

}
```

O restante do código compartilhado usa a classe abstrata para "armazenar" e "recuperar" objetos do banco de dados. Em qualquer aplicativo que usa essa classe abstrata, devemos passar uma implementação completa que fornece a funcionalidade real do banco de dados.

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid e TaskyiOS

Os projetos de aplicativos iOS e Android contêm a interface do usuário e outros códigos específicos da plataforma usados para conectar o código compartilhado no PCL.

Esses projetos também contêm uma implementação da API de banco de dados abstrata que funciona nessa plataforma. No iOS e Android, o mecanismo de banco de dados SQLite é integrado ao sistema operacional, de modo que a implementação pode usar `[DllImport]` conforme mostrado para fornecer a implementação concreta da conectividade de banco de dados. Um trecho do código de implementação específico da plataforma é mostrado aqui:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

A implementação completa pode ser vista no código de exemplo.

## <a name="summary"></a>Resumo

Este artigo abordou brevemente os benefícios e as armadilhas das bibliotecas de classes portáteis, demonstrou como criar e consumir PCLs de dentro Visual Studio para Mac e Visual Studio; e, finalmente, introduziu um aplicativo de exemplo completo – TaskyPortable – que mostra uma PCL em ação.

## <a name="related-links"></a>Links relacionados

- [TaskyPortable (exemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)
- [Compilar aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Visual Basic portátil](~/cross-platform/platform/visual-basic/index.md)
- [Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opções de código de compartilhamento](~/cross-platform/app-fundamentals/code-sharing.md)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
