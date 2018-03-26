---
title: Preparar um aplicativo para lançamento
ms.topic: article
ms.prod: xamarin
ms.assetid: 9C8145B3-FCF1-4649-8C6A-49672DDA4159
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/21/2018
ms.openlocfilehash: baaa40bc89a1ca6728189563c8350f9c9f011762
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="preparing-an-application-for-release"></a>Preparar um aplicativo para lançamento


Depois que um aplicativo tiver sido codificado e testado, será necessário preparar um pacote para distribuição. A primeira tarefa na preparação desse pacote é compilar o aplicativo para a versão, o que envolve principalmente a configuração de alguns atributos do aplicativo.

Use as seguintes etapas para criar o aplicativo para versão:

-   **[Especificar o Ícone do Aplicativo](#Specify_the_Application_Icon)** &ndash; Cada aplicativo Xamarin.Android deve ter um ícone do aplicativo especificado. Embora não seja tecnicamente necessário, alguns mercados, como Google Play, exigem isso.

-   **[Controlar a versão do aplicativo](#Versioning)** &ndash; Essa etapa envolve a inicialização ou atualização das informações de controle de versão. Isso é importante para atualizações futuras de aplicativos e para garantir que os usuários saibam qual versão do aplicativo foi instalada.

-   **[Reduzir o APK](#shrink_apk)** &ndash; O tamanho do APK final pode ser reduzido significativamente usando o vinculador do Xamarin.Android no código gerenciado e o ProGuard no código de bytes Java.

-   **[Proteger o Aplicativo](#protect_app)** &ndash; Impeça que usuários ou invasores depurem, adulterem ou façam engenharia reversa do aplicativo, desabilitando a depuração, ofuscando o código gerenciado, adicionando a antidepuração e antiadulteração e usando compilação nativa.

-   **[Definir propriedades de empacotamento](#Set_Packaging_Properties)** &ndash; Propriedades de empacotamento controlam a criação do pacote APK (Pacote de Aplicativo Android). Esta etapa otimiza o APK, protege seus ativos e modula o empacotamento conforme necessário.

-   **[Compilar](#Compile)** &ndash; Esta etapa compila o código e os ativos para verificar se ele é compilado no modo Versão.

-   **[Arquivamento para Publicação](#archive)** &ndash; Esta etapa compila o aplicativo e coloca-o em um arquivo para assinatura e publicação.

Cada uma dessas etapas é descrita abaixo em mais detalhes.

<a name="Specify_the_Application_Icon" />

## <a name="specify-the-application-icon"></a>Especificar o ícone do aplicativo

É altamente recomendável que cada aplicativo Xamarin.Android especifique um ícone do aplicativo. Alguns mercados de aplicativo não permitirão que um aplicativo Android seja ser publicado sem um. O atributo `Icon` propriedade do `Application` é usado para especificar o ícone do aplicativo de um projeto Xamarin.Android.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio 2015 e posteriores, especifique o ícone do aplicativo por meio da seção **Manifesto do Android** do projeto **Propriedades**, conforme é mostrado na seguinte captura de tela:

[![Definir o ícone do aplicativo](images/vs/01-application-icon-sml.png)](images/vs/01-application-icon.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

No Visual Studio para Mac, também é possível especificar o ícone do aplicativo usando a seção **Aplicativo Android** de **Opções de Projeto**, conforme mostrado na seguinte captura de tela:

[![Definir o ícone do aplicativo](images/xs/01-application-icon-sml.png)](images/xs/01-application-icon.png#lightbox)

-----

Nesses exemplos, `@drawable/icon` refere-se a um arquivo de ícone localizado em **Resources/drawable/icon.png** (a extensão **.png** não está incluída no nome do recurso). Esse atributo também pode ser declarado no arquivo **Properties\AssemblyInfo.cs**, conforme mostrado neste trecho de código de exemplo:

```csharp
[assembly: Application(Icon = "@drawable/icon")]
```

Normalmente, `using Android.App` é declarado na parte superior de **AssemblyInfo.cs** (o namespace do atributo `Application` é `Android.App`), mas talvez você precisará adicionar esta instrução `using` se ela ainda não estiver presente.


<a name="Versioning" />

## <a name="version-the-application"></a>Controle de versão do aplicativo

Controle de versão é importante para a distribuição e manutenção de aplicativos Android. Sem algum tipo de controle de versão em vigor, é difícil determinar se ou como um aplicativo deve ser atualizado. Para ajudar no controle de versão, o Android reconhece dois tipos diferentes de informação: 

-   **Número de versão** &ndash; Um valor inteiro (usado internamente pelo Android e o aplicativo) que representa a versão do aplicativo. A maioria dos aplicativos começa com esse valor é definido como 1 e, em seguida, ele é incrementado a cada build. Esse valor não tem relação ou afinidade com o atributo de nome de versão (veja abaixo). Aplicativos e serviços de publicação não devem exibir esse valor para os usuários. Esse valor é armazenado no arquivo **AndroidManifest.xml** como `android:versionCode`. 

-   **Nome da versão** &ndash; Uma cadeia de caracteres usada apenas para comunicação de informações ao usuário sobre a versão do aplicativo (como instalado em um dispositivo específico). O nome da versão será exibido aos usuários ou no Google Play. Essa cadeia de caracteres não é usada internamente pelo Android. O nome da versão pode ser qualquer valor de cadeia de caracteres que ajude um usuário a identificar o build instalado no dispositivo. Esse valor é armazenado no arquivo **AndroidManifest.xml** como `android:versionName`. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

No Visual Studio, esses valores podem ser definidos na seção **Manifesto Android** do projeto **Propriedades**, conforme mostrado na seguinte captura de tela:

[![Definir o número de versão](images/vs/02-versioning-sml.png)](images/vs/02-versioning.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Esses valores podem ser definidos na seção **Compilar > Aplicativo Android** em **Opções de Projeto** conforme mostrado na seguinte captura de tela:

[![Definir o número de versão](images/xs/02-versioning-sml.png)](images/xs/02-versioning.png#lightbox)

-----

<a name="shrink_apk" />

## <a name="shrink-the-apk"></a>Reduzir o APK

APKs do Xamarin.Android podem ficar menores por meio de uma combinação do vinculador Xamarin.Android, que remove código *gerenciado* desnecessário e a ferramenta *ProGuard* do SDK do Android, que remove *código de bytes Java* não utilizado. O processo de build primeiro usa o vinculador do Xamarin.Android para otimizar o aplicativo no nível do código gerenciado (C#) e posteriormente usa o ProGuard (se habilitado) para otimizar o APK no nível do código de bytes Java.


### <a name="configure-the-linker"></a>Configurar o vinculador

O modo Versão desativa o tempo de execução compartilhado e ativa a vinculação para que o aplicativo seja fornecido apenas com as partes necessárias do Xamarin.Android em tempo de execução. O *vinculador* no Xamarin.Android usa análise estática para determinar quais assemblies, tipos e membros de tipo são usados ou referenciados por um aplicativo Xamarin.Android. O vinculador, em seguida, descarta todos os assemblies, tipos e membros que não são usados (ou referenciados). Isso pode resultar em uma redução significativa no tamanho do pacote. Considere o exemplo [HelloWorld](~/android/deploy-test/linker.md), que apresenta uma redução 83% no tamanho final de seu APK: 

-   Configuração: Nenhum &ndash; Xamarin.Android 4.2.5 Tamanho = 17,4 MB.

-   Configuração: Somente Assemblies do SDK &ndash; Xamarin.Android 4.2.5 Tamanho = 3 MB.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Defina opções de vinculador por meio da seção **Opções do Android** das **Propriedades** do projeto:

[![Opções do Vinculador](images/vs/03-linking-sml.png)](images/vs/03-linking.png#lightbox)

O menu suspenso **Vinculação** fornece as seguintes opções para controlar o vinculador:

-   **Nenhum** &ndash; Isso desativa o vinculador; nenhuma vinculação será executada.

-   **Apenas Assemblies do SDK** &ndash; Isso vinculará apenas os assemblies [exigidos pelo Xamarin.Android](~/cross-platform/internals/available-assemblies.md). 
    Outros assemblies não serão vinculados.

-   **Assemblies de Usuário e do SDK** &ndash; Isso vinculará todos os assemblies exigidos pelo aplicativo e não apenas os exigidos pelo Xamarin.Android.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Defina as opções do vinculador por meio da guia **Vinculador** na seção **Build do Android** de **Opções de Projeto**, conforme mostrado na seguinte captura de tela:

[![Opções do Vinculador](images/xs/03-linking-sml.png)](images/xs/03-linking.png#lightbox)

As opções para controlar o vinculador são as seguintes:

-   **Não vincular** &ndash; Isso desativa o vinculador; nenhuma vinculação será executada.

-   **Vincular apenas assemblies do SDK** &ndash; Isso vinculará apenas os assemblies [exigidos pelo Xamarin.Android](~/cross-platform/internals/available-assemblies.md). Outros assemblies não serão vinculados.

-   **Vincular todos os assemblies** &ndash; Isso vinculará todos os assemblies exigidos pelo aplicativo e não apenas os exigidos pelo Xamarin.Android.

-----

A vinculação pode produzir alguns efeitos colaterais indesejados, portanto, é importante que um aplicativo seja testado novamente no modo Liberação em um dispositivo físico.


### <a name="proguard"></a>ProGuard

*ProGuard* é uma ferramenta de SDK do Android que vincula e ofusca o código Java. O ProGuard normalmente é usado para criar aplicativos menores, reduzindo a superfície de grandes bibliotecas incluídas (como Google Play Services) em seu APK. O ProGuard remove códigos de bytes Java não utilizados, o que torna o aplicativo menor. Por exemplo, usar o ProGuard em pequenos aplicativos Xamarin.Android geralmente alcança uma redução de 24% no tamanho &ndash; usar o ProGuard em aplicativos maiores com várias dependências de biblioteca geralmente alcança uma redução de tamanho ainda maior. 

O ProGuard é não uma alternativa ao vinculador do Xamarin.Android. O vinculador do Xamarin.Android vincula código *gerenciado*, enquanto o ProGuard vincula código de bytes Java. O processo de build primeiro usa o vinculador do Xamarin.Android para otimizar o código (C#) gerenciado no aplicativo e posteriormente usa o ProGuard (se habilitado) para otimizar o APK no código de bytes Java. 

Quando **Habilitar ProGuard** está marcado, o Xamarin.Android executa a ferramenta ProGuard no APK resultante. Um arquivo de configuração do ProGuard é gerado e usado pelo ProGuard no momento da build. O Xamarin. Android também dá suporte a ações de build personalizadas em *ProguardConfiguration*. Você pode adicionar um arquivo de configuração ProGuard personalizado ao projeto, clicar com o botão direito do mouse nele e selecioná-lo como uma ação de build, conforme mostrado neste exemplo: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Ação de build do ProGuard](images/vs/05-proguard-build-action-sml.png)](images/vs/05-proguard-build-action.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Ação de build do ProGuard](images/xs/05-proguard-build-action-sml.png)](images/xs/05-proguard-build-action.png#lightbox)

-----

O ProGuard é desabilitado por padrão. A opção **Habilitar o ProGuard** só está disponível quando o projeto é definido para o modo **Versão**. Todas as ações de build do ProGuard serão ignoradas, a menos que **Habilitar ProGuard** esteja marcada. A configuração de ProGuard do Xamarin.Android não ofusca o APK e não é possível habilitar a ofuscação, mesmo com arquivos de configuração personalizados. Se você quiser usar ofuscação, consulte [Proteção de aplicativo com o Dotfuscator](~/android/deploy-test/release-prep/index.md#dotfuscator). 

Para obter mais informações sobre como usar a ferramenta do ProGuard, consulte [ProGuard](~/android/deploy-test/release-prep/proguard.md).

<a name="protect_app" />

## <a name="protect-the-application"></a>Proteger o aplicativo

<a name="Disable_Debugging" />

### <a name="disable-debugging"></a>Desabilitar a depuração

Durante o desenvolvimento de um aplicativo Android, a depuração é realizada com o uso do JDWP *(Java Debug Wire Protocol)*. Esta é uma tecnologia que permite que ferramentas como **adb** se comuniquem com uma JVM para fins de depuração. O JDWP é ativado por padrão para compilações de depuração de um aplicativo Xamarin.Android. Embora JDWP seja importante durante o desenvolvimento, pode representar um problema de segurança em aplicativos lançados. 

> [!IMPORTANT]
> Sempre desabilite o estado de depuração em um aplicativo lançado na medida do possível (via JDWP) para obter acesso completo ao processo de Java e executar código arbitrário no contexto do aplicativo se esse estado de depuração não estiver desabilitado.

O manifesto do Android contém o atributo `android:debuggable`, que controla se o aplicativo pode ou não ser depurado. Ele é considerado uma prática recomendada para definir o atributo `android:debuggable` como `false`. A maneira mais simples de fazer isso é adicionar uma instrução de compilação condicional ao **AssemblyInfo.cs**: 

```csharp
#if DEBUG
[assembly: Application(Debuggable=true)]
#else
[assembly: Application(Debuggable=false)]
#endif
```

Observe que as compilações de depuração definem automaticamente algumas permissões para facilitar a depuração (como **Internet** e **ReadExternalStorage**). Compilações de versão, no entanto, usam apenas permissões explicitamente configuradas. Se você achar que alternar para o build de versão faz com que o aplicativo perca uma permissão que estava disponível no build de depuração, verifique se habilitou essa permissão explicitamente na lista **Permissões necessárias** conforme descrito em [Permissões](~/android/app-fundamentals/permissions.md). 
 

<a name="dotfuscator" id="dotfuscator" />

### <a name="application-protection-with-dotfuscator"></a>Proteção de aplicativo com o Dotfuscator

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Mesmo com [depuração desabilitada](#Disable_Debugging), os invasores ainda poderão reempacotar um aplicativo, adicionando ou removendo permissões ou opções de configuração. Isso permite que eles façam engenharia reversa, depurem ou adulterem o aplicativo.
O [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) pode ser usado para ofuscar o código gerenciado e injetar código de detecção do estado de segurança de tempo de execução em um aplicativo Xamarin.Android no tempo de compilação.

O Dotfuscator CE está incluído no Visual Studio, no entanto, somente o Visual Studio 2015 Atualização 3 (e superiores) tem a versão correta para trabalhar com o Xamarin.Android. Para usar o Dotfuscator, clique em **Ferramentas > Proteção PreEmptive – Dotfuscator**.

Para configurar o Dotfuscator CE, consulte [Using Dotfuscator Community Edition with Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator) (Como usar o Dotfuscator Community Edition com o Xamarin).
Quando estiver configurado, o Dotfuscator CE protegerá automaticamente cada build criado.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Mesmo com [depuração desabilitada](#Disable_Debugging), os invasores ainda poderão reempacotar um aplicativo, adicionando ou removendo permissões ou opções de configuração. Isso permite que eles façam engenharia reversa, depurem ou adulterem o aplicativo.
Embora não tenha suporte no Visual Studio para Mac, é possível usar o [Dotfuscator Community Edition (CE)](https://www.preemptive.com/products/dotfuscator/overview) com o Visual Studio para ofuscar o código gerenciado e injetar código de detecção do estado de segurança de tempo de execução em um aplicativo Xamarin.Android no momento do build.

Para configurar o Dotfuscator CE, consulte [Using Dotfuscator Community Edition with Xamarin](https://www.preemptive.com/obfuscating-xamarin-with-dotfuscator) (Como usar o Dotfuscator Community Edition com o Xamarin).
Quando estiver configurado, o Dotfuscator CE protegerá automaticamente cada build criado.

-----

<a name="bundle" />

### <a name="bundle-assemblies-into-native-code"></a>Agrupar assemblies em código nativo

Quando essa opção é habilitada, os assemblies são agrupados em uma biblioteca compartilhada nativa. Essa opção protege o código; protege os assemblies gerenciados incorporando-os em binários nativos.

Essa opção requer uma licença corporativa e só está disponível quando a opção **Usar Implantação Rápida** está desabilitada. **Agrupar assemblies em código nativo** é desabilitada por padrão.

Observe que a opção **Agrupar em Código Nativo** *não* significa que os assemblies são compilados em código nativo. Não é possível usar [**Compilação AOT**](#aot) para compilar assemblies em código nativo (atualmente apenas um recurso experimental e não para uso em produção).

<a name="aot" />

### <a name="aot-compilation"></a>Compilação AOT

A opção **Compilação AOT** (na página [Propriedades de Empacotamento](#Set_Packaging_Properties)) permite compilação AOT (Ahead-of-Time) de assemblies. Quando essa opção é habilitada, a sobrecarga de inicialização JIT (Just In Time) é minimizada pela pré-compilação de assemblies antes do tempo de execução. O código nativo resultante está incluído no APK juntamente com os assemblies não compilados. Isso resulta em menor tempo de inicialização do aplicativo, mas às custas de tamanhos um pouco maiores de APK.

A opção **Compilação AOT** requer uma licença Enterprise ou superior. **Compilação AOT** só está disponível quando o projeto é configurado para o modo Versão e é desabilitada por padrão. Para obter mais informações sobre Compilação AOT, consulte [AOT](http://www.mono-project.com/docs/advanced/aot/).


#### <a name="llvm-optimizing-compiler"></a>Compilador de otimização de LLVM

O _compilador de otimização LLVM_ criará código compilado mais rápido e menor e converterá os assemblies compilados AOT em código nativo, mas às custas de tempos de compilação mais lentos. O compilador LLVM é desabilitado por padrão. Para usar o compilador LLVM, a opção **Compilação AOT** deve ser habilitada primeiro (na página [Propriedades de Empacotamento](#Set_Packaging_Properties)).


> [!NOTE]
> A opção **Compilador de otimização LLVM** requer uma licença comercial.  

<a name="Set_Packaging_Properties" />

## <a name="set-packaging-properties"></a>Definir propriedades de empacotamento

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

As propriedades de empacotamento podem ser definidas na seção **Opções do Android** das **Propriedades** do projeto, conforme mostrado na seguinte captura de tela:

[![Propriedades de empacotamento](images/vs/04-packaging-sml.png)](images/vs/04-packaging.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

As propriedades de empacotamento podem ser definidas em **Opções de Projeto**, conforme mostrado na seguinte captura de tela:

[![Propriedades de empacotamento](images/xs/04-packaging-sml.png)](images/xs/04-packaging.png#lightbox)

-----

Muitas dessas propriedades, como **Usar Tempo de Execução Compartilhado** e **Usar Implantação Rápida**, destinam-se ao modo de Depuração. No entanto, quando o aplicativo é configurado para modo Versão, existem outras configurações que determinam como o aplicativo é [otimizado para velocidade de execução e tamanho](#shrink_apk), [como é protegido contra violação](#protect_app) e como pode ser empacotado para dar suporte a restrições de tamanho e arquiteturas diferentes.


### <a name="specify-supported-architectures"></a>Especificar arquiteturas com suporte

Ao preparar um aplicativo Xamarin.Android para a versão, é necessário especificar as arquiteturas de CPU com suporte. Um único APK pode conter código de computador para dar suporte a várias arquiteturas diferentes. Consulte [Arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md) para obter detalhes sobre o suporte a várias arquiteturas de CPU.


### <a name="generate-one-package-apk-per-selected-abi"></a>Gerar um pacote (. APK) por ABI selecionado

Quando essa opção é habilitada, um APK é criado para cada ABI com suporte (selecionado na guia **Avançado**, conforme descrito em [arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md)) em vez de um único e grande APK para todas as ABIs com suporte. Essa opção só está disponível quando o projeto é configurado para o modo Versão e é desabilitada por padrão.


### <a name="multi-dex"></a>Multi-Dex

Quando a opção **Habilitar Multi-Dex** é habilitada, as ferramentas de SDK do Android são usadas para ignorar o limite de método de 65K do formato de arquivo **.dex**. A limitação do método de 65K baseia-se no número de métodos aos quais um aplicativo _faz referência_ (inclusive aqueles em todas as bibliotecas das quais o aplicativo depende) &ndash; e não se baseia no número de métodos _gravados no código-fonte_. Se um aplicativo apenas definir alguns métodos, mas usar muitos (ou grandes bibliotecas), é possível que o limite de 65K seja excedido.

É possível que um aplicativo não use todos os métodos em cada biblioteca referenciada, portanto, é possível que uma ferramenta como o ProGuard (veja acima) possa remover métodos não utilizados do código. A prática recomendada será habilitar **Habilitar Multi-Dex** somente se for absolutamente necessário, ou seja, o aplicativo ainda faz referência a mais métodos Java de 65K, mesmo após usar o ProGuard.

Para obter mais informações sobre Multi-Dex, consulte [Configurar aplicativos com métodos acima de 64K](http://developer.android.com/tools/building/multidex.html).

<a name="Compile" />

## <a name="compile"></a>Compilar

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Depois de concluir todas as etapas acima, o aplicativo estará pronto para build. Selecione **Compilar > Recompilar Solução** para verificar se o build é bem-sucedido no modo Liberação. Observe que esta etapa ainda não produz um APK.

[Assinatura do Pacote do Aplicativo](~/android/deploy-test/signing/index.md) trata do empacotamento e da assinatura em mais detalhes.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Depois de concluir todas as etapas acima, compile o aplicativo (selecione **Compilar > Compilar Tudo**) para verificar se o build é bem-sucedido no modo Liberação. Observe que esta etapa ainda não produz um APK.

-----


<a name="archive" />

## <a name="archive-for-publishing"></a>Arquivo morto para publicação

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para começar o processo de publicação, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e selecione o item de menu de contexto **Arquivar...**:

[![Arquivar aplicativo](images/vs/07-archive-for-publishing-sml.png)](images/vs/07-archive-for-publishing.png#lightbox)

**Arquivar...** inicia o **Gerenciador de Arquivo Morto** e inicia o processo de arquivamento do pacote de Aplicativo, conforme mostrado nesta captura de tela:

[![Gerenciador de Arquivo Morto](images/vs/08-archive-manager-sml.png)](images/vs/08-archive-manager.png#lightbox)

Outra maneira de criar um arquivo morto é clicar com o botão direito do mouse na Solução no **Gerenciador de Soluções** e selecionar **Arquivar Tudo...**, que compila a solução e arquiva todos os projetos do Xamarin que podem gerar um arquivo morto:

[![Arquivar Tudo](images/vs/09-archive-all-sml.png)](images/vs/09-archive-all.png#lightbox)


Tanto **Arquivar** quanto **Arquivar Tudo** inicializam automaticamente o **Gerenciador de Arquivo Morto**. Para iniciar o **Gerenciador de Arquivo Morto** diretamente, clique no item de menu **Ferramentas > Gerenciador de Arquivo Morto...**:

[![Iniciar o Gerenciador de Arquivo Morto](images/vs/10-launch-archive-manager-sml.png)](images/vs/10-launch-archive-manager.png#lightbox)

Acesse os arquivos mortos da solução a qualquer momento clicando com o botão direito do mouse no nó **Solução** e selecionando **Exibir Arquivos Mortos**:

[![Exibir Arquivos Mortos](images/vs/11-view-archives-sml.png)](images/vs/11-view-archives.png#lightbox)

### <a name="the-archive-manager"></a>O Gerenciador de Arquivo Morto

O **Gerenciador de Arquivo Morto** é composto por um painel de **Lista de Soluções**, uma **Lista de Arquivos Mortos** e um **Painel de Detalhes**:

[![Painéis do Gerenciador de Arquivo Morto](images/vs/12-archive-manager-detail-sml.png)](images/vs/12-archive-manager-detail.png#lightbox)

A **Lista de Soluções** exibe todas as soluções que têm pelo menos um projeto arquivado. A **Lista de Soluções** inclui as seguintes seções:

* **Solução Atual** &ndash; Exibe a solução atual. Observe que essa área poderá estar vazia se a solução atual não tiver um arquivo morto existente.
* **Todos os Arquivos Mortos** &ndash; exibe todas as soluções que têm um arquivo morto.
* Caixa de texto **Pesquisar** (na parte superior) &ndash; Filtra as soluções listadas em **Todos os Arquivos Mortos** de acordo com a cadeia de caracteres de pesquisa digitada na caixa de texto.

O **Lista de Arquivos Mortos** exibe a lista de todos os arquivos mortos para a solução selecionada. O **Lista de Arquivos Mortos** inclui as seguintes seções:

* **Nome da solução selecionada** &ndash; Exibe o nome da solução selecionada na **Lista de Soluções**. Todas as informações mostradas na **Lista Arquivos Mortos** refere-se a essa solução selecionada.
* **Filtro de Plataformas** &ndash; Esse campo torna possível filtrar arquivos por tipo de plataforma (por exemplo, iOS ou Android).
* **Itens do Arquivo Morto** &ndash; Lista de arquivos mortos para a solução selecionada. Cada item da lista inclui o nome do projeto, a data de criação e a plataforma. Também pode mostrar informações adicionais, como o progresso quando um item está sendo arquivado ou publicado.

O **Painel de Detalhes** exibe informações adicionais sobre cada arquivo morto. Também permite que o usuário iniciar o fluxo de trabalho de distribuição ou abra a pasta na qual a distribuição foi criada. A seção **Comentários de Build** torna possível incluir comentários de build no arquivo morto.

### <a name="distribution"></a>Distribuição

Quando uma versão arquivada do aplicativo estiver pronta para publicação, selecione o arquivo morto no **Gerenciador de Arquivo Morto** e clique no botão **Distribuir...**:

[![Botão Distribuir](images/vs/13-distribute-sml.png)](images/vs/13-distribute.png#lightbox)

A caixa de diálogo **Canal de Distribuição** mostra informações sobre o aplicativo, uma indicação de progresso do fluxo de trabalho de distribuição e uma variedade de canais de distribuição. Na primeira execução, são apresentadas duas opções:

[![Selecionar Canal de Distribuição](images/vs/14-distribution-channel-sml.png)](images/vs/14-distribution-channel.png#lightbox)

É possível escolher um dos seguintes canais de distribuição:

* **Ad Hoc** &ndash; Salva um APK assinado no disco cujo sideload pode ser feito para dispositivos Android. Prossiga para a [Assinatura do Pacote do Aplicativo](~/android/deploy-test/signing/index.md) para aprender a criar um identidade de assinatura do Android, criar um novo certificado de autenticação para aplicativos Android e publicar uma versão _ad hoc_ do aplicativo no disco. Essa é uma boa maneira de criar um APK para teste.

* O **Google Play** &ndash; publica um APK assinado para o Google Play. Prossiga para [Publicar no Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) para saber como assinar e publicar um APK na Google Play Store.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Para começar o processo de publicação, selecione **Compilar > Arquivo Morto para Publicação**:

[![Arquivo morto para publicação](images/xs/07-archive-for-publishing-sml.png)](images/xs/07-archive-for-publishing.png#lightbox)

**Arquivo Morto para Publicação** compila o projeto e empacota-o em um arquivo morto. A opção de menu **Arquivar Tudo** arquiva todos os projetos arquiváveis na solução. Ambas as opções abrem automaticamente o **Gerenciador de Arquivo Morto** quando as operações de build e de agrupamento são concluídas:

[![Exibição de Arquivo Morto](images/xs/08-archives-view-sml.png)](images/xs/08-archives-view.png#lightbox)

Neste exemplo, o **Gerenciador de Arquivo Morto** lista somente um aplicativo arquivado, **MyApp**. Observe que o campo de comentários permite salvar um breve comentário com o arquivo morto. Para publicar uma versão arquivada de um aplicativo Xamarin.Android, selecione o aplicativo no **Gerenciador de Arquivo Morto** e clique em **Assinar e Distribuir...** conforme mostrado acima. A caixa de diálogo **Assinar e Distribuir** resultante apresenta duas opções:

[![Assinar e Distribuir](images/xs/09-sign-and-distribute-sml.png)](images/xs/09-sign-and-distribute.png#lightbox)


Aqui, é possível selecionar o canal de distribuição:

-   **Ad Hoc** &ndash; Salva um APK assinado no disco de modo que possa ser feito seu sideload para dispositivos Android. Prossiga para a [Assinatura do Pacote do Aplicativo](~/android/deploy-test/signing/index.md) para aprender a criar um identidade de assinatura do Android, criar um novo certificado de autenticação para aplicativos Android e publicar uma versão &ldquo;ad hoc&rdquo; do aplicativo no disco. Essa é uma boa maneira de criar um APK para teste.


-   O **Google Play** &ndash; publica um APK assinado para o Google Play.
    Prossiga para [Publicar no Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md) para saber como assinar e publicar um APK na Google Play Store.

-----


## <a name="related-links"></a>Links relacionados

- [Dispositivos de vários núcleos e o Xamarin.Android](~/android/deploy-test/multicore-devices.md)
- [Arquiteturas de CPU](~/android/app-fundamentals/cpu-architectures.md)
- [AOT](http://www.mono-project.com/docs/advanced/aot/)
- [Reduzir seus códigos e recursos](http://developer.android.com/tools/help/proguard.html)
- [Configurar aplicativos com mais de 64 mil métodos](http://developer.android.com/tools/building/multidex.html)
