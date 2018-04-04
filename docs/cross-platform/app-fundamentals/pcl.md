---
title: Introdução às bibliotecas de classes portátil
description: Este artigo apresenta os projetos de biblioteca de classe portátil (PCL) e explica a criação e consumo projetos PCL no Visual Studio para Mac e Visual Studio.
ms.prod: xamarin
ms.assetid: 76ba8f7a-9b6e-40f5-9a29-ff1274ece4f2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: feef0a4083d2455cc189ddab6ed22762c044d848
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-portable-class-libraries"></a>Introdução às bibliotecas de classes portátil

_Este artigo apresenta os projetos de biblioteca de classe portátil (PCL) e explica a criação e consumo projetos PCL no Visual Studio para Mac e Visual Studio._


Um componente fundamental da criação de aplicativos de plataforma cruzada é a possibilidade de compartilhar código entre vários projetos de plataforma específica. No entanto, isso é complicado pelo fato de que diferentes plataformas geralmente usam um sub conjunto diferente do .NET biblioteca BCL (Base Class) e, portanto, na verdade, são criadas para um perfil de biblioteca diferente .NET Core. Isso significa que cada plataforma só pode usar bibliotecas de classes que são direcionadas para o mesmo perfil, portanto apareceriam exigir projetos de biblioteca de classe separada para cada plataforma.

Há três métodos principais para o compartilhamento de código que resolva esse problema: **projetos .NET padrão**, **projetos de biblioteca de classe portátil (PCL)**, e **projetos de ativo compartilhado**.

- **Projetos .NET padrão** [.NET padrão](~/cross-platform/app-fundamentals/net-standard.md).
-  **PCL** projetos perfis específicos que dão suporte a um conjunto conhecido de BCL classes/recursos de destino. No entanto, o lado para PCL é que eles normalmente exigem esforço extra de arquitetura para separar o código específico do perfil em suas próprias bibliotecas. Para obter uma discussão mais detalhada sobre essas duas abordagens, consulte o [guia Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md) .
-  **Projetos de ativos compartilhados** usa um único conjunto de arquivos e oferece um modo rápido e simple compartilhar código dentro de uma solução e geralmente emprega diretivas de compilação condicional para especificar caminhos de código para várias plataformas que usarão (para obter mais informações informações, consulte o [artigo projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) e [Configurando um guia de solução de plataforma cruzada Xamarin](~/cross-platform/app-fundamentals/code-sharing.md) ).


Esta página explica como criar um **PCL** projeto que tem como alvo um perfil específico, o que pode ser referenciado por vários projetos de plataforma específica.


## <a name="what-is-a-portable-class-library"></a>O que é uma biblioteca de classes portátil?

Quando você cria um projeto de aplicativo ou um projeto de biblioteca, a DLL resultante é restrita a trabalhar na plataforma específica, para que ele é criado. Isso evita que você gravar um assembly para um aplicativo do Windows e, em seguida, usá-lo novamente no xamarin e xamarin.

Quando você cria uma biblioteca de classes portátil, no entanto, você pode escolher uma combinação de plataformas que você deseja que seu código seja executado. As opções de compatibilidade feitas durante a criação de uma biblioteca de classes portátil são convertidas em um identificador "Perfil", que descreve quais plataformas que oferece suporte à biblioteca.

A tabela a seguir mostra alguns dos recursos que variam por plataforma .NET. Para escrever um assembly PCL sempre é executado em dispositivos/plataformas específicas você simplesmente escolher qual suporte é necessário quando você cria o projeto.

|Recurso|.NET Framework|Aplicativos UWP|Silverlight|Windows Phone|Xamarin|
|---|---|---|---|---|---|
|Núcleo|S|S|S|S|S|
|LINQ|S|S|S|S|S|
|IQueryable|S|S|S|7.5 +|S|
|Serialização|S|S|S|S|S|
|Anotações de dados|4.0.3 +|S|S||S|

A coluna Xamarin reflete o fato de que todos os perfis que acompanha o Visual Studio oferece suporte ao xamarin e xamarin, e a disponibilidade de recursos na biblioteca que você criar será limitada somente pelas outras plataformas que você escolher para dar suporte.

Isso inclui os perfis são combinações de:

-  O .NET 4 ou .NET 4.5
-  Silverlight 5
-  Windows Phone 8
-  Aplicativos UWP

Você pode ler mais sobre os recursos dos perfis diferentes em [site da Microsoft](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx) e veja outro membro da comunidade [resumo do perfil PCL](http://embed.plnkr.co/03ck2dCtnJogBKHJ9EjY) que inclui suporte para informações de estrutura e outras observações.



Criar um PCL para compartilhar código tem um número de prós e contras versus a alternativa de vinculação de arquivo:


**Benefícios**

1. Compartilhamento de código centralizado – escrever e testar o código em um único projeto que pode ser consumido por outros aplicativos ou bibliotecas.
1. Operações de refatoração afetará todo o código carregado na solução (biblioteca de classes portátil e os projetos específicos de plataforma).
1. O projeto PCL pode ser facilmente referenciado por outros projetos em uma solução ou o assembly de saída pode ser compartilhado para que outros usuários fazer referência em suas soluções.


**Desvantagens**

1. Como a mesma biblioteca de classes portátil é compartilhada entre vários aplicativos, bibliotecas de plataforma específica não podem ser referenciadas (por exemplo. Community.CsharpSqlite.WP7).
1. O subconjunto de biblioteca de classes portátil não pode incluir classes que estariam disponíveis no MonoTouch e Mono para Android (como DllImport ou System.IO).



Até certo ponto ambas as desvantagens podem ser contornadas usando o provedor padrão ou a injeção de dependência para a implementação real nos projetos de plataforma em relação a uma interface ou classe base que é definido na biblioteca de classes portátil de código.



Este diagrama mostra a arquitetura de um aplicativo de plataforma cruzada usando uma biblioteca de classes portátil para compartilhar código, mas também usando injeção de dependência para passar nos recursos dependente de plataforma:



[![](pcl-images/image1.png "Este diagrama mostra a arquitetura de um aplicativo de plataforma cruzada usando uma biblioteca de classes portátil para compartilhar código, mas também usando injeção de dependência para passar nos recursos dependente de plataforma")](pcl-images/image1.png#lightbox)



# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)



## <a name="visual-studio-for-mac-walkthrough"></a>Passo a passo do Visual Studio para Mac


Esta seção explica como criar e usar uma biblioteca de classes portátil usando o Visual Studio para Mac. Consulte a seção de exemplo PCL para uma implementação completa.



### <a name="creating-a-pcl"></a>Criando um PCL


Adicionar uma biblioteca de classes portátil para sua solução é muito semelhante à adição de um projeto de biblioteca comum.


1. Na caixa de diálogo Novo projeto, selecione o `Multiplatform > Library > Portable Library` opção


    ![](pcl-images/image2.png "Multiplatform > Biblioteca > biblioteca portátil")


1. Quando um PCL é criado no Visual Studio para Mac, ele é automaticamente configurado com um perfil que funciona para xamarin e xamarin. O projeto PCL serão exibidos conforme mostrado nesta captura de tela:

    ![](pcl-images/image3.png "O projeto PCL serão exibidos conforme mostrado nesta captura de tela")

O PCL agora está pronto para o código a ser adicionado. Ele também pode ser referenciado por outros projetos (projetos de aplicativo, projetos de biblioteca e até mesmo outros projetos PCL).



### <a name="editing-pcl-settings"></a>Editar configurações de PCL


Para exibir e alterar as configurações de PCL para este projeto, clique com o botão direito e escolha **opções > compilar > geral** para ver a tela mostrada aqui:



[![](pcl-images/image4.png "Para exibir e alterar as configurações de PCL para este projeto, clique com o botão direito e escolha Opções de compilação gerais para ver a tela mostrada aqui")](pcl-images/image4.png#lightbox)



As configurações nesta tela controlam quais plataformas este PCL específico é compatível com. Alteração de qualquer uma dessas opções altera o perfil que está sendo usado por este PCL, que por sua vez, controla os recursos que podem ser usadas no código portátil.



Qualquer alteração de `Target Framework` opções atualiza automaticamente o `Current Profile`; a tela também exibirá um aviso se opções incompatíveis são selecionadas.



[![](pcl-images/image5.png "Alterar qualquer uma das opções de estrutura de destino automaticamente atualiza o perfil atual a tela também exibirá um aviso se opções incompatíveis forem selecionadas")](pcl-images/image5.png#lightbox)



Se o perfil for alterado depois que o código já foi adicionado para o PCL, é possível que a biblioteca não serão mais compilados se o código faz referência a recursos que não fazem parte do perfil selecionado mais recentemente.


## <a name="working-with-a-pcl"></a>Trabalhando com um PCL


Quando o código é escrito em uma biblioteca PCL, o Visual Studio para o editor de Mac reconhecerá as limitações do perfil selecionado e ajustar as opções de preenchimento automático adequadamente. Por exemplo, nesta captura de tela mostra as opções de preenchimento automático para System.IO usando o perfil padrão (Profile136) usado no Visual Studio para Mac – Observe a barra de rolagem que indica a cerca de metade das classes disponíveis é exibida (na verdade são apenas 14 classes disponíveis).



[![](pcl-images/image6.png "E/s usando o perfil padrão Profile136 usada no Visual Studio para aviso de Mac, a barra de rolagem que indica a cerca de metade das classes disponíveis é exibida na verdade lá estão apenas 14 classes disponíveis")](pcl-images/image6.png#lightbox)



Comparar que com o System.IO preenchimento automático em um projeto xamarin ou xamarin – há 40 classes disponível, incluindo comumente usados classes como `File` e `Directory` que não estão em nenhum perfil PCL.



[![](pcl-images/image7.png "Classes de 40 disponível, incluindo comumente usados classes como arquivos e diretórios que não estão em nenhum perfil PCL")](pcl-images/image7.png#lightbox)



Isso reflete a compensação subjacente do uso de PCL – a capacidade de compartilhar código diretamente em muitas plataformas significa que determinadas APIs não estão disponíveis para você porque eles não têm implementações semelhantes em todas as plataformas possíveis.



### <a name="using-pcl"></a>Usando PCL


Quando um projeto PCL tiver sido criado, você pode adicionar uma referência a ele de qualquer projeto de aplicativo ou biblioteca compatível da mesma maneira que você normalmente adicionar referências. No Visual Studio para Mac, clique com botão direito no nó de referências e escolha `Edit References…` , em seguida, alternar para a guia de projetos, conforme mostrado:



[![](pcl-images/image8.png "No Visual Studio para Mac, clique com botão direito no nó de referências e escolha Editar referências e alternar para a guia de projetos, conforme mostrado")](pcl-images/image8.png#lightbox)



Captura de tela a seguir mostra o teclado de solução para o aplicativo de exemplo TaskyPortable, mostrando a biblioteca PCL na parte inferior e uma referência a essa biblioteca PCL no projeto xamarin.



[![](pcl-images/image9.png "O preenchimento de solução para o aplicativo de exemplo TaskyPortable")](pcl-images/image9.png#lightbox)



A saída de um PCL (ie. a DLL do assembly resultante) também pode ser adicionado como uma referência para a maioria dos projetos. Isso torna PCL maneira ideal para enviar a bibliotecas e componentes de plataforma cruzada.




# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)




## <a name="visual-studio-walkthrough"></a>Passo a passo do Visual Studio


Esta seção explica como criar e usar uma biblioteca de classes portátil usando o Visual Studio. Consulte a seção de exemplo PCL para uma implementação completa.



### <a name="creating-a-pcl"></a>Criando um PCL


Adicionar um PCL à sua solução no Visual Studio é ligeiramente diferente para adicionar um projeto comum.



1. Na tela Adicionar novo projeto, selecione o `Portable Class Library` opção


    ![](pcl-images/image10.png "Biblioteca de classes portátil")


1. O Visual Studio solicitará imediatamente com a seguinte caixa de diálogo para que o perfil pode ser configurado.
 As plataformas que você precisa para dar suporte e pressione Okey de escala.


    ![](pcl-images/image11.png "As plataformas que você precisa para dar suporte e pressione Okey de escala")


1. O projeto PCL serão exibidos conforme mostrado no Gerenciador de soluções. O nó referências indicará que a biblioteca usa um subconjunto do .NET Framework (definidas pelo perfil de PCL).

    ![](pcl-images/image12.png "NET Framework definidas pelo perfil de PCL")

O PCL agora está pronto para o código a ser adicionado. Ele também pode ser referenciado por outros projetos (projetos de aplicativo, projetos de biblioteca e até mesmo outros projetos PCL).



### <a name="editing-pcl-settings"></a>Editar configurações de PCL


As configurações de PCL podem ser exibidas e alteradas clicando duas vezes no projeto e escolhendo **Propriedades > biblioteca** , conforme mostrado nesta captura de tela:



[![](pcl-images/image13.png "As configurações de PCL podem ser exibidas e alteradas clicando duas vezes no projeto e escolhendo a biblioteca de propriedades, conforme mostrado nesta captura de tela")](pcl-images/image13.png#lightbox)



Se o perfil for alterado depois que o código já foi adicionado para o PCL, é possível que a biblioteca não serão mais compilados se o código faz referência a recursos que não fazem parte do perfil selecionado mais recentemente.



### <a name="working-with-a-pcl"></a>Trabalhando com um PCL


Quando o código é escrito em uma biblioteca PCL, o Visual Studio reconhece as limitações do perfil selecionado e ajustar as opções do Intellisense adequadamente. Por exemplo, nesta captura de tela mostra as opções de preenchimento automático para System.IO usando o perfil padrão (Profile136) – Observe a barra de rolagem que indica a cerca de metade das classes disponíveis é exibida (na verdade são apenas 14 classes disponíveis).



[![](pcl-images/image14.png "E/s usando o perfil padrão Profile136")](pcl-images/image14.png#lightbox)



Comparar que com o System.IO preenchimento automático em um projeto comum – há 40 classes disponível, incluindo comumente usados classes como `File` e `Directory` que não estão em nenhum perfil PCL.



[![](pcl-images/image15.png "Preenchimento automático em um projeto regular")](pcl-images/image15.png#lightbox)



Isso reflete a compensação subjacente do uso de PCL – a capacidade de compartilhar código diretamente em muitas plataformas significa que determinadas APIs não estão disponíveis para você porque eles não têm implementações semelhantes em todas as plataformas possíveis.



### <a name="using-pcl"></a>Usando PCL


Quando um projeto PCL tiver sido criado, você pode adicionar uma referência a ele de qualquer projeto de aplicativo ou biblioteca compatível da mesma maneira que você normalmente adicionar referências. No Visual Studio, clique com botão direito no nó de referências e escolha `Add Reference...` , em seguida, alterne para o **solução: projetos** guia conforme mostrado:



[![](pcl-images/image16.png "Guia de projetos, conforme mostrado")](pcl-images/image16.png#lightbox)



Captura de tela a seguir mostra o painel de solução para o aplicativo de exemplo TaskyPortable, mostrando a biblioteca PCL na parte inferior e uma referência a essa biblioteca PCL no projeto xamarin.



[![](pcl-images/image17.png "O painel de solução para o aplicativo de exemplo TaskyPortable")](pcl-images/image17.png#lightbox)



A saída de um PCL (ie. a DLL do assembly resultante) também pode ser adicionado como uma referência para a maioria dos projetos.
Isso torna PCL maneira ideal para enviar a bibliotecas e componentes de plataforma cruzada.




-----



## <a name="pcl-example"></a>Exemplo PCL


O [TaskyPortable](https://developer.xamarin.com/samples/mobile/TaskyPortable/) aplicativo de exemplo demonstra como uma biblioteca de classes portátil pode ser usada com o Xamarin.
Aqui estão algumas capturas de tela dos aplicativos resultantes em execução no iOS, Android e Windows Phone:



[![](pcl-images/image18.png "Aqui estão algumas capturas de tela dos aplicativos resultantes em execução no iOS, Android e Windows Phone")](pcl-images/image18.png#lightbox)



Ele compartilha um número de classes de dados e lógica de código totalmente portátil, e também demonstra como incorporar a requisitos específicos de plataforma usando a injeção de dependência para a implementação do banco de dados SQLite.




A estrutura de solução é mostrada abaixo (no Visual Studio para Mac e Visual Studio respectivamente):



[![](pcl-images/image19.png "A estrutura da solução é mostrada aqui no Visual Studio para Mac e o Visual Studio respectivamente")](pcl-images/image19.png#lightbox)



Porque o código SQLite NET tem partes específicas de plataforma (para trabalhar com as implementações do SQLite em cada sistema operacional diferente) para demonstração fins foi refatorado em um resumo da classe que pode ser compilada em uma biblioteca de classes portátil, e o código real implementado como subclasses em projetos Android e iOS.



### <a name="taskyportablelibrary"></a>TaskyPortableLibrary

A biblioteca de classes portátil é limitada os recursos do .NET que pode dar suporte. Porque ele é compilado para ser executado em várias plataformas, não é possível fazer uso de `[DllImport]` funcionalidade que é usada em SQLite-NET. Em vez disso, SQLite NET é implementado como uma classe abstrata e, em seguida, referenciada através do restante do código compartilhado. Um trecho da API abstrata é mostrado abaixo:


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


O restante do código compartilhado usa a classe abstrata para "armazenar" e "recuperar" objetos do banco de dados. Em qualquer aplicativo que usa essa classe abstrata, deve passar em uma implementação completa que fornece a funcionalidade de banco de dados real.



### <a name="taskyandroid-and-taskyios"></a>TaskyAndroid e TaskyiOS


Os projetos de aplicativo do Android e iOS contém a interface de usuário e outro código específico da plataforma usada com a transmissão o código compartilhado o PCL.



Esses projetos também contêm uma implementação do banco de dados abstrato API funciona nessa plataforma. No iOS e Android a Sqlite mecanismo de banco de dados é integrado ao sistema operacional, para que possa usar a implementação `[DllImport]` conforme mostrado para fornecer a implementação concreta de conectividade de banco de dados. Um trecho de código de implementação específica de plataforma é mostrado aqui:


```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open")]
public static extern Result Open(string filename, out IntPtr db);

[DllImport("sqlite3", EntryPoint = "sqlite3_close")]
public static extern Result Close(IntPtr db);
```


A implementação completa pode ser vista no código de exemplo.

### <a name="taskywinphone"></a>TaskyWinPhone


O aplicativo do Windows Phone tem sua interface de usuário criado com XAML e contém outros código específico da plataforma para se conectar os objetos compartilhados com a interface do usuário.



Em contraste com a implementação usada para iOS e Android, o aplicativo do Windows Phone deve criar e usar uma instância de `Community.Sqlite.dll` como parte de seu resumo de banco de dados API. Em vez de usar `DllImport`, métodos, como abrir são implementados em relação ao assembly Community.Sqlite que é referenciado no `TaskWinPhone` projeto. Um trecho é mostrado aqui, para comparação com o iOS e Android versão acima


```csharp
public static Result Open(string filename, out Sqlite3.sqlite3 db)
{
    db = new Sqlite3.sqlite3();
    return (Result)Sqlite3.sqlite3_open(filename, ref db);
}

public static Result Close(Sqlite3.sqlite3 db)
{
    return (Result)Sqlite3.sqlite3_close(db);
}
```


## <a name="summary"></a>Resumo


Este artigo tem brevemente discutidos os benefícios e armadilhas de bibliotecas de classes portáteis, demonstraram como criar e consumir PCLs de dentro do Visual Studio para Mac e o Visual Studio; e finalmente introduziu um aplicativo de exemplo completo – TaskyPortable – que mostra um PCL em ação.


## <a name="related-links"></a>Links relacionados

- [TaskyPortable (exemplo)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Compilar aplicativos de plataforma cruzada](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Portátil Visual Basic](~/cross-platform/platform/visual-basic/index.md)
- [Projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md)
- [Opções de compartilhamento de código](~/cross-platform/app-fundamentals/code-sharing.md)
- [Desenvolvimento de plataforma cruzada com o .NET Framework (Microsoft)](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
