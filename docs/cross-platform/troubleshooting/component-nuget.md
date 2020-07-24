---
title: Atualizando referências de componente para o NuGet
description: Este documento descreve como substituir as referências de componente por pacotes NuGet para verificar seus aplicativos no futuro, pois a loja de componentes do Xamarin foi descontinuada.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: b9b771efe338fbcc250aa6e7a83b73f35269d3bf
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996806"
---
# <a name="updating-component-references-to-nuget"></a>Atualizando referências de componente para o NuGet

> [!IMPORTANT]
> A loja de componentes foi descontinuada a partir de 15 de maio de 2018 (este fechamento foi originalmente [anunciado](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) em novembro de 2017).
>
> Os componentes do Xamarin não têm mais suporte no Visual Studio e devem ser substituídos por pacotes NuGet. Siga as instruções abaixo para remover manualmente as referências de componente de seus projetos.

Consulte estas instruções para adicionar pacotes NuGet no [Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package) ou [Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Uma lista de plug- [ins e bibliotecas](https://github.com/xamarin/XamarinComponents/blob/master/README.md) populares do Xamarin está disponível para ajudar a encontrar alternativas para componentes que não estão disponíveis como pacotes NuGet.

## <a name="manually-removing-component-references"></a>Removendo referências de componente manualmente

A versão 15,6 do Visual Studio e a versão 7,4 do Visual Studio para Mac não oferecem mais suporte a componentes em seu projeto.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Se você carregar um projeto no Visual Studio, a caixa de diálogo a seguir será exibida, explicando que você deve remover manualmente todos os componentes do seu projeto:

![Caixa de diálogo de alerta explicando que um componente foi encontrado em seu projeto e deve ser removido](component-nuget-images/component-alert-vs.png)

Para remover um componente do seu projeto:

1. Abra o arquivo **.csproj**. Para fazer isso, clique com o botão direito do mouse no nome do projeto e selecione **descarregar projeto**.

2. Clique com o botão direito do mouse novamente no projeto descarregado e selecione **Editar {Your-Project-Name}. csproj**.

3. Localize todas as referências no arquivo para `XamarinComponentReference` . Ele deve ser semelhante ao exemplo a seguir:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. Remova as referências para `XamarinComponentReference` e salve o arquivo. No exemplo acima, é seguro remover todo o `ItemGroup` .

5. Depois que o arquivo tiver sido salvo, clique com o botão direito do mouse no nome do projeto e selecione **recarregar projeto**.

6. Repita as etapas acima para cada projeto em sua solução.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Se você carregar um projeto em Visual Studio para Mac, a caixa de diálogo a seguir será exibida, explicando que você deve remover todos os componentes do seu projeto manualmente:

![Caixa de diálogo de alerta explicando que um componente foi encontrado em seu projeto e deve ser removido](component-nuget-images/component-alert.png)

Para remover um componente do seu projeto:

1. Abra o arquivo .csproj. Para fazer isso, clique com o botão direito do mouse no nome do projeto e selecione **ferramentas > arquivo de edição**.

2. Localize todas as referências no arquivo para `XamarinComponentReference` . Ele deve ser semelhante ao exemplo a seguir:

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. Remova as referências para `XamarinComponentReference` e salve o arquivo. No exemplo acima, é seguro remover todo o`ItemGroup`

4. Repita as etapas acima para cada projeto em sua solução.

-----

> [!WARNING]
> As instruções a seguir funcionam apenas com versões mais antigas do Visual Studio.
> O nó **componentes** não está mais disponível nas versões atuais do Visual Studio 2017 ou Visual Studio para Mac.

As seções a seguir explicam como atualizar as soluções Xamarin existentes para alterar Referências de componentes para pacotes NuGet.

- [Componentes que contêm pacotes NuGet](#contain)
- [Componentes com substituições de NuGet](#replace)

A maioria dos componentes se enquadra em uma das categorias acima.
Se você estiver usando um componente que não parece ter um pacote NuGet equivalente, leia a seção [componentes sem um caminho de migração do NuGet](#require-update) abaixo.

<a name="contain"></a>

## <a name="components-that-contain-nuget-packages"></a>Componentes que contêm pacotes NuGet

Muitos componentes já contêm pacotes NuGet, e o caminho de migração é simplesmente para excluir a referência de componente.

Você pode determinar se o componente já inclui um pacote NuGet clicando duas vezes no componente na solução:

![Nó de componentes expandido](component-nuget-images/solution-sml.png)

A guia **pacotes** listará todos os pacotes NuGet incluídos no componente:

![A guia pacotes contém NuGet](component-nuget-images/packages-tab-sml.png)

Observe que a guia **assemblies** estará vazia:

![A guia assemblies está vazia](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>Atualizando a solução

Para atualizar sua solução, exclua a entrada de **componente** da solução:

![Excluir componente](component-nuget-images/delete-component-sml.png)

O pacote NuGet permanecerá listado no nó **pacotes** e seu aplicativo será compilado e executado como de costume. No futuro, as atualizações para esse pacote serão executadas por meio do recurso de atualização do **NuGet** :

![Atualizar pacote NuGet](component-nuget-images/nuget-update-sml.png)

<a name="replace"></a>

## <a name="components-with-nuget-replacements"></a>Componentes com substituições de NuGet

Se a guia **assemblies** da página informações do componente contiver entradas, conforme mostrado abaixo, você precisará localizar o pacote NuGet equivalente manualmente.

![Contém assemblies](component-nuget-images/assemblies-tab-sml.png)

Observe que a guia **pacotes** provavelmente estará vazia:

![Guia Pacotes](component-nuget-images/packages-tab-empty-sml.png)

_Ele pode conter dependências do NuGet, mas eles podem ser ignorados._

Para confirmar se existe um pacote NuGet substituto, pesquise em [NuGet.org](https://www.nuget.org/packages), usando o nome do componente ou, como alternativa, por autor.

Por exemplo, você pode encontrar o pacote **SQLite-net-PCL** popular procurando por:

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl)– o nome do produto.
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum)– o perfil do autor.

### <a name="updating-the-solution"></a>Atualizando a solução

Depois de confirmar que o componente está disponível no NuGet, siga estas etapas:

#### <a name="delete-the-component"></a>Excluir o componente

Clique com o botão direito do mouse no componente na solução e escolha **remover**:

![Remover componente](component-nuget-images/remove-component-sml.png)

Isso excluirá o componente e todas as referências. Isso interromperá sua compilação, até que você adicione o pacote NuGet equivalente para substituí-lo.

#### <a name="add-the-nuget-package"></a>Adicionar o pacote NuGet

1. Clique com o botão direito do mouse no nó **pacotes** e escolha **adicionar pacotes...**.
2. Procure a substituição do NuGet por nome ou autor:

    ![Pesquisa do NuGet](component-nuget-images/nuget-search-sml.png)

3. Pressione **Adicionar pacote**.

O pacote NuGet será adicionado ao seu projeto, juntamente com quaisquer dependências.
Isso deve corrigir a compilação. Se a compilação continuar a falhar, investigue cada erro para ver se há diferenças de API entre o componente e o pacote NuGet.

<a name="require-update"></a>

## <a name="components-without-a-nuget-migration-path"></a>Componentes sem um caminho de migração do NuGet

Não se preocupe se você não encontrar imediatamente uma substituição para os componentes usados em seu aplicativo. Os componentes existentes continuarão a funcionar no Visual Studio 15,5, e o nó **componentes** será exibido em sua solução como de costume.

Futuras versões do Visual Studio, no entanto, _não_ irão restaurar ou atualizar componentes.
Isso significa que se você abrir a solução em um novo computador, o componente não será baixado e instalado; e o autor não será capaz de fornecer atualizações. Você deve planejar:

- Extraia os assemblies do componente e faça referência a eles diretamente em seu projeto.
- Entre em contato com o autor do componente e peça sobre os planos para migrar para o NuGet.
- Investigue os pacotes do NuGet alternativos ou busque o código-fonte se o componente for de código-fonte aberto.

Muitos fornecedores de componentes ainda estão trabalhando na migração para o NuGet, e outros (incluindo produtos comercialmente disponíveis) podem estar investigando opções de entrega alternativas.

## <a name="related-links"></a>Links Relacionados

- [Lista de plug-ins e bibliotecas populares do Xamarin](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [Instalar e usar um pacote NuGet (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [Incluindo um pacote NuGet (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
