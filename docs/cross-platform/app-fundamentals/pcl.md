---
title: Introdução às bibliotecas de classes portátil (PCL)
description: Este artigo apresenta os projetos de biblioteca de classe portátil (PCL) e explica a criação e consumo de projetos PCL no Visual Studio para Mac e Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: a1e7152ca0f24d1ac660742e97907ea9300df9a7
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666873"
---
# <a name="portable-class-libraries-pcl"></a>PCL (Bibliotecas de classe portáteis)

> [!WARNING]
> Bibliotecas de classes portáteis (PCLs) são consideradas preterida nas versões mais recentes do Visual Studio.
> Enquanto você ainda pode abrir, editar e compilar PCLs, para novos projetos, é recomendável usar [bibliotecas .NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

Um componente fundamental da criação de aplicativos de plataforma cruzada é a possibilidade de compartilhar código entre vários projetos específicos da plataforma. No entanto, isso é complicado pelo fato de que plataformas diferentes geralmente usam um sub conjunto diferente de .NET Base classe Library (BCL) e, portanto, na verdade, são criadas para um perfil diferente do .NET Core Library. Isso significa que cada plataforma só pode usar bibliotecas de classes que são direcionadas para o mesmo perfil para que eles seriam exibidos exigir que os projetos de biblioteca de classe separada para cada plataforma.

Há três abordagens principais para compartilhamento de código que resolvem esse problema: **projetos do .NET Standard**, **projetos de ativos compartilhados**, e **projetos de biblioteca de classe portátil (PCL)**.

- **Projetos do .NET standard** são a abordagem preferencial para o compartilhamento de código .NET, leia mais sobre [projetos do .NET Standard e o Xamarin](~/cross-platform/app-fundamentals/net-standard.md).
- **Projetos de ativos compartilhados** usar um único conjunto de arquivos e oferece uma maneira rápida e simple de compartilhar código dentro de uma solução e geralmente emprega diretivas de compilação condicional para especificar caminhos de código para várias plataformas que irá usá-lo (para obter mais informações informações, consulte o [artigo projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)).
- **PCL** perfis específicos que dão suporte a um conjunto conhecido de BCL classes/recursos de destino de projetos. No entanto, o lado para PCL é que eles normalmente exigem esforço extra de arquitetura para separar o código específico do perfil em suas próprias bibliotecas.

Esta página explica como criar uma **PCL** projeto que tem como alvo um perfil específico, o que pode ser referenciado por vários projetos específicos da plataforma.

## <a name="what-is-a-portable-class-library"></a>O que é uma biblioteca de classes portáteis?

Quando você cria um projeto de aplicativo ou um projeto de biblioteca, a DLL resultante é restrita a trabalhar na plataforma específica, para que ele será criado. Isso evita que você escrever um assembly para um aplicativo do Windows e, em seguida, usá-lo novamente no xamarin. IOS e xamarin. Android.

No entanto, quando você cria uma biblioteca de classes portátil, você pode escolher uma combinação de plataformas que você deseja que seu código seja executado no. As opções de compatibilidade feitas durante a criação de uma biblioteca de classes portátil são convertidas em um identificador "Perfil", que descreve as plataformas em que a biblioteca suporta.

A tabela a seguir mostra alguns dos recursos que variam de acordo com a plataforma .NET. Para gravar um assembly PCL que certamente será executado em dispositivos/plataformas específicas você simplesmente escolher qual suporte é necessário quando você cria o projeto.

|Recurso|.NET Framework|Aplicativos UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Núcleo|S|S|S|S|S|
|LINQ|S|S|S|S|S|
|IQueryable|S|S|S|7.5 +|S|
|Serialização|S|S|S|S|S|
|Anotações de dados|4.0.3 +|S|S||S|

A coluna de Xamarin reflete o fato de que o xamarin. IOS e xamarin. Android dá suporte a todos os perfis que acompanham o Visual Studio e a disponibilidade de recursos em todas as bibliotecas que você cria só será limitada por outras plataformas que você escolher para dar suporte.

Isso inclui os perfis que são combinações de:

- O .NET 4 ou o .NET 4.5
- Silverlight 5
- Windows Phone 8
- Aplicativos UWP

Você pode ler mais sobre os recursos a diferentes perfis na [site da Microsoft](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx) e ver outro membro da comunidade [perfil PCL resumo](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) que inclui suporte para informações de estrutura e outras observações.

**Benefícios**

1. Compartilhamento de código centralizado – escrever e testar o código em um único projeto que pode ser consumido por outras bibliotecas ou aplicativos.
2. Operações de refatoração afetará todo o código carregado na solução (biblioteca de classes portátil e os projetos específicos da plataforma).
3. O projeto PCL pode ser facilmente referenciado por outros projetos em uma solução ou o assembly de saída pode ser compartilhado para outras pessoas fazer referência a suas soluções.

**Desvantagens**

1. Como a mesma biblioteca de classes portátil é compartilhada entre vários aplicativos, bibliotecas específicas da plataforma (por exemplo, não podem ser referenciadas Community.CsharpSqlite.WP7).
2. O subconjunto da biblioteca de classes portátil pode não incluir classes que estariam disponíveis no MonoTouch e Mono para Android (como DllImport ou IO).

> [!NOTE]
> Bibliotecas de classes portáteis foram preteridas na versão mais recente do Visual Studio, e [.NET Standard Libraries](net-standard.md) são recomendadas em vez disso.

Até certo ponto as duas desvantagens podem ser evitadas usando o padrão do provedor ou a injeção de dependência para a implementação real dos projetos de plataforma em relação a uma interface ou classe base que é definida na biblioteca de classes portátil de código.

Este diagrama mostra a arquitetura de um aplicativo de plataforma cruzada usando uma biblioteca de classes portátil para compartilhar código, mas também usando a injeção de dependência para passar os recursos dependentes de plataforma:

[![](pcl-images/image1.png "Este diagrama mostra a arquitetura de um aplicativo de plataforma cruzada usando uma biblioteca de classes portátil para compartilhar código, mas também usando a injeção de dependência para passar os recursos dependentes de plataforma")](pcl-images/image1.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>O Visual Studio para Mac instruções passo a passo

Esta seção explica como criar e usar uma biblioteca de classes portátil, usando o Visual Studio para Mac. Consulte a seção de exemplo de PCL para uma implementação completa.

### <a name="creating-a-pcl"></a>Criando uma PCL

Adicionar uma biblioteca de classes portátil à sua solução é muito semelhante à adição de um projeto de biblioteca regular.

1. No **novo projeto** diálogo, selecione o **multiplataforma > Biblioteca > biblioteca portátil** opção:

    ![Criar um novo projeto PCL](pcl-images/image2.png)

1. Quando uma PCL é criada no Visual Studio para Mac, ele é automaticamente configurado com um perfil que funciona para xamarin. IOS e xamarin. Android. O projeto PCL será exibida conforme mostrado nesta captura de tela:

    ![Projeto PCL no painel de soluções](pcl-images/image3.png)

O PCL agora está pronto para o código a ser adicionado. Ele também pode ser referenciado por outros projetos (projetos de aplicativos, projetos de biblioteca e até mesmo outros projetos PCL).

### <a name="editing-pcl-settings"></a>Editar configurações de PCL

Para exibir e alterar as configurações de PCL para este projeto, clique com botão direito no projeto e escolha **opções > Build > geral** para ver a tela mostrada aqui:

[![Opções de projeto de PCL para definir o perfil](pcl-images/image4-sml.png)](pcl-images/image4.png#lightbox)

Clique em **alteração...**  para alterar o perfil de destino para esta biblioteca de classes portátil.

Se o perfil for alterado após o código já foi adicionado à PCL, é possível que a biblioteca não serão mais compilados se o código faz referência a recursos que não fazem parte do perfil selecionado mais recentemente.

## <a name="working-with-a-pcl"></a>Trabalhando com uma PCL

Quando o código é escrito em uma biblioteca PCL, o editor Visual Studio para Mac reconhecerá as limitações do perfil selecionado e ajustar as opções de preenchimento automático de acordo. Por exemplo, nesta captura de tela mostra as opções de preenchimento automático para System.IO usando o perfil padrão (Profile136) usado no Visual Studio para Mac – Observe a barra de rolagem que indica a cerca de metade das classes disponíveis é exibida (na verdade existem apenas 14 classes disponíveis).

[![Lista do IntelliSense das 14 classes na classe System.IO de uma PCL](pcl-images/image6.png)](pcl-images/image6.png#lightbox)

Comparar que com o System.IO preenchimento automático em um projeto xamarin. IOS ou xamarin. Android – há 40 classes comumente disponível, incluindo classes, como usadas `File` e `Directory` que não estão em perfis de PCL.

[![Lista do IntelliSense de 40 classes no namespace System.IO do .NET Framework](pcl-images/image7.png)](pcl-images/image7.png#lightbox)

Isso reflete a compensação subjacente do uso de PCL – a capacidade de compartilhar o código diretamente em muitas plataformas significa que determinadas APIs não estão disponíveis para você, porque eles não têm implementações comparáveis em todas as plataformas possíveis.

### <a name="using-pcl"></a>Usando o PCL

Quando um projeto PCL tiver sido criado, você pode adicionar uma referência a ele de qualquer projeto de aplicativo ou biblioteca compatível da mesma maneira que você normalmente adicionar referências. No Visual Studio para Mac, clique com botão direito no nó referências e escolha **Editar referências...**  , em seguida, alterne para o **projetos** guia conforme mostrado:

[![Adicione uma referência a uma PCL por meio da opção de editar referências](pcl-images/image8.png)](pcl-images/image8.png#lightbox)

Captura de tela a seguir mostra o painel de soluções para o aplicativo de exemplo TaskyPortable, mostrando a biblioteca PCL na parte inferior e uma referência a essa biblioteca PCL no projeto xamarin. IOS.

[![Projeto de PCL TaskyPortable exemplo solução mostrando](pcl-images/image9.png)](pcl-images/image9.png#lightbox)

A saída de uma PCL (ie. a DLL do assembly resultante) também pode ser adicionado como uma referência para a maioria dos projetos. Isso torna o PCL maneira ideal para o envio de bibliotecas e componentes de plataforma cruzada.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Passo a passo de Visual Studio

Esta seção explica como criar e usar uma biblioteca de classes portátil, usando o Visual Studio. Consulte a seção de exemplo de PCL para uma implementação completa.

### <a name="creating-a-pcl"></a>Criando uma PCL

Adicionar uma PCL para sua solução no Visual Studio é um pouco diferente para adicionar um projeto regular:

1. No **adicionar novo projeto** tela, selecione a **biblioteca de classes (portátil herdado)** opção. Observe que a descrição no lado direito aconselha que esse tipo de projeto foi preterido.

    [![Janela novo projeto para criar uma biblioteca de classes portátil](pcl-images/image10-sml.png "biblioteca de classes portátil")](pcl-images/image10.png#lightbox)

2. Visual Studio solicitará imediatamente com a seguinte caixa de diálogo para que o perfil pode ser configurado.
 As plataformas que você precisa para dar suporte e pressione Okey de escala.

    [![Selecione as plataformas de destino para a biblioteca](pcl-images/image11-sml.png "as plataformas que você precisa para dar suporte e pressione Okey de escala")](pcl-images/image11.png#lightbox)

3. O projeto PCL aparecerá como mostrado no Gerenciador de soluções &ndash; o texto **(portátil)** aparece ao lado do nome do projeto para indicar que ele é uma PCL:

    ![NET Framework definidas pelo perfil de PCL](pcl-images/image12.png "NET Framework definidas pelo perfil de PCL")

O PCL agora está pronto para o código a ser adicionado. Ele também pode ser referenciado por outros projetos (projetos de aplicativo, projetos de biblioteca e até mesmo outros projetos PCL).

### <a name="editing-pcl-settings"></a>Editar configurações de PCL

As configurações de PCL podem ser exibidas e alteradas clicando com botão direito no projeto e escolhendo **Propriedades > biblioteca** , conforme mostrado nesta captura de tela:

[![Editar os destinos de plataforma](pcl-images/image13-sml.png)](pcl-images/image13.png#lightbox)

Se o perfil for alterado após o código já foi adicionado à PCL, é possível que a biblioteca não serão mais compilados se o código faz referência a recursos que não fazem parte do perfil selecionado mais recentemente.

> [!TIP]
> Também há uma mensagem informando que **. NETStandard é o método recomendado para compartilhar código**. Isso é uma indicação de que, embora PCLs, ainda são suportados, é recomendável atualizar para o .NET Standard.

### <a name="working-with-a-pcl"></a>Trabalhando com uma PCL

Quando o código é escrito em uma biblioteca PCL, o Visual Studio reconhece as limitações do perfil selecionado e ajustar as opções do Intellisense adequadamente. Por exemplo, nesta captura de tela mostra as opções de preenchimento automático para System.IO usando o perfil padrão (Profile136) – Observe a barra de rolagem que indica a cerca de metade das classes disponíveis é exibida (na verdade são apenas 14 classes disponível).

[![Redução do número de classes de e/s disponíveis em uma PCL](pcl-images/image14.png)](pcl-images/image14.png#lightbox)

Comparar que com o preenchimento automático em um projeto de regular de System.IO há 40 classes comumente disponível, incluindo classes, como usadas `File` e `Directory` que não estão em perfis de PCL.

[![Muitos mais classes de e/s disponíveis no .NET Framework](pcl-images/image15.png)](pcl-images/image15.png#lightbox)

Isso reflete a compensação subjacente do uso de PCL – a capacidade de compartilhar o código diretamente em muitas plataformas significa que determinadas APIs não estão disponíveis para você, porque eles não têm implementações comparáveis em todas as plataformas possíveis.

> [!TIP]
> .NET standard 2.0 representa uma quantidade maior API área de superfície que PCLs, incluindo o namespace System.IO. Para novos projetos, .NET Standard é mais recomendada PCL.

### <a name="using-pcl"></a>Usando o PCL

Quando um projeto PCL tiver sido criado, você pode adicionar uma referência a ele de qualquer projeto de aplicativo ou biblioteca compatível da mesma maneira que você normalmente adicionar referências. No Visual Studio, clique com botão direito no nó referências e escolha `Add Reference...` , em seguida, alterne para o **solução > projetos** guia conforme mostrado:

[![Adicione uma referência a uma PCL por meio da guia adicionar projetos de referência](pcl-images/image16.png)](pcl-images/image16.png#lightbox)

Captura de tela a seguir mostra o painel de solução para o aplicativo de exemplo TaskyPortable, mostrando a biblioteca PCL na parte inferior e uma referência a essa biblioteca PCL no projeto xamarin. IOS.

[![Solução de exemplo TaskyPortable mostrando uma biblioteca PCL](pcl-images/image17.png)](pcl-images/image17.png#lightbox)

A saída de uma PCL (ie. a DLL do assembly resultante) também pode ser adicionado como uma referência para a maioria dos projetos.
Isso torna o PCL maneira ideal para o envio de bibliotecas e componentes de plataforma cruzada.

-----

## <a name="pcl-example"></a>Exemplo PCL

O [TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/) aplicativo de exemplo demonstra como uma biblioteca de classes portátil pode ser usada com o Xamarin.
Aqui estão algumas capturas de tela dos aplicativos resultantes em execução no iOS e Android:

[![](pcl-images/image18.png "Aqui estão algumas capturas de tela dos aplicativos resultantes que executam o iOS, Android e Windows Phone")](pcl-images/image18.png#lightbox)

Ele compartilha um número de classes de dados e lógica que são código puramente portátil, e ele também demonstra como incorporar os requisitos específicos da plataforma usando injeção de dependência para a implementação do banco de dados SQLite.

A estrutura da solução é mostrada abaixo (no Visual Studio para Mac e Visual Studio, respectivamente):

[![](pcl-images/image19.png "A estrutura da solução é mostrada aqui no Visual Studio para Mac e Visual Studio, respectivamente")](pcl-images/image19.png#lightbox)

Porque o código SQLite-NET tem partes específicas da plataforma (para trabalhar com as implementações do SQLite em cada sistema operacional diferente) para demonstração fins foi refatorado em um resumo da classe que pode ser compilada em uma biblioteca de classes portátil, e o código real implementado como subclasses em projetos do Android e iOS.

### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

Biblioteca de classes portátil é limitada nos recursos do .NET que ele pode suportar. Porque ele é compilado para ser executado em várias plataformas, não é possível fazer uso de `[DllImport]` funcionalidade que é usada em SQLite-NET. Em vez disso, SQLite-NET é implementado como uma classe abstrata e, em seguida, referenciado pelo restante do código compartilhado. Abaixo está um trecho da API abstrata:

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

O restante do código compartilhado usa a classe abstrata "armazenar" e "recuperar" objetos do banco de dados. Em qualquer aplicativo que usa essa classe abstrata deve passamos em uma implementação completa que fornece a funcionalidade de banco de dados real.

### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid e TaskyiOS

Os projetos de aplicativo Android e iOS contém a interface do usuário e outro código específico da plataforma usado para transmissão para cima o código compartilhado na PCL.

Esses projetos também contêm uma implementação do banco de dados API que funciona nessa plataforma abstrata. No iOS e Android o Sqlite mecanismo de banco de dados é inserido no sistema operacional, para que a implementação possa usar `[DllImport]` conforme mostrado para fornecer a implementação concreta da conectividade de banco de dados. Um trecho de código de implementação específico da plataforma é mostrado aqui:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```

A implementação completa pode ser vista no código de exemplo.

## <a name="summary"></a>Resumo

Este artigo tem brevemente discutidas as vantagens e desvantagens das bibliotecas de classes portátil, demonstrou como criar e consumir PCLs de dentro do Visual Studio para Mac e Visual Studio; e finalmente apresentados a um aplicativo de exemplo completo – TaskyPortable – que mostra uma PCL em ação.

## <a name="related-links"></a>Links relacionados

- [TaskyPortable (amostra)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Compilar aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Portátil Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](https://docs.microsoft.com/dotnet/standard/cross-platform/cross-platform-development-with-the-portable-class-library)
